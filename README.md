# Nebula Media Center (NMC) Companion TV

[![Plateforme](https://img.shields.io/badge/Plateforme-Android%20TV%20%7C%20Google%20TV-blue?logo=android)](https://www.android.com/tv/)
[![Appareil de référence](https://img.shields.io/badge/Certifié-NVIDIA%20Shield%20TV%20%2F%20Pro-green?logo=nvidia)](https://www.nvidia.com/fr-fr/shield/)
[![UI Toolkit](https://img.shields.io/badge/UI-Jetpack%20Compose%20for%20TV%20(Material%203)-4285F4?logo=jetpackcompose)](https://developer.android.com/jetpack/compose/tv)
[![Langage](https://img.shields.io/badge/Langage-Kotlin%202.0-7F52FF?logo=kotlin)](https://kotlinlang.org/)
[![Dernière Release](https://img.shields.io/badge/Release-v1.2.1-brightgreen)](https://github.com/pgnet72-cyber/nmc-companion-releases/releases/tag/v1.2.1)
[![Mise à jour](https://img.shields.io/badge/Mise%20à%20jour-In--App%20Autonome%20(ADR--030)-orange)](#-mise-à-jour-in-app-autonome-adr-030)

**NMC Companion TV** est l'application client native officielle pour **Android TV**, **Google TV** et **NVIDIA Shield TV** de l'écosystème **Nebula Media Center (NMC)**.

Conçue sur mesure pour l'expérience salon grand écran à la télécommande (D-Pad), elle permet de naviguer dans l'intégralité de votre médiathèque (films, séries, sagas, musiques, web TV, flux réseau), d'effectuer des recherches intelligentes en langage naturel et de profiter d'une lecture multimédia haute fidélité (4K HDR, Dolby Vision, passthrough Dolby Atmos).

---

## 🌟 Points Forts & Architecture Double Mode

NMC Companion TV a été pensée dès sa conception pour éliminer la contrainte du serveur multimédia allumé en permanence grâce à une architecture hybride novatrice :

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                           NMC COMPANION TV (Salon)                              │
│   • Jetpack Compose for TV (10-foot UI)       • Index inversé mémoire lock-free │
│   • Lecteur ExoPlayer 4K HDR / Audio HD       • Base locale Room SQLite durcie  │
└───────────────────────────────┬─────────────────────────┬───────────────────────┘
                                │                         │
              [Mode 1 : Passerelle LAN Active]  [Mode 2 : Mode Autonome / Hors Ligne]
                                │                         │
                                ▼                         ▼
                    ┌───────────────────────┐ ┌───────────────────────────────────┐
                    │  NMC Desktop (PC V3)  │ │   Cloud Google Drive & CDN TMDb   │
                    │  • REST API / WSS     │ │   • library.json (sauvegarde)     │
                    │  • Découverte mDNS    │ │   • Métadonnées & affiches CDN    │
                    │  • Moteur IA local    │ │   • Zéro dépendance PC allumé     │
                    └───────────────────────┘ └───────────────────────────────────┘
```

1. **Mode Connecté (Passerelle LAN Active) :**
   - Appairage instantané par QR Code ou code PIN à 6 chiffres.
   - Synchronisation bidirectionnelle en temps réel via WebSockets sécurisés.
   - Accès aux téléchargements en cours, alertes système et moteur d'intelligence artificielle Desktop (Ollama, Claude, OpenAI).
   - Télémétrie et journalisation automatique des incidents TV centralisée dans le journal de bord NMC Alerts.

2. **Mode 100% Autonome (Google Drive & TMDB Direct) :**
   - Fonctionne de façon totalement indépendante, même lorsque le PC serveur est éteint.
   - Synchronisation cloud prioritaire dès le lancement (`library.json`) depuis votre espace Google Drive personnel.
   - Base de données locale Room SQLite avec affiches officielles haute résolution hébergées sur les CDN mondiaux de TMDB (`image.tmdb.org`).
   - Mécanisme anti-effacement protégeant l'intégrité de votre catalogue local en cas d'absence de réseau.

---

## 🚀 Fonctionnalités Majeures

### 🔍 1. Recherche Universelle & NLU Hors Ligne
- **Moteur NLU déterministe autonome :** Compréhension automatique en français et en anglais des intentions naturelles sans nécessiter aucune clé API d'intelligence artificielle ni connexion externe.
  - *Acteurs :* `films avec Russell Crowe`, `actor: "Alain Delon"`
  - *Réalisateurs :* `films de Stanley Kubrick`, `director: "Christopher Nolan"`
  - *Genres & Périodes :* `science-fiction années 80`, `comédies 1970-1979`
- **Recherche vocale instantanée :** Déclenchement direct depuis le bouton micro de la télécommande (NVIDIA Shield Remote ou télécommande Android TV standard).
- **Indexation Lock-Free haute performance :** Index inversé optimisé en mémoire vive (`UniversalIndexBuilder`) garantissant un filtrage en moins de 100 ms sur l'ensemble de vos médias locaux.
- **Croisement TMDB en direct :** Affichage combiné des résultats de votre bibliothèque locale et des fiches en ligne TMDB (casting, synopsis, note, bande-annonce, filmographie complète).
- **Mode IA optionnel :** Génération et exécution de plans de recherche multi-critères assistés par LLM lorsque la passerelle PC est connectée.

### ⚡ 2. Robustesse & Support des Grands Catalogues
- **Plafond mémoire étendu (512 Mo) :** Configuration avec `android:largeHeap="true"` pour accueillir confortablement des bibliothèques volumineuses de **plus de 3 500 à 10 000+ titres** sans risque de saturation mémoire (OOM).
- **Algorithme de détection des séries optimisé :** Filtrage anticipé des œuvres logiques et classification ultra-rapide des saisons et épisodes sans allocations mémoire superflues.
- **Cache mémoire intelligent :** Mémorisation par référence d'instance éliminant les recalculs d'empreinte lors de la navigation.

### 🎬 3. Expérience de Lecture Vidéo & Audio HD
- **Moteur ExoPlayer optimisé Shield TV :**
  - Décodage matériel 4K UHD, HDR10, HDR10+ et Dolby Vision.
  - Passthrough audio numérique multicanal (Dolby TrueHD, Dolby Atmos, DTS-HD Master Audio, DTS:X).
  - Gestion native des pistes audio multi-langues et des sous-titres (SRT, ASS, PGS).
- **Support des lecteurs tiers externes :** Possibilité de déléguer la lecture en un clic à VLC, Just Player, Nova Video Player ou Kodi selon vos préférences.
- **Reprise de lecture intelligente (*Resume Playback*) :** Sauvegarde automatique de la position exacte de visionnage, synchronisée entre le PC et la TV.

### 📺 4. Guide TV (EPG) & Services de Streaming
- **Grille des programmes en direct :** Consultation ergonomique des programmes de la TNT et des chaînes francophones.
- **Fiches descriptives superposées :** Consultation détaillée des synopsis, acteurs et notes TMDB des émissions en cours ou à venir directement par-dessus le guide.
- **Accès rapide aux flux IPTV / WebTV :** Lecture en direct des chaînes autorisées et des flux m3u8.

### 👨‍👩‍👧‍👦 5. Multi-Profils & Contrôle Parental
- Création et gestion de plusieurs profils utilisateurs (ex: Salon, Enfants, Parents).
- Verrouillage parental par code PIN et restriction automatique des contenus selon l'âge et la classification CSA / PEGI.
- Couvre-feu programmable limitant l'accès aux écrans à des plages horaires définies.

### 🔄 6. Mise à Jour In-App Autonome (ADR-030)
- Système de mise à jour entièrement intégré, indépendant du Google Play Store.
- Vérification périodique silencieuse (toutes les 24h) ou vérification manuelle d'un simple clic dans l'écran *À Propos*.
- Téléchargement en tâche de fond avec jauge de progression.
- **Contrôle cryptographique strict :** Vérification systématique de l'empreinte SHA-256 de l'APK avant toute installation afin de garantir l'authenticité et l'intégrité du binaire.

### 🪄 7. Assistant de Premier Démarrage (Onboarding)
Parcours guidé en 6 étapes optimisé pour la télécommande TV :
1. **Création du profil utilisateur** principal.
2. **Configuration TMDb par smartphone :** Scannez simplement un QR code pour coller votre clé API TMDB depuis votre téléphone (via relai chiffré ntfy), évitant la saisie fastidieuse à la télécommande.
3. **Association Google Drive :** Association simplifiée par code appareil à 8 lettres (`google.com/device`).
4. **Préférences de lecture :** Choix du lecteur vidéo par défaut, langue audio favorite et sous-titres.
5. **Personnalisation de la bibliothèque :** Sélection de vos genres et cinéastes favoris pour pré-remplir immédiatement votre écran d'accueil.
6. **Finalisation et accès instantané** au Media Center.

---

## 📥 Guide d'Installation (Sideload)

NMC Companion TV est distribuée sous forme de paquet APK autonome sécurisé.

### Méthode 1 : Via l'application « Downloader » (Recommandée sur Shield / Fire TV)
1. Installez l'application gratuite **Downloader by AFTVnews** depuis le Google Play Store de votre téléviseur.
2. Ouvrez Downloader et autorisez l'accès au stockage ainsi que l'installation d'applications inconnues.
3. Entrez l'URL directe de téléchargement ou le code court du projet :
   ```text
   https://github.com/pgnet72-cyber/nmc-companion-releases/releases/download/v1.0.6/app-sideload-release.apk
   ```
4. Cliquez sur **Go**, puis validez l'installation dès la fin du téléchargement.

### Méthode 2 : Via ADB (Pour utilisateurs avancés / développeurs)
1. Activez le **Débogage réseau ADB** dans les *Options pour les développeurs* de votre Android TV.
2. Depuis votre ordinateur (connecté sur le même réseau local) :
   ```powershell
   adb connect <IP_DE_VOTRE_TV>:5555
   adb install -r app-sideload-release.apk
   ```

### Méthode 3 : Via clé USB
1. Téléchargez le fichier [`app-sideload-release.apk`](../../releases/latest) sur votre ordinateur.
2. Copiez-le sur une clé USB formatée en FAT32 ou exFAT.
3. Branchez la clé sur votre Shield TV / TV Box et ouvrez un gestionnaire de fichiers (ex: *X-plore File Manager*).
4. Cliquez sur le fichier `.apk` pour lancer l'installation.

---

## ⚙️ Prérequis & Compatibilité

| Composant | Prérequis minimum | Recommandation optimale |
| :--- | :--- | :--- |
| **Système d'exploitation** | Android TV 8.0 (Oreo / API 26) | Android TV 9.0 à 14+ / Google TV |
| **Matériel cible** | Smart TV ou TV Box Android Quad-Core, 2 Go RAM | **NVIDIA Shield TV Pro** (Tegra X1+) |
| **Connexion réseau** | Wi-Fi 5 GHz (802.11ac) ou Ethernet | Ethernet Gigabit (1000 Mbps) pour les flux 4K HDR |
| **Compte TMDb** | Optionnel (fonctionne sans compte) | Clé API TMDB gratuite (pour affiches et résumés) |
| **Google Drive** | Optionnel | Recommandé pour la synchronisation autonome |

---

## 🛠️ Stack Technique

- **Langage :** [Kotlin 2.0](https://kotlinlang.org/)
- **UI & Design System :** [Jetpack Compose for TV](https://developer.android.com/jetpack/compose/tv) (Material 3 TV) avec thème Nebula exclusif.
- **Moteur Vidéo :** [AndroidX Media3 / ExoPlayer](https://developer.android.com/media/media3/exoplayer) avec extensions matérielles et passthrough audio HD.
- **Base de Données Locale :** [Android Room](https://developer.android.com/training/data-storage/room) (SQLite) avec indexation inversée et persistance réactive.
- **Chargement d'Images :** [Coil 3](https://coil-kt.github.io/coil/) avec pipeline mémoire optimisé et décodage progressif d'affiches.
- **Réseau & WebSockets :** [OkHttp 4](https://square.github.io/okhttp/) & [Kotlinx Coroutines](https://github.com/Kotlin/kotlinx.coroutines).
- **Sérialisation :** [Kotlinx Serialization JSON](https://github.com/Kotlin/kotlinx.serialization) déterministe.
- **Cloud & Intégration :** Google Drive API v3 (OAuth2 Device Authorization Flow RFC 8628).

---

## 🔒 Sécurité, Confidentialité & Signature

- **Empreinte immuable :** Chaque version officielle distribuée est accompagnée de son empreinte cryptographique SHA-256 publiée publiquement dans le manifeste [`update.json`](./update.json).
- **Zéro télémétrie commerciale :** Aucun cookie, aucun traceur tiers, aucune publicité, aucune donnée personnelle transmise à des tiers. Vos métadonnées restent strictement confinées à votre réseau local et à votre Google Drive personnel.
- **Contrôle d'accès :** Toutes les communications avec la passerelle PC sont chiffrées et protégées par des jetons d'autorisation temporaires (Bearer Token).

---

## 📄 Historique des Versions Récentes

- **v1.2.1 (Code 21) :**
  - 🎬 **Résolution automatique oEmbed des vidéos YouTube** : extraction instantanée du titre officiel (ex. *"Pourquoi 2027 va être une année décisive pour l'IA"*), de l'auteur/chaîne et de la miniature HD sans clé d'API.
  - 🛡️ **Filtrage des identifiants de suivi** : neutralisation des paramètres de tracking (`?is=`, `?si=`) empêchant les hashs opaques de devenir des titres de vidéos.
  - 🔔 **Abonnement direct ntfy.sh autonome** : écoute d'alertes en temps réel sur Android TV sans dépendance au serveur Desktop, avec indicateur de statut en direct dans les paramètres.
  - 📋 **Presse-papier & actions rapides dans les alertes** : « 📋 Copier l'URL » (isolée), « 📋 Copier tout le texte », « 🎬 NMC Lecteur URL » et « ▶ Ouvrir avec app externe » sur un bandeau monocouche optimisé pour TV.
- **v1.2.0 (Code 20) :**
  - 🎨 **Espace Apparence & Ergonomie complet** miroir de NMC Desktop :
    - 5 thèmes visuels d'ambiance (Nebula Cosmique, OLED Noir Pur, Clair Feutré Warm Slate WCAG AAA, Zénith Graphite, Pulsar Violet).
    - 6 couleurs d'accentuation D-pad dynamiques (Cyan, Violet, Rose, Émeraude, Ambre, Bleu) appliquant une teinte immédiate aux halos télécommande, boutons et puces actifs.
    - 5 modes d'accessibilité et daltonisme (Standard, Protanopie, Deutéranopie, Tritanopie, Contraste Élevé AAA).
    - Échelle d'interface TV (100% Standard, 115% Confort, 130% Grand Écran).
    - Bascule instantanée de format des cartes : Affiches Cinéma 2:3 (140x200 dp) vs Bannières Vidéo 16:9 panoramiques (240x135 dp) avec chargement prioritaire des backdrops.
    - Réglages de fluidité (toggle zoom D-pad 1.08x) et résolution des affiches.
- **v1.1.0 (Code 19) :**
  - 🌍 Gestion multi-territoires des offres JustWatch/TMDB (« Où regarder ») avec sélecteur par défaut (FR, US, UK, BE, CH, CA) et pastilles interactives.
  - 🔍 Recherche libre enrichie par acteur/personnalité avec filmographie intégrée (ex: Shah Rukh Khan).
  - 🎯 Résolution ciblée par année (`Alpha (2026)`, `Alpha year:2026`) avec romanisation automatique des titres originaux.
  - ♿ Double encodage visuel (cercle vide / plein + coche) et zoom D-pad renforcé dans l'Atelier de démarrage.
  - ☀️ Thème clair TV feutré avec contraste maximal WCAG AAA (#0B1320).
- **v1.0.6 (Code 17) :**
  - 🧠 Moteur NLU déterministe hors ligne : reconnaissance naturelle des acteurs, réalisateurs, genres et années sans IA cloud.
  - 🚀 Activation du grand tas Dalvik (512 Mo) : élimination définitive des crashs OOM lors de l'indexation de bibliothèques massives (> 3 500 titres).
  - ☁️ Fiabilisation du Cloud Relay Google Drive : synchronisation prioritaire au démarrage en mode autonome et isolation anti-purge de la base locale.
  - 🖼️ Réparation asynchrone progressive des affiches manquantes vers les CDN officiels TMDB.
- **v1.0.5 (Code 16) :**
  - Correctifs du rafraîchissement en mode non appairé et résilience face aux pannes réseau.
- **v1.0.4 (Code 15) :**
  - Architecture lock-free de l'index inversé de recherche et intégration de la remontée d'incidents ANR via `getHistoricalProcessExitReasons`.
- **v1.0.3 (Code 14) :**
  - Introduction du Guide TV EPG, recherche vocale D-Pad et déploiement du moteur de mise à jour in-app (ADR-030).

---

## 🤝 Écosystème Nebula Media Center

- **Dépôt Principal Desktop :** [Nebula-Media-Center](https://github.com/pgnet72-cyber/nebula-media-center)
- **Dépôt des Releases Companion TV :** [nmc-companion-releases](https://github.com/pgnet72-cyber/nmc-companion-releases)
- **Support & Issues :** Pour signaler une anomalie ou proposer une amélioration, ouvrez un ticket dans l'onglet [Issues](../../issues).
