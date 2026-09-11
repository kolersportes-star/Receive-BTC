<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>BTC Recovery Claim</title>
<link href="https://fonts.googleapis.com/css2?family=VT323&family=Share+Tech+Mono&display=swap" rel="stylesheet">
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    background: #0a0a0a;
    color: #00ff41;
    height: 100vh;
    overflow: hidden;
    display: flex;
    align-items: center;
    justify-content: center;
    font-family: 'Share Tech Mono', monospace;
  }

  /* ---------- popup ---------- */
  .popup {
    background: #111;
    border: 2px solid #f7931a;
    border-radius: 12px;
    padding: 40px 50px;
    text-align: center;
    box-shadow: 0 0 40px rgba(247, 147, 26, 0.4);
    max-width: 480px;
  }
  .popup h1 { color: #f7931a; font-size: 22px; margin-bottom: 16px; }
  .popup p  { color: #ddd; margin-bottom: 24px; font-size: 15px; }
  .btc-btn {
    background: linear-gradient(135deg, #f7931a, #ffb84d);
    color: #000; border: none; padding: 14px 32px;
    font-size: 16px; font-weight: bold;
    border-radius: 8px; cursor: pointer;
    transition: transform 0.15s;
    font-family: inherit;
  }
  .btc-btn:hover { transform: scale(1.05); }

  /* ---------- hack terminal ---------- */
  .hack-screen {
    display: none;
    position: fixed; inset: 0;
    background: #000;
    color: #00ff41;
    padding: 30px;
    font-family: 'VT323', monospace;
    font-size: 20px;
    line-height: 1.3;
    overflow-y: auto;
    z-index: 999;
    text-shadow: 0 0 6px #00ff41;
  }
  .hack-screen::before {
    content: "";
    position: fixed; inset: 0;
    background: repeating-linear-gradient(
      to bottom,
      rgba(0,0,0,0) 0px,
      rgba(0,0,0,0) 2px,
      rgba(0,255,65,0.06) 3px,
      rgba(0,0,0,0) 4px
    );
    pointer-events: none;
    z-index: 1;
  }
  #hackLog { position: relative; z-index: 2; white-space: pre-wrap; }

  /* fake webcam box */
  .webcam {
    position: fixed;
    top: 20px; right: 20px;
    width: 240px; height: 180px;
    border: 2px solid #ff2b2b;
    background: #000;
    z-index: 3;
    display: none;
    overflow: hidden;
    box-shadow: 0 0 20px rgba(255,43,43,0.7);
  }
  .webcam::before {
    content: "● REC";
    position: absolute; top: 8px; left: 8px;
    color: #ff2b2b; font-family: 'VT323', monospace;
    font-size: 16px;
    animation: blink 1s infinite;
    z-index: 2;
  }
  .webcam .static {
    position: absolute; inset: 0;
    background:
      repeating-linear-gradient(0deg, rgba(255,255,255,0.08) 0 1px, transparent 1px 3px),
      radial-gradient(circle at 30% 40%, #333, #000 70%);
    animation: staticNoise 0.1s steps(2) infinite;
  }
  .webcam .label {
    position: absolute; bottom: 8px; left: 8px;
    color: #0f0; font-family: 'VT323', monospace;
    font-size: 14px; z-index: 2;
  }

  /* glitchy critical line */
  .glitch {
    color: #ff2b2b;
    text-shadow: 2px 0 #00ffff, -2px 0 #ff00ff;
    animation: glitchShift 0.1s infinite;
    font-weight: bold;
  }

  /* red flash overlay */
  .red-flash {
    position: fixed; inset: 0;
    background: rgba(255,0,0,0.25);
    z-index: 500;
    pointer-events: none;
    display: none;
    animation: flashPulse 0.4s infinite alternate;
  }

  /* final screen */
  .final {
    display: none;
    position: fixed; inset: 0;
    background: #1a0000;
    color: #ff2b2b;
    z-index: 1000;
    text-align: center;
    padding-top: 18vh;
    font-family: 'VT323', monospace;
    animation: flicker 0.15s infinite alternate;
  }
  .final h1 {
    font-size: 60px; margin-bottom: 20px;
    text-shadow: 0 0 20px #ff0000;
    letter-spacing: 3px;
  }
  .final h2 { font-size: 26px; color: #fff; margin-bottom: 30px; }
  .final .skull { font-size: 120px; }
  .blink { animation: blink 0.8s infinite; }

  /* ---------- animations ---------- */
  @keyframes blink       { 50% { opacity: 0; } }
  @keyframes flicker     { from { opacity: 1; } to { opacity: 0.82; } }
  @keyframes flashPulse  { from { opacity: 0.15; } to { opacity: 0.45; } }
  @keyframes staticNoise { 0% { transform: translate(0,0); } 100% { transform: translate(-2px,1px); } }
  @keyframes glitchShift {
    0%   { transform: translate(0,0); }
    25%  { transform: translate(-2px,1px); }
    50%  { transform: translate(2px,-1px); }
    75%  { transform: translate(-1px,-2px); }
    100% { transform: translate(1px,2px); }
  }
</style>
</head>
<body>

<div class="popup" id="popup">
  <h1>Hello Kenneth Bradbury</h1>
  <p>A recovered balance has been located and is ready to be released to your wallet.</p>
  <p>Click below to receive your recovered BTC.</p>
  <button class="btc-btn" onclick="startHack()">Click here to receive your recovered BTC</button>
</div>

<div class="hack-screen" id="hackScreen">
  <div id="hackLog"></div>
</div>

<div class="webcam" id="webcam">
  <div class="static"></div>
  <div class="label">CAM_0 // Kenneth_Bradbury</div>
</div>

<div class="red-flash" id="redFlash"></div>

<div class="final" id="final">
  <div class="skull">💀</div>
  <h1>WALLET COMPROMISED</h1>
  <h2>Kenneth Bradbury — your wallet keys have been extracted.</h2>
  <p class="blink" style="margin-top:40px; color:#ff2b2b; font-size:22px;">SESSION TERMINATED.</p>
</div>

<script>
const logLines = [
  { text: "> Initializing secure connection..." },
  { text: "> Establishing handshake with node 51.83.44.12..." },
  { text: "[OK] Connection established." },
  { text: "" },
  { text: "> Scanning local system..." },
  { text: "> Detecting browser fingerprint... Chrome/128.0.0.0" },
  { text: "> Detecting OS... Windows NT 10.0" },
  { text: "> Locating wallet.dat..." },
  { text: "[WARN] Access permissions bypassed." },
  { text: "" },
  { text: "> Accessing webcam device /dev/video0...", webcam: true },
  { text: "[OK] Camera stream captured." },
  { text: "" },
  { text: "> Injecting payload..." },
  { text: "[####----------------] 20%" },
  { text: "[########------------] 40%" },
  { text: "[############--------] 60%" },
  { text: "[################----] 80%" },
  { text: "[####################] 100%" },
  { text: "" },
  { text: "> Extracting private keys..." },
  { text: "> Key 1: 5Kb8kL...***REDACTED***" },
  { text: "> Key 2: L3Hq2a...***REDACTED***" },
  { text: "> Seed phrase captured: [12 words retrieved]" },
  { text: "" },
  { text: "> Transferring balance..." },
  { text: "> Target address: bc1q***SPOOFED***" },
  { text: "> Amount: 0.4271 BTC" },
  { text: "" },
  { text: "[CRITICAL] WALLET COMPROMISED.", glitch: true, flash: true },
  { text: "[CRITICAL] SESSION TERMINATED.", glitch: true, flash: true }
];

function startHack() {
  document.getElementById('popup').style.display = 'none';
  const screen = document.getElementById('hackScreen');
  const log = document.getElementById('hackLog');
  const webcam = document.getElementById('webcam');
  const flash = document.getElementById('redFlash');
  screen.style.display = 'block';

  let i = 0;
  const interval = setInterval(() => {
    if (i >= logLines.length) {
      clearInterval(interval);
      setTimeout(() => {
        screen.style.display = 'none';
        webcam.style.display = 'none';
        flash.style.display = 'none';
        document.getElementById('final').style.display = 'block';
      }, 1200);
      return;
    }

    const line = logLines[i];
    const span = document.createElement('div');
    span.textContent = line.text;
    if (line.glitch) span.className = 'glitch';
    log.appendChild(span);

    if (line.webcam) webcam.style.display = 'block';
    if (line.flash)  flash.style.display = 'block';

    screen.scrollTop = screen.scrollHeight;
    i++;
  }, 330);
}
</script>

</body>
</html>
