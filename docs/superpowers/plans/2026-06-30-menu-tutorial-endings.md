# Menu, Tutorial e Saída de Finais — Plano de Implementação

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Adicionar tela de menu (Jogar/Tutorial/Sair), tutorial de 2 páginas e botão "Jogar Novamente" nos finais — tudo via camadas extras no layout Game existente.

**Architecture:** Duas novas layers (`Menu` e `Tutorial`) vivem no topo do layout Game. Seis novos tipos de objeto Text substituem qualquer necessidade de sprites gráficos novos. Eventos de clique controlam visibilidade de layers e o botão `btn_Reiniciar` aparece nos finais.

**Tech Stack:** Construct 3 JSON manual (layouts/, objectTypes/, eventSheets/, project.c3proj)

## Global Constraints

- Todos os nomes em português brasileiro
- Plugin `Text` (id `"Text"`) já está em `usedAddons` — não adicionar novamente
- SIDs novos: range `999000000000001`–`999000000001999` (acima do max atual `998291389101836`)
- UIDs novos: range `100`–`112` (max atual é `61`)
- Font: `"ThaleahFat"` em todos os novos Text objects
- Viewport: 1280×720; centro em (640, 360)
- Após cada tarefa: `node -e "JSON.parse(require('fs').readFileSync('<arquivo>','utf8')); console.log('OK')"` para validar JSON

---

## Arquivos Modificados/Criados

| Arquivo | Ação |
|---|---|
| `objectTypes/sf_Menu_Botao.json` | Criar |
| `objectTypes/sf_Tutorial_Titulo.json` | Criar |
| `objectTypes/sf_Tutorial_Corpo.json` | Criar |
| `objectTypes/btn_Tutorial_Proximo.json` | Criar |
| `objectTypes/btn_Tutorial_Anterior.json` | Criar |
| `objectTypes/btn_Reiniciar.json` | Criar |
| `project.c3proj` | Modificar — registrar 6 novos tipos |
| `layouts/Game.json` | Modificar — 2 novas layers + 13 novas instâncias |
| `eventSheets/Folha de eventos 1.json` | Modificar — variável + eventos |

---

## Task 1: Criar os 6 novos tipos de objeto

**Files:**
- Create: `objectTypes/sf_Menu_Botao.json`
- Create: `objectTypes/sf_Tutorial_Titulo.json`
- Create: `objectTypes/sf_Tutorial_Corpo.json`
- Create: `objectTypes/btn_Tutorial_Proximo.json`
- Create: `objectTypes/btn_Tutorial_Anterior.json`
- Create: `objectTypes/btn_Reiniciar.json`
- Modify: `project.c3proj`

- [ ] **Passo 1: Criar `objectTypes/sf_Menu_Botao.json`**

```json
{
  "name": "sf_Menu_Botao",
  "plugin-id": "Text",
  "sid": 999000000000001,
  "isGlobal": false,
  "editorNewInstanceIsReplica": true,
  "instanceVariables": [
    {
      "name": "Contexto",
      "type": "string",
      "desc": "",
      "show": true,
      "sid": 999000000000002
    }
  ],
  "behaviorTypes": [],
  "effectTypes": []
}
```

- [ ] **Passo 2: Criar `objectTypes/sf_Tutorial_Titulo.json`**

```json
{
  "name": "sf_Tutorial_Titulo",
  "plugin-id": "Text",
  "sid": 999000000000003,
  "isGlobal": false,
  "editorNewInstanceIsReplica": true,
  "instanceVariables": [],
  "behaviorTypes": [],
  "effectTypes": []
}
```

- [ ] **Passo 3: Criar `objectTypes/sf_Tutorial_Corpo.json`**

```json
{
  "name": "sf_Tutorial_Corpo",
  "plugin-id": "Text",
  "sid": 999000000000004,
  "isGlobal": false,
  "editorNewInstanceIsReplica": true,
  "instanceVariables": [],
  "behaviorTypes": [],
  "effectTypes": []
}
```

- [ ] **Passo 4: Criar `objectTypes/btn_Tutorial_Proximo.json`**

```json
{
  "name": "btn_Tutorial_Proximo",
  "plugin-id": "Text",
  "sid": 999000000000005,
  "isGlobal": false,
  "editorNewInstanceIsReplica": true,
  "instanceVariables": [],
  "behaviorTypes": [],
  "effectTypes": []
}
```

- [ ] **Passo 5: Criar `objectTypes/btn_Tutorial_Anterior.json`**

```json
{
  "name": "btn_Tutorial_Anterior",
  "plugin-id": "Text",
  "sid": 999000000000006,
  "isGlobal": false,
  "editorNewInstanceIsReplica": true,
  "instanceVariables": [],
  "behaviorTypes": [],
  "effectTypes": []
}
```

- [ ] **Passo 6: Criar `objectTypes/btn_Reiniciar.json`**

```json
{
  "name": "btn_Reiniciar",
  "plugin-id": "Text",
  "sid": 999000000000007,
  "isGlobal": false,
  "editorNewInstanceIsReplica": true,
  "instanceVariables": [],
  "behaviorTypes": [],
  "effectTypes": []
}
```

- [ ] **Passo 7: Registrar os 6 tipos em `project.c3proj`**

Em `project.c3proj`, localizar o array `"objectTypes": { "items": [...] }` e adicionar os 6 nomes ao final da lista (antes do `]` que fecha `items`):

```json
"sf_Menu_Botao",
"sf_Tutorial_Titulo",
"sf_Tutorial_Corpo",
"btn_Tutorial_Proximo",
"btn_Tutorial_Anterior",
"btn_Reiniciar"
```

- [ ] **Passo 8: Validar JSONs**

```bash
node -e "
['project.c3proj','objectTypes/sf_Menu_Botao.json','objectTypes/sf_Tutorial_Titulo.json','objectTypes/sf_Tutorial_Corpo.json','objectTypes/btn_Tutorial_Proximo.json','objectTypes/btn_Tutorial_Anterior.json','objectTypes/btn_Reiniciar.json'].forEach(f => {
  JSON.parse(require('fs').readFileSync(f,'utf8'));
  console.log('OK:', f);
});"
```

Esperado: `OK:` para cada arquivo.

- [ ] **Passo 9: Commit**

```bash
git add objectTypes/sf_Menu_Botao.json objectTypes/sf_Tutorial_Titulo.json objectTypes/sf_Tutorial_Corpo.json objectTypes/btn_Tutorial_Proximo.json objectTypes/btn_Tutorial_Anterior.json objectTypes/btn_Reiniciar.json project.c3proj
git commit -m "feat: adicionar tipos de objeto para menu e tutorial"
```

---

## Task 2: Adicionar variável global e layers em Game.json

**Files:**
- Modify: `eventSheets/Folha de eventos 1.json`
- Modify: `layouts/Game.json`

### Variável global `Tutorial_Pagina`

- [ ] **Passo 1: Adicionar variável em `Folha de eventos 1.json`**

Localizar o último objeto com `"eventType": "variable"` no array `events` (procurar pelo `sid: 555902019077242` — variável `Dilema_Tipo`). Inserir APÓS ele:

```json
{
  "eventType": "variable",
  "name": "Tutorial_Pagina",
  "type": "number",
  "initialValue": "1",
  "comment": "",
  "isStatic": false,
  "isConstant": false,
  "sid": 999000000001000
}
```

### Layers Menu e Tutorial em Game.json

- [ ] **Passo 2: Adicionar layer `Menu` em `layouts/Game.json`**

O array `layers` tem 3 items: `Background`, `Game`, `UI`. Adicionar após `UI` (ao final do array):

```json
{
  "name": "Menu",
  "overriden": 0,
  "subLayers": [],
  "sid": 999000000000008,
  "effectTypes": [],
  "isInitiallyVisible": true,
  "isInitiallyInteractive": true,
  "isHTMLElementsLayer": false,
  "color": [1, 1, 1, 1],
  "backgroundColor": [0, 0, 0, 1],
  "isTransparent": true,
  "sampling": "auto",
  "parallaxX": 0,
  "parallaxY": 0,
  "scaleRate": 0,
  "forceOwnTexture": false,
  "renderingMode": "3d",
  "drawOrder": "z-order",
  "useRenderCells": false,
  "blendMode": "normal",
  "zElevation": 0,
  "global": false,
  "instances": []
}
```

- [ ] **Passo 3: Adicionar layer `Tutorial` logo após `Menu`**

```json
{
  "name": "Tutorial",
  "overriden": 0,
  "subLayers": [],
  "sid": 999000000000009,
  "effectTypes": [],
  "isInitiallyVisible": false,
  "isInitiallyInteractive": true,
  "isHTMLElementsLayer": false,
  "color": [1, 1, 1, 1],
  "backgroundColor": [0, 0, 0, 1],
  "isTransparent": true,
  "sampling": "auto",
  "parallaxX": 0,
  "parallaxY": 0,
  "scaleRate": 0,
  "forceOwnTexture": false,
  "renderingMode": "3d",
  "drawOrder": "z-order",
  "useRenderCells": false,
  "blendMode": "normal",
  "zElevation": 0,
  "global": false,
  "instances": []
}
```

- [ ] **Passo 4: Validar**

```bash
node -e "
const d = JSON.parse(require('fs').readFileSync('layouts/Game.json','utf8'));
console.log('Layers:', d.layers.map(l=>l.name));
"
```

Esperado: `Layers: [ 'Background', 'Game', 'UI', 'Menu', 'Tutorial' ]`

- [ ] **Passo 5: Commit**

```bash
git add eventSheets/"Folha de eventos 1.json" layouts/Game.json
git commit -m "feat: adicionar variável Tutorial_Pagina e layers Menu/Tutorial"
```

---

## Task 3: Adicionar instâncias na layer Menu

**Files:**
- Modify: `layouts/Game.json` — array `instances` da layer `Menu`

Todas as instâncias abaixo vão no `"instances": []` da layer `Menu`.

- [ ] **Passo 1: Adicionar fundo escuro (Filtro_Dilema)**

```json
{
  "type": "Filtro_Dilema",
  "properties": {
    "initially-visible": true,
    "initial-animation": "Animation 1",
    "initial-frame": 0,
    "enable-collisions": false,
    "live-preview": false
  },
  "uid": 100,
  "sid": 999000000000100,
  "tags": "",
  "instanceVariables": {},
  "behaviors": {},
  "showing": true,
  "locked": false,
  "world": {
    "x": 640,
    "y": 360,
    "width": 1280,
    "height": 720,
    "originX": 0.5,
    "originY": 0.5,
    "color": [0, 0, 0, 1],
    "z": 0,
    "angle": 0
  }
}
```

- [ ] **Passo 2: Adicionar título PORRILAND**

```json
{
  "type": "sf_Menu_Botao",
  "properties": {
    "text": "PORRILAND",
    "enable-bbcode": false,
    "font": "ThaleahFat",
    "size": 64,
    "line-height": 0,
    "bold": false,
    "italic": false,
    "color": [1, 0.9, 0.3, 1],
    "horizontal-alignment": "center",
    "vertical-alignment": "top",
    "wrapping": "word",
    "text-direction": "ltr",
    "icon-set": -1,
    "initially-visible": true,
    "origin": "top-left",
    "read-aloud": false
  },
  "uid": 101,
  "sid": 999000000000101,
  "tags": "",
  "instanceVariables": {"Contexto": "Titulo"},
  "behaviors": {},
  "showing": true,
  "locked": false,
  "world": {
    "x": 390,
    "y": 160,
    "width": 500,
    "height": 100,
    "originX": 0,
    "originY": 0,
    "color": [1, 1, 1, 1],
    "z": 1,
    "angle": 0
  }
}
```

- [ ] **Passo 3: Adicionar botão JOGAR**

```json
{
  "type": "sf_Menu_Botao",
  "properties": {
    "text": "JOGAR",
    "enable-bbcode": false,
    "font": "ThaleahFat",
    "size": 32,
    "line-height": 0,
    "bold": false,
    "italic": false,
    "color": [1, 1, 1, 1],
    "horizontal-alignment": "center",
    "vertical-alignment": "top",
    "wrapping": "word",
    "text-direction": "ltr",
    "icon-set": -1,
    "initially-visible": true,
    "origin": "top-left",
    "read-aloud": false
  },
  "uid": 102,
  "sid": 999000000000102,
  "tags": "",
  "instanceVariables": {"Contexto": "Jogar"},
  "behaviors": {},
  "showing": true,
  "locked": false,
  "world": {
    "x": 490,
    "y": 310,
    "width": 300,
    "height": 50,
    "originX": 0,
    "originY": 0,
    "color": [1, 1, 1, 1],
    "z": 1,
    "angle": 0
  }
}
```

- [ ] **Passo 4: Adicionar botão TUTORIAL**

```json
{
  "type": "sf_Menu_Botao",
  "properties": {
    "text": "TUTORIAL",
    "enable-bbcode": false,
    "font": "ThaleahFat",
    "size": 32,
    "line-height": 0,
    "bold": false,
    "italic": false,
    "color": [1, 1, 1, 1],
    "horizontal-alignment": "center",
    "vertical-alignment": "top",
    "wrapping": "word",
    "text-direction": "ltr",
    "icon-set": -1,
    "initially-visible": true,
    "origin": "top-left",
    "read-aloud": false
  },
  "uid": 103,
  "sid": 999000000000103,
  "tags": "",
  "instanceVariables": {"Contexto": "Tutorial"},
  "behaviors": {},
  "showing": true,
  "locked": false,
  "world": {
    "x": 490,
    "y": 380,
    "width": 300,
    "height": 50,
    "originX": 0,
    "originY": 0,
    "color": [1, 1, 1, 1],
    "z": 1,
    "angle": 0
  }
}
```

- [ ] **Passo 5: Adicionar botão SAIR**

```json
{
  "type": "sf_Menu_Botao",
  "properties": {
    "text": "SAIR",
    "enable-bbcode": false,
    "font": "ThaleahFat",
    "size": 32,
    "line-height": 0,
    "bold": false,
    "italic": false,
    "color": [1, 1, 1, 1],
    "horizontal-alignment": "center",
    "vertical-alignment": "top",
    "wrapping": "word",
    "text-direction": "ltr",
    "icon-set": -1,
    "initially-visible": true,
    "origin": "top-left",
    "read-aloud": false
  },
  "uid": 104,
  "sid": 999000000000104,
  "tags": "",
  "instanceVariables": {"Contexto": "Sair"},
  "behaviors": {},
  "showing": true,
  "locked": false,
  "world": {
    "x": 490,
    "y": 450,
    "width": 300,
    "height": 50,
    "originX": 0,
    "originY": 0,
    "color": [1, 1, 1, 1],
    "z": 1,
    "angle": 0
  }
}
```

- [ ] **Passo 6: Validar**

```bash
node -e "
const d = JSON.parse(require('fs').readFileSync('layouts/Game.json','utf8'));
const menu = d.layers.find(l=>l.name==='Menu');
console.log('Menu instances:', menu.instances.map(i=>i.type+':'+i.uid));
"
```

Esperado: `Menu instances: [ 'Filtro_Dilema:100', 'sf_Menu_Botao:101', 'sf_Menu_Botao:102', 'sf_Menu_Botao:103', 'sf_Menu_Botao:104' ]`

- [ ] **Passo 7: Commit**

```bash
git add layouts/Game.json
git commit -m "feat: adicionar instâncias da layer Menu"
```

---

## Task 4: Adicionar instâncias na layer Tutorial

**Files:**
- Modify: `layouts/Game.json` — array `instances` da layer `Tutorial`

- [ ] **Passo 1: Adicionar fundo escuro (Filtro_Dilema)**

```json
{
  "type": "Filtro_Dilema",
  "properties": {
    "initially-visible": true,
    "initial-animation": "Animation 1",
    "initial-frame": 0,
    "enable-collisions": false,
    "live-preview": false
  },
  "uid": 105,
  "sid": 999000000000105,
  "tags": "",
  "instanceVariables": {},
  "behaviors": {},
  "showing": true,
  "locked": false,
  "world": {
    "x": 640,
    "y": 360,
    "width": 1280,
    "height": 720,
    "originX": 0.5,
    "originY": 0.5,
    "color": [0, 0, 0, 1],
    "z": 0,
    "angle": 0
  }
}
```

- [ ] **Passo 2: Adicionar painel (Painel_Dilema)**

```json
{
  "type": "Painel_Dilema",
  "properties": {
    "initially-visible": true,
    "initial-animation": "Animation 1",
    "initial-frame": 0,
    "enable-collisions": false,
    "live-preview": false
  },
  "uid": 106,
  "sid": 999000000000106,
  "tags": "",
  "instanceVariables": {},
  "behaviors": {},
  "showing": true,
  "locked": false,
  "world": {
    "x": 640,
    "y": 360,
    "width": 700,
    "height": 500,
    "originX": 0.5,
    "originY": 0.5,
    "color": [1, 1, 1, 1],
    "z": 1,
    "angle": 0
  }
}
```

- [ ] **Passo 3: Adicionar título da página tutorial**

```json
{
  "type": "sf_Tutorial_Titulo",
  "properties": {
    "text": "O MUNDO",
    "enable-bbcode": false,
    "font": "ThaleahFat",
    "size": 28,
    "line-height": 0,
    "bold": false,
    "italic": false,
    "color": [0.12, 0.07, 0.03, 1],
    "horizontal-alignment": "center",
    "vertical-alignment": "top",
    "wrapping": "word",
    "text-direction": "ltr",
    "icon-set": -1,
    "initially-visible": true,
    "origin": "top-left",
    "read-aloud": false
  },
  "uid": 107,
  "sid": 999000000000107,
  "tags": "",
  "instanceVariables": {},
  "behaviors": {},
  "showing": true,
  "locked": false,
  "world": {
    "x": 290,
    "y": 125,
    "width": 700,
    "height": 50,
    "originX": 0,
    "originY": 0,
    "color": [1, 1, 1, 1],
    "z": 2,
    "angle": 0
  }
}
```

- [ ] **Passo 4: Adicionar corpo da página tutorial**

```json
{
  "type": "sf_Tutorial_Corpo",
  "properties": {
    "text": "ADEPTOS — fiéis comuns. Geram Fé e se reproduzem quando felizes.\n\nCÉTICOS — duvidam. Reduzem Fé e afetam Adeptos próximos.\n\nFANÁTICOS — surgem com Fé alta. Perseguem e matam.\n\nFÉ:\n0–49   CETICISMO (Céticos se espalham)\n50–70  EQUILÍBRIO (a civilização só evolui aqui)\n71–100 FANATISMO (Fanáticos surgem)",
    "enable-bbcode": false,
    "font": "ThaleahFat",
    "size": 16,
    "line-height": 4,
    "bold": false,
    "italic": false,
    "color": [0.12, 0.07, 0.03, 1],
    "horizontal-alignment": "left",
    "vertical-alignment": "top",
    "wrapping": "word",
    "text-direction": "ltr",
    "icon-set": -1,
    "initially-visible": true,
    "origin": "top-left",
    "read-aloud": false
  },
  "uid": 108,
  "sid": 999000000000108,
  "tags": "",
  "instanceVariables": {},
  "behaviors": {},
  "showing": true,
  "locked": false,
  "world": {
    "x": 300,
    "y": 185,
    "width": 680,
    "height": 300,
    "originX": 0,
    "originY": 0,
    "color": [1, 1, 1, 1],
    "z": 2,
    "angle": 0
  }
}
```

- [ ] **Passo 5: Adicionar botão PRÓXIMO**

```json
{
  "type": "btn_Tutorial_Proximo",
  "properties": {
    "text": "PRÓXIMO ›",
    "enable-bbcode": false,
    "font": "ThaleahFat",
    "size": 22,
    "line-height": 0,
    "bold": false,
    "italic": false,
    "color": [0.12, 0.07, 0.03, 1],
    "horizontal-alignment": "center",
    "vertical-alignment": "top",
    "wrapping": "word",
    "text-direction": "ltr",
    "icon-set": -1,
    "initially-visible": true,
    "origin": "top-left",
    "read-aloud": false
  },
  "uid": 109,
  "sid": 999000000000109,
  "tags": "",
  "instanceVariables": {},
  "behaviors": {},
  "showing": true,
  "locked": false,
  "world": {
    "x": 820,
    "y": 570,
    "width": 160,
    "height": 40,
    "originX": 0,
    "originY": 0,
    "color": [1, 1, 1, 1],
    "z": 2,
    "angle": 0
  }
}
```

- [ ] **Passo 6: Adicionar botão ANTERIOR (começa invisível)**

```json
{
  "type": "btn_Tutorial_Anterior",
  "properties": {
    "text": "‹ ANTERIOR",
    "enable-bbcode": false,
    "font": "ThaleahFat",
    "size": 22,
    "line-height": 0,
    "bold": false,
    "italic": false,
    "color": [0.12, 0.07, 0.03, 1],
    "horizontal-alignment": "center",
    "vertical-alignment": "top",
    "wrapping": "word",
    "text-direction": "ltr",
    "icon-set": -1,
    "initially-visible": false,
    "origin": "top-left",
    "read-aloud": false
  },
  "uid": 110,
  "sid": 999000000000110,
  "tags": "",
  "instanceVariables": {},
  "behaviors": {},
  "showing": false,
  "locked": false,
  "world": {
    "x": 300,
    "y": 570,
    "width": 160,
    "height": 40,
    "originX": 0,
    "originY": 0,
    "color": [1, 1, 1, 1],
    "z": 2,
    "angle": 0
  }
}
```

- [ ] **Passo 7: Adicionar botão VOLTAR**

```json
{
  "type": "sf_Menu_Botao",
  "properties": {
    "text": "VOLTAR",
    "enable-bbcode": false,
    "font": "ThaleahFat",
    "size": 22,
    "line-height": 0,
    "bold": false,
    "italic": false,
    "color": [0.12, 0.07, 0.03, 1],
    "horizontal-alignment": "center",
    "vertical-alignment": "top",
    "wrapping": "word",
    "text-direction": "ltr",
    "icon-set": -1,
    "initially-visible": true,
    "origin": "top-left",
    "read-aloud": false
  },
  "uid": 111,
  "sid": 999000000000111,
  "tags": "",
  "instanceVariables": {"Contexto": "Voltar"},
  "behaviors": {},
  "showing": true,
  "locked": false,
  "world": {
    "x": 540,
    "y": 570,
    "width": 200,
    "height": 40,
    "originX": 0,
    "originY": 0,
    "color": [1, 1, 1, 1],
    "z": 2,
    "angle": 0
  }
}
```

- [ ] **Passo 8: Validar**

```bash
node -e "
const d = JSON.parse(require('fs').readFileSync('layouts/Game.json','utf8'));
const tut = d.layers.find(l=>l.name==='Tutorial');
console.log('Tutorial instances:', tut.instances.map(i=>i.type+':'+i.uid));
console.log('Tutorial visível?', tut.isInitiallyVisible);
"
```

Esperado: 7 instâncias listadas; `Tutorial visível? false`.

- [ ] **Passo 9: Commit**

```bash
git add layouts/Game.json
git commit -m "feat: adicionar instâncias da layer Tutorial"
```

---

## Task 5: Adicionar btn_Reiniciar na layer UI

**Files:**
- Modify: `layouts/Game.json` — array `instances` da layer `UI`

- [ ] **Passo 1: Adicionar instância btn_Reiniciar**

No array `instances` da layer `"UI"` (a terceira layer), inserir ao final:

```json
{
  "type": "btn_Reiniciar",
  "properties": {
    "text": "JOGAR NOVAMENTE",
    "enable-bbcode": false,
    "font": "ThaleahFat",
    "size": 22,
    "line-height": 0,
    "bold": false,
    "italic": false,
    "color": [0.12, 0.07, 0.03, 1],
    "horizontal-alignment": "center",
    "vertical-alignment": "top",
    "wrapping": "word",
    "text-direction": "ltr",
    "icon-set": -1,
    "initially-visible": false,
    "origin": "top-left",
    "read-aloud": false
  },
  "uid": 112,
  "sid": 999000000000112,
  "tags": "",
  "instanceVariables": {},
  "behaviors": {},
  "showing": false,
  "locked": false,
  "world": {
    "x": 490,
    "y": 600,
    "width": 300,
    "height": 40,
    "originX": 0,
    "originY": 0,
    "color": [1, 1, 1, 1],
    "z": 0,
    "angle": 0
  }
}
```

- [ ] **Passo 2: Validar**

```bash
node -e "
const d = JSON.parse(require('fs').readFileSync('layouts/Game.json','utf8'));
const ui = d.layers.find(l=>l.name==='UI');
const r = ui.instances.find(i=>i.uid===112);
console.log('btn_Reiniciar:', r ? r.type + ' showing=' + r.showing : 'NÃO ENCONTRADO');
"
```

Esperado: `btn_Reiniciar: btn_Reiniciar showing=false`

- [ ] **Passo 3: Commit**

```bash
git add layouts/Game.json
git commit -m "feat: adicionar btn_Reiniciar na layer UI"
```

---

## Task 6: Modificar evento on-start-of-layout

**Files:**
- Modify: `eventSheets/Folha de eventos 1.json`

O evento on-start-of-layout tem `sid: 315559751233962`. Está no path `events[33].children[0]`. Sua lista de actions contém 5 ações. Precisamos:
1. Remover as 2 ações de timer (sids `426093821802008` e `940129668594202`) — elas serão movidas para o evento de Jogar
2. Adicionar ação `set-time-scale 0` no início

- [ ] **Passo 1: Editar a lista `actions` do evento sid `315559751233962`**

Substituir o array `actions` completo do evento por:

```json
[
  {
    "id": "set-time-scale",
    "objectClass": "System",
    "sid": 999000000001001,
    "parameters": {
      "time-scale": "0"
    }
  },
  {
    "id": "set-range",
    "objectClass": "Porri´s",
    "sid": 505900547021874,
    "behaviorType": "CampoDeVisão",
    "parameters": {
      "range": "200"
    }
  },
  {
    "id": "set-cone-of-view",
    "objectClass": "Porri´s",
    "sid": 721241560619267,
    "behaviorType": "CampoDeVisão",
    "parameters": {
      "cone-of-view": "360"
    }
  },
  {
    "id": "set-cursor-style2",
    "objectClass": "Mouse",
    "sid": 607500060102074,
    "parameters": {
      "cursor-style": "none"
    }
  }
]
```

> Nota: os SIDs 505900547021874, 721241560619267 e 607500060102074 são das ações originais — mantê-los garante que o C3 não os trate como duplicatas.

- [ ] **Passo 2: Validar**

```bash
node -e "
const d = JSON.parse(require('fs').readFileSync('eventSheets/Folha de eventos 1.json','utf8'));
function find(evs, sid) {
  for(const e of evs) {
    if(e.sid===sid) return e;
    if(e.children) { const r=find(e.children,sid); if(r) return r; }
  }
}
const ev = find(d.events, 315559751233962);
console.log('actions count:', ev.actions.length);
console.log('first action:', ev.actions[0].id);
console.log('timer presente?', ev.actions.some(a=>a.id==='start-timer'));
"
```

Esperado: `actions count: 4`, `first action: set-time-scale`, `timer presente? false`

- [ ] **Passo 3: Commit**

```bash
git add eventSheets/"Folha de eventos 1.json"
git commit -m "feat: pausar jogo no início até o menu ser dispensado"
```

---

## Task 7: Adicionar eventos de menu

**Files:**
- Modify: `eventSheets/Folha de eventos 1.json` — inserir 4 blocos no final do array `events`

Inserir os 4 eventos abaixo ao final do array `events` (antes do `]` final):

- [ ] **Passo 1: Evento — Clique em JOGAR**

```json
{
  "eventType": "block",
  "conditions": [
    {
      "id": "on-object-clicked",
      "objectClass": "Mouse",
      "sid": 999000000001100,
      "parameters": {
        "mouse-button": "left",
        "click-type": "clicked",
        "object-clicked": "sf_Menu_Botao"
      }
    },
    {
      "id": "compare-instance-variable",
      "objectClass": "sf_Menu_Botao",
      "sid": 999000000001101,
      "parameters": {
        "instance-variable": "Contexto",
        "comparison": 0,
        "value": "\"Jogar\""
      }
    }
  ],
  "actions": [
    {
      "id": "set-time-scale",
      "objectClass": "System",
      "sid": 999000000001102,
      "parameters": {
        "time-scale": "1"
      }
    },
    {
      "id": "set-layer-visible",
      "objectClass": "System",
      "sid": 999000000001103,
      "parameters": {
        "layer": "\"Menu\"",
        "visible": "false"
      }
    },
    {
      "id": "start-timer",
      "objectClass": "Controlador",
      "sid": 999000000001104,
      "behaviorType": "Cronômetro",
      "parameters": {
        "duration": "random(90,150)",
        "type": "once",
        "tag": "\"Dilema\""
      }
    },
    {
      "id": "start-timer",
      "objectClass": "Controlador",
      "sid": 999000000001105,
      "behaviorType": "Cronômetro",
      "parameters": {
        "duration": "10",
        "type": "once",
        "tag": "\"Cetico_Aleatorio\""
      }
    }
  ],
  "sid": 999000000001099
}
```

- [ ] **Passo 2: Evento — Clique em TUTORIAL**

```json
{
  "eventType": "block",
  "conditions": [
    {
      "id": "on-object-clicked",
      "objectClass": "Mouse",
      "sid": 999000000001200,
      "parameters": {
        "mouse-button": "left",
        "click-type": "clicked",
        "object-clicked": "sf_Menu_Botao"
      }
    },
    {
      "id": "compare-instance-variable",
      "objectClass": "sf_Menu_Botao",
      "sid": 999000000001201,
      "parameters": {
        "instance-variable": "Contexto",
        "comparison": 0,
        "value": "\"Tutorial\""
      }
    }
  ],
  "actions": [
    {
      "id": "set-layer-visible",
      "objectClass": "System",
      "sid": 999000000001202,
      "parameters": {
        "layer": "\"Menu\"",
        "visible": "false"
      }
    },
    {
      "id": "set-layer-visible",
      "objectClass": "System",
      "sid": 999000000001203,
      "parameters": {
        "layer": "\"Tutorial\"",
        "visible": "true"
      }
    },
    {
      "id": "set-eventvar-value",
      "objectClass": "System",
      "sid": 999000000001204,
      "parameters": {
        "variable": "Tutorial_Pagina",
        "value": "1"
      }
    },
    {
      "id": "set-text",
      "objectClass": "sf_Tutorial_Titulo",
      "sid": 999000000001205,
      "parameters": {
        "text": "\"O MUNDO\""
      }
    },
    {
      "id": "set-text",
      "objectClass": "sf_Tutorial_Corpo",
      "sid": 999000000001206,
      "parameters": {
        "text": "\"ADEPTOS — fiéis comuns. Geram Fé e se reproduzem quando felizes.\" & newline & newline & \"CÉTICOS — duvidam. Reduzem Fé e afetam Adeptos próximos.\" & newline & newline & \"FANÁTICOS — surgem com Fé alta. Perseguem e matam.\" & newline & newline & \"FÉ:\" & newline & \"0–49   CETICISMO  (Céticos se espalham)\" & newline & \"50–70  EQUILÍBRIO (a civilização só evolui aqui)\" & newline & \"71–100 FANATISMO  (Fanáticos surgem)\""
      }
    },
    {
      "id": "set-visible",
      "objectClass": "btn_Tutorial_Proximo",
      "sid": 999000000001207,
      "parameters": {
        "visibility": "visible"
      }
    },
    {
      "id": "set-visible",
      "objectClass": "btn_Tutorial_Anterior",
      "sid": 999000000001208,
      "parameters": {
        "visibility": "invisible"
      }
    }
  ],
  "sid": 999000000001199
}
```

- [ ] **Passo 3: Evento — Clique em SAIR**

```json
{
  "eventType": "block",
  "conditions": [
    {
      "id": "on-object-clicked",
      "objectClass": "Mouse",
      "sid": 999000000001300,
      "parameters": {
        "mouse-button": "left",
        "click-type": "clicked",
        "object-clicked": "sf_Menu_Botao"
      }
    },
    {
      "id": "compare-instance-variable",
      "objectClass": "sf_Menu_Botao",
      "sid": 999000000001301,
      "parameters": {
        "instance-variable": "Contexto",
        "comparison": 0,
        "value": "\"Sair\""
      }
    }
  ],
  "actions": [
    {
      "id": "close",
      "objectClass": "System",
      "sid": 999000000001302
    }
  ],
  "sid": 999000000001299
}
```

- [ ] **Passo 4: Evento — Clique em VOLTAR**

```json
{
  "eventType": "block",
  "conditions": [
    {
      "id": "on-object-clicked",
      "objectClass": "Mouse",
      "sid": 999000000001400,
      "parameters": {
        "mouse-button": "left",
        "click-type": "clicked",
        "object-clicked": "sf_Menu_Botao"
      }
    },
    {
      "id": "compare-instance-variable",
      "objectClass": "sf_Menu_Botao",
      "sid": 999000000001401,
      "parameters": {
        "instance-variable": "Contexto",
        "comparison": 0,
        "value": "\"Voltar\""
      }
    }
  ],
  "actions": [
    {
      "id": "set-layer-visible",
      "objectClass": "System",
      "sid": 999000000001402,
      "parameters": {
        "layer": "\"Tutorial\"",
        "visible": "false"
      }
    },
    {
      "id": "set-layer-visible",
      "objectClass": "System",
      "sid": 999000000001403,
      "parameters": {
        "layer": "\"Menu\"",
        "visible": "true"
      }
    }
  ],
  "sid": 999000000001399
}
```

- [ ] **Passo 5: Validar**

```bash
node -e "
const d = JSON.parse(require('fs').readFileSync('eventSheets/Folha de eventos 1.json','utf8'));
const sids = [999000000001099, 999000000001199, 999000000001299, 999000000001399];
sids.forEach(s => {
  const e = d.events.find(ev => ev.sid === s);
  console.log(s, e ? 'OK: ' + e.conditions[1].parameters['value'] : 'NÃO ENCONTRADO');
});
"
```

Esperado: cada SID OK com o valor de Contexto correto.

- [ ] **Passo 6: Commit**

```bash
git add eventSheets/"Folha de eventos 1.json"
git commit -m "feat: eventos de clique do menu (Jogar, Tutorial, Sair, Voltar)"
```

---

## Task 8: Adicionar eventos de navegação do tutorial

**Files:**
- Modify: `eventSheets/Folha de eventos 1.json` — inserir 2 blocos no final do array `events`

- [ ] **Passo 1: Evento — Clique em PRÓXIMO**

```json
{
  "eventType": "block",
  "conditions": [
    {
      "id": "on-object-clicked",
      "objectClass": "Mouse",
      "sid": 999000000001500,
      "parameters": {
        "mouse-button": "left",
        "click-type": "clicked",
        "object-clicked": "btn_Tutorial_Proximo"
      }
    }
  ],
  "actions": [
    {
      "id": "set-eventvar-value",
      "objectClass": "System",
      "sid": 999000000001501,
      "parameters": {
        "variable": "Tutorial_Pagina",
        "value": "2"
      }
    },
    {
      "id": "set-text",
      "objectClass": "sf_Tutorial_Titulo",
      "sid": 999000000001502,
      "parameters": {
        "text": "\"SEUS PODERES\""
      }
    },
    {
      "id": "set-text",
      "objectClass": "sf_Tutorial_Corpo",
      "sid": 999000000001503,
      "parameters": {
        "text": "\"TOQUE  (2s)   — mova qualquer Porri.\" & newline & newline & \"RAIO   (5s)   — elimina Céticos e Fanáticos.\" & newline & newline & \"SUSSURRO (10s) — converte e fortalece Adeptos.\" & newline & newline & \"DILEMAS surgem a cada ~2 min e pausam o jogo.\" & newline & \"Toda escolha tem um custo. Não há resposta perfeita.\""
      }
    },
    {
      "id": "set-visible",
      "objectClass": "btn_Tutorial_Proximo",
      "sid": 999000000001504,
      "parameters": {
        "visibility": "invisible"
      }
    },
    {
      "id": "set-visible",
      "objectClass": "btn_Tutorial_Anterior",
      "sid": 999000000001505,
      "parameters": {
        "visibility": "visible"
      }
    }
  ],
  "sid": 999000000001499
}
```

- [ ] **Passo 2: Evento — Clique em ANTERIOR**

```json
{
  "eventType": "block",
  "conditions": [
    {
      "id": "on-object-clicked",
      "objectClass": "Mouse",
      "sid": 999000000001600,
      "parameters": {
        "mouse-button": "left",
        "click-type": "clicked",
        "object-clicked": "btn_Tutorial_Anterior"
      }
    }
  ],
  "actions": [
    {
      "id": "set-eventvar-value",
      "objectClass": "System",
      "sid": 999000000001601,
      "parameters": {
        "variable": "Tutorial_Pagina",
        "value": "1"
      }
    },
    {
      "id": "set-text",
      "objectClass": "sf_Tutorial_Titulo",
      "sid": 999000000001602,
      "parameters": {
        "text": "\"O MUNDO\""
      }
    },
    {
      "id": "set-text",
      "objectClass": "sf_Tutorial_Corpo",
      "sid": 999000000001603,
      "parameters": {
        "text": "\"ADEPTOS — fiéis comuns. Geram Fé e se reproduzem quando felizes.\" & newline & newline & \"CÉTICOS — duvidam. Reduzem Fé e afetam Adeptos próximos.\" & newline & newline & \"FANÁTICOS — surgem com Fé alta. Perseguem e matam.\" & newline & newline & \"FÉ:\" & newline & \"0–49   CETICISMO  (Céticos se espalham)\" & newline & \"50–70  EQUILÍBRIO (a civilização só evolui aqui)\" & newline & \"71–100 FANATISMO  (Fanáticos surgem)\""
      }
    },
    {
      "id": "set-visible",
      "objectClass": "btn_Tutorial_Proximo",
      "sid": 999000000001604,
      "parameters": {
        "visibility": "visible"
      }
    },
    {
      "id": "set-visible",
      "objectClass": "btn_Tutorial_Anterior",
      "sid": 999000000001605,
      "parameters": {
        "visibility": "invisible"
      }
    }
  ],
  "sid": 999000000001599
}
```

- [ ] **Passo 3: Validar**

```bash
node -e "
const d = JSON.parse(require('fs').readFileSync('eventSheets/Folha de eventos 1.json','utf8'));
[999000000001499, 999000000001599].forEach(s => {
  const e = d.events.find(ev => ev.sid === s);
  console.log(s, e ? 'OK: ' + e.conditions[0].parameters['object-clicked'] : 'NÃO ENCONTRADO');
});
"
```

Esperado: `OK: btn_Tutorial_Proximo` e `OK: btn_Tutorial_Anterior`.

- [ ] **Passo 4: Commit**

```bash
git add eventSheets/"Folha de eventos 1.json"
git commit -m "feat: navegação entre páginas do tutorial"
```

---

## Task 9: Adicionar eventos de saída nos finais

**Files:**
- Modify: `eventSheets/Folha de eventos 1.json`

Há 6 blocos de eventos que exibem texto de final. Em cada um, adicionar a action `set-visible btn_Reiniciar → visible`. Depois, adicionar 1 novo bloco para o clique em btn_Reiniciar.

Os 6 blocos alvo, identificados pelo SID:

| Final | SID do bloco |
|---|---|
| O Deus Vivo | `990000000000070` |
| A Partida | `990000000000075` |
| O Senhor | `990000000000080` |
| O Pastor | `990000000000087` |
| FinalEncarnacao (Sim) | `990000000000172` |
| FinalPastor/secret (Não) | `990000000000180` |

- [ ] **Passo 1: Adicionar action em cada bloco de final**

Para cada um dos 6 blocos acima, localizar o bloco pelo SID na árvore de eventos e adicionar esta action ao final do array `actions`:

```json
{
  "id": "set-visible",
  "objectClass": "btn_Reiniciar",
  "sid": 999000000001801,
  "parameters": {
    "visibility": "visible"
  }
}
```

> Usar SIDs distintos para cada: `999000000001801` (Deus Vivo), `999000000001802` (Partida), `999000000001803` (Senhor), `999000000001804` (Pastor), `999000000001805` (Encarnação), `999000000001806` (Pastor/secret).

- [ ] **Passo 2: Adicionar evento — Clique em JOGAR NOVAMENTE**

Inserir ao final do array `events`:

```json
{
  "eventType": "block",
  "conditions": [
    {
      "id": "on-object-clicked",
      "objectClass": "Mouse",
      "sid": 999000000001700,
      "parameters": {
        "mouse-button": "left",
        "click-type": "clicked",
        "object-clicked": "btn_Reiniciar"
      }
    }
  ],
  "actions": [
    {
      "id": "restart-layout",
      "objectClass": "System",
      "sid": 999000000001701
    }
  ],
  "sid": 999000000001699
}
```

- [ ] **Passo 3: Validar**

```bash
node -e "
const d = JSON.parse(require('fs').readFileSync('eventSheets/Folha de eventos 1.json','utf8'));
// Verificar os 6 blocos
function find(evs, sid) {
  for(const e of evs) {
    if(e.sid===sid) return e;
    if(e.children) { const r=find(e.children,sid); if(r) return r; }
  }
}
[990000000000070,990000000000075,990000000000080,990000000000087,990000000000172,990000000000180].forEach(s => {
  const e = find(d.events, s);
  const hasRei = e && e.actions.some(a=>a.objectClass==='btn_Reiniciar');
  console.log(s, hasRei ? 'OK' : 'FALTANDO btn_Reiniciar');
});
// Verificar bloco reiniciar
const rei = d.events.find(ev=>ev.sid===999000000001699);
console.log('Reiniciar block:', rei ? 'OK - ' + rei.actions[0].id : 'NÃO ENCONTRADO');
"
```

Esperado: todos os 6 blocos com `OK`, e `Reiniciar block: OK - restart-layout`.

- [ ] **Passo 4: Commit**

```bash
git add eventSheets/"Folha de eventos 1.json"
git commit -m "feat: botão Jogar Novamente nos finais + restart-layout"
```

---

## Task 10: Validação final

**Files:** Todos os arquivos modificados

- [ ] **Passo 1: Validar todos os JSONs do projeto**

```bash
node -e "
const fs = require('fs');
const files = [
  'project.c3proj',
  'layouts/Game.json',
  'eventSheets/Folha de eventos 1.json',
  'objectTypes/sf_Menu_Botao.json',
  'objectTypes/sf_Tutorial_Titulo.json',
  'objectTypes/sf_Tutorial_Corpo.json',
  'objectTypes/btn_Tutorial_Proximo.json',
  'objectTypes/btn_Tutorial_Anterior.json',
  'objectTypes/btn_Reiniciar.json'
];
let ok = true;
files.forEach(f => {
  try { JSON.parse(fs.readFileSync(f,'utf8')); console.log('OK:', f); }
  catch(e) { console.error('ERRO:', f, e.message); ok = false; }
});
process.exit(ok ? 0 : 1);
"
```

Esperado: `OK:` para todos os 9 arquivos.

- [ ] **Passo 2: Verificar UIDs únicos no layout**

```bash
node -e "
const d = JSON.parse(require('fs').readFileSync('layouts/Game.json','utf8'));
const uids = [];
d.layers.forEach(l => l.instances.forEach(i => uids.push(i.uid)));
const dupes = uids.filter((u,i) => uids.indexOf(u) !== i);
console.log('Total instâncias:', uids.length);
console.log('UIDs duplicados:', dupes.length === 0 ? 'nenhum' : dupes);
"
```

Esperado: `UIDs duplicados: nenhum`.

- [ ] **Passo 3: Verificar SIDs únicos no event sheet**

```bash
node -e "
const d = JSON.parse(require('fs').readFileSync('eventSheets/Folha de eventos 1.json','utf8'));
const sids = [];
function collect(evs) {
  for(const e of evs) {
    if(e.sid) sids.push(e.sid);
    if(e.conditions) e.conditions.forEach(c => { if(c.sid) sids.push(c.sid); });
    if(e.actions) e.actions.forEach(a => { if(a.sid) sids.push(a.sid); });
    if(e.children) collect(e.children);
  }
}
collect(d.events);
const dupes = sids.filter((s,i) => sids.indexOf(s) !== i);
console.log('Total SIDs:', sids.length);
console.log('SIDs duplicados:', dupes.length === 0 ? 'nenhum' : dupes);
"
```

Esperado: `SIDs duplicados: nenhum`.

- [ ] **Passo 4: Abrir projeto no Construct 3 e verificar**

Abrir o projeto no C3 e confirmar:
- [ ] Projeto carrega sem erros no console
- [ ] Layer Menu aparece sobre o jogo ao iniciar (Porris visíveis ao fundo)
- [ ] Botão JOGAR inicia o jogo e oculta o menu
- [ ] Botão TUTORIAL abre o tutorial com página 1
- [ ] PRÓXIMO vai para página 2; ANTERIOR volta para página 1
- [ ] VOLTAR retorna ao menu
- [ ] Botão SAIR funciona (pode não fechar em browser, comportamento esperado)
- [ ] Ao atingir um final, "JOGAR NOVAMENTE" aparece
- [ ] Clicar "JOGAR NOVAMENTE" reinicia o jogo e volta ao menu

- [ ] **Passo 5: Commit final**

```bash
git add .
git commit -m "feat: menu, tutorial e saída de finais completos"
```
