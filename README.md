<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RENAN.BET</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #1a1a1a;
            color: white;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
        }

        /* Abas */
        .tabs {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
        }

        .tab-button {
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
            background-color: #333;
            color: white;
            border: none;
            border-radius: 5px;
            transition: background-color 0.3s;
        }

        .tab-button.active {
            background-color: #4CAF50;
        }

        .tab-content {
            display: none;
        }

        .tab-content.active {
            display: block;
        }

        /* Estilos do Slot Machine */
        .slot-machine {
            display: flex;
            justify-content: center;
            margin: 20px 0;
        }

        .reel {
            width: 100px;
            height: 100px;
            border: 3px solid #4CAF50;
            margin: 0 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 50px;
            background-color: #333;
            border-radius: 10px;
        }

        /* Estilos do Mines */
        .mines-grid {
            display: grid;
            grid-template-columns: repeat(5, 60px);
            gap: 10px;
            margin: 20px 0;
        }

        .mine-cell {
            width: 60px;
            height: 60px;
            display: flex;
            align-items: center;
            justify-content: center;
            background-color: #333;
            border: 2px solid #4CAF50;
            border-radius: 5px;
            cursor: pointer;
            font-size: 20px;
            transition: background-color 0.3s;
        }

        .mine-cell.revealed {
            background-color: #444;
            cursor: default;
        }

        .mine-cell.bomb {
            background-color: #ff4444;
        }

        .mine-cell.safe {
            background-color: #4CAF50;
        }

        /* Estilos comuns */
        .controls {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 15px;
        }

        .button {
            padding: 12px 30px;
            font-size: 18px;
            cursor: pointer;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 25px;
            transition: all 0.3s;
        }

        .button:hover {
            background-color: #45a049;
            transform: scale(1.05);
        }

        .button:disabled {
            background-color: #666;
            cursor: not-allowed;
        }

        .saldo {
            font-size: 24px;
            margin-bottom: 20px;
            color: #4CAF50;
        }

        input[type="number"] {
            padding: 10px;
            font-size: 16px;
            width: 120px;
            text-align: center;
            border: 2px solid #4CAF50;
            border-radius: 5px;
            background-color: #333;
            color: white;
        }

        .resultado {
            margin-top: 20px;
            font-size: 18px;
            min-height: 25px;
        }

        /* Área de texto para o comando */
        .comando-area {
            margin-top: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 10px;
        }

        .comando-area input {
            padding: 10px;
            font-size: 16px;
            width: 200px;
            text-align: center;
            border: 2px solid #4CAF50;
            border-radius: 5px;
            background-color: #333;
            color: white;
        }

        .comando-area button {
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
            transition: background-color 0.3s;
        }

        .comando-area button:hover {
            background-color: #45a049;
        }
    </style>
</head>
<body>
    <h1>🎰 RENAN.BET 🎰</h1>
    <div class="saldo" id="saldo">Saldo: UR$<span id="valorSaldo">100</span></div>

    <!-- Abas -->
    <div class="tabs">
        <button class="tab-button active" onclick="mudarAba('slot')">Slot Machine</button>
        <button class="tab-button" onclick="mudarAba('mines')">Mines</button>
    </div>

    <!-- Conteúdo do Slot Machine -->
    <div id="slot-tab" class="tab-content active">
        <div class="controls">
            <input type="number" id="aposta" min="1" max="100" value="1" placeholder="Valor da aposta">
            <button class="button" onclick="rolar()">Girar Roletas!</button>
        </div>
        
        <div class="slot-machine">
            <div class="reel" id="reel1">🦁</div>
            <div class="reel" id="reel2">🐯</div>
            <div class="reel" id="reel3">🐻</div>
        </div>
        
        <div class="resultado" id="resultado-slot"></div>
    </div>

    <!-- Conteúdo do Mines -->
    <div id="mines-tab" class="tab-content">
        <div class="controls">
            <input type="number" id="aposta-mines" min="1" max="100" value="1" placeholder="Valor da aposta">
            <input type="number" id="num-bombas" min="1" max="24" value="5" placeholder="Número de bombas">
            <button class="button" onclick="iniciarMines()">Iniciar Jogo</button>
            <button class="button" id="parar-button" onclick="pararMines()" disabled>Parar e Sacar</button>
        </div>
        
        <div class="mines-grid" id="mines-grid"></div>
        
        <div class="resultado" id="resultado-mines"></div>

        <!-- Área de texto para o comando -->
        <div class="comando-area">
            <input type="text" id="comando-input" placeholder="Digite o comando">
            <button onclick="executarComando()">Executar</button>
        </div>
    </div>

    <script>
        let saldo = 100;
        const animais = ["🦁", "🐯", "🐻", "🦊", "🐺", "🦄"];
        let minesGame = null;

        // Sistema de abas
        function mudarAba(aba) {
            document.querySelectorAll('.tab-content').forEach(tab => tab.classList.remove('active'));
            document.querySelectorAll('.tab-button').forEach(btn => btn.classList.remove('active'));
            document.getElementById(`${aba}-tab`).classList.add('active');
            document.querySelector(`button[onclick="mudarAba('${aba}')"]`).classList.add('active');
        }

        // Atualizar saldo
        function atualizarSaldo() {
            document.getElementById('valorSaldo').textContent = saldo;
            document.getElementById('aposta').max = saldo;
            document.getElementById('aposta-mines').max = saldo;
        }

        // Slot Machine
        function gerarMultiplicador() {
            return (Math.random() * 0.5) + 1.5; // Entre 1.5x e 2x
        }

        function rolar() {
            const aposta = parseInt(document.getElementById('aposta').value);
            const resultadoDiv = document.getElementById('resultado-slot');
            const button = document.querySelector('#slot-tab .button');
            
            if(aposta < 1 || aposta > saldo) {
                resultadoDiv.textContent = "Aposta inválida!";
                return;
            }

            button.disabled = true;
            saldo -= aposta;
            atualizarSaldo();
            
            // Girar roletas
            const reels = document.querySelectorAll('.reel');
            const resultados = [];
            
            reels.forEach(reel => {
                const randomAnimal = animais[Math.floor(Math.random() * animais.length)];
                reel.textContent = randomAnimal;
                resultados.push(randomAnimal);
            });

            // Verificar vitória
            setTimeout(() => {
                if(resultados[0] === resultados[1] && resultados[1] === resultados[2]) {
                    const multiplicador = gerarMultiplicador();
                    const ganho = Math.round(aposta * multiplicador);
                    saldo += ganho;
                    resultadoDiv.textContent = `🎉 Vitória! ${multiplicador.toFixed(1)}x → Ganhou UR$${ganho}!`;
                } else {
                    resultadoDiv.textContent = "❌ Tente novamente!";
                }
                atualizarSaldo();
                button.disabled = false;
            }, 100);
        }

        // Mines
        function iniciarMines() {
            const aposta = parseInt(document.getElementById('aposta-mines').value);
            const numBombas = parseInt(document.getElementById('num-bombas').value);
            if(aposta < 1 || aposta > saldo || numBombas < 1 || numBombas > 24) {
                document.getElementById('resultado-mines').textContent = "Configuração inválida!";
                return;
            }

            saldo -= aposta;
            atualizarSaldo();

            minesGame = {
                aposta: aposta,
                bombas: [],
                revelados: [],
                ganho: aposta,
                multiplicador: 1.0
            };

            // Gerar bombas aleatórias
            while(minesGame.bombas.length < numBombas) {
                const pos = Math.floor(Math.random() * 25);
                if(!minesGame.bombas.includes(pos)) minesGame.bombas.push(pos);
            }

            // Criar grid
            const grid = document.getElementById('mines-grid');
            grid.innerHTML = '';
            for(let i = 0; i < 25; i++) {
                const cell = document.createElement('div');
                cell.className = 'mine-cell';
                cell.addEventListener('click', () => revelarCelula(i));
                grid.appendChild(cell);
            }

            document.getElementById('resultado-mines').textContent = "Escolha uma célula segura!";
            document.getElementById('parar-button').disabled = false;
        }

        function revelarCelula(index) {
            if(!minesGame || minesGame.revelados.includes(index)) return;

            const cell = document.getElementById('mines-grid').children[index];
            cell.classList.add('revealed');

            if(minesGame.bombas.includes(index)) {
                cell.classList.add('bomb');
                cell.textContent = '💣';
                document.getElementById('resultado-mines').textContent = "💥 Você atingiu uma bomba!";
                minesGame = null;
                document.getElementById('parar-button').disabled = true;
            } else {
                cell.classList.add('safe');
                minesGame.multiplicador *= 10;
                minesGame.ganho = Math.round(minesGame.aposta * minesGame.multiplicador);
                minesGame.revelados.push(index);
                document.getElementById('resultado-mines').textContent = `✅ Seguro! Ganho atual: UR$${minesGame.ganho} (${minesGame.multiplicador.toFixed(1)}x)`;
            }
        }

        function pararMines() {
            if(!minesGame) return;

            saldo += minesGame.ganho;
            atualizarSaldo();
            document.getElementById('resultado-mines').textContent = `💰 Você sacou UR$${minesGame.ganho}!`;
            minesGame = null;
            document.getElementById('parar-button').disabled = true;
        }

        // Comando "verbomba"
        function executarComando() {
            const comando = document.getElementById('comando-input').value.trim().toLowerCase();
            if(comando === "verbomba" && minesGame) {
                minesGame.bombas.forEach(index => {
                    const cell = document.getElementById('mines-grid').children[index];
                    if(!cell.classList.contains('revealed')) {
                        cell.classList.add('bomb');
                        cell.textContent = '💣';
                    }
                });
                document.getElementById('resultado-mines').textContent = "Comando executado: Bombas reveladas!";
            } else {
                document.getElementById('resultado-mines').textContent = "Comando inválido ou jogo não iniciado!";
            }
        }

        // Inicializar
        document.getElementById('aposta').addEventListener('input', function(e) {
            if(parseInt(this.value) > saldo) this.value = saldo;
        });
        document.getElementById('aposta-mines').addEventListener('input', function(e) {
            if(parseInt(this.value) > saldo) this.value = saldo;
        });
        atualizarSaldo();
    </script>
</body>
</html>
