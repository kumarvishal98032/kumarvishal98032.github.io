---
layout: single
title: "Fluid Flow & Reaction–Diffusion Demo"
permalink: /fluid-demo/
---

## 🌊 Fluid Flow Intuition (2D Channel Flow)

**Interactive demo** illustrating pressure-driven viscous flow,
Reynolds number intuition, and velocity profiles.

### Pressure Gradient
<input type="range" id="pressure" min="0.1" max="2" step="0.1" value="1">

### Reynolds Number
<input type="range" id="reynolds" min="10" max="500" step="10" value="100">

<canvas id="flowCanvas" width="500" height="200"
style="border:1px solid #ccc;"></canvas>

---

## 🧪 Reaction–Diffusion (Turing Patterns)

Emergence of spatial patterns via **diffusion-driven instability**.

<canvas id="rdCanvas"
style="border:1px solid #ccc;"></canvas>

---

<script>
/* ========= FLUID FLOW ========= */

const canvas = document.getElementById("flowCanvas");
const ctx = canvas.getContext("2d");

function drawFlow() {
  ctx.clearRect(0,0,canvas.width,canvas.height);

  const P = document.getElementById("pressure").value;
  const Re = document.getElementById("reynolds").value;

  for (let y = 0; y < canvas.height; y++) {
    const u = P * (1 - Math.pow((y - canvas.height/2)/(canvas.height/2),2));
    ctx.strokeStyle = `rgba(0,0,255,${u/Re})`;
    ctx.beginPath();
    ctx.moveTo(0,y);
    ctx.lineTo(u*150,y);
    ctx.stroke();
  }

  requestAnimationFrame(drawFlow);
}

drawFlow();

/* ========= REACTION–DIFFUSION ========= */

const rd = document.getElementById("rdCanvas");
const rctx = rd.getContext("2d");

const size = 150;
const scale = 2;
rd.width = size * scale;
rd.height = size * scale;

let U = new Float32Array(size*size);
let V = new Float32Array(size*size);

for (let i=0;i<size*size;i++){
  U[i]=1; V[i]=0;
}

// Seed
for (let y=65;y<85;y++){
  for (let x=65;x<85;x++){
    V[x+y*size]=1;
  }
}

function lap(A,x,y){
  const i=x+y*size;
  return -A[i]
    +A[i-1]+A[i+1]
    +A[i-size]+A[i+size];
}

function stepRD(){
  for(let y=1;y<size-1;y++){
    for(let x=1;x<size-1;x++){
      const i=x+y*size;
      const u=U[i], v=V[i];
      const uvv=u*v*v;

      U[i]+=0.16*lap(U,x,y)-uvv+0.035*(1-u);
      V[i]+=0.08*lap(V,x,y)+uvv-0.065*v;

      const c=Math.max(0,Math.min(255,Math.floor((U[i]-V[i])*255)));
      rctx.fillStyle=`rgb(${c},${c},${c})`;
      rctx.fillRect(x*scale,y*scale,scale,scale);
    }
  }
  requestAnimationFrame(stepRD);
}

stepRD();
</script>
