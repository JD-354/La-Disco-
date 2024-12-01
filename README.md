<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>🎨 Fiesta de Colores 🎉</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background-color: #f0f0f0;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            text-align: center;
        }

        .container {
            background-color: white;
            border-radius: 15px;
            padding: 30px;
            box-shadow: 0 10px 20px rgba(0,0,0,0.1);
            max-width: 600px;
            width: 100%;
        }

        #party-title {
            font-size: 2.5em;
            margin-bottom: 20px;
            transition: color 0.5s ease;
        }

        .color-palette {
            display: flex;
            justify-content: center;
            flex-wrap: wrap;
            gap: 10px;
            margin-bottom: 20px;
        }

        .color-btn {
            width: 50px;
            height: 50px;
            border-radius: 50%;
            border: none;
            cursor: pointer;
            transition: transform 0.2s;
        }

        .color-btn:hover {
            transform: scale(1.1);
        }

        #new-color-input {
            margin-right: 10px;
            padding: 5px;
        }

        #info-display {
            margin-top: 20px;
            font-weight: bold;
        }

        .final-results {
            background-color: #f4f4f4;
            border-radius: 10px;
            padding: 20px;
            margin-top: 20px;
        }

        .color-result {
            display: flex;
            align-items: center;
            justify-content: center;
            margin-bottom: 10px;
            padding: 10px;
            border-radius: 5px;
            font-size: 1.2em;
        }

        .inactive-warning {
            color: red;
            font-size: 0.8em;
            margin-top: 10px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 id="party-title">¡Bienvenidos a la Fiesta de Colores! 🎨</h1>
        
        <div>
            <input type="color" id="new-color-input">
            <button onclick="addNewColor()">Añadir Color</button>
            <button onclick="resetParty()">Reiniciar Fiesta</button>
            <button onclick="finishParty()">Finalizar Fiesta</button>
        </div>

        <div class="color-palette" id="color-palette"></div>

        <div id="info-display"></div>
        <div id="timer-display" class="inactive-warning"></div>
        <div id="final-results" class="final-results"></div>
    </div>

    <script>
        const initialColors = ['#FF6B6B', '#4ECDC4', '#45B7D1', '#FFA07A', '#98D8AA'];
        let colors = [...initialColors];
        const colorVotes = {};
        let inactivityTimer;
        let clickSound;

        function generateRandomColor() {
            const letters = '0123456789ABCDEF';
            let color = '#';
            for (let i = 0; i < 6; i++) {
                color += letters[Math.floor(Math.random() * 16)];
            }
            return color;
        }

        function initializeParty() {
            const palette = document.getElementById('color-palette');
            palette.innerHTML = '';
            
            colors.forEach(color => {
                const colorBtn = document.createElement('button');
                colorBtn.className = 'color-btn';
                colorBtn.style.backgroundColor = color;
                colorBtn.onclick = () => selectColor(color);
                palette.appendChild(colorBtn);
                colorVotes[color] = 0;
            });

            // Preparar sonido de clic
            clickSound = new Audio('https://www.soundjay.com/button/sounds/button-09.mp3');

            // Limpiar resultados finales
            document.getElementById('final-results').innerHTML = '';
        }

        function selectColor(color) {
            // Reproducir sonido
            if (clickSound) clickSound.play();

            // Cambiar título
            const title = document.getElementById('party-title');
            title.style.color = color;

            // Contar votos
            colorVotes[color]++;
            updateMostPopularColor();

            // Iniciar temporizador de 10 segundos SOLO al seleccionar un color
            startColorSelectionTimer();
        }

        function startColorSelectionTimer() {
            const timerDisplay = document.getElementById('timer-display');
            clearTimeout(inactivityTimer);
            timerDisplay.innerHTML = '';

            inactivityTimer = setTimeout(() => {
                timerDisplay.innerHTML = 'Tiempo de selección agotado. Reiniciando la fiesta...';
                setTimeout(resetParty, 2000);
            }, 10000);
        }

        function addNewColor() {
            const newColor = generateRandomColor();

            if (newColor && !colors.includes(newColor)) {
                colors.push(newColor);
                colorVotes[newColor] = 0;

                const palette = document.getElementById('color-palette');
                const colorBtn = document.createElement('button');
                colorBtn.className = 'color-btn';
                colorBtn.style.backgroundColor = newColor;
                colorBtn.onclick = () => selectColor(newColor);
                palette.appendChild(colorBtn);
            }
        }

        function updateMostPopularColor() {
            const infoDisplay = document.getElementById('info-display');
            const mostPopularColor = Object.keys(colorVotes).reduce((a, b) => 
                colorVotes[a] > colorVotes[b] ? a : b
            );

            infoDisplay.innerHTML = `Color más popular: 
                <span style="color:${mostPopularColor}">
                    ${mostPopularColor} (${colorVotes[mostPopularColor]} votos)
                </span>`;
        }

        function resetParty() {
            // Limpiar temporizador
            clearTimeout(inactivityTimer);
            const timerDisplay = document.getElementById('timer-display');
            timerDisplay.innerHTML = '';

            // Reiniciar votos
            Object.keys(colorVotes).forEach(color => {
                colorVotes[color] = 0;
            });

            const title = document.getElementById('party-title');
            title.style.color = 'black';

            const infoDisplay = document.getElementById('info-display');
            infoDisplay.innerHTML = '';

            const finalResults = document.getElementById('final-results');
            finalResults.innerHTML = '';

            colors = [...initialColors];
            initializeParty();
        }

        function finishParty() {
            // Limpiar temporizador
            clearTimeout(inactivityTimer);
            const timerDisplay = document.getElementById('timer-display');
            timerDisplay.innerHTML = '';

            // Encontrar el color más usado
            const mostPopularColor = Object.keys(colorVotes).reduce((a, b) => 
                colorVotes[a] > colorVotes[b] ? a : b
            );

            const finalResults = document.getElementById('final-results');
            finalResults.innerHTML = `
                <h3>🏆 Color Ganador de la Fiesta 🎉</h3>
                <div class="color-result" style="background-color:${mostPopularColor}; color:white;">
                    ${mostPopularColor} (${colorVotes[mostPopularColor]} votos)
                </div>
            `;

            // Reiniciar la fiesta automáticamente
            setTimeout(resetParty, 5000);
        }

        // Inicializar la fiesta al cargar
        document.addEventListener('DOMContentLoaded', () => {
            initializeParty();
        });
    </script>
</body>
</html>
