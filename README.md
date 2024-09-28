<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cube Fighter Game</title>
    <style>
        body {
            display: flex;
            flex-direction: column;
            align-items: center;
            font-family: Arial, sans-serif;
            margin: 0;
            background-color: #f0f0f0;
        }
        #game {
            width: 600px;
            height: 400px;
            background-color: #ffffff;
            border: 1px solid #000;
            position: relative;
            overflow: hidden;
        }
        .cube {
            width: 30px;
            height: 30px;
            position: absolute;
            background-color: blue;
            transition: left 0.1s, top 0.1s;
        }
        #shop, #factory, #menu {
            margin: 10px;
            padding: 10px;
            background-color: #ccc;
            border: 1px solid #000;
            cursor: pointer;
        }
        #moneyDisplay {
            position: absolute;
            top: 10px;
            left: 50%;
            transform: translateX(-50%);
            font-size: 20px;
        }
    </style>
</head>
<body>
    <div id="game">
        <div id="moneyDisplay">Money: $0</div>
        <div class="cube" id="playerCube" style="background-color: green;"></div>
    </div>
    <div id="shop">Shop</div>
    <div id="factory">Go to Factory</div>
    <div id="menu">Menu</div>

    <script>
        let money = 0;
        const playerCube = document.getElementById("playerCube");
        const moneyDisplay = document.getElementById("moneyDisplay");
        const gameArea = document.getElementById("game");
        
        // Function to move the player cube
        document.addEventListener('keydown', (event) => {
            const step = 5;
            let rect = playerCube.getBoundingClientRect();
            if (event.key === 'ArrowUp' && rect.top > 0) {
                playerCube.style.top = `${rect.top - step}px`;
            } else if (event.key === 'ArrowDown' && rect.bottom < gameArea.clientHeight) {
                playerCube.style.top = `${rect.top + step}px`;
            } else if (event.key === 'ArrowLeft' && rect.left > 0) {
                playerCube.style.left = `${rect.left - step}px`;
            } else if (event.key === 'ArrowRight' && rect.right < gameArea.clientWidth) {
                playerCube.style.left = `${rect.left + step}px`;
            }
            checkCollision();
        });

        // Function to check for collisions with other cubes
        function checkCollision() {
            const enemies = document.querySelectorAll('.enemyCube');
            enemies.forEach(enemy => {
                const enemyRect = enemy.getBoundingClientRect();
                const playerRect = playerCube.getBoundingClientRect();
                if (
                    playerRect.x < enemyRect.x + enemyRect.width &&
                    playerRect.x + playerRect.width > enemyRect.x &&
                    playerRect.y < enemyRect.y + enemyRect.height &&
                    playerRect.y + playerRect.height > enemyRect.y
                ) {
                    // Collision detected
                    money += 10; // Example reward for defeating an enemy
                    moneyDisplay.innerText = `Money: $${money}`;
                    enemy.remove(); // Remove the defeated enemy cube
                }
            });
        }

        // Function to spawn enemy cubes
        function spawnEnemy() {
            const enemyCube = document.createElement("div");
            enemyCube.classList.add("cube", "enemyCube");
            enemyCube.style.backgroundColor = "red";
            enemyCube.style.width = "30px";
            enemyCube.style.height = "30px";
            enemyCube.style.position = "absolute";
            enemyCube.style.left = `${Math.random() * (gameArea.clientWidth - 30)}px`;
            enemyCube.style.top = `${Math.random() * (gameArea.clientHeight - 30)}px`;
            gameArea.appendChild(enemyCube);
        }

        // Function to simulate factory income
        function goToFactory() {
            const tax = 5;
            const bill = 10;

            // Simple factory logic
            if (money >= tax + bill) {
                money -= (tax + bill);
                moneyDisplay.innerText = `Money: $${money}`;
                const factoryIncome = 15; // Example income from factory
                money += factoryIncome;
                moneyDisplay.innerText = `Money: $${money}`;
            } else {
                alert("You don't have enough money for taxes and bills!");
            }
        }

        // Function to save progress
        function saveProgress() {
            localStorage.setItem('money', money);
            alert("Progress saved!");
        }

        // Event listeners for shop and factory buttons
        document.getElementById("shop").addEventListener("click", () => {
            alert("Welcome to the shop! You have $"+ money);
            // Add shop functionalities here
        });

        document.getElementById("factory").addEventListener("click", goToFactory);
        document.getElementById("menu").addEventListener("click", () => {
            alert("Menu: Select Difficulty");
            // Add difficulty selection logic here
        });

        // Spawn enemies periodically
        setInterval(spawnEnemy, 2000);

        // Load progress
        window.onload = () => {
            money = parseInt(localStorage.getItem('money')) || 0;
            moneyDisplay.innerText = `Money: $${money}`;
        };

        // Save progress on beforeunload
        window.onbeforeunload = saveProgress;
    </script>
</body>
</html>
