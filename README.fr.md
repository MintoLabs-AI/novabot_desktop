# 🚀 NovaBOT Desktop

🇬🇧 **English:** [README.md](README.md)  
📘 **Guide utilisateur détaillé :** [NOVABOT_USER_GUIDE_2026-08-05_FR.md](NOVABOT_USER_GUIDE_2026-08-05_FR.md)

---

**NovaBOT** est une application Windows développée en Python qui automatise le traitement des signaux de trading entre **Telegram** et **MetaTrader 5 ou MetaTrader 4**.

Elle écoute des groupes et canaux Telegram, filtre et interprète leurs messages, applique les règles du profil actif, exécute les ordres sur la plateforme sélectionnée, suit leur cycle de vie, publie les notifications dans un groupe privé et alimente un Dashboard analytique passif.

> **Important** — NovaBOT peut envoyer automatiquement des ordres réels. Les premiers essais doivent être effectués sur un compte de démonstration avec des volumes contrôlés.

---

# ✨ Fonctionnalités

## 🧭 Assistant de configuration

Une check-list facultative accompagne la création d’un profil sans remplacer les modules existants.

Elle vérifie automatiquement :

- les identifiants API Telegram ;
- la connexion Telegram complète ;
- le bot et le groupe privé NovaBOT ;
- les groupes Telegram à écouter ;
- le filtrage Telegram ;
- la connexion MetaTrader ;
- le Money Management enregistré ;
- la préparation de l’écoute Telegram.

La progression est persistée. L’utilisateur peut passer la configuration guidée, la reprendre plus tard et consulter ensuite l’**État du profil**. Le bouton d’état devient rouge, orange ou vert selon l’avancement.

---

## 📡 Intégration Telegram

- connexion avec Telethon ;
- création assistée du bot et du groupe privé ;
- confirmation de l’ajout du bot comme administrateur ;
- sélection de plusieurs groupes et canaux ;
- transfert manuel ou écoute automatique ;
- filtrage configurable avec raison de rejet ;
- détection des messages modifiés et supprimés ;
- correction d’un signal Telegram déjà traité sans recréer tout son batch ;
- récupération manuelle d’un ancien signal ;
- notifications durables avec nouvelles tentatives d’envoi ;
- corrélation obligatoire entre une commande Smart et son message parent.

---

## 📈 Trading MetaTrader 5 et MetaTrader 4

- choix explicite de la plateforme puis de l’installation ;
- découverte des terminaux MT5 et MT4 ;
- catalogue de serveurs regroupé par entité broker ;
- connexion multi-broker ;
- résolution des symboles et génération d’alias broker ;
- exécution des ordres MARKET, LIMIT et STOP ;
- gestion multi-TP et multi-positions ;
- conversion automatique MARKET → LIMIT selon les règles du profil ;
- moteurs d’exécution et lifecycles MT4/MT5 clairement séparés ;
- batch commun pour les branches MARKET et LIMIT issues d’un même signal ;
- suivi des positions, pending, modifications et fermetures ;
- activation assistée du trading algorithmique MT5 ;
- démarrage optimisé sans recollecte systématique des symboles.

### Parcours MT5

NovaBOT utilise la connexion Python MetaTrader 5, les informations réelles du symbole, les retcodes du terminal et les historiques MT5 pour exécuter et caractériser les opérations.

### Parcours MT4

MT4 utilise l’EA `NovaBot_MT4_Slave_ZMQ`, mql-zmq et les DLL ZeroMQ. La connexion n’est confirmée qu’après réception de `READY` et vérification du login réellement observé. Le compte est lancé avec un fichier INI temporaire propre au profil ; pywinauto reste réservé à l’installation et à la compilation assistées du pont.

---

## 💰 Money Management

- lot fixe, lot par TP ou risque en pourcentage ;
- capital réel, capital virtuel et coffre de protection ;
- répartition et normalisation des volumes selon le broker ;
- tolérance d’entrée manuelle ou automatique par famille d’actifs ;
- création automatique d’un ordre LIMIT lorsque l’écart est élevé ;
- prévention des doublons et limite du nombre de trades ;
- contrôle facultatif des pending dans cette limite ;
- exécution sans condition configurable par groupe ;
- Break Even, sécurisation automatique et clôtures progressives ;
- décalage des TP pour tous les groupes ou par groupe ;
- ajustement sécurisé des TP à l’entrée réellement exécutée ;
- entrée fractionnée commune à tous les groupes ou configurée par groupe ;
- annulation ou maintien des LIMIT sœurs après TP1 ;
- déplacement des TP de la première entrée à son prix d’entrée lorsque la seconde entrée est déclenchée ;
- restauration des valeurs par défaut après confirmation.

La configuration Money Management du profil est la source de vérité pour l’admission et le dimensionnement des opérations.

---

## ⚖️ Validation des signaux

Le validateur travaille sur le symbole broker résolu et les données disponibles de la plateforme active. Il contrôle notamment la géométrie SL/TP, le volume, le risque et les conditions de marché.

Décisions possibles :

- `ALLOW` : exécution au volume prévu ;
- `REDUCE` : exécution avec une réduction effective ;
- `BLOCK` : aucun ordre envoyé.

Les seuils de marché, notamment le spread, tiennent compte des principales familles d’actifs : Forex, métaux, indices, cryptomonnaies et énergies.

---

## 🤖 Smart Automations

NovaBOT reconnaît des commandes Telegram envoyées en réponse au signal parent :

- Break Even ;
- Secure ;
- Close ;
- Close Half ;
- Modify Stop Loss ;
- Modify Take Profit ;
- Edit.

Chaque famille dispose de son propre dictionnaire et peut être activée, désactivée ou simulée selon les réglages disponibles. Les actions passent par la gateway de la plateforme active et doivent être confirmées par le terminal.

---

## 📊 Lifecycle et notifications

NovaBOT reconstruit le cycle de vie du batch à partir des preuves de la plateforme active :

- déclenchement d’un ordre pending ;
- Take Profit et Stop Loss ;
- Break Even et SL sécurisé ;
- fermeture manuelle ;
- Smart Close et Close Half ;
- clôture progressive ;
- modification ou suppression d’un pending ;
- causes mixtes et fin du trade.

Les branches MARKET et LIMIT d’un même signal restent rattachées au même batch. La notification finale n’est produite que lorsque plus aucune opération active ne subsiste.

---

## 📊 Dashboard analytique

Le Dashboard est un consommateur passif des preuves archivées. Il n’envoie jamais d’ordre.

Il présente notamment :

- les signaux reçus et exécutés ;
- le taux de réussite et le résultat net confirmés ;
- le Profit Factor et le drawdown réalisé ;
- les TP1 à TP4, Break Even et Smart Close ;
- la durée moyenne et les séries ;
- la couverture des preuves ;
- une note sur 100 et un classement par étoiles ;
- une réinitialisation statistique indépendante par source.

Un résultat insuffisamment attribuable reste inconnu au lieu d’être estimé.

---

## 🔄 Copy Trader

Le Copy Trader prend en charge les parcours suivants :

| Source | Cible | Prise en charge |
|---|---|---|
| MT5 | MT5 | Oui |
| MT5 | MT4 | Oui |
| Compte MT4 du module principal | MT5 | Oui |
| MT4 | MT4 | Non |

Fonctionnalités principales :

- utilisation d’un compte source saisi directement ou du compte du module MetaTrader ;
- copie des positions et des ordres pending ;
- multiplicateur de lots, ratio d’equity ou lot fixe ;
- normalisation du volume cible ;
- copie facultative des SL/TP ;
- alias de symboles et apprentissage de variantes broker confirmées ;
- mappings persistants avec vérification de l’existence cible ;
- sécurité de synchronisation initiale : ignorer, copier après double confirmation ou annuler ;
- protection anti-doublon conservée au moment de l’ouverture ;
- suivi des ouvertures, modifications, clôtures et suppressions.

Lorsque plusieurs instances NovaBOT utilisent MT4, une paire de ports libre est attribuée sans fermer le bridge d’une autre instance visible.

---

## 👤 Profils isolés

Chaque profil possède ses propres :

- identifiants et sessions Telegram ;
- plateforme et compte MetaTrader ;
- sources Telegram sélectionnées ;
- règles de filtrage et dictionnaires Smart ;
- paramètres Money Management ;
- alias, mappings, historiques, logs et bases analytiques ;
- réglages de supervision.

NovaBOT permet l’import/export ZIP, le verrouillage des profils utilisés, le lancement par `--profile` et l’exécution simultanée de plusieurs profils.

---

## 📱 Supervision distante

- transport HTTP en lecture seule ;
- compatible avec NovaBOT Companion ;
- réseau local privé ou VPN privé ;
- authentification Bearer obligatoire en mode réseau privé ;
- QR code d’appairage ;
- masquage des données sensibles ;
- aucune commande distante.

---

## 🌍 Interface

- PyQt5 ;
- thèmes clair, sombre et bleu clair ;
- 10 langues ;
- 6 modes d’affichage ;
- 3 tailles de caractères ;
- adaptation commune des modules et dialogues ;
- barres de défilement indépendantes pour Telegram, MetaTrader, Dashboard et Copy Trader ;
- console globale limitée à deux lignes visibles, avec historique défilable ;
- icônes Paramètres et À propos dans les modules.

---

# 🛠 Technologies principales

- Python ;
- PyQt5 ;
- MetaTrader5 ;
- MQL4 ;
- Telethon ;
- ZeroMQ / mql-zmq ;
- SQLite ;
- AsyncIO ;
- pywinauto, uniquement pour l’installation assistée du pont MT4.

---

# 📂 Architecture fonctionnelle

```text
Telegram
   ↓
Filtrage et corrélation
   ↓
Parser de signaux
   ↓
Résolution du symbole broker
   ↓
Admission + Money Management + Validation
   ↓
Moteur MT5 ou moteur MT4
   ↓
Terminal MetaTrader
   ↓
Lifecycle de la plateforme
   ├──→ Notifications Telegram durables
   ├──→ Dashboard analytique
   └──→ Supervision en lecture seule
```

Les règles communes restent séparées des transports spécifiques. MT5 utilise sa connexion native Python ; MT4 communique avec l’EA au moyen du bridge ZeroMQ.

---

# 📌 État du projet

NovaBOT est activement développé avec une priorité donnée à la stabilité, aux performances, à la traçabilité des preuves terminales et à la cohérence entre MT4 et MT5.

Le comportement réel dépend toujours du profil actif, de ses paramètres enregistrés, de la plateforme sélectionnée et des informations retournées par le broker.

---

## 🤝 Composants tiers

NovaBOT utilise notamment :

- mql-zmq — Apache License 2.0 ;
- ZeroMQ ;
- Telethon ;
- PyQt5.

---

# 📄 Licence

Aucune licence de redistribution n’est définie pour le moment. Jusqu’à la publication d’une licence officielle, **tous les droits sont réservés**.

---

# ⚠️ Avertissement

NovaBOT est fourni **en l’état**, sans garantie de fonctionnement, de fiabilité ou d’adéquation à un usage particulier.

Son utilisation relève entièrement de la responsabilité de l’utilisateur. L’auteur ne pourra être tenu responsable d’une perte financière, perte de données, interruption d’activité ou autre conséquence directe ou indirecte.

Le trading comporte des risques importants. NovaBOT est un outil logiciel et ne constitue ni un conseil financier ni un conseil en investissement. Toutes les décisions de trading relèvent exclusivement de l’utilisateur.
