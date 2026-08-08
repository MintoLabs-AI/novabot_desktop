# NovaBOT User Guide

🇫🇷 **French version:** [NOVABOT_USER_GUIDE_2026-08-05_EN.md](NOVABOT_USER_GUIDE_2026-08-05_EN.md)

---

# Guide utilisateur NovaBOT

Date : 5 août 2026  
Public : utilisateurs de NovaBOT Desktop et NovaBOT Companion

## 1. Présentation

Ce guide accompagne un nouvel utilisateur depuis la première ouverture de NovaBOT jusqu'à son utilisation quotidienne.

Chaque chapitre suit l'ordre recommandé de configuration.

NovaBOT est une application Windows qui relie Telegram à MetaTrader 5. Elle peut écouter des groupes ou canaux Telegram, reconnaître leurs signaux de trading, appliquer les règles du profil actif, exécuter les ordres sur MT5, suivre leur cycle de vie et publier les résultats dans un groupe Telegram privé.

L'application comprend quatre modules principaux :

- **Telegram** : connexion, choix des sources, filtrage et écoute ;
- **MetaTrader 5** : connexion au broker, Money Management, exécution et suivi ;
- **Dashboard** : statistiques fondées sur les exécutions et preuves MT5 ;
- **Copy Trader** : copie d'un compte source MT5 vers une cible MT5 ou MT4.

NovaBOT Companion permet de consulter à distance l'état de NovaBOT. Companion est un outil de supervision en lecture seule : il ne peut pas envoyer d'ordre de trading.

> **Important** — NovaBOT peut envoyer automatiquement des ordres réels. Effectuez toujours les premiers essais sur des comptes de démonstration et vérifiez les volumes, le symbole broker, le Stop Loss et les Take Profits avant d'activer l'écoute.

## 2. Prérequis

Avant de commencer, préparez :

- un compte Telegram accessible depuis l'application officielle ;
- un `API ID` et un `API Hash` obtenus sur `my.telegram.org`, rubrique **API development tools** ;
- une installation MetaTrader 5 fonctionnelle ;
- le login, le mot de passe et le serveur du compte MT5 ;
- l'autorisation du trading algorithmique dans MT5 ;
- pour une cible MT4 : une installation MT4 et l'autorisation des imports DLL pour l'EA NovaBOT.

Le terminal MetaTrader doit pouvoir se connecter au compte choisi et afficher les symboles du broker. Les noms peuvent varier selon le broker, par exemple `XAUUSD`, `XAUUSD-VIP` ou une autre variante.

## 3. Premiers pas

Pour une première mise en service, suivez cet ordre :

1. créez un profil NovaBOT ;
2. connectez Telegram ;
3. créez le bot et le groupe Telegram privé du profil ;
4. sélectionnez les sources dans **TRANSFERT TELEGRAM** ;
5. configurez le filtrage Telegram ;
6. connectez le compte MT5 ;
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
- MT5 affiche le bon login et le bon serveur ;
- le trading algorithmique est autorisé ;
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
- compte MT5 ;
- sources Telegram sélectionnées ;
- règles de filtrage ;
- Money Management ;
- dictionnaire de commandes Smart ;
- historiques, mappings, statistiques et réglages de supervision.

Une modification faite dans un profil ne doit donc pas être considérée comme active dans les autres profils.

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

Lorsque Telegram, le bot, le groupe privé, les sources et MT5 sont prêts, cliquez sur **ACTIVER L'ÉCOUTE**.

NovaBOT transfère alors les messages admis vers le groupe privé et soumet les signaux reconnus au parcours MT5. Cliquer de nouveau désactive l'écoute.

### 5.7 Récupérer manuellement un ancien signal

Le bouton **RÉCUPÉRER UN TRADE DEPUIS UN MESSAGE** permet de parcourir une conversation, choisir un ancien message et l'envoyer dans le parcours de traitement.

Cette action reste soumise aux règles du profil actif. Les tolérances et options prises en compte sont celles enregistrées au moment de la récupération.

## 6. Connecter MetaTrader 5

### 6.1 Préparer MT5

Avant la connexion NovaBOT :

1. installez et ouvrez le terminal MT5 du broker ;
2. vérifiez que le compte peut se connecter dans MT5 ;
3. activez le trading algorithmique ;
4. affichez les symboles utiles dans l'observation du marché.

### 6.2 Connexion dans NovaBOT

1. Ouvrez l'onglet **MetaTrader 5**.
2. Choisissez le chemin du terminal détecté. Utilisez le sélecteur si plusieurs installations existent.
3. Saisissez le login, le mot de passe et le serveur.
4. Cliquez sur **DÉCOUVRIR SERVEURS** si le serveur n'est pas proposé.
5. Cliquez sur **CONNECTER METATRADER**.
6. Contrôlez dans la console le nom du compte, le login, le serveur, le solde et l'état du trading algorithmique.

NovaBOT surveille ensuite la présence du terminal et actualise l'interface en cas de déconnexion.

### 6.3 Symboles et alias broker

Après la connexion :

- **COLLECTER LES SYMBOLS** enregistre les informations des symboles disponibles ;
- **GÉNÉRER ALIAS (BROKER)** construit les correspondances entre les noms usuels et les noms du broker.

Utilisez ces fonctions si Telegram publie `GOLD` ou `XAUUSD` alors que le broker utilise un suffixe particulier. Contrôlez toujours le symbole indiqué dans la prévisualisation d'exécution.

### 6.4 Tester sans Telegram

La zone de saisie manuelle MT5 permet de soumettre un texte de signal au même parser et au même parcours d'exécution. Utilisez un compte de démonstration et un lot minimal pour valider les alias et le Money Management.

## 7. Configurer le Money Management

Ouvrez l'onglet MT5 puis cliquez sur **MONEY MANAGEMENT**. Les réglages sont propres au profil.

Les icônes `?` donnent la description du paramètre correspondant.

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

Contrôlez les quatre valeurs affichées : solde réel MT5, capital virtuel, capital protégé et base de calcul du risque.

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

Si le minimum ou le pas broker empìhe une vraie réduction, l'exécution est bloquée au lieu de conserver silencieusement le même volume.

### 7.5 Entrée fractionnée et groupes

L'entrée fractionnée se configure séparément pour chaque groupe sélectionné dans **TRANSFERT TELEGRAM**.

Pour une zone MARKET admissible et suffisamment large, NovaBOT peut créer :

- une branche MARKET ;
- une branche LIMIT dans la zone.

Le volume disponible est partagé entre les deux branches. Si le lot de chaque TP ne permet pas au moins deux volumes conformes au minimum broker, le fractionnement n'est pas appliqué.

**Autoriser les LIMIT après TP1** détermine le sort des LIMIT sœurs :

- option désactivée : les LIMIT encore non déclenchées sont annulées après confirmation MT5 du TP1 MARKET ;
- option activée : les LIMIT restent actives.

## 8. Configurer les commandes Smart

Cliquez sur **AUTOMATISATION TELEGRAM** dans le module MT5.

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
2. le parent doit être corrélé à un batch MT5 ;
3. le symbole et le batch doivent être identifiables sans ambiguïté ;
4. les commandes Modify SL/TP doivent contenir une nouvelle valeur numérique exploitable.

Une clôture de moitié n'est pas exécutée si le volume restant ou le volume à fermer est incompatible avec le minimum et le pas du broker.

## 9. Configurer le Copy Trader

Le Copy Trader utilise une source MT5 et une cible MT5 ou MT4.

### 9.1 Choisir la source

Deux modes sont disponibles :

- saisir un compte MT5 source directement dans le Copy Trader ;
- cocher **UTILISER LE COMPTE DU MODULE MT5**.

Le second mode est recommandé lorsque la source est le même compte que celui déjà connecté dans le module MT5. Il partage la session existante et évite deux initialisations concurrentes du même terminal.

### 9.2 Choisir la cible

1. Choisissez la plateforme cible : **MT5** ou **MT4**.
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

## 10. Installer le bridge MT4

La cible MT4 utilise l'EA `NovaBot_MT4_Slave_ZMQ.mq4` et les composants mql-zmq.

### Installation depuis NovaBOT

1. Ouvrez l'onglet **Copy Trader**.
2. Choisissez **MT4** comme plateforme cible.
3. Cliquez sur **INSTALLER LE PONT MT4**.
4. Confirmez l'installation.
5. Laissez le script PowerShell rechercher automatiquement les dossiers de données MT4.
6. S'il trouve plusieurs installations, choisissez celle correspondant au terminal cible.
7. Si aucune installation n'est détectée, indiquez manuellement le dossier de données demandé.

Le script installe ou met à jour :

- l'EA NovaBOT dans `MQL4/Experts` ;
- les fichiers include mql-zmq ;
- les DLL nécessaires ;
- la licence tierce associée.

### Compilation et activation

1. Ouvrez MT4 puis MetaEditor.
2. Ouvrez `NovaBot_MT4_Slave_ZMQ.mq4`.
3. Compilez avec **F7**.
4. Vérifiez l'absence d'erreur de compilation.
5. Placez l'EA sur un graphique.
6. Autorisez le trading automatique et les imports DLL.
7. Laissez le graphique et l'EA actifs.

La console doit afficher successivement la préparation du pont, l'attente de l'EA puis `[TGT] Connecté`.

Le bridge utilise localement les ports 6001 et 6002. NovaBOT peut récupérer le port d'un ancien worker NovaBOT invisible lorsqu'il est identifié avec suffisamment de certitude. Il ne ferme pas volontairement une instance visible ou un processus étranger.

Après une mise à jour de l'EA du dossier projet, relancez l'installation puis recompilez l'EA dans MetaEditor pour que MT4 utilise la nouvelle version.

## 11. Utiliser NovaBOT Companion

NovaBOT Companion consulte l'état d'un profil Desktop. Il n'envoie aucune commande Telegram, Smart ou MT5.

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

Le Dashboard présente les statistiques par source Telegram à partir des événements et deals attribuables dans MT5.

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
- la connexion automatique à MT5 au démarrage ;
- le mode d'affichage ;
- la taille des caractères ;
- la supervision distante.

Les thèmes disponibles sont clair, sombre et bleu clair. NovaBOT adapte sa fenêtre et ses onglets aux changements d'écran et de mise à l'échelle Windows.

L'icône **ℹ️ À propos** présente la version, le build, le copyright, le lien GitHub et les principaux composants tiers.

## 14. Comprendre les notifications

Les principales notifications Telegram distinguent :

- **Signal exécuté** : positions MARKET confirmées ;
- **Signal placé** : ordres pending acceptés mais pas encore déclenchés ;
- **Signal exécuté partiellement** : une partie des ordres a réussi ;
- **Signal non exécuté** : aucun ordre confirmé ;
- **TP atteint et confirmé** : preuve MT5 disponible ;
- **Break Even appliqué** : modification du SL confirmée ;
- **Ordres LIMIT annulés** : suppression confirmée par MT5 ;
- **Trade terminé** : aucune opération active ne reste dans le batch suivi.

Le prix affiché pour un MARKET correspond au prix d'exécution. Le prix d'un pending correspond au prix de placement de l'ordre.

## 15. Procédure quotidienne recommandée

### Au démarrage

1. lancez le bon profil ;
2. contrôlez Telegram et MT5 ;
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

- contrôlez la connexion MT5 et le trading algorithmique ;
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
- les journaux Telegram et MT5 correspondants ;
- les tickets et retcodes confirmés par le terminal.

Un comportement observé dans un profil ne permet pas de conclure que les autres profils utilisent les mêmes règles.
