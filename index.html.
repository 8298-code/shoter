<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Carrera Arcade</title>

<style>
*{
    margin:0;
    padding:0;
    box-sizing:border-box;
}

body{
    display:flex;
    justify-content:center;
    align-items:center;
    height:100vh;
    background:#1a1a1a;
    font-family:Arial,sans-serif;
}

#game{
    position:relative;
    width:400px;
    height:700px;
    background:#444;
    overflow:hidden;
    border:5px solid white;
    box-shadow:0 0 30px rgba(255,255,255,0.3);
}

/* Césped */
#game::before,
#game::after{
    content:"";
    position:absolute;
    top:0;
    width:50px;
    height:100%;
    background:#2d8a34;
}

#game::before{
    left:0;
}

#game::after{
    right:0;
}

/* Carretera */
.road-line{
    position:absolute;
    left:195px;
    width:10px;
    height:80px;
    background:white;
}

/* Marcador */
#score{
    position:absolute;
    top:10px;
    left:10px;
    color:white;
    font-size:28px;
    font-weight:bold;
    z-index:100;
}

/* COCHES */
.car{
    position:absolute;
    width:50px;
    height:80px;
}

/* Jugador */
#player{
    bottom:25px;
    left:175px;
}

/* Enemigos */
.enemy{
    top:-100px;
}

/* Carrocería */
.body{
    position:absolute;
    width:100%;
    height:100%;
    border-radius:12px;
}

/* Jugador rojo */
#player .body{
    background:linear-gradient(to bottom,#ff6666,#b40000);
    box-shadow:0 0 15px rgba(255,0,0,.7);
}

/* Enemigo azul */
.enemy .body{
    background:linear-gradient(to bottom,#6ba9ff,#0047c2);
    box-shadow:0 0 10px rgba(0,100,255,.5);
}

/* Ventanas */
.window{
    position:absolute;
    top:12px;
    left:10px;
    width:30px;
    height:20px;
    background:#9fe7ff;
    border-radius:4px;
}

/* Luneta trasera */
.rear-window{
    position:absolute;
    bottom:15px;
    left:12px;
    width:26px;
    height:12px;
    background:#9fe7ff;
    border-radius:4px;
}

/* Ruedas */
.wheel{
    position:absolute;
    width:8px;
    height:18px;
    background:#111;
    border-radius:3px;
}

.lt{left:-4px;top:10px;}
.rt{right:-4px;top:10px;}
.lb{left:-4px;bottom:10px;}
.rb{right:-4px;bottom:10px;}

/* Faros */
.headlight{
    position:absolute;
    width:8px;
    height:8px;
    background:#ffffcc;
    border-radius:50%;
    top:4px;
}

.hl{left:6px;}
.hr{right:6px;}

/* Pilotos traseros */
.taillight{
    position:absolute;
    width:8px;
    height:8px;
    background:red;
    border-radius:50%;
    bottom:4px;
}

.tl{left:6px;}
.tr{right:6px;}

#message{
    position:absolute;
    top:50%;
    left:50%;
    transform:translate(-50%,-50%);
    color:white;
    font-size:42px;
    font-weight:bold;
    display:none;
    z-index:200;
}
</style>
</head>
<body>

<div id="game">
    <div id="score">0</div>
    <div id="message">GAME OVER</div>

    <div id="player" class="car">
        <div class="body"></div>
        <div class="window"></div>
        <div class="rear-window"></div>

        <div class="wheel lt"></div>
        <div class="wheel rt"></div>
        <div class="wheel lb"></div>
        <div class="wheel rb"></div>

        <div class="headlight hl"></div>
        <div class="headlight hr"></div>

        <div class="taillight tl"></div>
        <div class="taillight tr"></div>
    </div>
</div>

<script>

const game = document.getElementById("game");
const player = document.getElementById("player");
const scoreText = document.getElementById("score");
const message = document.getElementById("message");

let playerX = 175;
let score = 0;
let speed = 6;
let gameOver = false;

/* Líneas de carretera */

for(let i=0;i<8;i++){
    const line = document.createElement("div");
    line.className="road-line";
    line.style.top=(i*100)+"px";
    game.appendChild(line);
}

/* Crear coche enemigo */

function createEnemy(){

    const enemy = document.createElement("div");
    enemy.className="car enemy";

    enemy.innerHTML=`
        <div class="body"></div>
        <div class="window"></div>
        <div class="rear-window"></div>

        <div class="wheel lt"></div>
        <div class="wheel rt"></div>
        <div class="wheel lb"></div>
        <div class="wheel rb"></div>

        <div class="headlight hl"></div>
        <div class="headlight hr"></div>

        <div class="taillight tl"></div>
        <div class="taillight tr"></div>
    `;

    const lanes=[75,125,175,225,275];
    enemy.style.left=
        lanes[Math.floor(Math.random()*lanes.length)]+"px";

    enemy.style.top="-100px";

    game.appendChild(enemy);

    return enemy;
}

const enemy=createEnemy();

document.addEventListener("keydown",(e)=>{

    if(gameOver) return;

    if(e.key==="ArrowLeft"){
        playerX=Math.max(75,playerX-50);
    }

    if(e.key==="ArrowRight"){
        playerX=Math.min(275,playerX+50);
    }

    player.style.left=playerX+"px";
});

function update(){

    if(gameOver) return;

    document.querySelectorAll(".road-line").forEach(line=>{

        let y=parseInt(line.style.top);

        y+=speed;

        if(y>700){
            y=-80;
        }

        line.style.top=y+"px";
    });

    let enemyY=parseInt(enemy.style.top);

    enemyY+=speed+2;

    if(enemyY>750){

        enemyY=-100;

        const lanes=[75,125,175,225,275];

        enemy.style.left=
            lanes[Math.floor(Math.random()*lanes.length)]+"px";

        score++;
        scoreText.textContent=score;

        if(score%5===0){
            speed+=0.5;
        }
    }

    enemy.style.top=enemyY+"px";

    const p=player.getBoundingClientRect();
    const e=enemy.getBoundingClientRect();

    if(
        p.left<e.right &&
        p.right>e.left &&
        p.top<e.bottom &&
        p.bottom>e.top
    ){
        gameOver=true;
        message.style.display="block";
    }

    requestAnimationFrame(update);
}

update();

</script>

</body>
</html>
