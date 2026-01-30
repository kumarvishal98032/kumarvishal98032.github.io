---
layout: single
title: "Fluid Flow Intuition Demo"
permalink: /fluid-demo/
---

<h2>🌊 Fluid Flow Intuition (Laminar Channel Flow)</h2>

<p>
This interactive demo illustrates how velocity varies across a channel
for laminar flow (parabolic velocity profile).
</p>

<label for="speed">Flow Strength:</label>
<input type="range" id="speed" min="1" max="10" value="5" step="1">
<span id="speedValue">5</span>

<canvas id="flowCanvas" width="600" height="250"
        style="border:1px solid #ccc; margin-top:15px;"></canvas>

<script>
const canvas = document.getElementById("flowCanvas");
const ctx = canvas.getContext("2d");
const slider = document.getElementById("speed");
const speedValue = document.getElementById("speedValue");

function drawFlow(strength) {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  const H = canvas.height;
  const W = canvas.width;

  for (let y = 0; y <= H; y += 12) {
    const eta = (y - H/2) / (H/2);
    const velocity = strength * (1 - eta*eta);

    ctx.beginPath();
    ctx.moveTo(50, y);
    ctx.lineTo(50 + velocity * 20, y);
    ctx.strokeStyle = "#1E88E5";
    ctx.lineWidth = 2;
    ctx.stroke();
  }
}

slider.addEventListener("input", () => {
  speedValue.textContent = slider.value;
  drawFlow(slider.value);
});

drawFlow(slider.value);
</script>

<hr>

<p style="font-size:0.9em; color:#555;">
<strong>Concept:</strong> Fully developed laminar flow between parallel plates
(Poiseuille flow). The parabolic profile arises from the balance between
viscous diffusion and pressure forcing.
</p>
