<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AeroMaster - 통합 훈련 시스템</title>
    <style>
        /* 기본 스타일 및 스크롤 활성화 */
        body {
            margin: 0;
            font-family: 'Arial', sans-serif;
            background: #1a1a2e;
            color: white;
            min-height: 100vh;
            overflow-y: auto; /* 스크롤 해결 */
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        .container {
            width: 90%;
            max-width: 600px;
            padding: 20px;
            text-align: center;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 15px;
            margin: 20px 0;
            box-shadow: 0 0 20px rgba(0,0,0,0.5);
        }
        button {
            padding: 12px 24px;
            margin: 10px;
            cursor: pointer;
            border-radius: 8px;
            border: none;
            font-weight: bold;
            transition: 0.3s;
        }
        .btn-main { background: #16213e; color: white; }
        .btn-answer { width: 100%; background: #0f3460; color: white; margin: 5px 0; }
        .btn-answer:hover { background: #e94560; }
        .hidden { display: none; }
        
        /* 관리자 모드 스타일 */
        #admin-panel { background: #222; border: 2px solid #e94560; padding: 15px; margin-top: 50px; }
        textarea { width: 100%; height: 200px; background: #000; color: #0f0; font-family: monospace; }
        
        /* 애니메이션 */
        .shake { animation: shake 0.5s; }
        @keyframes shake {
            0%, 100% { transform: translateX(0); }
            25% { transform: translateX(-10px); }
            75% { transform: translateX(10px); }
        }
    </style>
</head>
<body>

    <div id="start-screen" class="container">
        <h1>👨‍✈️ AeroMaster Pro</h1>
        <p>항공 지식 실전 훈련 시스템</p>
        <input type="text" id="nickname" placeholder="조종사 호출부호 입력" style="padding:10px; width:80%;">
        <br>
        <button class="btn-main" onclick="startGame('easy')">EASY</button>
        <button class="btn-main" onclick="startGame('medium')">NORMAL</button>
        <button class="btn-main" onclick="startGame('hard')">HARD</button>
        <br>
        <button onclick="toggleAdmin()" style="background:none; color:gray; font-size:12px;">Admin Mode</button>
    </div>

    <div id="quiz-screen" class="container hidden">
        <div id="info-bar">Score: <span id="score">0</span> | Combo: <span id="combo">0</span></div>
        <h2 id="question">질문이 여기에 표시됩니다.</h2>
        <div id="answer-buttons"></div>
    </div>

    <div id="admin-panel" class="container hidden">
        <h3>🛠 데이터 관리자</h3>
        <p>아래 JSON 데이터를 수정하고 저장하세요.</p>
        <textarea id="json-input"></textarea>
        <br>
        <button class="btn-main" onclick="saveData()">변경사항 저장</button>
        <button onclick="toggleAdmin()">닫기</button>
    </div>

    <audio id="s-ok" src="https://assets.mixkit.co/active_storage/sfx/2013/2013-preview.mp3"></audio>
    <audio id="s-no" src="https://assets.mixkit.co/active_storage/sfx/951/951-preview.mp3"></audio>

    <script>
        // --- 문제 데이터 (여기에 모든 문제를 넣으세요) ---
        let quizData = {
            easy: [
                {q: "비행기 왼쪽 날개 끝등의 색깔은?", a: ["빨간색", "초록색", "파란색", "노란색"], tag: "Nav"},
                {q: "조종석을 부르는 명칭은?", a: ["콕핏", "갤리", "캐빈", "라운지"], tag: "Common"}
                // ... 조종사님이 주신 180개 문제를 여기에 쭉 붙여넣으시면 됩니다.
            ],
            medium: [],
            hard: []
        };

        // 로컬 스토리지에 데이터가 있으면 불러오기
        const savedData = localStorage.getItem('aeroQuizData');
        if(savedData) quizData = JSON.parse(savedData);

        let currentQuestions = [];
        let score = 0;
        let combo = 0;

        function startGame(level) {
            const nick = document.getElementById('nickname').value;
            if(!nick) { alert("호출부호를 입력하세요!"); return; }
            
            currentQuestions = [...quizData[level]].sort(() => Math.random() - 0.5).slice(0, 10);
            score = 0;
            combo = 0;
            
            document.getElementById('start-screen').classList.add('hidden');
            document.getElementById('quiz-screen').classList.remove('hidden');
            showQuestion();
        }

        function showQuestion() {
            if(currentQuestions.length === 0) {
                alert(`임무 완료! 최종 점수: ${score}`);
                location.reload();
                return;
            }
            const qObj = currentQuestions.pop();
            document.getElementById('question').innerText = qObj.q;
            const btnArea = document.getElementById('answer-buttons');
            btnArea.innerHTML = '';

            const answers = [...qObj.a];
            const correctBtn = answers[0];
            answers.sort(() => Math.random() - 0.5);

            answers.forEach(ans => {
                const btn = document.createElement('button');
                btn.className = 'btn-answer';
                btn.innerText = ans;
                btn.onclick = () => {
                    if(ans === correctBtn) {
                        document.getElementById('s-ok').play();
                        score += 10 + (combo * 2);
                        combo++;
                        showQuestion();
                    } else {
                        document.getElementById('s-no').play();
                        document.body.classList.add('shake');
                        setTimeout(() => document.body.classList.remove('shake'), 500);
                        combo = 0;
                        alert("오답입니다!");
                    }
                    updateDisplay();
                };
                btnArea.appendChild(btn);
            });
        }

        function updateDisplay() {
            document.getElementById('score').innerText = score;
            document.getElementById('combo').innerText = combo;
        }

        // 관리자 기능
        function toggleAdmin() {
            const panel = document.getElementById('admin-panel');
            panel.classList.toggle('hidden');
            document.getElementById('json-input').value = JSON.stringify(quizData, null, 4);
        }

        function saveData() {
            try {
                const newData = JSON.parse(document.getElementById('json-input').value);
                quizData = newData;
                localStorage.setItem('aeroQuizData', JSON.stringify(newData));
                alert("데이터가 성공적으로 저장되었습니다!");
                location.reload();
            } catch(e) {
                alert("JSON 형식이 잘못되었습니다.");
            }
        }
    </script>
</body>
</html>
