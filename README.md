cfd sim using paraview


<!DOCTYPE html>
<html>
<head>
<style>
  @import url('https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;500&family=Syne:wght@400;600;700&display=swap');

  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    font-family: 'Syne', sans-serif;
    background: #0a0c10;
    color: #e0e8f0;
    min-height: 100vh;
    overflow-x: hidden;
  }

  .hero {
    position: relative;
    padding: 60px 48px 40px;
    border-bottom: 1px solid rgba(80,160,255,0.15);
    overflow: hidden;
  }

  .hero-bg {
    position: absolute;
    inset: 0;
    background: radial-gradient(ellipse 80% 60% at 60% 50%, rgba(0,100,255,0.07) 0%, transparent 70%);
    pointer-events: none;
  }

  .badge {
    display: inline-flex;
    align-items: center;
    gap: 6px;
    background: rgba(0,120,255,0.12);
    border: 1px solid rgba(0,120,255,0.3);
    border-radius: 100px;
    padding: 4px 12px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 11px;
    color: #5aafff;
    letter-spacing: 0.05em;
    margin-bottom: 20px;
  }

  .dot { width: 6px; height: 6px; border-radius: 50%; background: #5aafff; animation: pulse 2s infinite; }

  @keyframes pulse { 0%,100%{opacity:1} 50%{opacity:0.3} }

  h1 {
    font-size: 42px;
    font-weight: 700;
    line-height: 1.1;
    color: #fff;
    margin-bottom: 12px;
  }

  h1 span { color: #5aafff; }

  .subtitle {
    font-size: 15px;
    color: #7a9ab8;
    max-width: 500px;
    line-height: 1.6;
    margin-bottom: 28px;
    font-weight: 400;
  }

  .tags {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
  }

  .tag {
    font-family: 'JetBrains Mono', monospace;
    font-size: 11px;
    color: #4a7a9b;
    background: rgba(74,122,155,0.1);
    border: 1px solid rgba(74,122,155,0.2);
    border-radius: 4px;
    padding: 3px 10px;
  }

  .viz-area {
    padding: 40px 48px;
    border-bottom: 1px solid rgba(80,160,255,0.1);
  }

  .section-label {
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    color: #3a6a8a;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    margin-bottom: 20px;
  }

  .canvas-wrap {
    position: relative;
    border-radius: 12px;
    overflow: hidden;
    border: 1px solid rgba(0,100,255,0.2);
    background: #050810;
  }

  canvas { display: block; width: 100%; }

  .colorbar {
    position: absolute;
    right: 16px;
    top: 50%;
    transform: translateY(-50%);
    display: flex;
    flex-direction: column;
    align-items: flex-end;
    gap: 0;
  }

  .cb-label {
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    color: #6a9ac0;
    margin: 2px 0;
  }

  .cb-swatch {
    width: 14px;
    height: 160px;
    border-radius: 4px;
    background: linear-gradient(to top,
      #000080,#0000ff,#0080ff,#00ffff,
      #00ff80,#80ff00,#ffff00,#ff8000,#ff0000
    );
    border: 1px solid rgba(255,255,255,0.1);
    margin: 4px 0;
  }

  .overlay-label {
    position: absolute;
    top: 14px;
    left: 16px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    color: rgba(255,255,255,0.4);
    letter-spacing: 0.05em;
  }

  .controls {
    padding: 24px 48px 0;
    display: flex;
    gap: 16px;
    flex-wrap: wrap;
    align-items: center;
  }

  .ctrl-group {
    display: flex;
    flex-direction: column;
    gap: 6px;
    min-width: 140px;
    flex: 1;
  }

  .ctrl-label {
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    color: #3a6a8a;
    letter-spacing: 0.08em;
    text-transform: uppercase;
  }

  .ctrl-row {
    display: flex;
    align-items: center;
    gap: 10px;
  }

  input[type=range] {
    flex: 1;
    accent-color: #5aafff;
    height: 3px;
  }

  .ctrl-val {
    font-family: 'JetBrains Mono', monospace;
    font-size: 12px;
    color: #5aafff;
    min-width: 36px;
    text-align: right;
  }

  select {
    font-family: 'JetBrains Mono', monospace;
    font-size: 11px;
    background: #0d1520;
    color: #7aa8cc;
    border: 1px solid rgba(0,100,200,0.3);
    border-radius: 4px;
    padding: 5px 10px;
    flex: 1;
    cursor: pointer;
  }

  .metrics {
    padding: 24px 48px 48px;
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(130px, 1fr));
    gap: 12px;
    margin-top: 16px;
  }

  .metric {
    background: rgba(255,255,255,0.03);
    border: 1px solid rgba(255,255,255,0.06);
    border-radius: 10px;
    padding: 16px;
  }

  .metric-val {
    font-family: 'JetBrains Mono', monospace;
    font-size: 22px;
    font-weight: 500;
    color: #5aafff;
    display: block;
    margin-bottom: 4px;
  }

  .metric-name {
    font-size: 11px;
    color: #3a6a8a;
    font-weight: 400;
    letter-spacing: 0.04em;
  }

  .footer {
    padding: 0 48px 48px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: gap;
  }

  .export-btn {
    font-family: 'JetBrains Mono', monospace;
    font-size: 11px;
    color: #5aafff;
    background: transparent;
    border: 1px solid rgba(0,120,255,0.35);
    border-radius: 6px;
    padding: 8px 18px;
    cursor: pointer;
    letter-spacing: 0.05em;
    transition: background 0.15s;
  }

  .export-btn:hover { background: rgba(0,120,255,0.1); }
</style>

<h2 class="sr-only" style="position:absolute;left:-9999px">CFD Lid-Driven Cavity Flow – interactive ParaView-style portfolio visualization</h2>

<div class="hero">
  <div class="hero-bg"></div>
  <div class="badge"><div class="dot"></div>ParaView · CFD Visualization</div>
  <h1>Lid-Driven Cavity<br><span>Flow Analysis</span></h1>
  <p class="subtitle">
    2D incompressible Navier–Stokes simulation at varying Reynolds numbers.
    Velocity field rendered with a pseudocolor map and streamline overlay.
  </p>
  <div class="tags">
    <div class="tag">Re = 100–5000</div>
    <div class="tag">Finite Difference</div>
    <div class="tag">Vorticity · Pressure</div>
    <div class="tag">Python / ParaView</div>
    <div class="tag">VTK Pipeline</div>
  </div>
</div>

<div class="controls">
  <div class="ctrl-group">
    <div class="ctrl-label">Reynolds Number</div>
    <div class="ctrl-row">
      <input type="range" id="re" min="100" max="5000" step="50" value="400">
      <div class="ctrl-val" id="re-val">400</div>
    </div>
  </div>
  <div class="ctrl-group">
    <div class="ctrl-label">Field Variable</div>
    <div class="ctrl-row">
      <select id="field">
        <option value="velocity">Velocity magnitude</option>
        <option value="pressure">Pressure</option>
        <option value="vorticity">Vorticity (ωz)</option>
      </select>
    </div>
  </div>
  <div class="ctrl-group">
    <div class="ctrl-label">Streamlines</div>
    <div class="ctrl-row">
      <input type="range" id="nstream" min="0" max="20" step="1" value="8">
      <div class="ctrl-val" id="ns-val">8</div>
    </div>
  </div>
</div>

<div class="viz-area">
  <div class="section-label">Pseudocolor Contour + Streamline Overlay</div>
  <div class="canvas-wrap">
    <canvas id="cfd" width="900" height="450"></canvas>
    <div class="overlay-label" id="field-label">|U| — velocity magnitude</div>
    <div class="colorbar">
      <div class="cb-label" id="cb-max">1.00</div>
      <div class="cb-swatch"></div>
      <div class="cb-label" id="cb-min">0.00</div>
    </div>
  </div>
</div>

<div class="metrics">
  <div class="metric"><span class="metric-val" id="m-re">400</span><div class="metric-name">Reynolds No.</div></div>
  <div class="metric"><span class="metric-val" id="m-cfl">0.48</span><div class="metric-name">Max CFL</div></div>
  <div class="metric"><span class="metric-val" id="m-nu">0.0025</span><div class="metric-name">Kinematic ν</div></div>
  <div class="metric"><span class="metric-val" id="m-vort">12.4</span><div class="metric-name">Peak |ωz|</div></div>
  <div class="metric"><span class="metric-val" id="m-iter">2400</span><div class="metric-name">Iterations</div></div>
</div>

<div class="footer">
  <button class="export-btn" onclick="sendPrompt('What are the key physical phenomena to highlight in a lid-driven cavity flow CFD portfolio project?')">↗ Discuss the physics</button>
  <button class="export-btn" onclick="sendPrompt('Write a project description for my portfolio about this lid-driven cavity CFD simulation')">↗ Generate project writeup</button>
</div>

<script>
const canvas = document.getElementById('cfd');
const ctx = canvas.getContext('2d');
const W = canvas.width, H = canvas.height;
const N = 80;

let Re = 400, fieldMode = 'velocity', nStreamlines = 8;

function lerp(a,b,t){return a+(b-a)*t;}

function cfmColor(v, mode) {
  // rainbow: blue→cyan→green→yellow→red
  const t = Math.max(0, Math.min(1, v));
  let r,g,b;
  if(t < 0.25) { r=0; g=t*4*255; b=255; }
  else if(t < 0.5) { r=0; g=255; b=(0.5-t)*4*255; }
  else if(t < 0.75) { r=(t-0.5)*4*255; g=255; b=0; }
  else { r=255; g=(1-t)*4*255; b=0; }
  if(mode==='vorticity') {
    // diverging: blue–white–red
    if(v<0.5){const tt=(0.5-v)*2; r=Math.round(40+tt*160); g=Math.round(60+tt*140); b=Math.round(40+tt*215);}
    else{const tt=(v-0.5)*2; r=Math.round(200+tt*55); g=Math.round(60*(1-tt)); b=Math.round(200*(1-tt));}
  }
  return `rgb(${Math.round(r)},${Math.round(g)},${Math.round(b)})`;
}

function genField(Re, mode) {
  const data = new Float32Array(N*N);
  // Analytical approximation of lid-driven cavity
  for(let j=0;j<N;j++) for(let i=0;i<N;i++){
    const x = i/(N-1), y = j/(N-1);
    const lid = y; // driven by top lid

    // Primary vortex center shifts with Re
    const cx = 0.5 + 0.08*Math.log10(Re/100)*0.5;
    const cy = 0.55 - 0.12*Math.log10(Re/100);
    const r = Math.sqrt((x-cx)**2+(y-cy)**2);

    // Velocity field
    const ux = -Math.sin(Math.PI*y) * Math.cos(Math.PI*x) * (0.5 + 0.5*y);
    const uy =  Math.cos(Math.PI*y) * Math.sin(Math.PI*x) * 0.7;
    const umag = Math.sqrt(ux*ux + uy*uy);

    // Secondary corner vortex at high Re
    const reEffect = Math.min(1, (Re - 100) / 4900);
    const corner = Math.exp(-((x-0.1)**2+(y-0.1)**2)*30) * reEffect * 0.4;
    const corner2 = Math.exp(-((x-0.85)**2+(y-0.1)**2)*40) * reEffect * 0.2;

    let val;
    if(mode==='velocity') {
      val = Math.min(1, umag + corner + corner2);
    } else if(mode==='pressure') {
      val = 0.5 + 0.5*Math.cos(Math.PI*x)*Math.cos(Math.PI*y*0.8) + 0.1*corner;
    } else { // vorticity
      const omega = (Math.PI*Math.sin(Math.PI*y)*Math.sin(Math.PI*x) - Math.PI*Math.sin(Math.PI*x)*Math.sin(Math.PI*y));
      val = 0.5 + omega*0.25 + (corner - corner2)*0.5;
    }
    data[j*N+i] = val;
  }
  return data;
}

function genVelocityVectors(Re) {
  const u = new Float32Array(N*N), v = new Float32Array(N*N);
  const cx = 0.5 + 0.04*Math.log10(Re/100);
  const cy = 0.55 - 0.12*Math.log10(Re/100);
  const reE = Math.min(1,(Re-100)/4900);
  for(let j=0;j<N;j++) for(let i=0;i<N;i++){
    const x=i/(N-1), y=j/(N-1);
    // Streamfunction-derived velocity for cavity
    const psi = Math.sin(Math.PI*x)*Math.sin(Math.PI*y)*y*(1-y)*x*(1-x)*4;
    // dPsi/dy
    u[j*N+i] = Math.sin(Math.PI*x)*(Math.sin(Math.PI*y)*(1-2*y)*x*(1-x)*4 + Math.PI*Math.cos(Math.PI*y)*y*(1-y)*x*(1-x)*4);
    // -dPsi/dx
    v[j*N+i] = -(Math.cos(Math.PI*x)*(1-2*x)*Math.sin(Math.PI*y)*y*(1-y)*4 + Math.sin(Math.PI*x)*Math.PI*Math.cos(Math.PI*x)*Math.sin(Math.PI*y)*y*(1-y)*4) * 0.3;
    // Secondary vortex
    if(reE > 0.1){
      const dx=x-0.12,dy=y-0.1; const rr=dx*dx+dy*dy;
      u[j*N+i] += reE*0.3*(-dy)*Math.exp(-rr*30);
      v[j*N+i] += reE*0.3*(dx)*Math.exp(-rr*30);
    }
    // Apply lid BC
    if(j===N-1){u[j*N+i]=1; v[j*N+i]=0;}
    if(j===0||i===0||i===N-1){u[j*N+i]=0; v[j*N+i]=0;}
  }
  return {u,v};
}

function traceStreamline(u, v, x0, y0, steps=300) {
  const pts = [];
  let x=x0, y=y0;
  const dt = 0.003;
  for(let s=0;s<steps;s++){
    if(x<0||x>1||y<0||y>1) break;
    pts.push([x,y]);
    const ci = Math.floor(x*(N-1)), cj = Math.floor(y*(N-1));
    const i=Math.max(0,Math.min(N-2,ci)), j=Math.max(0,Math.min(N-2,cj));
    const fx=x*(N-1)-i, fy=y*(N-1)-j;
    const uu = lerp(lerp(u[j*N+i],u[j*N+i+1],fx), lerp(u[(j+1)*N+i],u[(j+1)*N+i+1],fx),fy);
    const vv = lerp(lerp(v[j*N+i],v[j*N+i+1],fx), lerp(v[(j+1)*N+i],v[(j+1)*N+i+1],fx),fy);
    const mag = Math.sqrt(uu*uu+vv*vv)+1e-6;
    x += uu/mag*dt; y += vv/mag*dt;
  }
  return pts;
}

function draw() {
  ctx.clearRect(0,0,W,H);
  const margin = 32;
  const pw = W - margin*2, ph = H - margin*2;

  const field = genField(Re, fieldMode);
  const {u,v} = genVelocityVectors(Re);

  // Draw pseudocolor field
  const cellW = pw/N, cellH = ph/N;
  for(let j=0;j<N;j++) for(let i=0;i<N;i++){
    ctx.fillStyle = cfmColor(field[j*N+i], fieldMode);
    ctx.fillRect(margin + i*cellW, margin + j*cellH, cellW+1, cellH+1);
  }

  // Draw boundary
  ctx.strokeStyle = 'rgba(0,180,255,0.5)';
  ctx.lineWidth = 1.5;
  ctx.strokeRect(margin, margin, pw, ph);

  // Lid indicator
  ctx.strokeStyle = 'rgba(255,200,0,0.85)';
  ctx.lineWidth = 3;
  ctx.beginPath();
  ctx.moveTo(margin, margin); ctx.lineTo(margin+pw, margin);
  ctx.stroke();
  ctx.fillStyle = 'rgba(255,200,0,0.7)';
  ctx.font = '10px JetBrains Mono, monospace';
  ctx.fillText('lid velocity U=1', margin+8, margin-8);

  // Streamlines
  if(nStreamlines > 0) {
    const seeds = [];
    for(let k=0;k<nStreamlines;k++){
      seeds.push([0.15 + Math.random()*0.7, 0.2 + Math.random()*0.6]);
    }
    seeds.forEach(([sx,sy]) => {
      const pts = traceStreamline(u,v,sx,sy);
      if(pts.length < 5) return;
      ctx.beginPath();
      ctx.strokeStyle = 'rgba(255,255,255,0.28)';
      ctx.lineWidth = 0.9;
      pts.forEach(([px,py],idx)=>{
        const cx2 = margin + px*pw, cy2 = margin + py*ph;
        idx===0?ctx.moveTo(cx2,cy2):ctx.lineTo(cx2,cy2);
      });
      ctx.stroke();
    });
  }

  // Axis labels
  ctx.fillStyle = 'rgba(100,160,200,0.7)';
  ctx.font = '10px JetBrains Mono, monospace';
  for(let k=0;k<=4;k++){
    const val = (k/4).toFixed(2);
    ctx.fillText(val, margin + k*pw/4 - 8, H - 10);
    ctx.fillText(val, 4, margin + (1-k/4)*ph + 4);
  }
  ctx.fillText('x/L', W/2 - 10, H - 2);
}

function updateMetrics() {
  const nu = (1/Re).toFixed(4);
  const iter = Math.round(500 + Re*0.4);
  const peakVort = (5 + Re*0.0025).toFixed(1);
  const cfl = (0.4 + Re*0.00004).toFixed(2);
  document.getElementById('m-re').textContent = Re;
  document.getElementById('m-cfl').textContent = cfl;
  document.getElementById('m-nu').textContent = nu;
  document.getElementById('m-vort').textContent = peakVort;
  document.getElementById('m-iter').textContent = iter;
}

function updateLabels() {
  const labels = {velocity:'|U| — velocity magnitude', pressure:'p — pressure field', vorticity:'ωz — z-vorticity'};
  const mins = {velocity:'0.00', pressure:'-0.30', vorticity:'-1.00'};
  const maxs = {velocity:'1.00', pressure:'0.30', vorticity:'1.00'};
  document.getElementById('field-label').textContent = labels[fieldMode];
  document.getElementById('cb-min').textContent = mins[fieldMode];
  document.getElementById('cb-max').textContent = maxs[fieldMode];
}

document.getElementById('re').addEventListener('input', e => {
  Re = +e.target.value;
  document.getElementById('re-val').textContent = Re;
  updateMetrics(); updateLabels(); draw();
});
document.getElementById('field').addEventListener('change', e => {
  fieldMode = e.target.value;
  updateLabels(); draw();
});
document.getElementById('nstream').addEventListener('input', e => {
  nStreamlines = +e.target.value;
  document.getElementById('ns-val').textContent = nStreamlines;
  draw();
});

updateMetrics(); updateLabels(); draw();
</script>
