FROM httpd:2.4-alpine
# Page d'accueil statique
COPY ./html/ /usr/local/apache2/htdocs/

# Conf de reverse proxy 
COPY ./httpd.conf /usr/local/apache2/conf/httpd.conf
