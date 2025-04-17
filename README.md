<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
  <title>惡靈古堡：網頁試煉</title>
  <style>
    body {
      background-color: #2e2e2e;
      color: #e0e0e0;
      font-family: "微軟正黑體", sans-serif;
      padding: 20px;
      background-image: url('https://i.imgur.com/1g6UupB.jpg');
      background-size: cover;
      background-attachment: fixed;
      background-position: center;
      animation: bgAnimation 30s infinite linear;
      overflow: hidden;
    }

    @keyframes bgAnimation {
      0% { background-position: center; }
      50% { background-position: right; }
      100% { background-position: center; }
    }

    h1 {
      text-align: center;
      font-size: 4rem;
      color: #ffcc00;
      text-shadow: 3px 3px 10px rgba(0, 0, 0, 0.7);
      font-family: 'Arial', sans-serif;
    }

    #story {
      background: rgba(0, 0, 0, 0.8);
      padding: 25px;
      border-radius: 20px;
      margin-bottom: 30px;
      box-shadow: 0 0 25px rgba(0, 0, 0, 0.8);
      font-size: 1.3rem;
      line-height: 1.8;
      max-width: 800px;
      margin: 0 auto;
    }

    p {
      font-size: 1.4rem;
      line-height: 1.8;
    }

    button {
      margin: 15px;
      padding: 18px 35px;
      background: linear-gradient(145deg, #3b3b3b, #1a1a1a);
      color: #fff;
      border: none;
      border-radius: 12px;
      font-size: 1.2rem;
      text-shadow: 2px 2px 6px rgba(0, 0, 0, 0.7);
      cursor: pointer;
      transition: all 0.3s ease-in-out;
    }

    button:hover {
      transform: scale(1.1);
      background: linear-gradient(145deg, #555555, #333333);
      box-shadow: 0 0 20px rgba(255, 255, 255, 0.5);
    }

    button:active {
      transform: scale(1);
      box-shadow: none;
    }

    #inventory {
      margin-top: 20px;
      font-size: 1.2rem;
      color: #ffcc00;
    }

    .fadeIn {
      animation: fadeIn 2s ease-in-out;
    }

    @keyframes fadeIn {
      0% { opacity: 0; }
      100% { opacity: 1; }
    }

    #damageEffect {
      position: fixed;
      top: 0; left: 0;
      width: 100%; height: 100%;
      background: rgba(255, 0, 0, 0.4);
      z-index: 1000;
      display: none;
      animation: damageFlash 0.3s ease-in-out;
    }

    @keyframes damageFlash {
      0% { opacity: 1; }
      100% { opacity: 0; }
    }

    #jill {
      position: absolute;
      bottom: 10px;
      left: 0;
      height: 150px;
      transition: left 2s;
      z-index: 10;
    }

    @media screen and (orientation: landscape) {
      body::after {
        content: "請旋轉裝置為直式模式以獲得最佳體驗";
        position: fixed;
        top: 0; left: 0;
        width: 100%; height: 100%;
        background: rgba(0,0,0,0.9);
        color: #fff;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 2rem;
        z-index: 9999;
      }
    }
  </style>
</head>
<body>
  <h1>惡靈古堡：網頁試煉</h1>
  <div id="story" class="fadeIn">
    <p id="text">你醒來在一棟廢棄醫院，外頭傳來奇怪的聲音...</p>
    <div id="buttons">
      <button onclick="explore()">探索房間</button>
      <button onclick="fight()">與怪物戰鬥</button>
      <button onclick="escape()">逃跑</button>
      <button onclick="restartGame()">重新開始</button>
    </div>
    <p>血量：<span id="hp">100</span></p>
    <div id="inventory">
      <strong>背包：</strong> <span id="bag">空</span>
    </div>
  </div>
  <div id="damageEffect"></div>
  <img id="jill" src="https://i.imgur.com/KVd0K6U.png" alt="Jill">

  <audio id="bgm" autoplay loop>
    <source src="https://freesound.org/data/previews/415/415209_5121236-lq.mp3" type="audio/mp3">
  </audio>
  <audio id="pickupSound" src="https://freesound.org/data/previews/146/146721_2558340-lq.mp3"></audio>
  <audio id="attackSound" src="https://freesound.org/data/previews/178/178186_2859978-lq.mp3"></audio>

  <script>
    let hp = 100;
    let bag = [];

    function updateUI(text) {
      document.getElementById("text").textContent = text;
      document.getElementById("hp").textContent = hp;
      document.getElementById("bag").textContent = bag.length ? bag.join(", ") : "空";
    }

    function playSound(id) {
      const sound = document.getElementById(id);
      if (sound) sound.play();
    }

    function showDamageEffect() {
      const effect = document.getElementById("damageEffect");
      effect.style.display = "block";
      setTimeout(() => { effect.style.display = "none"; }, 300);
    }

    function explore() {
      const events = [
        "你發現一瓶綠色藥草，回復10點血量。",
        "你撿到一把手槍。",
        "一隻喪屍突然撲向你！你損失20點血。",
        "這裡什麼也沒有，只有冷風..."
      ];
      let event = events[Math.floor(Math.random() * events.length)];
      if (event.includes("藥草")) {
        hp = Math.min(hp + 10, 100);
        bag.push("綠色藥草");
        playSound("pickupSound");
      } else if (event.includes("手槍")) {
        bag.push("手槍");
        playSound("pickupSound");
      } else if (event.includes("喪屍")) {
        hp -= 20;
        showDamageEffect();
        playSound("attackSound");
      }
      updateUI(event);
    }

    function fight() {
      if (bag.includes("手槍")) {
        updateUI("你使用手槍射擊，成功擊退怪物！");
        playSound("attackSound");
      } else {
        hp -= 30;
        updateUI("你沒有武器，被怪物重擊！損失30點血！");
        showDamageEffect();
        playSound("attackSound");
      }
    }

    function escape() {
      const result = Math.random() < 0.5 ? "你逃跑成功！" : "你被抓住，損失10點血。";
      if (result.includes("損失")) {
        hp -= 10;
        showDamageEffect();
        playSound("attackSound");
      }
      updateUI(result);
    }

    function restartGame() {
      hp = 100;
      bag = [];
      updateUI("你重新開始了冒險，仍然身處詭異的醫院...");
    }

    function randomMoveJill() {
      const jill = document.getElementById("jill");
      const newLeft = Math.random() * (window.innerWidth - 150) + "px";
      jill.style.left = newLeft;
    }
    setInterval(randomMoveJill, 3000);
  </script>
</body>
</html>
