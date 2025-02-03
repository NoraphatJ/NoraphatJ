<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title> เกมภาษาไทย ม.1</title>
    <style>
        body { font-family: 'Charmonman', cursive; background: linear-gradient(to right, #ffcc00, #ff6699); text-align: center; padding: 20px; }
        .container { display: none; background-color: white; padding: 20px; border-radius: 15px; box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2); margin: auto; max-width: 600px; }
        h1 { font-size: 3em; color: #ff0000; text-shadow: 3px 3px 5px white; }
        .options { display: flex; flex-direction: column; gap: 15px; }
        .option { padding: 15px; border-radius: 15px; cursor: pointer; font-size: 1.4em; font-weight: bold; transition: transform 0.2s, background-color 0.3s; }
        .option:nth-child(1) { background-color: #ff9999; } 
        .option:nth-child(2) { background-color: #99ccff; }
        .option:nth-child(3) { background-color: #99ff99; } 
        .option:nth-child(4) { background-color: #ffcc66; }
        .option:hover { transform: scale(1.05); }
        .score, .timer { font-size: 1.4em; font-weight: bold; color: #ff0000; }
        .btn { padding: 15px 30px; font-size: 1.5em; cursor: pointer; background: #ff6600; color: white; border: none; border-radius: 10px; box-shadow: 3px 3px 5px rgba(0,0,0,0.3); transition: 0.3s; }
        .btn:hover { background: #ff3300; transform: scale(1.1); }
        .start-screen { display: flex; flex-direction: column; align-items: center; justify-content: center; height: 100vh; }
        .hidden { display: none; }
        .overlay {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: rgba(0, 0, 0, 0.8);
            color: white;
            padding: 20px;
            border-radius: 10px;
            z-index: 100;
        }
        .credits { font-size: 1.2em; margin-top: 20px; font-weight: bold; }
        .game-rules { font-size: 1.4em; margin-top: 20px; font-weight: bold; }
        .game-rules ul { text-align: left; }
    </style>
</head>
<body>

    <div class="start-screen">
        <h1> ยินดีต้อนรับสู่เกมภาษาไทย ม.1</h1>
        <button class="btn" onclick="showRules()"> เริ่มเกม</button>
    </div>

    <div class="game-rules overlay hidden">
        <h2>กติกาการเล่น</h2>
        <ul>
            <li>ผู้เล่นจะต้องตอบคำถามที่ปรากฏในแต่ละด่าน</li>
            <li>แต่ละด่านจะมีตัวเลือก 4 ตัวเลือก โดยเลือกตัวเลือกที่คิดว่าถูกต้อง</li>
            <li>หากตอบถูกต้องจะได้คะแนน 2 คะแนนและจะไปยังด่านถัดไป</li>
            <li>หากตอบผิด ระบบจะเล่นเสียงผิดและจะไปด่านถัดไปหลังจากนั้น</li>
            <li>เกมจะมีเวลาในการตอบคำถาม 20 วินาที ถ้าหมดเวลาแล้วจะถือว่า "ผิด"</li>
            <li>เกมจบเมื่อผ่านด่านทั้งหมด และจะโชว์คะแนนรวม</li>
        </ul>
        <button class="btn" onclick="startGame()">เริ่มเกม</button>
    </div>

    <div class="container hidden">
        <h1> เกมภาษาไทย ม.1 </h1>
        <button class="btn" onclick="goToMainMenu()"> กลับไปยังหน้าหลัก</button>
        <div class="level"> ด่านที่ <span id="level">1</span></div>
        <div class="question" id="question"> คำถามที่ 1</div>
        <div class="options" id="options">
            <div class="option" onclick="checkAnswer(1)"></div>
            <div class="option" onclick="checkAnswer(2)"></div>
            <div class="option" onclick="checkAnswer(3)"></div>
            <div class="option" onclick="checkAnswer(4)"></div>
        </div>
        <div class="timer">⏳ เวลาที่เหลือ: <span id="timeLeft">20</span> วินาที</div>
        <div class="score"> คะแนน: <span id="score">0</span></div>
        <button class="btn" onclick="restartGame()"> เริ่มใหม่</button>
    </div>

    <div class="credits overlay hidden">
        <h2>ผู้จัดทำ</h2>
        <p>นายนรภัทร จันทร์ส่งแสง เลขที่ 8 ชั้นม.3/8</p>
        <p>นางสาวสุรางคณาขวัญนิมิตร เลขที่ 36 ชั้นม.3/8</p>
        <p>โรงเรียนห้วยยอด จังหวัดตรัง</p>
        <button class="btn" onclick="goToMainMenu()">กลับไปยังหน้าหลัก</button>
    </div>

    <audio id="correctSound" src="https://www.myinstants.com/media/sounds/success.mp3"></audio>
    <audio id="wrongSound" src="https://www.myinstants.com/media/sounds/buzzer.mp3"></audio>

    <script>
        const questions = [
            { question: "🎭 'จับแพะชนแกะ' หมายถึงอะไร?", options: ["ทำแบบลวกๆ", "ทำอย่างมีระเบียบ", "ทำดีมีศีลธรรม", "ทำด้วยความรอบคอบ"], answer: 1 },
            { question: "🌊 คำว่า 'วารี' เป็นคำไวพจน์ของอะไร?", options: ["ไฟ", "น้ำ", "ดิน", "ลม"], answer: 2 },
            { question: "👑 คำว่า 'เสวย' เป็นคำราชาศัพท์ของอะไร?", options: ["กิน", "เดิน", "นอน", "พูด"], answer: 1 },
           { question: "📜 'สุริยัน' เป็นคำไวพจน์ของอะไร?", options: ["พระจันทร์", "พระอาทิตย์", "ดวงดาว", "สายฟ้า"], answer: 2 },
            { question: "🐘 คำราชาศัพท์ของ 'ช้าง' คืออะไร?", options: ["คชสาร", "สิงห์", "คชไกร", "พญาช้าง"], answer: 1 },
            { question: "🌙 คำว่า 'จันทรา' มีความหมายเหมือนกับอะไร?", options: ["พระจันทร์", "พระอาทิตย์", "ดวงดาว", "สายฟ้า"], answer: 1 },
            { question: "⚡ 'อัสนี' หมายถึงอะไร?", options: ["พายุ", "สายฟ้า", "ดวงอาทิตย์", "ภูเขา"], answer: 2 },
            { question: "💡 'สังเคราะห์' มีความหมายว่าอะไร?", options: ["สร้างขึ้น", "ทำลาย", "ผสมกัน", "คิดค้น"], answer: 1 },
            { question: "🔥 คำว่า 'อัคคี' หมายถึงอะไร?", options: ["ดิน", "น้ำ", "ไฟ", "ลม"], answer: 3 },
            { question: "📖 คำว่า 'พจนานุกรม' ใช้ทำอะไร?", options: ["อธิบายความหมายของคำ", "ใช้คำนวณ", "ใช้บันทึกความรู้", "ใช้วาดภาพ"], answer: 1 },
            { question: "🦁 คำว่า 'ราชสีห์' หมายถึงอะไร?", options: ["เสือ", "สิงโต", "หมาป่า", "มังกร"], answer: 2 },
            { question: "⛰️ คำว่า 'ศิขริน' หมายถึงอะไร?", options: ["ท้องฟ้า", "ภูเขา", "แม่น้ำ", "ทะเล"], answer: 2 },
            { question: "🐦 คำว่า 'วิหค' หมายถึงอะไร?", options: ["ปลา", "แมว", "นก", "เสือ"], answer: 3 },
            { question: "💎 คำว่า 'รัตนชาติ' หมายถึงอะไร?", options: ["หิน", "เพชรพลอย", "ต้นไม้", "ดิน"], answer: 2 },
            { question: "🌊 คำว่า 'สาคร' หมายถึงอะไร?", options: ["ภูเขา", "ทะเล", "ท้องฟ้า", "ลม"], answer: 2 }
        ];

        let currentLevel = 0;
        let score = 0;
        let timeLeft = 20;
        let timer;

        function stopSound(sound) {
            sound.pause();
            sound.currentTime = 0;
        }

        function nextLevel() {
            currentLevel++;
            if (currentLevel >= questions.length) {
                showCredits(); // แสดงรายชื่อผู้จัดทำเมื่อเกมจบ
                return;
            }
            loadLevel();
        }

        function checkAnswer(selected) {
            clearInterval(timer);
            const levelData = questions[currentLevel];
            let sound;
            
            if (selected === levelData.answer) {
                score += 2;
                document.getElementById('score').textContent = score;
                sound = document.getElementById('correctSound');
            } else {
                sound = document.getElementById('wrongSound');
            }

            sound.play();
            setTimeout(() => {
                stopSound(sound);
                nextLevel();
            }, 2000);
        }

        function loadLevel() {
            timeLeft = 20;
            document.getElementById("timeLeft").textContent = timeLeft;
            const levelData = questions[currentLevel];
            document.getElementById('question').textContent = levelData.question;
            const options = document.getElementById('options').children;
            for (let i = 0; i < options.length; i++) {
                options[i].textContent = levelData.options[i];
            }
            document.getElementById('level').textContent = currentLevel + 1;

            timer = setInterval(() => {
                timeLeft--;
                document.getElementById("timeLeft").textContent = timeLeft;
                if (timeLeft <= 0) {
                    clearInterval(timer);
                    document.getElementById('wrongSound').play();
                    setTimeout(() => {
                        stopSound(document.getElementById('wrongSound'));
                        nextLevel();
                    }, 2000);
                }
            }, 1000);
        }

        function startGame() {
            document.querySelector(".start-screen").classList.add("hidden");
            document.querySelector(".game-rules").classList.add("hidden");  // ซ่อนหน้ากติกา
            document.querySelector(".container").style.display = "block";
            restartGame();
        }

        function restartGame() {
            currentLevel = 0;
            score = 0;
            document.getElementById('score').textContent = score;
            loadLevel();
        }

        function showCredits() {
            document.querySelector(".container").style.display = "none"; // ซ่อนหน้าจอเกม
            document.querySelector(".credits").classList.remove("hidden"); // แสดงหน้าผู้จัดทำ
        }

        function goToMainMenu() {
            document.querySelector(".container").style.display = "none";
            document.querySelector(".start-screen").classList.remove("hidden");
            document.querySelector(".credits").classList.add("hidden");
            document.querySelector(".game-rules").classList.add("hidden");
        }

        function showRules() {
            document.querySelector(".start-screen").classList.add("hidden");
            document.querySelector(".game-rules").classList.remove("hidden");
        }
    </script>

</body>
</html>
