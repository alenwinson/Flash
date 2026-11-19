<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Flashcard Terminal // Alen's Archive</title>
  <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@500;700;900&family=Rajdhani:wght@500;600;700&display=swap" rel="stylesheet" />
  <style>
    :root {
      --bg: #030712;
      --panel: rgba(15, 23, 42, 0.85);
      --border: rgba(99, 102, 241, 0.3);
      --accent: #38bdf8;
      --green: #34d399;
      --red: #f43f5e;
      --amber: #fbbf24;
      --text: #f1f5f9;
      --text-dim: #94a3b8;
    }

    * { box-sizing: border-box; margin: 0; padding: 0; }
    body { background-color: var(--bg); color: var(--text); font-family: 'Rajdhani', sans-serif; min-height: 100vh; }
    
    .container { max-width: 900px; margin: 0 auto; padding: 24px 16px; }
    
    header { border-bottom: 1px solid var(--border); padding-bottom: 16px; margin-bottom: 24px; display: flex; justify-content: space-between; align-items: flex-end; }
    h1 { font-family: 'Orbitron', sans-serif; font-size: 1.8rem; background: linear-gradient(135deg, #fff 40%, var(--accent) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
    .subtitle { font-family: 'Orbitron', monospace; font-size: 0.75rem; color: var(--accent); letter-spacing: 2px; text-transform: uppercase; }

    .nav-tabs { display: flex; gap: 10px; margin-bottom: 24px; overflow-x: auto; padding-bottom: 8px; }
    .tab-btn { background: var(--panel); border: 1px solid var(--border); color: var(--text-dim); font-family: 'Orbitron', monospace; font-size: 0.8rem; padding: 10px 16px; border-radius: 6px; cursor: pointer; white-space: nowrap; transition: all 0.2s; }
    .tab-btn:hover { border-color: var(--accent); color: var(--accent); }
    .tab-btn.active { background: rgba(56, 189, 248, 0.15); border-color: var(--accent); color: #fff; font-weight: 700; }

    .view-panel { display: none; }
    .view-panel.active-view { display: block; animation: fadeIn 0.3s ease; }
    @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }

    /* EDITOR STYLES */
    .form-group { margin-bottom: 16px; }
    label { display: block; font-family: 'Orbitron', monospace; font-size: 0.75rem; color: var(--text-dim); margin-bottom: 6px; }
    input, select { width: 100%; background: var(--panel); border: 1px solid var(--border); border-radius: 6px; padding: 12px; color: #fff; font-family: 'Rajdhani', sans-serif; font-size: 1rem; outline: none; }
    input:focus, select:focus { border-color: var(--accent); box-shadow: 0 0 10px rgba(56, 189, 248, 0.2); }
    
    .rich-editor { width: 100%; min-height: 120px; background: var(--panel); border: 1px solid var(--border); border-radius: 6px; padding: 12px; color: #fff; font-size: 1.05rem; overflow-y: auto; outline: none; line-height: 1.5; }
    .rich-editor:focus { border-color: var(--accent); box-shadow: 0 0 10px rgba(56, 189, 248, 0.2); }
    .rich-editor img { max-width: 100%; max-height: 250px; border-radius: 6px; margin: 8px 0; border: 1px solid rgba(255,255,255,0.1); display: block; }
    
    .btn-submit { width: 100%; background: linear-gradient(135deg, var(--accent), #6366f1); color: #000; border: none; padding: 14px; font-family: 'Orbitron', monospace; font-weight: 700; font-size: 0.9rem; border-radius: 6px; cursor: pointer; transition: filter 0.2s; margin-top: 10px; }
    .btn-submit:hover { filter: brightness(1.2); }

    /* CALENDAR ARCHIVE */
    .calendar-wrap { background: var(--panel); border: 1px solid var(--border); border-radius: 10px; padding: 20px; margin-bottom: 24px; }
    .cal-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 16px; font-family: 'Orbitron', monospace; font-size: 1.1rem; }
    .cal-nav { background: transparent; border: 1px solid var(--border); color: var(--accent); padding: 4px 12px; border-radius: 6px; cursor: pointer; }
    .cal-grid { display: grid; grid-template-columns: repeat(7, 1fr); gap: 8px; text-align: center; }
    .cal-day-label { color: var(--text-dim); font-family: 'Orbitron', monospace; font-size: 0.75rem; padding-bottom: 8px; }
    .cal-day { padding: 12px 0; border-radius: 6px; cursor: pointer; font-weight: 600; background: rgba(0,0,0,0.3); border: 1px solid transparent; transition: all 0.2s; position: relative; }
    .cal-day:hover { border-color: var(--accent); background: rgba(56, 189, 248, 0.1); }
    .cal-day.has-cards { background: rgba(52, 211, 153, 0.15); border-color: rgba(52, 211, 153, 0.4); color: var(--green); }
    .cal-day.selected { background: var(--green); color: #000; font-weight: 700; box-shadow: 0 0 15px rgba(52, 211, 153, 0.4); }

    .card-list { display: flex; flex-direction: column; gap: 12px; }
    .list-item { background: var(--panel); border-left: 4px solid var(--accent); padding: 16px; border-radius: 6px; border-top: 1px solid var(--border); border-right: 1px solid var(--border); border-bottom: 1px solid var(--border); }
    .list-header { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 8px; font-weight: 700; font-size: 1.1rem; }
    .list-body { font-size: 0.95rem; color: var(--text-dim); margin-bottom: 12px; }
    .list-body img { max-height: 100px; max-width: 100%; border-radius: 4px; display: block; margin-top: 6px; }
    .btn-action { background: transparent; border: 1px solid var(--border); color: var(--text-dim); font-family: 'Orbitron', monospace; font-size: 0.65rem; padding: 4px 8px; border-radius: 4px; cursor: pointer; }
    .btn-action.del:hover { border-color: var(--red); color: var(--red); }

    /* REVISION TERMINAL (QUIZLET STYLE) */
    .rev-filters { display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 24px; }
    .rev-filter-btn { background: var(--panel); border: 1px solid var(--border); color: var(--text-dim); font-family: 'Orbitron', monospace; font-size: 0.75rem; padding: 8px 14px; border-radius: 6px; cursor: pointer; }
    .rev-filter-btn.active { background: rgba(56, 189, 248, 0.2); border-color: var(--accent); color: #fff; font-weight: 700; }

    .flashcard-stage { perspective: 1200px; width: 100%; min-height: 380px; margin-bottom: 24px; cursor: pointer; position: relative; }
    .flashcard { width: 100%; height: 100%; position: absolute; transform-style: preserve-3d; transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1); }
    .flashcard.flipped { transform: rotateY(180deg); }
    
    .fc-face { position: absolute; inset: 0; backface-visibility: hidden; background: var(--panel); border: 1px solid var(--border); border-radius: 12px; padding: 32px; display: flex; flex-direction: column; box-shadow: 0 10px 30px rgba(0,0,0,0.5); overflow-y: auto; }
    .fc-front { border-top: 4px solid var(--accent); }
    .fc-back { transform: rotateY(180deg); border-top: 4px solid var(--green); background: radial-gradient(circle at top, #111827, #030712); }
    
    .fc-header { font-family: 'Orbitron', monospace; font-size: 0.75rem; color: var(--text-dim); margin-bottom: 20px; display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid rgba(255,255,255,0.1); padding-bottom: 10px; }
    .fc-content { flex: 1; display: flex; align-items: center; justify-content: center; text-align: center; font-size: 1.4rem; font-weight: 600; line-height: 1.5; }
    .fc-back .fc-content { align-items: flex-start; text-align: left; font-size: 1.1rem; font-weight: 500; }
    .fc-content img { max-height: 200px; max-width: 100%; object-fit: contain; margin: 12px auto; border-radius: 6px; display: block; border: 1px solid rgba(255,255,255,0.1); }
    .fc-hint { font-family: 'Orbitron', monospace; font-size: 0.7rem; color: var(--text-dim); text-align: center; margin-top: 20px; }

    .fc-controls { display: flex; justify-content: space-between; gap: 12px; align-items: center; flex-wrap: wrap; }
    .nav-btn { background: var(--panel); border: 1px solid var(--border); color: #fff; font-family: 'Orbitron', monospace; font-size: 0.85rem; font-weight: 700; padding: 12px 24px; border-radius: 8px; cursor: pointer; }
    .grade-group { display: flex; gap: 12px; flex: 1; justify-content: center; min-width: 250px; }
    .grade-btn { flex: 1; max-width: 180px; padding: 12px; font-family: 'Orbitron', monospace; font-size: 0.8rem; font-weight: 700; border-radius: 8px; cursor: pointer; border: none; }
    .grade-unknown { background: rgba(244, 63, 94, 0.2); border: 1px solid var(--red); color: var(--red); }
    .grade-known { background: rgba(52, 211, 153, 0.2); border: 1px solid var(--green); color: var(--green); }
    
    .status-badge { padding: 3px 8px; border-radius: 4px; font-size: 0.65rem; font-weight: 700; }
    .s-untested { background: rgba(148, 163, 184, 0.2); color: var(--text-dim); }
    .s-known { background: rgba(52, 211, 153, 0.2); color: var(--green); }
    .s-unknown { background: rgba(244, 63, 94, 0.2); color: var(--red); }

    /* UTILS */
    .empty-state { text-align: center; padding: 40px 20px; color: var(--text-dim); background: var(--panel); border: 1px dashed var(--border); border-radius: 8px; }
  </style>
</head>
<body>

  <div class="container">
    <header>
      <div>
        <div class="subtitle">USMLE // Active Recall Terminal</div>
        <h1>FLASHCARD SYSTEM</h1>
      </div>
    </header>

    <div class="nav-tabs">
      <button class="tab-btn active" onclick="switchTab('upload')">+ CREATE CARD</button>
      <button class="tab-btn" onclick="switchTab('review')">REVISION TERMINAL</button>
      <button class="tab-btn" onclick="switchTab('calendar')">CALENDAR ARCHIVE</button>
      <button class="tab-btn" onclick="switchTab('settings')" style="margin-left: auto;">⚙️ SETTINGS</button>
    </div>

    <!-- VIEW 1: UPLOAD / CREATE CARD -->
    <div id="view-upload" class="view-panel active-view">
      <form id="flashcard-form" onsubmit="saveCard(event)">
        <input type="hidden" id="edit-id" value="" />
        
        <div style="display:flex; gap:16px;">
          <div class="form-group" style="flex: 1;">
            <label>CATEGORY</label>
            <select id="card-category">
              <option value="Medicine Concepts">Medicine Concepts</option>
              <option value="Pharmacology">Pharmacology</option>
              <option value="Pathology">Pathology</option>
              <option value="General Concepts">General Concepts</option>
            </select>
          </div>
          <div class="form-group" style="flex: 1;">
            <label>LOG DATE</label>
            <input type="date" id="card-date" required />
          </div>
        </div>

        <div class="form-group">
          <label>FRONT / QUESTION (Paste images here via Ctrl+V)</label>
          <div id="card-front" class="rich-editor" contenteditable="true" placeholder="e.g., Mechanism of Action for..."></div>
        </div>

        <div class="form-group">
          <label>BACK / ANSWER (Paste images here via Ctrl+V)</label>
          <div id="card-back" class="rich-editor" contenteditable="true" style="min-height: 200px;" placeholder="High-yield USMLE facts..."></div>
        </div>

        <button type="submit" id="submit-btn" class="btn-submit">SAVE FLASHCARD</button>
        <button type="button" id="cancel-btn" class="btn-action" style="display:none; width:100%; margin-top:10px; padding:10px;" onclick="cancelEdit()">CANCEL EDIT</button>
      </form>
    </div>

    <!-- VIEW 2: REVISION TERMINAL -->
    <div id="view-review" class="view-panel">
      <div class="rev-filters">
        <button class="rev-filter-btn active" id="filter-untested" onclick="setFilter('untested')">UNTESTED (<span id="count-untested">0</span>)</button>
        <button class="rev-filter-btn" id="filter-unknown" onclick="setFilter('unknown')">UNKNOWN / WEAK (<span id="count-unknown">0</span>)</button>
        <button class="rev-filter-btn" id="filter-known" onclick="setFilter('known')">KNOWN FACTS (<span id="count-known">0</span>)</button>
        <button class="rev-filter-btn" id="filter-all" onclick="setFilter('all')">ALL CARDS (<span id="count-all">0</span>)</button>
      </div>

      <div id="deck-status" style="font-family:'Orbitron', monospace; font-size:0.8rem; color:var(--accent); margin-bottom:12px;">// ACTIVE DECK</div>

      <div id="fc-container"></div>
    </div>

    <!-- VIEW 3: CALENDAR ARCHIVE -->
    <div id="view-calendar" class="view-panel">
      <div class="calendar-wrap">
        <div class="cal-header">
          <button class="cal-nav" onclick="changeMonth(-1)">&lt;</button>
          <span id="cal-month-year">Month Year</span>
          <button class="cal-nav" onclick="changeMonth(1)">&gt;</button>
        </div>
        <div class="cal-grid" id="cal-grid"></div>
      </div>

      <div style="font-family:'Orbitron', monospace; font-size:0.9rem; color:var(--accent); margin-bottom:12px;" id="archive-date-title">// SELECT A DATE TO VIEW CARDS</div>
      <div id="archive-list" class="card-list"></div>
    </div>

    <!-- VIEW 4: SETTINGS (IMGBB API) -->
    <div id="view-settings" class="view-panel">
      <div style="background:var(--panel); border:1px solid var(--border); border-radius:10px; padding:24px;">
        <h2 style="font-family:'Orbitron', sans-serif; font-size:1.2rem; color:var(--accent); margin-bottom:16px;">IMAGE HOSTING CONFIGURATION</h2>
        <p style="color:var(--text-dim); font-size:0.9rem; margin-bottom:20px; line-height:1.5;">
          Your master API key is currently hardcoded into the system. You can override it locally by pasting a new <strong>ImgBB API Key</strong> below, or leave it as is to continue using the master key.
        </p>
        <div class="form-group">
          <label>IMGBB API KEY OVERRIDE</label>
          <input type="password" id="imgbb-key-input" placeholder="e.g., 5fb1ab9140665b9496..." />
        </div>
        <button class="btn-submit" onclick="saveSettings()">SAVE SETTINGS</button>
      </div>
      
      <div style="margin-top: 24px; text-align: right;">
        <button class="btn-action" onclick="exportData()">💾 Export JSON Backup</button>
        <button class="btn-action del" onclick="clearAllData()">⚠️ Factory Reset</button>
      </div>
    </div>

  </div>

  <script>
    /* ================== CONFIGURATION ================== */
    // Hardcoded master key. Will be used if localStorage has no override.
    const MASTER_IMGBB_KEY = '5fb1ab9140665b9496cd0899c34c3069';

    /* ================== STATE MANAGEMENT ================== */
    function getDb() { return JSON.parse(localStorage.getItem('alen_flashcards') || '[]'); }
    function saveDb(data) { localStorage.setItem('alen_flashcards', JSON.stringify(data)); updateCounters(); }
    function getToday() { return new Intl.DateTimeFormat('en-CA').format(new Date()); }

    document.getElementById('card-date').value = getToday();
    let currentEditId = null;

    /* ================== IMGBB UPLOAD LOGIC ================== */
    document.querySelectorAll('.rich-editor').forEach(editor => {
      editor.addEventListener('paste', async (e) => {
        const clipboard = e.clipboardData || window.clipboardData;
        if (!clipboard || !clipboard.items) return;

        // Use localStorage override if present, otherwise fall back to master key
        const apiKey = localStorage.getItem('imgbb_api_key') || MASTER_IMGBB_KEY;

        for (let item of clipboard.items) {
          if (item.type.indexOf('image') !== -1) {
            e.preventDefault();
            
            const file = item.getAsFile();
            const selection = window.getSelection();
            if (!selection.rangeCount) return;
            const range = selection.getRangeAt(0);
            range.deleteContents();

            // Insert placeholder
            const placeholder = document.createElement('span');
            placeholder.style.color = 'var(--accent)';
            placeholder.textContent = '[Uploading screenshot...]';
            range.insertNode(placeholder);
            range.setStartAfter(placeholder);
            range.setEndAfter(placeholder);
            selection.removeAllRanges();
            selection.addRange(range);

            try {
              const formData = new FormData();
              formData.append('image', file);

              const res = await fetch(`https://api.imgbb.com/1/upload?key=${apiKey}`, {
                method: 'POST',
                body: formData
              });
              
              const json = await res.json();
              if (json.success) {
                const img = document.createElement('img');
                img.src = json.data.url;
                placeholder.parentNode.replaceChild(img, placeholder);
              } else {
                throw new Error(json.error.message || 'Upload failed');
              }
            } catch (err) {
              console.error(err);
              placeholder.textContent = `[Upload Error: ${err.message}]`;
              placeholder.style.color = 'var(--red)';
            }
          }
        }
      });
    });

    function saveSettings() {
      const key = document.getElementById('imgbb-key-input').value.trim();
      if (key === '') {
        localStorage.removeItem('imgbb_api_key');
        alert('Local override cleared. System will now use the master API key.');
      } else {
        localStorage.setItem('imgbb_api_key', key);
        alert('New local API key saved!');
      }
    }
    
    // Load existing local key into input if present, otherwise show the master key to indicate it's active
    document.getElementById('imgbb-key-input').value = localStorage.getItem('imgbb_api_key') || MASTER_IMGBB_KEY;

    /* ================== TAB NAVIGATION ================== */
    function switchTab(view) {
      document.querySelectorAll('.tab-btn').forEach(btn => btn.classList.remove('active'));
      document.querySelectorAll('.view-panel').forEach(panel => panel.classList.remove('active-view'));
      
      event.target.classList.add('active');
      document.getElementById(`view-${view}`).classList.add('active-view');

      if (view === 'calendar') renderCalendar();
      if (view === 'review') setFilter(currentFilter);
    }

    /* ================== CARD CREATION / EDITING ================== */
    // Helper to format LaTeX arrows on save
    function formatArrows(html) {
      return html.replace(/\\text\{([^}]+)\}/g, '$1')
                 .replace(/\\uparrow/gi, '↑').replace(/\\downarrow/gi, '↓')
                 .replace(/\\rightarrow/gi, '→').replace(/\\leftarrow/gi, '←')
                 .replace(/\$\s*([↑↓→←↔\w\s+\-*\/=<>%]+?)\s*\$/g, '$1');
    }

    function saveCard(e) {
      e.preventDefault();
      const front = formatArrows(document.getElementById('card-front').innerHTML.trim());
      const back = formatArrows(document.getElementById('card-back').innerHTML.trim());
      const category = document.getElementById('card-category').value;
      const date = document.getElementById('card-date').value;

      if (!front || !back) { alert("Both front and back must have content."); return; }

      let db = getDb();
      if (currentEditId) {
        db = db.map(c => c.id === currentEditId ? { ...c, front, back, category, date } : c);
        alert("Card updated successfully!");
      } else {
        db.push({ id: Date.now(), front, back, category, date, status: 'untested' });
        alert("Card added successfully!");
      }

      saveDb(db);
      cancelEdit();
      if (currentArchiveDate) renderArchiveList(currentArchiveDate);
    }

    function editCard(id) {
      const card = getDb().find(c => c.id === id);
      if (!card) return;
      
      currentEditId = id;
      document.getElementById('card-category').value = card.category;
      document.getElementById('card-date').value = card.date;
      document.getElementById('card-front').innerHTML = card.front;
      document.getElementById('card-back').innerHTML = card.back;
      
      document.getElementById('submit-btn').textContent = 'UPDATE FLASHCARD';
      document.getElementById('cancel-btn').style.display = 'block';
      
      switchTab('upload');
      document.querySelector('.tab-btn:nth-child(1)').classList.add('active');
    }

    function cancelEdit() {
      currentEditId = null;
      document.getElementById('flashcard-form').reset();
      document.getElementById('card-date').value = getToday();
      document.getElementById('card-front').innerHTML = '';
      document.getElementById('card-back').innerHTML = '';
      document.getElementById('submit-btn').textContent = 'SAVE FLASHCARD';
      document.getElementById('cancel-btn').style.display = 'none';
    }

    function deleteCard(id) {
      if (!confirm("Delete this flashcard?")) return;
      saveDb(getDb().filter(c => c.id !== id));
      if (currentArchiveDate) renderArchiveList(currentArchiveDate);
      if (document.getElementById('view-review').classList.contains('active-view')) setFilter(currentFilter);
    }

    /* ================== CALENDAR ARCHIVE ================== */
    let calDate = new Date();
    let currentArchiveDate = null;

    function changeMonth(delta) {
      calDate.setMonth(calDate.getMonth() + delta);
      renderCalendar();
    }

    function renderCalendar() {
      const grid = document.getElementById('cal-grid');
      grid.innerHTML = '';
      const year = calDate.getFullYear(), month = calDate.getMonth();
      document.getElementById('cal-month-year').textContent = `${["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"][month]} ${year}`;

      ['SUN', 'MON', 'TUE', 'WED', 'THU', 'FRI', 'SAT'].forEach(d => grid.innerHTML += `<div class="cal-day-label">${d}</div>`);
      
      const firstDay = new Date(year, month, 1).getDay();
      const totalDays = new Date(year, month + 1, 0).getDate();
      for (let i = 0; i < firstDay; i++) grid.appendChild(document.createElement('div'));

      const db = getDb();
      for (let day = 1; day <= totalDays; day++) {
        const ds = `${year}-${String(month + 1).padStart(2, '0')}-${String(day).padStart(2, '0')}`;
        const dayCards = db.filter(c => c.date === ds);
        
        const el = document.createElement('div');
        el.className = `cal-day ${dayCards.length ? 'has-cards' : ''} ${ds === currentArchiveDate ? 'selected' : ''}`;
        el.innerHTML = `${day} ${dayCards.length ? `<br><span style="font-size:0.6rem; color:inherit;">${dayCards.length} CARDS</span>` : ''}`;
        
        el.onclick = () => {
          currentArchiveDate = ds;
          renderCalendar();
          renderArchiveList(ds);
        };
        grid.appendChild(el);
      }
    }

    function renderArchiveList(dateStr) {
      const list = document.getElementById('archive-list');
      const cards = getDb().filter(c => c.date === dateStr);
      document.getElementById('archive-date-title').textContent = `// LOGS FOR: ${dateStr} [${cards.length}]`;
      
      if (!cards.length) {
        list.innerHTML = `<div class="empty-state">No flashcards recorded on this date.</div>`;
        return;
      }

      list.innerHTML = cards.map(c => `
        <div class="list-item">
          <div class="list-header">
            <span style="font-family:'Orbitron', monospace; font-size:0.75rem; color:var(--accent);">[${c.category.toUpperCase()}]</span>
            <div>
              <button class="btn-action" onclick="editCard(${c.id})">EDIT</button>
              <button class="btn-action del" onclick="deleteCard(${c.id})">DEL</button>
            </div>
          </div>
          <div class="list-body"><strong>Q:</strong> ${c.front}</div>
          <div class="list-body"><strong>A:</strong> ${c.back}</div>
        </div>
      `).join('');
    }

    /* ================== REVISION TERMINAL ================== */
    let currentFilter = 'untested';
    let activeDeck = [];
    let fcIndex = 0;
    let isFlipped = false;

    function updateCounters() {
      const db = getDb();
      document.getElementById('count-untested').textContent = db.filter(c => c.status === 'untested').length;
      document.getElementById('count-unknown').textContent = db.filter(c => c.status === 'unknown').length;
      document.getElementById('count-known').textContent = db.filter(c => c.status === 'known').length;
      document.getElementById('count-all').textContent = db.length;
    }

    function setFilter(filter) {
      currentFilter = filter;
      document.querySelectorAll('.rev-filter-btn').forEach(b => b.classList.remove('active'));
      document.getElementById(`filter-${filter}`).classList.add('active');
      
      const db = getDb();
      if (filter === 'all') activeDeck = db;
      else activeDeck = db.filter(c => c.status === filter);
      
      fcIndex = 0;
      isFlipped = false;
      renderFlashcard();
    }

    function renderFlashcard() {
      const container = document.getElementById('fc-container');
      const header = document.getElementById('deck-status');
      
      if (!activeDeck.length) {
        header.textContent = `// ACTIVE DECK: ${currentFilter.toUpperCase()} (0 CARDS)`;
        container.innerHTML = `<div class="empty-state">Deck is empty. Great job!</div>`;
        return;
      }

      const c = activeDeck[fcIndex];
      header.textContent = `// ACTIVE DECK: ${currentFilter.toUpperCase()} — CARD ${fcIndex + 1} OF ${activeDeck.length}`;

      const statusMap = { untested: 's-untested', known: 's-known', unknown: 's-unknown' };
      const statusTag = `<span class="status-badge ${statusMap[c.status]}">${c.status.toUpperCase()}</span>`;

      container.innerHTML = `
        <div class="flashcard-stage" onclick="toggleFlip(event)">
          <div class="flashcard ${isFlipped ? 'flipped' : ''}" id="fc-element">
            
            <!-- FRONT -->
            <div class="fc-face fc-front">
              <div class="fc-header">
                <span>// QUESTION</span>
                <div style="display:flex; gap:8px; align-items:center;">
                  ${statusTag}
                  <button class="btn-action" style="border-color:var(--accent); color:var(--accent);" onclick="editCardFromReview(${c.id}, event)">EDIT</button>
                </div>
              </div>
              <div class="fc-content">${c.front}</div>
              <div class="fc-hint">TAP SPACEBAR TO FLIP ⟳</div>
            </div>

            <!-- BACK -->
            <div class="fc-face fc-back">
              <div class="fc-header">
                <span style="color:var(--green);">// ANSWER</span>
                <div style="display:flex; gap:8px; align-items:center;">
                  ${statusTag}
                  <button class="btn-action" style="border-color:var(--accent); color:var(--accent);" onclick="editCardFromReview(${c.id}, event)">EDIT</button>
                </div>
              </div>
              <div class="fc-content">${c.back}</div>
            </div>
          </div>
        </div>

        <div class="fc-controls">
          <button class="nav-btn" onclick="navCard(-1)">&larr; PREV</button>
          <div class="grade-group">
            <button class="grade-btn grade-unknown" onclick="gradeCard(${c.id}, 'unknown')">✗ UNKNOWN</button>
            <button class="grade-btn grade-known" onclick="gradeCard(${c.id}, 'known')">✓ KNEW IT</button>
          </div>
          <button class="nav-btn" onclick="navCard(1)">NEXT &rarr;</button>
        </div>
      `;
    }

    function toggleFlip(e) {
      if (e && (e.target.tagName === 'BUTTON' || e.target.closest('button'))) return;
      isFlipped = !isFlipped;
      const el = document.getElementById('fc-element');
      if (el) el.classList.toggle('flipped', isFlipped);
    }

    function navCard(dir) {
      isFlipped = false;
      fcIndex += dir;
      if (fcIndex >= activeDeck.length) fcIndex = 0;
      if (fcIndex < 0) fcIndex = activeDeck.length - 1;
      renderFlashcard();
    }

    function gradeCard(id, status) {
      let db = getDb();
      db = db.map(c => c.id === id ? { ...c, status } : c);
      saveDb(db);
      
      // Remove from current view if it no longer matches the filter (unless we are viewing 'all')
      if (currentFilter !== 'all' && currentFilter !== status) {
        activeDeck = activeDeck.filter(c => c.id !== id);
        if (fcIndex >= activeDeck.length) fcIndex = 0;
      } else {
        // If it stays in the deck, update the local object so the badge changes
        activeDeck[fcIndex].status = status;
        fcIndex++;
        if (fcIndex >= activeDeck.length) fcIndex = 0;
      }
      
      isFlipped = false;
      renderFlashcard();
    }

    function editCardFromReview(id, e) {
      e.stopPropagation();
      editCard(id);
    }

    /* KEYBOARD SHORTCUTS */
    window.addEventListener('keydown', (e) => {
      if (document.getElementById('view-review').classList.contains('active-view') && activeDeck.length) {
        if (e.target.isContentEditable || e.target.tagName === 'INPUT') return;
        if (e.code === 'Space') { e.preventDefault(); toggleFlip(); }
        if (e.code === 'ArrowRight') navCard(1);
        if (e.code === 'ArrowLeft') navCard(-1);
      }
    });

    /* ================== UTILS ================== */
    function exportData() {
      const blob = new Blob([JSON.stringify(getDb(), null, 2)], { type: 'application/json' });
      const a = document.createElement('a');
      a.href = URL.createObjectURL(blob); 
      a.download = `flashcards_backup_${getToday()}.json`;
      a.click();
    }

    function clearAllData() {
      if (confirm("WARNING: This will delete ALL flashcards permanently! Are you sure?")) {
        localStorage.removeItem('alen_flashcards');
        alert("Database cleared.");
        location.reload();
      }
    }

    // Initialize counters on load
    updateCounters();
  </script>
</body>
</html>
