# index.html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>App Roteiros Premium</title>

<style>
:root {
  --bg: #0f172a;
  --card: #111827;
  --primary: #f97316;
  --secondary: #22c55e;
  --text: #f8fafc;
  --muted: #94a3b8;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: Arial;
}

body {
  background: var(--bg);
  color: var(--text);
}

/* NAVBAR */
.navbar {
  position: sticky;
  top: 0;
  background: var(--bg);
  padding: 15px;
  display: flex;
  justify-content: space-between;
  border-bottom: 1px solid #1f2937;
}

.navbar h2 {
  color: var(--primary);
}

/* PROGRESS */
.progress {
  height: 6px;
  background: #1f2937;
}

.progress-fill {
  height: 100%;
  width: 0;
  background: var(--primary);
}

/* GRID */
.grid {
  padding: 15px;
  display: grid;
  gap: 12px;
}

/* CARD */
.card {
  background: var(--card);
  padding: 15px;
  border-radius: 12px;
  border: 1px solid #1f2937;
  cursor: pointer;
  transition: 0.2s;
}

.card:hover {
  transform: scale(1.02);
  border-color: var(--primary);
}

.card small {
  color: var(--muted);
}

/* MODAL */
.modal {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.8);
  display: none;
  justify-content: center;
  align-items: center;
}

.modal-content {
  background: var(--card);
  padding: 20px;
  border-radius: 15px;
  width: 90%;
  max-width: 500px;
}

button {
  margin-top: 10px;
  padding: 10px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}

.btn-primary {
  background: var(--primary);
  color: white;
}

.btn-secondary {
  background: transparent;
  color: var(--muted);
}
</style>
</head>

<body>

<div class="navbar">
  <h2>Roteiros</h2>
  <div id="user"></div>
</div>

<div class="progress">
  <div class="progress-fill" id="progressFill"></div>
</div>

<div class="grid" id="grid"></div>

<div class="modal" id="modal">
  <div class="modal-content" id="modalContent"></div>
</div>

<script>

// ===== LOGIN =====
let user = localStorage.getItem("user");

if (!user) {
  user = prompt("Digite seu nome:");
  localStorage.setItem("user", user);
}

document.getElementById("user").innerText = user;

// ===== DADOS =====
const scripts = [
  {
    title: "Pare de usar o celular de manhã",
    hook: "Isso destrói seu foco diário",
    text: "Evite celular na primeira hora do dia"
  },
  {
    title: "Regra dos 2 minutos",
    hook: "Pare de procrastinar hoje",
    text: "Comece tarefas por 2 minutos"
  },
  {
    title: "Você está desidratado",
    hook: "Seu cérebro está lento",
    text: "Beba água regularmente"
  }
];

// ===== FUNÇÕES =====
function getFavs() {
  return JSON.parse(localStorage.getItem("favs") || "[]");
}

function getSeen() {
  return JSON.parse(localStorage.getItem("seen") || "[]");
}

function toggleFav(i) {
  let favs = getFavs();

  if (favs.includes(i)) {
    favs = favs.filter(f => f !== i);
  } else {
    favs.push(i);
  }

  localStorage.setItem("favs", JSON.stringify(favs));
  render();
}

function markSeen(i) {
  let seen = getSeen();

  if (!seen.includes(i)) {
    seen.push(i);
    localStorage.setItem("seen", JSON.stringify(seen));
  }
}

function updateProgress() {
  let seen = getSeen();
  let percent = (seen.length / scripts.length) * 100;
  document.getElementById("progressFill").style.width = percent + "%";
}

// ===== RENDER =====
function render() {
  const grid = document.getElementById("grid");
  grid.innerHTML = "";

  let favs = getFavs();
  let seen = getSeen();

  scripts.forEach((s, i) => {
    const card = document.createElement("div");
    card.className = "card";

    const fav = favs.includes(i) ? "⭐" : "☆";
    const done = seen.includes(i) ? "✔" : "";

    card.innerHTML = `
      <h3>${fav} ${s.title} ${done}</h3>
      <small>${s.hook}</small>
      <br>
      <button onclick="event.stopPropagation();toggleFav(${i})">
        Favoritar
      </button>
    `;

    card.onclick = () => openModal(i);

    grid.appendChild(card);
  });

  updateProgress();
}

// ===== MODAL =====
function openModal(i) {
  const s = scripts[i];
  markSeen(i);

  document.getElementById("modalContent").innerHTML = `
    <h2>${s.title}</h2>
    <p>${s.text}</p>

    <button class="btn-primary" onclick="copyText('${s.text}')">
      📋 Copiar
    </button>

    <button class="btn-secondary" onclick="closeModal()">
      Fechar
    </button>
  `;

  document.getElementById("modal").style.display = "flex";
}

function closeModal() {
  document.getElementById("modal").style.display = "none";
  render();
}

function copyText(text) {
  navigator.clipboard.writeText(text);
  alert("Copiado!");
}

// INIT
render();

</script>

</body>
</html>