<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>MusicFlow | YouTube API плеер + поиск</title>
    <script src="https://www.youtube.com/iframe_api"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', 'Inter', system-ui, sans-serif;
            min-height: 100vh;
            padding: 20px;
            transition: background 0.4s ease;
            background: linear-gradient(145deg, #0b0f1c 0%, #141b2b 100%);
            background-attachment: fixed;
        }

        .dashboard {
            max-width: 1400px;
            margin: 0 auto;
            display: flex;
            flex-wrap: wrap;
            gap: 28px;
        }

        .player-col {
            flex: 2;
            min-width: 280px;
        }

        .search-col {
            flex: 1.2;
            min-width: 280px;
            background: rgba(10, 15, 28, 0.75);
            backdrop-filter: blur(16px);
            border-radius: 48px;
            padding: 22px 20px;
            border: 1px solid rgba(255, 255, 255, 0.08);
            box-shadow: 0 20px 35px rgba(0, 0, 0, 0.3);
            height: fit-content;
        }

        .player-container {
            background: rgba(18, 25, 40, 0.7);
            backdrop-filter: blur(14px);
            border-radius: 56px;
            padding: 24px 26px 32px;
            box-shadow: 0 25px 45px rgba(0, 0, 0, 0.4);
        }

        h1 {
            font-size: 1.9rem;
            font-weight: 600;
            background: linear-gradient(135deg, #ffffff, #b9c8ff);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            margin-bottom: 8px;
        }

        .sub {
            color: #8e9bb5;
            font-size: 0.85rem;
            margin-bottom: 28px;
            border-left: 3px solid #3b82f6;
            padding-left: 12px;
        }

        .input-group {
            background: #0f1422e0;
            border-radius: 80px;
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 5px 5px 5px 22px;
            margin-bottom: 28px;
            border: 1px solid #2d374b;
        }
        .input-group:focus-within {
            border-color: #3b82f6;
            box-shadow: 0 0 0 3px rgba(59,130,246,0.2);
        }
        .input-group input {
            flex: 1;
            background: transparent;
            border: none;
            padding: 14px 0;
            font-size: 1rem;
            color: #f0f3fa;
            outline: none;
        }
        .input-group input::placeholder {
            color: #5f6c8a;
        }
        .input-group button {
            background: #3b82f6;
            border: none;
            padding: 10px 24px;
            border-radius: 60px;
            color: white;
            font-weight: 600;
            cursor: pointer;
            transition: 0.2s;
        }
        .input-group button:hover {
            background: #2563eb;
            transform: scale(0.97);
        }

        .track-info {
            background: #0b1020aa;
            border-radius: 36px;
            padding: 16px 22px;
            margin-bottom: 24px;
            border: 1px solid #25304b;
        }
        .track-title {
            font-size: 1.3rem;
            font-weight: 600;
            color: white;
            display: flex;
            align-items: center;
            gap: 8px;
            flex-wrap: wrap;
        }
        .track-title span:first-child {
            background: #3b82f620;
            padding: 4px 12px;
            border-radius: 40px;
            font-size: 0.75rem;
            color: #90b4ff;
        }
        .track-name {
            color: #eef3ff;
        }
        .track-meta {
            font-size: 0.8rem;
            color: #94a3c7;
            margin-top: 8px;
            display: flex;
            gap: 20px;
        }

        .youtube-player-box {
            width: 100%;
            background: #00000030;
            border-radius: 32px;
            overflow: hidden;
            margin-bottom: 20px;
        }
        #youtube-player {
            width: 100%;
            aspect-ratio: 16 / 9;
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
            background: #0e1428b3;
            padding: 8px 18px;
            border-radius: 60px;
        }
        .ctrl-btn {
            background: transparent;
            border: none;
            color: #dce5ff;
            font-size: 1.9rem;
            cursor: pointer;
            width: 48px;
            height: 48px;
            border-radius: 60px;
            transition: 0.15s;
        }
        .ctrl-btn:hover {
            background: #2d3a5e;
            transform: scale(1.02);
        }
        .volume-slider {
            flex: 1;
            min-width: 120px;
            background: #10172a;
            padding: 5px 15px;
            border-radius: 40px;
            display: flex;
            align-items: center;
            gap: 12px;
        }
        input[type="range"] {
            flex: 1;
            height: 4px;
            -webkit-appearance: none;
            background: #2c385c;
            border-radius: 10px;
        }
        input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 14px;
            height: 14px;
            background: #3b82f6;
            border-radius: 50%;
            cursor: pointer;
        }
        .status-msg {
            font-size: 0.75rem;
            color: #6f7da0;
            text-align: center;
            margin-top: 20px;
            padding-top: 12px;
            border-top: 1px solid #253153;
        }

        .search-header {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
            flex-wrap: wrap;
        }
        .search-header input {
            flex: 1;
            background: #0a0f1cee;
            border: 1px solid #2f3b5c;
            padding: 12px 18px;
            border-radius: 60px;
            color: white;
            font-size: 0.9rem;
            outline: none;
        }
        .search-header button {
            background: #3b82f6;
            border: none;
            padding: 0 20px;
            border-radius: 60px;
            color: white;
            font-weight: 600;
            cursor: pointer;
        }
        .search-header button:hover {
            background: #2563eb;
        }

        .api-status {
            background: #1e2a3e;
            border-radius: 16px;
            padding: 10px 14px;
            margin-bottom: 16px;
            font-size: 0.75rem;
            border-left: 3px solid #f59e0b;
        }
        .api-status.success {
            border-left-color: #10b981;
            background: #064e3b30;
        }
        .api-status.error {
            border-left-color: #ef4444;
            background: #7f1d1d30;
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
            background: #10172ad4;
            border-radius: 28px;
            padding: 12px 16px;
            cursor: pointer;
            transition: 0.1s;
            border: 1px solid #2a3455;
            display: flex;
            align-items: center;
            gap: 14px;
        }
        .result-item:hover {
            background: #1e2a4e;
            border-color: #3b82f6;
        }
        .result-thumb {
            width: 48px;
            height: 48px;
            border-radius: 16px;
            object-fit: cover;
        }
        .result-info {
            flex: 1;
        }
        .result-title {
            font-weight: 600;
            color: white;
            font-size: 0.85rem;
        }
        .result-channel {
            font-size: 0.7rem;
            color: #9aa9cc;
        }

        .bg-changer {
            display: flex;
            gap: 10px;
            margin-top: 20px;
            justify-content: center;
            flex-wrap: wrap;
        }
        .bg-btn {
            background: #1f2a48;
            border: none;
            padding: 8px 14px;
            border-radius: 40px;
            color: white;
            font-size: 0.75rem;
            cursor: pointer;
            transition: 0.2s;
        }
        .bg-btn:hover {
            background: #3b82f6;
        }

        footer {
            font-size: 0.7rem;
            text-align: center;
            margin-top: 18px;
            color: #4b567c;
        }
        ::-webkit-scrollbar {
            width: 5px;
        }
        ::-webkit-scrollbar-track {
            background: #1e2642;
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb {
            background: #3b82f6;
            border-radius: 10px;
        }
        @media (max-width: 800px) {
            .dashboard {
                flex-direction: column;
            }
        }
    </style>
</head>
<body>

<div class="dashboard">
    <div class="player-col">
        <div class="player-container">
            <h1>🎵 Музодром</h1>
            <div class="sub">YouTube API · поиск песен · смена фона</div>

            <div class="input-group">
                <input type="text" id="youtube-url" placeholder="Или вставь ссылку / ID видео">
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
                    <button class="ctrl-btn" id="play-btn">▶️</button>
                    <button class="ctrl-btn" id="pause-btn">⏸️</button>
                    <button class="ctrl-btn" id="stop-reset-btn">⏹️</button>
                </div>
                <div class="volume-slider">
                    <span>🔊</span>
                    <input type="range" id="volume-control" min="0" max="100" value="70">
                </div>
            </div>
            <div class="status-msg" id="status-msg">
                ✨ Вставляй ссылки или ищи музыку справа
            </div>
        </div>
    </div>

    <div class="search-col">
        <div style="font-weight: 600; margin-bottom: 12px; color:white;">🔍 Поиск песен</div>
        
        <div id="api-status" class="api-status">
            ⚙️ Проверка API ключа...
        </div>
        
        <div class="search-header">
            <input type="text" id="search-input" placeholder="Например: Imagine Dragons, lo-fi, rock" value="chill music">
            <button id="search-btn">🔎 Найти</button>
        </div>
        <div class="results-list" id="results-list">
            <div style="text-align:center; padding:20px; color:#9aa9cc;">✨ Введи запрос и нажми «Найти»</div>
        </div>
        <div class="search-suggest" style="font-size:0.7rem; color:#6a7aa8; text-align:center; margin-top:16px;">
            💡 Попробуй: «металл», «рэп», «инструментал», «джаз», «phonk»
        </div>
        
        <div style="margin-top: 28px;">
            <div style="color:white; font-size:0.8rem; margin-bottom: 8px;">🎨 Сменить фон</div>
            <div class="bg-changer">
                <button class="bg-btn" data-bg="gradient">🌌 Градиент</button>
                <button class="bg-btn" data-bg="dark">🌑 Тёмный</button>
                <button class="bg-btn" data-bg="forest">🌲 Лес</button>
                <button class="bg-btn" data-bg="ocean">🌊 Океан</button>
                <button class="bg-btn" data-bg="sunset">🌅 Закат</button>
                <button class="bg-btn" data-bg="abstract">🎨 Абстракция</button>
            </div>
        </div>
    </div>
</div>

<script>
    // ======================== ВАШ API КЛЮЧ ========================
    const API_KEY = 'AIzaSyB-CXxXs0lj-5IkBBJswxncefw-v3UTfB8';
    
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
    const apiStatusDiv = document.getElementById('api-status');
    
    function setStatusMessage(text, isError = false) {
        statusMsgDiv.innerHTML = isError ? `⚠️ ${text}` : `✨ ${text}`;
        statusMsgDiv.style.color = isError ? "#ffb7a7" : "#6f7da0";
    }
    
    function extractVideoId(url) {
        if (!url) return null;
        url = url.trim();
        if (/^[a-zA-Z0-9_-]{11}$/.test(url)) return url;
        let regExp = /^.*(youtu.be\/|v\/|embed\/|watch\?v=|\&v=)([^#\&\?]*).*/;
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
            if (dur && dur > 0) {
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
            return true;
        }
        setStatusMessage(`Загружаем трек...`);
        if (startPlaying) player.loadVideoById(videoId);
        currentVideoId = videoId;
        updateTrackMetadata(videoId);
        setTimeout(() => updateTrackMetadata(videoId), 1200);
        return true;
    }
    
    function playVideo() { if(playerReady && currentVideoId) player.playVideo(); else setStatusMessage("Нет трека", true); }
    function pauseVideo() { if(playerReady) player.pauseVideo(); }
    function stopAndReset() { if(playerReady && currentVideoId) player.stopVideo(); }
    function setVolume(val) { if(playerReady) player.setVolume(val); }
    
    // ======================== ПРОВЕРКА API КЛЮЧА ========================
    async function testApiKey() {
        try {
            const testUrl = `https://www.googleapis.com/youtube/v3/search?part=snippet&maxResults=1&q=test&type=video&key=${API_KEY}`;
            const response = await fetch(testUrl);
            const data = await response.json();
            
            if (data.error) {
                let errorMessage = '';
                if (data.error.code === 403) {
                    errorMessage = '❌ API ключ недействителен или YouTube Data API v3 не включён в проекте. Зайдите в Google Cloud Console → выберите проект → включите YouTube Data API v3';
                } else if (data.error.code === 400) {
                    errorMessage = '❌ Ошибка в запросе. Проверьте ключ.';
                } else {
                    errorMessage = `❌ Ошибка: ${data.error.message || 'Неизвестная ошибка'}`;
                }
                apiStatusDiv.className = 'api-status error';
                apiStatusDiv.innerHTML = `⚠️ ${errorMessage}<br><small style="display:block; margin-top:6px;">🔧 Решение: перейдите на <a href="https://console.cloud.google.com/apis/library/youtube.googleapis.com" target="_blank" style="color:#3b82f6;">console.cloud.google.com</a> и включите YouTube Data API v3 для вашего проекта</small>`;
                return false;
            } else {
                apiStatusDiv.className = 'api-status success';
                apiStatusDiv.innerHTML = '✅ API ключ работает! YouTube Data API v3 активен. Можно искать музыку. 🎵';
                return true;
            }
        } catch (err) {
            apiStatusDiv.className = 'api-status error';
            apiStatusDiv.innerHTML = '❌ Не удалось подключиться к Google API. Проверьте интернет-соединение.';
            return false;
        }
    }
    
    // ======================== ПОИСК ЧЕРЕЗ YOUTUBE API ========================
    async function searchYouTubeVideos(query, maxResults = 12) {
        try {
            const url = `https://www.googleapis.com/youtube/v3/search?part=snippet&maxResults=${maxResults}&q=${encodeURIComponent(query)}&type=video&key=${API_KEY}`;
            const response = await fetch(url);
            const data = await response.json();
            
            if (data.error) {
                let errorMsg = data.error.message || 'Ошибка API';
                resultsListDiv.innerHTML = `<div style="text-align:center; padding:20px; color:#ffb79e;">❌ ${errorMsg}<br><br>🔧 Включите YouTube Data API v3 в Google Cloud Console</div>`;
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
            resultsListDiv.innerHTML = '<div style="text-align:center; padding:20px; color:#ffb79e;">⚠️ Ошибка сети</div>';
            return [];
        }
    }
    
    const searchInput = document.getElementById('search-input');
    const searchBtn = document.getElementById('search-btn');
    const resultsListDiv = document.getElementById('results-list');
    
    async function performSearch() {
        let query = searchInput.value.trim();
        if (!query) {
            resultsListDiv.innerHTML = '<div style="text-align:center; padding:20px; color:#9aa9cc;">Введите название песни или исполнителя</div>';
            return;
        }
        resultsListDiv.innerHTML = '<div style="text-align:center; padding:20px; color:#9aa9cc;">🔎 Ищем через YouTube API...</div>';
        const items = await searchYouTubeVideos(query, 14);
        if (!items.length) {
            if (resultsListDiv.innerHTML.includes('Включите YouTube Data API')) return;
            resultsListDiv.innerHTML = '<div style="text-align:center; padding:20px; color:#ffb79e;">😕 Ничего не найдено. Попробуйте другой запрос.</div>';
            return;
        }
        resultsListDiv.innerHTML = '';
        items.forEach(item => {
            const div = document.createElement('div');
            div.className = 'result-item';
            div.innerHTML = `
                <img class="result-thumb" src="${item.thumbnail}" onerror="this.src='https://i.ytimg.com/vi/${item.id}/default.jpg'">
                <div class="result-info">
                    <div class="result-title">${escapeHtml(item.title)}</div>
                    <div class="result-channel">${escapeHtml(item.channel)}</div>
                </div>
            `;
            div.addEventListener('click', () => {
                loadVideoById(item.id, true);
                setStatusMessage(`🎵 ${item.title.substring(0, 60)}`);
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
    
    // ======================== СМЕНА ФОНА ========================
    function setBackground(style) {
        const body = document.body;
        switch(style) {
            case 'gradient': body.style.background = "linear-gradient(145deg, #0b0f1c 0%, #141b2b 100%)"; break;
            case 'dark': body.style.background = "#05070f"; break;
            case 'forest': body.style.background = "url('https://images.pexels.com/photos/2387793/pexels-photo-2387793.jpeg?auto=compress&cs=tinysrgb&w=1600') center/cover no-repeat fixed"; break;
            case 'ocean': body.style.background = "url('https://images.pexels.com/photos/1118873/pexels-photo-1118873.jpeg?auto=compress&cs=tinysrgb&w=1600') center/cover no-repeat fixed"; break;
            case 'sunset': body.style.background = "url('https://images.pexels.com/photos/1261728/pexels-photo-1261728.jpeg?auto=compress&cs=tinysrgb&w=1600') center/cover no-repeat fixed"; break;
            case 'abstract': body.style.background = "url('https://images.pexels.com/photos/1966452/pexels-photo-1966452.jpeg?auto=compress&cs=tinysrgb&w=1600') center/cover no-repeat fixed"; break;
            default: body.style.background = "linear-gradient(145deg, #0b0f1c 0%, #141b2b 100%)";
        }
        body.style.backgroundSize = "cover";
        body.style.backgroundAttachment = "fixed";
    }
    
    // ======================== ИНИЦИАЛИЗАЦИЯ ========================
    function onYouTubeIframeAPIReady() {
        player = new YT.Player('youtube-player', {
            height: '100%', width: '100%', videoId: '',
            playerVars: { autoplay: 0, controls: 0, disablekb: 0, fs: 0, rel: 0, modestbranding: 1 },
            events: {
                onReady: (e) => { 
                    playerReady = true; 
                    setVolume(volumeSlider.value); 
                    setStatusMessage("Плеер готов! Ищи музыку справа");
                    if(window.pendingVideoId) { loadVideoById(window.pendingVideoId); delete window.pendingVideoId; } 
                },
                onStateChange: (e) => { 
                    if(e.data === YT.PlayerState.PLAYING) setStatusMessage("🎶 Воспроизведение");
                    else if(e.data === YT.PlayerState.PAUSED) setStatusMessage("⏸ Пауза");
                    if(currentVideoId) setTimeout(()=>updateTrackMetadata(currentVideoId),300);
                },
                onError: (e) => { setStatusMessage("Ошибка плеера", true); currentVideoId = null; }
            }
        });
    }
    window.onYouTubeIframeAPIReady = onYouTubeIframeAPIReady;
    if (typeof YT !== 'undefined' && YT.Player) onYouTubeIframeAPIReady();
    
    // Обработчики
    loadBtn.addEventListener('click', () => { let id = extractVideoId(urlInput.value); if(id) loadVideoById(id, true); else setStatusMessage("Некорректная ссылка", true); });
    playBtn.addEventListener('click', playVideo);
    pauseBtn.addEventListener('click', pauseVideo);
    stopResetBtn.addEventListener('click', stopAndReset);
    volumeSlider.addEventListener('input', (e) => setVolume(parseInt(e.target.value)));
    searchBtn.addEventListener('click', performSearch);
    searchInput.addEventListener('keypress', (e) => { if(e.key === 'Enter') performSearch(); });
    document.querySelectorAll('.bg-btn').forEach(btn => {
        btn.addEventListener('click', () => setBackground(btn.getAttribute('data-bg')));
    });
    
    // Запуск проверки API и авто-поиск
    testApiKey().then(works => {
        if (works && searchInput.value) {
            performSearch();
        }
    });
</script>
</body>
</html>
