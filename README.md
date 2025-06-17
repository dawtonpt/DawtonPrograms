<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Math Master: The Brain Arena</title>
  <em><subtitle>Por: Dawton Pessanha </subtitle></em>
  <style>
    body {
      background: #111;
      color: #eee;
      font-family: 'Courier New', monospace;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      margin: 0;
      overflow: hidden;
    }
    h1 { margin-bottom: 1rem; }
    #game-box {
      background: #222;
      padding: 2rem;
      border-radius: 10px;
      box-shadow: 0 0 20px #00ffcc44;
      text-align: center;
      width: 300px;
      z-index: 2;
    }
    #question { font-size: 1.5rem; margin-bottom: 1rem; }
    #answer, #name-input {
      width: 100%;
      padding: 0.5rem;
      font-size: 1rem;
      border-radius: 5px;
      border: none;
      margin-bottom: 1rem;
    }
    #submit, #restart {
      background: #00ffcc;
      color: #000;
      font-weight: bold;
      padding: 0.5rem 1rem;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      margin: 0.25rem;
    }
    #submit:hover, #restart:hover { background: #00c9a7; }
    #status { margin-top: 1rem; }
    .red { color: #ff5252; }
    .green { color: #00ffcc; }
    #timer { font-size: 1.2rem; margin: 0.5rem 0; }
    #leaderboard { margin-top: 2rem; text-align: center; }
    #leaderboard h2 { margin-bottom: 0.5rem; }
    #leaderboard ul { list-style: none; padding: 0; }
    #leaderboard li {
      background: #333;
      padding: 0.3rem 0.5rem;
      margin-bottom: 0.15rem;
      border-radius: 5px;
      font-size: 1rem;
    }
    /* Classic Podium styles */
    .podium-classic {
      display: flex;
      justify-content: center;
      align-items: flex-end;
      height: 160px;
      margin-bottom: 1.2rem;
      gap: 18px;
    }
    .podium-spot {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: flex-end;
      width: 80px;
      margin: 0 6px;
      position: relative;
    }
    .podium-bar {
      width: 100%;
      border-radius: 8px 8px 0 0;
      position: relative;
      transition: height 1s cubic-bezier(.68,-0.55,.27,1.55);
      overflow: hidden;
      display: flex;
      align-items: flex-end;
      justify-content: center;
    }
    .gold { background: linear-gradient(180deg, #ffe066 70%, #bfa900 100%); }
    .silver { background: linear-gradient(180deg, #e0e0e0 70%, #a0a0a0 100%); }
    .bronze { background: linear-gradient(180deg, #e6b980 70%, #a67c52 100%); }
    .podium-rank {
      position: absolute;
      bottom: 8px;
      left: 0;
      width: 100%;
      font-size: 1.2em;
      font-weight: bold;
      color: #222;
      text-shadow: 0 1px 2px #fff8;
      z-index: 2;
      text-align: center;
    }
    .podium-name {
      margin-bottom: 2px;
      font-size: 1em;
      color: #fff;
      font-weight: bold;
      text-shadow: 0 1px 2px #2228;
      z-index: 2;
      word-break: break-word;
      max-width: 90%;
      text-align: center;
    }
    .podium-score {
      font-size: 0.95em;
      color: #222;
      font-weight: bold;
      margin-bottom: 2px;
      text-shadow: 0 1px 2px #fff8;
      z-index: 2;
      text-align: center;
    }
    /* Math images in margins */
    .math-img {
      position: absolute;
      z-index: 1;
      opacity: 0.7;
      pointer-events: none;
    }
    .math-img.topleft { top: 10px; left: 10px; width: 80px; }
    .math-img.topright { top: 10px; right: 10px; width: 80px; }
    .math-img.bottomleft { bottom: 10px; left: 10px; width: 80px; }
    .math-img.bottomright { bottom: 10px; right: 10px; width: 80px; }
    .math-img.leftcenter { left: 10px; top: 50%; transform: translateY(-50%); width: 60px; }
    .math-img.rightcenter { right: 10px; top: 50%; transform: translateY(-50%); width: 60px; }
  </style>
  <!-- Firebase App (the core Firebase SDK) -->
  <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>
  <script>
    const firebaseConfig = {
      apiKey: "AIzaSyDoVZwTuwx6Cgf1V5OsBx20CB26rcdx_Ls",
      authDomain: "mathsgame-2bcb4.firebaseapp.com",
      databaseURL: "https://mathsgame-2bcb4-default-rtdb.firebaseio.com",
      projectId: "mathsgame-2bcb4",
      storageBucket: "mathsgame-2bcb4.firebasestorage.app",
      messagingSenderId: "316993513512",
      appId: "1:316993513512:web:bc9aee590dfa7191ecad1d"
    };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();
  </script>
</head>
<body>

  <h1>🧠 Bora Estudasses</h1>
  <div id="game-box">
    <input type="text" id="name-input" placeholder="Enter your name" />
    <div id="question">Loading...</div>
    <input type="number" id="answer" placeholder="Your answer" />
    <button id="submit">Submit</button>
    <button id="restart">Restart</button>
    <div id="timer">Time left: <span id="time">10</span>s</div>
    <div id="status"></div>
    <div id="scoreboard">Score: 0 | Lives: 3</div>
  </div>
  <div id="leaderboard">
    <h2>🏆 Leaderboard</h2>
    <div class="podium-classic" id="podium-classic">
      <!-- Podium spots will be injected here -->
    </div>
    <ul id="leaderboard-list"></ul>
  </div>
  <script>
    const questionEl = document.getElementById('question');
    const answerEl = document.getElementById('answer');
    const submitBtn = document.getElementById('submit');
    const restartBtn = document.getElementById('restart');
    const statusEl = document.getElementById('status');
    const scoreboardEl = document.getElementById('scoreboard');
    const timeEl = document.getElementById('time');
    const nameInput = document.getElementById('name-input');
    const leaderboardList = document.getElementById('leaderboard-list');
    const podiumClassic = document.getElementById('podium-classic');

    let score = 0;
    let lives = 3;
    let currentAnswer = 0;
    let level = 1;
    let timer;
    let timeLeft = 10;

    function getRandomInt(min, max) {
      return Math.floor(Math.random() * (max - min + 1)) + min;
    }

    function generateQuestion() {
      const operators = ['+', '-', '*', '/'];
      const op = operators[getRandomInt(0, operators.length - 1)];
      let a = getRandomInt(1, level * 10);
      let b = getRandomInt(1, level * 10);
      if (op === '/') a = a * b;
      let question = `${a} ${op} ${b}`;
      currentAnswer = Math.round(eval(question) * 100) / 100;
      questionEl.textContent = `What is ${question}?`;
      answerEl.value = '';
      statusEl.textContent = '';
      timeLeft = 10;
      updateTimer();
    }

    function updateTimer() {
      clearInterval(timer);
      timer = setInterval(() => {
        timeLeft--;
        timeEl.textContent = timeLeft;
        if (timeLeft <= 0) {
          clearInterval(timer);
          lives--;
          checkGameOver("⏰ Time's up!");
        }
      }, 1000);
    }

    function updateScoreboard() {
      scoreboardEl.textContent = `Score: ${score} | Lives: ${lives}`;
    }

    function checkGameOver(msg) {
      updateScoreboard();
      if (lives <= 0) {
        statusEl.textContent = `💀 Game Over! Final Score: ${score}`;
        statusEl.className = 'red';
        clearInterval(timer);
        submitBtn.disabled = true;
        answerEl.disabled = true;
        nameInput.disabled = true;
        saveScore();
      } else {
        statusEl.textContent = msg;
        statusEl.className = 'red';
        setTimeout(generateQuestion, 1000);
      }
    }

    function checkAnswer() {
      const userAnswer = parseFloat(answerEl.value);
      if (isNaN(userAnswer)) return;
      if (Math.abs(userAnswer - currentAnswer) < 0.01) {
        score += 10;
        level += 0.5;
        statusEl.textContent = '✅ Correct!';
        statusEl.className = 'green';
        clearInterval(timer);
        setTimeout(generateQuestion, 1000);
      } else {
        lives--;
        checkGameOver(`❌ Incorrect! Correct answer: ${currentAnswer}`);
      }
      updateScoreboard();
    }

    function saveScore() {
      const name = nameInput.value.trim() || 'Anonymous';
      db.ref('leaderboard').push({ name, score });
      setTimeout(renderLeaderboard, 1000); // Wait a bit for DB update
    }

    function renderLeaderboard() {
      db.ref('leaderboard').orderByChild('score').limitToLast(20).once('value', snapshot => {
        let scores = [];
        snapshot.forEach(child => {
          const entry = child.val();
          if (entry.score && entry.score > 0) {
            scores.push(entry);
          }
        });
        scores.sort((a, b) => b.score - a.score);
        scores = scores.slice(0, 10);

        // Classic podium: 2nd, 1st, 3rd
        podiumClassic.innerHTML = '';
        const podiumHeights = [90, 130, 70]; // px for 2nd, 1st, 3rd
        const podiumClasses = ['silver', 'gold', 'bronze'];
        const podiumRanks = [2, 1, 3];
        for (let i = 0; i < 3; i++) {
          const idx = [1, 0, 2][i]; // 2nd, 1st, 3rd
          const entry = scores[idx];
          const spot = document.createElement('div');
          spot.className = 'podium-spot';
          spot.innerHTML = `
            <div class="podium-name">${entry ? entry.name : ''}</div>
            <div class="podium-score">${entry ? entry.score : ''}</div>
            <div class="podium-bar ${podiumClasses[i]}" style="height:0px"></div>
            <div class="podium-rank">${entry ? podiumRanks[i] : ''}</div>
          `;
          podiumClassic.appendChild(spot);
          // Animate height
          setTimeout(() => {
            const bar = spot.querySelector('.podium-bar');
            bar.style.height = entry ? `${podiumHeights[i]}px` : '0px';
          }, 100);
        }

        // Leaderboard list (top 10, skip 0 scores)
        leaderboardList.innerHTML = '';
        scores.forEach((entry, idx) => {
          const li = document.createElement('li');
          li.textContent = `${idx + 1}. ${entry.name}: ${entry.score}`;
          leaderboardList.appendChild(li);
        });
      });
    }

    function restartGame() {
      score = 0;
      lives = 3;
      level = 1;
      submitBtn.disabled = false;
      answerEl.disabled = false;
      nameInput.disabled = false;
      generateQuestion();
      updateScoreboard();
    }

    submitBtn.addEventListener('click', checkAnswer);
    restartBtn.addEventListener('click', restartGame);
    answerEl.addEventListener('keydown', (e) => {
      if (e.key === 'Enter') checkAnswer();
    });

    renderLeaderboard();
    restartGame();
  </script>
</body>
</html>
