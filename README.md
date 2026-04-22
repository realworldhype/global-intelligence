idex.html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>GLOBAL INTELLIGENCE TERMINAL</title>

<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;500;700&display=swap" rel="stylesheet">

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<style>
body {
    margin: 0;
    font-family: Inter;
    background: radial-gradient(circle at top, #0f0f0f, #000);
    color: white;
    overflow: hidden;
}

/* HEADER */
header {
    padding: 18px;
    display: flex;
    justify-content: space-between;
    border-bottom: 1px solid #222;
    background: rgba(10,10,10,0.9);
}

.title {
    font-size: 18px;
    font-weight: 700;
    letter-spacing: 2px;
    color: #00ffcc;
}

/* LAYOUT */
.container {
    display: flex;
    height: calc(100vh - 60px);
}

/* MAP */
#map {
    flex: 2;
}

/* PANEL */
.panel {
    flex: 1;
    background: rgba(15,15,15,0.95);
    padding: 15px;
    overflow-y: auto;
    border-left: 1px solid #222;
}

/* CARDS */
.card {
    background: linear-gradient(145deg, #111, #0a0a0a);
    border: 1px solid #222;
    padding: 12px;
    border-radius: 12px;
    margin-bottom: 12px;
    transition: 0.2s;
}

.card:hover {
    transform: scale(1.02);
    border-color: #00ffcc;
}

/* AI BOX */
.ai {
    font-size: 12px;
    color: #aaa;
    margin-top: 8px;
}

/* SEARCH */
input {
    width: 100%;
    padding: 10px;
    margin-bottom: 10px;
    background: #111;
    border: 1px solid #333;
    color: white;
    border-radius: 8px;
}
</style>

<link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css"/>
<script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>

</head>

<body>

<header>
<div class="title">GLOBAL INTELLIGENCE TERMINAL</div>
<div id="clock"></div>
</header>

<div class="container">

<div id="map"></div>

<div class="panel">

<input id="search" placeholder="Search stock (AAPL, TSLA, BTC...)" />

<h3>📊 LIVE MARKETS</h3>
<div id="stocks"></div>

<h3>📰 GLOBAL NEWS</h3>
<div id="news"></div>

</div>
</div>

<script>

// 🌍 MAP
var map = L.map('map').setView([20, 0], 2);
L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);

// ⏰ CLOCK
function updateClock(){
    document.getElementById("clock").innerText = new Date().toLocaleTimeString();
}
setInterval(updateClock,1000);

// 📊 STOCK API
const apiKey = "d7kc2n1r01qiqbctqjpgd7kc2n1r01qiqbctqjq0";

// default stocks
let stocks = ["AAPL","TSLA","MSFT","NVDA"];

// 🔥 LOAD STOCKS LIVE
async function loadStocks() {
    const container = document.getElementById("stocks");
    container.innerHTML = "";

    for (let s of stocks) {
        try {
            const res = await fetch(`https://finnhub.io/api/v1/quote?symbol=${s}&token=${apiKey}`);
            const data = await res.json();

            const price = data.c;
            const change = data.d;

            const div = document.createElement("div");
            div.className = "card";

            div.innerHTML = `
                <b>${s}</b><br>
                $${price}
                <div style="color:${change>=0?'lime':'red'}">
                    ${change>=0?'▲':'▼'} ${change}
                </div>
                <div class="ai">🤖 AI: analyzing market movement...</div>
            `;

            div.onclick = () => {
                div.innerHTML += `
                <div class="ai">
                📊 AI ANALYSIS:<br>
                ${s} is currently ${change>=0?'bullish 📈':'bearish 📉'}.<br>
                Market sentiment + global news likely influencing movement.
                </div>`;
            };

            container.appendChild(div);

        } catch(e){
            console.log(e);
        }
    }
}

// 🔄 AUTO REFRESH EVERY 5 SECONDS
setInterval(loadStocks, 5000);
loadStocks();

// 🔍 SEARCH STOCK
document.getElementById("search").addEventListener("keydown", (e)=>{
    if(e.key === "Enter"){
        stocks.push(e.target.value.toUpperCase());
        loadStocks();
        e.target.value = "";
    }
});

// 📰 NEWS (simple)
async function loadNews(){
    const res = await fetch("https://newsapi.org/v2/top-headlines?language=en&pageSize=5&apiKey=97a32a44f39d479b8e3af9c445fa07d0");
    const data = await res.json();

    const container = document.getElementById("news");
    container.innerHTML = "";

    data.articles.forEach(a=>{
        const div = document.createElement("div");
        div.className = "card";
        div.innerHTML = `
            <b>${a.title}</b>
            <div class="ai">🤖 AI: impact unknown, analyzing global sentiment...</div>
        `;
        container.appendChild(div);
    });
}

loadNews();

</script>

</body>
</html>
