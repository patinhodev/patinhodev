# Hi, I'm Fabiano 👋

🌱 Software Engineering student [Estácio de Sá] (2025-2029)  
💻 Focused on Python and C programming  
🎮 Aspiring game developer  
📍 Rio de Janeiro, Brazil  

## Projects

- 🧪 **Data Analysis Tool** (Python)  
- 🛠️ **System Utilities** (C)  
- 🎮 **2D Game Prototype** (Python + Pygame)  
- 🧠 **Future Projects**: experimenting with **Unity**, **Godot**, and **SDL**  

## Skills

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![C](https://img.shields.io/badge/C-00599C?style=for-the-badge&logo=c&logoColor=white)
![Git](https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white)
![VS Code](https://img.shields.io/badge/VS%20Code-007ACC?style=for-the-badge&logo=visual-studio-code&logoColor=white)
![Pygame](https://img.shields.io/badge/Pygame-009688?style=for-the-badge&logo=python&logoColor=white)
![Unity](https://img.shields.io/badge/Unity-000000?style=for-the-badge&logo=unity&logoColor=white)
![Godot](https://img.shields.io/badge/Godot-478CBF?style=for-the-badge&logo=godot-engine&logoColor=white)
![SDL](https://img.shields.io/badge/SDL-FF6F00?style=for-the-badge&logo=c&logoColor=white)

## Certifications

- Introduction to Programming with Python – Santander Open Academy  
- Python 3 – World 1 – Guanabara  
- Python 3 – World 2 – Guanabara 
- Python 3 – World 3 – Guanabara 

<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="utf-8">
<title>River Raid HTML5 Demo - Pixel Art</title>
<style>
  body {
    background-color: #111;
    margin: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    overflow: hidden;
    color: #fff;
    font-family: 'Courier New', monospace;
    flex-direction: column;
  }
  canvas {
    image-rendering: pixelated; 
    box-shadow: 0 0 20px rgba(0,0,0,0.8);
    border: 4px solid #333;
    width: 100%;
    max-width: 500px; 
  }
</style>
</head>
<body>

<canvas id="gameCanvas" width="320" height="400"></canvas>

<script>
  const canvas = document.getElementById('gameCanvas');
  const ctx = canvas.getContext('2d');
  
  let frame = 0;
  let score = 0;

  // Paleta de Cores Atari 2600
  const PALETTE = {
    water: '#2C3A9B',   
    grass: '#4B7B24',   
    grassLight: '#5C962C', 
    plane: '#FFFAFA',   
    enemyRed: '#C23B22',
    bullet: '#000000'
  };

  // Sprite do Avião Principal
  const SPRITE_PLANE = [
    "    1    ",
    "    1    ",
    "   111   ",
    "   111   ",
    "  11111  ",
    "111111111",
    "111111111",
    "100111001",
    "100111001"
  ];

  // Navio inimigo (Vermelho)
  const SPRITE_SHIP = [
    "   2222  ",
    " 22222222",
    "222222222",
    "222222222"
  ];

  function drawSprite(sprite, x, y, scale) {
    for(let r=0; r < sprite.length; r++) {
      for(let c=0; c < sprite[r].length; c++) {
        let char = sprite[r][c];
        if(char === "1") ctx.fillStyle = PALETTE.plane;
        else if(char === "2") ctx.fillStyle = PALETTE.enemyRed;
        else continue;
        
        ctx.fillRect(x + (c * scale), y + (r * scale), scale, scale);
      }
    }
  }

  // Arrays que controlam a geração "infinita" do cenário
  let leftBank = new Array(100).fill(60);
  let rightBank = new Array(100).fill(60);
  
  let playerX = 160;
  let playerY = 300;
  
  let bullets = [];
  let enemies = [
    { x: 140, y: -50, type: 'ship', active: true },
    { x: 180, y: -300, type: 'ship', active: true }
  ];
  let explosions = [];

  function update() {
    frame++;
    
    // Movimentar e desenhar o rio de forma dinâmica 
    if(frame % 2 === 0) {
      let newLeft = 50 + Math.sin(frame * 0.03) * 35 + (Math.random() * 5);
      let newRight = 50 + Math.cos(frame * 0.04) * 35 + (Math.random() * 5);
      
      leftBank.unshift(newLeft);
      leftBank.pop();
      rightBank.unshift(newRight);
      rightBank.pop();
    }

    // Piloto Automático
    playerX = 160 + Math.sin(frame * 0.04) * 45;

    // Atirar
    if(frame % 60 === 0) {
       bullets.push({ x: playerX - 1, y: playerY });
    }

    for(let i = bullets.length - 1; i >= 0; i--) {
        bullets[i].y -= 8;
        if(bullets[i].y < 0) bullets.splice(i, 1);
    }

    // Inimigos 
    enemies.forEach(en => {
        if(en.active) {
            en.y += 2.2; 
            
            bullets.forEach((b, bIdx) => {
                if(b.x > en.x - 25 && b.x < en.x + 35 && b.y > en.y && b.y < en.y + 20) {
                    en.active = false;
                    score += 50;
                    bullets.splice(bIdx, 1);
                    explosions.push({ x: en.x, y: en.y, timer: 15 });
                }
            });
        }
        
        if(en.y > 450 || (!en.active && en.y > en.y + 80)) {
            en.y = -80 - Math.random() * 150;
            en.x = leftBank[0] + 30 + Math.random() * 80; 
            en.active = true;
        }
    });

    // Explosões
    for(let i = explosions.length - 1; i >= 0; i--) {
        explosions[i].timer--;
        explosions[i].y += 1.5; 
        if(explosions[i].timer <= 0) explosions.splice(i, 1);
    }
  }

  function draw() {
    // 1. Água
    ctx.fillStyle = PALETTE.water;
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    // 2. Grama lateral (River Banks)
    for(let i = 0; i < leftBank.length; i++) {
        let ry = i * 4;
        ctx.fillStyle = (i + Math.floor(frame/3)) % 4 < 2 ? PALETTE.grass : PALETTE.grassLight;
        ctx.fillRect(0, ry, leftBank[i], 4);
        ctx.fillRect(320 - rightBank[i], ry, rightBank[i], 4);
    }

    // 3. Barcos Inimigos
    enemies.forEach(en => {
        if(en.active) {
            drawSprite(SPRITE_SHIP, en.x - 16, en.y, 4);
        }
    });

    // 4. Explosões do jogo
    explosions.forEach(exp => {
        ctx.fillStyle = '#FFA500'; 
        let spread = 18 - exp.timer;
        ctx.fillRect(exp.x - spread, exp.y - spread, 10, 10);
        ctx.fillRect(exp.x + spread, exp.y + spread, 10, 10);
        ctx.fillRect(exp.x - spread, exp.y + spread, 10, 10);
        ctx.fillRect(exp.x + spread, exp.y - spread, 10, 10);
    });

    // 5. Balas do jogador
    bullets.forEach(b => {
         ctx.fillStyle = PALETTE.bullet;
         ctx.fillRect(b.x, b.y, 4, 12);
    });

    // 6. Avião do Jogador
    drawSprite(SPRITE_PLANE, playerX - 16, playerY, 4);
    
    // Animação de chama no propulsor do avião
    if(frame % 6 < 3) {
        ctx.fillStyle = '#FFA500';
        ctx.fillRect(playerX - 6, playerY + 36, 12, 6);
    }
  }

  function gameLoop() {
    update();
    draw();
    requestAnimationFrame(gameLoop);
  }

  // Da o Start na Animação
  gameLoop();

</script>
</body>
</html>



