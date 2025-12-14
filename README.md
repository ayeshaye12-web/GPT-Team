<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0">
<title>HydroSmart iOS 26 - Irigasi IoT</title>

<script src="https://cdn.tailwindcss.com"></script>
<script src="https://unpkg.com/lucide@latest"></script>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@200;300;400;500;600;700&display=swap" rel="stylesheet">

<style>

/* ================= GLOBAL SAFETY ================= */
html, body {
    max-width: 100%;
    overflow-x: hidden !important;
}

body {
    font-family: 'Inter', sans-serif;
    color: white;
    min-height: 100vh;
    transition: color 1.5s ease;
}

/* ================= AMBIENT BACKGROUND ================= */
.ambient-bg {
    position: fixed;
    inset: 0;
    z-index: -1;
    transition: background 1.5s ease;
}

.orb {
    position: absolute;
    border-radius: 50%;
    filter: blur(80px);
    opacity: 0.6;
    animation: float 10s infinite ease-in-out alternate;
}

.orb-1 { width: 400px; height: 400px; top: -50px; left: -50px; }
.orb-2 { width: 500px; height: 500px; bottom: -100px; right: -100px; }
.orb-3 { width: 300px; height: 300px; top: 40%; left: 40%; }

@keyframes float {
    from { transform: translate(0,0) scale(1); }
    to { transform: translate(30px,50px) scale(1.1); }
}

/* ================= GLASS PANEL ================= */
.glass-panel {
    background: rgba(255,255,255,0.03);
    backdrop-filter: blur(40px) saturate(180%);
    -webkit-backdrop-filter: blur(40px) saturate(180%);
    border: 1px solid rgba(255,255,255,0.15);
    border-radius: 32px;
    box-shadow: 0 8px 32px rgba(0,0,0,0.3);
    max-width: 100%;
    overflow: hidden;
}

body.light-theme .glass-panel {
    background: rgba(0,0,0,0.05);
    border: 1px solid rgba(0,0,0,0.1);
}

/* ================= BUTTON ================= */
.liquid-btn {
    background: linear-gradient(135deg, rgba(255,255,255,0.1), rgba(255,255,255,0.05));
    border: 1px solid rgba(255,255,255,0.2);
    backdrop-filter: blur(10px);
    transition: 0.3s;
}

.liquid-btn.active {
    background: linear-gradient(135deg,#3b82f6,#06b6d4);
    box-shadow: 0 0 20px rgba(59,130,246,.5);
}

/* ================= CANVAS ================= */
canvas {
    display: block;
    max-width: 100%;
}

</style>
</head>

<body class="p-4 md:p-8 flex justify-center">

<!-- BACKGROUND -->
<div class="ambient-bg">
    <div class="orb orb-1"></div>
    <div class="orb orb-2"></div>
    <div class="orb orb-3"></div>
</div>

<main class="w-full max-w-[1200px] grid grid-cols-1 lg:grid-cols-12 gap-6 relative z-10">

<!-- HEADER -->
<header class="lg:col-span-12 glass-panel p-5 flex justify-between items-center">
    <h1 class="text-xl font-light">Hydro<span class="font-bold">Smart</span></h1>
    <span class="text-xs text-green-300">DEMO / ESP READY</span>
</header>

<!-- SIDEBAR -->
<aside class="lg:col-span-4 flex flex-col gap-6">
    <div class="glass-panel p-6">
        <h3 class="text-sm mb-2">Status</h3>
        <p class="text-xs text-green-300">Simulasi Aktif</p>
    </div>
</aside>

<!-- DASHBOARD -->
<section class="lg:col-span-8 grid grid-cols-1 sm:grid-cols-1 md:grid-cols-2 gap-6">

<div class="glass-panel p-6 md:col-span-2">
    <h3 class="text-sm mb-4">Monitoring</h3>
    <div class="grid grid-cols-2 md:grid-cols-4 gap-4 text-center">
        <div><div id="moistureVal">--%</div><div class="text-xs">Tanah</div></div>
        <div><div id="tempVal">--°C</div><div class="text-xs">Suhu</div></div>
        <div><div id="humidVal">--%</div><div class="text-xs">RH</div></div>
        <div><div id="phVal">--</div><div class="text-xs">pH</div></div>
    </div>
</div>

<div class="glass-panel p-6">
    <h3 class="text-sm mb-2">Grafik Kelembaban</h3>
    <div class="relative w-full h-40 overflow-hidden">
        <canvas id="moistureChart" class="absolute inset-0 w-full h-full"></canvas>
    </div>
</div>

</section>
</main>

<script>
lucide.createIcons();

/* ================= CHART FIX ================= */
const canvas = document.getElementById("moistureChart");
const ctx = canvas.getContext("2d");
let data = [65,68,72,70,66,64,67,69,71,73,70,68];

function resizeCanvas() {
    const rect = canvas.parentElement.getBoundingClientRect();
    const dpr = window.devicePixelRatio || 1;

    canvas.width = rect.width * dpr;
    canvas.height = rect.height * dpr;
    canvas.style.width = rect.width + "px";
    canvas.style.height = rect.height + "px";

    ctx.setTransform(dpr,0,0,dpr,0,0);
}

window.addEventListener("resize", resizeCanvas);
resizeCanvas();

function drawChart() {
    const w = canvas.width / (window.devicePixelRatio||1);
    const h = canvas.height / (window.devicePixelRatio||1);
    ctx.clearRect(0,0,w,h);

    ctx.beginPath();
    data.forEach((v,i)=>{
        const x=i*(w/(data.length-1));
        const y=h-(v/100*h);
        i===0?ctx.moveTo(x,y):ctx.lineTo(x,y);
    });
    ctx.strokeStyle="#38bdf8";
    ctx.lineWidth=3;
    ctx.stroke();
}

setInterval(()=>{
    data.shift();
    data.push(60+Math.random()*15);
    document.getElementById("moistureVal").innerText=Math.floor(data.at(-1))+"%";
    drawChart();
},2000);

drawChart();
</script>

</body>
</html>
