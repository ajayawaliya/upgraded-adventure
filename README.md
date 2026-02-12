# upgraded-adventure
<!DOCTYPE html>
<html>
<head>
    <title>Simple Animation Game</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background: black;
        }
        canvas {
            display: block;
            margin: auto;
            background: #111;
        }
    </style>
</head>
<body>

<canvas id="gameCanvas"></canvas>

<script>
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

canvas.width = window.innerWidth;
canvas.height = window.innerHeight;

let player = {
    x: canvas.width / 2 - 25,
    y: canvas.height - 60,
    width: 50,
    height: 50,
    speed: 7
};

let obstacles = [];
let score = 0;
let gameOver = false;

// Controls
let keys = {};
document.addEventListener("keydown", e => keys[e.key] = true);
document.addEventListener("keyup", e => keys[e.key] = false);

function spawnObstacle() {
    obstacles.push({
        x: Math.random() * (canvas.width - 40),
        y: -40,
        width: 40,
        height: 40,
        speed: 4 + Math.random() * 3
    });
}

function update() {
    if (gameOver) return;

    // Move player
    if (keys["ArrowLeft"] && player.x > 0)
        player.x -= player.speed;
    if (keys["ArrowRight"] && player.x + player.width < canvas.width)
        player.x += player.speed;

    // Move obstacles
    obstacles.forEach(ob => ob.y += ob.speed);

    // Collision detection
    obstacles.forEach(ob => {
        if (
            player.x < ob.x + ob.width &&
            player.x + player.width > ob.x &&
            player.y < ob.y + ob.height &&
            player.y + player.height > ob.y
        ) {
            gameOver = true;
        }
    });

    // Remove off-screen obstacles
    obstacles = obstacles.filter(ob => ob.y < canvas.height);

    score++;
}

function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // Draw player
    ctx.fillStyle = "blue";
    ctx.fillRect(player.x, player.y, player.width, player.height);

    // Draw obstacles
    ctx.fillStyle = "red";
    obstacles.forEach(ob => {
        ctx.fillRect(ob.x, ob.y, ob.width, ob.height);
    });

    // Draw score
    ctx.fillStyle = "white";
    ctx.font = "20px Arial";
    ctx.fillText("Score: " + score, 20, 30);

    if (gameOver) {
        ctx.fillStyle = "white";
        ctx.font = "50px Arial";
        ctx.fillText("GAME OVER", canvas.width / 2 - 150, canvas.height / 2);
    }
}

function gameLoop() {
    update();
    draw();
    requestAnimationFrame(gameLoop);
}

// Spawn obstacles every second
setInterval(spawnObstacle, 1000);

gameLoop();
</script>

</body>
</html>
