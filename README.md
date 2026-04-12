<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>MusicFlow | Яркий музыкальный плеер</title>
    <!-- YouTube IFrame API -->
    <script src="https://www.youtube.com/iframe_api"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', 'Inter', system-ui, -apple-system, 'Roboto', sans-serif;
            min-height: 100vh;
            padding: 20px;
            transition: all 0.5s cubic-bezier(0.4, 0, 0.2, 1);
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            background-size: 400% 400%;
            animation: gradientShift 15s ease infinite;
            position: relative;
        }

        @keyframes gradientShift {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }

        /* Анимированные частицы фона */
        body::before {
            content: '';
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: url('data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100"><circle cx="10" cy="20" r="2" fill="rgba(255,255,255,0.1)"/><circle cx="30" cy="70" r="1.5" fill="rgba(255,255,255,0.08)"/><circle cx="60" cy="15" r="2.5" fill="rgba(255,255,255,0.12)"/><circle cx="85" cy="45" r="1.8" fill="rgba(255,255,255,0.1)"/><circle cx="45" cy="88" r="2" fill="rgba(255,255,255,0.08)"/><circle cx="75" cy="90" r="1.2" fill="rgba(255,255,255,0.1)"/></svg>');
            background-repeat: repeat;
            pointer-events: none;
            animation: float 20s linear infinite;
            opacity: 0.5;
        }

        @keyframes float {
            0% { transform: translateY(0px); }
            100% { transform: translateY(-100px); }
        }

        /* главный layout: колонки */
        .dashboard {
            max-width: 1400px;
            margin: 0 auto;
            display: flex;
            flex-wrap: wrap;
            gap: 28px;
            position: relative;
            z-index: 1;
        }

        /* Левая колонка - плеер */
        .player-col {
            flex: 2;
            min-width: 280px;
            animation: slideInLeft 0.6s ease-out;
        }

        /* Правая колонка - поиск и результаты */
        .search-col {
            flex: 1.2;
            min-width: 280px;
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(20px);
            border-radius: 48px;
            padding: 22px 20px;
            border: 1px solid rgba(255, 255, 255, 0.3);
            box-shadow: 0 25px 45px rgba(0, 0, 0, 0.2), 0 0 20px rgba(255, 255, 255, 0.1);
            height: fit-content;
            transition: all 0.3s;
            animation: slideInRight 0.6s ease-out;
        }

        .search-col:hover {
            transform: translateY(-5px);
            box-shadow: 0 30px 55px rgba(0, 0, 0, 0.3), 0 0 30px rgba(255, 255, 255, 0.2);
        }

        @keyframes slideInLeft {
            from {
                opacity: 0;
                transform: translateX(-50px);
            }
            to {
                opacity: 1;
                transform: translateX(0);
            }
        }

        @keyframes slideInRight {
            from {
                opacity: 0;
                transform: translateX(50px);
            }
            to {
                opacity: 1;
                transform: translateX(0);
            }
        }

        /* карточка плеера */
        .player-container {
            background: rgba(255, 255, 255, 0.12);
            backdrop-filter: blur(20px);
            border-radius: 56px;
            box-shadow: 0 25px 45px rgba(0, 0, 0, 0.2), 0 0 0 1px rgba(255, 255, 255, 0.2);
            overflow: hidden;
            padding: 24px 26px 32px;
            transition: transform 0.3s, box-shadow 0.3s;
        }

        .player-container:hover {
            transform: translateY(-5px);
            box-shadow: 0 30px 55px rgba(0, 0, 0, 0.3), 0 0 0 1px rgba(255, 255, 255, 0.3);
        }

        h1 {
            font-size: 2.2rem;
            font-weight: 700;
            background: linear-gradient(135deg, #fff, #ffd89b, #c7e9fb);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            letter-spacing: -0.5px;
            display: inline-block;
            margin-bottom: 8px;
            animation: titleGlow 3s ease-in-out infinite;
        }

        @keyframes titleGlow {
            0%, 100% { filter: drop-shadow(0 0 5px rgba(255,255,255,0.3)); }
            50% { filter: drop-shadow(0 0 20px rgba(255,216,155,0.6)); }
        }

        .sub {
            color: rgba(255, 255, 255, 0.8);
            font-size: 0.85rem;
            margin-bottom: 28px;
            border-left: 3px solid #ffd89b;
            padding-left: 12px;
        }

        /* блок вставки ссылки */
        .input-group {
            background: rgba(255, 255, 255, 0.15);
            border-radius: 80px;
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 5px 5px 5px 22px;
            margin-bottom: 28px;
            border: 1px solid rgba(255, 255, 255, 0.3);
            transition: all 0.3s;
        }
        .input-group:focus-within {
            border-color: #ffd89b;
            box-shadow: 0 0 0 3px rgba(255, 216, 155, 0.3);
            background: rgba(255, 255, 255, 0.25);
        }
        .input-group input {
            flex: 1;
            background: transparent;
            border: none;
            padding: 14px 0;
            font-size: 1rem;
            color: white;
            outline: none;
            font-weight: 500;
        }
        .input-group input::placeholder {
            color: rgba(255, 255, 255, 0.6);
            font-weight: 400;
        }
        .input-group button {
            background: linear-gradient(135deg, #ffd89b, #c7e9fb);
            border: none;
            padding: 10px 24px;
            border-radius: 60px;
            color: #333;
            font-weight: 700;
            font-size: 0.9rem;
            cursor: pointer;
            transition: all 0.3s;
            box-shadow: 0 4px 15px rgba(0,0,0,0.2);
            display: flex;
            align-items: center;
            gap: 6px;
        }
        .input-group button:hover {
            transform: scale(1.05);
            box-shadow: 0 6px 20px rgba(0,0,0,0.3);
            background: linear-gradient(135deg, #ffe6b3, #d4f0ff);
        }

        .track-info {
            background: rgba(0, 0, 0, 0.3);
            border-radius: 36px;
            padding: 16px 22px;
            margin-bottom: 24px;
            border: 1px solid rgba(255, 255, 255, 0.2);
            backdrop-filter: blur(10px);
        }
        .track-title {
            font-size: 1.3rem;
            font-weight: 600;
            color: white;
            word-break: break-word;
            display: flex;
            align-items: center;
            gap: 8px;
            flex-wrap: wrap;
        }
        .track-title span:first-child {
            background: linear-gradient(135deg, #ffd89b, #c7e9fb);
            padding: 4px 12px;
            border-radius: 40px;
            font-size: 0.75rem;
            font-weight: 600;
            color: #333;
        }
        .track-name {
            color: #ffd89b;
            text-shadow: 0 0 10px rgba(255, 216, 155, 0.5);
        }
        .track-meta {
            font-size: 0.8rem;
            color: rgba(255, 255, 255, 0.7);
            margin-top: 8px;
            display: flex;
            gap: 20px;
            flex-wrap: wrap;
        }

        .youtube-player-box {
            position: relative;
            width: 100%;
            background: #00000050;
            border-radius: 32px;
            overflow: hidden;
            margin-bottom: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
            transition: transform 0.3s;
        }
        .youtube-player-box:hover {
            transform: scale(1.02);
        }
        #youtube-player {
            width: 100%;
            aspect-ratio: 16 / 9;
            pointer-events: auto;
            border-radius: 24px;
        }
        
        .controls {
            display: flex;
            align-items: center;
            justify-content: space-between;
            gap: 12px;
            margin-top: 12px;
            flex-wrap: wrap;
        }
        .playback-buttons {
            display: flex;
            gap: 18px;
            background: rgba(0, 0, 0, 0.4);
            padding: 8px 18px;
            border-radius: 60px;
            backdrop-filter: blur(10px);
        }
        .ctrl-btn {
            background: transparent;
            border: none;
            color: white;
            font-size: 1.9rem;
            cursor: pointer;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            transition: all 0.2s;
            width: 48px;
            height: 48px;
            border-radius: 60px;
            filter: drop-shadow(0 2px 5px rgba(0,0,0,0.3));
        }
        .ctrl-btn:hover {
            background: rgba(255, 216, 155, 0.3);
            transform: scale(1.1) rotate(5deg);
            color: #ffd89b;
        }
        .volume-slider {
            flex: 1;
            min-width: 120px;
            background: rgba(0, 0, 0, 0.4);
            padding: 5px 15px;
            border-radius: 40px;
            display: flex;
            align-items: center;
            gap: 12px;
            backdrop-filter: blur(10px);
        }
        .volume-slider span {
            font-size: 1.2rem;
            color: #ffd89b;
        }
        input[type="range"] {
            flex: 1;
            height: 4px;
            -webkit-appearance: none;
            background: rgba(255, 255, 255, 0.3);
            border-radius: 10px;
            outline: none;
        }
        input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 14px;
            height: 14px;
            background: #ffd89b;
            border-radius: 50%;
            cursor: pointer;
            box-shadow: 0 0 10px #ffd89b;
        }
        .status-msg {
            font-size: 0.75rem;
            color: rgba(255, 255, 255, 0.7);
            text-align: center;
            margin-top: 20px;
            padding-top: 12px;
            border-top: 1px solid rgba(255, 255, 255, 0.2);
        }
        /* стили поиска */
        .search-header {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
            flex-wrap: wrap;
        }
        .search-header input {
            flex: 1;
            background: rgba(0, 0, 0, 0.4);
            border: 1px solid rgba(255, 255, 255, 0.3);
            padding: 12px 18px;
            border-radius: 60px;
            color: white;
            font-size: 0.9rem;
            outline: none;
            transition: all 0.3s;
        }
        .search-header input:focus {
            border-color: #ffd89b;
            box-shadow: 0 0 15px rgba(255, 216, 155, 0.3);
        }
        .search-header button {
            background: linear-gradient(135deg, #ffd89b, #c7e9fb);
            border: none;
            padding: 0 25px;
            border-radius: 60px;
            color: #333;
            font-weight: 700;
            cursor: pointer;
            transition: all 0.3s;
        }
        .search-header button:hover {
            transform: scale(1.05);
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
        }
        .results-list {
            max-height: 460px;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            gap: 12px;
            padding-right: 6px;
        }
        .result-item {
            background: rgba(0, 0, 0, 0.4);
            border-radius: 28px;
            padding: 12px 16px;
            cursor: pointer;
            transition: all 0.3s;
            border: 1px solid rgba(255, 255, 255, 0.2);
            display: flex;
            align-items: center;
            gap: 14px;
            backdrop-filter: blur(10px);
        }
        .result-item:hover {
            background: rgba(255, 216, 155, 0.2);
            transform: translateX(5px) scale(1.02);
            border-color: #ffd89b;
            box-shadow: 0 5px 20px rgba(0,0,0,0.3);
        }
        .result-thumb {
            width: 48px;
            height: 48px;
            border-radius: 16px;
            background: #00000055;
            object-fit: cover;
            transition: transform 0.3s;
        }
        .result-item:hover .result-thumb {
            transform: scale(1.1);
        }
        .result-info {
            flex: 1;
        }
        .result-title {
            font-weight: 600;
            color: white;
            font-size: 0.85rem;
            display: -webkit-box;
            -webkit-line-clamp: 2;
            -webkit-box-orient: vertical;
            overflow: hidden;
        }
        .result-channel {
            font-size: 0.7rem;
            color: #ffd89b;
        }
        .search-suggest {
            font-size: 0.7rem;
            color: rgba(255, 255, 255, 0.6);
            margin-top: 16px;
            text-align: center;
        }
        footer {
            font-size: 0.7rem;
            text-align: center;
            margin-top: 18px;
            color: rgba(255, 255, 255, 0.6);
        }
        .block-warning {
            background: rgba(220, 38, 38, 0.2);
            border: 1px solid #ff6b6b;
            border-radius: 12px;
            padding: 10px;
            margin-top: 12px;
            text-align: center;
            font-size: 0.7rem;
            color: #ffb3b3;
        }
        ::-webkit-scrollbar {
            width: 5px;
        }
        ::-webkit-scrollbar-track {
            background: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb {
            background: #ffd89b;
            border-radius: 10px;
        }
        @media (max-width: 800px) {
            .dashboard {
                flex-direction: column;
            }
            .player-container {
                padding: 18px;
            }
        }

        /* Анимация загрузки */
        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.5; }
        }
        .loading {
            animation: pulse 1.5s ease-in-out infinite;
        }
    </style>
</head>
<body>

<div class="dashboard">
    <!-- ЛЕВАЯ КОЛОНКА: ПЛЕЕР -->
    <div class="player-col">
        <div class="player-container">
            <h1>🎵 MusicFlow</h1>
            <div class="sub">✨ Яркий музыкальный мир · YouTube API · Магия звука ✨</div>

            <div class="input-group">
                <input type="text" id="youtube-url" placeholder="🔗 Вставь ссылку или ID видео">
                <button id="load-btn">▶ Загрузить</button>
            </div>

            <div class="track-info">
                <div class="track-title">
                    <span>🎧 Сейчас играет</span>
                    <span class="track-name" id="track-name">—</span>
                </div>
                <div class="track-meta">
                    <span id="video-id-status">⏳ ID не загружен</span>
                    <span id="duration-status">🕒 —</span>
                </div>
            </div>

            <div class="youtube-player-box">
                <div id="youtube-player"></div>
            </div>

            <div class="controls">
                <div class="playback-buttons">
                    <button class="ctrl-btn" id="play-btn" title="Воспроизвести">▶️</button>
                    <button class="ctrl-btn" id="pause-btn" title="Пауза">⏸️</button>
                    <button class="ctrl-btn" id="stop-reset-btn" title="Стоп / сброс">⏹️</button>
                </div>
                <div class="volume-slider">
                    <span>🔊</span>
                    <input type="range" id="volume-control" min="0" max="100" value="70">
                </div>
            </div>
            <div class="status-msg" id="status-msg">
                ✨ Вставь ссылку или найди песню справа через YouTube API
            </div>
            <div class="block-warning" id="block-warning" style="display: none;">
                🚫 Доступ к YouTube ограничен в соответствии с законодательством РФ
            </div>
            <footer>
                💡 Работает с любыми YouTube ссылками / ID
            </footer>
        </div>
    </div>

    <!-- ПРАВАЯ КОЛОНКА: ПОИСК ЧЕРЕЗ YOUTUBE DATA API -->
    <div class="search-col">
        <div style="font-weight: 700; margin-bottom: 12px; color:#ffd89b; font-size: 1.1rem;">🔍 Поиск песен (YouTube API)</div>
        
        <div class="search-header">
            <input type="text" id="search-input" placeholder="🎤 Найти песню или исполнителя..." value="chill vibes">
            <button id="search-btn">🔎 Найти</button>
        </div>
        <div class="results-list" id="results-list">
            <div style="text-align:center; padding:20px; color:rgba(255,255,255,0.7);">✨ Введи запрос и нажми «Найти»</div>
        </div>
        <div class="search-suggest">
            💡 Популярные запросы: «металл», «рэп 2025», «инструментал», «джаз», «phonk»
        </div>
    </div>
</div>

<script>
    // ======================== ЗАПРЕТ ОБХОДА YouTube ========================
    (function blockProxyBypass() {
        const originalFetch = window.fetch;
        Object.defineProperty(window, 'fetch', {
            get: function() { return originalFetch; },
            set: function() { showBlockWarning(); }
        });
        
        const originalWebSocket = window.WebSocket;
        window.WebSocket = function(...args) {
            const url = args[0];
            if (url && (url.includes('proxy') || url.includes('cors') || url.includes('bypass'))) {
                showBlockWarning();
                return null;
            }
            return new originalWebSocket(...args);
        };
        
        const originalXHROpen = XMLHttpRequest.prototype.open;
        XMLHttpRequest.prototype.open = function(method, url, ...rest) {
            if (url && typeof url === 'string') {
                const lowerUrl = url.toLowerCase();
                const blockedPatterns = ['cors-anywhere', 'corsproxy', 'bypass', 'proxy', 'allorigins', 'cors-unblock'];
                if (blockedPatterns.some(pattern => lowerUrl.includes(pattern))) {
                    showBlockWarning();
                    throw new Error('Доступ к прокси запрещен');
                }
            }
            return originalXHROpen.call(this, method, url, ...rest);
        };
        
        function showBlockWarning() {
            const warningEl = document.getElementById('block-warning');
            if (warningEl) {
                warningEl.style.display = 'block';
                setTimeout(() => {
                    if (warningEl) warningEl.style.display = 'none';
                }, 5000);
            }
        }
    })();
    
    // ======================== ПЛЕЕР ========================
    let player = null;
    let playerReady = false;
    let currentVideoId = null;
    
    const urlInput = document.getElementById('youtube-url');
    const loadBtn = document.getElementById('load-btn');
    const playBtn = document.getElementById('play-btn');
    const pauseBtn = document.getElementById('pause-btn');
    const stopResetBtn = document.getElementById('stop-reset-btn');
    const volumeSlider = document.getElementById('volume-control');
    const trackNameSpan = document.getElementById('track-name');
    const videoIdStatusSpan = document.getElementById('video-id-status');
    const durationStatusSpan = document.getElementById('duration-status');
    const statusMsgDiv = document.getElementById('status-msg');
    
    function setStatusMessage(text, isError = false) {
        statusMsgDiv.innerHTML = isError ? `⚠️ ${text}` : `✨ ${text}`;
        if (isError) {
            statusMsgDiv.style.color = "#ffb3b3";
            setTimeout(() => { if(statusMsgDiv.innerHTML.includes("⚠️")) statusMsgDiv.style.color = "rgba(255,255,255,0.7)"; }, 3000);
        } else {
            statusMsgDiv.style.color = "rgba(255,255,255,0.7)";
        }
    }
    
    function extractVideoId(url) {
        if (!url || typeof url !== 'string') return null;
        url = url.trim();
        if (/^[a-zA-Z0-9_-]{11}$/.test(url)) return url;
        let regExp = /^.*(youtu.be\/|v\/|u\/\w\/|embed\/|watch\?v=|\&v=)([^#\&\?]*).*/;
        let match = url.match(regExp);
        if (match && match[2] && match[2].length === 11) return match[2];
        const shortsRegex = /youtube\.com\/shorts\/([a-zA-Z0-9_-]{11})/;
        let shortsMatch = url.match(shortsRegex);
        if (shortsMatch && shortsMatch[1]) return shortsMatch[1];
        let anyIdRegex = /([a-zA-Z0-9_-]{11})/;
        let fallback = url.match(anyIdRegex);
        if (fallback && fallback[1] && fallback[1].length === 11) return fallback[1];
        return null;
    }
    
    async function updateTrackMetadata(videoId) {
        if (!videoId) {
            trackNameSpan.innerText = "—";
            videoIdStatusSpan.innerText = "⏳ ID не загружен";
            durationStatusSpan.innerText = "🕒 —";
            return;
        }
        videoIdStatusSpan.innerText = `🎬 ID: ${videoId}`;
        try {
            let res = await fetch(`https://www.youtube.com/oembed?url=https://www.youtube.com/watch?v=${videoId}&format=json`);
            if (res.ok) {
                let data = await res.json();
                trackNameSpan.innerText = data.title ? (data.title.length > 55 ? data.title.slice(0,52)+'...' : data.title) : `Видео ${videoId}`;
            } else {
                trackNameSpan.innerText = `Трек (${videoId})`;
            }
        } catch(e) {
            trackNameSpan.innerText = `Видео ${videoId}`;
        }
        if (player && playerReady && player.getDuration) {
            let dur = player.getDuration();
            if (dur && isFinite(dur) && dur > 0) {
                let mins = Math.floor(dur / 60);
                let secs = Math.floor(dur % 60);
                durationStatusSpan.innerText = `🕒 ${mins}:${secs.toString().padStart(2,'0')}`;
            }
        }
    }
    
    function loadVideoById(videoId, startPlaying = true) {
        if (!videoId) { setStatusMessage("Неверный ID", true); return false; }
        if (!player || !playerReady) { window.pendingVideoId = videoId; setStatusMessage("Плеер инициализируется...", false); return false; }
        if (currentVideoId === videoId && startPlaying) {
            player.playVideo();
            setStatusMessage("Продолжаем слушать");
            return true;
        }
        setStatusMessage(`🎵 Загружаем трек...`);
        if (startPlaying) player.loadVideoById(videoId);
        else player.cueVideoById(videoId);
        currentVideoId = videoId;
        updateTrackMetadata(videoId);
        setTimeout(() => updateTrackMetadata(videoId), 1200);
        return true;
    }
    
    function playVideo() { if(playerReady && currentVideoId) player.playVideo(); else setStatusMessage("Нет трека или плеер не готов", true); }
    function pauseVideo() { if(playerReady) player.pauseVideo(); }
    function stopAndReset() { if(playerReady && currentVideoId) player.stopVideo(); }
    function setVolume(val) { if(playerReady) player.setVolume(Math.min(100, Math.max(0, val))); }
    
    // ======================== ПОИСК С ПРЕДУСТАНОВЛЕННЫМ API КЛЮЧОМ ========================
    // API ключ по умолчанию
    const DEFAULT_API_KEY = 'AIzaSyB-CXxXs0lj-5IkBBJswxncefw-v3UTfB8';
    let currentApiKey = localStorage.getItem('youtube_api_key') || DEFAULT_API_KEY;
    
    const searchInput = document.getElementById('search-input');
    const searchBtn = document.getElementById('search-btn');
    const resultsListDiv = document.getElementById('results-list');
    
    // Сохраняем ключ если его нет в localStorage
    if (!localStorage.getItem('youtube_api_key')) {
        localStorage.setItem('youtube_api_key', DEFAULT_API_KEY);
    }
    
    async function searchYouTubeVideos(query, maxResults = 14) {
        if (!currentApiKey) {
            resultsListDiv.innerHTML = '<div style="text-align:center; padding:20px; color:#ffb79e;">🔑 Ошибка: API ключ не найден</div>';
            return [];
        }
        if (!query.trim()) return [];
        
        try {
            const url = `https://www.googleapis.com/youtube/v3/search?part=snippet&maxResults=${maxResults}&q=${encodeURIComponent(query)}&type=video&key=${currentApiKey}`;
            const response = await fetch(url);
            const data = await response.json();
            
            if (data.error) {
                let errorMsg = data.error.message || 'Ошибка API';
                if (data.error.code === 403) errorMsg = 'Ключ API недействителен или квота исчерпана';
                resultsListDiv.innerHTML = `<div style="text-align:center; padding:20px; color:#ffb79e;">❌ ${errorMsg}</div>`;
                return [];
            }
            if (!data.items || data.items.length === 0) return [];
            return data.items.map(item => ({
                id: item.id.videoId,
                title: item.snippet.title,
                channel: item.snippet.channelTitle,
                thumbnail: item.snippet.thumbnails.default?.url || `https://i.ytimg.com/vi/${item.id.videoId}/default.jpg`
            }));
        } catch (err) {
            console.error(err);
            resultsListDiv.innerHTML = '<div style="text-align:center; padding:20px; color:#ffb79e;">⚠️ Ошибка сети или API недоступен</div>';
            return [];
        }
    }
    
    async function performSearch() {
        let query = searchInput.value.trim();
        if (!query) {
            resultsListDiv.innerHTML = '<div style="text-align:center; padding:20px; color:rgba(255,255,255,0.7);">Введите название песни</div>';
            return;
        }
        resultsListDiv.innerHTML = '<div style="text-align:center; padding:20px; color:rgba(255,255,255,0.7);" class="loading">🔎 Ищем музыку через YouTube API...</div>';
        const items = await searchYouTubeVideos(query, 14);
        if (!items.length) {
            resultsListDiv.innerHTML = '<div style="text-align:center; padding:20px; color:#ffb79e;">😕 Ничего не найдено. Попробуйте другой запрос.</div>';
            return;
        }
        resultsListDiv.innerHTML = '';
        items.forEach(item => {
            const div = document.createElement('div');
            div.className = 'result-item';
            div.innerHTML = `
                <img class="result-thumb" src="${item.thumbnail}" alt="thumb" onerror="this.src='https://i.ytimg.com/vi/${item.id}/default.jpg'">
                <div class="result-info">
                    <div class="result-title">${escapeHtml(item.title)}</div>
                    <div class="result-channel">${escapeHtml(item.channel)}</div>
                </div>
            `;
            div.addEventListener('click', () => {
                loadVideoById(item.id, true);
                setStatusMessage(`🎵 Загружено: ${item.title.substring(0, 60)}`);
                if(urlInput) urlInput.value = `https://youtu.be/${item.id}`;
            });
            resultsListDiv.appendChild(div);
        });
    }
    
    function escapeHtml(str) { 
        if(!str) return '';
        return str.replace(/[&<>]/g, function(m){
            if(m==='&') return '&amp;';
            if(m==='<') return '&lt;';
            if(m==='>') return '&gt;';
            return m;
        });
    }
    
    // --- ИНИЦИАЛИЗАЦИЯ ---
    function onYouTubeIframeAPIReady() {
        player = new YT.Player('youtube-player', {
            height: '100%', width: '100%', videoId: '',
            playerVars: { autoplay: 0, controls: 0, disablekb: 0, fs: 0, rel: 0, modestbranding: 1 },
            events: {
                onReady: (e) => { 
                    playerReady = true; 
                    setVolume(volumeSlider.value); 
                    setStatusMessage("🎉 Плеер готов! Наслаждайся музыкой"); 
                    if(window.pendingVideoId) { 
                        loadVideoById(window.pendingVideoId); 
                        delete window.pendingVideoId; 
                    } 
                },
                onStateChange: (e) => { 
                    if(e.data === YT.PlayerState.PLAYING) setStatusMessage("🎶 Музыка играет"); 
                    else if(e.data === YT.PlayerState.PAUSED) setStatusMessage("⏸ На паузе"); 
                    else if(e.data === YT.PlayerState.ENDED) setStatusMessage("🏁 Трек завершён"); 
                    if(currentVideoId) setTimeout(()=>updateTrackMetadata(currentVideoId),300);
                },
                onError: (e) => { setStatusMessage("Ошибка плеера", true); currentVideoId = null; }
            }
        });
    }
    window.onYouTubeIframeAPIReady = onYouTubeIframeAPIReady;
    if (typeof YT !== 'undefined' && YT.Player) onYouTubeIframeAPIReady();
    
    // ---- ОБРАБОТЧИКИ ----
    loadBtn.addEventListener('click', () => { let id = extractVideoId(urlInput.value); if(id) loadVideoById(id, true); else setStatusMessage("Некорректная ссылка YouTube", true); });
    playBtn.addEventListener('click', playVideo);
    pauseBtn.addEventListener('click', pauseVideo);
    stopResetBtn.addEventListener('click', stopAndReset);
    volumeSlider.addEventListener('input', (e) => setVolume(e.target.value));
    searchBtn.addEventListener('click', performSearch);
    searchInput.addEventListener('keypress', (e) => { if(e.key === 'Enter') performSearch(); });
    
    // Автоматический поиск при загрузке
    setTimeout(() => { 
        if(searchInput.value) performSearch(); 
        setStatusMessage("💫 API ключ установлен! Можно искать музыку", false);
    }, 1200);
</script>
</body>
</html>
