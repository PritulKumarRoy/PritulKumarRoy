# 🌌 Pritul Kumar Roy — Magic Repo

> A single, self-contained animated component that displays my **works** and **studies** as a living constellation.
> Pure HTML + CSS + JavaScript — **zero dependencies, zero build step.**

![Made with](https://img.shields.io/badge/made%20with-HTML%20%7C%20CSS%20%7C%20JS-a855f7?style=flat-square)
![Dependencies](https://img.shields.io/badge/dependencies-0-22d3ee?style=flat-square)

---

## ✨ What It Is

One `<canvas>` element. Every project and study milestone is a **glowing node** in a drifting constellation. Nodes link by proximity, magnetize toward your cursor, pulse gently, and expand into a detail card on click.

| Color | Meaning |
|---|---|
| 🟣 Purple | **Works** — projects, internships, design |
| 🔵 Cyan | **Studies** — education, research, certifications |

---

## 🚀 How to Use

1. Create `index.html` in your repo root.
2. Paste the **entire code block below** into it.
3. Commit, push, enable **GitHub Pages** (`Settings → Pages → main / root`).
4. Your animated portfolio is live.

To update content later, edit **only** the `PROFILE` and `NODES` objects at the top of the `<script>`.

---

## 📄 `index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <meta name="description" content="Pritul Kumar Roy — works and studies as a living constellation." />
  <title>Pritul Kumar Roy — Works & Studies</title>
  <style>
    :root {
      --bg: #05060d;
      --fg: #e5e7eb;
      --muted: #94a3b8;
      --work: #a855f7;
      --study: #22d3ee;
    }
    * { box-sizing: border-box; margin: 0; padding: 0; }
    html, body {
      height: 100%;
      background: radial-gradient(circle at 50% 30%, #0b0f1f 0%, var(--bg) 70%);
      color: var(--fg);
      font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
      overflow: hidden;
      -webkit-font-smoothing: antialiased;
    }

    /* ---- THE SINGLE ANIMATED COMPONENT ---- */
    #constellation {
      position: fixed;
      inset: 0;
      width: 100%;
      height: 100%;
      display: block;
      cursor: crosshair;
      touch-action: none;
    }

    /* ---- Header HUD ---- */
    .hud {
      position: fixed;
      top: 2rem; left: 2rem;
      z-index: 5;
      pointer-events: none;
      max-width: 60vw;
    }
    .hud h1 {
      font-size: clamp(1.4rem, 3vw, 2.2rem);
      font-weight: 700;
      letter-spacing: 0.02em;
      background: linear-gradient(90deg, var(--study), var(--work));
      -webkit-background-clip: text;
      background-clip: text;
      color: transparent;
    }
    .hud p {
      color: var(--muted);
      font-size: 0.85rem;
      margin-top: 0.35rem;
      letter-spacing: 0.1em;
      text-transform: uppercase;
    }

    /* ---- Detail panel ---- */
    .detail {
      position: fixed;
      top: 50%; right: 2rem;
      transform: translateY(-50%);
      width: min(380px, 86vw);
      padding: 1.7rem 1.5rem 1.5rem;
      background: rgba(15, 18, 35, 0.88);
      backdrop-filter: blur(16px);
      -webkit-backdrop-filter: blur(16px);
      border: 1px solid rgba(168, 85, 247, 0.35);
      border-radius: 18px;
      box-shadow: 0 0 40px rgba(168, 85, 247, 0.18);
      z-index: 10;
      transition: opacity .35s ease, transform .35s ease;
    }
    .detail.hidden {
      opacity: 0;
      pointer-events: none;
      transform: translateY(-50%) translateX(30px);
    }
    .detail h2 {
      font-size: 1.25rem;
      margin-bottom: 0.45rem;
      padding-right: 1.4rem;
    }
    .badge {
      display: inline-block;
      font-size: 0.68rem;
      text-transform: uppercase;
      letter-spacing: 0.12em;
      padding: 0.22rem 0.65rem;
      border-radius: 999px;
      margin-bottom: 0.9rem;
      font-weight: 600;
    }
    .badge.work  { background: rgba(168,85,247,.18); color: var(--work); }
    .badge.study { background: rgba(34,211,238,.18); color: var(--study); }
    .detail p { font-size: 0.92rem; line-height: 1.55; color: #cbd5e1; }
    .tags {
      margin-top: 1rem;
      display: flex;
      flex-wrap: wrap;
      gap: 0.4rem;
    }
    .tags span {
      font-size: 0.72rem;
      padding: 0.25rem 0.65rem;
      border-radius: 999px;
      background: rgba(255,255,255,0.06);
      border: 1px solid rgba(255,255,255,0.1);
      color: var(--muted);
    }
    #closeDetail {
      position: absolute;
      top: 0.6rem; right: 0.9rem;
      background: none; border: none;
      color: var(--muted);
      font-size: 1.5rem;
      cursor: pointer;
      line-height: 1;
      transition: color .2s;
    }
    #closeDetail:hover { color: var(--fg); }

    /* ---- Legend ---- */
    .legend {
      position: fixed;
      bottom: 1.4rem; left: 50%;
      transform: translateX(-50%);
      display: flex;
      gap: 1.6rem;
      align-items: center;
      font-size: 0.8rem;
      color: var(--muted);
      z-index: 5;
      background: rgba(10,12,25,.55);
      padding: 0.5rem 1.2rem;
      border-radius: 999px;
      backdrop-filter: blur(8px);
      -webkit-backdrop-filter: blur(8px);
      border: 1px solid rgba(255,255,255,0.05);
    }
    .legend .dot {
      display: inline-block;
      width: 9px; height: 9px;
      border-radius: 50%;
      margin-right: 0.45rem;
      vertical-align: middle;
    }
    .dot.work  { background: var(--work); box-shadow: 0 0 8px var(--work); }
    .dot.study { background: var(--study); box-shadow: 0 0 8px var(--study); }
    .legend .hint { opacity: 0.55; font-style: italic; }

    @media (max-width: 640px) {
      .hud { top: 1.2rem; left: 1.2rem; max-width: 80vw; }
      .hud p { font-size: 0.7rem; }
      .detail {
        top: auto; bottom: 5.5rem;
        right: 50%;
        transform: translateX(50%);
        width: 90vw;
      }
      .detail.hidden { transform: translateX(50%) translateY(20px); }
      .legend { gap: 0.9rem; font-size: 0.68rem; padding: 0.4rem 0.9rem; }
      .legend .hint { display: none; }
    }
    @media (prefers-reduced-motion: reduce) {
      .detail { transition: none; }
    }
  </style>
</head>
<body>
  <canvas id="constellation" aria-label="Interactive constellation of works and studies"></canvas>

  <header class="hud">
    <h1 id="name"></h1>
    <p id="tagline"></p>
  </header>

  <aside id="detail" class="detail hidden" role="dialog" aria-live="polite">
    <button id="closeDetail" aria-label="Close detail panel">×</button>
    <h2 id="dTitle"></h2>
    <span id="dType" class="badge"></span>
    <p id="dDesc"></p>
    <div id="dTags" class="tags"></div>
  </aside>

  <footer class="legend">
    <span><i class="dot work"></i> Works</span>
    <span><i class="dot study"></i> Studies</span>
    <span class="hint">Hover a node · click to expand</span>
  </footer>

  <script>
  /* ================================================================
     EDIT ONLY THE TWO OBJECTS BELOW TO UPDATE YOUR PORTFOLIO
     ================================================================ */

  const PROFILE = {
    name: "Pritul Kumar Roy",
    tagline: "ECE · Flutter Dev · UI/UX · RTL Design"
  };

  // Every entry below becomes an animated node.
  const NODES = [
    // ---------- WORKS ----------
    { id: "collabflo", label: "Collabflo", type: "work",
      desc: "Flutter + Supabase platform connecting creators & businesses. Built Home, Chat, Post and Profile screens end-to-end.",
      tags: ["Flutter", "Supabase", "SQL", "UI/UX"] },

    { id: "codeflare", label: "Codeflare Labs", type: "work",
      desc: "Mobile Application Developer intern — built a real-world Learning Management System with the team.",
      tags: ["Flutter", "Android", "MongoDB", "Debugging"] },

    { id: "skillforge", label: "SkillForge / CompEdu", type: "work",
      desc: "UI/UX design internship — designed an ed-tech e-learning UI and a fully responsive college website.",
      tags: ["HTML", "CSS", "Bootstrap", "UX"] },

    { id: "geo", label: "Smart GNSS 'Geo'", type: "work",
      desc: "Android app: geofencing, real-time location tracking, location sharing, map navigation and coordinate capture.",
      tags: ["Android", "Firebase", "Maps"] },

    { id: "kitchen", label: "Global Kitchen", type: "work",
      desc: "Brand logo design — exploring color, typography and visual storytelling with Canva.",
      tags: ["Canva", "Logo", "Branding"] },

    // ---------- STUDIES ----------
    { id: "btech", label: "B.Tech ECE", type: "study",
      desc: "Dr. B.C. Roy Engineering College, Durgapur · 2021–2025 · CGPA 8.03",
      tags: ["ECE", "Communication", "Database"] },

    { id: "gnss", label: "GNSS Receiver Architecture", type: "study",
      desc: "System-level study of the signal chain: RF front-end → IF → baseband → PVT. Trade-offs in noise figure, phase noise, and integration time.",
      tags: ["GNSS", "DSP", "RF", "Systems"] },

    { id: "openroad", label: "OpenROAD ASIC · IIT Guwahati", type: "study",
      desc: "RTL-to-GDSII fully automated flow workshop by IIT Guwahati with MeitY. ASIC prototyping methodology.",
      tags: ["ASIC", "RTL", "STA", "VLSI"] },

    { id: "bsnl", label: "BSNL Telecom Training", type: "study",
      desc: "Industrial training on telecom technology and networking.",
      tags: ["Telecom", "Networking"] },

    { id: "hs", label: "Higher Secondary · Science", type: "study",
      desc: "Bidhan Chandra Institution, Durgapur · 71.66%",
      tags: ["Science", "Art", "Sports"] }
  ];

  const COLORS = {
    work:  { node: "#a855f7", glow: "rgba(168,85,247,0.9)" },
    study: { node: "#22d3ee", glow: "rgba(34,211,238,0.9)" }
  };

  /* ================================================================
     ANIMATED CONSTELLATION COMPONENT — no need to edit below
     ================================================================ */
  (() => {
    'use strict';

    const canvas = document.getElementById('constellation');
    const ctx    = canvas.getContext('2d');

    let W = 0, H = 0, DPR = 1;

    function resize() {
      DPR = Math.min(window.devicePixelRatio || 1, 2);
      W = canvas.clientWidth;
      H = canvas.clientHeight;
      canvas.width  = Math.floor(W * DPR);
      canvas.height = Math.floor(H * DPR);
      ctx.setTransform(DPR, 0, 0, DPR, 0, 0);
    }

    const REDUCED = window.matchMedia('(prefers-reduced-motion: reduce)').matches;

    // ---- Build nodes ----
    const nodes = NODES.map((n, i) => {
      const angle  = (i / NODES.length) * Math.PI * 2;
      const radius = Math.min(window.innerWidth, window.innerHeight) * 0.28;
      return {
        ...n,
        x: window.innerWidth  / 2 + Math.cos(angle) * radius + (Math.random() - 0.5) * 60,
        y: window.innerHeight / 2 + Math.sin(angle) * radius + (Math.random() - 0.5) * 60,
        vx: REDUCED ? 0 : (Math.random() - 0.5) * 0.25,
        vy: REDUCED ? 0 : (Math.random() - 0.5) * 0.25,
        r: n.type === 'work' ? 6 : 5,
        pulse: Math.random() * Math.PI * 2,
        hovered: false,
        reveal: 0
      };
    });

    // ---- Ambient background dust ----
    const dust = Array.from({ length: 60 }, () => ({
      x: Math.random() * window.innerWidth,
      y: Math.random() * window.innerHeight,
      vx: REDUCED ? 0 : (Math.random() - 0.5) * 0.15,
      vy: REDUCED ? 0 : (Math.random() - 0.5) * 0.15,
      a: Math.random() * 0.25 + 0.05,
      r: Math.random() * 1.2 + 0.3
    }));

    // ---- Pointer state ----
    const mouse = { x: -9999, y: -9999, active: false };

    function pointerPos(e) {
      const rect = canvas.getBoundingClientRect();
      const p = e.touches ? e.touches[0] : e;
      return { x: p.clientX - rect.left, y: p.clientY - rect.top };
    }

    function updateHover() {
      let hit = null;
      if (mouse.active) {
        for (const n of nodes) {
          const rad = Math.max(n.r + 14, 22);
          if (Math.hypot(n.x - mouse.x, n.y - mouse.y) < rad) { hit = n; break; }
        }
      }
      for (const n of nodes) n.hovered = (n === hit);
      canvas.style.cursor = hit ? 'pointer' : 'crosshair';
    }

    canvas.addEventListener('mousemove', e => {
      const p = pointerPos(e);
      mouse.x = p.x; mouse.y = p.y; mouse.active = true;
      updateHover();
    });
    canvas.addEventListener('mouseleave', () => {
      mouse.x = mouse.y = -9999; mouse.active = false;
      for (const n of nodes) n.hovered = false;
    });
    canvas.addEventListener('touchmove', e => {
      e.preventDefault();
      const p = pointerPos(e);
      mouse.x = p.x; mouse.y = p.y; mouse.active = true;
      updateHover();
    }, { passive: false });
    canvas.addEventListener('touchend', () => {
      const hit = nodes.find(n => n.hovered);
      if (hit) showDetail(hit);
      mouse.active = false;
    });

    // ---- Detail panel ----
    const detail = document.getElementById('detail');
    const dTitle = document.getElementById('dTitle');
    const dType  = document.getElementById('dType');
    const dDesc  = document.getElementById('dDesc');
    const dTags  = document.getElementById('dTags');

    function showDetail(node) {
      dTitle.textContent = node.label;
      dType.textContent  = node.type;
      dType.className    = 'badge ' + node.type;
      dDesc.textContent  = node.desc;
      dTags.innerHTML    = node.tags.map(t => `<span>${t}</span>`).join('');
      detail.classList.remove('hidden');
    }

    canvas.addEventListener('click', () => {
      const hit = nodes.find(n => n.hovered);
      if (hit) showDetail(hit);
    });

    document.getElementById('closeDetail').onclick = () =>
      detail.classList.add('hidden');

    document.addEventListener('keydown', e => {
      if (e.key === 'Escape') detail.classList.add('hidden');
    });

    // ---- Header ----
    document.getElementById('name').textContent    = PROFILE.name;
    document.getElementById('tagline').textContent = PROFILE.tagline;

    // ---- Render loop ----
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
        ctx.fillStyle = `rgba(200,210,255,${d.a})`;
        ctx.fill();
      }

      // physics
      for (const n of nodes) {
        if (n.reveal < 1) n.reveal += 0.02;

        if (!REDUCED) {
          n.x += n.vx; n.y += n.vy;
          n.pulse += 0.03;

          if (n.x < 60)     n.vx += 0.02;
          if (n.x > W - 60) n.vx -= 0.02;
          if (n.y < 60)     n.vy += 0.02;
          if (n.y > H - 60) n.vy -= 0.02;

          n.vx *= 0.995; n.vy *= 0.995;

          const dx = mouse.x - n.x, dy = mouse.y - n.y;
          const dist = Math.hypot(dx, dy) || 1;
          if (mouse.active && dist < 160) {
            n.vx += (dx / dist) * 0.03;
            n.vy += (dy / dist) * 0.03;
          }
        }
      }

      // links
      for (let i = 0; i < nodes.length; i++) {
        for (let j = i + 1; j < nodes.length; j++) {
          const a = nodes[i], b = nodes[j];
          const d = Math.hypot(a.x - b.x, a.y - b.y);
          if (d < LINK_DIST) {
            const alpha = (1 - d / LINK_DIST) * 0.35 *
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
        const c = COLORS[n.type];
        const baseR = n.r + (REDUCED ? 0 : Math.sin(n.pulse) * 1.2);
        const r = (n.hovered ? baseR + 4 : baseR) * n.reveal;

        const glowR = r * 5;
        const glow = ctx.createRadialGradient(n.x, n.y, 0, n.x, n.y, glowR);
        glow.addColorStop(0, c.glow);
        glow.addColorStop(1, 'rgba(0,0,0,0)');
        ctx.fillStyle = glow;
        ctx.beginPath();
        ctx.arc(n.x, n.y, glowR, 0, Math.PI * 2);
        ctx.fill();

        ctx.beginPath();
        ctx.arc(n.x, n.y, r, 0, Math.PI * 2);
        ctx.fillStyle = c.node;
        ctx.fill();

        if (n.hovered) {
          ctx.beginPath();
          ctx.arc(n.x, n.y, r + 7, 0, Math.PI * 2);
          ctx.strokeStyle = c.node;
          ctx.lineWidth = 1.5;
          ctx.stroke();
        }

        const la = Math.min(1, n.reveal);
        ctx.font = `${n.hovered ? '600 13px' : '500 11px'} system-ui, sans-serif`;
        ctx.fillStyle = n.hovered
          ? `rgba(255,255,255,${la})`
          : `rgba(226,232,240,${0.75 * la})`;
        ctx.textAlign = 'center';
        ctx.fillText(n.label, n.x, n.y - r - 10);
      }

      requestAnimationFrame(draw);
    }

    // ---- Boot ----
    resize();
    nodes.forEach((n, i) => {
      const angle  = (i / NODES.length) * Math.PI * 2;
      const radius = Math.min(W, H) * 0.28;
      n.x = W / 2 + Math.cos(angle) * radius + (Math.random() - 0.5) * 60;
      n.y = H / 2 + Math.sin(angle) * radius + (Math.random() - 0.5) * 60;
    });

    window.addEventListener('resize', () => {
      resize();
      nodes.forEach(n => {
        n.x = Math.min(Math.max(n.x, 60), W - 60);
        n.y = Math.min(Math.max(n.y, 60), H - 60);
      });
    });

    draw();
  })();
  </script>
</body>
</html>
```

---

## 🎛️ Customization

| Want to change… | Where (inside `index.html`) |
|---|---|
| Add / remove a project or study | `NODES` array |
| Name & tagline | `PROFILE` object |
| Node colors | `COLORS` object |
| Link distance | `LINK_DIST` in the render loop |
| Cursor magnet strength | `0.03` inside "cursor magnet" block |
| Drift speed | `0.25` in node `vx`/`vy` init |

---

## ♿ Accessibility

- **`prefers-reduced-motion`** — freezes all drift, keeps nodes static and usable.
- **Keyboard** — `Esc` closes the detail card.
- **Touch** — drag to attract, tap a node to open its card.
- **DPR-aware** — crisp on retina displays.
- **Zero dependencies** — no framework, no CDN, no build.

---

## 🧾 License

MIT © Pritul Kumar Roy
