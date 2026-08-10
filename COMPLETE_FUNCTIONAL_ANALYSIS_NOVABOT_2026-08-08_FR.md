🌐 Language: [Français](./COMPLETE_FUNCTIONAL_ANALYSIS_NOVABOT_2026-08-08_FR.md) | [English](./COMPLETE_FUNCTIONAL_ANALYSIS_NOVABOT_2026-08-08_EN.md)

---

# Analyse fonctionnelle complète de NovaBOT

Date de l’analyse initiale : 8 août 2026  
Dernière vérification et mise à jour : 10 août 2026  
Source de vérité : code présent dans `NOVABOT-WORKING v225`  
Nature du document : description fonctionnelle sans modification du code

## 1. Périmètre, méthode et niveau de preuve

L’analyse porte sur les fichiers réellement présents dans l’arborescence fournie : point d’entrée, modules PyQt5, intégrations Telegram et MetaTrader, workers, Expert Advisor MT4, supervision, persistance, langues, scripts et tests.

La lecture a été conduite à partir :

- des appels et câblages partant de `main.py` ;
- des façades publiques et de leurs implémentations ;
- des valeurs par défaut effectivement chargées ;
- des écritures persistantes et de leurs consommateurs ;
- des handlers Telethon, timers Qt, workers et transports ;
- des tests de caractérisation et de non-régression ;
- d’une compilation et d’une exécution réelles de la suite de tests.

Un nom de fichier ou de classe n’a pas été considéré comme une preuve suffisante. Les statuts utilisés dans ce rapport sont :

| Statut | Signification |
|---|---|
| Branché | Le composant est instancié ou appelé dans le fonctionnement courant. |
| Facultatif | Le composant est branché, mais dépend d’une option ou d’une action utilisateur. |
| Désactivé par défaut | La fonction est présente mais son réglage initial empêche son action. |
| Compatibilité | Le code conserve un contrat historique, un format ou une façade. |
| Non branché | Le code existe, mais aucun appel de production courant n’a été constaté. |

### 1.1 Inventaire

L’arborescence contient 310 fichiers utiles au moment de l’inventaire :

- 190 fichiers Python, dont 125 applicatifs/techniques et 65 modules de test ;
- 80 fichiers JSON ;
- 18 images PNG ;
- 11 fichiers Markdown ;
- 5 fichiers BAT ;
- 2 scripts PowerShell ;
- 1 fichier SPEC PyInstaller ;
- 1 icône Windows ;
- 1 Expert Advisor MQ4 ;
- 1 notice texte.

Les 80 fichiers de langue correspondent à 8 domaines dans 10 langues. La suite comprend 65 modules `test_*.py` et 800 méthodes de test.

### 1.2 Évolutions fonctionnelles vérifiées dans la révision v225

La présente révision intègre dans les sections concernées les comportements supplémentaires réellement branchés dans le code actuel :

- association persistante du groupe Telegram de destination au compte Telegram qui l’a créé, avec détection et rétablissement contrôlé après changement de compte ;
- réinitialisation confirmée des seuls paramètres du Money Management vers leurs valeurs par défaut ;
- conversion automatique en ordre LIMIT d’une entrée unique devenue trop éloignée lorsque l’auto-LIMIT est actif ;
- option « Ajuster les TP à l’entrée réelle », globale ou par groupe, pour les entrées uniques, multiples et fractionnées, hors ordres STOP ;
- mode d’entrée fractionnée commun à tous les groupes, en plus du mode historique par groupe ;
- proposition d’activation du trading algorithmique depuis l’assistant, précédée du contrôle du Runtime Visual C++ adapté à l’architecture Windows ;
- application du réglage de taille des caractères aux fenêtres et boîtes de dialogue, à l’exception volontaire de la gestion des profils ;
- bouton Paramètres dans le module Copy Trader ;
- classification explicite des clôtures au prix d’entrée quand un TP a volontairement été déplacé sur ce prix ;
- suivi MT5 plus tolérant aux indisponibilités transitoires du tick, sans fabriquer de prix ni de preuve de clôture.

## 2. Finalité générale et architecture fonctionnelle

NovaBOT est une application de bureau Windows en PyQt5 qui relie Telegram à MetaTrader 5, suit le cycle de vie des opérations et peut répliquer un compte MT5 vers un autre compte MT5 ou vers MT4.

Ses sous-systèmes principaux sont :

1. la gestion des profils et de l’interface ;
2. la réception, le filtrage et la copie Telegram ;
3. le parsing des signaux et des commandes Smart ;
4. la résolution des symboles broker ;
5. l’admission, le Money Management et la validation ;
6. l’exécution et les actions MT5 ;
7. le lifecycle, les notifications et le Dashboard ;
8. le Copy Trader ;
9. la supervision distante en lecture seule.

Le câblage principal est réalisé dans `main.py` :

- `TelegramApp.new_trade_text` alimente `MT5App.process_external_message` ;
- le détecteur Smart du module MT5 est injecté dans Telegram ;
- `MT5App.trade_outgoing_text` alimente l’envoi vers le groupe Telegram privé ;
- les accusés d’enregistrement de l’outbox reviennent au lifecycle MT5 ;
- les sources Telegram sélectionnées alimentent la barre d’identité et le Dashboard ;
- le Copy Trader reçoit l’instance MT5 principale afin de pouvoir partager sa connexion source ;
- la supervision observe les modules sans devenir propriétaire de leurs décisions.

## 3. Démarrage, profils et fenêtre principale

### 3.1 Démarrage

Le point d’entrée :

- active `multiprocessing.freeze_support()` pour les builds PyInstaller ;
- applique un AppUserModelID Windows ;
- accepte `--profile <nom>` pour un lancement direct ;
- initialise la langue, le thème, la typographie et l’adaptation d’affichage ;
- diffère l’import des modules lourds jusqu’à la sélection du profil ;
- affiche un placeholder si MT5 ou le Copy Trader est indisponible au lieu de bloquer Telegram et le Dashboard.

La bibliothèque `cryptography` est requise pour les secrets. Son absence est signalée ; la disponibilité du Copy Trader dépend en plus de Windows et de `MetaTrader5`.

### 3.2 Profils

Le dialogue de profils permet de :

- créer, choisir, renommer indirectement via les fonctions de gestion, supprimer, importer et exporter un profil ;
- sélectionner langue et thème avant l’ouverture ;
- démarrer un profil précis avec l’argument CLI.

Chaque profil est stocké sous `%USERPROFILE%\.novabot\profiles\<profil>` avec les sous-dossiers :

- `data` ;
- `logs` ;
- `secrets` ;
- `sessions` ;
- `mt5_sessions`.

Un verrou PID empêche l’ouverture simultanée du même profil et sa suppression pendant son utilisation. Des profils différents peuvent fonctionner en parallèle. L’import ZIP rejette les chemins absolus, les traversées de répertoire et les structures non reconnues ; un nom disponible est choisi en cas de conflit.

### 3.3 Interface principale

La fenêtre contient quatre onglets :

- Telegram ;
- MetaTrader 5 ;
- Dashboard ;
- Copy Trader.

La barre supérieure affiche le profil, les sources écoutées, l’état MT5, une icône d’état et un bouton de configuration du profil. La couleur de ce bouton représente la check-list : rouge si aucune étape n’est prête, orange si elle est partielle et vert si elle est complète.

À la fermeture, NovaBOT arrête les workers Copy Trader, les timers et threads concernés, l’observateur et le transport de supervision, puis libère le verrou du profil.

### 3.4 Assistant de configuration

L’assistant est branché pour les nouveaux profils et reste rouvrable depuis la barre globale. Il ne recrée pas les écrans métier : il ouvre les modules existants.

Les huit étapes sont :

1. identifiants API Telegram ;
2. connexion Telegram complète et autorisée ;
3. bot et groupe privé NovaBOT ;
4. au moins une source Telegram ;
5. existence des réglages de filtrage ;
6. connexion MT5 ;
7. existence d’un document `mm_settings` ;
8. écoute Telegram activée.

La progression est persistée dans `data/onboarding.json`. Une étape déjà vérifiée reste inscrite dans l’historique, tandis que `ready_now` reflète l’état courant. La fin positionne le statut `complete`, désactive l’ouverture automatique et remplace l’action « Reprendre plus tard » par « Fermer ». « Passer la configuration guidée » place le document en état `skipped` sans configurer les modules.

Après une connexion MT5, l’assistant vérifie aussi l’autorisation de trading algorithmique. Si elle est désactivée, il réutilise l’activateur déjà présent dans le module MT5. Avant cette tentative, `detect_visual_cpp_runtime()` détermine l’architecture Windows et lit le registre du Runtime Visual C++ 14 ; si le runtime requis manque, une boîte de dialogue explique le prérequis et propose le lien Microsoft x64 ou x86 correspondant. Cette assistance ne crée pas une neuvième étape et ne considère jamais l’activation comme acquise sans confirmation du terminal.

Le Copy Trader, le bridge MT4 et NovaBOT Companion sont volontairement hors de cette check-list.

## 4. Préférences, langues, thèmes et affichage

Les langues présentes sont le français, l’anglais, l’espagnol, l’allemand, l’italien, le portugais, l’arabe, le chinois, le japonais et le russe. Les domaines sont `main`, `telegram`, `mt5`, `copy_mt5`, `dashboard`, `documentation`, `notifications` et `onboarding`.

La langue peut être automatique ou manuelle. La détection système est mise en cache pendant le processus et se replie sur le français. Les préférences globales sont dans `.novabot/config.json`.

Trois thèmes sont implémentés : clair, sombre et bleu clair. Six modes d’affichage existent : compact, équilibré, étendu, zoom, original et automatique. Le mode initial est `original`. Les tailles de caractères sont normale, moyenne et grande.

L’adaptation d’affichage réapplique géométrie, métriques des onglets et typographie lors des changements d’écran ou d’échelle. Le réglage de taille des caractères est propagé aux fenêtres et dialogues de l’application, y compris About et Copy Trader ; la fenêtre de gestion des profils est volontairement exclue. Chaque module possède un logo adapté au thème, un dialogue « About NovaBOT » commun et, dans la v225, le Copy Trader expose également le bouton Paramètres partagé.

Le dialogue About affiche actuellement, dans le code :

- version `v2026.0.200` ;
- build `200` ;
- copyright `Copyright © 2026 NovaBOT by MintoLabs` ;
- lien « View on GitHub » vers `https://github.com/MintoLabs-AI/` ;
- mql-zmq, Telethon, PyQt5 et ZeroMQ.

## 5. Telegram

### 5.1 Connexion et secrets

Telegram utilise Telethon. La connexion requiert API ID, API Hash, numéro de téléphone et code Telegram. Les identifiants sont chiffrés dans `secrets/credentials.enc` avec une clé Fernet dans `secrets/master.key`. La session Telethon est propre au profil dans `sessions`.

Le bouton de modification des API est disponible hors connexion et masqué lorsque Telegram est connecté. Une combinaison API ID/API Hash invalide déclenche le parcours de récupération des identifiants. L’état « connecté » de l’assistant exige une session réellement autorisée, pas seulement un client créé.

### 5.2 Reconnexion

Le watchdog distingue une déconnexion inattendue d’une déconnexion demandée. Il évite les tentatives concurrentes et utilise les délais `0, 2, 5, 10, 30` secondes. Après restauration, il réinstalle les handlers si l’écoute était activée. Une session révoquée ou nécessitant une nouvelle authentification est signalée comme intervention requise.

### 5.3 Bot et groupe privé

Le groupe privé de destination n’est plus identifié uniquement par `group_id.txt`. `telegram_destination.json` associe le compte Telegram courant à l’identifiant marqué du peer, au channel ID brut, à l’access hash, au titre du groupe et au nom du bot. Au chargement et après authentification, NovaBOT contrôle que cette liaison appartient bien au compte connecté et tente de résoudre l’entité réelle. Un changement de compte, une entité inaccessible ou une liaison invalide produit un état explicite au lieu de réutiliser aveuglément une ancienne entité. La liaison est écrite atomiquement et l’ancien identifiant texte reste un support de compatibilité.

Le parcours de création dialogue avec BotFather pour créer le bot, définir description, texte « À propos » et photo. Il crée un groupe privé, configure son identité, ajoute le bot, puis confirme sa promotion comme administrateur avec plusieurs vérifications temporisées.

L’état de l’assistant devient prêt lorsque les identifiants bot existent et que `group_id.txt` contient un identifiant non nul. La suppression propose un nettoyage local et des actions côté Telegram/BotFather ; les échecs externes restent visibles.

### 5.4 Sources et écoute

L’utilisateur sélectionne canaux, groupes ou conversations utilisateur. La sélection est persistée dans `data/selected_chats.json` et sert aussi aux interfaces « Entrée fractionnée », dérogation d’exécution et décalage de TP par groupe.

Quand l’écoute est activée, trois handlers Telethon sont installés sur les sources choisies :

- nouveau message ;
- message édité ;
- message supprimé.

Un changement de sélection peut rafraîchir les handlers sans recréer la session Telegram.

### 5.5 Filtrage

Le filtre peut être global ou propre à chaque source. Ses critères portent sur :

- présence d’une valeur numérique autonome ;
- présence d’un marqueur TP ;
- présence d’un marqueur SL ;
- texte, image, document, vidéo, vocal, GIF, sticker et sondage ;
- présence d’une légende pour les médias ;
- réponses ;
- messages déjà traités ;
- réanalyse des messages édités ;
- affichage et journalisation des refus.

Les valeurs par défaut du code sont : valeur numérique, TP et SL requis ; texte et réponses autorisés ; images et documents légendés non autorisés ; principaux autres médias filtrés ; doublons filtrés ; messages édités réanalysés si leur empreinte a changé ; refus et raisons affichés et journalisés.

Le registre des messages traités est borné à 5 000 identités. L’identité est `source_id:message_id` et l’empreinte dépend du type de contenu et du texte. Les décisions sont écrites dans `logs/telegram_filter.log` lorsque l’option correspondante est active. Une erreur de journalisation ne change pas la décision.

Un BUY STOP ou SELL STOP n’est pas confondu avec un Stop Loss : `STOP` n’est retenu comme SL que lorsqu’il porte une valeur et n’est pas immédiatement précédé de BUY/SELL.

Les commandes Smart reconnues dans une réponse peuvent traverser les critères structurels du signal, mais restent soumises à l’autorisation des réponses. Une commande Smart Telegram sans parent n’est pas exécutée.

### 5.6 Transfert et copie

Après admission, Telegram émet d’abord l’enveloppe vers MT5. Le message est ensuite transféré ou copié vers le groupe privé, selon les capacités de la source. Les sources protégées contre le transfert utilisent une copie du texte ou du média. La correspondance source/destination est persistée dans `telegram_forward_map.json` afin de conserver les fils de réponses.

Une commande Smart traitée est représentée par sa notification métier et n’est pas recopiée comme un simple texte. Les messages ordinaires produisent un statut « Transfert auto » uniquement lorsqu’un message destination a réellement été envoyé.

La récupération manuelle d’un ancien message passe par le même pipeline de parsing/exécution, avec un contexte source construit explicitement.

### 5.7 Messages édités et correction de signal

Si le filtre autorise une édition dont l’empreinte a changé, le message est reparsé. Une édition reconnue comme signal ne crée pas automatiquement un nouveau batch : `TelegramSignalCorrectionService` recherche le batch corrélé au message original.

La correction :

- exige exactement une instruction parsée ;
- exige le même symbole, le même sens, la même zone/entrée et le même nombre de TP ;
- peut modifier le SL des positions et pending du batch ;
- peut modifier un TP existant ;
- peut recréer l’opération d’un TP manquant avec son volume et sa branche MARKET/LIMIT d’origine ;
- applique aussi le décalage de TP configuré ;
- met à jour le plan persistant uniquement si aucune action n’a échoué ;
- publie un résumé avec niveaux, opérations modifiées, opérations ajoutées et échecs.

Une modification d’identité ou un batch absent est refusé explicitement. La tentative d’éditer la copie Telegram peut échouer si le message destination n’est pas éditable ; cette erreur n’annule pas les modifications MT5 déjà confirmées.

### 5.8 Audit des suppressions

Le handler de suppression est passif : il n’exécute aucun trade, ne publie rien dans Telegram et n’ajoute rien à la console métier.

Il conserve au plus 5 000 messages dans `telegram_message_deletion_cache.json`, journalise les suppressions dans `telegram_deleted_messages.log`, effectue une rotation à 2 Mio et garde 250 suppressions récentes. Si le même texte normalisé réapparaît avec un autre ID dans la même source sous 15 minutes, un événement local `possible_message_recreation` est écrit. Il s’agit d’un diagnostic, pas d’une règle anti-doublon.

### 5.9 Corrélations et outbox

NovaBOT relie source, message source, copie privée, parent, batch, symbole, sens et magic number. Les commandes Smart et notifications utilisent cette corrélation ; un parent sans batch ne se replie pas sur un autre trade.

Les notifications passent par une outbox SQLite propre au profil. Les états incluent `waiting_context`, `pending`, `sending`, `retry_wait`, `sent`, `dead_letter` et `discarded`. L’outbox :

- attend le contexte de réponse lorsque nécessaire ;
- loue les envois afin d’éviter les doublons concurrents ;
- applique des nouvelles tentatives ;
- permet actualisation, relance, abandon ou réaffectation depuis l’interface ;
- sauvegarde une base corrompue avant reconstruction ;
- accuse réception au lifecycle après enregistrement/envoi.

Lorsqu’une destination équivalente est rétablie, les notifications en attente peuvent être réaffectées vers la liaison valide. La réaffectation ne change ni leur contenu ni leur clé de déduplication et les échecs de maintenance de l’outbox restent fail-open vis-à-vis du trading.

## 6. Parser et symboles

### 6.1 Parsing

`SignalParser` transforme un texte en une ou plusieurs instructions contenant :

- symbole ;
- BUY ou SELL ;
- intention MARKET, LIMIT ou STOP ;
- entrée unique ou zone ;
- Stop Loss ;
- jusqu’à quatre Take Profits numériques.

Il reconnaît les variantes BUY/SELL LIMIT et BUY/SELL STOP, des formulations françaises et anglaises, emojis, ponctuations, zones inversées et certaines bornes abrégées. Un TP textuel comme « ouvert » n’est pas transformé en prix.

Le parser possède un parcours principal structuré et un parcours générique de compatibilité. `diagnose_message()` distingue l’absence de signal d’une tentative incomplète et peut détailler symbole, direction, entrée, SL, TP ou format manquants.

### 6.2 Catalogue et résolution broker

La résolution donne la priorité à un symbole exact et pleinement négociable. Sinon, elle utilise :

- `symbol_aliases.json` ;
- des familles intégrées pour Forex, métaux, indices, énergies, matières premières et cryptomonnaies ;
- les préfixes/suffixes broker observés ;
- les symboles réellement exposés par `symbols_get()` ;
- le `trade_mode`, avec priorité au mode pleinement négociable.

Les croisements tels que BTCXAU ou ETHXAU sont exclus des candidats GOLD. Les alias peuvent être régénérés depuis le broker. Les métadonnées symboles sont collectées dans `mt5_sessions/symbol_info.json`; les anciens fichiers horodatés restent lisibles en repli puis sont supprimés après une collecte canonique réussie.

Le symbole broker final est résolu avant la validation et l’exécution.

## 7. Connexion MetaTrader 5

### 7.1 Découverte et connexion

La découverte centrale cherche les terminaux MT4 et MT5 dans les emplacements Windows usuels et les données système. Le catalogue de serveurs est partagé avec le Copy Trader ; celui-ci n’a pas de catalogue de secours autonome.

La connexion MT5 utilise chemin du terminal, login, mot de passe et serveur. Les identifiants sont chiffrés dans `secrets/credentials.json` avec `secrets/key.key`. La connexion :

- initialise la bibliothèque MetaTrader5 ;
- confirme le compte ;
- charge/collecte les symboles ;
- charge ou génère les alias ;
- restaure le lifecycle persistant ;
- publie l’état à l’interface et à la supervision.

Le watchdog vérifie le terminal et le compte. Une perte de session désactive les nouvelles exécutions et la source partagée du Copy Trader.

### 7.2 Préparation au trading

Avant l’ordre, NovaBOT contrôle l’état du terminal, du compte et des permissions. Si Algo Trading est désactivé dans un parcours Telegram, il publie l’information, demande l’autorisation de l’utilisateur puis peut chercher et activer le contrôle Windows du terminal. Le signal initial reste bloqué après cette demande : il n’est pas envoyé silencieusement pendant l’interaction.

Depuis l’assistant, la même activation est proposée après connexion. Le Runtime Visual C++ requis est contrôlé en lecture seule dans le registre Windows ; la boîte de dialogue fournit le lien officiel correspondant à l’architecture détectée si l’installation manque.

Le module permet aussi la saisie manuelle d’un texte, qui utilise le parser et le pipeline d’exécution.

## 8. Money Management

### 8.1 Valeurs par défaut réelles

Les valeurs initiales de `DEFAULT_MM_SETTINGS` sont notamment :

| Paramètre | Valeur |
|---|---:|
| Mode | lot fixe par TP (`fixed_all`) |
| Lot fixe | 0,10 |
| Lots TP1–TP4 | 0,10 chacun |
| Risque | 1 % |
| Répartition égale | désactivée |
| Déviation | 5 points |
| Conversion automatique LIMIT | désactivée |
| Seuil d’écart | 100 points |
| Tolérance automatique de zone | désactivée |
| Tolérance historique | 100 points |
| Break Even / suivi TP / spread / validation | désactivés |
| Anti-doublon / limite de trades | désactivés |
| Nombre maximal | 3 |
| Pending inclus | désactivé |
| Capital virtuel / coffre / progressive TP / décalage TP | désactivés |

Les valeurs affichées en repli dans certains widgets ne remplacent pas ces clés lorsque le document par défaut complet est chargé.

### 8.2 Calcul de volume

Trois modes sont branchés :

- un lot fixe appliqué à chaque TP, ou partagé entre les TP si l’option est active ;
- un lot distinct pour TP1 à TP4 ;
- un volume calculé depuis le pourcentage de risque, l’entrée, le SL et les propriétés du symbole.

Les volumes sont normalisés selon `volume_min`, `volume_max` et `volume_step`. Le calcul principal appartient à `MoneyManagementVolumePlanner`, pas au validateur.

### 8.3 Capital, capital virtuel et coffre

La base de risque est l’equity MT5, sauf si un capital virtuel strictement positif est activé. Le coffre calcule des paliers protégés à partir du pas configuré et persiste le capital protégé. `get_risk_base()` retourne cependant le capital virtuel complet : le montant protégé n’est pas soustrait de la base de risque.

### 8.4 Exécution et contrôles

Les options comprennent :

- déviation ;
- conversion automatique en LIMIT ;
- seuil de distance ;
- tolérance automatique selon la famille d’actifs ;
- adaptation SL/TP au spread broker ;
- anti-doublon symbole/sens/SL/TP avec tolérance de prix ;
- nombre maximal de positions, avec pending facultatifs ;
- validation équilibrée ou stricte ;
- exécution sans condition par groupe.

La dérogation par groupe ne devient active que si la conversion automatique LIMIT ou la tolérance automatique est cochée. Elle force la branche MARKET à travers la règle de distance ; elle ne contourne pas les contrôles du terminal, le volume, la validation, l’anti-doublon ou le nombre maximal.

Le dialogue contient aussi « Réinitialiser les paramètres par défaut ». L’action exige une confirmation, remplace uniquement le document Money Management par une copie indépendante de `DEFAULT_MM_SETTINGS`, le repersiste pour le profil et ferme le dialogue afin d’éviter qu’un ancien état de widgets ne réécrive les valeurs réinitialisées.

### 8.5 Sécurisation

Les réglages branchés sont :

- Break Even après TP1 avec offset en points ;
- déplacement SL à TP1 après TP2 ;
- déplacement SL au TP précédent pour TP2 à TP4 ;
- sécurisation après un gain en pips, avec seuil et offset ;
- clôtures partielles progressives ;
- décalage des Take Profits.

Les clôtures progressives démarrent après confirmation de TP1 et agissent sur les positions restantes. Les modes sont 25/25/25/25, 40/20/20/20, 50/20/20/10 ou personnalisé à 100 %. Le volume initial doit être strictement supérieur au seuil normalisé, au minimum 0,02. Les arrondis préservent un reliquat négociable ; le reliquat peut être fermé au dernier TP.

### 8.6 Décalage des Take Profits

Cette fonction est désactivée par défaut. Elle peut s’appliquer à tous les groupes ou par groupe, avec un décalage propre et une sélection TP1–TP4.

Elle rapproche uniquement les TP valides de l’entrée : soustraction pour un BUY dont le TP est au-dessus, addition pour un SELL dont le TP est en dessous, sans franchir l’entrée. Un marqueur interne empêche une double application. Elle est utilisée dans le signal initial et dans la correction d’un message édité.

### 8.7 Ajustement des TP à l’entrée réelle

Cette option distincte est désactivée par défaut et peut être activée pour tous les groupes ou individuellement pour les sources sélectionnées dans Telegram. Elle marque le plan avant le fractionnement afin que les entrées uniques, multiples et les deux branches d’une entrée fractionnée conservent la politique. Les ordres STOP sont explicitement exclus.

Pour chaque TP, NovaBOT conserve la distance du plan original : `TP ajusté = TP de référence + (entrée réellement confirmée - entrée prévue)`. Pour un MARKET, l’ajustement est appliqué avec le prix retenu à l’exécution ; pour un pending déclenché, le lifecycle utilise le `price_open` confirmé puis envoie une modification SL/TP. Les tickets déjà ajustés sont persistés pour éviter une double translation. La correction d’un signal édité reconstruit également les références avant de modifier ou recréer une opération manquante.

## 9. Admission et type d’ordre

Pour MARKET, NovaBOT utilise ask pour BUY et bid pour SELL. `usable_tick()` effectue jusqu’à deux lectures avant de considérer le tick indisponible.

Les pending explicites conservent leur intention : BUY LIMIT sous le marché, SELL LIMIT au-dessus, BUY STOP au-dessus et SELL STOP sous le marché. Une géométrie explicite du mauvais côté est refusée.

Pour une instruction MARKET, la politique compare le marché à l’entrée ou à la zone. Une entrée unique favorable ou dans la tolérance reste MARKET. Si sa dérive défavorable dépasse la tolérance, elle est convertie en BUY/SELL LIMIT au prix prévu lorsque l’auto-LIMIT est actif ; elle n’est bloquée avec la raison « entrée unique trop éloignée » que lorsque cet automatisme est désactivé. Les pending explicitement LIMIT ou STOP conservent leur intention et ne passent pas par cette conversion. Les tolérances automatiques sont proportionnelles au prix :

| Famille | Ratio |
|---|---:|
| Forex | 0,02 % |
| Métaux | 0,05 % |
| Indices | 0,05 % |
| Énergies | 0,10 % |
| Cryptomonnaies | 0,10 % |

La tolérance historique en points sert lorsque l’automatisme est désactivé ou inexploitable. `ExecutionAdmissionPolicy.is_tradable()` retourne actuellement toujours vrai : le libellé historique de blocage week-end ne correspond donc pas à un contrôle calendaire actif.

## 10. Entrée fractionnée

Le fractionnement possède deux modes exclusifs : « Tous les groupes » et « Par groupe ». Le mode historique par groupe reste la valeur par défaut pour préserver les profils existants. Le mode global applique une même configuration complète à toutes les sources : activation, largeur minimale de zone, autorisation des LIMIT après TP1 et déplacement des TP de l’entrée 1 à son prix d’entrée. Les deux documents de configuration sont conservés séparément lors d’un changement de mode. Dans tous les cas, il faut une véritable zone dont la largeur atteint le minimum applicable et un STOP explicite n’est jamais fractionné.

Pour un BUY, l’entrée 1 est la borne haute et l’entrée 2 la borne basse. Pour un SELL, l’entrée 1 est la borne basse et l’entrée 2 la borne haute. Un signal MARKET produit une branche MARKET et une branche LIMIT ; un signal explicitement LIMIT garde deux branches LIMIT. Les volumes de chaque TP sont d’abord calculés, doivent pouvoir être divisés en deux lots broker valides, puis sont partagés entre les branches.

Les deux branches partagent batch, magic, SL, TP et contexte Telegram, mais conservent les rôles `entry_1` et `entry_2`.

Comportements associés :

- si TP1 de la branche MARKET est confirmé par l’historique broker, les LIMIT sœurs non déclenchées sont annulées lorsque « Autoriser les LIMIT après TP1 » est désactivé ;
- la notification TP1 est publiée avant la notification d’annulation ;
- si l’option est activée, les LIMIT restent présentes ;
- fermer manuellement l’unique MARKET d’un batch composite peut annuler l’unique LIMIT et l’indiquer dans la notification finale ;
- si « Placer le TP de l’entrée 1 à son prix d’entrée » est activé, le déclenchement réel de l’entrée 2 déplace les TP de toutes les positions encore ouvertes de l’entrée 1 à leur propre prix d’ouverture ; les tickets réussis sont persistés et les échecs sont retentés lors des cycles suivants ;
- une fermeture manuelle de TP1 identifiée dans une branche n’applique le BE qu’aux positions sœurs de cette même branche. Les états de réussite et de retry sont séparés par rôle.

La validation est exécutée séparément pour chaque branche au moment de son envoi ; le moteur ne produit pas une décision de risque agrégée unique pour l’ensemble composite.

## 11. Validateur de signal

### 11.1 Activation et position dans le pipeline

Le validateur est branché mais désactivé par défaut. Le mode configuré est réellement utilisé : `balanced` ou `strict`.

Il intervient après :

- résolution du symbole broker ;
- choix MARKET/LIMIT/STOP ;
- adaptation finale des prix ;
- anti-doublon ;
- calcul des volumes.

Il intervient avant tout `order_send`. Une exception sur les données indispensables produit un BLOCK explicite. L’écriture de l’historique est fail-open et ne change jamais la décision.

### 11.2 Données de marché

`MarketContext` lit via `MT5BrokerFacts` : bid, ask, point, précision, 60 bougies M15 et 80 bougies H1. Il calcule ATR 14 ainsi que EMA 20/50 M15 et H1. Le snapshot indique aussi la session UTC ASIA, EUROPE ou US.

### 11.3 Règles

Les règles s’exécutent dans cet ordre :

1. métadonnées broker indispensables ;
2. géométrie direction/entrée/SL/TP ;
3. spread ;
4. tendance EMA H1 ;
5. rapport distance TP1/ATR M15 ;
6. validité des volumes broker ;
7. risque monétaire et budget du mode risque.

Les métadonnées, la géométrie, le volume ou un risque inexploitable sont bloquants. En mode équilibré, tendance ou ATR indisponible demandent une réduction. En mode strict, leur indisponibilité bloque. Une tendance opposée ou un TP1 trop proche/ambitieux pénalise le score ; le mode équilibré demande en plus une réduction explicite.

### 11.4 Familles et spread

| Famille | Équilibré | Strict | Ratio prix équilibré / strict |
|---|---:|---:|---:|
| Forex | 25 pts | 15 pts | aucun |
| Métaux | 60 pts | 40 pts | 0,015 % / 0,010 % |
| Indices | 100 pts | 60 pts | 0,020 % / 0,012 % |
| Cryptomonnaies | 2 500 pts | 1 500 pts | 0,050 % / 0,030 % |
| Énergies | 80 pts | 50 pts | 0,050 % / 0,030 % |
| Autres | 50 pts | 35 pts | aucun |

Pour les familles avec ratio, la limite effective est le maximum entre le plancher en points et `prix médian × ratio ÷ point broker`. Un spread nul ou absent demande REDUCE ; un spread supérieur bloque.

### 11.5 Décisions

- `ALLOW` : coefficient 1,0 ;
- `REDUCE` : coefficient 0,50 en équilibré ou 0,25 en strict, éventuellement abaissé par la règle de budget risque ;
- `BLOCK` : coefficient 0 et aucun ordre envoyé.

La réduction est appliquée à chaque volume par arrondi inférieur au pas broker. Si le volume réduit tombe sous le minimum, n’est pas strictement inférieur à l’original ou viole les bornes broker, REDUCE est transformé en BLOCK.

Chaque décision est écrite dans `logs/validation_history.csv` avec contexte Telegram, batch, score, raisons, résultats des règles, volumes et risques avant/après. BLOCK et REDUCE produisent des notifications contextualisées et des événements de supervision ; BLOCK marque aussi le batch analytique.

## 12. Exécution MT5

Le pipeline exact d’une branche est :

1. normalisation des paramètres historiques tuple/dict ;
2. résolution et activation du symbole ;
3. création ou reprise du batch et du magic ;
4. contrôles terminal/compte/Algo Trading ;
5. limite de trades ;
6. lecture du tick ;
7. choix du type et du prix ;
8. adaptation spread éventuelle ;
9. anti-doublon ;
10. calcul des volumes ;
11. validation facultative ;
12. envoi d’un ordre par TP numérique ;
13. enregistrement des tickets et armement du lifecycle ;
14. publication du résultat.

Les transports utilisent `MT5ActionGateway` et essaient les modes de remplissage compatibles. Les résultats conservent ticket, retcode, commentaire et message broker. L’interface distingue positions MARKET ouvertes, ordres pending placés, succès total, succès partiel et échec.

## 13. Actions MT5 et Smart Automations

### 13.1 Actions disponibles

`MT5PositionActionsMixin` fournit :

- sécurisation en pips ;
- Break Even ;
- modification SL ;
- modification TP ;
- déplacement SL vers un TP ;
- clôture complète ;
- clôture à 50 % ;
- clôture progressive ;
- suppression de pending.

Les sélections peuvent être limitées par symbole, sens, batch, magic et tickets. Une clôture à 50 % n’est pas envoyée si le volume minimal et le pas broker ne permettent pas une véritable réduction conservant un reliquat négociable.

### 13.2 Smart Automations

Sept commandes existent : Secure, Break Even, Close Half, Close, Modify SL, Modify TP et Edit. Le dictionnaire intégré français/anglais est fusionné avec `telegram_command_dictionary.json`. L’ordre de détection limite les chevauchements et les ambiguïtés sont signalées.

Chaque commande est désactivée tant que son option n’est pas activée dans les réglages. Le mode normalisé par défaut est `execution`; le mode `simulation` n’envoie aucune action.

Dans le flux Telegram :

- le message doit répondre à un parent ;
- le parent doit résoudre un batch ;
- symbole, sens, batch et magic bornent l’action ;
- aucun batch voisin n’est choisi en repli ;
- Modify SL/TP exige un prix ; Edit exige au moins un SL ou TP ;
- un succès n’est publié qu’après confirmation MT5 ;
- Smart Close agit sur positions et pending du batch ;
- Smart Close Half respecte le minimum broker.

Le docstring historique en tête de `SmartAutomationEngine` affirme encore que le flux ne déclenche qu’une simulation, mais le code exécuté appelle bien `SmartCommandExecutor` lorsque le mode est `execution`. Le docstring n’est donc plus une description fonctionnelle exacte.

## 14. Lifecycle des trades

### 14.1 État suivi

La watchlist `be_watchlist.json` conserve par batch : symbole, sens, entrée, SL initial, TP, tickets, magic, branches, contexte Telegram, snapshots, transitions, publications et états des automatismes. Elle est restaurée au redémarrage et resynchronisée avec positions, pending, ordres historiques et deals.

### 14.2 Preuves et classifications

Le lifecycle croise tickets, magic, commentaires, prix, retcodes, historiques et deals. Il classe notamment :

- Take Profit ;
- Stop Loss initial ;
- Break Even ;
- SL sécurisé ;
- fermeture manuelle ;
- Smart Close / Close Half ;
- progressive TP ;
- fermeture partielle externe ;
- pending annulé, expiré ou rejeté ;
- causes mixtes, inconnues ou preuves indisponibles.
- clôture au prix d’entrée lorsqu’un TP avait été volontairement déplacé sur ce prix.

Une disparition momentanée ne suffit pas à publier une clôture. Les événements de position passent par un état résolu/non résolu et les publications possèdent des identifiants persistés afin d’être dédupliquées. Une indisponibilité transitoire du tick ne fabrique aucun franchissement de TP : le suivi conserve le batch et attend une lecture exploitable ou une preuve historique.

### 14.3 Automatismes

- TP1 confirmé peut déclencher BE, clôtures progressives et suppression des LIMIT sœurs ;
- TP2–TP4 peuvent déplacer le SL selon le mode choisi ;
- une fermeture manuelle identifiée exactement comme TP1 peut déclencher BE ;
- si le niveau BE est momentanément invalide à cause du prix ou des stops broker, un retry est persisté avec délai ;
- le suivi de l’entrée fractionnée borne les actions à la branche concernée ;
- le déclenchement de l’entrée 2 peut déplacer les TP de l’entrée 1 à son prix d’ouverture ;
- une fermeture finale met à jour Money Management, Dashboard et supervision.

## 15. Notifications Telegram liées aux trades

Les notifications couvrent notamment :

- signal exécuté, pending placé, succès partiel ou refus ;
- validation réduite ou bloquée ;
- TP atteint et confirmé ;
- Break Even réussi, partiel, impossible ou différé ;
- fermeture manuelle ;
- Smart Close / Close Half ;
- suppression de LIMIT après TP1 ou fermeture manuelle du MARKET ;
- expiration, annulation ou rejet d’un pending ;
- trade terminé avec causes détaillées.
- clôture au prix d’entrée, avec le TP concerné, lorsqu’un TP déplacé sur l’entrée ferme effectivement la position.

Les messages utilisent le fil du signal lorsque la corrélation est disponible. Le lifecycle demande un contexte de réponse pour les publications correspondantes et l’outbox gère l’attente plutôt que de publier arbitrairement à la racine.

## 16. Dashboard

Le Dashboard est un consommateur passif. Sa base `dashboard_analytics.sqlite3`, schéma 2, contient sources, empreintes de messages, batches, instructions, items d’exécution, preuves terminales, deals, ordres et événements lifecycle. Le texte brut du message Telegram n’est pas stocké dans la table des messages.

Les écritures analytiques sont fail-open : une erreur ne bloque ni Telegram ni MT5.

Les périodes sont toutes les données, 30 jours, 7 jours et aujourd’hui. La synthèse affiche sources, signaux reçus/exécutés, taux de réussite et résultat net. Le tableau par source ajoute score, étoiles, activité, trades terminés, profit factor et drawdown. Le détail couvre TP1–TP4, BE, Smart Close, durées, moyennes, séries et couverture des preuves.

Le résultat financier additionne profit, commission, swap et frais uniquement pour des deals attribuables au batch et au compte attendu. Sans preuve, il reste inconnu. Une réinitialisation de source pose une date de coupure, sans supprimer les archives.

Le score qualité pondère réussite 25 %, profit factor 20 %, drawdown 15 %, TP profonds 15 %, régularité 10 %, résultat net 10 % et couverture 5 %. La confiance atteint son maximum à 20 trades confirmés.

## 17. Copy Trader MT5 vers MT5/MT4

### 17.1 Connexions

La source est toujours MT5. Elle peut utiliser un worker MT5 autonome ou la session déjà détenue par le module MT5. En mode partagé, le Copy Trader ne réinitialise pas le terminal et sa déconnexion ne ferme pas la session principale.

La cible peut être MT5 ou MT4. Les boutons reflètent les états Connecter/Déconnecter et Démarrer/Arrêter la copie. La copie n’est activable que lorsque source et cible sont confirmées. La perte d’un worker ou de la session partagée met l’état à jour.

### 17.2 Synchronisation initiale

Au démarrage de la copie, l’inventaire source est comparé au mapping persistant :

- un mapping dont le ticket cible est confirmé présent est exclu ;
- un mapping confirmé obsolète est supprimé et redevient éligible ;
- si l’inventaire cible n’est pas vérifiable, le mapping est conservé par sécurité ;
- si tout est déjà mappé, une information est affichée et aucun OPEN initial n’est envoyé ;
- sinon, le résumé distingue positions source, déjà copiées, positions à synchroniser, pending à synchroniser et volume total.

Les choix sont Ignorer, Copier ou Annuler. Ignorer est le choix par défaut. Copier exige une seconde confirmation et ne porte que sur les opérations éligibles. L’anti-doublon à l’ouverture reste une seconde protection.

### 17.3 Réplication

Le worker source détecte OPEN, UPDATE et CLOSE pour positions et pending : ouverture, réduction partielle, SL/TP, prix pending, suppression, fermeture et transformation pending→position.

Le mapping maître/esclave est enregistré dans `copy_trader_ticket_map.json`. Les commandes de fermeture et modification visent le ticket cible exact. Une ouverture envoyée mais non confirmée est protégée contre la répétition.

Les modes de volume sont multiplicateur, ratio d’equity et lot fixe, multipliés par le coefficient de sécurité. Le ratio d’equity se replie sur le multiplicateur si les equities sont indisponibles. La cible aligne le volume aux contraintes broker. SL/TP et pending sont copiables séparément.

### 17.4 Symboles

Le Copy Trader utilise les alias centralisés et la recherche de variantes négociables. Un alias appris par la cible est renvoyé puis persisté. Les suffixes broker courants sont comparés et les instruments non négociables sont évités.

### 17.5 Bridge MT4

MT4 utilise ZeroMQ : port 6001 pour les commandes vers l’EA, 6002 pour les événements. `NovaBot_MT4_Slave_ZMQ.mq4` accepte OPEN, CLOSE_TICKET, DELETE_PENDING, MODIFY_SLTP et MODIFY_PENDING, puis publie READY, HEARTBEAT, OPENED, CLOSED, UPDATED, ALIAS_LEARNED et ERROR.

Le worker attend READY et considère le heartbeat perdu après 8 secondes. Il écrit `logs/mt4_worker.log`, avec rotation à 2 Mio et trois sauvegardes, et journalise périodiquement le heartbeat.

L’installateur PowerShell détecte les dossiers de données MT4, privilégie un terminal ouvert, propose un choix si nécessaire, télécharge mql-zmq, installe includes/DLL, copie l’EA et les notices, puis demande la compilation et l’autorisation des imports DLL. Le kit complet est inclus dans le build PyInstaller et peut être extrait/lancé depuis le bouton du Copy Trader.

Le worker peut récupérer le port 6002 d’une ancienne instance NovaBOT invisible, y compris un ancien build, mais refuse de terminer un processus tiers ou une instance NovaBOT visible.

## 18. Supervision distante et NovaBOT Companion

La supervision est activée par défaut en mode local sur `127.0.0.1:8765`, sans authentification. Le mode privé choisit une IPv4 locale et impose l’authentification Bearer.

Les seules routes sont :

- `GET /supervision/health` ;
- `GET /supervision/identity` ;
- `GET /supervision/snapshot` ;
- `GET /supervision/events?after=<séquence>`.

POST et les autres méthodes sont refusés. Il n’existe aucune route de commande, de trading ou de modification distante.

L’observateur projette identité, profil, Telegram, MT5, Dashboard, configuration, Money Management, santé et événements. Les logins sont masqués et les objets publics sont nettoyés avant exposition.

Le token privé est généré localement, protégé par DPAPI dans `supervision/token.protected` et comparé via SHA-256. Le dialogue peut le régénérer et produire un QR code d’appairage.

Le code de NovaBOT Companion n’est pas présent dans cette arborescence. L’interaction attestée est donc uniquement celle d’un client externe qui s’appaire puis lit ces quatre routes. Aucun comportement du Companion au-delà de ce contrat ne peut être déduit ici.

## 19. Persistance, caches et journaux

### 19.1 Principales données de profil

| Fichier | Rôle |
|---|---|
| `data/app_settings.json` | Thème Telegram et réglages Smart historiques. |
| `data/app_startup_settings.json` | Auto-connexion, affichage et police. |
| `data/onboarding.json` | Progression de configuration. |
| `data/selected_chats.json` | Sources Telegram. |
| `data/telegram_filter_settings.json` | Filtre global/par source. |
| `data/telegram_filter_processed.json` | Empreintes déjà traitées. |
| `data/telegram_forward_map.json` | Correspondances de copie. |
| `data/telegram_message_deletion_cache.json` | Audit passif des suppressions. |
| `data/telegram_notification_outbox.sqlite3` | Livraisons Telegram. |
| `data/telegram_command_dictionary.json` | Dictionnaire Smart utilisateur. |
| `data/group_id.txt` | Groupe privé destination. |
| `data/telegram_destination.json` | Liaison structurée entre compte Telegram, groupe privé, peer/channel et access hash. |
| `data/mt5_app_settings.json` | Money Management et réglages MT5. |
| `data/symbol_aliases.json` | Alias broker, partagés avec le Copy Trader. |
| `data/be_watchlist.json` | Lifecycle persistant. |
| `data/chat_history.json` | Historique de messages du module MT5. |
| `data/dashboard_analytics.sqlite3` | Analytique. |
| `data/copy_trader_settings.json` | Connexions et options de réplication. |
| `data/copy_trader_ticket_map.json` | Mapping maître/esclave. |

### 19.2 Secrets et sessions

- Telegram : `master.key`, `credentials.enc`, `bot.enc`, session Telethon ;
- MT5 : `key.key`, `credentials.json` ;
- Copy Trader : `master.key` et mots de passe chiffrés dans ses réglages ;
- supervision : token DPAPI.

### 19.3 Journaux

Les consoles Telegram, MT5 et Copy Trader sont persistées et réinitialisables. Les journaux incluent filtre, commandes Smart, suppressions Telegram, trades, snapshots marché, validation CSV et worker MT4.

Les stores JSON de profil utilisent des écritures atomiques ou tolérantes selon le rôle. Les erreurs analytiques, de journalisation et d’audit passif sont fail-open ; les données indispensables au trading produisent au contraire un refus explicite.

## 20. Composants internes et propriété des décisions

| Composant | Responsabilité fonctionnelle |
|---|---|
| `MT5BrokerFacts` | Lectures MT5 : compte, symbole, tick, positions, ordres, deals, rates. |
| `MT5ActionGateway` | Envoi brut des actions MT5. |
| `MT5OrderTransport` / `MT5PositionTransport` | Adaptation des requêtes et retcodes. |
| `ExecutionAdmissionPolicy` | Géométrie, familles et tolérances. |
| `MoneyManagementVolumePlanner` | Calcul, partage et réduction des volumes. |
| `ValidationEngine` | Règles, score et décision ALLOW/REDUCE/BLOCK. |
| `TelegramSignalCorrectionService` | Correction d’un batch après édition Telegram. |
| `LifecycleWatchlistStore` | Persistance du suivi de trades. |
| `LifecycleTerminalPublisher` | Déduplication et publication finale. |
| Repositories/stores de profil | Isolation et écritures robustes. |
| Dashboard | Lecture analytique, jamais décision de trading. |
| Supervision | Projection en lecture seule, jamais action métier. |

La façade publique `MT5App` conserve un ordre explicite de mixins pour préserver les contrats historiques. `ExecutionOrderPolicy` existe et est testé isolément, mais aucun appel de production courant ne l’utilise : le choix d’ordre est actuellement réalisé dans `MT5OrderExecutionMixin`. Il s’agit du principal composant fonctionnel présent mais non branché identifié.

## 21. Parcours fonctionnels de bout en bout

### 21.1 Signal Telegram

1. Telethon reçoit le message d’une source sélectionnée.
2. L’audit de suppression mémorise passivement son identité.
3. Le filtre choisit sa configuration globale ou source et décide.
4. La décision alimente console/log et Dashboard.
5. Telegram transmet l’enveloppe à MT5.
6. Le parser extrait une ou plusieurs instructions.
7. Le décalage TP éventuel est appliqué.
8. Les instructions partageant symbole/sens/SL/TP reçoivent le même batch.
9. L’entrée fractionnée peut produire deux branches.
10. Chaque branche résout le symbole broker et lit les faits réels.
11. Admission, type d’ordre, prix final, anti-doublon et volume sont calculés.
12. Le validateur facultatif décide et réduit éventuellement les volumes.
13. Les ordres TP sont envoyés.
14. Le batch et les tickets arment le lifecycle.
15. La publication d’ouverture est mise dans l’outbox et répond au signal copié.
16. Le lifecycle observe positions, pending, historique et deals.
17. Les automatismes confirmés exécutent BE, suivis SL, progressive TP ou annulations.
18. Les notifications sont dédupliquées et livrées.
19. Le Dashboard archive les preuves et la supervision expose l’état public.

### 21.2 Commande Smart

1. Le filtre reconnaît une réponse Smart autorisée.
2. Le texte est transmis à MT5 avant la copie Telegram.
3. Le parser de signal échoue normalement, puis le moteur Smart prend la main.
4. Le parent résout strictement le batch.
5. L’option et le mode sont vérifiés.
6. L’action est bornée au contexte et envoyée par la gateway.
7. Le résultat confirmé produit une notification métier ; le texte Smart n’est pas recopié comme doublon.

### 21.3 Message édité

1. Le handler MessageEdited reçoit le même ID avec une nouvelle empreinte.
2. Le filtre autorise la réanalyse si l’option est active.
3. Le parser reconstruit le plan.
4. Le service retrouve le batch original.
5. SL/TP existants sont modifiés et les opérations TP disparues recréées.
6. Le plan n’est persisté intégralement que sans échec.
7. Un résumé est publié ; la copie Telegram est éditée si Telegram l’autorise.

### 21.4 Copy Trader

1. Source et cible se connectent.
2. L’inventaire source est comparé au mapping et à l’inventaire cible.
3. L’utilisateur ignore, synchronise ou annule les opérations éligibles.
4. Le worker source produit OPEN/UPDATE/CLOSE.
5. Volume et symbole cible sont résolus.
6. Le worker cible exécute et renvoie le ticket.
7. Le mapping est persisté jusqu’à confirmation de la fermeture/suppression.

## 22. Fonctionnalités facultatives ou désactivées par défaut

Sont présentes mais désactivées initialement : auto-connexion Telegram/MT5, conversion auto-LIMIT, tolérance automatique, adaptation spread, validation, anti-doublon, limite de trades, capital virtuel, coffre, BE, suivi TP/SL, sécurisation en pips, progressive TP, décalage TP, ajustement TP à l’entrée réelle, entrée fractionnée globale/par groupe et dérogation par groupe.

Les commandes Smart individuelles sont également inactives sans réglage explicite. La supervision locale fait exception : elle est activée par défaut.

## 23. Compatibilités historiques et code non courant

- La façade MT5 accepte encore les tuples issus des anciens parsers.
- `move_sl_to_tp1_on_tp2` est conservé et converti vers `tp_sl_follow_mode`.
- Les anciens fichiers `symbol_info_<date>.json` restent lisibles.
- Les commentaires, magic numbers et formats de watchlist historiques possèdent des replis.
- L’ordre des mixins de `MT5App` est fixé pour préserver la résolution des méthodes publiques.
- `ExecutionOrderPolicy` est caractérisé par les tests mais non branché au pipeline courant.
- Le docstring Smart « simulation uniquement » est historique et contredit l’exécution actuelle.

## 24. Tests et contrats observés

### 24.1 Résultat réel

Contrôles exécutés depuis le dossier analysé :

```text
Compilation syntaxique en lecture seule de tous les fichiers Python
python -m unittest discover -s validation/tests -p "test_*.py"
```

Résultat :

- compilation syntaxique : 190 fichiers réussis ;
- tests : 800 ;
- échecs d’assertion : 26 ;
- erreurs : 0.

### 24.2 Couverture

Les tests caractérisent notamment profils/import/export/verrous, lancement CLI, langues, thèmes, typographie des dialogues, affichage, About, onboarding et contrôle Visual C++, parser, alias, LIMIT/STOP et auto-LIMIT d’entrée unique, filtre et routage Telegram, liaison de destination et reconnexion, bot administrateur, audit de suppression, correction de signal, outbox, Smart, réinitialisation Money Management, décalage TP, ajustement TP à l’entrée réelle, entrée fractionnée par groupe ou globale, validation, transports/actions/lifecycle MT5, progressive TP, Dashboard, supervision, Copy Trader et son bouton Paramètres, workers MT5/MT4 et installation/récupération du bridge.

Ils utilisent des doubles et des AST pour une partie des contrats. Ils ne prouvent pas la disponibilité réelle de Telegram, du broker, d’un terminal, du marché ou du réseau.

### 24.3 Les 26 échecs constatés

Les échecs ne sont pas masqués dans cette analyse :

- 1 métadonnée About encore attendue en version 180 par le test ;
- 7 attentes du classifier MT5 ;
- 1 ordre/persistance des notifications TP lifecycle ;
- 1 message terminal manuel/inconnu ;
- 1 suivi live des TP ;
- 1 style du bouton onboarding ;
- 1 valeur/préréglage progressive TP ;
- 13 assertions du filtre Telegram, dont quatre sous-cas paramétrés.

Dans le filtre, plusieurs tests attendent encore `require_numeric_value=False` et un ordre de raisons différent, alors que le code courant place ce critère à `True`. Dans About, le test attend `v2026.0.180` alors que le code affiche `v2026.0.200`. Ces divergences sont des contrats de test non alignés avec le code actuel ; aucune correction n’a été effectuée.

## 25. Limites et divergences observables

- Le dossier est orienté Windows ; MT5, MT4, DPAPI et l’activation assistée ne sont pas portables tels quels.
- MT4 exige EA compilé, mql-zmq, DLL et imports DLL autorisés.
- Un seul worker peut posséder le bridge local 6001/6002.
- Le Companion n’est pas fourni ; seule l’API de supervision est attestée.
- La supervision ne permet aucune commande distante.
- Seuls quatre TP numériques peuvent être exécutés.
- Le contrôle nommé week-end est inactif car `is_tradable()` retourne toujours vrai.
- Le risque du validateur est évalué par branche d’entrée fractionnée, pas comme exposition composite atomique.
- Le coffre n’est pas soustrait de la base de risque.
- Le Dashboard ne fabrique aucun résultat sans preuve terminale attribuable.
- Une commande Smart Telegram sans parent ou avec parent non corrélé n’agit pas.
- La correction d’un message édité refuse un changement d’identité, d’entrée ou de nombre de TP.
- L’audit de suppression n’empêche ni ne rejoue un signal recréé ; il journalise seulement.
- Les retcodes, contraintes du broker et délais Telegram restent déterminants.
- About déclare version/build 200, `main.py` transmet encore `5.0` à l’identité de supervision, et le dossier analysé porte un autre numéro.
- Le lanceur multi-profils référence encore `NOVABOT-WORKING 141.0.exe` et cinq profils codés en dur.
- `main.ps1` contient deux boucles de relance successives ; la seconde est inatteignable tant que la première boucle infinie fonctionne.
- Le README indique qu’aucune licence générale du projet n’est définie ; la notice Apache 2.0 de mql-zmq est séparée.

## Conclusion

Le code analysé implémente une chaîne fonctionnelle complète Telegram → filtrage → parsing → correction/décalage → résolution broker → admission → Money Management → validation → exécution MT5 → lifecycle → notifications → Dashboard → supervision.

Le Copy Trader constitue un sous-système parallèle MT5→MT5/MT4 avec connexion source partagée, synchronisation initiale protégée, mapping persistant et bridge ZeroMQ.

Les propriétaires fonctionnels sont globalement séparés : lectures MT5, actions, volume, validation, persistance, analytique et supervision possèdent des frontières identifiables. Les principales réserves constatées sont les 26 divergences de tests, quelques métadonnées de version non harmonisées, un contrôle de tradabilité actuellement neutre, une policy d’ordre non branchée et les limites explicites des services externes.

Ce rapport décrit le fonctionnement attesté par l’arborescence au 8 août 2026 et n’attribue à NovaBOT aucun comportement absent du code.
