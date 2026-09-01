<!DOCTYPE html>
<html lang="ckb" dir="rtl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>یاری ڕێکخستنی ڕەنگەکان</title>
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: system-ui, -apple-system, sans-serif;
    }

    body {
      background: linear-gradient(to bottom, #7ec0ee 0%, #a1dbcd 60%, #10c694 100%);
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: space-between;
      padding: 15px;
      overflow: hidden;
      position: relative;
    }

    /* خۆرەکە لە بەشی ڕاستی سەرەوە */
    .sun {
      position: absolute;
      top: 100px;
      right: -20px;
      width: 130px;
      height: 130px;
      background: #ffe600;
      border-radius: 50%;
      box-shadow: 0 0 40px #ffe600;
      z-index: 1;
    }

    /* گرد و چیا سەوزەکان */
    .hills {
      position: absolute;
      bottom: 0;
      left: 0;
      width: 100%;
      height: 250px;
      background: radial-gradient(circle at 50% 100%, #2ecc71 50%, #1abc9c 100%);
      border-radius: 50% 50% 0 0 / 40% 40% 0 0;
      z-index: 1;
    }

    /* بەشی سەرەوە (Header) */
    .top-bar {
      width: 100%;
      max-width: 380px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      z-index: 10;
      margin-top: 10px;
    }

    .info-card {
      background: rgba(255, 255, 255, 0.45);
      backdrop-filter: blur(10px);
      border: 1px solid rgba(255, 255, 255, 0.6);
      padding: 8px 20px;
      border-radius: 25px;
      font-weight: bold;
      color: #1a365d;
      display: flex;
      gap: 15px;
      font-size: 16px;
      box-shadow: 0 4px 15px rgba(0,0,0,0.05);
    }

    .coin-box {
      background: rgba(255, 255, 255, 0.7);
      padding: 6px 14px;
      border-radius: 20px;
      font-weight: bold;
      color: #d68910;
      box-shadow: 0 2px 8px rgba(0,0,0,0.1);
      display: flex;
      align-items: center;
      gap: 5px;
    }

    .music-btn {
      background: rgba(255, 255, 255, 0.7);
      border: none;
      padding: 6px 12px;
      border-radius: 50%;
      font-size: 16px;
      cursor: pointer;
      box-shadow: 0 2px 8px rgba(0,0,0,0.1);
    }

    /* شوێنی تاوەر/تستیووبەکان */
    .game-container {
      display: flex;
      justify-content: center;
      gap: 10px;
      z-index: 10;
      margin-top: 40px;
      flex-wrap: wrap;
      max-width: 380px;
    }

    .tube {
      width: 52px;
      height: 190px;
      border: 3px solid rgba(255, 255, 255, 0.85);
      border-radius: 0 0 25px 25px;
      display: flex;
      flex-direction: column-reverse;
      overflow: hidden;
      background: rgba(255, 255, 255, 0.25);
      backdrop-filter: blur(4px);
      cursor: pointer;
      transition: transform 0.2s ease;
      box-shadow: inset 0 0 10px rgba(255,255,255,0.5);
    }

    .tube.selected {
      transform: translateY(-20px);
      border-color: #ffffff;
      box-shadow: 0 10px 20px rgba(0,0,0,0.2), inset 0 0 15px rgba(255,255,255,0.8);
    }

    .block {
      width: 100%;
      height: 25%;
      border-bottom: 1px solid rgba(255, 255, 255, 0.2);
      transition: all 0.2s ease;
    }

    /* ڕەنگەکانی تاوەر */
    .red { background-color: #e74c3c; }
    .blue { background-color: #3498db; }
    .yellow { background-color: #f1c40f; }
    .green { background-color: #2ecc71; }

    /* دۆگمەکانی ژێرەوە */
    .controls {
      z-index: 10;
      display: flex;
      gap: 15px;
      margin-bottom: 20px;
    }

    .btn {
      background: rgba(255, 255, 255, 0.45);
      backdrop-filter: blur(8px);
      border: 1px solid rgba(255, 255, 255, 0.6);
      padding: 10px 22px;
      border-radius: 20px;
      font-weight: bold;
      color: #1a365d;
      cursor: pointer;
      box-shadow: 0 4px 10px rgba(0,0,0,0.08);
    }

    .btn:active {
      transform: scale(0.95);
    }
  </style>
</head>
<body>

  <div class="sun"></div>
  <div class="hills"></div>

  <!-- بەشی سەرەوە -->
  <div class="top-bar">
    <div class="coin-box">
      🪙 <span id="coinCount">0</span>
    </div>

    <div class="info-card">
      <span>🌲 قۆناغ: <span id="level">1</span></span>
      <span>جووڵەکان: <span id="moves">0</span>/12</span>
    </div>

    <button class="music-btn" onclick="toggleMusic()" id="musicBtn">🎵</button>
  </div>

  <!-- یارییەکە -->
  <div class="game-container" id="gameContainer"></div>

  <!-- دۆگمەکان -->
  <div class="controls">
    <button class="btn" onclick="restartLevel()">دووبارەکردنەوە 🔄</button>
    <button class="btn" onclick="undoMove()">پاشگەزبوونەوە ↩️</button>
  </div>

  <!-- دەنگی میوزیک -->
  <audio id="bgMusic" loop preload="auto">
    <source src="https://assets.mixkit.co/music/preview/mixkit-game-show-suspense-waiting-667.mp3" type="audio/mpeg">
  </audio>

  <script>
    let coins = parseInt(localStorage.getItem('userCoins')) || 0;
    let moveCount = 0;
    let selectedTubeIndex = null;
    let history = [];

    const bgMusic = document.getElementById('bgMusic');
    document.getElementById('coinCount').innerText = coins;

    // بارگاویکردنی ڕەنگەکان ڕێک وەک وێنەکەی یەکەم سکرین شۆت
    let tubesData = [
      [], 
      ['yellow', 'red', 'red', 'red'],
      ['blue', 'green', 'blue', 'red'],
      ['yellow', 'blue', 'yellow', 'yellow'],
      ['red', 'green', 'blue', 'blue']
    ];

    function render() {
      const container = document.getElementById('gameContainer');
      container.innerHTML = '';

      tubesData.forEach((tube, index) => {
        const tubeDiv = document.createElement('div');
        tubeDiv.className = 'tube' + (selectedTubeIndex === index ? ' selected' : '');
        tubeDiv.onclick = () => handleTubeClick(index);

        tube.forEach(color => {
          const block = document.createElement('div');
          block.className = `block ${color}`;
          tubeDiv.appendChild(block);
        });

        container.appendChild(tubeDiv);
      });

      document.getElementById('moves').innerText = moveCount;
    }

    function handleTubeClick(index) {
      if (bgMusic.paused) {
        bgMusic.play().catch(() => {});
      }

      if (selectedTubeIndex === null) {
        if (tubesData[index].length > 0) {
          selectedTubeIndex = index;
        }
      } else {
        if (selectedTubeIndex !== index) {
          moveBlock(selectedTubeIndex, index);
        }
        selectedTubeIndex = null;
      }
      render();
    }

    function moveBlock(from, to) {
      const fromTube = tubesData[from];
      const toTube = tubesData[to];

      if (fromTube.length === 0 || toTube.length >= 4) return;

      const topColor = fromTube[fromTube.length - 1];
      if (toTube.length === 0 || toTube[toTube.length - 1] === topColor) {
        // خەزنکردنی مێژوو بۆ پاشگەزبوونەوە
        history.push(JSON.parse(JSON.stringify(tubesData)));

        toTube.push(fromTube.pop());
        moveCount++;
        checkWin();
      }
    }

    function undoMove() {
      if (history.length > 0) {
        tubesData = history.pop();
        if (moveCount > 0) moveCount--;
        render();
      }
    }

    function checkWin() {
      let isWin = tubesData.every(tube => 
        tube.length === 0 || (tube.length === 4 && tube.every(c => c === tube[0]))
      );

      if (isWin) {
        setTimeout(() => {
          coins += 10;
          localStorage.setItem('userCoins', coins);
          document.getElementById('coinCount').innerText = coins;
          alert("🎉 دەستخۆش! قۆناغەکەت بڕی و ۱۰ کۆینت بردەوە 🪙");
          restartLevel();
        }, 200);
      }
    }

    function restartLevel() {
      moveCount = 0;
      selectedTubeIndex = null;
      history = [];
      tubesData = [
        [], 
        ['yellow', 'red', 'red', 'red'],
        ['blue', 'green', 'blue', 'red'],
        ['yellow', 'blue', 'yellow', 'yellow'],
        ['red', 'green', 'blue', 'blue']
      ];
      render();
    }

    function toggleMusic() {
      const btn = document.getElementById('musicBtn');
      if (bgMusic.paused) {
        bgMusic.play();
        btn.innerText = '🎵';
      } else {
        bgMusic.pause();
        btn.innerText = '🔇';
      }
    }

    render();
  </script>
</body>
</html>
