<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <meta name="theme-color" content="#0b1020" />
  <meta name="apple-mobile-web-app-capable" content="yes" />
  <meta name="apple-mobile-web-app-status-bar-style" content="black" />
  <title>Battle Royale King</title>
  <link rel="manifest" href="manifest.json" />
  <style>
    :root {
      --bg: #0b1020;
      --panel: #151d38;
      --panel-alt: #1d2749;
      --primary: #4969ff;
      --accent: #6ee7ff;
      --success: #19a974;
      --danger: #e64f67;
      --warning: #ffd76e;
      --text: #f5f7ff;
      --muted: #9ca8cc;
      --line: rgba(110, 231, 255, 0.18);
    }

    * { box-sizing: border-box; }

    html, body {
      margin: 0;
      width: 100%;
      height: 100%;
      background: var(--bg);
      color: var(--text);
      font-family: Arial, sans-serif;
      overflow: hidden;
    }

    body {
      background: radial-gradient(circle at top, rgba(73, 105, 255, 0.2), transparent 28%), var(--bg);
    }

    button {
      font: inherit;
      cursor: pointer;
      border: none;
      border-radius: 12px;
      color: #fff;
      transition: 0.2s ease;
    }

    button:active { transform: scale(0.98); }

    .screen {
      display: none;
      width: 100%;
      height: 100%;
      position: absolute;
      inset: 0;
    }

    .screen.active { display: block; }

    .menu-screen {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 20px;
      background: linear-gradient(135deg, #19265b 0%, #612c78 55%, #0b1020 100%);
    }

    .logo {
      font-size: clamp(42px, 9vw, 80px);
      margin-bottom: 10px;
      filter: drop-shadow(0 0 18px rgba(110, 231, 255, 0.5));
    }

    .title {
      font-size: clamp(28px, 5vw, 54px);
      font-weight: 700;
      color: var(--accent);
      text-transform: uppercase;
      letter-spacing: 1px;
      margin-bottom: 6px;
      text-align: center;
    }

    .subtitle {
      color: var(--muted);
      font-size: 16px;
      margin-bottom: 34px;
      text-align: center;
    }

    .menu-actions {
      display: flex;
      flex-direction: column;
      gap: 12px;
      width: min(90vw, 320px);
    }

    .btn {
      width: 100%;
      padding: 16px 18px;
      background: var(--primary);
      font-weight: 700;
      letter-spacing: 0.5px;
      box-shadow: 0 12px 28px rgba(73, 105, 255, 0.25);
    }

    .btn.secondary {
      background: rgba(255,255,255,0.04);
      border: 1px solid var(--primary);
      color: var(--accent);
      box-shadow: none;
    }

    .game-screen {
      display: flex;
      flex-direction: column;
      height: 100%;
    }

    .topbar {
      height: 72px;
      background: rgba(21, 29, 56, 0.95);
      border-bottom: 1px solid var(--line);
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 12px 16px;
      gap: 12px;
    }

    .topbar .left {
      display: flex;
      gap: 10px;
      align-items: center;
      flex-wrap: wrap;
    }

    .badge {
      background: rgba(73, 105, 255, 0.16);
      border: 1px solid var(--primary);
      color: var(--accent);
      padding: 8px 12px;
      border-radius: 999px;
      font-size: 12px;
      font-weight: 700;
    }

    .badge.gold {
      background: rgba(255, 215, 110, 0.12);
      border-color: var(--warning);
      color: var(--warning);
    }

    .status-text {
      flex: 1;
      text-align: center;
      font-weight: 700;
      color: var(--accent);
    }

    .main-area {
      flex: 1;
      display: flex;
      gap: 14px;
      padding: 14px;
      min-height: 0;
    }

    .arena {
      position: relative;
      flex: 1;
      min-width: 0;
      border-radius: 18px;
      border: 1px solid var(--line);
      background: linear-gradient(180deg, rgba(17, 24, 46, 0.9), rgba(8, 12, 22, 0.95));
      overflow: hidden;
    }

    .arena::before {
      content: "";
      position: absolute;
      inset: 0;
      background:
        linear-gradient(rgba(255,255,255,0.03) 1px, transparent 1px),
        linear-gradient(90deg, rgba(255,255,255,0.03) 1px, transparent 1px),
        radial-gradient(circle at center, rgba(73, 105, 255, 0.14), transparent 55%);
      background-size: 26px 26px, 26px 26px, 100% 100%;
      opacity: 0.9;
    }

    .player, .enemy {
      position: absolute;
      width: 82px;
      height: 90px;
      border-radius: 18px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 42px;
      font-weight: bold;
      z-index: 2;
      box-shadow: 0 10px 28px rgba(0,0,0,0.28);
      transform: translateY(-50%);
    }

    .player {
      left: 7%;
      top: 52%;
      background: linear-gradient(135deg, #4969ff, #6ee7ff);
    }

    .enemy {
      right: 7%;
      top: 52%;
      background: linear-gradient(135deg, #e64f67, #ff8d7a);
    }

    .enemy-panel {
      position: absolute;
      left: 50%;
      top: 14px;
      transform: translateX(-50%);
      z-index: 3;
      text-align: center;
      min-width: 200px;
    }

    .enemy-name {
      color: var(--accent);
      font-weight: 700;
      margin-bottom: 8px;
      font-size: 18px;
    }

    .hpbar {
      width: 210px;
      height: 16px;
      border-radius: 999px;
      background: rgba(255,255,255,0.08);
      overflow: hidden;
      border: 1px solid rgba(255,255,255,0.12);
      margin: 0 auto;
    }

    .hpfill {
      width: 100%;
      height: 100%;
      background: linear-gradient(90deg, #2ecf8d, #7ef2be);
      transition: width 0.3s ease;
    }

    .enemy-hp-text {
      color: var(--muted);
      font-size: 12px;
      margin-top: 6px;
    }

    .panel {
      width: 260px;
      background: var(--panel);
      border: 1px solid var(--line);
      border-radius: 18px;
      padding: 14px;
      display: flex;
      flex-direction: column;
      gap: 12px;
    }

    .panel h3 {
      margin: 0;
      text-align: center;
      color: var(--accent);
      font-size: 16px;
    }

    .stat-row {
      display: flex;
      justify-content: space-between;
      align-items: center;
      background: rgba(255,255,255,0.04);
      border-radius: 10px;
      padding: 10px 12px;
      border: 1px solid rgba(255,255,255,0.04);
    }

    .stat-row small {
      color: var(--muted);
      font-size: 11px;
    }

    .stat-row strong {
      font-size: 18px;
      color: var(--accent);
    }

    .log-panel {
      max-height: 220px;
      overflow: auto;
      padding: 10px;
      border-radius: 12px;
      background: rgba(10, 15, 30, 0.8);
      border: 1px solid rgba(255,255,255,0.04);
    }

    .log-entry {
      padding: 8px 10px;
      border-left: 2px solid var(--primary);
      margin-bottom: 8px;
      color: var(--muted);
      font-size: 12px;
      background: rgba(255,255,255,0.015);
      border-radius: 0 8px 8px 0;
    }

    .log-entry.damage { border-left-color: var(--danger); color: #ffd7dc; }
    .log-entry.heal { border-left-color: var(--success); color: #d7fbe7; }
    .log-entry.win { border-left-color: var(--warning); color: #fff1b6; }

    .controls {
      display: grid;
      grid-template-columns: repeat(4, minmax(120px, 1fr));
      gap: 10px;
      padding: 12px 14px 16px;
      background: rgba(21, 29, 56, 0.95);
      border-top: 1px solid var(--line);
    }

    .action-btn {
      padding: 14px 10px;
      background: rgba(73, 105, 255, 0.16);
      border: 1px solid var(--primary);
      color: var(--accent);
      font-weight: 700;
    }

    .action-btn.ultimate {
      background: rgba(230, 79, 103, 0.16);
      border-color: var(--danger);
      color: #ffb2bf;
    }

    .modal {
      position: fixed;
      inset: 0;
      background: rgba(0, 0, 0, 0.8);
      display: none;
      align-items: center;
      justify-content: center;
      padding: 20px;
      z-index: 8;
    }

    .modal.active { display: flex; }

    .modal-card {
      position: relative;
      width: min(90vw, 500px);
      background: var(--panel);
      border: 1px solid var(--line);
      border-radius: 18px;
      padding: 22px 20px 18px;
    }

    .modal-title {
      margin: 0 0 14px;
      color: var(--accent);
      font-size: 28px;
      text-align: center;
    }

    .close-btn {
      position: absolute;
      right: 12px;
      top: 12px;
      width: 34px;
      height: 34px;
      background: var(--danger);
      border-radius: 50%;
      font-weight: 700;
      font-size: 20px;
    }

    .store-item {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 10px;
      background: rgba(255,255,255,0.04);
      border: 1px solid rgba(255,255,255,0.06);
      border-radius: 12px;
      padding: 14px 12px;
      margin-bottom: 10px;
    }

    .store-item strong { color: var(--warning); }

    .store-item small { color: var(--muted); }

    @media (max-width: 820px) {
      .main-area {
        flex-direction: column;
      }
      .panel {
        width: 100%;
      }
      .controls {
        grid-template-columns: repeat(2, minmax(120px, 1fr));
      }
      .arena {
        min-height: 350px;
      }
    }
  </style>
</head>
<body>
  <div id="menuScreen" class="screen active menu-screen">
    <div class="logo">⚔️</div>
    <div class="title">Battle Royale King</div>
    <div class="subtitle">Bertarung di arena ringan seperti battle royale</div>
    <div class="menu-actions">
      <button class="btn" onclick="startGame()">Mulai Game</button>
      <button class="btn secondary" onclick="openModal('settingsModal')">Pengaturan</button>
    </div>
  </div>

  <div id="gameScreen" class="screen game-screen">
    <div class="topbar">
      <div class="left">
        <div class="badge">LV <span id="levelLabel">1</span></div>
        <div class="badge gold">💎 <span id="diamondLabel">250</span></div>
      </div>
      <div class="status-text" id="statusText">Siap bertarung</div>
      <button class="btn secondary" style="padding:10px 16px; width:auto;" onclick="showMenu()">Menu</button>
    </div>

    <div class="main-area">
      <div class="arena">
        <div class="enemy-panel">
          <div class="enemy-name" id="enemyName">Enemy</div>
          <div class="hpbar"><div class="hpfill" id="enemyHpFill"></div></div>
          <div class="enemy-hp-text" id="enemyHpText">100/100</div>
        </div>
        <div class="player">🧑</div>
        <div class="enemy">👹</div>
      </div>

      <div class="panel">
        <h3>Player Stats</h3>
        <div class="stat-row"><small>HP</small><strong id="hpStat">100</strong></div>
        <div class="stat-row"><small>MP</small><strong id="mpStat">20</strong></div>
        <div class="stat-row"><small>ATK</small><strong id="atkStat">18</strong></div>
        <div class="stat-row"><small>DEF</small><strong id="defStat">8</strong></div>
        <div class="stat-row"><small>XP</small><strong id="xpStat">0</strong></div>
        <button class="btn" onclick="startBattle()">Battle Baru</button>
        <div class="log-panel" id="battleLog"></div>
      </div>
    </div>

    <div class="controls">
      <button class="action-btn" onclick="playerAction('attack')">⚔ Serang</button>
      <button class="action-btn" onclick="playerAction('skill')">✨ Skill</button>
      <button class="action-btn" onclick="playerAction('heal')">💚 Heal</button>
      <button class="action-btn ultimate" onclick="playerAction('ultimate')">🔥 Ultimate</button>
    </div>
  </div>

  <div id="topupModal" class="modal" aria-modal="true">
    <div class="modal-card">
      <button class="close-btn" onclick="closeModal('topupModal')">×</button>
      <h2 class="modal-title">💎 Top Up</h2>
      <div class="store-item">
        <div>
          <strong>100 Diamond</strong><br>
          <small>Rp10.000</small>
        </div>
        <button class="btn" style="width:auto;padding:12px 16px;" onclick="topup(100)">Beli</button>
      </div>
      <div class="store-item">
        <div>
          <strong>550 Diamond</strong><br>
          <small>Rp50.000</small>
        </div>
        <button class="btn" style="width:auto;padding:12px 16px;" onclick="topup(550)">Beli</button>
      </div>
      <div class="store-item">
        <div>
          <strong>1.200 Diamond</strong><br>
          <small>Rp100.000</small>
        </div>
        <button class="btn" style="width:auto;padding:12px 16px;" onclick="topup(1200)">Beli</button>
      </div>
      <div id="topupStatus" style="margin-top:14px; text-align:center; color: var(--success); font-weight:700;"></div>
    </div>
  </div>

  <div id="settingsModal" class="modal" aria-modal="true">
    <div class="modal-card">
      <button class="close-btn" onclick="closeModal('settingsModal')">×</button>
      <h2 class="modal-title">⚙️ Pengaturan</h2>
      <div class="store-item" style="display:block;">
        <label style="display:flex; align-items:center; gap:8px; color:var(--text);">
          <input type="checkbox" id="soundToggle" checked /> Suara
        </label>
      </div>
      <button class="btn" onclick="openModal('topupModal')">💎 Top Up</button>
    </div>
  </div>

  <script>
    const game = {
      level: 1,
      diamond: 250,
      xp: 0,
      maxHp: 100,
      hp: 100,
      maxMp: 20,
      mp: 20,
      atk: 18,
      def: 8,
      battleActive: false,
      currentEnemy: null,
      soundEnabled: true
    };

    const enemies = [
      { name: 'ShadowX', emoji: '👹', hp: 90, atk: 14, reward: 30 },
      { name: 'DragonKing', emoji: '🐉', hp: 110, atk: 17, reward: 45 },
      { name: 'IceMage', emoji: '🧙', hp: 100, atk: 15, reward: 35 },
      { name: 'TitanZero', emoji: '🤖', hp: 140, atk: 20, reward: 70 }
    ];

    function startGame() {
      document.getElementById('menuScreen').classList.remove('active');
      document.getElementById('gameScreen').classList.add('active');
      updateUI();
    }

    function showMenu() {
      document.getElementById('gameScreen').classList.remove('active');
      document.getElementById('menuScreen').classList.add('active');
    }

    function openModal(id) {
      closeModal('settingsModal');
      closeModal('topupModal');
      document.getElementById(id).classList.add('active');
    }

    function closeModal(id) {
      document.getElementById(id).classList.remove('active');
    }

    function startBattle() {
      if (game.battleActive) return;
      const enemy = enemies[Math.floor(Math.random() * enemies.length)];
      game.currentEnemy = { ...enemy, currentHp: enemy.hp };
      game.battleActive = true;
      game.hp = game.maxHp;
      game.mp = game.maxMp;
      addLog('Battle dimulai melawan ' + enemy.name + '.', 'win');
      updateUI();
    }

    function playerAction(action) {
      if (!game.battleActive || !game.currentEnemy) {
        setStatus('Mulai battle dulu');
        return;
      }

      if (action === 'attack') {
        const damage = Math.max(8, game.atk + randomInt(2, 10) - Math.floor(game.currentEnemy.atk / 4));
        game.currentEnemy.currentHp -= damage;
        addLog('Kamu menyerang ' + game.currentEnemy.name + ' dengan ' + damage + ' damage.', 'damage');
        setStatus('Serangan berhasil!');
      }

      if (action === 'skill') {
        if (game.mp < 6) {
          addLog('MP tidak cukup untuk skill.', 'damage');
          return;
        }
        game.mp -= 6;
        const damage = game.atk + randomInt(10, 22);
        game.currentEnemy.currentHp -= damage;
        addLog('Skill aktif! ' + game.currentEnemy.name + ' kena ' + damage + ' damage.', 'damage');
        setStatus('Skill dipakai!');
      }

      if (action === 'heal') {
        if (game.mp < 4) {
          addLog('MP tidak cukup untuk heal.', 'damage');
          return;
        }
        game.mp -= 4;
        const heal = randomInt(14, 24);
        game.hp = Math.min(game.maxHp, game.hp + heal);
        addLog('Kamu heal + ' + heal + ' HP.', 'heal');
        setStatus('HP pulih!');
      }

      if (action === 'ultimate') {
        if (game.mp < 10) {
          addLog('MP tidak cukup untuk ultimate.', 'damage');
          return;
        }
        game.mp -= 10;
        const damage = game.atk + randomInt(25, 42);
        game.currentEnemy.currentHp -= damage;
        addLog('ULTIMATE! ' + game.currentEnemy.name + ' menerima ' + damage + ' damage besar!', 'damage');
        setStatus('Ultimate aktif!');
      }

      if (game.currentEnemy.currentHp <= 0) {
        game.currentEnemy.currentHp = 0;
        winBattle();
        updateUI();
        return;
      }

      updateUI();
      setTimeout(enemyTurn, 500);
    }

    function enemyTurn() {
      if (!game.battleActive || !game.currentEnemy) return;
      const damage = Math.max(5, game.currentEnemy.atk + randomInt(0, 8) - game.def);
      game.hp = Math.max(0, game.hp - damage);
      addLog(game.currentEnemy.name + ' menyerang balik ' + damage + ' damage.', 'damage');

      if (game.hp <= 0) {
        game.hp = 0;
        loseBattle();
      }

      updateUI();
    }

    function winBattle() {
      const reward = game.currentEnemy.reward;
      game.diamond += reward;
      game.xp += 20;
      game.battleActive = false;

      if (game.xp >= 100) {
        game.xp -= 100;
        game.level += 1;
        game.maxHp += 8;
        game.maxMp += 2;
        game.atk += 2;
        game.def += 1;
        game.hp = game.maxHp;
        game.mp = game.maxMp;
        addLog('Level Up! Sekarang LV ' + game.level + '.', 'win');
      }

      addLog('Kamu menang! + ' + reward + ' Diamond.', 'win');
      setStatus('Battle dimenangkan!');
      game.currentEnemy = null;
      updateUI();
    }

    function loseBattle() {
      game.battleActive = false;
      addLog('Kamu kalah. Coba lagi!', 'damage');
      setStatus('Kamu kalah');
      game.currentEnemy = null;
      updateUI();
    }

    function topup(amount) {
      game.diamond += amount;
      document.getElementById('topupStatus').textContent = 'Pembayaran berhasil! +' + amount + ' Diamond';
      setTimeout(() => {
        document.getElementById('topupStatus').textContent = '';
      }, 1800);
      updateUI();
    }

    function addLog(message, type = 'default') {
      const log = document.getElementById('battleLog');
      const row = document.createElement('div');
      row.className = 'log-entry ' + type;
      row.textContent = message;
      log.prepend(row);
      while (log.children.length > 12) log.removeChild(log.lastChild);
    }

    function setStatus(msg) {
      document.getElementById('statusText').textContent = msg;
    }

    function updateUI() {
      document.getElementById('levelLabel').textContent = game.level;
      document.getElementById('diamondLabel').textContent = game.diamond;
      document.getElementById('hpStat').textContent = game.hp;
      document.getElementById('mpStat').textContent = game.mp;
      document.getElementById('atkStat').textContent = game.atk;
      document.getElementById('defStat').textContent = game.def;
      document.getElementById('xpStat').textContent = game.xp;

      if (game.currentEnemy) {
        const hpPercent = (game.currentEnemy.currentHp / game.currentEnemy.hp) * 100;
        document.getElementById('enemyName').textContent = game.currentEnemy.name;
        document.getElementById('enemyHpFill').style.width = Math.max(0, hpPercent) + '%';
        document.getElementById('enemyHpText').textContent = Math.max(0, game.currentEnemy.currentHp) + '/' + game.currentEnemy.hp;
      } else {
        document.getElementById('enemyName').textContent = 'Enemy';
        document.getElementById('enemyHpFill').style.width = '100%';
        document.getElementById('enemyHpText').textContent = '0/0';
      }
    }

    function randomInt(min, max) {
      return Math.floor(Math.random() * (max - min + 1)) + min;
    }

    document.getElementById('soundToggle').addEventListener('change', (e) => {
      game.soundEnabled = e.target.checked;
    });

    updateUI();

    if ('serviceWorker' in navigator) {
      window.addEventListener('load', () => {
        navigator.serviceWorker.register('./sw.js').catch(() => {});
      });
    }
  </script>
</body>
</html>
