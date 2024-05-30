# Utiliser Apache HTTP Server comme image de base
FROM httpd:2.4-alpine

# Copier le fichier index.html dans le répertoire HTML d'Apache
COPY index.html /usr/local/apache2/htdocs/

# Exposer le port 80
# EXPOSE 80

# Copier le fichier de configuration personnalisé
COPY httpd.conf /usr/local/apache2/conf/httpd.conf