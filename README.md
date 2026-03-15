<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Geometry Dash - Optimized by Sewie</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Fredoka+One&display=swap');
        
        body {
            margin: 0; padding: 0; background-color: #111;
            display: flex; justify-content: center; align-items: center;
            height: 100vh; width: 100vw; overflow: hidden;
            font-family: 'Fredoka One', cursive; color: white; user-select: none;
        }

        #game-container {
            position: relative; width: 100vw; height: 100vh;
            background: linear-gradient(145deg, #1b1033 0%, #2d1b4a 100%); /* аниме-небо */
            overflow: hidden;
        }
        
        #bg-overlay {
            position: absolute; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.2); z-index: 1; pointer-events: none;
        }

        /* АНИМЕ-ТЕКСТ НА ФОНЕ */
        #anime-bg-text {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            font-family: 'Fredoka One', cursive;
            font-size: clamp(3rem, 15vw, 10rem);
            font-weight: 900;
            text-transform: uppercase;
            color: rgba(255, 230, 0, 0.25);
            text-shadow: 
                8px 8px 0 rgba(255, 0, 150, 0.4),
                -4px -4px 0 rgba(0, 255, 255, 0.4),
                12px 12px 20px black;
            transform: rotate(-5deg) scale(1.2);
            letter-spacing: 0.2em;
            line-height: 1.3;
            white-space: pre-line;
            text-align: center;
            z-index: 0;
            pointer-events: none;
            animation: subtleFloat 8s infinite alternate ease-in-out;
        }

        @keyframes subtleFloat {
            0% { transform: rotate(-5deg) scale(1.2) translateY(0); }
            100% { transform: rotate(-3deg) scale(1.25) translateY(-20px); }
        }

        /* Второй слой текста для эффекта глубины */
        #anime-bg-text::after {
            content: "БОАВЫЕ СПАЙБ　　ИХ!　ОЧКОМ СРАЗЬ!";
            position: absolute;
            top: 5px;
            left: 5px;
            width: 100%;
            height: 100%;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            color: rgba(255, 50, 100, 0.2);
            text-shadow: none;
            transform: rotate(3deg) scale(1.1);
            z-index: -1;
            opacity: 0.6;
        }

        canvas { position: absolute; top: 0; left: 0; z-index: 2; display: block; }
        
        .screen {
            position: absolute; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.8); backdrop-filter: blur(8px);
            display: flex; flex-direction: column; justify-content: center; align-items: center;
            z-index: 20; transition: opacity 0.3s ease;
        }
        .hidden { display: none !important; opacity: 0; pointer-events: none;}
        
        h1 { font-size: 7vw; margin: 0 0 10px 0; color: #ffeb3b; text-shadow: 0 5px 0 #f57f17; text-align: center;}
        h2 { font-size: 4vw; margin-bottom: 20px; color: #fff; text-shadow: 3px 3px 0 #000; text-transform: uppercase;}

        .btn {
            background: linear-gradient(to bottom, #4caf50, #2e7d32); border: 3px solid #fff; color: #fff;
            padding: 15px 40px; font-size: 2vw; font-family: inherit;
            cursor: pointer; margin: 10px; border-radius: 15px;
            box-shadow: 0 8px 0 #1b5e20; transition: 0.1s; text-transform: uppercase;
        }
        @media (max-width: 800px) { .btn { font-size: 4vw; padding: 10px 30px; } h1 { font-size: 12vw;} h2 { font-size: 8vw;} }
        
        .btn:active { transform: translateY(8px); box-shadow: 0 0px 0 #1b5e20; }
        .btn-red { background: linear-gradient(to bottom, #f44336, #c62828); box-shadow: 0 8px 0 #b71c1c; }
        .btn-red:active { box-shadow: 0 0px 0 #b71c1c; }
        .btn-blue { background: linear-gradient(to bottom, #2196f3, #1565c0); box-shadow: 0 8px 0 #0d47a1; }
        .btn-blue:active { box-shadow: 0 0px 0 #0d47a1; }
        .btn-purple { background: linear-gradient(to bottom, #9c27b0, #6a1b9a); box-shadow: 0 8px 0 #4a148c; }
        .btn-purple:active { box-shadow: 0 0px 0 #4a148c; }

        /* СПИСОК УРОВНЕЙ */
        .level-list { 
            display: flex; flex-direction: column; gap: 10px; 
            width: 80%; max-width: 800px; max-height: 50vh; 
            overflow-y: auto; padding-right: 15px; margin-bottom: 20px;
        }
        .level-row {
            display: flex; justify-content: space-between; align-items: center;
            background: rgba(0,0,0,0.6); padding: 15px 25px; border-radius: 15px;
            border: 3px solid #555; transition: 0.1s; cursor: pointer;
        }
        .level-row:hover { border-color: #ffeb3b; background: rgba(255,255,255,0.1); }
        .lvl-name { font-size: 24px; color: white; }
        .lvl-diff { font-size: 18px; color: #ffeb3b; }

        /* ГАРАЖ */
        .color-row, .icon-row { display: flex; gap: 10px; justify-content: center; flex-wrap: wrap; margin-bottom: 15px;}
        .color-btn { width: 40px; height: 40px; border: 3px solid #fff; cursor: pointer; border-radius: 8px; }
        .color-btn.active { border: 5px solid #ffeb3b; transform: scale(1.1); }
        .icon-btn { width: 50px; height: 50px; border: 3px solid #fff; cursor: pointer; border-radius: 10px; background: rgba(0,0,0,0.5); }
        .icon-btn.active { border-color: #ffeb3b; background: rgba(255,255,255,0.3); }

        /* РЕКЛАМА И АВТОР */
        #tg-ad {
            position: absolute; top: 0; left: 0; width: 100%; 
            background: linear-gradient(90deg, #0088cc, #00aaff);
            border-bottom: 4px solid #fff; text-align: center; padding: 10px 0;
            color: #fff; font-size: 2vw; cursor: pointer; z-index: 30;
            text-decoration: none; display: block;
        }
        #creator-tag {
            position: absolute; bottom: 10px; right: 20px;
            color: rgba(255,255,255,0.5); font-size: 20px; z-index: 30; pointer-events: none;
        }

        /* HUD */
        #hud { position: absolute; top: 60px; left: 20px; font-size: 24px; z-index: 5; text-shadow: 2px 2px 0 #000; }
        #pause-btn {
            position: absolute; top: 60px; right: 20px; z-index: 5;
            background: rgba(0,0,0,0.6); border: 3px solid #fff; color: #fff; font-size: 24px;
            padding: 5px 20px; border-radius: 10px; cursor: pointer;
        }
        #progress-container {
            position: absolute; top: 60px; left: 50%; transform: translateX(-50%);
            width: 50%; max-width: 500px; height: 25px; background: rgba(0,0,0,0.6); 
            border: 3px solid #fff; border-radius: 15px; z-index: 5; overflow: hidden;
            display: flex; align-items: center; justify-content: center;
        }
        #progress-fill { position: absolute; top: 0; left: 0; height: 100%; background: #4caf50; width: 0%; z-index: 1;}
        #progress-text { position: relative; z-index: 2; font-size: 18px; color: white; text-shadow: 2px 2px 2px black; }
    </style>
</head>
<body>

<div id="game-container">
    <div id="bg-overlay"></div>
    <!-- АНИМЕ-ФОН С ТЕКСТОМ ИЗ ФАЙЛА -->
    <div id="anime-bg-text">БОАВЫЕ<br>СПАЙБ<br><span style="font-size: 0.7em;">ИХ! ОЧКОМ СРАЗЬ!</span></div>
    
    <canvas id="gameCanvas"></canvas>
    
    <a id="tg-ad" href="https://t.me/Snokiworld" target="_blank">✈️ ПОДПИСЫВАЙСЯ НА ТГ КАНАЛ @Snokiworld ✈️</a>
    <div id="creator-tag">Владелец игры by: Sewie</div>

    <div id="hud" class="hidden">Попытка: <span id="hud-att">1</span></div>
    <button id="pause-btn" class="hidden" onclick="togglePause()">||</button>
    <div id="progress-container" class="hidden"><div id="progress-fill"></div><span id="progress-text">0%</span></div>

    <!-- МЕНЮ -->
    <div id="screen-main" class="screen">
        <h1>WEB DASH</h1>
        <div style="display: flex; flex-wrap: wrap; justify-content: center;">
            <button class="btn" onclick="showScreen('screen-levels')">ИГРАТЬ</button>
            <button class="btn btn-blue" onclick="showScreen('screen-garage'); drawIconsInGarage();">ГАРАЖ</button>
            <button class="btn btn-purple" onclick="showScreen('screen-settings')">НАСТРОЙКИ</button>
        </div>
    </div>

    <!-- НАСТРОЙКИ (ВЫБОР УСТРОЙСТВА) -->
    <div id="screen-settings" class="screen hidden">
        <h2>ВЫБОР ГРАФИКИ</h2>
        <p style="color:#ccc; font-size:18px; text-align:center;">Если игра лагает, выбери устройство послабее!</p>
        <button class="btn btn-blue" onclick="setGfx('PC')">🖥 PC (Ультра)</button>
        <button class="btn btn-purple" onclick="setGfx('Tablet')">📱 Планшет (Средне)</button>
        <button class="btn btn-red" onclick="setGfx('Phone')">🥔 Телефон (Картошка)</button>
        <button class="btn" style="margin-top:20px;" onclick="showScreen('screen-main')">НАЗАД</button>
    </div>

    <!-- УРОВНИ (25 ШТУК) -->
    <div id="screen-levels" class="screen hidden">
        <h2>ВЫБЕРИ УРОВЕНЬ</h2>
        <div class="level-list" id="level-list"></div>
        <button class="btn btn-red" onclick="showScreen('screen-main')">В МЕНЮ</button>
    </div>

    <!-- ГАРАЖ -->
    <div id="screen-garage" class="screen hidden">
        <h2>КАСТОМИЗАЦИЯ</h2>
        <div style="color:#ccc;">ИКОНКА</div><div class="icon-row" id="icon-list"></div>
        <div style="color:#ccc;">ЦВЕТ 1</div><div class="color-row" id="color1-list"></div>
        <div style="color:#ccc;">ЦВЕТ 2</div><div class="color-row" id="color2-list"></div>
        <button class="btn btn-blue" onclick="showScreen('screen-main')">СОХРАНИТЬ</button>
    </div>

    <!-- ПАУЗА / СМЕРТЬ -->
    <div id="screen-pause" class="screen hidden" style="background: rgba(0,0,0,0.8);">
        <h1>ПАУЗА</h1>
        <button class="btn btn-blue" onclick="togglePause()">ПРОДОЛЖИТЬ</button>
        <button class="btn btn-red" onclick="quitToMenu()">ВЫЙТИ</button>
    </div>

    <div id="screen-death" class="screen hidden">
        <h1 style="color: #f44336;">УНИЧТОЖЕН</h1>
        <p style="font-size: 30px;" id="death-percent">Прогресс: 0%</p>
        <button class="btn" onclick="restartLevel()">РЕСТАРТ</button>
        <button class="btn btn-red" onclick="quitToMenu()">МЕНЮ</button>
    </div>
</div>

<script>
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');
    const bgOverlay = document.getElementById('bg-overlay');

    // Настройки графики
    let gfxMode = 'PC'; // PC, Tablet, Phone
    function setGfx(mode) {
        gfxMode = mode;
        alert(`Установлена графика: ${mode}. Игра оптимизирована!`);
    }

    // Ресайз
    let groundY = 0;
    function resizeCanvas() {
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
        groundY = canvas.height - 100;
    }
    window.addEventListener('resize', resizeCanvas);
    resizeCanvas();

    // 25 ПРОХОДИМЫХ УРОВНЕЙ
    const levels = [];
    const diffs = ["Auto", "Easy", "Normal", "Hard", "Harder", "Insane", "Demon"];
    const colors = ['#0f2d66', '#370f66', '#660f0f', '#66570f', '#0f6651', '#1c4a9e', '#9e4a1c', '#1c9e4a', '#000000'];
    
    for(let i=1; i<=24; i++) {
        levels.push({
            id: i, 
            name: `Level ${i}`, 
            speed: 4.0 + (i * 0.15), // Скорость растет ОЧЕНЬ медленно
            length: 3000 + (i * 200),
            ground: colors[i % colors.length],
            diff: diffs[Math.floor(i/4)] || "Insane"
        });
    }
    levels.push({ id: 25, name: "CRAZY TIME EZ", speed: 9.0, length: 6000, ground: '#ff0055', diff: "EXTREME DEMON", isCrazy: true });

    // Паттерны препятствий
    const patterns = [
        [{t: 'spike', dx: 0, y: 0}],
        [{t: 'spike', dx: 0, y: 0}, {t: 'spike', dx: 40, y: 0}], 
        [{t: 'block', dx: 0, y: 0}], 
        [{t: 'block', dx: 0, y: 0}, {t: 'block', dx: 45, y: 0}, {t: 'block', dx: 90, y: 0}], 
        [{t: 'block', dx: 0, y: 0}, {t: 'spike', dx: 0, y: 40}], 
        [{t: 'block', dx: 0, y: 0}, {t: 'block', dx: 45, y: 45}] 
    ];

    let currentLevel = null;
    let frames = 0; let gameSpeed = 0; 
    let isPlaying = false; let isPaused = false;
    let animationId; let attempts = 1;

    // Игрок
    const gdColors = ['#ff0000', '#00ff00', '#00aaff', '#ffff00', '#00ffff', '#ff00ff', '#ff8800', '#ffffff', '#777', '#9c27b0'];
    let pColor1 = '#00ff00'; let pColor2 = '#00ffff'; let pIcon = 0;
    const player = { x: window.innerWidth * 0.15, y: 0, size: 40, dy: 0, grounded: false, rotation: 0, trail: [] };
    
    let entities = []; let groundScroll = 0; let nextSpawnDistance = 0;
    const crazyColors = ['rgba(255,0,0,0.5)', 'rgba(0,255,0,0.5)', 'rgba(0,0,255,0.5)', 'rgba(255,255,0,0.5)', 'rgba(255,0,255,0.5)'];
    let crazyIndex = 0;

    // ИНИЦИАЛИЗАЦИЯ
    function initUI() {
        const list = document.getElementById('level-list');
        levels.forEach(l => {
            let row = document.createElement('div');
            row.className = 'level-row';
            row.innerHTML = `<span class="lvl-name">${l.id}. ${l.name}</span> <span class="lvl-diff">${l.diff}</span>`;
            row.onclick = () => { attempts = 1; startLevel(l); };
            list.appendChild(row);
        });

        const setupColors = (containerId, isColor1) => {
            const container = document.getElementById(containerId);
            gdColors.forEach(c => {
                let btn = document.createElement('div');
                btn.className = 'color-btn ' + ((isColor1 ? pColor1 : pColor2) === c ? 'active' : '');
                btn.style.backgroundColor = c;
                btn.onclick = () => {
                    if (isColor1) pColor1 = c; else pColor2 = c;
                    container.querySelectorAll('.color-btn').forEach(b => b.classList.remove('active'));
                    btn.classList.add('active');
                    drawIconsInGarage();
                };
                container.appendChild(btn);
            });
        };
        setupColors('color1-list', true); setupColors('color2-list', false);

        const iconContainer = document.getElementById('icon-list');
        for(let i=0; i<4; i++) {
            let btn = document.createElement('canvas');
            btn.className = 'icon-btn ' + (pIcon === i ? 'active' : '');
            btn.width = 50; btn.height = 50;
            btn.onclick = () => {
                pIcon = i;
                iconContainer.querySelectorAll('.icon-btn').forEach(b => b.classList.remove('active'));
                btn.classList.add('active');
            };
            iconContainer.appendChild(btn);
        }
    }

    function drawCubeIcon(ctxTarget, id, size, c1, c2) {
        let half = size/2;
        ctxTarget.fillStyle = c1; ctxTarget.fillRect(-half, -half, size, size);
        ctxTarget.strokeStyle = '#000'; ctxTarget.lineWidth = 3; ctxTarget.strokeRect(-half, -half, size, size);

        ctxTarget.fillStyle = c2;
        if (id === 0) { 
            ctxTarget.fillRect(-half+8, -half+8, size-16, size-16);
            ctxTarget.fillStyle = c1; ctxTarget.fillRect(-half+14, -half+14, size-28, size-28);
        } else if (id === 1) { 
            ctxTarget.fillRect(-half+6, -half+12, size-12, 12);
            ctxTarget.fillStyle = '#fff'; ctxTarget.fillRect(-5, -half+14, 10, 8);
        } else if (id === 2) { 
            ctxTarget.fillRect(-half+8, -half+10, 8, 8); ctxTarget.fillRect(half-16, -half+10, 8, 8); 
            ctxTarget.fillRect(-half+8, half-14, size-16, 6); 
        } else if (id === 3) { 
            ctxTarget.fillRect(-5, -half+5, 10, size-10); ctxTarget.fillRect(-half+5, -5, size-10, 10);
        }
    }

    function drawIconsInGarage() {
        document.querySelectorAll('.icon-btn').forEach((c, index) => {
            let cx = c.getContext('2d');
            cx.clearRect(0,0,50,50);
            cx.save(); cx.translate(25, 25);
            drawCubeIcon(cx, index, 36, pColor1, pColor2); 
            cx.restore();
        });
    }

    // --- УПРАВЛЕНИЕ ЭКРАНАМИ И ИГРОЙ ---
    function showScreen(id) {
        document.querySelectorAll('.screen').forEach(s => s.classList.add('hidden'));
        document.getElementById('hud').classList.add('hidden');
        document.getElementById('pause-btn').classList.add('hidden');
        document.getElementById('progress-container').classList.add('hidden');
        document.getElementById('tg-ad').style.display = (id === 'screen-main') ? 'block' : 'none';
        document.getElementById('creator-tag').style.display = (id === 'screen-main') ? 'block' : 'none';
        
        document.getElementById(id).classList.remove('hidden');
        isPlaying = false; isPaused = false; cancelAnimationFrame(animationId);
    }

    function startLevel(levelData) { currentLevel = levelData; restartLevel(); }

    function restartLevel() {
        showScreen('screen-main'); 
        document.getElementById('screen-main').classList.add('hidden');
        document.getElementById('tg-ad').style.display = 'none';
        document.getElementById('creator-tag').style.display = 'none';
        
        document.getElementById('hud').classList.remove('hidden');
        document.getElementById('pause-btn').classList.remove('hidden');
        document.getElementById('progress-container').classList.remove('hidden');
        document.getElementById('hud-att').innerText = attempts;
        
        player.y = groundY - player.size; player.dy = 0; player.rotation = 0; player.grounded = true; player.trail = [];
        entities = []; frames = 0; gameSpeed = currentLevel.speed;
        
        bgOverlay.style.background = 'rgba(0,0,0,0.5)';
        nextSpawnDistance = window.innerWidth * 0.8; 
        
        isPlaying = true; isPaused = false; loop();
    }

    function quitToMenu() { showScreen('screen-levels'); }

    function togglePause() {
        if (!isPlaying && !isPaused) return; 
        isPaused = !isPaused;
        if (isPaused) { document.getElementById('screen-pause').classList.remove('hidden'); cancelAnimationFrame(animationId); } 
        else { document.getElementById('screen-pause').classList.add('hidden'); loop(); }
    }

    function jump() {
        if (!isPlaying || isPaused) return;
        if (player.grounded) { player.dy = -13.5; player.grounded = false; }
    }
    
    window.addEventListener('keydown', (e) => { if(e.code === 'Space' || e.code === 'ArrowUp') jump(); if(e.code === 'Escape') togglePause(); });
    window.addEventListener('mousedown', (e) => { if(e.target.tagName !== 'BUTTON' && e.target.id !== 'tg-ad') jump(); });
    window.addEventListener('touchstart', (e) => { if(e.target.tagName !== 'BUTTON' && e.target.id !== 'tg-ad') jump(); });

    // ГЕНЕРАТОР (ОПТИМИЗИРОВАН И ОБЛЕГЧЕН)
    function spawnPattern() {
        nextSpawnDistance -= gameSpeed;
        if (nextSpawnDistance <= 0) {
            let pat = patterns[Math.floor(Math.random() * patterns.length)];
            let startX = canvas.width + 100;
            let maxDx = 0;

            pat.forEach(obj => {
                let eY = groundY - obj.y;
                if(obj.t === 'block' && obj.y === 0) eY = groundY - 40; 
                if(obj.t === 'block' && obj.y > 0) eY = groundY - 40 - obj.y;

                entities.push({x: startX + obj.dx, y: eY, w: 40, h: 40, t: obj.t});
                if (obj.dx > maxDx) maxDx = obj.dx;
            });
            // ОГРОМНЫЕ РАССТОЯНИЯ ДЛЯ ПРОХОДИМОСТИ!
            nextSpawnDistance = maxDx + 600 + (gameSpeed * 50) + Math.random() * 400; 
        }
    }

    function die() {
        isPlaying = false; attempts++;
        let percent = Math.floor((frames / currentLevel.length) * 100);
        document.getElementById('death-percent').innerText = `Прогресс: ${percent}%`;
        document.getElementById('screen-death').classList.remove('hidden');
        document.getElementById('pause-btn').classList.add('hidden');
    }

    // ОТРИСОВКА (С УЧЕТОМ НАСТРОЕК ГРАФИКИ)
    function drawGame() {
        // Если телефон, не чистим прозрачностью, а просто заливаем
        if(gfxMode === 'Phone') {
            ctx.fillStyle = currentLevel.ground;
            ctx.fillRect(0,0, canvas.width, canvas.height); // заливаем все цветом земли для скорости
        } else {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
        }

        // CRAZY TIME
        if (currentLevel.isCrazy && frames % 20 === 0 && gfxMode !== 'Phone') {
            crazyIndex = (crazyIndex + 1) % crazyColors.length;
            bgOverlay.style.background = crazyColors[crazyIndex];
        }

        groundScroll -= gameSpeed;
        if(groundScroll <= -80) groundScroll = 0;
        
        ctx.fillStyle = currentLevel.ground;
        if (gfxMode === 'PC') ctx.globalAlpha = 0.9;
        ctx.fillRect(0, groundY, canvas.width, canvas.height - groundY);
        ctx.globalAlpha = 1.0;
        
        // Полосы на земле (отключаем на картошках)
        if (gfxMode !== 'Phone') {
            ctx.fillStyle = 'rgba(0,0,0,0.5)';
            for(let i=0; i<canvas.width/80 + 2; i++) {
                ctx.fillRect(groundScroll + i*80, groundY, 40, canvas.height - groundY);
            }
        }
        
        ctx.strokeStyle = '#fff'; ctx.lineWidth = 4;
        ctx.beginPath(); ctx.moveTo(0, groundY); ctx.lineTo(canvas.width, groundY); ctx.stroke();

        // Препятствия
        for (let i = 0; i < entities.length; i++) {
            let e = entities[i];
            if (e.t === 'block') {
                ctx.fillStyle = '#000'; ctx.fillRect(e.x, e.y, e.w, e.h);
                ctx.strokeStyle = '#fff'; ctx.lineWidth = 3; ctx.strokeRect(e.x, e.y, e.w, e.h);
            } else if (e.t === 'spike') {
                ctx.fillStyle = '#000';
                ctx.beginPath(); ctx.moveTo(e.x + e.w/2, e.y - e.h); ctx.lineTo(e.x + e.w, e.y); ctx.lineTo(e.x, e.y); ctx.fill();

                ctx.fillStyle = currentLevel.isCrazy ? '#fff' : pColor1; 
                ctx.beginPath(); ctx.moveTo(e.x + e.w/2, e.y - e.h + 8); ctx.lineTo(e.x + e.w - 6, e.y - 3); ctx.lineTo(e.x + 6, e.y - 3); ctx.fill();
            }
        }

        if (isPlaying) {
            // Шлейф (Только на PC и Планшете)
            if (gfxMode === 'PC' || gfxMode === 'Tablet') {
                let trailLength = gfxMode === 'PC' ? player.trail.length : Math.min(3, player.trail.length);
                for(let i=0; i<trailLength; i++) {
                    let t = player.trail[i];
                    ctx.save(); ctx.translate(t.x + player.size/2, t.y + player.size/2); ctx.rotate(t.r);
                    ctx.globalAlpha = (i + 1) / trailLength * 0.3;
                    drawCubeIcon(ctx, pIcon, player.size, pColor1, pColor2);
                    ctx.restore();
                }
                ctx.globalAlpha = 1.0;
            }
            
            // Игрок
            ctx.save(); ctx.translate(player.x + player.size/2, player.y + player.size/2); ctx.rotate(player.rotation);
            drawCubeIcon(ctx, pIcon, player.size, pColor1, pColor2);
            ctx.restore();
        }
    }

    function loop() {
        if (!isPlaying || isPaused) return;

        player.dy += 0.75; 
        player.y += player.dy;

        if (player.y + player.size >= groundY) {
            player.y = groundY - player.size; player.dy = 0; player.grounded = true;
            player.rotation = Math.round(player.rotation / (Math.PI/2)) * (Math.PI/2);
        } else {
            player.rotation += 0.12;
        }

        // Запись истории шлейфа
        if (gfxMode !== 'Phone' && frames % 2 === 0) {
            player.trail.push({x: player.x, y: player.y, r: player.rotation});
            if (player.trail.length > 6) player.trail.shift();
        }

        spawnPattern();

        // ХИТБОКСЫ: Прощают целых 10 пикселей! Играть очень легко.
        let crashed = false;
        const tol = 10; 

        for (let i = 0; i < entities.length; i++) {
            let e = entities[i];
            e.x -= gameSpeed;

            let ex = e.x; let ey = e.t === 'spike' ? e.y - e.h : e.y;

            if (player.x + tol < ex + e.w && player.x + player.size - tol > ex &&
                player.y + tol < ey + e.h && player.y + player.size - tol > ey) {
                
                if (e.t === 'spike') { crashed = true; break; }
                else if (e.t === 'block') {
                    if (player.dy > 0 && player.y + player.size - player.dy <= e.y + 20) {
                        player.y = e.y - player.size; player.dy = 0; player.grounded = true;
                        player.rotation = Math.round(player.rotation/(Math.PI/2))*(Math.PI/2);
                    } else { crashed = true; break; }
                }
            }
            if (e.x + e.w < -100) { entities.splice(i, 1); i--; }
        }

        if (crashed) { die(); return; }

        drawGame();

        frames++;
        let percent = Math.floor((frames / currentLevel.length) * 100);
        if (percent > 100) percent = 100;
        
        document.getElementById('progress-fill').style.width = percent + '%';
        document.getElementById('progress-text').innerText = percent + '%';

        if (frames >= currentLevel.length) { 
            isPlaying = false;
            document.getElementById('hud').classList.add('hidden');
            document.getElementById('pause-btn').classList.add('hidden');
            document.getElementById('progress-container').classList.add('hidden');
            document.getElementById('screen-main').classList.remove('hidden');
            document.querySelector('#screen-main h1').innerText = "ПРОЙДЕНО!";
            document.querySelector('#screen-main h1').style.color = "#4caf50";
            document.getElementById('tg-ad').style.display = 'block';
            document.getElementById('creator-tag').style.display = 'block';
            return; 
        }

        animationId = requestAnimationFrame(loop);
    }

    initUI();
    drawIconsInGarage();
</script>
</body>
</html>
