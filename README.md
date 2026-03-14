# 🚀 Figdex
**Figdex** est le pont local bidirectionnel définitif entre votre agent IA (comme Codex) et Figma.
Fini les clics et le drag-and-drop : laissez votre IA analyser votre code React/Tailwind et générer ou modifier vos interfaces Figma en temps réel grâce à vos propres composants (Reverse Design).

## 🛠️ Installation du client Figma

L'outil nécessite l'application Figma Desktop pour charger le plugin en développement.

- **Windows & macOS** : Installez l'application officielle depuis le site de Figma.
- **Linux** :
  - **Ubuntu/Debian** : `sudo snap install figma-linux`
  - **Arch Linux** : `yay -S figma-linux-bin`
  - **Fedora** : `sudo dnf install figma-linux`
  > 💡 **Astuce Fedora (Contournement OAuth)** : Si la connexion Figma via le navigateur tourne dans le vide, copiez l'URL de redirection `figma://...` depuis la barre d'adresse de votre navigateur et collez-la directement dans votre terminal avec `xdg-open "figma://..."` pour forcer l'authentification.

## ⚙️ Configuration de Figdex

1. **Lancer le serveur local (Le Pont)**
   Ouvrez un terminal dans le dossier du projet :
   ```bash
   cd server
   npm install
   node server.js
