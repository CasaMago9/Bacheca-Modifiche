# Bacheca-Modifiche
<!DOCTYPE html>
<html lang="it">
<head>
  <meta charset="UTF-8">
  <title>Bacheca Utenti</title>
  <style>
    body { font-family: sans-serif; max-width: 600px; margin: 40px auto; padding: 20px; background: #f5f5f5; }
    input, button { padding: 10px; margin: 5px 0; width: 100%; }
    .user { background: white; padding: 10px; margin: 10px 0; border-radius: 5px; cursor: pointer; }
  </style>
</head>
<body>
  <h2>Lista Utenti</h2>
  <input type="text" id="usernameInput" placeholder="Inserisci un nome utente...">
  <button onclick="addUser()">Aggiungi Utente</button>
  <div id="userList"></div>

  <!-- Firebase -->
  <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-database-compat.js"></script>

  <script>
    const firebaseConfig = {
      apiKey: "API_KEY",
      authDomain: "PROJECT_ID.firebaseapp.com",
      databaseURL: "https://PROJECT_ID.firebaseio.com",
      projectId: "PROJECT_ID",
      storageBucket: "PROJECT_ID.appspot.com",
      messagingSenderId: "SENDER_ID",
      appId: "APP_ID"
    };

    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    const userList = document.getElementById("userList");

    function addUser() {
      const name = document.getElementById("usernameInput").value.trim();
      if (name !== "") {
        db.ref("users").push({ name });
        document.getElementById("usernameInput").value = "";
      }
    }

    db.ref("users").on("child_added", (data) => {
      const user = data.val();
      const div = document.createElement("div");
      div.className = "user";
      div.innerText = user.name;
      div.onclick = () => {
        window.location.href = `bacheca.html?userId=${data.key}&username=${encodeURIComponent(user.name)}`;
      };
      userList.appendChild(div);
    });
  </script>
</body>
</html>

