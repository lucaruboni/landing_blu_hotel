# BLU — landing page per hotel

Progetto front-end (Vue 3 + Vite), pensato per essere pubblicato gratuitamente su Cloudflare Pages.

## Sviluppo locale

```bash
npm install
npm run dev
```

## Build di produzione

```bash
npm run build
```

Genera i file statici nella cartella `dist/`.

## Pubblicazione su Cloudflare Pages (gratuita)

**Opzione A — collegando il repository Git (consigliata)**

1. Pusha questo progetto su GitHub o GitLab.
2. Su [dash.cloudflare.com](https://dash.cloudflare.com) → **Workers & Pages** → **Create application** → **Pages** → **Connect to Git**.
3. Seleziona il repository.
4. Imposta i parametri di build:
   - **Framework preset**: Vite (o "None")
   - **Build command**: `npm run build`
   - **Build output directory**: `dist`
5. Deploy. Ad ogni push su `main` Cloudflare rifà la build e pubblica automaticamente.

**Opzione B — upload diretto senza Git**

```bash
npm install -g wrangler
npm run build
wrangler pages deploy dist
```

Segui le istruzioni di `wrangler` per autenticarti e scegliere il nome del progetto.

## Struttura

```
src/
  components/   sezioni della landing (Hero contiene l'animazione 3D a pallini)
  style.css     design system: colori, tipografia, layout
  App.vue       composizione della pagina
index.html      entry point, carica i font Google
```
