<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>O & X Game</title>

<link rel="stylesheet" href="style.css">
</head>

<body>

<!-- INTRO -->
<div id="intro">
    <h1>O & X Game</h1>
    <button onclick="startGame()">Start Game</button>
</div>

<!-- GAME -->
<div id="game">
    <h2>Tic Tac Toe</h2>
    <div class="board" id="board"></div>
    <div id="status">Player X's Turn</div>
</div>

<script src="script.js"></script>
</body>
</html>
body {
    margin: 0;
    font-family: Arial;
    background: linear-gradient(135deg, #1e1e2f, #2b2b45);
    color: white;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}

/* Intro Screen */
#intro {
    position: absolute;
    width: 100%;
    height: 100%;
    background: rgba(0,0,0,0.9);
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}

button {
    padding: 12px 25px;
    font-size: 18px;
    border: none;
    background: #00c2ff;
    color: white;
    border-radius: 8px;
    cursor: pointer;
}

/* Game */
#game {
    display: none;
    text-align: center;
}

.board {
    display: grid;
    grid-template-columns: repeat(3, 100px);
    gap: 8px;
    margin-top: 20px;
}

.cell {
    width: 100px;
    height: 100px;
    background: #ffffff10;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 40px;
    cursor: pointer;
    border-radius: 10px;
}

.cell:hover {
    background: #ffffff25;
}

#status {
    margin-top: 15px;
    font-size: 20px;
}
let currentPlayer = "X";
let cells = Array(9).fill(null);

function startGame() {
    document.getElementById("intro").style.display = "none";
    document.getElementById("game").style.display = "block";
    createBoard();
}

function createBoard() {
    const board = document.getElementById("board");
    board.innerHTML = "";

    cells.forEach((_, i) => {
        const cell = document.createElement("div");
        cell.classList.add("cell");
        cell.addEventListener("click", () => makeMove(cell, i));
        board.appendChild(cell);
    });
}

function playSound() {
    const ctx = new (window.AudioContext || window.webkitAudioContext)();
    const osc = ctx.createOscillator();
    const gain = ctx.createGain();

    osc.type = "square";
    osc.frequency.value = 600;
    gain.gain.value = 0.1;

    osc.connect(gain);
    gain.connect(ctx.destination);

    osc.start();
    osc.stop(ctx.currentTime + 0.1);
}

function restartGame(message) {
    setTimeout(() => {
        alert(message);
        cells = Array(9).fill(null);
        currentPlayer = "X";
        document.getElementById("status").textContent = "Player X's Turn";
        createBoard();
    }, 500);
}

function makeMove(cell, index) {
    if (cells[index]) return;

    cells[index] = currentPlayer;
    cell.textContent = currentPlayer;

    playSound();

    if (checkWinner()) {
        document.getElementById("status").textContent = currentPlayer + " Wins!";
        restartGame(currentPlayer + " Wins!");
        return;
    }

    if (cells.every(c => c !== null)) {
        document.getElementById("status").textContent = "Draw!";
        restartGame("It's a Draw!");
        return;
    }

    currentPlayer = currentPlayer === "X" ? "O" : "X";
    document.getElementById("status").textContent =
        "Player " + currentPlayer + "'s Turn";
}

function checkWinner() {
    const win = [
        [0,1,2],[3,4,5],[6,7,8],
        [0,3,6],[1,4,7],[2,5,8],
        [0,4,8],[2,4,6]
    ];

    return win.some(p => {
        const [a,b,c] = p;
        return cells[a] && cells[a] === cells[b] && cells[a] === cells[c];
    });
}
