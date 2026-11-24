# run-roll
run and roll
```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Run &amp; Roll</title>
<style>
  :root {
    --a: #2f80ff;
    --d: #ff8a2b;
    --ink: #12212e;
  }
  * { box-sizing: border-box; }
  html, body {
    margin: 0;
    height: 100%;
    overflow: hidden;
    background: #6ed0f5;
    font-family: "Trebuchet MS", "Segoe UI", sans-serif;
    color: var(--ink);
    user-select: none;
    -webkit-user-select: none;
  }
  #game { display: block; width: 100vw; height: 100vh; }

  #hud {
    position: fixed;
    top: 14px;
    left: 14px;
    right: 14px;
    display: flex;
    align-items: flex-start;
    gap: 12px;
    pointer-events: none;
  }
  .card {
    pointer-events: auto;
    background: rgba(255,255,255,0.9);
    border: 1px solid rgba(255,255,255,0.9);
    border-radius: 16px;
    padding: 12px 16px;
    box-shadow: 0 8px 24px rgba(20,50,70,0.18);
    backdrop-filter: blur(6px);
  }
  .brand {
    font-size: 19px;
    font-weight: 900;
    letter-spacing: 1.5px;
    margin-bottom: 6px;
  }
  .brand span { color: #e2631f; }
  .stats { display: flex; gap: 18px; font-size: 14px; font-weight: 700; }
  .stats b { font-size: 17px; }
  .barrow { display: flex; align-items: center; gap: 8px; margin-top: 8px; font-size: 11px; font-weight: 800; letter-spacing: .6px; }
  .bar {
    width: 132px; height: 9px; border-radius: 9px;
    background: rgba(20,40,60,0.12);
    overflow: hidden;
  }
  .bar i { display: block; height: 100%; width: 0%; border-radius: 9px; transition: width .08s linear; }
  #speedFill { background: linear-gradient(90deg, #35c26b, #b6e04c); }
  #gapFill { background: linear-gradient(90deg, #ff4d4d, #ffc23d); }
  .hint { margin-top: 8px; font-size: 12px; color: #4c6070; font-weight: 600; max-width: 340px; line-height: 1.35; }
  .hint b { color: var(--ink); }

  #muteBtn {
    margin-left: auto;
    pointer-events: auto;
    border: none;
    border-radius: 14px;
    width: 44px; height: 44px;
    font-size: 20px;
    cursor: pointer;
    background: rgba(255,255,255,0.9);
    box-shadow: 0 6px 18px rgba(20,50,70,0.18);
  }
  #muteBtn:active { transform: translateY(2px); }

  #nextWrap {
    position: fixed;
    left: 50%;
    bottom: 22px;
    transform: translateX(-50%);
    display: flex;
    align-items: center;
    gap: 14px;
    pointer-events: none;
  }
  .key {
    pointer-events: auto;
    cursor: pointer;
    width: 76px; height: 76px;
    border-radius: 18px;
    display: grid;
    place-items: center;
    font-size: 34px;
    font-weight: 900;
    color: #fff;
    background: #9aa9b4;
    box-shadow: 0 6px 0 #66747e, 0 10px 20px rgba(0,0,0,.22);
    transition: transform .08s, box-shadow .08s, background .12s;
  }
  .key:active { transform: translateY(4px); box-shadow: 0 2px 0 #66747e; }
  #keyA.on { background: var(--a); box-shadow: 0 6px 0 #1c56b5, 0 0 30px rgba(47,128,255,.65); }
  #keyD.on { background: var(--d); box-shadow: 0 6px 0 #bb5a12, 0 0 30px rgba(255,138,43,.65); }
  #nextLabel { font-size: 12px; font-weight: 900; letter-spacing: 2px; color: #fff; text-shadow: 0 2px 6px rgba(0,0,0,.45); }

  #combo {
    position: fixed;
    left: 50%;
    top: 118px;
    transform: translateX(-50%);
    font-size: 22px;
    font-weight: 900;
    color: #fff;
    text-shadow: 0 3px 0 rgba(0,0,0,.28);
    pointer-events: none;
    opacity: 0;
  }

  #overlay {
    position: fixed;
    inset: 0;
    display: grid;
    place-items: center;
    background: rgba(8, 25, 38, 0.45);
    pointer-events: auto;
  }
  #overlay.hidden { display: none; pointer-events: none; }
  .panel {
    text-align: center;
    background: rgba(255,255,255,0.95);
    border-radius: 24px;
    padding: 28px 38px 30px;
    box-shadow: 0 18px 50px rgba(0,0,0,.3);
    max-width: min(92vw, 520px);
  }
  .panel h1 { margin: 0; font-size: clamp(30px, 6vw, 48px); line-height: 1.05; letter-spacing: 1px; }
  .panel h1 em { font-style: normal; color: #e2631f; }
  .panel p { margin: 14px 0 0; font-size: 15px; line-height: 1.55; color: #3c5060; font-weight: 600; }
  .caps { display: inline-flex; gap: 10px; margin: 12px 0 2px; }
  .cap {
    width: 46px; height: 46px; border-radius: 12px; display: grid; place-items: center;
    font-weight: 900; font-size: 22px; color: white;
  }
  .cap.a { background: var(--a); box-shadow: 0 4px 0 #1c56b5; }
  .cap.d { background: var(--d); box-shadow: 0 4px 0 #bb5a12; }
  .cta {
    display: inline-block;
    margin-top: 18px;
    padding: 11px 22px;
    border-radius: 12px;
    background: #1f9d55;
    color: white;
    font-weight: 900;
    box-shadow: 0 5px 0 #146b3a;
    cursor: pointer;
  }
  .final { margin-top: 14px; font-size: 18px; font-weight: 900; }

  .shop { margin-top: 16px; text-align: left; }
  .shop h3 { margin: 14px 0 8px; font-size: 12px; letter-spacing: 1px; color: #5a6d79; text-transform: uppercase; }
  .row { display: flex; gap: 8px; flex-wrap: wrap; }
  .btn {
    pointer-events: auto; cursor: pointer; border: 1px solid rgba(0,0,0,0.08);
    background: #f4f8fb; color: var(--ink); border-radius: 10px; padding: 8px 10px;
    font: inherit; font-size: 12px; font-weight: 700; line-height: 1.25;
  }
  .btn:hover { filter: brightness(0.98); }
  .btn:disabled { opacity: 0.45; cursor: default; }
  .btn.primary { background: #1f9d55; color: #fff; border-color: #1f9d55; }
  .btn.sel { background: #21313b; color: #fff; border-color: #21313b; }
  .pill { display: inline-block; padding: 2px 8px; border-radius: 999px; font-size: 11px; font-weight: 800; background: rgba(0,0,0,0.06); margin-left: 6px; }
  .meta { font-size: 12px; color: #4c6070; font-weight: 700; margin-top: 6px; }
  .monster-card { display: flex; gap: 12px; align-items: center; background: #f7fbff; border: 1px solid rgba(0,0,0,0.06); border-radius: 12px; padding: 10px; }
  .monster-icon { width: 52px; height: 52px; border-radius: 12px; display: grid; place-items: center; font-size: 28px; background: #fff; }
  .monster-card b { display: block; font-size: 15px; }
  .monster-card small { color: #567; font-weight: 700; }

  #cookieBanner {
    position: fixed; left: 16px; right: 16px; bottom: 16px; z-index: 40;
    display: none; gap: 14px; align-items: center; justify-content: space-between;
    background: rgba(255,255,255,0.96); border: 1px solid rgba(0,0,0,0.08);
    border-radius: 16px; padding: 14px 16px; box-shadow: 0 10px 30px rgba(20,50,70,0.2);
    backdrop-filter: blur(6px);
  }
  #cookieBanner p { margin: 0; font-size: 13px; color: #3c5060; font-weight: 600; line-height: 1.4; }
  #cookieBanner b { color: var(--ink); }
  #cookieBanner .row { flex-wrap: nowrap; }
  @media (max-width: 620px) {
    .stats { gap: 10px; font-size: 12px; }
    .stats b { font-size: 14px; }
    .bar { width: 96px; }
    .hint { display: none; }
    .key { width: 64px; height: 64px; font-size: 28px; }
  }
</style>
</head>
<body>
  <canvas id="game"></canvas>

  <div id="hud">
    <div class="card">
      <div class="brand">RUN <span>&amp; ROLL</span></div>
      <div class="stats">
        <div>🏃 <b id="distance">0</b> m</div>
        <div>⭐ <b id="score">0</b></div>
        <div>🏆 <b id="best">0</b> m</div>
      </div>
      <div class="barrow"><span>SPEED</span><div class="bar"><i id="speedFill"></i></div></div>
      <div class="barrow"><span>LEAD</span><div class="bar"><i id="gapFill"></i></div></div>
      <div class="barrow"><span>STAMINA</span><div class="bar"><i id="stamFill" style="background:linear-gradient(90deg,#ff5a4a,#ffb347)"></i></div></div>
      <div class="hint">Tap <b>A</b> then <b>D</b> ... to run. Same key twice = stumble. ⚡ boosts you. Survive until the monster runs out of stamina, or roll a tougher one for bigger rewards!</div>
      <div class="meta">Coins: <span id="coins">0</span> · Luck: <span id="luckStat">1</span> · Monster: <span id="monsterStat">Boulder</span></div>
    </div>
    <button id="muteBtn" title="Sound on/off">🔊</button>
  </div>

  <div id="cookieBanner">
    <p><b>Save your game?</b> We use browser storage/cookies-like local data to remember coins, upgrades, skins, and best distance on this device.</p>
    <div class="row">
      <button class="btn primary" id="cookieAccept">Accept</button>
      <button class="btn" id="cookieDecline">Decline</button>
    </div>
  </div>

  <div id="combo"></div>

  <div id="nextWrap">
    <div id="keyA" class="key">A</div>
    <div id="nextLabel">NEXT</div>
    <div id="keyD" class="key">D</div>
  </div>

  <div id="overlay">
    <div class="panel">
      <h1 id="ovTitle">RUN <em>&amp; ROLL</em></h1>
      <div id="ovStart">
        <p>Help the harp seal pup outrun the boulder!<br>Keep alternating A and D to scoot along.</p>
        <div class="caps"><div class="cap a">A</div><div class="cap d">D</div><div class="cap a">A</div><div class="cap d">D</div></div>
        <p>Press the highlighted key — or tap it on screen.<br>Same key twice = stumble. ⚡ = speed boost.</p>
        <div class="cta" id="startBtn">Start Run</div>
      </div>
      <div id="ovOver" style="display:none">
        <p id="ovOverMsg">The monster got you!</p>
        <div class="final" id="ovFinal"></div>
        <div class="shop">
          <h3>Monster Roll</h3>
          <div class="monster-card">
            <div class="monster-icon" id="monsterIcon">🪨</div>
            <div style="flex:1">
              <b id="monsterName">Rolling Boulder</b>
              <small id="monsterDesc">Tier 1 · slow · short stamina</small>
            </div>
            <button class="btn primary" id="rollBtn">Roll</button>
          </div>
          <div class="meta" id="rollMsg">Roll for a tougher monster. Higher tier = faster, more stamina, bigger rewards.</div>

          <h3>Upgrades</h3>
          <div class="row" id="upgradeRow"></div>

          <h3>Seal Skins</h3>
          <div class="row" id="skinRow"></div>
        </div>
        <div class="cta" id="againBtn">Back to Start</div>
      </div>
    </div>
  </div>

<script>
(() => {
  'use strict';

  const canvas = document.getElementById('game');
  const ctx = canvas.getContext('2d');
  const sealImg = new Image();
  sealImg.src = 'seal-pup.webp';
  const el = {
    distance: document.getElementById('distance'),
    score: document.getElementById('score'),
    best: document.getElementById('best'),
    coins: document.getElementById('coins'),
    luckStat: document.getElementById('luckStat'),
    monsterStat: document.getElementById('monsterStat'),
    speedFill: document.getElementById('speedFill'),
    gapFill: document.getElementById('gapFill'),
    stamFill: document.getElementById('stamFill'),
    keyA: document.getElementById('keyA'),
    keyD: document.getElementById('keyD'),
    combo: document.getElementById('combo'),
    overlay: document.getElementById('overlay'),
    ovStart: document.getElementById('ovStart'),
    ovOver: document.getElementById('ovOver'),
    ovOverMsg: document.getElementById('ovOverMsg'),
    ovFinal: document.getElementById('ovFinal'),
    startBtn: document.getElementById('startBtn'),
    rollBtn: document.getElementById('rollBtn'),
    rollMsg: document.getElementById('rollMsg'),
    monsterIcon: document.getElementById('monsterIcon'),
    monsterName: document.getElementById('monsterName'),
    monsterDesc: document.getElementById('monsterDesc'),
    upgradeRow: document.getElementById('upgradeRow'),
    skinRow: document.getElementById('skinRow'),
    againBtn: document.getElementById('againBtn'),
    cookieBanner: document.getElementById('cookieBanner'),
    cookieAccept: document.getElementById('cookieAccept'),
    cookieDecline: document.getElementById('cookieDecline'),
    mute: document.getElementById('muteBtn')
  };

  const BASE_MAX_SPEED = 18;
  const FRICTION = 0.955;
  const BASE_STEP_BOOST = 4.0;
  const CHASER_R = 72;

  const MONSTERS = [
    { id:'boulder', tier:1, name:'Rolling Boulder', icon:'🪨', color:'#7a838a', speed:4.7, ramp:6.8, stamina:15000, reward:1.0, desc:'Classic rock. Slow and tired.' },
    { id:'crab', tier:2, name:'Angry Crab', icon:'🦀', color:'#e0533f', speed:5.6, ramp:7.8, stamina:19000, reward:1.25, desc:'Snaps fast, gives up sooner.' },
    { id:'wolf', tier:3, name:'Snow Wolf', icon:'🐺', color:'#8ea2b7', speed:6.5, ramp:8.8, stamina:24000, reward:1.55, desc:'Relentless tundra hunter.' },
    { id:'shark', tier:4, name:'Land Shark', icon:'🦈', color:'#4ea2d8', speed:7.5, ramp:9.6, stamina:30000, reward:1.95, desc:'Fast and hungry for seals.' },
    { id:'dragon', tier:5, name:'Fire Dragon', icon:'🐉', color:'#ff6a3d', speed:8.6, ramp:10.6, stamina:38000, reward:2.5, desc:'Very fast, huge stamina.' },
    { id:'demon', tier:6, name:'Void Demon', icon:'👹', color:'#9a5cff', speed:9.8, ramp:11.8, stamina:48000, reward:3.3, desc:'Top tier nightmare chase.' }
  ];

  const SKINS = [
    { id:'natural', name:'Natural', cost:0, filter:'none' },
    { id:'arctic', name:'Arctic Blue', cost:250, filter:'hue-rotate(185deg) saturate(1.15)' },
    { id:'sunset', name:'Sunset', cost:350, filter:'hue-rotate(330deg) saturate(1.5) brightness(1.03)' },
    { id:'shadow', name:'Shadow', cost:500, filter:'grayscale(1) brightness(0.72) contrast(1.25)' }
  ];

  const UPGRADE_DEFS = [
    { id:'luck', name:'Luck', baseCosts:[150,300,600,1000,1600], desc:'Better monster roll odds' },
    { id:'maxSpeed', name:'Max Speed', baseCosts:[120,240,480,800,1300], desc:'Higher top speed' },
    { id:'accel', name:'Acceleration', baseCosts:[120,240,480,800,1300], desc:'More speed per step' }
  ];
  const UPGRADE_BASE_MAX = 5;
  const UPGRADE_CAP_STEP = 5;
  const LIMIT_COST_BASE = 500;

  let W = 0, H = 0, groundY = 0, dpr = 1;
  let clouds = [];
  let state = 'ready';
  let player, chaser, orbs, particles, particlesText;
  let nextKey = null, combo = 0, score = 0, distance = 0, best = 0, coinsEarned = 0;
  let elapsed = 0, cameraX = 0, shake = 0, orbSpawnX = 0, comboFlash = 0;
  let muted = false, audioCtx = null;
  let lastTime = 0;

  const SAVE_KEY = 'sealRunnerSaveV1';
  const CONSENT_KEY = 'sealRunnerConsent';
  let saveConsent = null; // 'accepted' | 'declined' | null
  let save = {
    best: 0,
    coins: 0,
    luck: 0,
    maxSpeed: 0,
    accel: 0,
    limitTier: 0,
    skin: 'natural',
    ownedSkins: ['natural'],
    monster: 'boulder'
  };

  function readConsent() {
    try { saveConsent = localStorage.getItem(CONSENT_KEY); } catch (e) { saveConsent = null; }
    if (saveConsent !== 'accepted' && saveConsent !== 'declined') saveConsent = null;
  }

  function loadSave() {
    if (saveConsent !== 'accepted') return;
    try {
      const raw = localStorage.getItem(SAVE_KEY);
      if (raw) save = Object.assign(save, JSON.parse(raw));
      best = parseInt(localStorage.getItem('alternateRunnerBest') || save.best || '0', 10) || save.best || 0;
    } catch (e) {}
    save.ownedSkins = Array.isArray(save.ownedSkins) ? save.ownedSkins.filter(Boolean) : ['natural'];
    if (!save.ownedSkins.includes('natural')) save.ownedSkins.unshift('natural');
    if (!SKINS.find(s => s.id === save.skin)) save.skin = 'natural';
    if (!MONSTERS.find(m => m.id === save.monster)) save.monster = 'boulder';
    ['luck','maxSpeed','accel','coins','best','limitTier'].forEach(k => { save[k] = Math.max(0, Number(save[k]) || 0); });
    const oldCaps = [Number(save.luckCap)||0, Number(save.maxSpeedCap)||0, Number(save.accelCap)||0];
    if (oldCaps.some(v => v > 0)) save.limitTier = Math.max(save.limitTier, ...oldCaps);
    best = Math.max(best, save.best || 0);
  }
  function persist() {
    if (saveConsent !== 'accepted') return;
    save.best = best;
    try {
      localStorage.setItem(CONSENT_KEY, 'accepted');
      localStorage.setItem(SAVE_KEY, JSON.stringify(save));
      localStorage.setItem('alternateRunnerBest', String(best));
    } catch (e) {}
  }
  function clearSavedData() {
    try {
      localStorage.removeItem(SAVE_KEY);
      localStorage.removeItem('alternateRunnerBest');
      localStorage.removeItem(CONSENT_KEY);
    } catch (e) {}
  }
  readConsent();
  loadSave();

  function getMonster() { return MONSTERS.find(m => m.id === save.monster) || MONSTERS[0]; }
  function getMaxSpeed() { return BASE_MAX_SPEED + save.maxSpeed * 1.1; }
  function getStepBoost() { return BASE_STEP_BOOST + save.accel * 0.32; }
  function currentSkin() { return SKINS.find(s => s.id === save.skin) || SKINS[0]; }
  function luckBonus() { return save.luck * 0.045; }
  function upgradeMax(def) { return UPGRADE_BASE_MAX + save.limitTier * UPGRADE_CAP_STEP; }
  function upgradeCost(def, lvl) {
    if (lvl < def.baseCosts.length) return def.baseCosts[lvl];
    const extra = lvl - def.baseCosts.length + 1;
    return Math.floor(def.baseCosts[def.baseCosts.length - 1] * Math.pow(1.55, extra));
  }
  function limitIncreaseCost() { return LIMIT_COST_BASE * (save.limitTier + 1) * (save.limitTier + 1); }
  function allUpgradesMaxed() { return UPGRADE_DEFS.every(def => save[def.id] >= upgradeMax(def)); }

  function resize() {
    W = window.innerWidth;
    H = window.innerHeight;
    groundY = H - 90;
    dpr = Math.min(window.devicePixelRatio || 1, 2);
    canvas.width = Math.round(W * dpr);
    canvas.height = Math.round(H * dpr);
    canvas.style.width = W + 'px';
    canvas.style.height = H + 'px';
    clouds = [];
    for (let i = 0; i < 9; i++) {
      clouds.push({
        x: i * (W + 400) / 9 + Math.random() * 120,
        y: 45 + Math.random() * (groundY * 0.42),
        s: 0.55 + Math.random() * 0.9
      });
    }
  }

  function reset() {
    const m = getMonster();
    player = { x: 0, vx: 0, stride: 0, stumble: 0, hurt: 0 };
    chaser = { x: -1000, spin: 0, stamina: m.stamina, maxStamina: m.stamina };
    orbs = [];
    particles = [];
    particlesText = [];
    nextKey = null;
    combo = 0;
    score = 0;
    distance = 0;
    coinsEarned = 0;
    elapsed = 0;
    shake = 0;
    comboFlash = 0;
    cameraX = -W * 0.32;
    orbSpawnX = 520;
    state = 'menu';
    el.overlay.classList.remove('hidden');
    el.ovStart.style.display = 'block';
    el.ovOver.style.display = 'none';
    el.combo.style.opacity = '0';
    updateHUD();
  }

  function opposite(k) { return k === 'a' ? 'd' : 'a'; }

  function beginRun() {
    if (state === 'playing') return;
    reset();
    state = 'ready';
    el.overlay.classList.add('hidden');
    el.ovStart.style.display = 'none';
    el.ovOver.style.display = 'none';
  }

  function onPress(k) {
    k = String(k).toLowerCase();
    if (k !== 'a' && k !== 'd') return;
    if (state === 'over' || state === 'menu') return; // must use Start button
    if (state === 'ready') {
      state = 'playing';
      el.overlay.classList.add('hidden');
    }
    if (player.stumble > 0) return;

    if (nextKey === null || k === nextKey) {
      combo++;
      score += 10 * Math.min(1 + Math.floor(combo / 5), 5);
      player.stride += Math.PI;
      player.vx = Math.min(getMaxSpeed(), player.vx + getStepBoost() + Math.min(combo, 20) * 0.02);
      nextKey = opposite(k);
      dust(player.x - 12, groundY - 2, 7, 'rgba(190,160,110,0.8)', 1.6);
      beep(k === 'a' ? 320 : 420, 0.07, 'triangle', 0.05, k === 'a' ? 430 : 310);
      if (combo > 0 && combo % 10 === 0) {
        comboFlash = 1;
        particlesText.push({ x: player.x, y: groundY - 150, text: combo + ' COMBO!', life: 60, max: 60 });
        beep(880, 0.14, 'sine', 0.05, 1320);
      }
    } else {
      combo = 0;
      player.stumble = 36;
      player.hurt = 26;
      player.vx = Math.min(player.vx, 1.5);
      shake = 13;
      dust(player.x, groundY - 8, 16, 'rgba(230,110,80,0.85)', 2.8);
      beep(150, 0.22, 'sawtooth', 0.09, 60);
    }
    updateHUD();
  }

  function dust(x, y, n, color, power) {
    for (let i = 0; i < n; i++) {
      particles.push({
        x: x + (Math.random() - 0.5) * 20,
        y: y + (Math.random() - 0.5) * 8,
        vx: (Math.random() - 0.75) * 3 * power,
        vy: -Math.random() * 2.6 * power,
        size: 2 + Math.random() * 4,
        life: 20 + Math.random() * 22,
        max: 42,
        color: color,
        grav: 0.12
      });
    }
  }

  function ensureAudio() {
    if (muted) return null;
    try {
      if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
      if (audioCtx.state === 'suspended') audioCtx.resume();
      return audioCtx;
    } catch (e) { return null; }
  }

  function beep(freq, dur, type, vol, endFreq) {
    const ac = ensureAudio();
    if (!ac) return;
    const t = ac.currentTime;
    const o = ac.createOscillator();
    const g = ac.createGain();
    o.type = type || 'sine';
    o.frequency.setValueAtTime(freq, t);
    if (endFreq) o.frequency.exponentialRampToValueAtTime(endFreq, t + dur);
    g.gain.setValueAtTime(vol || 0.06, t);
    g.gain.exponentialRampToValueAtTime(0.001, t + dur);
    o.connect(g);
    g.connect(ac.destination);
    o.start(t);
    o.stop(t + dur + 0.02);
  }

  function gameOver(reason) {
    state = 'over';
    if (distance > best) best = distance;
    const m = getMonster();
    coinsEarned = Math.max(5, Math.floor((distance * 0.7 + score * 0.08) * m.reward));
    if (reason === 'escaped') coinsEarned = Math.floor(coinsEarned * 1.35 + 40 * m.tier);
    save.coins += coinsEarned;
    persist();
    shake = reason === 'caught' ? 20 : 8;
    if (reason === 'caught') beep(320, 0.5, 'sawtooth', 0.09, 70);
    else { beep(660, 0.12, 'sine', 0.06, 990); beep(990, 0.18, 'sine', 0.05, 1320); }
    el.ovOverMsg.textContent = reason === 'escaped' ? (m.icon + ' ' + m.name + ' ran out of stamina! You escaped!') : ('The ' + m.name.toLowerCase() + ' got you!');
    el.ovFinal.innerHTML = 'Distance <b>' + distance + ' m</b> · Score <b>' + score + '</b><br>Coins earned <b>+' + coinsEarned + '</b> · Total <b>' + save.coins + '</b><br>Best: ' + best + ' m';
    el.ovStart.style.display = 'none';
    el.ovOver.style.display = 'block';
    el.overlay.classList.remove('hidden');
    renderShop();
    updateHUD();
  }

  function update(dt) {
    const f = Math.min(dt, 50) / (1000 / 60);
    if (comboFlash > 0) comboFlash -= f * 0.02;

    if (state === 'playing') {
      elapsed += dt;

      if (player.stumble > 0) {
        player.stumble -= f;
        player.vx *= Math.pow(0.87, f);
      } else {
        player.vx *= Math.pow(FRICTION, f);
      }
      if (player.vx < 0.05) player.vx = 0;
      if (player.hurt > 0) player.hurt -= f;

      player.x += player.vx * f;
      player.stride += player.vx * f * 0.055;

      const m = getMonster();
      const difficulty = Math.min(1, elapsed / 90000);
      const chaserSpeed = m.speed + difficulty * m.ramp;
      if (state === 'playing') {
        chaser.x += chaserSpeed * f;
        chaser.spin -= chaserSpeed * f * 0.007;
        chaser.stamina = Math.max(0, chaser.stamina - dt * (0.85 + difficulty * 0.25));
      }

      distance = Math.max(distance, Math.floor(player.x / 28));

      while (orbSpawnX < player.x + W * 1.4) {
        orbs.push({ x: orbSpawnX, y: groundY - 72 - Math.random() * 30, spin: Math.random() * 6, taken: false });
        orbSpawnX += 480 + Math.random() * 420;
      }

      for (let i = orbs.length - 1; i >= 0; i--) {
        const o = orbs[i];
        o.spin += 0.05 * f;
        if (!o.taken && Math.abs(o.x - player.x) < 38) {
          o.taken = true;
          player.vx = Math.min(getMaxSpeed(), player.vx + 2.8);
          score += 75;
          dust(o.x, o.y, 14, 'rgba(255,225,90,0.95)', 2.2);
          beep(700, 0.1, 'sine', 0.055, 1200);
          beep(1050, 0.14, 'sine', 0.04, 1500);
        }
        if (o.taken || o.x < cameraX - 120) orbs.splice(i, 1);
      }

      if (state === 'playing') {
        const gap = player.x - chaser.x;
        if (gap < CHASER_R + 8) gameOver('caught');
        else if (chaser.stamina <= 0) gameOver('escaped');
      }

      cameraX += ((player.x - W * 0.32) - cameraX) * Math.min(1, 0.12 * f);
      updateHUD();
    }

    for (let i = particles.length - 1; i >= 0; i--) {
      const p = particles[i];
      p.vy += p.grav * f;
      p.x += p.vx * f;
      p.y += p.vy * f;
      p.life -= f;
      if (p.y > groundY - 1) { p.y = groundY - 1; p.vy *= -0.3; p.vx *= 0.7; }
      if (p.life <= 0) particles.splice(i, 1);
    }
    for (let i = particlesText.length - 1; i >= 0; i--) {
      const t = particlesText[i];
      t.y -= 0.7 * f;
      t.life -= f;
      if (t.life <= 0) particlesText.splice(i, 1);
    }

    shake *= Math.pow(0.88, f);
    if (shake < 0.2) shake = 0;
  }

  function updateHUD() {
    const m = getMonster();
    el.distance.textContent = distance;
    el.score.textContent = score;
    el.best.textContent = best;
    el.coins.textContent = save.coins;
    el.luckStat.textContent = save.luck;
    el.monsterStat.textContent = m.name;
    el.speedFill.style.width = (player ? (player.vx / getMaxSpeed()) * 100 : 0) + '%';
    const gap = player ? Math.max(0, player.x - chaser.x - CHASER_R) : 1000;
    el.gapFill.style.width = Math.min(100, (gap / 1200) * 100) + '%';
    el.stamFill.style.width = (chaser && chaser.maxStamina ? (chaser.stamina / chaser.maxStamina) * 100 : 100) + '%';
    el.keyA.classList.toggle('on', nextKey === 'a');
    el.keyD.classList.toggle('on', nextKey === 'd');
    el.combo.textContent = combo >= 5 ? 'COMBO × ' + combo : '';
    el.combo.style.opacity = combo >= 5 ? '1' : '0';
  }

  function drawCloud(c) {
    const span = W + 400;
    let x = ((c.x - cameraX * 0.12) % span + span) % span - 200;
    const y = c.y, s = c.s;
    ctx.fillStyle = 'rgba(255,255,255,0.85)';
    ctx.beginPath();
    ctx.arc(x, y, 24 * s, 0, Math.PI * 2);
    ctx.arc(x + 26 * s, y - 10 * s, 30 * s, 0, Math.PI * 2);
    ctx.arc(x + 58 * s, y, 22 * s, 0, Math.PI * 2);
    ctx.rect(x, y, 58 * s, 24 * s);
    ctx.fill();
  }

  function drawBackground() {
    const sky = ctx.createLinearGradient(0, 0, 0, groundY);
    sky.addColorStop(0, '#4fbdf0');
    sky.addColorStop(0.65, '#a6e4fb');
    sky.addColorStop(1, '#e8f9ff');
    ctx.fillStyle = sky;
    ctx.fillRect(0, 0, W, groundY);

    ctx.fillStyle = 'rgba(255,246,180,0.95)';
    ctx.beginPath();
    ctx.arc(W - 110, 100, 44, 0, Math.PI * 2);
    ctx.fill();
    ctx.strokeStyle = 'rgba(255,246,180,0.35)';
    ctx.lineWidth = 3;
    for (let i = 0; i < 8; i++) {
      const a = i * Math.PI / 4 + elapsed * 0.0002;
      ctx.beginPath();
      ctx.moveTo(W - 110 + Math.cos(a) * 58, 100 + Math.sin(a) * 58);
      ctx.lineTo(W - 110 + Math.cos(a) * 70, 100 + Math.sin(a) * 70);
      ctx.stroke();
    }

    for (const c of clouds) drawCloud(c);

    // mountains
    drawRidge(0.18, groundY - 60, 150, '#9dc8e2');
    drawRidge(0.32, groundY - 28, 105, '#7fb9d6');
    drawRidge(0.55, groundY - 8, 62, '#84c46f');

    // ground
    ctx.fillStyle = '#6d4a30';
    ctx.fillRect(0, groundY, W, H - groundY);
    ctx.fillStyle = '#7dc24f';
    ctx.fillRect(0, groundY - 8, W, 12);
    ctx.fillStyle = '#5f3f28';
    const off = ((cameraX % 84) + 84) % 84;
    for (let x = -off; x < W + 84; x += 84) {
      ctx.fillRect(x + 12, groundY + 20, 40, 6);
      ctx.fillRect(x + 52, groundY + 46, 24, 5);
    }
    ctx.fillStyle = '#69ae43';
    for (let x = -(((cameraX * 1) % 120 + 120) % 120); x < W + 120; x += 120) {
      ctx.beginPath();
      ctx.moveTo(x, groundY - 8);
      ctx.lineTo(x - 5, groundY - 18);
      ctx.lineTo(x + 1, groundY - 12);
      ctx.lineTo(x + 7, groundY - 19);
      ctx.lineTo(x + 4, groundY - 8);
      ctx.fill();
    }
  }

  function drawRidge(parallax, baseY, amp, color) {
    ctx.fillStyle = color;
    ctx.beginPath();
    ctx.moveTo(0, groundY + 2);
    const shift = cameraX * parallax;
    for (let x = 0; x <= W + 40; x += 40) {
      const wx = x + shift;
      const y = baseY - Math.abs(Math.sin(wx * 0.0028 + parallax * 7) * amp)
                     - Math.abs(Math.sin(wx * 0.0011 + parallax * 3)) * amp * 0.4;
      ctx.lineTo(x, y);
    }
    ctx.lineTo(W, groundY + 2);
    ctx.closePath();
    ctx.fill();
  }

  function roundRect(x, y, w, h, r) {
    const rr = Math.min(r, w / 2, h / 2);
    ctx.beginPath();
    ctx.moveTo(x + rr, y);
    ctx.arcTo(x + w, y, x + w, y + h, rr);
    ctx.arcTo(x + w, y + h, x, y + h, rr);
    ctx.arcTo(x, y + h, x, y, rr);
    ctx.arcTo(x, y, x + w, y, rr);
    ctx.closePath();
  }

  function drawPlayer() {
    const sx = player.x - cameraX;
    const ratio = player.vx / getMaxSpeed();
    ctx.save();
    ctx.translate(sx, groundY);

    ctx.fillStyle = 'rgba(20,40,50,0.18)';
    ctx.beginPath();
    ctx.ellipse(0, 1, 26 + ratio * 8, 6, 0, 0, Math.PI * 2);
    ctx.fill();

    const stumbling = player.stumble > 0;
    if (stumbling) ctx.rotate(-0.85 + Math.sin(player.stumble * 0.5) * 0.1);
    else ctx.rotate(-0.04 - ratio * 0.2);

    const bob = stumbling ? 0 : Math.sin(player.stride * 2) * 3 * ratio;

    ctx.save();
    ctx.translate(0, -bob);

    // the seal pup is the runner now
    if (sealImg.complete && sealImg.naturalWidth > 0) {
      const w = 120;
      const h = w * (sealImg.naturalHeight / sealImg.naturalWidth);
      ctx.fillStyle = 'rgba(255,255,255,0.96)';
      roundRect(-w / 2 - 5, -h - 5, w + 10, h + 10, 17);
      ctx.fill();
      ctx.save();
      roundRect(-w / 2, -h, w, h, 13);
      ctx.clip();
      ctx.filter = currentSkin().filter;
      ctx.drawImage(sealImg, -w / 2, -h, w, h);
      ctx.filter = 'none';
      ctx.restore();
    }

    ctx.restore();

    if (stumbling) {
      ctx.fillStyle = '#ffd93d';
      ctx.font = 'bold 20px sans-serif';
      ctx.textAlign = 'center';
      for (let i = 0; i < 3; i++) {
        const a = elapsed * 0.012 + i * Math.PI * 2 / 3;
        ctx.fillText('★', Math.cos(a) * 24, -124 + Math.sin(a) * 8);
      }
    }
    if (player.hurt > 0) {
      ctx.strokeStyle = 'rgba(255,70,60,' + (player.hurt / 26) + ')';
      ctx.lineWidth = 4;
      ctx.beginPath();
      ctx.arc(0, -62, 40, 0, Math.PI * 2);
      ctx.stroke();
    }
    ctx.restore();
  }

  function drawBoulder() {
    const m = getMonster();
    const sx = chaser.x - cameraX;
    if (sx < -CHASER_R - 40) {
      const gap = Math.max(0, player.x - chaser.x - CHASER_R);
      const a = 0.45 + 0.35 * Math.sin(elapsed * 0.012);
      ctx.fillStyle = 'rgba(220,60,50,' + a + ')';
      ctx.beginPath();
      ctx.moveTo(18, groundY - 60);
      ctx.lineTo(64, groundY - 34);
      ctx.lineTo(18, groundY - 8);
      ctx.fill();
      ctx.fillStyle = '#fff';
      ctx.font = 'bold 12px sans-serif';
      ctx.fillText(m.icon + ' ' + Math.round(gap / 28) + 'm', 16, groundY - 68);
      return;
    }

    ctx.save();
    ctx.translate(sx, groundY - CHASER_R + 4);
    ctx.fillStyle = 'rgba(20,40,50,0.2)';
    ctx.beginPath();
    ctx.ellipse(0, CHASER_R - 2, CHASER_R * 0.95, 12, 0, 0, Math.PI * 2);
    ctx.fill();

    ctx.save();
    ctx.rotate(chaser.spin);
    const g = ctx.createRadialGradient(-22, -26, 8, 0, 0, CHASER_R);
    g.addColorStop(0, '#ffffff55');
    g.addColorStop(0.05, m.color);
    g.addColorStop(0.7, m.color);
    g.addColorStop(1, '#1f2933');
    ctx.fillStyle = g;
    ctx.beginPath();
    ctx.arc(0, 0, CHASER_R, 0, Math.PI * 2);
    ctx.fill();
    ctx.strokeStyle = 'rgba(0,0,0,0.25)';
    ctx.lineWidth = 3;
    for (let i = 0; i < 7; i++) {
      const a = i * Math.PI * 2 / 7;
      ctx.beginPath();
      ctx.moveTo(Math.cos(a) * 18, Math.sin(a) * 18);
      ctx.lineTo(Math.cos(a + 0.35) * (CHASER_R - 10), Math.sin(a + 0.35) * (CHASER_R - 10));
      ctx.stroke();
    }
    ctx.restore();

    // face/icon
    ctx.fillStyle = 'rgba(255,255,255,0.2)';
    ctx.beginPath(); ctx.arc(0, 0, CHASER_R * 0.72, 0, Math.PI * 2); ctx.fill();
    ctx.font = '64px serif';
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';
    ctx.fillText(m.icon, 0, 4);

    // angry eyes overlay for menace
    ctx.strokeStyle = 'rgba(20,10,10,0.55)';
    ctx.lineWidth = 4;
    ctx.beginPath();
    ctx.moveTo(-26, -18); ctx.lineTo(-8, -10);
    ctx.moveTo(26, -18); ctx.lineTo(8, -10);
    ctx.stroke();

    ctx.restore();

    // stamina label
    ctx.fillStyle = 'rgba(255,255,255,0.85)';
    ctx.font = 'bold 12px sans-serif';
    ctx.textAlign = 'center';
    ctx.fillText(m.name, sx, groundY - CHASER_R * 2 - 10);
  }

  function drawOrbs() {
    for (const o of orbs) {
      const sx = o.x - cameraX;
      if (sx < -60 || sx > W + 60) continue;
      ctx.save();
      ctx.translate(sx, o.y + Math.sin(o.spin) * 5);
      ctx.fillStyle = 'rgba(255,220,70,0.25)';
      ctx.beginPath();
      ctx.arc(0, 0, 24, 0, Math.PI * 2);
      ctx.fill();
      ctx.fillStyle = '#ffd93d';
      ctx.beginPath();
      ctx.arc(0, 0, 15, 0, Math.PI * 2);
      ctx.fill();
      ctx.strokeStyle = '#e9a400';
      ctx.lineWidth = 2;
      ctx.stroke();
      ctx.fillStyle = '#fff';
      ctx.beginPath();
      ctx.moveTo(-2, -11); ctx.lineTo(5, -1); ctx.lineTo(1, -1);
      ctx.lineTo(4, 11); ctx.lineTo(-6, -2); ctx.lineTo(-1, -2);
      ctx.fill();
      ctx.restore();
    }
  }

  function drawParticles() {
    for (const p of particles) {
      ctx.globalAlpha = Math.max(0, Math.min(1, p.life / 18));
      ctx.fillStyle = p.color;
      ctx.beginPath();
      ctx.arc(p.x - cameraX, p.y, p.size, 0, Math.PI * 2);
      ctx.fill();
    }
    ctx.globalAlpha = 1;
    ctx.textAlign = 'center';
    ctx.font = 'bold 22px "Trebuchet MS", sans-serif';
    for (const t of particlesText) {
      ctx.globalAlpha = Math.max(0, Math.min(1, t.life / 25));
      ctx.fillStyle = '#ffdd44';
      ctx.strokeStyle = 'rgba(60,40,10,0.6)';
      ctx.lineWidth = 4;
      ctx.strokeText(t.text, t.x - cameraX, t.y);
      ctx.fillText(t.text, t.x - cameraX, t.y);
    }
    ctx.globalAlpha = 1;
  }

  function drawDanger() {
    if (!player) return;
    const gap = player.x - chaser.x - CHASER_R;
    if (gap > 420 || state !== 'playing') return;
    const a = (1 - gap / 420) * 0.45;
    const grd = ctx.createRadialGradient(W / 2, H / 2, H * 0.25, W / 2, H / 2, H * 0.85);
    grd.addColorStop(0, 'rgba(255,40,30,0)');
    grd.addColorStop(1, 'rgba(255,40,30,' + a + ')');
    ctx.fillStyle = grd;
    ctx.fillRect(0, 0, W, H);
  }

  function draw() {
    ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
    ctx.clearRect(0, 0, W, H);
    ctx.save();
    if (shake > 0) ctx.translate((Math.random() - 0.5) * shake, (Math.random() - 0.5) * shake);
    drawBackground();
    drawOrbs();
    drawBoulder();
    drawPlayer();
    drawParticles();
    drawDanger();
    ctx.restore();
  }

  function rollMonster() {
    const luck = luckBonus();
    const weights = MONSTERS.map((m, i) => {
      const base = Math.max(1, 8 - i * 1.1);
      return base + i * luck * 18;
    });
    const total = weights.reduce((a,b)=>a+b,0);
    let r = Math.random() * total;
    let picked = MONSTERS[0];
    for (let i = 0; i < MONSTERS.length; i++) { r -= weights[i]; if (r <= 0) { picked = MONSTERS[i]; break; } }
    save.monster = picked.id;
    persist();
    el.rollMsg.textContent = 'Rolled: ' + picked.icon + ' ' + picked.name + ' (Tier ' + picked.tier + ') — faster chase, more stamina, ' + Math.round(picked.reward*100) + '% coin reward.';
    renderShop();
    updateHUD();
    beep(520 + picked.tier * 60, 0.08, 'triangle', 0.05, 780 + picked.tier * 70);
  }

  function buyUpgrade(id) {
    const def = UPGRADE_DEFS.find(u => u.id === id);
    if (!def) return;
    const lvl = save[id];
    const max = upgradeMax(def);
    if (lvl >= max) { el.rollMsg.textContent = def.name + ' is at the current limit. Increase the limit first.'; return; }
    const cost = upgradeCost(def, lvl);
    if (save.coins < cost) { el.rollMsg.textContent = 'Not enough coins for ' + def.name + '.'; return; }
    save.coins -= cost;
    save[id]++;
    persist();
    el.rollMsg.textContent = def.name + ' upgraded to level ' + save[id] + '!';
    renderShop();
    updateHUD();
    beep(740, 0.09, 'sine', 0.05, 1100);
  }

  function increaseUpgradeLimit() {
    if (!allUpgradesMaxed()) { el.rollMsg.textContent = 'Max out all upgrades before increasing the limit.'; return; }
    const cost = limitIncreaseCost();
    if (save.coins < cost) { el.rollMsg.textContent = 'Not enough coins to increase the limit (' + cost + 'c).'; return; }
    save.coins -= cost;
    save.limitTier++;
    persist();
    el.rollMsg.textContent = 'Upgrade limit increased! New max level: ' + upgradeMax(UPGRADE_DEFS[0]) + '.';
    renderShop();
    updateHUD();
    beep(520, 0.08, 'sine', 0.05, 1040);
  }

  function buyOrEquipSkin(id) {
    const skin = SKINS.find(s => s.id === id);
    if (!skin) return;
    if (!save.ownedSkins.includes(id)) {
      if (save.coins < skin.cost) { el.rollMsg.textContent = 'Not enough coins for ' + skin.name + '.'; return; }
      save.coins -= skin.cost;
      save.ownedSkins.push(id);
      el.rollMsg.textContent = 'Unlocked ' + skin.name + ' skin!';
    } else {
      el.rollMsg.textContent = 'Equipped ' + skin.name + ' skin.';
    }
    save.skin = id;
    persist();
    renderShop();
    updateHUD();
  }

  function renderShop() {
    const m = getMonster();
    el.monsterIcon.textContent = m.icon;
    el.monsterIcon.style.background = m.color + '22';
    el.monsterName.textContent = m.name;
    el.monsterDesc.textContent = 'Tier ' + m.tier + ' · speed ' + m.speed.toFixed(1) + ' · stamina ' + Math.round(m.stamina/1000) + 's · reward ' + Math.round(m.reward*100) + '%';

    el.upgradeRow.innerHTML = '';
    UPGRADE_DEFS.forEach(def => {
      const lvl = save[def.id];
      const max = upgradeMax(def);
      const btn = document.createElement('button');
      btn.className = 'btn';
      const cost = lvl >= max ? null : upgradeCost(def, lvl);
      btn.textContent = def.name + ' L' + lvl + '/' + max + (cost ? ' (' + cost + 'c)' : ' MAX');
      btn.title = def.desc;
      btn.disabled = lvl >= max || (cost !== null && save.coins < cost);
      btn.addEventListener('click', () => buyUpgrade(def.id));
      el.upgradeRow.appendChild(btn);
    });

    const capBtn = document.createElement('button');
    capBtn.className = 'btn primary';
    const canCap = allUpgradesMaxed();
    const capCost = limitIncreaseCost();
    capBtn.textContent = canCap ? 'Increase Limit (' + capCost + 'c)' : 'Increase Limit (max all first)';
    capBtn.disabled = !canCap || save.coins < capCost;
    capBtn.title = 'When all upgrades reach the current limit, pay coins to raise every upgrade limit by ' + UPGRADE_CAP_STEP + '.';
    capBtn.addEventListener('click', increaseUpgradeLimit);
    el.upgradeRow.appendChild(capBtn);

    el.skinRow.innerHTML = '';
    SKINS.forEach(skin => {
      const owned = save.ownedSkins.includes(skin.id);
      const selected = save.skin === skin.id;
      const btn = document.createElement('button');
      btn.className = 'btn' + (selected ? ' sel' : '');
      btn.textContent = skin.name + (selected ? ' ✓' : owned ? '' : ' (' + skin.cost + 'c)');
      btn.disabled = !owned && save.coins < skin.cost;
      btn.addEventListener('click', () => buyOrEquipSkin(skin.id));
      el.skinRow.appendChild(btn);
    });
  }

  function loop(now) {
    const dt = lastTime ? now - lastTime : 16.7;
    lastTime = now;
    update(dt);
    draw();
    requestAnimationFrame(loop);
  }

  window.addEventListener('keydown', (e) => {
    if (e.repeat) return;
    const k = e.key.toLowerCase();
    if (k === 'a' || k === 'd') { e.preventDefault(); onPress(k); }
    else if ((k === 'enter' || k === ' ') && state !== 'playing') { e.preventDefault(); if (state === 'over') reset(); else beginRun(); }
  });

  el.keyA.addEventListener('pointerdown', (e) => { e.preventDefault(); onPress('a'); });
  el.keyD.addEventListener('pointerdown', (e) => { e.preventDefault(); onPress('d'); });
  el.startBtn.addEventListener('click', beginRun);
  el.againBtn.addEventListener('click', () => { reset(); });
  el.rollBtn.addEventListener('click', rollMonster);
  el.mute.addEventListener('click', () => {
    muted = !muted;
    el.mute.textContent = muted ? '🔇' : '🔊';
    if (!muted) beep(660, 0.08, 'sine', 0.05);
  });
  function showCookiePrompt() {
    el.cookieBanner.style.display = saveConsent ? 'none' : 'flex';
  }
  el.cookieAccept.addEventListener('click', () => {
    saveConsent = 'accepted';
    try { localStorage.setItem(CONSENT_KEY, 'accepted'); } catch (e) {}
    persist();
    showCookiePrompt();
    el.rollMsg.textContent = 'Save enabled. Your progress will be remembered on this device.';
  });
  el.cookieDecline.addEventListener('click', () => {
    saveConsent = 'declined';
    clearSavedData();
    el.rollMsg.textContent = 'Save declined. Progress will not be saved after closing this page.';
    showCookiePrompt();
  });

  window.addEventListener('resize', resize);

  resize();
  renderShop();
  reset();
  showCookiePrompt();
  updateHUD();
  requestAnimationFrame(loop);
})();
</script>
</body>
</html>
```