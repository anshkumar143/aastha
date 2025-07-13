<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Our Romantic Memory Book</title>
<style>
  body {
    margin: 0; padding: 20px;
    font-family: 'Georgia', serif;
    background: #fce4ec;
    display: flex; justify-content: center; align-items: center;
    min-height: 100vh;
  }
  #container {
    perspective: 1500px;
    width: 700px; height: 500px;
    position: relative;
  }
  #passwordSection {
    max-width: 400px;
    margin: auto;
    text-align: center;
  }
  #passwordSection input[type="password"] {
    padding: 10px;
    font-size: 18px;
    width: 80%;
    border-radius: 8px;
    border: 1px solid #c72c48;
    margin-bottom: 10px;
  }
  #passwordSection button {
    padding: 10px 20px;
    font-size: 18px;
    background: #c72c48;
    border: none;
    color: white;
    border-radius: 8px;
    cursor: pointer;
  }
  #passwordSection button:hover {
    background: #9e1e3a;
  }
  #wrongPassword {
    color: red;
    margin-top: 10px;
    display: none;
  }

  /* Book styles */
  #book {
    width: 700px;
    height: 500px;
    background: linear-gradient(145deg, #ffe6f0 0%, #ffccdb 100%);
    box-shadow: 0 15px 30px rgba(199,44,72,0.3);
    border-radius: 20px;
    position: relative;
    overflow: hidden;
    display: none;
  }

  .page {
    position: absolute;
    width: 350px; /* half of book width */
    height: 100%;
    background: white;
    border-radius: 0 20px 20px 0;
    box-shadow: inset 0 0 15px #ffbfd4;
    padding: 20px;
    box-sizing: border-box;
    overflow-y: auto;
    font-size: 18px;
    line-height: 1.5;
  }

  .page.left {
    left: 0;
    border-radius: 20px 0 0 20px;
    box-shadow: inset 0 0 15px #ffd9e2;
  }

  .page.right {
    right: 0;
  }

  .page h2 {
    text-align: center;
    color: #c72c48;
    margin-top: 0;
  }

  .entry {
    margin-bottom: 15px;
    padding: 10px;
    border-radius: 12px;
  }
  .entry.me {
    background: #ffe6e9;
    border: 1px solid #d85a71;
  }
  .entry.her {
    background: #e6faf4;
    border: 1px solid #55bca3;
  }

  .timestamp {
    font-size: 12px;
    color: #999;
    text-align: right;
  }

  /* Page Flip Buttons */
  #controls {
    position: absolute;
    bottom: 20px;
    width: 100%;
    text-align: center;
  }
  #controls button {
    background: #c72c48;
    border: none;
    color: white;
    padding: 12px 25px;
    margin: 0 10px;
    font-size: 16px;
    border-radius: 8px;
    cursor: pointer;
    user-select: none;
  }
  #controls button:disabled {
    background: #f5a6b8;
    cursor: default;
  }
  #controls button:hover:not(:disabled) {
    background: #9e1e3a;
  }

  /* Add entry form */
  #addEntry {
    position: absolute;
    bottom: 70px;
    width: 660px;
    left: 20px;
    background: #ffe6f0;
    border-radius: 15px;
    padding: 10px 15px;
    box-sizing: border-box;
    display: flex;
    gap: 10px;
    align-items: center;
  }
  #addEntry select, #addEntry textarea {
    font-size: 16px;
    border-radius: 8px;
    border: 1px solid #c72c48;
  }
  #addEntry select {
    width: 100px;
    padding: 8px;
  }
  #addEntry textarea {
    flex-grow: 1;
    height: 50px;
    padding: 8px;
    resize: none;
  }
  #addEntry button {
    width: 120px;
    padding: 10px;
  }

  /* Scrollbar styling */
  .page::-webkit-scrollbar {
    width: 8px;
  }
  .page::-webkit-scrollbar-thumb {
    background: #c72c48;
    border-radius: 4px;
  }
  .page::-webkit-scrollbar-track {
    background: #ffe6f0;
  }
</style>
</head>
<body>

<div id="passwordSection">
  <h1>🔒 Enter Password to Open Memory Book</h1>
  <input type="password" id="passwordInput" placeholder="Password" />
  <br />
  <button onclick="unlockBook()">Open Book</button>
  <p id="wrongPassword">Incorrect password. Try again.</p>
</div>

<div id="container">
  <div id="book">
    <div class="page left" id="leftPage">
      <!-- Left page content -->
    </div>
    <div class="page right" id="rightPage">
      <!-- Right page content -->
    </div>

    <div id="addEntry">
      <select id="authorSelect" aria-label="Select author">
        <option value="me">Me</option>
        <option value="her">Aastha</option>
      </select>
      <textarea id="entryText" placeholder="Write your memory here..." aria-label="Memory text"></textarea>
      <button onclick="addMemory()">Add Memory</button>
    </div>

    <div id="controls">
      <button onclick="prevPage()" id="prevBtn" disabled>← Previous</button>
      <button onclick="nextPage()" id="nextBtn">Next →</button>
    </div>
  </div>
</div>

<script>
  const PASSWORD = "aastha";
  const memoriesPerPage = 4; // two pages, each page shows 2 memories
  let memories = [];
  let currentPageIndex = 0;

  // The special first meeting memory always first
  const firstMeeting = {
    who: "her",
    content: "I remember your smile the first time I met you in September 2024. Everything felt magical and new.",
    timestamp: "September 2024",
  };

  function unlockBook() {
    const input = document.getElementById("passwordInput").value;
    const wrongMsg = document.getElementById("wrongPassword");
    if(input === PASSWORD) {
      document.getElementById("passwordSection").style.display = "none";
      document.getElementById("book").style.display = "block";
      wrongMsg.style.display = "none";

      loadMemories();
      currentPageIndex = 0;
      showPage(currentPageIndex);
    } else {
      wrongMsg.style.display = "block";
    }
  }

  function loadMemories() {
    // Load memories from localStorage or start fresh with firstMeeting
    memories = JSON.parse(localStorage.getItem("memoryBookMemories")) || [];
    if(!memories.length){
      memories = [firstMeeting];
      localStorage.setItem("memoryBookMemories", JSON.stringify(memories));
    }
  }

  function saveMemories() {
    localStorage.setItem("memoryBookMemories", JSON.stringify(memories));
  }

  function addMemory() {
    const author = document.getElementById("authorSelect").value;
    const text = document.getElementById("entryText").value.trim();
    if(text === "") {
      alert("Please write a memory before adding!");
      return;
    }
    const newMemory = {
      who: author,
      content: text,
      timestamp: new Date().toLocaleString(),
    };
    memories.unshift(newMemory);
    saveMemories();
    document.getElementById("entryText").value = "";
    currentPageIndex = 0;
    showPage(currentPageIndex);
  }

  function showPage(pageIndex) {
    const leftPage = document.getElementById("leftPage");
    const rightPage = document.getElementById("rightPage");
    leftPage.innerHTML = "";
    rightPage.innerHTML = "";

    // Calculate start index for this spread (2 memories per page side, total 4 memories per spread)
    let startIndex = pageIndex * memoriesPerPage;

    function createMemoryElement(memory) {
      const div = document.createElement("div");
      div.className = "entry " + (memory.who === "me" ? "me" : "her");
      div.innerHTML = `
        <p>${memory.content}</p>
        <div class="timestamp">${memory.timestamp}</div>
      `;
      return div;
    }

    // Left page shows memories at startIndex and startIndex + 1
    if(memories[startIndex]) leftPage.appendChild(createMemoryElement(memories[startIndex]));
    if(memories[startIndex + 1]) leftPage.appendChild(createMemoryElement(memories[startIndex + 1]));

    // Right page shows memories at startIndex + 2 and startIndex + 3
    if(memories[startIndex + 2]) rightPage.appendChild(createMemoryElement(memories[startIndex + 2]));
    if(memories[startIndex + 3]) rightPage.appendChild(createMemoryElement(memories[startIndex + 3]));

    // Update button states
    document.getElementById("prevBtn").disabled = pageIndex <= 0;
    document.getElementById("nextBtn").disabled = (startIndex + memoriesPerPage) >= memories.length;
  }

  function nextPage() {
    if ((currentPageIndex + 1) * memoriesPerPage < memories.length) {
      currentPageIndex++;
      showPage(currentPageIndex);
    }
  }

  function prevPage() {
    if (currentPageIndex > 0) {
      currentPageIndex--;
      showPage(currentPageIndex);
    }
  }
</script>

</body>
</html>
