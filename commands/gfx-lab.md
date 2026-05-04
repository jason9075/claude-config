---
description: 在 NixOS 環境下初始化基於 Three.js 的靜態網頁開發專案，包含 KaTeX 整合、英文化工具介面、數學原理解說 modal、讀取 guidelines，以及完整的 flake.nix / Justfile 工作流。
argument-hint: [project-dir]
---

你是一位熟悉 NixOS 與 Three.js 圖形開發的資深工程師。請在指定目錄（預設為當前目錄）初始化一個 `gfx-lab` 開發專案。

## 強制規則

1. 這個功能一啟用，就必須先讀取目標專案資料夾中的 `./guidelines` 檔案：
   - 路徑為 `$ARGUMENTS/guidelines`。
   - 若 `$ARGUMENTS` 未指定，則為 `./guidelines`。
   - 若檔案存在，先讀取內容並在生成專案時遵守。
   - 若檔案不存在，可繼續生成，但仍要明確檢查這個路徑。
2. 網頁上的工具類選項、按鈕、控制項、說明標題與預設介面文字，一律使用英文。
3. 頁面上必須固定提供一個燈泡 emoji 按鈕 `💡`，點擊後開啟 modal，解釋該頁面的數學原理。
4. modal 右上角必須固定提供 `Eng/中` 語言切換控制。
5. 只有 modal 內容允許出現台灣繁體中文；modal 以外的頁面內容必須保持英文。

## 目標目錄

`$ARGUMENTS`（若未指定，預設為當前工作目錄 `.`）。

---

## 完整執行步驟

### Step 0：讀取 `guidelines`

在生成任何檔案前，先檢查並讀取：

```text
$ARGUMENTS/guidelines
```

若 `$ARGUMENTS` 未指定，則檢查目前工作目錄的：

```text
./guidelines
```

若檔案存在，將其中規範合併到專案生成結果；若不存在，繼續執行後續步驟，但不要略過這次檢查。

---

### Step 1：建立專案結構

產生以下檔案，若目錄不存在則先建立：

```
$ARGUMENTS/
├── flake.nix
├── .envrc
├── Justfile
├── index.html
├── src/
│   └── main.js
└── .gitignore
```

---

### Step 2：生成 `flake.nix`

使用 `nixpkgs` unstable channel，提供 `live-server` 與 `just` 作為開發工具：

```nix
{
  description = "gfx-lab — Three.js static dev environment";

  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixpkgs-unstable";
    flake-utils.url = "github:numtide/flake-utils";
  };

  outputs = { self, nixpkgs, flake-utils }:
    flake-utils.lib.eachDefaultSystem (system:
      let
        pkgs = import nixpkgs { inherit system; };
      in {
        devShells.default = pkgs.mkShell {
          packages = with pkgs; [
            live-server
            just
          ];
          shellHook = ''
            echo "gfx-lab dev shell ready. Run: just dev"
          '';
        };
      });
}
```

---

### Step 3：生成 `.envrc`

```bash
use flake
```

---

### Step 4：生成 `Justfile`

包含開發、refresh、版本檢查三個 recipe：

```just
set shell := ["sh", "-c"]

# 列出所有可用指令
default:
    @just --list

# 啟動開發 server（port 8080，預設不開瀏覽器）
dev:
    @echo "\033[36m[Nord] Running gfx-lab dev server...\033[0m"
    live-server --port 8080 .

# 觸發 live-server 重新載入（touch index.html）
refresh:
    @echo "\033[34m[Nord] Triggering workspace refresh...\033[0m"
    touch index.html

# 檢查工具版本
check:
    @live-server --version 2>&1 || true
    @just --version
```

---

### Step 5：生成 `index.html`

整合 Three.js (Import Maps)、KaTeX 自動渲染、Nord 配色 CSS 變數，並加入英文工具列、`💡` 數學說明按鈕與可切換 `Eng/中` 的 modal。除 modal 內容外，頁面文字一律使用英文：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>gfx-lab</title>

  <!-- KaTeX -->
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.16.9/dist/katex.min.css" />
  <script defer src="https://cdn.jsdelivr.net/npm/katex@0.16.9/dist/katex.min.js"></script>
  <script defer src="https://cdn.jsdelivr.net/npm/katex@0.16.9/dist/contrib/auto-render.min.js"
          onload="renderMathInElement(document.body, {
            delimiters: [
              {left: '$$', right: '$$', display: true},
              {left: '$', right: '$', display: false}
            ],
            macros: {
              '\\R': '\\mathbb{R}',
              '\\vec': '\\mathbf{#1}',
              '\\mat': '\\mathbf{#1}'
            }
          });"></script>

  <!-- Prism.js (Nord theme) — syntax highlighting for modal code blocks -->
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/prism-themes@1.9.0/themes/prism-nord.css" />
  <script src="https://cdn.jsdelivr.net/npm/prismjs@1.29.0/prism.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/prismjs@1.29.0/components/prism-javascript.min.js"></script>

  <!-- Import Maps for Three.js (ESM) -->
  <script type="importmap">
  {
    "imports": {
      "three": "https://cdn.jsdelivr.net/npm/three@0.163.0/build/three.module.js",
      "three/addons/": "https://cdn.jsdelivr.net/npm/three@0.163.0/examples/jsm/"
    }
  }
  </script>

  <style>
    /* Nord colour palette */
    :root {
      --nord0:  #2E3440;
      --nord1:  #3B4252;
      --nord2:  #434C5E;
      --nord3:  #4C566A;
      --nord4:  #D8DEE9;
      --nord5:  #E5E9F0;
      --nord6:  #ECEFF4;
      --nord7:  #8FBCBB;
      --nord8:  #88C0D0;
      --nord9:  #81A1C1;
      --nord10: #5E81AC;
      --nord11: #BF616A;
      --nord12: #D08770;
      --nord13: #EBCB8B;
      --nord14: #A3BE8C;
      --nord15: #B48EAD;
    }

    * { margin: 0; padding: 0; box-sizing: border-box; }

    body {
      background: var(--nord0);
      color: var(--nord4);
      font-family: 'JetBrains Mono', monospace, sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      min-height: 100vh;
    }

    canvas { display: block; }

    #info {
      position: absolute;
      top: 1rem;
      left: 1rem;
      color: var(--nord8);
      font-size: 0.85rem;
    }

    #toolbar {
      position: fixed;
      top: 1rem;
      left: 50%;
      transform: translateX(-50%);
      display: flex;
      gap: 0.75rem;
      align-items: center;
      padding: 0.75rem 1rem;
      border: 1px solid var(--nord3);
      border-radius: 999px;
      background: rgba(59, 66, 82, 0.88);
      backdrop-filter: blur(10px);
      z-index: 20;
    }

    #toolbar label,
    #toolbar select,
    #toolbar button {
      font: inherit;
    }

    #toolbar select,
    #toolbar button,
    .modal-toggle {
      border: 1px solid var(--nord3);
      border-radius: 999px;
      background: var(--nord1);
      color: var(--nord6);
      padding: 0.45rem 0.8rem;
    }

    .icon-button {
      font-size: 1.1rem;
      line-height: 1;
      cursor: pointer;
    }

    #math-modal[hidden] {
      display: none;
    }

    #math-modal {
      position: fixed;
      inset: 0;
      display: grid;
      place-items: center;
      background: rgba(46, 52, 64, 0.78);
      z-index: 50;
    }

    .modal-panel {
      width: min(720px, calc(100vw - 2rem));
      max-height: calc(100vh - 2rem);
      overflow: auto;
      background: var(--nord1);
      border: 1px solid var(--nord3);
      border-radius: 20px;
      box-shadow: 0 20px 60px rgba(0, 0, 0, 0.35);
      padding: 1.25rem;
    }

    .modal-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      gap: 1rem;
      margin-bottom: 1rem;
    }

    .modal-header h2 {
      font-size: 1rem;
      color: var(--nord8);
    }

    .modal-actions {
      display: flex;
      gap: 0.5rem;
      align-items: center;
    }

    .modal-body {
      display: grid;
      gap: 0.85rem;
      line-height: 1.7;
      color: var(--nord5);
    }

    .modal-body pre[class*="language-"] {
      border-radius: 8px;
      overflow-x: auto;
      font-size: 0.82rem;
      margin: 0;
    }

  </style>
</head>
<body>

  <div id="info">gfx-lab | Three.js + KaTeX + Nord</div>
  <div id="toolbar" aria-label="Tool controls">
    <label for="render-mode">Render Mode</label>
    <select id="render-mode">
      <option value="wireframe">Wireframe</option>
      <option value="solid">Solid</option>
    </select>
    <button id="pause-button" type="button">Pause Rotation</button>
    <button id="open-math" class="icon-button" type="button" aria-haspopup="dialog" aria-controls="math-modal" aria-label="Explain the math">💡</button>
  </div>
  <canvas id="canvas"></canvas>

  <div id="math-modal" role="dialog" aria-modal="true" aria-labelledby="math-title" hidden>
    <div class="modal-panel">
      <div class="modal-header">
        <h2 id="math-title">Math Behind the Scene</h2>
        <div class="modal-actions">
          <button id="language-toggle" class="modal-toggle" type="button">Eng/中</button>
          <button id="close-math" class="modal-toggle" type="button" aria-label="Close">Close</button>
        </div>
      </div>
      <div id="math-content" class="modal-body"></div>
    </div>
  </div>

  <script type="module" src="./src/main.js"></script>
</body>
</html>
```

---

### Step 6：生成 `src/main.js`

提供最小可運行的 Three.js 場景骨架（含 resize handler、animation loop、英文工具控制，以及 `💡` modal 的 `Eng/中` 切換；中文內容僅存在於 modal）：

```js
import * as THREE from 'three';

const canvas = document.getElementById('canvas');
const renderMode = document.getElementById('render-mode');
const pauseButton = document.getElementById('pause-button');
const openMathButton = document.getElementById('open-math');
const closeMathButton = document.getElementById('close-math');
const languageToggle = document.getElementById('language-toggle');
const mathModal = document.getElementById('math-modal');
const mathContent = document.getElementById('math-content');

const renderer = new THREE.WebGLRenderer({ canvas, antialias: true });
renderer.setPixelRatio(window.devicePixelRatio);

const scene = new THREE.Scene();
scene.background = new THREE.Color(0x2E3440); // Nord0

const camera = new THREE.PerspectiveCamera(60, 1, 0.1, 100);
camera.position.set(0, 0, 3);

// Sample geometry
const geometry = new THREE.IcosahedronGeometry(1, 1);
const material = new THREE.MeshStandardMaterial({
  color: 0x88C0D0, // Nord8
  wireframe: true,
});
const mesh = new THREE.Mesh(geometry, material);
scene.add(mesh);

let isAnimating = true;
let modalLanguage = 'en';

const modalCopy = {
  en: `
    <p>This scene rotates a mesh in 3D space using two angular velocities, one for the x-axis and one for the y-axis.</p>
    <p>The camera uses perspective projection, which maps a 3D point $(x, y, z)$ into screen space by dividing by depth.</p>
    <p>Rotation is a matrix transform. For example, rotation around the y-axis is expressed as:</p>
    <p>$$
      R_y(\\theta) =
      \\begin{bmatrix}
      \\cos\\theta & 0 & \\sin\\theta \\\\
      0 & 1 & 0 \\\\
      -\\sin\\theta & 0 & \\cos\\theta
      \\end{bmatrix}
    $$</p>
    <p>Animating over time means the angle becomes a function $\\theta(t)$, so the object moves smoothly frame by frame. The implementation applies this each tick:</p>
    <pre><code class="language-js">function animate(t = 0) {
  requestAnimationFrame(animate);
  mesh.rotation.x = t * 0.0003; // ωₓ
  mesh.rotation.y = t * 0.0005; // ω_y
  renderer.render(scene, camera);
}
animate();</code></pre>
  `,
  zhTW: `
    <p>這個場景讓一個 3D 網格以兩個角速度持續旋轉，分別對應 x 軸與 y 軸。</p>
    <p>相機使用透視投影，會把三維點 $(x, y, z)$ 依照深度做縮放後映射到螢幕平面。</p>
    <p>旋轉本質上是矩陣變換。以 y 軸旋轉為例，可寫成：</p>
    <p>$$
      R_y(\\theta) =
      \\begin{bmatrix}
      \\cos\\theta & 0 & \\sin\\theta \\\\
      0 & 1 & 0 \\\\
      -\\sin\\theta & 0 & \\cos\\theta
      \\end{bmatrix}
    $$</p>
    <p>當角度變成時間函數 $\\theta(t)$，物體就會隨著每一幀平滑地運動。實際程式碼如下：</p>
    <pre><code class="language-js">function animate(t = 0) {
  requestAnimationFrame(animate);
  mesh.rotation.x = t * 0.0003; // ωₓ
  mesh.rotation.y = t * 0.0005; // ω_y
  renderer.render(scene, camera);
}
animate();</code></pre>
  `,
};

function renderModalContent() {
  mathContent.innerHTML = modalCopy[modalLanguage];
  if (window.renderMathInElement) {
    window.renderMathInElement(mathContent, {
      delimiters: [
        { left: '$$', right: '$$', display: true },
        { left: '$', right: '$', display: false },
      ],
    });
  }
  if (window.Prism) {
    window.Prism.highlightAllUnder(mathContent);
  }
}

// Lighting
const light = new THREE.DirectionalLight(0xECEFF4, 1.5);
light.position.set(2, 3, 4);
scene.add(light);
scene.add(new THREE.AmbientLight(0x4C566A, 0.8));

/** Resize handler */
function resize() {
  const w = window.innerWidth;
  const h = window.innerHeight;
  renderer.setSize(w, h);
  camera.aspect = w / h;
  camera.updateProjectionMatrix();
}
window.addEventListener('resize', resize);
resize();

renderMode.addEventListener('change', (event) => {
  material.wireframe = event.target.value === 'wireframe';
});

pauseButton.addEventListener('click', () => {
  isAnimating = !isAnimating;
  pauseButton.textContent = isAnimating ? 'Pause Rotation' : 'Resume Rotation';
});

openMathButton.addEventListener('click', () => {
  renderModalContent();
  mathModal.hidden = false;
});

closeMathButton.addEventListener('click', () => {
  mathModal.hidden = true;
});

languageToggle.addEventListener('click', () => {
  modalLanguage = modalLanguage === 'en' ? 'zhTW' : 'en';
  renderModalContent();
});

/** Animation loop */
function animate(t = 0) {
  requestAnimationFrame(animate);
  if (isAnimating) {
    mesh.rotation.x = t * 0.0003;
    mesh.rotation.y = t * 0.0005;
  }
  renderer.render(scene, camera);
}
animate();
```

---

### Step 7：生成 `.gitignore`

```gitignore
# Nix
result
result-*
.direnv/

# Neovim
*.swp
*.swo
.netrwhist

# OS
.DS_Store
Thumbs.db
```

---

### Step 8：輸出完成提示

所有檔案生成後，輸出以下摘要（繁體中文）：

1. 列出已建立的所有檔案路徑
2. 說明啟動方式：
   - `direnv allow`（啟用自動載入 Nix Shell）
   - 或手動 `nix develop`
   - 執行 `just dev` 啟動 `live-server`，瀏覽器開啟 `http://localhost:8080`
3. 提醒：修改 `src/main.js` 後瀏覽器會自動重新載入（no-cache 模式確保不讀舊的 bytecode 快取）

---

## 開始執行

請立即依照上述步驟，在 `$ARGUMENTS`（預設 `.`）目錄內生成全部檔案，並在完成後輸出摘要。
