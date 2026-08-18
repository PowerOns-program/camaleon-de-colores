<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>🦎 El Camaleón de Colores</title>
<style>
  :root{
    --bg-deep:#0B3D2E;
    --bg-mid:#0F5940;
    --panel:#146B4F;
    --panel-light:#1F8A66;
    --gold:#F4C542;
    --paper:#F6F1DC;
    --ink:#20321F;
    --shadow: rgba(0,0,0,.35);
  }
  *{box-sizing:border-box;}
  html,body{
    margin:0; padding:0; height:100%;
    font-family: 'Segoe UI', Verdana, Arial, sans-serif;
    background: radial-gradient(circle at 50% 0%, var(--bg-mid), var(--bg-deep) 70%);
    color: var(--paper);
    overflow:hidden;
    -webkit-user-select:none; user-select:none;
  }
  #app{
    height:100vh; width:100vw;
    display:flex; align-items:center; justify-content:center;
    position:relative;
  }

  .screen{
    width:100%; max-width:520px; height:100%; max-height:840px;
    display:none; flex-direction:column; align-items:center; justify-content:center;
    padding:20px; position:relative; z-index:1;
  }
  .screen.active{display:flex;}

  h1{
    font-size: clamp(26px,7vw,38px);
    font-weight:800; letter-spacing:.5px;
    margin:0 0 6px 0; text-align:center;
    text-shadow: 0 3px 0 var(--shadow);
  }
  .subtitle{
    font-size:15px; opacity:.85; text-align:center; margin-bottom:18px; line-height:1.5;
  }
  .notebook-hint{
    background: var(--paper); color: var(--ink);
    border-radius: 14px; padding:14px 18px; font-size:14px; line-height:1.5;
    max-width:360px; text-align:center; margin-bottom:24px;
    box-shadow: 0 6px 0 var(--shadow);
    border: 2px dashed #c9bf94;
  }
  .btn{
    font-family:inherit; font-weight:800; font-size:17px; letter-spacing:.3px;
    padding:14px 34px; border-radius:40px; border:none; cursor:pointer;
    background: var(--gold); color:#3a2a00;
    box-shadow: 0 6px 0 #a9821f;
    transition: transform .08s ease;
  }
  .btn:active{ transform: translateY(4px); box-shadow: 0 2px 0 #a9821f; }
  .btn:disabled{ opacity:.5; cursor:not-allowed; pointer-events:none; }
  .btn.secondary{
    background: var(--panel-light); color: var(--paper);
    box-shadow: 0 6px 0 #0d4a37;
  }
  .btn-row{ display:flex; gap:14px; margin-top:8px; flex-wrap:wrap; justify-content:center;}

  /* ---- HUD ---- */
  #hud{
    width:100%; display:flex; justify-content:space-between; align-items:center;
    font-size:13px; font-weight:700; margin-bottom:8px; gap:6px;
  }
  #hud span{ background: rgba(0,0,0,.25); padding:6px 10px; border-radius:20px; white-space:nowrap;}
  #hudLevel{ transition: background .3s ease; }
  #timerTrack{
    width:100%; height:12px; background: rgba(0,0,0,.3); border-radius:10px; overflow:hidden; margin-bottom:14px;
  }
  #timerBar{
    height:100%; width:100%; background: linear-gradient(90deg, #F4C542, #F3722C);
    transition: width linear;
  }

  /* ---- Stage & Chameleon ---- */
  #stage{
    width:100%; display:flex; flex-direction:column; align-items:center; justify-content:center;
    flex:1; position:relative; min-height:0;
  }
  #chamWrap{
    width:230px; height:180px; position:relative;
    animation: bob 2.6s ease-in-out infinite;
  }
  @keyframes bob{
    0%,100%{ transform: translateY(0); }
    50%{ transform: translateY(-6px); }
  }
  #leafBack{
    position:absolute; left:10px; top:20px; width:70px; height:70px;
    background: rgba(255,255,255,.05); border-radius: 0 100% 0 100%;
    transform: rotate(20deg);
  }
  #tongue{
    position:absolute; top:64px; left:196px; width:0px; height:7px;
    background: #ff6b81; border-radius:4px; transform-origin:left center; z-index:3;
    box-shadow: 0 0 0 2px #d43f5c inset;
  }
  #tongue::after{
    content:''; position:absolute; right:-6px; top:-4px; width:14px; height:14px;
    border-radius:50%; background:#ff6b81; opacity:0;
  }
  .tongue-shoot{ animation: shoot .5s ease-out; }
  .tongue-shoot::after{ animation: tip .5s ease-out; }
  @keyframes shoot{
    0%{ width:0px; }
    55%{ width:105px; }
    100%{ width:0px; }
  }
  @keyframes tip{
    0%{ opacity:0; right:-6px;}
    55%{ opacity:1; right:-6px;}
    100%{ opacity:0; right:-6px;}
  }
  #branch{
    width:230px; height:15px; background: linear-gradient(180deg,#7a5233,#4a3018);
    border-radius:10px; margin-top:-4px;
    box-shadow: 0 4px 0 #2f1c0c;
    position:relative; z-index:0;
  }

  #prompt{
    text-align:center; font-size:16px; font-weight:600; margin:14px 0 4px 0; line-height:1.5;
  }
  #prompt b{ color: var(--gold); }

  #revealCard{
    display:none; background: var(--paper); color:var(--ink);
    border-radius:18px; padding:20px 28px; text-align:center;
    box-shadow: 0 8px 0 var(--shadow);
    margin-top:8px;
  }
  #revealWord{ font-size:32px; font-weight:900; letter-spacing:1px; text-transform:uppercase; }
  #revealEs{ font-size:15px; font-weight:600; opacity:.7; text-transform:none; margin-top:2px; }
  #revealEmoji{ font-size:28px; }

  #feedback{
    margin-top:10px; font-size:14px; font-weight:700; min-height:20px;
  }

  #soundToggle{
    position:absolute; top:16px; right:16px; z-index:5;
    background: rgba(0,0,0,.3); border:none; color:var(--paper);
    width:38px; height:38px; border-radius:50%; font-size:18px; cursor:pointer;
  }

  #stars{ font-size:40px; margin:14px 0; letter-spacing:4px;}

  @media (max-height: 720px){
    #chamWrap{ width:190px; height:150px; }
    #branch{ width:190px; }
    h1{ font-size: 24px; }
  }
</style>
</head>
<body>
<div id="app">
  <button id="soundToggle" title="Silenciar música">🔊</button>

  <!-- START SCREEN -->
  <div class="screen active" id="screen-start">
    <h1>🦎 El Camaleón de Colores</h1>
    <div class="subtitle">Practica los colores en inglés — ¡la dificultad sube en cada ronda!</div>
    <div class="notebook-hint">
      📓✏️ <b>Prepara tu cuaderno y un lápiz.</b><br>
      El camaleón cambiará de color y tendrás cada vez <b>menos tiempo</b> para escribir. Anota el color en inglés y toca "Ya escribí" para revisar.
    </div>
    <button class="btn" id="btnStart">¡Empezar a jugar!</button>
  </div>

  <!-- GAME SCREEN -->
  <div class="screen" id="screen-game">
    <div id="hud">
      <span id="hudRound">Ronda 1/12</span>
      <span id="hudLevel">🟢 Fácil</span>
      <span id="hudScore">⭐ 0</span>
    </div>
    <div id="timerTrack"><div id="timerBar"></div></div>

    <div id="stage">
      <div id="chamWrap">
        <div id="leafBack"></div>
        <svg id="chamSvg" viewBox="0 0 240 190" width="100%" height="100%">
          <defs>
            <linearGradient id="chamGrad" x1="0" y1="0" x2="1" y2="1">
              <stop id="gradStop1" offset="0%" stop-color="#2f6f56"/>
              <stop id="gradStop2" offset="45%" stop-color="#43AA8B"/>
              <stop id="gradStop3" offset="100%" stop-color="#1f4a3a"/>
            </linearGradient>
          </defs>
          <!-- tail (curled) -->
          <path id="chamTail" d="M52,138 C18,140 6,112 20,92 C32,76 52,84 48,98 C45,108 32,106 34,96"
                fill="none" stroke="url(#chamGrad)" stroke-width="16" stroke-linecap="round"/>
          <!-- back legs -->
          <path d="M75,150 Q68,168 52,172" fill="none" stroke="#2f6f56" stroke-width="10" stroke-linecap="round"/>
          <path d="M150,152 Q158,170 174,174" fill="none" stroke="#2f6f56" stroke-width="10" stroke-linecap="round"/>
          <!-- body -->
          <ellipse id="chamBody" cx="112" cy="118" rx="68" ry="44" fill="url(#chamGrad)"/>
          <!-- belly highlight -->
          <ellipse id="chamBelly" cx="112" cy="138" rx="46" ry="20" fill="#ffffff" opacity="0.18"/>
          <!-- brillo (más fuerte en tonos metálicos) -->
          <ellipse id="chamShine" cx="145" cy="92" rx="28" ry="11" fill="#ffffff" opacity="0" transform="rotate(-18 145 92)"/>
          <!-- back ridge spikes -->
          <g id="chamSpikes" fill="#2f6f56">
            <polygon points="60,80 68,64 76,82"/>
            <polygon points="82,70 90,52 98,72"/>
            <polygon points="105,64 113,46 121,66"/>
            <polygon points="128,66 136,48 144,68"/>
            <polygon points="150,72 158,56 166,76"/>
          </g>
          <!-- head -->
          <circle id="chamHead" cx="180" cy="92" r="42" fill="url(#chamGrad)"/>
          <!-- front leg -->
          <path d="M108,152 Q102,170 86,174" fill="none" stroke="#2f6f56" stroke-width="10" stroke-linecap="round"/>
          <path d="M180,152 Q188,168 202,170" fill="none" stroke="#2f6f56" stroke-width="10" stroke-linecap="round"/>
          <!-- snout -->
          <ellipse id="chamSnout" cx="222" cy="100" rx="14" ry="10" fill="url(#chamGrad)"/>
          <!-- nostril -->
          <circle cx="230" cy="98" r="2" fill="#20321F" opacity="0.5"/>
          <!-- mouth -->
          <path d="M198,116 Q210,122 220,114" fill="none" stroke="#20321F" stroke-width="2.5" stroke-linecap="round" opacity="0.55"/>
          <!-- eye turret -->
          <circle id="chamEyeTurret" cx="192" cy="66" r="26" fill="url(#chamGrad)"/>
          <circle cx="192" cy="66" r="19" fill="#fdfdfd"/>
          <g id="pupilGroup">
            <circle cx="194" cy="66" r="10" fill="#20321F"/>
            <circle cx="197" cy="62" r="3.4" fill="#ffffff"/>
          </g>
          <g id="eyelid">
            <rect x="166" y="45" width="52" height="0" fill="#43AA8B"/>
          </g>
        </svg>
        <div id="tongue"></div>
      </div>
      <div id="branch"></div>

      <div id="prompt">¿De qué color es el camaleón?<br>Escríbelo en inglés en tu <b>cuaderno</b> ✏️</div>

      <div id="revealCard">
        <div id="revealEmoji"></div>
        <div id="revealWord"></div>
        <div id="revealEs"></div>
      </div>

      <div id="feedback"></div>
    </div>

    <div class="btn-row" id="preRevealBtns">
      <button class="btn" id="btnReveal">✍️ Ya escribí, ¡mostrar!</button>
    </div>
    <div class="btn-row" id="postRevealBtns" style="display:none;">
      <button class="btn" id="btnCorrect">✅ ¡Lo tenía bien!</button>
      <button class="btn secondary" id="btnWrong">❌ Me equivoqué</button>
    </div>
  </div>

  <!-- END SCREEN -->
  <div class="screen" id="screen-end">
    <h1>🎉 ¡Buen trabajo!</h1>
    <div id="stars"></div>
    <div class="subtitle" id="finalScoreText"></div>
    <div class="btn-row">
      <button class="btn" id="btnReplay">Jugar de nuevo</button>
    </div>
  </div>

  <!-- GAME OVER SCREEN -->
  <div class="screen" id="screen-gameover">
    <h1>⏰ ¡Se acabó el tiempo!</h1>
    <div class="notebook-hint" id="gameoverWord"></div>
    <div class="subtitle" id="gameoverScoreText"></div>
    <div class="btn-row">
      <button class="btn" id="btnRetry">Intentar de nuevo</button>
    </div>
  </div>
</div>

<script>
/* ============ AUDIO (Web Audio API — sin archivos externos) ============ */
let actx;
let musicOn = true;
let musicTimer = null;

function ensureAudio(){
  if(!actx){
    actx = new (window.AudioContext || window.webkitAudioContext)();
  }
  if(actx.state !== 'running'){
    actx.resume();
  }
  return actx;
}

// Red de seguridad: algunos navegadores en PC solo permiten iniciar audio
// tras CUALQUIER gesto del usuario (clic, tecla, toque), no solo el botón de inicio.
['pointerdown','keydown','touchstart'].forEach(evt=>{
  window.addEventListener(evt, ()=>{ ensureAudio(); }, {once:false});
});

function tone(freq, start, dur, type='sine', vol=0.22){
  ensureAudio();
  if(!actx) return;
  const osc = actx.createOscillator();
  const gain = actx.createGain();
  osc.type = type;
  osc.frequency.setValueAtTime(freq, actx.currentTime + start);
  gain.gain.setValueAtTime(0, actx.currentTime + start);
  gain.gain.linearRampToValueAtTime(vol, actx.currentTime + start + 0.02);
  gain.gain.exponentialRampToValueAtTime(0.001, actx.currentTime + start + dur);
  osc.connect(gain).connect(actx.destination);
  osc.start(actx.currentTime + start);
  osc.stop(actx.currentTime + start + dur + 0.05);
}

function playCorrect(){
  tone(523.25,0,0.15,'triangle',0.28); tone(659.25,0.1,0.15,'triangle',0.28); tone(783.99,0.2,0.25,'triangle',0.28);
}
function playWrong(){
  tone(220,0,0.2,'sawtooth',0.22); tone(180,0.12,0.25,'sawtooth',0.22);
}
function playClick(){
  tone(440,0,0.08,'square',0.16);
}
function playVictory(){
  const notes=[523.25,587.33,659.25,783.99,880,1046.5];
  notes.forEach((n,i)=> tone(n, i*0.13, 0.22,'triangle',0.26));
}

// Melodía original en escala pentatónica (do mayor), con línea de bajo simple.
// Progresión armónica sencilla: C – G – Am – C (I – V – vi – I)
const MELODY = [
  {n:783.99,d:0.26}, {n:659.25,d:0.26}, {n:523.25,d:0.26}, {n:659.25,d:0.26}, // G5 E5 C5 E5
  {n:783.99,d:0.26}, {n:880.00,d:0.26}, {n:783.99,d:0.26}, {n:659.25,d:0.26}, // G5 A5 G5 E5
  {n:523.25,d:0.26}, {n:587.33,d:0.26}, {n:659.25,d:0.26}, {n:587.33,d:0.26}, // C5 D5 E5 D5
  {n:523.25,d:0.26}, {n:440.00,d:0.26}, {n:523.25,d:0.26}, {n:0,   d:0.26}    // C5 A4 C5 (silencio)
];
const BASS = { 0:130.81, 4:196.00, 8:220.00, 12:130.81 }; // C3, G3, A3, C3
const STEP_MS = 280;
let stepIndex = 0;

function musicStep(){
  if(!musicOn || !actx) return;
  const step = MELODY[stepIndex % MELODY.length];
  if(step.n > 0) tone(step.n, 0, step.d + 0.08, 'triangle', 0.11);
  const bass = BASS[stepIndex % MELODY.length];
  if(bass) tone(bass, 0, 0.95, 'sine', 0.06);
  stepIndex++;
}
function startMusic(){
  if(musicTimer) return;
  stepIndex = 0;
  musicStep();
  musicTimer = setInterval(musicStep, STEP_MS);
}
function stopMusic(){
  if(musicTimer){ clearInterval(musicTimer); musicTimer=null; }
}

document.getElementById('soundToggle').addEventListener('click', ()=>{
  musicOn = !musicOn;
  document.getElementById('soundToggle').textContent = musicOn ? '🔊' : '🔇';
  if(musicOn){ ensureAudio(); startMusic(); } else { stopMusic(); }
});

/* ============ Blinking + wandering eye (decorativo) ============ */
setInterval(()=>{
  const lid = document.querySelector('#eyelid rect');
  if(!lid) return;
  lid.setAttribute('height','38');
  setTimeout(()=> lid.setAttribute('height','0'), 140);
}, 3200);

setInterval(()=>{
  const pg = document.getElementById('pupilGroup');
  if(!pg) return;
  const dx = (Math.random()-0.5)*6;
  const dy = (Math.random()-0.5)*6;
  pg.setAttribute('transform', `translate(${dx},${dy})`);
}, 2200);

/* ============ GAME DATA ============ */
// Colores agrupados por dificultad, con su traducción al español.
// Cada pool tiene más colores de los que se usan por partida, así que
// el conjunto y el orden cambian cada vez que juegas.
const EASY   = [
  {en:'red', es:'Rojo', hex:'#E63946', emoji:'🍓'},
  {en:'blue', es:'Azul', hex:'#2563EB', emoji:'🫐'},
  {en:'yellow', es:'Amarillo', hex:'#FDE047', emoji:'🍋'},
  {en:'green', es:'Verde', hex:'#16A34A', emoji:'🍏'},
  {en:'orange', es:'Naranja', hex:'#F3722C', emoji:'🍊'},
  {en:'purple', es:'Morado', hex:'#800080', emoji:'🍇'},
  {en:'pink', es:'Rosa', hex:'#F15BB5', emoji:'🌸'},
  {en:'brown', es:'Marrón o café', hex:'#8B5E34', emoji:'🍫'},
  {en:'black', es:'Negro', hex:'#0A0A0A', emoji:'🎩'},
  {en:'white', es:'Blanco', hex:'#F8F9FA', emoji:'☁️'},
  {en:'gray', es:'Gris', hex:'#5B6472', emoji:'🐘'},
];
const MEDIUM = [
  {en:'gold', es:'Dorado', hex:'#D4AF37', emoji:'🏆', metallic:true},
  {en:'silver', es:'Plateado', hex:'#C0C0C0', emoji:'🥈', metallic:true},
  {en:'bronze', es:'Bronceado', hex:'#7A3B1E', emoji:'🥉', metallic:true},
  {en:'copper', es:'Cobre', hex:'#DA8A55', emoji:'🪙', metallic:true},
];
const HARD   = [
  {en:'turquoise', es:'Turquesa', hex:'#2DD4BF', emoji:'🌊'},
  {en:'beige', es:'Beige', hex:'#E8DCC4', emoji:'🏖️'},
  {en:'navy blue', es:'Azul marino', hex:'#0B1F3F', emoji:'⚓'},
  {en:'sky blue', es:'Azul cielo', hex:'#7EC8E3', emoji:'🌤️'},
  {en:'lime green', es:'Verde limón', hex:'#A3E635', emoji:'🥝'},
  {en:'olive green', es:'Verde oliva', hex:'#708238', emoji:'🌿'},
  {en:'magenta', es:'Magenta', hex:'#D6249F', emoji:'🌺'},
  {en:'violet', es:'Violeta', hex:'#7F00FF', emoji:'💜'},
];

function shuffle(arr){
  const a = arr.slice();
  for(let i=a.length-1;i>0;i--){
    const j = Math.floor(Math.random()*(i+1));
    [a[i],a[j]] = [a[j],a[i]];
  }
  return a;
}

function shadeColor(hex, percent){
  let r = parseInt(hex.slice(1,3),16), g = parseInt(hex.slice(3,5),16), b = parseInt(hex.slice(5,7),16);
  const amt = Math.round(2.55 * percent);
  r = Math.max(0, Math.min(255, r + amt));
  g = Math.max(0, Math.min(255, g + amt));
  b = Math.max(0, Math.min(255, b + amt));
  return '#' + [r,g,b].map(v=>v.toString(16).padStart(2,'0')).join('');
}

let roundOrder = [];
let currentRound = 0;
let score = 0;
let streak = 0;
const TOTAL_ROUNDS = 12;
const ROUNDS_PER_LEVEL = 4; // 4 fáciles + 4 medios + 4 difíciles = 12
const BASE_SECONDS = 9;
const MIN_SECONDS = 3.5;
const DECAY = 0.5;
let timerInterval = null;
let roundLocked = false; // evita doble clic / múltiples respuestas en la misma ronda

function roundSeconds(index){
  return Math.max(MIN_SECONDS, BASE_SECONDS - index*DECAY);
}
function levelInfo(index){
  if(index < ROUNDS_PER_LEVEL) return {label:'🟢 Fácil', bar:'linear-gradient(90deg,#F4C542,#F3722C)'};
  if(index < ROUNDS_PER_LEVEL*2) return {label:'✨ Metálico', bar:'linear-gradient(90deg,#F3722C,#E63946)'};
  return {label:'🔥 Avanzado', bar:'linear-gradient(90deg,#E63946,#9B1D2E)'};
}

/* ============ SCREEN HELPERS ============ */
function showScreen(id){
  document.querySelectorAll('.screen').forEach(s=>s.classList.remove('active'));
  document.getElementById(id).classList.add('active');
}

/* ============ GAME LOGIC ============ */
function startGame(){
  ensureAudio();
  startMusic();
  roundOrder = [
    ...shuffle(EASY).slice(0, ROUNDS_PER_LEVEL),
    ...shuffle(MEDIUM).slice(0, ROUNDS_PER_LEVEL),
    ...shuffle(HARD).slice(0, ROUNDS_PER_LEVEL)
  ];
  currentRound = 0;
  score = 0;
  streak = 0;
  showScreen('screen-game');
  nextRound();
}

function setChameleonColor(hex, metallic){
  const dark = shadeColor(hex, -28);

  let s1, s2, s3;
  if(metallic){
    // bandas oscuras a los lados, color real en el centro = efecto metal pulido
    // (el brillo lo aporta el reflejo #chamShine, no aclarar todo el cuerpo)
    s1 = shadeColor(hex, -48);
    s2 = hex;
    s3 = shadeColor(hex, -26);
  } else {
    // sombreado suave tipo "plástico brillante"
    s1 = shadeColor(hex, 18);
    s2 = hex;
    s3 = shadeColor(hex, -22);
  }
  document.getElementById('gradStop1').setAttribute('stop-color', s1);
  document.getElementById('gradStop2').setAttribute('stop-color', s2);
  document.getElementById('gradStop3').setAttribute('stop-color', s3);
  document.getElementById('chamShine').setAttribute('opacity', metallic ? 0.4 : 0.12);

  document.getElementById('chamSpikes').setAttribute('fill', dark);
  document.querySelectorAll('#chamSvg path[stroke="#2f6f56"]').forEach(p=> p.setAttribute('stroke', dark));
  document.querySelector('#eyelid rect').setAttribute('fill', hex);
}

function nextRound(){
  if(currentRound >= TOTAL_ROUNDS){
    endGame();
    return;
  }
  roundLocked = false;
  document.getElementById('btnReveal').disabled = false;
  document.getElementById('btnCorrect').disabled = false;
  document.getElementById('btnWrong').disabled = false;
  const c = roundOrder[currentRound];
  const lvl = levelInfo(currentRound);
  document.getElementById('hudRound').textContent = `Ronda ${currentRound+1}/${TOTAL_ROUNDS}`;
  document.getElementById('hudScore').textContent = `⭐ ${score}`;
  document.getElementById('hudLevel').textContent = lvl.label;

  setChameleonColor(c.hex, !!c.metallic);

  document.getElementById('revealCard').style.display = 'none';
  document.getElementById('feedback').textContent = '';
  document.getElementById('preRevealBtns').style.display = 'flex';
  document.getElementById('postRevealBtns').style.display = 'none';
  document.getElementById('prompt').style.display = 'block';

  const secs = roundSeconds(currentRound);
  const bar = document.getElementById('timerBar');
  bar.style.background = lvl.bar;
  bar.style.transition = 'none';
  bar.style.width = '100%';
  void bar.offsetWidth; // fuerza al navegador a aplicar el 100% antes de animar
  bar.style.transition = `width ${secs}s linear`;
  bar.style.width = '0%';
  clearTimeout(timerInterval);
  timerInterval = setTimeout(()=> timeUp(c), secs*1000);
}

function timeUp(c){
  if(roundLocked) return;
  roundLocked = true;
  clearTimeout(timerInterval);
  document.getElementById('btnReveal').disabled = true;
  playWrong();
  document.getElementById('prompt').style.display = 'none';
  document.getElementById('preRevealBtns').style.display = 'none';
  document.getElementById('postRevealBtns').style.display = 'none';
  document.getElementById('revealEmoji').textContent = c.emoji;
  document.getElementById('revealWord').textContent = c.en;
  document.getElementById('revealEs').textContent = c.es;
  document.getElementById('revealCard').style.display = 'block';
  document.getElementById('feedback').textContent = '¡El tiempo se acabó! 😵';
  setTimeout(()=> showGameOver(c), 2200);
}

function showGameOver(c){
  stopMusic();
  document.getElementById('gameoverWord').innerHTML =
    `La respuesta era <b>${c.en.toUpperCase()}</b> ${c.emoji}<br>Llegaste hasta la ronda ${currentRound+1} de ${TOTAL_ROUNDS}.`;
  document.getElementById('gameoverScoreText').textContent = `⭐ Aciertos: ${score}`;
  showScreen('screen-gameover');
}

function reveal(c){
  if(roundLocked) return;
  clearTimeout(timerInterval);
  document.getElementById('btnReveal').disabled = true;
  const tongue = document.getElementById('tongue');
  tongue.classList.remove('tongue-shoot');
  void tongue.offsetWidth;
  tongue.classList.add('tongue-shoot');

  document.getElementById('revealEmoji').textContent = c.emoji;
  document.getElementById('revealWord').textContent = c.en;
  document.getElementById('revealEs').textContent = c.es;
  document.getElementById('revealCard').style.display = 'block';
  document.getElementById('prompt').style.display = 'none';
  document.getElementById('preRevealBtns').style.display = 'none';
  document.getElementById('postRevealBtns').style.display = 'flex';
  playClick();
}

function answer(isCorrect){
  if(roundLocked) return;
  roundLocked = true;
  document.getElementById('btnCorrect').disabled = true;
  document.getElementById('btnWrong').disabled = true;
  if(isCorrect){
    score++;
    streak++;
    playCorrect();
    const msgs = ['¡Excelente! 🎉','¡Muy bien! 🌟','¡Genial! 🙌','¡Lo lograste! 💪'];
    document.getElementById('feedback').textContent = msgs[Math.floor(Math.random()*msgs.length)] + (streak>=3 ? ` Racha x${streak}!` : '');
  } else {
    streak = 0;
    playWrong();
    document.getElementById('feedback').textContent = 'No pasa nada, ¡sigue practicando! 💪';
  }
  currentRound++;
  setTimeout(nextRound, 1100);
}

function endGame(){
  stopMusic();
  playVictory();
  showScreen('screen-end');
  const pct = score / TOTAL_ROUNDS;
  let starCount = pct >= 0.9 ? 3 : pct >= 0.6 ? 2 : pct >= 0.3 ? 1 : 0;
  document.getElementById('stars').textContent = '⭐'.repeat(starCount) + '☆'.repeat(3-starCount);
  document.getElementById('finalScoreText').textContent = `Acertaste ${score} de ${TOTAL_ROUNDS} colores.`;
}

/* ============ EVENTS ============ */
document.getElementById('btnStart').addEventListener('click', startGame);
document.getElementById('btnReveal').addEventListener('click', ()=>{
  const c = roundOrder[currentRound];
  reveal(c);
});
document.getElementById('btnCorrect').addEventListener('click', ()=> answer(true));
document.getElementById('btnWrong').addEventListener('click', ()=> answer(false));
document.getElementById('btnReplay').addEventListener('click', startGame);
document.getElementById('btnRetry').addEventListener('click', startGame);
</script>
</body>
</html>
