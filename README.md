<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Catch the Ball Game</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #f4f4f4;
        }

        canvas {
            border: 2px solid #000;
            background-color: #e8e8e8;
        }

        h1 {
            text-align: center;
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas" width="500" height="600"></canvas>
    <script src="game.js"></script>
</body>
</html>const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');

// Spieler-Konfiguration
const playerWidth = 100;
const playerHeight = 20;
let playerX = (canvas.width - playerWidth) / 2;
let playerSpeed = 7;

// Ball-Konfiguration
const ballRadius = 10;
let balls = [];
let ballSpeed = 2;

// Spiel-Konfiguration
let rightPressed = false;
let leftPressed = false;
let score = 0;
let lives = 3;

// Tasten-Events
document.addEventListener('keydown', keyDownHandler, false);
document.addEventListener('keyup', keyUpHandler, false);

function keyDownHandler(e) {
    if (e.key === 'Right' || e.key === 'ArrowRight') {
        rightPressed = true;
    } else if (e.key === 'Left' || e.key === 'ArrowLeft') {
        leftPressed = true;
    }
}

function keyUpHandler(e) {
    if (e.key === 'Right' || e.key === 'ArrowRight') {
        rightPressed = false;
    } else if (e.key === 'Left' || e.key === 'ArrowLeft') {
        leftPressed = false;
    }
}

// Spieler zeichnen
function drawPlayer() {
    ctx.beginPath();
    ctx.rect(playerX, canvas.height - playerHeight, playerWidth, playerHeight);
    ctx.fillStyle = '#0095DD';
    ctx.fill();
    ctx.closePath();
}

// Ball zeichnen
function drawBalls() {
    balls.forEach((ball) => {
        ctx.beginPath();
        ctx.arc(ball.x, ball.y, ballRadius, 0, Math.PI * 2);
        ctx.fillStyle = '#DD4500';
        ctx.fill();
        ctx.closePath();
    });
}

// Ball hinzufügen
function addBall() {
    const ballX = Math.random() * (canvas.width - ballRadius * 2) + ballRadius;
    balls.push({ x: ballX, y: ballRadius });
}

// Ball-Position aktualisieren
function updateBalls() {
    balls.forEach((ball, index) => {
        ball.y += ballSpeed;

        // Überprüfen, ob der Ball gefangen wurde
        if (ball.y + ballRadius >= canvas.height - playerHeight &&
            ball.x > playerX && ball.x < playerX + playerWidth) {
            balls.splice(index, 1);
            score++;
            addBall();
        }

        // Überprüfen, ob der Ball den Boden erreicht
        if (ball.y + ballRadius > canvas.height) {
            balls.splice(index, 1);
            lives--;
            addBall();
        }
    });
}

// Spieler-Position aktualisieren
function updatePlayer() {
    if (rightPressed && playerX < canvas.width - playerWidth) {
        playerX += playerSpeed;
    } else if (leftPressed && playerX > 0) {
        playerX -= playerSpeed;
    }
}

// Score und Leben anzeigen
function drawScore() {
    ctx.font = '16px Arial';
    ctx.fillStyle = '#0095DD';
    ctx.fillText('Score: ' + score, 8, 20);
}

function drawLives() {
    ctx.font = '16px Arial';
    ctx.fillStyle = '#0095DD';
    ctx.fillText('Lives: ' + lives, canvas.width - 65, 20);
}

// Spielstatus überprüfen
function checkGameOver() {
    if (lives <= 0) {
        alert('Game Over! Your score was: ' + score);
        document.location.reload();
    }
}

// Haupt-Schleife des Spiels
function gameLoop() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    drawPlayer();
    drawBalls();
    drawScore();
    drawLives();
    updateBalls();
    updatePlayer();
    checkGameOver();
    requestAnimationFrame(gameLoop);
}

// Ball alle 2 Sekunden hinzufügen
setInterval(addBall, 2000);

// Spiel starten
addBall();
gameLoop();
