<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ChessCraft - مدرسة الشطرنج الذكية</title>
    <!-- مكتبة Chess.js -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/chess.js/0.10.3/chess.min.js"></script>
    <!-- مكتبة Chessboard.js ومسارات القطع الرسمية -->
    <link rel="stylesheet" href="https://unpkg.com/@chrisoakman/chessboardjs@1.0.0/dist/chessboard-1.0.0.min.css">
    <script src="https://code.jquery.com/jquery-3.5.1.min.js"></script>
    <script src="https://unpkg.com/@chrisoakman/chessboardjs@1.0.0/dist/chessboard-1.0.0.min.js"></script>
    
    <style>
        *, *::before, *::after { box-sizing: border-box; }
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #121824;
            color: #ffffff;
            margin: 0;
            padding: 0;
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        /* شاشة التحميل Loading Screen */
        #loading-screen {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: #121824;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 9999;
            transition: opacity 0.5s ease;
        }
        .loader-logo {
            font-size: 2.5rem;
            color: #ff9f43;
            font-weight: bold;
            margin-bottom: 20px;
        }
        .spinner {
            width: 50px;
            height: 50px;
            border: 4px solid rgba(255, 159, 67, 0.2);
            border-top: 4px solid #ff9f43;
            border-radius: 50%;
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        /* الحاويات الرئيسية */
        .app-container {
            width: 100%;
            max-width: 480px;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            padding: 15px;
        }

        /* لوحة التحكم الرئيسية Dashboard */
        .dashboard {
            display: flex;
            flex-direction: column;
            gap: 15px;
            width: 100%;
        }
        .dash-header {
            text-align: center;
            padding: 10px 0;
        }
        .dash-header h1 {
            color: #ff9f43;
            margin: 0;
            font-size: 1.8rem;
        }
        .dash-header p {
            color: #8395a7;
            font-size: 0.95rem;
            margin: 5px 0 0 0;
        }
        .section-title {
            color: #f5f6fa;
            font-size: 1.1rem;
            margin: 10px 0 5px 0;
            border-right: 3px solid #ff9f43;
            padding-right: 8px;
        }
        .cards-grid {
            display: grid;
            grid-template-columns: 1fr;
            gap: 12px;
        }
        .opening-card {
            background: #1e2736;
            border: 1px solid rgba(255, 255, 255, 0.05);
            padding: 15px;
            border-radius: 12px;
            cursor: pointer;
            transition: 0.2s;
            display: flex;
            justify-content: space-between;
            align-items: center;
            box-shadow: 0 4px 10px rgba(0,0,0,0.3);
        }
        .opening-card:hover {
            background: #273142;
            border-color: #ff9f43;
            transform: translateY(-2px);
        }
        .card-info h3 {
            margin: 0 0 5px 0;
            color: #ff9f43;
            font-size: 1.1rem;
        }
        .card-info p {
            margin: 0;
            color: #a4b0be;
            font-size: 0.85rem;
        }
        .card-arrow {
            color: #ff9f43;
            font-size: 1.2rem;
            font-weight: bold;
        }

        /* شاشة اللعب (Game View) */
        .game-view {
            display: none;
            flex-direction: column;
            gap: 15px;
            width: 100%;
        }
        .game-header-bar {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        .back-btn {
            background: #2f3640;
            color: #f5f6fa;
            border: none;
            padding: 8px 14px;
            border-radius: 8px;
            cursor: pointer;
            font-weight: bold;
            font-size: 0.9rem;
        }
        .back-btn:hover { background: #487eb0; }
        
        .board-wrapper {
            position: relative;
            width: 100%;
            background: #1e2736;
            padding: 8px;
            border-radius: 12px;
            box-shadow: 0 8px 20px rgba(0,0,0,0.4);
        }
        #board {
            width: 100%;
            border-radius: 6px;
            overflow: hidden;
        }

        /* طبقة الأسهم التوجيهية فوق الرقعة */
        #arrow-canvas {
            position: absolute;
            top: 8px;
            left: 8px;
            width: calc(100% - 16px);
            height: calc(100% - 16px);
            pointer-events: none;
            z-index: 10;
        }

        .game-panel {
            background: #1e2736;
            padding: 12px;
            border-radius: 12px;
            display: flex;
            flex-direction: column;
            gap: 10px;
        }
        .instruction-box {
            background: rgba(46, 213, 115, 0.1);
            border-right: 4px solid #2ed573;
            padding: 10px;
            border-radius: 6px;
            font-size: 0.95rem;
            line-height: 1.4;
            color: #d1d8e0;
        }
        .status-msg {
            text-align: center;
            font-weight: bold;
            color: #ff5252;
            font-size: 0.9rem;
            min-height: 18px;
        }
        .action-btns {
            display: flex;
            gap: 10px;
        }
        button.control-btn {
            flex: 1;
            background-color: #e58e26;
            color: #fff;
            border: none;
            padding: 10px;
            font-size: 0.9rem;
            font-weight: bold;
            border-radius: 8px;
            cursor: pointer;
            transition: 0.2s;
        }
        button.control-btn:hover { background-color: #fa983a; }
    </style>
</head>
<body>

    <!-- شاشة التحميل -->
    <div id="loading-screen">
        <div class="loader-logo">♟️ ChessCraft</div>
        <div class="spinner"></div>
    </div>

    <div class="app-container">
        <!-- لوحة التحكم الرئيسية (Dashboard) -->
        <div id="dashboard" class="dashboard">
            <div class="dash-header">
                <h1>ChessCraft</h1>
                <p>اختر الافتتاحية وتعلّم بالأسهم التوجيهية البسيطة!</p>
            </div>

            <div class="section-title">افتتاحيات القطع البيضاء (White)</div>
            <div class="cards-grid">
                <div class="opening-card" onclick="loadOpening('italian')">
                    <div class="card-info">
                        <h3>الافتتاحية الإيطالية</h3>
                        <p>السيطرة السريعة على الوسط ومهاجمة نقطة الضعف</p>
                    </div>
                    <div class="card-arrow">←</div>
                </div>
                <div class="opening-card" onclick="loadOpening('london')">
                    <div class="card-info">
                        <h3>نظام لندن (London System)</h3>
                        <p>افتتاحية آمنة، قوية وسهلة الفهم للمبتدئين</p>
                    </div>
                    <div class="card-arrow">←</div>
                </div>
            </div>

            <div class="section-title" style="margin-top: 15px;">افتتاحيات القطع السوداء (Black)</div>
            <div class="cards-grid">
                <div class="opening-card" onclick="loadOpening('sicilian')">
                    <div class="card-info">
                        <h3>الدفاع الصقلي</h3>
                        <p>الهجوم المضاد المثير والخطير ضد الأبيض</p>
                    </div>
                    <div class="card-arrow">←</div>
                </div>
            </div>
        </div>

        <!-- واجهة اللعب وتدريب الافتتاحيات -->
        <div id="game-view" class="game-view">
            <div class="game-header-bar">
                <button class="back-btn" onclick="backToDashboard()">🏠 الرئيسية</button>
                <h3 id="currentOpeningTitle" style="color: #ff9f43; margin:0; font-size: 1.1rem;">اسم الافتتاحية</h3>
            </div>

            <div class="board-wrapper">
                <div id="board"></div>
                <canvas id="arrow-canvas"></canvas>
            </div>

            <div class="game-panel">
                <div class="instruction-box" id="instructionText">جاري تحميل الإرشادات...</div>
                <div id="status" class="status-msg"></div>
                <div class="action-btns">
                    <button class="control-btn" onclick="showHintArrow()">💡 أين ألعب؟ (سهم أخضر)</button>
                    <button class="control-btn" style="background-color: #393e46;" onclick="resetCurrentGame()">إعادة 🔄</button>
                </div>
            </div>
        </div>
    </div>

    <script>
        // إخفاء شاشة التحميل بعد ثانية واحدة لإعطاء طابع احترافي
        window.addEventListener('load', function() {
            setTimeout(function() {
                const loader = document.getElementById('loading-screen');
                loader.style.opacity = '0';
                setTimeout(() => loader.style.display = 'none', 500);
            }, 1000);
        });

        var board = null;
        var game = new Chess();
        var currentStep = 0;
        var currentLine = null;

        var openings = {
            italian: {
                name: "الافتتاحية الإيطالية",
                steps: [
                    { move: "e4", from: "e2", to: "e4", hint: "انقل بيدق الملك خطوتين للأمام (السهم الأخضر يوضح لك الحركة المطلوبة)." },
                    { move: "e5", from: "e7", to: "e5", hint: "الخصم يرد بنفس النقلة في الوسط." },
                    { move: "Nf3", from: "g1", to: "f3", hint: "أخرج الحصان الملكي ليهاجم بيدق الخصم." },
                    { move: "Nc6", from: "b8", to: "c6", hint: "الخصم يحمي بيداكه بالحصان." },
                    { move: "Bc4", from: "f1", to: "c4", hint: "أخرج الفيل لتهديد أضعف مربع عند الخصم (تحذير باللون الأحمر على المربع المستهدف!)." }
                ]
            },
            london: {
                name: "نظام لندن القوي",
                steps: [
                    { move: "d4", from: "d2", to: "d4", hint: "ابدأ بدفع بيدق الوزير للأمام خطوتين." },
                    { move: "Nf6", from: "g8", to: "f6", hint: "الخصم يخرج حصانه." },
                    { move: "Bf4", from: "c1", to: "f4", hint: "أخرج فيل الوزير إلى المربع الآمن (بناء جدار لندن)." },
                    { move: "e6", from: "e7", to: "e6", hint: "الخصم يغلق الموقف جزئياً." },
                    { move: "e3", from: "e2", to: "e3", hint: "ادعم مركزك بدفع بيدق الملك خطوة واحدة." }
                ]
            },
            sicilian: {
                name: "الدفاع الصقلي",
                steps: [
                    { move: "e4", from: "e2", to: "e4", hint: "الأبيض يبدأ بالهجوم في الوسط." },
                    { move: "c5", from: "c7", to: "c5", hint: "أنت تلعب بالأسود وترد بالدفاع الصقلي المثير!" },
                    { move: "Nf3", from: "g1", to: "f3", hint: "الأبيض يخرج حصانه." },
                    { move: "d6", from: "d7", to: "d6", hint: "ثبت دفاعك بدفع البيدق خطوة واحدة." },
                    { move: "d4", from: "d2", to: "d4", hint: "الأبيض يضرب الوسط بقوة." },
                    { move: "cxd4", from: "c5", to: "d4", hint: "خذ بيدق الأبيض وافتح اللعب (انتبه لتهديدات الأبيض هنا!)." }
                ]
            }
        };

        function loadOpening(key) {
            currentLine = openings[key];
            game.reset();
            
            if (key === 'sicilian') {
                board.orientation('black');
            } else {
                board.orientation('white');
            }

            board.position('start');
            currentStep = 0;
            
            document.getElementById('dashboard').style.display = 'none';
            document.getElementById('game-view').style.display = 'flex';
            document.getElementById('currentOpeningTitle').innerText = currentLine.name;
            
            updateInstruction();
            document.getElementById('status').innerText = "";
            clearArrows();
        }

        function backToDashboard() {
            document.getElementById('game-view').style.display = 'none';
            document.getElementById('dashboard').style.display = 'flex';
            clearArrows();
        }

        function updateInstruction() {
            clearArrows();
            if (currentStep < currentLine.steps.length) {
                document.getElementById('instructionText').innerText = 
                    `الخطوة (${currentStep + 1} من ${currentLine.steps.length}): ` + currentLine.steps[currentStep].hint;
            } else {
                document.getElementById('instructionText').innerText = "🎉 بطل! لقد أتممت هذه الافتتاحية بنجاح تام وحفظت خطواتها.";
            }
        }

        // رسم الأسهم (أخضر للحركة الصحيحة، وأحمر للتهديدات)
        function showHintArrow() {
            if (!currentLine || currentStep >= currentLine.steps.length) return;
            var step = currentLine.steps[currentStep];
            
            // سهم أخضر للنقلة المطلوبة
            drawArrow(step.from, step.to, "#2ed573", 4);
            
            // إذا كانت الخطوة الأخيرة في الإيطالية مثلاً، نرسم سهم تحذير/تهديد أحمر على مربع الضعف (f7)
            if (currentLine === openings.italian && currentStep === 4) {
                drawArrow("c4", "f7", "#ff5252", 3);
            }
        }

        function drawArrow(fromSq, toSq, color, width) {
            var canvas = document.getElementById('arrow-canvas');
            var ctx = canvas.getContext('2d');
            
            // ضبط مقاس الكانفاس ليتطابق مع الرقعة بدقة
            canvas.width = document.getElementById('board').clientWidth;
            canvas.height = document.getElementById('board').clientHeight;
            
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            var sqSize = canvas.width / 8;
            
            function getCoords(sq) {
                var file = sq.charCodeAt(0) - 97; // a-h -> 0-7
                var rank = 8 - parseInt(sq[1]);   // 1-8 -> 7-0
                
                // عكس الإحداثيات إذا كانت اللوحة مقلوبة (للأسود)
                if (board.orientation() === 'black') {
                    file = 7 - file;
                    rank = 7 - rank;
                }
                
                return {
                    x: (file + 0.5) * sqSize,
                    y: (rank + 0.5) * sqSize
                };
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

            // رسم رأس السهم
            var angle = Math.atan2(end.y - start.y, end.x - start.x);
            ctx.beginPath();
            ctx.moveTo(end.x, end.y);
            ctx.lineTo(end.x - 12 * Math.cos(angle - Math.PI / 6), end.y - 12 * Math.sin(angle - Math.PI / 6));
            ctx.lineTo(end.x - 12 * Math.cos(angle + Math.PI / 6), end.y - 12 * Math.sin(angle + Math.PI / 6));
            ctx.fillStyle = color;
            ctx.fill();
        }

        function clearArrows() {
            var canvas = document.getElementById('arrow-canvas');
            if (canvas) {
                var ctx = canvas.getContext('2d');
                ctx.clearRect(0, 0, canvas.width, canvas.height);
            }
        }

        function onDragStart (source, piece, position, orientation) {
            if (!currentLine || currentStep >= currentLine.steps.length) return false;
            if (game.game_over()) return false;
        }

        function onDrop (source, target) {
            if (!currentLine || currentStep >= currentLine.steps.length) return 'snapback';

            var move = game.move({
                from: source,
                to: target,
                promotion: 'q'
            });

            if (move === null) return 'snapback';

            currentStep++;
            updateInstruction();
            board.position(game.fen());

            let isBlackOpening = (currentLine === openings.sicilian);
            let aiTurnCondition = isBlackOpening ? (currentStep % 2 === 0) : (currentStep % 2 === 1);

            if (currentStep < currentLine.steps.length && aiTurnCondition) {
                setTimeout(function() {
                    var aiMoveStr = currentLine.steps[currentStep].move;
                    game.move(aiMoveStr);
                    board.position(game.fen());
                    currentStep++;
                    updateInstruction();
                }, 400);
            }

            if (currentStep >= currentLine.steps.length) {
                document.getElementById('status').innerText = "🏆 تم إنهاء التدريب بنجاح تام!";
                clearArrows();
            }
        }

        function onSnapEnd () {
            board.position(game.fen());
        }

        function resetCurrentGame() {
            if (!currentLine) return;
            game.reset();
            board.position('start');
            currentStep = 0;
            updateInstruction();
            document.getElementById('status').innerText = "";
        }

        var config = {
            draggable: true,
            position: 'start',
            pieceTheme: 'https://chessboardjs.com/img/chesspieces/wikipedia/{piece}.png',
            onDragStart: onDragStart,
            onDrop: onDrop,
            onSnapEnd: onSnapEnd
        };
        board = Chessboard('board', customResizeBoard = config);
        
        // ضبط حجم الكانفاس تلقائياً عند تغيير الحجم
        window.addEventListener('resize', function() {
            if (board) board.resize();
        });
    </script>
</body>
</html> 
