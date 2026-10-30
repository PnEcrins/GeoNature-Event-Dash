Tableau de bord GeoNature avec Dash
===================================

Cette application permet de générer un tableau de bord de données en se connectant à une base de données GeoNature.
Elle a été utilisée pour restituer en continu et en temps réel les données lors d'un évenement naturaliste : Explor'Nature.

Elle affiche les données d'un jeu de données et d'un zonage géographique comme une commune ou autre (configurable dans le fichier `config.py`) avec les éléments suivants :

- tableau des espèces
- tableau des nouvelles espèces pour la commune
- tableau des nouvelles espèce pour la structure
- graphique de répartition des nouvelles espèces
- carte des observations

Tous les éléments sont filtrables par groupe2 INPN, ordre et famille.

Cette application est entièrement basée sur l'outil Python [Dash](https://dash.plotly.com/) et vous pouvez l'adapter à votre contexte ou la compléter avec les requêtes et restitutions que vous souhaitez.

![Aperçu](docs/GeoNature-event-dash-preview.jpg)

Installation et lancement
=========================

    # Télécharger ou cloner le dépôt
    # Installer les dépendances
    pip install -r requirements.in
    # désampler le fichier de config et le remplir
    cp config.py.sample config.py
    # Lancer l'application (par défaut disponible sur : http://127.0.0.1:8050/ )
    python dash_app.py
    # en mode debug (rechargement automatique, affichage des erreurs)
    python dash_app.py --debug
    # sur une autre adresse / un autre port
    python dash_app.py --host 0.0.0.0 --port 8051

Déploiement en production (Apache)
==================================

L'application est servie par gunicorn, lancé par systemd, derrière Apache en reverse proxy.
Les fichiers de configuration sont dans le dossier `deploy/` (à adapter : utilisateur, chemins, préfixe d'URL).

    # Dans config.py, renseigner le préfixe d'URL
    URL_PREFIX = "/event-dash/"

    # Service systemd
    sudo cp deploy/geonature-event-dash.service /etc/systemd/system/
    sudo systemctl daemon-reload
    sudo systemctl enable --now geonature-event-dash

    # Apache
    sudo a2enmod proxy proxy_http
    sudo cp deploy/geonature-event-dash.conf /etc/apache2/conf-available/
    sudo a2enconf geonature-event-dash
    sudo systemctl reload apache2

L'application est alors disponible sur `https://<domaine>/event-dash/`.

Les données sont chargées au démarrage du service : pour les rafraîchir, `sudo systemctl restart geonature-event-dash`.
