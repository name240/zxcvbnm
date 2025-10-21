
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Public Notepad - M.SaimMAJOKA</title>
  <style>
    body {
      margin: 0;
      font-family: 'Segoe UI', sans-serif;
      background-color: #f4f6f9;
    }

    header {
      background-color: #007bff;
      color: white;
      padding: 15px 20px;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }

    header h1 {
      margin: 0;
      font-size: 20px;
    }

    nav button {
      background: white;
      border: none;
      color: #007bff;
      padding: 8px 12px;
      font-size: 14px;
      margin-left: 10px;
      border-radius: 4px;
      cursor: pointer;
    }

    .container {
      max-width: 900px;
      margin: 20px auto;
      padding: 20px;
      background: white;
      border-radius: 8px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
    }

    textarea {
      width: 100%;
      height: 200px;
      padding: 10px;
      font-size: 16px;
      border: 1px solid #ccc;
      border-radius: 4px;
      resize: none;
    }

    .buttons {
      margin-top: 15px;
      text-align: center;
    }

    .buttons button,
    .buttons input[type="file"] {
      margin: 5px;
      padding: 10px 18px;
      font-size: 15px;
      background-color: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }

    .buttons button:hover {
      background-color: #0056b3;
    }

    .public-text {
      background: #f8f9fa;
      padding: 10px;
      margin-bottom: 10px;
      border-left: 4px solid #007bff;
      border-radius: 4px;
      white-space: pre-wrap;
    }

    footer {
      text-align: center;
      padding: 15px;
      background: #e9ecef;
      color: #333;
      font-weight: bold;
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
    <h1>📝 Public Notepad</h1>
    <nav>
      <button onclick="showPage('notepad')">Notepad</button>
      <button onclick="showPage('public')">Public Texts</button>
    </nav>
  </header>

  <!-- 📝 Notepad Section -->
  <section id="notepad" class="active container">
    <h2>Write, Upload, Share, Download</h2>
    <textarea id="textArea" placeholder="Start typing here..."></textarea>

    <div class="buttons">
      <input type="file" id="fileInput" accept=".txt">
      <button onclick="downloadText()">Download</button>
      <button onclick="copyText()">Copy</button>
      <button onclick="clearText()">Clear</button>
      <button onclick="uploadText()">Upload & Share</button>
    </div>
  </section>

  <!-- 🌍 Public Texts Section -->
  <section id="public" class="container">
    <h2>🌍 All Public Texts</h2>
    <div id="publicTexts"></div>
  </section>

  <footer>
    Created by: <strong>M.SaimMAJOKA</strong>
  </footer>

  <!-- Firebase SDK -->
  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

  <script>
    // 🔧 INSERT YOUR FIREBASE CONFIG HERE
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

    // 🔁 Live load public texts
    function loadPublicTexts() {
      const container = document.getElementById('publicTexts');
      db.ref("publicTexts").orderByChild("timestamp").limitToLast(100).on("value", snapshot => {
        container.innerHTML = "";
        const data = snapshot.val();
        if (data) {
          const entries = Object.values(data).reverse();
          entries.forEach(entry => {
            const div = document.createElement("div");
            div.className = "public-text";
            div.textContent = entry.text;
            container.appendChild(div);
          });
        } else {
          container.innerHTML = "<p>No public texts yet.</p>";
        }
      });
    }

    // 📤 Upload text to Firebase
    function uploadText() {
      const text = document.getElementById('textArea').value.trim();
      if (text === "") {
        alert("Write something before uploading.");
        return;
      }
      const entry = {
        text: text,
        timestamp: Date.now()
      };
      db.ref("publicTexts").push(entry);
      document.getElementById('textArea').value = "";
      alert("Uploaded successfully!");
    }

    // 📥 Download text as .txt
    function downloadText() {
      const text = document.getElementById('textArea').value;
      const blob = new Blob([text], { type: 'text/plain' });
      const link = document.createElement('a');
      link.href = URL.createObjectURL(blob);
      link.download = 'notepad.txt';
      link.click();
    }

    // 📋 Copy to clipboard
    function copyText() {
      const textarea = document.getElementById('textArea');
      textarea.select();
      document.execCommand("copy");
      alert("Text copied!");
    }

    // 🧹 Clear text
    function clearText() {
      document.getElementById('textArea').value = "";
    }

    // 📂 Upload .txt file into textarea
    document.getElementById('fileInput').addEventListener('change', function (e) {
      const file = e.target.files[0];
      if (file && file.type === "text/plain") {
        const reader = new FileReader();
        reader.onload = function (e) {
          document.getElementById('textArea').value = e.target.result;
        };
        reader.readAsText(file);
      } else {
        alert("Please upload a .txt file.");
      }
    });

    // 🔄 Page switcher
    function showPage(id) {
      document.querySelectorAll("section").forEach(s => s.classList.remove("active"));
      document.getElementById(id).classList.add("active");
      if (id === "public") loadPublicTexts();
    }

    // ✅ Auto load public text on first visit to Public page
    loadPublicTexts();
  </script>

</body>
</html>
