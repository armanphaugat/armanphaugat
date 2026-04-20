<!DOCTYPE html>
<html>
<head>
<link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&family=VT323&display=swap" rel="stylesheet">
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  
  body {
    background: #0a0a0a;
    color: #00ff41;
    font-family: 'VT323', monospace;
    font-size: 18px;
    line-height: 1.6;
    padding: 0;
    min-height: 100vh;
    position: relative;
    overflow-x: hidden;
  }

  body::before {
    content: '';
    position: fixed;
    top: 0; left: 0; right: 0; bottom: 0;
    background: repeating-linear-gradient(0deg, transparent, transparent 2px, rgba(0,0,0,0.08) 2px, rgba(0,0,0,0.08) 4px);
    pointer-events: none;
    z-index: 100;
  }

  .scanline {
    position: fixed;
    top: 0; left: 0; right: 0;
    height: 3px;
    background: rgba(0, 255, 65, 0.12);
    animation: scanline 8s linear infinite;
    pointer-events: none;
    z-index: 101;
  }

  @keyframes scanline {
    0% { top: -3px; }
    100% { top: 100vh; }
  }

  .container {
    max-width: 860px;
    margin: 0 auto;
    padding: 2rem 1.5rem 4rem;
  }

  .pixel-font { font-family: 'Press Start 2P', monospace; }

  .blink { animation: blink 1s step-end infinite; }
  @keyframes blink { 0%, 100% { opacity: 1; } 50% { opacity: 0; } }

  .flicker { animation: flicker 6s infinite; }
  @keyframes flicker {
    0%, 97%, 100% { opacity: 1; }
    98% { opacity: 0.85; }
    99% { opacity: 1; }
    99.5% { opacity: 0.6; }
  }

  /* Header */
  .header-box {
    border: 3px solid #00ff41;
    padding: 2rem 1.5rem;
    text-align: center;
    position: relative;
    margin-bottom: 2rem;
    box-shadow: 0 0 20px rgba(0, 255, 65, 0.3), inset 0 0 20px rgba(0, 255, 65, 0.05);
  }

  .corner-tl, .corner-tr, .corner-bl, .corner-br {
    position: absolute;
    font-family: 'Press Start 2P', monospace;
    font-size: 10px;
    color: #00cc33;
  }
  .corner-tl { top: 4px; left: 8px; }
  .corner-tr { top: 4px; right: 8px; }
  .corner-bl { bottom: 4px; left: 8px; }
  .corner-br { bottom: 4px; right: 8px; }

  .player-name {
    font-family: 'Press Start 2P', monospace;
    font-size: clamp(16px, 3.5vw, 26px);
    color: #ffff00;
    text-shadow: 3px 3px 0 #cc9900, 0 0 15px rgba(255,255,0,0.5);
    margin-bottom: 0.6rem;
    letter-spacing: 2px;
  }

  .player-title {
    font-family: 'Press Start 2P', monospace;
    font-size: clamp(7px, 1.5vw, 10px);
    color: #00ffff;
    text-shadow: 0 0 10px rgba(0,255,255,0.6);
    letter-spacing: 1px;
    margin-bottom: 1rem;
  }

  .status-line {
    display: flex;
    justify-content: center;
    gap: 1.5rem;
    flex-wrap: wrap;
    font-size: 16px;
    color: #00ff41;
  }

  .status-item { color: #aaffaa; }
  .status-val { color: #ffff00; font-weight: bold; }

  /* Section header */
  .section-header {
    font-family: 'Press Start 2P', monospace;
    font-size: clamp(9px, 1.8vw, 12px);
    color: #ff4444;
    margin: 2.5rem 0 1rem;
    padding: 0.5rem 0.8rem;
    border-left: 4px solid #ff4444;
    background: rgba(255, 68, 68, 0.05);
    text-shadow: 1px 1px 0 #880000;
    letter-spacing: 1px;
  }

  .section-header::before { content: '> '; color: #ffff00; }

  /* Stats grid */
  .stats-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
    gap: 10px;
    margin-bottom: 1.5rem;
  }

  .stat-card {
    border: 2px solid #00aa28;
    padding: 0.8rem 0.6rem;
    text-align: center;
    position: relative;
    background: rgba(0, 255, 65, 0.03);
    transition: all 0.15s;
  }

  .stat-card:hover {
    border-color: #00ff41;
    background: rgba(0, 255, 65, 0.08);
    box-shadow: 0 0 12px rgba(0, 255, 65, 0.25);
    transform: translateY(-2px);
  }

  .stat-icon {
    font-family: 'Press Start 2P', monospace;
    font-size: 8px;
    color: #888;
    margin-bottom: 4px;
    display: block;
    text-align: left;
  }

  .stat-val {
    font-family: 'Press Start 2P', monospace;
    font-size: clamp(14px, 2.5vw, 20px);
    color: #ffff00;
    display: block;
    text-shadow: 2px 2px 0 #996600;
    margin-bottom: 2px;
  }

  .stat-label {
    font-size: 13px;
    color: #88cc88;
    display: block;
  }

  /* HP Bar */
  .hp-bar-wrap { margin-bottom: 1.5rem; }

  .hp-row {
    display: flex;
    align-items: center;
    gap: 10px;
    margin-bottom: 8px;
  }

  .hp-label {
    font-family: 'Press Start 2P', monospace;
    font-size: 9px;
    color: #00ffff;
    min-width: 60px;
  }

  .hp-track {
    flex: 1;
    height: 16px;
    background: #111;
    border: 2px solid #333;
    position: relative;
    overflow: hidden;
  }

  .hp-fill {
    height: 100%;
    position: relative;
    animation: fillIn 1.5s ease-out forwards;
    transform-origin: left;
  }

  @keyframes fillIn {
    from { transform: scaleX(0); }
    to { transform: scaleX(1); }
  }

  .hp-fill::after {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0; bottom: 0;
    background: repeating-linear-gradient(90deg, transparent, transparent 6px, rgba(0,0,0,0.25) 6px, rgba(0,0,0,0.25) 8px);
  }

  .hp-num {
    font-family: 'Press Start 2P', monospace;
    font-size: 9px;
    color: #ffff00;
    min-width: 48px;
    text-align: right;
  }

  /* Tech grid */
  .tech-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(130px, 1fr));
    gap: 8px;
    margin-bottom: 1rem;
  }

  .tech-tag {
    border: 1px solid #005522;
    padding: 5px 8px;
    font-size: 15px;
    color: #00dd33;
    background: rgba(0, 85, 34, 0.2);
    text-align: center;
    transition: all 0.12s;
    cursor: default;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  .tech-tag:hover {
    border-color: #00ff41;
    color: #00ff41;
    background: rgba(0, 255, 65, 0.12);
    box-shadow: 0 0 8px rgba(0, 255, 65, 0.3);
  }

  .tech-tag.ai { border-color: #005577; color: #00ccff; background: rgba(0, 85, 119, 0.2); }
  .tech-tag.ai:hover { border-color: #00ffff; color: #00ffff; }
  .tech-tag.db { border-color: #553300; color: #ffaa00; background: rgba(85, 51, 0, 0.2); }
  .tech-tag.db:hover { border-color: #ffaa00; color: #ffdd00; }
  .tech-tag.devops { border-color: #440055; color: #dd88ff; background: rgba(68, 0, 85, 0.2); }
  .tech-tag.devops:hover { border-color: #cc88ff; color: #ffaaff; }

  /* Achievements */
  .achievement {
    border: 1px solid #333;
    padding: 0.7rem 1rem;
    margin-bottom: 8px;
    display: flex;
    align-items: flex-start;
    gap: 12px;
    background: rgba(255, 255, 0, 0.02);
    transition: all 0.12s;
    position: relative;
    overflow: hidden;
  }

  .achievement::before {
    content: '';
    position: absolute;
    left: 0; top: 0; bottom: 0;
    width: 3px;
    background: #ffff00;
  }

  .achievement:hover {
    border-color: #ffff00;
    background: rgba(255, 255, 0, 0.05);
    box-shadow: 0 0 10px rgba(255, 255, 0, 0.15);
  }

  .ach-badge {
    font-family: 'Press Start 2P', monospace;
    font-size: 8px;
    color: #ffff00;
    background: rgba(255, 255, 0, 0.1);
    border: 1px solid #665500;
    padding: 4px 6px;
    white-space: nowrap;
    flex-shrink: 0;
  }

  .ach-text { font-size: 16px; color: #ccffcc; line-height: 1.4; }
  .ach-text strong { color: #ffffff; font-size: 17px; }

  /* Certifications */
  .cert-list { list-style: none; }

  .cert-list li {
    padding: 0.5rem 0;
    border-bottom: 1px dashed #1a3a1a;
    font-size: 17px;
    color: #88cc88;
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .cert-list li:last-child { border-bottom: none; }

  .cert-list li::before {
    content: '[OK]';
    font-family: 'Press Start 2P', monospace;
    font-size: 7px;
    color: #00ff41;
    flex-shrink: 0;
  }

  .cert-year {
    margin-left: auto;
    font-family: 'Press Start 2P', monospace;
    font-size: 7px;
    color: #555;
  }

  /* Contact */
  .contact-box {
    border: 2px solid #00ff41;
    padding: 1.2rem 1.5rem;
    text-align: center;
    margin-top: 2rem;
    box-shadow: 0 0 20px rgba(0, 255, 65, 0.15);
    background: rgba(0, 255, 65, 0.03);
  }

  .contact-title {
    font-family: 'Press Start 2P', monospace;
    font-size: 11px;
    color: #ffff00;
    margin-bottom: 1rem;
    text-shadow: 1px 1px 0 #665500;
  }

  .contact-links {
    display: flex;
    justify-content: center;
    gap: 12px;
    flex-wrap: wrap;
  }

  .contact-link {
    font-family: 'Press Start 2P', monospace;
    font-size: 8px;
    color: #0a0a0a;
    background: #00ff41;
    padding: 8px 12px;
    text-decoration: none;
    border: 2px solid #00ff41;
    transition: all 0.12s;
    cursor: pointer;
  }

  .contact-link:hover {
    background: #0a0a0a;
    color: #00ff41;
  }

  .contact-link.cyan { background: #00ffff; border-color: #00ffff; }
  .contact-link.cyan:hover { background: transparent; color: #00ffff; }
  .contact-link.yellow { background: #ffff00; border-color: #ffff00; }
  .contact-link.yellow:hover { background: transparent; color: #ffff00; }

  /* Prompt cursor */
  .prompt {
    font-family: 'Press Start 2P', monospace;
    font-size: 10px;
    color: #00ff41;
    margin-bottom: 0.5rem;
  }

  .prompt::before { content: 'C:\\ARMAN> '; color: #ffff00; }

  /* Divider */
  .divider {
    border: none;
    border-top: 1px solid #1a3a1a;
    margin: 0.5rem 0 1.5rem;
  }

  /* Footer */
  .footer {
    text-align: center;
    font-family: 'Press Start 2P', monospace;
    font-size: 7px;
    color: #336633;
    padding: 1rem 0;
    letter-spacing: 1px;
  }

  .tech-section-label {
    font-family: 'Press Start 2P', monospace;
    font-size: 8px;
    color: #555;
    margin: 1rem 0 0.4rem;
  }

  /* Open to work pixel badge */
  .open-badge {
    display: inline-block;
    font-family: 'Press Start 2P', monospace;
    font-size: 8px;
    padding: 6px 10px;
    background: rgba(0, 255, 65, 0.1);
    color: #00ff41;
    border: 1px solid #00ff41;
    animation: pulse-border 2s infinite;
    margin-top: 0.8rem;
  }

  @keyframes pulse-border {
    0%, 100% { box-shadow: 0 0 0 0 rgba(0, 255, 65, 0.4); }
    50% { box-shadow: 0 0 0 5px rgba(0, 255, 65, 0); }
  }
</style>
</head>
<body>
<div class="scanline"></div>
<div class="container flicker">

  <!-- HEADER -->
  <div class="header-box">
    <span class="corner-tl">v2.0</span>
    <span class="corner-tr">MUJ</span>
    <span class="corner-bl">2023</span>
    <span class="corner-br">2027</span>

    <div style="font-family: 'Press Start 2P', monospace; font-size: 9px; color: #555; margin-bottom: 1rem; letter-spacing: 3px;">*** PLAYER PROFILE ***</div>

    <div class="player-name">ARMAN PHAUGAT</div>
    <div class="player-title">BACKEND ENGINEER &nbsp; // &nbsp; AI/ML BUILDER &nbsp; // &nbsp; CP WARRIOR</div>

    <div class="status-line">
      <span class="status-item">CLASS: <span class="status-val">B.Tech CSE</span></span>
      <span class="status-item">LEVEL: <span class="status-val">3rd Year</span></span>
      <span class="status-item">BASE: <span class="status-val">Jaipur, India</span></span>
    </div>

    <div>
      <div class="open-badge">
        <span class="blink">&#9646;</span>&nbsp; OPEN TO: SUMMER INTERNSHIP 2026
      </div>
    </div>
  </div>

  <!-- ABOUT -->
  <div class="prompt">whoami</div>
  <div class="divider"></div>
  <p style="color: #aaffaa; font-size: 18px; line-height: 1.8; margin-bottom: 0.5rem;">
    A CSE student who solves 900+ DSA problems not for the resume line, but because finding the most elegant path through a hard problem is genuinely satisfying.
  </p>
  <p style="color: #667766; font-size: 16px; margin-bottom: 1.5rem;">
    Core weapons: <span style="color: #00ffff;">Node.js &middot; Redis &middot; BullMQ &middot; MySQL</span> for backend warfare, and <span style="color: #00ffff;">LangChain &middot; FAISS &middot; Groq</span> for AI ops.
  </p>

  <!-- STATS -->
  <div class="section-header">PLAYER STATS</div>
  <div class="stats-grid">
    <div class="stat-card">
      <span class="stat-icon">// GPA</span>
      <span class="stat-val">9.05</span>
      <span class="stat-label">CGPA / 10.0</span>
    </div>
    <div class="stat-card">
      <span class="stat-icon">// DSA</span>
      <span class="stat-val">900+</span>
      <span class="stat-label">Problems Solved</span>
    </div>
    <div class="stat-card">
      <span class="stat-icon">// RANK</span>
      <span class="stat-val">TOP 0.3%</span>
      <span class="stat-label">LeetCode Global</span>
    </div>
    <div class="stat-card">
      <span class="stat-icon">// BUILDS</span>
      <span class="stat-val">10+</span>
      <span class="stat-label">Projects Shipped</span>
    </div>
  </div>

  <!-- SKILL BARS -->
  <div class="section-header">SKILL LEVELS</div>
  <div class="hp-bar-wrap">
    <div class="hp-row">
      <span class="hp-label">BACKEND</span>
      <div class="hp-track"><div class="hp-fill" style="width: 92%; background: #00cc33; animation-delay: 0s;"></div></div>
      <span class="hp-num">92/100</span>
    </div>
    <div class="hp-row">
      <span class="hp-label">DSA / CP</span>
      <div class="hp-track"><div class="hp-fill" style="width: 95%; background: #ffcc00; animation-delay: 0.15s;"></div></div>
      <span class="hp-num">95/100</span>
    </div>
    <div class="hp-row">
      <span class="hp-label">AI / ML</span>
      <div class="hp-track"><div class="hp-fill" style="width: 82%; background: #00ccff; animation-delay: 0.3s;"></div></div>
      <span class="hp-num">82/100</span>
    </div>
    <div class="hp-row">
      <span class="hp-label">FRONTEND</span>
      <div class="hp-track"><div class="hp-fill" style="width: 65%; background: #dd88ff; animation-delay: 0.45s;"></div></div>
      <span class="hp-num">65/100</span>
    </div>
    <div class="hp-row">
      <span class="hp-label">DEVOPS</span>
      <div class="hp-track"><div class="hp-fill" style="width: 70%; background: #ff8844; animation-delay: 0.6s;"></div></div>
      <span class="hp-num">70/100</span>
    </div>
  </div>

  <!-- TECH STACK -->
  <div class="section-header">EQUIPPED ITEMS (TECH STACK)</div>

  <div class="tech-section-label">// LANGUAGES</div>
  <div class="tech-grid">
    <div class="tech-tag">Python</div>
    <div class="tech-tag">JavaScript</div>
    <div class="tech-tag">C++</div>
    <div class="tech-tag">Java</div>
    <div class="tech-tag">C</div>
  </div>

  <div class="tech-section-label">// BACKEND</div>
  <div class="tech-grid">
    <div class="tech-tag">Node.js</div>
    <div class="tech-tag">Express.js</div>
    <div class="tech-tag">FastAPI</div>
    <div class="tech-tag">BullMQ</div>
    <div class="tech-tag">Redis</div>
    <div class="tech-tag">JWT</div>
  </div>

  <div class="tech-section-label">// DATABASES</div>
  <div class="tech-grid">
    <div class="tech-tag db">MySQL</div>
    <div class="tech-tag db">MongoDB</div>
    <div class="tech-tag db">SQLite</div>
    <div class="tech-tag db">Redis Cache</div>
  </div>

  <div class="tech-section-label">// AI / ML</div>
  <div class="tech-grid">
    <div class="tech-tag ai">LangChain</div>
    <div class="tech-tag ai">HuggingFace</div>
    <div class="tech-tag ai">Scikit-learn</div>
    <div class="tech-tag ai">XGBoost</div>
    <div class="tech-tag ai">FAISS</div>
    <div class="tech-tag ai">Groq</div>
    <div class="tech-tag ai">Streamlit</div>
  </div>

  <div class="tech-section-label">// DEVOPS & TOOLS</div>
  <div class="tech-grid">
    <div class="tech-tag devops">Docker</div>
    <div class="tech-tag devops">Git</div>
    <div class="tech-tag devops">GitHub</div>
    <div class="tech-tag devops">Postman</div>
    <div class="tech-tag devops">React</div>
    <div class="tech-tag devops">TailwindCSS</div>
  </div>

  <!-- ACHIEVEMENTS -->
  <div class="section-header">ACHIEVEMENTS UNLOCKED</div>

  <div class="achievement">
    <span class="ach-badge">GOLD<br>TROPHY</span>
    <div class="ach-text"><strong>Dean's Excellence Award</strong><br>Multiple semesters &middot; 9.05 CGPA at Manipal University Jaipur</div>
  </div>

  <div class="achievement">
    <span class="ach-badge">RANK<br>#TOP</span>
    <div class="ach-text"><strong>LeetCode Top 0.3%</strong><br>900+ problems solved &middot; Beats 99.7% of coders globally</div>
  </div>

  <div class="achievement">
    <span class="ach-badge">FIRE<br>STREAK</span>
    <div class="ach-text"><strong>900+ DSA Problems</strong><br>LeetCode &middot; Codeforces &middot; Multi-platform grind</div>
  </div>

  <div class="achievement">
    <span class="ach-badge">HACK<br>ROUND2</span>
    <div class="ach-text"><strong>MUJHackX Round 2 Qualifier</strong><br>Top performers among 1300+ participants</div>
  </div>

  <!-- CERTIFICATIONS -->
  <div class="section-header">SKILL CERTIFICATES</div>
  <ul class="cert-list">
    <li>Competitive Programming — Level 1, 2 & 3 <span style="color:#555; font-size: 14px;">(TLE Eliminators)</span><span class="cert-year">2023</span></li>
    <li>Database Design and Foundations <span style="color:#555; font-size: 14px;">(Oracle)</span><span class="cert-year">2024</span></li>
    <li>OOP Programming using Java <span style="color:#555; font-size: 14px;">(GeeksforGeeks)</span><span class="cert-year">2024</span></li>
    <li>RAG — Retrieval-Augmented Generation <span style="color:#555; font-size: 14px;">(Coursera)</span><span class="cert-year">2024</span></li>
    <li>Design and Analysis of Algorithms <span style="color:#555; font-size: 14px;">(NPTEL)</span><span class="cert-year">2025</span></li>
    <li>System Administration I & II <span style="color:#555; font-size: 14px;">(Red Hat)</span><span class="cert-year">2025</span></li>
  </ul>

  <!-- CONTACT -->
  <div class="contact-box">
    <div class="contact-title">INSERT COIN TO COLLABORATE</div>
    <div class="contact-links">
      <a class="contact-link" href="mailto:armanphaugat20@gmail.com">EMAIL</a>
      <a class="contact-link cyan" href="https://linkedin.com/in/armanphaugat05">LINKEDIN</a>
      <a class="contact-link yellow" href="https://armanphaugat.netlify.app">PORTFOLIO</a>
      <a class="contact-link" href="https://leetcode.com/u/armanphaugat20" style="background: #ff6600; border-color: #ff6600; color: #0a0a0a;">LEETCODE</a>
    </div>
  </div>

  <div class="footer">
    &gt; README.md &nbsp;|&nbsp; ARMAN PHAUGAT &copy; 2025 &nbsp;|&nbsp; ALL RIGHTS RESERVED &nbsp;|&nbsp;
    <span class="blink">_</span>
  </div>

</div>
</body>
</html>
