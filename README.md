<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1,user-scalable=no"/>
<title>⛏️ MiniCraft V5 RÉALISTE</title>
<style>
*{margin:0;padding:0;box-sizing:border-box;-webkit-tap-highlight-color:transparent;}
body{overflow:hidden;font-family:monospace;background:#000;touch-action:none;user-select:none;}
canvas{display:block;}

#crosshair{position:fixed;top:50%;left:50%;transform:translate(-50%,-50%);pointer-events:none;z-index:10;}
#crosshair::before,#crosshair::after{content:'';position:absolute;background:rgba(255,255,255,.8);}
#crosshair::before{width:20px;height:2px;left:-10px;top:-1px;}
#crosshair::after{width:2px;height:20px;left:-1px;top:-10px;}

#health,#hunger,#armor,#oxygen{position:fixed;left:50%;transform:translateX(-50%);display:flex;gap:1px;z-index:10;}
#health{bottom:144px;}#hunger{bottom:126px;}#armor{bottom:162px;}#oxygen{bottom:108px;}
#xp-bar{position:fixed;bottom:100px;left:50%;transform:translateX(-50%);width:182px;height:4px;background:#222;border:1px solid #333;z-index:10;}
#xp-fill{height:100%;background:#7dff00;width:0%;transition:width .3s;}
#xp-level{position:fixed;bottom:102px;left:50%;transform:translateX(-50%);color:#7dff00;font-size:10px;z-index:10;text-shadow:0 0 4px #000;}

#ui{position:fixed;bottom:52px;left:50%;transform:translateX(-50%);display:flex;gap:2px;z-index:10;}
.slot{width:44px;height:44px;background:rgba(60,60,60,.75);border:2px solid #222;border-radius:2px;display:flex;align-items:center;justify-content:center;font-size:15px;cursor:pointer;flex-direction:column;position:relative;}
.slot span{font-size:6px;color:#aaa;margin-top:1px;}
.slot.active{border-color:#ddd;background:rgba(100,100,100,.75);}
#hotbar-label{position:fixed;bottom:32px;left:50%;transform:translateX(-50%);color:rgba(255,255,255,.5);font-size:8px;z-index:10;}

#info{position:fixed;top:6px;left:6px;color:rgba(255,255,255,.8);font-size:10px;text-shadow:1px 1px 0 rgba(0,0,0,.8);z-index:10;line-height:1.7;background:rgba(0,0,0,.25);padding:5px 8px;border-radius:3px;}

#mine-bar{display:none;position:fixed;bottom:90px;left:50%;transform:translateX(-50%);width:120px;height:6px;background:#111;border:1px solid #555;z-index:10;}
#mine-fill{height:100%;background:#ff9900;width:0%;}

#weather-ui{position:fixed;top:6px;right:6px;color:#fff;font-size:16px;z-index:10;}
#fps-counter{position:fixed;top:6px;right:40px;color:#7dff00;font-size:9px;z-index:10;background:rgba(0,0,0,.5);padding:2px 5px;border-radius:2px;display:none;}
#toast{position:fixed;bottom:190px;left:50%;transform:translateX(-50%);background:rgba(0,0,0,.8);color:#fff;padding:6px 16px;border-radius:4px;font-size:12px;z-index:40;display:none;border:1px solid rgba(255,255,255,.2);}

#effects-bar{position:fixed;top:30px;right:6px;z-index:10;display:flex;flex-direction:column;gap:3px;}
.effect-icon{background:rgba(0,0,0,.6);border:1px solid rgba(255,255,255,.2);border-radius:3px;padding:2px 5px;font-size:9px;color:#adf;}

#boss-bar{display:none;position:fixed;top:14px;left:50%;transform:translateX(-50%);width:260px;z-index:15;}
#boss-bar p{color:#f66;text-align:center;font-size:10px;margin-bottom:2px;text-shadow:1px 1px 0 #000;}
#boss-bar-bg{background:#222;border-radius:2px;height:10px;border:1px solid #f44;}
#boss-bar-fill{background:linear-gradient(90deg,#f44,#f88);height:100%;border-radius:2px;transition:width .3s;}

#dim-label{position:fixed;top:50%;left:50%;transform:translate(-50%,-50%);font-size:32px;color:#fff;text-shadow:2px 2px 8px #000;z-index:12;display:none;animation:dimFade 3s forwards;pointer-events:none;}
@keyframes dimFade{0%{opacity:0;transform:translate(-50%,-50%) scale(.8);}15%{opacity:1;transform:translate(-50%,-50%) scale(1);}80%{opacity:1;}100%{opacity:0;}}

/* OVERLAY */
#overlay{position:fixed;inset:0;background:linear-gradient(180deg,rgba(0,0,0,.95),rgba(20,10,5,.95));display:flex;flex-direction:column;align-items:center;justify-content:center;gap:10px;z-index:20;overflow-y:auto;padding:16px;}
#overlay h1{color:#a0a0a0;font-size:36px;text-shadow:2px 2px 0 #000;letter-spacing:4px;}
#overlay h1 span{color:#5d3a1a;}
#overlay p{color:#888;font-size:11px;text-align:center;line-height:2;max-width:420px;}
#click-to-play{font-size:16px;background:rgba(80,80,80,.6);color:#ccc;border:2px solid #555;border-radius:3px;padding:10px 50px;cursor:pointer;font-family:monospace;letter-spacing:2px;transition:all .2s;}
#click-to-play:hover{background:rgba(100,100,100,.8);border-color:#888;color:#fff;}
.btn-row{display:flex;gap:8px;flex-wrap:wrap;justify-content:center;}
.mbtn{padding:7px 16px;font-size:12px;background:rgba(60,60,60,.6);color:#aaa;border:1px solid #555;border-radius:2px;cursor:pointer;font-family:monospace;transition:all .2s;}
.mbtn:hover{background:rgba(80,80,80,.8);color:#fff;}

/* MORT */
#death-screen{display:none;position:fixed;inset:0;background:rgba(120,0,0,.6);flex-direction:column;align-items:center;justify-content:center;gap:12px;z-index:25;}
#death-screen h1{color:#fff;font-size:40px;letter-spacing:3px;}
#death-screen p{color:#faa;font-size:12px;text-align:center;line-height:2;}

/* PANELS */
.panel{display:none;position:fixed;top:50%;left:50%;transform:translate(-50%,-50%);background:rgba(20,20,20,.96);border:2px solid #444;border-radius:4px;padding:16px;z-index:30;color:#ccc;min-width:300px;max-width:90vw;max-height:80vh;overflow-y:auto;}
.panel h2{text-align:center;margin-bottom:10px;font-size:16px;color:#aaa;letter-spacing:2px;}
.craft-row{display:flex;align-items:center;justify-content:space-between;padding:6px 8px;border:1px solid #333;border-radius:3px;margin-bottom:3px;cursor:pointer;transition:background .15s;font-size:12px;}
.craft-row:hover{background:rgba(255,255,255,.05);}
.craft-row .recipe{font-size:9px;color:#777;}
#inventory-grid{display:grid;grid-template-columns:repeat(9,38px);gap:2px;margin-bottom:8px;}
.inv-slot{width:38px;height:38px;background:rgba(60,60,60,.5);border:1px solid #444;border-radius:2px;display:flex;align-items:center;justify-content:center;font-size:14px;cursor:pointer;position:relative;}
.inv-slot:hover{background:rgba(100,100,100,.5);}
.inv-slot .inv-count{position:absolute;bottom:1px;right:2px;font-size:7px;color:#ddd;text-shadow:1px 1px 0 #000;}

/* OPTIONS */
#options-panel{min-width:340px;}
.opt-row{display:flex;align-items:center;justify-content:space-between;margin:8px 0;padding:5px 0;border-bottom:1px solid #222;}
.opt-row label{font-size:11px;color:#999;}
.opt-row input[type=range]{width:120px;cursor:pointer;accent-color:#7dff00;}
.opt-row select{background:#222;color:#ccc;border:1px solid #444;padding:3px 6px;border-radius:3px;font-family:monospace;font-size:11px;}

/* MOBILE CONTROLS */
#mobile-controls{display:none;position:fixed;z-index:15;}
#joystick-zone{position:fixed;bottom:30px;left:30px;width:110px;height:110px;background:rgba(255,255,255,.06);border:2px solid rgba(255,255,255,.15);border-radius:50%;z-index:15;}
#joystick-knob{width:44px;height:44px;background:rgba(255,255,255,.25);border-radius:50%;position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);}
.mobile-btn{position:fixed;z-index:15;width:50px;height:50px;background:rgba(255,255,255,.08);border:2px solid rgba(255,255,255,.15);border-radius:50%;display:none;align-items:center;justify-content:center;font-size:18px;color:rgba(255,255,255,.6);cursor:pointer;}
#btn-jump{bottom:30px;right:30px;}
#btn-break{bottom:95px;right:95px;}
#btn-place{bottom:95px;right:30px;}
#btn-inv{bottom:160px;right:30px;}
#btn-shoot{bottom:160px;right:95px;}

/* DEBUG CONSOLE */
#debug-console{display:none;position:fixed;bottom:0;left:0;right:0;z-index:50;background:rgba(0,0,0,.88);border-top:1px solid #333;padding:4px;}
#debug-console input{width:100%;background:transparent;border:none;color:#7dff00;font-family:monospace;font-size:12px;outline:none;}
#debug-log{max-height:100px;overflow-y:auto;font-size:9px;color:#7dff00;padding:2px 0;line-height:1.5;}
.debug-err{color:#f66!important;}.debug-ok{color:#6f6!important;}.debug-info{color:#6af!important;}

@media(max-width:768px){
  #mobile-controls{display:block!important;}
  #joystick-zone{display:block!important;}
  .mobile-btn{display:flex!important;}
  #hotbar-label{display:none!important;}
  .slot{width:36px;height:36px;font-size:12px;}
  .slot span{display:none;}
}
</style>
</head>
<body>

<div id="crosshair"></div>
<div id="mine-bar"><div id="mine-fill"></div></div>
<div id="armor"></div><div id="health"></div><div id="hunger"></div><div id="oxygen"></div>
<div id="xp-bar"><div id="xp-fill"></div></div>
<div id="xp-level"></div>
<div id="effects-bar"></div>
<div id="dim-label"></div>

<div id="ui">
  <div class="slot active">🟩<span>Herbe</span></div>
  <div class="slot">🟫<span>Terre</span></div>
  <div class="slot">⬜<span>Pierre</span></div>
  <div class="slot">🪵<span>Bois</span></div>
  <div class="slot">🔦<span>Torche</span></div>
  <div class="slot">💣<span>TNT</span></div>
  <div class="slot">🚪<span>Porte</span></div>
  <div class="slot">🪜<span>Escalier</span></div>
  <div class="slot">🟣<span>Portail</span></div>
</div>
<div id="hotbar-label">E=Inv C=Craft F=Four N=Enchant P=Potions O=Options /=Console</div>

<div id="info">
  ⛏️ <b>MiniCraft V5</b><br>
  📍 <span id="pos">—</span><br>
  🌅 <span id="time-info">—</span><br>
  🧟 <span id="mob-count">0</span><br>
  💎 <span id="inv-info">—</span><br>
  🌧️ <span id="weather-info">—</span><br>
  🌍 <span id="dim-info">Overworld</span><br>
  📦 <span id="chunk-info">—</span>
</div>

<div id="fps-counter">60 FPS</div>
<div id="weather-ui"><span id="weather-icon">☀️</span></div>

<div id="boss-bar">
  <p id="boss-label">💀 BOSS</p>
  <div id="boss-bar-bg"><div id="boss-bar-fill" style="width:100%"></div></div>
</div>

<!-- MOBILE -->
<div id="mobile-controls">
  <div id="joystick-zone"><div id="joystick-knob"></div></div>
  <div class="mobile-btn" id="btn-jump">⬆</div>
  <div class="mobile-btn" id="btn-break">⛏</div>
  <div class="mobile-btn" id="btn-place">🧱</div>
  <div class="mobile-btn" id="btn-inv">🎒</div>
  <div class="mobile-btn" id="btn-shoot">🏹</div>
</div>

<!-- OVERLAY -->
<div id="overlay">
  <h1><span>⛏️</span> MINICRAFT <span>V5</span></h1>
  <p>
    🌍 3 Dimensions — 🐉 Ender Dragon — 🏘️ Villages PNJ<br>
    ✨ Enchantements — 🧪 Potions — 🔴 Redstone<br>
    🧱 35+ Blocs — 🧟 8 Mobs — 📱 Mobile — ⚙️ Options
  </p>
  <button id="click-to-play">▶ JOUER</button>
  <div class="btn-row">
    <button class="mbtn" id="loadBtn">📂 Charger</button>
    <button class="mbtn" onclick="openPanel('options-panel')">⚙️ Options</button>
  </div>
</div>

<!-- MORT -->
<div id="death-screen">
  <h1>Vous êtes mort !</h1>
  <p>Score: <span id="death-xp">0</span> XP — <span id="death-blocks">0</span> blocs — <span id="death-mobs">0</span> mobs</p>
  <button class="mbtn" id="respawnBtn" style="font-size:14px;padding:10px 30px;">Réapparaître</button>
</div>

<!-- PANELS -->
<div class="panel" id="inventory-panel"><h2>INVENTAIRE</h2><div id="inventory-grid"></div><button class="mbtn" onclick="closePanel('inventory-panel')" style="width:100%">Fermer</button></div>
<div class="panel" id="crafting-panel"><h2>CRAFTING</h2><div id="craft-list"></div><button class="mbtn" onclick="closePanel('crafting-panel')" style="width:100%;margin-top:6px">Fermer</button></div>
<div class="panel" id="furnace-panel"><h2>FOUR</h2><div id="furnace-list"></div><div id="furnace-status" style="text-align:center;margin:4px 0;color:#f90;font-size:10px;"></div><button class="mbtn" onclick="closePanel('furnace-panel')" style="width:100%;margin-top:6px">Fermer</button></div>
<div class="panel" id="enchant-panel"><h2>ENCHANTEMENTS</h2><div id="enchant-list"></div><button class="mbtn" onclick="closePanel('enchant-panel')" style="width:100%;margin-top:6px">Fermer</button></div>
<div class="panel" id="potion-panel"><h2>POTIONS</h2><div id="potion-list"></div><button class="mbtn" onclick="closePanel('potion-panel')" style="width:100%;margin-top:6px">Fermer</button></div>
<div class="panel" id="trade-panel"><h2>COMMERCE</h2><div id="trade-list"></div><button class="mbtn" onclick="closePanel('trade-panel')" style="width:100%;margin-top:6px">Fermer</button></div>
<div class="panel" id="stats-panel"><h2>STATISTIQUES</h2><div id="stats-content" style="line-height:2;font-size:11px;"></div><button class="mbtn" onclick="closePanel('stats-panel')" style="width:100%;margin-top:6px">Fermer</button></div>

<!-- OPTIONS -->
<div class="panel" id="options-panel" style="min-width:340px;">
  <h2>⚙️ OPTIONS</h2>
  <div class="opt-row"><label>🖱️ Sensibilité</label><input type="range" id="opt-sens" min="1" max="20" value="10"/></div>
  <div class="opt-row"><label>👁️ FOV</label><input type="range" id="opt-fov" min="50" max="120" value="75"/></div>
  <div class="opt-row"><label>🌫️ Distance rendu</label><input type="range" id="opt-render" min="20" max="120" value="75"/></div>
  <div class="opt-row"><label>🔊 Volume</label><input type="range" id="opt-volume" min="0" max="100" value="40"/></div>
  <div class="opt-row"><label>🎨 Qualité</label><select id="opt-quality"><option value="low">Basse</option><option value="medium" selected>Moyenne</option><option value="high">Haute</option></select></div>
  <div class="opt-row"><label>📊 FPS</label><select id="opt-fps"><option value="off">Non</option><option value="on">Oui</option></select></div>
  <div class="opt-row"><label>🗺️ Minimap</label><select id="opt-minimap"><option value="off">Non</option><option value="on">Oui</option></select></div>
  <button class="mbtn" onclick="applyOptions();closePanel('options-panel')" style="width:100%;margin-top:10px">✅ Appliquer</button>
</div>

<div id="toast"></div>

<!-- DEBUG -->
<div id="debug-console">
  <div id="debug-log"></div>
  <input id="debug-input" type="text" placeholder="/help" autocomplete="off" spellcheck="false"/>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r134/three.min.js"></script>
<script>
'use strict';

// ═══════════ CONFIG ═══════════
const CFG={sens:.002,fov:75,renderDist:75,vol:.4,quality:'medium',showFps:false,showMinimap:false,gameMode:'survival'};
function applyOptions(){
  CFG.sens=+document.getElementById('opt-sens').value/5000;
  CFG.fov=+document.getElementById('opt-fov').value;
  CFG.renderDist=+document.getElementById('opt-render').value;
  CFG.vol=+document.getElementById('opt-volume').value/100;
  CFG.quality=document.getElementById('opt-quality').value;
  CFG.showFps=document.getElementById('opt-fps').value==='on';
  camera.fov=CFG.fov;camera.updateProjectionMatrix();
  scene.fog.far=CFG.renderDist;
  document.getElementById('fps-counter').style.display=CFG.showFps?'block':'none';
  if(CFG.quality==='low'){renderer.setPixelRatio(.7);renderer.shadowMap.enabled=false;}
  else if(CFG.quality==='high'){renderer.setPixelRatio(devicePixelRatio);renderer.shadowMap.enabled=true;renderer.shadowMap.type=THREE.PCFSoftShadowMap;}
  else{renderer.setPixelRatio(1);renderer.shadowMap.enabled=true;}
  renderer.setSize(innerWidth,innerHeight);
  localStorage.setItem('mc5_cfg',JSON.stringify(CFG));
  showToast('⚙️ Options appliquées');
}
function loadCfg(){try{const c=JSON.parse(localStorage.getItem('mc5_cfg'));if(c)Object.assign(CFG,c);}catch(e){}}

// ═══════════ AUDIO ═══════════
let AC;function initAudio(){try{AC=new(window.AudioContext||window.webkitAudioContext)();}catch(e){}}
function tone(f,t='square',d=.1,v=.1){if(!AC)return;const V=v*CFG.vol;if(V<.001)return;try{const o=AC.createOscillator(),g=AC.createGain();o.connect(g);g.connect(AC.destination);o.type=t;o.frequency.value=f;g.gain.setValueAtTime(V,AC.currentTime);g.gain.exponentialRampToValueAtTime(.001,AC.currentTime+d);o.start();o.stop(AC.currentTime+d);}catch(e){}}
const S={brk:()=>tone(110,'sawtooth',.12,.18),place:()=>tone(260,'square',.07,.12),jump:()=>tone(360,'sine',.1,.1),hurt:()=>tone(80,'sawtooth',.2,.25),heal:()=>tone(620,'sine',.15,.18),mob:()=>tone(100,'sawtooth',.25,.2),craft:()=>tone(420,'square',.12,.14),death:()=>tone(55,'sawtooth',1.2,.35),mine:()=>tone(180,'triangle',.08,.08),tnt:()=>{tone(60,'sawtooth',.4,.5);setTimeout(()=>tone(40,'sawtooth',.3,.4),100);},arrow:()=>tone(500,'sine',.06,.1),night:()=>tone(200,'sine',.4,.4),step:()=>tone(80,'triangle',.04,.04),animal:()=>tone(400,'sine',.08,.12),portal:()=>{[200,300,400,500,600].forEach((f,i)=>setTimeout(()=>tone(f,'sine',.2,.15),i*80));},dragon:()=>{tone(60,'sawtooth',.6,.4);setTimeout(()=>tone(80,'sawtooth',.4,.3),200);},enchant:()=>{[500,600,700,800].forEach((f,i)=>setTimeout(()=>tone(f,'sine',.15,.12),i*80));},potion:()=>tone(350,'sine',.2,.15),trade:()=>tone(450,'square',.1,.1),redstone:()=>tone(300,'square',.05,.08),lvl:()=>[300,400,500,600].forEach((f,i)=>setTimeout(()=>tone(f,'sine',.15,.2),i*100)),save:()=>{tone(500,'sine',.2,.1);setTimeout(()=>tone(700,'sine',.2,.1),150);},door:()=>tone(150,'square',.15,.15),splash:()=>tone(200,'sine',.15,.1),fall:()=>tone(90,'sawtooth',.15,.2)};

// ═══════════ PERLIN ═══════════
const pm=[];for(let i=0;i<256;i++)pm[i]=i;for(let i=255;i>0;i--){const j=Math.floor(Math.random()*(i+1));[pm[i],pm[j]]=[pm[j],pm[i]];}const PP=[...pm,...pm];
const fade=t=>t*t*t*(t*(t*6-15)+10),lerp=(a,b,t)=>a+t*(b-a),grad=(h,x,y)=>{const u=h<2?x:y,v=h<2?y:x;return((h&1)?-u:u)+((h&2)?-v:v);};
function n2(x,y){const X=Math.floor(x)&255,Y=Math.floor(y)&255;x-=Math.floor(x);y-=Math.floor(y);const u=fade(x),v=fade(y),a=PP[X]+Y,b=PP[X+1]+Y;return lerp(lerp(grad(PP[a],x,y),grad(PP[b],x-1,y),u),lerp(grad(PP[a+1],x,y-1),grad(PP[b+1],x-1,y-1),u),v);}
function oct(x,y,o=5,p=.5,l=2){let v=0,a=1,f=1,m=0;for(let i=0;i<o;i++){v+=n2(x*f,y*f)*a;m+=a;a*=p;f*=l;}return v/m;}

// ═══════════ TEXTURES RÉALISTES ═══════════
function mkT(fn,s=16){const c=document.createElement('canvas');c.width=c.height=s;fn(c.getContext('2d'),s);const t=new THREE.CanvasTexture(c);t.magFilter=THREE.NearestFilter;t.minFilter=THREE.NearestFilter;return t;}
function px(c,x,y,col){c.fillStyle=col;c.fillRect(x,y,1,1);}
function rndC(base,vary=20){const r=parseInt(base.slice(1,3),16),g=parseInt(base.slice(3,5),16),b=parseInt(base.slice(5,7),16);const v=()=>Math.floor(Math.random()*vary*2-vary);return`rgb(${Math.max(0,Math.min(255,r+v()))},${Math.max(0,Math.min(255,g+v()))},${Math.max(0,Math.min(255,b+v()))})`;};

const TEX={};
function buildTextures(){
  const S=16;
  // Herbe top - pixel art style
  TEX.grass_top=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#5a9b2f',15));},{},S);
  TEX.grass_side=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++){if(y<4)px(c,x,y,rndC('#5a9b2f',12));else if(y===4)px(c,x,y,rndC('#6b8c42',10));else px(c,x,y,rndC('#8b6b43',10));}},S);
  TEX.dirt=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#8b6b43',12));},S);
  TEX.stone=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#7d7d7d',18));for(let i=0;i<8;i++){const cx=Math.random()*s|0,cy=Math.random()*s|0;for(let dx=0;dx<3;dx++)for(let dy=0;dy<2;dy++)if(cx+dx<s&&cy+dy<s)px(c,cx+dx,cy+dy,rndC('#666666',10));}},S);
  TEX.wood_side=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++){const stripe=(y%4<2)?'#6b5024':'#8b6b34';px(c,x,y,rndC(stripe,8));}},S);
  TEX.wood_top=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++){const d=Math.sqrt((x-8)**2+(y-8)**2);px(c,x,y,rndC(d%3<2?'#8b6b34':'#6b5024',8));}},S);
  TEX.sand=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#dbc67b',12));},S);
  TEX.snow=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#eef4f8',6));},S);
  TEX.water=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#2864a8',15));},S);
  TEX.coal_ore=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#7d7d7d',15));for(let i=0;i<5;i++){const cx=2+Math.random()*(s-4)|0,cy=2+Math.random()*(s-4)|0;for(let dx=0;dx<3;dx++)for(let dy=0;dy<2;dy++)if(cx+dx<s&&cy+dy<s)px(c,cx+dx,cy+dy,rndC('#1a1a1a',5));}},S);
  TEX.iron_ore=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#7d7d7d',15));for(let i=0;i<4;i++){const cx=2+Math.random()*(s-4)|0,cy=2+Math.random()*(s-4)|0;for(let dx=0;dx<3;dx++)for(let dy=0;dy<2;dy++)if(cx+dx<s&&cy+dy<s)px(c,cx+dx,cy+dy,rndC('#d4a574',8));}},S);
  TEX.gold_ore=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#6d6d6d',15));for(let i=0;i<4;i++){const cx=2+Math.random()*(s-4)|0,cy=2+Math.random()*(s-4)|0;for(let dx=0;dx<3;dx++)for(let dy=0;dy<2;dy++)if(cx+dx<s&&cy+dy<s)px(c,cx+dx,cy+dy,rndC('#fcdb4a',8));}},S);
  TEX.diamond_ore=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#6d6d6d',15));for(let i=0;i<4;i++){const cx=2+Math.random()*(s-4)|0,cy=2+Math.random()*(s-4)|0;for(let dx=0;dx<3;dx++)for(let dy=0;dy<2;dy++)if(cx+dx<s&&cy+dy<s)px(c,cx+dx,cy+dy,rndC('#5ae4d4',8));}},S);
  TEX.emerald_ore=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#6d6d6d',15));for(let i=0;i<3;i++){const cx=3+Math.random()*(s-6)|0,cy=3+Math.random()*(s-6)|0;for(let dx=0;dx<2;dx++)for(let dy=0;dy<3;dy++)if(cx+dx<s&&cy+dy<s)px(c,cx+dx,cy+dy,rndC('#2dcc70',10));}},S);
  TEX.leaves=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC(Math.random()>.3?'#3a7a2a':'#2a5a1a',15));},S);
  TEX.planks=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++){const stripe=(x%4===0)?'#8a6830':'#b08b50';px(c,x,y,rndC(stripe,8));}},S);
  TEX.stone_brick=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++){const border=(x%8===0||y%8===0);px(c,x,y,rndC(border?'#666':'#8a8a8a',10));}},S);
  TEX.glass=mkT((c,s)=>{c.fillStyle='rgba(180,220,255,.15)';c.fillRect(0,0,s,s);c.strokeStyle='rgba(200,230,255,.4)';c.lineWidth=1;c.strokeRect(1,1,s-2,s-2);},S);
  TEX.bedrock=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#2a2a2a',20));},S);
  TEX.gravel=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#8a8580',18));},S);
  TEX.torch=mkT((c,s)=>{c.fillStyle='#422';c.fillRect(0,0,s,s);c.fillStyle='#8b6914';c.fillRect(6,4,4,10);c.fillStyle='#ff9900';c.fillRect(6,2,4,4);c.fillStyle='#ffdd00';c.fillRect(7,3,2,2);},S);
  TEX.tnt=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++){if(y<4||y>=12)px(c,x,y,rndC('#6b4b2a',8));else px(c,x,y,rndC('#cc3333',10));}c.fillStyle='#fff';c.font='bold 6px monospace';c.fillText('TNT',2,10);},S);
  TEX.obsidian=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#1a0a28',12));},S);
  TEX.netherrack=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#6b2020',15));},S);
  TEX.soul_sand=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#4a3828',12));},S);
  TEX.nether_brick=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++){const b=(x%8===0||y%4===0);px(c,x,y,rndC(b?'#1a0a0a':'#3c1414',8));}},S);
  TEX.glowstone=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#d4aa44',20));},S);
  TEX.lava=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#cc4400',25));},S);
  TEX.nether_portal=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#7b1fa2',20));},S);
  TEX.end_stone=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#d4cc94',10));},S);
  TEX.end_portal=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++)px(c,x,y,rndC('#0a0a2e',8));},S);
  TEX.redstone_wire=mkT((c,s)=>{c.fillStyle='#333';c.fillRect(0,0,s,s);c.fillStyle='#c00';c.fillRect(6,0,4,s);c.fillRect(0,6,s,4);},S);
  TEX.lever=mkT((c,s)=>{c.fillStyle='#555';c.fillRect(0,0,s,s);c.fillStyle='#8b6914';c.fillRect(6,3,4,8);c.fillStyle='#aaa';c.fillRect(6,2,4,3);},S);
  TEX.door=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++){const b=(x===0||x===s-1||y===0||y===s-1);px(c,x,y,rndC(b?'#5a4020':'#8b6914',6));}c.fillStyle='#d4aa44';c.fillRect(11,7,3,3);},S);
  TEX.chest=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++){const b=(x===0||x===s-1||y===0||y===s-1);px(c,x,y,rndC(b?'#5a4020':'#8b6914',6));}c.fillStyle='#333';c.fillRect(6,6,4,4);c.fillStyle='#d4aa44';c.fillRect(7,7,2,2);},S);
  TEX.enchant_table=mkT((c,s)=>{for(let x=0;x<s;x++)for(let y=0;y<s;y++){if(y<6)px(c,x,y,rndC('#1a1a6b',10));else px(c,x,y,rndC('#6b2020',10));}c.fillStyle='#d4aa44';c.fillRect(5,2,6,3);},S);
  TEX.stair=TEX.planks;TEX.slab=TEX.stone;
  TEX.wheat=mkT((c,s)=>{c.fillStyle='#5a8b2f';c.fillRect(0,0,s,s);for(let i=0;i<6;i++){c.fillStyle='#d4aa44';c.fillRect(2+i*2,2,1,12);}},S);
  TEX.carrot=mkT((c,s)=>{c.fillStyle='#5a8b2f';c.fillRect(0,0,s,s);for(let i=0;i<5;i++){c.fillStyle='#e67030';c.fillRect(2+i*3,4,2,10);}},S);
  TEX.potato=mkT((c,s)=>{c.fillStyle='#5a8b2f';c.fillRect(0,0,s,s);for(let i=0;i<5;i++){c.fillStyle='#c4a060';c.fillRect(2+i*3,4,2,10);}},S);
}

// ═══════════ THREE.JS ═══════════
const scene=new THREE.Scene();scene.background=new THREE.Color(0x7ec8e3);scene.fog=new THREE.FogExp2(0x7ec8e3,.008);
const camera=new THREE.PerspectiveCamera(75,innerWidth/innerHeight,.1,200);camera.position.set(0,22,0);
const renderer=new THREE.WebGLRenderer({antialias:true,powerPreference:'high-performance'});renderer.setSize(innerWidth,innerHeight);renderer.shadowMap.enabled=true;renderer.shadowMap.type=THREE.PCFSoftShadowMap;renderer.outputEncoding=THREE.sRGBEncoding;document.body.appendChild(renderer.domElement);
window.addEventListener('resize',()=>{camera.aspect=innerWidth/innerHeight;camera.updateProjectionMatrix();renderer.setSize(innerWidth,innerHeight);});
const sunLight=new THREE.DirectionalLight(0xffeedd,1.3);sunLight.position.set(60,120,60);sunLight.castShadow=true;sunLight.shadow.mapSize.set(1024,1024);sunLight.shadow.camera.left=-30;sunLight.shadow.camera.right=30;sunLight.shadow.camera.top=30;sunLight.shadow.camera.bottom=-30;scene.add(sunLight);
const ambLight=new THREE.AmbientLight(0x404060,.6);scene.add(ambLight);
const moonLight=new THREE.DirectionalLight(0x334477,.2);moonLight.position.set(-60,80,-60);scene.add(moonLight);
// Hemisphere light for more realism
const hemiLight=new THREE.HemisphereLight(0x87ceeb,0x362a1a,.3);scene.add(hemiLight);

// ═══════════ BLOCS ═══════════
const ALL_B=['grass','dirt','stone','wood','sand','snow','torch','water','coal_ore','iron_ore','gold_ore','diamond_ore','emerald_ore','leaves','gravel','bedrock','tnt','obsidian','planks','stone_brick','glass','chest','netherrack','soul_sand','nether_brick','glowstone','lava','nether_portal','end_stone','end_portal','redstone_wire','lever','door','stair','slab','enchant_table','wheat','carrot','potato'];
const mats={};
function buildMats(){
  buildTextures();
  const multiF={
    grass:[TEX.grass_side,TEX.grass_side,TEX.grass_top,TEX.dirt,TEX.grass_side,TEX.grass_side],
    wood:[TEX.wood_side,TEX.wood_side,TEX.wood_top,TEX.wood_top,TEX.wood_side,TEX.wood_side]
  };
  ALL_B.forEach(b=>{
    if(multiF[b]){mats[b]=multiF[b].map(t=>new THREE.MeshLambertMaterial({map:t}));}
    else if(TEX[b]){
      const o={map:TEX[b]};
      if(b==='water'||b==='lava'){o.transparent=true;o.opacity=.7;}
      if(b==='glass'||b==='nether_portal'||b==='end_portal'){o.transparent=true;o.opacity=.5;}
      if(b==='torch'||b==='glowstone'){o.emissive=new THREE.Color(b==='glowstone'?0xddaa44:0xff8800);o.emissiveIntensity=.5;}
      if(b==='lava'){o.emissive=new THREE.Color(0xff4400);o.emissiveIntensity=.7;}
      if(b==='nether_portal'){o.emissive=new THREE.Color(0x9c27b0);o.emissiveIntensity=.6;}
      if(b==='end_portal'){o.emissive=new THREE.Color(0x1a237e);o.emissiveIntensity=.8;}
      if(b==='redstone_wire'){o.emissive=new THREE.Color(0xcc0000);o.emissiveIntensity=.4;}
      mats[b]=new THREE.MeshLambertMaterial(o);
    }
  });
}

const geoBox=new THREE.BoxGeometry(1,1,1);const geoSlab=new THREE.BoxGeometry(1,.5,1);const geoDoor=new THREE.BoxGeometry(.2,2,1);
function getGeo(t){if(t==='slab')return geoSlab;if(t==='door')return geoDoor;return geoBox;}

// ═══════════ MONDE + DIMS ═══════════
let dim='overworld';
const W={overworld:{},nether:{},end:{}},M={overworld:{},nether:{},end:{}},TL={overworld:{},nether:{},end:{}};
const CHUNK=16,loadedC={overworld:new Set(),nether:new Set(),end:new Set()};
function wk(x,y,z){return`${x},${y},${z}`;}
function gW(){return W[dim];}function gM(){return M[dim];}function gT(){return TL[dim];}
function getB(x,y,z){return gW()[wk(x,y,z)]||'air';}
function setB(x,y,z,t){
  const k=wk(x,y,z),ms=gM(),tl=gT();
  gW()[k]=t;
  if(ms[k]){scene.remove(ms[k]);delete ms[k];}
  if(tl[k]){scene.remove(tl[k]);delete tl[k];}
  if(t==='air'||!mats[t])return;
  const g=getGeo(t);
  const m=new THREE.Mesh(g,Array.isArray(mats[t])?mats[t]:mats[t]);
  m.position.set(x,y+(t==='slab'?-.25:0),z);m.castShadow=true;m.receiveShadow=true;
  if(t==='door')m.userData.open=false;
  scene.add(m);ms[k]=m;
  if(['torch','glowstone','lava'].includes(t)){
    const pl=new THREE.PointLight(t==='lava'?0xff4400:t==='glowstone'?0xddaa44:0xff8800,t==='glowstone'?1.8:1.2,t==='glowstone'?14:10);
    pl.position.set(x,y+.5,z);scene.add(pl);tl[k]=pl;
  }
}

// CHUNKS
function ck(cx,cz){return`${cx},${cz}`;}
function gCC(x,z){return{cx:Math.floor(x/CHUNK),cz:Math.floor(z/CHUNK)};}
const LR=3,UR=5;

function genChunk(cx,cz){
  const cs=loadedC[dim],key=ck(cx,cz);if(cs.has(key))return;cs.add(key);
  const sx=cx*CHUNK,sz=cz*CHUNK;
  if(dim==='overworld')genOW(sx,sz);
  else if(dim==='nether')genNE(sx,sz);
  else genEN(sx,sz);
}
function genOW(sx,sz){
  for(let x=sx;x<sx+CHUNK;x++)for(let z=sz;z<sz+CHUNK;z++){
    const n=oct(x*.04,z*.04),h=Math.floor(n*14)+5;
    setB(x,0,z,'bedrock');
    for(let y=1;y<=h;y++){
      const cv=n2(x*.1,z*.1)*n2(y*.15,x*.07);
      if(cv>.28&&y>2&&y<h-1)continue;
      let t;if(y===h){if(h>=15)t='snow';else if(h<=5)t='sand';else t='grass';}
      else if(y>=h-2)t='dirt';else if(y<3)t='gravel';else t='stone';
      setB(x,y,z,t);
      if(t==='stone'&&y<h-2){const r=Math.random();if(y<5&&r<.008)setB(x,y,z,'diamond_ore');else if(y<5&&r<.013)setB(x,y,z,'emerald_ore');else if(y<8&&r<.022)setB(x,y,z,'gold_ore');else if(y<12&&r<.042)setB(x,y,z,'iron_ore');else if(r<.065)setB(x,y,z,'coal_ore');}
    }
    if(h<=4)for(let y=h+1;y<=4;y++)setB(x,y,z,'water');
  }
  for(let i=0;i<3;i++){const tx=sx+(Math.random()*CHUNK|0),tz=sz+(Math.random()*CHUNK|0);const n=oct(tx*.04,tz*.04),th=Math.floor(n*14)+5;if(th>5&&th<15)mkTree(tx,th,tz);}
  if(Math.random()<.12)mkVillage(sx+8,sz+8);
}
function genNE(sx,sz){for(let x=sx;x<sx+CHUNK;x++)for(let z=sz;z<sz+CHUNK;z++){const n=oct(x*.06,z*.06),h=Math.floor(n*8)+4;setB(x,0,z,'bedrock');for(let y=1;y<=h;y++){const cv=n2(x*.12,z*.12)*n2(y*.2,x*.08);if(cv>.22&&y>2&&y<h-1)continue;setB(x,y,z,Math.random()<.1?'soul_sand':'netherrack');}if(h<=3)for(let y=h+1;y<=3;y++)setB(x,y,z,'lava');for(let y=18;y<=20;y++)setB(x,y,z,'netherrack');setB(x,20,z,'bedrock');if(Math.random()<.02)setB(x,18,z,'glowstone');}}
function genEN(sx,sz){for(let x=sx;x<sx+CHUNK;x++)for(let z=sz;z<sz+CHUNK;z++){const d=Math.sqrt(x*x+z*z);if(d>40)continue;const n=oct(x*.06,z*.06),h=Math.max(1,Math.floor(n*6)+3-Math.floor(d/10));for(let y=0;y<=h;y++)setB(x,y,z,'end_stone');}if(sx===0&&sz===0)for(let i=0;i<8;i++){const a=i*Math.PI*.25,px=Math.round(Math.cos(a)*12),pz=Math.round(Math.sin(a)*12),ph=8+(Math.random()*8|0);for(let y=0;y<ph;y++)setB(px,y,pz,'obsidian');setB(px,ph,pz,'torch');}}
function unloadC(cx,cz){const cs=loadedC[dim],key=ck(cx,cz);if(!cs.has(key))return;const sx=cx*CHUNK,sz=cz*CHUNK,ms=gM(),tl=gT(),w=gW();for(let x=sx;x<sx+CHUNK;x++)for(let z=sz;z<sz+CHUNK;z++)for(let y=0;y<25;y++){const k=wk(x,y,z);if(ms[k]){scene.remove(ms[k]);delete ms[k];}if(tl[k]){scene.remove(tl[k]);delete tl[k];}delete w[k];}cs.delete(key);}
function updateChunks(){const{cx,cz}=gCC(Math.round(camera.position.x),Math.round(camera.position.z));for(let dx=-LR;dx<=LR;dx++)for(let dz=-LR;dz<=LR;dz++)genChunk(cx+dx,cz+dz);loadedC[dim].forEach(key=>{const[x,z]=key.split(',').map(Number);if(Math.abs(x-cx)>UR||Math.abs(z-cz)>UR)unloadC(x,z);});document.getElementById('chunk-info').textContent=`Chunks:${loadedC[dim].size}`;}

function mkTree(x,by,z){const h=4+(Math.random()*3|0);for(let i=1;i<=h;i++)setB(x,by+i,z,'wood');for(let dx=-2;dx<=2;dx++)for(let dz=-2;dz<=2;dz++)for(let dy=0;dy<=2;dy++)if(Math.abs(dx)+Math.abs(dz)+Math.abs(dy)<5&&getB(x+dx,by+h+dy,z+dz)==='air')setB(x+dx,by+h+dy,z+dz,'leaves');}

// Villages + NPCs
const npcs=[];
function mkVillage(cx,cz){const n=oct(cx*.04,cz*.04),by=Math.floor(n*14)+5;if(by<=5||by>=14)return;for(let h=0;h<3;h++){const hx=cx+(h-1)*8,hz=cz+(Math.random()*6-3|0);for(let dx=0;dx<5;dx++)for(let dz=0;dz<5;dz++){setB(hx+dx,by+1,hz+dz,'planks');for(let dy=2;dy<=4;dy++)if(dx===0||dx===4||dz===0||dz===4)setB(hx+dx,by+dy,hz+dz,'planks');setB(hx+dx,by+5,hz+dz,'planks');}setB(hx+2,by+2,hz,'air');setB(hx+2,by+3,hz,'air');setB(hx,by+3,hz+2,'glass');setB(hx+4,by+3,hz+2,'glass');spawnNPC(hx+2,by+2,hz+2);}}
function spawnNPC(x,y,z){const g=new THREE.BoxGeometry(.6,1.8,.6);const m=new THREE.MeshLambertMaterial({color:[0xc8a165,0x8b6914,0x5d4037,0xd4a574][Math.random()*4|0]});const mesh=new THREE.Mesh(g,m);mesh.position.set(x,y+.9,z);mesh.castShadow=true;const head=new THREE.Mesh(new THREE.BoxGeometry(.5,.5,.5),new THREE.MeshLambertMaterial({color:0xe8b87c}));head.position.y=1.15;mesh.add(head);scene.add(mesh);
  const trades=[];const possible=[{g:'emerald',ga:1,r:'diamond',ra:1},{g:'iron_ingot',ga:5,r:'emerald',ra:1},{g:'emerald',ga:3,r:'iron_ingot',ra:8},{g:'emerald',ga:2,r:'cooked_beef',ra:6},{g:'coal_ore',ga:10,r:'emerald',ra:1}];
  for(let i=0;i<2+(Math.random()*2|0);i++)trades.push(possible[Math.random()*possible.length|0]);
  npcs.push({mesh,trades,timer:0,dir:Math.random()*Math.PI*2,hx:x,hz:z});}
let curTrader=null;
function openTrade(npc){curTrader=npc;const l=document.getElementById('trade-list');l.innerHTML='';npc.trades.forEach((t,i)=>{const r=document.createElement('div');r.className='craft-row';const can=(inv[t.g]||0)>=t.ga;r.innerHTML=`<span>${EM[t.g]||'?'}×${t.ga} → ${EM[t.r]||'?'}×${t.ra}</span><span class="recipe">${inv[t.g]||0}</span>`;r.style.opacity=can?1:.4;if(can)r.onclick=()=>{if((inv[t.g]||0)>=t.ga){inv[t.g]-=t.ga;inv[t.r]=(inv[t.r]||0)+t.ra;S.trade();showToast(`✅ ${EM[t.r]}×${t.ra}`);updInv();openTrade(npc);}};l.appendChild(r);});openPanel('trade-panel');}
function updNPCs(){npcs.forEach(n=>{n.timer++;if(n.timer%100===0)n.dir+=(Math.random()-.5)*Math.PI;const dx=n.hx-n.mesh.position.x,dz=n.hz-n.mesh.position.z;if(Math.sqrt(dx*dx+dz*dz)>8)n.dir=Math.atan2(dx,dz);n.mesh.position.x+=Math.sin(n.dir)*.01;n.mesh.position.z+=Math.cos(n.dir)*.01;n.mesh.rotation.y=n.dir;});}

// Dimensions
function switchDim(d){Object.values(gM()).forEach(m=>scene.remove(m));Object.values(gT()).forEach(l=>scene.remove(l));dim=d;Object.values(gM()).forEach(m=>scene.add(m));Object.values(gT()).forEach(l=>scene.add(l));
  if(d==='nether'){scene.background.set(0x220000);scene.fog=new THREE.FogExp2(0x220000,.025);sunLight.intensity=.05;ambLight.intensity=.3;ambLight.color.set(0x441111);hemiLight.intensity=.1;}
  else if(d==='end'){scene.background.set(0x060610);scene.fog=new THREE.FogExp2(0x060610,.015);sunLight.intensity=.02;ambLight.intensity=.2;ambLight.color.set(0x222244);hemiLight.intensity=.1;}
  else{scene.background.set(0x7ec8e3);scene.fog=new THREE.FogExp2(0x7ec8e3,.008);sunLight.intensity=1.3;ambLight.intensity=.6;ambLight.color.set(0x404060);hemiLight.intensity=.3;}
  updateChunks();S.portal();
  const names={overworld:'🌍 Overworld',nether:'🌋 Nether',end:'🐉 The End'};
  const lb=document.getElementById('dim-label');lb.textContent=names[d];lb.style.display='block';lb.style.animation='none';lb.offsetHeight;lb.style.animation='dimFade 3s forwards';
  setTimeout(()=>lb.style.display='none',3000);
  document.getElementById('dim-info').textContent=names[d];
  if(d==='end'&&!dragonAlive)spawnDragon();}

// Dragon
let dragon=null,dragonAlive=false;
function spawnDragon(){const body=new THREE.Group();const bm=new THREE.MeshLambertMaterial({color:0x1a1a2e,emissive:0x220044,emissiveIntensity:.3});body.add(new THREE.Mesh(new THREE.BoxGeometry(3,1.5,6),bm));const head=new THREE.Mesh(new THREE.BoxGeometry(1.5,1,2),bm);head.position.set(0,.5,4);body.add(head);const eyeG=new THREE.BoxGeometry(.3,.2,.1),eyeM=new THREE.MeshLambertMaterial({color:0xff00ff,emissive:0xff00ff,emissiveIntensity:1});[-.4,.4].forEach(ex=>{const e=new THREE.Mesh(eyeG,eyeM);e.position.set(ex,.6,5);body.add(e);});
  const wG=new THREE.BoxGeometry(5,.1,3),wM=new THREE.MeshLambertMaterial({color:0x2d2d4e,transparent:true,opacity:.8});[-1,1].forEach(s=>{const w=new THREE.Mesh(wG,wM);w.position.set(s*3.5,.3,0);w.rotation.z=s*.3;body.add(w);w.userData.ws=s;});
  body.position.set(0,20,0);scene.add(body);dragon={mesh:body,hp:200,maxHp:200,timer:0,atkTimer:0,angle:0};dragonAlive=true;document.getElementById('boss-bar').style.display='block';document.getElementById('boss-label').textContent='🐉 ENDER DRAGON';S.dragon();}
function updDragon(){if(!dragon||!dragonAlive)return;dragon.timer++;dragon.angle+=.01;dragon.mesh.position.set(Math.cos(dragon.angle)*15,18+Math.sin(dragon.timer*.03)*4,Math.sin(dragon.angle)*15);dragon.mesh.rotation.y=-dragon.angle+Math.PI/2;dragon.mesh.children.forEach(c=>{if(c.userData.ws)c.rotation.z=c.userData.ws*(.3+Math.sin(dragon.timer*.1)*.2);});dragon.atkTimer++;if(dragon.atkTimer>300){dragon.atkTimer=0;const d=new THREE.Vector3().subVectors(camera.position,dragon.mesh.position).normalize();mkArrow(dragon.mesh.position.clone(),camera.position.clone().add(d.multiplyScalar(30)),false);}if(camera.position.distanceTo(dragon.mesh.position)<4)takeDmg(4);document.getElementById('boss-bar-fill').style.width=(dragon.hp/dragon.maxHp*100)+'%';}
function hitDragon(d){if(!dragon||!dragonAlive)return;dragon.hp-=d;S.hurt();if(dragon.hp<=0){scene.remove(dragon.mesh);dragonAlive=false;dragon=null;document.getElementById('boss-bar').style.display='none';gainXP(500);showToast('🏆 DRAGON VAINCU !');setB(0,1,0,'end_portal');inv.diamond=(inv.diamond||0)+10;inv.emerald=(inv.emerald||0)+10;updInv();}}

// Mobs
const mobs=[];let mobT=0,bossA=false;
const MC={zombie:{hp:6,spd:.025,col:0x2ecc40,w:.8,h:1.8,sc:10,dmg:1},skeleton:{hp:5,spd:.03,col:0xe0e0e0,w:.7,h:1.8,sc:15,dmg:1,arc:true},spider:{hp:8,spd:.05,col:0x4a148c,w:1.2,h:.6,sc:20,dmg:2},creeper:{hp:10,spd:.03,col:0x1b5e20,w:.7,h:1.7,sc:30,dmg:5,expl:true},blaze:{hp:10,spd:.02,col:0xff9800,w:.8,h:1.8,sc:25,dmg:2,fly:true,arc:true},enderman:{hp:20,spd:.06,col:0x1a1a1a,w:.6,h:2.9,sc:40,dmg:3}};
function spawnMob(t='zombie'){const c=MC[t];if(!c)return;const a=Math.random()*Math.PI*2,d=20+Math.random()*12;let mx=camera.position.x+Math.cos(a)*d,mz=camera.position.z+Math.sin(a)*d,my=18;for(let y=18;y>=0;y--)if(getB(Math.round(mx),y,Math.round(mz))!=='air'){my=y+1;break;}const g=new THREE.BoxGeometry(c.w,c.h,c.w),m=new THREE.MeshLambertMaterial({color:c.col}),mesh=new THREE.Mesh(g,m);mesh.position.set(mx,my+c.h/2,mz);mesh.castShadow=true;const eg=new THREE.BoxGeometry(.12,.12,.08),em=new THREE.MeshLambertMaterial({color:t==='enderman'?0xff00ff:0xff0000,emissive:t==='enderman'?0xff00ff:0xff0000,emissiveIntensity:1});[-.18,.18].forEach(ex=>{const e=new THREE.Mesh(eg,em);e.position.set(ex,c.h*.32,c.w/2+.04);mesh.add(e);});scene.add(mesh);mobs.push({mesh,type:t,hp:c.hp,maxHp:c.hp,timer:0,shootT:0,explT:-1,...c});}
function updMobs(){const should=(dim==='overworld'&&isNight)||(dim==='nether')||(dim==='end');if(!should)return;mobT++;if(mobT>Math.max(80,250-mobs.length*8)&&mobs.length<18){let ts;if(dim==='nether')ts=['blaze','blaze','zombie'];else if(dim==='end')ts=['enderman'];else ts=['zombie','zombie','zombie','skeleton','spider','creeper'];spawnMob(ts[Math.random()*ts.length|0]);mobT=0;}dmgCool=Math.max(0,dmgCool-1);
  for(let i=mobs.length-1;i>=0;i--){const m=mobs[i];m.timer++;const dx=camera.position.x-m.mesh.position.x,dz=camera.position.z-m.mesh.position.z,dist=Math.sqrt(dx*dx+dz*dz);if(dist>1.5&&m.explT<0){m.mesh.position.x+=dx/dist*m.spd;m.mesh.position.z+=dz/dist*m.spd;}m.mesh.rotation.y=Math.atan2(dx,dz);if(m.fly)m.mesh.position.y=Math.max(m.mesh.position.y,8+Math.sin(m.timer*.05)*2);if(m.arc){m.shootT++;if(m.shootT>140&&dist<18){mkArrow(m.mesh.position.clone(),camera.position.clone(),false);m.shootT=0;}}if(m.expl){if(dist<3&&m.explT<0)m.explT=80;if(m.explT>0){m.explT--;m.mesh.material.color.setHex(m.explT%10<5?0xffffff:0x1b5e20);if(m.explT===0){S.tnt();takeDmg(5);for(let x=-2;x<=2;x++)for(let y=-2;y<=2;y++)for(let z=-2;z<=2;z++){const ex=Math.round(m.mesh.position.x)+x,ey=Math.round(m.mesh.position.y)+y,ez=Math.round(m.mesh.position.z)+z;if(getB(ex,ey,ez)!=='bedrock')setB(ex,ey,ez,'air');}killMob(i);continue;}}}if(dist<1.8&&m.timer%80===0)takeDmg(m.dmg);if(m.timer%220===0)S.mob();}document.getElementById('mob-count').textContent=`Mobs:${mobs.length}${should?' ⚠':''}`;
}
function killMob(i){if(i<0||i>=mobs.length)return;const m=mobs[i];scene.remove(m.mesh);gainXP(m.sc);mobsK++;const dr={zombie:['dirt','iron_ingot'],skeleton:['bone'],spider:['web'],creeper:['tnt'],blaze:['glowstone'],enderman:['emerald']};const d=dr[m.type];if(d&&Math.random()<.6){const it=d[Math.random()*d.length|0];inv[it]=(inv[it]||0)+1+(Math.random()*2|0);updInv();}mobs.splice(i,1);}

// Animals
const anims=[];let animT=0;
const AC2={cow:{col:0x8b6914,w:1.2,h:1,spd:.015,f:'beef'},pig:{col:0xff9999,w:1,h:.8,spd:.02,f:'porkchop'},chicken:{col:0xffffff,w:.5,h:.7,spd:.025,f:'chicken_raw'},sheep:{col:0xe0e0e0,w:1,h:1,spd:.015,f:'mutton'}};
function spawnAnim(t){const c=AC2[t];if(!c)return;const a=Math.random()*Math.PI*2,d=12+Math.random()*22,ax=camera.position.x+Math.cos(a)*d,az=camera.position.z+Math.sin(a)*d;let ay=15;for(let y=15;y>=0;y--)if(getB(Math.round(ax),y,Math.round(az))!=='air'){ay=y+1;break;}if(getB(Math.round(ax),ay-1,Math.round(az))==='water')return;const g=new THREE.BoxGeometry(c.w,c.h,c.w*1.5),m=new THREE.MeshLambertMaterial({color:c.col}),mesh=new THREE.Mesh(g,m);mesh.position.set(ax,ay+c.h/2,az);mesh.castShadow=true;scene.add(mesh);anims.push({mesh,type:t,food:c.f,timer:Math.random()*200|0,dir:Math.random()*Math.PI*2,...c});}
function updAnims(){if(dim!=='overworld')return;animT++;if(animT>350&&anims.length<15&&!isNight){const t=Object.keys(AC2);spawnAnim(t[Math.random()*t.length|0]);animT=0;}anims.forEach(a=>{a.timer++;if(a.timer%100===0)a.dir+=(Math.random()-.5)*Math.PI;a.mesh.position.x+=Math.sin(a.dir)*a.spd;a.mesh.position.z+=Math.cos(a.dir)*a.spd;a.mesh.rotation.y=a.dir;});}
function killAnim(i){if(i<0||i>=anims.length)return;const a=anims[i];scene.remove(a.mesh);inv[a.food]=(inv[a.food]||0)+2;gainXP(5);S.animal();showToast(`🍖+2 ${a.food}`);updInv();anims.splice(i,1);}

// Arrows
const arrows=[];const aGeo=new THREE.CylinderGeometry(.04,.04,.6,6),aMat=new THREE.MeshLambertMaterial({color:0x8b6914});
function mkArrow(from,to,isP=true){const mesh=new THREE.Mesh(aGeo,aMat.clone());mesh.position.copy(from);const d=new THREE.Vector3().subVectors(to,from).normalize();mesh.quaternion.setFromUnitVectors(new THREE.Vector3(0,1,0),d);scene.add(mesh);arrows.push({mesh,vel:d.clone().multiplyScalar(.5),isP,life:100});S.arrow();}
function updArrows(){for(let i=arrows.length-1;i>=0;i--){const a=arrows[i];a.vel.y-=.01;a.mesh.position.add(a.vel);a.life--;if(a.life<=0){scene.remove(a.mesh);arrows.splice(i,1);continue;}if(a.isP){for(let j=mobs.length-1;j>=0;j--)if(a.mesh.position.distanceTo(mobs[j].mesh.position)<1.5){mobs[j].hp-=(enchants.has('power')?4:2);if(mobs[j].hp<=0)killMob(j);scene.remove(a.mesh);arrows.splice(i,1);break;}if(dragon&&dragonAlive&&a.mesh.position.distanceTo(dragon.mesh.position)<5){hitDragon(enchants.has('power')?8:4);scene.remove(a.mesh);arrows.splice(i,1);}}else{if(a.mesh.position.distanceTo(camera.position)<1.5){takeDmg(1);scene.remove(a.mesh);arrows.splice(i,1);}}}}

// TNT + Redstone + Door
function placeTNT(x,y,z){setB(x,y,z,'tnt');showToast('💣3s');setTimeout(()=>{S.tnt();setB(x,y,z,'air');for(let dx=-3;dx<=3;dx++)for(let dy=-3;dy<=3;dy++)for(let dz=-3;dz<=3;dz++)if(Math.sqrt(dx*dx+dy*dy+dz*dz)<=3&&getB(x+dx,y+dy,z+dz)!=='bedrock')setB(x+dx,y+dy,z+dz,'air');if(camera.position.distanceTo(new THREE.Vector3(x,y,z))<5)takeDmg(effects.fire_res?1:5);},3000);}
function toggleDoor(x,y,z){const k=wk(x,y,z),ms=gM();if(ms[k]){ms[k].userData.open=!ms[k].userData.open;ms[k].rotation.y=ms[k].userData.open?Math.PI/2:0;S.door();}}
function toggleLever(x,y,z){S.redstone();showToast('🔴 Levier activé');for(let dx=-3;dx<=3;dx++)for(let dy=-2;dy<=2;dy++)for(let dz=-3;dz<=3;dz++)if(getB(x+dx,y+dy,z+dz)==='door')toggleDoor(x+dx,y+dy,z+dz);}

// Day/Night + Weather
let dayT=0;const DAY=7200;let isNight=false,lastN=false;
function updDay(){if(dim!=='overworld')return;dayT=(dayT+1)%DAY;const t=dayT/DAY;const D=new THREE.Color(0x7ec8e3),SS2=new THREE.Color(0xff9944),N=new THREE.Color(0x040410),DW=new THREE.Color(0xff7744);let sky,sI,aI;
  if(t<.25){sky=D;sI=.3+(t/.25)*1;aI=.3+(t/.25)*.4;}
  else if(t<.5){sky=D;sI=1.3;aI=.7;}
  else if(t<.6){const p=(t-.5)/.1;sky=D.clone().lerp(SS2,p);sI=1.3-p*.9;aI=.7-p*.4;}
  else if(t<.65){const p=(t-.6)/.05;sky=SS2.clone().lerp(N,p);sI=.4-p*.35;aI=.3-p*.2;}
  else if(t<.9){sky=N;sI=.03;aI=effects.night_vision?.4:.08;}
  else{const p=(t-.9)/.1;sky=N.clone().lerp(DW,p);sI=.03+p*.3;aI=.08+p*.2;}
  scene.background=sky;scene.fog.color=sky;sunLight.intensity=sI;ambLight.intensity=aI;moonLight.intensity=(t>.62&&t<.92)?.35:0;
  isNight=t>.62&&t<.92;if(isNight&&!lastN)S.night();lastN=isNight;
  document.getElementById('time-info').textContent=`${isNight?'🌙':'☀️'} ${String(Math.floor(t*24)).padStart(2,'0')}h`;
}
let weather='clear',weatherT=0;const WS=['clear','rain','snow','storm'];
const wPos=new Float32Array(200*3),wPts2=Array.from({length:200},()=>({x:0,y:0,z:0}));
const wGeo=new THREE.BufferGeometry();wGeo.setAttribute('position',new THREE.BufferAttribute(wPos,3));
const wMat=new THREE.PointsMaterial({color:0xaaf,size:.12,transparent:true,opacity:.6});
const wPart=new THREE.Points(wGeo,wMat);wPart.visible=false;scene.add(wPart);
function updWeather(){if(dim!=='overworld'){wPart.visible=false;return;}weatherT++;if(weatherT>800+Math.random()*1200){weather=WS[Math.random()*WS.length|0];weatherT=0;}if(weather==='clear'){wPart.visible=false;document.getElementById('weather-info').textContent='Dégagé';return;}wPart.visible=true;const sp=weather==='storm'?.6:.3;wMat.color.setHex(weather==='snow'?0xffffff:0x6699ff);wPts2.forEach((p,i)=>{p.y-=sp;if(p.y<camera.position.y-15){p.y=camera.position.y+20;p.x=camera.position.x+(Math.random()-.5)*60;p.z=camera.position.z+(Math.random()-.5)*60;}wPos[i*3]=p.x;wPos[i*3+1]=p.y;wPos[i*3+2]=p.z;});wGeo.attributes.position.needsUpdate=true;const wn={rain:'🌧️Pluie',snow:'❄️Neige',storm:'⛈️Orage'};document.getElementById('weather-info').textContent=wn[weather]||'';if(weather==='storm'&&Math.random()<.002){ambLight.intensity=2.5;setTimeout(()=>ambLight.intensity=.1,80);tone(55,'sawtooth',.5,.3);}}

// Stats
let hp=20,maxHp=20,hunger=20,maxHunger=20,xp=0,level=1,armor=0;
let blocksB=0,mobsK=0,dmgCool=0,hungerT=0,fallStart=-1;
let hasSword=false,hasPick=false,hasAxe=false,hasBow=false;
let toolD={sword:50,pick:50,axe:50,bow:30};
let armorP={helmet:false,chestplate:false,leggings:false,boots:false};
let flyMode=false,playerSpd=.1;
let gameOn=false,frame=0;

function calcArmor(){armor=0;if(armorP.helmet)armor+=3;if(armorP.chestplate)armor+=8;if(armorP.leggings)armor+=6;if(armorP.boots)armor+=3;}
function updArmorUI(){calcArmor();const d=document.getElementById('armor');d.innerHTML='';for(let i=0;i<Math.ceil(armor/2);i++){const s=document.createElement('span');s.style.cssText='font-size:11px';s.textContent='🛡️';d.appendChild(s);}}
function updHP(){const d=document.getElementById('health');d.innerHTML='';for(let i=0;i<maxHp/2;i++){const s=document.createElement('span');s.style.cssText='font-size:11px';s.textContent=i<hp/2?'❤️':'🖤';d.appendChild(s);}}
function updHunger(){const d=document.getElementById('hunger');d.innerHTML='';for(let i=0;i<maxHunger/2;i++){const s=document.createElement('span');s.style.cssText='font-size:10px';s.textContent=i<hunger/2?'🍖':'🦴';d.appendChild(s);}}
function updXP(){document.getElementById('xp-fill').style.width=Math.min(100,xp%100)+'%';document.getElementById('xp-level').textContent=`Lv.${level}`;}
function gainXP(a){xp+=a;const nl=Math.floor(xp/100)+1;if(nl>level){level=nl;S.lvl();showToast(`⭐ Niveau ${level}`);}updXP();}
function takeDmg(d=1){if(dmgCool>0||CFG.gameMode==='creative')return;let r=Math.max(1,d-Math.floor(armor/8));if(enchants.has('prot'))r=Math.ceil(r*.7);hp=Math.max(0,hp-r);S.hurt();dmgCool=60;updHP();if(hp<=0)die();}
function heal(a=2){hp=Math.min(maxHp,hp+a);updHP();}
function updHungerT(){if(CFG.gameMode==='creative')return;hungerT++;if(hungerT>500){hunger=Math.max(0,hunger-1);updHunger();hungerT=0;if(hunger===0)takeDmg(1);else if(hunger>=18)heal(1);}}
function die(){document.getElementById('death-xp').textContent=xp;document.getElementById('death-blocks').textContent=blocksB;document.getElementById('death-mobs').textContent=mobsK;document.getElementById('death-screen').style.display='flex';S.death();gameOn=false;}
function respawn(){hp=maxHp;hunger=maxHunger;xp=0;level=1;flyMode=false;mobs.forEach(m=>scene.remove(m.mesh));mobs.length=0;arrows.forEach(a=>scene.remove(a.mesh));arrows.length=0;anims.forEach(a=>scene.remove(a.mesh));anims.length=0;updHP();updHunger();updArmorUI();updXP();if(dim!=='overworld'){camera.position.set(0,20,0);switchDim('overworld');}else camera.position.set(0,22,0);velY=0;document.getElementById('death-screen').style.display='none';renderer.domElement.requestPointerLock();gameOn=true;loop();}

// Inventory
const inv={grass:99,dirt:50,stone:30,wood:20,sand:15,snow:5,torch:5,coal_ore:0,iron_ore:0,gold_ore:0,diamond:0,emerald:0,tnt:0,obsidian:0,planks:0,stone_brick:0,glass:0,beef:0,porkchop:0,chicken_raw:0,mutton:0,cooked_beef:0,cooked_pork:0,cooked_chicken:0,cooked_mutton:0,iron_ingot:0,gold_ingot:0,bone:0,web:0,charcoal:0,glowstone:0,nether_portal:3,end_portal:1,door:3,stair:5,slab:5,lever:2,redstone_wire:5};
const EM={grass:'🟩',dirt:'🟫',stone:'⬜',wood:'🪵',sand:'🟨',snow:'🏔️',torch:'🔦',water:'💧',coal_ore:'⚫',iron_ore:'🟤',gold_ore:'🟡',diamond:'💎',emerald:'💚',tnt:'💣',obsidian:'🟣',planks:'🪵',stone_brick:'🧱',glass:'🪟',bone:'🦴',web:'🕸️',charcoal:'🪨',beef:'🥩',porkchop:'🥓',chicken_raw:'🍗',mutton:'🍖',cooked_beef:'🥩',cooked_pork:'🥓',cooked_chicken:'🍗',cooked_mutton:'🍖',iron_ingot:'🔩',gold_ingot:'🥇',glowstone:'🟡',nether_portal:'🟣',end_portal:'⚫',door:'🚪',stair:'🪜',slab:'➖',lever:'🔘',redstone_wire:'🔴'};
function updInv(){document.getElementById('inv-info').textContent=`💎${inv.diamond||0} 🔩${inv.iron_ingot||0} 🥇${inv.gold_ingot||0} 💚${inv.emerald||0}`;}
function buildInvUI(){const g=document.getElementById('inventory-grid');g.innerHTML='';Object.entries(inv).filter(([,v])=>v>0).forEach(([it,cnt])=>{const s=document.createElement('div');s.className='inv-slot';s.innerHTML=`${EM[it]||'📦'}<div class="inv-count">${cnt}</div>`;s.title=it;const foods=['cooked_beef','cooked_pork','cooked_chicken','cooked_mutton','beef','porkchop','chicken_raw','mutton'];if(foods.includes(it))s.onclick=()=>{if(inv[it]>0){inv[it]--;hunger=Math.min(maxHunger,hunger+6);heal(4);updHunger();S.heal();showToast('🍖+4PV');updInv();closePanel('inventory-panel');}};g.appendChild(s);});}

// Crafting + Furnace
const RECIPES=[
  {id:'planks',n:'Planches×4',needs:{wood:1},gives:{planks:4}},{id:'torch',n:'Torche×4',needs:{wood:1,coal_ore:1},gives:{torch:4}},{id:'stone_brick',n:'Briques×4',needs:{stone:4},gives:{stone_brick:4}},{id:'door',n:'Porte×2',needs:{planks:6},gives:{door:2}},{id:'stair',n:'Escalier×4',needs:{planks:6},gives:{stair:4}},{id:'slab',n:'Dalle×6',needs:{stone:3},gives:{slab:6}},{id:'lever',n:'Levier',needs:{stone:1,wood:1},gives:{lever:1}},{id:'redstone_wire',n:'Redstone×4',needs:{iron_ingot:1,coal_ore:2},gives:{redstone_wire:4}},{id:'obsidian',n:'Obsidienne',needs:{diamond:2,stone:4},gives:{obsidian:1}},{id:'nether_portal',n:'Portail Nether',needs:{obsidian:10},gives:{nether_portal:1}},{id:'end_portal',n:'Portail End',needs:{emerald:12,diamond:4},gives:{end_portal:1}},{id:'sword',n:'Épée',needs:{iron_ingot:2,wood:1},gives:{sword:1}},{id:'pickaxe',n:'Pioche',needs:{iron_ingot:3,wood:2},gives:{pickaxe:1}},{id:'axe',n:'Hache',needs:{iron_ingot:3,wood:2},gives:{axe:1}},{id:'bow',n:'Arc',needs:{wood:3,bone:2},gives:{bow:1}},{id:'helmet',n:'Casque',needs:{iron_ingot:5},gives:{helmet:1}},{id:'chestplate',n:'Plastron',needs:{iron_ingot:8},gives:{chestplate:1}},{id:'leggings',n:'Jambières',needs:{iron_ingot:7},gives:{leggings:1}},{id:'boots',n:'Bottes',needs:{iron_ingot:4},gives:{boots:1}},{id:'tnt',n:'TNT',needs:{coal_ore:5,sand:4},gives:{tnt:1}},
];
function buildCraftUI(){const l=document.getElementById('craft-list');l.innerHTML='';RECIPES.forEach(r=>{const row=document.createElement('div');row.className='craft-row';const can=Object.entries(r.needs).every(([m,a])=>(inv[m]||0)>=a);row.innerHTML=`<span>${EM[Object.keys(r.gives)[0]]||'🔨'} ${r.n}</span><span class="recipe">${Object.entries(r.needs).map(([m,a])=>`${EM[m]||m}×${a}`).join('+')}</span>`;row.style.opacity=can?1:.4;if(can)row.onclick=()=>doCraft(r.id);l.appendChild(row);});}
function doCraft(id){const r=RECIPES.find(x=>x.id===id);if(!r)return;for(const[m,a]of Object.entries(r.needs)){if((inv[m]||0)<a)return;}for(const[m,a]of Object.entries(r.needs))inv[m]-=a;for(const[it]of Object.entries(r.gives)){if(it==='sword'){hasSword=true;toolD.sword=50;}else if(it==='pickaxe'){hasPick=true;toolD.pick=50;}else if(it==='axe'){hasAxe=true;toolD.axe=50;}else if(it==='bow'){hasBow=true;toolD.bow=30;}else if(it in armorP){armorP[it]=true;updArmorUI();}else inv[it]=(inv[it]||0)+(r.gives[it]||1);}S.craft();showToast(`✅ ${r.n}`);updInv();buildCraftUI();}

const FURN={iron_ore:{r:'iron_ingot'},gold_ore:{r:'gold_ingot'},coal_ore:{r:'charcoal'},sand:{r:'glass'},beef:{r:'cooked_beef'},porkchop:{r:'cooked_pork'},chicken_raw:{r:'cooked_chicken'},mutton:{r:'cooked_mutton'}};
let furnI=null,furnC=false,furnT=0;
function buildFurnUI(){const l=document.getElementById('furnace-list');l.innerHTML='';Object.entries(FURN).forEach(([input,out])=>{const d=document.createElement('div');d.className='craft-row';const h=inv[input]||0;d.innerHTML=`<span>${EM[input]||'?'}→${out.r}</span><span class="recipe">${h}</span>`;d.style.opacity=h>0?1:.4;if(h>0)d.onclick=()=>{if(inv[input]>0){inv[input]--;furnI=input;furnC=true;furnT=0;S.craft();document.getElementById('furnace-status').textContent=`🔥 ${input}...`;updInv();buildFurnUI();}};l.appendChild(d);});}
function updFurn(){if(!furnC)return;furnT+=16;if(furnT>=2500){const r=FURN[furnI];inv[r.r]=(inv[r.r]||0)+1;furnC=false;furnI=null;S.lvl();showToast(`✅ ${r.r}`);document.getElementById('furnace-status').textContent='✅';updInv();buildFurnUI();}}

// Enchants + Potions
const effects={};const enchants=new Set();
const POTIONS=[{id:'speed',n:'🏃Vitesse',dur:1200,needs:{emerald:1}},{id:'strength',n:'⚔️Force',dur:1200,needs:{iron_ingot:2}},{id:'regen',n:'💚Régén',dur:600,needs:{gold_ingot:1}},{id:'fire_res',n:'🔥Résist.Feu',dur:1800,needs:{gold_ingot:2}},{id:'night_vision',n:'👁️Vision',dur:1200,needs:{gold_ingot:1,coal_ore:2}},{id:'jump',n:'🐇Saut',dur:1200,needs:{emerald:1,iron_ingot:1}}];
const ENCHANTS=[{id:'sharp',n:'⚔️Tranchant',needs:{diamond:2},xp:50},{id:'eff',n:'⛏️Efficacité',needs:{diamond:2,iron_ingot:3},xp:50},{id:'prot',n:'🛡️Protection',needs:{diamond:3},xp:100},{id:'power',n:'🏹Puissance',needs:{diamond:1,emerald:2},xp:50},{id:'unbreak',n:'💎Solidité',needs:{diamond:3},xp:75}];

function buildPotUI(){const l=document.getElementById('potion-list');l.innerHTML='';POTIONS.forEach(p=>{const r=document.createElement('div');r.className='craft-row';const can=Object.entries(p.needs).every(([m,a])=>(inv[m]||0)>=a);r.innerHTML=`<span>${p.n}</span><span class="recipe">${Object.entries(p.needs).map(([m,a])=>`${EM[m]||m}×${a}`).join('+')}</span>`;r.style.opacity=can&&!effects[p.id]?1:.4;if(can&&!effects[p.id])r.onclick=()=>{for(const[m,a]of Object.entries(p.needs))inv[m]-=a;effects[p.id]={timer:p.dur};S.potion();showToast(`🧪${p.n}`);updInv();buildPotUI();};l.appendChild(r);});}
function buildEnchUI(){const l=document.getElementById('enchant-list');l.innerHTML='';ENCHANTS.forEach(e=>{const r=document.createElement('div');r.className='craft-row';const can=Object.entries(e.needs).every(([m,a])=>(inv[m]||0)>=a)&&xp>=e.xp;const has=enchants.has(e.id);r.innerHTML=`<span>${e.n}${has?' ✅':''}</span><span class="recipe">${Object.entries(e.needs).map(([m,a])=>`${EM[m]||m}×${a}`).join('+')}+${e.xp}XP</span>`;r.style.opacity=can&&!has?1:.4;if(can&&!has)r.onclick=()=>{for(const[m,a]of Object.entries(e.needs))inv[m]-=a;xp-=e.xp;updXP();enchants.add(e.id);S.enchant();showToast(`✨${e.n}`);updInv();buildEnchUI();};l.appendChild(r);});}
function updEffects(){const bar=document.getElementById('effects-bar');bar.innerHTML='';Object.entries(effects).forEach(([id,e])=>{e.timer--;if(e.timer<=0){delete effects[id];return;}const d=document.createElement('div');d.className='effect-icon';d.textContent=`${id} ${Math.ceil(e.timer/20)}s`;bar.appendChild(d);if(id==='regen'&&frame%40===0)heal(1);});}

// Save/Load
function saveGame(){const data={W:{},pos:{x:camera.position.x,y:camera.position.y,z:camera.position.z},yaw,pitch,dim,hp,hunger,xp,level,blocksB,mobsK,dayT,inv,hasSword,hasPick,hasAxe,hasBow,armorP,toolD,enchants:[...enchants]};['overworld','nether','end'].forEach(d=>{data.W[d]={};Object.entries(W[d]).forEach(([k,v])=>{if(v!=='air')data.W[d][k]=v;});});try{localStorage.setItem('mc5',JSON.stringify(data));S.save();showToast('💾 Sauvegardé');}catch(e){showToast('❌ Erreur');}}
function loadGame(){const raw=localStorage.getItem('mc5');if(!raw)return false;try{const data=JSON.parse(raw);['overworld','nether','end'].forEach(d=>{Object.keys(M[d]).forEach(k=>{scene.remove(M[d][k]);delete M[d][k];});Object.keys(TL[d]).forEach(k=>{scene.remove(TL[d][k]);delete TL[d][k];});W[d]={};loadedC[d].clear();});dim=data.dim||'overworld';Object.entries(data.W).forEach(([d,blocks])=>{const prev=dim;dim=d;Object.entries(blocks).forEach(([k,t])=>{const[x,y,z]=k.split(',').map(Number);setB(x,y,z,t);});dim=prev;});['overworld','nether','end'].forEach(d=>{const show=d===dim;Object.values(M[d]).forEach(m=>{if(show)scene.add(m);else scene.remove(m);});Object.values(TL[d]).forEach(l=>{if(show)scene.add(l);else scene.remove(l);});});camera.position.set(data.pos.x,data.pos.y,data.pos.z);yaw=data.yaw||0;pitch=data.pitch||0;hp=data.hp||20;hunger=data.hunger||20;xp=data.xp||0;level=data.level||1;dayT=data.dayT||0;if(data.inv)Object.assign(inv,data.inv);hasSword=data.hasSword||false;hasPick=data.hasPick||false;hasAxe=data.hasAxe||false;hasBow=data.hasBow||false;if(data.armorP)Object.assign(armorP,data.armorP);if(data.toolD)Object.assign(toolD,data.toolD);if(data.enchants)data.enchants.forEach(e=>enchants.add(e));updHP();updHunger();updArmorUI();updXP();updInv();if(dim!=='overworld')switchDim(dim);return true;}catch(e){return false;}}

// Raycaster
const outGeo=new THREE.BoxGeometry(1.005,1.005,1.005);const outMat=new THREE.LineBasicMaterial({color:0x000000});
const outEdges=new THREE.EdgesGeometry(outGeo);const outline=new THREE.LineSegments(outEdges,outMat);outline.visible=false;scene.add(outline);
const ray=new THREE.Raycaster(),ctr=new THREE.Vector2(0,0);
function getHit(){ray.setFromCamera(ctr,camera);const all=[...Object.values(gM()),...mobs.map(m=>m.mesh),...anims.map(a=>a.mesh),...npcs.map(n=>n.mesh)];if(dragon&&dragonAlive)all.push(dragon.mesh);return ray.intersectObjects(all,true)[0]||null;}

// Controls
let selSlot=0;const slots=document.querySelectorAll('.slot');let panelOpen=false,ptrLock=false,consoleOpen=false;
function selS(i){slots[selSlot].classList.remove('active');selSlot=i;slots[selSlot].classList.add('active');}
slots.forEach((s,i)=>s.addEventListener('click',()=>selS(i)));
function openPanel(id){document.getElementById(id).style.display='block';panelOpen=true;document.exitPointerLock();if(id==='inventory-panel')buildInvUI();if(id==='crafting-panel')buildCraftUI();if(id==='furnace-panel')buildFurnUI();if(id==='enchant-panel')buildEnchUI();if(id==='potion-panel')buildPotUI();if(id==='stats-panel'){document.getElementById('stats-content').innerHTML=`❤️${hp}/${maxHp} 🍖${hunger}/${maxHunger} 🛡️${armor} ⭐Lv${level}(${xp}XP)<br>⛏️${blocksB} 🧟${mobsK} 📦${loadedC[dim].size}ch 🌍${dim}<br>⚔️${hasSword?'✅':'❌'} ⛏️${hasPick?'✅':'❌'} 🏹${hasBow?'✅':'❌'}<br>✨${[...enchants].join(',')||'-'} 🧪${Object.keys(effects).join(',')||'-'}`;}}
function closePanel(id){document.getElementById(id).style.display='none';panelOpen=false;if(gameOn&&!isMob)try{renderer.domElement.requestPointerLock();}catch(e){}}

let toastTm=null;function showToast(msg,d=2000){const t=document.getElementById('toast');t.textContent=msg;t.style.display='block';clearTimeout(toastTm);toastTm=setTimeout(()=>t.style.display='none',d);}

// FPS
let fpsF=0,fpsT2=performance.now(),curFps=60;
function updFPS(){fpsF++;const n=performance.now();if(n-fpsT2>=1000){curFps=fpsF;fpsF=0;fpsT2=n;if(CFG.showFps)document.getElementById('fps-counter').textContent=`${curFps} FPS`;}}

// Keyboard
const keys={};let yaw=0,pitch=0,velY=0,onGround=false;
const GRAV=.015,JUMP=.3;let stepT=0,mineBlock=null,mineTime=0,mineMax=0,leftDown=false,bowCD=0;
const MT={bedrock:Infinity,obsidian:8000,diamond_ore:4000,emerald_ore:4000,gold_ore:3000,iron_ore:3000,coal_ore:2000,stone:1500,gravel:800,dirt:600,grass:600,sand:500,snow:300,wood:1000,leaves:200,planks:500,stone_brick:1500,door:400,stair:500,slab:500,lever:200,netherrack:600,nether_brick:1200,end_stone:1500,default:500};

// Mobile
let isMob=/Android|iPhone|iPad|iPod/i.test(navigator.userAgent)||'ontouchstart'in window;
let mJoy={on:false,dx:0,dy:0},lastTX=0,lastTY=0,tLook=false;
if(isMob){
  const jz=document.getElementById('joystick-zone'),jk=document.getElementById('joystick-knob');
  jz.addEventListener('touchstart',e=>{mJoy.on=true;e.preventDefault();},{passive:false});
  jz.addEventListener('touchmove',e=>{if(!mJoy.on)return;const t=e.touches[0],r=jz.getBoundingClientRect();let dx=t.clientX-r.left-r.width/2,dy=t.clientY-r.top-r.height/2;const d=Math.sqrt(dx*dx+dy*dy);if(d>45){dx=dx/d*45;dy=dy/d*45;}jk.style.left=(55+dx)+'px';jk.style.top=(55+dy)+'px';jk.style.transform='none';mJoy.dx=dx/45;mJoy.dy=dy/45;e.preventDefault();},{passive:false});
  jz.addEventListener('touchend',()=>{mJoy.on=false;mJoy.dx=0;mJoy.dy=0;jk.style.left='50%';jk.style.top='50%';jk.style.transform='translate(-50%,-50%)';});
  document.getElementById('btn-jump').addEventListener('touchstart',e=>{keys['Space']=true;e.preventDefault();},{passive:false});
  document.getElementById('btn-jump').addEventListener('touchend',()=>keys['Space']=false);
  document.getElementById('btn-break').addEventListener('touchstart',e=>{leftDown=true;e.preventDefault();},{passive:false});
  document.getElementById('btn-break').addEventListener('touchend',()=>{leftDown=false;mineBlock=null;mineTime=0;document.getElementById('mine-bar').style.display='none';});
  document.getElementById('btn-place').addEventListener('touchstart',e=>{doPlace();e.preventDefault();},{passive:false});
  document.getElementById('btn-inv').addEventListener('touchstart',e=>{openPanel('inventory-panel');e.preventDefault();},{passive:false});
  document.getElementById('btn-shoot').addEventListener('touchstart',e=>{doShoot();e.preventDefault();},{passive:false});
  renderer.domElement.addEventListener('touchstart',e=>{if(e.touches.length===1){tLook=true;lastTX=e.touches[0].clientX;lastTY=e.touches[0].clientY;}},{passive:true});
  renderer.domElement.addEventListener('touchmove',e=>{if(!tLook||e.touches.length!==1)return;yaw-=(e.touches[0].clientX-lastTX)*.004;pitch-=(e.touches[0].clientY-lastTY)*.004;pitch=Math.max(-Math.PI/2+.05,Math.min(Math.PI/2-.05,pitch));lastTX=e.touches[0].clientX;lastTY=e.touches[0].clientY;},{passive:true});
  renderer.domElement.addEventListener('touchend',()=>tLook=false);
}

// Console debug
function toggleConsole(){consoleOpen=!consoleOpen;document.getElementById('debug-console').style.display=consoleOpen?'block':'none';if(consoleOpen){document.getElementById('debug-input').focus();document.exitPointerLock();}else if(gameOn&&!isMob)try{renderer.domElement.requestPointerLock();}catch(e){}}
document.getElementById('debug-input').addEventListener('keydown',e=>{
  if(e.key==='Enter'){const cmd=e.target.value.trim();e.target.value='';if(!cmd)return;const p=cmd.split(' '),c=p[0].toLowerCase();const log=document.getElementById('debug-log');const add=(m,cl='debug-info')=>{const d=document.createElement('div');d.className=cl;d.textContent=m;log.appendChild(d);log.scrollTop=log.scrollHeight;};
    add(`> ${cmd}`);
    if(c==='/help')add('/tp /give /time /weather /heal /kill /killall /summon /fly /dim /gamemode /xp','debug-ok');
    else if(c==='/tp'&&p.length>=4){camera.position.set(+p[1],+p[2],+p[3]);add('✅','debug-ok');}
    else if(c==='/give'&&p[1]){inv[p[1]]=(inv[p[1]]||0)+(+p[2]||1);updInv();add('✅','debug-ok');}
    else if(c==='/time'&&p[1]==='set'){if(p[2]==='day')dayT=DAY*.3;else if(p[2]==='night')dayT=DAY*.75;add('✅','debug-ok');}
    else if(c==='/weather'&&p[1]){weather=p[1];add('✅','debug-ok');}
    else if(c==='/heal'){hp=maxHp;hunger=maxHunger;updHP();updHunger();add('✅','debug-ok');}
    else if(c==='/kill'){hp=0;die();add('💀','debug-err');}
    else if(c==='/killall'){mobs.forEach(m=>scene.remove(m.mesh));mobs.length=0;add('✅','debug-ok');}
    else if(c==='/summon'&&p[1]){spawnMob(p[1]);add('✅','debug-ok');}
    else if(c==='/fly'){flyMode=!flyMode;add(`Vol:${flyMode?'ON':'OFF'}`,'debug-ok');}
    else if(c==='/dim'&&p[1]){camera.position.set(0,d==='end'?8:d==='nether'?10:20,0);switchDim(p[1]);add('✅','debug-ok');}
    else if(c==='/gamemode'){CFG.gameMode=p[1]==='creative'||p[1]==='c'?'creative':'survival';if(CFG.gameMode==='creative')ALL_B.forEach(b=>inv[b]=999);updInv();add(`Mode:${CFG.gameMode}`,'debug-ok');}
    else if(c==='/xp'){gainXP(+(p[1]||100));add('✅','debug-ok');}
    else add('❌ /help','debug-err');
  }if(e.key==='Escape')toggleConsole();e.stopPropagation();
});

document.addEventListener('keydown',e=>{
  keys[e.code]=true;if(consoleOpen)return;
  if(panelOpen){if(e.code==='Escape'){document.querySelectorAll('.panel').forEach(p=>p.style.display='none');panelOpen=false;if(gameOn&&!isMob)setTimeout(()=>{try{renderer.domElement.requestPointerLock();}catch(e){}},100);}return;}
  if(!gameOn)return;
  for(let i=1;i<=9;i++)if(e.code===`Digit${i}`)selS(i-1);
  if(e.code==='KeyE')openPanel('inventory-panel');
  if(e.code==='KeyC')openPanel('crafting-panel');
  if(e.code==='KeyF')openPanel('furnace-panel');
  if(e.code==='KeyN')openPanel('enchant-panel');
  if(e.code==='KeyP')openPanel('potion-panel');
  if(e.code==='KeyO')openPanel('options-panel');
  if(e.code==='KeyT'){const h=getHit();if(h){const npc=npcs.find(n=>n.mesh===h.object||h.object.parent===n.mesh);if(npc)openTrade(npc);else openPanel('stats-panel');}else openPanel('stats-panel');}
  if(e.code==='Tab'){e.preventDefault();openPanel('stats-panel');}
  if(e.code==='Slash')toggleConsole();
  if(e.code==='F5'){e.preventDefault();saveGame();}
  if(e.code==='F6'){e.preventDefault();if(loadGame())showToast('✅ Chargé');}
  if(e.code==='F11'){e.preventDefault();if(!document.fullscreenElement)document.documentElement.requestFullscreen();else document.exitFullscreen();}
  if(['Space','ArrowUp','ArrowDown','ArrowLeft','ArrowRight'].includes(e.code))e.preventDefault();
});
document.addEventListener('keyup',e=>keys[e.code]=false);
document.addEventListener('mousemove',e=>{if(!ptrLock)return;yaw-=e.movementX*CFG.sens;pitch-=e.movementY*CFG.sens;pitch=Math.max(-Math.PI/2+.05,Math.min(Math.PI/2-.05,pitch));});

function doPlace(){
  const h=getHit();if(!h||!h.face)return;
  const npc=npcs.find(n=>n.mesh===h.object||h.object.parent===n.mesh);if(npc){openTrade(npc);return;}
  const p=h.object.position,n=h.face.normal;const bx=Math.round(p.x+n.x),by=Math.round(p.y+n.y),bz=Math.round(p.z+n.z);
  if(getB(bx,by,bz)!=='air')return;
  const sb=['grass','dirt','stone','wood','torch','tnt','door','stair','nether_portal'];
  let t=sb[selSlot]||'stone';
  if(CFG.gameMode!=='creative'){if(!inv[t]||inv[t]<=0){const f=Object.keys(inv).find(k=>inv[k]>0&&ALL_B.includes(k));if(f)t=f;else return;}if(t==='tnt'){placeTNT(bx,by,bz);inv.tnt--;}else{setB(bx,by,bz,t);inv[t]--;}}else setB(bx,by,bz,t);
  S.place();updInv();
}
function doShoot(){if(!hasBow||bowCD>0)return;const d=new THREE.Vector3(0,0,-1).applyQuaternion(camera.quaternion);mkArrow(camera.position.clone(),camera.position.clone().add(d.multiplyScalar(30)),true);if(CFG.gameMode!=='creative'){toolD.bow--;if(toolD.bow<=0){hasBow=false;showToast('🏹Cassé');}}bowCD=20;}

document.addEventListener('mousedown',e=>{if(!ptrLock)return;if(e.button===0)leftDown=true;if(e.button===2)doPlace();});
document.addEventListener('mouseup',e=>{if(e.button===0){leftDown=false;mineBlock=null;mineTime=0;document.getElementById('mine-bar').style.display='none';}});
document.addEventListener('contextmenu',e=>e.preventDefault());

// Mining
function updMine(){
  if(!leftDown||(!(ptrLock||isMob))){mineBlock=null;mineTime=0;return;}
  const h=getHit();if(!h){mineBlock=null;mineTime=0;document.getElementById('mine-bar').style.display='none';return;}
  const obj=h.object;
  const mi=mobs.findIndex(m=>m.mesh===obj),ai=anims.findIndex(a=>a.mesh===obj);
  const npc=npcs.find(n=>n.mesh===obj||obj.parent===n.mesh);
  if(dragon&&dragonAlive&&(obj===dragon.mesh||obj.parent===dragon.mesh)){if(dmgCool>0)return;let d=hasSword?6:2;if(enchants.has('sharp'))d=Math.ceil(d*1.5);if(effects.strength)d*=2;hitDragon(d);dmgCool=20;return;}
  if(mi>=0){if(dmgCool>0)return;let d=hasSword?3:1;if(enchants.has('sharp'))d=Math.ceil(d*1.5);if(effects.strength)d*=2;mobs[mi].hp-=d;if(mobs[mi].hp<=0)killMob(mi);else S.hurt();dmgCool=20;return;}
  if(ai>=0){killAnim(ai);return;}
  if(npc){openTrade(npc);return;}
  const p=obj.position;const bx=Math.round(p.x),by=Math.round(p.y),bz=Math.round(p.z);const type=getB(bx,by,bz);const k=wk(bx,by,bz);
  if(type==='air'||type==='bedrock')return;
  if(type==='lever'){toggleLever(bx,by,bz);leftDown=false;return;}
  if(type==='door'){toggleDoor(bx,by,bz);leftDown=false;return;}
  if(type==='nether_portal'||type==='end_portal'){if(dim==='overworld'&&type==='nether_portal'){camera.position.set(0,10,0);switchDim('nether');}else if(dim==='nether'){camera.position.set(0,20,0);switchDim('overworld');}else if(dim==='overworld'&&type==='end_portal'){camera.position.set(0,8,0);switchDim('end');}else if(dim==='end'){camera.position.set(0,20,0);switchDim('overworld');}leftDown=false;return;}
  if(mineBlock!==k){mineBlock=k;mineTime=0;mineMax=MT[type]||MT.default;}
  let sp=CFG.gameMode==='creative'?999:16;
  if(hasPick&&['stone','coal_ore','iron_ore','gold_ore','diamond_ore','emerald_ore','gravel','stone_brick','obsidian','netherrack','nether_brick','end_stone'].includes(type)){sp*=3.5;if(enchants.has('eff'))sp*=1.5;const dl=enchants.has('unbreak')?.015:.03;toolD.pick=Math.max(0,toolD.pick-dl);if(toolD.pick<=0){hasPick=false;showToast('⛏️Cassée');}}
  if(hasAxe&&['wood','leaves','planks','door','stair','chest'].includes(type))sp*=3;
  mineTime+=sp;if(frame%5===0)S.mine();
  const pct=Math.min(100,mineTime/mineMax*100);
  document.getElementById('mine-bar').style.display='block';document.getElementById('mine-fill').style.width=pct+'%';
  if(mineTime>=mineMax){
    const drops={grass:'grass',dirt:'dirt',stone:'stone',sand:'sand',snow:'snow',wood:'wood',leaves:'leaves',gravel:'gravel',planks:'planks',stone_brick:'stone_brick',coal_ore:'coal_ore',iron_ore:'iron_ore',gold_ore:'gold_ore',diamond_ore:'diamond',emerald_ore:'emerald',torch:'torch',obsidian:'obsidian',netherrack:'netherrack',soul_sand:'soul_sand',nether_brick:'nether_brick',glowstone:'glowstone',end_stone:'end_stone',chest:'chest',door:'door',stair:'stair',slab:'slab',lever:'lever',redstone_wire:'redstone_wire',tnt:'tnt',wheat:'wheat',carrot:'carrot',potato:'potato'};
    const drop=drops[type];if(drop){inv[drop]=(inv[drop]||0)+1;updInv();}
    if(['wheat','carrot','potato'].includes(type)){hunger=Math.min(maxHunger,hunger+2);updHunger();}
    setB(bx,by,bz,'air');S.brk();blocksB++;gainXP(1);mineBlock=null;mineTime=0;document.getElementById('mine-bar').style.display='none';
  }
}

// Player physics
function movePlayer(){
  if(panelOpen||consoleOpen)return;
  const front=new THREE.Vector3(Math.sin(yaw),0,Math.cos(yaw));
  const right=new THREE.Vector3(Math.cos(yaw),0,-Math.sin(yaw));
  const dir=new THREE.Vector3();
  if(keys['KeyZ']||keys['ArrowUp'])dir.addScaledVector(front,-1);
  if(keys['KeyS']||keys['ArrowDown'])dir.addScaledVector(front,1);
  if(keys['KeyQ']||keys['ArrowLeft'])dir.addScaledVector(right,-1);
  if(keys['KeyD']||keys['ArrowRight'])dir.addScaledVector(right,1);
  if(mJoy.on){dir.addScaledVector(front,-mJoy.dy);dir.addScaledVector(right,mJoy.dx);}
  let spd=keys['ShiftLeft']?playerSpd*1.9:playerSpd;
  if(effects.speed)spd*=2;
  if(dir.length()>0){dir.normalize().multiplyScalar(spd);stepT++;if(stepT%32===0&&onGround)S.step();}
  camera.position.x+=dir.x;camera.position.z+=dir.z;

  if(flyMode||CFG.gameMode==='creative'&&keys['Space']){
    if(keys['Space'])camera.position.y+=.15;if(keys['ShiftLeft']&&flyMode)camera.position.y-=.1;velY=0;
  }else{
    velY-=GRAV;camera.position.y+=velY;
    const px=Math.round(camera.position.x),py=Math.floor(camera.position.y-1.7),pz=Math.round(camera.position.z);
    const bl=getB(px,py,pz);
    if(bl!=='air'&&bl!=='water'&&bl!=='lava'){
      // Fall damage
      if(fallStart>=0){const fallDist=fallStart-camera.position.y;if(fallDist>3&&CFG.gameMode!=='creative'){const dmg=Math.floor(fallDist-3);takeDmg(dmg);S.fall();}fallStart=-1;}
      camera.position.y=py+1+1.7;velY=0;onGround=true;
    }else{
      onGround=false;
      if(fallStart<0&&velY<0)fallStart=camera.position.y;
    }
    if(getB(px,Math.floor(camera.position.y-.5),pz)==='water'){velY=Math.max(velY,-.04);S.splash();fallStart=-1;}
    if(getB(px,Math.floor(camera.position.y-.5),pz)==='lava'&&!effects.fire_res)takeDmg(2);
    let jp=JUMP;if(effects.jump)jp*=1.8;
    if(keys['Space']&&onGround){velY=jp;onGround=false;S.jump();fallStart=camera.position.y;}
  }
  if(camera.position.y<-10){camera.position.y=22;velY=0;takeDmg(4);}
  camera.rotation.order='YXZ';camera.rotation.y=yaw;camera.rotation.x=pitch;
  document.getElementById('pos').textContent=`${Math.round(camera.position.x)} ${Math.round(camera.position.y)} ${Math.round(camera.position.z)}`;
  bowCD=Math.max(0,bowCD-1);if(keys['KeyR'])doShoot();
}

// Main loop
let waterAnim=0;
function loop(){
  if(!gameOn)return;
  requestAnimationFrame(loop);frame++;
  updFPS();movePlayer();
  updDay();updWeather();
  updMobs();updAnims();updNPCs();
  updArrows();updHungerT();
  updMine();updFurn();updEffects();
  if(dim==='end')updDragon();
  // Water + lava anim
  waterAnim+=.02;
  if(mats.water&&!Array.isArray(mats.water))mats.water.opacity=.5+Math.sin(waterAnim)*.15;
  if(mats.lava&&!Array.isArray(mats.lava))mats.lava.opacity=.7+Math.sin(waterAnim*1.5)*.15;
  // Torch flicker
  if(frame%3===0)Object.values(gT()).forEach(pl=>{pl.intensity=1.2+Math.sin(frame*.15+pl.position.x)*.4;});
  // Chunks
  if(frame%30===0)updateChunks();
  // Outline
  const hit=getHit();
  if(hit){const p=hit.object.position;if(p){const k=wk(Math.round(p.x),Math.round(p.y),Math.round(p.z));if(gM()[k]){outline.position.copy(p);outline.visible=true;}else outline.visible=false;}else outline.visible=false;}else outline.visible=false;
  renderer.render(scene,camera);
}

// Start
function start(doLoad=false){
  document.getElementById('overlay').style.display='none';
  document.getElementById('death-screen').style.display='none';
  initAudio();buildMats();loadCfg();
  if(doLoad&&loadGame()){}
  else{updateChunks();const n=oct(0,0);camera.position.set(0,Math.floor(n*14)+12,0);}
  updHP();updHunger();updArmorUI();updXP();updInv();
  if(dim==='overworld'){const at=Object.keys(AC2);for(let i=0;i<10;i++)setTimeout(()=>spawnAnim(at[Math.random()*at.length|0]),i*200);}
  gameOn=true;if(!isMob)try{renderer.domElement.requestPointerLock();}catch(e){}
  applyOptions();loop();
}

document.getElementById('click-to-play').addEventListener('click',()=>start(false));
document.getElementById('loadBtn').addEventListener('click',()=>start(true));
document.getElementById('respawnBtn').addEventListener('click',respawn);
document.addEventListener('pointerlockchange',()=>{ptrLock=!!document.pointerLockElement;if(!ptrLock&&gameOn&&!panelOpen&&!consoleOpen&&!isMob)showToast('⏸️ Cliquez pour jouer',3000);});
renderer.domElement.addEventListener('click',()=>{if(gameOn&&!panelOpen&&!consoleOpen&&!ptrLock&&!isMob)try{renderer.domElement.requestPointerLock();}catch(e){}});
</script>
</body>
</html>
