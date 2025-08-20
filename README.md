# Ca-a-ao-pl-stico
Ajude a limpar o oceano
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Caça ao Plástico</title>
  <style>
    body {
      margin: 0;
      overflow: hidden;
      background: linear-gradient(#0099cc, #003366);
    }
    canvas {
      display: block;
      margin: auto;
      background: url('https://i.ibb.co/dLfZ1bR/fundo-mar.png') no-repeat center/cover;
    }
    #hud {
      position: absolute;
      top: 10px;
      left: 10px;
      color: white;
      font-family: Arial, sans-serif;
      font-size: 22px;
      font-weight: bold;
      text-shadow: 2px 2px 4px black;
    }
    #endScreen {
      display: none;
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0,0,0,0.7);
      color: white;
      font-family: Arial, sans-serif;
      font-size: 32px;
      font-weight: bold;
      text-align: center;
      padding-top: 200px;
    }
  </style>
</head>
<body>
  <div id="hud">Pontos: 0 | Tempo: 30s</div>
  <div id="endScreen"></div>
  <canvas id="gameCanvas" width="500" height="700"></canvas>

  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");
    const hud = document.getElementById("hud");
    const endScreen = document.getElementById("endScreen");

    // imagens
    let playerImg = new Image();
    playerImg.src = "https://i.ibb.co/XDdz2mP/diver.png";

    let plasticImgs = [
      "https://i.ibb.co/sykVRd1/plastic-bottle.png",
      "https://i.ibb.co/6r5HxgV/plastic-bag.png"
    ].map(src => {
      let img = new Image();
      img.src = src;
      return img;
    });

    let fishImg = new Image();
    fishImg.src = "https://i.ibb.co/pbXKrC1/fish.png";

    // sons
    let soundCollect = new Audio("https://actions.google.com/sounds/v1/cartoon/wood_plank_flicks.ogg");
    let soundHit = new Audio("https://actions.google.com/sounds/v1/cartoon/clang_and_wobble.ogg");
    let soundWin = new Audio("https://actions.google.com/sounds/v1/cartoon/descending_whistle.ogg");

    // jogador e objetos
    let player = { x: 220, y: 600, w: 60, h: 60 };
    let plastics = [];
    let fishes = [];
    let score = 0;
    let timeLeft = 30;
    let gameOver = false;

    function drawPlayer() {
      ctx.drawImage(playerImg, player.x, player.y, player.w, player.h);
    }

    function spawnPlastic() {
      plastics.push({
        x: Math.random()*460, y: -50, w: 40, h: 40,
        img: plasticImgs[Math.floor(Math.random()*plasticImgs.length)]
      });
    }

    function spawnFish() {
      fishes.push({ x: Math.random()*460, y: -50, w: 60, h: 40, img: fishImg });
    }

    function drawPlastics() {
      plastics.forEach(p => ctx.drawImage(p.img, p.x, p.y, p.w, p.h));
    }

    function drawFishes() {
      fishes.forEach(f => ctx.drawImage(f.img, f.x, f.y, f.w, f.h));
    }

    function update() {
      if (gameOver) return;

      ctx.clearRect(0, 0, canvas.width, canvas.height);
      drawPlayer();
      drawPlastics();
      drawFishes();

      plastics.forEach(p => p.y += 3);
      fishes.forEach(f => f.y += 4);

      // colisões com plásticos
      plastics = plastics.filter(p => {
        if (p.x < player.x + player.w &&
            p.x + p.w > player.x &&
            p.y < player.y + player.h &&
            p.y + p.h > player.y) {
          score += 10;
          soundCollect.play();
          return false;
        }
        return p.y < 700;
      });

      // colisões com peixes
      fishes = fishes.filter(f => {
        if (f.x < player.x + player.w &&
            f.x + f.w > player.x &&
            f.y < player.y + player.h &&
            f.y + f.h > player.y) {
          score -= 15;
          soundHit.play();
          return false;
        }
        return f.y < 700;
      });

      hud.innerText = "Pontos: " + score + " | Tempo: " + timeLeft + "s";
    }

    // movimento do jogador
    document.addEventListener("keydown", e => {
      if (e.key === "ArrowLeft" && player.x > 0) player.x -= 20;
      if (e.key === "ArrowRight" && player.x < canvas.width - player.w) player.x += 20;
    });

    setInterval(spawnPlastic, 1500);
    setInterval(spawnFish, 2500);
    setInterval(update, 30);

    // contador de tempo
    let timer = setInterval(() => {
      if (timeLeft > 0) {
        timeLeft--;
      } else {
        gameOver = true;
        clearInterval(timer);
        endScreen.style.display = "block";
        soundWin.play();
        endScreen.innerHTML = `🌊 Você salvou o oceano! <br><br> Pontos finais: ${score} <br><br> 🌟`;
      }
    }, 1000);
  </script>
</body>
</html>
