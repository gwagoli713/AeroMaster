
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Aviation Quiz Master - Premium</title>
    <link href="https://fonts.googleapis.com/css2?family=Black+Han+Sans&family=Orbitron:wght@400;700&family=Pretendard:wght@400;700&display=swap" rel="stylesheet">
    <link href="https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css" rel="stylesheet">
    <style>
        :root {
            --primary: #6366f1;
            --bg-dark: #0f172a;
            --accent-gold: #fbbf24;
            --danger: #ef4444;
            --success: #22c55e;
            --glass: rgba(255, 255, 255, 0.05);
        }

        body {
            font-family: 'Pretendard', sans-serif;
            background: var(--bg-dark);
            background-image: 
                radial-gradient(at 0% 0%, rgba(99, 102, 241, 0.15) 0, transparent 50%),
                radial-gradient(at 100% 100%, rgba(251, 191, 36, 0.1) 0, transparent 50%);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            color: #f8fafc;
            overflow: hidden;
        }

        .container {
            background: rgba(30, 41, 59, 0.7);
            backdrop-filter: blur(20px);
            padding: 3rem;
            border-radius: 30px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
            width: 90%;
            max-width: 550px;
            text-align: center;
        }

        h1 {
            font-family: 'Black Han Sans', sans-serif;
            font-size: 2.8rem;
            background: linear-gradient(to right, #818cf8, #c084fc);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            margin-bottom: 0.5rem;
        }

        .best-score-badge {
            font-family: 'Orbitron', sans-serif;
            color: var(--accent-gold);
            font-size: 0.9rem;
            letter-spacing: 2px;
            margin-bottom: 2rem;
            display: block;
        }

        input, select, button {
            width: 100%;
            padding: 15px;
            margin: 10px 0;
            border-radius: 15px;
            border: 1px solid rgba(255,255,255,0.1);
            background: rgba(0,0,0,0.2);
            color: white;
            font-size: 1rem;
            transition: 0.3s;
        }

        button {
            background: var(--primary);
            border: none;
            font-weight: 800;
            cursor: pointer;
            box-shadow: 0 4px 14px 0 rgba(99, 102, 241, 0.39);
        }

        button:hover { transform: translateY(-3px); box-shadow: 0 6px 20px rgba(99, 102, 241, 0.5); }

        .option-btn {
            background: var(--glass);
            text-align: left;
            padding-left: 25px;
        }

        .option-btn:hover { background: rgba(255, 255, 255, 0.15); border-color: var(--primary); }

        /* 상태창 */
        .status-panel {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            margin-bottom: 20px;
            font-family: 'Orbitron', sans-serif;
        }

        .stat-item {
            background: rgba(0,0,0,0.3);
            padding: 12px;
            border-radius: 12px;
            border-bottom: 3px solid var(--primary);
        }

        /* 타이머 */
        .timer-track {
            height: 8px;
            background: rgba(255,255,255,0.1);
            border-radius: 4px;
            margin: 25px 0;
            overflow: hidden;
        }

        #timer-bar {
            height: 100%;
            width: 100%;
            background: linear-gradient(90deg, var(--primary), #c084fc);
            transition: width 0.1s linear;
        }

        /* 25% 확률 이벤트 효과 */
        .turbulence-fx { animation: shake 0.2s infinite; }
        @keyframes shake {
            0% { transform: translate(1px, 1px); }
            50% { transform: translate(-1px, -1px); }
            100% { transform: translate(1px, -1px); }
        }

        .atc-highlight { background: rgba(251, 191, 36, 0.3) !important; border: 2px solid var(--accent-gold); }

        .hidden { display: none !important; }
    </style>
</head>
<body>

<div class="container animate__animated animate__fadeIn" id="main-frame">
    <div id="lobby-scene">
        <h1>PILOT QUIZ</h1>
        <span class="best-score-badge">🏆 MAX ALTITUDE: <span id="best-val">0</span></span>
        
        <input type="text" id="nick" placeholder="CALL SIGN">
        <select id="diff-level">
            <option value="easy">EASY (15s)</option>
            <option value="medium">MEDIUM (10s)</option>
            <option value="hard">HARD (5s)</option>
            <option value="timeattack">🔥 TIME ATTACK (5s)</option>
        </select>
        <select id="q-qty">
            <option value="10">10 QUESTIONS</option>
            <option value="20">20 QUESTIONS</option>
            <option value="30">30 QUESTIONS</option>
        </select>
        <button onclick="initFlight()">TAKE OFF</button>
        
        <div style="margin-top:40px; opacity:0.3; font-size:0.7rem; cursor:pointer" onclick="document.getElementById('admin-gate').classList.toggle('hidden')">
            ADMIN ACCESS
            <div id="admin-gate" class="hidden">
                <input type="text" id="adm-id" placeholder="ID">
                <input type="password" id="adm-pw" placeholder="PW">
                <button onclick="adminLogin()">ENTER</button>
            </div>
        </div>
    </div>

    <div id="quiz-scene" class="hidden">
        <div id="event-msg" style="height:25px; color:var(--accent-gold); font-weight:bold; margin-bottom:10px;"></div>
        <div class="status-panel">
            <div class="stat-item">ALT: <span id="cur-score">0</span></div>
            <div class="stat-item">COMBO: <span id="cur-combo">0</span></div>
        </div>
        <div class="timer-track"><div id="timer-bar"></div></div>
        <h2 id="q-text" style="font-size:1.4rem; min-height:80px; line-height:1.5;"></h2>
        <div id="options-box"></div>
    </div>

    <div id="end-scene" class="hidden">
        <h1 style="color:var(--accent-gold)">MISSION END</h1>
        <p id="final-report" style="font-size:1.2rem; margin:25px 0;"></p>
        <button onclick="location.reload()">RE-FLY</button>
    </div>
</div>

<script src="questions.js"></script>
<script>
    let questions = [], qIdx = 0, score = 0, combo = 0, timer, timeLeft, maxTime;

    // 최고 기록 불러오기
    document.getElementById('best-val').innerText = localStorage.getItem('aviationBest') || 0;

    function initFlight() {
        const diff = document.getElementById('diff-level').value;
        const qty = parseInt(document.getElementById('q-qty').value);
        
        questions = (diff === 'timeattack') ? 
            [...quizData.easy, ...quizData.medium, ...quizData.hard] : [...quizData[diff]];
        
        questions.sort(() => Math.random() - 0.5);
        questions = questions.slice(0, qty);

        document.getElementById('lobby-scene').classList.add('hidden');
        document.getElementById('quiz-scene').classList.remove('hidden');
        nextQ();
    }

    function nextQ() {
        if(qIdx >= questions.length) return endFlight();
        
        const data = questions[qIdx];
        const diff = document.getElementById('diff-level').value;
        const msg = document.getElementById('event-msg');
        const frame = document.getElementById('main-frame');
        
        frame.classList.remove('turbulence-fx');
        msg.innerText = "";
        maxTime = {easy:15, medium:10, hard:5, timeattack:5}[diff];
// 관리자 버튼 클릭 전파 방지 수정
    document.getElementById('admin-btn').addEventListener('click', (e) => {
        e.stopPropagation();
        document.getElementById('admin-login').classList.toggle('hidden');
    });

    function takeOff() {
        const diff = document.getElementById('diff-select').value;
        const qty = parseInt(document.getElementById('qty-select').value);
        
        // questions.js의 quizData 참고
        questions = (diff === 'timeattack') ? 
            [...quizData.easy, ...quizData.medium, ...quizData.hard] : [...quizData[diff]];
        
        questions.sort(() => Math.random() - 0.5);
        questions = questions.slice(0, qty);

        document.getElementById('lobby').classList.add('hidden');
        document.getElementById('quiz-screen').classList.remove('hidden');
        loadNext();
    }

    function loadNext() {
        if(qIdx >= questions.length) return finishMission();
        const data = questions[qIdx];
        const diff = document.getElementById('diff-select').value;
        const msg = document.getElementById('warn-msg');
        const frame = document.getElementById('ui-frame');
        
        frame.classList.remove('turbulence');
        msg.innerText = "";
        totalTime = {easy:15, medium:10, hard:5, timeattack:5}[diff];

        // 25% 확률 이벤트
        if(Math.random() < 0.25) {
            const ev = Math.floor(Math.random() * 3);
            if(ev === 0) { msg.innerText = "🚨 ENGINE FAIL: TIME X2"; totalTime /= 2; }
            else if(ev === 1) { msg.innerText = "📡 ATC: TARGET LOCKED"; }
            else { msg.innerText = "🌪 TURBULENCE ACTIVE"; frame.classList.add('turbulence'); totalTime /= 3; }
        }

        document.getElementById('q-text').innerText = data.q;
        const box = document.getElementById('ans-box');
        box.innerHTML = '';

        let ans = [...data.a];
        const correct = ans[0];
        ans.sort(() => Math.random() - 0.5);

        ans.forEach(a => {
            const btn = document.createElement('button');
            btn.className = 'option-btn animate__animated animate__fadeInUp';
            btn.innerText = a;
            if(msg.innerText.includes("ATC") && a === correct) btn.classList.add('atc-highlight');
            btn.onclick = () => judge(a, correct);
            box.appendChild(btn);
        });
        startClock();
    }

    function startClock() {
        clearInterval(timer);
        left = totalTime;
        timer = setInterval(() => {
            left -= 0.05;
            document.getElementById('timer-fill').style.width = (left/totalTime)*100 + "%";
            if(left <= 0) judge(null, null);
        }, 50);
    }

    function judge(u, c) {
        clearInterval(timer);
        const diff = document.getElementById('diff-select').value;
        const base = {easy:10, medium:20, hard:30, timeattack:40}[diff];

        if(u === c && u !== null) {
            combo++;
            score += base + (combo > 1 ? combo * 2 : 0);
            document.getElementById('q-text').style.color = "var(--neon-green)";
        } else {
            combo = 0;
            document.getElementById('q-text').style.color = "var(--neon-red)";
        }
        
        document.getElementById('cur-alt').innerText = score;
        document.getElementById('cur-combo').innerText = combo;
        qIdx++;
        setTimeout(() => {
            document.getElementById('q-text').style.color = "white";
            loadNext();
        }, 700);
    }

    function finishMission() {
        document.getElementById('quiz-screen').classList.add('hidden');
        document.getElementById('result-screen').classList.remove('hidden');
        const best = parseInt(localStorage.getItem('pilotBest') || 0);
        if(score > best) {
            localStorage.setItem('pilotBest', score);
            document.getElementById('final-report').innerHTML = `🎊 NEW RECORD 🎊<br>ALTITUDE: ${score}ft`;
        } else {
            document.getElementById('final-report').innerText = `TOTAL ALTITUDE: ${score}ft`;
        }
    }

    function authAdmin() {
        if(document.getElementById('a-id').value === 'tremill0103' && document.getElementById('a-pw').value === 'tt0104tt!') {
            document.getElementById('lobby').classList.add('hidden');
            document.getElementById('admin-panel').classList.remove('hidden');
        } else { alert("ACCESS DENIED"); }
    }

    function saveNewQ() {
        const q = document.getElementById('n-q').value, a1 = document.getElementById('n-a1').value;
        if(q && a1) {
            quizData[document.getElementById('n-diff').value].push({q, a: [a1, document.getElementById('n-a2').value, document.getElementById('n-a3').value, document.getElementById('n-a4').value]});
            alert("INJECTED");
        }
    }
</script>[questions.js](https://github.com/user-attachments/files/25053054/questions.js)
const quizData = {
    easy: [
        // --- 기존 문제 ---
        {q: "비행기 왼쪽 날개 끝등의 색깔은?", a: ["빨간색", "초록색", "파란색", "노란색"], tag: "Navigation"},
        {q: "조종석을 부르는 명칭은?", a: ["콕핏", "갤리", "캐빈", "라운지"], tag: "Common"},
        {q: "비행기가 활주로에 들어가기 전, 최종적으로 이륙 허가를 받는 곳은?", a: ["관제탑(Tower)", "계류장", "체크인 카운터", "항로 관제소"], tag: "ATC"},
        {q: "비행기 조종사와 관제사가 통신할 때 사용하는 공용 언어는?", a: ["영어", "한국어", "프랑스어", "스페인어"], tag: "Common"},
        {q: "비행기에서 비상사태를 알릴 때 외치는 말은?", a: ["메이데이(Mayday)", "헬프미", "에스오에스", "브레이크"], tag: "Emergency"},
        {q: "대한민국 인천국제공항의 IATA 코드는?", a: ["ICN", "GMP", "PUS", "CJU"], tag: "Airport"},
        {q: "비행기 조종사가 기내 방송을 할 때 자신을 지칭하는 말은?", a: ["기장", "매니저", "디렉터", "치프"], tag: "Common"},
        {q: "비행기가 하늘에 그리는 하얀 줄 모양의 구름은?", a: ["비행운", "안개", "매연", "뭉게구름"], tag: "Weather"},
        {q: "비행기 표를 예약할 때 적힌 'Economy'는 무엇을 의미하나요?", a: ["일반석", "일등석", "비즈니스석", "조종석"], tag: "Airport"},
        {q: "비행기 날개 끝에서 발생하는 공기 소용돌이를 줄여 연료 효율을 높이는 장치는?", a: ["윙렛", "플랩", "카나드", "스포일러"], tag: "Engineering"},
        {q: "미국 보잉(Boeing)사와 경쟁하는 유럽의 거대 항공기 제조사는?", a: ["에어버스(Airbus)", "록히드 마틴", "엠브라에르", "봄바디어"], tag: "History"},
        {q: "비행기 탑승 전 수하물을 부치고 탑승권을 받는 과정은?", a: ["체크인(Check-in)", "보딩", "이미그레이션", "커스텀"], tag: "Airport"},
        {q: "기내 안전을 위해 이착륙 시 반드시 매어야 하는 것은?", a: ["좌석 벨트", "구명조끼", "산소 마스크", "헤드셋"], tag: "Safety"},
        {q: "비행기 안에서 음식을 준비하고 승무원들이 머무는 공간은?", a: ["갤리(Galley)", "카고", "라운지", "벙크"], tag: "Interior"},
        {q: "여객기 화물칸에 실리는 짐을 무엇이라 하나요?", a: ["위탁 수하물", "기내 수하물", "휴대품", "면세품"], tag: "Airport"},
        {q: "비행기가 활주로를 떠나 이륙하는 단계는?", a: ["테이크오프", "랜딩", "택싱", "디센트"], tag: "Operation"},
        {q: "비행기의 승강타(Elevator)를 위로 올리면 기체는 어떻게 움직이나요?", a: ["기수가 올라간다", "기수가 내려간다", "왼쪽으로 기운다", "속도가 빨라진다"], tag: "Physics"},
        {q: "비행 중 기내 산소 마스크가 내려왔을 때 올바른 착용 순서는?", a: ["보호자가 착용 후 어린이를 도와준다", "어린이를 착용후 보호자가 쓴다", "승무원의 지시가 있을 때까지 기다린다", "옆 사람과 동시에 쓴다"], tag: "Safety"},
        {q: "여객기 화장실 변기에서 '슈욱' 하는 큰 소리가 나는 이유는?", a: ["진공 흡입 방식을 사용하여 오물을 배출하기 때문", "물 수압이 너무 강하기 때문", "엔진의 압력을 직접 끌어다 쓰기 때문", "외부 공기가 기내로 역류하기 때문"], tag: "Engineering"},
        {q: "비행기 이착륙 시 객실 창문 덮개를 반드시 열어야 하는 주된 이유는?", a: ["비상시 외부 상황 파악 및 구조 대원의 시야 확보", "객실 조명 밝기 조절", "승객들의 경치 구경", "기내 압력 조절을 위한 환기"], tag: "Safety"},
        {q: "B777 기종과 다른 광동체기를 구분하는 가장 큰 외형적 특징은?", a: ["메인 랜딩기어의 바퀴가 한 축에 6개(총 12개)", "수직 꼬리날개의 끝이 평평함", "조종석 유리창이 4매 구성", "동체 단면이 완전한 원형"], tag: "Aircraft"},
        {q: "B787 드림라이너의 창문에는 덮개(Shade) 대신 무엇이 달려 있나?", a: ["전기색 가변 투과 유리(에어크로믹)", "자동 편광 필터", "액체 질소 냉각 패널", "탄소 섬유 차광막"], tag: "Technology"},
        {q: "보잉은 조종간으로 '요크'를 쓰지만, 에어버스 기종이 채택한 조종 장치는?", a: ["사이드스틱(Side-stick)", "센터스틱", "조이스틱", "텔러"], tag: "Aircraft"},
        {q: "세계 최대의 화물기였으나 전쟁으로 파괴된 안토노프 An-225의 엔진 개수는?", a: ["6개", "4개", "8개", "10개"], tag: "History"},
        {q: "F-22 랩터의 엔진 노즐이 상하로 움직이며 기동성을 높이는 기술은?", a: ["2차원 추력 편향 제어(TVC)", "애프터버너 가변 노즐", "바이패스 가변 제어", "스텔스 배기 시스템"], tag: "Military"},
        {q: "머리 부분이 2층으로 툭 튀어나와 '하늘의 여왕'이라 불리는 기종은?", a: ["B747", "A380", "B777", "A330"], tag: "Aircraft"},
        {q: "비행기 전체가 2층으로 되어 있는 세계 최대의 여객기는?", a: ["A380", "B747", "B787", "A350"], tag: "Aircraft"},
        {q: "엔진이 날개 밑이 아니라 꼬리 부분에 2개 달려 있는 소형 여객기는?", a: ["B717 (또는 MD-80)", "B737", "A320", "B777"], tag: "Aircraft"},
        {q: "프로펠러가 외부에 노출되어 돌아가는 형태의 여객기를 부르는 말은?", a: ["터보프롭기", "제트기", "글라이더", "수상비행기"], tag: "Aircraft"},
        {q: "꼬리날개에 '태극 문양'이 그려진 대한민국의 대표 항공사는?", a: ["대한항공", "아시아나항공", "제주항공", "진에어"], tag: "Airline"},
        {q: "꼬리날개에 '색동저고리' 무늬가 그려진 대한민국의 항공사는?", a: ["아시아나항공", "대한항공", "에어부산", "에어서울"], tag: "Airline"},
        {q: "저비용 항공사(LCC) 중 주황색 상징색과 '돌하르방' 이미지를 사용하는 곳은?", a: ["제주항공", "티웨이항공", "이스타항공", "에어로케이"], tag: "Airline"},
        {q: "비행기 이륙 직전, 승무원이 문을 잠그고 비상 슬라이드를 작동 가능 상태로 만드는 절차는?", a: ["디암(Disarm) 및 크로스체크", "체크인", "보딩", "택싱"], tag: "Safety"},
        {q: "세계에서 가장 많이 팔린 베스트셀러 단거리 여객기 시리즈는?", a: ["B737", "B747", "A380", "콘코드"], tag: "History"},
        {q: "비행기 꼬리날개에 그려진 항공사 고유의 마크나 무늬를 무엇이라 하나요?", a: ["로고(Logo) 또는 리버리", "스탬프", "사인", "태그"], tag: "Airline"},
        {q: "비행기 안에서 승객들에게 식사와 음료를 제공하고 안전을 책임지는 분들은?", a: ["객실 승무원", "관제사", "정비사", "마샬러"], tag: "Common"},
        {q: "비행기 이륙 전, 활주로까지 이동하는 과정을 무엇이라 하나요?", a: ["택싱(Taxiing)", "보딩", "푸시백", "다이빙"], tag: "Operation"},
        {q: "비행기 날개 아래에 달려 있으며, 공기를 빨아들여 추진력을 만드는 장치는?", a: ["엔진", "바퀴", "안테나", "꼬리날개"], tag: "Engineering"},
        {q: "비행기를 타기 위해 공항 카운터에서 가방을 맡기는 것을 무엇이라 하나요?", a: ["수하물 위탁", "티켓팅", "출국 심사", "보안 검색"], tag: "Airport"},
        {q: "비행기 창문은 왜 동그란 모양일까요?", a: ["모서리에 집중되는 압력을 분산시켜 깨지는 것을 막기 위해", "밖을 더 넓게 보기 위해", "디자인이 예뻐서", "창문을 만들기 더 쉬워서"], tag: "Physics"},
        {q: "비행기를 운전하는 사람을 무엇이라 부를까요?", a: ["조종사(파일럿)", "선장", "기관사", "운전기사"], tag: "Common"},
        {q: "비행기가 하늘로 날아오르기 위해 빠르게 달리는 길은?", a: ["활주로", "고속도로", "철길", "산길"], tag: "Airport"},
        {q: "비행기에서 밥을 먹을 때 사용하는 의자 앞 작은 상자의 이름은?", a: ["트레이 테이블", "책상", "도시락통", "서랍"], tag: "Interior"},
        {q: "비행기를 타기 위해 꼭 가지고 있어야 하는 종이는?", a: ["탑승권(티켓)", "신문", "도서상품권", "전단지"], tag: "Airport"},
        {q: "세계에서 가장 빠른 여객기였던 '콩코드'의 최대 속도는 마하 얼마였을까요?", a: ["마하 2.0 (시속 약 2,100km)", "마하 1.0", "마하 3.5", "마하 5.0"], tag: "History"},
        {q: "보잉 747-8 여객기에 들어가는 총 부품의 개수는 대략 몇 개일까요?", a: ["약 600만 개", "약 10만 개", "약 50만 개", "약 1,000만 개"], tag: "Engineering"},
        {q: "비행기 활주로 중간을 일반 자동차 도로가 가로지르고 있어 기차가 지나가듯 차단기가 내려가는 공항은?", a: ["지브롤터 공항", "런던 히스로 공항", "나리타 공항", "카이로 공항"], tag: "Airport"},
        {q: "비행기가 지구 한 바퀴를 쉬지 않고 돌 수 없어서 중간에 기름을 채우는 기술은?", a: ["공중 급유", "터보 차징", "하이재킹", "퀵 턴"], tag: "Military"},
        {q: "과거 초음속 여객기 '콩코드'는 뉴욕에서 런던까지를 몇 시간 만에 주파했을까요?", a: ["약 3시간 내외", "약 7시간", "약 10시간", "약 1시간"], tag: "History"},
        {q: "대한민국 대통령이 해외 순방 시 사용하는 전용기(코드원)의 기종은?", a: ["보잉 747-8i", "에어버스 A380", "보잉 777", "보잉 737"], tag: "Aircraft"},
        {q: "B777-300ER 기종의 메인 랜딩기어 타이어는 총 몇 개일까요?" , a: ["12개", "8개", "10개", "14개"], tag: "Aircraft"},
        {q: "장거리 비행 중 기내식 사이사이에 제공되는 가벼운 음식은?", a: ["간식(Snack)", "애피타이저", "디저트", "메인 코스"], tag: "Interior"},
        {q: "비행기 이착륙 시 창문의 덮개(쉐이드)를 반드시 열어야 하는 이유는?", a: ["비상 상황 시 외부 상태를 확인하기 위해", "기내 조명을 아끼기 위해", "창문이 깨지는지 감시하기 위해", "승객들의 시력을 보호하기 위해"], tag: "Safety"},

        // --- 추가 10문제 (Easy) ---
        {q: "비행기 표에 적힌 'Gate'는 무엇을 의미하나요?", a: ["비행기를 타는 문", "공항 정문", "가방 맡기는 곳", "식당 입구"], tag: "Airport"},
        {q: "비행기 이착륙 시 등받이를 어떻게 해야 하나요?", a: ["똑바로 세워야 한다", "뒤로 최대한 눕힌다", "상관없다", "앞으로 숙인다"], tag: "Safety"},
        {q: "여객기 날개 아래 매달려 있는 둥근 원통 모양은?", a: ["엔진", "바퀴", "연료탱크", "안테나"], tag: "Engineering"},
        {q: "비행기 기내식을 준비해주는 승무원을 부르는 명칭은?", a: ["객실 승무원(스튜어디스/스튜어드)", "정비사", "관제사", "기상 예보관"], tag: "Common"},
        {q: "공항에서 여권 검사를 하는 곳은?", a: ["출입국 심사대", "면세점", "수하물 수취대", "체크인 카운터"], tag: "Airport"},
        {q: "비행기 안에서 영화를 보거나 음악을 듣는 시스템은?", a: ["엔터테인먼트(IFE)", "블랙박스", "자동 조종 장치", "레이더"], tag: "Interior"},
        {q: "비행기가 안전하게 날기 위해 날개 위의 얼음을 제거하는 작업은?", a: ["디아이싱(De-icing)", "워싱", "페인팅", "코팅"], tag: "Operation"},
        {q: "조종사가 되기 위해 통과해야 하는 신체검사 증명서는?", a: ["화이트 카드", "그린 카드", "레드 티켓", "골드 패스"], tag: "Operation"},
        {q: "비행기가 구름 위로 높이 올라가서 일정하게 비행하는 단계는?", a: ["순항(Cruise)", "이륙", "강하", "착륙"], tag: "Operation"},
        {q: "기내 면세품을 살 수 있는 곳은?", a: ["비행기 안", "활주로", "조종실", "화물칸"], tag: "Airport"}
    ],
    medium: [
        // --- 기존 문제 ---
        {q: "기수를 위아래로 조절하는 조종면은?", a: ["엘리베이터", "러더", "에일러론", "슬랫"], tag: "Physics"},
        {q: "비행기의 고도를 측정할 때 기준이 되는 것은?", a: ["기압", "엔진 출력", "비행기 무게", "날개 길이"], tag: "Physics"},
        {q: "나침반이 가리키는 북쪽(자북)과 실제 지도의 북쪽(진북)의 차이를 무엇이라 하나요?", a: ["편각", "오차", "자차", "각도"], tag: "Navigation"},
        {q: "구름 속에서 앞이 보이지 않을 때 사용하는 계기 착륙 보조 장치는?", a: ["ILS", "GPS", "VHF", "DME"], tag: "Navigation"},
        {q: "헬리콥터가 제자리에서 공중에 떠 있는 비행 상태는?", a: ["호버링", "택싱", "순항", "다이빙"], tag: "Operation"},
        {q: "전투기가 음속보다 빠르게 날 때 기체 뒤로 생기는 수증기 고리 현상은?", a: ["베이퍼 코운", "소닉 붐", "오로라", "헤일로"], tag: "Physics"},
        {q: "항공기의 속도가 너무 느려져 양력을 잃고 떨어지는 현상은?", a: ["실속", "가속", "오버런", "뱅크"], tag: "Physics"},
        {q: "공항 활주로 번호(예: 32L)는 무엇을 기준으로 정해지나요?", a: ["나침반 방위", "공항 건설 순서", "활주로 길이", "관제탑 높이"], tag: "Airport"},
        {q: "베르누이 원리에 따르면, 공기의 흐름이 빨라지면 압력은 어떻게 되나요?", a: ["낮아진다", "높아진다", "변하지 않는다", "사라진다"], tag: "Physics"},
        {q: "날개가 동체 아래쪽에 붙어 있는 비행기 형태를 무엇이라 하나요?", a: ["저익기", "고익기", "중익기", "복엽기"], tag: "Aircraft"},
        {q: "헬리콥터 꼬리 부분에 달린 작은 회전날개의 역할은?", a: ["회전 반동 억제(안정)", "상승력 발생", "추진력 발생", "연료 절약"], tag: "Physics"},
        {q: "공항 활주로 끝에 써진 숫자 '09'는 방위각 몇 도를 의미하나요?", a: ["90도(동쪽)", "9도", "90도(서쪽)", "270도"], tag: "Airport"},
        {q: "비행기 조종석에서 속도, 고도, 자세 등을 보여주는 화면 시스템을 통칭하는 말은?", a: ["글래스 콕핏", "아날로그 패널", "터치 보드", "HUD"], tag: "Technology"},
        {q: "전투기 날개 아래에 다는 보조 연료 탱크의 명칭은?", a: ["드롭 탱크(Drop Tank)", "카고 팟", "미사일 팩", "나셀"], tag: "Military"},
        {q: "관제 용어 중 'Line up and wait'은 무슨 뜻인가요?", a: ["활주로에 진입해서 대기하라", "이륙해도 좋다", "유도로에서 멈춰라", "착륙을 허가한다"], tag: "ATC"},
        {q: "비행기 날개에 장착된 '볼텍스 제너레이터(Vortex Generator)'의 목적은?", a: ["실속을 방지", "소음 공해 감소", "양력 증폭", "기체 경량화"], tag: "Engineering"},
        {q: "항공기 타이어 내부에 공기 대신 질소를 충전하는 핵심 이유는?", a: ["부피 변화가 적고 화재 위험을 방지", "이륙 중량을 감소", "타이어 수명을 2배 이상 연장", "착륙 시 제동 거리 단축"], tag: "Engineering"},
        {q: "비행기 후방 꼬리날개 끝부분에 있는 작은 구멍(APU 배기구)의 역할은?", a: ["전력과 에어컨을 공급하는 보조 동력 장치의 배출구", "연료를 공중에서 투하하는 노즐", "공기 흡입구", "공기를 빼내는 통로"], tag: "Engineering"},
        {q: "비행 중 난기류(Turbulence)를 만났을 때 가장 먼저 취해야 할 조치는?", a: ["좌석 벨트 착용", "구명조끼 지참", "산소 마스크 착용", "비상구로 이동"], tag: "Safety"},
        {q: "착륙 직후 엔진 공기 흐름을 역방향으로 바꿔 제동을 돕는 장치는?", a: ["역추진 장치(Reverse Thrust)", "스포일러", "안티 스키드", "에어 브레이크"], tag: "Engineering"},
        {q: "비행기의 '블랙박스'는 추락 시 발견을 용이하게 하기 위해 보통 어디에 설치하나?", a: ["기체 후방(꼬리 부분)", "조종석 바로 아래", "주날개 연결 부위", "랜딩기어 내부"], tag: "Safety"},
        {q: "엔진 1개가 정지해도 안전하게 회항할 수 있는 시간을 정한 규정은?", a: ["ETOPS", "ICAO", "FAR", "TCAS"], tag: "Emergency"},
        {q: "표준 대기압(29.92 inHg)을 기준으로 설정한 비행 고도의 명칭은?", a: ["기압 고도(Pressure Altitude)", "진고도", "절대고도", "밀도고도"], tag: "Navigation"},
        {q: "B747-8과 B787 엔진 덮개 뒷부분이 톱니바퀴 모양(Chevron)인 이유는?", a: ["엔진 배기 소음 저감", "공기 저항 증가 억제", "역추진 성능 향상", "엔진 냉각 효율 증대"], tag: "Engineering"},
        {q: "B737 기종의 메인 랜딩기어에는 왜 별도의 덮개(Door)가 없는가?", a: ["동체 하부 공간 절약 및 무게 감소", "타이어 냉각 효율 극대화", "비상시 수동 강하 용이성", "이착륙 시 공기 저항 활용"], tag: "Engineering"},
        {q: "A350의 윙렛이 다른 기종과 달리 뒤로 크게 휘어진 형태인 명칭은?", a: ["샤클렛(Sharklet)", "레이키드 윙팁", "펜스 윙렛", "스플릿 스키미타"], tag: "Aircraft"},
        {q: "A380 기종의 엔진 4개 중 역추진 장치(Reverse Thrust)가 장착된 엔진은?", a: ["안쪽 2개 엔진(2번, 3번)", "바깥쪽 2개 엔진(1번, 4번)", "4개 엔진 모두 장착", "장착되지 않음"], tag: "Aircraft"},
        {q: "전투기 F-16의 조종석 캐노피가 프레임 없는 통유리(Bubble) 형태인 이유는?", a: ["조종사에게 360도 전방위 시야 제공", "기체 경량화 및 항력 감소", "탈출 시 캐노피 파쇄 용이성", "레이더 반사 면적 최소화"], tag: "Military"},
        {q: "조종석 창문 주변이 검게 칠해져 '너구리'처럼 보이는 최신형 에어버스 기종은?", a: ["A350", "A320", "A300", "A310"], tag: "Aircraft"},
        {q: "날개 끝이 칼처럼 위로 뾰족하게 꺾여 있는 '윙렛'을 가진 보잉의 주력 단거리 기종은?", a: ["B737", "B777", "B747", "B707"], tag: "Aircraft"},
        {q: "날개 끝이 뒤로 부드럽게 휘어져 올라간 '레이키드 윙팁'이 특징인 기종은?", a: ["B787 드림라이너", "B737", "A320", "MD-11"], tag: "Aircraft"},
        {q: "기체 앞부분에 작은 날개(귀날개)가 달려 있는 '라팔'이나 '유로파이터' 같은 설계 방식은?", a: ["카나드(Canard)", "델타익", "가변익", "후퇴익"], tag: "Military"},
        {q: "비행 중 엔진 화재가 발생했을 때, 조종사가 엔진으로 가는 연료와 유압을 즉시 차단하기 위해 당기는 레버는?", a: ["파이어 핸들(Fire Handle)", "스로틀 레버", "플랩 레버", "랜딩기어 레버"], tag: "Emergency"},
        {q: "주로 단거리 노선에 사용되며, 엔진이 2개인 항공기를 통칭하는 용어는?", a: ["쌍발기(Twin-engine)", "단발기", "3발기", "4발기"], tag: "Aircraft"},
        {q: "비행기 날개 밑에 달린 작은 카누 모양의 구조물(Flap Track Fairing)의 역할은?", a: ["플랩 구동 장치를 보호하고 공기 저항을 감소시킴", "비상 시 연료를 배출하는 통로", "보조 연료 탱크의 고정 장치", "레이더 신호를 흡수하는 스텔스 장치"], tag: "Engineering"},
        {q: "비행기 꼬리날개 쪽에 있는 아주 작은 엔진(보조동력장치)의 이름은?", a: ["APU", "CPU", "GPU", "MPU"], tag: "Engineering"},
        {q: "비행기가 착륙한 후 게이트까지 정확한 위치에 멈추도록 유도하는 주황색 옷을 입은 사람은?", a: ["마샬러(Marshaller)", "로드 마스터", "관제사", "체크인 에이전트"], tag: "Airport"},
        {q: "비행 중 갑자기 기체가 흔들리는 현상을 부르는 말은?", a: ["난기류", "소닉 붐", "버드 스트라이크", "테일 윈드"], tag: "Weather"},
        {q: "비행기 뒤쪽에 생기는 하얀 줄(비행운)은 주로 무엇으로 이루어져 있나요?", a: ["수증기", "엔진 매연", "특수 가스", "구름 조각"], tag: "Weather"},
        {q: "비행기가 활주로에서 이륙하기 위해 달리는 속도는 보통 시속 몇 km 정도일까요?", a: ["약 250~300km/h", "약 100km/h", "약 500km/h", "약 800km/h"], tag: "Physics"},
        {q: "비행기 날개 끝에서 공기가 소용돌이치며 발생하는 저항을 무엇이라 하나요?", a: ["와류(Vortex)", "기압", "추력", "중력"], tag: "Physics"},
        {q: "독일의 '루프트한자(Lufthansa)'에서 '루프트(Luft)'는 공기를 뜻합니다. 그렇다면 '한자(Hansa)'는 무엇을 뜻할까요?", a: ["중세 상업 도시들의 연합(동맹)", "독수리의 날개", "독일의 자부심", "안전한 여행"], tag: "Airline"},
        {q: "미국의 '델타 항공(Delta Air Lines)' 이름은 어느 지역의 지형에서 유래했을까요?", a: ["미시시피강 삼각주(Delta)", "그랜드 캐니언 계곡", "플로리다반도", "로키산맥"], tag: "Airline"},
        {q: "핀란드의 '핀에어(Finnair)'의 원래 이름이자 '태양의 땅'이라는 뜻을 가진 옛 이름은?", a: ["아에로(Aero)", "스칸딕", "노르딕", "수오미"], tag: "Airline"},
        {q: "해발 2,845m에 위치하며 활주로가 짧고 경사져 있어 '세계에서 가장 위험한 공항'으로 불리는 루클라 공항의 정식 명칭은?", a: ["텐징 힐러리 공항", "에베레스트 공항", "네팔 국제공항", "히말라야 게이트"], tag: "Airport"},
        {q: "승무원의 스카프가 꼿꼿하게 서 있는 '비상하는 느낌'의 유니폼으로 유명한 우리나라 항공사는?", a: ["대한항공", "아시아나항공", "제주항공", "티웨이항공"], tag: "Airline"},
        {q: "강렬한 빨간색 유니폼과 금색 자수로 유명하며, 중동을 대표하는 대형 항공사는?", a: ["에미레이트 항공", "카타르 항공", "에티하드 항공", "사우디 항공"], tag: "Airline"},
        {q: "현존하는 세계 최강의 전투기로 불리며, 레이더에 거의 잡히지 않는 'F-22'의 별명은?", a: ["랩터(Raptor)", "라이트닝", "이글", "팰컨"], tag: "Military"},
        {q: "대한민국 공군의 주력 전투기 중 하나로, '슬램 이글'이라는 별명을 가진 기종은?", a: ["F-15K", "KF-16", "FA-50", "F-4 팬텀"], tag: "Military"},
        {q: "수직 이착륙이 가능하여 항공모함이나 함정에서 자유롭게 뜨고 내리는 최신예 스텔스기는?", a: ["F-35B", "F-22", "A-10", "F-16V"], tag: "Military"},
        {q: "세계에서 가장 긴 직항 노선(싱가포르~뉴욕)의 비행시간은 대략 얼마나 될까요?", a: ["약 18~19시간", "약 12시간", "약 24시간", "약 15시간"], tag: "History"},
        {q: "세계에서 가장 짧은 정기 여객 노선(스코틀랜드 웨스트레이~파파 웨스트레이)의 공식 비행시간은?", a: ["약 1분 30초", "약 10분", "약 30분", "약 5분"], tag: "History"},
        {q: "2차 세계대전 당시 활약했으며, 프로펠러가 기체 앞에 달린 유명한 영국 전투기는?", a: ["스핏파이어(Spitfire)", "제로센", "무스탕", "메서슈미트"], tag: "History"},
        {q: "비행기가 순항 고도에 도달했을 때 기내 기압은 보통 어느 정도 높이로 유지될까요?", a: ["해발 약 1,500~2,400m(한라산~백두산 수준)", "해발 0m(평지 수준)", "해발 8,000m(에베레스트 수준)", "외부 기압과 똑같이 유지됨"], tag: "Physics"},
        {q: "비행기 타이어에 새겨진 홈(Groove)이 자동차 타이어와 달리 세로 방향으로만 되어 있는 이유는?", a: ["수막현상을 방지하고 직진 안정성을 높이기 위해", "코너링 성능을 극대화하기 위해", "타이어 무게를 줄이기 위해", "디자인적 아름다움을 위해"], tag: "Engineering"},
        {q: "비행기가 착륙한 후 엔진 출력을 반대로 내뿜어 속도를 줄이는 장치는?", a: ["역추진 장치(Thrust Reverser)", "에어 브레이크", "플랩", "랜딩 기어"], tag: "Engineering"},
        {q: "비행기 조종석 유리창은 왜 2중, 3중 구조로 되어 있을까요?", a: ["높은 고도의 기압 차이를 견디고 새와 충돌(버드 스트라이크) 시 버티기 위해", "방음 효과를 높여 엔진 소음을 차단하기 위해", "햇빛의 자외선을 100% 차단하기 위해", "유리가 깨졌을 때 안쪽에서 갈아 끼우기 위해"], tag: "Physics"},
        {q: "비행기 무게 중심(CG)이 너무 뒤쪽(Rear)에 위치했을 때 발생하는 현상은?", a: ["기수 들림 현상이 강해져 실속 회복이 어려워짐", "기수가 무거워져 이륙 거리가 길어짐", "순항 속도가 급격히 느려짐", "연료 효율이 좋아지나 조종 안정성이 지나치게 높아짐"], tag: "Physics"},
        {q: "실속(Stall)이 발생했을 때 조종사가 가장 먼저 수행해야 하는 조작은?", a: ["기수를 낮추어 받음각을 줄인다", "엔진 출력을 즉시 최대로 높인다", "플랩을 신속하게 모두 접는다", "기수를 들어 올려 양력을 확보한다"], tag: "Physics"},
        {q: "항공기가 착륙할 때 지면 근처에서 양력이 증가하고 항력이 감소하여 붕 떠오르는 듯한 현상은?", a: ["지면 효과(Ground Effect)", "벤추리 효과", "코안다 효과", "도플러 효과"], tag: "Physics"},

        // --- 추가 10문제 (Medium) ---
        {q: "비행기의 방향타(Rudder)를 조종하기 위해 조종사가 발로 밟는 것은?", a: ["러더 페달", "브레이크", "가속 페달", "클러치"], tag: "Operation"},
        {q: "공항 조명 중 활주로의 시작 지점을 알려주는 녹색 등화는?", a: ["진입단 등(Threshold Lights)", "중심선 등", "말단 등", "유도로 등"], tag: "Airport"},
        {q: "비행기가 선회할 때 기체가 옆으로 미끄러지는 현상을 방지하는 계기는?", a: ["선회 경사계", "고도계", "속도계", "수직 속도계"], tag: "Navigation"},
        {q: "비행기의 날개 상면과 하면의 압력 차이로 발생하는 뜨게 하는 힘은?", a: ["양력", "중력", "항력", "추력"], tag: "Physics"},
        {q: "항공 교통 관제사에게 자신의 비행기 정보를 전송하는 장치는?", a: ["트랜스폰더(Transponder)", "블랙박스", "오토파일럿", "마이크"], tag: "ATC"},
        {q: "전투기에서 조종사가 비상시 의자째로 탈출하는 장치는?", a: ["사출 좌석(Ejection Seat)", "슬라이드", "구명보트", "낙하산 배낭"], tag: "Military"},
        {q: "기체 구조 중 비행기의 몸체 부분을 부르는 용어는?", a: ["동체(Fuselage)", "날개", "꼬리날개", "랜딩기어"], tag: "Engineering"},
        {q: "비행 중 연료를 버려야 할 때 사용하는 시스템은?", a: ["연료 덤핑(Fuel Dumping)", "리필", "체크", "드레인"], tag: "Emergency"},
        {q: "날개 뒷부분에 달려 있으며, 이착륙 시 양력을 높여주는 장치는?", a: ["플랩(Flap)", "에일러론", "스포일러", "윙렛"], tag: "Engineering"},
        {q: "비행기가 음속(마하 1)에 도달했을 때의 속도는 약 얼마인가요?", a: ["시속 1,225km", "시속 800km", "시속 2,000km", "시속 500km"], tag: "Physics"}
    ],
    hard: [
        // --- 기존 문제 ---
        {q: "음속 돌파 시 발생하는 충격파와 소음은?", a: ["소닉 붐", "제트 노이즈", "에어 뱅", "마하 킥"], tag: "Physics"},
        {q: "관제사가 특정 비행기를 식별하기 위해 항공기에 부여하는 4자리 고유 번호는?", a: ["스쿼크 코드", "플라이트 넘버", "게이트 번호", "기체 번호"], tag: "ATC"},
        {q: "비행기가 지정된 경로를 벗어나지 않도록 지상에서 쏘아 올리는 무선 표지 시설은?", a: ["VOR", "NDB", "ILS", "PAPI"], tag: "Navigation"},
        {q: "활주로 옆에서 불빛으로 착륙 각도가 적절한지 알려주는 장치는?", a: ["PAPI", "REIL", "VASI", "ALS"], tag: "Airport"},
        {q: "초음속 비행기 콩코드가 퇴역하게 된 결정적인 사고가 발생한 장소는?", a: ["프랑스 파리", "미국 뉴욕", "영국 런던", "독일 베를린"], tag: "History"},
        {q: "비행기의 3축 운동 중 '요(Yaw)'를 제어하는 조종면은?", a: ["러더(Rudder)", "에일러론", "엘리베이터", "슬랫"], tag: "Physics"},
        {q: "항공기 날개 구조물 중 앞뒤로 길게 뻗어 뼈대 역할을 하는 부품은?", a: ["스파", "리브", "스트링거", "스킨"], tag: "Engineering"},
        {q: "비행기가 음속에 가까워질 때 발생하는 항력이 급증하는 구간은?", a: ["천음속 영역", "아음속 영역", "초음속 영역", "극초음속 영역"], tag: "Physics"},
        {q: "비행기가 수평 선회 비행을 할 때 조종사가 느끼는 가속도 힘을 무엇이라 하나요?", a: ["G-로드", "추력", "항력", "양력"], tag: "Physics"},
        {q: "고정익 항공기가 이륙하기 위해 필요한 최소 속도를 무엇이라 하나요?", a: ["Vsr", "Vne", "Vmo", "Vfe"], tag: "Physics"},
        {q: "대기 속도(IAS)와 실제 지면에 대한 속도(GS)의 차이를 만드는 가장 큰 요인은?", a: ["바람(풍향 및 풍속)", "엔진 온도", "기체 무게", "날개 면적"], tag: "Navigation"},
        {q: "날개의 받음각이 변해도 압력 중심이 거의 변하지 않는 에어포일은?", a: ["대칭형 에어포일", "캠버형 에어포일", "초임계 에어포일", "층류 에어포일"], tag: "Physics"},
        {q: "음속 근처에서 조파 항력을 최소화하기 위해 설계된 날개 단면은?", a: ["초임계 에어포일(Supercritical)", "델타익", "가변익", "테이퍼익"], tag: "Physics"},
        {q: "항공기의 관성항법장치(INS)에서 위치를 계산하기 위해 가속도를 측정하는 부품은?", a: ["가속도계(Accelerometer)", "자이로스코프", "자력계", "기압 센서"], tag: "Navigation"},
        {q: "비행기 표면에 얼음이 생기는 것을 막기 위해 가열하는 시스템은?", a: ["안티 아이싱(Anti-icing)", "디 아이싱", "부트 시스템", "글리콜 분사"], tag: "Engineering"},
        {q: "피토관(Pitot tube)이 막혔을 때 고도계와 함께 오작동하는 계기는?", a: ["속도계(ASI)", "수직속도계", "자이로 수평의", "선회계"], tag: "Emergency"},
        {q: "착륙 중 지면 효과(Ground Effect)가 발생할 때 나타나는 현상은?", a: ["유도 항력 감소 및 양력 증가", "추력 감소 및 항력 증가", "기수 내려감 현상", "실속 속도 상승"], tag: "Physics"},
        {q: "항공기가 실속 직전 조종간을 진동시켜 경고를 주는 장치는?", a: ["스틱 쉐이커(Stick Shaker)", "스틱 푸셔", "오토 스로틀", "GPWS"], tag: "Safety"},
        {q: "B787 드림라이너의 엔진 나셀 뒷부분이 물결 모양(Chevrons)으로 되어 있는 공학적 이유는?", a: ["찬 공기와 뜨거운 배기가스의 혼합을 도와 소음을 줄이기 위해", "엔진의 추력을 높여 이륙 거리를 단축하기 위해", "역추진 장치 작동 시 공기 저항을 극대화하기 위해", "엔진 내부로 새가 빨려 들어가는 것을 막기 위해"], tag: "Engineering"},
        {q: "쌍발 엔진 항공기가 엔진 하나가 고장 난 상태로 안전하게 비행할 수 있는 시간을 증명해야 하는 국제 인증 규정은?", a: ["ETOPS (Extended-range Twin-engine Ops)", "ICAO 표준", "FAA 파트 145", "EASA 에어워디니스"], tag: "Emergency"},
        {q: "MACS가 활성화되어 기수를 강제로 내릴 때, 실제로 움직이는 기체 부위는?", a: ["수평 안정판 (Horizontal Stabilizer)", "주날개 뒤쪽의 플랩", "수직 꼬리날개의 러더", "날개 상면의 스포일러"], tag: "MCAS"},
        {q: "사고 당시 MACS가 오작동했던 가장 결정적인 로직 설계의 허점은?", a: ["두 개의 받음각(AoA) 센서 중 단 하나의 센서값만 신뢰하여 시스템이 가동됨", "조종사의 조종간 입력값을 무조건 무시하도록 설정됨", "엔진 출력이 낮을 때만 작동하도록 설계됨", "속도가 빠를수록 기수를 더 강하게 들어 올리도록 설계됨"], tag: "MCAS"},
        {q: "표준 대기압 상태(29.92 inHg)를 기준으로 설정하여 모든 항공기가 동일한 고도계를 사용하는 고도 층을 무엇이라 하나요?", a: ["비행 레벨(Flight Level)", "전이 고도", "진고도", "수정 고도"], tag: "Navigation"},
        {q: "비행기가 활주로 중심선을 따라 내려오도록 좌우 방향 정보를 제공하는 ILS(계기착륙장치)의 구성 요소는?", a: ["로컬라이저(Localizer)", "글라이드 슬로프", "마커 비콘", "DME"], tag: "Navigation"},

        // --- 추가 10문제 (Hard) ---
        {q: "엔진의 압축기 실속(Compressor Stall) 시 조종사가 취해야 할 응급 조치는?", a: ["스로틀을 지체 없이 줄인다", "스로틀을 최대로 올린다", "연료 펌프를 끈다", "플랩을 내린다"], tag: "Emergency"},
        {q: "비행 중 조종사가 의도하지 않게 기체가 회전하며 추락하는 상태는?", a: ["스핀(Spin)", "루프", "롤", "다이빙"], tag: "Physics"},
        {q: "제트 엔진 내부에서 가장 뜨거운 가스에 노출되는 부품은?", a: ["고압 터빈 블레이드", "압축기 팬", "연소기 입구", "배기 노즐"], tag: "Engineering"},
        {q: "기체 구조 중 비행 하중을 가장 많이 받는 주날개의 가로 뼈대는?", a: ["스파(Spar)", "리브", "스트링거", "벌크헤드"], tag: "Engineering"},
        {q: "고고도 비행 시 낮은 기압으로 인해 혈액 속에 기포가 생기는 증상은?", a: ["감압병", "빈혈", "고산병", "과호흡"], tag: "Safety"},
        {q: "전투기의 레이더 반사 면적을 줄이기 위해 사용하는 도료는?", a: ["RAM(Radar Absorbent Material)", "에폭시", "우레탄", "테플론"], tag: "Military"},
        {q: "항공기 엔진의 총 추력 중 바이패스 공기가 만드는 추력의 비율을 뜻하는 용어는?", a: ["바이패스 비(Bypass Ratio)", "압축비", "공연비", "추중비"], tag: "Engineering"},
        {q: "지구의 자전으로 인해 북반구에서 비행기가 오른쪽으로 휘어지는 힘은?", a: ["코리올리 힘(Coriolis Force)", "원심력", "구심력", "중력"], tag: "Physics"},
        {q: "비행기 날개 주위의 흐름이 부분적으로 음속을 넘기 시작하는 마하 수는?", a: ["임계 마하 수(Mcr)", "절대 마하 수", "항력 마하 수", "충격 마하 수"], tag: "Physics"},
        {q: "항공기 항법 중 지상의 여러 무선 기지국 신호를 받아 위치를 결정하는 방식은?", a: ["RNAV(지역 항법)", "INS", "GPS", "VFR"], tag: "Navigation"}
    ]
};
</body>
</html>
