<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dragon Dice - Edisi Remot Cloud</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { 
            display: flex; flex-direction: column; align-items: center; 
            height: 100vh; background: radial-gradient(circle, #1a0505 0%, #0a0101 100%);
            font-family: 'Segoe UI', Roboto, sans-serif; color: #fff; padding: 20px;
            overflow: hidden; 
        }
        .title {
            font-size: 2.5rem; font-weight: 900; text-transform: uppercase;
            letter-spacing: 4px; color: #e63946;
            text-shadow: 0 0 15px rgba(230, 57, 70, 0.7); margin-top: 10px; text-align: center;
        }
        .dice-grid {
            display: grid; grid-template-columns: repeat(3, 1fr); gap: 20px; 
            margin: auto 0; width: 100%; max-width: 450px;
        }
        .scene { width: 100px; height: 100px; perspective: 400px; margin: auto; }
        .cube { 
            width: 100%; height: 100%; position: relative; transform-style: preserve-3d; 
            transition: transform 0.6s cubic-bezier(0.175, 0.885, 0.32, 1.275); 
            transform: translateZ(-50px) rotateX(0deg) rotateY(0deg); 
        }
        .cube__face { 
            position: absolute; width: 100px; height: 100px; border: 2px solid #e63946; 
            background: rgba(18, 18, 18, 0.98); display: grid; 
            grid-template-columns: repeat(3, 1fr); grid-template-rows: repeat(3, 1fr); padding: 10px;
            box-shadow: inset 0 0 15px rgba(230, 57, 70, 0.5); backface-visibility: hidden; 
        }
        .cube__face--1 { transform: rotateY(0deg) translateZ(50px); }
        .cube__face--2 { transform: rotateY(180deg) translateZ(50px); }
        .cube__face--3 { transform: rotateY(90deg) translateZ(50px); }
        .cube__face--4 { transform: rotateY(-90deg) translateZ(50px); }
        .cube__face--5 { transform: rotateX(90deg) translateZ(50px); }
        .cube__face--6 { transform: rotateX(-90deg) translateZ(50px); }
        .dot { 
            width: 14px; height: 14px; background: radial-gradient(circle, #ff4d4d, #990000); 
            border-radius: 50%; justify-self: center; align-self: center; box-shadow: 0 0 6px #ff4d4d; 
        }
        .cube__face--1 .dot:nth-child(1) { grid-area: 2 / 2; } 
        .cube__face--2 .dot:nth-child(1) { grid-area: 1 / 1; } .cube__face--2 .dot:nth-child(2) { grid-area: 3 / 3; }
        .cube__face--3 .dot:nth-child(1) { grid-area: 1 / 1; } .cube__face--3 .dot:nth-child(2) { grid-area: 2 / 2; } .cube__face--3 .dot:nth-child(3) { grid-area: 3 / 3; }
        .cube__face--4 .dot:nth-child(1) { grid-area: 1 / 1; } .cube__face--4 .dot:nth-child(2) { grid-area: 1 / 3; } .cube__face--4 .dot:nth-child(3) { grid-area: 3 / 1; } .cube__face--4 .dot:nth-child(4) { grid-area: 3 / 3; }
        .cube__face--5 .dot:nth-child(1) { grid-area: 1 / 1; } .cube__face--5 .dot:nth-child(2) { grid-area: 1 / 3; } .cube__face--5 .dot:nth-child(3) { grid-area: 2 / 2; } .cube__face--5 .dot:nth-child(4) { grid-area: 3 / 1; } .cube__face--5 .dot:nth-child(5) { grid-area: 3 / 3; }
        .cube__face--6 .dot:nth-child(1) { grid-area: 1 / 1; } .cube__face--6 .dot:nth-child(2) { grid-area: 1 / 3; } .cube__face--6 .dot:nth-child(3) { grid-area: 2 / 1; } .cube__face--6 .dot:nth-child(4) { grid-area: 2 / 3; } .cube__face--6 .dot:nth-child(5) { grid-area: 3 / 1; } .cube__face--6 .dot:nth-child(6) { grid-area: 3 / 3; }

        /* Posisi tombol dinaikkan agar lebih nyaman di layar HP */
        .footer-controls { 
            display: flex; flex-direction: column; align-items: center; gap: 15px; 
            width: 100%; max-width: 450px; 
            margin-bottom: 75px; 
        }
        .range-info-row { display: flex; justify-content: space-between; width: 100%; padding: 0 5px; }
        .info-label { font-size: 1.1rem; font-weight: bold; letter-spacing: 1px; }
        .info-label.kecil { color: #4ea8de; } .info-label.besar { color: #ff4d4d; }
        .result { font-size: 1.8rem; color: #ffb703; font-weight: bold; text-align: center; text-shadow: 0 0 15px rgba(255, 183, 3, 0.5); margin: 5px 0; }
        .btn-roll { 
            padding: 16px 80px; font-size: 1.3rem; font-weight: bold; background: linear-gradient(135deg, #b7091a, #e63946);
            color: white; border: none; border-radius: 50px; cursor: pointer; box-shadow: 0 6px 20px rgba(230, 57, 70, 0.4); width: 100%; text-transform: uppercase;
        }
        .btn-roll:active { transform: scale(0.98); }
    </style>
</head>
<body>

    <h1 class="title">Dragon Dice</h1>
    <div class="dice-grid" id="dice-container"></div>

    <div class="footer-controls">
        <div class="range-info-row">
            <div class="info-label kecil">KECIL: 1-31</div>
            <div class="info-label besar">BESAR: 32-54</div>
        </div>
        <div class="result" id="result-text">Klik untuk Mulai 🐉</div>
        <button class="btn-roll" id="roll-button" onclick="rollAllDice()">KOCOK SEMUA</button>
    </div>

    <script>
        const API_URL = "https://6a11340a3e35df37ce31851.mockapi.io/control/1"; 

        const container = document.getElementById('dice-container');
        const resultText = document.getElementById('result-text');
        const rollButton = document.getElementById('roll-button');
        
        const diceTemplate = `
            <div class="scene"><div class="cube">
                <div class="cube__face cube__face--1"><span class="dot"></span></div>
                <div class="cube__face cube__face--2"><span class="dot"></span><span class="dot"></span></div>
                <div class="cube__face cube__face--3"><span class="dot"></span><span class="dot"></span><span class="dot"></span></div>
                <div class="cube__face cube__face--4"><span class="dot"></span><span class="dot"></span><span class="dot"></span><span class="dot"></span></div>
                <div class="cube__face cube__face--5"><span class="dot"></span><span class="dot"></span><span class="dot"></span><span class="dot"></span><span class="dot"></span></div>
                <div class="cube__face cube__face--6"><span class="dot"></span><span class="dot"></span><span class="dot"></span><span class="dot"></span><span class="dot"></span><span class="dot"></span></div>
            </div></div>`;

        for(let i=0; i<9; i++) container.innerHTML += diceTemplate;

        const perfectFlatRotations = {
            1: { x: 0, y: 0 }, 2: { x: 0, y: 180 }, 3: { x: 0, y: -90 },
            4: { x: 0, y: 90 }, 5: { x: -90, y: 0 }, 6: { x: 90, y: 0 }
        };

        let totalRotationsX = Array(9).fill(0);
        let totalRotationsY = Array(9).fill(0);

        async function rollAllDice() {
            rollButton.disabled = true;
            resultText.innerText = "Menghubungkan ke Server...";

            let modeRemot = 'normal';

            const controller = new AbortController();
            const timeoutId = setTimeout(() => controller.abort(), 2500);

            try {
                const response = await fetch(API_URL, { signal: controller.signal });
                const data = await response.json();
                if (data && data.mode) {
                    modeRemot = data.mode.toLowerCase().replace('/', ''); 
                }
            } catch (err) {
                console.log("Gagal mengambil mode remot, otomatis beralih ke acak murni.");
                modeRemot = 'normal';
            } finally {
                clearTimeout(timeoutId);
            }

            resultText.innerText = "Mengocok...";
            
            const cubes = document.querySelectorAll('.cube');
            let finalValues = [];
            let total = 0;

            while (true) {
                finalValues = [];
                total = 0;
                for (let i = 0; i < 9; i++) {
                    let singleValue = Math.floor(Math.random() * 6) + 1;
                    finalValues.push(singleValue);
                    total += singleValue;
                }
                
                if (modeRemot === 'kecil' && total <= 31) break;
                if (modeRemot === 'besar' && total >= 32) break;
                if (modeRemot !== 'besar' && modeRemot !== 'kecil') break; 
            }
            
            cubes.forEach((cube, index) => {
                const val = finalValues[index];
                const randomMultiplierX = (Math.floor(Math.random() * 3) + 5) * 360; 
                const randomMultiplierY = (Math.floor(Math.random() * 3) + 5) * 360; 

                totalRotationsX[index] += randomMultiplierX;
                totalRotationsY[index] += randomMultiplierY;

                const targetX = perfectFlatRotations[val].x + totalRotationsX[index];
                const targetY = perfectFlatRotations[val].y + totalRotationsY[index];

                cube.style.transform = `translateZ(-50px) rotateX(${targetX}deg) rotateY(${targetY}deg)`;
            });
            
            setTimeout(() => {
                let status = total >= 32 ? " (BESAR) 🔴" : " (KECIL) 🔵";
                resultText.innerText = "Total Skor: " + total + status;
                rollButton.disabled = false;
            }, 600);
        }
    </script>
</body>
</html>
