För att publicera sidan live:
 
1. På GitHub, i .github --> skapa en ny mapp: workflows

   
2. I den nya mappen, skapa en fil: deploy.yml 
Klistra in detta i filen och spara (denna använder pnpm, om du använder npm måste du ändra):

```bash
# Sample workflow for building and deploying a VitePress site to GitHub Pages
name: Deploy site to Pages
 
on:
  # Runs on pushes targeting the `main` branch.
  push:
    branches: [main]
 
  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:
 
# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write
 
# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: pages
  cancel-in-progress: false
 
jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - uses: pnpm/action-setup@v4
        with:
          version: latest
      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: latest
          cache: pnpm
      - name: Setup Pages
        uses: actions/configure-pages@v4
      - name: Install dependencies
        run: pnpm install
      - name: Build with Vite
        run: pnpm build
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: dist
 
  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    needs: build
    runs-on: ubuntu-latest
    name: Deploy
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```
 

3. Gå in på settings. Välj pages under build & deployment, välj "source GitHub Actions"

   
4. Se till att det ligger en länk till sidan under "about" på repot, klicka i "Use your GitHub Pages website".

   
5. Skapa eller uppdatera vite.config.ts så att den innehåller rätt "base", dvs. rätt URL
   
```
import { fileURLToPath, URL } from 'node:url';
 
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';
import vueDevTools from 'vite-plugin-vue-devtools';
 
// https://vite.dev/config/
export default defineConfig({
  base: '/fed24d-arbetsmetodik-inl-2-vue-TeaGross/',
  plugins: [vue(), vueDevTools()],
  resolve: {
    alias: {
      '@': fileURLToPath(new URL('./src', import.meta.url)),
    },
  },
});
```
