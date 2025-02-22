<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Rolador de Dados 3D</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #222;
            color: white;
        }
        .controls {
            margin: 20px;
        }
        .dice-container {
            display: flex;
            justify-content: center;
            flex-wrap: wrap;
            margin-top: 20px;
        }
        .dice {
            width: 80px;
            height: 80px;
            margin: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 28px;
            font-weight: bold;
            color: black;
            background-color: white;
            border: 3px solid black;
            border-radius: 10px;
            position: relative;
            transform: rotate(0deg);
            animation: rollAnimation 1s ease-out;
        }
        @keyframes rollAnimation {
            0% { transform: rotate(0deg); }
            25% { transform: rotate(90deg); }
            50% { transform: rotate(180deg); }
            75% { transform: rotate(270deg); }
            100% { transform: rotate(360deg); }
        }
        button, select, input {
            padding: 10px;
            font-size: 16px;
            margin: 5px;
            cursor: pointer;
            background: #444;
            color: white;
            border: none;
            border-radius: 5px;
        }
        .npc-list {
            margin-top: 20px;
        }
        .npc-entry {
            background: #333;
            padding: 10px;
            margin: 5px;
            border-radius: 5px;
            display: flex;
            align-items: center;
            justify-content: space-between;
        }
        .npc-entry span {
            margin-left: 10px;
        }
        .remove-btn {
            background: red;
            padding: 5px 10px;
            border-radius: 5px;
            cursor: pointer;
        }
        .advantage-input {
            width: 40px;
            margin-left: 10px;
        }
    </style>
</head>
<body>

    <h2>Rolador de Dados 3D</h2>

    <div class="controls">
        <label for="npcName">Nome do NPC:</label>
        <input type="text" id="npcName" placeholder="Digite o nome do NPC">
        <button onclick="addNPC()">➕ Adicionar NPC</button>
    </div>

    <div class="npc-list" id="npcList"></div>

    <div class="controls">
        <label for="diceType">Escolha o dado:</label>
        <select id="diceType">
            <option value="4">D4</option>
            <option value="6" selected>D6</option>
            <option value="8">D8</option>
            <option value="10">D10</option>
            <option value="12">D12</option>
            <option value="20">D20</option>
        </select>

        <label for="diceCount">Quantidade:</label>
        <input type="number" id="diceCount" value="1" min="1" max="10">
        
        <button onclick="rollDice()">🎲 Rolar</button>
        <button onclick="clearDice()">❌ Limpar</button>
    </div>

    <h3>Resultados:</h3>
    <div class="dice-container" id="result"></div>
    <h3 id="total"></h3>

    <script>
        let npcNames = [];

        function addNPC() {
            let npcName = document.getElementById("npcName").value.trim();
            if (npcName && !npcNames.some(npc => npc.name === npcName)) {
                npcNames.push({ name: npcName, result: "-", advantage: 0 });
                updateNPCList();
                document.getElementById("npcName").value = "";
            }
        }

        function removeNPC(index) {
            npcNames.splice(index, 1);
            updateNPCList();
        }

        function updateNPCList() {
            let npcListDiv = document.getElementById("npcList");
            npcListDiv.innerHTML = "";
            npcNames.forEach((npc, index) => {
                let npcDiv = document.createElement("div");
                npcDiv.className = "npc-entry";
                npcDiv.innerHTML = `
                    <span>${npc.name}: <strong>${npc.result}</strong></span>
                    <input type="number" class="advantage-input" 
                           value="${npc.advantage}" 
                           onchange="updateAdvantage(${index}, event)" 
                           min="0" placeholder="Vantagem">
                    <button class="remove-btn" onclick="removeNPC(${index})">❌</button>
                `;
                npcListDiv.appendChild(npcDiv);
            });
        }

        function updateAdvantage(index, event) {
            npcNames[index].advantage = parseInt(event.target.value) || 0;
        }

        function createDice(number, color) {
            let dice = document.createElement("div");
            dice.className = "dice";
            dice.style.backgroundColor = color;
            dice.innerText = number;
            document.getElementById("result").appendChild(dice);
        }

        function rollDice() {
            if (npcNames.length === 0) {
                alert("Adicione pelo menos um NPC antes de rolar os dados.");
                return;
            }

            let total = 0;
            document.getElementById("result").innerHTML = "";

            let sides = parseInt(document.getElementById("diceType").value);
            let count = parseInt(document.getElementById("diceCount").value);

            npcNames.forEach((npc, index) => {
                let npcTotal = 0;
                for (let i = 0; i < count; i++) {
                    let value = Math.floor(Math.random() * sides) + 1;
                    npcTotal += value;
                    let color = i % 2 === 0 ? "white" : "red";
                    createDice(value, color);
                }
                // Add advantage to the NPC's total result
                npcTotal += npc.advantage;
                npcNames[index].result = npcTotal;
            });

            updateNPCList();

            setTimeout(() => {
                document.getElementById("total").innerText = `Soma total: ${total}`;
            }, 1000);
        }

        function clearDice() {
            document.getElementById("result").innerHTML = "";
            document.getElementById("total").innerText = "";
            npcNames.forEach(npc => npc.result = "-");
            updateNPCList();
        }
    </script>

</body>
</html>
