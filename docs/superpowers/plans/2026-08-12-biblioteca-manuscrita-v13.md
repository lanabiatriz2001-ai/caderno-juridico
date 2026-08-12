# Biblioteca Manuscrita v13 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Entregar uma Biblioteca autônoma com 160 referências manuscritas originais, apresentadas num mosaico editorial inspirado no painel de ideias da usuária, com busca global, rotas persistentes, detalhe ampliado, zoom e exportação PNG.

**Architecture:** A aplicação v11 existente e seus dados serão preservados. A v13 será desenvolvida em módulos-fonte pequenos (`src/v13`) e compilada por um script para dentro do HTML monolítico, mantendo o funcionamento em `file://`. O catálogo terá metadados separados dos 160 WEBPs; o build incorporará todos os ativos como data URIs e substituirá somente blocos explicitamente marcados da Biblioteca.

**Tech Stack:** HTML/CSS/JavaScript sem framework, Node.js ESM, JSDOM, `node:test`, ImageMagick 6, ImageGen, html2canvas já incorporado, ChatGPT Library e GitHub Pages.

## Global Constraints

- Preservar resumos, preferências, favoritos legados, backups, inventário v10 e rotas antigas.
- A Biblioteca exibe seis famílias e uma visão “Todos”, com 160 referências: 32 títulos, 28 caixas, 30 setas, 28 estruturas, 24 divisores e 18 páginas completas.
- O HTML final funciona em `file://` e GitHub Pages, sem arquivos externos obrigatórios.
- Não usar CSS art, SVG artesanal, emoji decorativo, miniatura de prancha inteira ou recorte borrado.
- O mosaico inicial mistura páginas completas, lettering aplicado e estruturas jurídicas.
- Tamanhos mínimos: 280 px em 1440, 220 px em 1024 e quase toda a largura em 390.
- O primeiro modelo aparece antes de 180 px de rolagem no celular.
- O build só pode ser publicado com 40 testes legados e todos os testes v13 aprovados, console sem erros e `design-qa.md` com `final result: passed`.

---

## File Map

- `src/v13/catalog.mjs`: esquema, metadados e validação das 160 referências.
- `src/v13/library.css`: layout masonry, cabeçalho compacto, pin, modal e responsividade.
- `src/v13/library-runtime.js`: renderização, busca, URL, modal, zoom, foco e PNG.
- `assets/v13/source/`: 25 folhas-fonte geradas e inspecionadas.
- `assets/v13/items/`: 160 WEBPs individuais otimizados.
- `scripts/crop-v13-atlases.mjs`: exportação determinística dos itens das folhas-fonte.
- `scripts/build-v13-standalone.mjs`: valida catálogo/ativos e injeta CSS, JS e data URIs no HTML final.
- `tests/v13-catalog.test.mjs`: contrato de dados e ativos.
- `tests/v13-library.test.mjs`: comportamento da Biblioteca.
- `tests/v13-routing.test.mjs`: URL, histórico e busca.
- `tests/v13-export.test.mjs`: detalhe, zoom e exportação.
- `tests/v13-responsive.test.mjs`: regras e critérios estruturais de responsividade.
- `design-qa.md`: comparação visual final em três viewports.

---

### Task 1: Contrato do catálogo e proteção dos dados existentes

**Files:**
- Create: `src/v13/catalog.mjs`
- Create: `tests/v13-catalog.test.mjs`
- Modify: `tests/helpers/load-app.mjs`

**Interfaces:**
- Produces: `V13_FAMILIES`, `V13_CATALOG`, `validateV13Catalog(items)`.
- Item shape: `{id,family,title,use,tags,difficulty,strokes,ratio,variant,imagePath,steps,example,alt}`.
- Consumes: caminhos relativos a `assets/v13/items/*.webp`.

- [ ] **Step 1: Write the failing catalog test**

```js
import test from 'node:test';
import assert from 'node:assert/strict';
import { access } from 'node:fs/promises';
import { V13_CATALOG, validateV13Catalog } from '../src/v13/catalog.mjs';

test('catalog has 160 unique, usable legal references', async () => {
  assert.deepEqual(
    Object.fromEntries(['titulos','caixas','setas','estruturas','divisores','paginas'].map(
      family => [family, V13_CATALOG.filter(item => item.family === family).length]
    )),
    { titulos: 32, caixas: 28, setas: 30, estruturas: 28, divisores: 24, paginas: 18 }
  );
  assert.equal(new Set(V13_CATALOG.map(item => item.id)).size, 160);
  assert.deepEqual(validateV13Catalog(V13_CATALOG), []);
  await Promise.all(V13_CATALOG.map(item => access(new URL(`../${item.imagePath}`, import.meta.url))));
});
```

- [ ] **Step 2: Run test to verify RED**

Run: `node --test tests/v13-catalog.test.mjs`  
Expected: FAIL with `ERR_MODULE_NOT_FOUND` for `src/v13/catalog.mjs`.

- [ ] **Step 3: Implement schema and catalog metadata**

```js
export const V13_FAMILIES = Object.freeze([
  ['titulos','Títulos'], ['caixas','Caixas'], ['setas','Setas'],
  ['estruturas','Estruturas'], ['divisores','Divisores'], ['paginas','Páginas completas']
]);

export function validateV13Catalog(items) {
  const issues = [];
  const ids = new Set();
  for (const [index, item] of items.entries()) {
    for (const key of ['id','family','title','use','difficulty','variant','imagePath','example','alt']) {
      if (!String(item[key] || '').trim()) issues.push(`${index}:${key}`);
    }
    if (ids.has(item.id)) issues.push(`${index}:duplicate-id`);
    ids.add(item.id);
    if (!Array.isArray(item.tags) || item.tags.length < 2) issues.push(`${index}:tags`);
    if (!Array.isArray(item.steps) || item.steps.length < 2 || item.steps.length > 4) issues.push(`${index}:steps`);
    if (!Number.isInteger(item.strokes) || item.strokes < 1) issues.push(`${index}:strokes`);
  }
  return issues;
}
```

Populate all 160 entries with legal uses and unique names. The first six mixed items must be one page complete, one title, one structure, one box, one arrow and one divider so “Todos” starts with the visual variety shown in the reference screenshot.

- [ ] **Step 4: Run catalog test**

Run: `node --test tests/v13-catalog.test.mjs`  
Expected: FAIL only because the 160 image paths do not exist yet.

- [ ] **Step 5: Commit the contract**

Commit message: `Define o catálogo visual v13`

---

### Task 2: Produção e exportação dos 160 ativos

**Files:**
- Create: `assets/v13/source/*.png`
- Create: `assets/v13/items/*.webp`
- Create: `scripts/crop-v13-atlases.mjs`

**Interfaces:**
- Consumes: `V13_CATALOG[].imagePath` and 25 generated source sheets.
- Produces: one correct WEBP for every catalog item, minimum 384 × 512 for components and 512 × 900 for complete pages.

- [ ] **Step 1: Generate 25 inspected source sheets with ImageGen**

Generate original art rather than copying Pinterest content:

- 4 sheets × 8 title assets;
- 4 sheets containing 28 box assets;
- 4 sheets containing 30 arrow assets;
- 4 sheets containing 28 legal-structure assets;
- 3 sheets × 8 divider assets;
- 6 sheets × 3 complete-page assets.

Each component sheet uses a 4 × 2 atlas with equal gutters and no mark crossing a cell boundary. Each complete-page sheet uses three portrait sheets side by side. Inspect every source at original resolution before cropping; reject a sheet when text is malformed, two cells repeat the same composition, or a mark bleeds into a neighbor.

- [ ] **Step 2: Write the crop script**

```js
import { mkdir } from 'node:fs/promises';
import { spawnSync } from 'node:child_process';

export function cropSheet({source, outputs, columns, rows, width, height}) {
  const cellW = Math.floor(width / columns);
  const cellH = Math.floor(height / rows);
  outputs.forEach((output, index) => {
    const x = (index % columns) * cellW;
    const y = Math.floor(index / columns) * cellH;
    const run = spawnSync('convert', [source, '-crop', `${cellW}x${cellH}+${x}+${y}`,
      '+repage', '-strip', '-quality', '86', output], {stdio: 'inherit'});
    if (run.status !== 0) throw new Error(`crop failed: ${output}`);
  });
}
```

The script owns an explicit manifest mapping source-sheet cells to final item IDs. No output filename may be inferred only by numeric position.

- [ ] **Step 3: Run the crop script**

Run: `node scripts/crop-v13-atlases.mjs`  
Expected: 160 files under `assets/v13/items`, no process error.

- [ ] **Step 4: Inspect contact sheets of final outputs**

Run: `montage assets/v13/items/*.webp -thumbnail 240x320 -geometry +12+12 /tmp/v13-contact-sheet.jpg`.

Open `/tmp/v13-contact-sheet.jpg` and replace any blurry, repeated, malformed or semantically incorrect asset. Re-run the crop and inspection until all 160 outputs are usable.

- [ ] **Step 5: Verify GREEN**

Run: `node --test tests/v13-catalog.test.mjs`  
Expected: PASS.

- [ ] **Step 6: Commit assets**

Commit message: `Adiciona 160 referências manuscritas`

---

### Task 3: Build autônomo e shell compacto da Biblioteca

**Files:**
- Create: `scripts/build-v13-standalone.mjs`
- Create: `src/v13/library.css`
- Create: `src/v13/library-runtime.js`
- Create: `tests/v13-library.test.mjs`
- Modify: `upload/caderno-juridico-v10-gerador-completo (2).html`

**Interfaces:**
- Produces: `renderV13Library(route)`, `window.__CJ_V13_CATALOG__`, `.library-v13`.
- Consumes: validated catalog and base64 WEBPs.

- [ ] **Step 1: Write failing shell test**

```js
test('library opens as a compact Pinterest-like visual mosaic', async () => {
  const app = await loadApp();
  app.api.navigate({area: 'library'});
  await app.api.whenIdle();
  const document = app.window.document;
  assert.ok(document.querySelector('.library-v13'));
  assert.equal(document.querySelectorAll('[data-v13-family]').length, 7);
  assert.equal(document.querySelectorAll('.v13-pin').length, 160);
  assert.ok(document.querySelector('.v13-pin[data-family="paginas"]'));
  assert.equal(document.querySelector('.atelier-v12'), null);
  assert.equal(document.querySelectorAll('#global-nav [data-route]').length, 0);
});
```

- [ ] **Step 2: Run test to verify RED**

Run: `node --test tests/v13-library.test.mjs`  
Expected: FAIL because `.library-v13` is absent.

- [ ] **Step 3: Implement the standalone build**

The build script reads CSS, runtime, catalog and each image; emits:

```js
const embedded = V13_CATALOG.map(item => ({
  ...item,
  image: `data:image/webp;base64,${readFileSync(item.imagePath).toString('base64')}`
}));
```

It replaces only the content between `/* CJ_V13_CSS_START */…/* CJ_V13_CSS_END */` and `/* CJ_V13_JS_START */…/* CJ_V13_JS_END */`. If either marker pair is missing or duplicated, it exits nonzero without touching the HTML.

- [ ] **Step 4: Implement compact shell and masonry**

Use CSS columns for Pinterest rhythm and `break-inside: avoid` for pins. The first viewport starts with content, not a tutorial panel. Render UI text as HTML and all handwritten visual content as real images.

- [ ] **Step 5: Build and verify GREEN**

Run: `node scripts/build-v13-standalone.mjs && node --test tests/v13-library.test.mjs`  
Expected: PASS.

- [ ] **Step 6: Commit shell**

Commit message: `Cria o mosaico editorial da biblioteca`

---

### Task 4: Busca global, famílias e URL persistente

**Files:**
- Modify: `src/v13/library-runtime.js`
- Modify: `src/v13/library.css`
- Create: `tests/v13-routing.test.mjs`

**Interfaces:**
- Produces: `parseV13LibraryHash(hash)`, `formatV13LibraryHash(state)`, `filterV13Catalog(items,state)`.

- [ ] **Step 1: Write failing routing and search tests**

```js
test('search filters visible images and survives reload', async () => {
  const app = await loadApp({url: 'https://caderno.local/#/library/setas?q=retorno'});
  await app.api.whenIdle();
  const pins = [...app.window.document.querySelectorAll('.v13-pin:not([hidden])')];
  assert.ok(pins.length > 0);
  assert.ok(pins.every(pin => /retorno/i.test(pin.dataset.search)));
  assert.equal(app.window.document.querySelector('[data-v13-family="setas"]').getAttribute('aria-pressed'), 'true');
  assert.equal(app.window.document.querySelector('#v13-search').value, 'retorno');
});
```

- [ ] **Step 2: Run to verify RED**

Run: `node --test tests/v13-routing.test.mjs`  
Expected: FAIL because the v13 route parser is absent.

- [ ] **Step 3: Implement normalized global filtering and history**

`filterV13Catalog` searches `title`, `use`, `tags`, `difficulty`, `example` and family name through the existing `normalizeSearch`. Category selection and query updates use `history.pushState`; `popstate` and `hashchange` restore both values without clearing the search.

- [ ] **Step 4: Verify GREEN**

Run: `node scripts/build-v13-standalone.mjs && node --test tests/v13-routing.test.mjs`  
Expected: PASS.

- [ ] **Step 5: Commit search and routing**

Commit message: `Adiciona busca visual e rotas persistentes`

---

### Task 5: Detalhe didático, zoom e PNG

**Files:**
- Modify: `src/v13/library-runtime.js`
- Modify: `src/v13/library.css`
- Create: `tests/v13-export.test.mjs`

**Interfaces:**
- Produces: `openV13Detail(id,trigger)`, `closeV13Detail()`, `setV13Zoom(value)`, `exportV13Png(id)`.

- [ ] **Step 1: Write failing modal/export test**

```js
test('detail teaches the model and exports PNG', async () => {
  let downloads = 0;
  const app = await loadApp({beforeParse(window) {
    window.HTMLAnchorElement.prototype.click = function () { downloads += 1; };
  }});
  app.api.navigate({area:'library'});
  await app.api.whenIdle();
  await click(app.window.document, '.v13-pin');
  const dialog = app.window.document.querySelector('#v13-detail');
  assert.equal(dialog.hidden, false);
  assert.ok(dialog.querySelectorAll('.v13-step').length >= 2);
  assert.match(dialog.textContent, /Exemplo jurídico/);
  await click(app.window.document, '[data-v13-export]');
  await app.api.whenIdle();
  assert.equal(downloads, 1);
});
```

- [ ] **Step 2: Run to verify RED**

Run: `node --test tests/v13-export.test.mjs`  
Expected: FAIL because `#v13-detail` is absent.

- [ ] **Step 3: Implement accessible detail**

Open the selected asset at large size with name, legal use, 2–4 steps, filled example, difficulty and stroke count. Trap focus inside the dialog; Escape, overlay click and close button close it; focus returns to the originating pin.

- [ ] **Step 4: Implement zoom/pan and PNG**

Zoom values are 1, 1.5, 2 and 3. The image stage uses native overflow scrolling so touch panning works. PNG export renders only the paper/detail asset through `html2canvas`, uses `toBlob`, creates an object URL and downloads a normalized filename.

- [ ] **Step 5: Verify GREEN**

Run: `node scripts/build-v13-standalone.mjs && node --test tests/v13-export.test.mjs`  
Expected: PASS.

- [ ] **Step 6: Commit detail and export**

Commit message: `Adiciona detalhe didático e exportação PNG`

---

### Task 6: Responsividade, acessibilidade e regressão

**Files:**
- Modify: `src/v13/library.css`
- Modify: `src/v13/library-runtime.js`
- Create: `tests/v13-responsive.test.mjs`
- Modify: existing Library v12 tests to assert v13 behavior without weakening legacy coverage.

**Interfaces:**
- Consumes: all v13 selectors and catalog data.
- Produces: desktop, tablet, mobile and reduced-motion behavior.

- [ ] **Step 1: Write failing responsive assertions**

Assert a compact header, seven filter buttons, `column-width`/column counts, 44 px controls, one-column mobile pins, sticky categories and reduced-motion override. Assert that the first DOM pin belongs to `paginas` and no guide panel precedes the grid.

- [ ] **Step 2: Run to verify RED**

Run: `node --test tests/v13-responsive.test.mjs`  
Expected: FAIL on missing mobile and reduced-motion rules.

- [ ] **Step 3: Implement exact breakpoints**

- Desktop ≥ 1200: five columns, content width at least 1250 px.
- Tablet 768–1199: three columns and no side panel.
- Mobile ≤ 600: one column, 12 px gutters, compact brand/search, horizontal category strip and pins at `calc(100vw - 24px)`.
- Every button/input has at least 44 px hit height on mobile.

- [ ] **Step 4: Run all automated checks**

Run: `node scripts/build-v13-standalone.mjs && npm test`  
Expected: exit 0, all legacy and v13 tests pass, zero failures.

- [ ] **Step 5: Commit responsive polish**

Commit message: `Finaliza responsividade da biblioteca v13`

---

### Task 7: Browser QA, Library writeback and GitHub publication

**Files:**
- Replace: `design-qa.md`
- Replace in Library: `caderno-juridico-v10-gerador-completo (2).html`
- Replace on GitHub: `index.html`

**Interfaces:**
- Consumes: final standalone HTML.
- Produces: verified Library version, matching GitHub blob and GitHub Pages deployment.

- [ ] **Step 1: Start the local preview**

Serve the workspace on port 4173 with the route opening `#/library`. Use the approved browser only.

- [ ] **Step 2: Capture three required viewports**

Capture 1440 × 1000, 1024 × 768 and 390 × 844 with the same empty-search “Todos” state. Also capture one detail modal and one filtered `setas?q=retorno` state. Check console errors after each interaction.

- [ ] **Step 3: Run blocking design QA**

Combine the supplied Pinterest-board screenshot and each implementation screenshot for comparison. Fix every P0/P1/P2 issue, recapture and repeat until `design-qa.md` records typography, spacing, colors, image quality, copy, interactions, responsive evidence and exactly `final result: passed`.

- [ ] **Step 4: Fresh verification**

Run: `npm test && node scripts/build-v13-standalone.mjs && git hash-object 'upload/caderno-juridico-v10-gerador-completo (2).html'`  
Expected: tests exit 0 and a local blob SHA is printed.

- [ ] **Step 5: Replace the same Library file**

Replace `libfile_664eccff8ab4819181bfc49ba98f0d79` using its current version guard and persist returned xattrs on the local HTML.

- [ ] **Step 6: Publish to GitHub**

Fetch the current `index.html` SHA, replace it on `main` with commit message `Entrega a biblioteca manuscrita v13`, then fetch it again and verify its blob SHA equals the local `git hash-object` output.

- [ ] **Step 7: Verify GitHub Pages**

Open the deployed `#/library`, confirm the v13 heading, six families plus “Todos”, first images, search, detail modal and console state. Do not report completion if Pages still serves the previous blob.

---

## Self-Review Result

- Spec coverage: all 17 spec sections map to Tasks 1–7.
- Scope boundary: existing summaries/editor/backup are preserved but not modified.
- Placeholder scan: no deferred implementation markers are present.
- Type consistency: catalog and runtime field names are stable across all tasks.
- Visual reference update: the supplied Pinterest screenshot is explicitly represented by “Todos”, mixed content above the fold, masonry heights and varied palettes.

