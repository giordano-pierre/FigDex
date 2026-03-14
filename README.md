# 🚀 Figdex

*Read this in other languages: [🇫🇷 Français](#francais) | [🇬🇧 English](#english)*

---

<a id="francais"></a>
## 🇫🇷 Français

**Figdex** est le pont local bidirectionnel entre votre agent IA (comme Figdex) et Figma.
Fini les clics et le drag-and-drop : laissez votre IA analyser votre code (React, Tailwind, etc.) et générer ou modifier vos interfaces Figma en temps réel en utilisant vos propres composants (Reverse Design).

### 🚀 0. Par où commencer (première installation)

1. Clonez le repository :
   ```bash
   git clone git@github.com:giordano-pierre/FigDex.git
   cd FigDex
   ```
2. Lancez ensuite les étapes 1 → 4 ci-dessous.

### 🛠️ 1. Prérequis : l'application Figma

Pour que Figdex puisse faire le lien localement avec votre fichier, vous **devez** utiliser une application de bureau Figma (le plugin de développement ne fonctionne pas sur la version web).

**Windows & macOS :** 👉 [Télécharger Figma Desktop App (Officiel)](https://www.figma.com/downloads/)

**Utilisateurs Linux (AppImage & Contournement) :**
Figma ne proposant pas d'application native officielle, nous utilisons le client communautaire [Figma-Linux](https://github.com/Figma-Linux/figma-linux). Voici la méthode recommandée via AppImage (testée sur Fedora) :

1. Téléchargez et rendez l'AppImage exécutable :
   ```bash
   curl -LO https://github.com/Figma-Linux/figma-linux/releases/download/v0.11.5/figma-linux_0.11.5_linux_x86_64.AppImage
   chmod +x figma-linux_0.11.5_linux_x86_64.AppImage
   ```
2. Lancez l'application :
   ```bash
   ./figma-linux_0.11.5_linux_x86_64.AppImage
   ```

> 💡 **Astuce Linux (contournement OAuth)** : si la connexion à votre compte Figma tourne dans le vide dans le navigateur (problème fréquent sur Linux/Fedora), copiez l'URL de redirection qui commence par `figma://...` depuis la barre d'adresse, fermez l'application, puis relancez-la en passant l'URL en argument pour forcer l'authentification :
>
> ```bash
> ./figma-linux_0.11.5_linux_x86_64.AppImage "figma://app_auth/redeem?g_secret=VOTRE_SECRET_ICI"
> ```

(Alternatives d'installation : `sudo snap install figma-linux` pour Ubuntu, `yay -S figma-linux-bin` pour Arch.)

### ⚙️ 2. Lancer le serveur local (le pont)

Le serveur Node.js écoute les requêtes de votre IA et les transmet à Figma via WebSocket.
Ouvrez un terminal dans le dossier du projet :

```bash
cd server
npm install
node server.js
```

Le serveur écoute par défaut sur `http://localhost:9001`. Laissez ce terminal ouvert en arrière-plan.

### 🔌 3. Installer le plugin Figma en local

1. Ouvrez l'application Figma Desktop et un fichier de design.
2. Allez dans : `Plugins > Development > Import plugin from manifest...`
3. Sélectionnez `plugin/manifest.json` dans le dossier Figdex.
4. Lancez ensuite le plugin depuis : `Plugins > Development`.

Une petite fenêtre s'ouvre, et votre terminal devrait afficher que le plugin est connecté. Le pont est établi.

### 🧠 4. Utilisation avec Figdex (agent IA)

Pour que l'agent sache piloter Figma :

1. Copiez le contenu de `docs/AGENT_RULES.md`.
2. Collez-le dans les instructions système de votre agent, ou fournissez ce fichier en début de session.
3. Exemple de prompt :
   `Analyse mon fichier dashboard.tsx et recrée l'interface dans Figma en respectant les classes Tailwind.`

### ♻️ 5. Réutiliser Figdex sur un autre projet (cas concret)

Cas typique : vous avez déjà installé Figma Desktop + plugin Figdex + cloné ce repo une fois, et vous voulez l'utiliser sur un **nouveau projet** (ex: site vitrine).

1. Démarrez le bridge depuis ce repo Figdex :
   ```bash
   cd /chemin/vers/FigDex/server
   node server.js
   ```
2. Ouvrez votre fichier Figma cible et lancez le plugin Figdex (Development).
3. Ouvrez votre nouveau projet dans Figdex (ex: `/chemin/vers/mon-site-vitrine`).
4. Donnez à Figdex le contexte bridge + fichier à analyser.

Prompt prêt à copier :

```text
Je veux que tu fasses le reverse-design de mon interface dans Figma.
Analyse le fichier <CHEMIN_DU_FICHIER_SOURCE>.

CONTEXTE FIGDEX (OBLIGATOIRE) :
- Repo public Figdex : https://github.com/giordano-pierre/FigDex
- Bridge local : http://localhost:9001 (démarré via server/server.js)
- Vérifie la connexion du bridge avec GET /status (connected doit être true)
- Si non connecté : demander d'ouvrir Figma Desktop + lancer le plugin Development Figdex
- Utilise les règles du repo : docs/AGENT_RULES.md

Ta mission est de recréer cette interface dans Figma en respectant scrupuleusement les classes Tailwind (paddings, gaps, colors, arrondis).

PROCÉDURE :
1) Analyse la structure globale (ex: min-h-screen, background, container max-w-*).
2) Crée une Frame racine dans Figma nommée selon la page (ex: "SmartBin Dashboard").
3) Génère d'abord la section hero/overview :
   - Traduis les classes de spacing (gap-*, px-*, pb-*, pt-*) en propriétés Auto-layout.
   - Recrée les badges/chips (rounded-full, border, couleurs exactes).
   - Recrée le H1 + description avec les bonnes tailles de texte (text-*, tracking, line-height).
4) Pour les composants non triviaux (nav, stats cards, map, widgets), crée des Frames temporaires au bon style visuel (shadow-lg, backdrop-blur, borders).

CONSIGNES TECHNIQUES :
- Utilise toujours figma.loadAllPagesAsync() avant les recherches globales.
- Utilise figma.root.findOne(...) pour chercher les composants/nodes existants si besoin.
- Exécute via le Bridge local (http://localhost:9001), action runScript, avec wrapper obligatoire :
  return (async function() { /* code Figma */ })();
- Ajoute les enfants avant d'appliquer layoutSizingHorizontal = "FILL".
- Avance section par section : commence par la structure + header, puis demande si tu continues.
- Après chaque exécution, analyse la réponse du Bridge et corrige immédiatement les erreurs.

C'est parti : analyse le fichier et génère la structure initiale.
```

---

<a id="english"></a>
## 🇬🇧 English

**Figdex** is a local bi-directional bridge between your AI agent (like Figdex) and Figma.
No more clicking and dragging: let your AI analyze your code (React, Tailwind, etc.) and generate or modify your Figma UI in real time using your own components (Reverse Design).

### 🚀 0. Getting Started (first-time setup)

1. Clone the repository:
   ```bash
   git clone git@github.com:giordano-pierre/FigDex.git
   cd FigDex
   ```
2. Then follow steps 1 → 4 below.

### 🛠️ 1. Prerequisites: Figma Desktop App

To use Figdex locally with your design file, you **must** use a Figma Desktop app (development plugins do not run in the web version).

**Windows & macOS:** 👉 [Download Figma Desktop App (Official)](https://www.figma.com/downloads/)

**Linux Users (AppImage & Workaround):**
Since Figma does not provide an official native app for Linux, we use the community-driven [Figma-Linux](https://github.com/Figma-Linux/figma-linux) client. Here is the recommended method using AppImage (tested on Fedora):

1. Download and make the AppImage executable:
   ```bash
   curl -LO https://github.com/Figma-Linux/figma-linux/releases/download/v0.11.5/figma-linux_0.11.5_linux_x86_64.AppImage
   chmod +x figma-linux_0.11.5_linux_x86_64.AppImage
   ```
2. Launch the app:
   ```bash
   ./figma-linux_0.11.5_linux_x86_64.AppImage
   ```

> 💡 **Linux Tip (OAuth workaround):** If the Figma account login hangs in your browser (a common issue on Linux/Fedora), copy the redirect URL starting with `figma://...` from your browser's address bar, close the app, and run it again passing the URL as an argument to force authentication:
>
> ```bash
> ./figma-linux_0.11.5_linux_x86_64.AppImage "figma://app_auth/redeem?g_secret=YOUR_SECRET_HERE"
> ```

(Installation alternatives: `sudo snap install figma-linux` for Ubuntu, `yay -S figma-linux-bin` for Arch.)

### ⚙️ 2. Start the local server (the bridge)

The Node.js server receives AI requests and forwards them to Figma via WebSocket.
Open a terminal in the project folder:

```bash
cd server
npm install
node server.js
```

The server listens on `http://localhost:9001` by default. Keep this terminal running.

### 🔌 3. Install the local Figma plugin

1. Open Figma Desktop and any design file.
2. Go to: `Plugins > Development > Import plugin from manifest...`
3. Select `plugin/manifest.json` from the Figdex folder.
4. Run the plugin from: `Plugins > Development`.

A small plugin window opens, and your terminal should indicate the plugin is connected.

### 🧠 4. Use with Figdex (AI agent)

To let your agent control Figma:

1. Copy `docs/AGENT_RULES.md`.
2. Paste it into the agent system instructions, or provide it at session start.
3. Example prompt:
   `Analyze my dashboard.tsx file and recreate the UI in Figma, respecting Tailwind classes.`

### ♻️ 5. Reuse Figdex on another project (concrete use case)

Typical case: Figma Desktop + Figdex plugin are already installed, and you want to use the bridge on a **new project** (for example, a marketing site).

1. Start the bridge from this Figdex repo:
   ```bash
   cd /path/to/FigDex/server
   node server.js
   ```
2. Open your target Figma file and run the Figdex plugin (Development).
3. Open your new project in Figdex (example: `/path/to/marketing-site`).
4. Give Figdex the bridge context + files to analyze.

Copy-ready prompt:

```text
I want you to reverse-design my interface in Figma.
Analyze the file <PATH_TO_SOURCE_FILE>.

FIGDEX CONTEXT (REQUIRED):
- Public Figdex repo: https://github.com/giordano-pierre/FigDex
- Local bridge: http://localhost:9001 (started from server/server.js)
- Verify bridge connection with GET /status (connected must be true)
- If not connected: ask to open Figma Desktop + run the Development Figdex plugin
- Use repository rules from: docs/AGENT_RULES.md

Your mission is to recreate this interface in Figma while strictly respecting Tailwind classes (padding, gaps, colors, border radius).

PROCEDURE:
1) Analyze the global structure (for example: min-h-screen, background, max-w-* container).
2) Create a root Frame in Figma named after the page (for example: "SmartBin Dashboard").
3) Build the hero/overview section first:
   - Translate spacing classes (gap-*, px-*, pb-*, pt-*) into Auto-layout properties.
   - Recreate badges/chips (rounded-full, border, exact colors).
   - Recreate H1 + description with correct text sizes (text-*, tracking, line-height).
4) For non-trivial components (nav, stat cards, map, widgets), create temporary Frames with matching visual style (shadow-lg, backdrop-blur, borders).

TECHNICAL RULES:
- Always call figma.loadAllPagesAsync() before global searches.
- Use figma.root.findOne(...) to locate existing components/nodes when needed.
- Execute through the local Bridge (http://localhost:9001), action runScript, with mandatory wrapper:
  return (async function() { /* Figma code */ })();
- Append children before applying layoutSizingHorizontal = "FILL".
- Work incrementally: start with structure + header, then ask whether to continue.
- After each execution, analyze the Bridge response and fix errors immediately.

Start now: analyze the file and generate the initial structure.
```
