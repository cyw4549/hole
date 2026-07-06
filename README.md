<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>우주비행사 블랙홀 피하기</title>
  <style>
    :root {
      color-scheme: dark;
      font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
    }

    body {
      margin: 0;
      min-height: 100vh;
      display: grid;
      place-items: center;
      background: radial-gradient(circle at center, #240b2f 0%, #06030b 45%, #000000 100%);
      color: #f4f7ff;
      overflow: hidden;
    }

    .game-shell {
      width: min(96vw, 900px);
      padding: 16px;
      border: 1px solid rgba(255,255,255,0.12);
      border-radius: 18px;
      background: rgba(8, 4, 16, 0.85);
      box-shadow: 0 18px 70px rgba(0,0,0,0.55);
    }

    h1 {
      margin: 0 0 8px;
      font-size: 24px;
      letter-spacing: 1px;
    }

    .intro {
      margin: 0 0 10px;
      color: #98afc9;
      font-size: 13px;
      line-height: 1.5;
    }

    .hud {
      display: flex;
      flex-wrap: wrap;
      gap: 12px;
      margin-bottom: 10px;
      font-size: 13px;
      color: #d0d9e6;
    }

    .hud span {
      padding: 5px 9px;
      border-radius: 999px;
      background: rgba(255,255,255,0.06);
    }

    .shield-bar {
      margin-bottom: 10px;
      display: grid;
      gap: 6px;
    }

    .shield-bar__track {
      width: 100%;
      height: 10px;
      background: rgba(255,255,255,0.06);
      border-radius: 999px;
      overflow: hidden;
      box-shadow: inset 0 0 0 1px rgba(255,255,255,0.06);
    }

    .shield-bar__fill {
      width: 100%;
      height: 100%;
      background: linear-gradient(90deg, #a5f3fc, #60a5fa);
      transition: width 0.1s ease;
    }

    canvas {
      width: 100%;
      height: auto;
      border-radius: 16px;
      border: 1px solid rgba(255,255,255,0.14);
      background: radial-gradient(circle at center, #12051d 0%, #040208 65%, #000000 100%);
      display: block;
    }

    .controls {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-top: 12px;
      flex-wrap: wrap;
      gap: 10px;
    }

    button {
      border: none;
      border-radius: 999px;
      padding: 8px 14px;
      font-size: 13px;
      font-weight: 700;
      cursor: pointer;
      color: white;
      background: linear-gradient(90deg, #ff5e62, #ff8a00);
      box-shadow: 0 6px 18px rgba(255, 94, 98, 0.22);
    }

    .hint {
      color: #9fb3d1;
      font-size: 12px;
    }

    .music-panel {
      display: flex;
      align-items: center;
      gap: 10px;
      margin-top: 10px;
      flex-wrap: wrap;
    }

    .music-panel button {
      border: none;
      border-radius: 999px;
      padding: 8px 14px;
      font-size: 13px;
      font-weight: 700;
      cursor: pointer;
      color: white;
      background: linear-gradient(90deg, #3b82f6, #06b6d4);
      box-shadow: 0 6px 18px rgba(56, 189, 248, 0.2);
    }

    .shop-overlay {
      position: fixed;
      inset: 0;
      display: none;
      justify-content: center;
      align-items: center;
      background: rgba(0,0,0,0.78);
      z-index: 20;
    }

    .shop-overlay.open {
      display: flex;
    }

    .shop-modal {
      width: min(500px, 90vw);
      background: rgba(12, 18, 34, 0.96);
      border: 1px solid rgba(255,255,255,0.12);
      border-radius: 18px;
      padding: 18px;
      box-shadow: 0 18px 48px rgba(0,0,0,0.45);
    }

    .shop-modal h2 {
      margin: 0 0 10px;
      color: #f8fafc;
      font-size: 18px;
    }

    .shop-list {
      display: grid;
      gap: 10px;
      margin-bottom: 14px;
    }

    .shop-item {
      display: grid;
      grid-template-columns: 1fr auto;
      gap: 10px;
      align-items: center;
      padding: 12px 14px;
      border-radius: 14px;
      background: rgba(255,255,255,0.04);
      border: 1px solid rgba(255,255,255,0.07);
    }

    .shop-item__meta {
      color: #d8e3f2;
      font-size: 13px;
      line-height: 1.4;
    }

    .shop-item button {
      background: linear-gradient(90deg, #10b981, #06b6d4);
      box-shadow: 0 10px 24px rgba(6, 182, 212, 0.2);
    }

    .shop-item button[disabled] {
      background: rgba(255,255,255,0.1);
      cursor: not-allowed;
      box-shadow: none;
    }

    .shop-close {
      margin-top: 12px;
      padding: 10px 16px;
      border-radius: 999px;
      background: linear-gradient(90deg, #f97316, #fb7185);
      color: white;
      border: none;
      cursor: pointer;
    }

    .warning-note {
      color: #fda4af;
      font-size: 14px;
      margin-top: 12px;
    }

    .bgm-frame {
      display: none;
    }
  </style>
</head>
<body>
  <div class="game-shell">
    <h1>우주비행사 블랙홀 피하기</h1>
    <p class="intro">일반 상대성 이론을 바탕으로 시공간이 휘어진 우주를 누비세요. 여러 개의 블랙홀 주변에 남은 별의 잔해가 흩어져 있고, 그 잔해를 모을수록 포인트와 돈이 쌓입니다. 블랙홀에 닿으면 끝납니다.</p>
    <div class="hud">
      <span>포인트: <strong id="points">0</strong></span>
      <span>돈: <strong id="money">0</strong></span>
      <span>수집한 잔해: <strong id="debrisCount">0</strong></span>
      <span>생존 시간: <strong id="time">0.0</strong>초</span>
      <span>상태: <strong id="status">준비</strong></span>
      <span>버티기: <strong id="shieldValue">100%</strong></span>
    </div>
    <div class="shield-bar">
      <div class="shield-bar__track">
        <div class="shield-bar__fill" id="shieldGauge"></div>
      </div>
      <div class="hint">좌클릭 길게 눌러 블랙홀 끌어당김을 반으로 감소</div>
    </div>
    <canvas id="gameCanvas" width="900" height="520"></canvas>
    <div class="controls">
      <button id="restartBtn">게임 시작</button>
      <div class="hint">조작: 방향키 또는 WASD</div>
    </div>
    <div class="music-panel">
      <button id="musicToggle">BGM 재생</button>
      <div class="hint">버튼을 눌러 유튜브 음악을 켜거나 끌 수 있습니다.</div>
    </div>
    <audio id="bgm" loop>
    <source src="bgm.mp3" type="audio/mpeg">
     </audio>
  </div>

  <div class="shop-overlay" id="shopOverlay">
    <div class="shop-modal">
      <h2>상점</h2>
      <div class="hint">오른쪽 클릭으로 진입, 잔해를 팔아 번 돈으로 장비를 구입하세요.</div>
      <div class="shop-list" id="shopList"></div>
      <div class="hint">현재 자금: <strong id="shopMoney">0</strong>원</div>
      <button id="closeShopBtn">상점 닫기</button>
      <div class="warning-note">시간이 정지된 상태입니다. 구입 후 닫기를 눌러 게임을 계속하세요.</div>
    </div>
  </div>

  <script>
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');
    const pointsEl = document.getElementById('points');
    const moneyEl = document.getElementById('money');
    const debrisCountEl = document.getElementById('debrisCount');
    const timeEl = document.getElementById('time');
    const statusEl = document.getElementById('status');
    const shieldValueEl = document.getElementById('shieldValue');
    const shieldGaugeEl = document.getElementById('shieldGauge');
    const restartBtn = document.getElementById('restartBtn');
    const musicToggle = document.getElementById('musicToggle');
    const bgm = document.getElementById('bgm');
    const shopOverlay = document.getElementById('shopOverlay');
    const shopList = document.getElementById('shopList');
    const shopMoney = document.getElementById('shopMoney');
    const closeShopBtn = document.getElementById('closeShopBtn');


    let musicPlaying = false;
    let audioContext;
    let alarmGain;
    let alarmOsc;
    let alarmLFO;
    let alarmStarted = false;

    const keys = {};
    const state = {
      running: false,
      elapsed: 0,
      points: 0,
      money: 0,
      collectedDebris: 0,
      status: '준비',
      stars: [],
      debris: [],
      obstacles: [],
      shieldCharge: 1,
      shieldActive: false,
      holdingShield: false,
      shopOpen: false,
      wasRunning: false,
      shieldDrainFactor: 1,
      shieldRegenFactor: 1,
      gravityFactor: 1,
      closestDanger: 0,
      deathReason: 'none'
    };

    const player = {
      x: canvas.width / 2,
      y: canvas.height / 2,
      vx: 0,
      vy: 0,
      radius: 6,
      maxSpeed: 5.2
    };

    const trail = [];

    let blackHoles = [];
    let lastTime = 0;

    const shopItems = [
      {
        id: 'shieldUpgrade',
        name: '쉴드 용량 강화',
        desc: '버티기 소모량 20% 감소, 회복 속도 15% 증가',
        cost: 100,
        bought: false,
        apply() {
          state.shieldDrainFactor *= 0.8;
          state.shieldRegenFactor *= 1.15;
        }
      },
      {
        id: 'engineTuning',
        name: '엔진 튜닝',
        desc: '최대 속도 1.4 증가',
        cost: 120,
        bought: false,
        apply() {
          player.maxSpeed += 1.4;
        }
      },
      {
        id: 'gravityField',
        name: '중력 안정 장치',
        desc: '일반 중력 영향을 15% 감소',
        cost: 150,
        bought: false,
        apply() {
          state.gravityFactor *= 0.85;
        }
      }
    ];

    function resetGame() {
      state.running = true;
      state.elapsed = 0;
      state.points = 0;
      state.money = 0;
      state.collectedDebris = 0;
      state.status = '우주 항해 중';
      state.deathReason = 'none';
      state.shieldCharge = 1;
      state.shieldActive = false;
      state.holdingShield = false;
      player.x = canvas.width / 2;
      player.y = canvas.height / 2;
      player.vx = 0;
      player.vy = 0;
      blackHoles = Array.from({ length: 9 }, () => createHole());
      state.debris = [];
      blackHoles.forEach((hole, index) => {
        const debrisCount = 6 + Math.floor(Math.random() * 4);
        for (let i = 0; i < debrisCount; i += 1) {
          state.debris.push(createDebris(hole, index));
        }
      });
      state.obstacles = Array.from({ length: 6 }, () => createObstacle());
      state.stars = Array.from({ length: 260 }, () => ({
        x: Math.random() * canvas.width,
        y: Math.random() * canvas.height,
        r: Math.random() * 2.5 + 0.2,
        alpha: Math.random() * 0.6 + 0.18
      }));
      updateHud();
    }

    function createHole() {
      const centerX = canvas.width / 2;
      const centerY = canvas.height / 2;
      let x;
      let y;
      let radius;
      let attempts = 0;

      do {
        radius = 18 + Math.random() * 18;
        x = Math.random() * (canvas.width - 120) + 60;
        y = Math.random() * (canvas.height - 120) + 60;
        attempts += 1;
      } while (attempts < 40 && Math.hypot(x - centerX, y - centerY) < radius + 90);

      return {
        x,
        y,
        radius,
        mass: 0.9 + Math.random() * 1.2,
        driftX: (Math.random() - 0.5) * 0.8,
        driftY: (Math.random() - 0.5) * 0.8
      };
    }

    function createDebris(hole, holeIndex) {
      const angle = Math.random() * Math.PI * 2;
      const orbitRadius = hole.radius + 10 + Math.random() * 18;
      return {
        holeIndex,
        angle,
        orbitRadius,
        orbitSpeed: 0.6 + Math.random() * 0.8,
        radius: 2 + Math.random() * 2.4,
        value: 10 + Math.floor(Math.random() * 6) * 5,
        active: true
      };
    }

    function createObstacle() {
      const radius = 6 + Math.random() * 5;
      return {
        x: Math.random() * (canvas.width - radius * 2) + radius,
        y: Math.random() * (canvas.height - radius * 2) + radius,
        radius,
     driftX: -2,
     driftY: 2,
        rotation: Math.random() * Math.PI * 2,
        rotationSpeed: (Math.random() - 0.5) * 0.02
      };
    }

    function clamp(value, min, max) {
      return Math.max(min, Math.min(max, value));
    }

    function updateHud() {
      pointsEl.textContent = state.points;
      moneyEl.textContent = state.money;
      debrisCountEl.textContent = state.collectedDebris;
      timeEl.textContent = state.elapsed.toFixed(1);
      statusEl.textContent = state.status;
      shieldValueEl.textContent = `${Math.round(state.shieldCharge * 100)}%`;
      shieldGaugeEl.style.width = `${Math.round(state.shieldCharge * 100)}%`;
    }

    function updateTrail(dt) {
      const speed = Math.hypot(player.vx, player.vy);
      if (speed > 0.4) {
        trail.push({ x: player.x, y: player.y, alpha: 0.8, size: 4 + speed * 1.2 });
      }
      for (let i = trail.length - 1; i >= 0; i -= 1) {
        trail[i].alpha -= dt * 0.9;
        if (trail[i].alpha <= 0) {
          trail.splice(i, 1);
        }
      }
      if (trail.length > 28) {
        trail.splice(0, trail.length - 28);
      }
    }

    function drawTrail() {
      trail.forEach((point, index) => {
        const fade = point.alpha * (1 - index / trail.length);
        ctx.fillStyle = `rgba(255,255,255,${fade * 0.25})`;
        ctx.beginPath();
        ctx.arc(point.x, point.y, point.size * 0.75, 0, Math.PI * 2);
        ctx.fill();
      });
    }

    function updateShield(dt) {
      if (!state.running) return;

      if (state.holdingShield && state.shieldCharge > 0) {
        state.shieldActive = true;
        state.shieldCharge -= dt / 8 * state.shieldDrainFactor;
        if (state.shieldCharge <= 0) {
          state.shieldCharge = 0;
          state.shieldActive = false;
        }
      } else {
        state.shieldActive = false;
        state.shieldCharge += dt / 12 * state.shieldRegenFactor;
        state.shieldCharge = clamp(state.shieldCharge, 0, 1);
      }

      if (state.shieldActive) {
        state.status = '버티기 발동 중';
      } else if (state.status === '버티기 발동 중') {
        state.status = '우주 항해 중';
      }
    }

    function initAlarm() {
      if (alarmStarted || !window.AudioContext && !window.webkitAudioContext) return;
      const AudioCtx = window.AudioContext || window.webkitAudioContext;
      audioContext = new AudioCtx();
      alarmGain = audioContext.createGain();
      alarmGain.gain.value = 0;
      alarmGain.connect(audioContext.destination);

      alarmOsc = audioContext.createOscillator();
      alarmOsc.type = 'square';
      alarmOsc.frequency.value = 220;
      alarmOsc.connect(alarmGain);
      alarmOsc.start();

      alarmStarted = true;
    }

    function updateAlarm(danger) {
      if (!alarmStarted) return;
      const targetVolume = state.running && !state.shopOpen ? Math.pow(danger, 2) * 0.008 : 0;
      alarmGain.gain.setTargetAtTime(targetVolume, audioContext.currentTime, 0.008);
      const frequency = 180 + danger * 280;
      alarmOsc.frequency.setTargetAtTime(frequency, audioContext.currentTime, 0.008);
    }

    function updateShopUI() {
      shopMoney.textContent = state.money;
      shopList.innerHTML = '';
      shopItems.forEach(item => {
        const itemEl = document.createElement('div');
        itemEl.className = 'shop-item';
        itemEl.innerHTML = `
          <div class="shop-item__meta">
            <strong>${item.name}</strong><br />
            ${item.desc}
            <div>가격: ${item.cost}원</div>
          </div>
        `;
        const button = document.createElement('button');
        button.textContent = item.bought ? '구입됨' : '구입';
        button.disabled = item.bought || state.money < item.cost;
        button.addEventListener('click', () => {
          if (!item.bought && state.money >= item.cost) {
            state.money -= item.cost;
            item.bought = true;
            item.apply();
            updateHud();
            updateShopUI();
          }
        });
        itemEl.appendChild(button);
        shopList.appendChild(itemEl);
      });
    }

    function openShop() {
      if (state.shopOpen) return;
      state.wasRunning = state.running;
      state.running = false;
      state.shopOpen = true;
      shopOverlay.classList.add('open');
      updateShopUI();
      initAlarm();
      if (audioContext && audioContext.state === 'suspended') {
        audioContext.resume();
      }
    }

    function closeShop() {
      state.shopOpen = false;
      shopOverlay.classList.remove('open');
      if (state.wasRunning) {
        state.running = true;
      }
    }

    function handleInput(dt) {
      let ax = 0;
      let ay = 0;

      if (keys['ArrowLeft'] || keys['a'] || keys['A']) ax -= 420;
      if (keys['ArrowRight'] || keys['d'] || keys['D']) ax += 420;
      if (keys['ArrowUp'] || keys['w'] || keys['W']) ay -= 420;
      if (keys['ArrowDown'] || keys['s'] || keys['S']) ay += 420;

      player.vx += ax * dt;
      player.vy += ay * dt;

      player.vx *= 0.92;
      player.vy *= 0.92;

      player.vx = clamp(player.vx, -player.maxSpeed, player.maxSpeed);
      player.vy = clamp(player.vy, -player.maxSpeed, player.maxSpeed);

      player.x += player.vx;
      player.y += player.vy;

      if (player.x < 0) {
        player.x += canvas.width;
      } else if (player.x > canvas.width) {
        player.x -= canvas.width;
      }

      if (player.y < 0) {
        player.y += canvas.height;
      } else if (player.y > canvas.height) {
        player.y -= canvas.height;
      }
    }

    function updateDebris(dt) {
      state.debris = state.debris.filter(debris => debris.active);

      state.debris.forEach(debris => {
        const hole = blackHoles[debris.holeIndex];
        if (!hole) {
          debris.active = false;
          return;
        }

        debris.angle += debris.orbitSpeed * dt;
        debris.x = hole.x + Math.cos(debris.angle) * debris.orbitRadius;
        debris.y = hole.y + Math.sin(debris.angle) * debris.orbitRadius;

        const dx = debris.x - player.x;
        const dy = debris.y - player.y;
        const dist = Math.hypot(dx, dy);

        if (dist < debris.radius + player.radius + 2) {
          debris.active = false;
          state.points += debris.value;
          state.money = state.points * 10;
          state.collectedDebris += 1;
          state.status = '별 잔해 수집!';
          updateHud();
        }
      });
    }

    function updateObstacles(dt) {
      state.obstacles.forEach(obstacle => {
        obstacle.x += obstacle.driftX;
        obstacle.y += obstacle.driftY;
        obstacle.rotation += obstacle.rotationSpeed;

     if (
     obstacle.x < -obstacle.radius ||
     obstacle.y > canvas.height + obstacle.radius
     ) {
     if (Math.random() < 0.5) {
        obstacle.x = canvas.width + obstacle.radius;
        obstacle.y = Math.random() * canvas.height;
      } else {
        obstacle.x = Math.random() * canvas.width;
        obstacle.y = -obstacle.radius;
     }
     }

        const dx = obstacle.x - player.x;
        const dy = obstacle.y - player.y;
        const dist = Math.hypot(dx, dy);
        if (dist < obstacle.radius + player.radius + 2) {
          state.running = false;
          state.deathReason = 'meteor';
          state.status = '우주 장애물 충돌';
          updateHud();
        }
      });
    }

    function applyGravity(dt) {
      let gravityX = 0;
      let gravityY = 0;
      let danger = 0;

      blackHoles.forEach(hole => {
        hole.x += hole.driftX;
        hole.y += hole.driftY;
        if (hole.x < 50 || hole.x > canvas.width - 50) hole.driftX *= -1;
        if (hole.y < 50 || hole.y > canvas.height - 50) hole.driftY *= -1;

        const dx = hole.x - player.x;
        const dy = hole.y - player.y;
        const distSq = dx * dx + dy * dy;
        const dist = Math.sqrt(distSq) || 1;

        if (dist < hole.radius + player.radius + 6) {
          state.running = false;
          state.deathReason = 'blackhole';
          state.status = '블랙홀 포획';
          updateHud();
          return;
        }

        const influence = clamp(1 - dist / 260, 0, 1);
        danger = Math.max(danger, influence);

        if (dist < 260) {
          const pull = (3600 / (distSq + 1200)) * hole.mass * dt * 60 * state.gravityFactor;
          const effectivePull = state.shieldActive ? pull * 0.2 : pull;
          gravityX += (dx / dist) * effectivePull;
          gravityY += (dy / dist) * effectivePull;
        }
      });

      state.closestDanger = danger;
      player.vx += gravityX;
      player.vy += gravityY;
      const speed = Math.hypot(player.vx, player.vy);
      if (speed > player.maxSpeed) {
        player.vx = (player.vx / speed) * player.maxSpeed;
        player.vy = (player.vy / speed) * player.maxSpeed;
      }

      if (state.running) {
        if (state.shieldActive) {
          state.status = '버티기 발동 중';
        } else if (danger > 0.7) {
          state.status = '시간 왜곡 심함';
        } else if (danger > 0.35) {
          state.status = '시간 왜곡';
        } else if (state.status !== '별 잔해 수집!') {
          state.status = '우주 항해 중';
        }
      }
    }

    function drawBackground() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      const gradient = ctx.createRadialGradient(canvas.width / 2, canvas.height / 2, 40, canvas.width / 2, canvas.height / 2, canvas.width * 0.7);
      gradient.addColorStop(0, '#130716');
      gradient.addColorStop(0.3, '#06030b');
      gradient.addColorStop(0.7, '#020104');
      gradient.addColorStop(1, '#000000');
      ctx.fillStyle = gradient;
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      state.stars.forEach(star => {
        ctx.beginPath();
        ctx.arc(star.x, star.y, star.r, 0, Math.PI * 2);
        ctx.fillStyle = `rgba(255,255,255,${star.alpha})`;
        ctx.fill();
      });

      drawTrail();
    }

    function drawObstacles() {
      state.obstacles.forEach(obstacle => {
        ctx.save();
        ctx.translate(obstacle.x, obstacle.y);
        ctx.rotate(obstacle.rotation);
        ctx.fillStyle = '#9f7aea';
        ctx.beginPath();
        for (let i = 0; i < 8; i += 1) {
          const angle = i * (Math.PI * 2 / 8);
          const radii = i % 2 === 0 ? obstacle.radius : obstacle.radius * 0.6;
          const x = Math.cos(angle) * radii;
          const y = Math.sin(angle) * radii;
          if (i === 0) ctx.moveTo(x, y);
          else ctx.lineTo(x, y);
        }
        ctx.closePath();
        ctx.fill();
        ctx.fillStyle = 'rgba(255,255,255,0.65)';
        ctx.beginPath();
        ctx.arc(0, 0, obstacle.radius * 0.28, 0, Math.PI * 2);
        ctx.fill();
        ctx.restore();
      });
    }

    function drawDebris() {
      state.debris.forEach(debris => {
        if (!debris.active) return;
        ctx.save();
        ctx.translate(debris.x, debris.y);
        ctx.rotate(debris.angle);
        ctx.fillStyle = '#ffd38a';
        ctx.beginPath();
        ctx.moveTo(0, -debris.radius * 2.2);
        ctx.lineTo(debris.radius * 1.4, debris.radius * 1.4);
        ctx.lineTo(0, debris.radius * 1.8);
        ctx.lineTo(-debris.radius * 1.6, debris.radius * 0.8);
        ctx.closePath();
        ctx.fill();
        ctx.fillStyle = 'rgba(255,255,255,0.8)';
        ctx.beginPath();
        ctx.arc(0, 0, debris.radius * 0.45, 0, Math.PI * 2);
        ctx.fill();
        ctx.restore();
      });
    }

    function drawHole(hole) {
      ctx.save();
      const glow = ctx.createRadialGradient(hole.x, hole.y, 2, hole.x, hole.y, hole.radius * 1.7);
      glow.addColorStop(0, 'rgba(255,240,190,0.9)');
      glow.addColorStop(0.2, 'rgba(255,112,0,0.7)');
      glow.addColorStop(0.6, 'rgba(132,0,255,0.3)');
      glow.addColorStop(1, 'rgba(0,0,0,0)');
      ctx.fillStyle = glow;
      ctx.beginPath();
      ctx.arc(hole.x, hole.y, hole.radius * 1.7, 0, Math.PI * 2);
      ctx.fill();

      ctx.beginPath();
      ctx.arc(hole.x, hole.y, hole.radius, 0, Math.PI * 2);
      ctx.fillStyle = '#06070d';
      ctx.fill();

      ctx.beginPath();
      ctx.arc(hole.x, hole.y, hole.radius * 0.42, 0, Math.PI * 2);
      ctx.fillStyle = '#ffffff';
      ctx.fill();
      ctx.restore();
    }

    function drawPlayerAt(x, y) {
      ctx.save();
      ctx.translate(x, y);
      ctx.rotate(Math.atan2(player.vy, player.vx) + Math.PI / 2);

      // Main body
      const bodyGradient = ctx.createLinearGradient(0, -10, 0, 8);
      bodyGradient.addColorStop(0, '#ffffff');
      bodyGradient.addColorStop(0.45, '#f2f5ff');
      bodyGradient.addColorStop(1, '#d7dbe7');
      ctx.fillStyle = bodyGradient;
      ctx.beginPath();
      ctx.moveTo(0, -10);
      ctx.quadraticCurveTo(8, -3, 7, 7);
      ctx.quadraticCurveTo(4, 8, 0, 8);
      ctx.quadraticCurveTo(-4, 8, -7, 7);
      ctx.quadraticCurveTo(-8, -3, 0, -10);
      ctx.closePath();
      ctx.fill();

      // Body outline
      ctx.strokeStyle = 'rgba(255,255,255,0.9)';
      ctx.lineWidth = 1.2;
      ctx.stroke();

      // Cockpit glass
      ctx.fillStyle = 'rgba(25, 36, 70, 0.96)';
      ctx.beginPath();
      ctx.ellipse(0, -3, 4, 5.5, 0, 0, Math.PI * 2);
      ctx.fill();
      ctx.strokeStyle = 'rgba(186, 218, 255, 0.75)';
      ctx.lineWidth = 1.1;
      ctx.stroke();

      // Wing accents
      ctx.fillStyle = 'rgba(245, 247, 255, 0.95)';
      ctx.beginPath();
      ctx.moveTo(-10, 2);
      ctx.lineTo(-16, 8);
      ctx.lineTo(-10, 8);
      ctx.closePath();
      ctx.fill();
      ctx.beginPath();
      ctx.moveTo(10, 2);
      ctx.lineTo(16, 8);
      ctx.lineTo(10, 8);
      ctx.closePath();
      ctx.fill();

      // Rear engine glow
      const glowGradient = ctx.createRadialGradient(0, 13, 1, 0, 13, 8);
      glowGradient.addColorStop(0, 'rgba(255,255,255,0.95)');
      glowGradient.addColorStop(0.35, 'rgba(255,255,255,0.25)');
      glowGradient.addColorStop(1, 'rgba(255,255,255,0)');
      ctx.fillStyle = glowGradient;
      ctx.beginPath();
      ctx.arc(0, 16, 8, 0, Math.PI * 2);
      ctx.fill();

      ctx.restore();
    }

    function drawPlayer() {
      const nearLeft = player.x < player.radius * 2;
      const nearRight = player.x > canvas.width - player.radius * 2;
      const nearTop = player.y < player.radius * 2;
      const nearBottom = player.y > canvas.height - player.radius * 2;

      drawPlayerAt(player.x, player.y);
      if (nearLeft) drawPlayerAt(player.x + canvas.width, player.y);
      if (nearRight) drawPlayerAt(player.x - canvas.width, player.y);
      if (nearTop) drawPlayerAt(player.x, player.y + canvas.height);
      if (nearBottom) drawPlayerAt(player.x, player.y - canvas.height);
      if (nearLeft && nearTop) drawPlayerAt(player.x + canvas.width, player.y + canvas.height);
      if (nearLeft && nearBottom) drawPlayerAt(player.x + canvas.width, player.y - canvas.height);
      if (nearRight && nearTop) drawPlayerAt(player.x - canvas.width, player.y + canvas.height);
      if (nearRight && nearBottom) drawPlayerAt(player.x - canvas.width, player.y - canvas.height);
    }

    function drawOverlay() {
      if (state.running) return;
      ctx.save();
      ctx.fillStyle = 'rgba(0,0,0,0.62)';
      ctx.fillRect(0, 0, canvas.width, canvas.height);
      ctx.fillStyle = '#ffffff';
      ctx.font = 'bold 36px Segoe UI';
      ctx.textAlign = 'center';
      let message = '게임 오버';

if (state.deathReason === 'blackhole') {
    message = '우주선이 블랙홀에 빨려 들어갔습니다';
} else if (state.deathReason === 'meteor') {
    message = '자색 유성과 충돌했습니다';
}

ctx.fillText(message, canvas.width / 2, canvas.height / 2 - 20);
      ctx.font = '20px Segoe UI';
      ctx.fillStyle = '#c9d8ef';
      ctx.fillText('다시 시작하려면 버튼을 누르세요', canvas.width / 2, canvas.height / 2 + 20);
      ctx.restore();
    }

    function gameLoop(timestamp) {
      if (!lastTime) lastTime = timestamp;
      const dt = Math.min(0.032, (timestamp - lastTime) / 1000);
      lastTime = timestamp;

      if (state.running) {
        state.elapsed += dt;
        handleInput(dt);
        updateTrail(dt);
        updateShield(dt);
        updateDebris(dt);
        updateObstacles(dt);
        applyGravity(dt);
        updateAlarm(state.closestDanger);
        updateHud();
      }

      drawBackground();
      blackHoles.forEach(drawHole);
      drawObstacles();
      drawDebris();
      drawPlayer();
      drawOverlay();
      requestAnimationFrame(gameLoop);
    }

    window.addEventListener('keydown', (event) => {
      keys[event.key] = true;
      if (event.key === ' ' && !state.running) {
        resetGame();
      }
      if (['ArrowUp', 'ArrowDown', 'ArrowLeft', 'ArrowRight', ' ', 'w', 'a', 's', 'd', 'W', 'A', 'S', 'D'].includes(event.key)) {
        event.preventDefault();
      }
    });

    window.addEventListener('keyup', (event) => {
      keys[event.key] = false;
    });

    window.addEventListener('mousedown', (event) => {
      if (event.button === 0) {
        state.holdingShield = true;
      }
      if (!alarmStarted) {
        initAlarm();
        if (audioContext && audioContext.state === 'suspended') {
          audioContext.resume();
        }
      }
    });

    window.addEventListener('mouseup', (event) => {
      if (event.button === 0) {
        state.holdingShield = false;
      }
    });

    window.addEventListener('contextmenu', (event) => {
      event.preventDefault();
      if (state.running && !state.shopOpen) {
        openShop();
      }
    });

    closeShopBtn.addEventListener('click', () => {
      closeShop();
    });

    window.addEventListener('blur', () => {
      state.holdingShield = false;
    });

    musicToggle.addEventListener('click', () => {
    musicPlaying = !musicPlaying;

    if (musicPlaying) {
        bgm.play();
        musicToggle.textContent = 'BGM 중지';
       } else {
        bgm.pause();
        bgm.currentTime = 0;
        musicToggle.textContent = 'BGM 재생';
        }
       });
    restartBtn.addEventListener('click', resetGame);

    resetGame();
    requestAnimationFrame(gameLoop);
  </script>
</body>
</html>
