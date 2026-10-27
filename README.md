# Gardefou

> Nom provisoire. Dépôt **privé** : ne pas rendre public, ne pas partager le contenu hors de l'équipe.

Gardefou est une **passerelle IA sécurisée pour les PME**. Les employés discutent avec l'IA comme sur ChatGPT, mais chaque demande passe d'abord par un filtre installé sur le serveur de l'entreprise :

- les données sensibles (IBAN, noms de clients, montants…) sont **masquées** avant de sortir ;
- les demandes les plus sensibles sont traitées par un **modèle local** et ne quittent jamais l'entreprise ;
- les demandes interdites sont **bloquées** ;
- la direction dispose d'un **tableau de bord** : qui utilise l'IA, pour quoi, ce qui a été filtré ou bloqué.

| | |
| --- | --- |
| **Problème** | Les employés utilisent l'IA sans aucun contrôle sur les données envoyées |
| **Cible** | PME et ETI de 20 à 200 employés, Côte d'Ivoire puis Afrique francophone |
| **Offre** | Installation chez le client + abonnement de maintenance |
| **Horizon** | Environ 32 semaines jusqu'au premier contrat payant |

**Statut actuel : planification. Aucun code n'est encore écrit.**

---

## Sommaire

1. [Calendrier](#calendrier)
2. [Les phases](#les-phases)
3. [Décisions à prendre avant de commencer](#décisions-à-prendre-avant-de-commencer)
4. [Budget de démarrage](#budget-de-démarrage)
5. [Cadre juridique à vérifier](#cadre-juridique-à-vérifier)
6. [Risques principaux](#risques-principaux)
7. [Organisation de l'équipe](#organisation-de-léquipe)
8. [Stack technique prévue](#stack-technique-prévue)
9. [Règles de confidentialité](#règles-de-confidentialité)

---

## Calendrier

Le calendrier suppose environ **15 à 20 heures de travail par semaine**. Il sera ajusté selon le temps réel de l'équipe (voir la décision n° 1).

```
Semaine      1   4   8   12  16  20  24  28  32
             │   │   │   │   │   │   │   │   │
0 Validation ████                                    ◆ G0 (S4)
1 Fondations   ████████                              ◆ G1 (S10)
2 Cœur produit         ████████                      ◆ G2 (S18)
3 Pilote                       ████████              ◆ G3 (S26)
4 Lancement                            ██████
LinkedIn     ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  (en continu)
```

À la fin de chaque phase, un **point de décision** (G0 à G3) dit, sur des critères concrets, si l'on continue, si l'on ajuste ou si l'on s'arrête.

---

## Les phases

### Phase 0 · Validation du marché (S1 – S4)

**Objectif :** prouver que des directeurs de PME ressentent ce problème, et qu'ils paieraient pour le résoudre. C'est la phase la moins coûteuse, et celle qui évite de construire pour personne.

À faire :
- [ ] Vérifier la disponibilité du nom : Google, domaines `.com` / `.ci` / `.ai`, base de marques OAPI
- [ ] Réserver le nom de domaine retenu
- [ ] Lister 10 PME cibles avec un contact direct ou indirect
- [ ] Mener au moins 5 entretiens, **sans présenter le produit**
- [ ] Publier un post LinkedIn par semaine, sur le **problème** et pas sur la solution
- [ ] Faire les maquettes Figma : espace employé, tableau de bord direction

Questions pour les entretiens :
1. Vos employés utilisent-ils l'IA ? Lesquelles, pour quoi faire ?
2. Est-ce que ça vous inquiète ? Pourquoi ?
3. Avez-vous déjà eu un incident ou une crainte précise ?
4. Combien seriez-vous prêt à payer pour contrôler ça ?

Livrables : notes d'entretien (une page par entreprise), synthèse des besoins et du budget évoqué, maquettes.

> **Décision G0, fin S4**
> - **On continue** si au moins 3 dirigeants sur 5 confirment le problème, et qu'au moins 1 accepte d'étudier un pilote.
> - **On ajuste** si le problème est reconnu mais pas prioritaire : changer de cible, d'angle ou de taille d'entreprise.
> - **On s'arrête** si personne ne s'en soucie.

### Phase 1 · Fondations techniques (S3 – S10)

**Objectif :** une passerelle qui masque déjà les données. La phase commence pendant la fin de la phase 0, uniquement sur ce qui ne coûte presque rien.

- [ ] **Étape 1 :** passerelle minimale Node.js + TypeScript + Fastify, qui reçoit un message et le transmet à un modèle externe
- [ ] **Étape 2 :** comptes, mots de passe hachés (Argon2), double authentification (TOTP), rôles vérifiés côté serveur
- [ ] **Étape 3 :** filtre, couche 1 (IBAN, téléphones, emails, cartes, clés d'API), avec masquage par jetons et remise en place dans la réponse
- [ ] **Étape 4 :** journal des demandes **masquées** uniquement, base chiffrée

Livrables : code testé dans ce dépôt, une série de cas piégés pour le filtre, une démo où une demande contenant un IBAN part masquée et revient démasquée.

> **Décision G1, fin S10**
> - La démo de masquage fonctionne de bout en bout.
> - Tous les tests passent, et la relecture de sécurité ne laisse aucun problème critique.
> - Chaque membre de l'équipe technique sait expliquer seul comment le filtre décide.

### Phase 2 · Cœur produit (S11 – S18)

**Objectif :** un produit montrable à un directeur.

- [ ] **Étape 5 :** modèle local avec Ollama, règles de routage, mode hybride
- [ ] Tester plusieurs modèles locaux sur de vraies demandes en français
- [ ] **Étape 6 :** filtre, couche 2 (listes propres à l'entreprise) et couche 3 (classification par le modèle local)
- [ ] **Étape 7 :** interfaces Flutter Web, espace employé et espace direction

Livrables : application complète sur une machine de test, comparatif chiffré des modèles locaux (qualité, vitesse, mémoire), démo de 10 minutes.

> **Décision G2, fin S18**
> - Les trois modes fonctionnent : 100 % local, hybride, externe filtré.
> - Un directeur rencontré en phase 0 a vu la démo et confirme le pilote.
> - Le matériel du pilote est trouvé et son coût est connu.

### Phase 3 · Durcissement et pilote (S19 – S26)

**Objectif :** une installation chez une vraie entreprise, gratuite ou à prix réduit, avec un accord écrit.

- [ ] **Étape 8 :** revue de sécurité complète, dépendances à jour, HTTPS sur le réseau interne, sauvegardes chiffrées, déploiement par Docker Compose
- [ ] Accord de pilote écrit : durée, données, responsabilités, conditions de sortie
- [ ] **Étape 9 :** installation, formation des employés, 4 semaines d'usage réel
- [ ] Un point par semaine avec le responsable côté client

Livrables : guide d'installation testé sur une machine vierge, rapport de pilote (usage, demandes masquées et bloquées, retours), liste des corrections.

> **Décision G3, fin S26**
> - Les employés ont réellement utilisé l'outil.
> - Aucun incident de données.
> - Le client accepte de payer, ou de servir de référence.

### Phase 4 · Lancement commercial (S27 – S32)

**Objectif :** le premier contrat payant. La solution n'est présentée publiquement qu'à ce moment-là.

- [ ] **Étape 10 :** test d'intrusion par un professionnel indépendant, puis corrections
- [ ] Contrat de service et clauses sur les données relus par un juriste
- [ ] Grille de prix fixée à partir des entretiens et du pilote
- [ ] Création de la structure juridique, si ce n'est pas déjà fait
- [ ] Présentation publique : démo, et témoignage du pilote s'il est accordé

Livrables : rapport de test d'intrusion avec les problèmes critiques corrigés, contrat type, plaquette commerciale, page web, premier contrat signé.

### Phase 5 · Agents complémentaires (après S32)

Seulement une fois la passerelle vendue et stable, et dans cet ordre :

1. Assistant documentaire interne (questions sur les procédures de l'entreprise, en local)
2. Extraction de factures et de documents comptables
3. Comptes rendus de réunion
4. Tri et rédaction de mails

Aucun agent qui agit seul (envoi de mails, paiements, modification de comptes) tant que la sécurité n'a pas fait ses preuves chez plusieurs clients.

---

## Décisions à prendre avant de commencer

Chacune change le calendrier ou le budget. Les recommandations sont des propositions, à trancher ensemble.

| # | Décision | Options | Recommandation |
| --- | --- | --- | --- |
| 1 | Temps consacré au projet | Heures par semaine, par personne | Un chiffre réaliste, tenable pendant 8 mois. C'est lui qui fixe toutes les dates |
| 2 | Nom définitif | Gardefou, Écluse, Seuil, Veilleur | Gardefou s'il est libre. Domaine réservé dès la vérification, marque OAPI après G0 |
| 3 | Fournisseur d'IA pour les tests | Claude, OpenAI, Gemini | Un seul pour commencer, avec un plafond de dépense bas, et un code prévu pour en changer facilement |
| 4 | Mode du pilote | Hybride (avec GPU) ou externe filtré (sans) | Hybride si le budget matériel est trouvé. Sinon externe filtré, en le disant clairement au client |
| 5 | Pilote gratuit ou à prix réduit | — | Prix réduit, même symbolique, avec un accord écrit |
| 6 | Création de l'entreprise | Maintenant, ou plus tard | Après G0, avant la signature de l'accord de pilote. Se renseigner auprès du guichet unique du CEPICI et d'un comptable |
| 7 | Qui achète le serveur | Le client, ou nous en location | Le client pour les premiers contrats, pour garder notre trésorerie |

---

## Budget de démarrage

Les montants restent **à chiffrer sur place** : les prix à Abidjan ne se devinent pas. Les postes sont rangés dans l'ordre où la dépense arrive.

| Poste | Quand | Indispensable ? | Montant | Remarque |
| --- | --- | --- | --- | --- |
| Nom de domaine | Phase 0 | Oui | à chiffrer | Petit montant annuel |
| Crédit API IA (tests) | Phase 1 | Oui | à chiffrer | Plafond de dépense réglé chez le fournisseur |
| Dépôt de marque OAPI | Après G0 | Recommandé | à chiffrer | Procédure à vérifier avec un conseil en propriété industrielle |
| Création de l'entreprise | Avant le pilote | Oui | à chiffrer | Frais de création, plus un comptable |
| Machine de test avec GPU | Phase 2 | Selon le mode | à chiffrer | Une location ou un serveur en ligne peut suffire pour les tests |
| Matériel du pilote | Phase 3 | Selon la décision 7 | à chiffrer | À la charge du client si la recommandation est suivie |
| Juriste | Phases 3 et 4 | Oui | à chiffrer | Accord de pilote, puis contrat de service |
| Test d'intrusion | Phase 4 | Oui | à chiffrer | Probablement la plus grosse dépense : demander plusieurs devis tôt |

---

## Cadre juridique à vérifier

À valider avec un juriste. Rien ici ne constitue un avis juridique.

- **Données personnelles :** la loi ivoirienne n° 2013-450 encadre leur traitement, sous le contrôle de l'ARTCI. À clarifier : qui, de nous ou du client, doit faire une déclaration.
- **Envoi vers un fournisseur étranger :** en mode hybride ou externe, des demandes masquées partent hors du pays. Le contrat doit le dire clairement.
- **Responsabilité :** le contrat ne promet jamais « zéro fuite » et précise ce qui relève du client : réseau, téléphones des employés, politique interne.
- **Marque :** le dépôt se fait auprès de l'OAPI et vaut dans ses pays membres. Le code est protégé par le droit d'auteur dès qu'il est écrit.

---

## Risques principaux

| Risque | Niveau | Parade |
| --- | --- | --- |
| Personne n'achète | Élevé | La phase 0 sert à le savoir tôt ; presque aucune dépense avant G0 |
| Incident de données chez un client | Élevé | Pas d'action automatique, journaux masqués, relecture de sécurité à chaque étape, test d'intrusion avant de facturer |
| Épuisement, manque de temps | Élevé | Temps hebdomadaire fixé à l'avance, une étape à la fois, points de décision pour ralentir si besoin |
| Le filtre laisse passer des données | Moyen | Trois couches de filtre, cas piégés dans les tests, promesse honnête dans le contrat |
| Matériel GPU trop cher | Moyen | Mode externe filtré pour démarrer, chiffrage en phase 2 |
| Code que l'équipe ne comprend pas | Moyen | Chaque étape expliquée et relue ; critère de G1 : savoir expliquer le filtre seul |
| Un grand éditeur vise le même marché | Moyen | Rester là où ils sont faibles : prix PME, français, installation locale, présence sur place |
| Dépendance à un fournisseur d'IA | Moyen | Code prévu pour changer de fournisseur ; modèle local en solution de repli |

---

## Organisation de l'équipe

### Rôles à attribuer

| Rôle | Responsabilités | Qui |
| --- | --- | --- |
| Produit et décisions | Arbitre aux points de décision, garde le cap du plan | Franck |
| Commercial | Entretiens, prospection, relation avec le client pilote | _à attribuer_ |
| Technique, passerelle | API, filtre, routage, sécurité | _à attribuer_ |
| Technique, interfaces | Flutter Web, maquettes Figma | _à attribuer_ |
| Communication | Posts LinkedIn, visuels | _à attribuer_ |

### Ce qui est fait avec l'IA, et ce qui ne l'est pas

Avec Claude (Claude Code et les outils ECC) :
- écrire le code, tests d'abord ;
- relire chaque étape (sécurité, TypeScript, Flutter) ;
- expliquer chaque choix technique ;
- préparer les guides d'entretien, les posts LinkedIn, la plaquette ;
- rédiger des premières versions de contrats, **toujours relues par un juriste**.

Par l'équipe ou par des professionnels :
- les entretiens, la vente, la relation client ;
- les décisions aux points de passage ;
- l'installation et le support chez le client ;
- le test d'intrusion, par un prestataire indépendant ;
- la validation juridique et le dépôt de marque.

### Règles de travail sur ce dépôt

- La branche `main` reste stable : chaque changement passe par une branche et une pull request relue par une autre personne.
- Aucune clé d'API, aucun mot de passe et aucune donnée client dans le dépôt. Les secrets vont dans un fichier `.env`, exclu de Git.
- Une étape du plan à la fois. On ne passe à la suivante qu'une fois la précédente testée et comprise.

---

## Stack technique prévue

| Brique | Choix |
| --- | --- |
| Passerelle (API) | Node.js + TypeScript, framework Fastify |
| Base de données | PostgreSQL, chiffrée |
| Modèle local | Ollama, modèle open source choisi après tests en français |
| Interfaces employé et direction | Flutter Web, une seule application avec deux rôles |
| Authentification | Argon2 + double authentification TOTP |
| Déploiement | Docker Compose sur le serveur du client, une installation par client |

Le document d'architecture détaillé existe à part. Il sera ajouté au dépôt quand l'équipe en aura besoin.

---

## Règles de confidentialité

Tant que le produit n'est pas lancé (phase 4) :

- On parle publiquement du **problème**, jamais de la solution.
- On ne publie **jamais** le fonctionnement détaillé du filtre : ce serait un mode d'emploi pour le contourner.
- On ne cite aucun client ou prospect sans son accord écrit.
- La stratégie de prix reste interne.
