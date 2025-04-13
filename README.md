<!DOCTYPE html><html lang="ar">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>لعبة الحروف</title>
  <style>
    body {
      background-color: #fff9f2;
      font-family: sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 10px;
    }
    #grid {
      display: grid;
      grid-template-columns: repeat(5, 1fr);
      gap: 0;
      margin-bottom: 15px;
      transition: all 0.3s ease;
    }
    .hex {
      width: var(--cell-size, 60px);
      height: var(--cell-size, 60px);
      background-color: white;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 1.5em;
      border: 3px solid #caa4ff;
      color: black;
      transition: background-color 0.3s;
      cursor: pointer;
      border-radius: 10px;
    }
    .controls {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      gap: 10px;
      margin-bottom: 15px;
    }
    button {
      padding: 10px;
      font-size: 1.1em;
      border-radius: 8px;
      border: none;
      cursor: pointer;
    }
    .celebration {
      font-size: 3em;
      color: #a259ff;
      display: none;
      margin-bottom: 10px;
    }
    .slider {
      width: 200px;
    }
  </style>
</head>
<body>
  <div id="celebration" class="celebration">مبروك!</div>
  <div id="grid"></div>
  <div class="controls">
    <button onclick="shuffleLetters()">تغيير الحروف</button>
    <button onclick="toggleColors()">تغيير الألوان</button>
    <button onclick="celebrate()">احتفال</button>
    <input type="range" min="40" max="100" value="60" class="slider" onchange="resizeCells(this.value)" />
  </div>  <script>
    const arabicLetters = [..."أبجدهوزحطيكلمنسعفصقرشتثخذضظغ"];
    const grid = document.getElementById("grid");
    const celebration = document.getElementById("celebration");

    const yellow = "#fff3bf";
    const greenCycle = ["#b2f2bb", "#ff6b6b", "#ced4da"];
    const orangeCycle = ["#ffa94d", "#91a7ff", "#eebefa"];
    let greenIndex = 0;
    let orangeIndex = 0;

    let colorCycle = ["#fff9f2", yellow, greenCycle[greenIndex], orangeCycle[orangeIndex]];

    function generateGrid() {
      grid.innerHTML = "";
      for (let i = 0; i < 25; i++) {
        const hex = document.createElement("div");
        hex.className = "hex";
        hex.textContent = arabicLetters[Math.floor(Math.random() * arabicLetters.length)];
        hex.style.backgroundColor = colorCycle[0];
        hex.dataset.colorIndex = 0;
        grid.appendChild(hex);
        hex.addEventListener("click", () => cycleColor(hex));
      }
    }

    function shuffleLetters() {
      [...grid.children].forEach(hex => {
        hex.textContent = arabicLetters[Math.floor(Math.random() * arabicLetters.length)];
        hex.style.backgroundColor = colorCycle[0];
        hex.dataset.colorIndex = 0;
      });
      celebration.style.display = "none";
    }

    function toggleColors() {
      const prevGreen = greenCycle[greenIndex];
      const prevOrange = orangeCycle[orangeIndex];

      greenIndex = (greenIndex + 1) % greenCycle.length;
      orangeIndex = (orangeIndex + 1) % orangeCycle.length;
      colorCycle = ["#fff9f2", yellow, greenCycle[greenIndex], orangeCycle[orangeIndex]];

      [...grid.children].forEach(hex => {
        if (hex.style.backgroundColor === rgb(prevGreen)) {
          hex.style.backgroundColor = greenCycle[greenIndex];
        } else if (hex.style.backgroundColor === rgb(prevOrange)) {
          hex.style.backgroundColor = orangeCycle[orangeIndex];
        }
      });
    }

    function rgb(hex) {
      const dummy = document.createElement("div");
      dummy.style.color = hex;
      document.body.appendChild(dummy);
      const computed = getComputedStyle(dummy).color;
      document.body.removeChild(dummy);
      return computed;
    }

    function resizeCells(value) {
      document.documentElement.style.setProperty('--cell-size', value + 'px');
    }

    function celebrate() {
      celebration.style.display = "block";
      playCelebrationSound();
    }

    function cycleColor(cell) {
      let index = parseInt(cell.dataset.colorIndex);
      index = (index + 1) % colorCycle.length;
      cell.style.backgroundColor = colorCycle[index];
      cell.dataset.colorIndex = index;
    }

    function playCelebrationSound() {
      const context = new (window.AudioContext || window.webkitAudioContext)();
      const o = context.createOscillator();
      const g = context.createGain();
      o.type = 'triangle';
      o.connect(g);
      g.connect(context.destination);
      o.frequency.setValueAtTime(880, context.currentTime);
      g.gain.setValueAtTime(0.4, context.currentTime);
      o.start();
      o.stop(context.currentTime + 0.3);
    }

    generateGrid();
  </script></body>
</html>
