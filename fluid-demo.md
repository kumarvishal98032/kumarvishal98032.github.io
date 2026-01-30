---
layout: single
title: "Fluid Flow Intuition Demo"
permalink: /fluid-demo/
---

 <h2>🌊 Fluid Flow Intuition: Channel Flow</h2>

<p>
This demo visualizes laminar flow between parallel plates and builds intuition
about <strong>pressure gradient</strong>, <strong>Reynolds number</strong>,
and <strong>streamlines</strong>.
</p>

<!-- Controls -->
<div style="margin-bottom:15px;">
  <label>Pressure Gradient:</label>
  <input type="range" id="pg" min="1" max="10" value="5">
  <span id="pgVal">5</span>

  <br><br>

  <label>Reynolds Number (intuition):</label>
  <input type="range" id="re" min="1" max="10" value="3">
  <span id="reVal">Low (Laminar)</span>
</div>

<canvas id="flowCanvas" width="700" height="300"
        style="border:1px solid #ccc;"></canvas>

<script>
const canvas = document.getElementById("flowCanvas");
const ctx = canvas.getContext("2d");

const pgSlider = document.getElementById("pg");
const reSlider = document.getElementById("re");
const pgVal = document.getElementById("pgVal");
const reVal = document.getElementById("reVal");

function drawFlow(pressure, reynolds) {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  const H = canvas.height;
  const W = canvas.width;

  // ---- Velocity profile (parabolic) ----
  for (let y = 0; y <= H; y += 12) {
    const eta = (y - H / 2) / (H / 2);
    const velocity = pressure * (1 - eta * eta);

    ctx.beginPath();
    ctx.moveTo(80, y);
    ctx.lineTo(80 + velocity * 25, y);
    ctx.strokeStyle = "#1E88E5";
    ctx.lineWidth = 2;
    ctx.stroke();
  }

  // ---- Streamlines ----
  for (let y = 40; y < H; y += 40) {
    ctx.beginPath();
    let x = 80;
    ctx.moveTo(x, y);

    for (let i = 0; i < 40; i++) {
      const eta = (y - H / 2) / (H / 2);
      const baseVel = pressure * (1 - eta * eta);

      // Reynolds intuition: more waviness at higher Re
      const fluctuation = reynolds > 6
        ? Math.sin(i * 0.6 + y * 0.05) * (reynolds - 6)
        : 0;

      x += baseVel * 0.6;
      ctx.lineTo(x, y + fluctuation);
    }

    ctx.strokeStyle = "rgba(0,0,0,0.35)";
    ctx.lineWidth = 1;
    ctx.stroke();
  }
}

function updateLabels() {
  pgVal.textContent = pgSlider.value;

  const re = reSlider.value;
  if (re < 4) reVal.textContent = "Low (Laminar)";
  else if (re < 7) reVal.textContent = "Moderate";
  else reVal.textContent = "High (Transition)";
}

pgSlider.addEventListener("input", () => {
  updateLabels();
  drawFlow(pgSlider.value, reSlider.value);
});

reSlider.addEventListener("input", () => {
  updateLabels();
  drawFlow(pgSlider.value, reSlider.value);
});

updateLabels();
drawFlow(pgSlider.value, reSlider.value);
</script>

<hr>

<p style="font-size:0.9em; color:#555;">
<strong>Physical Interpretation:</strong><br>
• Pressure gradient controls the <em>magnitude</em> of velocity.<br>
• Reynolds number controls <em>flow stability</em> (laminar → transitional).<br>
• Streamlines represent instantaneous flow paths.
</p>
