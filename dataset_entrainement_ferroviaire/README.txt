DATASET D'ENTRAINEMENT — RESEAU FERROVIAIRE (fictif, inspire du reseau SNCF)
============================================================================

Ce jeu de donnees est concu pour t'entrainer a la fois sur Power BI et sur
Databricks. Il suit un modele en etoile (star schema) : une table de faits
et trois dimensions.

FICHIERS
--------
fait_trajets.csv   9000 lignes — table de FAITS (un trajet = une ligne)
  - trajet_id             identifiant unique du trajet
  - date                  date du trajet (relie a dim_dates.date)
  - train_id              train utilise (relie a dim_trains.train_id)
  - gare_depart_id        gare de depart (relie a dim_gares.gare_id)
  - gare_arrivee_id       gare d'arrivee (relie a dim_gares.gare_id)
  - heure_depart_prevue   heure prevue au format HH:MM
  - heure_depart_reelle   heure reelle (vide si trajet annule)
  - retard_minutes        retard en minutes (0 si a l'heure ou annule)
  - statut                A l'heure / Retarde / Fortement retarde / Annule
  - nb_passagers          nombre de passagers a bord
  - prix_moyen_eur        prix moyen du billet sur ce trajet

dim_gares.csv      15 lignes — DIMENSION gares (nom, region, latitude/longitude)
dim_trains.csv     40 lignes — DIMENSION trains (type: TGV inOui, Ouigo,
                   Intercites, TER — capacite, operateur)
dim_dates.csv      365 lignes — DIMENSION calendrier (jour de semaine, mois,
                   trimestre, annee, week-end, jour ferie)

Periode couverte : 01/09/2025 au 31/08/2026.

POURQUOI CE MODELE
-------------------
C'est volontairement un schema en etoile classique : une table de faits
(mesurable, granulaire) entouree de dimensions (descriptives). C'est
exactement la structure que Power BI attend pour une modelisation propre,
et exactement ce que tu recrees en Databricks avec des tables Silver/Gold.

A FAIRE AVEC — POWER BI
------------------------
1. Importer les 4 fichiers, creer les relations (etoile autour de fait_trajets)
2. Construire des mesures DAX : taux de ponctualite, retard moyen,
   revenu total, taux de remplissage moyen
3. Un dashboard avec : carte des gares, retard moyen par mois (time
   intelligence), classement des trains les plus en retard

A FAIRE AVEC — DATABRICKS
---------------------------
1. Charger les CSV comme tables Bronze (lecture brute)
2. Nettoyer/typer en Silver (dates en type date, gestion des heures vides
   pour les trajets annules)
3. Agreger en Gold (ex : ponctualite par gare et par mois) prete a etre
   branchee sur Power BI
4. Convertir les tables en format Delta et tester le time travel

LE COURS QUI VA AVEC
---------------------
Ce dataset est le support de travail du cours "Cours Power BI & Databricks"
(fichier index.html a la racine du projet, a ouvrir dans un navigateur) :

  - 8 modules : Power Query, modelisation en etoile, DAX, Power BI Service,
    Lakehouse & Spark, PySpark & Spark SQL, Delta Lake, Jobs & pont Power BI
  - 21 exercices corriges (serie A Power BI, serie B Databricks, projet fil
    rouge), chacun avec un resultat attendu chiffre calcule sur CES fichiers
  - une grille d'auto-evaluation et un suivi de progression

Les listes ci-dessus ("A FAIRE AVEC") restent valables comme resume : la
version detaillee, guidee et corrigee se trouve dans le cours.

CONVENTION DE CALCUL DU COURS
------------------------------
Les 86 trajets annules ont retard_minutes = 0 alors qu'ils n'ont jamais
roule. Le cours applique donc partout :
  - trajets realises = statut <> 'Annule', soit 8914 lignes
  - retard moyen et taux de ponctualite : sur les trajets realises seulement
    (retard moyen = 3,80 min ; ponctualite = 6059/8914 = 68,0 %)
  - taux d'annulation et revenu : sur les 9000 lignes
    (revenu total = 128 507 473 EUR)
Si tes chiffres different, verifie d'abord quelle convention tu appliques.

Bon entrainement !
