# simple-game
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simple Catching Game</title>
    <style>
        body {
            margin: 0;
            background: #222;
            color: white;
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            overflow: hidden;
        }
        #score-board {
            font-size: 24px;
            margin-bottom: 10px;
        }
        #game-container {
            width: 400px;
            height: 500px;
            background: #333;
            position: relative;
            border: 4px solid #fff;
            border-radius: 8px;
            overflow: hidden;
        }
        #basket {
            width: 80px;
            height: 20px;
            background: #00ffcc;
            position: absolute;
            bottom: 10px;
            left: 160px;
            border-radius: 5px;
        }
        #falling-object {
            width: 20px;
            height: 20px;
            background: #ff4444;
            position: absolute;
            top: 0;
            left: 190px;
            border-radius: 50%;
        }
        #game-over-screen {
            display: none;
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.85);
            flex-direction: column;
            align-items: center;
            justify-content: center;
        }
        button {
            padding: 10px 20px;
            font-size: 18px;
            background: #00ffcc;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-weight: bold;
        }
        button:hover {
            background: #00b38f;
        }
    </style>
</head>
<body>

    <div id="score-board">Score: <span id="score">0</span></div>
    
    <div id="game-container">
        <div id="basket"></div>
        <div id="falling-object"></div>
        
        <div id="game-over-screen">
            <h2>Game Over!</h2>
            <p>Final Score: <span id="final-score">0</span></p>
            <button onclick="resetGame()">Play Again</button>
        </div>
    </div>

    <p style="color: #aaa; margin-top: 15px;">Use Left & Right Arrow keys or Move Mouse to steer</p>

    <script>
        const container = document.getElementById('game-container');
        const basket = document.getElementById('basket');
        const fallingObject = document.getElementById('falling-object');
        const scoreDisplay = document.getElementById('score');
        const gameOverScreen = document.getElementById('game-over-screen');
        const finalScoreDisplay = document.getElementById('final-score');

        let score = 0;
        let basketX = 160;
        let objectX = 190;
        let objectY = 0;
        let speed = 4;
        let gameActive = true;

        // Mouse/Touch movement
        container.addEventListener('mousemove', (e) => {
            if (!gameActive) return;
            const rect = container.getBoundingClientRect();
            basketX = e.clientX - rect.left - 40; // Center basket on cursor
            keepBasketInBounds();
        });

        // Keyboard movement
        document.addEventListener('keydown', (e) => {
            if (!gameActive) return;
            if (e.key === 'ArrowLeft') {
                basketX -= 25;
            } else if (e.key === 'ArrowRight') {
                basketX += 25;
            }
            keepBasketInBounds();
        });

        function keepBasketInBounds() {
            if (basketX < 0) basketX = 0;
            if (basketX > 320) basketX = 320; // 400 container width - 80 basket width
            basket.style.left = basketX + 'px';
        }

        function spawnObject() {
            objectY = 0;
            objectX = Math.floor(Math.random() * 380); // Random position within width
            fallingObject.style.left = objectX + 'px';
        }

        function updateGame() {
            if (!gameActive) return;

            // Move object down
            objectY += speed;
            fallingObject.style.top = objectY + 'px';

            // Check for catch (Collision detection)
            if (objectY >= 470 && objectY <= 490) { // Basket vertical level
                if (objectX + 20 >= basketX && objectX <= basketX + 80) {
                    score++;
                    scoreDisplay.innerText = score;
                    speed += 0.3; // Make it progressively faster
                    spawnObject();
                }
            }

            // Check if object fell past the basket (Game Over)
            if (objectY > 500) {
                gameActive = false;
                finalScoreDisplay.innerText = score;
                gameOverScreen.style.display = 'flex';
            }

            requestAnimationFrame(updateGame);
        }

        function resetGame() {
            score = 0;
            speed = 4;
            basketX = 160;
            gameActive = true;
            scoreDisplay.innerText = score;
            gameOverScreen.style.display = 'none';
            basket.style.left = basketX + 'px';
            spawnObject();
            updateGame();
        }

        // Initialize game
        spawnObject();
        updateGame();
    </script>
</body>
</html>
