<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Public Notepad - M.SaimMAJOKA</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background: #f0f4f8;
      margin: 0;
      padding: 0;
    }

    header {
      background-color: #007BFF;
      color: white;
      padding: 15px 20px;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }

    header h1 {
      margin: 0;
      font-size: 22px;
    }

    nav button {
      background-color: white;
      color: #007BFF;
      border: none;
      padding: 8px 14px;
      margin-left: 10px;
      font-weight: bold;
      border-radius: 4px;
      cursor: pointer;
    }

    .container {
      padding: 20px;
      max-width: 900px;
      margin: auto;
    }

    textarea {
      width: 100%;
      height: 250px;
      padding: 12px;
      font-size: 16px;
      margin-top: 10px;
      border: 1px solid #ccc;
      border-radius: 6px;
      resize: none;
    }

    .buttons {
      margin-top: 15px;
      text-align: center;
    }

    .buttons button, .buttons input[type="file"] {
      margin: 6px;
      padding: 10px 18px;
      font-size: 16px;
      border: none;
      background-color: #007BFF;
      color: white;
      border-radius: 4px;
      cursor: pointer;
    }

    .buttons button:hover {
      background-color: #0056b3;
    }

    .public-texts {
      margin-top: 30px;
    }

    .public-entry {
      background: white;
      padding: 12px;
      margin-bottom: 10px;
      border-left: 5px solid #007BFF;
      border-radius: 4px;
      white-space: pre-wrap;
    }

    footer {
      text-align: center;
      padding: 20px;
      font-weight: bold;
      background-color: #f8f9fa;
      color: #555;
    }

    section {
      display: none;
    }

    section.active {
      display: block;
    }

  </style>
</head>
<body>

  <header>
    <h1>🌐 Public Notepad</h1>
    <nav>
      <button onclick="showSection('notepadSection')">📝 Notepad</button>
      <button onclick="showSection('publicSection')">🌍 Public Texts</button>
    </nav>
  </header>

  <!-- Section 1: Notepad -->
  <section id="notepadSection" class="active container">
    <h2>📝 Write, Upload, Download & Share</h2>
    <textarea id="notepad" placeholder="Type your notes here..."></textarea>

    <div class="buttons">
      <input type="file" id="fileInput" accept=".txt">
      <button onclick="downloadText()">Download</button>
      <button onclick="copyText()">Copy</button>
      <button onclick="clearText()">Clear</button>
      <button onclick="uploadText()">Upload to Public</button>
    </div>
  </section>

  <!-- Section 2: Public Texts -->
  <section id="publicSection" class="container">
    <h2>🌍 Shared Public Notes</h2>
    <div id="publicTexts" class="public-texts">
      <p>Loading public texts...</p>
    </div>
  </section>

  <footer>
    Created by: <strong>M.SaimMAJOKA</strong>
  </footer>

  <!-- Firebase Scripts -->
  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

  <script>
    // 🔧 Replace with your Firebase config
    const firebaseConfig = {
      apiKey: "YOUR_API_KEY",
      authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
      databaseURL: "https://YOUR_PROJECT_ID.firebaseio.com",
      projectId: "YOUR_PROJECT_ID",
      storageBucket: "YOUR_PROJECT_ID.appspot.com",
      messagingSenderId: "YOUR_SENDER_ID",
      appId: "YOUR_APP_ID"
    };

    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    // 📤 Upload text to Firebase
    function uploadText() {
      const text = document.getElementById('notepad').value.trim();
      if (text.length === 0) {
        alert("Please enter some text to upload.");
        return;
      }

      const entry = {
        text: text,
        timestamp: Date.now()
      };

      db.ref("publicTexts").push(entry);
      document.getElementById('notepad').value = '';
      alert("Text uploaded successfully!");
    }

    // 📥 Load public texts
    function loadPublicTexts() {
      const container = document.getElementById("publicTexts");
      db.ref("publicTexts").orderByChild("timestamp").limitToLast(100).on("value", (snapshot) => {
        container.innerHTML = '';
        const data = snapshot.val();
        if (data) {
          const entries = Object.values(data).reverse();
          entries.forEach(entry => {
            const div = document.createElement("div");
            div.className = "public-entry";
            div.textContent = entry.text;
            container.appendChild(div);
          });
        } else {
          container.innerHTML = "<p>No public texts yet.</p>";
        }
      });
    }

    // 📄 Download text
    function downloadText() {
      const text = document.getElementById("notepad").value;
      const blob = new Blob([text], { type: "text/plain" });
      const link = document.createElement("a");
      link.download = "notepad.txt";
      link.href = URL.createObjectURL(blob);
      link.click();
    }

    // 📋 Copy text
    function copyText() {
      const textarea = document.getElementById("notepad");
      textarea.select();
      document.execCommand("copy");
      alert("Text copied to clipboard!");
    }

    // 🧹 Clear notepad
    function clearText() {
      document.getElementById("notepad").value = "";
    }

    // 📂 Load file into notepad
    document.getElementById("fileInput").addEventListener("change", function (e) {
      const file = e.target.files[0];
      if (file && file.type === "text/plain") {
        const reader = new FileReader();
        reader.onload = function (e) {
          document.getElementById("notepad").value = e.target.result;
        };
        reader.readAsText(file);
      } else {
        alert("Only .txt files are supported.");
      }
    });

    // 🌐 Page navigation
    function showSection(id) {
      document.querySelectorAll('section').forEach(section => {
        section.classList.remove('active');
      });
      document.getElementById(id).classList.add('active');

      if (id === 'publicSection') {
        loadPublicTexts();
      }
    }

    // Auto-load public texts if already on that section
    if (document.getElementById('publicSection').classList.contains('active')) {
      loadPublicTexts();
    }

  </script>
</body>
</html>
