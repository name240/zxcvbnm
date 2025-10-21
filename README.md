# zxcvbnm
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>WIERTE - Simple Text Pad</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    :root {
      --bg-color: #f0f4ff;
      --primary-color: #4b6cb7;
      --secondary-color: #182848;
      --text-color: #333;
      --button-bg: #4b6cb7;
      --button-hover: #3a539b;
      --button-text: #fff;
    }

    body {
      font-family: 'Segoe UI', sans-serif;
      margin: 0;
      padding: 0;
      background: var(--bg-color);
      color: var(--text-color);
      display: flex;
      flex-direction: column;
      align-items: center;
      min-height: 100vh;
    }

    header {
      background: linear-gradient(to right, var(--primary-color), var(--secondary-color));
      width: 100%;
      padding: 20px 0;
      text-align: center;
      color: white;
    }

    h1 {
      margin: 0;
      font-size: 2em;
      letter-spacing: 1px;
    }

    main {
      padding: 20px;
      width: 100%;
      max-width: 800px;
      flex-grow: 1;
    }

    textarea {
      width: 100%;
      height: 400px;
      padding: 15px;
      font-size: 16px;
      border: 1px solid #ccc;
      border-radius: 8px;
      box-sizing: border-box;
      resize: vertical;
      background: #fff;
      box-shadow: 0 4px 6px rgba(0,0,0,0.1);
    }

    .buttons {
      margin-top: 15px;
      display: flex;
      gap: 10px;
      flex-wrap: wrap;
    }

    button {
      padding: 10px 20px;
      border: none;
      border-radius: 6px;
      background-color: var(--button-bg);
      color: var(--button-text);
      cursor: pointer;
      font-size: 14px;
      transition: background 0.3s;
    }

    button:hover {
      background-color: var(--button-hover);
    }

    footer {
      text-align: center;
      padding: 15px;
      font-size: 14px;
      color: #666;
      background: #e4e9f2;
      width: 100%;
    }

    @media (max-width: 600px) {
      textarea {
        height: 300px;
      }
    }
  </style>
</head>
<body>

  <header>
    <h1>📝 WIERTE - Simple Text Pad</h1>
  </header>

  <main>
    <textarea id="note" placeholder="Write or paste your text here..."></textarea>
    <div class="buttons">
      <button onclick="downloadNote()">💾 Download</button>
      <button onclick="copyNote()">📋 Copy</button>
      <button onclick="clearNote()">🗑️ Clear</button>
    </div>
  </main>

  <footer>
    Created by: <strong>M. Saim Majoka</strong>
  </footer>

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

    function copyNote() {
      textarea.select();
      document.execCommand('copy');
      alert('Text copied to clipboard!');
    }

    function clearNote() {
      if (confirm("Are you sure you want to clear the note?")) {
        textarea.value = '';
        localStorage.removeItem('wierte_note');
      }
    }
  </script>

</body>
</html>

