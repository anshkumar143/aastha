<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Our Memory Book</title>
  <style>
    body {
      font-family: 'Georgia', serif;
      background: linear-gradient(to bottom, #fff5f9 0%, #ffe6f2 100%);
      color: #333;
      max-width: 700px;
      margin: auto;
      padding: 20px;
    }
    h1, h2 {
      text-align: center;
      color: #c72c48;
    }
    #countdown { text-align: center; font-size: 20px; color: #a8325e; margin-bottom: 20px; }
    input, textarea, select {
      width: 100%; padding: 12px; margin: 10px 0;
      box-sizing: border-box; border-radius: 6px; border: 1px solid #ccc;
      font-size: 16px;
    }
    button {
      width: 100%; padding: 14px; font-size: 18px;
      background: #c72c48; color: white; border: none;
      border-radius: 6px; cursor: pointer;
    }
    button:hover { background: #9e1e3a; }
    .entry { background: #ffffffcc; padding: 12px; margin-top: 15px;
             border-radius: 8px; border: 1px solid #ddd; }
    .entry.me { background: #ffe6e9; }
    .entry.her { background: #e6faf4; }
    .timestamp { font-size: 12px; color: #666; text-align: right; }
    #diary { display: none; }
    #wrong { color: red; text-align: center; display: none; }
    .heart { color: #c72c48; font-size: 24px; }
  </style>
</head>
<body>

  <h1>📖 Our Best Memories 📖</h1>

  <!-- Password -->
  <div id="pwdSection">
    <p>Enter our secret password to open the book:</p>
    <input type="password" id="pwd" placeholder="Password" />
    <button onclick="unlock()">Open Memory Book</button>
    <p id="wrong">Wrong password, try again 💔</p>
  </div>

  <!-- Main Book -->
  <div id="diary">
    <h2 class="heart">💕 Aastha & Me</h2>
    <p style="text-align: center; font-style: italic;">
      "Our journey began in <strong>September 2024</strong>—the moment I saw you, everything felt magical..."
    </p>
    <div class="entry her">
      <strong>Aastha (First Meeting):</strong>
      <p>I remember your smile the first time I met you—sunlight caught in your eyes…</p>
      <div class="timestamp">September 2024</div>
    </div>

    <div style="margin-top: 20px;">
      <label>Who’s writing?</label>
      <select id="who">
        <option value="me">Me</option>
        <option value="her">Aastha</option>
      </select>

      <textarea id="moment" placeholder="Write our next beautiful memory..."></textarea>
      <button onclick="addMoment()">Add to Our Book</button>
    </div>

    <h2>🌟 Our Favorite Moments</h2>
    <div id="moments"></div>
  </div>

  <script>
    const PASSWORD = "aastha";
    function unlock() {
      if (document.getElementById("pwd").value === PASSWORD) {
        document.getElementById("pwdSection").style.display = "none";
        document.getElementById("diary").style.display = "block";
        document.getElementById("wrong").style.display = "none";
        loadMoments();
      } else {
        document.getElementById("wrong").style.display = "block";
      }
    }

    function addMoment() {
      const who = document.getElementById("who").value;
      const text = document.getElementById("moment").value.trim();
      if (!text) { alert("Share your moment 💖"); return; }
      const entry = { who, content: text, time: new Date().toLocaleString() };

      let arr = JSON.parse(localStorage.getItem("moments_book")) || [];
      arr.unshift(entry);
      localStorage.setItem("moments_book", JSON.stringify(arr));
      document.getElementById("moment").value = "";
      loadMoments();
    }

    function loadMoments() {
      const container = document.getElementById("moments");
      container.innerHTML = '';
      const arr = JSON.parse(localStorage.getItem("moments_book")) || [];
      arr.forEach(e => {
        const d = document.createElement("div");
        d.className = "entry " + (e.who === "me" ? "me" : "her");
        d.innerHTML = `<strong>${e.who === "me" ? "Me" : "Aastha"}:</strong>
                       <p>${e.content}</p>
                       <div class="timestamp">${e.time}</div>`;
        container.appendChild(d);
      });
    }
  </script>

</body>
</html>
