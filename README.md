<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>MusicFlow Pro | Свои плейлисты + Плеер</title>
    <script src="https://www.youtube.com/iframe_api"></script>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: 'Segoe UI', 'Inter', system-ui, sans-serif;
            min-height: 100vh;
            padding: 20px;
            background: linear-gradient(135deg, #0f0c29, #302b63, #24243e);
            background-attachment: fixed;
            transition: background 0.5s ease;
        }
        /* Фоны */
        body.bg-gradient-dark { background: linear-gradient(135deg, #0f0c29, #302b63, #24243e); }
        body.bg-sunset { background: linear-gradient(135deg, #ff7e5f, #feb47b); }
        body.bg-ocean { background: linear-gradient(135deg, #2193b0, #6dd5ed); }
        body.bg-forest { background: linear-gradient(135deg, #134e5e, #71b280); }
        body.bg-midnight { background: linear-gradient(135deg, #232526, #414345); }
        body.bg-aurora { background: linear-gradient(135deg, #1a2980, #26d0ce); }
        body.bg-cherry { background: linear-gradient(135deg, #cb2d3e, #ef473a); }
        body.bg-space { background: linear-gradient(135deg, #0b0b2b, #1b1b4b, #2d2d6b); }
        body.bg-abstract { background: linear-gradient(135deg, #8E2DE2, #4A00E0); }

        .dashboard { max-width: 1600px; margin: 0 auto; display: flex; flex-wrap: wrap; gap: 24px; }
        .player-col { flex: 2; min-width: 300px; }
        .sidebar-col { flex: 1.5; min-width: 380px; display: flex; flex-direction: column; gap: 24px; }
        .card { background: rgba(18, 25, 40, 0.85); backdrop-filter: blur(14px); border-radius: 32px; padding: 20px; border: 1px solid rgba(255,255,255,0.1); box-shadow: 0 20px 35px rgba(0,0,0,0.3); }
        .player-container { background: rgba(18, 25, 40, 0.8); backdrop-filter: blur(14px); border-radius: 48px; padding: 24px 26px 32px; box-shadow: 0 25px 45px rgba(0,0,0,0.4); }
        h1 { font-size: 1.9rem; font-weight: 600; background: linear-gradient(135deg, #fff, #b9c8ff); -webkit-background-clip: text; background-clip: text; color: transparent; margin-bottom: 4px; }
        .sub { color: #8e9bb5; font-size: 0.85rem; margin-bottom: 24px; border-left: 3px solid #3b82f6; padding-left: 12px; }
        .input-group { background: #0f1422e0; border-radius: 80px; display: flex; gap: 12px; padding: 5px 5px 5px 22px; margin-bottom: 20px; border: 1px solid #2d374b; }
        .input-group input { flex: 1; background: transparent; border: none; padding: 12px 0; font-size: 0.95rem; color: #f0f3fa; outline: none; }
        .input-group button { background: #3b82f6; border: none; padding: 8px 20px; border-radius: 60px; color: white; font-weight: 600; cursor: pointer; transition: 0.2s; }
        .input-group button:hover { background: #2563eb; transform: scale(0.97); }
        .track-info { background: #0b1020aa; border-radius: 28px; padding: 14px 20px; margin-bottom: 20px; }
        .track-name { font-size: 1.2rem; font-weight: 600; color: white; margin-bottom: 8px; }
        .track-meta { font-size: 0.75rem; color: #94a3c7; display: flex; gap: 16px; flex-wrap: wrap; }
        .progress-section { margin-bottom: 16px; }
        .progress-bar-container { background: #2d374b; border-radius: 10px; height: 6px; cursor: pointer; position: relative; }
        .progress-bar-fill { background: #3b82f6; border-radius: 10px; height: 100%; width: 0%; transition: width 0.1s linear; }
        .time-labels { display: flex; justify-content: space-between; margin-top: 6px; font-size: 0.7rem; color: #8e9bb5; }
        .controls { display: flex; align-items: center; justify-content: space-between; gap: 12px; flex-wrap: wrap; }
        .playback-buttons { display: flex; gap: 12px; background: #0e1428b3; padding: 6px 16px; border-radius: 60px; }
        .ctrl-btn { background: transparent; border: none; color: #dce5ff; font-size: 1.6rem; cursor: pointer; width: 44px; height: 44px; border-radius: 60px; transition: 0.15s; }
        .ctrl-btn:hover { background: #2d3a5e; transform: scale(1.02); }
        .volume-slider { display: flex; align-items: center; gap: 10px; background: #10172a; padding: 5px 15px; border-radius: 40px; }
        input[type="range"] { width: 100px; height: 4px; -webkit-appearance: none; background: #2c385c; border-radius: 10px; }
        input[type="range"]::-webkit-slider-thumb { -webkit-appearance: none; width: 12px; height: 12px; background: #3b82f6; border-radius: 50%; cursor: pointer; }
        #youtube-player { width: 100%; aspect-ratio: 16/9; border-radius: 24px; margin-bottom: 16px; }
        
        .current-track-actions { display: flex; gap: 10px; margin-top: 10px; justify-content: flex-start; flex-wrap: wrap; }
        .btn-primary { background: #3b82f6; border: none; border-radius: 30px; padding: 8px 16px; color: white; font-size: 0.8rem; cursor: pointer; transition: 0.2s; display: inline-flex; align-items: center; gap: 6px; }
        .btn-primary:hover { background: #2563eb; transform: scale(0.98); }
        .btn-secondary { background: #1f2a48; border: none; border-radius: 30px; padding: 8px 16px; color: white; font-size: 0.8rem; cursor: pointer; transition: 0.2s; }
        .btn-secondary:hover { background: #2d3a5e; transform: scale(0.98); }
        
        .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.7); backdrop-filter: blur(5px); z-index: 1000; justify-content: center; align-items: center; }
        .modal-content { background: #1a1f2e; border-radius: 32px; padding: 24px; width: 350px; max-width: 90%; border: 1px solid #3b82f6; max-height: 80%; overflow-y: auto; }
        .modal-content h3 { color: white; margin-bottom: 16px; }
        .playlist-option { padding: 10px 16px; margin: 6px 0; background: #0f1422; border-radius: 40px; cursor: pointer; color: white; transition: 0.1s; display: flex; justify-content: space-between; align-items: center; }
        .playlist-option:hover { background: #3b82f6; transform: scale(0.98); }
        .modal-close { background: #ef4444; border: none; padding: 8px 16px; border-radius: 40px; color: white; cursor: pointer; margin-top: 16px; width: 100%; }
        
        .tabs { display: flex; gap: 8px; margin-bottom: 16px; border-bottom: 1px solid #2d374b; }
        .tab-btn { background: transparent; border: none; padding: 10px 20px; color: #94a3c7; font-weight: 600; cursor: pointer; border-radius: 20px 20px 0 0; }
        .tab-btn.active { color: #3b82f6; border-bottom: 2px solid #3b82f6; }
        .tab-content { display: none; }
        .tab-content.active { display: block; }
        
        .playlist-list, .history-list { max-height: 350px; overflow-y: auto; display: flex; flex-direction: column; gap: 8px; }
        .playlist-item { background: #0f1422e0; border-radius: 20px; padding: 12px 14px; border: 1px solid #2a3455; }
        .playlist-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 8px; flex-wrap: wrap; gap: 8px; }
        .playlist-name { font-weight: 700; color: white; font-size: 1rem; cursor: pointer; }
        .playlist-name:hover { color: #3b82f6; }
        .playlist-actions { display: flex; gap: 6px; }
        .playlist-delete { background: none; border: none; color: #ef4444; cursor: pointer; font-size: 1rem; padding: 4px 8px; border-radius: 20px; }
        .playlist-delete:hover { background: #ef444420; }
        .playlist-play { background: #3b82f6; border: none; border-radius: 20px; padding: 4px 12px; color: white; cursor: pointer; font-size: 0.7rem; }
        .playlist-play:hover { background: #2563eb; }
        .playlist-tracks-list { margin-top: 8px; padding-left: 12px; border-left: 2px solid #3b82f6; }
        .playlist-track { display: flex; justify-content: space-between; align-items: center; padding: 6px 0; font-size: 0.8rem; color: #cbd5e1; border-bottom: 1px solid #2a3455; }
        .playlist-track-title { cursor: pointer; flex: 1; }
        .playlist-track-title:hover { color: #3b82f6; }
        .playlist-track-remove { background: none; border: none; color: #ef4444; cursor: pointer; font-size: 0.7rem; padding: 2px 8px; border-radius: 20px; }
        
        .history-item { background: #0f1422e0; border-radius: 20px; padding: 10px 14px; display: flex; justify-content: space-between; align-items: center; border: 1px solid #2a3455; margin-bottom: 6px; }
        .history-info { flex: 1; }
        .history-title { font-weight: 600; color: white; font-size: 0.85rem; }
        .history-date { font-size: 0.65rem; color: #6f7da0; }
        .history-actions { display: flex; gap: 6px; }
        .history-play { background: #3b82f6; border: none; border-radius: 20px; padding: 4px 10px; color: white; cursor: pointer; font-size: 0.7rem; }
        
        .new-playlist { display: flex; gap: 8px; margin-top: 12px; }
        .new-playlist input { flex: 1; background: #0a0f1c; border: 1px solid #2f3b5c; padding: 8px 12px; border-radius: 60px; color: white; }
        .new-playlist button { background: #3b82f6; border: none; padding: 8px 16px; border-radius: 60px; color: white; cursor: pointer; }
        
        .bg-changer { display: flex; gap: 8px; margin-top: 16px; flex-wrap: wrap; justify-content: center; }
        .bg-btn { background: #1f2a48; border: none; padding: 8px 12px; border-radius: 40px; color: white; font-size: 0.7rem; cursor: pointer; transition: 0.2s; }
        .bg-btn:hover { background: #3b82f6; transform: scale(0.96); }
        .status-msg { font-size: 0.7rem; color: #6f7da0; text-align: center; margin-top: 16px; }
        
        .search-results { max-height: 250px; overflow-y: auto; margin-bottom: 12px; }
        .result-item { background: #0f1422e0; border-radius: 16px; padding: 8px 12px; margin-bottom: 6px; cursor: pointer; display: flex; align-items: center; gap: 10px; border: 1px solid #2a3455; }
        .result-item:hover { background: #1e2a4e; border-color: #3b82f6; }
        .result-thumb { width: 40px; height: 40px; border-radius: 12px; object-fit: cover; }
        .result-info { flex: 1; }
        .result-title { font-weight: 600; color: white; font-size: 0.8rem; }
        .result-channel { font-size: 0.65rem; color: #9aa9cc; }
        .result-actions { display: flex; gap: 6px; }
        .small-btn { background: #3b82f6; border: none; border-radius: 20px; padding: 4px 10px; color: white; font-size: 0.7rem; cursor: pointer; }
        
        footer { font-size: 0.7rem; text-align: center; margin-top: 16px; color: #4b567c; }
        ::-webkit-scrollbar { width: 5px; }
        ::-webkit-scrollbar-track { background: #1e2642; border-radius: 10px; }
        ::-webkit-scrollbar-thumb { background: #3b82f6; border-radius: 10px; }
        @media (max-width: 900px) { .dashboard { flex-direction: column; } }
    </style>
</head>
<body>

<div class="dashboard">
    <div class="player-col">
        <div class="player-container">
            <h1>🎵 Музодром Pro</h1>
            <div class="sub">Свои плейлисты · Автовоспроизведение · Перемотка</div>
            <div class="input-group">
                <input type="text" id="youtube-url" placeholder="Ссылка YouTube или ID видео">
                <button id="load-btn">▶ Загрузить</button>
            </div>
            <div class="track-info">
                <div class="track-name" id="track-name">—</div>
                <div class="track-meta"><span id="video-id-status">⏳ ID не загружен</span></div>
            </div>
            <div id="youtube-player"></div>
            <div class="progress-section">
                <div class="progress-bar-container" id="progress-bar"><div class="progress-bar-fill" id="progress-fill"></div></div>
                <div class="time-labels"><span id="current-time">0:00</span><span id="total-time">0:00</span></div>
            </div>
            <div class="controls">
                <div class="playback-buttons">
                    <button class="ctrl-btn" id="play-btn">▶️</button>
                    <button class="ctrl-btn" id="pause-btn">⏸️</button>
                    <button class="ctrl-btn" id="stop-reset-btn">⏹️</button>
                    <button class="ctrl-btn" id="next-btn" title="Следующий трек">⏭️</button>
                </div>
                <div class="volume-slider"><span>🔊</span><input type="range" id="volume-control" min="0" max="100" value="70"></div>
            </div>
            <div class="current-track-actions">
                <button class="btn-primary" id="add-current-to-playlist">📋 Добавить текущий трек в плейлист</button>
                <button class="btn-secondary" id="play-current-playlist">🎵 Слушать текущий плейлист</button>
            </div>
            <div class="status-msg" id="status-msg">✨ Создавайте плейлисты и слушайте свою музыку!</div>
        </div>
    </div>

    <div class="sidebar-col">
        <div class="card">
            <div class="input-group" style="margin-bottom: 16px;">
                <input type="text" id="search-input" placeholder="Поиск песен...">
                <button id="search-btn">🔎 Найти</button>
            </div>
            <div id="search-results" class="search-results"></div>
            
            <div class="tabs">
                <button class="tab-btn active" data-tab="playlists">📋 Мои плейлисты</button>
                <button class="tab-btn" data-tab="history">📜 История</button>
            </div>
            <div id="playlists-tab" class="tab-content active">
                <div class="playlist-list" id="playlists-list"></div>
                <div class="new-playlist"><input type="text" id="new-playlist-name" placeholder="Название плейлиста"><button id="create-playlist-btn">+ Создать</button></div>
            </div>
            <div id="history-tab" class="tab-content">
                <div class="history-list" id="history-list"></div>
                <div style="margin-top: 12px; text-align: center;"><button id="clear-history-btn" style="background:#ef4444; border:none; padding:6px 16px; border-radius:40px; color:white; cursor:pointer;">🗑 Очистить историю</button></div>
            </div>
        </div>

        <div class="card">
            <div style="font-weight:600; margin-bottom:12px; color:white;">🎨 Сменить фон</div>
            <div class="bg-changer">
                <button class="bg-btn" data-bg="gradient-dark">🌌 Космический</button>
                <button class="bg-btn" data-bg="sunset">🌅 Закат</button>
                <button class="bg-btn" data-bg="ocean">🌊 Океан</button>
                <button class="bg-btn" data-bg="forest">🌲 Лес</button>
                <button class="bg-btn" data-bg="midnight">🌙 Полночь</button>
                <button class="bg-btn" data-bg="aurora">✨ Аврора</button>
                <button class="bg-btn" data-bg="cherry">🌸 Сакура</button>
                <button class="bg-btn" data-bg="space">🚀 Космос</button>
                <button class="bg-btn" data-bg="abstract">🎨 Абстракция</button>
            </div>
        </div>
    </div>
</div>

<!-- Модальное окно выбора плейлиста -->
<div id="playlist-modal" class="modal">
    <div class="modal-content">
        <h3>📁 Выберите плейлист</h3>
        <div id="modal-playlist-list"></div>
        <button class="modal-close" id="modal-close">Отмена</button>
    </div>
</div>

<script>
    // ========== API КЛЮЧ ==========
    const API_KEY = 'AIzaSyB-CXxXs0lj-5IkBBJswxncefw-v3UTfB8';
    
    // ========== ПЛЕЕР ==========
    let player = null, playerReady = false, currentVideoId = null, currentTrackTitle = "";
    let progressInterval = null;
    let currentPlaylistQueue = []; // Очередь для автовоспроизведения
    let currentQueueIndex = -1;
    
    const urlInput = document.getElementById('youtube-url');
    const loadBtn = document.getElementById('load-btn');
    const playBtn = document.getElementById('play-btn');
    const pauseBtn = document.getElementById('pause-btn');
    const stopResetBtn = document.getElementById('stop-reset-btn');
    const nextBtn = document.getElementById('next-btn');
    const volumeSlider = document.getElementById('volume-control');
    const trackNameSpan = document.getElementById('track-name');
    const videoIdStatusSpan = document.getElementById('video-id-status');
    const currentTimeSpan = document.getElementById('current-time');
    const totalTimeSpan = document.getElementById('total-time');
    const progressFill = document.getElementById('progress-fill');
    const progressBar = document.getElementById('progress-bar');
    const statusMsgDiv = document.getElementById('status-msg');
    
    function setStatusMessage(text, isError = false) { statusMsgDiv.innerHTML = isError ? `⚠️ ${text}` : `✨ ${text}`; }
    function formatTime(seconds) { if (isNaN(seconds)) return "0:00"; const mins = Math.floor(seconds / 60); const secs = Math.floor(seconds % 60); return `${mins}:${secs.toString().padStart(2,'0')}`; }
    
    function updateProgress() {
        if (player && playerReady && currentVideoId) {
            const current = player.getCurrentTime();
            const duration = player.getDuration();
            if (duration && duration > 0) {
                progressFill.style.width = `${(current / duration) * 100}%`;
                currentTimeSpan.innerText = formatTime(current);
                totalTimeSpan.innerText = formatTime(duration);
            }
        }
    }
    function seekTo(event) { if (!player || !playerReady || !currentVideoId) return; const rect = progressBar.getBoundingClientRect(); const x = event.clientX - rect.left; const duration = player.getDuration(); if (duration && duration > 0) player.seekTo((x / rect.width) * duration, true); }
    progressBar.addEventListener('click', seekTo);
    function startProgressInterval() { if (progressInterval) clearInterval(progressInterval); progressInterval = setInterval(updateProgress, 500); }
    function stopProgressInterval() { if (progressInterval) { clearInterval(progressInterval); progressInterval = null; } }
    
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
        return fallback && fallback[1].length === 11 ? fallback[1] : null;
    }
    
    async function fetchTrackTitle(videoId) {
        try { let res = await fetch(`https://www.youtube.com/oembed?url=https://www.youtube.com/watch?v=${videoId}&format=json`); if (res.ok) { let data = await res.json(); return data.title || `Видео ${videoId}`; } } catch(e) {}
        return `Трек ${videoId}`;
    }
    
    function playNextTrack() {
        if (currentPlaylistQueue.length > 0 && currentQueueIndex + 1 < currentPlaylistQueue.length) {
            currentQueueIndex++;
            const nextTrack = currentPlaylistQueue[currentQueueIndex];
            loadVideoById(nextTrack.id, true, false);
            setStatusMessage(`⏭ Следующий трек: ${nextTrack.title.substring(0, 50)}`);
        } else {
            setStatusMessage("🎵 Плейлист закончился");
            currentPlaylistQueue = [];
            currentQueueIndex = -1;
        }
    }
    
    async function loadVideoById(videoId, startPlaying = true, saveToHistory = true) {
        if (!videoId) { setStatusMessage("Неверный ID", true); return false; }
        if (!player || !playerReady) { window.pendingVideoId = videoId; setStatusMessage("Плеер инициализируется...", false); return false; }
        currentVideoId = videoId;
        currentTrackTitle = await fetchTrackTitle(videoId);
        trackNameSpan.innerText = currentTrackTitle;
        videoIdStatusSpan.innerText = `🎬 ID: ${videoId}`;
        if (startPlaying) player.loadVideoById(videoId);
        if (saveToHistory) addToHistory(videoId, currentTrackTitle);
        startProgressInterval();
        return true;
    }
    
    function playVideo() { if(playerReady && currentVideoId) player.playVideo(); else setStatusMessage("Нет трека", true); }
    function pauseVideo() { if(playerReady) player.pauseVideo(); }
    function stopAndReset() { if(playerReady && currentVideoId) player.stopVideo(); }
    function setVolume(val) { if(playerReady) player.setVolume(val); }
    
    // ========== ПЛЕЙЛИСТЫ ==========
    let playlists = JSON.parse(localStorage.getItem('music_playlists_v2') || '{"Мой плейлист":[]}');
    function savePlaylists() { localStorage.setItem('music_playlists_v2', JSON.stringify(playlists)); renderPlaylists(); }
    
    function renderPlaylists() {
        const container = document.getElementById('playlists-list');
        container.innerHTML = '';
        for (let name in playlists) {
            const tracks = playlists[name];
            const div = document.createElement('div'); div.className = 'playlist-item';
            div.innerHTML = `
                <div class="playlist-header">
                    <span class="playlist-name">📁 ${name}</span>
                    <div class="playlist-actions">
                        <button class="playlist-play" data-name="${name}">▶ Слушать</button>
                        <button class="playlist-delete" data-name="${name}">🗑</button>
                    </div>
                </div>
                <div class="playlist-tracks-list" id="tracks-${name.replace(/[^a-zA-Z0-9]/g, '_')}"></div>
            `;
            const tracksContainer = div.querySelector(`.playlist-tracks-list`);
            tracks.forEach((track, idx) => {
                const trackDiv = document.createElement('div'); trackDiv.className = 'playlist-track';
                trackDiv.innerHTML = `
                    <span class="playlist-track-title" data-id="${track.id}" data-title="${escapeHtml(track.title)}">🎵 ${track.title.substring(0, 50)}</span>
                    <button class="playlist-track-remove" data-name="${name}" data-idx="${idx}">✖</button>
                `;
                tracksContainer.appendChild(trackDiv);
            });
            div.querySelector('.playlist-play').addEventListener('click', (e) => { e.stopPropagation(); playPlaylist(name); });
            div.querySelector('.playlist-delete').addEventListener('click', (e) => { e.stopPropagation(); if (name === 'Мой плейлист') { setStatusMessage("Нельзя удалить плейлист по умолчанию", true); return; } delete playlists[name]; savePlaylists(); });
            div.querySelectorAll('.playlist-track-title').forEach(el => {
                el.addEventListener('click', () => { loadVideoById(el.dataset.id, true, true); urlInput.value = `https://youtu.be/${el.dataset.id}`; });
            });
            div.querySelectorAll('.playlist-track-remove').forEach(el => {
                el.addEventListener('click', () => { playlists[name].splice(parseInt(el.dataset.idx), 1); if(playlists[name].length === 0 && name !== 'Мой плейлист') delete playlists[name]; savePlaylists(); });
            });
            container.appendChild(div);
        }
        if (Object.keys(playlists).length === 0) { playlists['Мой плейлист'] = []; savePlaylists(); }
    }
    
    function playPlaylist(playlistName) {
        if (!playlists[playlistName] || playlists[playlistName].length === 0) {
            setStatusMessage(`Плейлист "${playlistName}" пуст`, true);
            return;
        }
        currentPlaylistQueue = [...playlists[playlistName]];
        currentQueueIndex = 0;
        const firstTrack = currentPlaylistQueue[0];
        loadVideoById(firstTrack.id, true, true);
        setStatusMessage(`🎵 Играет плейлист "${playlistName}" (${currentPlaylistQueue.length} треков)`);
    }
    
    function addToPlaylist(playlistName, videoId, title) {
        if (!playlists[playlistName]) playlists[playlistName] = [];
        if (!playlists[playlistName].some(t => t.id === videoId)) {
            playlists[playlistName].push({ id: videoId, title: title, added: Date.now() });
            savePlaylists();
            setStatusMessage(`✅ Добавлено в "${playlistName}"`);
        } else setStatusMessage(`⏺ Уже есть в плейлисте "${playlistName}"`);
    }
    
    function createPlaylist(name) {
        if (playlists[name]) { setStatusMessage("Плейлист уже существует", true); return false; }
        playlists[name] = []; savePlaylists(); setStatusMessage(`📁 Плейлист "${name}" создан`); return true;
    }
    
    function showPlaylistSelector(videoId, title) {
        const modal = document.getElementById('playlist-modal');
        const modalList = document.getElementById('modal-playlist-list');
        modalList.innerHTML = '';
        for (let name in playlists) {
            const option = document.createElement('div'); option.className = 'playlist-option';
            option.innerHTML = `<span>📁 ${name}</span><span style="font-size:0.7rem;">${playlists[name].length} треков</span>`;
            option.addEventListener('click', () => { addToPlaylist(name, videoId, title); modal.style.display = 'none'; });
            modalList.appendChild(option);
        }
        modal.style.display = 'flex';
    }
    
    document.getElementById('modal-close').addEventListener('click', () => { document.getElementById('playlist-modal').style.display = 'none'; });
    document.getElementById('add-current-to-playlist').addEventListener('click', () => { if (currentVideoId && currentTrackTitle) showPlaylistSelector(currentVideoId, currentTrackTitle); else setStatusMessage("Нет активного трека", true); });
    document.getElementById('play-current-playlist').addEventListener('click', () => { if (currentPlaylistQueue.length > 0 && currentQueueIndex >= 0) { player.playVideo(); } else { setStatusMessage("Нет активного плейлиста. Сначала выберите плейлист для прослушивания", true); } });
    
    // ========== ИСТОРИЯ ==========
    let history = JSON.parse(localStorage.getItem('music_history') || '[]');
    function saveHistory() { localStorage.setItem('music_history', JSON.stringify(history)); renderHistory(); }
    async function addToHistory(videoId, title) { history.unshift({ id: videoId, title: title, date: new Date().toLocaleString(), timestamp: Date.now() }); if (history.length > 50) history.pop(); saveHistory(); }
    function renderHistory() {
        const container = document.getElementById('history-list');
        container.innerHTML = '';
        if (history.length === 0) { container.innerHTML = '<div style="text-align:center; padding:20px; color:#9aa9cc;">История пуста</div>'; return; }
        history.forEach((item) => {
            const div = document.createElement('div'); div.className = 'history-item';
            div.innerHTML = `<div class="history-info"><div class="history-title">${escapeHtml(item.title)}</div><div class="history-date">${item.date}</div></div><div class="history-actions"><button class="history-play" data-id="${item.id}">▶</button><button class="small-btn" data-id="${item.id}" data-title="${escapeHtml(item.title)}">📋</button></div>`;
            div.querySelector('.history-play').addEventListener('click', () => { loadVideoById(item.id, true, false); });
            div.querySelector('.small-btn').addEventListener('click', (e) => { e.stopPropagation(); showPlaylistSelector(item.id, item.title); });
            container.appendChild(div);
        });
    }
    document.getElementById('clear-history-btn').addEventListener('click', () => { history = []; saveHistory(); setStatusMessage("История очищена"); });
    
    // ========== ПОИСК ==========
    async function searchYouTube(query) {
        try {
            const url = `https://www.googleapis.com/youtube/v3/search?part=snippet&maxResults=10&q=${encodeURIComponent(query)}&type=video&key=${API_KEY}`;
            const response = await fetch(url);
            const data = await response.json();
            if (data.error) return [];
            return data.items.map(item => ({ id: item.id.videoId, title: item.snippet.title, channel: item.snippet.channelTitle, thumbnail: item.snippet.thumbnails.default?.url }));
        } catch(e) { return []; }
    }
    
    const searchInput = document.getElementById('search-input');
    const searchBtn = document.getElementById('search-btn');
    const searchResults = document.getElementById('search-results');
    searchBtn.addEventListener('click', async () => {
        const query = searchInput.value.trim();
        if (!query) return;
        searchResults.innerHTML = '<div style="text-align:center; padding:20px; color:#9aa9cc;">🔎 Поиск...</div>';
        const results = await searchYouTube(query);
        searchResults.innerHTML = '';
        results.forEach(r => {
            const div = document.createElement('div'); div.className = 'result-item';
            div.innerHTML = `<img class="result-thumb" src="${r.thumbnail}" onerror="this.src='https://i.ytimg.com/vi/${r.id}/default.jpg'"><div class="result-info"><div class="result-title">${escapeHtml(r.title)}</div><div class="result-channel">${escapeHtml(r.channel)}</div></div><div class="result-actions"><button class="small-btn add-to-playlist" data-id="${r.id}" data-title="${escapeHtml(r.title)}">📋</button><button class="small-btn play-now" data-id="${r.id}" style="background:#3b82f6;">▶</button></div>`;
            div.querySelector('.play-now').addEventListener('click', (e) => { e.stopPropagation(); loadVideoById(r.id, true, true); urlInput.value = `https://youtu.be/${r.id}`; });
            div.querySelector('.add-to-playlist').addEventListener('click', (e) => { e.stopPropagation(); showPlaylistSelector(r.id, r.title); });
            div.addEventListener('click', () => { loadVideoById(r.id, true, true); urlInput.value = `https://youtu.be/${r.id}`; });
            searchResults.appendChild(div);
        });
    });
    searchInput.addEventListener('keypress', (e) => { if(e.key === 'Enter') searchBtn.click(); });
    
    function escapeHtml(str) { if(!str) return ''; return str.replace(/[&<>]/g, m => ({'&':'&amp;','<':'&lt;','>':'&gt;'}[m])); }
    
    // ========== ФОНЫ ==========
    function setBackground(style) {
        const body = document.body;
        body.className = '';
        if (style === 'gradient-dark') body.classList.add('bg-gradient-dark');
        else if (style === 'sunset') body.classList.add('bg-sunset');
        else if (style === 'ocean') body.classList.add('bg-ocean');
        else if (style === 'forest') body.classList.add('bg-forest');
        else if (style === 'midnight') body.classList.add('bg-midnight');
        else if (style === 'aurora') body.classList.add('bg-aurora');
        else if (style === 'cherry') body.classList.add('bg-cherry');
        else if (style === 'space') body.classList.add('bg-space');
        else if (style === 'abstract') body.classList.add('bg-abstract');
        localStorage.setItem('selected_bg', style);
    }
    const savedBg = localStorage.getItem('selected_bg');
    if (savedBg) setBackground(savedBg);
    document.querySelectorAll('.bg-btn').forEach(btn => { btn.addEventListener('click', () => setBackground(btn.dataset.bg)); });
    
    // ========== ИНИЦИАЛИЗАЦИЯ ПЛЕЕРА ==========
    function onYouTubeIframeAPIReady() {
        player = new YT.Player('youtube-player', {
            height: '100%', width: '100%', videoId: '',
            playerVars: { autoplay: 0, controls: 0, disablekb: 0, fs: 0, rel: 0, modestbranding: 1 },
            events: {
                onReady: (e) => { playerReady = true; setVolume(volumeSlider.value); setStatusMessage("Плеер готов!"); if(window.pendingVideoId) { loadVideoById(window.pendingVideoId); delete window.pendingVideoId; } },
                onStateChange: (e) => { 
                    if(e.data === YT.PlayerState.PLAYING) startProgressInterval(); 
                    else if(e.data === YT.PlayerState.PAUSED) stopProgressInterval(); 
                    else if(e.data === YT.PlayerState.ENDED) { 
                        stopProgressInterval(); 
                        if (currentPlaylistQueue.length > 0 && currentQueueIndex + 1 < currentPlaylistQueue.length) {
                            playNextTrack();
                        }
                    }
                    if(currentVideoId) setTimeout(() => updateProgress(), 200); 
                },
                onError: (e) => { setStatusMessage("Ошибка плеера", true); }
            }
        });
    }
    window.onYouTubeIframeAPIReady = onYouTubeIframeAPIReady;
    if (typeof YT !== 'undefined' && YT.Player) onYouTubeIframeAPIReady();
    
    loadBtn.addEventListener('click', () => { let id = extractVideoId(urlInput.value); if(id) loadVideoById(id, true, true); else setStatusMessage("Некорректная ссылка", true); });
    playBtn.addEventListener('click', playVideo);
    pauseBtn.addEventListener('click', pauseVideo);
    stopResetBtn.addEventListener('click', stopAndReset);
    nextBtn.addEventListener('click', playNextTrack);
    volumeSlider.addEventListener('input', (e) => setVolume(parseInt(e.target.value)));
    
    document.querySelectorAll('.tab-btn').forEach(btn => { btn.addEventListener('click', () => { document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active')); document.querySelectorAll('.tab-content').forEach(t => t.classList.remove('active')); btn.classList.add('active'); document.getElementById(`${btn.dataset.tab}-tab`).classList.add('active'); }); });
    document.getElementById('create-playlist-btn').addEventListener('click', () => { let name = document.getElementById('new-playlist-name').value.trim(); if (name) { createPlaylist(name); document.getElementById('new-playlist-name').value = ''; } else setStatusMessage("Введите название", true); });
    
    renderPlaylists();
    renderHistory();
</script>
</body>
</html>
