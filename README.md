<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Public Notepad - M.SaimMAJOKA</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #eef2f3;
      padding: 20px;
      text-align: center;
    }
    textarea {
      width: 90%;
      height: 200px;
      padding: 10px;
      font-size: 16px;
      margin-top: 10px;
      resize: none;
    }
    .buttons {
      margin-top: 15px;
    }
    button, input[type="file"] {
      margin: 5px;
      padding: 10px 15px;
      font-size: 15px;
      cursor: pointer;
    }
    .uploaded-texts {
      margin-top: 30px;
      text-align: left;
      max-width: 800px;
      margin-left: auto;
      margin-right: auto;
    }
    .uploaded-texts h3 {
      text-align: center;
    }
    .text-box {
      background: #fff;
      padding: 10px;
      margin-bottom: 10px;
      border-left: 5px solid #007bff;
      white-space: pre-wrap;
    }
    footer {
      margin-top: 30px;
      font-weight: bold;
      color: #444;
    }
  </style>
</head>
<body>

  <h1>🌐 Public Notepad</h1>
  <p>Write, Upload, Share & View Public Text</p>

  <textarea id="notepad" placeholder="Start typing or upload a file..."></textarea>

  <div class="buttons">
    <input type="file" id="fileInput" accept=".txt">
    <button onclick="downloadText()">Download</button>
    <button onclick="copyText()">Copy</button>
    <button onclick="clearText()">Clear</button>
    <button onclick="uploadText()">Upload to Public Board</button>
  </div>

  <div class="uploaded-texts">
    <h3>🌍 Public Uploaded Texts</h3>
    <div id="publicTexts"></div>
  </div>

  <footer>
    Created by: <strong>M.SaimMAJOKA</strong>
  </footer>

  <!-- Firebase SDKs -->
  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

  <script>
    // ✅ Firebase Configuration
    const firebaseConfig = {
      apiKey: "YOUR_API_KEY",
      authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
      databaseURL: "https://YOUR_PROJECT_ID.firebaseio.com",
      projectId: "YOUR_PROJECT_ID",
      storageBucket: "YOUR_PROJECT_ID.appspot.com",
      messagingSenderId: "SENDER_ID",
      appId: "APP_ID"
    };

    // ✅ Initialize Firebase
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    // ✅ Upload Text to Firebase
    function uploadText() {
      const text = document.getElementById("notepad").value.trim();
      if (text.length === 0) {
        alert("Please enter some text before uploading.");
        return;
      }
      const newRef = db.ref("publicTexts").push();
      newRef.set({
        text: text,
        timestamp: Date.now()
      });
      document.getElementById("notepad").value = "";
      alert("Uploaded successfully!");
    }

    // ✅ Display all uploaded public texts
    function loadPublicTexts() {
      const container = document.getElementById("publicTexts");
      db.ref("publicTexts").orderByChild("timestamp").limitToLast(100).on("value", (snapshot) => {
        container.innerHTML = ""; // clear
        const data = snapshot.val();
        if (data) {
          const entries = Object.values(data).reverse(); // latest first
          entries.forEach(entry => {
            const div = document.createElement("div");
            div.className = "text-box";
            div.textContent = entry.text;
            container.appendChild(div);
          });
        } else {
          container.innerHTML = "<p>No public texts yet.</p>";
        }
      });
    }

    // ✅ Download Text
    function downloadText() {
      const text = document.getElementById("notepad").value;
      const blob = new Blob([text], { type: "text/plain" });
      const link = document.createElement("a");
      link.download = "notepad.txt";
      link.href = URL.createObjectURL(blob);
      link.click();
    }

    // ✅ Copy Text
    function copyText() {
      const textarea = document.getElementById("notepad");
      textarea.select();
      document.execCommand("copy");
      alert("Text copied!");
    }

    // ✅ Clear Text
    function clearText() {
      document.getElementById("notepad").value = "";
    }

    // ✅ Upload File (.txt)
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

    // Load public texts on page load
    window.onload = loadPublicTexts;
  </script>

</body>
</html>
