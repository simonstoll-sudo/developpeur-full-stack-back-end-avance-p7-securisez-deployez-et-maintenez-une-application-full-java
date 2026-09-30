# Note de contexte — application Suivi Transports

**Équipe Suivi Transports — Santélog**
**Rédaction : Élodie Vasseur, avec les retours de l'équipe**
**Version : 1.4.2 en production**

## Pourquoi cette note

La direction attend une V2 de l'application de suivi des transports. Avant de la lancer, l'équipe a rassemblé ce qu'elle sait de l'état actuel de l'application et ce qu'elle attend de la V2. Cette note est un point de départ pour la personne qui nous rejoint en renfort ; elle n'est pas exhaustive.

## Ce que fait l'application aujourd'hui

- Enregistrement des transports de produits de santé (médicaments, dispositifs médicaux, échantillons biologiques) avec leur plage de température autorisée, leur chauffeur et leur destinataire.
- Suivi du statut de chaque transport : planifié, en cours, livré, incident.
- Saisie des relevés de température remontés par les capteurs embarqués, avec passage automatique en incident quand un relevé sort de la plage.
- Gestion des destinataires (établissements de santé, pharmacies, laboratoires).
- Rapports par transport et synthèse globale, utilisés par le responsable d'exploitation le matin.
- Connexion des utilisateurs avec trois rôles : administrateur, opérateur, lecteur.

L'application est utilisée par une quinzaine de personnes en interne. Il n'y a pas encore de client externe qui s'y connecte directement.

## État technique tel que l'équipe le connaît

- Application Spring Boot avec une base PostgreSQL. Le schéma est géré automatiquement par Hibernate au démarrage.
- Les mots de passe des utilisateurs sont chiffrés en base.
- Les services sont couverts par des tests unitaires ; la suite passe avec `mvn test`.
- Le pipeline GitHub construit et teste l'application à chaque push.
- Seuls les administrateurs peuvent supprimer un transport.
- La documentation de l'API est disponible via Swagger UI.

## Problèmes connus

1. **Livraisons manuelles.** Chacun construit le `.jar` sur son poste et le copie sur le serveur avec la procédure de son choix. Deux incidents de production en 2024 sont venus d'un `.jar` construit à partir d'une branche non à jour.
2. **Quelques tests manquants.** Le module des rapports et celui des relevés n'ont pas de tests dédiés, ce qui rend les modifications sur le calcul des moyennes délicates.
3. **Relevés parfois en doublon.** Certains capteurs renvoient deux fois le même relevé à quelques secondes d'intervalle ; l'application les enregistre tous les deux.
4. **Configuration copiée d'un environnement à l'autre.** Le fichier de configuration est modifié à la main avant chaque déploiement.
5. **Logs difficiles à exploiter.** Le volume de logs est important et personne ne sait vraiment quoi y chercher lors d'un incident.
6. **Le rapport par transport est lent à modifier.** La méthode qui le génère a grossi au fil des demandes et plus personne n'ose y toucher.

## Attentes pour la V2

- **Multi-agences** : rattacher chaque transport à une agence (Lille, Arras, Valenciennes) et filtrer les écrans par agence.
- **Alertes par mail** : prévenir automatiquement le destinataire et le responsable d'exploitation quand un transport passe en incident.
- **Application mobile chauffeurs** : permettre aux chauffeurs de consulter leurs transports du jour et de signaler un problème depuis leur téléphone. Cela implique d'ouvrir l'API à des clients externes au réseau interne.
- **Historique des statuts** : conserver la date et l'auteur de chaque changement de statut.
- **Export réglementaire** : produire l'attestation de respect de la chaîne du froid demandée par certains établissements.

## Ce que l'équipe attend du renfort

Un regard extérieur sur l'état réel de l'application avant d'engager la V2, en particulier sur l'ouverture de l'API aux chauffeurs, qui inquiète l'équipe compte tenu de la nature des données transportées. L'équipe reste disponible pour répondre aux questions et pour valider les décisions structurantes.
