<!DOCTYPE html>
<html>
<head>
  <title>Galaxy Quest: Beyond the Stars</title>
  <style>
    body { 
      background: linear-gradient(135deg, #000428 0%, #004e92 100%);
      color: #00ff41; 
      font-family: 'Courier New', monospace; 
      margin: 0;
      padding: 20px;
      min-height: 100vh;
    }
    h1 {
      text-align: center;
      font-size: 2.5em;
      text-shadow: 0 0 20px #00ff41;
      margin-bottom: 30px;
      animation: glow 2s ease-in-out infinite alternate;
    }
    @keyframes glow {
      from { text-shadow: 0 0 20px #00ff41; }
      to { text-shadow: 0 0 30px #00ff41, 0 0 40px #00ff41; }
    }
    .game-container {
      max-width: 800px;
      margin: 0 auto;
      background: rgba(0, 20, 40, 0.8);
      border: 2px solid #00ff41;
      border-radius: 15px;
      padding: 30px;
      box-shadow: 0 0 30px rgba(0, 255, 65, 0.3);
    }
    .stats-bar {
      display: flex;
      justify-content: space-between;
      background: rgba(0, 0, 0, 0.5);
      padding: 15px;
      border-radius: 10px;
      margin-bottom: 20px;
      border: 1px solid #00ff41;
    }
    .stat {
      display: flex;
      align-items: center;
      font-size: 1.2em;
      font-weight: bold;
    }
    .fuel-bar {
      width: 200px;
      height: 20px;
      background: #333;
      border: 1px solid #00ff41;
      border-radius: 10px;
      overflow: hidden;
      margin-left: 10px;
    }
    .fuel-fill {
      height: 100%;
      background: linear-gradient(90deg, #ff0000, #ffff00, #00ff00);
      transition: width 0.5s ease;
    }
    #log { 
      white-space: pre-line; 
      margin-bottom: 20px; 
      background: rgba(0, 0, 0, 0.3);
      padding: 20px;
      border-radius: 10px;
      border: 1px solid #00ff41;
      min-height: 150px;
      font-size: 1.1em;
      line-height: 1.5;
    }
    .actions-container {
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
      justify-content: center;
    }
    button { 
      background: linear-gradient(145deg, #1a4a5c, #2d5a73);
      color: #00ff41; 
      border: 2px solid #00ff41; 
      padding: 12px 20px; 
      margin: 5px;
      border-radius: 8px;
      font-family: 'Courier New', monospace;
      font-size: 1em;
      cursor: pointer;
      transition: all 0.3s ease;
      text-shadow: 0 0 5px #00ff41;
    }
    button:hover {
      background: linear-gradient(145deg, #2d5a73, #1a4a5c);
      box-shadow: 0 0 15px rgba(0, 255, 65, 0.5);
      transform: translateY(-2px);
    }
    button:active {
      transform: translateY(0);
    }
    .achievement {
      background: rgba(255, 215, 0, 0.1);
      border: 1px solid gold;
      color: gold;
      padding: 10px;
      border-radius: 5px;
      margin: 10px 0;
      text-align: center;
      animation: achievementGlow 1s ease-in-out;
    }
    @keyframes achievementGlow {
      0% { box-shadow: 0 0 5px gold; }
      50% { box-shadow: 0 0 20px gold; }
      100% { box-shadow: 0 0 5px gold; }
    }
    .game-over {
      background: rgba(255, 0, 0, 0.1);
      border: 2px solid #ff0000;
      color: #ff6666;
      padding: 20px;
      border-radius: 10px;
      text-align: center;
      font-size: 1.3em;
      animation: gameOverPulse 1s ease-in-out infinite alternate;
    }
    @keyframes gameOverPulse {
      from { box-shadow: 0 0 10px #ff0000; }
      to { box-shadow: 0 0 25px #ff0000; }
    }
    .restart-btn {
      background: linear-gradient(145deg, #4a1a1a, #732d2d) !important;
      border-color: #ff6666 !important;
      color: #ff6666 !important;
    }
  </style>
</head>
<body>
  <div class="game-container">
    <h1>🚀 Galaxy Quest: Beyond the Stars</h1>
    
    <div class="stats-bar">
      <div class="stat">
        ⛽ Bahan Bakar: <div class="fuel-bar"><div class="fuel-fill" id="fuelBar"></div></div>
        <span id="fuelText">100</span>
      </div>
      <div class="stat">
        📅 Hari: <span id="dayText">1</span>
      </div>
      <div class="stat">
        🏆 Skor: <span id="scoreText">0</span>
      </div>
    </div>

    <div id="log">Selamat datang di petualangan luar angkasa! 🌌
    
Kamu adalah kapten pesawat ruang angkasa yang menjelajahi galaksi yang belum dipetakan. Setiap keputusan yang kamu buat akan menentukan nasib perjalananmu!</div>
    
    <div class="actions-container" id="actions"></div>
  </div>

  <script>
    let fuel = 100;
    let day = 1;
    let score = 0;
    let discoveries = 0;
    let aliensDefeated = 0;
    let log = document.getElementById("log");
    let actions = document.getElementById("actions");
    let gameOver = false;

    function updateDisplay() {
      document.getElementById("fuelText").textContent = fuel;
      document.getElementById("dayText").textContent = day;
      document.getElementById("scoreText").textContent = score;
      
      // Update fuel bar
      let fuelPercentage = Math.max(0, (fuel / 100) * 100);
      document.getElementById("fuelBar").style.width = fuelPercentage + "%";
    }

    function updateLog(text, isGameOver = false) {
      if (isGameOver) {
        log.innerHTML = `<div class="game-over">${text}</div>`;
      } else {
        log.textContent = text;
      }
      updateDisplay();
    }

    function showAchievement(text) {
      let achievement = document.createElement("div");
      achievement.className = "achievement";
      achievement.textContent = "🏆 " + text;
      log.appendChild(achievement);
      setTimeout(() => achievement.remove(), 3000);
    }

    function setActions(buttons) {
      actions.innerHTML = '';
      buttons.forEach(b => {
        let btn = document.createElement("button");
        btn.textContent = b.text;
        btn.onclick = b.action;
        if (b.className) btn.className = b.className;
        actions.appendChild(btn);
      });
    }

    function explore() {
      if (gameOver) return;
      
      day++;
      fuel -= 10;
      let eventChance = Math.random();
      
      if (eventChance < 0.2) {
        // Rare discovery
        discoveries++;
        score += 50;
        fuel += 30;
        updateLog("🌟 Penemuan luar biasa! Kamu menemukan kristal energi langka yang memberikan +30 bahan bakar dan +50 poin!");
        if (discoveries === 3) {
          showAchievement("Penjelajah Ulung - 3 Penemuan Langka!");
        }
      } else if (eventChance < 0.35) {
        // Fuel discovery
        score += 20;
        fuel += 20;
        updateLog("⛽ Kamu menemukan stasiun bahan bakar terbengkalai dan berhasil mengisi +20 bahan bakar!");
      } else if (eventChance < 0.55) {
        // Alien attack
        aliensDefeated++;
        fuel -= 15;
        score += 30;
        updateLog("👽 Serangan alien! Kamu berhasil mengalahkan mereka tapi kehilangan 15 bahan bakar. +30 poin untuk keberanian!");
        if (aliensDefeated === 5) {
          showAchievement("Pembela Galaksi - 5 Alien Dikalahkan!");
        }
      } else if (eventChance < 0.7) {
        // Asteroid field
        fuel -= 8;
        score += 10;
        updateLog("☄️ Medan asteroid! Kamu berhasil melewatinya dengan kehilangan 8 bahan bakar.");
      } else if (eventChance < 0.85) {
        // Peaceful journey
        score += 5;
        updateLog("🌌 Perjalanan damai melalui nebula yang indah. Tidak ada kejadian berarti.");
      } else {
        // Space pirates
        fuel -= 20;
        score += 25;
        updateLog("🏴‍☠️ Bajak laut ruang angkasa! Kamu berhasil kabur tapi kehilangan 20 bahan bakar dalam pengejaran.");
      }
      
      checkStatus();
    }

    function rest() {
      if (gameOver) return;
      
      day++;
      fuel += 5; // Small fuel recovery during rest
      score += 2;
      updateLog("🛑 Kamu beristirahat di orbit planet terdekat. Sistem kapal melakukan perbaikan kecil (+5 bahan bakar).");
      checkStatus();
    }

    function emergencyRefuel() {
      if (gameOver) return;
      
      day++;
      fuel += 40;
      score -= 10; // Penalty for emergency
      updateLog("🚨 Panggilan darurat ke stasiun terdekat! +40 bahan bakar tapi -10 poin karena biaya darurat.");
      checkStatus();
    }

    function checkStatus() {
      if (fuel <= 0) {
        gameOver = true;
        updateLog(`💀 Bahan bakar habis! Kapalmu terjebak di ruang angkasa.

GAME OVER
Skor Akhir: ${score}
Hari Bertahan: ${day}
Penemuan: ${discoveries}
Alien Dikalahkan: ${aliensDefeated}`, true);
        
        setActions([
          { text: "🔄 Mulai Petualangan Baru", action: restartGame, className: "restart-btn" }
        ]);
        return;
      }

      // Check for achievements
      if (day === 10 && !gameOver) {
        showAchievement("Survivor - Bertahan 10 Hari!");
        score += 100;
      }
      if (score >= 500 && !gameOver) {
        showAchievement("Master Explorer - 500+ Poin!");
      }

      // Dynamic action options based on fuel level
      let actionButtons = [
        { text: "🌌 Jelajah Ruang Angkasa", action: explore },
        { text: "🛑 Istirahat & Perbaikan", action: rest }
      ];

      if (fuel <= 30) {
        actionButtons.push({ text: "🚨 Panggilan Darurat", action: emergencyRefuel });
      }

      setActions(actionButtons);
    }

    function restartGame() {
      fuel = 100;
      day = 1;
      score = 0;
      discoveries = 0;
      aliensDefeated = 0;
      gameOver = false;
      
      updateLog("Selamat datang di petualangan luar angkasa! 🌌\n\nKamu adalah kapten pesawat ruang angkasa yang menjelajahi galaksi yang belum dipetakan. Setiap keputusan yang kamu buat akan menentukan nasib perjalananmu!");
      checkStatus();
    }

    // Initialize game
    updateDisplay();
    checkStatus();
  </script>
<script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'96dcd80c204b8208',t:'MTc1NDk2ODkwMC4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
