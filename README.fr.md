# 🚀 NovaBot Desktop

🇬🇧 **English version:** [README.md](README.md)

---


**NovaBot** est une application de bureau développée en Python permettant d'automatiser le trading entre **Telegram** et **MetaTrader 5**.

Elle détecte les signaux publiés sur Telegram, les filtre, les interprète, les exécute automatiquement sur MetaTrader 5, suit entièrement le cycle de vie des positions, alimente un Dashboard analytique basé exclusivement sur les preuves MT5 et intègre un moteur de Copy Trading MT5/MT4.

---

# ✨ Fonctionnalités

## 📡 Intégration Telegram

- Connexion via Telethon
- Sélection de plusieurs groupes ou canaux
- Filtrage intelligent des messages
- Détection automatique des signaux
- Gestion des réponses aux signaux
- Création automatique d'un bot privé
- Création automatique d'un groupe Telegram privé
- Synchronisation des notifications MT5

---

## 📈 Trading MetaTrader 5

- Connexion multi-broker
- Détection automatique des terminaux MT5
- Découverte automatique des serveurs
- Exécution des ordres MARKET
- Exécution des ordres LIMIT
- Exécution des ordres STOP
- Gestion multi-TP
- Gestion multi-positions
- Conversion automatique Market → Limit
- Prévention des doublons
- Validation optionnelle des conditions de marché
- Activation assistée du trading algorithmique
- Reconstruction complète du lifecycle des trades
- Cache intelligent des symboles
- Démarrage optimisé

---

## 💰 Money Management

- Lots fixes
- Lots par TP
- Risque en %
- Capital réel
- Capital virtuel
- Coffre de protection
- Répartition automatique des volumes
- Gestion avancée des zones d'entrée
- Tolérance de slippage
- Contrôle du nombre maximum de positions

---

## 🤖 Smart Automations

NovaBot reconnaît automatiquement plusieurs commandes envoyées sur Telegram.

Parmi elles :

- Break Even
- Secure
- Close
- Close Half
- Modify Stop Loss
- Modify Take Profit
- Edit

Chaque commande peut être activée ou désactivée indépendamment.

---


## 📊 Dashboard analytique

- Analyse automatique des groupes Telegram
- Calcul exclusivement à partir des exécutions et des deals MT5
- Taux de réussite confirmé
- Profit net confirmé
- Profit Factor
- Drawdown maximal réalisé
- TP1 à TP4
- Break Even
- Smart Close
- Séries de gains et de pertes
- Durée moyenne des trades
- Note sur 100 et classement par étoiles
- Réinitialisation indépendante par groupe

---

## 📊 Suivi des positions

NovaBot reconstruit automatiquement le cycle de vie complet d'un trade.

Détection de :

- Take Profit
- Stop Loss
- Break Even
- Secure Stop
- Fermeture manuelle
- Smart Close
- Smart Close Half
- Clôture progressive
- Causes mixtes

Toutes les notifications sont automatiquement publiées dans Telegram.

---

## 🔄 Copy Trader

NovaBot possède un moteur de Copy Trading intégré.

Source :

- MetaTrader 5

Destination :

- MetaTrader 5
- MetaTrader 4

Fonctionnalités :

- Copie des positions
- Copie des ordres LIMIT
- Multiplicateur de lots
- Ratio d'equity
- Lot fixe
- Mapping automatique des symboles

---

## 👤 Gestion des profils

Chaque profil possède son propre environnement isolé.

Le profil contient notamment :

- paramètres
- sessions Telegram
- comptes MT5
- historiques
- logs
- alias de symboles

Plusieurs instances de NovaBot peuvent fonctionner simultanément avec des profils différents.

Fonctionnalités supplémentaires :

- Import / Export de profils
- Verrouillage des profils en cours d'utilisation
- Lancement par ligne de commande (--profile)


## 📱 Supervision distante

- Supervision HTTP en lecture seule
- Compatible NovaBOT Companion
- Fonctionnement sur réseau local ou VPN privé
- Authentification Bearer
- QR Code d'appairage
- Aucune commande distante

---

## 🌍 Interface

- PyQt5
- Thèmes clair, sombre et bleu clair
- 10 langues
- 6 modes d'affichage
- 3 tailles de caractères
- Interface adaptative
- Console unifiée
- Modules indépendants

---

# 🛠 Technologies

- Python
- PyQt5
- MetaTrader5
- Telethon
- ZeroMQ
- AsyncIO

---

# 📂 Architecture

NovaBot est organisé autour de quatre modules principaux :

```
Telegram
     │
     ▼
Filtre intelligent
     │
     ▼
Parser de signaux
     │
     ▼
MetaTrader 5
     │
     ▼
Lifecycle des positions
     ├────────► Dashboard analytique
     │
     ▼
Notifications Telegram
```

Le moteur de Copy Trading fonctionne indépendamment.

---

# ⚙️ Principales capacités

- Parsing intelligent des signaux
- Alias automatiques des symboles
- Multi-TP
- Multi-profils
- Multi-broker
- Copy Trading
- Smart Automations
- Break Even automatique
- Secure automatique
- Clôture progressive
- Lifecycle complet des trades
- Validation de marché
- Synchronisation Telegram ↔ MT5
- Dashboard analytique
- Notation automatique des groupes
- Multi-instances

---

# 📌 État du projet

NovaBot est un projet personnel développé en Python visant à automatiser entièrement le trading entre Telegram et MetaTrader 5.

Il intègre un moteur de parsing avancé, un suivi complet du cycle de vie des trades, un Dashboard analytique reposant exclusivement sur les preuves MT5 et un moteur de Copy Trading MT5/MT4.

Le projet est activement développé avec un accent mis désormais sur la stabilité, les performances, la fiabilité et la qualité des analyses plutôt que sur l'ajout de nouvelles fonctionnalités.

---

## Prérequis facultatif — Activation automatique du trading algorithmique

Le Microsoft Visual C++ Redistributable peut être nécessaire pour utiliser la fonctionnalité d'activation automatique du trading algorithmique de NovaBOT.

Téléchargements officiels Microsoft :

- [Visual C++ Redistributable (x64)](https://aka.ms/vc14/vc_redist.x64.exe)
- [Visual C++ Redistributable (x86)](https://aka.ms/vc14/vc_redist.x86.exe)

Ces composants ne sont pas nécessaires au fonctionnement de NovaBOT lui-même et doivent être téléchargés directement depuis Microsoft.

---

### 🤝 Composants tiers

NovaBOT utilise plusieurs composants open source :

- mql-zmq (Apache License 2.0)
- ZeroMQ
- Telethon
- PyQt5

---

# 📄 Licence

Licence non définie pour le moment.

En attendant la publication d'une licence officielle, tous les droits sont réservés.

---

⚠️ Avertissement

Ce projet est mis à disposition publiquement afin de permettre sa consultation, son étude et son amélioration.

NovaBOT est fourni en l'état, sans aucune garantie de fonctionnement, de fiabilité ou d'adéquation à un usage particulier.

L'utilisation de ce logiciel est entièrement sous la responsabilité de son utilisateur. L'auteur ne pourra être tenu responsable de toute perte financière, perte de données, dommage matériel, interruption d'activité ou de toute conséquence directe ou indirecte résultant de l'utilisation de NovaBOT.

Le trading comporte des risques importants. NovaBOT est un outil logiciel et ne constitue en aucun cas un conseil en investissement. Toutes les décisions de trading relèvent exclusivement de l'utilisateur.

Aucune assistance, garantie ou responsabilité n'est fournie par l'auteur.
