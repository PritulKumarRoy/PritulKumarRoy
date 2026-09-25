<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Pritul Kumar Roy</title>
<style>
  html, body { margin: 0; padding: 0; height: 100%; background: #05060d; overflow: hidden; }
  canvas { display: block; }
</style>
</head>
<body>
<canvas id="c"></canvas>

<script>
(function () {
  var canvas = document.getElementById('c');
  var ctx    = canvas.getContext('2d');
  var W = 0, H = 0, DPR = 1;

  var NAME = "Pritul Kumar Roy";

  var ITEMS = [
    { label: "Collabflo",        type: "work"  },
    { label: "Codeflare Labs",   type: "work"  },
    { label: "SkillForge",       type: "work"  },
    { label: "Geo GNSS App",     type: "work"  },
    { label: "Global Kitchen",   type: "work"  },
    { label: "B.Tech ECE",       type: "study" },
    { label: "GNSS Receiver",    type: "study" },
    { label: "OpenROAD ASIC",    type: "study" },
    { label: "BSNL Telecom",     type: "study" },
    { label: "Higher Secondary", type: "study" }
  ];

  var COLORS = { work: "#a855f7", study: "#22d3ee" };

  var nodes = [];
  var dust  = [];
  var mouse = { x: -9999, y: -9999, active: false };

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

  function buildScene() {
    nodes = ITEMS.map(function (item, i) {
      var angle  = (i / ITEMS.length) * Math.PI * 2;
      var radius = Math.min(W, H) * 0.30;
      return {
        label: item.label,
        type:  item.type,
        x: W / 2 + Math.cos(angle) * radius,
        y: H / 2 + Math.sin(angle) * radius,
        vx: (Math.random() - 0.5) * 0.6,
        vy: (Math.random() - 0.5) * 0.6,
        r: item.type === 'work' ? 7 : 5.5,
        pulse: Math.random() * Math.PI * 2,
        hover: false
      };
    });

    dust = [];
    for (var k = 0; k < 70; k++) {
      dust.push({
        x: Math.random() * W,
        y: Math.random() * H,
        vx: (Math.random() - 0.5) * 0.2,
        vy: (Math.random() - 0.5) * 0.2,
        a: Math.random() * 0.3 + 0.05,
        r: Math.random() * 1.3 + 0.3
      });
    }
  }

  canvas.addEventListener('mousemove', function (e) {
    mouse.x = e.clientX; mouse.y = e.clientY; mouse.active = true;
  });
  canvas.addEventListener('mouseleave', function () {
    mouse.x = -9999; mouse.y = -9999; mouse.active = false;
  });
  canvas.addEventListener('touchmove', function (e) {
    var t = e.touches[0];
    mouse.x = t.clientX; mouse.y = t.clientY; mouse.active = true;
  }, { passive: true });
  canvas.addEventListener('touchend', function () {
    mouse.x = -9999; mouse.y = -9999; mouse.active = false;
  });

  var LINK_DIST = 220;

  function draw() {
    ctx.clearRect(0, 0, W, H);

    // dust
    for (var i = 0; i < dust.length; i++) {
      var d = dust[i];
      d.x += d.vx; d.y += d.vy;
      if (d.x < 0 || d.x > W) d.vx *= -1;
      if (d.y < 0 || d.y > H) d.vy *= -1;
      ctx.beginPath();
      ctx.arc(d.x, d.y, d.r, 0, Math.PI * 2);
      ctx.fillStyle = 'rgba(180,190,255,' + d.a + ')';
      ctx.fill();
    }

    // physics
    for (var i = 0; i < nodes.length; i++) {
      var n = nodes[i];
      n.x += n.vx; n.y += n.vy;
      n.pulse += 0.04;

      if (n.x < 80)     { n.x = 80;     n.vx = Math.abs(n.vx); }
      if (n.x > W - 80) { n.x = W - 80; n.vx = -Math.abs(n.vx); }
      if (n.y < 80)     { n.y = 80;     n.vy = Math.abs(n.vy); }
      if (n.y > H - 80) { n.y = H - 80; n.vy = -Math.abs(n.vy); }

      n.vx *= 0.995; n.vy *= 0.995;

      var dx = mouse.x - n.x, dy = mouse.y - n.y;
      var dist = Math.sqrt(dx * dx + dy * dy) || 1;
      if (mouse.active && dist < 180) {
        n.vx += (dx / dist) * 0.08;
        n.vy += (dy / dist) * 0.08;
      }

      var sp = Math.sqrt(n.vx * n.vx + n.vy * n.vy);
      if (sp > 1.8) { n.vx = (n.vx / sp) * 1.8; n.vy = (n.vy / sp) * 1.8; }

      // hover
      n.hover = mouse.active && dist < n.r + 18;
    }

    // links
    for (var i = 0; i < nodes.length; i++) {
      for (var j = i + 1; j < nodes.length; j++) {
        var a = nodes[i], b = nodes[j];
        var dx2 = a.x - b.x, dy2 = a.y - b.y;
        var d2 = Math.sqrt(dx2 * dx2 + dy2 * dy2);
        if (d2 < LINK_DIST) {
          var alpha = (1 - d2 / LINK_DIST) * 0.4;
          var grad = ctx.createLinearGradient(a.x, a.y, b.x, b.y);
          grad.addColorStop(0, 'rgba(168,85,247,' + alpha + ')');
          grad.addColorStop(1, 'rgba(34,211,238,' + alpha + ')');
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
    for (var i = 0; i < nodes.length; i++) {
      var n = nodes[i];
      var color = COLORS[n.type];
      var baseR = n.r + Math.sin(n.pulse) * 1.3;
      var r = n.hover ? baseR + 5 : baseR;

      var glowR = r * 5;
      var glow = ctx.createRadialGradient(n.x, n.y, 0, n.x, n.y, glowR);
      glow.addColorStop(0, color + 'cc');
      glow.addColorStop(1, color + '00');
      ctx.fillStyle = glow;
      ctx.beginPath();
      ctx.arc(n.x, n.y, glowR, 0, Math.PI * 2);
      ctx.fill();

      ctx.beginPath();
      ctx.arc(n.x, n.y, r, 0, Math.PI * 2);
      ctx.fillStyle = color;
      ctx.fill();

      if (n.hover) {
        ctx.beginPath();
        ctx.arc(n.x, n.y, r + 8, 0, Math.PI * 2);
        ctx.strokeStyle = color;
        ctx.lineWidth = 1.5;
        ctx.stroke();
      }

      ctx.font = (n.hover ? '600 13px' : '500 11px') + ' system-ui, sans-serif';
      ctx.fillStyle = n.hover ? '#ffffff' : 'rgba(226,232,240,0.75)';
      ctx.textAlign = 'center';
      ctx.textBaseline = 'alphabetic';
      ctx.fillText(n.label, n.x, n.y - r - 10);
    }

    // center name
    var namePulse = 0.7 + Math.sin(Date.now() * 0.0015) * 0.3;
    ctx.font = '700 26px system-ui, sans-serif';
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';
    ctx.shadowColor = 'rgba(168,85,247,' + namePulse + ')';
    ctx.shadowBlur = 22;

    var nameGrad = ctx.createLinearGradient(W / 2 - 160, 0, W / 2 + 160, 0);
    nameGrad.addColorStop(0, '#22d3ee');
    nameGrad.addColorStop(1, '#a855f7');
    ctx.fillStyle = nameGrad;
    ctx.fillText(NAME, W / 2, H / 2);

    ctx.shadowBlur = 0;
    ctx.font = '400 11px system-ui, sans-serif';
    ctx.fillStyle = 'rgba(148,163,184,0.7)';
    ctx.fillText('WORKS  ·  STUDIES', W / 2, H / 2 + 26);

    requestAnimationFrame(draw);
  }

  window.addEventListener('resize', function () {
    resize();
    nodes.forEach(function (n) {
      n.x = Math.min(Math.max(n.x, 80), W - 80);
      n.y = Math.min(Math.max(n.y, 80), H - 80);
    });
  });

  resize();
  buildScene();
  draw();
})();
</script>
</body>
</html>
