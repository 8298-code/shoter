<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Shooter Básico</title>
<style>
body {
    margin: 0;
    overflow: hidden;
    background: black;
}
canvas {
    display: block;
}
</style>
</head>
<body>
<canvas id="game"></canvas>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

canvas.width = window.innerWidth;
canvas.height = window.innerHeight;

const player = {
    x: canvas.width / 2,
    y: canvas.height - 80,
    size: 30,
    speed: 6
};

let bullets = [];
let enemies = [];
let keys = {};

document.addEventListener("keydown", e => keys[e.key] = true);
document.addEventListener("keyup", e => keys[e.key] = false);

document.addEventListener("click", () => {
    bullets.push({
        x: player.x,
        y: player.y,
        speed: 8
    });
});

function spawnEnemy() {
    enemies.push({
        x: Math.random() * canvas.width,
        y: -20,
        size: 20,
        speed: 2 + Math.random() * 2
    });
}

setInterval(spawnEnemy, 1000);

function update() {

    if (keys["a"] || keys["ArrowLeft"])
        player.x -= player.speed;

    if (keys["d"] || keys["ArrowRight"])
        player.x += player.speed;

    bullets.forEach((b, i) => {
        b.y -= b.speed;
        if (b.y < 0) bullets.splice(i, 1);
    });

    enemies.forEach((e, ei) => {
        e.y += e.speed;

        bullets.forEach((b, bi) => {
            const dx = e.x - b.x;
            const dy = e.y - b.y;
            const dist = Math.sqrt(dx*dx + dy*dy);

            if (dist < e.size) {
                enemies.splice(ei, 1);
                bullets.splice(bi, 1);
            }
        });

        if (e.y > canvas.height)
            enemies.splice(ei, 1);
    });
}

function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    ctx.fillStyle = "lime";
    ctx.fillRect(
        player.x - player.size/2,
        player.y - player.size/2,
        player.size,
        player.size
    );

    ctx.fillStyle = "yellow";
    bullets.forEach(b => {
        ctx.fillRect(b.x - 2, b.y, 4, 10);
    });

    ctx.fillStyle = "red";
    enemies.forEach(e => {
        ctx.beginPath();
        ctx.arc(e.x, e.y, e.size, 0, Math.PI * 2);
        ctx.fill();
    });
}

function gameLoop() {
    update();
    draw();
    requestAnimationFrame(gameLoop);
}

gameLoop();
</script>
</body>
</html>
