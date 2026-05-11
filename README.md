
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>YouTube Stats — Global Tracker</title>
  <link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=Noto+Sans+KR:wght@400;700;900&display=swap" rel="stylesheet" />
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    :root {
      --red: #FF0000;
      --accent: #FF3C00;
      --accent2: #FF9100;
      --bg: #0a0a0a;
      --bg2: #111;
      --bg3: #1a1a1a;
      --text: #f0f0f0;
      --muted: #666;
      --border: rgba(255,255,255,0.07);
    }
    html { scroll-behavior: smooth; }
    body {
      background: var(--bg);
      color: var(--text);
      font-family: 'Noto Sans KR', sans-serif;
      min-height: 100vh;
    }
    ::-webkit-scrollbar { width: 4px; }
    ::-webkit-scrollbar-track { background: #111; }
    ::-webkit-scrollbar-thumb { background: var(--accent); border-radius: 4px; }

    /* ── API Key Screen ── */
    #apiScreen {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      min-height: 100vh;
      padding: 24px;
      background: radial-gradient(ellipse at center, #1a0000 0%, #0a0a0a 70%);
    }
    .api-box {
      width: 100%;
      max-width: 480px;
      background: #111;
      border: 1px solid rgba(255,60,0,0.25);
      border-radius: 20px;
      padding: 36px 32px;
      text-align: center;
    }
    .logo-big {
      font-family: 'Bebas Neue', cursive;
      font-size: 42px;
      letter-spacing: 4px;
      color: var(--accent);
      line-height: 1;
      margin-bottom: 4px;
    }
    .logo-sub { font-size: 11px; color: var(--muted); letter-spacing: 3px; margin-bottom: 28px; }
    .api-label { font-size: 13px; color: #aaa; margin-bottom: 10px; text-align: left; font-weight: 700; }
    .api-input {
      width: 100%;
      background: var(--bg);
      border: 1px solid rgba(255,255,255,0.12);
      border-radius: 10px;
      padding: 12px 16px;
      color: var(--text);
      font-size: 13px;
      font-family: monospace;
      outline: none;
      transition: border-color 0.2s;
      margin-bottom: 12px;
    }
    .api-input:focus { border-color: var(--accent); }
    .api-btn {
      width: 100%;
      background: linear-gradient(135deg, var(--accent), var(--accent2));
      color: #fff;
      border: none;
      border-radius: 10px;
      padding: 13px;
      font-size: 14px;
      font-weight: 900;
      font-family: 'Noto Sans KR', sans-serif;
      cursor: pointer;
      letter-spacing: 1px;
      transition: opacity 0.2s, transform 0.1s;
    }
    .api-btn:hover { opacity: 0.9; transform: translateY(-1px); }
    .api-hint {
      margin-top: 16px;
      font-size: 11px;
      color: #444;
      line-height: 1.7;
    }
    .api-hint a { color: var(--accent); text-decoration: none; }
    .api-hint a:hover { text-decoration: underline; }
    .api-error {
      margin-top: 10px;
      font-size: 12px;
      color: #ff5252;
      display: none;
    }

    /* ── Main App ── */
    #app { display: none; }

    /* Header */
    .header {
      background: linear-gradient(180deg, #1a0000 0%, var(--bg) 100%);
      border-bottom: 1px solid rgba(255,60,0,0.2);
      padding: 18px 20px 0;
      position: sticky;
      top: 0;
      z-index: 100;
      backdrop-filter: blur(10px);
    }
    .header-inner { max-width: 840px; margin: 0 auto; }
    .header-top {
      display: flex;
      align-items: center;
      gap: 12px;
      margin-bottom: 16px;
    }
    .logo-mark {
      background: var(--red);
      border-radius: 8px;
      width: 36px; height: 36px;
      display: flex; align-items: center; justify-content: center;
      font-size: 16px;
      flex-shrink: 0;
    }
    .logo-text { line-height: 1; }
    .logo-text .t1 {
      font-family: 'Bebas Neue', cursive;
      font-size: 26px;
      letter-spacing: 3px;
      color: var(--accent);
    }
    .logo-text .t2 { font-size: 9px; color: #444; letter-spacing: 2px; }
    .header-right { margin-left: auto; display: flex; flex-direction: column; align-items: flex-end; gap: 4px; }
    .update-time { font-size: 10px; color: #444; }
    .live-badge {
      display: flex; align-items: center; gap: 5px;
      font-size: 10px; color: var(--accent); font-weight: 700;
    }
    .live-dot {
      width: 6px; height: 6px; border-radius: 50%;
      background: var(--accent);
      animation: blink 1.5s infinite;
    }
    .change-key-btn {
      font-size: 10px;
      color: #555;
      background: none;
      border: none;
      cursor: pointer;
      font-family: 'Noto Sans KR', sans-serif;
      text-decoration: underline;
    }
    .change-key-btn:hover { color: #aaa; }

    /* Tabs */
    .tabs { display: flex; gap: 2px; overflow-x: auto; }
    .tabs::-webkit-scrollbar { display: none; }
    .tab-btn {
      padding: 10px 16px;
      border: none;
      border-bottom: 2px solid transparent;
      border-radius: 10px 10px 0 0;
      background: transparent;
      color: var(--muted);
      font-size: 12px;
      font-family: 'Noto Sans KR', sans-serif;
      font-weight: 400;
      cursor: pointer;
      white-space: nowrap;
      transition: all 0.2s;
    }
    .tab-btn.active {
      color: var(--accent);
      font-weight: 700;
      background: rgba(255,60,0,0.1);
      border-bottom-color: var(--accent);
    }
    .tab-btn:hover:not(.active) { color: #aaa; }

    /* Main */
    .main { max-width: 840px; margin: 0 auto; padding: 20px; }

    /* Genre Pills */
    .genre-pills {
      display: flex; gap: 8px; overflow-x: auto;
      padding-bottom: 4px; margin-bottom: 20px;
    }
    .genre-pills::-webkit-scrollbar { display: none; }
    .genre-pill {
      padding: 7px 14px;
      border-radius: 20px;
      border: 1px solid rgba(255,255,255,0.1);
      background: rgba(255,255,255,0.04);
      color: #888;
      font-size: 12px;
      font-family: 'Noto Sans KR', sans-serif;
      cursor: pointer;
      white-space: nowrap;
      transition: all 0.2s;
    }
    .genre-pill.active {
      border-color: var(--accent);
      background: rgba(255,60,0,0.18);
      color: var(--accent);
      font-weight: 700;
    }

    /* Section header */
    .section-header {
      display: flex; align-items: center; gap: 10px; margin-bottom: 16px;
    }
    .section-bar {
      width: 3px; height: 22px;
      background: linear-gradient(180deg, var(--accent), var(--accent2));
      border-radius: 2px; flex-shrink: 0;
    }
    .section-title {
      font-family: 'Bebas Neue', cursive;
      font-size: 18px; letter-spacing: 1px; color: var(--text);
    }
    .section-desc { font-size: 10px; color: #444; }
    .refresh-btn {
      margin-left: auto;
      background: rgba(255,60,0,0.12);
      border: 1px solid rgba(255,60,0,0.25);
      color: var(--accent);
      padding: 5px 12px;
      border-radius: 20px;
      font-size: 11px;
      font-family: 'Noto Sans KR', sans-serif;
      cursor: pointer;
      transition: all 0.2s;
    }
    .refresh-btn:hover { background: rgba(255,60,0,0.22); }

    /* Video Card */
    .video-card {
      display: flex;
      align-items: center;
      gap: 14px;
      padding: 13px 16px;
      border-radius: 12px;
      border: 1px solid var(--border);
      background: rgba(255,255,255,0.02);
      margin-bottom: 10px;
      cursor: pointer;
      transition: all 0.18s ease;
      text-decoration: none;
      color: inherit;
    }
    .video-card:hover {
      background: rgba(255,60,0,0.08);
      transform: translateX(4px);
      border-color: rgba(255,60,0,0.2);
    }
    .video-card.top3 {
      background: rgba(255,60,0,0.05);
      border-color: rgba(255,60,0,0.18);
    }

    /* Rank */
    .rank {
      min-width: 36px; height: 36px;
      border-radius: 10px;
      display: flex; align-items: center; justify-content: center;
      font-family: 'Bebas Neue', cursive;
      font-size: 16px;
      flex-shrink: 0;
    }
    .rank.r1 { background: linear-gradient(135deg,#FFD700,#FFD70099); color:#111; font-size:18px; box-shadow:0 0 12px #FFD70066; }
    .rank.r2 { background: linear-gradient(135deg,#C0C0C0,#C0C0C099); color:#111; font-size:18px; box-shadow:0 0 10px #C0C0C066; }
    .rank.r3 { background: linear-gradient(135deg,#CD7F32,#CD7F3299); color:#111; font-size:18px; box-shadow:0 0 10px #CD7F3266; }
    .rank.rn { background: rgba(255,255,255,0.08); color:#888; }

    /* Thumbnail */
    .thumb {
      width: 96px; height: 54px;
      border-radius: 8px;
      overflow: hidden;
      flex-shrink: 0;
      position: relative;
      background: var(--bg3);
    }
    .thumb img { width: 100%; height: 100%; object-fit: cover; display: block; }
    .thumb-placeholder {
      width: 100%; height: 100%;
      display: flex; align-items: center; justify-content: center;
      font-size: 22px;
    }
    .thumb-duration {
      position: absolute; bottom: 3px; right: 4px;
      background: rgba(0,0,0,0.85);
      font-size: 9px; color:#fff;
      padding: 1px 4px; border-radius: 3px;
    }

    /* Info */
    .info { flex: 1; min-width: 0; }
    .info-title {
      font-size: 13px; font-weight: 700; color: var(--text);
      margin-bottom: 3px;
      white-space: nowrap; overflow: hidden; text-overflow: ellipsis;
    }
    .info-channel { font-size: 11px; color: #777; margin-bottom: 5px; }
    .info-meta { display: flex; align-items: center; gap: 8px; flex-wrap: wrap; }
    .views {
      font-size: 12px; color: var(--accent); font-weight: 700;
      font-family: 'Bebas Neue', cursive; letter-spacing: 0.5px;
    }
    .badge {
      font-size: 9px; font-weight: 800;
      padding: 1px 7px; border-radius: 20px;
      font-family: 'Bebas Neue', cursive; letter-spacing: 0.5px;
    }
    .badge-new { background: linear-gradient(90deg,#00C853,#69F0AE); color:#000; }
    .badge-hot { background: linear-gradient(90deg,var(--accent),var(--accent2)); color:#fff; }
    .upload-time { font-size: 10px; color: #444; }

    /* Flag */
    .flag { font-size: 20px; flex-shrink: 0; }

    /* Loading */
    .loading-list { padding: 4px 0; }
    .loading-card {
      display: flex; gap: 14px; align-items: center;
      padding: 13px 16px; margin-bottom: 10px;
      border-radius: 12px;
      background: rgba(255,255,255,0.02);
      border: 1px solid var(--border);
    }
    .skel {
      border-radius: 6px;
      background: linear-gradient(90deg, #1a1a1a 25%, #222 50%, #1a1a1a 75%);
      background-size: 200% 100%;
      animation: shimmer 1.5s infinite;
    }
    @keyframes shimmer { 0%{background-position:200% 0} 100%{background-position:-200% 0} }
    @keyframes blink { 0%,100%{opacity:1} 50%{opacity:0.2} }

    /* Error */
    .error-box {
      padding: 40px; text-align: center;
      background: rgba(255,60,0,0.05);
      border: 1px solid rgba(255,60,0,0.2);
      border-radius: 12px;
      color: var(--accent);
    }
    .error-box .ei { font-size: 30px; margin-bottom: 10px; }
    .error-box .et { font-size: 13px; line-height: 1.7; color: #aaa; }

    /* Empty */
    .empty-box { padding: 40px; text-align: center; color: #444; font-size: 13px; }

    /* Footer */
    .footer {
      margin-top: 32px;
      padding-top: 16px;
      border-top: 1px solid rgba(255,255,255,0.05);
      text-align: center;
      font-size: 10px;
      color: #2a2a2a;
      padding-bottom: 30px;
    }
  </style>
</head>
<body>

<!-- ── API Key Screen ── -->
<div id="apiScreen">
  <div class="api-box">
    <div class="logo-big">YOUTUBE STATS</div>
    <div class="logo-sub">GLOBAL TRENDING TRACKER</div>

    <div class="api-label">🔑 YouTube Data API v3 키 입력</div>
    <input
      type="text"
      id="apiKeyInput"
      class="api-input"
      placeholder="AIza..."
      autocomplete="off"
      spellcheck="false"
    />
    <button class="api-btn" onclick="startApp()">시작하기 →</button>
    <div class="api-error" id="apiError">API 키가 올바르지 않거나 연결에 실패했습니다.</div>

    <div class="api-hint">
      API 키가 없으신가요?<br />
      1. <a href="https://console.cloud.google.com" target="_blank">Google Cloud Console</a> 접속<br />
      2. 새 프로젝트 생성<br />
      3. <strong>YouTube Data API v3</strong> 검색 후 사용 설정<br />
      4. 사용자 인증 정보 → API 키 생성<br />
      5. 생성된 키를 위에 붙여넣기<br /><br />
      <span style="color:#333">※ API 키는 이 브라우저에만 저장되며 외부로 전송되지 않습니다.</span>
    </div>
  </div>
</div>

<!-- ── Main App ── -->
<div id="app">
  <div class="header">
    <div class="header-inner">
      <div class="header-top">
        <div class="logo-mark">▶</div>
        <div class="logo-text">
          <div class="t1">YOUTUBE STATS</div>
          <div class="t2">GLOBAL TRENDING TRACKER</div>
        </div>
        <div class="header-right">
          <div class="live-badge">
            <div class="live-dot"></div> LIVE
          </div>
          <div class="update-time" id="updateTime"></div>
          <button class="change-key-btn" onclick="changeKey()">API 키 변경</button>
        </div>
      </div>

      <div class="tabs">
        <button class="tab-btn active" data-tab="trending" onclick="switchTab(this,'trending')">🔥 실시간 트렌딩</button>
        <button class="tab-btn" data-tab="global" onclick="switchTab(this,'global')">🌍 글로벌 베스트</button>
        <button class="tab-btn" data-tab="new" onclick="switchTab(this,'new')">🆕 신규 진입</button>
        <button class="tab-btn" data-tab="genre" onclick="switchTab(this,'genre')">🎯 장르별 베스트</button>
      </div>
    </div>
  </div>

  <div class="main">
    <!-- Genre pills (genre tab only) -->
    <div class="genre-pills" id="genrePills" style="display:none">
      <button class="genre-pill active" data-genre="" onclick="switchGenre(this,'')">🌐 전체</button>
      <button class="genre-pill" data-genre="10" onclick="switchGenre(this,'10')">🎵 음악</button>
      <button class="genre-pill" data-genre="20" onclick="switchGenre(this,'20')">🎮 게임</button>
      <button class="genre-pill" data-genre="17" onclick="switchGenre(this,'17')">⚽ 스포츠</button>
      <button class="genre-pill" data-genre="25" onclick="switchGenre(this,'25')">📰 뉴스</button>
      <button class="genre-pill" data-genre="24" onclick="switchGenre(this,'24')">🎬 엔터</button>
      <button class="genre-pill" data-genre="27" onclick="switchGenre(this,'27')">📚 교육</button>
      <button class="genre-pill" data-genre="28" onclick="switchGenre(this,'28')">💻 테크</button>
      <button class="genre-pill" data-genre="26" onclick="switchGenre(this,'26')">🍜 라이프</button>
    </div>

    <!-- Section header -->
    <div class="section-header">
      <div class="section-bar"></div>
      <div>
        <div class="section-title" id="sectionTitle">🔥 실시간 트렌딩</div>
        <div class="section-desc" id="sectionDesc">지금 가장 빠르게 상승 중인 영상</div>
      </div>
      <button class="refresh-btn" onclick="refreshData()">↻ 새로고침</button>
    </div>

    <!-- Video list -->
    <div id="videoList"></div>

    <div class="footer">
      YouTube Data API v3 실데이터 · YouTube Stats Global Tracker
    </div>
  </div>
</div>

<script>
  // ── State ──
  let API_KEY = '';
  let currentTab = 'trending';
  let currentGenre = '';
  const cache = {};

  const TABS_META = {
    trending: { title: '🔥 실시간 트렌딩', desc: '지금 가장 빠르게 상승 중인 영상' },
    global:   { title: '🌍 글로벌 베스트 10', desc: '전세계 최고 조회수 영상' },
    new:      { title: '🆕 신규 진입', desc: '최근 새롭게 주목받는 영상' },
    genre:    { title: '🎯 장르별 베스트 10', desc: '카테고리별 인기 영상' },
  };

  const GENRE_LABELS = {
    '':'🌐 전체','10':'🎵 음악','20':'🎮 게임','17':'⚽ 스포츠',
    '25':'📰 뉴스','24':'🎬 엔터','27':'📚 교육','28':'💻 테크','26':'🍜 라이프'
  };

  // ── API Key ──
  function startApp() {
    const key = document.getElementById('apiKeyInput').value.trim();
    if (!key || key.length < 10) {
      showApiError('API 키를 입력해주세요.');
      return;
    }
    API_KEY = key;
    localStorage.setItem('yt_api_key', key);
    launchApp();
  }

  function showApiError(msg) {
    const el = document.getElementById('apiError');
    el.textContent = msg;
    el.style.display = 'block';
  }

  function launchApp() {
    document.getElementById('apiScreen').style.display = 'none';
    document.getElementById('app').style.display = 'block';
    fetchVideos(currentTab, currentGenre);
  }

  function changeKey() {
    document.getElementById('apiScreen').style.display = 'flex';
    document.getElementById('app').style.display = 'none';
    document.getElementById('apiKeyInput').value = API_KEY;
  }

  // ── Tab/Genre switching ──
  function switchTab(btn, tab) {
    document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    currentTab = tab;

    const pills = document.getElementById('genrePills');
    pills.style.display = tab === 'genre' ? 'flex' : 'none';

    const meta = TABS_META[tab];
    document.getElementById('sectionTitle').textContent = meta.title;
    document.getElementById('sectionDesc').textContent = meta.desc;

    fetchVideos(tab, currentGenre);
  }

  function switchGenre(btn, genre) {
    document.querySelectorAll('.genre-pill').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    currentGenre = genre;
    fetchVideos(currentTab, genre);
  }

  function refreshData() {
    const key = `${currentTab}_${currentGenre}`;
    delete cache[key];
    fetchVideos(currentTab, currentGenre);
  }

  // ── YouTube API ──
  async function fetchVideos(tab, genre) {
    const key = `${tab}_${genre}`;
    if (cache[key]) { renderVideos(cache[key], tab); return; }

    showLoading();

    try {
      let items = [];

      if (tab === 'trending' || tab === 'global') {
        items = await fetchTrending(genre, 10);
      } else if (tab === 'new') {
        items = await fetchNew(genre, 10);
      } else if (tab === 'genre') {
        items = await fetchTrending(genre, 10);
      }

      // Fetch statistics (views)
      if (items.length > 0) {
        const ids = items.map(i => i.id?.videoId || i.id).filter(Boolean).join(',');
        const stats = await fetchStats(ids);
        items = items.map(item => {
          const id = item.id?.videoId || item.id;
          return { ...item, stats: stats[id] || {} };
        });
      }

      cache[key] = items;
      renderVideos(items, tab);
      updateTime();
    } catch (e) {
      showError(e.message || '데이터를 불러오지 못했습니다.');
    }
  }

  async function fetchTrending(categoryId, maxResults) {
    let url = `https://www.googleapis.com/youtube/v3/videos?part=snippet,contentDetails&chart=mostPopular&maxResults=${maxResults}&key=${API_KEY}`;
    if (categoryId) url += `&videoCategoryId=${categoryId}`;
    const res = await apiFetch(url);
    return res.items || [];
  }

  async function fetchNew(categoryId, maxResults) {
    const since = new Date(Date.now() - 7 * 24 * 60 * 60 * 1000).toISOString();
    let url = `https://www.googleapis.com/youtube/v3/search?part=snippet&type=video&order=viewCount&publishedAfter=${since}&maxResults=${maxResults}&key=${API_KEY}`;
    if (categoryId) url += `&videoCategoryId=${categoryId}`;
    const res = await apiFetch(url);
    return res.items || [];
  }

  async function fetchStats(ids) {
    const url = `https://www.googleapis.com/youtube/v3/videos?part=statistics&id=${ids}&key=${API_KEY}`;
    const res = await apiFetch(url);
    const map = {};
    (res.items || []).forEach(item => { map[item.id] = item.statistics; });
    return map;
  }

  async function apiFetch(url) {
    const res = await fetch(url);
    const data = await res.json();
    if (data.error) throw new Error(data.error.message || 'API 오류');
    return data;
  }

  // ── Render ──
  function renderVideos(items, tab) {
    const list = document.getElementById('videoList');
    if (!items || items.length === 0) {
      list.innerHTML = '<div class="empty-box">표시할 영상이 없습니다.</div>';
      return;
    }

    list.innerHTML = items.map((item, i) => {
      const rank = i + 1;
      const isSearch = !!item.id?.videoId;
      const videoId = isSearch ? item.id.videoId : item.id;
      const snippet = item.snippet || {};
      const stats = item.stats || {};

      const title = snippet.title || '제목 없음';
      const channel = snippet.channelTitle || '';
      const thumb = snippet.thumbnails?.medium?.url || snippet.thumbnails?.default?.url || '';
      const publishedAt = snippet.publishedAt ? timeAgo(snippet.publishedAt) : '';
      const duration = item.contentDetails?.duration ? parseDuration(item.contentDetails.duration) : '';
      const views = stats.viewCount ? formatViews(parseInt(stats.viewCount)) : '';
      const country = snippet.defaultAudioLanguage || '';

      const rankClass = rank === 1 ? 'r1' : rank === 2 ? 'r2' : rank === 3 ? 'r3' : 'rn';
      const isNew = tab === 'new';
      const isTrending = tab === 'trending';

      return `
        <a class="video-card ${rank <= 3 ? 'top3' : ''}" href="https://www.youtube.com/watch?v=${videoId}" target="_blank" rel="noopener">
          <div class="rank ${rankClass}">${rank}</div>
          <div class="thumb">
            ${thumb
              ? `<img src="${thumb}" alt="" loading="lazy" onerror="this.style.display='none'" />`
              : `<div class="thumb-placeholder">🎬</div>`
            }
            ${duration ? `<div class="thumb-duration">${duration}</div>` : ''}
          </div>
          <div class="info">
            <div class="info-title" title="${escHtml(title)}">${escHtml(title)}</div>
            <div class="info-channel">${escHtml(channel)}</div>
            <div class="info-meta">
              ${views ? `<span class="views">👁 ${views}</span>` : ''}
              ${isNew ? `<span class="badge badge-new">NEW</span>` : ''}
              ${isTrending ? `<span class="badge badge-hot">🔥 트렌딩</span>` : ''}
              ${publishedAt ? `<span class="upload-time">${publishedAt}</span>` : ''}
            </div>
          </div>
        </a>
      `;
    }).join('');
  }

  function showLoading() {
    const list = document.getElementById('videoList');
    list.innerHTML = '<div class="loading-list">' +
      Array(10).fill(0).map((_, i) => `
        <div class="loading-card" style="animation-delay:${i*0.08}s">
          <div class="skel" style="width:36px;height:36px;border-radius:10px"></div>
          <div class="skel" style="width:96px;height:54px;border-radius:8px"></div>
          <div style="flex:1">
            <div class="skel" style="height:13px;width:70%;margin-bottom:7px"></div>
            <div class="skel" style="height:10px;width:40%"></div>
          </div>
        </div>
      `).join('') + '</div>';
  }

  function showError(msg) {
    document.getElementById('videoList').innerHTML = `
      <div class="error-box">
        <div class="ei">⚠️</div>
        <div class="et">${escHtml(msg)}<br><br>
        API 키를 확인하거나 YouTube Data API v3가 활성화되어 있는지 확인해주세요.</div>
      </div>
    `;
  }

  function updateTime() {
    const el = document.getElementById('updateTime');
    if (el) el.textContent = '업데이트: ' + new Date().toLocaleTimeString('ko-KR');
  }

  // ── Helpers ──
  function formatViews(n) {
    if (n >= 1e8) return (n / 1e8).toFixed(1) + '억';
    if (n >= 1e4) return (n / 1e4).toFixed(1) + '만';
    return n.toLocaleString();
  }

  function parseDuration(iso) {
    const m = iso.match(/PT(?:(\d+)H)?(?:(\d+)M)?(?:(\d+)S)?/);
    if (!m) return '';
    const h = parseInt(m[1] || 0), min = parseInt(m[2] || 0), s = parseInt(m[3] || 0);
    if (h > 0) return `${h}:${String(min).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
    return `${min}:${String(s).padStart(2,'0')}`;
  }

  function timeAgo(iso) {
    const diff = Date.now() - new Date(iso).getTime();
    const min = Math.floor(diff / 60000);
    if (min < 60) return `${min}분 전`;
    const h = Math.floor(min / 60);
    if (h < 24) return `${h}시간 전`;
    const d = Math.floor(h / 24);
    if (d < 30) return `${d}일 전`;
    const mo = Math.floor(d / 30);
    if (mo < 12) return `${mo}개월 전`;
    return `${Math.floor(mo/12)}년 전`;
  }

  function escHtml(str) {
    return String(str).replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;');
  }

  // ── Enter key ──
  document.addEventListener('DOMContentLoaded', () => {
    const savedKey = localStorage.getItem('yt_api_key');
    if (savedKey) {
      API_KEY = savedKey;
      document.getElementById('apiKeyInput').value = savedKey;
    }
    document.getElementById('apiKeyInput').addEventListener('keydown', e => {
      if (e.key === 'Enter') startApp();
    });
  });
</script>
</body>
</html>
