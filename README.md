<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cube Fighter Game</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background-color: #f0f0f0;
        }

        #game-container {
            position: relative;
            width: 100vw;
            height: 100vh;
        }

        #shop, #factory, #menu {
            position: absolute;
            background: white;
            border: 1px solid #ccc;
            padding: 10px;
            box-shadow: 2px 2px 10px rgba(0,0,0,0.5);
        }

        #shop {
            top: 10px;
            left: 10px;
        }

        #factory {
            top: 10px;
            right: 10px;
        }

        #menu {
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
        }

        #game-canvas {
            border: 1px solid black;
            background-color: #e0e0e0;
        }

        #game-over {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 32px;
            color: red;
            display: none;
        }

        #shop-menu, #factory-menu {
            display: none;
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: white;
            border: 1px solid #ccc;
            padding: 20px;
            box-shadow: 2px 2px 10px rgba(0,0,0,0.5);
        }
    </style>
</head>
<body>
    <div id="game-container">
        <div id="shop">
            <div id="money-display">Money: $0</div>
            <button id="shop-button">Shop</button>
        </div>
        <div id="factory">
            <button id="factory-button">Go to Factory</button>
        </div>
        <div id="menu">
            <select id="difficulty-select">
                <option value="normal">Normal</option>
                <option value="hardcore">Hardcore</option>
            </select>
            <button id="start-button">Start Game</button>
        </div>
        <div id="game-over">Game Over</div>
        <canvas id="game-canvas"></canvas>

        <!-- Shop Menu -->
        <div id="shop-menu">
            <h2>Shop</h2>
            <button id="buy-attack-upgrade">Buy Attack Upgrade ($50)</button>
            <button id="close-shop">Close Shop</button>
        </div>

        <!-- Factory Menu -->
        <div id="factory-menu">
            <h2>Factory</h2>
            <button id="start-factory">Start Factory ($20)</button>
            <button id="close-factory">Close Factory</button>
        </div>
    </div>
    <script>
        const canvas = document.getElementById('game-canvas');
        const ctx = canvas.getContext('2d');
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        let player = { x: canvas.width / 2, y: canvas.height / 2, size: 20, money: 0, attackUpgrade: 0 };
        let enemies = [];
        let isGameActive = false;
        let difficulty = 'normal';
        let taxes = 0;
        let bills = 0;
        let factoryActive = false;
        let factoryInterval;

        document.getElementById('start-button').onclick = function() {
            isGameActive = true;
            enemies = [];
            player.money = 0;
            player.attackUpgrade = 0;
            spawnEnemies();
            gameLoop();
            document.getElementById('game-over').style.display = 'none'; // Hide game over message
        };

        document.getElementById('shop-button').onclick = function() {
            document.getElementById('shop-menu').style.display = 'block';
        };

        document.getElementById('factory-button').onclick = function() {
            document.getElementById('factory-menu').style.display = 'block';
        };

        document.getElementById('close-shop').onclick = function() {
            document.getElementById('shop-menu').style.display = 'none';
        };

        document.getElementById('close-factory').onclick = function() {
            document.getElementById('factory-menu').style.display = 'none';
            if (factoryActive) {
                clearInterval(factoryInterval);
                factoryActive = false;
            }
        };

        document.getElementById('buy-attack-upgrade').onclick = function() {
            if (player.money >= 50) {
                player.money -= 50;
                player.attackUpgrade++;
                document.getElementById('money-display').innerText = 'Money: $' + player.money;
                alert('Attack upgraded! Level: ' + player.attackUpgrade);
            } else {
                alert('Not enough money!');
            }
        };

        document.getElementById('start-factory').onclick = function() {
            if (player.money >= 20 && !factoryActive) {
                player.money -= 20;
                factoryActive = true;
                document.getElementById('money-display').innerText = 'Money: $' + player.money;
                factoryInterval = setInterval(() => {
                    player.money += 5; // Earn $5 every second in factory
                    document.getElementById('money-display').innerText = 'Money: $' + player.money;
                }, 1000);
            } else {
                alert(factoryActive ? 'Factory already running!' : 'Not enough money!');
            }
        };

        function spawnEnemies() {
            for (let i = 0; i < 5; i++) {
                enemies.push({ x: Math.random() * canvas.width, y: Math.random() * canvas.height, size: 20 });
            }
        }

        function gameLoop() {
            if (!isGameActive) return;

            ctx.clearRect(0, 0, canvas.width, canvas.height);
            drawPlayer();
            drawEnemies();
            updateGame();
            requestAnimationFrame(gameLoop);
        }

        function drawPlayer() {
            ctx.fillStyle = 'blue';
            ctx.fillRect(player.x, player.y, player.size, player.size);
        }

        function drawEnemies() {
            ctx.fillStyle = 'red';
            enemies.forEach(enemy => {
                ctx.fillRect(enemy.x, enemy.y, enemy.size, enemy.size);
            });
        }

        function updateGame() {
            // Simulate fighting
            if (Math.random() < 0.01) { 
                player.money += 10 + (player.attackUpgrade * 2); // More money with attack upgrade
                document.getElementById('money-display').innerText = 'Money: $' + player.money;
            }

            // Taxes and bills logic
            if (player.money > 0) {
                taxes += 0.1; // Example tax logic
                bills += 0.05; // Example bills logic
                if (player.money < taxes + bills) {
                    alert('You lost due to unpaid taxes/bills!');
                    isGameActive = false;
                    document.getElementById('game-over').style.display = 'block';
                    clearInterval(factoryInterval);
                }
            } else {
                alert('You lost all your money!');
                isGameActive = false;
                document.getElementById('game-over').style.display = 'block';
                clearInterval(factoryInterval);
            }
        }

        // Control player movement
        window.addEventListener('keydown', function(e) {
            if (isGameActive) {
                if (e.key === 'ArrowUp') player.y -= 5;
                if (e.key === 'ArrowDown') player.y += 5;
                if (e.key === 'ArrowLeft') player.x -= 5;
                if (e.key === 'ArrowRight') player.x += 5;
            }
        });

        // Functionality for saving progress would go here
    </script>
</body>
</html>
