<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>오늘의 1분 스트레칭 & 운동 추천</title>
    <style>
        :root {
            --primary-color: #4A90E2;
            --secondary-color: #50E3C2;
            --bg-color: #F4F7F9;
            --card-bg: #FFFFFF;
            --text-color: #333333;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Pretendard', -apple-system, BlinkMacSystemFont, system-ui, Roboto, sans-serif;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-color);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            background-color: var(--card-bg);
            border-radius: 20px;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.05);
            width: 100%;
            max-width: 450px;
            padding: 30px;
            text-align: center;
        }

        h1 {
            font-size: 1.5rem;
            margin-bottom: 8px;
            color: #2C3E50;
        }

        p.subtitle {
            font-size: 0.9rem;
            color: #7F8C8D;
            margin-bottom: 25px;
        }

        .exercise-card {
            background: linear-gradient(135deg, #e0c3fc 0%, #8ec5fc 100%);
            border-radius: 15px;
            padding: 25px 20px;
            color: #fff;
            margin-bottom: 25px;
            min-height: 160px;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            transition: all 0.3s ease;
        }

        .exercise-category {
            font-size: 0.8rem;
            background: rgba(255, 255, 255, 0.3);
            padding: 4px 12px;
            border-radius: 20px;
            margin-bottom: 10px;
            text-transform: uppercase;
            font-weight: bold;
        }

        .exercise-title {
            font-size: 1.4rem;
            font-weight: bold;
            margin-bottom: 8px;
        }

        .exercise-desc {
            font-size: 0.95rem;
            opacity: 0.9;
            word-break: keep-all;
        }

        .timer-display {
            font-size: 2.5rem;
            font-weight: bold;
            color: #2C3E50;
            margin-bottom: 15px;
        }

        .btn-group {
            display: flex;
            gap: 10px;
            justify-content: center;
        }

        button {
            border: none;
            padding: 12px 20px;
            font-size: 0.95rem;
            font-weight: bold;
            border-radius: 10px;
            cursor: pointer;
            transition: transform 0.1s ease, background-color 0.2s ease;
        }

        button:active {
            transform: scale(0.98);
        }

        .btn-random {
            background-color: var(--primary-color);
            color: white;
            flex: 2;
        }

        .btn-random:hover {
            background-color: #357ABD;
        }

        .btn-timer {
            background-color: #E74C3C;
            color: white;
            flex: 1;
        }

        .btn-timer:hover {
            background-color: #C0392B;
        }

        .btn-timer:disabled {
            background-color: #BDC3C7;
            cursor: not-allowed;
        }
    </style>
</head>
<body>

    <div class="container">
        <h1>오늘의 1분 리프레시</h1>
        <p class="subtitle">버튼을 눌러 찌뿌둥한 몸을 풀어보세요!</p>

        <div class="exercise-card" id="card">
            <span class="exercise-category" id="category">준비</span>
            <div class="exercise-title" id="title">어떤 운동을 할까요?</div>
            <div class="exercise-desc" id="desc">아래 '다른 운동 추천' 버튼을 눌러주세요.</div>
        </div>

        <div class="timer-display" id="timer">00:45</div>

        <div class="btn-group">
            <button class="btn-random" onclick="getRandomExercise()">🎲 다른 운동 추천</button>
            <button class="btn-timer" id="startBtn" onclick="startTimer()">▶ 타이머 시작</button>
        </div>
    </div>

    <script>
        // 운동 데이터 목록
        const exercises = [
            {
                category: "목/어깨",
                title: "거북목 방지 승모근 스트레칭",
                desc: "한쪽 손으로 반대쪽 머리를 잡고 옆으로 천천히 늘려줍니다.",
                time: 45
            },
            {
                category: "하체/골반",
                title: "앉아서 하는 고관절 풀기",
                desc: "의자에 앉아 한쪽 다리를 반대쪽 무릎 위에 올리고 상체를 앞으로 숙입니다.",
                time: 60
            },
            {
                category: "허리/등",
                title: "의자 캣카우(Cat-Cow) 자세",
                desc: "손을 무릎에 얹고 등과 허리를 말았다가 펴는 동작을 반복합니다.",
                time: 45
            },
            {
                category: "전신 유산소",
                title: "제자리 걸음 & 고관절 올리기",
                desc: "제자리에서 무릎을 허수아비처럼 가슴 높이까지 높게 들어올리며 걷습니다.",
                time: 60
            },
            {
                category: "눈/두통",
                title: "관자놀이 마사지 & 눈 감기",
                desc: "손가락 끝으로 관자놀이를 원을 그리며 마사지하고 눈을 지그시 감습니다.",
                time: 30
            },
            {
                category: "손목/팔",
                title: "컴퓨터용 손목 굴근 스트레칭",
                desc: "한쪽 팔을 앞으로 뻗고, 반대 손으로 손가락을 몸쪽으로 당겨줍니다.",
                time: 45
            }
        ];

        let currentExercise = null;
        let timerInterval = null;
        let timeLeft = 0;

        // 랜덤 운동 선택 함수
        function getRandomExercise() {
            clearInterval(timerInterval);
            document.getElementById('startBtn').disabled = false;
            
            const randomIndex = Math.floor(Math.random() * exercises.length);
            currentExercise = exercises[randomIndex];

            document.getElementById('category').innerText = currentExercise.category;
            document.getElementById('title').innerText = currentExercise.title;
            document.getElementById('desc').innerText = currentExercise.desc;
            
            timeLeft = currentExercise.time;
            updateTimerDisplay(timeLeft);
        }

        // 타이머 표시 업데이트
        function updateTimerDisplay(seconds) {
            const mins = Math.floor(seconds / 60);
            const secs = seconds % 60;
            document.getElementById('timer').innerText = 
                `${String(mins).padStart(2, '0')}:${String(secs).padStart(2, '0')}`;
        }

        // 타이머 시작 함수
        function startTimer() {
            if (!currentExercise) {
                alert("먼저 운동을 추천받아주세요!");
                return;
            }

            const startBtn = document.getElementById('startBtn');
            startBtn.disabled = true;

            timerInterval = setInterval(() => {
                timeLeft--;
                updateTimerDisplay(timeLeft);

                if (timeLeft <= 0) {
                    clearInterval(timerInterval);
                    alert("👏 수고하셨습니다! 몸이 한결 가벼워졌기를 바라요.");
                    startBtn.disabled = false;
                    timeLeft = currentExercise.time;
                    updateTimerDisplay(timeLeft);
                }
            }, 1000);
        }

        // 페이지 로드시 첫 랜덤 운동 불러오기
        window.onload = getRandomExercise;
    </script>
</body>
</html>
