- creer un fichier .env sur le serveur pour stocker de maniere permanente les variables d'environnements
- creer un fichier gitignore pour ignorer certains fichiers

- freeze > requirements.txt (pour update les dependances du projet)

-
----------------------------------------------------------------------------------
- Autre methode de configuration des logs ccloudwatchs
# Configuration des logs CloudWatch
cloudwatch_logs = boto3.client('logs', region_name='your-region')  # Remplacez par votre région AWS

# Créer un logger pour les logs métier
business_logger = logging.getLogger('business')
business_logger.setLevel(logging.INFO)
cloudwatch_handler_business = watchtower.CloudWatchLogHandler(
    log_group='/flask/app-logs',
    stream_name='business-logs',
    boto3_client=cloudwatch_logs
)
business_logger.addHandler(cloudwatch_handler_business)

# business_logger.info(f"Numéro {client_phone} dans la whitelist, règles de gestion ignorées.")

# business_logger.warning(f"Numéro {client_phone} est blacklisté.")
----------------------------------------------------------------------------------

tester la connexion a la base de donnée
sudo yum install postgresql15
psql -h <urlbd> -U <postgres> -d <databse>


-nginx
----------------------------------------------------------------------------------
server {
    listen 80;
    server_name _;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /static/ {
        alias /path/to/your/app/static/;
    }
}
----------------------------------------------------------------------------------

-github action
----------------------------------------------------------------------------------
Script de deploiement Github action
name: Deploy Flask App to Amazon Linux

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Install SSH key
      uses: shimataro/ssh-key-action@v2
      with:
        key: ${{ secrets.SSH_PRIVATE_KEY }}
        known_hosts: ${{ secrets.KNOWN_HOSTS }}

    - name: Deploy to Amazon Linux
      run: |
        ssh -o StrictHostKeyChecking=no ec2-user@${{ secrets.SERVER_IP }} << 'EOF'
        cd /path/to/your/app
        git pull origin main
        python3 -m venv venv
        source venv/bin/activate
        pip install -r requirements.txt
        sudo cp nginx.conf /etc/nginx/conf.d/my_flask_app.conf
        sudo systemctl restart nginx
        gunicorn --bind 127.0.0.1:8000 wsgi:app
        EOF
known_hosts 
Il s'agit d'un fichier qui stocke les clés publiques des serveurs auxquels vous vous êtes déjà connecté via SSH.
Sur votre machine locale, connectez-vous une fois à votre serveur via SSH.
La clé publique du serveur sera ajoutée à votre fichier ~/.ssh/known_hosts
Extrayez la ligne correspondant à votre serveur et ajoutez-la en tant que secret GitHub sous le nom KNOWN_HOSTS
my-server-ip ecdsa-sha2-nistp256 AAAA... (clé publique du serveur)
----------------------------------------------------------------------------------
