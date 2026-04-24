# Am-outfit
Outfit generator
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>VESTIS — AI Outfit Curator</title>
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,500;1,300;1,400&family=DM+Mono:wght@300;400&display=swap" rel="stylesheet">
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --cream: #f5f0e8;
    --ink: #1a1612;
    --gold: #c9a84c;
    --gold-light: #e8d5a3;
    --muted: #8a7f74;
    --warm-white: #faf7f2;
    --border: #ddd5c8;
    --rose: #c97b6a;
  }

  body {
    font-family: 'DM Mono', monospace;
    background: var(--cream);
    color: var(--ink);
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* GRAIN OVERLAY */
  body::before {
    content: '';
    position: fixed; inset: 0;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)' opacity='0.04'/%3E%3C/svg%3E");
    pointer-events: none; z-index: 9999; opacity: 0.6;
  }

  /* HEADER */
  header {
    border-bottom: 1px solid var(--border);
    padding: 28px 48px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    background: var(--warm-white);
    position: sticky; top: 0; z-index: 100;
  }

  .logo {
    font-family: 'Cormorant Garamond', serif;
    font-size: 2rem;
    font-weight: 300;
    letter-spacing: 0.35em;
    color: var(--ink);
  }

  .logo span {
    color: var(--gold);
  }

  .tagline {
    font-size: 0.65rem;
    letter-spacing: 0.2em;
    color: var(--muted);
    text-transform: uppercase;
  }

  /* LAYOUT */
  .app {
    display: grid;
    grid-template-columns: 380px 1fr;
    min-height: calc(100vh - 85px);
  }

  /* LEFT PANEL */
  .panel-left {
    border-right: 1px solid var(--border);
    background: var(--warm-white);
    padding: 40px 32px;
    display: flex;
    flex-direction: column;
    gap: 32px;
  }

  .section-label {
    font-size: 0.6rem;
    letter-spacing: 0.25em;
    text-transform: uppercase;
    color: var(--muted);
    margin-bottom: 12px;
  }

  /* UPLOAD ZONE */
  .upload-zone {
    border: 1.5px dashed var(--border);
    border-radius: 2px;
    padding: 32px 20px;
    text-align: center;
    cursor: pointer;
    transition: all 0.3s ease;
    position: relative;
    background: var(--cream);
  }

  .upload-zone:hover, .upload-zone.dragover {
    border-color: var(--gold);
    background: rgba(201, 168, 76, 0.04);
  }

  .upload-zone input[type=file] {
    position: absolute; inset: 0; opacity: 0; cursor: pointer; width: 100%; height: 100%;
  }

  .upload-icon {
    font-size: 2rem;
    margin-bottom: 10px;
    opacity: 0.5;
  }

  .upload-text {
    font-family: 'Cormorant Garamond', serif;
    font-size: 1.1rem;
    font-weight: 300;
    color: var(--ink);
    margin-bottom: 4px;
  }

  .upload-sub {
    font-size: 0.65rem;
    color: var(--muted);
    letter-spacing: 0.1em;
  }

  /* WARDROBE GRID */
  .wardrobe-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 8px;
    max-height: 340px;
    overflow-y: auto;
    padding-right: 4px;
  }

  .wardrobe-grid::-webkit-scrollbar { width: 3px; }
  .wardrobe-grid::-webkit-scrollbar-track { background: transparent; }
  .wardrobe-grid::-webkit-scrollbar-thumb { background: var(--border); }

  .cloth-item {
    aspect-ratio: 1;
    border-radius: 2px;
    overflow: hidden;
    position: relative;
    cursor: pointer;
    border: 1.5px solid transparent;
    transition: all 0.2s ease;
    background: var(--cream);
  }

  .cloth-item:hover { border-color: var(--gold); }

  .cloth-item.selected {
    border-color: var(--gold);
    box-shadow: 0 0 0 2px rgba(201, 168, 76, 0.3);
  }

  .cloth-item img {
    width: 100%; height: 100%; object-fit: cover;
  }

  .cloth-item .remove-btn {
    position: absolute; top: 4px; right: 4px;
    background: rgba(26,22,18,0.7);
    color: white; border: none;
    width: 18px; height: 18px; border-radius: 50%;
    font-size: 10px; cursor: pointer;
    display: none; align-items: center; justify-content: center;
    line-height: 1;
  }

  .cloth-item:hover .remove-btn { display: flex; }

  .cloth-item .category-badge {
    position: absolute; bottom: 0; left: 0; right: 0;
    background: rgba(26,22,18,0.65);
    color: var(--gold-light);
    font-size: 0.5rem;
    letter-spacing: 0.1em;
    padding: 3px 5px;
    text-transform: uppercase;
    text-align: center;
  }

  /* OCCASION SELECTOR */
  .occasion-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 6px;
  }

  .occasion-btn {
    padding: 10px 8px;
    border: 1px solid var(--border);
    background: transparent;
    font-family: 'DM Mono', monospace;
    font-size: 0.6rem;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    cursor: pointer;
    transition: all 0.2s;
    color: var(--muted);
    border-radius: 2px;
  }

  .occasion-btn:hover, .occasion-btn.active {
    background: var(--ink);
    color: var(--gold-light);
    border-color: var(--ink);
  }

  /* WEATHER / MOOD */
  .mood-row {
    display: flex;
    gap: 6px;
    flex-wrap: wrap;
  }

  .mood-chip {
    padding: 6px 12px;
    border: 1px solid var(--border);
    border-radius: 20px;
    font-size: 0.58rem;
    letter-spacing: 0.1em;
    cursor: pointer;
    transition: all 0.2s;
    background: transparent;
    color: var(--muted);
    font-family: 'DM Mono', monospace;
  }

  .mood-chip.active {
    background: var(--gold);
    border-color: var(--gold);
    color: var(--ink);
  }

  /* GENERATE BTN */
  .generate-btn {
    width: 100%;
    padding: 18px;
    background: var(--ink);
    color: var(--gold-light);
    border: none;
    font-family: 'Cormorant Garamond', serif;
    font-size: 1.1rem;
    font-weight: 300;
    letter-spacing: 0.3em;
    text-transform: uppercase;
    cursor: pointer;
    transition: all 0.3s;
    position: relative;
    overflow: hidden;
    border-radius: 2px;
  }

  .generate-btn::before {
    content: '';
    position: absolute; top: 0; left: -100%; width: 100%; height: 100%;
    background: linear-gradient(90deg, transparent, rgba(201,168,76,0.15), transparent);
    transition: left 0.5s;
  }

  .generate-btn:hover::before { left: 100%; }
  .generate-btn:hover { background: #2a231c; }
  .generate-btn:disabled { opacity: 0.5; cursor: not-allowed; }

  /* RIGHT PANEL */
  .panel-right {
    padding: 48px;
    display: flex;
    flex-direction: column;
    gap: 40px;
  }

  /* EMPTY STATE */
  .empty-state {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    text-align: center;
    gap: 16px;
    padding: 60px;
  }

  .empty-icon {
    font-size: 4rem;
    opacity: 0.2;
  }

  .empty-title {
    font-family: 'Cormorant Garamond', serif;
    font-size: 1.8rem;
    font-weight: 300;
    color: var(--muted);
    letter-spacing: 0.05em;
  }

  .empty-sub {
    font-size: 0.65rem;
    color: var(--border);
    letter-spacing: 0.15em;
    text-transform: uppercase;
  }

  /* OUTFIT RESULT */
  .outfit-result {
    animation: fadeSlideIn 0.6s ease forwards;
  }

  @keyframes fadeSlideIn {
    from { opacity: 0; transform: translateY(20px); }
    to { opacity: 1; transform: translateY(0); }
  }

  .result-header {
    display: flex;
    align-items: flex-end;
    justify-content: space-between;
    margin-bottom: 32px;
    padding-bottom: 20px;
    border-bottom: 1px solid var(--border);
  }

  .result-title {
    font-family: 'Cormorant Garamond', serif;
    font-size: 2.2rem;
    font-weight: 300;
    font-style: italic;
    line-height: 1.1;
  }

  .result-occasion-badge {
    font-size: 0.58rem;
    letter-spacing: 0.2em;
    text-transform: uppercase;
    color: var(--gold);
    border: 1px solid var(--gold);
    padding: 4px 12px;
  }

  .outfit-layout {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 24px;
    margin-bottom: 32px;
  }

  .outfit-piece {
    border: 1px solid var(--border);
    border-radius: 2px;
    overflow: hidden;
    background: var(--warm-white);
    position: relative;
    animation: fadeSlideIn 0.5s ease forwards;
  }

  .outfit-piece:nth-child(2) { animation-delay: 0.1s; }
  .outfit-piece:nth-child(3) { animation-delay: 0.2s; }
  .outfit-piece:nth-child(4) { animation-delay: 0.3s; }

  .outfit-piece img {
    width: 100%;
    aspect-ratio: 3/4;
    object-fit: cover;
    display: block;
  }

  .piece-info {
    padding: 12px 14px;
    border-top: 1px solid var(--border);
  }

  .piece-type {
    font-size: 0.55rem;
    letter-spacing: 0.2em;
    color: var(--gold);
    text-transform: uppercase;
    margin-bottom: 2px;
  }

  .piece-name {
    font-family: 'Cormorant Garamond', serif;
    font-size: 1rem;
    font-weight: 400;
    color: var(--ink);
  }

  /* AI STYLING NOTES */
  .styling-notes {
    background: var(--warm-white);
    border: 1px solid var(--border);
    border-left: 3px solid var(--gold);
    padding: 24px 28px;
    border-radius: 0 2px 2px 0;
    margin-bottom: 24px;
  }

  .notes-label {
    font-size: 0.58rem;
    letter-spacing: 0.2em;
    text-transform: uppercase;
    color: var(--gold);
    margin-bottom: 12px;
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .notes-label::after {
    content: '';
    flex: 1;
    height: 1px;
    background: var(--border);
  }

  .notes-text {
    font-family: 'Cormorant Garamond', serif;
    font-size: 1.05rem;
    font-weight: 300;
    line-height: 1.75;
    color: var(--ink);
  }

  /* COLOR PALETTE */
  .palette-row {
    display: flex;
    align-items: center;
    gap: 12px;
    margin-bottom: 24px;
  }

  .palette-dot {
    width: 28px; height: 28px;
    border-radius: 50%;
    border: 2px solid white;
    box-shadow: 0 0 0 1px var(--border);
    transition: transform 0.2s;
  }

  .palette-dot:hover { transform: scale(1.2); }

  /* REGENERATE / SAVE */
  .action-row {
    display: flex;
    gap: 12px;
  }

  .action-btn {
    flex: 1;
    padding: 14px;
    border: 1px solid var(--border);
    background: transparent;
    font-family: 'DM Mono', monospace;
    font-size: 0.62rem;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    cursor: pointer;
    transition: all 0.2s;
    color: var(--muted);
    border-radius: 2px;
  }

  .action-btn:hover {
    background: var(--ink);
    color: var(--gold-light);
    border-color: var(--ink);
  }

  .action-btn.primary {
    background: var(--gold);
    border-color: var(--gold);
    color: var(--ink);
    font-weight: 500;
  }

  .action-btn.primary:hover {
    background: #b8923d;
  }

  /* LOADING STATE */
  .loading-state {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 20px;
    text-align: center;
  }

  .loading-spinner {
    width: 48px; height: 48px;
    border: 1.5px solid var(--border);
    border-top-color: var(--gold);
    border-radius: 50%;
    animation: spin 1s linear infinite;
  }

  @keyframes spin { to { transform: rotate(360deg); } }

  .loading-text {
    font-family: 'Cormorant Garamond', serif;
    font-size: 1.3rem;
    font-weight: 300;
    font-style: italic;
    color: var(--muted);
  }

  .loading-steps {
    font-size: 0.6rem;
    letter-spacing: 0.15em;
    color: var(--border);
    text-transform: uppercase;
    animation: pulse 2s ease-in-out infinite;
  }

  @keyframes pulse {
    0%, 100% { opacity: 0.5; }
    50% { opacity: 1; }
  }

  /* SAVED OUTFITS */
  .saved-section { margin-top: 20px; }

  .saved-grid {
    display: flex;
    gap: 10px;
    flex-wrap: wrap;
    margin-top: 12px;
  }

  .saved-thumb {
    width: 70px; height: 70px;
    border: 1px solid var(--border);
    border-radius: 2px;
    overflow: hidden;
    cursor: pointer;
    position: relative;
    transition: all 0.2s;
  }

  .saved-thumb:hover { border-color: var(--gold); }

  .saved-thumb img {
    width: 100%; height: 100%; object-fit: cover;
  }

  /* STATS BAR */
  .stats-bar {
    display: flex;
    gap: 0;
    border: 1px solid var(--border);
    border-radius: 2px;
    overflow: hidden;
    margin-bottom: 4px;
  }

  .stat-item {
    flex: 1;
    padding: 10px 14px;
    border-right: 1px solid var(--border);
    text-align: center;
  }

  .stat-item:last-child { border-right: none; }

  .stat-num {
    font-family: 'Cormorant Garamond', serif;
    font-size: 1.4rem;
    font-weight: 300;
    color: var(--gold);
  }

  .stat-label {
    font-size: 0.55rem;
    letter-spacing: 0.12em;
    color: var(--muted);
    text-transform: uppercase;
  }

  /* SCROLLBAR */
  ::-webkit-scrollbar { width: 4px; }
  ::-webkit-scrollbar-track { background: transparent; }
  ::-webkit-scrollbar-thumb { background: var(--border); }

  /* RESPONSIVE */
  @media (max-width: 900px) {
    .app { grid-template-columns: 1fr; }
    .panel-left { border-right: none; border-bottom: 1px solid var(--border); }
    header { padding: 20px 24px; }
    .panel-right { padding: 24px; }
  }

  /* KEY FEATURE TAG */
  .feature-tag {
    display: inline-flex;
    align-items: center;
    gap: 4px;
    font-size: 0.55rem;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    color: var(--muted);
    border: 1px solid var(--border);
    padding: 3px 8px;
    border-radius: 20px;
  }

  .feature-tag .dot {
    width: 5px; height: 5px;
    background: var(--gold);
    border-radius: 50%;
  }
</style>
</head>
<body>

<header>
  <div>
    <div class="logo">VEST<span>IS</span></div>
  </div>
  <div class="tagline">AI-Powered Personal Stylist</div>
  <div class="feature-tag"><span class="dot"></span> Powered by Claude AI</div>
</header>

<div class="app">

  <!-- LEFT PANEL -->
  <div class="panel-left">

    <!-- Stats -->
    <div>
      <div class="section-label">Your Wardrobe</div>
      <div class="stats-bar">
        <div class="stat-item">
          <div class="stat-num" id="clothCount">0</div>
          <div class="stat-label">Pieces</div>
        </div>
        <div class="stat-item">
          <div class="stat-num" id="outfitCount">0</div>
          <div class="stat-label">Outfits</div>
        </div>
        <div class="stat-item">
          <div class="stat-num" id="categoryCount">0</div>
          <div class="stat-label">Categories</div>
        </div>
      </div>
    </div>

    <!-- Upload -->
    <div>
      <div class="section-label">Add Clothing</div>
      <div class="upload-zone" id="uploadZone">
        <input type="file" id="fileInput" accept="image/*" multiple>
        <div class="upload-icon">🧥</div>
        <div class="upload-text">Drop clothes here</div>
        <div class="upload-sub">or click to browse — JPG, PNG, WEBP</div>
      </div>
    </div>

    <!-- Wardrobe -->
    <div id="wardrobeSection" style="display:none">
      <div class="section-label">My Wardrobe — <span id="selectedInfo">tap to select pieces</span></div>
      <div class="wardrobe-grid" id="wardrobeGrid"></div>
    </div>

    <!-- Occasion -->
    <div>
      <div class="section-label">Occasion</div>
      <div class="occasion-grid">
        <button class="occasion-btn active" data-val="casual">☕ Casual</button>
        <button class="occasion-btn" data-val="work">💼 Work</button>
        <button class="occasion-btn" data-val="dinner">🍷 Dinner</button>
        <button class="occasion-btn" data-val="formal">✨ Formal</button>
        <button class="occasion-btn" data-val="date">🌹 Date Night</button>
        <button class="occasion-btn" data-val="gym">🏋️ Gym</button>
      </div>
    </div>

    <!-- Mood -->
    <div>
      <div class="section-label">Mood & Weather</div>
      <div class="mood-row">
        <button class="mood-chip active" data-val="comfortable">Comfortable</button>
        <button class="mood-chip" data-val="bold">Bold</button>
        <button class="mood-chip" data-val="minimal">Minimal</button>
        <button class="mood-chip" data-val="warm">Warm day</button>
        <button class="mood-chip" data-val="cold">Cold</button>
        <button class="mood-chip" data-val="rainy">Rainy</button>
      </div>
    </div>

    <!-- Generate -->
    <button class="generate-btn" id="generateBtn" onclick="generateOutfit()">
      ✦ &nbsp; Curate My Outfit
    </button>

  </div>

  <!-- RIGHT PANEL -->
  <div class="panel-right" id="rightPanel">
    <div class="empty-state" id="emptyState">
      <div class="empty-icon">👗</div>
      <div class="empty-title">Your outfit awaits</div>
      <div class="empty-sub">Upload clothes & let AI style you</div>
    </div>
  </div>

</div>

<script>
  // ── STATE ──────────────────────────────────────────────────────────────
  let wardrobe = []; // { id, src, base64, filename, category }
  let selectedPieces = [];
  let occasion = 'casual';
  let mood = 'comfortable';
  let savedOutfits = [];
  let outfitCount = 0;

  // ── UPLOAD ─────────────────────────────────────────────────────────────
  const fileInput = document.getElementById('fileInput');
  const uploadZone = document.getElementById('uploadZone');

  uploadZone.addEventListener('dragover', e => { e.preventDefault(); uploadZone.classList.add('dragover'); });
  uploadZone.addEventListener('dragleave', () => uploadZone.classList.remove('dragover'));
  uploadZone.addEventListener('drop', e => {
    e.preventDefault();
    uploadZone.classList.remove('dragover');
    handleFiles(Array.from(e.dataTransfer.files));
  });

  fileInput.addEventListener('change', () => handleFiles(Array.from(fileInput.files)));

  async function handleFiles(files) {
    const imageFiles = files.filter(f => f.type.startsWith('image/'));
    if (!imageFiles.length) return;

    for (const file of imageFiles) {
      const src = await readFileAsDataURL(file);
      const base64 = src.split(',')[1];
      const id = Date.now() + Math.random();
      const category = guessCategory(file.name);
      wardrobe.push({ id, src, base64, filename: file.name, category });
    }

    renderWardrobe();
    updateStats();
    fileInput.value = '';
  }

  function readFileAsDataURL(file) {
    return new Promise(res => {
      const r = new FileReader();
      r.onload = e => res(e.target.result);
      r.readAsDataURL(file);
    });
  }

  function guessCategory(name) {
    name = name.toLowerCase();
    if (/shirt|tee|top|blouse|polo|tank/.test(name)) return 'Top';
    if (/pant|jean|trouser|chino|legging/.test(name)) return 'Bottom';
    if (/dress|skirt/.test(name)) return 'Dress';
    if (/shoe|sneaker|boot|heel|sandal/.test(name)) return 'Shoes';
    if (/jacket|coat|blazer|hoodie|sweater|sweat/.test(name)) return 'Outer';
    if (/bag|purse|clutch|backpack/.test(name)) return 'Bag';
    if (/hat|cap|scarf|belt|watch|accessory/.test(name)) return 'Accessory';
    return 'Clothing';
  }

  // ── WARDROBE RENDER ────────────────────────────────────────────────────
  function renderWardrobe() {
    const section = document.getElementById('wardrobeSection');
    const grid = document.getElementById('wardrobeGrid');
    section.style.display = wardrobe.length ? 'block' : 'none';
    grid.innerHTML = '';

    wardrobe.forEach(item => {
      const div = document.createElement('div');
      div.className = 'cloth-item' + (selectedPieces.includes(item.id) ? ' selected' : '');
      div.innerHTML = `
        <img src="${item.src}" alt="${item.filename}" loading="lazy">
        <div class="category-badge">${item.category}</div>
        <button class="remove-btn" onclick="removeItem('${item.id}', event)">✕</button>
      `;
      div.addEventListener('click', () => toggleSelect(item.id));
      grid.appendChild(div);
    });

    updateSelectedInfo();
  }

  function toggleSelect(id) {
    if (selectedPieces.includes(id)) {
      selectedPieces = selectedPieces.filter(x => x !== id);
    } else {
      selectedPieces.push(id);
    }
    renderWardrobe();
  }

  function removeItem(id, e) {
    e.stopPropagation();
    wardrobe = wardrobe.filter(x => x.id != id);
    selectedPieces = selectedPieces.filter(x => x != id);
    renderWardrobe();
    updateStats();
  }

  function updateSelectedInfo() {
    const el = document.getElementById('selectedInfo');
    if (selectedPieces.length === 0) el.textContent = 'tap to select pieces';
    else el.textContent = `${selectedPieces.length} selected`;
  }

  function updateStats() {
    document.getElementById('clothCount').textContent = wardrobe.length;
    document.getElementById('outfitCount').textContent = outfitCount;
    const cats = new Set(wardrobe.map(w => w.category));
    document.getElementById('categoryCount').textContent = cats.size;
  }

  // ── OCCASION / MOOD ────────────────────────────────────────────────────
  document.querySelectorAll('.occasion-btn').forEach(btn => {
    btn.addEventListener('click', () => {
      document.querySelectorAll('.occasion-btn').forEach(b => b.classList.remove('active'));
      btn.classList.add('active');
      occasion = btn.dataset.val;
    });
  });

  document.querySelectorAll('.mood-chip').forEach(chip => {
    chip.addEventListener('click', () => {
      document.querySelectorAll('.mood-chip').forEach(c => c.classList.remove('active'));
      chip.classList.add('active');
      mood = chip.dataset.val;
    });
  });

  // ── GENERATE OUTFIT ────────────────────────────────────────────────────
  async function generateOutfit() {
    if (wardrobe.length < 2) {
      showToast('Upload at least 2 clothing items first');
      return;
    }

    const btn = document.getElementById('generateBtn');
    btn.disabled = true;
    btn.textContent = '✦   Curating...';

    showLoading();

    try {
      const piecesToAnalyze = selectedPieces.length > 0
        ? wardrobe.filter(w => selectedPieces.includes(w.id))
        : wardrobe;

      // Build content with images
      const imageContent = piecesToAnalyze.slice(0, 8).map((item, i) => ([
        {
          type: 'image',
          source: { type: 'base64', media_type: 'image/jpeg', data: item.base64 }
        },
        {
          type: 'text',
          text: `[Item ${i + 1}: ${item.category} — ${item.filename}]`
        }
      ])).flat();

      imageContent.push({
        type: 'text',
        text: `You are VESTIS, a luxury personal stylist AI. The user has uploaded ${piecesToAnalyze.length} clothing items above.

Occasion: ${occasion}
Mood/Weather: ${mood}

Your job:
1. Analyze each clothing item (color, style, formality, season-appropriateness)
2. Select the BEST 2–4 items that work together as a complete outfit
3. Return a JSON object (ONLY JSON, no markdown, no explanation outside JSON)

JSON format:
{
  "outfitName": "A poetic 3-word outfit name",
  "selectedItems": [0, 2, 3],
  "categories": ["Top", "Bottom", "Shoes"],
  "colorPalette": ["#hexcolor1", "#hexcolor2", "#hexcolor3"],
  "stylingNotes": "2-3 sentence editorial styling advice in a refined, confident tone. Mention specific items, colors, textures. Explain why this combination works for the occasion.",
  "confidence": 92
}`
      });

      const response = await fetch('https://api.anthropic.com/v1/messages', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          model: 'claude-sonnet-4-20250514',
          max_tokens: 1000,
          messages: [{ role: 'user', content: imageContent }]
        })
      });

      const data = await response.json();
      const text = data.content?.map(c => c.text || '').join('');

      let result;
      try {
        const clean = text.replace(/```json|```/g, '').trim();
        result = JSON.parse(clean);
      } catch {
        // Try to extract JSON from response
        const match = text.match(/\{[\s\S]*\}/);
        if (match) result = JSON.parse(match[0]);
        else throw new Error('Could not parse AI response');
      }

      outfitCount++;
      updateStats();
      showOutfitResult(result, piecesToAnalyze);

    } catch (err) {
      console.error(err);
      showError('Could not generate outfit. Please try again.');
    } finally {
      btn.disabled = false;
      btn.textContent = '✦   Curate My Outfit';
    }
  }

  // ── SHOW STATES ────────────────────────────────────────────────────────
  function showLoading() {
    const loadingSteps = [
      'Analysing your wardrobe...',
      'Matching colours & textures...',
      'Considering the occasion...',
      'Curating your perfect look...'
    ];
    let step = 0;
    const panel = document.getElementById('rightPanel');
    panel.innerHTML = `
      <div class="loading-state" id="loadingState">
        <div class="loading-spinner"></div>
        <div class="loading-text">Your stylist is at work</div>
        <div class="loading-steps" id="loadingStep">${loadingSteps[0]}</div>
      </div>
    `;
    const interval = setInterval(() => {
      step = (step + 1) % loadingSteps.length;
      const el = document.getElementById('loadingStep');
      if (el) el.textContent = loadingSteps[step];
      else clearInterval(interval);
    }, 1500);
  }

  function showOutfitResult(result, pieces) {
    const selected = (result.selectedItems || [0, 1]).map(i => pieces[i]).filter(Boolean);
    if (selected.length === 0) selected.push(...pieces.slice(0, 2));

    const colors = (result.colorPalette || ['#c9a84c', '#1a1612', '#8a7f74']);

    const piecesHTML = selected.map((item, i) => `
      <div class="outfit-piece">
        <img src="${item.src}" alt="${item.category}">
        <div class="piece-info">
          <div class="piece-type">${result.categories?.[i] || item.category}</div>
          <div class="piece-name">${item.filename.replace(/\.[^/.]+$/, '').replace(/[-_]/g, ' ')}</div>
        </div>
      </div>
    `).join('');

    const paletteHTML = colors.map(c => `<div class="palette-dot" style="background:${c}" title="${c}"></div>`).join('');

    const panel = document.getElementById('rightPanel');
    panel.innerHTML = `
      <div class="outfit-result">
        <div class="result-header">
          <div>
            <div class="section-label">Today's Curated Look</div>
            <div class="result-title">${result.outfitName || 'Your Perfect Outfit'}</div>
          </div>
          <div class="result-occasion-badge">${occasion}</div>
        </div>

        <div class="outfit-layout">${piecesHTML}</div>

        <div class="styling-notes">
          <div class="notes-label">Stylist Notes</div>
          <div class="notes-text">${result.stylingNotes || 'A beautiful combination of pieces that work harmoniously for this occasion.'}</div>
        </div>

        <div>
          <div class="section-label" style="margin-bottom:10px">Colour Palette</div>
          <div class="palette-row">${paletteHTML}</div>
        </div>

        <div class="action-row">
          <button class="action-btn" onclick="generateOutfit()">↻ Regenerate</button>
          <button class="action-btn" onclick="shuffleOutfit()">⇄ Shuffle Pieces</button>
          <button class="action-btn primary" onclick="saveOutfit(${JSON.stringify(selected.map(s => s.src)).replace(/"/g, '&quot;')})">♡ Save Outfit</button>
        </div>

        <div id="savedSection"></div>
      </div>
    `;

    renderSaved();
  }

  function showError(msg) {
    const panel = document.getElementById('rightPanel');
    panel.innerHTML = `
      <div class="empty-state">
        <div class="empty-icon">⚠️</div>
        <div class="empty-title">Oops</div>
        <div class="empty-sub">${msg}</div>
        <button class="action-btn" style="margin-top:16px;max-width:200px" onclick="resetPanel()">Try Again</button>
      </div>
    `;
  }

  function resetPanel() {
    const panel = document.getElementById('rightPanel');
    panel.innerHTML = `
      <div class="empty-state" id="emptyState">
        <div class="empty-icon">👗</div>
        <div class="empty-title">Your outfit awaits</div>
        <div class="empty-sub">Upload clothes & let AI style you</div>
      </div>
    `;
  }

  // ── SAVED OUTFITS ──────────────────────────────────────────────────────
  function saveOutfit(srcs) {
    savedOutfits.push({ srcs: typeof srcs === 'string' ? JSON.parse(srcs) : srcs, id: Date.now() });
    renderSaved();
    showToast('Outfit saved ✓');
  }

  function shuffleOutfit() {
    if (wardrobe.length >= 2) generateOutfit();
  }

  function renderSaved() {
    const sec = document.getElementById('savedSection');
    if (!sec || savedOutfits.length === 0) return;
    sec.innerHTML = `
      <div class="saved-section">
        <div class="section-label">Saved Looks (${savedOutfits.length})</div>
        <div class="saved-grid">
          ${savedOutfits.map(o => `
            <div class="saved-thumb">
              <img src="${o.srcs[0]}" alt="saved">
            </div>
          `).join('')}
        </div>
      </div>
    `;
  }

  // ── TOAST ──────────────────────────────────────────────────────────────
  function showToast(msg) {
    const t = document.createElement('div');
    t.style.cssText = `
      position:fixed; bottom:32px; left:50%; transform:translateX(-50%);
      background:var(--ink); color:var(--gold-light);
      padding:12px 24px; font-size:0.65rem; letter-spacing:0.15em;
      border-radius:2px; z-index:10000; animation:fadeSlideIn 0.3s ease;
      font-family:'DM Mono',monospace; text-transform:uppercase;
    `;
    t.textContent = msg;
    document.body.appendChild(t);
    setTimeout(() => t.remove(), 2500);
  }
</script>
</body>
</html>

