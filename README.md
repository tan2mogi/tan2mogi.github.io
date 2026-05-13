# tan2mogi.github.io
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>あだ名 被り・類似判定ツール</title>
    <script src="https://cdn.jsdelivr.net/npm/kuromoji@0.1.2/build/kuromoji.js"></script>
    <style>
        body {
            font-family: 'Helvetica Neue', Arial, 'Hiragino Kaku Gothic ProN', 'Hiragino Sans', Meiryo, sans-serif;
            background-color: #f7f9fc;
            color: #333;
            padding: 20px;
            margin: 0;
            padding-bottom: 50px; 
            overflow-x: hidden; 
        }
        
        .layout-wrapper {
            display: flex;
            justify-content: center;
            align-items: flex-start;
            gap: 40px; 
            max-width: 1200px; 
            margin: 0 auto;
            transition: all 0.4s cubic-bezier(0.25, 0.8, 0.25, 1);
        }

        .container {
            width: 100%;
            max-width: 600px;
            background: #fff;
            padding: 25px;
            border-radius: 12px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.05);
            box-sizing: border-box;
            position: relative;
            flex-shrink: 0;
        }

        h1 {
            text-align: center;
            font-size: 22px;
            color: #2c3e50;
            margin-top: 0;
            margin-bottom: 15px;
            padding: 0 40px; 
        }

        .help-btn {
            position: absolute;
            top: 20px;
            left: 20px;
            font-size: 20px;
            font-weight: bold;
            cursor: pointer;
            color: #64748b;
            background: none;
            border: 2px solid #cbd5e1;
            border-radius: 50%;
            width: 32px;
            height: 32px;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.2s;
            user-select: none;
        }
        .help-btn:hover {
            color: #3b82f6;
            border-color: #3b82f6;
            background-color: #f0f9ff;
        }

        .hamburger-btn {
            position: absolute;
            top: 20px;
            right: 20px;
            font-size: 24px;
            cursor: pointer;
            color: #64748b;
            background: none;
            border: none;
            padding: 5px;
            line-height: 1;
            transition: color 0.2s;
            user-select: none;
        }
        .hamburger-btn:hover { color: #3b82f6; }

        p {
            font-size: 14px;
            color: #555;
            line-height: 1.6;
            margin-bottom: 20px;
        }
        .input-group {
            margin-bottom: 5px;
            display: flex;
            width: 100%;
        }

        input[type="text"] {
            flex: 1;
            padding: 20px;
            font-size: 24px;
            font-weight: bold;
            border: 2px solid #cbd5e1;
            border-radius: 8px;
            box-sizing: border-box;
            width: 100%;
            transition: border-color 0.3s, box-shadow 0.3s;
            text-align: center;
        }
        input[type="text"]:focus {
            outline: none;
            border-color: #3b82f6;
            box-shadow: 0 0 10px rgba(59, 130, 246, 0.2);
        }

        .status-text {
            font-size: 12px;
            color: #64748b;
            text-align: right;
            margin-bottom: 20px;
            height: 18px;
            transition: opacity 0.5s ease;
        }
        
        .result {
            padding: 20px;
            border-radius: 8px;
            font-weight: bold;
            line-height: 1.6;
            word-break: break-word;
        }
        .error { background-color: #fef2f2; color: #ef4444; border: 1px solid #fecaca; }
        .warning { background-color: #fffbeb; color: #f59e0b; border: 1px solid #fde68a; }
        .success { background-color: #f0fdf4; color: #22c55e; border: 1px solid #bbf7d0; }

        .match-list-container {
            max-height: 250px;
            overflow-y: auto;
            margin-top: 15px;
            padding-right: 5px;
            border-top: 1px dashed #cbd5e1;
            padding-top: 10px;
        }

        .list-item {
            font-weight: normal;
            margin-top: 8px;
            padding-left: 10px;
            border-bottom: 1px dashed #cbd5e1;
            padding-bottom: 8px;
            display: flex;
            align-items: center;
            flex-wrap: wrap; 
            gap: 8px;
            color: #334155;
        }
        .list-item:last-child { border-bottom: none; }
        
        .dept-badge {
            display: inline-block;
            font-size: 0.85em;
            padding: 4px 10px;
            border-radius: 20px;
            white-space: nowrap; 
            font-weight: bold;
        }
        .badge-mogi { background-color: #dbeafe; color: #1e40af; }
        .badge-talent { background-color: #fee2e2; color: #b91c1c; }
        .badge-kikaku { background-color: #dcfce7; color: #15803d; }
        .badge-shougai { background-color: #fce7f3; color: #be185d; }
        .badge-jousen { background-color: #f3e8ff; color: #7e22ce; }
        .badge-kokusai { background-color: #fef9c3; color: #a16207; }
        .badge-soumu { background-color: #f1f5f9; color: #475569; }
        .badge-pinned { background-color: #f8fafc; color: #0f172a; border: 1px solid #cbd5e1; } 

        .pin-btn {
            margin-top: 15px;
            margin-bottom: 5px;
            padding: 12px;
            background-color: #3b82f6;
            color: white;
            border: none;
            border-radius: 6px;
            cursor: pointer;
            font-weight: bold;
            font-size: 15px;
            width: 100%;
            transition: background-color 0.2s;
        }
        .pin-btn:hover { background-color: #2563eb; }

        /* サイドバーラッパー */
        .sidebar-wrapper {
            width: 0;
            opacity: 0;
            overflow: hidden;
            transition: width 0.4s cubic-bezier(0.25, 0.8, 0.25, 1), opacity 0.3s ease;
            flex-shrink: 0;
        }
        .sidebar-wrapper.visible {
            width: 320px;
            opacity: 1;
            overflow: visible; 
        }

        .sidebar {
            width: 320px;
            background: #fff;
            padding: 20px;
            border-radius: 12px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.05);
            display: flex;
            flex-direction: column;
            max-height: calc(100vh - 40px);
            overflow-y: auto;
            box-sizing: border-box;
        }

        .sidebar-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 2px solid #e2e8f0;
            padding-bottom: 10px;
            margin-bottom: 10px;
        }
        .sidebar-header h3 {
            font-size: 16px;
            color: #334155;
            margin: 0;
        }

        .header-actions {
            display: flex;
            align-items: center;
            gap: 12px;
        }

        .mobile-select-btn {
            display: none;
            padding: 6px 16px;
            font-size: 13px;
            font-weight: bold;
            border: 2px solid #cbd5e1;
            border-radius: 20px;
            background-color: #fff;
            color: #64748b;
            cursor: pointer;
            transition: all 0.2s;
        }
        .mobile-select-btn.active {
            background-color: #3b82f6;
            color: #fff;
            border-color: #3b82f6;
        }

        .delete-all-btn {
            background: none;
            border: none;
            color: #94a3b8;
            font-size: 12px;
            cursor: pointer;
            text-decoration: underline;
            padding: 0;
            transition: color 0.2s;
        }
        .delete-all-btn:hover { color: #ef4444; }

        .action-bar-container {
            height: 48px; 
            margin-bottom: 10px;
            transition: height 0.3s;
        }
        .action-bar {
            display: flex;
            gap: 8px;
            padding: 10px;
            background-color: #eff6ff;
            border-radius: 8px;
            border: 1px solid #bfdbfe;
            align-items: center;
            opacity: 0;
            visibility: hidden;
            transition: all 0.2s;
            height: 100%;
            box-sizing: border-box;
        }
        .action-bar.active {
            opacity: 1;
            visibility: visible;
        }
        .action-bar span {
            font-size: 13px;
            color: #1e3a8a;
            font-weight: bold;
            flex: 1;
        }
        .action-btn {
            padding: 6px 10px;
            font-size: 12px;
            font-weight: bold;
            border: none;
            border-radius: 4px;
            cursor: pointer;
        }
        .btn-copy { background-color: #3b82f6; color: white; }
        .btn-delete { background-color: #ef4444; color: white; }

        .pinned-list {
            display: flex;
            flex-direction: column; 
            gap: 10px;
        }
        .pinned-badge {
            display: flex;
            justify-content: space-between;
            align-items: center;
            background-color: #f8fafc;
            border: 2px solid #cbd5e1;
            padding: 10px 14px;
            border-radius: 8px;
            font-weight: bold;
            color: #0f172a;
            font-size: 15px;
            box-shadow: 0 1px 3px rgba(0,0,0,0.05);
            word-break: break-all;
            position: relative;
            cursor: pointer;
            transition: all 0.2s;
            user-select: none;
        }
        .pinned-badge:hover { border-color: #94a3b8; }
        
        .pinned-badge.selected {
            background-color: #eff6ff;
            border-color: #3b82f6;
            color: #1d4ed8;
        }

        .remove-pin {
            margin-left: 10px;
            color: #94a3b8;
            font-size: 14px;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            width: 24px;
            height: 24px;
            border-radius: 50%;
            transition: background-color 0.2s, color 0.2s;
            flex-shrink: 0;
        }
        .remove-pin:hover {
            background-color: #fee2e2;
            color: #ef4444;
        }

        /* ツールチップ */
        .pin-tooltip {
            position: fixed; 
            background: #fff;
            border: 2px solid #cbd5e1;
            box-shadow: 0 10px 30px rgba(0,0,0,0.15);
            padding: 15px;
            border-radius: 12px;
            z-index: 9999;
            font-size: 14px;
            display: none;
            width: 280px;
            max-height: 350px;
            overflow-y: auto;
            pointer-events: none; 
            animation: fadeIn 0.2s ease-out;
        }
        @keyframes fadeIn {
            from { opacity: 0; transform: translateX(-10px); }
            to { opacity: 1; transform: translateX(0); }
        }
        .pin-tooltip h4 {
            margin: 0 0 10px 0;
            font-size: 14px;
            border-bottom: 1px dashed #cbd5e1;
            padding-bottom: 8px;
        }
        .pin-tooltip ul {
            margin: 0; padding: 0; list-style: none; color: #334155;
        }
        .pin-tooltip li {
            margin-bottom: 8px; display: flex; align-items: center; gap: 6px; flex-wrap: wrap;
        }
        .pin-tooltip .no-similars {
            color: #10b981; font-weight: bold; text-align: center;
        }

        /* ヒント（トースト通知） */
        .toast-hint {
            position: fixed;
            bottom: 30px;
            left: 50%;
            transform: translateX(-50%) translateY(20px);
            background: rgba(30, 41, 59, 0.95);
            color: #fff;
            padding: 12px 20px;
            border-radius: 30px;
            font-size: 14px;
            font-weight: bold;
            box-shadow: 0 10px 25px rgba(0,0,0,0.2);
            z-index: 10000;
            opacity: 0;
            visibility: hidden;
            transition: all 0.4s cubic-bezier(0.25, 0.8, 0.25, 1);
            pointer-events: none;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        .toast-hint.show {
            opacity: 1;
            visibility: visible;
            transform: translateX(-50%) translateY(0);
            pointer-events: auto;
        }
        .toast-hint-btn {
            background: #3b82f6;
            border: none;
            color: white;
            padding: 6px 12px;
            border-radius: 20px;
            cursor: pointer;
            font-weight: bold;
            transition: background 0.2s;
        }
        .toast-hint-btn:hover { background: #2563eb; }

        /* チュートリアルポップアップ */
        .modal-overlay {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.6);
            display: flex; justify-content: center; align-items: center;
            z-index: 10000;
            opacity: 0; visibility: hidden;
            transition: opacity 0.3s, visibility 0.3s;
        }
        .modal-overlay.active {
            opacity: 1; visibility: visible;
        }
        .modal-content {
            background: #fff;
            padding: 30px;
            border-radius: 12px;
            max-width: 550px; width: 90%;
            max-height: 90vh; overflow-y: auto;
            box-shadow: 0 10px 25px rgba(0,0,0,0.2);
            position: relative;
        }
        .modal-content h2 {
            margin-top: 0; color: #2c3e50; font-size: 22px; text-align: center;
            border-bottom: 2px solid #e2e8f0; padding-bottom: 15px;
        }
        .tutorial-step { margin-bottom: 20px; }
        .tutorial-step h3 {
            color: #3b82f6; font-size: 16px; margin: 0 0 8px 0; display: flex; align-items: center; gap: 6px;
        }
        .tutorial-step p {
            margin: 0; color: #334155; font-size: 14px; line-height: 1.6; padding-left: 24px;
        }
        .modal-close-btn {
            display: block; width: 100%; padding: 14px; margin-top: 25px;
            background-color: #3b82f6; color: white; border: none; border-radius: 8px;
            font-size: 16px; font-weight: bold; cursor: pointer; transition: background-color 0.2s;
        }
        .modal-close-btn:hover { background-color: #2563eb; }

        .creator-text {
            position: fixed; bottom: 10px; right: 15px; font-size: 11px; color: #cbd5e1; user-select: none; z-index: 100;
        }

        /* レスポンシブ */
        @media (max-width: 1150px) {
            .layout-wrapper {
                flex-direction: column;
                align-items: center;
                gap: 20px;
            }
            .sidebar-wrapper { width: 100%; overflow: hidden; }
            .sidebar-wrapper.visible { width: 100%; }
            .sidebar {
                width: 100%; max-width: 600px; margin: 0 auto; max-height: none;
            }
            .mobile-select-btn { display: block; } 
            
            .action-bar-container { height: 0; margin-bottom: 0; }
            .action-bar-container.show { height: 48px; margin-bottom: 10px; }
            
            .pinned-list { flex-direction: row; flex-wrap: wrap; }
            .pinned-badge { flex: 0 0 auto; }
            input[type="text"] { font-size: 20px; padding: 16px; }
            
            .pin-tooltip {
                left: 50% !important; top: auto !important; bottom: 20px !important;
                transform: translateX(-50%); width: 90%; max-width: 400px;
                animation: fadeInMobile 0.2s ease-out;
            }
            @keyframes fadeInMobile {
                from { opacity: 0; transform: translate(-50%, 10px); }
                to { opacity: 1; transform: translate(-50%, 0); }
            }
        }
    </style>
</head>
<body>

<script>
    // ==========================================
    // 💡 1. エラー防止の最優先：UIコントロール関数
    // ==========================================
    // 万が一以降の処理でエラーが起きても、「はじめる」ボタン等が機能するように最初に定義します。
    window.closeTutorial = function() {
        const modal = document.getElementById('tutorialModal');
        if (modal) {
            modal.classList.remove('active');
            try { 
                localStorage.setItem('hasSeenTutorial', 'true'); 
            } catch(e) { 
                console.warn("LocalStorage access failed:", e); 
            }
        }
    };

    window.openTutorial = function() {
        const modal = document.getElementById('tutorialModal');
        if(modal) modal.classList.add('active');
    };

    window.hideToast = function() {
        const toast = document.getElementById('toastHint');
        if(toast) toast.classList.remove('show');
    };

    window.hidePinTooltip = function() {
        const tooltip = document.getElementById('pinTooltip');
        if(tooltip) tooltip.style.display = 'none';
    };
</script>

<div class="layout-wrapper">
    <div class="container">
        <button class="help-btn" onclick="openTutorial()" title="使い方を見る">?</button>
        <button class="hamburger-btn" onclick="toggleSidebar()" title="ピン留めリストを開閉">☰</button>
        
        <h1>あだ名 被り・類似判定ツール</h1>
        <p>新しいあだ名を入力すると、既存のあだ名と「被り」や「類似（音の近さ、1文字違いなど）」がないかリアルタイムで確認します。</p>
        
        <div class="input-group">
            <input type="text" id="nicknameInput" placeholder="あだ名を入力" autocomplete="off" oninput="checkNickname()">
        </div>
        <div id="statusText" class="status-text">データ準備中...</div>
        
        <div id="resultArea"></div>
    </div>

    <div id="sidebarWrapper" class="sidebar-wrapper hidden">
        <div id="sidebar" class="sidebar">
            <div class="sidebar-header">
                <h3>📌 ピン留め <span id="pinCount" style="font-weight:normal; font-size:0.85em; color:#64748b;"></span></h3>
                <div class="header-actions">
                    <button id="mobileSelectBtn" class="mobile-select-btn" onclick="toggleMobileMode()">選択</button>
                    <button class="delete-all-btn" onclick="deleteAllPins()">全削除</button>
                </div>
            </div>
            
            <div id="actionBarContainer" class="action-bar-container">
                <div id="actionBar" class="action-bar">
                    <span id="selectedCount">0件選択中</span>
                    <button class="action-btn btn-copy" onclick="copySelectedPins()">コピー</button>
                    <button class="action-btn btn-delete" onclick="deleteSelectedPins()">削除</button>
                </div>
            </div>

            <div id="pinnedList" class="pinned-list">
                <span style="color:#94a3b8; font-size: 14px;">ピン留めはありません</span>
            </div>
        </div>
    </div>
</div>

<div id="pinTooltip" class="pin-tooltip"></div>

<div id="toastHint" class="toast-hint">
    <span id="toastHintText"></span>
    <button id="toastHintBtn" class="toast-hint-btn" style="display:none;"></button>
</div>

<div id="tutorialModal" class="modal-overlay">
    <div class="modal-content">
        <h2>🔰 このツールの使い方</h2>
        
        <div class="tutorial-step">
            <h3>1. あだ名を入力しよう</h3>
            <p>調べたいあだ名を入力すると、既存データやピン留めと被りがないか自動で判定します。<br>※漢字も対応していますが、<b>ひらがな</b>で入力したほうが精度が高くなります。</p>
        </div>
        
        <div class="tutorial-step">
            <h3>2. 📌 ピン留めでキープ</h3>
            <p>「決定（ピン留め）」ボタン、または入力欄で <b>Ctrl + Enter</b>（Macは <b>Cmd + Enter</b>）を押すとストックできます。<br>
            <b>★保存されたピン留めはリロードしたり後日開いても消えません！</b></p>
        </div>
        
        <div class="tutorial-step">
            <h3>3. 類似の再確認（ホバー / タップ）</h3>
            <p>PCではピン留めに<b>少しマウスを乗せて待つ</b>と、スマホではそのまま<b>タップ</b>するだけで、そのあだ名の「類似名」を再確認できます。</p>
        </div>

        <div class="tutorial-step">
            <h3>4. ☑️ 複数選択と一括操作</h3>
            <p>PCでは <b>Ctrl/Cmd</b> や <b>Shift</b> を押しながらクリックで複数選択が可能です。キーボードの<b>Shift＋十字キー</b>でも選択できます。<br>
            スマホでは右上の<b>「選択」ボタンを青色（オン）</b>にすると選択モードになり、タップするだけで選べます。<br>まとめてコピー・削除する時に便利です。</p>
        </div>

        <button class="modal-close-btn" onclick="closeTutorial()">はじめる</button>
    </div>
</div>

<div class="creator-text">created by たんたん</div>

<script>
    // ==========================================
    // 💡 2. データ定義
    // ==========================================
    const customReadingDict = { "年": "ねん", "翠": "すい" };
    const existingData = [
        {"名前":"るせ","所属局":"模擬","読み":"るせ"},
        {"名前":"テオ","所属局":"模擬","読み":"てお"},
        {"名前":"リコピン","所属局":"模擬","読み":"りこぴん"},
        {"名前":"ごはん","所属局":"模擬","読み":"ごはん"},
        {"名前":"華チ","所属局":"模擬","読み":"はなち"},
        {"名前":"れお","所属局":"模擬","読み":"れお"},
        {"名前":"りんりん","所属局":"模擬","読み":"りんりん"},
        {"名前":"まめっツ","所属局":"模擬","読み":"まめっつ"},
        {"名前":"むび","所属局":"模擬","読み":"むび"},
        {"名前":"つな","所属局":"模擬","読み":"つな"},
        {"名前":"スリンキー","所属局":"模擬","読み":"すりんきー"},
        {"名前":"めろ","所属局":"模擬","読み":"めろ"},
        {"名前":"ぷりん","所属局":"模擬","読み":"ぷりん"},
        {"名前":"ぴっふぃー","所属局":"模擬","読み":"ぴっふぃー"},
        {"名前":"みょんちぃ","所属局":"模擬","読み":"みょんちぃ"},
        {"名前":"べべ","所属局":"模擬","読み":"べべ"},
        {"名前":"りっか","所属局":"模擬","読み":"りっか"},
        {"名前":"るこ","所属局":"模擬","読み":"るこ"},
        {"名前":"翠","所属局":"模擬","読み":"すい"},
        {"名前":"そる","所属局":"模擬","読み":"そる"},
        {"名前":"るっち","所属局":"模擬","読み":"るっち"},
        {"名前":"ルカ","所属局":"模擬","読み":"るか"},
        {"名前":"ぺぺち","所属局":"模擬","読み":"ぺぺち"},
        {"名前":"ほかっちゃ","所属局":"模擬","読み":"ほかっちゃ"},
        {"名前":"ねん","所属局":"模擬","読み":"ねん"},
        {"名前":"さっつん","所属局":"模擬","読み":"さっつん"},
        {"名前":"さん","所属局":"模擬","読み":"さん"},
        {"名前":"リーナ","所属局":"模擬","読み":"りーな"},
        {"名前":"すいみぃ","所属局":"模擬","読み":"すいみぃ"},
        {"名前":"しお","所属局":"模擬","読み":"しお"},
        {"名前":"ともとも","所属局":"模擬","読み":"ともとも"},
        {"名前":"まるちゃ","所属局":"模擬","読み":"まるちゃ"},
        {"名前":"ぽぽ","所属局":"模擬","読み":"ぽぽ"},
        {"名前":"もな","所属局":"模擬","読み":"もな"},
        {"名前":"めいめい","所属局":"模擬","読み":"めいめい"},
        {"名前":"なち","所属局":"模擬","読み":"なち"},
        {"名前":"サカリキ","所属局":"模擬","読み":"さかりき"},
        {"名前":"たんたん","所属局":"模擬","読み":"たんたん"},
        {"名前":"あいす","所属局":"模擬","読み":"あいす"},
        {"名前":"にょん","所属局":"模擬","読み":"にょん"},
        {"名前":"みさす","所属局":"模擬","読み":"みさす"},
        {"名前":"がんちゃん","所属局":"タレント","読み":"がんちゃん"},
        {"名前":"ショーン","所属局":"タレント","読み":"しょーん"},
        {"名前":"なちょす","所属局":"タレント","読み":"なちょす"},
        {"名前":"ぴさ","所属局":"タレント","読み":"ぴさ"},
        {"名前":"りぃたん","所属局":"タレント","読み":"りぃたん"},
        {"名前":"みぷぅ","所属局":"タレント","読み":"みぷぅ"},
        {"名前":"ぷあ","所属局":"タレント","読み":"ぷあ"},
        {"名前":"アルミ","所属局":"タレント","読み":"あるみ"},
        {"名前":"ぷにょん","所属局":"タレント","読み":"ぷにょん"},
        {"名前":"まなみん","所属局":"タレント","読み":"まなみん"},
        {"名前":"ちゃむ","所属局":"タレント","読み":"ちゃむ"},
        {"名前":"いっちぇー","所属局":"タレント","読み":"いっちぇー"},
        {"名前":"るぅ","所属局":"タレント","読み":"るぅ"},
        {"名前":"メジっぴ","所属局":"タレント","読み":"めじっぴ"},
        {"名前":"テッシー","所属局":"タレント","読み":"てっしー"},
        {"名前":"わちゃめーん","所属局":"タレント","読み":"わちゃめーん"},
        {"名前":"きょろちゃん","所属局":"タレント","読み":"きょろちゃん"},
        {"名前":"くらん","所属局":"タレント","読み":"くらん"},
        {"名前":"ちょぬ","所属局":"タレント","読み":"ちょぬ"},
        {"名前":"りゃん","所属局":"タレント","読み":"りゃん"},
        {"名前":"りんな","所属局":"タレント","読み":"りんな"},
        {"名前":"なひょん","所属局":"タレント","読み":"なひょん"},
        {"名前":"よな","所属局":"タレント","読み":"よな"},
        {"名前":"リラックマ","所属局":"タレント","読み":"りらっくま"},
        {"名前":"くっぴー","所属局":"タレント","読み":"くっぴー"},
        {"名前":"うらやす","所属局":"タレント","読み":"うらやす"},
        {"名前":"ぷりめりょ","所属局":"タレント","読み":"ぷりめりょ"},
        {"名前":"じょな","所属局":"タレント","読み":"じょな"},
        {"名前":"さやちん","所属局":"タレント","読み":"さやちん"},
        {"名前":"しげさん","所属局":"企画","読み":"しげさん"},
        {"名前":"ちゃがわ","所属局":"企画","読み":"ちゃがわ"},
        {"名前":"そんみょん","所属局":"企画","読み":"そんみょん"},
        {"名前":"らこ","所属局":"企画","読み":"らこ"},
        {"名前":"バニ","所属局":"企画","読み":"ばに"},
        {"名前":"MON","所属局":"企画","読み":"もん"},
        {"名前":"弓べぇ","所属局":"企画","読み":"ゆみべぇ"},
        {"名前":"ぷぃ","所属局":"企画","読み":"ぷぃ"},
        {"名前":"うぉる","所属局":"企画","読み":"うぉる"},
        {"名前":"まっちゃ","所属局":"企画","読み":"まっちゃ"},
        {"名前":"めろ","所属局":"企画","読み":"めろ"},
        {"名前":"ヒョンちゃん","所属局":"企画","読み":"ひょんちゃん"},
        {"名前":"こん","所属局":"企画","読み":"こん"},
        {"名前":"あずりん","所属局":"企画","読み":"あずりん"},
        {"名前":"まーち","所属局":"企画","読み":"まーち"},
        {"名前":"チワワ","所属局":"企画","読み":"ちわわ"},
        {"名前":"ぶんちゃん","所属局":"企画","読み":"ぶんちゃん"},
        {"名前":"GALAXY","所属局":"企画","読み":"ぎゃらくしー"},
        {"名前":"らす","所属局":"企画","読み":"らす"},
        {"名前":"ばぅんでぃ","所属局":"企画","読み":"ばぅんでぃ"},
        {"名前":"きゃす","所属局":"企画","読み":"きゃす"},
        {"名前":"じゃんぷ","所属局":"企画","読み":"じゃんぷ"},
        {"名前":"との","所属局":"企画","読み":"との"},
        {"名前":"なべさん","所属局":"企画","読み":"なべさん"},
        {"名前":"うな","所属局":"渉外","読み":"うな"},
        {"名前":"むぅ","所属局":"渉外","読み":"むぅ"},
        {"名前":"はち","所属局":"渉外","読み":"はち"},
        {"名前":"松田","所属局":"渉外","読み":"まつだ"},
        {"名前":"こてぃ","所属局":"渉外","読み":"こてぃ"},
        {"名前":"エルフィー","所属局":"渉外","読み":"えるふぃー"},
        {"名前":"しるふぃー","所属局":"渉外","読み":"しるふぃー"},
        {"名前":"たくあん","所属局":"渉外","読み":"たくあん"},
        {"名前":"くろ","所属局":"渉外","読み":"くろ"},
        {"名前":"ゆり","所属局":"渉外","読み":"ゆり"},
        {"名前":"えご","所属局":"渉外","読み":"えご"},
        {"名前":"やまパン","所属局":"情報宣伝","読み":"やまぱん"},
        {"名前":"本仕込み","所属局":"情報宣伝","読み":"ほんじこみ"},
        {"名前":"神Ｐ","所属局":"情報宣伝","読み":"かみぴー"},
        {"名前":"かげむぅ","所属局":"情報宣伝","読み":"かげむぅ"},
        {"名前":"天城","所属局":"情報宣伝","読み":"あまぎ"},
        {"名前":"えりー","所属局":"情報宣伝","読み":"えりー"},
        {"名前":"ましゅ","所属局":"情報宣伝","読み":"ましゅ"},
        {"名前":"もくろー","所属局":"情報宣伝","読み":"もくろー"},
        {"名前":"＠ZEち","所属局":"情報宣伝","読み":"ぜち"},
        {"名前":"憩","所属局":"情報宣伝","読み":"いこい"},
        {"名前":"さりー","所属局":"情報宣伝","読み":"さりー"},
        {"名前":"えび","所属局":"情報宣伝","読み":"えび"},
        {"名前":"ちゃむ","所属局":"情報宣伝","読み":"ちゃむ"},
        {"名前":"すーみん","所属局":"情報宣伝","読み":"すーみん"},
        {"名前":"みりん","所属局":"情報宣伝","読み":"みりん"},
        {"名前":"おと","所属局":"情報宣伝","読み":"おと"},
        {"名前":"ゆみりん","所属局":"情報宣伝","読み":"ゆみりん"},
        {"名前":"みかん","所属局":"情報宣伝","読み":"みかん"},
        {"名前":"めぐりん","所属局":"情報宣伝","読み":"めぐりん"},
        {"名前":"でっち","所属局":"情報宣伝","読み":"でっち"},
        {"名前":"をんぬ。","所属局":"情報宣伝","読み":"をんぬ"},
        {"名前":"のんのん","所属局":"情報宣伝","読み":"のんのん"},
        {"名前":"ぽみ","所属局":"情報宣伝","読み":"ぽみ"},
        {"名前":"ちょっちょ","所属局":"情報宣伝","読み":"ちょっちょ"},
        {"名前":"みーまん","所属局":"情報宣伝","読み":"みーまん"},
        {"名前":"しゅーくりーむ","所属局":"国際関係","読み":"しゅーくりーむ"},
        {"名前":"よっしー↑","所属局":"国際関係","読み":"よっしー"},
        {"名前":"おりん","所属局":"国際関係","読み":"おりん"},
        {"名前":"ちゃりけっと","所属局":"国際関係","読み":"ちゃりけっと"},
        {"名前":"れーと","所属局":"国際関係","読み":"れーと"},
        {"名前":"アリス","所属局":"国際関係","読み":"ありす"},
        {"名前":"すみ","所属局":"国際関係","読み":"すみ"},
        {"名前":"じるちゃん","所属局":"国際関係","読み":"じるちゃん"},
        {"名前":"寝川","所属局":"国際関係","読み":"ねかわ"},
        {"名前":"ハチ","所属局":"総務","読み":"はち"},
        {"名前":"ドーリー","所属局":"総務","読み":"どーりー"},
        {"名前":"ぽも","所属局":"総務","読み":"ぽも"},
        {"名前":"ふぃる","所属局":"総務","読み":"ふぃる"},
        {"名前":"サニー","所属局":"総務","読み":"さにー"},
        {"名前":"ぺんちゃん","所属局":"総務","読み":"ぺんちゃん"},
        {"名前":"なごみん","所属局":"総務","読み":"なごみん"},
        {"名前":"るんるん","所属局":"総務","読み":"るんるん"},
        {"名前":"みーちゃん♡","所属局":"総務","読み":"みーちゃん"},
        {"名前":"ぷーみん","所属局":"総務","読み":"ぷーみん"},
        {"名前":"あまめろ","所属局":"総務","読み":"あまめろ"},
        {"名前":"キャンディポップちゃん","所属局":"総務","読み":"きゃんでぃぽっぷちゃん"},
        {"名前":"みんぴゅ","所属局":"総務","読み":"みんぴゅ"},
        {"名前":"ふれん","所属局":"総務","読み":"ふれん"},
        {"名前":"カル","所属局":"総務","読み":"かる"},
        {"名前":"ハーツ","所属局":"総務","読み":"はーつ"},
        {"名前":"こっぺちゃん","所属局":"総務","読み":"こっぺちゃん"},
        {"名前":"マイマイ","所属局":"総務","読み":"まいまい"}
    ];

    // ==========================================
    // 💡 3. メインのロジック群
    // ==========================================
    let pinnedNames = [];
    let isSidebarVisible = false;
    let currentInputName = ""; 
    let isMobileSelectMode = false;
    let selectedPinIndices = new Set();
    let lastSelectedPinIndex = -1;
    let singleClickCount = 0;
    let addedPinsWhileHidden = 0;
    let tokenizer = null;
    let tooltipTimeout;
    let toastTimeout;

    function getBadgeClass(dept) {
        switch(dept) {
            case '模擬': return 'badge-mogi';
            case 'タレント': return 'badge-talent';
            case '企画': return 'badge-kikaku';
            case '渉外': return 'badge-shougai';
            case '情報宣伝': return 'badge-jousen';
            case '国際関係': return 'badge-kokusai';
            case '総務': return 'badge-soumu';
            case 'ピン留め済み': return 'badge-pinned';
            default: return '';
        }
    }

    // LocalStorageから安全に読み込み
    try {
        const stored = localStorage.getItem('nicknamePins');
        if (stored) pinnedNames = JSON.parse(stored);
        if (!Array.isArray(pinnedNames)) pinnedNames = [];
        isSidebarVisible = localStorage.getItem('isSidebarVisible') === 'true';
    } catch(e) { console.warn(e); }

    function savePins() { 
        try { localStorage.setItem('nicknamePins', JSON.stringify(pinnedNames)); } catch(e){}
    }

    window.showToast = function(htmlMsg, btnText = null, callback = null) {
        const toast = document.getElementById('toastHint');
        const text = document.getElementById('toastHintText');
        const btn = document.getElementById('toastHintBtn');
        text.innerHTML = htmlMsg;
        if(btnText && callback) {
            btn.style.display = 'inline-block';
            btn.innerText = btnText;
            btn.onclick = () => { callback(); hideToast(); };
        } else {
            btn.style.display = 'none';
        }
        toast.classList.add('show');
        clearTimeout(toastTimeout);
        toastTimeout = setTimeout(hideToast, 5000);
    };

    window.toggleSidebar = function() {
        const wrapper = document.getElementById('sidebarWrapper');
        isSidebarVisible = !isSidebarVisible;
        try { localStorage.setItem('isSidebarVisible', isSidebarVisible); } catch(e){}
        
        if (isSidebarVisible) {
            wrapper.classList.remove('hidden');
            wrapper.classList.add('visible');
            addedPinsWhileHidden = 0;
        } else {
            wrapper.classList.remove('visible');
            setTimeout(() => { if(!isSidebarVisible) wrapper.classList.add('hidden'); }, 400);
        }
    };

    window.toggleMobileMode = function() {
        isMobileSelectMode = !isMobileSelectMode;
        const btn = document.getElementById('mobileSelectBtn');
        if(isMobileSelectMode) {
            btn.classList.add('active');
            hidePinTooltip();
        } else {
            btn.classList.remove('active');
            selectedPinIndices.clear();
        }
        updateActionBar();
    };

    window.addPin = function() {
        if (!currentInputName) return;
        if (!pinnedNames.includes(currentInputName)) {
            pinnedNames.push(currentInputName);
            savePins(); 
            
            if (pinnedNames.length === 1 && !isSidebarVisible) {
                toggleSidebar();
            } else if (!isSidebarVisible) {
                addedPinsWhileHidden++;
                if (addedPinsWhileHidden >= 2) {
                    showToast("📌 ピン留めリストが隠れています。表示しますか？", "表示する", () => {
                        toggleSidebar();
                    });
                }
            }
            renderPinnedList();
        }
        document.getElementById('nicknameInput').value = '';
        checkNickname(); 
    };

    window.removePin = function(index) {
        if (confirm(`「${pinnedNames[index]}」のピン留めを削除してもよろしいですか？`)) {
            pinnedNames.splice(index, 1);
            selectedPinIndices.delete(index);
            savePins(); 
            renderPinnedList();
            hidePinTooltip();
            checkNickname(); 
        }
    };

    window.deleteAllPins = function() {
        if (pinnedNames.length === 0) return;
        if (confirm("ピン留めしたあだ名をすべて削除してもよろしいですか？\n※この操作は元に戻せません。")) {
            pinnedNames = [];
            selectedPinIndices.clear();
            savePins(); 
            renderPinnedList();
            hidePinTooltip();
            checkNickname(); 
        }
    };

    function updateActionBar() {
        const bar = document.getElementById('actionBar');
        const container = document.getElementById('actionBarContainer');
        const countSpan = document.getElementById('selectedCount');
        const isMobile = window.matchMedia("(max-width: 1150px)").matches;
        
        if (isMobile && !isMobileSelectMode) {
            container.classList.remove('show');
            bar.classList.remove('active');
        } else {
            container.classList.add('show');
            if (selectedPinIndices.size > 0) {
                bar.classList.add('active');
                countSpan.innerText = `${selectedPinIndices.size}件選択中`;
            } else {
                bar.classList.remove('active');
            }
        }

        const list = document.getElementById('pinnedList');
        const badges = list.querySelectorAll('.pinned-badge');
        badges.forEach((badge, index) => {
            if (selectedPinIndices.has(index)) badge.classList.add('selected');
            else badge.classList.remove('selected');
        });
    }

    window.copySelectedPins = function() {
        const textToCopy = Array.from(selectedPinIndices)
                                .map(index => pinnedNames[index])
                                .join('\n');
        navigator.clipboard.writeText(textToCopy).then(() => {
            alert('選択したあだ名をコピーしました！');
            selectedPinIndices.clear();
            updateActionBar();
        });
    };

    window.deleteSelectedPins = function() {
        if (confirm(`選択した ${selectedPinIndices.size} 件のあだ名を削除しますか？`)) {
            const indicesToDelete = Array.from(selectedPinIndices).sort((a, b) => b - a);
            indicesToDelete.forEach(idx => { pinnedNames.splice(idx, 1); });
            selectedPinIndices.clear();
            savePins();
            renderPinnedList();
            hidePinTooltip();
            checkNickname();
        }
    };

    function renderPinnedList() {
        const list = document.getElementById('pinnedList');
        const count = document.getElementById('pinCount');
        count.innerText = pinnedNames.length > 0 ? `(${pinnedNames.length}件)` : '';

        let newSelected = new Set();
        selectedPinIndices.forEach(idx => { if (idx < pinnedNames.length) newSelected.add(idx); });
        selectedPinIndices = newSelected;

        if (pinnedNames.length === 0) {
            list.innerHTML = '<span style="color:#94a3b8; font-size: 14px;">ピン留めはありません</span>';
            updateActionBar();
            return;
        }
        
        list.innerHTML = pinnedNames.map((name, index) => 
            `<div class="pinned-badge" data-name="${name}" data-index="${index}">
                <span>${name}</span>
                <span class="remove-pin" onclick="event.stopPropagation(); removePin(${index})" title="削除">✖</span>
            </div>`
        ).join('');

        updateActionBar();

        const badges = list.querySelectorAll('.pinned-badge');
        badges.forEach(badge => {
            const name = badge.getAttribute('data-name');
            const idx = parseInt(badge.getAttribute('data-index'), 10);

            badge.addEventListener('click', (e) => {
                if (e.target.classList.contains('remove-pin')) return;
                
                const isMobile = window.matchMedia("(max-width: 1150px)").matches;
                if (isMobile) {
                    if (isMobileSelectMode) {
                        if (selectedPinIndices.has(idx)) selectedPinIndices.delete(idx);
                        else selectedPinIndices.add(idx);
                        updateActionBar();
                    } else {
                        showPinTooltip(name, null, true);
                    }
                    return;
                }

                const isMac = navigator.platform.toUpperCase().indexOf('MAC') >= 0;
                const isCmdOrCtrl = isMac ? e.metaKey : e.ctrlKey;

                if (e.shiftKey && lastSelectedPinIndex !== -1) {
                    const start = Math.min(idx, lastSelectedPinIndex);
                    const end = Math.max(idx, lastSelectedPinIndex);
                    if (!isCmdOrCtrl) selectedPinIndices.clear();
                    for (let i = start; i <= end; i++) selectedPinIndices.add(i);
                } else if (isCmdOrCtrl) {
                    if (selectedPinIndices.has(idx)) selectedPinIndices.delete(idx);
                    else selectedPinIndices.add(idx);
                    lastSelectedPinIndex = idx;
                } else {
                    if (selectedPinIndices.size === 1 && selectedPinIndices.has(idx)) {
                        selectedPinIndices.clear();
                    } else {
                        selectedPinIndices.clear();
                        selectedPinIndices.add(idx);
                        try {
                            if(!localStorage.getItem('knowsMultiSelect')) {
                                singleClickCount++;
                                if(singleClickCount >= 3) {
                                    showToast("💡 <b>Ctrl/Shift</b> を押しながらクリックで一括選択できます");
                                    localStorage.setItem('knowsMultiSelect', 'true');
                                }
                            }
                        }catch(e){}
                    }
                    lastSelectedPinIndex = idx;
                }

                try { if(isCmdOrCtrl || e.shiftKey) localStorage.setItem('knowsMultiSelect', 'true'); }catch(e){}
                updateActionBar();
            });

            badge.addEventListener('mouseenter', (e) => {
                if (!window.matchMedia("(max-width: 1150px)").matches) {
                    tooltipTimeout = setTimeout(() => {
                        showPinTooltip(name, badge, false);
                    }, 600); 
                }
            });
            badge.addEventListener('mouseleave', () => {
                clearTimeout(tooltipTimeout);
                hidePinTooltip();
            });
        });
    }

    function levenshteinDistance(s, t) {
        if (!s.length) return t.length;
        if (!t.length) return s.length;
        const arr = [];
        for (let i = 0; i <= t.length; i++) {
            arr[i] = [i];
            for (let j = 1; j <= s.length; j++) {
                arr[i][j] = i === 0 ? j : Math.min(arr[i - 1][j] + 1, arr[i][j - 1] + 1, arr[i - 1][j - 1] + (s[j - 1] === t[i - 1] ? 0 : 1));
            }
        }
        return arr[t.length][s.length];
    }

    function checkSimilar(inStr, targetStr) {
        if (!inStr || !targetStr) return false;
        const dist = levenshteinDistance(inStr, targetStr);
        const isSub = (inStr.length >= 2 && targetStr.includes(inStr)) || (targetStr.length >= 2 && inStr.includes(targetStr));
        return dist === 0 || dist === 1 || (dist === 2 && Math.max(inStr.length, targetStr.length) >= 4) || isSub;
    }

    function getSimilarForPin(pinName) {
        const inputNameNorm = normalizeString(pinName);
        const inputYomiNorm = normalizeString(getReading(pinName));
        let results = [];
        let combinedData = [...existingData];
        pinnedNames.forEach(p => {
            if(p !== pinName) combinedData.push({"名前": p, "所属局": "ピン留め済み", "読み": getReading(p)});
        });

        combinedData.forEach(item => {
            const dataNameNorm = normalizeString(item.名前);
            const dataYomiNorm = normalizeString(item.読み);
            if (checkSimilar(inputNameNorm, dataNameNorm) || checkSimilar(inputNameNorm, dataYomiNorm) ||
                checkSimilar(inputYomiNorm, dataNameNorm) || checkSimilar(inputYomiNorm, dataYomiNorm)) {
                results.push(item);
            }
        });
        return results;
    }

    function showPinTooltip(name, badgeElement = null, isMobile = false) {
        const tooltip = document.getElementById('pinTooltip');
        const results = getSimilarForPin(name);

        if (results.length === 0) {
            tooltip.innerHTML = `<div class="no-similars">✅ 類似あだ名なし</div>`;
        } else {
            let hasExact = false;
            const inputNameNorm = normalizeString(name);
            const inputYomiNorm = normalizeString(getReading(name));

            results.forEach(item => {
                const dataNameNorm = normalizeString(item.名前);
                const dataYomiNorm = normalizeString(item.読み);
                if (inputNameNorm === dataNameNorm || inputNameNorm === dataYomiNorm ||
                    inputYomiNorm === dataNameNorm || inputYomiNorm === dataYomiNorm) {
                    hasExact = true;
                }
            });

            let html = "";
            if (hasExact) {
                html = `<h4 style="color: #ef4444; border-bottom: 1px dashed #fca5a5;">❌ 被りあり (${results.length}件)</h4><ul>`;
            } else {
                html = `<h4 style="color: #d97706; border-bottom: 1px dashed #fcd34d;">⚠️ 類似名 (${results.length}件)</h4><ul>`;
            }
            
            results.forEach(item => {
                const badgeClass = getBadgeClass(item.所属局);
                html += `<li>${item.名前} <span class="dept-badge ${badgeClass}" style="font-size:10px; padding:2px 6px;">${item.所属局}</span></li>`;
            });
            html += `</ul>`;
            tooltip.innerHTML = html;
        }

        tooltip.style.display = 'block';

        if (badgeElement && !isMobile) {
            const rect = badgeElement.getBoundingClientRect();
            let top = rect.top + window.scrollY;
            let left = rect.right + window.scrollX + 15; 
            const tooltipRect = tooltip.getBoundingClientRect();
            if (left + tooltipRect.width > window.innerWidth) {
                left = rect.left + window.scrollX - tooltipRect.width - 15;
            }
            tooltip.style.top = top + 'px';
            tooltip.style.left = left + 'px';
        }

        if (isMobile) {
            setTimeout(hidePinTooltip, 4000);
        }
    }

    document.addEventListener('touchstart', function(e) {
        if (!e.target.closest('.pinned-badge') && !e.target.closest('.pin-tooltip')) {
            hidePinTooltip();
        }
    });

    document.addEventListener('keydown', function(e) {
        if (!isSidebarVisible || pinnedNames.length === 0) return;
        if (document.activeElement.tagName === 'INPUT') return;

        if (e.shiftKey && (e.key === 'ArrowDown' || e.key === 'ArrowUp' || e.key === 'ArrowRight' || e.key === 'ArrowLeft')) {
            e.preventDefault(); 
            if (lastSelectedPinIndex === -1) {
                lastSelectedPinIndex = 0;
                selectedPinIndices.add(0);
            } else {
                let nextIdx = lastSelectedPinIndex;
                if (e.key === 'ArrowDown' || e.key === 'ArrowRight') nextIdx++;
                if (e.key === 'ArrowUp' || e.key === 'ArrowLeft') nextIdx--;
                
                if (nextIdx >= 0 && nextIdx < pinnedNames.length) {
                    selectedPinIndices.add(nextIdx);
                    lastSelectedPinIndex = nextIdx;
                }
            }
            updateActionBar();
        }
    });

    function kanaToHira(str) {
        return str.replace(/[\u30a1-\u30f6]/g, function(match) {
            var chr = match.charCodeAt(0) - 0x60;
            return String.fromCharCode(chr);
        });
    }

    function normalizeString(str) {
        if (!str) return "";
        let s = str.trim().toLowerCase();
        return kanaToHira(s);
    }

    function getReading(text) {
        try {
            if (customReadingDict[text]) return customReadingDict[text];
            if (!tokenizer || !text) return text;
            const tokens = tokenizer.tokenize(text);
            let reading = "";
            tokens.forEach(token => { reading += token.reading ? token.reading : token.surface_form; });
            return reading;
        } catch(e) { return text; }
    }

    window.checkNickname = function() {
        const inputRaw = document.getElementById('nicknameInput').value;
        const resultArea = document.getElementById('resultArea');
        currentInputName = inputRaw.trim();
        
        if (!currentInputName) {
            resultArea.innerHTML = '';
            return;
        }

        const inputNameNorm = normalizeString(currentInputName);
        const inputYomiNorm = normalizeString(getReading(currentInputName));
        
        let exactMatches = [];
        let similarMatches = [];

        let combinedData = [...existingData];
        pinnedNames.forEach(pinName => {
            combinedData.push({ "名前": pinName, "所属局": "ピン留め済み", "読み": getReading(pinName) });
        });

        combinedData.forEach(item => {
            const dataNameNorm = normalizeString(item.名前);
            const dataYomiNorm = normalizeString(item.読み);
            
            if (inputNameNorm === dataNameNorm || inputNameNorm === dataYomiNorm ||
                inputYomiNorm === dataNameNorm || inputYomiNorm === dataYomiNorm) {
                exactMatches.push(item);
                return;
            }

            if (checkSimilar(inputNameNorm, dataNameNorm) || checkSimilar(inputNameNorm, dataYomiNorm) ||
                checkSimilar(inputYomiNorm, dataNameNorm) || checkSimilar(inputYomiNorm, dataYomiNorm)) {
                similarMatches.push(item);
            }
        });

        const pinButtonHtml = `<button class="pin-btn" onclick="addPin()" title="ショートカット: Ctrl+Enter (Macは Cmd+Enter)">📌 このあだ名に決定（ピン留め）</button>`;

        if (exactMatches.length > 0) {
            let html = `<div class="result error">❌ 既に使われているあだ名です（完全一致）<br><br>`;
            exactMatches.forEach(match => {
                const badgeClass = getBadgeClass(match.所属局);
                html += `<div class="list-item">・ ${match.名前} <span class="dept-badge ${badgeClass}">${match.所属局}</span></div>`;
            });
            html += `</div>`;
            resultArea.innerHTML = html;
        } else if (similarMatches.length > 0) {
            let html = `<div class="result warning">⚠️ 類似しているあだ名があります<br>`;
            html += `<span style="font-size:0.85em; font-weight:normal; color:#64748b;">読みや音が似ている、1文字違い、または一部が一致している可能性があります。</span><br>`;
            html += pinButtonHtml;
            html += `<div class="match-list-container">`; 
            similarMatches.forEach(match => {
                const badgeClass = getBadgeClass(match.所属局);
                html += `<div class="list-item">・ ${match.名前} <span class="dept-badge ${badgeClass}">${match.所属局}</span></div>`;
            });
            html += `</div></div>`;
            resultArea.innerHTML = html;
        } else {
            let html = `<div class="result success">✅ このあだ名は被りや類似名がありません！<br><span style="font-size:0.9em; font-weight:normal;">使用可能です。</span><br>`;
            html += pinButtonHtml;
            html += `</div>`;
            resultArea.innerHTML = html;
        }
    };

    // ==========================================
    // 💡 4. ページ読み込み完了時の処理（安全に実行）
    // ==========================================
    window.onload = function() {
        // ① チュートリアルの表示判定
        let hasSeenTutorial = false;
        try { hasSeenTutorial = localStorage.getItem('hasSeenTutorial') === 'true'; }catch(e){}
        if (!hasSeenTutorial) {
            openTutorial();
        }

        // ② サイドバーの状態復元
        const wrapper = document.getElementById('sidebarWrapper');
        if (isSidebarVisible && wrapper) {
            wrapper.classList.remove('hidden');
            wrapper.classList.add('visible');
        } else if (wrapper) {
            wrapper.classList.remove('visible');
            wrapper.classList.add('hidden');
        }

        renderPinnedList();

        // ③ 入力欄のイベント登録
        const nicknameInput = document.getElementById('nicknameInput');
        if(nicknameInput) {
            nicknameInput.addEventListener('keydown', function(e) {
                if ((e.ctrlKey || e.metaKey) && e.key === 'Enter') {
                    e.preventDefault(); 
                    addPin();
                }
            });
        }

        // ④ Kuromojiの安全な初期化（最後に実行）
        const statusEl = document.getElementById('statusText');
        if (typeof kuromoji !== 'undefined') {
            try {
                kuromoji.builder({ dicPath: "https://cdn.jsdelivr.net/npm/kuromoji@0.1.2/dict/" }).build(function (err, _tokenizer) {
                    if (err) {
                        console.error("Kuromoji load error:", err);
                        if(statusEl) {
                            statusEl.innerText = "※辞書の読み込みに失敗しました（簡易判定モード）";
                            statusEl.style.color = "#ef4444";
                        }
                    } else {
                        tokenizer = _tokenizer;
                        if(statusEl) {
                            statusEl.innerText = "✅ 漢字の自動読み取り準備完了";
                            statusEl.style.color = "#22c55e";
                            setTimeout(() => {
                                statusEl.style.opacity = '0';
                                setTimeout(() => { statusEl.style.display = 'none'; }, 500);
                            }, 3000);
                        }
                        if(nicknameInput && nicknameInput.value) {
                            checkNickname();
                        }
                    }
                });
            } catch(e) {
                console.error("Kuromoji init error:", e);
                if(statusEl) statusEl.style.display = 'none';
            }
        } else {
            if(statusEl) {
                statusEl.innerText = "※外部ライブラリの読み込みに失敗しました（簡易モード）";
                statusEl.style.color = "#ef4444";
            }
        }
    };
</script>

</body>
</html>
