<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1,user-scalable=no,viewport-fit=cover"/>
<title>Cipher Stratos</title>
<link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;600;700;900&family=DM+Mono:ital,wght@0,300;0,400;0,500;1,300&family=Bebas+Neue&family=Exo+2:wght@200;300;400;700&display=swap" rel="stylesheet"/>
<!-- Three.js for 3D -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<style>
:root{
  --bg:#03050d;--s1:#070d1a;--s2:#0b1425;--s3:#0f1c34;
  --b1:#122040;--b2:#1a2e55;--b3:#243d6e;
  --pu:#9b5de5;--pu2:#7b3dc5;--pu3:#5a1fa0;
  --go:#f4c430;--go2:#d4a510;
  --cyan:#00f0ff;--cyan2:#00b8cc;
  --green:#00ff9d;--red:#ff2255;
  --text:#e0ecff;--text2:#7a9fc8;--muted:#2a4570;
  --orb:'Orbitron',monospace;--mono:'DM Mono',monospace;
  --bbn:'Bebas Neue',cursive;--exo:'Exo 2',sans-serif;
  --safe-top:env(safe-area-inset-top,0px);
  --safe-bot:env(safe-area-inset-bottom,0px);
}
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
html{font-size:16px;-webkit-text-size-adjust:100%}
body{
  background:var(--bg);color:var(--text);
  font-family:var(--mono);
  min-height:100vh;min-height:100dvh;
  overflow:hidden;
  -webkit-tap-highlight-color:transparent;
  cursor:default;
}

/* ══ 3D CANVAS (fullscreen background) ══ */
#threeCanvas{
  position:fixed;inset:0;z-index:0;
  pointer-events:none;
  opacity:.85;
}

/* ══ 2D OVERLAY CANVAS ══ */
#fxCanvas{
  position:fixed;inset:0;z-index:1;
  pointer-events:none;
}

/* ══ SCANLINES ══ */
.scanlines{
  position:fixed;inset:0;z-index:2;pointer-events:none;
  background:repeating-linear-gradient(
    0deg,transparent,transparent 2px,
    rgba(0,240,255,.01) 2px,rgba(0,240,255,.01) 4px
  );
}

/* ══ VIGNETTE ══ */
.vignette{
  position:fixed;inset:0;z-index:3;pointer-events:none;
  background:radial-gradient(ellipse 80% 80% at 50% 50%,transparent 40%,rgba(0,0,0,.85) 100%);
}

/* ══ APP SHELL ══ */
#app{
  position:relative;z-index:10;
  height:100vh;height:100dvh;
  display:flex;flex-direction:column;
  overflow:hidden;
}

/* ══ HEADER ══ */
header{
  flex-shrink:0;
  padding-top:var(--safe-top);
  background:linear-gradient(180deg,rgba(3,5,13,.97) 0%,rgba(7,13,26,.92) 100%);
  border-bottom:1px solid rgba(155,93,229,.2);
  position:relative;overflow:hidden;
  backdrop-filter:blur(12px);
  -webkit-backdrop-filter:blur(12px);
}
.hdr-aurora{
  position:absolute;inset:0;
  background:linear-gradient(90deg,
    rgba(155,93,229,.04) 0%,
    rgba(0,240,255,.06) 35%,
    rgba(244,196,48,.04) 70%,
    rgba(155,93,229,.04) 100%
  );
  animation:auroraShift 8s ease infinite;
}
@keyframes auroraShift{
  0%{transform:translateX(-20%)}
  50%{transform:translateX(20%)}
  100%{transform:translateX(-20%)}
}
.hdr-line{
  position:absolute;bottom:0;left:0;right:0;height:1px;
  background:linear-gradient(90deg,transparent,var(--pu),var(--cyan),var(--go),transparent);
  animation:linePulse 4s ease infinite;
}
@keyframes linePulse{0%,100%{opacity:.4}50%{opacity:1}}

.hdr-inner{
  display:flex;align-items:center;justify-content:space-between;
  padding:10px 16px;position:relative;
}

/* LOGO */
.logo-wrap{display:flex;align-items:center;gap:10px}
.logo-orb{
  width:36px;height:36px;border-radius:50%;
  background:radial-gradient(circle at 35% 35%,rgba(155,93,229,.6),rgba(0,240,255,.2),transparent 70%);
  border:1.5px solid rgba(155,93,229,.5);
  display:grid;place-items:center;
  position:relative;
  box-shadow:0 0 20px rgba(155,93,229,.3),0 0 40px rgba(155,93,229,.15);
  animation:orbRotate 10s linear infinite;
}
@keyframes orbRotate{
  0%{box-shadow:0 0 20px rgba(155,93,229,.3),0 0 40px rgba(155,93,229,.15)}
  33%{box-shadow:0 0 20px rgba(0,240,255,.3),0 0 40px rgba(0,240,255,.15)}
  66%{box-shadow:0 0 20px rgba(244,196,48,.3),0 0 40px rgba(244,196,48,.15)}
  100%{box-shadow:0 0 20px rgba(155,93,229,.3),0 0 40px rgba(155,93,229,.15)}
}
.logo-orb svg{width:16px;height:16px;filter:drop-shadow(0 0 4px var(--pu))}
.logo-orb::after{
  content:'';position:absolute;inset:-3px;
  border-radius:50%;border:1px solid rgba(155,93,229,.2);
  animation:orbRing 3s linear infinite;
}
@keyframes orbRing{from{transform:rotate(0)}to{transform:rotate(360deg)}}

.logo-texts{}
.logo-name{
  font-family:var(--orb);font-size:13px;font-weight:900;
  letter-spacing:3px;
  background:linear-gradient(90deg,var(--pu),var(--cyan),var(--go));
  -webkit-background-clip:text;-webkit-text-fill-color:transparent;
  background-clip:text;
  filter:drop-shadow(0 0 8px rgba(155,93,229,.5));
  animation:nameGlow 4s ease infinite;
}
@keyframes nameGlow{
  0%,100%{filter:drop-shadow(0 0 8px rgba(155,93,229,.5))}
  50%{filter:drop-shadow(0 0 12px rgba(0,240,255,.6))}
}
.logo-sub{
  font-family:var(--exo);font-size:7px;font-weight:200;
  letter-spacing:3px;color:var(--muted);text-transform:uppercase;margin-top:2px;
}

.hdr-right{display:flex;align-items:center;gap:7px}
.live-badge{
  display:flex;align-items:center;gap:5px;
  font-family:var(--exo);font-size:8px;font-weight:700;letter-spacing:2px;
  color:var(--green);
  background:rgba(0,255,157,.06);
  border:1px solid rgba(0,255,157,.2);
  border-radius:3px;padding:5px 10px;
  text-transform:uppercase;
}
.live-dot{
  width:5px;height:5px;border-radius:50%;
  background:var(--green);
  box-shadow:0 0 8px var(--green),0 0 16px rgba(0,255,157,.4);
  animation:livePulse 1.4s ease infinite;
}
@keyframes livePulse{0%,100%{transform:scale(1)}50%{transform:scale(1.6);opacity:.5}}

.api-btn{
  background:linear-gradient(135deg,rgba(155,93,229,.15),rgba(155,93,229,.05));
  border:1px solid rgba(155,93,229,.3);
  color:rgba(155,93,229,.9);
  border-radius:3px;padding:6px 11px;
  font-family:var(--exo);font-size:8px;font-weight:700;
  letter-spacing:2px;cursor:pointer;
  transition:all .2s;text-transform:uppercase;
}
.api-btn:hover{background:rgba(155,93,229,.25);border-color:var(--pu);box-shadow:0 0 12px rgba(155,93,229,.3)}

/* ══ TICKER ══ */
.ticker-wrap{
  background:rgba(0,0,0,.6);
  border-bottom:1px solid rgba(155,93,229,.1);
  height:26px;overflow:hidden;
  display:flex;align-items:center;
  position:relative;flex-shrink:0;
}
.ticker-wrap::before,.ticker-wrap::after{
  content:'';position:absolute;top:0;bottom:0;width:32px;z-index:2;
}
.ticker-wrap::before{left:0;background:linear-gradient(90deg,rgba(3,5,13,1),transparent)}
.ticker-wrap::after{right:0;background:linear-gradient(-90deg,rgba(3,5,13,1),transparent)}
.ticker-track{
  display:flex;gap:24px;white-space:nowrap;
  padding:0 16px;
  transition:none;
}
.ticker-track.anim{animation:tickScroll 35s linear infinite}
@keyframes tickScroll{from{transform:translateX(0)}to{transform:translateX(-50%)}}
.ti{display:inline-flex;gap:5px;align-items:center;font-size:9px;cursor:default}
.ti-sym{color:var(--muted)}
.ti-val{font-family:var(--orb);font-size:9px}
.ti-up{color:var(--green);text-shadow:0 0 4px rgba(0,255,157,.4)}
.ti-dn{color:var(--red);text-shadow:0 0 4px rgba(255,34,85,.4)}
.ti-chg{font-size:8px;opacity:.7}
.ti-loading{font-family:var(--exo);font-size:8px;color:var(--muted);letter-spacing:2px;padding:0 12px;animation:blink 1.2s ease infinite}
@keyframes blink{0%,100%{opacity:.4}50%{opacity:1}}

/* ══ PAGES ══ */
.pages{flex:1;overflow:hidden;position:relative}
.page{
  position:absolute;inset:0;
  overflow-y:auto;overflow-x:hidden;
  padding:16px 15px calc(var(--safe-bot) + 72px);
  display:none;
  -webkit-overflow-scrolling:touch;
  scrollbar-width:none;
}
.page::-webkit-scrollbar{display:none}
.page.on{display:block;animation:pgIn .4s cubic-bezier(.16,1,.3,1)}
@keyframes pgIn{from{opacity:0;transform:translateY(14px)}to{opacity:1;transform:translateY(0)}}

/* ══ BOTTOM NAV ══ */
.bnav{
  flex-shrink:0;
  display:grid;grid-template-columns:repeat(4,1fr);
  background:rgba(3,5,13,.97);
  border-top:1px solid rgba(155,93,229,.15);
  padding-bottom:var(--safe-bot);
  position:relative;
}
.bnav::before{
  content:'';position:absolute;top:-1px;left:0;right:0;height:1px;
  background:linear-gradient(90deg,transparent,rgba(155,93,229,.4),rgba(0,240,255,.3),transparent);
}
.nb{
  display:flex;flex-direction:column;align-items:center;justify-content:center;
  gap:3px;padding:9px 4px;
  background:none;border:none;
  color:var(--muted);
  font-family:var(--exo);font-size:7.5px;font-weight:700;
  letter-spacing:1.5px;text-transform:uppercase;
  cursor:pointer;transition:all .25s;position:relative;overflow:hidden;
}
.nb .ni{font-size:17px;line-height:1;transition:all .3s;filter:grayscale(1) brightness(.3)}
.nb.on{color:var(--pu)}
.nb.on .ni{filter:grayscale(0) drop-shadow(0 0 6px var(--pu)) brightness(1.2)}
.nb.on::after{
  content:'';position:absolute;top:0;left:15%;right:15%;height:2px;
  background:linear-gradient(90deg,var(--pu),var(--cyan));
  border-radius:0 0 2px 2px;
  box-shadow:0 0 10px var(--pu),0 0 20px rgba(155,93,229,.4);
}
.nb::before{
  content:'';position:absolute;inset:0;
  background:radial-gradient(ellipse at 50% 0%,rgba(155,93,229,.08),transparent 60%);
  opacity:0;transition:.3s;
}
.nb.on::before{opacity:1}
/* Ripple on tap */
.nb:active::after{animation:none}
.nb:active{transform:scale(.92)}

/* ══ SHARED UI ══ */
.sh{
  display:flex;align-items:center;gap:8px;
  font-family:var(--exo);font-size:8px;font-weight:700;
  letter-spacing:4px;color:var(--muted);
  text-transform:uppercase;margin-bottom:14px;
}
.sh .sh-ico{
  font-family:var(--orb);font-size:9px;color:var(--pu);
  opacity:.7;
}
.sh::after{content:'';flex:1;height:1px;background:linear-gradient(90deg,var(--b1),transparent)}

.card{
  background:linear-gradient(135deg,rgba(11,20,37,.9),rgba(7,13,26,.95));
  border:1px solid var(--b1);
  border-radius:12px;overflow:hidden;
  margin-bottom:12px;position:relative;
  transition:border-color .3s,box-shadow .3s;
}
.card::before{
  content:'';position:absolute;inset:0;
  background:linear-gradient(135deg,rgba(155,93,229,.03),transparent 50%,rgba(0,240,255,.02));
  pointer-events:none;
}
.card:hover{border-color:var(--b2)}
.cp{padding:14px 15px}

/* Primary button */
.btn{
  width:100%;padding:15px;border:none;border-radius:8px;
  font-family:var(--orb);font-size:10px;font-weight:700;
  letter-spacing:4px;text-transform:uppercase;
  cursor:pointer;transition:all .2s;
  position:relative;overflow:hidden;
}
.btn:active{transform:scale(.97)}
.btn-p{
  background:linear-gradient(135deg,var(--pu3),var(--pu2),var(--pu));
  color:#fff;
  box-shadow:0 0 24px rgba(155,93,229,.35),0 4px 16px rgba(0,0,0,.4);
}
.btn-p::before{
  content:'';position:absolute;top:0;left:-100%;
  width:50%;height:100%;
  background:linear-gradient(90deg,transparent,rgba(255,255,255,.18),transparent);
  transform:skewX(-15deg);
  animation:btnSheen 3.5s ease infinite 1s;
}
@keyframes btnSheen{0%,60%{left:-100%}100%{left:180%}}
.btn-p:disabled{
  background:linear-gradient(135deg,var(--s2,#0b1425),var(--s1,#070d1a));
  color:var(--muted);box-shadow:none;cursor:not-allowed;
}
.btn-p:disabled::before{display:none}
.btn-p:not(:disabled):hover{box-shadow:0 0 32px rgba(155,93,229,.5),0 4px 20px rgba(0,0,0,.5)}

/* Inputs */
.inp{
  width:100%;
  background:rgba(0,0,0,.5);
  border:1px solid var(--b1);
  border-radius:7px;
  padding:12px 13px;
  font-family:var(--mono);font-size:11.5px;
  color:var(--text);outline:none;
  transition:border-color .2s,box-shadow .2s;
  -webkit-appearance:none;
}
.inp:focus{
  border-color:rgba(155,93,229,.5);
  box-shadow:0 0 0 3px rgba(155,93,229,.08),0 0 16px rgba(155,93,229,.1);
}
.inp::placeholder{color:var(--muted)}
select.inp option{background:#0b1425}

.lbl{
  display:block;font-family:var(--exo);font-size:8px;font-weight:700;
  letter-spacing:2px;text-transform:uppercase;
  color:var(--muted);margin-bottom:6px;
}

/* Loader */
.lbar{width:100%;height:2px;background:var(--b1);border-radius:1px;overflow:hidden;display:none}
.lbar.on{display:block}
.lbar-fill{
  height:100%;width:30%;
  background:linear-gradient(90deg,var(--pu3),var(--pu),var(--cyan));
  animation:lrun 1s ease-in-out infinite;
  box-shadow:0 0 10px var(--pu);
}
@keyframes lrun{from{transform:translateX(-200%)}to{transform:translateX(500%)}}
.ltxt{
  font-family:var(--exo);font-size:9px;font-weight:700;letter-spacing:3px;
  color:var(--pu);text-align:center;
  padding:8px 0;display:none;text-transform:uppercase;
  animation:ltxtFlash .8s ease infinite;
}
@keyframes ltxtFlash{0%,100%{opacity:.6}50%{opacity:1}}
.ltxt.on{display:block}

/* Error box */
.errbox{
  background:rgba(255,34,85,.06);
  border:1px solid rgba(255,34,85,.2);
  border-radius:7px;padding:11px 14px;
  font-size:10px;color:var(--red);
  display:none;margin-bottom:10px;
  animation:shake .3s ease;
}
@keyframes shake{0%,100%{transform:translateX(0)}25%{transform:translateX(-5px)}75%{transform:translateX(5px)}}
.errbox.on{display:block}

/* Tags */
.tag-buy{color:var(--green);text-shadow:0 0 8px rgba(0,255,157,.5)}
.tag-sell{color:var(--red);text-shadow:0 0 8px rgba(255,34,85,.5)}
.tag-hold{color:var(--go);text-shadow:0 0 6px rgba(244,196,48,.4)}

.badge{
  font-family:var(--exo);font-size:8px;font-weight:700;
  padding:3px 8px;border-radius:3px;letter-spacing:1px;
  text-transform:uppercase;
}
.b-buy{background:rgba(0,255,157,.08);color:var(--green);border:1px solid rgba(0,255,157,.2)}
.b-sell{background:rgba(255,34,85,.08);color:var(--red);border:1px solid rgba(255,34,85,.2)}
.b-hold{background:rgba(244,196,48,.06);color:var(--go);border:1px solid rgba(244,196,48,.15)}

/* ══ PAGE 1 · ANALYZE ══ */
.dropzone{
  border:1px dashed var(--b2);border-radius:12px;
  background:linear-gradient(135deg,rgba(155,93,229,.03),rgba(0,0,0,.4));
  padding:24px 20px;text-align:center;cursor:pointer;
  transition:all .3s;position:relative;overflow:hidden;
  margin-bottom:12px;
}
.dropzone::after{
  content:'';position:absolute;inset:0;
  background:conic-gradient(from 0deg,transparent,rgba(155,93,229,.06) 60deg,transparent 120deg,rgba(0,240,255,.04) 180deg,transparent 240deg,rgba(244,196,48,.04) 300deg,transparent);
  opacity:0;transition:opacity .3s;
  animation:conicSpin 5s linear infinite;
}
@keyframes conicSpin{from{transform:rotate(0)}to{transform:rotate(360deg)}}
.dropzone:hover::after,.dropzone.drag::after{opacity:1}
.dropzone:hover,.dropzone.drag{
  border-color:rgba(155,93,229,.4);
  box-shadow:0 0 28px rgba(155,93,229,.1),inset 0 0 20px rgba(155,93,229,.03);
}
.dropzone.has{border-style:solid;border-color:rgba(155,93,229,.3);padding:0}
.dz-ico{font-size:28px;margin-bottom:8px;filter:drop-shadow(0 0 8px rgba(155,93,229,.6))}
.dz-title{
  font-family:var(--orb);font-size:10px;letter-spacing:3px;
  color:var(--text);margin-bottom:4px;
}
.dz-hint{font-family:var(--exo);font-size:9px;color:var(--muted);letter-spacing:1px}

.prev-img{width:100%;max-height:220px;object-fit:contain;border-radius:11px;display:block}
.prev-bar{
  position:absolute;bottom:0;left:0;right:0;
  background:linear-gradient(transparent,rgba(0,0,0,.92));
  padding:10px 14px;
  display:flex;justify-content:space-between;align-items:center;
  border-radius:0 0 11px 11px;
}
.prev-lbl{font-family:var(--exo);font-size:8px;letter-spacing:2px;color:var(--pu)}
.prev-chg{
  font-size:9px;color:var(--muted);cursor:pointer;
  padding:4px 10px;border:1px solid var(--b2);
  border-radius:4px;background:rgba(255,255,255,.03);
  font-family:var(--exo);
}

/* Result card */
.res-card{display:none;margin-bottom:12px}
.res-card.on{display:block;animation:resIn .6s cubic-bezier(.16,1,.3,1)}
@keyframes resIn{
  from{opacity:0;transform:translateY(24px) scale(.97)}
  to{opacity:1;transform:translateY(0) scale(1)}
}
.res-hdr{
  padding:16px;
  display:flex;align-items:center;justify-content:space-between;
  border-radius:12px 12px 0 0;
  position:relative;overflow:hidden;
}
.res-hdr.rh-buy{
  background:linear-gradient(135deg,rgba(0,255,157,.12),rgba(0,255,157,.03));
  border:1px solid rgba(0,255,157,.25);border-bottom:none;
}
.res-hdr.rh-sell{
  background:linear-gradient(135deg,rgba(255,34,85,.12),rgba(255,34,85,.03));
  border:1px solid rgba(255,34,85,.25);border-bottom:none;
}
.res-hdr.rh-hold{
  background:linear-gradient(135deg,rgba(244,196,48,.1),rgba(244,196,48,.02));
  border:1px solid rgba(244,196,48,.2);border-bottom:none;
}
.res-sig{
  font-family:var(--bbn);font-size:42px;letter-spacing:6px;
  display:flex;align-items:center;gap:10px;line-height:1;
}
.res-arrow{font-size:32px;animation:arrowBounce 1.5s ease infinite}
@keyframes arrowBounce{0%,100%{transform:translateY(0)}50%{transform:translateY(-4px)}}
.res-conf{
  font-family:var(--orb);font-size:10px;font-weight:700;
  letter-spacing:2px;padding:8px 14px;border-radius:6px;
  position:relative;overflow:hidden;
}
.res-conf::before{
  content:'';position:absolute;inset:0;
  background:linear-gradient(135deg,rgba(255,255,255,.06),transparent);
}
.rc-buy{background:rgba(0,255,157,.1);color:var(--green);border:1px solid rgba(0,255,157,.25)}
.rc-sell{background:rgba(255,34,85,.1);color:var(--red);border:1px solid rgba(255,34,85,.25)}
.rc-hold{background:rgba(244,196,48,.08);color:var(--go);border:1px solid rgba(244,196,48,.2)}

.res-body{
  padding:14px 15px;
  background:linear-gradient(180deg,rgba(11,20,37,.95),rgba(7,13,26,.98));
  border:1px solid var(--b1);border-top:none;
  border-radius:0 0 12px 12px;
}
.res-txt{
  font-family:var(--exo);font-size:12.5px;font-weight:300;
  line-height:1.85;color:var(--text2);margin-bottom:14px;
}

.mets{display:grid;grid-template-columns:1fr 1fr;gap:8px}
.met{
  background:rgba(0,0,0,.4);border:1px solid var(--b1);
  border-radius:8px;padding:11px 13px;
  transition:all .2s;position:relative;overflow:hidden;
}
.met::before{
  content:'';position:absolute;top:0;left:0;right:0;height:1px;
  background:linear-gradient(90deg,transparent,rgba(155,93,229,.2),transparent);
}
.met:hover{border-color:var(--b2);transform:translateY(-1px)}
.met-l{
  font-family:var(--exo);font-size:7px;font-weight:700;
  letter-spacing:2px;color:var(--muted);margin-bottom:5px;text-transform:uppercase;
}
.met-v{font-family:var(--orb);font-size:12px;color:var(--text)}
.met-v.u{color:var(--green)}
.met-v.d{color:var(--red)}

/* Grade badge */
.grade-wrap{
  display:flex;align-items:center;justify-content:center;
  margin-top:14px;gap:10px;
}
.grade{
  font-family:var(--bbn);font-size:48px;letter-spacing:4px;
  background:linear-gradient(135deg,var(--go),var(--pu));
  -webkit-background-clip:text;-webkit-text-fill-color:transparent;
  filter:drop-shadow(0 0 12px rgba(244,196,48,.4));
  line-height:1;
}
.grade-info{font-family:var(--exo);font-size:10px;font-weight:700;color:var(--text2);letter-spacing:1px}

/* History */
.hist-row{
  display:flex;align-items:center;gap:10px;
  padding:11px 14px;
  border-bottom:1px solid rgba(0,0,0,.3);
  transition:background .2s;
}
.hist-row:last-child{border-bottom:none}
.hist-row:hover{background:rgba(155,93,229,.02)}
.hdot{width:7px;height:7px;border-radius:50%;flex-shrink:0}
.hd-buy{background:var(--green);box-shadow:0 0 6px var(--green)}
.hd-sell{background:var(--red);box-shadow:0 0 6px var(--red)}
.hd-hold{background:var(--go)}
.hi{flex:1;min-width:0}
.hi-s{font-family:var(--orb);font-size:10px;letter-spacing:1px}
.hi-t{font-family:var(--exo);font-size:8px;color:var(--muted);margin-top:2px;letter-spacing:1px}
.hi-c{font-family:var(--orb);font-size:9px;color:var(--muted)}

/* ══ PAGE 2 · LIVE SIGNALS ══ */
.mkt-strip{
  display:flex;gap:8px;overflow-x:auto;margin-bottom:14px;
  padding-bottom:2px;scrollbar-width:none;
}
.mkt-strip::-webkit-scrollbar{display:none}
.mkt-tile{
  flex-shrink:0;
  background:linear-gradient(135deg,rgba(11,20,37,.9),rgba(7,13,26,.95));
  border:1px solid var(--b1);border-radius:8px;
  padding:10px 12px;min-width:100px;
  transition:all .2s;
  animation:tileIn .4s ease backwards;
}
.mkt-tile:hover{border-color:var(--b2);transform:translateY(-2px)}
@keyframes tileIn{from{opacity:0;transform:translateY(8px)}to{opacity:1;transform:translateY(0)}}
.mt-sym{font-family:var(--orb);font-size:9px;letter-spacing:1px;color:var(--text2);margin-bottom:4px}
.mt-price{font-family:var(--orb);font-size:13px;color:var(--text);font-weight:700}
.mt-chg{font-family:var(--exo);font-size:9px;font-weight:700;margin-top:2px;letter-spacing:.5px}
.mt-up{color:var(--green)}
.mt-dn{color:var(--red)}

.topbar{display:flex;align-items:center;justify-content:space-between;margin-bottom:12px}
.upd-lbl{font-family:var(--exo);font-size:8px;letter-spacing:2px;color:var(--muted);text-transform:uppercase}
.rfbtn{
  background:linear-gradient(135deg,rgba(155,93,229,.12),rgba(155,93,229,.04));
  border:1px solid rgba(155,93,229,.25);color:var(--pu);
  border-radius:4px;padding:7px 13px;
  font-family:var(--exo);font-size:8px;font-weight:700;
  letter-spacing:2px;cursor:pointer;transition:all .2s;text-transform:uppercase;
}
.rfbtn:hover{background:rgba(155,93,229,.2);box-shadow:0 0 12px rgba(155,93,229,.2)}

.chips{display:flex;gap:5px;margin-bottom:12px;overflow-x:auto;padding-bottom:2px;scrollbar-width:none}
.chips::-webkit-scrollbar{display:none}
.chip{
  flex-shrink:0;
  background:rgba(11,20,37,.9);border:1px solid var(--b1);
  color:var(--muted);border-radius:3px;
  padding:6px 12px;
  font-family:var(--exo);font-size:8px;font-weight:700;
  cursor:pointer;letter-spacing:2px;transition:all .2s;
  white-space:nowrap;text-transform:uppercase;
}
.chip.on{
  border-color:rgba(155,93,229,.4);color:var(--pu);
  background:rgba(155,93,229,.08);
  box-shadow:0 0 10px rgba(155,93,229,.15);
}

.sigcard{
  background:linear-gradient(135deg,rgba(11,20,37,.95),rgba(7,13,26,.98));
  border:1px solid var(--b1);border-radius:10px;
  padding:13px 14px;margin-bottom:8px;
  display:flex;align-items:flex-start;gap:11px;
  transition:all .25s;
  animation:sigIn .45s cubic-bezier(.16,1,.3,1) backwards;
  position:relative;overflow:hidden;
}
.sigcard::before{
  content:'';position:absolute;left:0;top:0;bottom:0;width:2px;
}
.sigcard.sc-buy::before{background:linear-gradient(180deg,transparent,var(--green),transparent)}
.sigcard.sc-sell::before{background:linear-gradient(180deg,transparent,var(--red),transparent)}
.sigcard.sc-hold::before{background:linear-gradient(180deg,transparent,var(--go),transparent)}
.sigcard:hover{border-color:var(--b2);transform:translateX(2px)}
@keyframes sigIn{from{opacity:0;transform:translateX(-10px)}to{opacity:1;transform:translateX(0)}}

.sdot{width:9px;height:9px;border-radius:50%;flex-shrink:0;margin-top:4px}
.sd-buy{background:var(--green);box-shadow:0 0 10px var(--green),0 0 20px rgba(0,255,157,.25)}
.sd-sell{background:var(--red);box-shadow:0 0 10px var(--red),0 0 20px rgba(255,34,85,.25)}
.sd-hold{background:var(--go);box-shadow:0 0 8px var(--go)}

.smid{flex:1;min-width:0}
.sp{font-family:var(--orb);font-size:12.5px;letter-spacing:1px;margin-bottom:3px;display:flex;align-items:center;gap:6px}
.sr{font-family:var(--exo);font-size:10px;font-weight:300;color:var(--text2);line-height:1.5;margin-bottom:5px}
.s-levels{display:flex;gap:8px;flex-wrap:wrap}
.s-lev{font-family:var(--mono);font-size:9px;color:var(--muted)}
.s-lev span{color:var(--text2)}

.cbar{height:3px;background:var(--b1);border-radius:2px;overflow:hidden;margin-top:6px}
.cfill{height:100%;border-radius:2px;transition:width .9s cubic-bezier(.16,1,.3,1)}
.cf-buy{background:linear-gradient(90deg,var(--green2,#00cc7a),var(--green));box-shadow:0 0 4px var(--green)}
.cf-sell{background:linear-gradient(90deg,#cc1a44,var(--red));box-shadow:0 0 4px var(--red)}
.cf-hold{background:var(--go)}

.sright{text-align:right;flex-shrink:0}
.sa{font-family:var(--bbn);font-size:24px;letter-spacing:2px;line-height:1;margin-bottom:3px}
.stf{font-family:var(--exo);font-size:8px;font-weight:700;color:var(--muted);letter-spacing:1px}
.sc{font-family:var(--orb);font-size:8px;color:var(--muted)}

/* ══ PAGE 3 · RISK ══ */
.presets{display:flex;gap:7px;margin-bottom:14px}
.pst{
  flex:1;padding:10px 4px;
  border:1px solid var(--b1);border-radius:7px;
  background:rgba(11,20,37,.8);color:var(--text2);
  font-family:var(--exo);font-size:10px;font-weight:700;
  cursor:pointer;text-align:center;transition:all .2s;letter-spacing:.5px;
}
.pst:hover{border-color:rgba(155,93,229,.3);color:var(--pu);background:rgba(155,93,229,.05)}
.pst:active{transform:scale(.95)}

.rgrid{display:grid;grid-template-columns:1fr 1fr;gap:10px;margin-bottom:12px}

.rout{
  background:linear-gradient(135deg,rgba(11,20,37,.95),rgba(7,13,26,.98));
  border:1px solid var(--b1);border-radius:10px;
  overflow:hidden;display:none;margin-bottom:12px;
}
.rout.on{display:block;animation:pgIn .3s ease}
.rrow{
  padding:12px 15px;
  display:flex;justify-content:space-between;align-items:center;
  border-bottom:1px solid rgba(0,0,0,.3);
}
.rrow:last-child{border-bottom:none}
.rk{font-family:var(--exo);font-size:8px;font-weight:700;letter-spacing:2px;color:var(--muted);text-transform:uppercase}
.rv{font-family:var(--orb);font-size:13px;color:var(--text);font-weight:700;letter-spacing:1px}
.rv.ok{color:var(--green)}
.rv.wn{color:var(--go)}
.rv.bd{color:var(--red)}
.rmwrap{padding:0 15px 13px}
.rmet{width:100%;height:6px;background:var(--b1);border-radius:3px;overflow:hidden}
.rfil{height:100%;border-radius:3px;transition:width .6s cubic-bezier(.16,1,.3,1),background .5s}

.rwarn{
  background:rgba(244,196,48,.05);border:1px solid rgba(244,196,48,.15);
  border-radius:7px;padding:11px 14px;
  font-family:var(--exo);font-size:10px;font-weight:400;
  color:var(--go);line-height:1.7;
  display:none;margin-bottom:10px;
}
.rwarn.on{display:block;animation:pgIn .3s ease}

.guide{
  background:linear-gradient(135deg,rgba(11,20,37,.9),rgba(7,13,26,.95));
  border:1px solid var(--b1);border-radius:10px;padding:15px;
}
.guide p{
  font-family:var(--exo);font-size:10px;font-weight:300;
  color:var(--muted);line-height:2;
}
.guide b{color:var(--text2);font-weight:700}

/* ══ PAGE 4 · SETTINGS ══ */
.stat-row{display:grid;grid-template-columns:repeat(3,1fr);gap:8px;margin-bottom:14px}
.stat-box{
  background:linear-gradient(135deg,rgba(11,20,37,.9),rgba(7,13,26,.95));
  border:1px solid var(--b1);border-radius:10px;
  padding:14px 8px;text-align:center;
  position:relative;overflow:hidden;
}
.stat-box::before{
  content:'';position:absolute;inset:0;
  background:radial-gradient(ellipse at 50% 0%,rgba(155,93,229,.05),transparent 60%);
}
.stat-n{
  font-family:var(--bbn);font-size:30px;letter-spacing:2px;
  line-height:1;color:var(--pu);
}
.stat-l{font-family:var(--exo);font-size:7px;font-weight:700;letter-spacing:2px;color:var(--muted);text-transform:uppercase;margin-top:5px}

.setcard{
  background:linear-gradient(135deg,rgba(11,20,37,.9),rgba(7,13,26,.95));
  border:1px solid var(--b1);border-radius:10px;margin-bottom:10px;overflow:hidden;
}
.sset{
  display:flex;align-items:center;justify-content:space-between;
  padding:13px 15px;border-bottom:1px solid rgba(0,0,0,.3);
}
.sset:last-child{border-bottom:none}
.sfull{padding:13px 15px;border-bottom:1px solid rgba(0,0,0,.3)}
.sfull:last-child{border-bottom:none}
.stit{font-family:var(--exo);font-size:12px;font-weight:700;letter-spacing:.5px}
.sdesc{font-family:var(--mono);font-size:8px;color:var(--muted);margin-top:2px;letter-spacing:.5px}
.sdesc.ok{color:var(--green)}
.sdesc.bd{color:var(--red)}

.tog{
  width:42px;height:23px;border-radius:12px;
  background:var(--b1);border:1px solid var(--b2);
  cursor:pointer;position:relative;flex-shrink:0;
  transition:background .25s,border-color .25s,box-shadow .25s;
}
.tog.on{
  background:rgba(155,93,229,.4);
  border-color:rgba(155,93,229,.5);
  box-shadow:0 0 10px rgba(155,93,229,.25);
}
.tog::after{
  content:'';position:absolute;top:2.5px;left:2.5px;
  width:17px;height:17px;border-radius:50%;
  background:#fff;transition:transform .25s;
  box-shadow:0 1px 4px rgba(0,0,0,.4);
}
.tog.on::after{transform:translateX(19px)}

.kprev{
  font-family:var(--mono);font-size:10px;color:var(--muted);
  background:rgba(0,0,0,.4);border:1px solid var(--b1);
  border-radius:6px;padding:8px 12px;
  word-break:break-all;margin-top:8px;letter-spacing:.5px;
}
.delbtn{
  width:100%;padding:13px;
  border:1px solid rgba(255,34,85,.2);border-radius:7px;
  background:rgba(255,34,85,.04);color:var(--red);
  font-family:var(--exo);font-size:10px;font-weight:700;
  cursor:pointer;letter-spacing:2px;margin-bottom:8px;
  transition:all .2s;text-transform:uppercase;
}
.delbtn:hover{background:rgba(255,34,85,.08)}

/* ══ MODALS ══ */
.overlay{
  position:fixed;inset:0;z-index:500;
  background:rgba(0,0,0,.92);
  display:flex;align-items:flex-end;
  backdrop-filter:blur(8px);
  -webkit-backdrop-filter:blur(8px);
}
.overlay.hide{display:none}
.sheet{
  width:100%;
  background:linear-gradient(180deg,rgba(15,28,52,.98),rgba(7,13,26,.99));
  border-top:1px solid rgba(155,93,229,.2);
  border-radius:20px 20px 0 0;
  padding:20px 18px calc(var(--safe-bot) + 28px);
  animation:sheetUp .35s cubic-bezier(.16,1,.3,1);
  position:relative;overflow:hidden;
}
.sheet::before{
  content:'';position:absolute;top:0;left:0;right:0;height:1px;
  background:linear-gradient(90deg,transparent,var(--pu),var(--cyan),var(--go),transparent);
  box-shadow:0 0 16px var(--pu);
}
.sheet::after{
  content:'';position:absolute;inset:0;
  background:radial-gradient(ellipse at 50% 0%,rgba(155,93,229,.04),transparent 60%);
  pointer-events:none;
}
@keyframes sheetUp{from{transform:translateY(100%)}to{transform:translateY(0)}}
.sh-handle{width:36px;height:3px;background:var(--b2);border-radius:2px;margin:0 auto 18px}
.sh-title{
  font-family:var(--orb);font-size:12px;font-weight:700;letter-spacing:3px;
  background:linear-gradient(90deg,var(--pu),var(--cyan));
  -webkit-background-clip:text;-webkit-text-fill-color:transparent;
  margin-bottom:6px;
}
.sh-desc{
  font-family:var(--exo);font-size:11px;font-weight:300;
  color:var(--muted);line-height:1.8;margin-bottom:18px;
}
.sh-desc a{color:var(--pu);text-decoration:none}

/* Screen flash */
#flash{
  position:fixed;inset:0;z-index:400;pointer-events:none;
  opacity:0;transition:opacity .1s;
}
#flash.on{animation:screenFlash .7s ease forwards}
@keyframes screenFlash{
  0%{opacity:.15}30%{opacity:.08}100%{opacity:0}
}

/* Grade colors */
.grade-A{color:var(--green)}
.grade-B{color:var(--cyan)}
.grade-C{color:var(--go)}
.grade-D{color:var(--red)}
</style>
</head>
<body>

<!-- 3D Canvas -->
<canvas id="threeCanvas"></canvas>
<!-- 2D FX Canvas -->
<canvas id="fxCanvas"></canvas>
<div class="scanlines"></div>
<div class="vignette"></div>
<div id="flash"></div>

<!-- API SETUP MODAL -->
<div class="overlay hide" id="modalSetup">
  <div class="sheet">
    <div class="sh-handle"></div>
    <div class="sh-title">API CONFIGURATION</div>
    <div class="sh-desc">
      Configure your APIs to enable full functionality.<br><br>
      <b style="color:var(--text)">Gemini Pro (Analysis AI):</b> Free at <a href="https://aistudio.google.com" target="_blank">aistudio.google.com</a><br>
      <b style="color:var(--text)">Marketstack (Live Data):</b> Free at <a href="https://marketstack.com" target="_blank">marketstack.com</a> (100 req/mo free)
    </div>
    <label class="lbl" style="margin-bottom:6px">Gemini API Key</label>
    <input class="inp" id="geminiInp" type="password" placeholder="AIza..." style="margin-bottom:12px"/>
    <label class="lbl" style="margin-bottom:6px">Marketstack API Key (optional)</label>
    <input class="inp" id="mktInp" type="text" placeholder="Your marketstack key..." style="margin-bottom:16px"/>
    <button class="btn btn-p" onclick="saveKeys()">SAVE &amp; INITIALIZE</button>
  </div>
</div>

<div id="app">

  <!-- HEADER -->
  <header>
    <div class="hdr-aurora"></div>
    <div class="hdr-inner">
      <div class="logo-wrap">
        <div class="logo-orb">
          <svg viewBox="0 0 24 24" fill="none" stroke="#9b5de5" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round">
            <polyline points="22 7 13.5 15.5 8.5 10.5 2 17"/>
            <polyline points="16 7 22 7 22 13"/>
          </svg>
        </div>
        <div class="logo-texts">
          <div class="logo-name">CIPHER STRATOS</div>
          <div class="logo-sub">Self-Improving Trading Intelligence</div>
        </div>
      </div>
      <div class="hdr-right">
        <button class="api-btn" onclick="openSetup()">KEYS</button>
        <div class="live-badge"><div class="live-dot"></div>LIVE</div>
      </div>
    </div>
    <!-- TICKER -->
    <div class="ticker-wrap">
      <div class="ticker-track" id="tickerTrack">
        <span class="ti-loading">FETCHING MARKET DATA...</span>
      </div>
    </div>
    <div class="hdr-line"></div>
  </header>

  <!-- PAGES -->
  <div class="pages">

    <!-- PAGE 1: ANALYZE -->
    <div class="page on" id="pg-analyze">
      <div class="sh"><span class="sh-ico">◈</span>Chart Analysis</div>

      <div class="dropzone" id="dz" onclick="triggerFile()" role="button" aria-label="Upload chart screenshot">
        <div class="dz-ico">📊</div>
        <div class="dz-title">UPLOAD CHART SCREENSHOT</div>
        <div class="dz-hint">Tap to select · JPG · PNG · WEBP</div>
        <input type="file" id="fileIn" accept="image/*" onchange="onFile(event)" style="display:none" aria-hidden="true"/>
      </div>

      <button class="btn btn-p" id="anaBtn" onclick="doAnalyze()" disabled aria-label="Analyze chart signal" style="margin-bottom:12px">
        ⚡&nbsp;&nbsp;ANALYZE SIGNAL
      </button>

      <div class="lbar" id="al" role="progressbar" aria-label="Analyzing"><div class="lbar-fill"></div></div>
      <div class="ltxt" id="alt" aria-live="polite">INITIALIZING...</div>
      <div class="errbox" id="aerr" role="alert"></div>

      <div class="res-card" id="resCard" role="region" aria-label="Analysis result">
        <div class="res-hdr" id="resHdr">
          <div class="res-sig" id="resSig" aria-live="polite"></div>
          <div class="res-conf" id="resConf"></div>
        </div>
        <div class="res-body">
          <div class="res-txt" id="resTxt"></div>
          <div class="mets" id="resMet"></div>
          <div class="grade-wrap" id="gradeWrap" style="display:none">
            <div>
              <div class="grade" id="gradeVal"></div>
            </div>
            <div class="grade-info" id="gradeInfo"></div>
          </div>
        </div>
      </div>

      <div class="sh" style="margin-top:4px"><span class="sh-ico">◈</span>Signal History</div>
      <div class="card" id="histCard">
        <div style="text-align:center;padding:20px;font-family:var(--exo);font-size:9px;letter-spacing:2px;color:var(--muted);text-transform:uppercase">No Signals Yet</div>
      </div>
    </div>

    <!-- PAGE 2: LIVE SIGNALS -->
    <div class="page" id="pg-signals">
      <div class="sh"><span class="sh-ico">◈</span>Live Market Data</div>
      <div class="mkt-strip" id="mktStrip">
        <div style="font-family:var(--exo);font-size:9px;color:var(--muted);letter-spacing:2px;padding:10px;text-transform:uppercase">Loading market data...</div>
      </div>

      <div class="topbar">
        <span class="upd-lbl" id="updTime">AWAITING DATA</span>
        <button class="rfbtn" onclick="loadSignals()" aria-label="Refresh signals">↻&nbsp;&nbsp;REFRESH</button>
      </div>

      <div class="chips" id="chips" role="group" aria-label="Signal filters">
        <div class="chip on" onclick="setChip('ALL',this)" role="button" tabindex="0">ALL</div>
        <div class="chip" onclick="setChip('BUY',this)" role="button" tabindex="0">BUY</div>
        <div class="chip" onclick="setChip('SELL',this)" role="button" tabindex="0">SELL</div>
        <div class="chip" onclick="setChip('HOLD',this)" role="button" tabindex="0">HOLD</div>
        <div class="chip" onclick="setChip('FOREX',this)" role="button" tabindex="0">FOREX</div>
        <div class="chip" onclick="setChip('CRYPTO',this)" role="button" tabindex="0">CRYPTO</div>
        <div class="chip" onclick="setChip('METALS',this)" role="button" tabindex="0">METALS</div>
      </div>

      <div class="lbar" id="sl"><div class="lbar-fill"></div></div>
      <div class="ltxt" id="slt">GENERATING SIGNALS...</div>
      <div class="errbox" id="serr" role="alert"></div>
      <div id="sigList" role="list"></div>
    </div>

    <!-- PAGE 3: RISK -->
    <div class="page" id="pg-risk">
      <div class="sh"><span class="sh-ico">◈</span>Risk Calculator</div>
      <div class="presets">
        <div class="pst" onclick="preset('c')" role="button" tabindex="0">🛡 Safe<br><span style="font-size:8px;opacity:.6">1% Risk</span></div>
        <div class="pst" onclick="preset('m')" role="button" tabindex="0">⚖️ Moderate<br><span style="font-size:8px;opacity:.6">2% Risk</span></div>
        <div class="pst" onclick="preset('a')" role="button" tabindex="0">🔥 Aggressive<br><span style="font-size:8px;opacity:.6">5% Risk</span></div>
      </div>
      <div class="rgrid">
        <div><label class="lbl" for="r1">Balance ($)</label><input class="inp" id="r1" type="number" placeholder="10000" oninput="calcRisk()" aria-label="Account balance"/></div>
        <div><label class="lbl" for="r2">Risk %</label><input class="inp" id="r2" type="number" placeholder="2" step="0.1" oninput="calcRisk()" aria-label="Risk percentage"/></div>
        <div><label class="lbl" for="r3">Entry Price</label><input class="inp" id="r3" type="number" placeholder="1.0842" step="any" oninput="calcRisk()" aria-label="Entry price"/></div>
        <div><label class="lbl" for="r4">Stop Loss</label><input class="inp" id="r4" type="number" placeholder="1.0800" step="any" oninput="calcRisk()" aria-label="Stop loss price"/></div>
        <div><label class="lbl" for="r5">Take Profit</label><input class="inp" id="r5" type="number" placeholder="1.0950" step="any" oninput="calcRisk()" aria-label="Take profit price"/></div>
        <div><label class="lbl" for="r6">Lot Type</label>
          <select class="inp" id="r6" onchange="calcRisk()" aria-label="Lot type">
            <option value="100000">Standard (100k)</option>
            <option value="10000">Mini (10k)</option>
            <option value="1000">Micro (1k)</option>
          </select>
        </div>
      </div>

      <div class="rout" id="rOut" aria-live="polite">
        <div class="rrow"><span class="rk">MAX RISK AMOUNT</span><span class="rv" id="rv1">—</span></div>
        <div class="rrow"><span class="rk">RECOMMENDED LOT SIZE</span><span class="rv" id="rv2">—</span></div>
        <div class="rrow"><span class="rk">STOP LOSS (PIPS)</span><span class="rv" id="rv3">—</span></div>
        <div class="rrow"><span class="rk">TAKE PROFIT (PIPS)</span><span class="rv" id="rv4">—</span></div>
        <div class="rrow"><span class="rk">RISK / REWARD RATIO</span><span class="rv" id="rv5">—</span></div>
        <div class="rrow"><span class="rk">EXPOSURE LEVEL</span><span class="rv" id="rv6">—</span></div>
        <div class="rmwrap"><div class="rmet" role="progressbar"><div class="rfil" id="rmeter"></div></div></div>
      </div>
      <div class="rwarn" id="rwarn" role="alert"></div>

      <div class="sh"><span class="sh-ico">◈</span>Position Guide</div>
      <div class="guide">
        <p>
          <b>Lot Size =</b> (Balance × Risk%) ÷ (SL pips × pip value)<br>
          <b>Pip Values (standard lot):</b> EUR/USD · GBP/USD = $10 · USD/JPY ≈ $9.30 · Gold = $10<br>
          <b>Signal Grades:</b> A = 90%+ conf · B = 75–89% · C = 60–74% · D = below 60%<br>
          <b>Risk Rules:</b> ≤1% = Safe · 1–2% = Moderate · >3% = High ⚠ · >5% = Danger ☠
        </p>
      </div>
    </div>

    <!-- PAGE 4: SETTINGS -->
    <div class="page" id="pg-settings">
      <div class="sh"><span class="sh-ico">◈</span>Performance Stats</div>
      <div class="stat-row">
        <div class="stat-box"><div class="stat-n" id="stT">0</div><div class="stat-l">Total</div></div>
        <div class="stat-box"><div class="stat-n" style="color:var(--green)" id="stB">0</div><div class="stat-l">Buys</div></div>
        <div class="stat-box"><div class="stat-n" style="color:var(--red)" id="stS">0</div><div class="stat-l">Sells</div></div>
      </div>

      <div class="sh"><span class="sh-ico">◈</span>API Keys</div>
      <div class="setcard">
        <div class="sset">
          <div><div class="stit">Gemini Pro API</div><div class="sdesc" id="gemDesc">Not configured</div></div>
          <button class="api-btn" onclick="openSetup()">Edit</button>
        </div>
        <div class="sset">
          <div><div class="stit">Marketstack API</div><div class="sdesc" id="mktDesc">Not configured — using fallback data</div></div>
          <button class="api-btn" onclick="openSetup()">Edit</button>
        </div>
        <div class="sfull"><div class="kprev" id="kprev">—</div></div>
      </div>

      <div class="sh"><span class="sh-ico">◈</span>Preferences</div>
      <div class="setcard">
        <div class="sset">
          <div><div class="stit">Auto-Refresh Signals</div><div class="sdesc">Refresh when Signals tab opens</div></div>
          <button class="tog on" id="tog-auto" onclick="togSet('auto',this)" aria-label="Toggle auto-refresh" role="switch"></button>
        </div>
        <div class="sset">
          <div><div class="stit">Sound Alerts</div><div class="sdesc">Audio cue on signal result</div></div>
          <button class="tog" id="tog-sound" onclick="togSet('sound',this)" aria-label="Toggle sound" role="switch"></button>
        </div>
        <div class="sset">
          <div><div class="stit">Show Signal Grade</div><div class="sdesc">Display A/B/C/D grade on results</div></div>
          <button class="tog on" id="tog-grade" onclick="togSet('grade',this)" aria-label="Toggle grade display" role="switch"></button>
        </div>
        <div class="sfull">
          <div class="stit" style="margin-bottom:8px">Default Timeframe</div>
          <select class="inp" id="pref-tf" onchange="savePref('tf',this.value)" aria-label="Default timeframe">
            <option value="M5">M5 – 5 Minutes</option>
            <option value="M15" selected>M15 – 15 Minutes</option>
            <option value="H1">H1 – 1 Hour</option>
            <option value="H4">H4 – 4 Hours</option>
            <option value="D1">D1 – Daily</option>
          </select>
        </div>
      </div>

      <div class="sh"><span class="sh-ico">◈</span>Data</div>
      <div class="setcard">
        <div class="sfull">
          <button class="delbtn" onclick="clearHist()">🗑&nbsp;&nbsp;CLEAR SIGNAL HISTORY</button>
          <button class="delbtn" onclick="resetAll()">⚠&nbsp;&nbsp;RESET ALL SETTINGS</button>
        </div>
      </div>

      <div style="text-align:center;font-family:var(--exo);font-size:8px;font-weight:300;letter-spacing:2px;color:var(--muted);padding:12px 0 4px;line-height:2;text-transform:uppercase">
        Cipher Stratos v2.1 · Dual-API Trading Intelligence<br>
        For informational purposes only · Not financial advice
      </div>
    </div>

  </div><!-- /pages -->

  <!-- BOTTOM NAV -->
  <nav class="bnav" role="navigation" aria-label="Main navigation">
    <button class="nb on" id="nb-analyze" onclick="goPage('analyze')" aria-label="Analyze page" aria-current="page">
      <span class="ni" aria-hidden="true">📊</span>Analyze
    </button>
    <button class="nb" id="nb-signals" onclick="goPage('signals')" aria-label="Live signals page">
      <span class="ni" aria-hidden="true">⚡</span>Signals
    </button>
    <button class="nb" id="nb-risk" onclick="goPage('risk')" aria-label="Risk calculator page">
      <span class="ni" aria-hidden="true">🛡</span>Risk
    </button>
    <button class="nb" id="nb-settings" onclick="goPage('settings')" aria-label="Settings page">
      <span class="ni" aria-hidden="true">⚙️</span>Settings
    </button>
  </nav>

</div><!-- /app -->

<script>
// ═══════════════════════════════════════════════════
//  THREE.JS 3D BACKGROUND — Rotating neural sphere
//  with orbiting rings and energy lines
// ═══════════════════════════════════════════════════
(function init3D(){
  const canvas = document.getElementById('threeCanvas');
  const renderer = new THREE.WebGLRenderer({canvas, alpha:true, antialias:true});
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
  renderer.setClearColor(0x000000, 0);

  const scene = new THREE.Scene();
  const camera = new THREE.PerspectiveCamera(60, window.innerWidth/window.innerHeight, 0.1, 1000);
  camera.position.z = 4;

  function resize3D(){
    renderer.setSize(window.innerWidth, window.innerHeight);
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
  }
  resize3D();
  window.addEventListener('resize', resize3D);

  // — Node sphere (particle constellation) —
  const nodeGeo = new THREE.BufferGeometry();
  const nodeCount = 180;
  const nodePos = new Float32Array(nodeCount * 3);
  for(let i = 0; i < nodeCount; i++){
    const theta = Math.acos(2 * Math.random() - 1);
    const phi   = 2 * Math.PI * Math.random();
    const r = 1.6 + Math.random() * 0.3;
    nodePos[i*3]   = r * Math.sin(theta) * Math.cos(phi);
    nodePos[i*3+1] = r * Math.sin(theta) * Math.sin(phi);
    nodePos[i*3+2] = r * Math.cos(theta);
  }
  nodeGeo.setAttribute('position', new THREE.BufferAttribute(nodePos, 3));
  const nodeMat = new THREE.PointsMaterial({
    color: 0x9b5de5, size: 0.035,
    transparent: true, opacity: 0.7,
    sizeAttenuation: true,
  });
  const nodeMesh = new THREE.Points(nodeGeo, nodeMat);
  scene.add(nodeMesh);

  // — Connection lines between nearby nodes —
  const linePositions = [];
  for(let i = 0; i < nodeCount; i++){
    for(let j = i+1; j < nodeCount; j++){
      const dx = nodePos[i*3]  - nodePos[j*3];
      const dy = nodePos[i*3+1]- nodePos[j*3+1];
      const dz = nodePos[i*3+2]- nodePos[j*3+2];
      const dist = Math.sqrt(dx*dx+dy*dy+dz*dz);
      if(dist < 0.55 && Math.random() > 0.3){
        linePositions.push(
          nodePos[i*3], nodePos[i*3+1], nodePos[i*3+2],
          nodePos[j*3], nodePos[j*3+1], nodePos[j*3+2]
        );
      }
    }
  }
  const lineGeo = new THREE.BufferGeometry();
  lineGeo.setAttribute('position', new THREE.BufferAttribute(new Float32Array(linePositions), 3));
  const lineMat = new THREE.LineBasicMaterial({
    color: 0x5a1fa0, transparent: true, opacity: 0.25
  });
  scene.add(new THREE.LineSegments(lineGeo, lineMat));

  // — Outer wireframe icosahedron —
  const icoGeo = new THREE.IcosahedronGeometry(2.1, 1);
  const icoEdges = new THREE.EdgesGeometry(icoGeo);
  const icoMat = new THREE.LineBasicMaterial({
    color: 0x1a2e55, transparent: true, opacity: 0.35
  });
  const icoMesh = new THREE.LineSegments(icoEdges, icoMat);
  scene.add(icoMesh);

  // — Inner glowing core —
  const coreGeo = new THREE.SphereGeometry(0.18, 16, 16);
  const coreMat = new THREE.MeshBasicMaterial({
    color: 0x9b5de5, transparent: true, opacity: 0.9
  });
  const coreMesh = new THREE.Mesh(coreGeo, coreMat);
  scene.add(coreMesh);

  // — Equatorial ring —
  const ringGeo = new THREE.TorusGeometry(1.8, 0.008, 8, 80);
  const ringMat = new THREE.MeshBasicMaterial({
    color: 0x00f0ff, transparent: true, opacity: 0.3
  });
  const ring1 = new THREE.Mesh(ringGeo, ringMat);
  ring1.rotation.x = Math.PI / 4;
  scene.add(ring1);

  const ring2 = new THREE.Mesh(
    new THREE.TorusGeometry(1.6, 0.005, 8, 80),
    new THREE.MeshBasicMaterial({color:0xf4c430, transparent:true, opacity:0.2})
  );
  ring2.rotation.x = -Math.PI / 3;
  ring2.rotation.z = Math.PI / 5;
  scene.add(ring2);

  // — Ambient floating particles —
  const floatGeo = new THREE.BufferGeometry();
  const floatCount = 120;
  const floatPos = new Float32Array(floatCount * 3);
  for(let i = 0; i < floatCount; i++){
    floatPos[i*3]   = (Math.random() - .5) * 10;
    floatPos[i*3+1] = (Math.random() - .5) * 10;
    floatPos[i*3+2] = (Math.random() - .5) * 5 - 1;
  }
  floatGeo.setAttribute('position', new THREE.BufferAttribute(floatPos, 3));
  const floatMat = new THREE.PointsMaterial({
    color: 0x243d6e, size: 0.022, transparent: true, opacity: 0.6
  });
  scene.add(new THREE.Points(floatGeo, floatMat));

  // — Animation loop —
  let t = 0;
  function animate3D(){
    requestAnimationFrame(animate3D);
    t += 0.003;

    nodeMesh.rotation.y += 0.0018;
    nodeMesh.rotation.x += 0.0006;
    icoMesh.rotation.y  -= 0.0012;
    icoMesh.rotation.x  += 0.0008;
    ring1.rotation.z    += 0.004;
    ring2.rotation.y    += 0.005;

    // Core pulse
    const pulse = 0.85 + 0.15 * Math.sin(t * 2.5);
    coreMesh.scale.setScalar(pulse);
    coreMat.opacity = 0.6 + 0.3 * Math.sin(t * 2.5);

    // Node color oscillation
    const h = (t * 0.05) % 1;
    nodeMat.color.setHSL(0.76 + Math.sin(t * 0.3) * 0.08, 0.8, 0.55);

    renderer.render(scene, camera);
  }
  animate3D();
})();

// ═══════════════════════════════════════════════════
//  2D FX CANVAS — Matrix rain + scan beam + particles
// ═══════════════════════════════════════════════════
(function init2D(){
  const c = document.getElementById('fxCanvas');
  const ctx = c.getContext('2d');
  let W, H;

  function resize(){
    W = c.width  = window.innerWidth;
    H = c.height = window.innerHeight;
  }
  resize();
  window.addEventListener('resize', resize);

  // Matrix columns
  const cols = Math.floor(window.innerWidth / 18);
  const drops = Array.from({length: cols}, () => Math.random() * -100);
  const chars = '01アイウエオカキクケコサシスセソタチツテト∑∆∇⊕∞≈'.split('');

  // Scan beam
  let scanY = 0;

  // Floating data points
  const pts = Array.from({length:40}, () => ({
    x: Math.random()*window.innerWidth,
    y: Math.random()*window.innerHeight,
    vx: (Math.random()-.5)*.3,
    vy: (Math.random()-.5)*.3,
    life: Math.random(),
    maxLife: .3 + Math.random()*.5
  }));

  function fx(){
    // Dim background
    ctx.fillStyle = 'rgba(3,5,13,0.06)';
    ctx.fillRect(0, 0, W, H);

    // Matrix rain
    ctx.font = '11px "DM Mono",monospace';
    drops.forEach((y, i) => {
      if(Math.random() > 0.97){
        const ch = chars[Math.floor(Math.random()*chars.length)];
        const x  = i * 18;
        // Head char brighter
        ctx.fillStyle = 'rgba(155,93,229,0.7)';
        ctx.fillText(ch, x, y);
        // Tail
        ctx.fillStyle = 'rgba(90,31,160,0.2)';
        ctx.fillText(chars[Math.floor(Math.random()*chars.length)], x, y - 16);
        drops[i] += 18;
        if(y > H + 50) drops[i] = -Math.random() * 200;
      }
    });

    // Horizontal scan beam
    scanY = (scanY + .6) % H;
    const sg = ctx.createLinearGradient(0, scanY-30, 0, scanY+30);
    sg.addColorStop(0, 'transparent');
    sg.addColorStop(.5, 'rgba(0,240,255,0.025)');
    sg.addColorStop(1, 'transparent');
    ctx.fillStyle = sg;
    ctx.fillRect(0, scanY-30, W, 60);

    // Floating data points
    pts.forEach(p => {
      p.life += .006;
      p.x += p.vx; p.y += p.vy;
      if(p.life > p.maxLife || p.x<0||p.x>W||p.y<0||p.y>H){
        p.x=Math.random()*W; p.y=Math.random()*H;
        p.life=0; p.vx=(Math.random()-.5)*.3; p.vy=(Math.random()-.5)*.3;
      }
      const a = Math.sin((p.life/p.maxLife)*Math.PI)*0.35;
      ctx.fillStyle = `rgba(0,240,255,${a})`;
      ctx.fillRect(p.x, p.y, 2, 2);
    });

    requestAnimationFrame(fx);
  }
  fx();
})();

// ═══════════════════════════════════════════════════
//  APP STATE
// ═══════════════════════════════════════════════════
let GEMINI_KEY  = localStorage.getItem('cs_gemini') || '';
let MKT_KEY     = localStorage.getItem('cs_mkt')    || '';
let PREFS       = JSON.parse(localStorage.getItem('cs_prefs') || '{"auto":true,"sound":false,"grade":true,"tf":"M15"}');
let HISTORY     = JSON.parse(localStorage.getItem('cs_hist')  || '[]');
let SIGNALS     = []; // generated this session — NOT re-randomized on filter
let MKT_DATA    = {}; // live price data keyed by symbol
let FILTER      = 'ALL';
let imgB64      = null, imgMime = 'image/jpeg';

// ═══════════════════════════════════════════════════
//  BOOT
// ═══════════════════════════════════════════════════
(async function boot(){
  renderHist();
  renderStats();
  applyPrefs();
  await fetchMarketData(); // fetch real prices first
  buildTicker();           // then render ticker
  if(!GEMINI_KEY) openSetup();
  else if(PREFS.auto) loadSignals();
})();

// ═══════════════════════════════════════════════════
//  MARKETSTACK — Real price data
// ═══════════════════════════════════════════════════
// Fallback static prices (used when no MKT key or request fails)
const FALLBACK = {
  'EURUSD':{price:1.0842,chg:0.0021},'GBPUSD':{price:1.2615,chg:-0.0018},
  'USDJPY':{price:157.32,chg:0.45}, 'AUDUSD':{price:0.6542,chg:0.0012},
  'USDCAD':{price:1.3681,chg:-0.0009},'EURGBP':{price:0.8592,chg:-0.0005},
  'XAUUSD':{price:2331.0,chg:8.5},  'XAGUSD':{price:29.85,chg:-0.32},
  'BTCUSD':{price:67240,chg:1240},  'ETHUSD':{price:3512,chg:-88},
  'SOLUSD':{price:152.4,chg:4.2},
};

async function fetchMarketData(){
  // Marketstack free tier supports forex via 'forex' endpoint
  // If no key, use fallback
  if(!MKT_KEY){
    MKT_DATA = {...FALLBACK};
    // Add slight random variation to fallback so it feels live
    Object.keys(MKT_DATA).forEach(k=>{
      const v = MKT_DATA[k];
      const fuzz = v.price * (Math.random()-.5) * 0.0008;
      MKT_DATA[k] = {price: +(v.price + fuzz).toFixed(v.price>100?2:4), chg:v.chg};
    });
    return;
  }
  try{
    // Marketstack tickers (forex pairs use dot notation)
    const symbols = ['EURUSD','GBPUSD','USDJPY','AUDUSD','USDCAD','XAUUSD'];
    const url = `https://api.marketstack.com/v1/tickers?access_key=${MKT_KEY}&symbols=${symbols.map(s=>s+'.FOREX').join(',')}`;
    const res = await fetch(url);
    if(!res.ok) throw new Error('Marketstack error');
    const d = await res.json();
    if(d.data){
      d.data.forEach(item=>{
        const sym = item.symbol.replace('.FOREX','');
        if(item.eod && item.eod.length > 0){
          const latest = item.eod[0];
          MKT_DATA[sym] = {
            price: latest.close,
            chg: +(latest.close - latest.open).toFixed(4)
          };
        }
      });
    }
    // Fill any missing with fallback
    Object.keys(FALLBACK).forEach(k=>{ if(!MKT_DATA[k]) MKT_DATA[k]=FALLBACK[k]; });
  }catch(e){
    console.warn('Marketstack fetch failed, using fallback:', e.message);
    MKT_DATA = {...FALLBACK};
  }
}

// ═══════════════════════════════════════════════════
//  TICKER BUILD
// ═══════════════════════════════════════════════════
function buildTicker(){
  const DISPLAY = [
    {sym:'EUR/USD',key:'EURUSD'},{sym:'GBP/USD',key:'GBPUSD'},{sym:'USD/JPY',key:'USDJPY'},
    {sym:'AUD/USD',key:'AUDUSD'},{sym:'USD/CAD',key:'USDCAD'},{sym:'XAU/USD',key:'XAUUSD'},
    {sym:'XAG/USD',key:'XAGUSD'},{sym:'BTC/USD',key:'BTCUSD'},{sym:'ETH/USD',key:'ETHUSD'},
    {sym:'SOL/USD',key:'SOLUSD'},{sym:'EUR/GBP',key:'EURGBP'},
  ];
  const make = (d) => {
    const data = MKT_DATA[d.key] || FALLBACK[d.key];
    if(!data) return '';
    const up = data.chg >= 0;
    const chgStr = (up?'+':'')+data.chg;
    const valStr = data.price > 100 ? data.price.toLocaleString(undefined,{minimumFractionDigits:2,maximumFractionDigits:2}) : data.price.toFixed(4);
    return `<span class="ti"><span class="ti-sym">${d.sym}</span><span class="ti-val ${up?'ti-up':'ti-dn'}">${up?'▲':'▼'} ${valStr}</span><span class="ti-chg ${up?'ti-up':'ti-dn'}">(${chgStr})</span></span>`;
  };
  // Double for seamless loop
  const items = [...DISPLAY,...DISPLAY].map(make).join('');
  const track = document.getElementById('tickerTrack');
  track.innerHTML = items;
  track.className = 'ticker-track anim';
}

// ═══════════════════════════════════════════════════
//  NAVIGATION
// ═══════════════════════════════════════════════════
function goPage(name){
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('on'));
  document.querySelectorAll('.nb').forEach(b=>{b.classList.remove('on');b.removeAttribute('aria-current');});
  document.getElementById('pg-'+name).classList.add('on');
  const nb = document.getElementById('nb-'+name);
  nb.classList.add('on');
  nb.setAttribute('aria-current','page');
  if(name==='signals' && PREFS.auto && SIGNALS.length===0) loadSignals();
  if(name==='settings'){ renderStats(); applyPrefs(); }
}

// ═══════════════════════════════════════════════════
//  MODAL
// ═══════════════════════════════════════════════════
function openSetup(){
  document.getElementById('geminiInp').value = GEMINI_KEY;
  document.getElementById('mktInp').value    = MKT_KEY;
  document.getElementById('modalSetup').classList.remove('hide');
}
function saveKeys(){
  const g = document.getElementById('geminiInp').value.trim();
  const m = document.getElementById('mktInp').value.trim();
  if(g && !g.startsWith('AIza')){ alert('Gemini key should start with AIza'); return; }
  if(g){ GEMINI_KEY=g; localStorage.setItem('cs_gemini',g); }
  if(m){ MKT_KEY=m;    localStorage.setItem('cs_mkt',m); }
  document.getElementById('modalSetup').classList.add('hide');
  applyPrefs();
  // Refresh market data with new key
  if(m) fetchMarketData().then(buildTicker);
}
document.getElementById('modalSetup').addEventListener('click',function(e){
  if(e.target===this) this.classList.add('hide');
});

// ═══════════════════════════════════════════════════
//  GEMINI PRO API
// ═══════════════════════════════════════════════════
async function geminiPro(parts, system, maxTok=1000){
  if(!GEMINI_KEY) throw new Error('No Gemini key. Tap KEYS to configure.');
  const msgs = [];
  if(system){
    msgs.push({role:'user',  parts:[{text:'[SYSTEM] '+system+'\n\nACKNOWLEDGE with "Understood."'}]});
    msgs.push({role:'model', parts:[{text:'Understood.'}]});
  }
  msgs.push({role:'user', parts});
  const res = await fetch(
    `https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-pro-latest:generateContent?key=${GEMINI_KEY}`,
    {method:'POST', headers:{'Content-Type':'application/json'},
     body: JSON.stringify({
       contents: msgs,
       generationConfig:{temperature:.3, maxOutputTokens:maxTok, topP:.8}
     })}
  );
  if(!res.ok){
    const e = await res.json();
    throw new Error(e.error?.message || `Gemini API error ${res.status}`);
  }
  const d = await res.json();
  return d.candidates?.[0]?.content?.parts?.[0]?.text || '';
}

// ═══════════════════════════════════════════════════
//  SCREEN FLASH
// ═══════════════════════════════════════════════════
function flashScreen(sig){
  const el = document.getElementById('flash');
  el.style.background = sig==='BUY'
    ? 'radial-gradient(ellipse at center,rgba(0,255,157,.15),transparent 70%)'
    : sig==='SELL'
    ? 'radial-gradient(ellipse at center,rgba(255,34,85,.15),transparent 70%)'
    : 'radial-gradient(ellipse at center,rgba(244,196,48,.1),transparent 70%)';
  el.classList.remove('on');
  void el.offsetWidth;
  el.classList.add('on');
}

// ═══════════════════════════════════════════════════
//  PAGE 1 · CHART ANALYSIS
// ═══════════════════════════════════════════════════
function triggerFile(){ document.getElementById('fileIn').click(); }

function onFile(e){
  const f = e.target.files[0]; if(!f) return;
  imgMime = f.type || 'image/jpeg';
  const fr = new FileReader();
  fr.onload = ev => {
    imgB64 = ev.target.result.split(',')[1];
    const dz = document.getElementById('dz');
    dz.className = 'dropzone has';
    dz.onclick = null;
    dz.innerHTML = `
      <img class="prev-img" src="${ev.target.result}" alt="Uploaded chart"/>
      <div class="prev-bar">
        <span class="prev-lbl">📈 CHART LOADED</span>
        <span class="prev-chg" onclick="resetImg(event)" role="button" tabindex="0">Change</span>
      </div>`;
    document.getElementById('anaBtn').disabled = false;
    document.getElementById('resCard').classList.remove('on');
    document.getElementById('aerr').classList.remove('on');
  };
  fr.readAsDataURL(f);
}

function resetImg(e){
  e && e.stopPropagation();
  imgB64 = null;
  const dz = document.getElementById('dz');
  dz.className = 'dropzone';
  dz.onclick = triggerFile;
  dz.innerHTML = `<div class="dz-ico">📊</div><div class="dz-title">UPLOAD CHART SCREENSHOT</div><div class="dz-hint">Tap to select · JPG · PNG · WEBP</div><input type="file" id="fileIn" accept="image/*" onchange="onFile(event)" style="display:none" aria-hidden="true"/>`;
  document.getElementById('anaBtn').disabled = true;
}

// Drag & drop support
const dz = document.getElementById('dz');
dz.addEventListener('dragover', e=>{ e.preventDefault(); dz.classList.add('drag'); });
dz.addEventListener('dragleave', ()=> dz.classList.remove('drag'));
dz.addEventListener('drop', e=>{
  e.preventDefault(); dz.classList.remove('drag');
  const f = e.dataTransfer.files[0];
  if(f && f.type.startsWith('image/')){ onFile({target:{files:[f]}}); }
});

const SCAN_MSGS = [
  'INITIALIZING NEURAL ANALYSIS...',
  'SCANNING CHART STRUCTURE...',
  'DETECTING KEY SUPPORT / RESISTANCE...',
  'READING CANDLESTICK PATTERNS...',
  'ANALYZING VOLUME PROFILE...',
  'CALCULATING MOMENTUM INDICATORS...',
  'ASSESSING TREND DIRECTION...',
  'CROSS-REFERENCING MARKET CONDITIONS...',
  'COMPUTING SIGNAL CONFIDENCE...',
  'GENERATING SIGNAL...',
];

async function doAnalyze(){
  if(!imgB64) return;
  if(!GEMINI_KEY){ openSetup(); return; }
  const btn = document.getElementById('anaBtn');
  const al=document.getElementById('al'), alt=document.getElementById('alt'), ae=document.getElementById('aerr');
  btn.disabled=true; al.classList.add('on'); alt.classList.add('on'); ae.classList.remove('on');
  document.getElementById('resCard').classList.remove('on');
  let mi=0;
  const iv = setInterval(()=>{ alt.textContent = SCAN_MSGS[mi++ % SCAN_MSGS.length]; }, 800);

  // Include live price context if available
  const priceCtx = Object.entries(MKT_DATA).slice(0,6)
    .map(([k,v])=>`${k}=${v.price}`).join(', ');

  try{
    const raw = await geminiPro([
      {inlineData:{mimeType:imgMime, data:imgB64}},
      {text:`Analyze this trading chart. Current live prices for context: ${priceCtx}. Provide a precise BUY, SELL, or HOLD trading signal.`}
    ],
    `You are a senior quantitative analyst and trading expert with 20 years of experience in forex, crypto, and commodities markets.

Analyze the provided chart screenshot with extreme precision. Consider: trend structure, key S/R levels, candlestick patterns, indicator readings (RSI, MACD, BB, MA if visible), volume, market structure, and the provided live price context.

CRITICAL: Respond ONLY with a single valid JSON object. No markdown. No explanation. No text before or after the JSON.

{
  "signal": "BUY" | "SELL" | "HOLD",
  "confidence": <integer 50-99>,
  "grade": "A" | "B" | "C" | "D",
  "summary": "<3 sentence professional analysis>",
  "entry": "<specific price>",
  "stopLoss": "<specific price>",
  "takeProfit": "<specific price>",
  "timeframe": "<detected or Unknown>",
  "pattern": "<chart pattern or None>",
  "keyLevels": "<support and resistance levels>",
  "riskNote": "<one sentence risk warning>"
}

Grade criteria: A=90%+ conf, B=75-89%, C=60-74%, D<60%.
Be precise with price levels. Never return random values.`
    , 900);

    const clean = raw.replace(/```json|```/g,'').trim();
    const r = JSON.parse(clean);
    showResult(r);
    flashScreen(r.signal);
    if(PREFS.sound) playBeep(r.signal);

  }catch(ex){
    ae.textContent = '⚠ ' + (ex.message || 'Analysis failed. Check your Gemini key and try again.');
    ae.classList.add('on');
  }finally{
    clearInterval(iv);
    btn.disabled = false;
    al.classList.remove('on');
    alt.classList.remove('on');
  }
}

function showResult(r){
  const sig = (r.signal||'HOLD').toUpperCase();
  const cls = sig==='BUY'?'buy':sig==='SELL'?'sell':'hold';
  const arr = sig==='BUY'?'↑':sig==='SELL'?'↓':'→';
  const conf = r.confidence||70;
  const grade = r.grade||'C';

  const card = document.getElementById('resCard');
  card.classList.add('on');

  document.getElementById('resHdr').className = `res-hdr rh-${cls}`;
  document.getElementById('resSig').className = `res-sig tag-${cls}`;
  document.getElementById('resSig').innerHTML =
    `<span class="res-arrow">${arr}</span>&nbsp;${sig}`;

  const cf = document.getElementById('resConf');
  cf.className = `res-conf rc-${cls}`;
  cf.textContent = `${conf}% CONFIDENCE`;

  document.getElementById('resTxt').textContent = r.summary || '';

  // Risk note appended if present
  let riskNote = '';
  if(r.riskNote) riskNote = `<div style="margin-top:10px;padding:8px 12px;background:rgba(244,196,48,.05);border-left:2px solid rgba(244,196,48,.3);font-family:var(--exo);font-size:10px;color:var(--go);line-height:1.5">⚠ ${r.riskNote}</div>`;

  document.getElementById('resMet').innerHTML = `
    <div class="met"><div class="met-l">Entry</div><div class="met-v">${r.entry||'—'}</div></div>
    <div class="met"><div class="met-l">Timeframe</div><div class="met-v">${r.timeframe||'—'}</div></div>
    <div class="met"><div class="met-l">Stop Loss</div><div class="met-v d">${r.stopLoss||'—'}</div></div>
    <div class="met"><div class="met-l">Take Profit</div><div class="met-v u">${r.takeProfit||'—'}</div></div>
    <div class="met"><div class="met-l">Pattern</div><div class="met-v" style="font-size:10px">${r.pattern||'—'}</div></div>
    <div class="met"><div class="met-l">Key Levels</div><div class="met-v" style="font-size:10px">${r.keyLevels||'—'}</div></div>
  ` + riskNote;

  // Grade display
  const gw = document.getElementById('gradeWrap');
  if(PREFS.grade){
    gw.style.display='flex';
    document.getElementById('gradeVal').textContent = grade;
    document.getElementById('gradeVal').className   = `grade grade-${grade}`;
    document.getElementById('gradeInfo').innerHTML  =
      `SIGNAL GRADE<br><span style="font-size:8px;color:var(--muted)">${grade==='A'?'EXCEPTIONAL':grade==='B'?'STRONG':grade==='C'?'MODERATE':'WEAK'} SETUP</span>`;
  } else {
    gw.style.display='none';
  }

  // Save to history
  const now = new Date();
  HISTORY.unshift({
    sig, conf, grade,
    time: now.toLocaleTimeString([],{hour:'2-digit',minute:'2-digit'})+' · '+now.toLocaleDateString(),
    sum: (r.summary||'').substring(0,75),
    entry: r.entry||'—', sl: r.stopLoss||'—', tp: r.takeProfit||'—'
  });
  localStorage.setItem('cs_hist', JSON.stringify(HISTORY.slice(0,50)));
  renderHist();
  renderStats();
  setTimeout(()=> card.scrollIntoView({behavior:'smooth',block:'start'}), 150);
}

function renderHist(){
  const el = document.getElementById('histCard');
  if(!HISTORY.length){
    el.innerHTML='<div style="text-align:center;padding:20px;font-family:var(--exo);font-size:9px;letter-spacing:2px;color:var(--muted);text-transform:uppercase">No Signals Yet</div>';
    return;
  }
  el.innerHTML = HISTORY.slice(0,8).map((h,i)=>`
    <div class="hist-row" style="animation:sigIn .4s ease ${i*.05}s backwards">
      <div class="hdot hd-${h.sig.toLowerCase()}"></div>
      <div class="hi">
        <div class="hi-s tag-${h.sig.toLowerCase()}">
          ${h.sig}
          <span style="font-family:var(--orb);font-size:8px;color:var(--muted)">${h.conf}%</span>
          ${h.grade?`<span class="badge b-${h.sig.toLowerCase()}">${h.grade}</span>`:''}
        </div>
        <div class="hi-t">${h.time}</div>
        ${h.sum?`<div style="font-family:var(--exo);font-size:9px;color:var(--muted);margin-top:2px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis">${h.sum}...</div>`:''}
      </div>
      <div style="text-align:right;flex-shrink:0">
        <div style="font-family:var(--mono);font-size:8px;color:var(--muted)">E: ${h.entry||'—'}</div>
        <div style="font-family:var(--mono);font-size:8px;color:var(--red)">SL: ${h.sl||'—'}</div>
        <div style="font-family:var(--mono);font-size:8px;color:var(--green)">TP: ${h.tp||'—'}</div>
      </div>
    </div>`).join('');
}

// Simple beep
function playBeep(sig){
  try{
    const ctx = new (window.AudioContext||window.webkitAudioContext)();
    const osc = ctx.createOscillator();
    const gain = ctx.createGain();
    osc.connect(gain); gain.connect(ctx.destination);
    osc.frequency.value = sig==='BUY'?880:sig==='SELL'?440:660;
    osc.type = 'sine';
    gain.gain.setValueAtTime(.15, ctx.currentTime);
    gain.gain.exponentialRampToValueAtTime(.001, ctx.currentTime+.4);
    osc.start(); osc.stop(ctx.currentTime+.4);
  }catch(e){}
}

// ═══════════════════════════════════════════════════
//  PAGE 2 · LIVE SIGNALS
// ═══════════════════════════════════════════════════
const PAIRS = [
  {p:'EUR/USD',k:'EURUSD',c:'FOREX'},{p:'GBP/USD',k:'GBPUSD',c:'FOREX'},
  {p:'USD/JPY',k:'USDJPY',c:'FOREX'},{p:'AUD/USD',k:'AUDUSD',c:'FOREX'},
  {p:'USD/CAD',k:'USDCAD',c:'FOREX'},{p:'EUR/GBP',k:'EURGBP',c:'FOREX'},
  {p:'BTC/USD',k:'BTCUSD',c:'CRYPTO'},{p:'ETH/USD',k:'ETHUSD',c:'CRYPTO'},
  {p:'SOL/USD',k:'SOLUSD',c:'CRYPTO'},
  {p:'XAU/USD',k:'XAUUSD',c:'METALS'},{p:'XAG/USD',k:'XAGUSD',c:'METALS'},
];

async function loadSignals(){
  if(!GEMINI_KEY){
    const e=document.getElementById('serr');
    e.textContent='⚠ Gemini API key required. Tap KEYS to configure.';
    e.classList.add('on'); return;
  }
  const sl=document.getElementById('sl'), slt=document.getElementById('slt'), se=document.getElementById('serr');
  sl.classList.add('on'); slt.classList.add('on'); se.classList.remove('on');
  document.getElementById('sigList').innerHTML='';
  document.getElementById('mktStrip').innerHTML='<div style="font-family:var(--exo);font-size:9px;color:var(--muted);letter-spacing:2px;padding:10px">Loading...</div>';

  // Refresh market data first
  await fetchMarketData();
  renderMarketTiles();
  buildTicker();

  // Build price context for ALL pairs
  const priceCtx = PAIRS.map(pair => {
    const d = MKT_DATA[pair.k];
    return d ? `${pair.p}: ${d.price} (${d.chg>=0?'+':''}${d.chg})` : pair.p;
  }).join(' | ');

  try{
    const raw = await geminiPro([{
      text: `Generate trading signals for these pairs using the ACTUAL current market prices: ${priceCtx}. Use ${PREFS.tf} timeframe as default. Analyze each pair's price action and trend.`
    }],
    `You are a professional live trading signal engine. You MUST use the exact price data provided to generate accurate, non-random signals.

For each pair, analyze the price level and change to determine trend direction. Generate realistic, CONSISTENT signals that are directly derived from the price data — do NOT randomize.

Rules:
- Pairs showing positive change and above key levels → bias toward BUY
- Pairs showing negative change and below key levels → bias toward SELL  
- Mixed signals or consolidation → HOLD
- Confidence must reflect actual market conditions (don't cluster all at 70%)
- Use the actual current price as the entry price

Respond ONLY with a JSON array. No markdown. No extra text.

[{"pair":"EUR/USD","category":"FOREX","signal":"BUY","confidence":78,"timeframe":"H1","entry":"1.0842","stopLoss":"1.0800","takeProfit":"1.0950","reason":"<precise 1-sentence technical reason using actual price>"},...]

Generate for ALL ${PAIRS.length} pairs.`, 1400);

    SIGNALS = JSON.parse(raw.replace(/```json|```/g,'').trim());
    renderSignals();
    document.getElementById('updTime').textContent =
      'UPDATED ' + new Date().toLocaleTimeString([],{hour:'2-digit',minute:'2-digit'}) +
      (MKT_KEY ? ' · LIVE DATA' : ' · FALLBACK DATA');

  }catch(ex){
    se.textContent = '⚠ ' + (ex.message || 'Failed to generate signals.');
    se.classList.add('on');
  }finally{
    sl.classList.remove('on'); slt.classList.remove('on');
  }
}

function renderMarketTiles(){
  const strip = document.getElementById('mktStrip');
  strip.innerHTML = PAIRS.map((pair,i)=>{
    const d = MKT_DATA[pair.k];
    if(!d) return '';
    const up = d.chg >= 0;
    const price = d.price > 100
      ? d.price.toLocaleString(undefined,{minimumFractionDigits:2,maximumFractionDigits:2})
      : d.price.toFixed(4);
    const chg = (up?'+':'')+d.chg;
    return `<div class="mkt-tile" style="animation-delay:${i*.05}s">
      <div class="mt-sym">${pair.p}</div>
      <div class="mt-price">${price}</div>
      <div class="mt-chg ${up?'mt-up':'mt-dn'}">${up?'▲':'▼'} ${chg}</div>
    </div>`;
  }).join('');
}

function setChip(f, el){
  FILTER = f;
  document.querySelectorAll('.chip').forEach(c=>c.classList.remove('on'));
  el.classList.add('on');
  renderSignals(); // renders EXISTING signals, no new API call
}

function renderSignals(){
  const list = SIGNALS.filter(s=>{
    if(FILTER==='ALL') return true;
    if(['BUY','SELL','HOLD'].includes(FILTER)) return s.signal===FILTER;
    return s.category===FILTER;
  });
  if(!list.length){
    document.getElementById('sigList').innerHTML=
      '<div style="text-align:center;padding:30px;font-family:var(--exo);font-size:9px;letter-spacing:2px;color:var(--muted);text-transform:uppercase">No signals match this filter</div>';
    return;
  }
  document.getElementById('sigList').innerHTML = list.map((s,i)=>{
    const cls = s.signal==='BUY'?'buy':s.signal==='SELL'?'sell':'hold';
    const arr = s.signal==='BUY'?'↑':s.signal==='SELL'?'↓':'→';
    const conf = s.confidence||70;
    // Get live price if available
    const pairKey = PAIRS.find(p=>p.p===s.pair);
    const livePrice = pairKey && MKT_DATA[pairKey.k] ? MKT_DATA[pairKey.k].price : null;
    const priceLine = livePrice ? `<span class="s-lev">Price: <span>${livePrice}</span></span>` : '';

    return `<div class="sigcard sc-${cls}" role="listitem" style="animation-delay:${i*.07}s">
      <div class="sdot sd-${cls}"></div>
      <div class="smid">
        <div class="sp">${s.pair} <span class="badge b-${cls}">${s.signal}</span></div>
        <div class="sr">${s.reason||''}</div>
        <div class="s-levels">
          ${priceLine}
          ${s.entry?`<span class="s-lev">Entry: <span>${s.entry}</span></span>`:''}
          ${s.stopLoss?`<span class="s-lev" style="color:var(--red)">SL: <span>${s.stopLoss}</span></span>`:''}
          ${s.takeProfit?`<span class="s-lev" style="color:var(--green)">TP: <span>${s.takeProfit}</span></span>`:''}
        </div>
        <div class="cbar"><div class="cfill cf-${cls}" style="width:${conf}%"></div></div>
      </div>
      <div class="sright">
        <div class="sa tag-${cls}">${arr}</div>
        <div class="stf">${s.timeframe||'—'}</div>
        <div class="sc">${conf}%</div>
      </div>
    </div>`;
  }).join('');
}

// ═══════════════════════════════════════════════════
//  PAGE 3 · RISK CALCULATOR
// ═══════════════════════════════════════════════════
function preset(t){
  const m={c:{r2:'1',r6:'10000'},m:{r2:'2',r6:'100000'},a:{r2:'5',r6:'100000'}};
  Object.entries(m[t]).forEach(([id,v])=>{ document.getElementById(id).value=v; });
  calcRisk();
}

function calcRisk(){
  const bal=parseFloat(document.getElementById('r1').value)||0;
  const rp =parseFloat(document.getElementById('r2').value)||0;
  const ent=parseFloat(document.getElementById('r3').value)||0;
  const sl =parseFloat(document.getElementById('r4').value)||0;
  const tp =parseFloat(document.getElementById('r5').value)||0;
  const lot=parseFloat(document.getElementById('r6').value)||100000;
  if(!bal||!rp||!ent||!sl){ document.getElementById('rOut').classList.remove('on'); return; }

  const pv  = 10*(lot/100000);
  const ra  = bal*(rp/100);
  const slp = Math.abs(ent-sl)*10000;
  const tpp = tp ? Math.abs(tp-ent)*10000 : 0;
  const ls  = slp>0 ? ra/(slp*pv) : 0;
  const rr  = tpp&&slp ? tpp/slp : 0;
  const exp = (ra/bal)*100;

  document.getElementById('rv1').textContent = `$${ra.toFixed(2)}`;
  document.getElementById('rv2').textContent = ls>0 ? `${ls.toFixed(2)} lots` : '—';
  document.getElementById('rv3').textContent = slp>0 ? `${slp.toFixed(1)} pips` : '—';
  document.getElementById('rv4').textContent = tpp>0 ? `${tpp.toFixed(1)} pips` : '—';
  document.getElementById('rv5').textContent = rr>0  ? `1 : ${rr.toFixed(2)}` : '—';

  let ec='ok', el='LOW';
  if(exp>3){ec='bd';el='HIGH';}
  else if(exp>1.5){ec='wn';el='MODERATE';}
  document.getElementById('rv6').textContent  = `${exp.toFixed(1)}% — ${el}`;
  document.getElementById('rv6').className    = 'rv '+ec;

  const pct = Math.min(100, exp/5*100);
  const col = exp>3?'#ff2255':exp>1.5?'#f4c430':'#00ff9d';
  document.getElementById('rmeter').style.cssText = `width:${pct}%;background:${col};box-shadow:0 0 8px ${col}`;
  document.getElementById('rOut').classList.add('on');

  const warns=[];
  if(rp>5) warns.push('⚠ Risk over 5% — extreme danger. Professional limit is 1-2%.');
  if(rr>0&&rr<1) warns.push('⚠ Risk/Reward below 1:1 — statistically unprofitable. Aim for 1:2+.');
  if(ls>10) warns.push('⚠ Very large lot size — verify your inputs carefully.');
  if(rp>0&&rp<=1) warns.push('✓ Conservative risk. Good discipline.');
  const w = document.getElementById('rwarn');
  if(warns.length){ w.innerHTML=warns.join('<br>'); w.classList.add('on'); }
  else w.classList.remove('on');
}

// ═══════════════════════════════════════════════════
//  PAGE 4 · SETTINGS
// ═══════════════════════════════════════════════════
function renderStats(){
  document.getElementById('stT').textContent = HISTORY.length;
  document.getElementById('stB').textContent = HISTORY.filter(h=>h.sig==='BUY').length;
  document.getElementById('stS').textContent = HISTORY.filter(h=>h.sig==='SELL').length;
}

function applyPrefs(){
  const gd=document.getElementById('gemDesc'),md=document.getElementById('mktDesc'),kp=document.getElementById('kprev');
  if(gd){
    if(GEMINI_KEY){ gd.textContent='Gemini Pro configured ✓'; gd.className='sdesc ok'; }
    else{ gd.textContent='Not configured'; gd.className='sdesc bd'; }
  }
  if(md){
    if(MKT_KEY){ md.textContent='Marketstack configured ✓ — Live data enabled'; md.className='sdesc ok'; }
    else{ md.textContent='Not configured — using fallback prices'; md.className='sdesc'; }
  }
  if(kp) kp.textContent = GEMINI_KEY ? GEMINI_KEY.slice(0,12)+'••••••'+GEMINI_KEY.slice(-4) : '—';
  ['auto','sound','grade'].forEach(k=>{
    const el=document.getElementById('tog-'+k);
    if(el){ el.className='tog'+(PREFS[k]?' on':''); el.setAttribute('aria-checked',PREFS[k]?'true':'false'); }
  });
  const tf=document.getElementById('pref-tf');
  if(tf) tf.value = PREFS.tf||'M15';
}

function togSet(key, el){
  PREFS[key] = !PREFS[key];
  el.className = 'tog'+(PREFS[key]?' on':'');
  el.setAttribute('aria-checked', PREFS[key]?'true':'false');
  localStorage.setItem('cs_prefs', JSON.stringify(PREFS));
}

function savePref(k, v){ PREFS[k]=v; localStorage.setItem('cs_prefs',JSON.stringify(PREFS)); }

function clearHist(){
  if(!confirm('Clear all signal history?')) return;
  HISTORY.length=0;
  localStorage.removeItem('cs_hist');
  renderHist(); renderStats();
}

function resetAll(){
  if(!confirm('Reset all settings and history? Cannot be undone.')) return;
  localStorage.clear();
  location.reload();
}
</script>
</body>
</html>
