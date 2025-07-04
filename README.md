<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Aviator Game</title>
  <style>
    body {
      background: #111;
      color: white;
      font-family: Arial, sans-serif;
      text-align: center;
      padding-top: 100px;
    }

    .game-container {
      background: #222;
      padding: 40px;
      border-radius: 10px;
      display: inline-block;
      width: 340px;
    }

    #multiplier {
      font-size: 48px;
      margin: 20px 0;
      color: lime;
    }

    input[type="number"] {
      padding: 5px;
      width: 100px;
      text-align: center;
      margin-top: 5px;
    }

    .buttons {
      margin-top: 20px;
    }

    button {
      padding: 10px 20px;
      font-size: 18px;
      cursor: pointer;
      background: crimson;
      color: white;
      border: none;
      border-radius: 5px;
      margin: 5px;
    }

    button:disabled {
      background: #555;
      cursor: not-allowed;
    }

    #result {
      margin-top: 20px;
      font-size: 20px;
    }

    .info {
      margin-bottom: 15px;
    }

    .plane-container {
      position: relative;
      height: 100px;
      margin-top: 20px;
      overflow: hidden;
      background: #333;
      border-radius: 10px;
    }

    #plane {
      position: absolute;
      left: 0;
      top: 30px;
      font-size: 32px;
      transition: transform 0.1s linear;
    }
  </style>
</head>
<body>
  <div class="game-container">
    <h1>✈️ Aviator Game</h1>

    <div class="info">
      <div>💰 Balance: <span id="balance">$1000</span></div>
      <div>💸 Bet: <input type="number" id="bet-input" value="10" min="1"></div>
      <div>🤖 Auto Cash Out at: <strong>x1.00</strong></div>
    </div>

    <div id="multiplier">x1.00</div>

    <div class="plane-container">
      <div id="plane">✈️</div>
    </div>

    <div class="buttons">
      <button id="start-btn">▶️ Start</button>
      <button id="cashout-btn" disabled>💸 Cash Out</button>
    </div>

    <div id="result"></div>
  </div>

  <script>
    const AUTO_CASHOUT = 10000.00;

    let multiplier = 1.00;
    let interval;
    let cashedOut = false;
    let crashed = false;
    let gameStarted = false;
    let balance = 1000;
    let bet = 0;

    const multiplierDisplay = document.getElementById("multiplier");
    const resultDisplay = document.getElementById("result");
    const cashoutBtn = document.getElementById("cashout-btn");
    const startBtn = document.getElementById("start-btn");
    const betInput = document.getElementById("bet-input");
    const balanceDisplay = document.getElementById("balance");
    const plane = document.getElementById("plane");

    function updateBalanceDisplay() {
      balanceDisplay.textContent = `$${balance.toFixed(2)}`;
    }

    function resetGameState() {
      multiplier = 1.00;
      cashedOut = false;
      crashed = false;
      gameStarted = false;
      startBtn.disabled = false;
      cashoutBtn.disabled = true;
      betInput.disabled = false;
      plane.style.transform = `translateX(0px)`;
    }

    function startGame() {
      if (gameStarted) return;

      bet = parseFloat(betInput.value);
      if (isNaN(bet) || bet <= 0 || bet > balance) {
        resultDisplay.textContent = "⚠️ Invalid bet amount!";
        return;
      }

      balance -= bet;
      updateBalanceDisplay();

      resetGameState();

      gameStarted = true;
      startBtn.disabled = true;
      cashoutBtn.disabled = false;
      betInput.disabled = true;
      resultDisplay.textContent = "";
      multiplierDisplay.textContent = `x${multiplier}`;

      interval = setInterval(() => {
        multiplier = +(multiplier + 0.05).toFixed(2);
        multiplierDisplay.textContent = `x${multiplier}`;
        plane.style.transform = `translateX(${(multiplier - 1) * 20}px)`;

        if (!cashedOut && multiplier >= AUTO_CASHOUT) {
          doCashout(true); // Auto cashout
        }

        if (Math.random() < 0.03) {
          crash();
        }
      }, 200);
    }

    function doCashout(isAuto = false) {
      if (!gameStarted || cashedOut || crashed) return;

      cashedOut = true;
      clearInterval(interval);

      const winnings = bet * multiplier;
      balance += winnings;
      updateBalanceDisplay();

      resultDisplay.textContent = isAuto
        ? `🤖 Auto-Cashed out at x${multiplier}! Won $${winnings.toFixed(2)}`
        : `✅ Cashed out at x${multiplier}! Won $${winnings.toFixed(2)}`;

      setTimeout(resetGameState, 1000);
    }

    function crash() {
      if (!crashed) {
        crashed = true;
        clearInterval(interval);
        if (!cashedOut) {
          resultDisplay.textContent = "💥 Plane crashed! You lost!";
        }
        setTimeout(resetGameState, 1000);
      }
    }

    cashoutBtn.addEventListener("click", () => doCashout(false));
    startBtn.addEventListener("click", startGame);
    updateBalanceDisplay();
  </script>
</body>
</html>
