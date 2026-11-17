<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Mini Shooter</title>

<style>
*{
    margin:0;
    padding:0;
    box-sizing:border-box;
}

body{
    overflow:hidden;
    background:#000;
}

canvas{
    display:block;
    background:linear-gradient(#000010,#000000);
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
    x: canvas.width/2,
    y: canvas.height - 80,
    width: 40,
    height: 40,
    speed: 7
};

let bullets = [];
let enemies = [];
let keys = {};

let score = 0;
let lives = 3;
let gameOver = false;

let enemySpawnRate = 1000;

document.addEventListener("keydown", e => {

    keys[e.key] = true;

    if(e.code === "Space" && !gameOver){
        shoot();
    }

    if((e.key === "r" || e.key === "R") && gameOver){
        restartGame();
    }
});

document.addEventListener("keyup", e => {
    keys[e.key] = false;
});

document.addEventListener("click", () => {
    if(!gameOver){
        shoot();
    }
});

function shoot(){
    bullets.push({
        x: player.x,
        y: player.y - 20,
        speed: 10
    });
}

function spawnEnemy(){

    enemies.push({
        x: Math.random() * (canvas.width - 40) + 20,
        y: -30,
        radius: 20,
        speed: 2 + Math.random() * 3
    });
}

let enemySpawner = setInterval(spawnEnemy, enemySpawnRate);

function restartGame(){

    score = 0;
    lives = 3;
    gameOver = false;

    bullets = [];
    enemies = [];

    player.x = canvas.width/2;

    enemySpawnRate = 1000;

    clearInterval(enemySpawner);
    enemySpawner = setInterval(spawnEnemy, enemySpawnRate);
}

function update(){

    if(gameOver) return;

    if(keys["a"] || keys["A"] || keys["ArrowLeft"]){
        player.x -= player.speed;
    }

    if(keys["d"] || keys["D"] || keys["ArrowRight"]){
        player.x += player.speed;
    }

    if(player.x < 20) player.x = 20;
    if(player.x > canvas.width - 20) player.x = canvas.width - 20;

    bullets.forEach((bullet,index)=>{

        bullet.y -= bullet.speed;

        if(bullet.y < -20){
            bullets.splice(index,1);
        }
    });

    enemies.forEach((enemy,eIndex)=>{

        enemy.y += enemy.speed;

        bullets.forEach((bullet,bIndex)=>{

            const dx = enemy.x - bullet.x;
            const dy = enemy.y - bullet.y;
            const distance = Math.sqrt(dx*dx + dy*dy);

            if(distance < enemy.radius){

                enemies.splice(eIndex,1);
                bullets.splice(bIndex,1);

                score += 10;

                if(score % 100 === 0){

                    clearInterval(enemySpawner);

                    enemySpawnRate = Math.max(
                        300,
                        enemySpawnRate - 100
                    );

                    enemySpawner =
                        setInterval(
                            spawnEnemy,
                            enemySpawnRate
                        );
                }
            }
        });

        if(enemy.y > canvas.height){

            enemies.splice(eIndex,1);

            lives--;

            if(lives <= 0){
                gameOver = true;
            }
        }
    });
}

function drawPlayer(){

    ctx.fillStyle = "#00ff00";

    ctx.beginPath();
    ctx.moveTo(player.x, player.y - 25);
    ctx.lineTo(player.x - 20, player.y + 20);
    ctx.lineTo(player.x + 20, player.y + 20);
    ctx.closePath();
    ctx.fill();
}

function drawBullets(){

    ctx.fillStyle = "#ffff00";

    bullets.forEach(bullet=>{

        ctx.fillRect(
            bullet.x - 2,
            bullet.y - 10,
            4,
            12
        );
    });
}

function drawEnemies(){

    ctx.fillStyle = "#ff3333";

    enemies.forEach(enemy=>{

        ctx.beginPath();

        ctx.arc(
            enemy.x,
            enemy.y,
            enemy.radius,
            0,
            Math.PI*2
        );

        ctx.fill();
    });
}

function drawHUD(){

    ctx.fillStyle = "white";
    ctx.font = "24px Arial";

    ctx.fillText(
        "Puntuación: " + score,
        20,
        40
    );

    ctx.fillText(
        "Vidas: " + lives,
        20,
        75
    );
}

function drawGameOver(){

    if(!gameOver) return;

    ctx.fillStyle = "rgba(0,0,0,0.7)";
    ctx.fillRect(
        0,
        0,
        canvas.width,
        canvas.height
    );

    ctx.textAlign = "center";

    ctx.fillStyle = "red";
    ctx.font = "70px Arial";
    ctx.fillText(
        "GAME OVER",
        canvas.width/2,
        canvas.height/2 - 40
    );

    ctx.fillStyle = "white";
    ctx.font = "35px Arial";
    ctx.fillText(
        "Puntuación: " + score,
        canvas.width/2,
        canvas.height/2 + 30
    );

    ctx.font = "25px Arial";
    ctx.fillText(
        "Pulsa R para reiniciar",
        canvas.width/2,
        canvas.height/2 + 90
    );

    ctx.textAlign = "left";
}

function draw(){

    ctx.clearRect(
        0,
        0,
        canvas.width,
        canvas.height
    );

    drawPlayer();
    drawBullets();
    drawEnemies();
    drawHUD();
    drawGameOver();
}

function gameLoop(){

    update();
    draw();

    requestAnimationFrame(gameLoop);
}

gameLoop();

window.addEventListener("resize", ()=>{

    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
});
</script>

</body>
</html>
