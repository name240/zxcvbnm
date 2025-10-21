# zxcvbnm
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>WIERTE - Simple Text Pad</title>
  <style>
    body {
      font-family: sans-serif;
      background: #f4f4f4;
      padding: 20px;
      max-width: 800px;
      margin: auto;
    }
    h1 {
      text-align: center;
      color: #333;
    }
    textarea {
      width: 100%;
      height: 400px;
      padding: 10px;
      font-size: 16px;
      box-sizing: border-box;
      resize: vertical;
    }
    button {
      margin-top: 10px;
      padding: 10px 15px;
      font-size: 14px;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <h1>📝 WIERTE - Text Pad</h1>
  <textarea id="note" placeholder="Write or paste your text here..."></textarea>
  <br>
  <button onclick="downloadNote()">💾 Download Text</button>
  <button onclick="clearNote()">🗑️ Clear</button>
  <button onclick="copyNote()">📋 Copy</button>

  <script>
    const textarea = document.getElementById('note');

    // Load saved note
    window.onload = () => {
      const saved = localStorage.getItem('wierte_note');
      if (saved) textarea.value = saved;
    };

    // Auto-save note
    textarea.addEventListener('input', () => {
      localStorage.setItem('wierte_note', textarea.value);
    });

    function downloadNote() {
      const text = textarea.value;
      const blob = new Blob([text], { type: 'text/plain' });
      const link = document.createElement('a');
      link.href = URL.createObjectURL(blob);
      link.download = 'wierte-note.txt';
      link.click();
    }

    function clearNote() {
      if (confirm("Are you sure you want to clear the note?")) {
        textarea.value = '';
        localStorage.removeItem('wierte_note');
      }
    }

    function copyNote() {
      textarea.select();
      document.execCommand('copy');
      alert('Text copied to clipboard!');
    }
  </script>
</body>
</html>
