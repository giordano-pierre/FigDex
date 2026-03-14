# AGENT RULES - FIGDEX

## Bridge Figma local
- Endpoint HTTP unique: `POST http://localhost:9001/command`
- Toujours envoyer du JSON avec `Content-Type: application/json`.
- Pour exécuter du code Figma, utiliser l'action `runScript`.
- Le code doit obligatoirement être encapsulé ainsi:
  - `return (async function() { /* code Figma */ })();`

## Format de requête recommandé
```bash
curl -s -X POST http://localhost:9001/command \
  -H "Content-Type: application/json" \
  -d '{"action":"runScript","code":"return (async function() { /* ... */ })();"}'
```

## Règles API Figma
- Toujours appeler `await figma.loadAllPagesAsync()` en début de script (mode `dynamic-page`).
- Pour rechercher des nodes globaux, utiliser `figma.root.findOne(...)` après `loadAllPagesAsync`.
- Si recherche par ID nécessaire en mode dynamic-page, utiliser `figma.getNodeByIdAsync(...)` (pas `getNodeById`).
- En Auto Layout, ajouter l'enfant avant de le mettre en fill:
  - `parent.appendChild(child)` puis `child.layoutSizingHorizontal = "FILL"`.
- Avant toute création/manipulation de texte:
  - `await figma.loadFontAsync({ family: "Inter", style: "Regular" })`
  - Charger aussi les variantes nécessaires (`Semi Bold`, etc.).

## Reverse Design (React/Tailwind -> Figma)
- Lire d'abord le fichier source (structure + classes Tailwind) puis générer.
- Reproduire les classes clés en priorité:
  - `padding`, `gap`, `radius`, `border`, `shadow`, `backdrop-blur`, couleurs hex.
- Construire par étapes:
  1. Frame racine + container
  2. Header/Overview
  3. Sections restantes (stats, map, alerts, table, etc.)
- Créer des placeholders visuels pour composants externes/interactifs (map, nav, boutons, cartes) quand nécessaire.
- Utiliser des noms de frames explicites pour faciliter les itérations.
- Rejouer le script de manière idempotente autant que possible (remplacer les placeholders précédents).

## Validation
- Vérifier la réponse du Bridge après chaque exécution:
  - succès attendu: `{"result":{"success":true,...}}`
  - sinon lire `error` et corriger immédiatement.
