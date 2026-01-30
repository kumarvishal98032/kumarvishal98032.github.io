---
layout: single
title: "Interactive Modeling Lab"
permalink: /fluid-demo/
---

<h2>🌊 Fluid Flow Intuition: Channel Flow</h2>

<button onclick="toggleEq()">🧮 Show / Hide Governing Equations</button>

<div id="equations" style="display:none; margin-top:10px;">
  <p><strong>Reduced Navier–Stokes:</strong></p>
  <p style="font-family:serif;">μ ∂²u/∂y² = ∂p/∂x</p>
</div>

<hr>

<label>Pressure Gradient:</label>
<input type="range" id="pg" min="1" max="10" value="5">
<span id="pgVal">5</span>

<br><br>

<label>Reynolds Number (intuition):</label>
<input type="range" id="re" min="1" max="10" value="3">
<span id="reVal">Low (Laminar)</span>

<br><br>

<canvas id="flowCanvas" width="700" height="300"
        style="border:1px solid #ccc;"></canvas>

<br><br>

<h3>📈 Velocity Profile</h3>
<canvas id="profileCanvas" width="300" height="250"
        style="border:1px solid #ccc;"></canvas>

<hr>

<h2>🧪 Reaction–Diffusion (Turing Pattern Demo)</h2>

<canvas id="rdCanvas" width="300" height="300"
        style="border:1px solid #ccc;"></canvas>

<script>
function toggleEq() {
  const e = document.getElementById("equations");
  e.style.display = e.style.display === "none" ? "block" : "none";
}

const canvas = document.getElementById("flowCanvas");
const ctx = canvas.getContext("2d");
const pCanvas = document.getElementById("profileCanvas");
const pCtx = pCanvas.getContext("2d");

const pg = document.getElementById("pg");
const re = document.getElementById("re");
const pgVal = document.getElementById("pgVal");
const reVal = document.getElementById("reVal");

let time = 0;

function drawFlow(P, R) {
  ctx.clearRect(0,0,canvas.width,canvas.height);
  const H = canvas.height;

  for (let y=0; y<=H; y+=12) {
    const eta = (y-H/2)/(H/2);
    const u = P*(1-eta*eta);
    ctx.strokeStyle = `rgb(${60+u*15},100,200)`;

    ctx.beginPath();
    ctx.moveTo(80,y);
    ctx.lineTo(80+u*25,y);
    ctx.stroke();
  }

  for (let y=40; y<H; y+=40) {
    ctx.beginPath();
    let x=80;
    ctx.moveTo(x,y);

    for (let i=0;i<40;i++) {
      const f = R>6 ? Math.sin(i*0.6+y*0.05)*(R-6):0;
      x += 2;
      ctx.lineTo(x,y+f);
    }
    ctx.strokeStyle="rgba(0,0,0,0.4)";
    ctx.stroke();
  }
}

function drawProfile(P) {
  pCtx.clearRect(0,0,pCanvas.width,pCanvas.height);
  const H = pCanvas.height;

  pCtx.beginPath();
  for (let y=0;y<=H;y++) {
    const eta=(y-H/2)/(H/2);
    const u=P*(1-eta*eta);
    const x=20+u*15;
    if (y===0) pCtx.moveTo(x,y);
    else pCtx.lineTo(x,y);
  }
  pCtx.strokeStyle="#E53935";
  pCtx.stroke();
}

function update() {
  pgVal.textContent=pg.value;
  reVal.textContent=re.value<4?"Low (Laminar)":re.value<7?"Moderate":"High (Transition)";
}

function animate() {
  time+=0.05;
  drawFlow(pg.value*(1+0.3*Math.sin(time)), re.value);
  drawProfile(pg.value);
  update();
  requestAnimationFrame(animate);
}
animate();

/* Reaction–Diffusion */
const rd=document.getElementById("rdCanvas");
const rctx=rd.getContext("2d");
const N=100;
let U=[],V=[];
for(let i=0;i<N*N;i++){U[i]=1;V[i]=0;}
for(let i=4500;i<5500;i++)V[i]=1;

function lap(A,i){
  return A[i]*-1+A[i-1]+A[i+1]+A[i-N]+A[i+N];
}

function rdStep(){
  for(let y=1;y<N-1;y++){
    for(let x=1;x<N-1;x++){
      const i=x+y*N;
      const uvv=U[i]*V[i]*V[i];
      U[i]+=0.16*lap(U,i)-uvv+0.035*(1-U[i]);
      V[i]+=0.08*lap(V,i)+uvv-0.065*V[i];
      const c=Math.floor((U[i]-V[i])*255);
      rctx.fillStyle=`rgb(${c},${c},${c})`;
      rctx.fillRect(x*3,y*3,3,3);
    }
  }
  requestAnimationFrame(rdStep);
}
rdStep();
</script>
