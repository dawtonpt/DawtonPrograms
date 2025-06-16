<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Math Master: The Brain Arena</title>
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
    }

    h1 {
      margin-bottom: 1rem;
    }

    #game-box {
      background: #222;
      padding: 2rem;
      border-radius: 10px;
      box-shadow: 0 0 20px #00ffcc44;
      text-align: center;
      width: 300px;
    }

    #question {
      font-size: 1.5rem;
      margin-bottom: 1rem;
    }

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

    #submit:hover, #restart:hover {
      background: #00c9a7;
    }

    #status {
      margin-top: 1rem;
    }

    .red { color: #ff5252; }
    .green { color: #00ffcc; }

    #timer {
      font-size: 1.2rem;
      margin: 0.5rem 0;
    }

    #leaderboard {
      margin-top: 2rem;
      text-align: center;
    }

    #leaderboard h2 {
      margin-bottom: 0.5rem;
    }

    #leaderboard ul {
      list-style: none;
      padding: 0;
    }

    #leaderboard li {
      background: #333;
      padding: 0.5rem;
      margin-bottom: 0.25rem;
      border-radius: 5px;
    }
  </style>
</head>
<body>
  <h1>🧠 Math Master</h1>
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
      const existing = JSON.parse(localStorage.getItem('mathLeaderboard') || '[]');
      existing.push({ name, score });
      existing.sort((a, b) => b.score - a.score);
      localStorage.setItem('mathLeaderboard', JSON.stringify(existing.slice(0, 10)));
      renderLeaderboard();
    }

    function renderLeaderboard() {
      const scores = JSON.parse(localStorage.getItem('mathLeaderboard') || '[]');
      leaderboardList.innerHTML = '';
      scores.forEach(entry => {
        const li = document.createElement('li');
        li.textContent = `${entry.name}: ${entry.score}`;
        leaderboardList.appendChild(li);
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
<script>
    // Initialize the game on page load
    document.addEventListener('DOMContentLoaded', () => {
      restartGame();
    });
  </script>
