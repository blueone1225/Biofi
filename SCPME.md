<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8">
  <title>SCP：極限戰境 - 假面特工休</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    body {
      margin: 0;
      padding: 0;
      font-family: 'Courier New', monospace;
      background-color: #111;
      color: #eee;
    }
    .container {
      max-width: 960px;
      margin: auto;
      padding: 20px;
    }
    .option-button {
      background-color: #444;
      border: none;
      color: white;
      padding: 10px 20px;
      margin: 10px 0;
      cursor: pointer;
      font-size: 1em;
    }
    .option-button:hover {
      background-color: #666;
    }
    .game-image {
      width: 100%;
      height: auto;
    }
    #background {
      background-size: cover;
      height: 400px;
      background-image: url('https://via.placeholder.com/960x400/111/eee?text=Background+Image');
    }
    #character {
      width: 200px;
      height: auto;
      display: block;
      margin: 20px auto;
      border-radius: 10px;
    }
    #musicControl {
      position: absolute;
      top: 10px;
      right: 10px;
      background-color: #222;
      color: white;
      padding: 5px;
      cursor: pointer;
    }
    select {
      background-color: #444;
      color: white;
      padding: 5px;
      font-size: 1em;
    }
  </style>
</head>
<body>
  <div id="background"></div>
  <div class="container">
    <h1>SCP：極限戰境 - 假面特工休</h1>
    <img id="character" src="https://i.ytimg.com/vi/z8zIWOP6UhY/maxresdefault.jpg" alt="SCP-173">
    <div id="story"></div>
    <div id="options"></div>
    <div id="musicControl" onclick="toggleMusic()">🔊 音樂開 / 關</div>
    <label for="language">選擇語言: </label>
    <select id="language" onchange="changeLanguage()">
      <option value="zh-Hant">繁體中文</option>
      <option value="en">English</option>
    </select>
  </div>

  <audio id="backgroundMusic" loop>
    <source src="https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3" type="audio/mp3">
    Your browser does not support the audio element.
  </audio>

  <script>
    const storyElement = document.getElementById('story');
    const optionsElement = document.getElementById('options');
    const languageSelect = document.getElementById('language');
    const backgroundMusic = document.getElementById('backgroundMusic');
    const characterImage = document.getElementById('character');

    const storyNodes = {
      start: {
        text: "你是特工休·拉弗格，SCP 基金會的秘密行動人員。收容失效發生，你身處 Site-17 地下層。前方是兩條走廊。",
        options: [
          { text: "前往東側 SCP 收容室", next: "scpRoom", image: "https://i.ytimg.com/vi/z8zIWOP6UhY/maxresdefault.jpg" },  // SCP-173
          { text: "向西走到控制室", next: "controlRoom", image: "https://truth.bahamut.com.tw/s01/201608/ecf5bd4589f1d4015d42732177c08655.JPG" }  // SCP-049
        ]
      },
      scpRoom: {
        text: "你走進東側的 SCP 收容區，警報聲刺耳。門後傳來劇烈敲擊聲。",
        options: [
          { text: "開門查看", next: "encounter096", image: "https://i.ytimg.com/vi/yA3CHMyyHZ8/maxresdefault.jpg" },  // SCP-096
          { text: "退後離開", next: "retreat", image: "https://truth.bahamut.com.tw/s01/201608/ecf5bd4589f1d4015d42732177c08655.JPG" }  // SCP-049
        ]
      },
      controlRoom: {
        text: "你到達控制室，螢幕顯示 SCP-173 失控中。系統顯示需要輸入緊急密碼。",
        options: [
          { text: "輸入密碼 1234", next: "wrongCode", image: "https://truth.bahamut.com.tw/s01/201608/ecf5bd4589f1d4015d42732177c08655.JPG" },  // SCP-049
          { text: "輸入密碼 A17X", next: "containSuccess", image: "https://i.ytimg.com/vi/z8zIWOP6UhY/maxresdefault.jpg" }  // SCP-173
        ]
      },
      encounter096: {
        text: "你看見一個瘦高的 humanoid 正哭泣。它抬頭看見你——SCP-096 瘋狂衝來！",
        options: [
          { text: "嘗試逃跑", next: "death", image: "https://i.ytimg.com/vi/yA3CHMyyHZ8/maxresdefault.jpg" },  // SCP-096
          { text: "引爆手榴彈", next: "sacrifice", image: "https://truth.bahamut.com.tw/s01/201608/ecf5bd4589f1d4015d42732177c08655.JPG" }  // SCP-049
        ]
      },
      retreat: {
        text: "你安全返回交叉口，但SCP-049 出現在你背後……他伸出手：'你需要治療。'",
        options: [
          { text: "戰鬥", next: "death", image: "https://truth.bahamut.com.tw/s01/201608/ecf5bd4589f1d4015d42732177c08655.JPG" },  // SCP-049
          { text: "假裝感染", next: "trick049", image: "https://truth.bahamut.com.tw/s01/201608/ecf5bd4589f1d4015d42732177c08655.JPG" }  // SCP-049
        ]
      },
      wrongCode: {
        text: "系統顯示錯誤。SCP-173 突然出現在你背後，一切陷入黑暗。",
        options: [
          { text: "重新開始", next: "start", image: "https://i.ytimg.com/vi/z8zIWOP6UhY/maxresdefault.jpg" }  // SCP-173
        ]
      },
      containSuccess: {
        text: "你成功啟動收容機制，至少暫時安全。你贏了這一回合。",
        options: [
          { text: "重新開始冒險", next: "start", image: "https://i.ytimg.com/vi/z8zIWOP6UhY/maxresdefault.jpg" }  // SCP-173
        ]
      },
      death: {
        text: "你遭遇致命危機，無法倖免。",
        options: [
          { text: "重新開始", next: "start", image: "https://i.ytimg.com/vi/z8zIWOP6UhY/maxresdefault.jpg" }  // SCP-173
        ]
      },
      sacrifice: {
        text: "你與 SCP-096 同歸於盡。英雄式犧牲。",
        options: [
          { text: "重新開始", next: "start", image: "https://i.ytimg.com/vi/z8zIWOP6UhY/maxresdefault.jpg" }  // SCP-173
        ]
      },
      trick049: {
        text: "SCP-049 相信你患病，將你拖入研究室……你暫時
