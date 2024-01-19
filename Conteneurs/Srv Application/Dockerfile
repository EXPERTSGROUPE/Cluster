# Utiliser une image de base PHP avec Apache (ou votre stack technologique préférée)
FROM php:7.4-apache

# Copier les fichiers de l'application dans le conteneur
COPY src/ /var/www/html/

# Exposer le port sur lequel l'application s'exécute
EXPOSE 80

# Lancer Apache en arrière-plan
CMD ["apache2-foreground"]
