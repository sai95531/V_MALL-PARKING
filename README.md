<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>V MALL — Parking Management v4</title>
<link href="https://fonts.googleapis.com/css2?family=Rajdhani:wght@500;600;700&family=DM+Sans:wght@300;400;500;600&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.0/chart.umd.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
<style>
/* ══════════════════════════════════
   ROOT & SHIFT VARS
══════════════════════════════════ */
:root{
  --bg:#07090f;--surface:#0d1220;--surface2:#141a2a;--surface3:#1a2235;
  --border:rgba(255,255,255,.06);--border2:rgba(255,255,255,.13);
  --text:#e2e8f0;--text-muted:#475569;--text-dim:#94a3b8;
  --amber:#f59e0b;--amber-dim:rgba(245,158,11,.12);
  --blue:#3b82f6;--blue-dim:rgba(59,130,246,.12);
  --green:#10b981;--green-dim:rgba(16,185,129,.12);
  --red:#ef4444;--red-dim:rgba(239,68,68,.12);
  --purple:#8b5cf6;--purple-dim:rgba(139,92,246,.12);
  --cyan:#06b6d4;--cyan-dim:rgba(6,182,212,.12);
  --orange:#f97316;--orange-dim:rgba(249,115,22,.12);
  --gold:#ffd700;--gold-dim:rgba(255,215,0,.12);
  --shift-a:#f59e0b;--shift-b:#fb923c;--shift-glow:rgba(245,158,11,.2);
  --sidebar-w:264px;
}
body.shift-morning{--shift-a:#f59e0b;--shift-b:#fb923c;--shift-glow:rgba(245,158,11,.2);}
body.shift-afternoon{--shift-a:#3b82f6;--shift-b:#f97316;--shift-glow:rgba(59,130,246,.2);}
body.shift-night{--shift-a:#8b5cf6;--shift-b:#3b82f6;--shift-glow:rgba(139,92,246,.2);}

*{margin:0;padding:0;box-sizing:border-box;}
body{font-family:'DM Sans',sans-serif;background:var(--bg);color:var(--text);min-height:100vh;overflow-x:hidden;}
::-webkit-scrollbar{width:4px;height:4px;}
::-webkit-scrollbar-track{background:transparent;}
::-webkit-scrollbar-thumb{background:rgba(255,255,255,.08);border-radius:2px;}

/* ══════════════════════════════════
   PARTICLES & NIGHT SKY
══════════════════════════════════ */
#particle-canvas{position:fixed;inset:0;z-index:0;pointer-events:none;opacity:.55;}
.night-sky-sidebar{display:none;position:absolute;inset:0;overflow:hidden;pointer-events:none;z-index:0;}
body.shift-night .night-sky-sidebar{display:block;}
.night-star{position:absolute;border-radius:50%;background:#fff;animation:starTwinkle var(--dur,3s) infinite var(--delay,0s);}
.night-moon{position:absolute;top:18px;right:18px;width:36px;height:36px;border-radius:50%;background:linear-gradient(135deg,#fef9c3,#fde68a);box-shadow:0 0 18px rgba(254,243,196,.4);animation:moonGlow 4s infinite;}
body.shift-morning .sidebar-brand::after{content:'';position:absolute;bottom:-1px;left:0;right:0;height:2px;background:linear-gradient(90deg,transparent,#f59e0b,transparent);animation:shimmer 2s infinite;}
@keyframes starTwinkle{0%,100%{opacity:1;transform:scale(1);}50%{opacity:.1;transform:scale(.5);}}
@keyframes moonGlow{0%,100%{box-shadow:0 0 18px rgba(254,243,196,.4);}50%{box-shadow:0 0 32px rgba(254,243,196,.7);}}
@keyframes shimmer{0%{background-position:-200px 0;}100%{background-position:200px 0;}}

/* ══════════════════════════════════
   ANIMATIONS
══════════════════════════════════ */
@keyframes slideInUp{from{opacity:0;transform:translateY(18px);}to{opacity:1;transform:translateY(0);}}
@keyframes slideInRight{from{opacity:0;transform:translateX(16px);}to{opacity:1;transform:translateX(0);}}
@keyframes scaleIn{from{opacity:0;transform:scale(.92);}to{opacity:1;transform:scale(1);}}
@keyframes fadeIn{from{opacity:0;}to{opacity:1;}}
@keyframes floatUp{0%,100%{transform:translateY(0);}50%{transform:translateY(-6px);}}
@keyframes blink{0%,100%{opacity:1;}50%{opacity:.25;}}
@keyframes peakPulse{0%,100%{border-color:rgba(239,68,68,.3);}50%{border-color:rgba(239,68,68,.7);}}
@keyframes gateOpen{0%{transform:scaleX(1);}100%{transform:scaleX(0);}}
@keyframes gatePulse{0%{opacity:1;}50%{opacity:.3;}100%{opacity:1;}}
@keyframes counterFly{0%{opacity:1;transform:translateY(0) scale(1);}80%{opacity:.8;transform:translateY(-80px) scale(1.4);}100%{opacity:0;transform:translateY(-120px) scale(.8);}}
@keyframes tickerRoll{0%{transform:translateY(0);}50%{transform:translateY(-2px);}100%{transform:translateY(0);}}
@keyframes cardFlip{0%{transform:rotateY(0deg);}50%{transform:rotateY(90deg);}100%{transform:rotateY(0deg);}}
@keyframes spotFlash{0%{background:rgba(16,185,129,.6);border-color:rgba(16,185,129,.9);}50%{background:rgba(239,68,68,.6);border-color:rgba(239,68,68,.9);}100%{background:rgba(239,68,68,.2);border-color:rgba(239,68,68,.4);}}
@keyframes spotReserve{0%,100%{background:rgba(255,215,0,.2);border-color:rgba(255,215,0,.4);}50%{background:rgba(255,215,0,.4);border-color:rgba(255,215,0,.7);}}
@keyframes overstayPulse{0%,100%{background:rgba(239,68,68,.06);}50%{background:rgba(239,68,68,.14);}}
@keyframes logIn{from{opacity:0;transform:translateX(-10px);}to{opacity:1;transform:translateX(0);}}
@keyframes heatIn{from{opacity:0;transform:scale(.5);}to{opacity:1;transform:scale(1);}}

body.shift-morning .sidebar{box-shadow:inset 0 0 60px rgba(245,158,11,.04);}
body.shift-afternoon .sidebar{box-shadow:inset 0 0 60px rgba(59,130,246,.04);}
body.shift-night .sidebar{box-shadow:inset 0 0 60px rgba(139,92,246,.06);}

/* ══════════════════════════════════
   LOGIN
══════════════════════════════════ */
#login-screen{position:fixed;inset:0;background:var(--bg);display:flex;align-items:center;justify-content:center;z-index:1000;}
.login-bg{position:absolute;inset:0;overflow:hidden;}
.login-bg-orb{position:absolute;border-radius:50%;filter:blur(80px);animation:floatUp 6s infinite ease-in-out;}
.login-bg-orb:nth-child(1){width:380px;height:380px;left:-80px;top:-80px;animation-delay:0s;}
.login-bg-orb:nth-child(2){width:300px;height:300px;right:-60px;bottom:-60px;animation-delay:-3s;}
body.shift-morning .login-bg-orb:nth-child(1){background:rgba(245,158,11,.07);}
body.shift-morning .login-bg-orb:nth-child(2){background:rgba(251,146,60,.05);}
body.shift-afternoon .login-bg-orb:nth-child(1){background:rgba(59,130,246,.07);}
body.shift-afternoon .login-bg-orb:nth-child(2){background:rgba(249,115,22,.05);}
body.shift-night .login-bg-orb:nth-child(1){background:rgba(139,92,246,.08);}
body.shift-night .login-bg-orb:nth-child(2){background:rgba(59,130,246,.05);}
.login-card{position:relative;z-index:1;width:440px;background:rgba(13,18,32,.9);backdrop-filter:blur(24px);border:1px solid var(--border);border-radius:20px;padding:42px 40px;text-align:center;box-shadow:0 30px 70px rgba(0,0,0,.5);animation:scaleIn .5s ease;}
.login-card::before{content:'';position:absolute;top:0;left:0;right:0;height:2px;border-radius:20px 20px 0 0;background:linear-gradient(90deg,transparent,var(--shift-a),transparent);}
.login-logo{font-family:'Rajdhani',sans-serif;font-size:50px;font-weight:700;color:var(--shift-a);letter-spacing:8px;line-height:1;margin-bottom:4px;}
.login-sub{font-size:9px;letter-spacing:3px;text-transform:uppercase;color:var(--text-muted);margin-bottom:24px;padding-bottom:20px;border-bottom:1px solid var(--border);}
.login-chips{display:grid;grid-template-columns:repeat(4,1fr);gap:8px;margin-bottom:20px;}
.login-chip{background:var(--surface2);border:1px solid var(--border);border-radius:10px;padding:10px 6px;text-align:center;}
.login-chip-val{font-family:'Rajdhani',sans-serif;font-size:20px;font-weight:700;color:var(--shift-a);}
.login-chip-lbl{font-size:9px;color:var(--text-muted);}
.login-rate-box{background:var(--surface2);border:1px solid var(--border);border-radius:10px;padding:12px 14px;margin-bottom:18px;text-align:left;}
.login-rate-title{font-size:9px;letter-spacing:2px;text-transform:uppercase;color:var(--text-muted);margin-bottom:8px;}
.login-rate-row{display:flex;justify-content:space-between;font-size:12px;padding:2px 0;}
.login-rate-lbl{color:var(--text-dim);} .login-rate-val{font-weight:600;color:var(--shift-a);}
.login-label{display:block;text-align:left;font-size:9px;letter-spacing:2px;text-transform:uppercase;color:var(--text-muted);margin-bottom:7px;}
.login-input{width:100%;background:var(--surface2);border:1px solid var(--border);border-radius:9px;padding:12px 16px;color:var(--text);font-size:15px;font-family:'DM Mono',monospace;letter-spacing:4px;outline:none;transition:border-color .2s,box-shadow .2s;margin-bottom:13px;}
.login-input:focus{border-color:var(--shift-a);box-shadow:0 0 0 3px var(--shift-glow);}
.login-btn{width:100%;border:none;border-radius:10px;padding:13px;font-size:13px;font-weight:700;font-family:'Rajdhani',sans-serif;letter-spacing:3px;text-transform:uppercase;cursor:pointer;background:linear-gradient(135deg,var(--shift-a),var(--shift-b));color:#000;transition:opacity .2s,transform .1s;box-shadow:0 4px 20px var(--shift-glow);}
.login-btn:hover{opacity:.9;transform:translateY(-1px);}
.login-btn:active{transform:scale(.98);}
.login-error{color:var(--red);font-size:12px;margin-top:10px;padding:8px 12px;background:var(--red-dim);border-radius:8px;display:none;}
.login-hint{font-size:11px;color:var(--text-muted);margin-top:12px;}
.login-hint span{color:var(--shift-a);font-family:'DM Mono',monospace;}

/* ══════════════════════════════════
   LAYOUT
══════════════════════════════════ */
#app{display:none;min-height:100vh;position:relative;z-index:1;}
.sidebar{position:fixed;left:0;top:0;bottom:0;width:var(--sidebar-w);background:rgba(13,18,32,.97);backdrop-filter:blur(20px);border-right:1px solid var(--border);display:flex;flex-direction:column;z-index:100;transition:box-shadow .5s;}
.sidebar-brand{padding:18px 20px;border-bottom:1px solid var(--border);position:relative;overflow:hidden;}
.brand-row{display:flex;align-items:center;justify-content:space-between;}
.brand-name{font-family:'Rajdhani',sans-serif;font-size:28px;font-weight:700;color:var(--shift-a);letter-spacing:5px;transition:color 1s;}
.brand-icon{font-size:22px;animation:floatUp 4s infinite;}
.brand-badge{display:inline-block;background:var(--amber-dim);border:1px solid rgba(245,158,11,.2);color:var(--amber);font-size:8px;letter-spacing:2px;text-transform:uppercase;padding:2px 8px;border-radius:4px;margin-top:5px;}
.brand-line{height:2px;margin-top:9px;border-radius:2px;background:linear-gradient(90deg,var(--shift-a),var(--shift-b),transparent);}
.sidebar-nav{padding:10px 10px;flex:1;overflow-y:auto;}
.nav-section-label{font-size:9px;letter-spacing:2px;text-transform:uppercase;color:var(--text-muted);padding:8px 12px 3px;}
.nav-item{display:flex;align-items:center;gap:9px;padding:8px 11px;border-radius:9px;cursor:pointer;transition:all .15s;color:var(--text-dim);font-size:12.5px;font-weight:500;margin-bottom:2px;border:1px solid transparent;user-select:none;position:relative;overflow:hidden;}
.nav-item:hover{background:var(--surface2);color:var(--text);}
.nav-item.active{background:rgba(245,158,11,.08);color:var(--shift-a);border-color:rgba(245,158,11,.15);}
.nav-icon{width:28px;height:28px;display:flex;align-items:center;justify-content:center;border-radius:6px;background:var(--surface2);font-size:13px;flex-shrink:0;}
.nav-item.active .nav-icon{background:rgba(245,158,11,.12);}
.nav-badge{margin-left:auto;background:var(--red);color:#fff;font-size:9px;font-weight:700;padding:1px 6px;border-radius:10px;animation:blink 2s infinite;}
.nav-shortcut{margin-left:auto;font-size:8px;color:var(--text-muted);font-family:'DM Mono',monospace;background:var(--surface2);padding:1px 5px;border-radius:3px;border:1px solid var(--border);}
.sidebar-tools{padding:8px;border-top:1px solid var(--border);display:flex;gap:5px;}
.tool-btn{flex:1;display:flex;flex-direction:column;align-items:center;gap:2px;padding:7px 4px;background:var(--surface2);border:1px solid var(--border);border-radius:7px;cursor:pointer;color:var(--text-muted);font-size:9px;font-weight:500;transition:all .2s;}
.tool-btn:hover{border-color:var(--shift-a);color:var(--shift-a);transform:translateY(-2px);}
.tool-btn .ti{font-size:14px;}
.sidebar-bottom{padding:9px;border-top:1px solid var(--border);}
.emp-card{display:flex;align-items:center;gap:9px;padding:9px 11px;background:var(--surface2);border-radius:10px;margin-bottom:7px;border:1px solid var(--border);}
.emp-avatar{width:32px;height:32px;border-radius:50%;background:linear-gradient(135deg,var(--shift-a),var(--shift-b));display:flex;align-items:center;justify-content:center;font-family:'Rajdhani',sans-serif;font-weight:700;color:#000;font-size:12px;flex-shrink:0;}
.emp-name{font-size:12px;font-weight:600;} .emp-role{font-size:9px;color:var(--text-muted);}
.logout-btn{display:flex;align-items:center;gap:8px;padding:8px 12px;border-radius:8px;cursor:pointer;color:var(--text-muted);font-size:12px;font-weight:500;transition:all .2s;background:none;border:1px solid var(--border);width:100%;justify-content:center;}
.logout-btn:hover{background:var(--red-dim);color:var(--red);border-color:rgba(239,68,68,.3);}
.main{margin-left:var(--sidebar-w);min-height:100vh;}
.topbar{height:60px;border-bottom:1px solid var(--border);display:flex;align-items:center;justify-content:space-between;padding:0 22px;background:rgba(13,18,32,.95);backdrop-filter:blur(12px);position:sticky;top:0;z-index:50;gap:10px;}
.topbar-left{display:flex;align-items:center;gap:12px;}
.topbar-title{font-family:'Rajdhani',sans-serif;font-size:16px;font-weight:600;letter-spacing:1px;}
.shift-pill{display:flex;align-items:center;gap:6px;padding:4px 12px;border-radius:30px;font-size:11px;font-weight:600;letter-spacing:.5px;border:1px solid;}
body.shift-morning .shift-pill{background:rgba(245,158,11,.1);color:var(--amber);border-color:rgba(245,158,11,.25);}
body.shift-afternoon .shift-pill{background:rgba(59,130,246,.1);color:var(--blue);border-color:rgba(59,130,246,.25);}
body.shift-night .shift-pill{background:rgba(139,92,246,.1);color:var(--purple);border-color:rgba(139,92,246,.25);}
.shift-dot{width:6px;height:6px;border-radius:50%;animation:blink 2s infinite;}
body.shift-morning .shift-dot{background:var(--amber);}
body.shift-afternoon .shift-dot{background:var(--blue);}
body.shift-night .shift-dot{background:var(--purple);}
.topbar-right{display:flex;align-items:center;gap:8px;flex-shrink:0;}
.price-badge{display:flex;align-items:center;gap:5px;padding:4px 10px;border-radius:7px;font-size:11px;font-weight:700;border:1px solid;font-family:'Rajdhani',sans-serif;letter-spacing:.5px;cursor:default;transition:all .3s;}
.price-badge.peak{background:rgba(239,68,68,.12);border-color:rgba(239,68,68,.3);color:var(--red);animation:peakPulse 2s infinite;}
.price-badge.normal{background:rgba(245,158,11,.1);border-color:rgba(245,158,11,.25);color:var(--amber);}
.price-badge.evening{background:rgba(139,92,246,.1);border-color:rgba(139,92,246,.25);color:var(--purple);}
.price-badge.night{background:rgba(59,130,246,.1);border-color:rgba(59,130,246,.25);color:var(--blue);}
.live-clock{font-family:'DM Mono',monospace;font-size:14px;color:var(--shift-a);background:var(--amber-dim);padding:5px 12px;border-radius:7px;border:1px solid rgba(245,158,11,.2);letter-spacing:.5px;}
.live-dot{width:7px;height:7px;border-radius:50%;background:var(--green);box-shadow:0 0 6px var(--green);animation:blink 2s infinite;flex-shrink:0;}

/* ALERT */
.alert-banner{background:linear-gradient(90deg,rgba(239,68,68,.15),rgba(239,68,68,.03));border-bottom:2px solid rgba(239,68,68,.4);padding:8px 22px;display:none;align-items:center;gap:10px;font-size:12px;}
.alert-banner.show{display:flex;animation:slideInUp .3s ease;}
.ab-chip{background:var(--red-dim);border:1px solid rgba(239,68,68,.3);color:var(--red);padding:3px 10px;border-radius:20px;font-size:11px;font-weight:700;animation:peakPulse 2s infinite;}

/* PAGES */
.page{display:none;padding:22px;}
.page.active{display:block;}
.page.active .stat-card:nth-child(1){animation:slideInUp .35s ease .05s both;}
.page.active .stat-card:nth-child(2){animation:slideInUp .35s ease .10s both;}
.page.active .stat-card:nth-child(3){animation:slideInUp .35s ease .15s both;}
.page.active .stat-card:nth-child(4){animation:slideInUp .35s ease .20s both;}
.page.active .stat-card:nth-child(5){animation:slideInUp .35s ease .25s both;}
.page.active .stat-card:nth-child(6){animation:slideInUp .35s ease .30s both;}

/* STAT CARDS — with flip */
.stats-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(162px,1fr));gap:11px;margin-bottom:18px;}
.stat-card-wrap{perspective:600px;}
.stat-card{background:var(--surface);border:1px solid var(--border);border-radius:13px;padding:15px 17px;position:relative;overflow:hidden;transition:border-color .2s,transform .2s,box-shadow .2s;cursor:default;transform-style:preserve-3d;}
.stat-card:hover{border-color:var(--border2);transform:translateY(-3px);box-shadow:0 10px 30px rgba(0,0,0,.22);}
.stat-card::before{content:'';position:absolute;top:0;left:0;right:0;height:2px;}
.stat-card.amber::before{background:linear-gradient(90deg,var(--amber),var(--orange));}
.stat-card.blue::before{background:linear-gradient(90deg,var(--blue),var(--cyan));}
.stat-card.green::before{background:linear-gradient(90deg,var(--green),#34d399);}
.stat-card.red::before{background:linear-gradient(90deg,var(--red),#f87171);}
.stat-card.purple::before{background:linear-gradient(90deg,var(--purple),#a78bfa);}
.stat-card.cyan::before{background:linear-gradient(90deg,var(--cyan),#67e8f9);}
.stat-card.gold::before{background:linear-gradient(90deg,var(--gold),#fbbf24);}
.stat-label{font-size:9px;letter-spacing:2px;text-transform:uppercase;color:var(--text-muted);margin-bottom:6px;}
.stat-value{font-family:'Rajdhani',sans-serif;font-size:27px;font-weight:700;line-height:1;margin-bottom:2px;}
.stat-card.amber .stat-value{color:var(--amber);}
.stat-card.blue .stat-value{color:var(--blue);}
.stat-card.green .stat-value{color:var(--green);}
.stat-card.red .stat-value{color:var(--red);}
.stat-card.purple .stat-value{color:var(--purple);}
.stat-card.cyan .stat-value{color:var(--cyan);}
.stat-card.gold .stat-value{color:var(--gold);}
.stat-sub{font-size:10px;color:var(--text-muted);}
.stat-icon{position:absolute;right:13px;top:13px;font-size:21px;opacity:.18;animation:floatUp 4s infinite ease-in-out;}
.stat-compare{position:absolute;bottom:10px;right:12px;font-size:10px;font-weight:600;padding:2px 7px;border-radius:10px;display:none;}
.stat-card:hover .stat-compare{display:inline-flex;align-items:center;gap:3px;}
.compare-up{background:rgba(16,185,129,.15);color:var(--green);}
.compare-down{background:rgba(239,68,68,.15);color:var(--red);}
.compare-same{background:var(--surface2);color:var(--text-muted);}

/* PANELS */
.panel{background:var(--surface);border:1px solid var(--border);border-radius:13px;overflow:hidden;margin-bottom:16px;transition:border-color .2s;}
.panel:hover{border-color:var(--border2);}
.panel-header{padding:13px 17px;border-bottom:1px solid var(--border);display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:8px;}
.panel-title{font-family:'Rajdhani',sans-serif;font-size:11px;font-weight:600;letter-spacing:1.5px;text-transform:uppercase;color:var(--text-dim);}
.panel-body{padding:17px;}
.two-col{display:grid;grid-template-columns:1fr 1fr;gap:16px;}
.three-col{display:grid;grid-template-columns:1fr 1fr 1fr;gap:13px;}

/* REVENUE TICKER */
.revenue-ticker{display:flex;align-items:center;gap:12px;padding:10px 17px;background:var(--surface2);border-bottom:1px solid var(--border);}
.ticker-label{font-size:9px;letter-spacing:2px;text-transform:uppercase;color:var(--text-muted);}
.ticker-val{font-family:'Rajdhani',sans-serif;font-size:22px;font-weight:700;color:var(--green);transition:color .3s;}
.ticker-val.updated{animation:tickerRoll .4s ease;}
.ticker-separator{color:var(--border2);font-size:18px;}
.ticker-sub{font-size:11px;color:var(--text-muted);}
.ticker-prediction{margin-left:auto;background:var(--purple-dim);border:1px solid rgba(139,92,246,.25);color:var(--purple);padding:5px 12px;border-radius:8px;font-size:11px;font-weight:600;}

/* GATE ANIMATION */
.gate-wrap{position:fixed;top:0;left:var(--sidebar-w);right:0;bottom:0;pointer-events:none;z-index:400;display:flex;align-items:center;justify-content:center;display:none;}
.gate-wrap.show{display:flex;}
.gate-svg-wrap{position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);}
.gate-flash{position:absolute;inset:0;background:rgba(16,185,129,.06);animation:gatePulse .4s ease;}

/* COUNTER FLY */
.counter-fly{position:fixed;font-family:'Rajdhani',sans-serif;font-size:48px;font-weight:700;color:var(--green);pointer-events:none;z-index:999;display:none;text-shadow:0 0 20px rgba(16,185,129,.6);}
.counter-fly.show{display:block;animation:counterFly .9s ease forwards;}

/* TABLES */
.table-wrap{overflow-x:auto;}
.search-row{display:flex;align-items:center;gap:10px;padding:10px 17px;border-bottom:1px solid var(--border);}
.table-search{flex:1;background:var(--surface2);border:1px solid var(--border);border-radius:7px;padding:7px 12px;color:var(--text);font-size:12px;outline:none;transition:border-color .2s;}
.table-search:focus{border-color:var(--shift-a);}
.table-search::placeholder{color:var(--text-muted);}
.data-table{width:100%;border-collapse:collapse;}
.data-table th{font-size:9px;letter-spacing:2px;text-transform:uppercase;color:var(--text-muted);padding:9px 13px;text-align:left;border-bottom:1px solid var(--border);white-space:nowrap;}
.data-table td{padding:8px 13px;border-bottom:1px solid rgba(255,255,255,.03);font-size:12px;color:var(--text-dim);white-space:nowrap;transition:color .15s;}
.data-table tr:hover td{background:rgba(255,255,255,.02);color:var(--text);}
.data-table tr:last-child td{border-bottom:none;}
.data-table tr.overstay-row{background:rgba(239,68,68,.04)!important;animation:overstayPulse 3s infinite;}
.data-table tr.overstay-row td{color:var(--red)!important;}
.badge{display:inline-flex;align-items:center;padding:2px 8px;border-radius:20px;font-size:10px;font-weight:600;text-transform:uppercase;letter-spacing:.5px;}
.badge-car{background:var(--blue-dim);color:var(--blue);}
.badge-bike{background:var(--green-dim);color:var(--green);}
.badge-active{background:var(--amber-dim);color:var(--amber);}
.badge-exited{background:var(--surface2);color:var(--text-muted);}
.badge-peak{background:var(--red-dim);color:var(--red);}
.badge-vip{background:var(--gold-dim);color:var(--gold);border:1px solid rgba(255,215,0,.3);}
.badge-overstay{background:var(--red-dim);color:var(--red);border:1px solid rgba(239,68,68,.3);animation:peakPulse 1.5s infinite;}
.badge-reserved{background:var(--gold-dim);color:var(--gold);}
.badge-discount{background:var(--green-dim);color:var(--green);}
.empty-state{text-align:center;padding:36px 20px;color:var(--text-muted);}
.empty-icon{font-size:36px;margin-bottom:8px;opacity:.4;animation:floatUp 3s infinite;}

/* FORMS */
.form-group{margin-bottom:14px;}
.form-label{display:block;font-size:9px;letter-spacing:2px;text-transform:uppercase;color:var(--text-muted);margin-bottom:6px;font-weight:500;}
.form-input,.form-select{width:100%;background:var(--surface2);border:1px solid var(--border);border-radius:8px;padding:10px 13px;color:var(--text);font-size:13px;font-family:'DM Sans',sans-serif;outline:none;transition:border-color .2s,box-shadow .2s;}
.form-input:focus,.form-select:focus{border-color:var(--shift-a);box-shadow:0 0 0 3px var(--shift-glow);}
.form-input::placeholder{color:var(--text-muted);}
.form-select{cursor:pointer;-webkit-appearance:none;appearance:none;background-image:url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 16 16'%3e%3cpath fill='none' stroke='%2364748b' stroke-linecap='round' stroke-linejoin='round' stroke-width='2' d='M2 5l6 6 6-6'/%3e%3c/svg%3e");background-repeat:no-repeat;background-position:right 12px center;background-size:12px;}
.form-select option{background:#141a2a;}

/* TYPE TOGGLE */
.type-toggle{display:grid;grid-template-columns:1fr 1fr;gap:9px;margin-bottom:14px;}
.type-btn{padding:14px;border-radius:10px;border:2px solid var(--border);background:var(--surface2);cursor:pointer;text-align:center;transition:all .2s;user-select:none;}
.type-btn:hover{border-color:var(--border2);transform:translateY(-2px);}
.type-btn .type-icon{font-size:26px;margin-bottom:4px;display:block;transition:transform .2s;}
.type-btn:hover .type-icon{transform:scale(1.15);}
.type-btn .type-label{font-family:'Rajdhani',sans-serif;font-size:14px;font-weight:700;letter-spacing:1px;color:var(--text-dim);}
.type-btn .type-sub{font-size:9px;color:var(--text-muted);margin-top:3px;}
.type-btn.sel-car{border-color:var(--blue);background:var(--blue-dim);box-shadow:0 4px 14px rgba(59,130,246,.15);}
.type-btn.sel-car .type-label{color:var(--blue);}
.type-btn.sel-bike{border-color:var(--green);background:var(--green-dim);box-shadow:0 4px 14px rgba(16,185,129,.15);}
.type-btn.sel-bike .type-label{color:var(--green);}

/* BUTTONS */
.btn{padding:8px 16px;border-radius:8px;border:none;cursor:pointer;font-size:12px;font-weight:700;font-family:'Rajdhani',sans-serif;letter-spacing:1.5px;text-transform:uppercase;transition:all .2s;display:inline-flex;align-items:center;gap:6px;white-space:nowrap;user-select:none;}
.btn-primary{background:linear-gradient(135deg,var(--shift-a),var(--shift-b));color:#000;box-shadow:0 4px 14px var(--shift-glow);}
.btn-primary:hover{opacity:.9;transform:translateY(-2px);box-shadow:0 6px 20px var(--shift-glow);}
.btn-danger{background:var(--red-dim);color:var(--red);border:1px solid rgba(239,68,68,.25);}
.btn-danger:hover{background:rgba(239,68,68,.2);transform:translateY(-1px);}
.btn-success{background:var(--green-dim);color:var(--green);border:1px solid rgba(16,185,129,.25);}
.btn-success:hover{background:rgba(16,185,129,.2);transform:translateY(-1px);}
.btn-secondary{background:var(--surface2);color:var(--text-dim);border:1px solid var(--border);}
.btn-secondary:hover{border-color:var(--border2);color:var(--text);transform:translateY(-1px);}
.btn-info{background:var(--blue-dim);color:var(--blue);border:1px solid rgba(59,130,246,.25);}
.btn-info:hover{background:rgba(59,130,246,.2);transform:translateY(-1px);}
.btn-gold{background:var(--gold-dim);color:var(--gold);border:1px solid rgba(255,215,0,.25);}
.btn-gold:hover{background:rgba(255,215,0,.2);}
.btn-whatsapp{background:rgba(37,211,102,.12);color:#25d366;border:1px solid rgba(37,211,102,.3);}
.btn-whatsapp:hover{background:rgba(37,211,102,.2);}
.btn:active{transform:scale(.96)!important;}
.btn-full{width:100%;justify-content:center;}
.btn-lg{padding:12px 22px;font-size:13px;}
.btn-sm{padding:4px 10px;font-size:10px;}

/* INFO / AMOUNT */
.info-box{background:var(--surface2);border:1px solid var(--border);border-radius:9px;padding:11px 15px;margin-bottom:13px;}
.info-row{display:flex;justify-content:space-between;align-items:center;padding:4px 0;}
.info-lbl{font-size:11px;color:var(--text-muted);}
.info-val{font-size:12px;font-weight:600;color:var(--text);}
.amount-box{text-align:center;padding:16px 0;}
.amount-lbl{font-size:9px;letter-spacing:2px;text-transform:uppercase;color:var(--text-muted);margin-bottom:4px;}
.amount-val{font-family:'Rajdhani',sans-serif;font-size:50px;font-weight:700;color:var(--amber);line-height:1;}
.amount-discount{font-size:13px;color:var(--green);margin-top:4px;}

/* OCC BARS */
.occ-wrap{margin-bottom:11px;}
.occ-header{display:flex;justify-content:space-between;font-size:11px;color:var(--text-muted);margin-bottom:4px;}
.occ-bg{height:7px;background:var(--surface2);border-radius:4px;overflow:hidden;}
.occ-fill{height:100%;border-radius:4px;transition:width .8s cubic-bezier(.4,0,.2,1);}

/* SHIFT GRID */
.shift-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:9px;}
.shift-card{background:var(--surface2);border:1px solid var(--border);border-radius:9px;padding:11px;text-align:center;transition:all .2s;}
.shift-card:hover{transform:translateY(-2px);border-color:var(--border2);}
.shift-card-icon{font-size:20px;margin-bottom:4px;}
.shift-card-lbl{font-size:9px;letter-spacing:1.5px;text-transform:uppercase;color:var(--text-muted);margin-bottom:2px;}
.shift-card-val{font-family:'Rajdhani',sans-serif;font-size:20px;font-weight:700;color:var(--amber);}
.shift-card-sub{font-size:9px;color:var(--text-muted);margin-top:1px;}

/* CHARTS */
.chart-container{position:relative;width:100%;}
.chart-sm{height:170px;} .chart-md{height:210px;}

/* FLOOR MAP with animations */
.floor-tabs{display:flex;gap:5px;}
.floor-tab{padding:6px 14px;border-radius:7px;border:1px solid var(--border);background:var(--surface2);color:var(--text-muted);cursor:pointer;font-size:12px;font-weight:600;font-family:'Rajdhani',sans-serif;letter-spacing:1px;transition:all .2s;}
.floor-tab.active{background:var(--amber-dim);color:var(--amber);border-color:rgba(245,158,11,.25);}
.map-type-tabs{display:flex;gap:6px;margin-bottom:14px;}
.map-type-tab{padding:5px 13px;border-radius:6px;border:1px solid var(--border);background:var(--surface2);color:var(--text-muted);cursor:pointer;font-size:11px;font-weight:600;transition:all .2s;}
.map-type-tab.act-car{background:var(--blue-dim);color:var(--blue);border-color:rgba(59,130,246,.25);}
.map-type-tab.act-bike{background:var(--green-dim);color:var(--green);border-color:rgba(16,185,129,.25);}
.spots-grid{display:flex;flex-wrap:wrap;gap:3px;padding:4px 0;}
.spot{width:22px;height:22px;border-radius:4px;cursor:pointer;transition:transform .15s,filter .15s;position:relative;}
.spot:hover{transform:scale(1.5);z-index:2;filter:brightness(1.5);}
.spot.available{background:rgba(16,185,129,.2);border:1px solid rgba(16,185,129,.4);}
.spot.occupied{background:rgba(239,68,68,.2);border:1px solid rgba(239,68,68,.4);}
.spot.reserved{animation:spotReserve 2s infinite;border:1px solid rgba(255,215,0,.4);}
.spot.just-occupied{animation:spotFlash .8s ease forwards;}
.spot.just-freed{background:rgba(16,185,129,.5);border-color:rgba(16,185,129,.8);transition:all .5s;}
.map-legend{display:flex;gap:14px;margin-top:10px;flex-wrap:wrap;}
.legend-item{display:flex;align-items:center;gap:5px;font-size:11px;color:var(--text-muted);}
.legend-dot{width:12px;height:12px;border-radius:3px;}
.reserve-controls{display:flex;gap:10px;margin-bottom:14px;flex-wrap:wrap;}

/* HEAT CALENDAR */
.heat-calendar{display:flex;flex-direction:column;gap:4px;}
.heat-month-label{font-size:9px;letter-spacing:2px;text-transform:uppercase;color:var(--text-muted);margin-bottom:6px;}
.heat-week{display:flex;gap:3px;}
.heat-day{width:14px;height:14px;border-radius:2px;cursor:default;transition:transform .15s;flex-shrink:0;}
.heat-day:hover{transform:scale(1.8);z-index:2;position:relative;}
.heat-day.empty{background:transparent;}
.heat-day.h0{background:var(--surface2);border:1px solid var(--border);}
.heat-day.h1{background:rgba(16,185,129,.2);}
.heat-day.h2{background:rgba(16,185,129,.35);}
.heat-day.h3{background:rgba(16,185,129,.55);}
.heat-day.h4{background:rgba(16,185,129,.8);}
.heat-day.h5{background:#10b981;}
.heat-day-labels{display:flex;gap:3px;margin-bottom:3px;}
.heat-day-lbl{width:14px;font-size:7px;color:var(--text-muted);text-align:center;}
.heat-legend{display:flex;align-items:center;gap:5px;margin-top:8px;font-size:10px;color:var(--text-muted);}
.heat-legend-dot{width:12px;height:12px;border-radius:2px;}

/* ACTIVITY LOG */
.log-list{max-height:380px;overflow-y:auto;padding:0;}
.log-item{display:flex;gap:10px;align-items:flex-start;padding:9px 0;border-bottom:1px solid rgba(255,255,255,.04);animation:logIn .3s ease;}
.log-item:last-child{border-bottom:none;}
.log-dot{width:8px;height:8px;border-radius:50%;flex-shrink:0;margin-top:4px;}
.log-dot.entry{background:var(--green);}
.log-dot.exit{background:var(--blue);}
.log-dot.reserve{background:var(--gold);}
.log-dot.lost{background:var(--red);}
.log-dot.handover{background:var(--purple);}
.log-dot.discount{background:var(--cyan);}
.log-time{font-family:'DM Mono',monospace;font-size:10px;color:var(--text-muted);flex-shrink:0;min-width:70px;}
.log-text{font-size:12px;color:var(--text-dim);}
.log-text strong{color:var(--text);}

/* VEHICLE HISTORY */
.history-header{background:var(--surface2);border:1px solid var(--border);border-radius:10px;padding:16px;margin-bottom:14px;}
.history-plate{font-family:'Rajdhani',sans-serif;font-size:28px;font-weight:700;color:var(--amber);letter-spacing:3px;}
.history-stats{display:grid;grid-template-columns:repeat(4,1fr);gap:10px;margin-top:12px;}
.history-stat{text-align:center;padding:10px;background:rgba(255,255,255,.03);border-radius:8px;}
.history-stat-val{font-family:'Rajdhani',sans-serif;font-size:20px;font-weight:700;color:var(--text);}
.history-stat-lbl{font-size:9px;color:var(--text-muted);letter-spacing:1px;}

/* DISCOUNT SECTION */
.discount-row{display:flex;gap:8px;margin-bottom:13px;}
.discount-btn{flex:1;padding:9px;border-radius:8px;border:1px solid var(--border);background:var(--surface2);color:var(--text-muted);cursor:pointer;text-align:center;font-size:12px;font-weight:600;transition:all .2s;}
.discount-btn:hover{border-color:var(--green);color:var(--green);}
.discount-btn.selected{border-color:var(--green);background:var(--green-dim);color:var(--green);}
.discount-none{border-color:var(--border)!important;color:var(--text-muted)!important;background:var(--surface2)!important;}

/* LOST TICKET */
.lost-section{background:rgba(239,68,68,.05);border:1px solid rgba(239,68,68,.2);border-radius:10px;padding:14px;margin-top:14px;}
.lost-title{font-size:9px;letter-spacing:2px;text-transform:uppercase;color:var(--red);margin-bottom:10px;font-weight:600;}

/* HANDOVER REPORT */
.handover-card{background:#fff;color:#111;border-radius:10px;padding:22px;font-family:'DM Sans',sans-serif;}
.handover-title{font-family:'Rajdhani',sans-serif;font-size:28px;font-weight:700;letter-spacing:3px;border-bottom:2px dashed #e5e7eb;padding-bottom:14px;margin-bottom:14px;}
.handover-row{display:flex;justify-content:space-between;padding:5px 0;border-bottom:1px solid #f3f4f6;font-size:12px;}
.handover-row:last-child{border-bottom:none;}
.handover-total{background:#111;color:#fff;border-radius:8px;padding:11px 15px;display:flex;justify-content:space-between;margin-top:12px;}
.handover-total-lbl{font-family:'Rajdhani',sans-serif;font-size:13px;font-weight:700;letter-spacing:2px;}
.handover-total-val{font-family:'Rajdhani',sans-serif;font-size:22px;font-weight:700;color:var(--amber);}
.handover-vehicles{margin-top:12px;}
.handover-v-item{font-size:11px;padding:4px 0;border-bottom:1px solid #f3f4f6;display:flex;justify-content:space-between;}

/* BILLING */
.billing-card{background:#fff;color:#111;border-radius:10px;padding:22px;font-family:'DM Sans',sans-serif;}
.bill-header{text-align:center;border-bottom:2px dashed #e5e7eb;padding-bottom:14px;margin-bottom:14px;}
.bill-mall{font-family:'Rajdhani',sans-serif;font-size:30px;font-weight:700;letter-spacing:4px;}
.bill-sub{font-size:9px;letter-spacing:2px;color:#9ca3af;text-transform:uppercase;}
.bill-addr{font-size:11px;color:#9ca3af;margin-top:2px;}
.bill-period{display:inline-block;font-size:10px;font-weight:600;padding:2px 9px;border-radius:10px;margin-top:5px;}
.bill-period.peak{background:#fee2e2;color:#dc2626;}
.bill-period.normal{background:#fef3c7;color:#92400e;}
.bill-period.evening{background:#ede9fe;color:#7c3aed;}
.bill-period.night{background:#dbeafe;color:#1d4ed8;}
.bill-ticket-no{font-family:'DM Mono',monospace;font-size:19px;color:#f59e0b;font-weight:700;margin-top:7px;}
.bill-qr-wrap{display:flex;justify-content:center;margin:9px 0;}
.bill-row{display:flex;justify-content:space-between;padding:5px 0;border-bottom:1px solid #f3f4f6;font-size:12px;}
.bill-row:last-child{border-bottom:none;}
.bill-lbl{color:#6b7280;} .bill-val{font-weight:600;color:#111;}
.bill-divider{border:none;border-top:1px dashed #e5e7eb;margin:9px 0;}
.bill-total{background:#111;color:#f59e0b;border-radius:8px;padding:11px 15px;display:flex;justify-content:space-between;align-items:center;margin-top:11px;}
.bill-total-lbl{font-family:'Rajdhani',sans-serif;font-size:13px;font-weight:700;letter-spacing:2px;}
.bill-total-amt{font-family:'Rajdhani',sans-serif;font-size:24px;font-weight:700;}
.bill-discount-row{background:#f0fdf4;border:1px solid #86efac;border-radius:6px;padding:8px 12px;display:flex;justify-content:space-between;margin:9px 0;font-size:12px;}
.bill-footer{text-align:center;margin-top:12px;font-size:10px;color:#9ca3af;border-top:1px dashed #e5e7eb;padding-top:11px;line-height:1.9;}
.wa-btn-wrap{margin-top:10px;text-align:center;}

/* MODAL */
.modal-overlay{position:fixed;inset:0;background:rgba(0,0,0,.88);z-index:500;display:none;align-items:center;justify-content:center;padding:18px;backdrop-filter:blur(4px);}
.modal-overlay.open{display:flex;animation:fadeIn .2s ease;}
.modal{background:var(--surface);border:1px solid var(--border);border-radius:15px;max-width:520px;width:100%;max-height:92vh;overflow-y:auto;animation:scaleIn .25s ease;}
.modal-header{padding:15px 19px;border-bottom:1px solid var(--border);display:flex;align-items:center;justify-content:space-between;position:sticky;top:0;background:var(--surface);z-index:1;}
.modal-title{font-family:'Rajdhani',sans-serif;font-size:15px;font-weight:700;letter-spacing:1px;}
.modal-close{width:27px;height:27px;border-radius:6px;border:1px solid var(--border);background:var(--surface2);color:var(--text-muted);cursor:pointer;display:flex;align-items:center;justify-content:center;font-size:15px;transition:all .2s;line-height:1;}
.modal-close:hover{border-color:var(--red);color:var(--red);}
.modal-body{padding:19px;}
.modal-lg{max-width:700px;}

/* RATE CARD */
.rate-card{border-radius:11px;border:1px solid;padding:13px 16px;margin-bottom:14px;position:relative;overflow:hidden;}
.rate-card.peak{background:rgba(239,68,68,.06);border-color:rgba(239,68,68,.25);animation:peakPulse 2.5s infinite;}
.rate-card.normal{background:rgba(245,158,11,.05);border-color:rgba(245,158,11,.2);}
.rate-card.evening{background:rgba(139,92,246,.05);border-color:rgba(139,92,246,.2);}
.rate-card.night{background:rgba(59,130,246,.05);border-color:rgba(59,130,246,.2);}
.rate-card-label{font-size:9px;letter-spacing:2px;text-transform:uppercase;margin-bottom:8px;}
.rate-card.peak .rate-card-label{color:var(--red);}
.rate-card.normal .rate-card-label{color:var(--amber);}
.rate-card.evening .rate-card-label{color:var(--purple);}
.rate-card.night .rate-card-label{color:var(--blue);}
.rate-card-grid{display:grid;grid-template-columns:1fr 1fr;gap:7px;}
.rate-item{text-align:center;padding:7px;background:rgba(255,255,255,.03);border-radius:7px;}
.rate-item-icon{font-size:16px;}
.rate-item-val{font-family:'Rajdhani',sans-serif;font-size:15px;font-weight:700;color:var(--text);}
.rate-item-lbl{font-size:9px;color:var(--text-muted);}
.rate-change-info{margin-top:7px;font-size:10px;color:var(--text-muted);display:flex;align-items:center;gap:5px;}

/* NOTIFICATION */
.notification{position:fixed;top:15px;right:15px;padding:11px 15px;border-radius:11px;font-size:13px;font-weight:500;z-index:9999;transform:translateX(130%);transition:transform .35s cubic-bezier(.34,1.56,.64,1);display:flex;align-items:center;gap:9px;max-width:330px;box-shadow:0 10px 40px rgba(0,0,0,.5);backdrop-filter:blur(12px);}
.notification.show{transform:translateX(0);}
.notification.success{background:rgba(6,78,59,.9);border:1px solid var(--green);color:var(--green);}
.notification.error{background:rgba(69,10,10,.9);border:1px solid var(--red);color:var(--red);}
.notification.info{background:rgba(30,27,75,.9);border:1px solid var(--blue);color:var(--blue);}
.notification.warn{background:rgba(69,26,3,.9);border:1px solid var(--amber);color:var(--amber);}

/* PRICING TABLE */
.pricing-box{background:var(--surface2);border:1px solid var(--border);border-radius:9px;overflow:hidden;}
.pricing-row{display:flex;justify-content:space-between;align-items:center;padding:8px 13px;border-bottom:1px solid rgba(255,255,255,.04);font-size:12px;}
.pricing-row:last-child{border-bottom:none;}
.pricing-row.active-rate{background:rgba(245,158,11,.05);}
.pricing-lbl{color:var(--text-dim);}
.pricing-val{font-weight:700;}

/* KB HINT */
.kb-hint{position:fixed;bottom:18px;right:18px;background:var(--surface);border:1px solid var(--border);border-radius:11px;padding:13px 15px;font-size:11px;color:var(--text-dim);z-index:200;display:none;min-width:200px;box-shadow:0 10px 40px rgba(0,0,0,.4);backdrop-filter:blur(12px);}
.kb-hint.show{display:block;animation:slideInRight .3s ease;}
.kb-hint h4{font-family:'Rajdhani',sans-serif;font-size:10px;font-weight:600;letter-spacing:1.5px;color:var(--text-muted);margin-bottom:7px;text-transform:uppercase;}
.kb-row{display:flex;justify-content:space-between;align-items:center;padding:2px 0;}
.kb-key{font-family:'DM Mono',monospace;font-size:9px;background:var(--surface2);border:1px solid var(--border);padding:1px 5px;border-radius:3px;color:var(--shift-a);}

/* FLEX */
.flex{display:flex;} .items-center{align-items:center;}
.gap-8{gap:8px;} .gap-10{gap:10px;} .gap-12{gap:12px;}
.mb-14{margin-bottom:14px;} .mt-10{margin-top:10px;} .ml-auto{margin-left:auto;}
.mt-14{margin-top:14px;}

/* PRINT */
@media print{
  @page{size:80mm auto;margin:2mm 3mm;}
  body,html{background:#fff!important;color:#000!important;}
  .sidebar,.topbar,.alert-banner,.notification,.kb-hint,.gate-wrap,.counter-fly,
  #page-dashboard,#page-entry,#page-exit,#page-map,#page-reports,#page-history,#page-log,.modal-header .modal-close,.flex.gap-10.mt-10,#particle-canvas{display:none!important;}
  .modal-overlay.open{position:static!important;background:none!important;padding:0!important;}
  .modal{border:none!important;border-radius:0!important;max-width:100%!important;box-shadow:none!important;}
  .modal-body{padding:0!important;}
  .billing-card,.handover-card{border-radius:0!important;padding:4mm!important;max-width:74mm!important;}
  .main{margin-left:0!important;}
}
</style>
</head>
<body class="shift-morning">
<canvas id="particle-canvas"></canvas>

<!-- GATE ANIMATION -->
<div class="gate-wrap" id="gate-wrap">
  <div class="gate-flash"></div>
  <div class="gate-svg-wrap">
    <svg width="200" height="120" viewBox="0 0 200 120" id="gate-svg">
      <rect x="10" y="10" width="180" height="100" rx="6" fill="none" stroke="rgba(16,185,129,.3)" stroke-width="1.5"/>
      <rect id="gate-left"  x="10"  y="10" width="90"  height="100" rx="6" fill="rgba(16,185,129,.08)" stroke="rgba(16,185,129,.5)" stroke-width="1.5" transform-origin="10 60"/>
      <rect id="gate-right" x="100" y="10" width="90"  height="100" rx="6" fill="rgba(16,185,129,.08)" stroke="rgba(16,185,129,.5)" stroke-width="1.5" transform-origin="190 60"/>
      <text x="100" y="68" text-anchor="middle" fill="rgba(16,185,129,.8)" font-size="11" font-family="Rajdhani,sans-serif" letter-spacing="2">GATE OPEN</text>
    </svg>
  </div>
</div>

<!-- COUNTER FLY -->
<div class="counter-fly" id="counter-fly">+1</div>

<!-- LOGIN -->
<div id="login-screen">
  <div class="login-bg"><div class="login-bg-orb"></div><div class="login-bg-orb"></div></div>
  <div class="login-card">
    <div class="login-logo" id="login-logo">V MALL</div>
    <div class="login-sub" id="login-sub">Morning Shift · Parking Control Panel v4</div>
    <div class="login-chips">
      <div class="login-chip"><div class="login-chip-val">300</div><div class="login-chip-lbl">Cars</div></div>
      <div class="login-chip"><div class="login-chip-val">600</div><div class="login-chip-lbl">Bikes</div></div>
      <div class="login-chip"><div class="login-chip-val">3</div><div class="login-chip-lbl">Floors</div></div>
      <div class="login-chip"><div class="login-chip-val">24/7</div><div class="login-chip-lbl">Open</div></div>
    </div>
    <div class="login-rate-box">
      <div class="login-rate-title" id="lr-title">Current Rate</div>
      <div class="login-rate-row"><span class="login-rate-lbl">🚗 Car</span><span class="login-rate-val" id="lr-car">—</span></div>
      <div class="login-rate-row"><span class="login-rate-lbl">🏍️ Bike</span><span class="login-rate-val" id="lr-bike">—</span></div>
    </div>
    <label class="login-label">Employee Password</label>
    <input type="password" class="login-input" id="login-pass" placeholder="••••••••" onkeydown="if(event.key==='Enter')doLogin()">
    <button class="login-btn" onclick="doLogin()">🔐 &nbsp;ACCESS CONTROL PANEL</button>
    <div class="login-error" id="login-error">❌ Incorrect password</div>
    <div class="login-hint">Password: <span>vmall2024</span> &nbsp;|&nbsp; Enter to login</div>
  </div>
</div>

<!-- APP -->
<div id="app">
  <div class="sidebar">
    <div class="night-sky-sidebar" id="night-sky"></div>
    <div class="sidebar-brand" style="position:relative;z-index:1;">
      <div class="brand-row">
        <div class="brand-name" id="brand-name">V MALL</div>
        <div class="brand-icon" id="brand-icon">🌅</div>
      </div>
      <div class="brand-badge">Parking Management System v4</div>
      <div class="brand-line"></div>
    </div>
    <nav class="sidebar-nav" style="position:relative;z-index:1;">
      <div class="nav-section-label">Overview</div>
      <div class="nav-item active" id="nav-dashboard" onclick="showPage('dashboard')"><div class="nav-icon">📊</div>Dashboard<span class="nav-shortcut">Ctrl+D</span></div>
      <div class="nav-section-label">Operations</div>
      <div class="nav-item" id="nav-entry" onclick="showPage('entry')"><div class="nav-icon">🚗</div>Vehicle Entry<span class="nav-shortcut">Ctrl+E</span></div>
      <div class="nav-item" id="nav-exit" onclick="showPage('exit')"><div class="nav-icon">🎫</div>Exit &amp; Billing<span class="nav-badge" id="active-badge" style="display:none">0</span></div>
      <div class="nav-section-label">Monitoring</div>
      <div class="nav-item" id="nav-map" onclick="showPage('map')"><div class="nav-icon">🗺️</div>Floor Map<span class="nav-shortcut">Ctrl+M</span></div>
      <div class="nav-section-label">Analytics</div>
      <div class="nav-item" id="nav-reports" onclick="showPage('reports')"><div class="nav-icon">📈</div>Reports<span class="nav-shortcut">Ctrl+R</span></div>
      <div class="nav-item" id="nav-history" onclick="showPage('history')"><div class="nav-icon">🔍</div>Vehicle History<span class="nav-shortcut">Ctrl+H</span></div>
      <div class="nav-item" id="nav-log" onclick="showPage('log')"><div class="nav-icon">📋</div>Activity Log</div>
    </nav>
    <div class="sidebar-tools" style="position:relative;z-index:1;">
      <div class="tool-btn" onclick="toggleTheme()"><div class="ti" id="theme-icon">🌙</div><span id="theme-label">Dark</span></div>
      <div class="tool-btn" onclick="toggleFullscreen()"><div class="ti">⛶</div><span>Full</span></div>
      <div class="tool-btn" onclick="showHandover()"><div class="ti">📝</div><span>Handover</span></div>
      <div class="tool-btn" onclick="toggleKbHint()"><div class="ti">⌨️</div><span>Keys</span></div>
    </div>
    <div class="sidebar-bottom" style="position:relative;z-index:1;">
      <div class="emp-card">
        <div class="emp-avatar" id="emp-av">EM</div>
        <div><div class="emp-name">Parking Staff</div><div class="emp-role" id="emp-role">Morning Duty</div></div>
      </div>
      <button class="logout-btn" onclick="doLogout()">🚪 &nbsp;Logout</button>
    </div>
  </div>

  <div class="main">
    <div class="topbar">
      <div class="topbar-left">
        <div class="topbar-title" id="topbar-title">📊 &nbsp;Dashboard</div>
        <div class="shift-pill" id="shift-pill"><span class="shift-dot"></span><span id="shift-pill-text">Morning Shift</span></div>
      </div>
      <div class="topbar-right">
        <div class="price-badge" id="price-badge"></div>
        <div class="live-dot"></div>
        <div class="live-clock" id="clock">12:00:00 AM</div>
      </div>
    </div>
    <div class="alert-banner" id="alert-banner">
      <div style="font-size:17px">🚨</div>
      <strong>Low Availability!</strong>
      <div id="alert-items" class="flex gap-10" style="flex:1;flex-wrap:wrap;"></div>
    </div>

    <!-- REVENUE TICKER -->
    <div class="revenue-ticker" id="rev-ticker">
      <div><div class="ticker-label">Today's Revenue</div><div class="ticker-val" id="ticker-rev">₹0</div></div>
      <div class="ticker-separator">|</div>
      <div><div class="ticker-label">Transactions</div><div class="ticker-val" id="ticker-txn" style="color:var(--blue)">0</div></div>
      <div class="ticker-separator">|</div>
      <div><div class="ticker-label">Active Vehicles</div><div class="ticker-val" id="ticker-active" style="color:var(--amber)">0</div></div>
      <div class="ticker-prediction" id="ticker-pred" title="Prediction based on 7-day average">📊 Predicting...</div>
    </div>

    <!-- ═══ DASHBOARD ═══ -->
    <div class="page active" id="page-dashboard">
      <div class="stats-grid">
        <div class="stat-card-wrap"><div class="stat-card amber" id="sc-today-rev"><div class="stat-icon">₹</div><div class="stat-label">Today's Revenue</div><div class="stat-value" id="s-today-rev">₹0</div><div class="stat-sub" id="s-today-txn">0 transactions</div><span class="stat-compare" id="cmp-rev"></span></div></div>
        <div class="stat-card-wrap"><div class="stat-card blue" id="sc-cars"><div class="stat-icon">🚗</div><div class="stat-label">Cars Now</div><div class="stat-value" id="s-cars">0</div><div class="stat-sub" id="s-cars-pct">0% occupancy</div><span class="stat-compare" id="cmp-cars"></span></div></div>
        <div class="stat-card-wrap"><div class="stat-card green" id="sc-bikes"><div class="stat-icon">🏍️</div><div class="stat-label">Bikes Now</div><div class="stat-value" id="s-bikes">0</div><div class="stat-sub" id="s-bikes-pct">0% occupancy</div><span class="stat-compare" id="cmp-bikes"></span></div></div>
        <div class="stat-card-wrap"><div class="stat-card cyan"><div class="stat-icon">🅿️</div><div class="stat-label">Car Spots Free</div><div class="stat-value" id="s-car-free">300</div><div class="stat-sub">of 300</div></div></div>
        <div class="stat-card-wrap"><div class="stat-card purple"><div class="stat-icon">🅿️</div><div class="stat-label">Bike Spots Free</div><div class="stat-value" id="s-bike-free">600</div><div class="stat-sub">of 600</div></div></div>
        <div class="stat-card-wrap"><div class="stat-card red"><div class="stat-icon">💰</div><div class="stat-label">All-Time Revenue</div><div class="stat-value" id="s-total-rev">₹0</div><div class="stat-sub" id="s-total-sub">0 total</div></div></div>
      </div>
      <div class="two-col">
        <div class="panel"><div class="panel-header"><span class="panel-title">📊 Weekly Revenue</span></div><div class="panel-body"><div class="chart-container chart-sm"><canvas id="chart-weekly"></canvas></div></div></div>
        <div class="panel"><div class="panel-header"><span class="panel-title">🥧 Revenue by Type</span></div><div class="panel-body"><div class="chart-container chart-sm"><canvas id="chart-type"></canvas></div></div></div>
      </div>
      <div class="two-col">
        <div class="panel"><div class="panel-header"><span class="panel-title">🏢 Floor Occupancy</span></div><div class="panel-body" id="floor-occ"></div></div>
        <div class="panel"><div class="panel-header"><span class="panel-title">⏰ Shift Revenue Today</span></div><div class="panel-body"><div class="shift-grid" id="shift-rev"></div></div></div>
      </div>
      <div class="panel">
        <div class="panel-header"><span class="panel-title">🕒 Recent Activity</span><button class="btn btn-sm btn-secondary" onclick="generateDemoData()">⚡ Demo Data</button></div>
        <div class="search-row"><input class="table-search" placeholder="🔍  Search recent..." oninput="filterTable(this,'recent-tbody')"></div>
        <div class="table-wrap"><table class="data-table"><thead><tr><th>Ticket</th><th>Type</th><th>Plate</th><th>Floor</th><th>Entry</th><th>Rate</th><th>Status</th><th>Amount</th></tr></thead><tbody id="recent-tbody"></tbody></table></div>
      </div>
    </div>

    <!-- ═══ ENTRY ═══ -->
    <div class="page" id="page-entry">
      <div class="two-col">
        <div>
          <div class="panel">
            <div class="panel-header"><span class="panel-title">🚗 Vehicle Entry</span></div>
            <div class="panel-body">
              <div id="entry-rate-card" class="rate-card normal"></div>
              <div class="type-toggle">
                <div class="type-btn sel-car" id="btn-car" onclick="selectVType('car')"><span class="type-icon">🚗</span><div class="type-label">CAR</div><div class="type-sub" id="car-rate-sub">₹50 · ₹30/hr</div></div>
                <div class="type-btn" id="btn-bike" onclick="selectVType('bike')"><span class="type-icon">🏍️</span><div class="type-label">BIKE</div><div class="type-sub" id="bike-rate-sub">₹20 · ₹10/hr</div></div>
              </div>
              <div class="form-group"><label class="form-label">License Plate *</label><input type="text" class="form-input" id="inp-plate" placeholder="AP 09 AB 1234" oninput="this.value=this.value.toUpperCase()" onkeydown="if(event.key==='Enter')doEntry()"></div>
              <div class="form-group"><label class="form-label">Floor</label><select class="form-select" id="inp-floor"><option value="auto">Auto-Assign</option><option value="1">Floor 1</option><option value="2">Floor 2</option><option value="3">Floor 3</option></select></div>
              <!-- DISCOUNT CODES -->
              <div class="form-group">
                <label class="form-label">Discount Code (optional)</label>
                <div class="discount-row">
                  <div class="discount-btn discount-none" id="disc-0" onclick="selectDiscount(0)">No Discount</div>
                  <div class="discount-btn" id="disc-10" onclick="selectDiscount(10)">10% OFF</div>
                  <div class="discount-btn" id="disc-20" onclick="selectDiscount(20)">20% OFF</div>
                </div>
              </div>
              <div class="info-box">
                <div class="info-row"><span class="info-lbl">🚗 Car spots</span><span class="info-val" id="avail-car">300/300</span></div>
                <div class="info-row"><span class="info-lbl">🏍️ Bike spots</span><span class="info-val" id="avail-bike">600/600</span></div>
                <div class="info-row"><span class="info-lbl">⏰ Shift</span><span class="info-val" id="entry-shift">—</span></div>
              </div>
              <button class="btn btn-primary btn-full btn-lg" onclick="doEntry()">✅ CHECK IN &nbsp;<span style="font-size:10px;opacity:.6">(Enter)</span></button>
            </div>
          </div>
          <div class="panel">
            <div class="panel-header"><span class="panel-title">💰 All Rates</span></div>
            <div class="panel-body"><div class="pricing-box" id="full-rate-table"></div></div>
          </div>
        </div>
        <div>
          <div class="panel" id="ticket-panel" style="display:none">
            <div class="panel-header"><span class="panel-title">✅ Ticket Generated</span><button class="btn btn-sm btn-secondary" onclick="window.print()">🖨️ Print</button></div>
            <div class="panel-body" id="ticket-body"></div>
          </div>
        </div>
      </div>
      <div class="panel">
        <div class="panel-header"><span class="panel-title">Active Vehicles</span><span class="stat-sub" id="entry-count">0</span></div>
        <div class="search-row"><input class="table-search" placeholder="🔍  Search..." oninput="filterTable(this,'entry-tbody')"></div>
        <div class="table-wrap"><table class="data-table"><thead><tr><th>Ticket</th><th>Type</th><th>Plate</th><th>Floor</th><th>Spot</th><th>Entry</th><th>Rate</th><th>Duration</th><th>Status</th></tr></thead><tbody id="entry-tbody"></tbody></table></div>
      </div>
    </div>

    <!-- ═══ EXIT ═══ -->
    <div class="page" id="page-exit">
      <div class="two-col">
        <div>
          <div class="panel">
            <div class="panel-header"><span class="panel-title">🎫 Vehicle Checkout</span></div>
            <div class="panel-body">
              <div class="form-group">
                <label class="form-label">Search — Ticket or Plate</label>
                <div class="flex gap-10">
                  <input type="text" class="form-input" id="exit-search" placeholder="VM-000001 or AP09AB1234" oninput="this.value=this.value.toUpperCase()" onkeydown="if(event.key==='Enter')doSearch()" style="flex:1">
                  <button class="btn btn-secondary" onclick="doSearch()">🔍</button>
                </div>
              </div>
              <div id="found-section" style="display:none">
                <div class="info-box" id="found-info"></div>
                <div class="amount-box"><div class="amount-lbl">Total Due</div><div class="amount-val" id="found-amount">₹0</div><div class="amount-discount" id="found-discount-line" style="display:none"></div></div>
                <div class="info-box" id="found-breakdown"></div>
                <div class="flex gap-10 mb-14">
                  <button class="btn btn-success btn-full btn-lg" onclick="doCheckout()">✅ CHECKOUT &nbsp;<span style="font-size:10px;opacity:.6">(Enter)</span></button>
                  <button class="btn btn-info btn-sm" onclick="previewBill()">🧾</button>
                </div>
              </div>
              <div id="not-found" style="display:none" class="empty-state"><div class="empty-icon">🔍</div><p>No active vehicle found</p></div>
              <!-- LOST TICKET -->
              <div class="lost-section">
                <div class="lost-title">🚨 Lost Ticket? &nbsp;+₹50 Penalty</div>
                <div class="flex gap-10">
                  <input type="text" class="form-input" id="lost-plate" placeholder="Enter license plate" oninput="this.value=this.value.toUpperCase()" style="flex:1">
                  <button class="btn btn-danger" onclick="handleLostTicket()">Find</button>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div>
          <div class="panel">
            <div class="panel-header"><span class="panel-title">Currently Parked</span><span class="stat-sub" id="active-count">0</span></div>
            <div class="search-row"><input class="table-search" placeholder="🔍  Search..." oninput="filterTable(this,'active-tbody')"></div>
            <div class="table-wrap" style="max-height:360px;overflow-y:auto"><table class="data-table"><thead><tr><th>Ticket</th><th>Type</th><th>Plate</th><th>Floor</th><th>Since</th><th>Est.</th><th>Status</th><th></th></tr></thead><tbody id="active-tbody"></tbody></table></div>
          </div>
        </div>
      </div>
      <div class="panel">
        <div class="panel-header"><span class="panel-title">Today's Checkouts</span><span class="stat-sub" id="checkout-count">0</span></div>
        <div class="search-row"><input class="table-search" placeholder="🔍  Search..." oninput="filterTable(this,'checkout-tbody')"></div>
        <div class="table-wrap"><table class="data-table"><thead><tr><th>Ticket</th><th>Type</th><th>Plate</th><th>Floor</th><th>Duration</th><th>Exit</th><th>Rate</th><th>Discount</th><th>Amount</th></tr></thead><tbody id="checkout-tbody"></tbody></table></div>
      </div>
    </div>

    <!-- ═══ MAP ═══ -->
    <div class="page" id="page-map">
      <div class="stats-grid">
        <div class="stat-card-wrap"><div class="stat-card blue"><div class="stat-label">Car Spots</div><div class="stat-value">300</div><div class="stat-sub">100/floor</div></div></div>
        <div class="stat-card-wrap"><div class="stat-card green"><div class="stat-label">Bike Spots</div><div class="stat-value">600</div><div class="stat-sub">200/floor</div></div></div>
        <div class="stat-card-wrap"><div class="stat-card gold"><div class="stat-label">Reserved</div><div class="stat-value" id="s-reserved">0</div><div class="stat-sub">VIP spots</div></div></div>
        <div class="stat-card-wrap"><div class="stat-card amber"><div class="stat-label">Area</div><div class="stat-value">6 Ac</div><div class="stat-sub">3 levels</div></div></div>
      </div>
      <div class="panel">
        <div class="panel-header">
          <span class="panel-title">🗺️ Floor Map</span>
          <div class="floor-tabs"><button class="floor-tab active" id="ft1" onclick="selFloor(1)">Floor 1</button><button class="floor-tab" id="ft2" onclick="selFloor(2)">Floor 2</button><button class="floor-tab" id="ft3" onclick="selFloor(3)">Floor 3</button></div>
        </div>
        <div class="panel-body">
          <div class="map-type-tabs"><button class="map-type-tab act-car" id="mt-car" onclick="selMapType('car')">🚗 Cars</button><button class="map-type-tab" id="mt-bike" onclick="selMapType('bike')">🏍️ Bikes</button></div>
          <div class="reserve-controls">
            <input type="text" class="form-input" id="reserve-spot-id" placeholder="Spot ID e.g. C-F1-001" style="flex:1;max-width:200px">
            <button class="btn btn-gold btn-sm" onclick="reserveSpot()">⭐ Reserve</button>
            <button class="btn btn-danger btn-sm" onclick="clearReservation()">✕ Clear</button>
          </div>
          <div class="info-box" id="map-stat-box"></div>
          <div class="spots-grid" id="spots-grid"></div>
          <div class="map-legend">
            <div class="legend-item"><div class="legend-dot" style="background:rgba(16,185,129,.2);border:1px solid rgba(16,185,129,.4)"></div>Available</div>
            <div class="legend-item"><div class="legend-dot" style="background:rgba(239,68,68,.2);border:1px solid rgba(239,68,68,.4)"></div>Occupied</div>
            <div class="legend-item"><div class="legend-dot" style="background:rgba(255,215,0,.2);border:1px solid rgba(255,215,0,.4)"></div>Reserved (VIP)</div>
          </div>
        </div>
      </div>
    </div>

    <!-- ═══ REPORTS ═══ -->
    <div class="page" id="page-reports">
      <div class="stats-grid">
        <div class="stat-card-wrap"><div class="stat-card amber"><div class="stat-label">Today's Revenue</div><div class="stat-value" id="r-today-rev">₹0</div><div class="stat-sub" id="r-today-txn">0 txns</div></div></div>
        <div class="stat-card-wrap"><div class="stat-card blue"><div class="stat-label">All-Time Revenue</div><div class="stat-value" id="r-total-rev">₹0</div><div class="stat-sub" id="r-total-txn">0 total</div></div></div>
        <div class="stat-card-wrap"><div class="stat-card green"><div class="stat-label">Cars Serviced</div><div class="stat-value" id="r-cars">0</div><div class="stat-sub">all time</div></div></div>
        <div class="stat-card-wrap"><div class="stat-card purple"><div class="stat-label">Bikes Serviced</div><div class="stat-value" id="r-bikes">0</div><div class="stat-sub">all time</div></div></div>
      </div>
      <div class="two-col">
        <div class="panel"><div class="panel-header"><span class="panel-title">📈 Hourly Entries</span></div><div class="panel-body"><div class="chart-container chart-md"><canvas id="chart-hourly"></canvas></div></div></div>
        <div class="panel"><div class="panel-header"><span class="panel-title">📊 Occupancy Timeline</span></div><div class="panel-body"><div class="chart-container chart-md"><canvas id="chart-occupancy"></canvas></div></div></div>
      </div>
      <div class="panel"><div class="panel-header"><span class="panel-title">🗓️ Revenue Calendar — Last 90 Days</span></div><div class="panel-body"><div id="heat-calendar"></div></div></div>
      <div class="panel">
        <div class="panel-header">
          <span class="panel-title">📋 Transactions</span>
          <div class="flex gap-10">
            <button class="btn btn-sm btn-success" onclick="exportCSV()">📥 CSV</button>
            <button class="btn btn-sm btn-danger" onclick="if(confirm('Clear ALL?'))clearData()">🗑️ Clear</button>
          </div>
        </div>
        <div class="panel-body" style="padding-bottom:0">
          <div class="two-col" style="margin-bottom:10px">
            <div class="form-group" style="margin:0"><label class="form-label">From</label><input type="date" class="form-input" id="r-from" onchange="renderReports()"></div>
            <div class="form-group" style="margin:0"><label class="form-label">To</label><input type="date" class="form-input" id="r-to" onchange="renderReports()"></div>
          </div>
        </div>
        <div class="search-row"><input class="table-search" placeholder="🔍  Search..." oninput="filterTable(this,'reports-tbody')"><span class="stat-sub" id="r-count">0 records</span></div>
        <div class="table-wrap"><table class="data-table"><thead><tr><th>Ticket</th><th>Date</th><th>Type</th><th>Plate</th><th>Floor</th><th>Entry</th><th>Exit</th><th>Duration</th><th>Rate</th><th>Discount</th><th>Amount</th></tr></thead><tbody id="reports-tbody"></tbody></table></div>
      </div>
    </div>

    <!-- ═══ VEHICLE HISTORY ═══ -->
    <div class="page" id="page-history">
      <div class="panel">
        <div class="panel-header"><span class="panel-title">🔍 Vehicle History Lookup</span></div>
        <div class="panel-body">
          <div class="flex gap-10 mb-14">
            <input type="text" class="form-input" id="history-plate" placeholder="Enter License Plate e.g. AP09AB1234" oninput="this.value=this.value.toUpperCase()" onkeydown="if(event.key==='Enter')lookupHistory()" style="flex:1">
            <button class="btn btn-primary" onclick="lookupHistory()">🔍 Lookup</button>
          </div>
          <div id="history-result" style="display:none">
            <div class="history-header" id="history-header"></div>
            <div class="table-wrap"><table class="data-table"><thead><tr><th>Ticket</th><th>Date</th><th>Type</th><th>Floor</th><th>Entry</th><th>Exit</th><th>Duration</th><th>Rate</th><th>Amount</th></tr></thead><tbody id="history-tbody"></tbody></table></div>
          </div>
          <div id="history-empty" style="display:none" class="empty-state"><div class="empty-icon">🔍</div><p>No history found for this plate</p></div>
          <div id="history-currently-parked" style="display:none" class="info-box mt-10">
            <div class="info-row"><span class="info-lbl" style="color:var(--amber)">⚠️ Currently Parked</span><span class="info-val" id="history-active-info">—</span></div>
          </div>
        </div>
      </div>
    </div>

    <!-- ═══ ACTIVITY LOG ═══ -->
    <div class="page" id="page-log">
      <div class="panel">
        <div class="panel-header">
          <span class="panel-title">📋 Employee Activity Log</span>
          <div class="flex gap-10">
            <button class="btn btn-sm btn-secondary" onclick="renderLog()">↻ Refresh</button>
            <button class="btn btn-sm btn-danger" onclick="if(confirm('Clear log?')){localStorage.removeItem('vm_log');renderLog();}">🗑️ Clear Log</button>
          </div>
        </div>
        <div class="search-row"><input class="table-search" placeholder="🔍  Filter log..." oninput="filterLog(this.value)"></div>
        <div class="panel-body" style="padding-top:0">
          <div class="log-list" id="log-list"></div>
        </div>
      </div>
    </div>

  </div><!-- /main -->
</div><!-- /app -->

<!-- BILL MODAL -->
<div class="modal-overlay" id="bill-modal" onclick="if(event.target===this)closeBill()">
  <div class="modal">
    <div class="modal-header"><span class="modal-title">🧾 Parking Receipt</span><button class="modal-close" onclick="closeBill()">×</button></div>
    <div class="modal-body">
      <div id="bill-html"></div>
      <div class="wa-btn-wrap"><a id="wa-link" href="#" target="_blank" class="btn btn-whatsapp btn-full" style="text-decoration:none;display:flex">📱 Send via WhatsApp</a></div>
      <div class="flex gap-10 mt-10"><button class="btn btn-primary btn-full" onclick="window.print()">🖨️ Print</button><button class="btn btn-secondary" onclick="closeBill()">Close</button></div>
    </div>
  </div>
</div>

<!-- HANDOVER MODAL -->
<div class="modal-overlay" id="handover-modal" onclick="if(event.target===this)closeHandover()">
  <div class="modal modal-lg">
    <div class="modal-header"><span class="modal-title">📝 Shift Handover Report</span><button class="modal-close" onclick="closeHandover()">×</button></div>
    <div class="modal-body">
      <div id="handover-html"></div>
      <div class="flex gap-10 mt-10"><button class="btn btn-primary btn-full" onclick="window.print()">🖨️ Print Report</button><button class="btn btn-secondary" onclick="closeHandover()">Close</button></div>
    </div>
  </div>
</div>

<div class="notification" id="notif"></div>
<div class="kb-hint" id="kb-hint">
  <h4>⌨️ Shortcuts</h4>
  <div class="kb-row"><span>Dashboard</span><span class="kb-key">Ctrl+D</span></div>
  <div class="kb-row"><span>Entry</span><span class="kb-key">Ctrl+E</span></div>
  <div class="kb-row"><span>Exit</span><span class="kb-key">Ctrl+X</span></div>
  <div class="kb-row"><span>Map</span><span class="kb-key">Ctrl+M</span></div>
  <div class="kb-row"><span>Reports</span><span class="kb-key">Ctrl+R</span></div>
  <div class="kb-row"><span>History</span><span class="kb-key">Ctrl+H</span></div>
  <div class="kb-row"><span>CSV Export</span><span class="kb-key">Ctrl+S</span></div>
  <div class="kb-row"><span>Submit</span><span class="kb-key">Enter</span></div>
</div>

<script>
'use strict';
// ══════════════════════════════════════════════
// PRICING
// ══════════════════════════════════════════════
const PP={
  normal: {label:'Standard',icon:'🟡',car:{first:50,extra:30},bike:{first:20,extra:10},color:'normal'},
  peak:   {label:'Peak Hour',icon:'🔥',car:{first:80,extra:50},bike:{first:35,extra:20},color:'peak'},
  evening:{label:'Evening',  icon:'🌇',car:{first:40,extra:25},bike:{first:15,extra:8 },color:'evening'},
  night:  {label:'Night',    icon:'🌙',car:{first:30,extra:15},bike:{first:12,extra:6 },color:'night'}
};
function getPP(date){
  const h=(date||new Date()).getHours();
  if(h>=6&&h<10)return 'normal';
  if(h>=10&&h<14)return 'peak';
  if(h>=14&&h<17)return 'normal';
  if(h>=17&&h<21)return 'peak';
  if(h>=21&&h<24)return 'evening';
  return 'night';
}
function getNextChange(){
  const h=new Date().getHours(),m=new Date().getMinutes();
  for(const t of [0,6,10,14,17,21,24]){const d=(t*60)-(h*60+m);if(d>0){const hr=Math.floor(d/60),mn=d%60;return `${hr>0?hr+'h ':''}${mn}m`;}}
  return '';
}

// ══════════════════════════════════════════════
// SHIFTS
// ══════════════════════════════════════════════
function getShift(){const h=new Date().getHours();if(h>=6&&h<14)return 'morning';if(h>=14&&h<22)return 'afternoon';return 'night';}
function getShiftForH(h){if(h>=6&&h<14)return 'morning';if(h>=14&&h<22)return 'afternoon';return 'night';}
const SM={
  morning:  {label:'Morning Shift',  icon:'🌅',cls:'shift-morning',  role:'Morning Duty Staff'},
  afternoon:{label:'Afternoon Shift',icon:'🌇',cls:'shift-afternoon',role:'Afternoon Duty Staff'},
  night:    {label:'Night Shift',    icon:'🌙',cls:'shift-night',    role:'Night Duty Staff'}
};

// ══════════════════════════════════════════════
// STORAGE
// ══════════════════════════════════════════════
const K={active:'vm_active',txns:'vm_txns',ctr:'vm_ctr',theme:'vm_theme',reservations:'vm_res',log:'vm_log'};
const getActive =()=>JSON.parse(localStorage.getItem(K.active)||'[]');
const saveActive=d=>localStorage.setItem(K.active,JSON.stringify(d));
const getTxns   =()=>JSON.parse(localStorage.getItem(K.txns)  ||'[]');
const saveTxns  =d=>localStorage.setItem(K.txns,  JSON.stringify(d));
const getRes    =()=>JSON.parse(localStorage.getItem(K.reservations)||'[]');
const saveRes   =d=>localStorage.setItem(K.reservations,JSON.stringify(d));
const getLogs   =()=>JSON.parse(localStorage.getItem(K.log)||'[]');
const saveLogs  =d=>localStorage.setItem(K.log,JSON.stringify(d));
function nextTicket(){const n=(parseInt(localStorage.getItem(K.ctr)||'0'))+1;localStorage.setItem(K.ctr,n);return 'VM-'+String(n).padStart(6,'0');}

// Log
function addLog(type,text){
  const logs=getLogs();
  logs.unshift({type,text,time:new Date().toISOString()});
  if(logs.length>200)logs.length=200;
  saveLogs(logs);
}

// ══════════════════════════════════════════════
// STATE
// ══════════════════════════════════════════════
let vType='car',mapFloor=1,mapType='car',foundVehicle=null,discountPct=0,charts={},audioCtx=null;
let lastRevenue=0;
const CFG={password:'vmall2024',carsPerFloor:100,bikesPerFloor:200,floors:3,lowThreshold:.10,overstayHours:8};

// ══════════════════════════════════════════════
// PARTICLES
// ══════════════════════════════════════════════
class ParticleSys{
  constructor(c){this.c=c;this.ctx=c.getContext('2d');this.p=[];this.raf=null;this.sh=null;window.addEventListener('resize',()=>this.resize());this.resize();}
  resize(){this.c.width=window.innerWidth;this.c.height=window.innerHeight;}
  init(sh){
    this.sh=sh;this.p=[];
    if(sh==='morning'){for(let i=0;i<70;i++)this.p.push({x:Math.random()*this.c.width,y:Math.random()*this.c.height,r:Math.random()*1.8+.4,vx:(Math.random()-.5)*.25,vy:-(Math.random()*.35+.05),a:Math.random()*.3+.05,c:'245,158,11'});}
    else if(sh==='afternoon'){for(let i=0;i<50;i++)this.p.push({x:Math.random()*this.c.width,y:Math.random()*this.c.height,r:Math.random()*2.5+.8,vx:(Math.random()-.5)*.55,vy:(Math.random()-.5)*.45,a:Math.random()*.22+.06,c:i%2===0?'59,130,246':'249,115,22',pu:Math.random()*Math.PI*2,ps:Math.random()*.04+.01});}
    else{for(let i=0;i<120;i++)this.p.push({x:Math.random()*this.c.width,y:Math.random()*this.c.height,r:Math.random()*1.6+.3,vx:(Math.random()-.5)*.04,vy:(Math.random()-.5)*.04,a:Math.random()*.55+.2,c:'200,210,255',tw:Math.random()*Math.PI*2,ts:Math.random()*.025+.005});}
  }
  draw(){
    const ctx=this.ctx;ctx.clearRect(0,0,this.c.width,this.c.height);
    for(const p of this.p){
      if(this.sh==='night'){p.tw+=p.ts;const a=p.a*(.5+.5*Math.sin(p.tw));ctx.beginPath();ctx.arc(p.x,p.y,p.r,0,Math.PI*2);ctx.fillStyle=`rgba(${p.c},${a})`;ctx.fill();}
      else if(this.sh==='afternoon'){p.pu+=p.ps;const a=p.a*(.7+.3*Math.sin(p.pu));ctx.beginPath();ctx.arc(p.x,p.y,p.r,0,Math.PI*2);ctx.fillStyle=`rgba(${p.c},${a})`;ctx.fill();}
      else{ctx.beginPath();ctx.arc(p.x,p.y,p.r,0,Math.PI*2);ctx.fillStyle=`rgba(${p.c},${p.a})`;ctx.fill();}
      p.x+=p.vx;p.y+=p.vy;
      if(p.x<-10)p.x=this.c.width+10;if(p.x>this.c.width+10)p.x=-10;
      if(p.y<-10)p.y=this.c.height+10;if(p.y>this.c.height+10)p.y=-10;
    }
  }
  start(sh){if(this.raf)cancelAnimationFrame(this.raf);this.init(sh);const loop=()=>{this.draw();this.raf=requestAnimationFrame(loop);};loop();}
}
let ptcls;

// Night sky sidebar stars
function buildNightSky(){
  const el=document.getElementById('night-sky'); if(!el)return;
  let html='<div class="night-moon"></div>';
  for(let i=0;i<60;i++){
    const x=Math.random()*100,y=Math.random()*100,s=Math.random()*2+.5;
    const dur=(Math.random()*3+2).toFixed(1),del=(Math.random()*4).toFixed(1);
    html+=`<div class="night-star" style="left:${x}%;top:${y}%;width:${s}px;height:${s}px;--dur:${dur}s;--delay:${del}s;"></div>`;
  }
  el.innerHTML=html;
}

// ══════════════════════════════════════════════
// AUDIO
// ══════════════════════════════════════════════
function initAudio(){if(!audioCtx)try{audioCtx=new(window.AudioContext||window.webkitAudioContext)();}catch(e){}}
function beep(freq,dur,type='sine',vol=.2){try{initAudio();if(!audioCtx)return;const o=audioCtx.createOscillator(),g=audioCtx.createGain();o.connect(g);g.connect(audioCtx.destination);o.frequency.value=freq;o.type=type;g.gain.setValueAtTime(vol,audioCtx.currentTime);g.gain.exponentialRampToValueAtTime(.001,audioCtx.currentTime+dur);o.start();o.stop(audioCtx.currentTime+dur);}catch(e){}}
function playEntry(){beep(880,.1);setTimeout(()=>beep(1100,.12),120);setTimeout(()=>beep(1320,.1),230);}
function playExit(){beep(660,.1);setTimeout(()=>beep(550,.1),100);setTimeout(()=>beep(440,.18),190);}
function playError(){beep(220,.3,'sawtooth',.12);}

// ══════════════════════════════════════════════
// GATE ANIMATION
// ══════════════════════════════════════════════
function showGate(){
  const w=document.getElementById('gate-wrap');
  const gl=document.getElementById('gate-left');
  const gr=document.getElementById('gate-right');
  w.style.display='flex';
  gl.style.transition='transform .6s ease';
  gr.style.transition='transform .6s ease';
  gl.style.transform='scaleX(0)';
  gr.style.transform='scaleX(0)';
  setTimeout(()=>{
    w.style.display='none';
    gl.style.transition='none';gr.style.transition='none';
    gl.style.transform='scaleX(1)';gr.style.transform='scaleX(1)';
  },900);
}

// COUNTER FLY
function showCounterFly(type='entry'){
  const el=document.getElementById('counter-fly');
  const text=type==='entry'?'+1':'-1';
  const color=type==='entry'?'var(--green)':'var(--blue)';
  el.textContent=text;
  el.style.color=color;
  el.style.display='block';
  el.style.top=(100+Math.random()*200)+'px';
  el.style.left=(window.innerWidth/2-60)+'px';
  el.className='counter-fly show';
  setTimeout(()=>{el.className='counter-fly';el.style.display='none';},950);
}

// ══════════════════════════════════════════════
// AUTH
// ══════════════════════════════════════════════
function doLogin(){
  initAudio();
  if(document.getElementById('login-pass').value===CFG.password){
    document.getElementById('login-screen').style.display='none';
    document.getElementById('app').style.display='block';
    initApp();
  }else{playError();const e=document.getElementById('login-error');e.style.display='block';document.getElementById('login-pass').value='';setTimeout(()=>e.style.display='none',3500);}
}
function doLogout(){document.getElementById('app').style.display='none';document.getElementById('login-screen').style.display='flex';document.getElementById('login-pass').value='';}

// ══════════════════════════════════════════════
// INIT
// ══════════════════════════════════════════════
function initApp(){
  ptcls=new ParticleSys(document.getElementById('particle-canvas'));
  buildNightSky();
  applyShift(getShift());
  applyTheme(localStorage.getItem(K.theme)||'dark');
  startClock();
  setDefaultDates();
  refreshAll();
}
function refreshAll(){
  updateDashboard();renderFloorOcc();buildWeeklyChart();buildTypeChart();buildShiftRev();renderRecentTable();
  updateAvailInfo();renderEntryTable();renderActiveTable();renderCheckoutTable();
  renderMapGrid();renderMapStat();renderReports();updateActiveBadge();checkAlerts();
  updatePricingUI();buildFullRateTable();updateTicker();updateReservedCount();
}
function setDefaultDates(){const td=today();const wa=new Date(Date.now()-7*86400000).toISOString().split('T')[0];document.getElementById('r-from').value=wa;document.getElementById('r-to').value=td;}
const today=()=>new Date().toISOString().split('T')[0];

// ══════════════════════════════════════════════
// 12-HOUR CLOCK
// ══════════════════════════════════════════════
function startClock(){
  const tick=()=>{
    const t=new Date().toLocaleTimeString('en-IN',{hour:'2-digit',minute:'2-digit',second:'2-digit',hour12:true}).toUpperCase();
    document.getElementById('clock').textContent=t;
    updatePricingUI();
  };
  tick();setInterval(tick,1000);
  setInterval(()=>{
    const s=getShift();if(!document.body.classList.contains(SM[s].cls))applyShift(s);
    renderActiveTable();if(foundVehicle)updateFoundCharge();
    checkOverstays();
  },30000);
  setInterval(checkAlerts,60000);
}

// ══════════════════════════════════════════════
// SHIFT
// ══════════════════════════════════════════════
function applyShift(sh){
  const meta=SM[sh];
  document.body.className=meta.cls;
  setT('brand-icon',meta.icon); setT('brand-name','V MALL');
  setT('shift-pill-text',meta.label); setT('emp-role',meta.role);
  setT('login-sub',`${meta.label} · Parking Control v4`);
  if(ptcls)ptcls.start(sh);
  const pp=getPP(); updateLoginRate(pp);
}
function updateLoginRate(pp){
  const pr=PP[pp];
  setT('lr-title',`${pr.icon} ${pr.label} — Active Now`);
  setT('lr-car',`₹${pr.car.first} first · ₹${pr.car.extra}/hr`);
  setT('lr-bike',`₹${pr.bike.first} first · ₹${pr.bike.extra}/hr`);
}

// ══════════════════════════════════════════════
// PRICING UI
// ══════════════════════════════════════════════
function updatePricingUI(){
  const pp=getPP(); const pr=PP[pp];
  const pb=document.getElementById('price-badge');
  if(pb){pb.className=`price-badge ${pp}`;pb.innerHTML=`${pr.icon} ${pr.label}`;pb.title=`Next change: ${getNextChange()}`;}
  const rc=document.getElementById('entry-rate-card');
  if(rc){
    rc.className=`rate-card ${pp}`;
    rc.innerHTML=`<div class="rate-card-label">${pr.icon} ${pr.label} — Active Now</div>
    <div class="rate-card-grid">
      <div class="rate-item"><div class="rate-item-icon">🚗</div><div class="rate-item-val">₹${pr.car.first}</div><div class="rate-item-lbl">Car First Hr</div></div>
      <div class="rate-item"><div class="rate-item-icon">🚗</div><div class="rate-item-val">₹${pr.car.extra}</div><div class="rate-item-lbl">Car Extra Hr</div></div>
      <div class="rate-item"><div class="rate-item-icon">🏍️</div><div class="rate-item-val">₹${pr.bike.first}</div><div class="rate-item-lbl">Bike First Hr</div></div>
      <div class="rate-item"><div class="rate-item-icon">🏍️</div><div class="rate-item-val">₹${pr.bike.extra}</div><div class="rate-item-lbl">Bike Extra Hr</div></div>
    </div>
    <div class="rate-change-info">⏱️ ${getNextChange()} to next rate change</div>`;
  }
  const cs=document.getElementById('car-rate-sub'); if(cs)cs.textContent=`₹${pr.car.first}·₹${pr.car.extra}/hr`;
  const bs=document.getElementById('bike-rate-sub');if(bs)bs.textContent=`₹${pr.bike.first}·₹${pr.bike.extra}/hr`;
  setT('entry-shift',SM[getShift()].label);
}
function buildFullRateTable(){
  const cur=getPP();
  const rows=[
    ['normal', 'Standard (6-10AM, 2-5PM)'],
    ['peak',   'Peak (10AM-2PM, 5-9PM)'],
    ['evening','Evening (9PM-12AM)'],
    ['night',  'Night (12-6AM)']
  ];
  const el=document.getElementById('full-rate-table'); if(!el)return;
  el.innerHTML=rows.map(([p,lbl])=>{
    const pr=PP[p]; const isA=p===cur;
    return `<div class="pricing-row${isA?' active-rate':''}">
      <span class="pricing-lbl">${pr.icon} ${lbl}${isA?' ← now':''}</span>
      <span class="pricing-val" style="font-size:10px;text-align:right;line-height:1.7">
        <span style="color:var(--blue)">🚗 ₹${pr.car.first}+₹${pr.car.extra}/hr</span><br>
        <span style="color:var(--green)">🏍️ ₹${pr.bike.first}+₹${pr.bike.extra}/hr</span>
      </span></div>`;
  }).join('');
}

// ══════════════════════════════════════════════
// REVENUE TICKER & PREDICTION
// ══════════════════════════════════════════════
function updateTicker(){
  const td=today(); const txns=getTxns();
  const todayT=txns.filter(t=>t.exitDate===td);
  const todayR=todayT.reduce((s,t)=>s+t.amt,0);
  const active=getActive();
  const rv=document.getElementById('ticker-rev');
  const tv=document.getElementById('ticker-txn');
  const av=document.getElementById('ticker-active');
  if(rv&&todayR!==lastRevenue){rv.textContent='₹'+todayR.toLocaleString('en-IN');rv.classList.remove('updated');void rv.offsetWidth;rv.classList.add('updated');lastRevenue=todayR;}
  if(tv)tv.textContent=todayT.length;
  if(av)av.textContent=active.length;
  // Prediction
  const pred=document.getElementById('ticker-pred');
  if(pred){
    const avg7=computeAvgRevenue7Days();
    const h=new Date().getHours(); const m=new Date().getMinutes();
    const elapsed=(h*60+m);const remaining=Math.max(0,(18*60)-elapsed);
    const dayProgress=elapsed/(24*60);
    const projected=elapsed>0?Math.round(todayR+(avg7*remaining/(24*60))):avg7;
    pred.textContent=`📊 Projected by 6PM: ₹${projected.toLocaleString('en-IN')}`;
  }
}
function computeAvgRevenue7Days(){
  const txns=getTxns(); const td=today(); let total=0,days=0;
  for(let i=1;i<=7;i++){
    const d=new Date(Date.now()-i*86400000).toISOString().split('T')[0];
    const r=txns.filter(t=>t.exitDate===d).reduce((s,t)=>s+t.amt,0);
    if(r>0){total+=r;days++;}
  }
  return days>0?Math.round(total/days):0;
}

// ══════════════════════════════════════════════
// THEME & FULLSCREEN
// ══════════════════════════════════════════════
function applyTheme(t){document.body.classList.toggle('light',t==='light');localStorage.setItem(K.theme,t);const ic=document.getElementById('theme-icon');const lb=document.getElementById('theme-label');if(ic)ic.textContent=t==='light'?'☀️':'🌙';if(lb)lb.textContent=t==='light'?'Light':'Dark';setTimeout(()=>{buildWeeklyChart();buildTypeChart();buildHourlyChart();buildOccupancyChart();},100);}
function toggleTheme(){applyTheme(document.body.classList.contains('light')?'dark':'light');}
function toggleFullscreen(){if(!document.fullscreenElement)document.documentElement.requestFullscreen().catch(()=>{});else document.exitFullscreen().catch(()=>{});}
function toggleKbHint(){document.getElementById('kb-hint').classList.toggle('show');}

// KEYBOARD
document.addEventListener('keydown',e=>{
  if(e.key==='Escape'){closeBill();closeHandover();document.getElementById('kb-hint').classList.remove('show');return;}
  if(!e.ctrlKey)return;
  const map={d:'dashboard',e:'entry',x:'exit',m:'map',r:'reports',h:'history'};
  const pg=map[e.key.toLowerCase()];
  if(pg){e.preventDefault();showPage(pg);return;}
  if(e.key.toLowerCase()==='s'){e.preventDefault();exportCSV();}
});

// ══════════════════════════════════════════════
// NAV
// ══════════════════════════════════════════════
const PT={dashboard:'📊 &nbsp;Dashboard',entry:'🚗 &nbsp;Vehicle Entry',exit:'🎫 &nbsp;Exit & Billing',map:'🗺️ &nbsp;Floor Map',reports:'📈 &nbsp;Reports',history:'🔍 &nbsp;Vehicle History',log:'📋 &nbsp;Activity Log'};
function showPage(id){
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.querySelectorAll('.nav-item').forEach(n=>n.classList.remove('active'));
  document.getElementById('page-'+id).classList.add('active');
  const ni=document.getElementById('nav-'+id); if(ni)ni.classList.add('active');
  document.getElementById('topbar-title').innerHTML=PT[id];
  if(id==='dashboard'){updateDashboard();renderFloorOcc();buildWeeklyChart();buildTypeChart();buildShiftRev();renderRecentTable();updateTicker();}
  if(id==='entry'){updateAvailInfo();renderEntryTable();updatePricingUI();buildFullRateTable();}
  if(id==='exit'){renderActiveTable();renderCheckoutTable();}
  if(id==='map'){renderMapGrid();renderMapStat();updateReservedCount();}
  if(id==='reports'){renderReports();}
  if(id==='history'){document.getElementById('history-result').style.display='none';document.getElementById('history-empty').style.display='none';}
  if(id==='log'){renderLog();}
}

// ══════════════════════════════════════════════
// SPOTS
// ══════════════════════════════════════════════
function occupiedSpots(type){return getActive().filter(v=>v.type===type).map(v=>v.spotId);}
function availCount(type){const total=type==='car'?CFG.carsPerFloor*3:CFG.bikesPerFloor*3;return total-occupiedSpots(type).length;}
function findSpot(type,pref){
  const count=type==='car'?CFG.carsPerFloor:CFG.bikesPerFloor;
  const pfx=type==='car'?'C':'B'; const occ=occupiedSpots(type); const res=getRes();
  const floors=pref==='auto'?[1,2,3]:[parseInt(pref),...[1,2,3].filter(f=>f!==parseInt(pref))];
  for(const fl of floors)for(let s=1;s<=count;s++){
    const sid=`${pfx}-F${fl}-${String(s).padStart(3,'0')}`;
    if(!occ.includes(sid)&&!res.includes(sid))return{floor:fl,num:s,spotId:sid};
  }
  return null;
}

// ══════════════════════════════════════════════
// SPOT RESERVATION
// ══════════════════════════════════════════════
function reserveSpot(){
  const sid=document.getElementById('reserve-spot-id').value.trim().toUpperCase();
  if(!sid){notify('Enter a spot ID','error');return;}
  const res=getRes();
  if(!res.includes(sid)){res.push(sid);saveRes(res);addLog('reserve',`Reserved spot <strong>${sid}</strong> as VIP`);}
  notify(`⭐ Spot ${sid} reserved`,'success');
  document.getElementById('reserve-spot-id').value='';
  renderMapGrid();updateReservedCount();
}
function clearReservation(){
  const sid=document.getElementById('reserve-spot-id').value.trim().toUpperCase();
  const res=getRes().filter(r=>r!==sid);saveRes(res);
  notify('Reservation cleared','info');document.getElementById('reserve-spot-id').value='';
  renderMapGrid();updateReservedCount();
}
function updateReservedCount(){setT('s-reserved',getRes().length);}

// ══════════════════════════════════════════════
// DISCOUNT
// ══════════════════════════════════════════════
function selectDiscount(pct){
  discountPct=pct;
  ['disc-0','disc-10','disc-20'].forEach((id,i)=>{
    document.getElementById(id).className='discount-btn'+(i===0?' discount-none':'')+(pct===[0,10,20][i]?' selected':'');
  });
}

// ══════════════════════════════════════════════
// VEHICLE ENTRY
// ══════════════════════════════════════════════
function selectVType(t){vType=t;document.getElementById('btn-car').className='type-btn'+(t==='car'?' sel-car':'');document.getElementById('btn-bike').className='type-btn'+(t==='bike'?' sel-bike':'');}
function updateAvailInfo(){setT('avail-car',`${availCount('car')}/300`);setT('avail-bike',`${availCount('bike')}/600`);}
function doEntry(){
  const plate=document.getElementById('inp-plate').value.trim();
  const fpref=document.getElementById('inp-floor').value;
  if(!plate){notify('Enter license plate','error');playError();return;}
  const active=getActive();
  if(active.find(v=>v.plate===plate.replace(/\s/g,''))){notify('Vehicle already parked!','error');playError();return;}
  const spot=findSpot(vType,fpref);
  if(!spot){notify(`No ${vType} spots!`,'error');playError();return;}
  const pp=getPP(); const pr=PP[pp]; const now=new Date();
  const v={ticketId:nextTicket(),type:vType,plate:plate.replace(/\s/g,''),plateDisp:plate,
    floor:spot.floor,spotId:spot.spotId,entryTime:now.toISOString(),entryDate:today(),
    shift:SM[getShift()].label,pricingPeriod:pp,rateLabel:pr.label,
    rateAtEntry:{car:{...pr.car},bike:{...pr.bike}},discount:discountPct};
  active.push(v);saveActive(active);
  showGate(); showCounterFly('entry');
  showGeneratedTicket(v);playEntry();
  addLog('entry',`Vehicle <strong>${plate}</strong> checked in → ${spot.spotId} [${pr.label}]${discountPct?` with ${discountPct}% discount`:''}`);
  notify(`✅ ${plate} → ${spot.spotId}`,'success');
  document.getElementById('inp-plate').value=''; document.getElementById('inp-floor').value='auto';
  selectDiscount(0);
  updateAvailInfo();renderEntryTable();updateDashboard();updateActiveBadge();checkAlerts();updateTicker();
  // Animate the spot on map
  setTimeout(()=>flashSpot(spot.spotId,'occupied'),200);
}
function flashSpot(sid,cls){
  const el=document.querySelector(`[data-sid="${sid}"]`);
  if(el){el.classList.add('just-'+cls);setTimeout(()=>el.classList.remove('just-'+cls),900);}
}

// ══════════════════════════════════════════════
// QR CODE
// ══════════════════════════════════════════════
function genQR(id,text,size=90){
  const el=document.getElementById(id);if(!el)return;el.innerHTML='';
  try{new QRCode(el,{text,width:size,height:size,colorDark:'#000',colorLight:'#fff',correctLevel:QRCode.CorrectLevel.M});}
  catch(e){el.innerHTML='<span style="font-size:9px;color:#aaa">QR N/A</span>';}
}

// TICKET
function showGeneratedTicket(v){
  const pr=PP[v.pricingPeriod||'normal'];
  document.getElementById('ticket-panel').style.display='block';
  document.getElementById('ticket-body').innerHTML=`
    <div style="background:var(--surface2);border:1px dashed rgba(245,158,11,.35);border-radius:11px;padding:16px;">
      <div style="font-size:9px;letter-spacing:2px;color:var(--text-muted);text-align:center;margin-bottom:7px;text-transform:uppercase">V MALL — ENTRY TICKET</div>
      <div style="font-family:'DM Mono',monospace;font-size:18px;color:var(--amber);letter-spacing:3px;text-align:center;margin-bottom:10px">${v.ticketId}</div>
      <div style="display:flex;justify-content:center;margin:8px 0;padding:8px;background:#fff;border-radius:7px;width:fit-content;margin-left:auto;margin-right:auto" id="tkt-qr"></div>
      ${[['Vehicle',v.type==='car'?'🚗 Car':'🏍️ Bike'],['Plate',v.plateDisp],['Spot',v.spotId],['Entry',fmt12(v.entryTime)],['Rate',pr.icon+' '+pr.label],['Discount',v.discount?v.discount+'%':'None']].map(([l,val])=>`<div style="display:flex;justify-content:space-between;padding:5px 0;border-bottom:1px solid rgba(255,255,255,.05);font-size:12px"><span style="color:var(--text-muted)">${l}</span><span style="color:var(--text);font-weight:500;font-family:${l==='Plate'||l==='Spot'?'DM Mono,monospace':'inherit'}">${val}</span></div>`).join('')}
    </div>
    <div style="margin-top:9px"><button class="btn btn-secondary btn-full btn-sm" onclick="window.print()">🖨️ Print</button></div>`;
  setTimeout(()=>genQR('tkt-qr',v.ticketId,90),100);
}

// ══════════════════════════════════════════════
// CALC CHARGE
// ══════════════════════════════════════════════
function calcCharge(v,exitTime){
  const ms=(exitTime?new Date(exitTime):new Date())-new Date(v.entryTime);
  const hrs=Math.max(1,Math.ceil(ms/3600000));
  const pr=v.rateAtEntry||PP[v.pricingPeriod||'normal'];
  const r=pr[v.type];
  let amt=hrs===1?r.first:r.first+(hrs-1)*r.extra;
  const disc=v.discount||0;
  const discAmt=disc?Math.round(amt*disc/100):0;
  amt=amt-discAmt;
  return{hrs,amt,r,disc,discAmt};
}
function fmtDuration(a,b){const ms=(b?new Date(b):new Date())-new Date(a);const h=Math.floor(ms/3600000),m=Math.floor((ms%3600000)/60000);return`${h}h ${String(m).padStart(2,'0')}m`;}
function isOverstay(v){return(new Date()-new Date(v.entryTime))>CFG.overstayHours*3600000;}

// ══════════════════════════════════════════════
// EXIT
// ══════════════════════════════════════════════
function doSearch(){
  const q=document.getElementById('exit-search').value.trim();if(!q)return;
  const v=getActive().find(x=>x.ticketId===q||x.plate===q||x.plate===q.replace(/\s/g,'')||x.plateDisp===q);
  if(v){foundVehicle=v;document.getElementById('found-section').style.display='block';document.getElementById('not-found').style.display='none';renderFoundInfo(v);updateFoundCharge();}
  else{foundVehicle=null;document.getElementById('found-section').style.display='none';document.getElementById('not-found').style.display='block';}
}
function renderFoundInfo(v){
  const pp=v.pricingPeriod||'normal';
  document.getElementById('found-info').innerHTML=`
    ${[['Ticket',v.ticketId,'DM Mono,monospace'],['Vehicle',v.type==='car'?'🚗 Car':'🏍️ Bike',''],['Plate',v.plateDisp,'DM Mono,monospace'],['Spot',v.spotId,''],['Entry',fmt12(v.entryTime),''],['Duration',fmtDuration(v.entryTime),'','live-dur'],['Rate',PP[pp].icon+' '+PP[pp].label,''],['Discount',v.discount?v.discount+'% OFF':'None',''],['Overstay',isOverstay(v)?'⚠️ YES — Over 8hrs':'No','']].map(([l,val,f,id])=>`<div class="info-row"><span class="info-lbl">${l}</span><span class="info-val"${id?` id="${id}`+'"':''} style="${f?`font-family:${f}`:''}">${val}</span></div>`).join('')}`;
}
function updateFoundCharge(){
  if(!foundVehicle)return;
  const{hrs,amt,r,disc,discAmt}=calcCharge(foundVehicle);
  document.getElementById('found-amount').textContent=`₹${amt}`;
  const dl=document.getElementById('found-discount-line');
  if(disc&&dl){dl.style.display='block';dl.textContent=`${disc}% discount applied — saved ₹${discAmt}`;}
  const el=document.getElementById('live-dur');if(el)el.textContent=fmtDuration(foundVehicle.entryTime);
  let bd=`First hr: ₹${r.first}`;if(hrs>1)bd+=` + ${hrs-1} extra × ₹${r.extra} = ₹${(hrs-1)*r.extra}`;
  if(disc)bd+=` → ${disc}% off = ₹${amt}`;
  document.getElementById('found-breakdown').innerHTML=`<div class="info-row"><span class="info-lbl">Billable Hours</span><span class="info-val">${hrs} hr${hrs>1?'s':''}</span></div><div class="info-row"><span class="info-lbl">Breakdown</span><span class="info-val" style="font-size:10px">${bd}</span></div>`;
}
function doCheckout(isLost=false){
  if(!foundVehicle)return;
  const now=new Date();
  const{hrs,amt,r,disc,discAmt}=calcCharge(foundVehicle,now.toISOString());
  const lostPenalty=isLost?50:0;
  const finalAmt=amt+lostPenalty;
  const txn={...foundVehicle,exitTime:now.toISOString(),exitDate:today(),exitShift:SM[getShift()].label,duration:fmtDuration(foundVehicle.entryTime,now.toISOString()),hrs,amt:finalAmt,discountPct:disc,discountAmt:discAmt,lostTicket:isLost};
  saveActive(getActive().filter(v=>v.ticketId!==foundVehicle.ticketId));
  const txns=getTxns();txns.push(txn);saveTxns(txns);
  showBill(txn);playExit();showCounterFly('exit');
  addLog('exit',`Vehicle <strong>${txn.plateDisp}</strong> checked out — ₹${finalAmt}${isLost?' (Lost Ticket +₹50)':''}`);
  notify(`✅ ${txn.plateDisp} out — ₹${finalAmt}`,'success');
  foundVehicle=null;document.getElementById('found-section').style.display='none';document.getElementById('exit-search').value='';
  renderActiveTable();renderCheckoutTable();updateDashboard();updateActiveBadge();checkAlerts();updateTicker();
  flashSpot(txn.spotId,'freed');
}
function previewBill(){
  if(!foundVehicle)return;
  const now=new Date();const{hrs,amt,disc,discAmt}=calcCharge(foundVehicle,now.toISOString());
  showBill({...foundVehicle,exitTime:now.toISOString(),duration:fmtDuration(foundVehicle.entryTime,now.toISOString()),hrs,amt,discountPct:disc,discountAmt:discAmt});
}
function quickCheckout(tid){document.getElementById('exit-search').value=tid;showPage('exit');setTimeout(doSearch,60);}

// LOST TICKET
function handleLostTicket(){
  const plate=document.getElementById('lost-plate').value.trim().toUpperCase();
  if(!plate){notify('Enter license plate','error');return;}
  const v=getActive().find(x=>x.plate===plate||x.plate===plate.replace(/\s/g,'')||x.plateDisp===plate);
  if(!v){notify('Vehicle not found in active list','error');return;}
  foundVehicle={...v,discount:0};
  document.getElementById('found-section').style.display='block';
  document.getElementById('not-found').style.display='none';
  renderFoundInfo(foundVehicle);updateFoundCharge();
  notify(`⚠️ Lost ticket — ₹50 penalty will be added`,'warn');
  addLog('lost',`Lost ticket reported for <strong>${plate}</strong>`);
  // Override checkout to add penalty
  document.querySelector('#page-exit .btn-success').setAttribute('onclick','doCheckout(true)');
}

// ══════════════════════════════════════════════
// BILLING
// ══════════════════════════════════════════════
function showBill(txn){
  document.getElementById('bill-html').innerHTML=buildBillHTML(txn);
  document.getElementById('bill-modal').classList.add('open');
  setTimeout(()=>genQR('bill-qr',txn.ticketId,80),150);
  // WhatsApp link
  const pr=PP[txn.pricingPeriod||'normal'];
  const waText=encodeURIComponent(`🅿️ V MALL PARKING RECEIPT\nTicket: ${txn.ticketId}\nVehicle: ${txn.type.toUpperCase()} | ${txn.plateDisp}\nSpot: ${txn.spotId}\nEntry: ${fmt12(txn.entryTime)}\nExit: ${fmt12(txn.exitTime)}\nDuration: ${txn.duration}\nRate: ${pr.label}\n${txn.discountPct?`Discount: ${txn.discountPct}% (-₹${txn.discountAmt})\n`:''}Amount: ₹${txn.amt}${txn.lostTicket?' (+₹50 lost ticket penalty)':''}\n\nThank you for visiting V Mall! 🙏`);
  const waLink=document.getElementById('wa-link');
  if(waLink)waLink.href=`https://wa.me/?text=${waText}`;
}
function buildBillHTML(txn){
  const pp=txn.pricingPeriod||'normal';const pr=PP[pp];
  const r=txn.rateAtEntry?txn.rateAtEntry[txn.type]:pr[txn.type];
  const extra=txn.hrs>1?(txn.hrs-1)*r.extra:0;
  const base=txn.hrs===1?r.first:r.first+extra;
  return `<div class="billing-card">
    <div class="bill-header">
      <div class="bill-sub">Parking Receipt</div><div class="bill-mall">V MALL</div>
      <div class="bill-addr">6 Acres · 3 Floors · Andhra Pradesh</div>
      <div class="bill-period ${pp}">${pr.icon} ${pr.label}</div>
      <div class="bill-ticket-no">${txn.ticketId}</div>
      <div class="bill-qr-wrap"><div id="bill-qr"></div></div>
    </div>
    <div class="bill-row"><span class="bill-lbl">Vehicle</span><span class="bill-val">${txn.type==='car'?'🚗 Car':'🏍️ Bike'}</span></div>
    <div class="bill-row"><span class="bill-lbl">Plate</span><span class="bill-val" style="font-family:'DM Mono',monospace">${txn.plateDisp}</span></div>
    <div class="bill-row"><span class="bill-lbl">Spot</span><span class="bill-val">${txn.spotId}</span></div>
    <div class="bill-row"><span class="bill-lbl">Entry</span><span class="bill-val">${fmt12(txn.entryTime)}, ${fmtDate(txn.entryTime)}</span></div>
    <div class="bill-row"><span class="bill-lbl">Exit</span><span class="bill-val">${fmt12(txn.exitTime)}, ${fmtDate(txn.exitTime)}</span></div>
    <div class="bill-row"><span class="bill-lbl">Duration</span><span class="bill-val">${txn.duration}</span></div>
    <div class="bill-row"><span class="bill-lbl">Shift</span><span class="bill-val">${txn.shift||'—'}</span></div>
    <hr class="bill-divider">
    <div class="bill-row"><span class="bill-lbl">First Hour</span><span class="bill-val">₹${r.first}</span></div>
    ${txn.hrs>1?`<div class="bill-row"><span class="bill-lbl">${txn.hrs-1} Extra Hr${txn.hrs>2?'s':''} × ₹${r.extra}</span><span class="bill-val">₹${extra}</span></div>`:''}
    ${txn.lostTicket?`<div class="bill-row"><span class="bill-lbl">Lost Ticket Penalty</span><span class="bill-val" style="color:#dc2626">₹50</span></div>`:''}
    <div class="bill-row"><span class="bill-lbl">Subtotal</span><span class="bill-val">₹${base+(txn.lostTicket?50:0)}</span></div>
    ${txn.discountPct?`<div class="bill-discount-row"><span>🎫 ${txn.discountPct}% Discount Applied</span><span style="color:#15803d;font-weight:700">-₹${txn.discountAmt}</span></div>`:''}
    <div class="bill-total"><span class="bill-total-lbl">TOTAL</span><span class="bill-total-amt">₹${txn.amt}</span></div>
    <div class="bill-footer">Thank you for visiting V Mall!<br>Drive safe &nbsp;•&nbsp; Have a great day!<br><span style="font-family:'DM Mono',monospace;font-size:9px">${txn.ticketId} · ${fmt12(txn.exitTime)}</span></div>
  </div>`;
}
function closeBill(){document.getElementById('bill-modal').classList.remove('open');}

// ══════════════════════════════════════════════
// SHIFT HANDOVER REPORT
// ══════════════════════════════════════════════
function showHandover(){
  const sh=getShift();const meta=SM[sh];
  const td=today();const txns=getTxns().filter(t=>t.exitDate===td);
  const shiftTxns=txns.filter(t=>t.shift&&t.shift.includes(meta.label.split(' ')[0]));
  const rev=shiftTxns.reduce((s,t)=>s+t.amt,0);
  const active=getActive();
  const cars=active.filter(v=>v.type==='car').length;
  const bikes=active.filter(v=>v.type==='bike').length;
  const now=new Date();
  document.getElementById('handover-html').innerHTML=`
    <div class="handover-card">
      <div class="handover-title">${meta.icon} V MALL — SHIFT HANDOVER</div>
      ${[['Shift',meta.label],['Date',fmtDate(now.toISOString())],['Time',fmt12(now.toISOString())],['Prepared By','Parking Staff']].map(([l,v])=>`<div class="handover-row"><span style="color:#6b7280">${l}</span><span style="font-weight:600">${v}</span></div>`).join('')}
      <hr style="border:none;border-top:1px dashed #e5e7eb;margin:12px 0">
      <div style="font-weight:700;font-size:12px;margin-bottom:6px;color:#374151">This Shift Summary</div>
      ${[['Transactions',shiftTxns.length],['Revenue Collected','₹'+rev.toLocaleString('en-IN')],['Cars Serviced',shiftTxns.filter(t=>t.type==='car').length],['Bikes Serviced',shiftTxns.filter(t=>t.type==='bike').length]].map(([l,v])=>`<div class="handover-row"><span style="color:#6b7280">${l}</span><span style="font-weight:600">${v}</span></div>`).join('')}
      <hr style="border:none;border-top:1px dashed #e5e7eb;margin:12px 0">
      <div style="font-weight:700;font-size:12px;margin-bottom:6px;color:#374151">Vehicles Handed Over (${active.length})</div>
      <div class="handover-vehicles">
        ${active.length===0?'<p style="color:#9ca3af;font-size:12px">No vehicles currently parked</p>':active.map(v=>`<div class="handover-v-item"><span>${v.type==='car'?'🚗':'🏍️'} ${v.plateDisp} — ${v.spotId}</span><span>Since ${fmt12(v.entryTime)}</span></div>`).join('')}
      </div>
      <div class="handover-total"><span class="handover-total-lbl">CURRENT PARKED</span><span class="handover-total-val">${cars}🚗 &nbsp;${bikes}🏍️</span></div>
      <div style="text-align:center;margin-top:14px;font-size:10px;color:#9ca3af">Handover completed at ${fmt12(now.toISOString())} · V Mall Parking System</div>
    </div>`;
  document.getElementById('handover-modal').classList.add('open');
  addLog('handover',`Shift handover report generated — ${meta.label}`);
}
function closeHandover(){document.getElementById('handover-modal').classList.remove('open');}

// ══════════════════════════════════════════════
// VEHICLE HISTORY LOOKUP
// ══════════════════════════════════════════════
function lookupHistory(){
  const plate=document.getElementById('history-plate').value.trim().replace(/\s/g,'');
  if(!plate){notify('Enter a license plate','error');return;}
  const all=getTxns().filter(t=>t.plate===plate||t.plateDisp.replace(/\s/g,'')===plate)
    .sort((a,b)=>new Date(b.entryTime)-new Date(a.entryTime));
  const activeNow=getActive().find(v=>v.plate===plate||v.plateDisp.replace(/\s/g,'')===plate);
  const res=document.getElementById('history-result');
  const emp=document.getElementById('history-empty');
  const hap=document.getElementById('history-currently-parked');
  if(all.length===0&&!activeNow){res.style.display='none';emp.style.display='block';hap.style.display='none';return;}
  emp.style.display='none';res.style.display='block';
  // Stats
  const totalSpent=all.reduce((s,t)=>s+t.amt,0);
  const avgDur=all.length?Math.round(all.reduce((s,t)=>s+(new Date(t.exitTime)-new Date(t.entryTime)),0)/all.length/60000):0;
  const fav=all.length?all.reduce((acc,t)=>{acc[t.type]=(acc[t.type]||0)+1;return acc;},{}):{};
  const favType=Object.keys(fav).length?Object.keys(fav).sort((a,b)=>fav[b]-fav[a])[0]:'—';
  document.getElementById('history-header').innerHTML=`
    <div class="history-plate">${all.length?all[0].plateDisp:plate}</div>
    <div class="history-stats">
      <div class="history-stat"><div class="history-stat-val">${all.length}</div><div class="history-stat-lbl">Total Visits</div></div>
      <div class="history-stat"><div class="history-stat-val">₹${totalSpent.toLocaleString('en-IN')}</div><div class="history-stat-lbl">Total Spent</div></div>
      <div class="history-stat"><div class="history-stat-val">${avgDur}m</div><div class="history-stat-lbl">Avg Duration</div></div>
      <div class="history-stat"><div class="history-stat-val">${favType==='car'?'🚗':'favType'==='bike'?'🏍️':favType}</div><div class="history-stat-lbl">Preferred</div></div>
    </div>`;
  if(activeNow){hap.style.display='block';setT('history-active-info',`Currently parked at ${activeNow.spotId} since ${fmt12(activeNow.entryTime)}`);}
  else hap.style.display='none';
  document.getElementById('history-tbody').innerHTML=all.length===0?`<tr><td colspan="9"><div class="empty-state"><div class="empty-icon">📋</div><p>No completed visits</p></div></td></tr>`:
    all.map(t=>`<tr>
      <td><span style="font-family:'DM Mono',monospace;color:var(--amber);font-size:11px">${t.ticketId}</span></td>
      <td style="font-size:11px">${t.exitDate}</td>
      <td><span class="badge badge-${t.type}">${t.type==='car'?'🚗':'🏍️'}</span></td>
      <td>F${t.floor}</td>
      <td style="font-size:11px">${fmt12(t.entryTime)}</td>
      <td style="font-size:11px">${fmt12(t.exitTime)}</td>
      <td>${t.duration}</td>
      <td><span class="badge badge-${t.pricingPeriod||'active'}">${PP[t.pricingPeriod||'normal'].icon}</span></td>
      <td><span style="color:var(--amber);font-weight:700;font-family:'Rajdhani',sans-serif;font-size:15px">₹${t.amt}</span></td>
    </tr>`).join('');
}

// ══════════════════════════════════════════════
// ACTIVITY LOG
// ══════════════════════════════════════════════
function renderLog(){
  const logs=getLogs();
  const el=document.getElementById('log-list'); if(!el)return;
  if(!logs.length){el.innerHTML='<div class="empty-state"><div class="empty-icon">📋</div><p>No activity yet</p></div>';return;}
  el.innerHTML=logs.map(l=>`<div class="log-item">
    <div class="log-dot ${l.type}"></div>
    <div class="log-time">${fmt12(l.time)}</div>
    <div class="log-text">${l.text}</div>
  </div>`).join('');
}
function filterLog(q){
  const el=document.getElementById('log-list'); if(!el)return;
  Array.from(el.children).forEach(r=>{r.style.display=!q||r.textContent.toLowerCase().includes(q.toLowerCase())?'':'none';});
}

// ══════════════════════════════════════════════
// OVERSTAY CHECK
// ══════════════════════════════════════════════
function checkOverstays(){
  const overstays=getActive().filter(isOverstay);
  if(overstays.length){
    const banner=document.getElementById('alert-banner');
    const items=document.getElementById('alert-items');
    banner.classList.add('show');
    let extra=overstays.map(v=>`<span class="ab-chip">⏰ ${v.plateDisp} overstay!</span>`).join('');
    if(items.innerHTML!==extra)items.innerHTML+=' '+extra;
  }
}

// ══════════════════════════════════════════════
// DASHBOARD
// ══════════════════════════════════════════════
function updateDashboard(){
  const active=getActive(),txns=getTxns(),td=today();
  const todayT=txns.filter(t=>t.exitDate===td);
  const todayR=todayT.reduce((s,t)=>s+t.amt,0);
  const totalR=txns.reduce((s,t)=>s+t.amt,0);
  const cars=active.filter(v=>v.type==='car').length;
  const bikes=active.filter(v=>v.type==='bike').length;
  // Yesterday compare
  const yd=new Date(Date.now()-86400000).toISOString().split('T')[0];
  const ydR=txns.filter(t=>t.exitDate===yd).reduce((s,t)=>s+t.amt,0);
  setT('s-today-rev',`₹${todayR.toLocaleString('en-IN')}`);
  setT('s-today-txn',`${todayT.length} transactions`);
  setT('s-cars',cars);setT('s-cars-pct',`${Math.round(cars/300*100)}% occ`);
  setT('s-bikes',bikes);setT('s-bikes-pct',`${Math.round(bikes/600*100)}% occ`);
  setT('s-car-free',availCount('car'));setT('s-bike-free',availCount('bike'));
  setT('s-total-rev',`₹${totalR.toLocaleString('en-IN')}`);setT('s-total-sub',`${txns.length} total`);
  // Compare badges
  const diff=todayR-ydR;
  const compEl=document.getElementById('cmp-rev');
  if(compEl){compEl.className='stat-compare '+(diff>0?'compare-up':diff<0?'compare-down':'compare-same');compEl.innerHTML=diff>0?`↑ ₹${diff} vs yday`:diff<0?`↓ ₹${Math.abs(diff)} vs yday`:'Same as yday';}
  updateTicker();
}
function buildShiftRev(){
  const td=today(),txns=getTxns().filter(t=>t.exitDate===td);
  const data=[{s:'morning',icon:'🌅',lbl:'Morning'},{s:'afternoon',icon:'🌇',lbl:'Afternoon'},{s:'night',icon:'🌙',lbl:'Night'}];
  document.getElementById('shift-rev').innerHTML=data.map(d=>{
    const st=txns.filter(t=>t.shift&&t.shift.toLowerCase().includes(d.s.split('noon')[0]));
    const rev=st.reduce((a,t)=>a+t.amt,0);
    return `<div class="shift-card"><div class="shift-card-icon">${d.icon}</div><div class="shift-card-lbl">${d.lbl}</div><div class="shift-card-val">₹${rev.toLocaleString('en-IN')}</div><div class="shift-card-sub">${st.length} txns</div></div>`;
  }).join('');
}
function renderFloorOcc(){
  const active=getActive();let html='';
  for(let f=1;f<=3;f++){
    const fc=active.filter(v=>v.type==='car'&&v.floor===f).length;
    const fb=active.filter(v=>v.type==='bike'&&v.floor===f).length;
    const cp=Math.round(fc/CFG.carsPerFloor*100),bp=Math.round(fb/CFG.bikesPerFloor*100);
    const cc=cp>80?'var(--red)':cp>50?'var(--amber)':'var(--blue)';
    const bc=bp>80?'var(--red)':bp>50?'var(--amber)':'var(--green)';
    html+=`<div style="margin-bottom:12px">
      <div style="font-family:'Rajdhani',sans-serif;font-size:11px;font-weight:700;letter-spacing:2px;color:var(--text-dim);margin-bottom:6px">FLOOR ${f}</div>
      <div class="occ-wrap"><div class="occ-header"><span>🚗</span><span>${fc}/${CFG.carsPerFloor} (${cp}%)</span></div><div class="occ-bg"><div class="occ-fill" style="width:${cp}%;background:${cc}"></div></div></div>
      <div class="occ-wrap"><div class="occ-header"><span>🏍️</span><span>${fb}/${CFG.bikesPerFloor} (${bp}%)</span></div><div class="occ-bg"><div class="occ-fill" style="width:${bp}%;background:${bc}"></div></div></div>
    </div>`;
  }
  const el=document.getElementById('floor-occ');if(el)el.innerHTML=html;
}
function renderRecentTable(){
  const active=getActive().map(v=>({...v,status:'active'}));
  const txns=getTxns().map(t=>({...t,status:'exited'}));
  const all=[...active,...txns].sort((a,b)=>new Date(b.entryTime)-new Date(a.entryTime)).slice(0,20);
  const tbody=document.getElementById('recent-tbody');if(!tbody)return;
  if(!all.length){tbody.innerHTML=`<tr><td colspan="8"><div class="empty-state"><div class="empty-icon">🅿️</div><p>No activity — click Demo Data</p></div></td></tr>`;return;}
  tbody.innerHTML=all.map(v=>`<tr>
    <td><span style="font-family:'DM Mono',monospace;color:var(--amber);font-size:11px">${v.ticketId}</span></td>
    <td><span class="badge badge-${v.type}">${v.type==='car'?'🚗':'🏍️'} ${v.type}</span></td>
    <td style="font-family:'DM Mono',monospace">${v.plateDisp}</td>
    <td>F${v.floor}</td>
    <td style="font-size:11px">${fmt12(v.entryTime)}</td>
    <td><span class="badge badge-${v.pricingPeriod||'active'}">${PP[v.pricingPeriod||'normal'].icon}</span></td>
    <td><span class="badge badge-${v.status==='active'?'active':'exited'}">${v.status==='active'?'● Active':'✓ Out'}</span></td>
    <td>${v.status==='exited'?`<span style="color:var(--amber);font-weight:700">₹${v.amt}</span>`:'—'}</td>
  </tr>`).join('');
}

// ══════════════════════════════════════════════
// CHARTS
// ══════════════════════════════════════════════
function cColors(){const il=document.body.classList.contains('light');return{grid:il?'rgba(0,0,0,.06)':'rgba(255,255,255,.05)',tick:il?'#94a3b8':'#64748b',legend:il?'#475569':'#94a3b8'};}
function dc(id){if(charts[id]){charts[id].destroy();delete charts[id];}}
function buildWeeklyChart(){
  dc('chart-weekly');const txns=getTxns();const days=[];
  for(let i=6;i>=0;i--){const d=new Date(Date.now()-i*86400000);const ds=d.toISOString().split('T')[0];const carR=txns.filter(t=>t.exitDate===ds&&t.type==='car').reduce((s,t)=>s+t.amt,0);const bikeR=txns.filter(t=>t.exitDate===ds&&t.type==='bike').reduce((s,t)=>s+t.amt,0);days.push({lbl:d.toLocaleDateString('en-IN',{weekday:'short',day:'numeric'}),carR,bikeR});}
  const{grid,tick}=cColors();const ctx=document.getElementById('chart-weekly');if(!ctx)return;
  charts['chart-weekly']=new Chart(ctx,{type:'bar',data:{labels:days.map(d=>d.lbl),datasets:[{label:'Cars',data:days.map(d=>d.carR),backgroundColor:'rgba(59,130,246,.65)',borderColor:'#3b82f6',borderWidth:1,borderRadius:5,borderSkipped:false},{label:'Bikes',data:days.map(d=>d.bikeR),backgroundColor:'rgba(16,185,129,.65)',borderColor:'#10b981',borderWidth:1,borderRadius:5,borderSkipped:false}]},options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{labels:{color:tick,font:{size:10}}}},scales:{y:{beginAtZero:true,grid:{color:grid},ticks:{color:tick,callback:v=>'₹'+v}},x:{grid:{display:false},ticks:{color:tick}}}}});
}
function buildTypeChart(){
  dc('chart-type');const txns=getTxns();
  const carR=txns.filter(t=>t.type==='car').reduce((s,t)=>s+t.amt,0);
  const bikeR=txns.filter(t=>t.type==='bike').reduce((s,t)=>s+t.amt,0);
  const{legend}=cColors();const ctx=document.getElementById('chart-type');if(!ctx)return;
  charts['chart-type']=new Chart(ctx,{type:'doughnut',data:{labels:['🚗 Cars','🏍️ Bikes'],datasets:[{data:[carR,bikeR],backgroundColor:['rgba(59,130,246,.75)','rgba(16,185,129,.75)'],borderColor:['#3b82f6','#10b981'],borderWidth:2}]},options:{responsive:true,maintainAspectRatio:false,cutout:'62%',plugins:{legend:{position:'bottom',labels:{color:legend,padding:12,font:{size:11}}},tooltip:{callbacks:{label:c=>`₹${c.raw.toLocaleString('en-IN')}` }}}}});
}
function buildHourlyChart(){
  dc('chart-hourly');const td=today();
  const all=[...getActive().filter(v=>v.entryDate===td),...getTxns().filter(t=>t.entryDate===td)];
  const hours=Array(24).fill(0);all.forEach(v=>{const h=new Date(v.entryTime).getHours();hours[h]++;});
  const labels=Array.from({length:24},(_,i)=>{const h=i%12||12;return`${h}${i<12?'AM':'PM'}`;});
  const{grid,tick}=cColors();const ctx=document.getElementById('chart-hourly');if(!ctx)return;
  charts['chart-hourly']=new Chart(ctx,{type:'line',data:{labels,datasets:[{label:'Entries',data:hours,borderColor:'#3b82f6',backgroundColor:'rgba(59,130,246,.1)',fill:true,tension:.4,pointBackgroundColor:'#3b82f6',pointRadius:3,pointHoverRadius:5}]},options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},scales:{y:{beginAtZero:true,grid:{color:grid},ticks:{color:tick,stepSize:1}},x:{grid:{display:false},ticks:{color:tick,maxRotation:45,maxTicksLimit:12}}}}});
}
function buildOccupancyChart(){
  dc('chart-occupancy');
  // Simulate hourly occupancy from entry/exit data
  const td=today();
  const all=[...getActive().filter(v=>v.entryDate===td),...getTxns().filter(t=>t.entryDate===td)];
  const occ=Array(24).fill(0);
  all.forEach(v=>{
    const entH=new Date(v.entryTime).getHours();
    const exH=v.exitTime?new Date(v.exitTime).getHours():new Date().getHours();
    for(let h=entH;h<=exH&&h<24;h++)occ[h]++;
  });
  const labels=Array.from({length:24},(_,i)=>{const h=i%12||12;return`${h}${i<12?'AM':'PM'}`;});
  const{grid,tick}=cColors();const ctx=document.getElementById('chart-occupancy');if(!ctx)return;
  charts['chart-occupancy']=new Chart(ctx,{type:'line',data:{labels,datasets:[{label:'Vehicles',data:occ,borderColor:'#f59e0b',backgroundColor:'rgba(245,158,11,.1)',fill:true,tension:.4,pointBackgroundColor:'#f59e0b',pointRadius:3}]},options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},scales:{y:{beginAtZero:true,grid:{color:grid},ticks:{color:tick,stepSize:1}},x:{grid:{display:false},ticks:{color:tick,maxRotation:45,maxTicksLimit:12}}}}});
}

// ══════════════════════════════════════════════
// HEAT CALENDAR
// ══════════════════════════════════════════════
function buildHeatCalendar(){
  const txns=getTxns();
  const revByDay={};
  txns.forEach(t=>{revByDay[t.exitDate]=(revByDay[t.exitDate]||0)+t.amt;});
  const maxRev=Math.max(...Object.values(revByDay),1);
  // 90 days
  const days=[];const now=new Date();
  for(let i=89;i>=0;i--){const d=new Date(now.getTime()-i*86400000);days.push({ds:d.toISOString().split('T')[0],day:d.getDay()});}
  // Group by weeks
  let html='<div style="display:flex;gap:12px;flex-wrap:wrap;">';
  // Day labels
  html+='<div>';
  html+='<div style="display:grid;grid-template-rows:repeat(7,14px);gap:3px;margin-top:18px;">';
  ['S','M','T','W','T','F','S'].forEach(d=>html+=`<div style="font-size:8px;color:var(--text-muted);height:14px;display:flex;align-items:center">${d}</div>`);
  html+='</div></div>';
  html+='<div style="display:flex;gap:3px;align-items:flex-start;flex-wrap:nowrap;overflow-x:auto;">';
  // Pad start
  const firstDay=days[0].day;
  let week=`<div style="display:flex;flex-direction:column;gap:3px;">`;
  for(let p=0;p<firstDay;p++)week+=`<div class="heat-day empty"></div>`;
  days.forEach((d,i)=>{
    const rev=revByDay[d.ds]||0;
    const pct=rev/maxRev;
    const lvl=pct===0?0:pct<.2?1:pct<.4?2:pct<.65?3:pct<.85?4:5;
    const dateLabel=new Date(d.ds).toLocaleDateString('en-IN',{day:'numeric',month:'short'});
    week+=`<div class="heat-day h${lvl}" title="${dateLabel}: ₹${rev.toLocaleString('en-IN')}" style="animation:heatIn .3s ease ${i*.005}s both"></div>`;
    if(d.day===6||(i===days.length-1)){html+=week+'</div>';week='<div style="display:flex;flex-direction:column;gap:3px;">';}
  });
  html+='</div></div>';
  html+='<div class="heat-legend"><span>Less</span>';
  [0,1,2,3,4,5].forEach(l=>html+=`<div class="heat-legend-dot h${l}"></div>`);
  html+='<span>More</span></div>';
  const el=document.getElementById('heat-calendar');if(el)el.innerHTML=html;
}

// ══════════════════════════════════════════════
// ENTRY TABLE
// ══════════════════════════════════════════════
function renderEntryTable(){
  const active=getActive().sort((a,b)=>new Date(b.entryTime)-new Date(a.entryTime));
  setT('entry-count',`${active.length} vehicles`);
  const tbody=document.getElementById('entry-tbody');if(!tbody)return;
  if(!active.length){tbody.innerHTML=`<tr><td colspan="9"><div class="empty-state"><div class="empty-icon">🚗</div><p>No vehicles parked</p></div></td></tr>`;return;}
  tbody.innerHTML=active.map(v=>{
    const over=isOverstay(v);
    return`<tr class="${over?'overstay-row':''}">
      <td><span style="font-family:'DM Mono',monospace;color:var(--amber);font-size:11px">${v.ticketId}</span></td>
      <td><span class="badge badge-${v.type}">${v.type==='car'?'🚗':'🏍️'} ${v.type}</span></td>
      <td style="font-family:'DM Mono',monospace">${v.plateDisp}</td>
      <td>F${v.floor}</td>
      <td style="font-family:'DM Mono',monospace;font-size:10px">${v.spotId}</td>
      <td style="font-size:11px">${fmt12(v.entryTime)}</td>
      <td><span class="badge badge-${v.pricingPeriod||'active'}">${PP[v.pricingPeriod||'normal'].icon}</span></td>
      <td>${fmtDuration(v.entryTime)}</td>
      <td>${over?'<span class="badge badge-overstay">⏰ OVERSTAY</span>':v.discount?`<span class="badge badge-discount">${v.discount}% OFF</span>`:'—'}</td>
    </tr>`;
  }).join('');
}

// ══════════════════════════════════════════════
// ACTIVE & CHECKOUT TABLES
// ══════════════════════════════════════════════
function renderActiveTable(){
  const active=getActive().sort((a,b)=>new Date(b.entryTime)-new Date(a.entryTime));
  setT('active-count',`${active.length} parked`);
  const tbody=document.getElementById('active-tbody');if(!tbody)return;
  if(!active.length){tbody.innerHTML=`<tr><td colspan="8"><div class="empty-state"><div class="empty-icon">🅿️</div><p>No vehicles</p></div></td></tr>`;return;}
  tbody.innerHTML=active.map(v=>{
    const{amt}=calcCharge(v); const over=isOverstay(v);
    return`<tr class="${over?'overstay-row':''}">
      <td><span style="font-family:'DM Mono',monospace;color:var(--amber);font-size:11px">${v.ticketId}</span></td>
      <td><span class="badge badge-${v.type}">${v.type==='car'?'🚗':'🏍️'}</span></td>
      <td style="font-family:'DM Mono',monospace;font-size:11px">${v.plateDisp}</td>
      <td>F${v.floor}</td>
      <td style="font-size:11px">${fmt12(v.entryTime)}</td>
      <td style="color:var(--amber);font-weight:600">₹${amt}</td>
      <td>${over?'<span class="badge badge-overstay">⏰</span>':v.discount?`<span class="badge badge-discount">${v.discount}%</span>`:'—'}</td>
      <td><button class="btn btn-danger btn-sm" onclick="quickCheckout('${v.ticketId}')">Checkout</button></td>
    </tr>`;
  }).join('');
}
function renderCheckoutTable(){
  const td=today();const txns=getTxns().filter(t=>t.exitDate===td).sort((a,b)=>new Date(b.exitTime)-new Date(a.exitTime));
  setT('checkout-count',`${txns.length} today`);
  const tbody=document.getElementById('checkout-tbody');if(!tbody)return;
  if(!txns.length){tbody.innerHTML=`<tr><td colspan="9"><div class="empty-state"><div class="empty-icon">🎫</div><p>No checkouts today</p></div></td></tr>`;return;}
  tbody.innerHTML=txns.map(t=>`<tr>
    <td><span style="font-family:'DM Mono',monospace;color:var(--amber);font-size:11px">${t.ticketId}</span></td>
    <td><span class="badge badge-${t.type}">${t.type==='car'?'🚗 Car':'🏍️ Bike'}</span></td>
    <td style="font-family:'DM Mono',monospace">${t.plateDisp}</td>
    <td>F${t.floor}</td>
    <td>${t.duration}</td>
    <td style="font-size:11px">${fmt12(t.exitTime)}</td>
    <td><span class="badge badge-${t.pricingPeriod||'active'}">${PP[t.pricingPeriod||'normal'].icon}</span></td>
    <td>${t.discountPct?`<span class="badge badge-discount">${t.discountPct}% (-₹${t.discountAmt})</span>`:'—'}</td>
    <td><span style="color:var(--amber);font-weight:700;font-family:'Rajdhani',sans-serif;font-size:15px">₹${t.amt}</span></td>
  </tr>`).join('');
}

// ══════════════════════════════════════════════
// FLOOR MAP
// ══════════════════════════════════════════════
function selFloor(f){mapFloor=f;[1,2,3].forEach(n=>{document.getElementById('ft'+n).className='floor-tab'+(n===f?' active':'');});renderMapGrid();renderMapStat();}
function selMapType(t){mapType=t;document.getElementById('mt-car').className='map-type-tab'+(t==='car'?' act-car':'');document.getElementById('mt-bike').className='map-type-tab'+(t==='bike'?' act-bike':'');renderMapGrid();renderMapStat();}
function renderMapGrid(){
  const count=mapType==='car'?CFG.carsPerFloor:CFG.bikesPerFloor;
  const pfx=mapType==='car'?'C':'B';
  const occ=occupiedSpots(mapType);const res=getRes();
  let html='';
  for(let i=1;i<=count;i++){
    const sid=`${pfx}-F${mapFloor}-${String(i).padStart(3,'0')}`;
    const isO=occ.includes(sid),isR=res.includes(sid);
    const cls=isR?'reserved':isO?'occupied':'available';
    html+=`<div class="spot ${cls}" data-sid="${sid}" title="${sid} — ${isR?'RESERVED (VIP)':isO?'OCCUPIED':'FREE'}"></div>`;
  }
  const el=document.getElementById('spots-grid');if(el)el.innerHTML=html;
}
function renderMapStat(){
  const count=mapType==='car'?CFG.carsPerFloor:CFG.bikesPerFloor;
  const occ=getActive().filter(v=>v.type===mapType&&v.floor===mapFloor).length;
  const res=getRes().filter(r=>r.startsWith(`${mapType==='car'?'C':'B'}-F${mapFloor}-`)).length;
  const el=document.getElementById('map-stat-box');if(!el)return;
  el.innerHTML=`<div class="info-row"><span class="info-lbl">Floor ${mapFloor} — ${mapType==='car'?'🚗 Cars':'🏍️ Bikes'}</span><span class="info-val">${occ} occupied · ${res} reserved · ${count-occ-res} free · ${Math.round(occ/count*100)}% full</span></div>`;
}

// ══════════════════════════════════════════════
// REPORTS
// ══════════════════════════════════════════════
function renderReports(){
  const all=getTxns(),td=today();
  const todayT=all.filter(t=>t.exitDate===td);
  setT('r-today-rev',`₹${todayT.reduce((s,t)=>s+t.amt,0).toLocaleString('en-IN')}`);
  setT('r-today-txn',`${todayT.length} txns`);
  setT('r-total-rev',`₹${all.reduce((s,t)=>s+t.amt,0).toLocaleString('en-IN')}`);
  setT('r-total-txn',`${all.length} total`);
  setT('r-cars',all.filter(t=>t.type==='car').length);
  setT('r-bikes',all.filter(t=>t.type==='bike').length);
  buildHourlyChart();buildOccupancyChart();buildHeatCalendar();
  const from=document.getElementById('r-from').value,to=document.getElementById('r-to').value;
  const filtered=all.filter(t=>(!from||t.exitDate>=from)&&(!to||t.exitDate<=to)).sort((a,b)=>new Date(b.exitTime)-new Date(a.exitTime));
  setT('r-count',`${filtered.length} records`);
  const tbody=document.getElementById('reports-tbody');if(!tbody)return;
  if(!filtered.length){tbody.innerHTML=`<tr><td colspan="11"><div class="empty-state"><div class="empty-icon">📊</div><p>No transactions</p></div></td></tr>`;return;}
  tbody.innerHTML=filtered.map(t=>`<tr>
    <td><span style="font-family:'DM Mono',monospace;color:var(--amber);font-size:11px">${t.ticketId}</span></td>
    <td style="font-size:11px">${t.exitDate}</td>
    <td><span class="badge badge-${t.type}">${t.type==='car'?'🚗':'🏍️'} ${t.type}</span></td>
    <td style="font-family:'DM Mono',monospace;font-size:11px">${t.plateDisp}</td>
    <td>F${t.floor}</td>
    <td style="font-size:11px">${fmt12(t.entryTime)}</td>
    <td style="font-size:11px">${fmt12(t.exitTime)}</td>
    <td>${t.duration}</td>
    <td><span class="badge badge-${t.pricingPeriod||'active'}">${PP[t.pricingPeriod||'normal'].icon}</span></td>
    <td>${t.discountPct?`<span class="badge badge-discount">${t.discountPct}%</span>`:'—'}</td>
    <td><span style="color:var(--amber);font-weight:700;font-family:'Rajdhani',sans-serif;font-size:15px">₹${t.amt}</span></td>
  </tr>`).join('');
}

// ══════════════════════════════════════════════
// CSV EXPORT
// ══════════════════════════════════════════════
function exportCSV(){
  const from=document.getElementById('r-from')?.value,to=document.getElementById('r-to')?.value;
  const txns=getTxns().filter(t=>(!from||t.exitDate>=from)&&(!to||t.exitDate<=to)).sort((a,b)=>new Date(b.exitTime)-new Date(a.exitTime));
  if(!txns.length){notify('No data to export','warn');return;}
  const h=['Ticket','Date','Type','Plate','Floor','Spot','Entry','Exit','Duration','Hrs','Rate','Shift','Discount %','Discount ₹','Amount'];
  const rows=txns.map(t=>[t.ticketId,t.exitDate,t.type,t.plateDisp,`F${t.floor}`,t.spotId,`"${fmt12(t.entryTime)}"`,`"${fmt12(t.exitTime)}"`,t.duration,t.hrs,(t.rateLabel||'Standard').replace(/[🔥🌇🌙🟡]/g,'').trim(),t.shift||'',t.discountPct||0,t.discountAmt||0,t.amt]);
  const csv=[h,...rows].map(r=>r.join(',')).join('\n');
  const blob=new Blob(['\uFEFF'+csv],{type:'text/csv;charset=utf-8;'});
  const url=URL.createObjectURL(blob);const a=document.createElement('a');
  a.href=url;a.download=`vmall_${from||'all'}_${to||'all'}.csv`;document.body.appendChild(a);a.click();document.body.removeChild(a);URL.revokeObjectURL(url);
  notify(`📥 Exported ${txns.length} records`,'success');
  addLog('export',`CSV exported — ${txns.length} records`);
}

// ══════════════════════════════════════════════
// ALERTS
// ══════════════════════════════════════════════
function checkAlerts(){
  const cf=availCount('car'),bf=availCount('bike');
  const cl=cf<CFG.carsPerFloor*3*CFG.lowThreshold,bl=bf<CFG.bikesPerFloor*3*CFG.lowThreshold;
  const banner=document.getElementById('alert-banner'),items=document.getElementById('alert-items');
  if(cl||bl){
    banner.classList.add('show');
    items.innerHTML=(cl?`<span class="ab-chip">🚗 Cars: ${cf} left!</span>`:'')+
      (bl?`<span class="ab-chip">🏍️ Bikes: ${bf} left!</span>`:'');
  } else banner.classList.remove('show');
  checkOverstays();
}

// ══════════════════════════════════════════════
// TABLE FILTER
// ══════════════════════════════════════════════
function filterTable(input,tbodyId){const q=input.value.toLowerCase().trim();const tbody=document.getElementById(tbodyId);if(!tbody)return;Array.from(tbody.rows).forEach(r=>{r.style.display=!q||r.textContent.toLowerCase().includes(q)?'':'none';});}
function updateActiveBadge(){const n=getActive().length;const b=document.getElementById('active-badge');if(b){b.textContent=n;b.style.display=n>0?'inline-block':'none';}}

// ══════════════════════════════════════════════
// DEMO DATA
// ══════════════════════════════════════════════
function generateDemoData(){
  const plates=[['AP09AB1234','AP 09 AB 1234','car'],['TS07CD5678','TS 07 CD 5678','car'],['KA01EF9012','KA 01 EF 9012','bike'],['MH12GH3456','MH 12 GH 3456','car'],['TN22IJ7890','TN 22 IJ 7890','bike'],['AP39KL2345','AP 39 KL 2345','car'],['TS11MN6789','TS 11 MN 6789','bike'],['AP16OP0123','AP 16 OP 0123','car'],['KA03QR4567','KA 03 QR 4567','bike'],['MH14ST8901','MH 14 ST 8901','car']];
  const txns=getTxns();
  for(let d=1;d<=10;d++){
    const base=new Date(Date.now()-d*86400000);const ds=base.toISOString().split('T')[0];const count=Math.floor(Math.random()*6)+4;
    for(let i=0;i<count;i++){
      const tp=plates[i%10];const entryHour=[7,9,11,13,16,18,20,22][i%8];
      const entry=new Date(base);entry.setHours(entryHour,Math.floor(Math.random()*59),0,0);
      const dur=(Math.floor(Math.random()*4)+1)*3600000+Math.random()*1800000;
      const exit=new Date(entry.getTime()+dur);
      const pp=getPP(entry);const pr=PP[pp];const r=pr[tp[2]];
      const hrs=Math.max(1,Math.ceil((exit-entry)/3600000));
      const baseAmt=hrs===1?r.first:r.first+(hrs-1)*r.extra;
      const disc=[0,0,0,10,0,20,0,0][i%8];const discAmt=disc?Math.round(baseAmt*disc/100):0;
      const amt=baseAmt-discAmt;
      const sh=SM[getShiftForH(entryHour)].label;
      txns.push({ticketId:nextTicket(),type:tp[2],plate:tp[0],plateDisp:tp[1],floor:(i%3)+1,spotId:`${tp[2]==='car'?'C':'B'}-F${(i%3)+1}-${String(i+10).padStart(3,'0')}`,entryTime:entry.toISOString(),entryDate:ds,exitTime:exit.toISOString(),exitDate:ds,duration:`${Math.floor((exit-entry)/3600000)}h ${String(Math.floor(((exit-entry)%3600000)/60000)).padStart(2,'0')}m`,shift:sh,exitShift:sh,pricingPeriod:pp,rateLabel:pr.label,rateAtEntry:{car:{...PP[pp].car},bike:{...PP[pp].bike}},discountPct:disc,discountAmt,amt,hrs});
    }
  }
  saveTxns(txns);
  const active=getActive();let added=0;
  for(let i=0;i<5;i++){const tp=plates[i];if(active.find(v=>v.plate===tp[0]))continue;const spot=findSpot(tp[2],'auto');if(!spot)continue;const entry=new Date(Date.now()-(i+1)*3600000-Math.random()*1800000);const pp=getPP(entry);const pr=PP[pp];active.push({ticketId:nextTicket(),type:tp[2],plate:tp[0],plateDisp:tp[1],floor:spot.floor,spotId:spot.spotId,entryTime:entry.toISOString(),entryDate:today(),shift:SM[getShift()].label,pricingPeriod:pp,rateLabel:pr.label,rateAtEntry:{car:{...pr.car},bike:{...pr.bike}},discount:0});added++;}
  saveActive(active);
  addLog('entry',`Demo data generated — ${added} active + 10-day history`);
  notify(`⚡ Demo data ready — ${added} active + 10 days`,'success');
  refreshAll();
}
function clearData(){localStorage.removeItem(K.active);localStorage.removeItem(K.txns);localStorage.removeItem(K.ctr);localStorage.removeItem(K.reservations);localStorage.removeItem(K.log);notify('All data cleared','info');refreshAll();}

// ══════════════════════════════════════════════
// HELPERS
// ══════════════════════════════════════════════
function fmt12(iso){return new Date(iso).toLocaleTimeString('en-IN',{hour:'2-digit',minute:'2-digit',hour12:true}).toUpperCase();}
function fmtDate(iso){return new Date(iso).toLocaleDateString('en-IN',{day:'2-digit',month:'short',year:'numeric'});}
function setT(id,val){const el=document.getElementById(id);if(el)el.textContent=val;}
function notify(msg,type='success'){
  const n=document.getElementById('notif');
  const icons={success:'✅',error:'❌',info:'ℹ️',warn:'⚠️'};
  n.className=`notification ${type}`;n.innerHTML=`${icons[type]||''} ${msg}`;
  n.classList.add('show');clearTimeout(n._t);n._t=setTimeout(()=>n.classList.remove('show'),4200);
}

// ══════════════════════════════════════════════
// STARTUP
// ══════════════════════════════════════════════
(function startup(){
  const sh=getShift();const meta=SM[sh];
  document.body.className=meta.cls;
  setT('login-sub',`${meta.label} · Parking Control v4`);
  const pp=getPP();updateLoginRate(pp);
  ptcls=new ParticleSys(document.getElementById('particle-canvas'));
  ptcls.start(sh);
  setInterval(()=>{
    const t=new Date().toLocaleTimeString('en-IN',{hour:'2-digit',minute:'2-digit',second:'2-digit',hour12:true}).toUpperCase();
    const el=document.getElementById('clock');if(el)el.textContent=t;
  },1000);
})();
</script>
</body>
</html>
