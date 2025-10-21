<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Online Notepad - M.SaimMAJOKA</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f4f4f4;
      padding: 20px;
      text-align: center;
    }

    h1 {
      color: #333;
    }

    textarea {
      width: 90%;
      height: 400px;
      padding: 10px;
      font-size: 16px;
      resize: none;
      margin-top: 20px;
    }

    .buttons {
      margin-top: 20px;
    }

    button, input[type="file"] {
      padding: 10px 15px;
      margin: 5px;
      font-size: 16px;
      cursor: pointer;
    }

    footer {
      margin-top: 30px;
      color: #555;
    }
  </style>
</head>
<body>

  <h1>Online Notepad</h1>
  <p>Write, Upload, Download & Copy Text</p>

  <textarea id="notepad" placeholder="Start typing here..."></textarea>

  <div class="buttons">
    <input type="file" id="fileInput" accept=".txt">
    <button onclick="downloadText()">Download Text</button>
    <button onclick="copyText()">Copy to Clipboard</button>
    <button onclick="clearText()">Clear Notepad</button>
  </div>

  <footer>
    <p>Created by: <strong>M.SaimMAJOKA</strong></p>
  </footer>

  <script>
    // Upload text file
    document.getElementById('fileInput').addEventListener('change', function(event) {
      const file = event.target.files[0];
      if (file && file.type === "text/plain") {
        const reader = new FileReader();
        reader.onload = function(e) {
          document.getElementById('notepad').value = e.target.result;
        };
        reader.readAsText(file);
      } else {
        alert("Please upload a valid .txt file.");
      }
    });

    // Download text as .txt
    function downloadText() {
      const text = document.getElementById('notepad').value;
      const blob = new Blob([text], { type: 'text/plain' });
      const link = document.createElement('a');
      link.download = 'notepad.txt';
      link.href = URL.createObjectURL(blob);
      link.click();
    }

    // Copy text to clipboard
    function copyText() {
      const textArea = document.getElementById('notepad');
      textArea.select();
      document.execCommand('copy');
      alert("Text copied to clipboard!");
    }

    // Clear the notepad
    function clearText() {
      document.getElementById('notepad').value = '';
    }
  </script>

</body>
</html>
