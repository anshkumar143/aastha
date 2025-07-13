<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Our Romantic Book</title>
  <style>
    body {
      margin: 0;
      font-family: 'Georgia', serif;
      background: #fff0f5;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
    }
    .lock-screen {
      text-align: center;
    }
    .lock-screen input {
      padding: 10px;
      font-size: 16px;
      border-radius: 8px;
      border: 1px solid #c72c48;
      margin-bottom: 10px;
    }
    .lock-screen button {
      background: #c72c48;
      color: white;
      border: none;
      padding: 10px 20px;
      font-size: 16px;
      border-radius: 8px;
      cursor: pointer;
    }

    .book {
      perspective: 2000px;
      width: 90%;
      max-width: 700px;
      height: 500px;
      position: relative;
      display: none;
    }

    .page {
      width: 100%;
      height: 100%;
      background: linear-gradient(to bottom right, #fff, #ffe6f0);
      position: absolute;
      border-radius: 10px;
      padding: 30px;
      box-sizing: border-box;
      backface-visibility: hidden;
      transition: transform 1s;
      box-shadow: 0 5px 15px rgba(0,0,0,0.2);
      overflow-y: auto;
    }

    .page h1 {
      text-align: center;
      color: #c72c48;
    }

    .page p {
      font-size: 20px;
      text-align: center;
      color: #333;
      line-height: 1.5;
    }

    .buttons {
      position: absolute;
      bottom: 10px;
      width: 100%;
      text-align: center;
    }

    .buttons button {
      background: #c72c48;
      color: white;
      border: none;
      padding: 10px 20px;
      margin: 0 10px;
      border-radius: 6px;
      font-size: 16px;
      cursor: pointer;
    }

    #photoDisplay img {
      max-width: 100%;
      border: 3px solid #c72c48;
      border-radius: 10px;
      margin-top: 20px;
    }

    .flipped {
      transform: rotateY(-180deg);
    }

    .page2, .page3 {
      transform: rotateY(180deg);
      z-index: 1;
    }

    .page1 {
      z-index: 2;
    }

  </style>
</head>
<body>

<!-- Lock screen -->
<div class="lock-screen" id="lockScreen">
  <h2>🔒 Enter Password to Unlock Love Book</h2>
  <input type="password" id="passwordInput" placeholder="Password" />
  <br>
  <button onclick="checkPassword()">Unlock</button>
  <p id="wrong" style="color:red; display:none;">Wrong password. Try again 💔</p>
</div>

<!-- Book container -->
<div class="book" id="book">
  <!-- Page 1 -->
  <div class="page page1" id="page1">
    <h1>💖 Welcome to Our Love Story 💖</h1>
    <p>
      From the first moment our eyes met, my world changed forever. Every smile, every word, every silence between us told its own story. This book holds pieces of my heart, moments shared, and love eternal. Welcome to the journey of us.
    </p>
    <div class="buttons">
      <button onclick="flipPage(1)">Next →</button>
    </div>
  </div>

  <!-- Page 2 -->
  <div class="page page2" id="page2">
    <h1>📸 Add a Photo Memory</h1>
    <p>Upload a photo that captures our moment together:</p>
    <input type="file" id="photoInput" accept="image/*" onchange="previewPhoto()"/>
    <div id="photoDisplay"></div>
    <div class="buttons">
      <button onclick="flipPage(-1)">← Back</button>
      <button onclick="flipPage(1)">Next →</button>
    </div>
  </div>

  <!-- Page 3 -->
  <div class="page page3" id="page3">
    <h1>❤️ My Lucky Charm ❤️</h1>
    <p>
      Thank you soooo much, my lucky charm, for coming into my life. Your love is my light, my strength, my everything.
    </p>
    <p style="font-size: 24px; color: #c72c48; margin-top: 30px;">
      I love you soooo much ❤️🔥 <br/>
      Forever yours 💋
    </p>
    <div class="buttons">
      <button onclick="flipPage(-1)">← Back</button>
    </div>
  </div>
</div>

<script>
  const PASSWORD = "aastha";
  let currentPage = 1;

  function checkPassword() {
    const input = document.getElementById("passwordInput").value;
    if (input === PASSWORD) {
      document.getElementById("lockScreen").style.display = "none";
      document.getElementById("book").style.display = "block";
    } else {
      document.getElementById("wrong").style.display = "block";
    }
  }

  function flipPage(direction) {
    const book = document.getElementById("book");
    const page1 = document.getElementById("page1");
    const page2 = document.getElementById("page2");
    const page3 = document.getElementById("page3");

    if (direction === 1 && currentPage === 1) {
      page1.classList.add("flipped");
    }
    if (direction === 1 && currentPage === 2) {
      page2.classList.add("flipped");
    }
    if (direction === -1 && currentPage === 3) {
      page2.classList.remove("flipped");
    }
    if (direction === -1 && currentPage === 2) {
      page1.classList.remove("flipped");
    }

    currentPage += direction;
  }

  function previewPhoto() {
    const input = document.getElementById("photoInput");
    const display = document.getElementById("photoDisplay");
    display.innerHTML = '';
    if (input.files && input.files[0]) {
      const reader = new FileReader();
      reader.onload = function(e) {
        const img = document.createElement("img");
        img.src = e.target.result;
        display.appendChild(img);
      }
      reader.readAsDataURL(input.files[0]);
    }
  }
</script>

</body>
</html>
