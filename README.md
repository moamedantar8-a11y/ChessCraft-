<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ChessCraft - MK CREATIVE AGENCY</title>
    <!-- مكتبة Chess.js -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/chess.js/0.10.3/chess.min.js"></script>
    <!-- مكتبة jQuery و Chessboard.js -->
    <script src="https://code.jquery.com/jquery-3.5.1.min.js"></script>
    <link rel="stylesheet" href="https://unpkg.com/@chrisoakman/chessboardjs@1.0.0/dist/chessboard-1.0.0.min.css">
    <script src="https://unpkg.com/@chrisoakman/chessboardjs@1.0.0/dist/chessboard-1.0.0.min.js"></script>
    
    <style>
        *, *::before, *::after { box-sizing: border-box; }
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #0d1117;
            color: #ffffff;
            margin: 0;
            padding: 0;
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        #loading-screen {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: #0d1117;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 9999;
            transition: opacity 0.5s ease;
        }
        .loader-logo { font-size: 2.8rem; color: #ff9f43; font-weight: bold; margin-bottom: 10px; }
        .loader-sub { color: #8b949e; font-size: 0.95rem; margin-bottom: 25px; text-align: center; padding: 0 15px; }
        .spinner {
            width: 45px; height: 45px;
            border: 4px solid rgba(255, 159, 67, 0.2);
            border-top: 4px solid #ff9f43;
            border-radius: 50%;
            animation: spin 1s linear infinite;
        }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }

        .app-container {
            width: 100%;
            max-width: 480px;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            background-color: #121824;
            position: relative;
            padding-bottom: 75px;
            box-shadow: 0 0 30px rgba(0,0,0,0.8);
        }

        .app-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 15px 20px;
            background: #121824;
            border-bottom: 1px solid rgba(255,255,255,0.05);
        }
        .app-title { font-size: 1.5rem; font-weight: bold; color: #ffffff; margin: 0; }
        .header-stats { display: flex; align-items: center; gap: 10px; }
        .streak-badge {
            background: #1e2736; padding: 6px 12px; border-radius: 20px;
            font-size: 0.85rem; color: #ff9f43; font-weight: bold; display: flex; align-items: center; gap: 5px; cursor: pointer;
            border: 1px solid rgba(255,159,67,0.2);
        }

        .tab-content {
            display: none;
            padding: 15px 20px;
            flex-direction: column;
            gap: 15px;
            animation: fadeIn 0.3s ease;
        }
        .tab-content.active { display: flex; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }

        .gift-banner {
            background: linear-gradient(135deg, #ff9f43, #e58e26);
            color: #121824; padding: 15px; border-radius: 14px;
            font-weight: bold; text-align: center; box-shadow: 0 4px 15px rgba(255, 159, 67, 0.3);
        }
        .gift-banner h3 { margin: 0 0 5px 0; font-size: 1.1rem; }
        .gift-banner p { margin: 0; font-size: 0.85rem; color: #2f3640; }

        .rank-card {
            background: #1e2736; border: 1px solid rgba(255, 255, 255, 0.05);
            padding: 15px; border-radius: 14px; display: flex; flex-direction: column; gap: 10px;
        }
        .rank-header { display: flex; justify-content: space-between; font-size: 0.85rem; color: #8b949e; }
        .rank-title { font-size: 1.1rem; font-weight: bold; color: #fff; }
        .progress-bar-bg { width: 100%; height: 8px; background: #2f3640; border-radius: 4px; overflow: hidden; }
        .progress-bar-fill { width: 0%; height: 100%; background: #ff9f43; border-radius: 4px; transition: width 0.4s ease; }

        .section-header {
            font-size: 1.1rem; font-weight: bold; color: #f5f6fa;
            margin-top: 10px; margin-bottom: 5px; border-right: 3px solid #ff9f43; padding-right: 8px;
        }
        .cards-container { display: grid; grid-template-columns: 1fr; gap: 12px; }
        .opening-card {
            background: #1e2736; border: 1px solid rgba(255, 255, 255, 0.05);
            padding: 14px; border-radius: 12px; cursor: pointer; transition: 0.2s;
            display: flex; justify-content: space-between; align-items: center; position: relative;
        }
        .opening-card:hover { background: #273142; border-color: #ff9f43; }
        .card-info h4 { margin: 0 0 4px 0; color: #ff9f43; font-size: 1rem; }
        .card-info p { margin: 0; color: #8395a7; font-size: 0.8rem; }
        .badge-icon { background: #2ed573; color: #121824; font-size: 0.7rem; padding: 2px 6px; border-radius: 6px; font-weight: bold; }
        .badge-done { background: #ff9f43; color: #121824; }

        .game-screen {
            display: none; flex-direction: column; gap: 12px; padding: 10px 15px;
        }
        .game-top-bar { display: flex; justify-content: space-between; align-items: center; }
        .back-dash-btn {
            background: #2f3640; color: #fff; border: none; padding: 6px 12px;
            border-radius: 8px; font-size: 0.85rem; cursor: pointer; font-weight: bold;
        }
        .back-dash-btn:hover { background: #487eb0; }

        .board-wrapper {
            position: relative; width: 100%; max-width: 380px; margin: 0 auto; background: #1e2736; padding: 8px;
            border-radius: 12px; box-shadow: 0 8px 20px rgba(0,0,0,0.4);
        }
        #board { width: 100%; }
        #arrow-canvas {
            position: absolute; top: 8px; left: 8px;
            width: calc(100% - 16px); height: calc(100% - 16px);
            pointer-events: none; z-index: 10;
        }

        .instruction-box {
            background: rgba(46, 213, 115, 0.1); border-right: 4px solid #2ed573;
            padding: 10px; border-radius: 6px; font-size: 0.85rem; color: #d1d8e0; line-height: 1.4;
        }
        .status-msg { text-align: center; font-weight: bold; color: #2ed573; font-size: 0.85rem; min-height: 18px; }
        .game-btns { display: flex; gap: 10px; }
        .action-btn {
            flex: 1; background: #e58e26; color: #fff; border: none; padding: 10px;
            border-radius: 8px; font-weight: bold; font-size: 0.85rem; cursor: pointer; transition: 0.2s;
        }
        .action-btn:hover { background: #fa983a; }
        .secondary-btn { background: #393e46; }

        #streak-modal {
            display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(13, 17, 23, 0.95); z-index: 2000;
            flex-direction: column; justify-content: space-between; padding: 30px 20px; text-align: center;
        }
        .streak-modal-content { display: flex; flex-direction: column; align-items: center; gap: 15px; margin-top: 20px; }
        .streak-huge-icon { font-size: 4.5rem; animation: pulseFlame 1.5s infinite alternate; }
        @keyframes pulseFlame { 0% { transform: scale(1); } 100% { transform: scale(1.08); } }
        .streak-days-row { display: flex; justify-content: center; gap: 6px; margin-top: 10px; }
        .day-circle {
            width: 34px; height: 34px; border-radius: 50%; background: #1e2736;
            display: flex; flex-direction: column; align-items: center; justify-content: center;
            font-size: 0.7rem; font-weight: bold; color: #8b949e; border: 2px dashed #30363d;
        }
        .day-circle.active { background: #ff9f43; color: #121824; border: 2px solid #ff9f43; border-style: solid; }
        .day-label { font-size: 0.55rem; margin-bottom: 2px; }

        .pieces-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 10px; }
        .piece-btn {
            background: #1e2736; border: 1px solid rgba(255,255,255,0.05); padding: 12px;
            border-radius: 10px; color: #fff; font-weight: bold; text-align: center;
            cursor: pointer; transition: 0.2s; display: flex; align-items: center; gap: 8px;
        }
        .piece-btn:hover { background: #273142; border-color: #ff9f43; }

        .terminal-box {
            background: #090d13; border: 1px solid #30363d; border-radius: 10px;
            padding: 15px; font-family: 'Courier New', Courier, monospace; color: #2ed573; font-size: 0.85rem; line-height: 1.6;
        }
        .terminal-box span { color: #ff9f43; }

        .bottom-nav {
            position: absolute; bottom: 0; left: 0; width: 100%; height: 65px;
            background: #121824; border-top: 1px solid rgba(255,255,255,0.08);
            display: flex; justify-content: space-around; align-items: center; z-index: 100;
        }
        .nav-item {
            display: flex; flex-direction: column; align-items: center; color: #8b949e;
            font-size: 0.75rem; cursor: pointer; gap: 3px; transition: 0.2s; background: none; border: none;
        }
        .nav-item svg { width: 20px; height: 20px; fill: currentColor; }
        .nav-item.active { color: #ff9f43; }
        .nav-item:hover { color: #ff9f43; }
    </style>
</head>
<body>

    <div id="loading-screen">
        <div class="loader-logo">♟️ ChessCraft</div>
        <div class="loader-sub">جاري تهيئة الرقعة ومحرك الشطرنج للمتصفح...</div>
        <div class="spinner"></div>
    </div>

    <!-- مودال الشعلة اليومية -->
    <div id="streak-modal">
        <div></div>
        <div class="streak-modal-content">
            <h2 style="color:#fff; font-size: 1.05rem; margin:0;">Learn a new line or solve a puzzle to reach your streak!</h2>
            <div class="streak-huge-icon">🔥</div>
            <h1 id="streakCountBig" style="color: #ff9f43; font-size: 2rem; margin:0;">1 Day Streak</h1>
            <p style="color: #8b949e; font-size: 0.8rem; margin:0;">تتزايد الشعلة عند فتح التطبيق كل يوم، وتتصفّر إذا فاتك يوم كامل!</p>
            
            <div class="streak-days-row" id="streakDaysContainer"></div>
        </div>
        <div style="padding-bottom: 20px;">
            <button class="action-btn" style="width: 100%; padding: 14px; font-size: 1rem; border-radius: 12px;" onclick="closeStreakModal()">Get your Streak!</button>
        </div>
    </div>

    <div class="app-container">
        <div class="app-header">
            <h1 class="app-title">ChessCraft</h1>
            <div class="header-stats">
                <div class="streak-badge" onclick="openStreakModal()">
                    <span id="headerStreakNum">🔥 1</span>
                </div>
            </div>
        </div>

        <!-- تبويب الافتتاحيات -->
        <div id="tab-openings" class="tab-content active">
            <div class="gift-banner">
                <h3>🎁 إهداء خاص</h3>
                <p>هذا التطبيق مهدى إلى أخي الصغير <strong>مالك عنتر</strong> ❤️ ليتعلم أسرار الشطرنج باحتراف!</p>
            </div>

            <div class="rank-card">
                <div class="rank-header">
                    <span id="nextRankText">Next Rank: Apprentice</span>
                    <span>Current Rank</span>
                </div>
                <div class="rank-title" id="currentRankTitle">Novice 🛡️</div>
                <div class="progress-bar-bg">
                    <div class="progress-bar-fill" id="rankProgressBar"></div>
                </div>
            </div>

            <div class="section-header">افتتاحيات القطع البيضاء (White)</div>
            <div class="cards-container">
                <div class="opening-card" onclick="startOpeningGame('italian')">
                    <div class="card-info">
                        <h4>الافتتاحية الإيطالية (Italian Game)</h4>
                        <p>السيطرة على الوسط ومهاجمة المربع الضعيف f7</p>
                    </div>
                    <span class="badge-icon" id="badge-italian">موصى به</span>
                </div>
                <div class="opening-card" onclick="startOpeningGame('london')">
                    <div class="card-info">
                        <h4>نظام لندن (London System)</h4>
                        <p>افتتاحية آمنة وقوية وسهلة للمبتدئين</p>
                    </div>
                    <span class="badge-icon" id="badge-london">محبوب</span>
                </div>
                <div class="opening-card" onclick="startOpeningGame('friedLiver')">
                    <div class="card-info">
                        <h4>هجوم الكبد المقطوع (Fried Liver)</h4>
                        <p>هجوم تكتيكي ناري ومثير ضد دفاعات الخصم</p>
                    </div>
                    <span class="badge-icon" id="badge-friedLiver">متقدم</span>
                </div>
                <div class="opening-card" onclick="startOpeningGame('kingsGambit')">
                    <div class="card-info">
                        <h4>غامبيت الملك (King's Gambit) 🔥 جديد</h4>
                        <p>تضحية جريئة ببيدق في الافتتاح لهجوم هائل وسريع</p>
                    </div>
                    <span class="badge-icon" id="badge-kingsGambit">هجومي</span>
                </div>
            </div>

            <div class="section-header">افتتاحيات القطع السوداء (Black)</div>
            <div class="cards-container">
                <div class="opening-card" onclick="startOpeningGame('sicilian')">
                    <div class="card-info">
                        <h4>الدفاع الصقلي (Sicilian Defense)</h4>
                        <p>الهجوم المضاد القوي والخطير ضد الأبيض</p>
                    </div>
                    <span class="badge-icon" id="badge-sicilian">احترافي</span>
                </div>
                <div class="opening-card" onclick="startOpeningGame('caroKann')">
                    <div class="card-info">
                        <h4>دفاع كارو-كان (Caro-Kann)</h4>
                        <p>دفاع صلب ومتين للقطع السوداء</p>
                    </div>
                    <span class="badge-icon" id="badge-caroKann">دفاعي</span>
                </div>
                <div class="opening-card" onclick="startOpeningGame('kingsIndian')">
                    <div class="card-info">
                        <h4>دفاع الملك الهندي (King's Indian) 🔥 جديد</h4>
                        <p>دفاع استراتيجي مرن وعميق للأسود</p>
                    </div>
                    <span class="badge-icon" id="badge-kingsIndian">استراتيجي</span>
                </div>
            </div>
        </div>

        <!-- شاشة اللعب والتدريب -->
        <div id="game-screen" class="game-screen">
            <div class="game-top-bar">
                <button class="back-dash-btn" onclick="closeOpeningGame()">⬅ القائمة</button>
                <h3 id="openingGameTitle" style="color: #ff9f43; margin:0; font-size: 0.95rem;">الافتتاحية</h3>
            </div>

            <div class="board-wrapper">
                <div id="board"></div>
                <canvas id="arrow-canvas"></canvas>
            </div>

            <div style="display: flex; flex-direction: column; gap: 6px;">
                <div class="instruction-box" id="instructionText">جاري تحميل التوجيهات...</div>
                <div id="status" class="status-msg"></div>
                <div class="game-btns">
                    <button class="action-btn secondary-btn" id="actionBtnMain" onclick="resetOpeningGame()">إعادة التدريب 🔄</button>
                </div>
            </div>
        </div>

        <!-- تبويب الأساسيات -->
        <div id="tab-foundations" class="tab-content">
            <div class="section-header">تعلم أساسيات وحركة القطع منفردة</div>
            <p style="color: #8b949e; font-size: 0.85rem; margin: 0 0 10px 0;">اختر القطعة لمعاينة حركتها التفاعلية وتحريكها بنفسك:</p>
            
            <div class="pieces-grid">
                <div class="piece-btn" onclick="startFoundationPiece('king')"><img src="https://chessboardjs.com/img/chesspieces/wikipedia/wK.png" width="28"> <span>الملك</span></div>
                <div class="piece-btn" onclick="startFoundationPiece('queen')"><img src="https://chessboardjs.com/img/chesspieces/wikipedia/wQ.png" width="28"> <span>الوزير</span></div>
                <div class="piece-btn" onclick="startFoundationPiece('rook')"><img src="https://chessboardjs.com/img/chesspieces/wikipedia/wR.png" width="28"> <span>القلعة</span></div>
                <div class="piece-btn" onclick="startFoundationPiece('bishop')"><img src="https://chessboardjs.com/img/chesspieces/wikipedia/wB.png" width="28"> <span>الفيل</span></div>
                <div class="piece-btn" onclick="startFoundationPiece('knight')"><img src="https://chessboardjs.com/img/chesspieces/wikipedia/wN.png" width="28"> <span>الحصان</span></div>
                <div class="piece-btn" onclick="startFoundationPiece('pawn')"><img src="https://chessboardjs.com/img/chesspieces/wikipedia/wP.png" width="28"> <span>البيدق</span></div>
            </div>
        </div>

        <!-- تبويب النهايات -->
        <div id="tab-endgames" class="tab-content">
            <div class="section-header">تحديات كش مات بسيطة</div>
            <div class="cards-container">
                <div class="opening-card" onclick="alert('قريباً جداً تحديات النهايات المتقدمة!')">
                    <div class="card-info">
                        <h4>مات القلعتين (Two Rooks Mate)</h4>
                        <p>كيف تحاصر الملك الخصم في الصف الأخير بطريقة منظمة.</p>
                    </div>
                </div>
            </div>
        </div>

        <!-- تبويب الألعاب -->
        <div id="tab-games" class="tab-content">
            <div class="section-header">الوضع التنافسي</div>
            <div class="cards-container">
                <div class="opening-card" onclick="alert('قريباً جداً وضع اللعب الحر مع أخيك مالك!')">
                    <div class="card-info">
                        <h4>لعب مع صديق (Pass & Play)</h4>
                        <p>تمرير الهاتف واللعب وجهاً لوجه على نفس الرقعة.</p>
                    </div>
                    <span class="badge-icon">قريباً</span>
                </div>
            </div>
        </div>

        <!-- تبويب الإعدادات والـ Terminal -->
        <div id="tab-settings" class="tab-content">
            <div class="section-header">لوحة تحكم النظام (Terminal)</div>
            <div class="terminal-box">
                > System init: <span>ChessCraft v3.4 (Interactive)</span><br>
                > Company: <span>MK CREATIVE AGENCY</span><br>
                > Developer / Creator: <span>Mohamed Antar</span><br>
                > Dedicated to: <span>Malek Antar (My Brother)</span><br>
                > Status: <span>Active & Secured in Browser</span><br>
                > Location: <span>Egypt / Dakahlia</span>
            </div>

            <div class="gift-banner" style="margin-top: 10px;">
                <h3>✨ صُنع بكل حب</h3>
                <p>تم تطوير هذا التطبيق بواسطة <strong>Mohamed Antar</strong> لصالح شركة <strong>MK CREATIVE AGENCY</strong>.</p>
            </div>
        </div>

        <!-- شريط التنقل السفلي -->
        <nav class="bottom-nav">
            <button class="nav-item active" onclick="switchTab('openings')">
                <svg viewBox="0 0 24 24"><path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zm-5 14H7v-2h7v2zm3-4H7v-2h10v2zm0-4H7V7h10v2z"/></svg>
                <span>Openings</span>
            </button>
            <button class="nav-item" onclick="switchTab('foundations')">
                <svg viewBox="0 0 24 24"><path d="M12 3L1 9l11 6 9-4.91V17h2V9M5 13.18v4L12 21l7-3.82v-4L12 17l-7-3.82z"/></svg>
                <span>Foundations</span>
            </button>
            <button class="nav-item" onclick="switchTab('endgames')">
                <svg viewBox="0 0 24 24"><path d="M19 5v14H5V5h14m1-2H4c-.55 0-1 .45-1 1v16c0 .55.45 1 1 1h16c.55 0 1-.45 1-1V4c0-.55-.45-1-1-1z"/></svg>
                <span>Endgames</span>
            </button>
            <button class="nav-item" onclick="switchTab('games')">
                <svg viewBox="0 0 24 24"><path d="M21 6H3c-1.1 0-2 .9-2 2v8c0 1.1.9 2 2 2h18c1.1 0 2-.9 2-2V8c0-1.1-.9-2-2-2zm-10 7H8v3H6v-3H3v-2h3V8h2v3h3v2zm4.5 2c-.83 0-1.5-.67-1.5-1.5s.67-1.5 1.5-1.5 1.5.67 1.5 1.5-.67 1.5-1.5 1.5zm3-3c-.83 0-1.5-.67-1.5-1.5S17.67 9 18.5 9s1.5.67 1.5 1.5-.67 1.5-1.5 1.5z"/></svg>
                <span>Games</span>
            </button>
            <button class="nav-item" onclick="switchTab('settings')">
                <svg viewBox="0 0 24 24"><path d="M19.14 12.94c.04-.3.06-.61.06-.94 0-.32-.02-.64-.07-.94l2.03-1.58c.18-.14.23-.41.12-.61l-1.92-3.32c-.12-.22-.37-.29-.59-.22l-2.39.96c-.5-.38-1.03-.7-1.62-.94l-.36-2.54c-.04-.24-.24-.41-.48-.41h-3.84c-.24 0-.43.17-.47.41l-.36 2.54c-.59.24-1.13.57-1.62.94l-2.39-.96c-.22-.08-.47 0-.59.22L2.74 8.87c-.12.21-.08.47.12.61l2.03 1.58c-.05.3-.09.63-.09.94s.02.64.07.94l-2.03 1.58c-.18.14-.23.41-.12.61l1.92 3.32c.12.22.37.29.59.22l2.39-.96c.5.38 1.03.7 1.62.94l.36 2.54c.05.24.24.41.48.41h3.84c.24 0 .44-.17.47-.41l.36-2.54c.59-.24 1.13-.56 1.62-.94l2.39.96c.22.08.47 0 .59-.22l1.92-3.32c.12-.22.07-.47-.12-.61l-2.01-1.58zM12 15.6c-1.98 0-3.6-1.62-3.6-3.6s1.62-3.6 3.6-3.6 3.6 1.62 3.6 3.6-1.62 3.6-3.6 3.6z"/></svg>
                <span>Settings</span>
            </button>
        </nav>
    </div>

    <script>
        let appData = {
            streak: 1,
            lastLoginDate: "",
            completedOpenings: []
        };

        function loadUserData() {
            let saved = localStorage.getItem('chessCraftData');
            let today = new Date().toDateString();
            
            if (saved) {
                appData = JSON.parse(saved);
                let lastDate = appData.lastLoginDate;
                
                if (lastDate !== today) {
                    let lastDateObj = new Date(lastDate);
                    let currentDateObj = new Date(today);
                    let diffTime = currentDateObj - lastDateObj;
                    let diffDays = Math.round(diffTime / (1000 * 60 * 60 * 24));

                    if (diffDays === 1) {
                        appData.streak += 1;
                    } else {
                        appData.streak = 1;
                    }
                    appData.lastLoginDate = today;
                }
            } else {
                appData.streak = 1;
                appData.lastLoginDate = today;
            }
            saveUserData();
            updateUIStats();
        }

        function saveUserData() {
            localStorage.setItem('chessCraftData', JSON.stringify(appData));
        }

        function updateUIStats() {
            document.getElementById('headerStreakNum').innerText = "🔥 " + appData.streak;
            document.getElementById('streakCountBig').innerText = appData.streak + " Day Streak";
            
            let totalOpenings = 7;
            let completedCount = appData.completedOpenings.length;
            let percent = (completedCount / totalOpenings) * 100;
            document.getElementById('rankProgressBar').style.width = percent + "%";

            if (completedCount >= totalOpenings) {
                document.getElementById('currentRankTitle').innerText = "Grandmaster 👑";
                document.getElementById('nextRankText').innerText = "Max Rank Achieved!";
            } else if (completedCount >= 3) {
                document.getElementById('currentRankTitle').innerText = "Apprentice ⚡";
                document.getElementById('nextRankText').innerText = "Next: Grandmaster";
            } else {
                document.getElementById('currentRankTitle').innerText = "Novice 🛡️";
                document.getElementById('nextRankText').innerText = "Next: Apprentice";
            }

            appData.completedOpenings.forEach(key => {
                let badge = document.getElementById('badge-' + key);
                if(badge) {
                    badge.innerText = "مكتمل ✓";
                    badge.classList.add('badge-done');
                }
            });
        }

        function openStreakModal() {
            buildStreakDaysUI();
            document.getElementById('streak-modal').style.display = 'flex';
        }

        function closeStreakModal() {
            document.getElementById('streak-modal').style.display = 'none';
        }

        function buildStreakDaysUI() {
            let container = document.getElementById('streakDaysContainer');
            container.innerHTML = '';
            let days = ['M', 'T', 'W', 'T', 'F', 'S', 'S'];
            let todayIndex = (new Date().getDay() + 6) % 7;

            days.forEach((day, index) => {
                let div = document.createElement('div');
                div.className = 'day-circle' + (index === todayIndex ? ' active' : '');
                div.innerHTML = `<span class="day-label">${day}</span><span>${index + 1}</span>`;
                container.appendChild(div);
            });
        }

        var board = null;
        var game = new Chess();
        var currentStep = 0;
        var currentLine = null;
        var isFoundationMode = false;
        var pendingArrow = null;

        var openings = {
            italian: {
                key: "italian",
                name: "الافتتاحية الإيطالية",
                steps: [
                    { move: "e4", from: "e2", to: "e4", hint: "انقل بيدق الملك خطوتين للأمام." },
                    { move: "e5", from: "e7", to: "e5", hint: "الخصم يرد في الوسط." },
                    { move: "Nf3", from: "g1", to: "f3", hint: "أخرج الحصان الملكي ليهاجم بيدق الخصم." },
                    { move: "Nc6", from: "b8", to: "c6", hint: "الخصم يحمي بيداكه." },
                    { move: "Bc4", from: "f1", to: "c4", hint: "أخرج الفيل لتهديد مربع f7 الضعيف." }
                ]
            },
            london: {
                key: "london",
                name: "نظام لندن",
                steps: [
                    { move: "d4", from: "d2", to: "d4", hint: "ابدأ بدفع بيدق الوزير." },
                    { move: "Nf6", from: "g8", to: "f6", hint: "الخصم يخرج حصانه." },
                    { move: "Bf4", from: "c1", to: "f4", hint: "أخرج فيل الوزير لتكوين جدار لندن." },
                    { move: "e6", from: "e7", to: "e6", hint: "الخصم يغلق اللعب جزئياً." },
                    { move: "e3", from: "e2", to: "e3", hint: "ادعم مركزك بدفع بيدق الملك." }
                ]
            },
            friedLiver: {
                key: "friedLiver",
                name: "هجوم الكبد المقطوع",
                steps: [
                    { move: "e4", from: "e2", to: "e4", hint: "ابدأ بنقلة بيدق الملك." },
                    { move: "e5", from: "e7", to: "e5", hint: "الخصم يرد." },
                    { move: "Nf3", from: "g1", to: "f3", hint: "أخرج الحصان." },
                    { move: "Nc6", from: "b8", to: "c6", hint: "الخصم يطور حصانه." },
                    { move: "Bc4", from: "f1", to: "c4", hint: "الافتتاحية الإيطالية." },
                    { move: "Nf6", from: "f6", to: "g4", hint: "دفاع الخصم المتقدم." },
                    { move: "Ng5", from: "f3", to: "g5", hint: "اضغط بقوة على مربع f7 الضعيف!" }
                ]
            },
            kingsGambit: {
                key: "kingsGambit",
                name: "غامبيت الملك",
                steps: [
                    { move: "e4", from: "e2", to: "e4", hint: "ادفع بيدق الملك للأمام." },
                    { move: "e5", from: "e7", to: "e5", hint: "الخصم يدافع في المركز." },
                    { move: "f4", from: "f2", to: "f4", hint: "اضحِ بالبيدق واعرض غامبيت الملك الهجومي!" }
                ]
            },
            sicilian: {
                key: "sicilian",
                name: "الدفاع الصقلي",
                steps: [
                    { move: "e4", from: "e2", to: "e4", hint: "الأبيض يبدأ بالوسط." },
                    { move: "c5", from: "c7", to: "c5", hint: "أنت تلعب بالأسود وترد بالدفاع الصقلي القوي." },
                    { move: "Nf3", from: "g1", to: "f3", hint: "الأبيض يخرج حصانه." },
                    { move: "d6", from: "d7", to: "d6", hint: "ثبت دفاعك بالبيدق." }
                ]
            },
            caroKann: {
                key: "caroKann",
                name: "دفاع كارو-كان",
                steps: [
                    { move: "e4", from: "e2", to: "e4", hint: "الأبيض يبدأ." },
                    { move: "c6", from: "c7", to: "c6", hint: "جهز لدفع بيدق d5 المتين." },
                    { move: "d4", from: "d2", to: "d4", hint: "الأبيض يحتل الوسط." },
                    { move: "d5", from: "d7", to: "d5", hint: "اضرب الوسط واحتل المربع بقوة." }
                ]
            },
            kingsIndian: {
                key: "kingsIndian",
                name: "دفاع الملك الهندي",
                steps: [
                    { move: "d4", from: "d2", to: "d4", hint: "الأبيض يبدأ ببيدق الوزير." },
                    { move: "Nf6", from: "g8", to: "f6", hint: "أنت تلعب بالأسود وتطور حصانك الملكي." },
                    { move: "c4", from: "c2", to: "c4", hint: "الأبيض يوسع سيطرته بالوسط." },
                    { move: "g6", from: "g7", to: "g6", hint: "جهز لتبييت الفيانكيتو الحصين." }
                ]
            }
        };

        var foundationPieces = {
            king: { name: "حركة الملك (King)", fen: "8/8/8/3K4/8/8/8/8 w - - 0 1", hint: "تحرك الملك مربعاً واحداً في أي اتجاه.", arrows: [{from:"d5", to:"d6"}, {from:"d5", to:"e5"}, {from:"d5", to:"c5"}] },
            queen: { name: "حركة الوزير (Queen)", fen: "8/8/8/3Q4/8/8/8/8 w - - 0 1", hint: "يتحرك الوزير أفقياً وعمودياً وبشكل مائل لأي عدد من المربعات.", arrows: [{from:"d5", to:"d8"}, {from:"d5", to:"h5"}, {from:"d5", to:"a2"}] },
            rook: { name: "حركة القلعة (Rook)", fen: "8/8/8/3R4/8/8/8/8 w - - 0 1", hint: "تحرك القلعة أفقياً وعمودياً فقط.", arrows: [{from:"d5", to:"d8"}, {from:"d5", to:"d1"}] },
            bishop: { name: "حركة الفيل (Bishop)", fen: "8/8/8/3B4/8/8/8/8 w - - 0 1", hint: "يتحرك الفيل بشكل مائل فقط.", arrows: [{from:"d5", to:"g8"}, {from:"d5", to:"a2"}] },
            knight: { name: "حركة الحصان (Knight)", fen: "8/8/8/3N4/8/8/8/8 w - - 0 1", hint: "يتحرك الحصان على شكل حرف L ويمكنه قفز القطع.", arrows: [{from:"d5", to:"e7"}, {from:"d5", to:"f6"}] },
            pawn: { name: "حركة البيدق (Pawn)", fen: "8/8/4P3/8/8/8/8/8 w - - 0 1", hint: "يتقدم البيدق خطوة للأمام (أو خطوتين في النقلة الأولى).", arrows: [{from:"e6", to:"e5"}] }
        };

        var currentFoundationKey = null;

        window.addEventListener('load', function() {
            loadUserData();
            
            var config = {
                draggable: true,
                position: 'start',
                pieceTheme: 'https://chessboardjs.com/img/chesspieces/wikipedia/{piece}.png',
                onDragStart: onDragStart,
                onDrop: onDrop,
                onSnapEnd: onSnapEnd
            };
            board = Chessboard('board', config);

            setTimeout(() => {
                openStreakModal();
                const loader = document.getElementById('loading-screen');
                loader.style.opacity = '0';
                setTimeout(() => loader.style.display = 'none', 500);
            }, 500);
        });

        function switchTab(tabName) {
            document.getElementById('game-screen').style.display = 'none';
            document.querySelectorAll('.tab-content').forEach(t => t.classList.remove('active'));
            document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));

            if (tabName === 'openings') {
                document.getElementById('tab-openings').classList.add('active');
                document.querySelectorAll('.nav-item')[0].classList.add('active');
            } else if (tabName === 'foundations') {
                document.getElementById('tab-foundations').classList.add('active');
                document.querySelectorAll('.nav-item')[1].classList.add('active');
            } else if (tabName === 'endgames') {
                document.getElementById('tab-endgames').classList.add('active');
                document.querySelectorAll('.nav-item')[2].classList.add('active');
            } else if (tabName === 'games') {
                document.getElementById('tab-games').classList.add('active');
                document.querySelectorAll('.nav-item')[3].classList.add('active');
            } else if (tabName === 'settings') {
                document.getElementById('tab-settings').classList.add('active');
                document.querySelectorAll('.nav-item')[4].classList.add('active');
            }
            clearArrows();
        }

        function startOpeningGame(key) {
            isFoundationMode = false;
            currentLine = openings[key];
            game.reset();
            
            if (['sicilian', 'caroKann', 'kingsIndian'].includes(key)) {
                board.orientation('black');
            } else {
                board.orientation('white');
            }

            document.querySelectorAll('.tab-content').forEach(t => t.classList.remove('active'));
            document.getElementById('game-screen').style.display = 'flex';
            document.getElementById('openingGameTitle').innerText = currentLine.name;
            document.getElementById('actionBtnMain').innerText = "إعادة التدريب 🔄";
            document.getElementById('actionBtnMain').onclick = resetOpeningGame;
            
            board.resize();
            board.position('start', false);
            
            currentStep = 0;
            updateInstruction();
            document.getElementById('status').innerText = "";
        }

        function startFoundationPiece(pieceKey) {
            isFoundationMode = true;
            currentFoundationKey = pieceKey;
            let pieceData = foundationPieces[pieceKey];
            game.load(pieceData.fen);
            
            document.querySelectorAll('.tab-content').forEach(t => t.classList.remove('active'));
            document.getElementById('game-screen').style.display = 'flex';
            document.getElementById('openingGameTitle').innerText = pieceData.name;
            document.getElementById('instructionText').innerText = pieceData.hint;
            document.getElementById('status').innerText = "جرّب تحريك القطعة بنفسك على الرقعة!";
            document.getElementById('actionBtnMain').innerText = "إعادة وضع القطعة 🔄";
            document.getElementById('actionBtnMain').onclick = () => startFoundationPiece(currentFoundationKey);
            
            board.orientation('white');
            board.resize();
            board.position(pieceData.fen, false);
            
            clearArrows();
            setTimeout(() => {
                pieceData.arrows.forEach(arr => {
                    drawArrow(arr.from, arr.to, "#2ed573", 4, true);
                });
            }, 300);
        }

        function closeOpeningGame() {
            document.getElementById('game-screen').style.display = 'none';
            if(isFoundationMode) {
                switchTab('foundations');
            } else {
                switchTab('openings');
            }
        }

        function updateInstruction() {
            if (currentStep < currentLine.steps.length) {
                var step = currentLine.steps[currentStep];
                document.getElementById('instructionText').innerText = 
                    `الخطوة (${currentStep + 1} من ${currentLine.steps.length}): ` + step.hint;
                
                setTimeout(() => {
                    drawArrow(step.from, step.to, "#2ed573", 4, false);
                }, 250);
            } else {
                document.getElementById('instructionText').innerText = "🏆 كفو يا بطل! أتممت هذه الافتتاحية بنجاح تام وتم حفظ التقدم.";
                clearArrows();
                
                if (!appData.completedOpenings.includes(currentLine.key)) {
                    appData.completedOpenings.push(currentLine.key);
                    saveUserData();
                    updateUIStats();
                }
            }
        }

        function drawArrow(fromSq, toSq, color, width, isMulti = false) {
            pendingArrow = { fromSq, toSq, color, width, isMulti };
            setTimeout(() => {
                var canvas = document.getElementById('arrow-canvas');
                if(!canvas) return;
                var boardEl = document.getElementById('board');
                if(!boardEl) return;
                
                var widthPx = boardEl.clientWidth;
                var heightPx = boardEl.clientHeight;
                if(widthPx === 0) return;

                canvas.width = widthPx;
                canvas.height = heightPx;
                
                var ctx = canvas.getContext('2d');
                if(!isMulti) {
                    ctx.clearRect(0, 0, canvas.width, canvas.height);
                }
                
                var sqSize = canvas.width / 8;
                
                function getCoords(sq) {
                    var file = sq.charCodeAt(0) - 97;
                    var rank = 8 - parseInt(sq[1]);
                    if (board.orientation() === 'black') {
                        file = 7 - file;
                        rank = 7 - rank;
                    }
                    return { x: (file + 0.5) * sqSize, y: (rank + 0.5) * sqSize };
                }

                var start = getCoords(fromSq);
                var end = getCoords(toSq);

                ctx.beginPath();
                ctx.moveTo(start.x, start.y);
                ctx.lineTo(end.x, end.y);
                ctx.strokeStyle = color;
                ctx.lineWidth = width;
                ctx.lineCap = 'round';
                ctx.stroke();

                var angle = Math.atan2(end.y - start.y, end.x - start.x);
                ctx.beginPath();
                ctx.moveTo(end.x, end.y);
                ctx.lineTo(end.x - 12 * Math.cos(angle - Math.PI / 6), end.y - 12 * Math.sin(angle - Math.PI / 6));
                ctx.lineTo(end.x - 12 * Math.cos(angle + Math.PI / 6), end.y - 12 * Math.sin(angle + Math.PI / 6));
                ctx.fillStyle = color;
                ctx.fill();
            }, 100);
        }

        function clearArrows() {
            pendingArrow = null;
            var canvas = document.getElementById('arrow-canvas');
            if (canvas) {
                var ctx = canvas.getContext('2d');
                ctx.clearRect(0, 0, canvas.width, canvas.height);
            }
        }

        function onDragStart (source, piece, position, orientation) {
            if (game.game_over()) return false;
            
            if (isFoundationMode) {
                // في وضع الأساسيات، اسمح بتحريك القطعة الموجودة فقط
                return true;
            }

            if (!currentLine || currentStep >= currentLine.steps.length) return false;
        }

        function onDrop (source, target) {
            if (isFoundationMode) {
                var move = game.move({ from: source, to: target, promotion: 'q' });
                if (move === null) return 'snapback';
                board.position(game.fen());
                return;
            }

            if (!currentLine || currentStep >= currentLine.steps.length) return 'snapback';

            var move = game.move({ from: source, to: target, promotion: 'q' });
            if (move === null) return 'snapback';

            currentStep++;
            board.position(game.fen());
            clearArrows();

            let isBlackOpening = ['sicilian', 'caroKann', 'kingsIndian'].includes(currentLine.key);
            let aiTurnCondition = isBlackOpening ? (currentStep % 2 === 0) : (currentStep % 2 === 1);

            if (currentStep < currentLine.steps.length && aiTurnCondition) {
                setTimeout(function() {
                    var aiMoveStr = currentLine.steps[currentStep].move;
                    game.move(aiMoveStr);
                    board.position(game.fen());
                    currentStep++;
                    updateInstruction();
                }, 400);
            } else {
                updateInstruction();
            }

            if (currentStep >= currentLine.steps.length) {
                document.getElementById('status').innerText = "🏆 ممتاز! تم اجتياز التدريب بنجاح.";
            }
        }

        function onSnapEnd () { board.position(game.fen()); }

        function resetOpeningGame() {
            if (isFoundationMode) return;
            if (!currentLine) return;
            game.reset();
            board.position('start');
            currentStep = 0;
            updateInstruction();
            document.getElementById('status').innerText = "";
        }

        window.addEventListener('resize', function() {
            if (board) {
                board.resize();
                if(pendingArrow) {
                    drawArrow(pendingArrow.fromSq, pendingArrow.toSq, pendingArrow.color, pendingArrow.width, pendingArrow.isMulti);
                }
            }
        });
    </script>
</body>
</html> 
