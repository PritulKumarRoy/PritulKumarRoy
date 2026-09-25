<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Pritul Kumar Roy — Works & Studies</title>
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; }
  html, body {
    height: 100%;
    background: #05060d;
    font-family: 'Segoe UI', system-ui, sans-serif;
    overflow: hidden;
    color: #e5e7eb;
  }
  canvas {
    display: block;
    width: 100vw;
    height: 100vh;
    cursor: crosshair;
    touch-action: none;
  }
</style>
</head>
<body>
<canvas id="c"></canvas>

<script>
/* ==========================================================
   PRITUL KUMAR ROY — ANIMATED WORKS & STUDIES
   Single canvas component. Nodes = my works & studies.
   Nodes drift like a snake, link by proximity, react to cursor.
   ========================================================== */

const canvas = document.getElementById('c');
const ctx    = canvas.getContext('2d');

let W, H, DPR;

function resize() {
  DPR = Math.min(window.devicePixelRatio || 1, 2);
  W = window.innerWidth;
  H = window.innerHeight;
  canvas.width  = W * DPR;
  canvas.height = H * DPR;
  canvas.style.width  = W + 'px';
  canvas.style.height = H + 'px';
  ctx.setTransform(DPR, 0, 0, DPR, 0, 0);
}
resize();

/* -------- MY DATA -------- */
const NAME = "Pritul Kumar Roy";

const ITEMS = [
  // works
  { label: "Collabflo",        type: "work"  },
  { label: "Codeflare Labs",   type: "work"  },
  { label: "SkillForge",       type: "work"  },
  { label: "Geo GNSS App",     type: "work"  },
  { label: "Global Kitchen",   type: "work"  },
  // studies
  { label: "B.Tech ECE",       type: "study" },
  { label: "GNSS Receiver",    type: "study" },
  { label: "OpenROAD ASIC",    type: "study" },
  { label: "BSNL Telecom",     type: "study" },
  { label: "Higher Secondary", type: "study" }
];

const COLORS = {
  work:  "#a855f7",
  study: "#22d3ee"
};

/* -------- BUILD NODES -------- */
const nodes = ITEMS.map((item, i) => {
  const angle  = (i / ITEMS.length) * Math.PI * 2;
  const radius = Math.min(W, H) * 0.28;
  return {
    label: item.label,
    type:  item.type,
    x: W / 2 + Math.cos(angle) * radius,
    y: H / 2 + Math.sin(angle) * radius,
    vx: (Math.random() - 0.5) * 0.6,
    vy: (Math.random() - 0.5) * 0.6,
    r: item.type === 'work' ? 7 : 5.5,
    pulse: Math.random() * Math.PI * 2,
    hover: false,
    reveal: 0
  };
});

/* -------- AMBIENT DUST -------- */
const dust = Array.from({ length: 70 }, () => ({
  x: Math.random() * W,
  y: Math.random() * H,
  vx: (Math.random() - 0.5) * 0.2,
  vy: (Math.random() - 0.5) * 0.2,
  a: Math.random() * 0.3 + 0.05,
  r: Math.random() * 1.3 + 0.3
}));

/* -------- MOUSE -------- */
const mouse = { x: -9999, y: -9999, active: false };

canvas.addEventListener('mousemove', e => {
  mouse.x = e.clientX;
  mouse.y = e.clientY;
  mouse.active = true;
});
canvas.addEventListener('mouseleave', () => {
  mouse.x = mouse.y = -9999;
  mouse.active = false;
});
canvas.addEventListener('touchmove', e => {
  const t = e.touches[0];
  mouse.x = t.clientX;
  mouse.y = t.clientY;
  mouse.active = true;
}, { passive: true });
canvas.addEventListener('touchend', () => {
  mouse.x = mouse.y = -9999;
  mouse.active = false;
});

/* -------- HOVER DETECTION -------- */
function updateHover() {
  for (const n of nodes) {
    n.hover = mouse.active &&
      Math.hypot(n.x - mouse.x, n.y - mouse.y) < n.r + 18;
  }
}

/* -------- RENDER LOOP -------- */
const LINK_DIST = 220;

function draw() {
  ctx.clearRect(0, 0, W, H);

  // dust
  for (const d of dust) {
    d.x += d.vx; d.y += d.vy;
    if (d.x < 0 || d.x > W) d.vx *= -1;
    if (d.y < 0 || d.y > H) d.vy *= -1;
    ctx.beginPath();
    ctx.arc(d.x, d.y, d.r, 0, Math.PI * 2);
    ctx.fillStyle = `rgba(180,190,255,${d.a})`;
    ctx.fill();
  }

  // physics
  for (const n of nodes) {
    if (n.reveal < 1) n.reveal += 0.02;

    n.x += n.vx; n.y += n.vy;
    n.pulse += 0.04;

    // soft bounce off edges
    if (n.x < 70)     { n.x = 70;     n.vx = Math.abs(n.vx); }
    if (n.x > W - 70) { n.x = W - 70; n.vx = -Math.abs(n.vx); }
    if (n.y < 70)     { n.y = 70;     n.vy = Math.abs(n.vy); }
    if (n.y > H - 70) { n.y = H - 70; n.vy = -Math.abs(n.vy); }

    // friction
    n.vx *= 0.995; n.vy *= 0.995;

    // cursor magnet — snake-like pull
    const dx = mouse.x - n.x, dy = mouse.y - n.y;
    const dist = Math.hypot(dx, dy) || 1;
    if (mouse.active && dist < 180) {
      n.vx += (dx / dist) * 0.08;
      n.vy += (dy / dist) * 0.08;
    }

    // clamp speed
    const sp = Math.hypot(n.vx, n.vy);
    if (sp > 1.8) { n.vx = (n.vx / sp) * 1.8; n.vy = (n.vy / sp) * 1.8; }
  }

  updateHover();

  // links
  for (let i = 0; i < nodes.length; i++) {
    for (let j = i + 1; j < nodes.length; j++) {
      const a = nodes[i], b = nodes[j];
      const d = Math.hypot(a.x - b.x, a.y - b.y);
      if (d < LINK_DIST) {
        const alpha = (1 - d / LINK_DIST) * 0.4 *
                      Math.min(a.reveal, b.reveal);
        if (alpha <= 0) continue;
        const grad = ctx.createLinearGradient(a.x, a.y, b.x, b.y);
        grad.addColorStop(0, `rgba(168,85,247,${alpha})`);
        grad.addColorStop(1, `rgba(34,211,238,${alpha})`);
        ctx.strokeStyle = grad;
        ctx.lineWidth = 1;
        ctx.beginPath();
        ctx.moveTo(a.x, a.y);
        ctx.lineTo(b.x, b.y);
        ctx.stroke();
      }
    }
  }

  // nodes
  for (const n of nodes) {
    const color = COLORS[n.type];
    const baseR = n.r + Math.sin(n.pulse) * 1.3;
    const r = (n.hover ? baseR + 5 : baseR) * n.reveal;

    // glow
    const glowR = r * 5;
    const glow = ctx.createRadialGradient(n.x, n.y, 0, n.x, n.y, glowR);
    glow.addColorStop(0, color + 'cc');
    glow.addColorStop(1, color + '00');
    ctx.fillStyle = glow;
    ctx.beginPath();
    ctx.arc(n.x, n.y, glowR, 0, Math.PI * 2);
    ctx.fill();

    // core
    ctx.beginPath();
    ctx.arc(n.x, n.y, r, 0, Math.PI * 2);
    ctx.fillStyle = color;
    ctx.fill();

    // hover ring
    if (n.hover) {
      ctx.beginPath();
      ctx.arc(n.x, n.y, r + 8, 0, Math.PI * 2);
      ctx.strokeStyle = color;
      ctx.lineWidth = 1.5;
      ctx.stroke();
    }

    // label
    const la = Math.min(1, n.reveal);
    ctx.font = `${n.hover ? '600 13px' : '500 11px'} system-ui, sans-serif`;
    ctx.fillStyle = n.hover
      ? `rgba(255,255,255,${la})`
      : `rgba(226,232,240,${0.7 * la})`;
    ctx.textAlign = 'center';
    ctx.fillText(n.label, n.x, n.y - r - 10);
  }

  // my name — center, glowing
  const namePulse = 0.7 + Math.sin(Date.now() * 0.0015) * 0.3;
  ctx.font = '700 26px system-ui, sans-serif';
  ctx.textAlign = 'center';
  ctx.textBaseline = 'middle';
  ctx.shadowColor = `rgba(168,85,247,${namePulse})`;
  ctx.shadowBlur = 22;
  const grad = ctx.createLinearGradient(
    W / 2 - 160, 0, W / 2 + 160, 0
  );
  grad.addColorStop(0, '#22d3ee');
  grad.addColorStop(1, '#a855f7');
  ctx.fillStyle = grad;
  ctx.fillText(NAME, W / 2, H / 2);
  ctx.shadowBlur = 0;

  ctx.font = '400 11px system-ui, sans-serif';
  ctx.fillStyle = 'rgba(148,163,184,0.7)';
  ctx.fillText('WORKS  ·  STUDIES', W / 2, H / 2 + 26);

  requestAnimationFrame(draw);
}
draw();

window.addEventListener('resize', () => {
  resize();
  nodes.forEach(n => {
    n.x = Math.min(Math.max(n.x, 70), W - 70);
    n.y = Math.min(Math.max(n.y, 70), H - 70);
  });
});

if (window.matchMedia('(prefers-reduced-motion: reduce)').matches) {
  nodes.forEach(n => { n.vx = 0; n.vy = 0; });
  dust.forEach(d => { d.vx = 0; d.vy = 0; });
}
</script>
</body>
</html>
