# mycv
trochoi
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Xé Túi Mù – Vần Vui Nhộn</title>
  <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@500&display=swap" rel="stylesheet"/>
  <style>
    body {
      font-family: 'Fredoka', sans-serif;
      background: linear-gradient(to right, #fceabb, #f8b500);
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 30px;
      margin: 0;
    }

    h1 {
      font-size: 2.2rem;
      color: #2c2c2c;
      margin-bottom: 10px;
    }

    h2 {
      font-size: 1rem;
      color: #333;
      margin-bottom: 30px;
    }

    #start-button, #reset-button {
      padding: 12px 24px;
      font-size: 1.2rem;
      background-color: #ff6f61;
      color: white;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      margin-bottom: 20px;
      transition: background-color 0.3s;
    }

    #start-button:hover, #reset-button:hover {
      background-color: #ff3b2e;
    }

    #bags-container {
      display: none;
      flex-wrap: wrap;
      justify-content: center;
      gap: 20px;
    }

    .bag-container {
      position: relative;
      width: 120px;
      height: 160px;
      cursor: pointer;
      perspective: 1000px;
      animation: drop 0.8s ease-in;
    }

    @keyframes drop {
      0% { transform: translateY(-200px) scale(0.7); opacity: 0; }
      100% { transform: translateY(0) scale(1); opacity: 1; }
    }

    .bag-sides {
      position: absolute;
      width: 100%;
      height: 100%;
      display: flex;
      z-index: 2;
    }

    .bag-side {
      width: 50%;
      height: 100%;
      background-size: cover;
      background-position: center;
      border: 3px solid white;
      box-shadow: 0 4px 10px rgba(0,0,0,0.3);
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 2rem;
      font-weight: bold;
      color: white;
      transition: transform 1.2s ease-in-out;
      background-color: #ffb347;
    }

    .left-side {
      border-top-left-radius: 15px;
      border-bottom-left-radius: 15px;
    }

    .right-side {
      border-top-right-radius: 15px;
      border-bottom-right-radius: 15px;
    }

    .bag-container.torn .left-side {
      transform: translateX(-140%) rotate(-30deg);
    }

    .bag-container.torn .right-side {
      transform: translateX(140%) rotate(30deg);
    }

    .bag-content {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: #fffbe6;
      color: #333;
      border-radius: 15px;
      display: flex;
      align-items: center;
      justify-content: center;
      padding: 10px;
      font-size: 1rem;
      font-weight: bold;
      box-shadow: 0 3px 6px rgba(0,0,0,0.2);
      opacity: 0;
      z-index: 1;
      transition: opacity 0.4s ease-in-out;
      text-align: center;
    }

    .bag-container.torn .bag-content {
      opacity: 1;
      z-index: 10;
    }

    #challenge-text {
      margin-top: 30px;
      padding: 20px;
      background: #ffffffcc;
      border-radius: 12px;
      box-shadow: 0 3px 6px rgba(0,0,0,0.15);
      font-size: 1.3rem;
      color: #333;
      max-width: 500px;
      text-align: center;
      display: none;
    }

    /* Rung túi */
    @keyframes shake {
      0% { transform: translateX(0); }
      25% { transform: translateX(-5px); }
      50% { transform: translateX(5px); }
      75% { transform: translateX(-5px); }
      100% { transform: translateX(0); }
    }

    .bag-container.shake {
      animation: shake 0.3s ease-in-out;
    }
  </style>
</head>
<body>
  <h1>XÉ TÚI MÙ – HỌC VẦN VUI NHỘN</h1>
  <h2>Giáo viên: Nguyễn Lê Minh Nhựt</h2>
  <button id="start-button">Bắt đầu trò chơi</button>
  <button id="reset-button" style="display:none;">Chơi lại</button>

  <div id="bags-container"></div>
  <div id="challenge-text">Hãy chọn một túi để khám phá nhiệm vụ!</div>

  <!-- Âm thanh xé túi -->
  <audio id="tear-sound" src="xe_giay.mp3.mp3" preload="auto"></audio>

  <!-- Nhạc nền -->
  <audio id="bg-music" src="nhac_nen.mp3.mp3" preload="auto" loop></audio>

  <script>
    const tasks = [
      "🎤 Nói nhanh 3 tiếng có vần ach trong 5 giây",
      "Đọc to tiếng: *bách, thách, nhách*",
      "Ghép từ với vần *êch*",
      "Viết câu với từ: *kịch*",
      "Tìm vần giống nhau trong: *mách, lách, tách*",
      "Phân biệt âm đầu trong *ich* và *êch*",
      "Trong 5 giây, em hãy nói 3 từ khác nhau có vần ich và diễn tả 1 từ bằng hành động hài hước!",
      "🗣️ Thì thầm vào tai bạn một từ có vần êch mà khiến bạn ấy phải bật cười"

    ];

    const colors = ['#ff9999', '#ffcc99', '#ffff99', '#ccff99', '#99ffcc', '#99ccff', '#cc99ff', '#ff99cc'];
    const container = document.getElementById("bags-container");
    const challengeText = document.getElementById("challenge-text");
    const startButton = document.getElementById("start-button");
    const resetButton = document.getElementById("reset-button");
    const bgMusic = document.getElementById("bg-music");

    let lastOpenedBag = null;
    let openedCount = 0;

    startButton.addEventListener("click", () => {
      startButton.style.display = "none";
      container.style.display = "flex";
      challengeText.style.display = "block";
      resetButton.style.display = "none";
      createBags();
      bgMusic.volume = 0.4;
      bgMusic.play();
    });

    resetButton.addEventListener("click", () => {
      container.innerHTML = "";
      challengeText.innerHTML = "Hãy chọn một túi để khám phá nhiệm vụ!";
      openedCount = 0;
      lastOpenedBag = null;
      resetButton.style.display = "none";
      container.style.display = "flex";
      createBags();
    });

    function createBags() {
      for (let i = 0; i < tasks.length; i++) {
        const bagContainer = document.createElement("div");
        bagContainer.className = "bag-container";

        const bagSides = document.createElement("div");
        bagSides.className = "bag-sides";

        const left = document.createElement("div");
        left.className = "bag-side left-side";
        left.style.backgroundColor = colors[i % colors.length];
        left.innerText = i + 1;

        const right = document.createElement("div");
        right.className = "bag-side right-side";
        right.style.backgroundColor = colors[i % colors.length];

        bagSides.appendChild(left);
        bagSides.appendChild(right);

        const content = document.createElement("div");
        content.className = "bag-content";
        content.innerHTML = tasks[i];

        bagContainer.appendChild(bagSides);
        bagContainer.appendChild(content);
        container.appendChild(bagContainer);

        bagContainer.addEventListener("click", () => {
          if (!bagContainer.classList.contains("torn") && !bagContainer.classList.contains("shake")) {
            if (lastOpenedBag) {
              lastOpenedBag.style.display = "none";
            }

            bagContainer.classList.add("shake");

            setTimeout(() => {
              bagContainer.classList.remove("shake");
              bagContainer.classList.add("torn");
              document.getElementById("tear-sound").play();

              setTimeout(() => {
                challengeText.innerHTML = `<strong>Nhiệm vụ:</strong><br>${tasks[i]}`;
              }, 1200);
            }, 300);

            lastOpenedBag = bagContainer;
            openedCount++;

            if (openedCount === tasks.length) {
              resetButton.style.display = "inline-block";
            }
          }
        });
      }
    }
  </script>
</body>
</html>
