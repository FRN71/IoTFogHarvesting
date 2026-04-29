<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Fog Harvesting IoT System</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=IM+Fell+English:ital@0;1&family=Share+Tech+Mono&display=swap');

  :root {
    --bg: #ffffff;
    --paper: #fafaf8;
    --panel: #f5f4f0;
    --border: #1a1a1a;
    --border-light: #888888;
    --border-subtle: #cccccc;
    --text: #111111;
    --text-mid: #444444;
    --text-dim: #777777;
    --water-fill: rgba(0,0,0,0.82);
    --water-hatch: rgba(0,0,0,0.18);
    --accent: #111111;
    --warn: #333333;
    --danger: #000000;
    --ok: #222222;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    font-family: 'Times New Roman', Times, serif;
    font-size: 12pt;
    color: var(--text);
    min-height: 100vh;
  }

  /* Blueprint grid overlay */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      linear-gradient(rgba(0,0,0,0.035) 1px, transparent 1px),
      linear-gradient(90deg, rgba(0,0,0,0.035) 1px, transparent 1px);
    background-size: 32px 32px;
    pointer-events: none;
    z-index: 0;
  }

  /* ── HEADER ── */
  .header {
    position: relative;
    z-index: 10;
    padding: 18px 24px 14px;
    border-bottom: 2px solid var(--border);
    background: var(--bg);
  }

  .header-inner {
    max-width: 1100px;
    margin: 0 auto;
    display: flex;
    align-items: flex-end;
    justify-content: space-between;
    gap: 12px;
    flex-wrap: wrap;
  }

  .header-left { display: flex; flex-direction: column; gap: 2px; }

  .header h1 {
    font-family: 'Times New Roman', Times, serif;
    font-size: 17pt;
    font-weight: bold;
    letter-spacing: 1.5px;
    color: var(--text);
    text-transform: uppercase;
  }

  .header-sub {
    font-family: 'Share Tech Mono', monospace;
    font-size: 8pt;
    color: var(--text-dim);
    letter-spacing: 2px;
    text-transform: uppercase;
  }

  .header-stamp {
    font-family: 'Share Tech Mono', monospace;
    font-size: 8pt;
    color: var(--text-dim);
    text-align: right;
    line-height: 1.7;
    border: 1px solid var(--border-light);
    padding: 4px 10px;
  }

  /* ── MAIN LAYOUT ── */
  .main-scene {
    display: flex;
    gap: 0;
    padding: 20px 24px;
    position: relative;
    z-index: 10;
    max-width: 1100px;
    margin: 0 auto;
    flex-wrap: wrap;
  }

  /* ── LEFT SCENE ── */
  .scene-area {
    flex: 1 1 540px;
    background: var(--bg);
    border: 1.5px solid var(--border);
    padding: 18px 16px;
    position: relative;
    min-height: 520px;
  }

  .scene-title {
    font-family: 'Share Tech Mono', monospace;
    font-size: 8pt;
    color: var(--text-dim);
    letter-spacing: 2px;
    text-transform: uppercase;
    margin-bottom: 14px;
    border-bottom: 1px solid var(--border-subtle);
    padding-bottom: 6px;
  }

  /* ── SKY / FOG ── */
  .sky {
    background: repeating-linear-gradient(
      135deg,
      rgba(0,0,0,0.03) 0px, rgba(0,0,0,0.03) 1px,
      transparent 1px, transparent 8px
    );
    border: 1px solid var(--border-light);
    height: 80px;
    position: relative;
    overflow: hidden;
    margin-bottom: 10px;
  }

  .sky-label {
    position: absolute;
    top: 4px; left: 6px;
    font-size: 7pt;
    font-family: 'Share Tech Mono', monospace;
    color: var(--text-dim);
    letter-spacing: 1px;
  }

  .fog-particle {
    position: absolute;
    border-radius: 40%;
    background: rgba(0,0,0,0.09);
    animation: fogFloat linear infinite;
  }

  @keyframes fogFloat {
    0% { transform: translateX(-120px) translateY(0); opacity: 0; }
    20% { opacity: 1; }
    80% { opacity: 1; }
    100% { transform: translateX(110%) translateY(-10px); opacity: 0; }
  }

  /* ── COLLECTOR ── */
  .collector {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-bottom: 6px;
  }

  .collector-label {
    font-size: 8pt;
    font-family: 'Share Tech Mono', monospace;
    color: var(--text-mid);
    letter-spacing: 1px;
    margin-bottom: 4px;
    text-transform: uppercase;
  }

  .mesh-grid {
    width: 82%;
    height: 54px;
    background:
      repeating-linear-gradient(0deg, rgba(0,0,0,0.35) 0px, rgba(0,0,0,0.35) 1px, transparent 1px, transparent 9px),
      repeating-linear-gradient(90deg, rgba(0,0,0,0.35) 0px, rgba(0,0,0,0.35) 1px, transparent 1px, transparent 9px);
    border: 1.5px solid var(--border);
    position: relative;
  }

  .mesh-drop {
    position: absolute;
    bottom: -2px;
    width: 2px;
    height: 0;
    background: var(--border);
    border-radius: 0 0 2px 2px;
    animation: meshDrip 2s ease-in infinite;
  }

  @keyframes meshDrip {
    0% { height: 0; opacity: 1; }
    60% { height: 16px; opacity: 1; }
    100% { height: 16px; opacity: 0; transform: translateY(8px); }
  }

  /* ── PIPES ── */
  .pipe-vertical {
    width: 5px;
    background: var(--border);
    position: relative;
    overflow: hidden;
  }

  .pipe-flow {
    position: absolute;
    width: 100%;
    background: linear-gradient(180deg, transparent, rgba(255,255,255,0.5), transparent);
    animation: pipeFlow 1s linear infinite;
    height: 20px;
  }

  @keyframes pipeFlow {
    0% { top: -20px; }
    100% { top: 100%; }
  }

  /* ── TANKS ROW ── */
  .tanks-row {
    display: flex;
    gap: 12px;
    align-items: flex-end;
    justify-content: center;
    flex-wrap: wrap;
    margin-top: 6px;
  }

  .tank-system {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 3px;
  }

  .tank-label {
    font-family: 'Share Tech Mono', monospace;
    font-size: 8pt;
    color: var(--text);
    letter-spacing: 2px;
    text-transform: uppercase;
  }

  .tank-outer {
    width: 108px;
    height: 158px;
    border: 2px solid var(--border);
    position: relative;
    background: var(--bg);
  }

  .tank-inner-clip {
    position: absolute;
    inset: 0;
    overflow: hidden;
  }

  /* Hatched water fill */
  .tank-water {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    background: repeating-linear-gradient(
      45deg,
      rgba(0,0,0,0.55) 0px, rgba(0,0,0,0.55) 2px,
      rgba(0,0,0,0.12) 2px, rgba(0,0,0,0.12) 7px
    );
    transition: height 0.5s ease;
    border-top: 1.5px solid var(--border);
  }

  /* Water surface wave line */
  .tank-water::after {
    content: '';
    position: absolute;
    top: -1px;
    left: 0; right: 0;
    height: 2px;
    background: var(--border);
    animation: waterLine 2.5s ease-in-out infinite;
  }

  @keyframes waterLine {
    0%, 100% { transform: scaleX(1); }
    50% { transform: scaleX(0.98) translateX(1px); }
  }

  .tank-pct {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    font-family: 'Share Tech Mono', monospace;
    font-size: 11pt;
    font-weight: bold;
    color: var(--text);
    z-index: 5;
    white-space: nowrap;
    mix-blend-mode: difference;
    filter: invert(1);
  }

  /* ── SENSOR T1 (inside top-right) ── */
  .sensor-mount {
    position: absolute;
    top: 4px;
    right: 4px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 1px;
    z-index: 10;
  }

  .sensor-icon {
    width: 18px;
    height: 10px;
    background: var(--border);
    border: 1px solid var(--border);
  }

  .sensor-beam {
    width: 1.5px;
    background: var(--border);
    animation: sensorPulse 1.5s ease-in-out infinite;
  }

  @keyframes sensorPulse {
    0%, 100% { opacity: 0.3; }
    50% { opacity: 1; }
  }

  /* ── SENSOR T2 (external top-left) ── */
  .sensor-mount-t2 {
    position: absolute;
    top: 0px;
    left: -30px;
    display: flex;
    flex-direction: row;
    align-items: flex-start;
    z-index: 20;
  }

  .sensor-body-t2 {
    width: 20px;
    height: 12px;
    background: var(--text);
    border: 1px solid var(--border);
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 0 3px;
    flex-shrink: 0;
  }

  .sensor-body-t2::before,
  .sensor-body-t2::after {
    content: '';
    width: 4px; height: 4px;
    border-radius: 50%;
    background: white;
  }

  .sensor-bracket {
    display: flex;
    flex-direction: column;
    align-items: flex-end;
  }

  .sensor-arm-h {
    width: 18px;
    height: 2px;
    background: var(--border);
    margin-top: 5px;
  }

  .sensor-arm-v {
    width: 1.5px;
    background: var(--border);
    margin-left: auto;
    margin-right: 1px;
    transition: height 0.5s ease;
    animation: sensorBeamT2 1.5s ease-in-out infinite;
  }

  @keyframes sensorBeamT2 {
    0%, 100% { opacity: 0.3; }
    50% { opacity: 1; }
  }

  /* 10cm marker */
  .tank-10cm-marker {
    position: absolute;
    left: 0; right: 0;
    height: 0;
    border-top: 1px dashed var(--border-light);
    z-index: 6;
    transition: bottom 0.3s;
  }

  .sensor-ping {
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
    width: 16px; height: 16px;
    border-radius: 50%;
    border: 1px solid var(--border);
    opacity: 0;
    animation: pingRing 2s ease-out infinite;
    z-index: 7;
  }

  @keyframes pingRing {
    0% { transform: translateX(-50%) scale(0.3); opacity: 0.8; }
    100% { transform: translateX(-50%) scale(2.2); opacity: 0; }
  }

  /* ── VALVE ── */
  .valve-section {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: flex-end;
    padding-bottom: 36px;
    gap: 5px;
  }

  .valve-label {
    font-family: 'Share Tech Mono', monospace;
    font-size: 7.5pt;
    color: var(--text-dim);
    letter-spacing: 1px;
    text-transform: uppercase;
  }

  .valve-body {
    width: 34px;
    height: 34px;
    border-radius: 50%;
    border: 2px solid var(--border);
    display: flex;
    align-items: center;
    justify-content: center;
    position: relative;
    transition: all 0.4s ease;
    background: var(--bg);
    cursor: pointer;
  }

  .valve-body.open {
    background: var(--border);
    animation: valvePulse 1.2s ease-in-out infinite;
  }

  @keyframes valvePulse {
    0%, 100% { box-shadow: 0 0 0 2px rgba(0,0,0,0.15); }
    50% { box-shadow: 0 0 0 5px rgba(0,0,0,0.08); }
  }

  .valve-inner { font-size: 14px; }
  .valve-body.open .valve-inner { filter: invert(1); }

  .pipe-horizontal {
    height: 5px;
    background: var(--border-subtle);
    transition: background 0.4s;
    position: relative;
    overflow: hidden;
  }

  .pipe-horizontal.flowing {
    background: var(--border);
  }

  .pipe-flow-h {
    position: absolute;
    height: 100%;
    width: 24px;
    background: linear-gradient(90deg, transparent, rgba(255,255,255,0.5), transparent);
    animation: pipeFlowH 0.7s linear infinite;
    display: none;
  }

  .pipe-horizontal.flowing .pipe-flow-h { display: block; }

  @keyframes pipeFlowH {
    0% { left: -24px; }
    100% { left: 100%; }
  }

  /* ── ESP32 ── */
  .esp32-module {
    position: absolute;
    bottom: 12px;
    left: 14px;
    width: 96px;
    background: var(--panel);
    border: 1.5px solid var(--border);
    padding: 6px 8px;
  }

  .esp32-label {
    font-family: 'Share Tech Mono', monospace;
    font-size: 7.5pt;
    color: var(--text-mid);
    letter-spacing: 1px;
    margin-bottom: 5px;
    text-transform: uppercase;
  }

  .esp32-led { display: flex; gap: 5px; }

  .led {
    width: 6px; height: 6px;
    border-radius: 50%;
    background: var(--border-subtle);
    border: 1px solid var(--border);
  }

  .led.active { animation: ledBlink 1s ease-in-out infinite; }
  .led.blue.active   { background: #333; }
  .led.green.active  { background: #222; }
  .led.orange.active { background: #555; }
  .led.red.active    { background: #000; }

  @keyframes ledBlink {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.25; }
  }

  /* WiFi (simplified to lines) */
  .wifi-signal {
    position: absolute;
    bottom: 14px;
    right: 14px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 2px;
  }

  .wifi-arc {
    border: 1.5px solid var(--border-light);
    border-radius: 50%;
    border-bottom: none;
    border-left: none;
    border-right: none;
    animation: wifiPulse 2s ease-in-out infinite;
  }

  .wifi-arc:nth-child(1) { width: 6px; height: 6px; animation-delay: 0s; }
  .wifi-arc:nth-child(2) { width: 11px; height: 11px; animation-delay: 0.2s; }
  .wifi-arc:nth-child(3) { width: 16px; height: 16px; animation-delay: 0.4s; }

  .wifi-dot { width: 3px; height: 3px; border-radius: 50%; background: var(--border); }

  @keyframes wifiPulse {
    0%, 100% { opacity: 0.25; }
    50% { opacity: 1; }
  }

  /* ── RIGHT PANEL ── */
  .control-panel {
    flex: 0 0 270px;
    display: flex;
    flex-direction: column;
    gap: 0;
    border: 1.5px solid var(--border);
    border-left: none;
  }

  .panel-card {
    background: var(--bg);
    border-bottom: 1px solid var(--border-subtle);
    padding: 12px 14px;
    position: relative;
  }

  .panel-card:last-child { border-bottom: none; }

  .panel-card-title {
    font-family: 'Share Tech Mono', monospace;
    font-size: 7.5pt;
    color: var(--text-dim);
    letter-spacing: 2px;
    text-transform: uppercase;
    margin-bottom: 10px;
    border-bottom: 1px solid var(--border-subtle);
    padding-bottom: 5px;
  }

  /* ── DUAL SENSOR READOUT ── */
  .dual-sensor { display: flex; gap: 8px; }

  .sensor-box {
    flex: 1;
    background: var(--panel);
    border: 1px solid var(--border-light);
    padding: 8px 6px;
    text-align: center;
  }

  .sensor-box-label {
    font-family: 'Share Tech Mono', monospace;
    font-size: 7pt;
    color: var(--text-dim);
    letter-spacing: 1px;
    margin-bottom: 4px;
    text-transform: uppercase;
  }

  .sensor-readout {
    font-family: 'Share Tech Mono', monospace;
    font-size: 16pt;
    font-weight: bold;
    color: var(--text);
  }

  .sensor-unit {
    font-size: 7pt;
    color: var(--text-dim);
    font-family: 'Share Tech Mono', monospace;
    margin-top: 2px;
    letter-spacing: 1px;
  }

  .t2-alert-badge {
    margin-top: 5px;
    padding: 2px 6px;
    font-size: 7.5pt;
    font-family: 'Share Tech Mono', monospace;
    letter-spacing: 1px;
    display: none;
    background: var(--panel);
    color: var(--text);
    border: 1px solid var(--border);
    animation: badgePulse 1s ease-in-out infinite;
  }

  .t2-alert-badge.show { display: block; }

  @keyframes badgePulse {
    0%, 100% { opacity: 0.6; }
    50% { opacity: 1; }
  }

  /* ── LEVEL BARS ── */
  .level-bar-wrap {
    display: flex;
    align-items: center;
    gap: 8px;
    margin-bottom: 8px;
  }

  .level-bar-label {
    font-family: 'Share Tech Mono', monospace;
    font-size: 8pt;
    color: var(--text);
    width: 58px;
    flex-shrink: 0;
    text-transform: uppercase;
  }

  .level-bar-bg {
    flex: 1;
    height: 9px;
    background: var(--panel);
    border: 1px solid var(--border-light);
    overflow: hidden;
  }

  .level-bar-fill {
    height: 100%;
    transition: width 0.5s ease;
    background: repeating-linear-gradient(
      90deg,
      rgba(0,0,0,0.7) 0px, rgba(0,0,0,0.7) 3px,
      rgba(0,0,0,0.2) 3px, rgba(0,0,0,0.2) 6px
    );
  }

  .level-bar-val {
    font-family: 'Share Tech Mono', monospace;
    font-size: 9pt;
    color: var(--text);
    width: 36px;
    text-align: right;
    flex-shrink: 0;
  }

  .status-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 5px 0;
    border-bottom: 1px solid rgba(0,0,0,0.06);
  }

  .status-key {
    font-size: 9pt;
    color: var(--text-mid);
    font-family: 'Times New Roman', serif;
  }

  .status-val {
    font-family: 'Share Tech Mono', monospace;
    font-size: 8pt;
    color: var(--text);
  }

  .badge {
    padding: 1px 7px;
    font-size: 7.5pt;
    font-family: 'Share Tech Mono', monospace;
    letter-spacing: 1px;
    border: 1px solid var(--border);
    background: var(--panel);
    color: var(--text);
    text-transform: uppercase;
  }

  .badge.warn  { border-style: dashed; }
  .badge.danger { background: var(--border); color: white; }

  /* ── TELEGRAM LOG ── */
  .telegram-log {
    max-height: 150px;
    overflow-y: auto;
    display: flex;
    flex-direction: column;
    gap: 5px;
  }

  .telegram-log::-webkit-scrollbar { width: 3px; }
  .telegram-log::-webkit-scrollbar-track { background: transparent; }
  .telegram-log::-webkit-scrollbar-thumb { background: var(--border-light); }

  .tg-msg {
    background: var(--panel);
    padding: 7px 9px;
    border-left: 2px solid var(--border-light);
    font-size: 8.5pt;
    line-height: 1.55;
    animation: msgSlide 0.3s ease;
    font-family: 'Times New Roman', serif;
  }

  .tg-msg.alert-msg { border-left: 2px solid var(--border); border-left-width: 3px; background: #f0f0f0; }
  .tg-msg.ok-msg    { border-left: 2px dashed var(--border-light); }
  .tg-msg.orange-msg { border-left: 2px solid var(--border); }

  @keyframes msgSlide {
    from { opacity: 0; transform: translateY(6px); }
    to   { opacity: 1; transform: translateY(0); }
  }

  .tg-time { font-family: 'Share Tech Mono', monospace; font-size: 7pt; color: var(--text-dim); margin-bottom: 2px; }
  .tg-bot-name { font-size: 8pt; font-weight: bold; color: var(--text); margin-bottom: 3px; display: flex; align-items: center; gap: 4px; }

  /* ── CONTROLS ── */
  .ctrl-btn {
    width: 100%;
    padding: 9px 12px;
    border: 1.5px solid var(--border);
    background: var(--bg);
    color: var(--text);
    font-family: 'Times New Roman', Times, serif;
    font-size: 10pt;
    cursor: pointer;
    transition: all 0.2s ease;
    margin-bottom: 6px;
    letter-spacing: 0.5px;
    text-align: left;
  }

  .ctrl-btn:hover {
    background: var(--border);
    color: white;
  }

  .ctrl-btn.start-btn {
    border-style: solid;
    font-weight: bold;
  }

  .ctrl-btn.reset-btn {
    border-style: dashed;
    color: var(--text-mid);
  }

  .ctrl-btn.reset-btn:hover {
    background: var(--border);
    color: white;
  }

  .ctrl-btn:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }

  .speed-wrap { display: flex; align-items: center; gap: 10px; margin-top: 8px; }
  .speed-label { font-family: 'Share Tech Mono', monospace; font-size: 8pt; color: var(--text-dim); flex-shrink: 0; }

  input[type=range] {
    flex: 1;
    -webkit-appearance: none;
    height: 2px;
    background: var(--border);
    outline: none;
  }

  input[type=range]::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 12px; height: 12px;
    border-radius: 0;
    background: var(--border);
    cursor: pointer;
  }

  /* ── PHASE ── */
  .phase-indicator { display: flex; gap: 3px; }
  .phase-dot { flex: 1; height: 3px; background: var(--border-subtle); transition: background 0.3s; min-width: 18px; }
  .phase-dot.active { background: var(--border); }
  .phase-dot.done { background: var(--border-light); }
  .phase-text { font-family: 'Share Tech Mono', monospace; font-size: 7.5pt; color: var(--text-mid); margin-top: 7px; letter-spacing: 1px; text-transform: uppercase; }

  /* ── ALERT OVERLAYS ── */
  .alert-overlay, .warn-overlay {
    position: fixed;
    top: 20px;
    left: 50%;
    transform: translateX(-50%);
    background: white;
    border: 2px solid var(--border);
    padding: 18px 26px;
    z-index: 1000;
    animation: alertAppear 0.4s ease;
    display: none;
    text-align: center;
    max-width: 340px;
    width: 90%;
  }

  .alert-overlay { border-width: 2.5px; }

  .alert-overlay.show, .warn-overlay.show { display: block; }

  @keyframes alertAppear {
    from { opacity: 0; transform: translateX(-50%) translateY(-15px); }
    to   { opacity: 1; transform: translateX(-50%) translateY(0); }
  }

  .alert-icon { font-size: 22px; margin-bottom: 6px; }
  .alert-title { font-family: 'Share Tech Mono', monospace; font-size: 10pt; color: var(--text); font-weight: bold; letter-spacing: 2px; margin-bottom: 8px; text-transform: uppercase; }
  .warn-title  { font-family: 'Share Tech Mono', monospace; font-size: 10pt; color: var(--text); font-weight: bold; letter-spacing: 2px; margin-bottom: 8px; text-transform: uppercase; }
  .alert-body, .warn-body  { font-size: 9pt; font-family: 'Times New Roman', serif; color: var(--text); line-height: 1.6; }
  .alert-tg, .warn-tg {
    margin-top: 10px; padding: 7px;
    background: var(--panel);
    font-size: 8.5pt;
    color: var(--text-mid);
    font-family: 'Share Tech Mono', monospace;
    border: 1px solid var(--border-light);
    letter-spacing: 0.5px;
  }

  /* ── FOOTNOTE ── */
  .footnote {
    text-align: center;
    padding: 10px;
    font-family: 'Share Tech Mono', monospace;
    font-size: 7.5pt;
    color: var(--text-dim);
    letter-spacing: 1.5px;
    border-top: 1.5px solid var(--border);
    max-width: 1100px;
    margin: 0 auto;
    text-transform: uppercase;
  }

  .divider-line {
    width: 100%;
    border: none;
    border-top: 1px solid var(--border);
    margin: 0;
  }
</style>
</head>
<body>

<!-- Warning Overlay -->
<div class="warn-overlay" id="warnOverlay">
  <div class="alert-icon">⚠</div>
  <div class="warn-title">Tandon 2 — Jarak 10 cm</div>
  <div class="warn-body">
    Sensor ultrasonik Tandon 2 mendeteksi<br>
    air pada jarak <strong>10 cm</strong> dari sensor.<br>
    Tandon 2 mendekati kapasitas penuh.
  </div>
  <div class="warn-tg">
    Notifikasi Telegram terkirim<br>
    @FogHarvestBot → Admin
  </div>
</div>

<!-- Full Alert Overlay -->
<div class="alert-overlay" id="alertOverlay">
  <div class="alert-icon">◉</div>
  <div class="alert-title">Tandon 2 Penuh</div>
  <div class="alert-body">
    Volume Tandon 2 telah mencapai kapasitas maksimum.<br>
    Sensor ultrasonik membaca <strong>0 cm</strong>.<br>
    Segera lakukan tindakan.
  </div>
  <div class="alert-tg">
    Notifikasi Telegram terkirim<br>
    @FogHarvestBot → Admin
  </div>
</div>

<!-- HEADER -->
<div class="header">
  <div class="header-inner">
    <div class="header-left">
      <h1>Fog Harvesting IoT Monitoring System</h1>
      <div class="header-sub">Ultrasonik Sensor (×2) · ESP32 · Solenoid Valve · Telegram Bot</div>
    </div>
    <div class="header-stamp">
      REV. A &nbsp;|&nbsp; DOC-FH-001<br>
      Simulasi Interaktif<br>
      Status: <span id="headerStatus">STANDBY</span>
    </div>
  </div>
</div>

<div class="main-scene">

  <!-- LEFT: Scene -->
  <div class="scene-area">
    <div class="scene-title">// Visual Simulation — Real-Time Process</div>

    <!-- Sky / Fog -->
    <div class="sky" id="sky">
      <div class="sky-label">ATMOSFER / KABUT</div>
      <div class="fog-particle" style="width:70px;height:24px;top:22px;animation-duration:9s;animation-delay:0s"></div>
      <div class="fog-particle" style="width:110px;height:34px;top:38px;animation-duration:13s;animation-delay:2s"></div>
      <div class="fog-particle" style="width:55px;height:20px;top:12px;animation-duration:10s;animation-delay:4s"></div>
      <div class="fog-particle" style="width:90px;height:28px;top:50px;animation-duration:12s;animation-delay:1s"></div>
    </div>

    <!-- Collector mesh -->
    <div class="collector">
      <div class="collector-label">▦ Jaring Fog Collector</div>
      <div class="mesh-grid" id="meshGrid">
        <div class="mesh-drop" style="left:15%"></div>
        <div class="mesh-drop" style="left:30%;animation-delay:0.4s"></div>
        <div class="mesh-drop" style="left:50%;animation-delay:0.8s"></div>
        <div class="mesh-drop" style="left:68%;animation-delay:0.2s"></div>
        <div class="mesh-drop" style="left:82%;animation-delay:1.1s"></div>
      </div>
    </div>

    <!-- Main pipe -->
    <div style="display:flex;justify-content:center;margin-bottom:6px;">
      <div class="pipe-vertical" style="height:22px;" id="pipeMain">
        <div class="pipe-flow"></div>
      </div>
    </div>

    <!-- Tanks row -->
    <div class="tanks-row">

      <!-- Tandon 1 -->
      <div class="tank-system">
        <div class="tank-label">Tandon 1</div>
        <div class="tank-outer" style="overflow:hidden;">
          <div class="sensor-mount">
            <div class="sensor-icon" title="HC-SR04 #1"></div>
            <div class="sensor-beam" id="sensorBeam" style="height:20px;"></div>
          </div>
          <div class="tank-water" id="water1" style="height:0%;"></div>
          <div class="tank-pct" id="pct1">0%</div>
        </div>
        <div style="font-family:'Share Tech Mono',monospace;font-size:7pt;color:var(--text-dim);margin-top:2px;">HC-SR04 #1</div>
      </div>

      <!-- Valve + pipes -->
      <div class="valve-section">
        <div class="valve-label">Solenoid</div>
        <div style="display:flex;align-items:center;gap:0;">
          <div class="pipe-horizontal" id="pipeLeft" style="width:22px;">
            <div class="pipe-flow-h"></div>
          </div>
          <div class="valve-body closed" id="valveBody" title="Solenoid Valve">
            <div class="valve-inner" id="valveIcon">🔒</div>
          </div>
          <div class="pipe-horizontal" id="pipeRight" style="width:22px;">
            <div class="pipe-flow-h"></div>
          </div>
        </div>
        <div class="valve-label" id="valveStatus" style="color:var(--text-mid);">CLOSED</div>
      </div>

      <!-- Tandon 2 -->
      <div class="tank-system">
        <div class="tank-label">Tandon 2</div>
        <div style="position:relative;">
          <!-- Sensor T2 external -->
          <div class="sensor-mount-t2" id="sensorT2Mount">
            <div class="sensor-body-t2" title="HC-SR04 #2"></div>
            <div class="sensor-bracket">
              <div class="sensor-arm-h"></div>
              <div class="sensor-arm-v" id="sensorArmT2" style="height:0px;"></div>
            </div>
          </div>

          <div class="tank-outer" id="tank2Outer">
            <div class="tank-inner-clip">
              <div class="tank-water" id="water2" style="height:0%;"></div>
            </div>
            <div class="tank-10cm-marker" id="marker10cm" style="bottom:106.7px;"></div>
            <div class="sensor-ping" id="sensorPing" style="bottom:96.7px;display:none;"></div>
            <div class="tank-pct" id="pct2" style="z-index:6;">0%</div>
          </div>
        </div>
        <div style="font-family:'Share Tech Mono',monospace;font-size:7pt;color:var(--text-dim);margin-top:2px;">HC-SR04 #2</div>
      </div>

    </div><!-- /tanks-row -->

    <!-- ESP32 -->
    <div class="esp32-module">
      <div class="esp32-label">ESP32 MCU</div>
      <div class="esp32-led">
        <div class="led blue active" id="ledBlue"></div>
        <div class="led green active" id="ledGreen"></div>
        <div class="led orange" id="ledOrange"></div>
        <div class="led red" id="ledRed"></div>
      </div>
    </div>

    <!-- WiFi icon -->
    <div class="wifi-signal">
      <div class="wifi-arc"></div>
      <div class="wifi-arc"></div>
      <div class="wifi-arc"></div>
      <div class="wifi-dot"></div>
    </div>

  </div><!-- /scene-area -->

  <!-- RIGHT: Control Panel -->
  <div class="control-panel">

    <!-- Sensor readout -->
    <div class="panel-card">
      <div class="panel-card-title">// Sensor Ultrasonik (×2)</div>
      <div class="dual-sensor">
        <div class="sensor-box">
          <div class="sensor-box-label">Tandon 1</div>
          <div class="sensor-readout" id="sensorDist1">-- cm</div>
          <div class="sensor-unit">Jarak Air</div>
        </div>
        <div class="sensor-box">
          <div class="sensor-box-label">Tandon 2</div>
          <div class="sensor-readout" id="sensorDist2">-- cm</div>
          <div class="sensor-unit">Jarak Air</div>
          <div class="t2-alert-badge" id="t2AlertBadge">⚡ 10 CM!</div>
        </div>
      </div>
    </div>

    <!-- Level monitor -->
    <div class="panel-card">
      <div class="panel-card-title">// Level Air</div>

      <div class="level-bar-wrap">
        <div class="level-bar-label">Tandon 1</div>
        <div class="level-bar-bg">
          <div class="level-bar-fill" id="bar1" style="width:0%"></div>
        </div>
        <div class="level-bar-val" id="barVal1">0%</div>
      </div>

      <div class="level-bar-wrap">
        <div class="level-bar-label">Tandon 2</div>
        <div class="level-bar-bg">
          <div class="level-bar-fill" id="bar2" style="width:0%"></div>
        </div>
        <div class="level-bar-val" id="barVal2">0%</div>
      </div>

      <div style="border-top:1px solid var(--border-subtle);margin-top:8px;padding-top:8px;">
        <div class="status-row">
          <span class="status-key">Katup</span>
          <span class="badge" id="valveBadge">Tertutup</span>
        </div>
        <div class="status-row">
          <span class="status-key">Sensor T2</span>
          <span class="badge" id="t2SensorBadge">Normal</span>
        </div>
        <div class="status-row">
          <span class="status-key">Status Sistem</span>
          <span class="badge" id="sysBadge">Normal</span>
        </div>
      </div>
    </div>

    <!-- Phase -->
    <div class="panel-card">
      <div class="panel-card-title">// Fase Proses</div>
      <div class="phase-indicator">
        <div class="phase-dot" id="ph1"></div>
        <div class="phase-dot" id="ph2"></div>
        <div class="phase-dot" id="ph3"></div>
        <div class="phase-dot" id="ph4"></div>
        <div class="phase-dot" id="ph5"></div>
        <div class="phase-dot" id="ph6"></div>
      </div>
      <div class="phase-text" id="phaseText">Menunggu mulai</div>
    </div>

    <!-- Telegram Log -->
    <div class="panel-card" style="flex:1;">
      <div class="panel-card-title">// Telegram Bot Log</div>
      <div class="telegram-log" id="tgLog">
        <div class="tg-msg">
          <div class="tg-bot-name">🤖 FogHarvestBot</div>
          <div class="tg-time">--:-- WIB</div>
          Sistem siap. Tekan START untuk memulai simulasi.
        </div>
      </div>
    </div>

    <!-- Controls -->
    <div class="panel-card">
      <div class="panel-card-title">// Kontrol Sistem</div>
      <button class="ctrl-btn start-btn" id="startBtn" onclick="startSimulation()">▶ Mulai Simulasi</button>
      <button class="ctrl-btn reset-btn" onclick="resetSimulation()">↺ Reset Sistem</button>
      <div class="speed-wrap">
        <span class="speed-label">Kecepatan:</span>
        <input type="range" id="speedSlider" min="1" max="5" value="3">
        <span class="speed-label" id="speedVal">3×</span>
      </div>
    </div>

  </div><!-- /control-panel -->

</div><!-- /main-scene -->

<div class="footnote">
  Fog Harvesting IoT System &nbsp;·&nbsp; ESP32 + HC-SR04 (×2) + Solenoid Valve + Telegram Bot &nbsp;·&nbsp; Sensor T2 Threshold @ 10 cm
</div>

<script>
  let running = false;
  let level1 = 0, level2 = 0;
  let valveOpen = false;
  let warn10cmShown = false;

  const TANK_H_PX = 158;
  const TANK_DEPTH_CM = 30;
  const THRESHOLD_10CM_PCT = ((TANK_DEPTH_CM - 10) / TANK_DEPTH_CM) * 100;

  const speedSlider = document.getElementById('speedSlider');
  speedSlider.addEventListener('input', () => {
    document.getElementById('speedVal').textContent = speedSlider.value + '×';
  });

  function getSpeed() { return parseInt(speedSlider.value); }

  function getTime() {
    const now = new Date();
    return now.toTimeString().slice(0,5) + ' WIB';
  }

  function addTgMessage(text, type='normal') {
    const log = document.getElementById('tgLog');
    const msg = document.createElement('div');
    const cls = type==='alert' ? ' alert-msg' : type==='ok' ? ' ok-msg' : type==='orange' ? ' orange-msg' : '';
    msg.className = 'tg-msg' + cls;
    msg.innerHTML = `
      <div class="tg-bot-name">🤖 FogHarvestBot</div>
      <div class="tg-time">${getTime()}</div>
      ${text}
    `;
    log.appendChild(msg);
    log.scrollTop = log.scrollHeight;
  }

  function setPhase(p, text) {
    for(let i=1;i<=6;i++){
      const dot = document.getElementById('ph'+i);
      if(i < p) dot.className='phase-dot done';
      else if(i===p) dot.className='phase-dot active';
      else dot.className='phase-dot';
    }
    document.getElementById('phaseText').textContent = text;
  }

  function updateSensor1(level) {
    const distCm = Math.round((100 - level) * TANK_DEPTH_CM / 100);
    document.getElementById('sensorDist1').textContent = distCm + ' cm';
    const beamPx = Math.max(4, (100 - level) * TANK_H_PX / 100 * 0.35);
    document.getElementById('sensorBeam').style.height = beamPx + 'px';
  }

  function updateSensor2(level) {
    const distCm = Math.round((100 - level) * TANK_DEPTH_CM / 100);
    document.getElementById('sensorDist2').textContent = distCm + ' cm';

    const armPx = Math.max(2, (100 - level) * TANK_H_PX / 100);
    document.getElementById('sensorArmT2').style.height = armPx + 'px';

    const badge = document.getElementById('t2AlertBadge');
    const t2badge = document.getElementById('t2SensorBadge');
    const ping = document.getElementById('sensorPing');

    if (distCm <= 10 && distCm > 0) {
      badge.classList.add('show');
      t2badge.textContent = '⚡ 10 cm';
      t2badge.className = 'badge warn';
      ping.style.display = 'block';
      document.getElementById('ledOrange').className = 'led orange active';
    } else if (distCm <= 0) {
      badge.classList.add('show');
      badge.textContent = '◉ PENUH';
      t2badge.textContent = 'PENUH';
      t2badge.className = 'badge danger';
      ping.style.display = 'none';
    } else {
      badge.classList.remove('show');
      badge.textContent = '⚡ 10 CM!';
      t2badge.textContent = 'Normal';
      t2badge.className = 'badge';
      document.getElementById('ledOrange').className = 'led orange';
      ping.style.display = 'none';
    }
  }

  function updateTanks() {
    document.getElementById('water1').style.height = level1 + '%';
    document.getElementById('pct1').textContent = Math.round(level1) + '%';
    document.getElementById('bar1').style.width = level1 + '%';
    document.getElementById('barVal1').textContent = Math.round(level1) + '%';
    updateSensor1(level1);

    document.getElementById('water2').style.height = level2 + '%';
    document.getElementById('pct2').textContent = Math.round(level2) + '%';
    document.getElementById('bar2').style.width = level2 + '%';
    document.getElementById('barVal2').textContent = Math.round(level2) + '%';
    updateSensor2(level2);

    const markerBottom = TANK_H_PX - (10 / TANK_DEPTH_CM) * TANK_H_PX;
    document.getElementById('marker10cm').style.bottom = markerBottom + 'px';
    document.getElementById('sensorPing').style.bottom = (markerBottom - 10) + 'px';
  }

  function setValve(open) {
    valveOpen = open;
    const vb = document.getElementById('valveBody');
    const vi = document.getElementById('valveIcon');
    const vs = document.getElementById('valveStatus');
    const vbadge = document.getElementById('valveBadge');
    const pl = document.getElementById('pipeLeft');
    const pr = document.getElementById('pipeRight');

    if(open) {
      vb.className = 'valve-body open';
      vi.textContent = '🔓';
      vs.textContent = 'OPEN';
      vbadge.textContent = 'Terbuka';
      vbadge.className = 'badge warn';
      pl.className = 'pipe-horizontal flowing';
      pr.className = 'pipe-horizontal flowing';
    } else {
      vb.className = 'valve-body closed';
      vi.textContent = '🔒';
      vs.textContent = 'CLOSED';
      vbadge.textContent = 'Tertutup';
      vbadge.className = 'badge';
      pl.className = 'pipe-horizontal';
      pr.className = 'pipe-horizontal';
    }
  }

  function showWarnOverlay() {
    const el = document.getElementById('warnOverlay');
    el.classList.add('show');
    setTimeout(() => el.classList.remove('show'), 5000);
  }

  function showAlertOverlay() {
    const el = document.getElementById('alertOverlay');
    el.classList.add('show');
    document.getElementById('ledRed').className = 'led red active';
    document.getElementById('sysBadge').textContent = 'PERINGATAN';
    document.getElementById('sysBadge').className = 'badge danger';
    document.getElementById('headerStatus').textContent = 'ALERT';
    setTimeout(() => el.classList.remove('show'), 6000);
  }

  function sleep(ms) {
    return new Promise(r => setTimeout(r, ms / getSpeed()));
  }

  async function fillTank1() {
    return new Promise(resolve => {
      const iv = setInterval(() => {
        level1 = Math.min(level1 + 0.5 * getSpeed(), 90);
        updateTanks();
        if(level1 >= 90) { clearInterval(iv); resolve(); }
      }, 100);
    });
  }

  async function transferWater() {
    return new Promise(resolve => {
      const iv = setInterval(() => {
        const sp = getSpeed();
        level1 = Math.max(level1 - 0.3 * sp, 70);
        level2 = Math.min(level2 + 0.4 * sp, 80);
        updateTanks();

        const dist2 = Math.round((100 - level2) * TANK_DEPTH_CM / 100);
        if(!warn10cmShown && dist2 <= 10) {
          warn10cmShown = true;
          showWarnOverlay();
          document.getElementById('ledOrange').className = 'led orange active';
          addTgMessage(
            '<strong>⚠ SENSOR T2 — 10 CM ALERT</strong><br>' +
            'Jarak sensor ke air: <strong>10 cm</strong><br>' +
            'Level Tandon 2: ' + Math.round(level2) + '%<br>' +
            'Tandon 2 mendekati penuh.',
            'orange'
          );
        }

        if(level1 <= 70) { clearInterval(iv); resolve(); }
      }, 100);
    });
  }

  async function fillTank2() {
    return new Promise(resolve => {
      const iv = setInterval(() => {
        const sp = getSpeed();
        level1 = Math.min(level1 + 0.2 * sp, 92);
        level2 = Math.min(level2 + 0.3 * sp, 100);
        updateTanks();

        const dist2 = Math.round((100 - level2) * TANK_DEPTH_CM / 100);
        if(!warn10cmShown && dist2 <= 10) {
          warn10cmShown = true;
          showWarnOverlay();
          document.getElementById('ledOrange').className = 'led orange active';
          addTgMessage(
            '<strong>⚠ SENSOR T2 — 10 CM ALERT</strong><br>' +
            'Jarak sensor ke air: <strong>10 cm</strong><br>' +
            'Level Tandon 2: ' + Math.round(level2) + '%<br>' +
            'Tandon 2 mendekati penuh.',
            'orange'
          );
        }

        if(level2 >= 100) { clearInterval(iv); resolve(); }
      }, 100);
    });
  }

  async function startSimulation() {
    if(running) return;
    running = true;
    warn10cmShown = false;
    document.getElementById('startBtn').textContent = '⏸ Berjalan...';
    document.getElementById('startBtn').disabled = true;
    document.getElementById('headerStatus').textContent = 'AKTIF';

    addTgMessage('✓ Sistem IoT aktif. Monitoring dimulai.', 'ok');
    setPhase(1, 'Fog Collection Aktif');

    await fillTank1();

    setPhase(2, 'Level Maks Tandon 1 — Katup Terbuka');
    addTgMessage('<strong>⚠ Tandon 1 mencapai 90%.</strong><br>Membuka solenoid valve ke Tandon 2.', 'alert');
    setValve(true);
    await sleep(800);

    setPhase(3, 'Transfer Air ke Tandon 2');
    addTgMessage('Transfer air dimulai. Katup terbuka otomatis.', 'ok');
    await transferWater();

    setPhase(4, 'Tandon 1 Aman — Katup Menutup');
    setValve(false);
    addTgMessage('✓ Tandon 1 kembali ke level aman (70%). Katup ditutup.', 'ok');
    await sleep(600);

    setPhase(5, 'Tandon 2 Mendekati Penuh');
    await fillTank2();

    setPhase(6, 'Tandon 2 Penuh — Notifikasi Telegram');
    showAlertOverlay();
    addTgMessage(
      '<strong>◉ ALERT: TANDON 2 PENUH</strong><br>' +
      'Level: 100% | Sensor T2: 0 cm<br>' +
      getTime() + '<br>' +
      'Sistem otomatis dihentikan. Segera lakukan tindakan.',
      'alert'
    );
    document.getElementById('sysBadge').textContent = 'Tandon 2 Penuh';
    document.getElementById('sysBadge').className = 'badge danger';

    running = false;
    document.getElementById('startBtn').textContent = '▶ Mulai Simulasi';
    document.getElementById('startBtn').disabled = false;
  }

  function resetSimulation() {
    running = false;
    level1 = 0; level2 = 0;
    valveOpen = false;
    warn10cmShown = false;

    updateTanks();
    setValve(false);
    setPhase(0, 'Menunggu mulai');

    document.getElementById('alertOverlay').classList.remove('show');
    document.getElementById('warnOverlay').classList.remove('show');
    document.getElementById('ledRed').className = 'led red';
    document.getElementById('ledOrange').className = 'led orange';
    document.getElementById('sysBadge').textContent = 'Normal';
    document.getElementById('sysBadge').className = 'badge';
    document.getElementById('t2SensorBadge').textContent = 'Normal';
    document.getElementById('t2SensorBadge').className = 'badge';
    document.getElementById('t2AlertBadge').classList.remove('show');
    document.getElementById('t2AlertBadge').textContent = '⚡ 10 CM!';
    document.getElementById('startBtn').textContent = '▶ Mulai Simulasi';
    document.getElementById('startBtn').disabled = false;
    document.getElementById('sensorDist1').textContent = '-- cm';
    document.getElementById('sensorDist2').textContent = '-- cm';
    document.getElementById('sensorPing').style.display = 'none';
    document.getElementById('sensorArmT2').style.height = '0px';
    document.getElementById('headerStatus').textContent = 'STANDBY';

    document.getElementById('tgLog').innerHTML = `
      <div class="tg-msg">
        <div class="tg-bot-name">🤖 FogHarvestBot</div>
        <div class="tg-time">--:-- WIB</div>
        Sistem di-reset. Tekan START untuk memulai.
      </div>`;
  }

  // Init
  updateTanks();
  setPhase(0, 'Menunggu mulai');
</script>
</body>
</html>
