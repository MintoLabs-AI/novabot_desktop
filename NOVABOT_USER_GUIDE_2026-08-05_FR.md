# NovaBOT User Guide

🌐 **Langue :** Français | [English](NOVABOT_USER_GUIDE_2026-08-05_EN.md)

---

# Guide utilisateur NovaBOT

Date de mise à jour : 14 août 2026  
Public : utilisateurs de NovaBOT Desktop et NovaBOT Companion

## 1. Présentation

Ce guide accompagne un nouvel utilisateur depuis la première ouverture de NovaBOT jusqu'à son utilisation quotidienne.

Chaque chapitre suit l'ordre recommandé de configuration.

NovaBOT est une application Windows qui relie Telegram à MetaTrader 5 ou MetaTrader 4. Elle peut écouter des groupes ou canaux Telegram, reconnaître leurs signaux de trading, appliquer les règles du profil actif, exécuter les ordres sur la plateforme active, suivre leur cycle de vie et publier les résultats dans un groupe Telegram privé.

L'application comprend quatre modules principaux :

- **Telegram** : connexion, choix des sources, filtrage et écoute ;
- **MetaTrader 5 ou 4** : choix de la plateforme et de l’installation, connexion au broker, Money Management, exécution et suivi ;
- **Dashboard** : statistiques fondées sur les exécutions et preuves de la plateforme active ;
- **Copy Trader** : copie MT5 vers MT5/MT4 ou copie du compte MT4 principal vers MT5.

NovaBOT Companion permet de consulter à distance l'état de NovaBOT. Companion est un outil de supervision en lecture seule : il ne peut pas envoyer d'ordre de trading.

> **Important** — NovaBOT peut envoyer automatiquement des ordres réels. Effectuez toujours les premiers essais sur des comptes de démonstration et vérifiez les volumes, le symbole broker, le Stop Loss et les Take Profits avant d'activer l'écoute.

## 2. Prérequis

Avant de commencer, préparez :

- un compte Telegram accessible depuis l'application officielle ;
- un `API ID` et un `API Hash` obtenus sur `my.telegram.org`, rubrique **API development tools** ;
- une installation MetaTrader 5 ou MetaTrader 4 fonctionnelle ;
- le login, le mot de passe et le serveur du compte choisi ;
- pour MT5 : l'autorisation du trading algorithmique et, si l’activation assistée le demande, le runtime Microsoft Visual C++ correspondant au système ;
- pour MT4 : l'EA NovaBOT compilé, mql-zmq, les DLL ZeroMQ, AutoTrading et l'autorisation des imports DLL.

Le terminal MetaTrader doit pouvoir se connecter au compte choisi et afficher les symboles du broker. Les noms peuvent varier selon le broker, par exemple `XAUUSD`, `XAUUSD-VIP` ou une autre variante.

## 3. Premiers pas

Pour une première mise en service, suivez cet ordre :

1. créez un profil NovaBOT ;
2. connectez Telegram ;
3. créez le bot et le groupe Telegram privé du profil ;
4. sélectionnez les sources dans **TRANSFERT TELEGRAM** ;
5. configurez le filtrage Telegram ;
6. choisissez MT5 ou MT4 puis connectez le compte MetaTrader ;
7. collectez les symboles ou générez les alias si nécessaire ;
8. configurez le Money Management ;
9. envoyez un signal d'essai sur un compte de démonstration ;
10. activez l'écoute Telegram seulement après avoir contrôlé le résultat.

### Contrôle rapide avant l'écoute

Vérifiez que :

- l'en-tête indique le bon profil ;
- Telegram affiche un état connecté ;
- le groupe privé NovaBOT existe ;
- au moins une source Telegram est sélectionnée ;
- le ruban affiche la bonne plateforme, le bon login et le bon serveur ;
- le trading algorithmique MT5 ou l’AutoTrading MT4 est autorisé ;
- le mode de volume et ses valeurs correspondent au risque souhaité ;
- les automatisations BE, sécurisation et clôture progressive sont volontairement activées ou désactivées.

## 4. Créer son premier profil

Au lancement, NovaBOT affiche le gestionnaire de profils.

### Création

1. Cliquez sur **NOUVEAU**.
2. Saisissez un nom explicite, par exemple le nom du broker, du compte ou de la stratégie.
3. Choisissez la langue et le thème.
4. Sélectionnez le nouveau profil dans la liste.
5. Cliquez sur **LANCER**.

Chaque profil possède ses propres :

- identifiants et sessions Telegram ;
- compte et plateforme MetaTrader ;
- sources Telegram sélectionnées ;
- règles de filtrage ;
- Money Management ;
- dictionnaire de commandes Smart ;
- historiques, mappings, statistiques et réglages de supervision.

Une modification faite dans un profil ne doit donc pas être considérée comme active dans les autres profils.

### Assistant de configuration du profil

Lorsqu’un nouveau profil est créé, NovaBOT affiche une check-list facultative. Elle ouvre les écrans existants et vérifie automatiquement huit étapes :

1. identifiants API Telegram ;
2. connexion Telegram complète ;
3. bot et groupe privé NovaBOT ;
4. groupes Telegram à écouter ;
5. filtrage Telegram vérifié ;
6. connexion MetaTrader ;
7. Money Management enregistré ;
8. écoute Telegram préparée.

**Passer la configuration guidée** ferme le parcours sans supprimer les réglages déjà réalisés. **Reprendre plus tard** conserve la progression. Une fois tout terminé, le bouton devient **Fermer**. Le bouton global indique **Configurer le profil** tant que des étapes manquent, puis **État du profil** lorsque la configuration est complète. Sa couleur résume l’état : rouge si aucune étape n’est prête, orange si la configuration est partielle, vert si elle est complète.

La connexion Telegram n’est validée qu’après une authentification réelle ; fermer la demande de numéro ne valide pas l’étape. Le bot/groupe passe au vert dès que la création et l’ajout administrateur sont confirmés. Le Copy Trader, le bridge MT4 et Companion restent volontairement hors de cette check-list principale.

### Importer ou exporter un profil

- **EXPORTER** crée une archive ZIP du profil sélectionné.
- **IMPORTER** restaure une archive de profil. Si le nom existe déjà, NovaBOT choisit un nom disponible.

L'export est recommandé avant une modification importante des règles Telegram, du Money Management ou des automatisations.

### Utiliser plusieurs profils

Plusieurs profils différents peuvent fonctionner simultanément. En revanche, un même profil est verrouillé pendant son utilisation afin d'éviter que deux instances modifient les mêmes fichiers.

## 5. Connecter Telegram

### 5.1 Obtenir les identifiants API

1. Ouvrez `https://my.telegram.org`.
2. Connectez-vous avec votre compte Telegram.
3. Ouvrez **API development tools**.
4. Relevez l'`API ID` et l'`API Hash`.

Ces identifiants correspondent à l'accès API Telegram, pas au token du bot privé.

### 5.2 Première connexion

1. Ouvrez l'onglet **Telegram**.
2. Renseignez l'API ID et l'API Hash lorsqu'ils sont demandés.
3. Cliquez sur **CONNECTER TELEGRAM**.
4. Saisissez le numéro de téléphone avec son indicatif international.
5. Consultez Telegram et saisissez le code de connexion reçu.
6. Attendez le message **Connecté à Telegram**.

Si la combinaison API ID/API Hash est invalide, utilisez **MODIFIER API ID / API HASH** avant de retenter la connexion. Ce bouton est masqué pendant que Telegram est connecté.

### 5.3 Créer le bot et le groupe privé

Cliquez sur **CRÉER UN BOT**. NovaBOT dialogue avec BotFather, enregistre les identifiants du bot et crée le groupe Telegram privé utilisé pour les transferts et notifications.

Attendez la confirmation de fin avant de fermer NovaBOT ou Telegram. Si le groupe n'apparaît pas immédiatement sur un autre appareil, envoyez un premier message dans le groupe afin d'amorcer son affichage dans l'application Telegram concernée.

Le bouton **SUPPRIMER LE BOT** peut proposer une suppression locale ou une purge via BotFather. La purge est irréversible.

### 5.4 Choisir les groupes et canaux écoutés

1. Cliquez sur **TRANSFERT TELEGRAM**.
2. Parcourez les onglets Canaux, Groupes et Chats.
3. Cochez les sources à surveiller.
4. Cliquez sur **Sauvegarder**.

Cette sélection alimente aussi les listes de groupes disponibles dans les réglages d'entrée fractionnée et d'exécution sans condition.

### 5.5 Configurer le filtrage Telegram

Cliquez sur **FILTRAGE**. Vous pouvez utiliser une configuration générale ou des règles propres à chaque source.

Les principaux critères sont :

- exiger une valeur numérique ;
- exiger au moins un TP ;
- exiger un SL ;
- autoriser ou refuser les réponses ;
- autoriser les textes, images avec légende, documents avec légende et vidéos ;
- filtrer les images ou documents sans légende, vocaux, GIF, stickers et sondages ;
- ignorer les messages déjà traités ;
- réanalyser ou non les messages modifiés ;
- afficher les messages filtrés et leur raison dans la console.

Une commande Smart envoyée en réponse doit posséder un message parent reconnu. Une commande sans parent n'est pas exécutée.

### 5.6 Activer l'écoute

Lorsque Telegram, le bot, le groupe privé, les sources et la plateforme MetaTrader active sont prêts, cliquez sur **ACTIVER L'ÉCOUTE**.

NovaBOT transfère alors les messages admis vers le groupe privé et soumet les signaux reconnus au parcours MT4 ou MT5 sélectionné. Cliquer de nouveau désactive l'écoute.

### 5.7 Récupérer manuellement un ancien signal

Le bouton **RÉCUPÉRER UN TRADE DEPUIS UN MESSAGE** permet de parcourir une conversation, choisir un ancien message et l'envoyer dans le parcours de traitement.

Cette action reste soumise aux règles du profil actif. Les tolérances et options prises en compte sont celles enregistrées au moment de la récupération.

## 6. Connecter MetaTrader 5 ou MetaTrader 4

Dans le module MetaTrader, choisissez d’abord **MetaTrader 5** ou **MetaTrader 4** dans **Plateforme**, puis sélectionnez uniquement une installation correspondant à ce choix. Les groupes de serveurs filtrent ensuite le catalogue pour faciliter la sélection du serveur exact.

### 6.1 Connexion MetaTrader 5

1. Sélectionnez **MetaTrader 5** et le terminal détecté.
2. Saisissez le login, le mot de passe et le serveur.
3. Utilisez **DÉCOUVRIR SERVEURS** si nécessaire.
4. Cliquez sur **CONNECTER METATRADER**.
5. Vérifiez le compte, le serveur, le solde et l’état du trading algorithmique dans la console et le ruban.

Si le trading algorithmique est désactivé, NovaBOT peut proposer son activation. Cette assistance vérifie la présence du runtime Visual C++ nécessaire et fournit le lien Microsoft x64 ou x86 lorsque celui-ci manque. Le compte n’est considéré comme prêt que lorsque la connexion MT5 et l’autorisation de trading sont cohérentes.

### 6.2 Préparer et connecter MetaTrader 4

MT4 utilise le pont local NovaBOT : EA compilé, mql-zmq et DLL ZeroMQ. Si un élément manque, **CONNECTER METATRADER** affiche une demande d’installation au lieu de déclarer une fausse connexion.

1. Sélectionnez **MetaTrader 4** et l’installation exacte.
2. Si nécessaire, utilisez **INSTALLER LE PONT MT4** et suivez le chapitre 10.
3. Saisissez le login, le mot de passe et le serveur.
4. Cliquez sur **CONNECTER METATRADER**.
5. Attendez que l’EA publie `READY`, puis vérifiez que le ruban affiche **MT4**, le serveur et le login attendus.

La connexion du compte utilise un fichier INI temporaire propre au profil. NovaBOT n’utilise pas pywinauto pour saisir le compte. Le bouton ne devient **DÉCONNECTER METATRADER** qu’après confirmation du bridge, de l’EA et du compte réellement observé. Un terminal ouvert sans EA fonctionnel ne constitue pas une connexion NovaBOT.

### 6.3 Symboles et alias broker

Après la première connexion :

- **COLLECTER LES SYMBOLS** enregistre les informations des symboles disponibles ;
- **GÉNÉRER ALIAS (BROKER)** construit les correspondances entre les noms usuels et les noms du broker.

La collecte n’est pas relancée inutilement à chaque ouverture. Utilisez le bouton lorsque le broker ajoute des symboles, lorsque vous changez de compte/serveur ou lorsque la résolution d’un symbole échoue. Contrôlez toujours le symbole broker affiché, par exemple `XAUUSD-VIP` au lieu de `XAUUSD`.

### 6.4 Tester sans Telegram

La zone de saisie manuelle soumet un texte au même parser et au moteur de la plateforme active. Utilisez un compte de démonstration et un lot minimal pour valider les alias, le Money Management et les notifications avant d’activer l’écoute.

## 7. Configurer le Money Management

Ouvrez le module MetaTrader puis cliquez sur **MONEY MANAGEMENT**. Les réglages sont propres au profil et s’appliquent au moteur MT4 ou MT5 actif.

Les icônes `?` donnent la description du paramètre correspondant.

Le bouton de réinitialisation restaure les valeurs Money Management par défaut uniquement après confirmation. Exportez le profil ou notez les valeurs personnalisées avant de l’utiliser.

### 7.1 Gestion du capital

Trois modes de volume sont disponibles.

#### Lot fixe pour l'ensemble des TP

Vous saisissez un volume fixe. Lorsque **Répartir le risque équitablement sur chaque TP** est activé, ce total est distribué entre les TP dans les limites du minimum et du pas broker. Lorsque l'option est désactivée, le volume configuré est appliqué à chaque TP.

#### Lot saisi par TP

Vous définissez séparément le lot de TP1 à TP4. Seules les valeurs correspondant aux TP présents dans le signal sont utilisées.

#### Lot en pourcentage du capital

NovaBOT calcule le volume à partir de la base de capital, du pourcentage choisi, de l'entrée, du SL et des caractéristiques du symbole. L'option de répartition détermine si le risque calculé est partagé entre les TP ou répété pour chaque TP.

Le volume final est toujours ajusté au minimum, au maximum et au pas autorisés par le broker.

#### Capital virtuel et coffre-fort

- **Utiliser un capital virtuel** remplace la base réelle affichée pour le calcul configuré.
- **Activer le coffre-fort** conserve les informations de capital protégé et de palier dans le profil.

Contrôlez les quatre valeurs affichées : solde réel MetaTrader, capital virtuel, capital protégé et base de calcul du risque.

### 7.2 Exécution

#### Créer un ordre LIMIT si l'écart est élevé

Pour un signal MARKET, NovaBOT peut convertir l'exécution en LIMIT lorsque la dérive entre l'entrée annoncée et le prix courant dépasse le seuil configuré. Un ordre explicitement BUY/SELL LIMIT ou BUY/SELL STOP conserve son type.

#### Tolérance automatique selon la famille d'actifs

La tolérance peut être calculée selon la famille du symbole : Forex, métaux, indices, énergies ou cryptomonnaies. Le champ de repli en points est utilisé lorsque le symbole ou sa famille ne peuvent pas être déterminés.

#### Slippage et déviation

La déviation indique la marge transmise au broker pour l'exécution. Elle ne remplace pas la protection de distance appliquée avant l'ordre.

#### Exécution sans condition par groupe

Pour les groupes affichés depuis **TRANSFERT TELEGRAM**, cette option autorise un MARKET malgré une distance défavorable lorsque la création automatique de LIMIT ou la tolérance automatique est activée. Les ordres explicitement LIMIT/STOP, le Money Management et les autres protections ne sont pas contournés.

### 7.3 Sécurisation

#### Break Even après TP

**Mettre au BE quand un TP est touché** déplace le SL des positions restantes vers leur prix d'entrée, avec l'offset configuré. Le déplacement est appliqué après confirmation de TP1, y compris lorsqu'une fermeture manuelle est identifiée comme la fermeture de TP1.

#### Sécurisation après TP2

Les choix disponibles sont :

- désactivé ;
- SL à TP1 quand TP2 est touché ;
- SL au TP précédent à chaque TP touché.

#### Sécurisation automatique en pips

NovaBOT surveille le gain et déplace le SL lorsque le seuil configuré est atteint. L'offset détermine le niveau de gain verrouillé par rapport à l'entrée.

#### Clôtures partielles progressives

Ces clôtures sont déclenchées à partir de TP1. Elles s'appliquent aux positions restantes des TP suivants.

Vous pouvez choisir :

- Équilibré : 25 / 25 / 25 / 25 % ;
- Agressif : 40 / 20 / 20 / 20 % ;
- Très agressif : 50 / 20 / 20 / 10 % ;
- Personnalisé, avec un total obligatoire de 100 %.

Le volume initial doit être strictement supérieur au minimum d'activation. Une option permet de fermer le volume résiduel sur le dernier TP.

#### Décalage des Take Profits

Cette option rapproche du prix d’entrée uniquement les TP cochés parmi TP1 à TP4. Elle peut appliquer une configuration commune à tous les groupes ou une valeur et une sélection propres à chaque groupe Telegram. Pour un BUY, le niveau est diminué ; pour un SELL, il est augmenté.

#### Ajuster les TP à l’entrée réelle

Cette option conserve les distances prévues entre l’entrée du signal et les TP, puis les reporte depuis le prix réellement exécuté pour les entrées uniques, multiples ou fractionnées. Elle se configure pour tous les groupes ou par groupe.

Une sécurité empêche l’ajustement lorsqu’il pourrait prolonger inutilement un trade :

- BUY : ajustement seulement si le prix courant reste strictement inférieur à l’entrée réelle ;
- SELL : ajustement seulement si le prix courant reste strictement supérieur à l’entrée réelle ;
- prix égal, déjà favorable ou tick indisponible : aucun ajustement.

Le SL n’est pas déplacé et les ordres STOP sont exclus.

### 7.4 Contrôles

#### Empêcher les doublons

NovaBOT compare symbole, sens, SL, TP et tolérance de prix. Un signal considéré comme doublon est bloqué avant une nouvelle ouverture.

#### Limiter le nombre de trades

Activez le blocage, choisissez le maximum et indiquez si les ordres en attente doivent être comptés. Une fois la limite atteinte, le nouveau signal est refusé.

#### Validation des signaux

La validation intervient avant l'envoi de l'ordre, sur le symbole broker réel, les informations du symbole, la géométrie SL/TP, le volume calculé, le risque et les conditions de marché disponibles.

- **Équilibré** accepte davantage de situations dégradées et peut réduire le volume.
- **Strict** bloque davantage lorsque des données de validation importantes sont absentes ou non conformes.

Les décisions possibles sont :

- `ALLOW` : volume normal ;
- `REDUCE` : volume effectivement diminué ;
- `BLOCK` : aucun ordre envoyé.

Si le minimum ou le pas broker empêche une vraie réduction, l'exécution est bloquée au lieu de conserver silencieusement le même volume.

### 7.5 Entrée fractionnée et groupes

L'entrée fractionnée peut utiliser une configuration commune à tous les groupes sélectionnés dans **TRANSFERT TELEGRAM** ou des réglages propres à chaque groupe.

Pour une zone MARKET admissible et suffisamment large, NovaBOT peut créer :

- une branche MARKET ;
- une branche LIMIT dans la zone.

Le volume disponible est partagé entre les deux branches. Si le lot de chaque TP ne permet pas au moins deux volumes conformes au minimum broker, le fractionnement n'est pas appliqué.

**Autoriser les LIMIT après TP1** détermine le sort des LIMIT sœurs :

- option désactivée : les LIMIT encore non déclenchées sont annulées après confirmation MetaTrader du TP1 MARKET ;
- option activée : les LIMIT restent actives.

**Placer le TP de l’entrée 1 à son prix d’entrée** agit lorsque la seconde entrée est réellement déclenchée : les TP des positions de la première entrée sont déplacés à leur prix d’entrée. L’événement doit être confirmé par la plateforme ; le simple placement du pending ne suffit pas.

## 8. Configurer les commandes Smart

Cliquez sur **AUTOMATISATION TELEGRAM** dans le module MetaTrader.

Les familles disponibles comprennent :

- Secure ;
- Break Even ;
- Close Half ;
- Close ;
- Modify SL ;
- Modify TP ;
- Edit.

Chaque famille peut être activée, désactivée ou utilisée en simulation selon les options disponibles. Le dictionnaire accepte des expressions personnalisées.

Pour une commande provenant de Telegram :

1. elle doit être envoyée en réponse au signal parent ;
2. le parent doit être corrélé à un batch de la plateforme active ;
3. le symbole et le batch doivent être identifiables sans ambiguïté ;
4. les commandes Modify SL/TP doivent contenir une nouvelle valeur numérique exploitable.

Une clôture de moitié n'est pas exécutée si le volume restant ou le volume à fermer est incompatible avec le minimum et le pas du broker.

## 9. Configurer le Copy Trader

Le Copy Trader respecte la matrice suivante :

| Source | Cible | Autorisé |
|---|---|---|
| MT5 | MT5 | Oui |
| MT5 | MT4 | Oui |
| Compte MT4 du module principal | MT5 | Oui |
| MT4 | MT4 | Non |

### 9.1 Choisir la source

Les modes disponibles dépendent de la plateforme source :

- saisir un compte MT5 source directement dans le Copy Trader ;
- cocher **UTILISER LE COMPTE DU MODULE METATRADER**.

Avec MT5, le second mode partage la session historique déjà connectée. Avec MT4, il charge automatiquement l’identité et la session live du module principal et n’autorise qu’une cible MT5. L’état de la case est appliqué dès l’ouverture du Copy Trader ; il n’est pas nécessaire de la décocher puis de la recocher.

### 9.2 Choisir la cible

1. Choisissez une plateforme cible autorisée par la matrice : **MT5** ou **MT4**.
2. Saisissez le login, le mot de passe et le serveur.
3. Sélectionnez le terminal détecté.
4. Cliquez sur **CONNECTER**.
5. Attendez le message `[TGT] Connecté` avant de démarrer la copie.

Une fois la connexion confirmée, le bouton **CONNECTER** devient **DÉCONNECTER**.

### 9.3 Régler le volume cible

Trois modes sont disponibles :

- `MULTIPLIER` : volume source × multiplicateur × coefficient Safety ;
- `EQUITY_RATIO` : volume source × ratio d'equity cible/source × Safety ;
- `FIXED` : lot fixe × Safety.

Le volume final est adapté au minimum, au maximum et au pas du symbole cible.

Les options permettent de copier les SL/TP et les futurs ordres pending. Le fichier d'alias permet d'imposer des correspondances de symboles ; NovaBOT peut également apprendre certaines variantes broker confirmées par la cible.

### 9.4 Démarrer la copie en sécurité

Le bouton **DÉMARRER LA COPIE** n'est disponible que lorsque la source et la cible sont connectées.

Avant le démarrage, NovaBOT examine les positions et ordres pending présents sur la source, les mappings persistants et, lorsqu'elle est disponible, la présence réelle des tickets sur la cible.

Le résumé distingue :

- les positions ouvertes sur la source ;
- les opérations déjà copiées ;
- les positions à synchroniser ;
- les ordres pending à synchroniser ;
- le volume total à synchroniser.

Trois choix sont proposés lorsqu'il existe des opérations éligibles :

#### Ignorer les trades déjà ouverts — recommandé

NovaBOT les enregistre comme état initial connu sans les copier. Seuls les nouveaux trades ouverts après le démarrage sont copiés.

#### Copier les trades déjà ouverts

Seules les opérations non mappées ou dont le mapping cible a été confirmé obsolète sont proposées. Une seconde confirmation est obligatoire avant leur envoi immédiat vers la cible.

La synchronisation initiale explicitement confirmée inclut les pending existants, même si la copie automatique des futurs pending est désactivée.

#### Annuler

La copie ne démarre pas. Fermer la fenêtre avec la croix produit le même résultat.

Si toutes les opérations sont déjà associées à la cible, NovaBOT affiche une information et démarre sans envoyer de nouvelle commande d'ouverture.

### 9.5 Pendant la copie

Le bouton devient **ARRÊTER LA COPIE**. La console affiche notamment :

- `[COPY→OPEN]` lors de la demande d'ouverture ;
- `[MAP]` après confirmation du ticket cible ;
- les modifications de volume, SL/TP ou pending ;
- les fermetures et suppressions ;
- les erreurs retournées par la cible.

Utilisez **ARRÊTER LA COPIE** avant de modifier les comptes, les chemins ou la plateforme cible. Utilisez ensuite **DÉCONNECTER** pour libérer les connexions.

## 10. Installer le pont MT4

Le module MetaTrader 4 et la cible MT4 du Copy Trader utilisent l'EA `NovaBot_MT4_Slave_ZMQ.mq4`, les composants mql-zmq et les DLL ZeroMQ.

### Installation depuis NovaBOT

1. Dans le module MetaTrader, choisissez **MT4**, ou choisissez une cible MT4 dans le Copy Trader.
2. Sélectionnez l’installation exacte.
3. Cliquez sur **INSTALLER LE PONT MT4**.
4. Confirmez l'installation et ne touchez ni aux fenêtres ni à la souris pendant l’automatisation.
5. Laissez NovaBOT rechercher le dossier de données, copier les composants et ouvrir MetaEditor pour la compilation.
6. S'il existe plusieurs installations, contrôlez que le terminal sélectionné correspond au bon dossier de données.

Le script installe ou met à jour :

- l'EA NovaBOT dans `MQL4/Experts` ;
- les fichiers include mql-zmq ;
- les DLL nécessaires ;
- la licence tierce associée.

### Compilation, attachement et activation

NovaBOT tente de compiler automatiquement l’EA dans MetaEditor. Vérifiez que `NovaBot_MT4_Slave_ZMQ` apparaît dans **Expert Consultant** et qu’aucune erreur de compilation n’est affichée. Si l’EA n’est pas déjà attaché :

1. renseignez le login et le mot de passe puis cliquez sur **CONNECTER METATRADER** ;
2. ouvrez un graphique du compte concerné ;
3. double-cliquez sur l’EA ;
4. cochez l’autorisation des imports DLL et du trading en direct ;
5. activez **AutoTrading** ;
6. validez et laissez le graphique actif.

La console doit afficher successivement la préparation du pont, l'attente de l'EA puis la connexion au compte attendu. Dans le module principal, le ruban doit afficher `MT4 • serveur • login`.

La première instance utilise normalement les ports 6001/6002. Si cette paire appartient déjà à une autre instance NovaBOT active, une autre paire libre est attribuée automatiquement et transmise à l’EA. NovaBOT ne ferme pas volontairement le bridge d’une instance visible ni un processus étranger.

Après une mise à jour de l'EA du dossier projet, relancez l'installation afin de recopier et recompiler la version embarquée utilisée par MT4.

## 11. Utiliser NovaBOT Companion

NovaBOT Companion consulte l'état d'un profil Desktop. Il n'envoie aucune commande Telegram, Smart ou MetaTrader.

### 11.1 Activer la supervision sur Desktop

1. Ouvrez les **Paramètres** de NovaBOT.
2. Dans **Supervision distante**, cochez **Activer le transport de supervision**.
3. Choisissez **Réseau privé** pour un téléphone ou un autre appareil.
4. Vérifiez l'adresse IPv4 proposée.
5. Choisissez le port, `8765` par défaut.
6. L'authentification par jeton est imposée en mode réseau privé.
7. Validez les paramètres.
8. Revenez dans les paramètres et contrôlez l'état, l'URL et l'empreinte du jeton.

Le pare-feu Windows peut demander une autorisation ou une règle locale. NovaBOT ne crée pas automatiquement cette règle.

### 11.2 Appairer Companion

1. Dans les paramètres Desktop, cliquez sur **Afficher le QR code**.
2. Dans NovaBOT Companion, ouvrez l'ajout ou l'appairage d'un Desktop.
3. Scannez le QR code.
4. Vérifiez le profil et l'adresse proposés.
5. Confirmez l'appairage.

Le QR code contient l'URL privée et le jeton d'authentification. Ne le partagez pas et ne le publiez pas dans un canal ou une capture publique.

### 11.3 Réseau recommandé

Le transport Desktop est en HTTP. Utilisez :

- un réseau local privé et de confiance ; ou
- de préférence un VPN privé chiffré lorsque le téléphone est distant.

N'exposez pas directement le port de supervision sur Internet et n'effectuez pas de redirection publique du port sur le routeur.

### 11.4 Jeton et reconnexion

- **Copier le jeton** place le jeton dans le presse-papiers du PC.
- **Régénérer le jeton** invalide les anciens appairages.
- Après régénération, affichez un nouveau QR code et recommencez l'appairage.

Pour plusieurs profils Desktop, créez un appairage Companion distinct pour chaque profil et chaque adresse/port utilisé.

## 12. Utiliser le Dashboard

Le Dashboard présente les statistiques par source Telegram à partir des événements et opérations attribuables sur la plateforme MetaTrader active. Il reste passif : il analyse les preuves archivées sans envoyer d'ordre.

Vous pouvez consulter :

- les signaux reçus et exécutés ;
- le taux de réussite ;
- le résultat net confirmé ;
- le Profit Factor ;
- le drawdown réalisé ;
- les TP1 à TP4 ;
- les Break Even et Smart Close ;
- la durée moyenne, les séries et la couverture des preuves ;
- la note et le classement des sources.

Les périodes disponibles comprennent toutes les données, 30 jours, 7 jours et aujourd'hui. Une réinitialisation par source crée une nouvelle coupure statistique sans supprimer les archives historiques.

Un résultat non attribuable avec suffisamment de certitude peut rester inconnu au lieu d'être estimé.

## 13. Paramètres généraux et interface

Les paramètres permettent notamment de choisir :

- la connexion automatique à Telegram au démarrage ;
- l'option **Se connecter à MetaTrader 5 (MT5) au démarrage**, dont le libellé historique déclenche en pratique la reconnexion de la plateforme enregistrée dans le profil, MT5 ou MT4 ;
- le mode d'affichage de la fenêtre principale ;
- la taille des caractères appliquée aux modules et aux dialogues, hors gestionnaire de profils ;
- la supervision distante.

Les thèmes disponibles sont clair, sombre et bleu clair. Les modes d'affichage redimensionnent l'interface selon l'espace disponible. Telegram, MetaTrader, Dashboard et Copy Trader disposent chacun de barres de défilement lorsque leur contenu ne tient pas dans la fenêtre, ce qui évite d'écraser les contrôles.

La console globale située en bas de la fenêtre affiche deux lignes. Les messages plus anciens restent accessibles avec sa barre de défilement ; cette console ne prend pas la priorité sur la hauteur utile du module actif.

NovaBOT adapte également sa fenêtre et ses onglets aux changements d'écran et de mise à l'échelle Windows.

L'icône **ℹ️ À propos** présente la version, le build, le copyright, le lien GitHub et les principaux composants tiers.

## 14. Comprendre les notifications

Les principales notifications Telegram distinguent :

- **Signal exécuté** : positions MARKET confirmées ;
- **Signal placé** : ordres pending acceptés mais pas encore déclenchés ;
- **Signal exécuté partiellement** : une partie des ordres a réussi ;
- **Signal non exécuté** : aucun ordre confirmé ;
- **Ordre en attente déclenché** : le pending est devenu une position sur la plateforme active ;
- **TP atteint et confirmé** : preuve terminale disponible ;
- **Break Even appliqué** : modification du SL confirmée ;
- **Ordres LIMIT annulés** : suppression confirmée par la plateforme active ;
- **Trade terminé** : aucune opération active ne reste dans le batch suivi.

Le prix affiché pour un MARKET correspond au prix d'exécution. Le prix d'un pending correspond au prix de placement de l'ordre. Un même signal conserve un batch commun pour ses branches MARKET et LIMIT, y compris avec MT4.

## 15. Procédure quotidienne recommandée

### Au démarrage

1. lancez le bon profil ;
2. contrôlez Telegram et la plateforme MetaTrader active ;
3. vérifiez le bon compte et le bon serveur ;
4. contrôlez les messages de reconnexion ou de restauration des batches ;
5. vérifiez le Money Management du profil ;
6. activez l'écoute seulement après ces contrôles ;
7. si le Copy Trader est utilisé, connectez source et cible puis examinez attentivement la synchronisation initiale.

### Avant une modification importante

1. arrêtez l'écoute Telegram ;
2. arrêtez la copie ;
3. exportez le profil ;
4. modifiez les paramètres ;
5. testez sur un compte de démonstration ;
6. réactivez progressivement les automatismes.

### Avant de fermer NovaBOT

Vérifiez les positions et ordres directement dans MT5 ou MT4. Fermer NovaBOT n'implique pas automatiquement la fermeture des opérations ouvertes chez le broker.

## 16. Dépannage courant

### Telegram refuse API ID/API Hash

- vérifiez que l'API ID est un entier ;
- recopiez l'API Hash sans espace ;
- utilisez **MODIFIER API ID / API HASH** lorsque Telegram est déconnecté.

### Aucun message n'est transféré

- vérifiez la connexion Telegram ;
- vérifiez que le bot et le groupe privé existent ;
- ouvrez **TRANSFERT TELEGRAM** et contrôlez les sources cochées ;
- vérifiez que l'écoute est activée ;
- examinez la raison de filtrage dans la console.

### Le signal est transféré mais non exécuté

- contrôlez la connexion à la plateforme active ; avec MT5, vérifiez le trading algorithmique ; avec MT4, vérifiez l'EA, le pont, les imports DLL et AutoTrading ;
- lisez la raison affichée : parsing, symbole, distance, géométrie SL/TP, volume, doublon, limite de trades, validation ou retcode broker ;
- vérifiez les paramètres du profil actif ;
- ne modifiez pas une tolérance après le signal pour interpréter rétroactivement la décision prise auparavant.

### L'entrée fractionnée ne s'applique pas

- vérifiez que le bon groupe est activé ;
- contrôlez la largeur minimale de zone ;
- vérifiez que le signal n'est pas explicitement LIMIT ou STOP ;
- contrôlez que le volume par TP permet deux lots conformes au minimum broker.

### La cible MT4 reste en attente

- vérifiez que le terminal MT4 est ouvert ;
- vérifiez que l'EA est présent sur un graphique ;
- activez le trading automatique et les imports DLL ;
- réinstallez le bridge puis recompilez l'EA avec F7 ;
- attendez le message `[TGT] Connecté` avant de démarrer la copie.

### Le module principal MT4 reste non connecté

- vérifiez que le chemin sélectionné correspond exactement au terminal attendu ;
- vérifiez que l'EA et les composants ZeroMQ sont installés, puis attachez l'EA à un graphique ;
- autorisez les imports DLL et le trading en direct, puis activez **AutoTrading** ;
- contrôlez que le compte observé par le bridge correspond au login demandé ;
- attendez la confirmation `MT4 • serveur • login` dans le ruban : la présence du terminal seule ne suffit pas ;
- si le pont est incomplet, utilisez **INSTALLER LE PONT MT4** sans manipuler les fenêtres ni la souris pendant l'automatisation.

### Les TP ne sont pas ajustés à l'entrée réelle

L'option est volontairement protégée. Elle agit uniquement lorsque le prix courant est encore défavorable par rapport à l'entrée réellement exécutée : prix inférieur à l'entrée pour un BUY, ou supérieur à l'entrée pour un SELL. Aucun ajustement n'est appliqué si le prix est égal ou déjà favorable, si le tick est indisponible ou pour un ordre STOP.

### Companion ne se connecte pas

- vérifiez que la supervision Desktop est en cours d'exécution ;
- contrôlez l'adresse IPv4 et le port affichés ;
- vérifiez que le téléphone rejoint le même LAN ou VPN ;
- contrôlez le pare-feu Windows ;
- recréez l'appairage si le jeton a été régénéré.

## 17. Glossaire

| Terme | Signification |
|---|---|
| MARKET | Ordre exécuté au prix disponible sur le marché. |
| LIMIT | Ordre en attente placé à un prix plus favorable. |
| STOP | Ordre en attente placé dans le sens d'une poursuite du mouvement. |
| Pending | Ordre placé mais pas encore transformé en position. |
| TP | Take Profit, objectif de prix. |
| SL | Stop Loss, niveau de protection. |
| BE | Break Even, SL replacé autour du prix d'entrée. |
| Batch | Ensemble des positions et ordres issus d'un même signal. |
| Mapping | Association entre un ticket source et son ticket cible dans le Copy Trader. |
| Alias | Correspondance entre le nom usuel d'un symbole et son nom chez le broker. |
| Retcode | Code de résultat retourné par MetaTrader. |
| Profil | Environnement isolé contenant les comptes, réglages et historiques. |

## 18. Règle essentielle

Lors d'une analyse ou d'un incident, utilisez toujours :

- le profil concerné ;
- les paramètres enregistrés au moment du signal ;
- les journaux Telegram et ceux de la plateforme MetaTrader correspondante ;
- les tickets et retcodes confirmés par le terminal.

Un comportement observé dans un profil ne permet pas de conclure que les autres profils utilisent les mêmes règles.
