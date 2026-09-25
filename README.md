<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Pritul Kumar Roy · profile</title>
  <!-- no external fonts, just system fonts with a clean look -->
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body {
      background-color: #0D0221;   /* same as pf system bg */
      font-family: 'Inter', 'Segoe UI', system-ui, -apple-system, BlinkMacSystemFont, 'Roboto', sans-serif;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      padding: 1.5rem;
      margin: 0;
    }

    /* main card – mimics the div align="center" but modern */
    .profile-card {
      max-width: 1100px;
      width: 100%;
      text-align: center;
      background: transparent;
      display: flex;
      flex-direction: column;
      gap: 1.2rem;
      animation: fadeIn 0.8s ease-out;
    }

    /* ----- animated title (white) ----- */
    .title-wrapper {
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 80px;
      margin-bottom: 0.25rem;
    }

    .title-glow {
      font-size: clamp(2.2rem, 10vw, 4.2rem);
      font-weight: 600;
      letter-spacing: 2px;
      color: #ffffff;
      font-family: 'VT323', 'Courier New', monospace; /* fallback for the vibe */
      text-shadow: 0 0 8px rgba(0, 255, 156, 0.4), 0 0 20px rgba(0, 255, 156, 0.2);
      animation: subtlePulse 3s infinite alternate, typingGlow 3s infinite;
      white-space: nowrap;
    }

    /* typing animation simulation with width + overflow */
    .typing-text {
      display: inline-block;
      overflow: hidden;
      white-space: nowrap;
      border-right: 2px solid #00FF9C;
      animation: blinkCursor 0.9s step-end infinite;
      padding-right: 6px;
    }

    /* progressive reveal for the name */
    .typing-text {
      animation: typeReveal 3s steps(20, end) forwards, blinkCursor 0.9s step-end infinite;
      max-width: fit-content;
    }

    @keyframes typeReveal {
      0% { width: 0; }
      100% { width: 100%; }
    }

    @keyframes blinkCursor {
      0%, 100% { border-color: #00FF9C; }
      50% { border-color: transparent; }
    }

    @keyframes subtlePulse {
      0% { text-shadow: 0 0 8px rgba(0, 255, 156, 0.3), 0 0 20px rgba(0, 255, 156, 0.1); }
      100% { text-shadow: 0 0 16px rgba(0, 255, 156, 0.8), 0 0 30px rgba(255, 46, 151, 0.3); }
    }

    /* ----- decorative wave lines (same as original capsule-render) ----- */
    .wave-divider {
      width: 100%;
      height: 3px;
      background: linear-gradient(90deg, #0D0221, #00FF9C, #FF2E97, #00E5FF, #0D0221);
      background-size: 300% 100%;
      border-radius: 10px;
      animation: waveShift 6s infinite alternate;
      margin: 0.2rem 0;
      opacity: 0.8;
    }

    @keyframes waveShift {
      0% { background-position: 0% 50%; }
      100% { background-position: 100% 50%; }
    }

    /* ----- badge groups ----- */
    .badge-section {
      display: flex;
      flex-direction: column;
      gap: 1rem;
      margin: 0.5rem 0;
    }

    .section-label {
      color: rgba(255, 255, 255, 0.6);
      font-size: 0.85rem;
      letter-spacing: 2px;
      text-transform: uppercase;
      font-weight: 500;
      margin-bottom: 0.25rem;
      border-bottom: 1px dashed rgba(0, 255, 156, 0.3);
      display: inline-block;
      padding-bottom: 0.25rem;
      align-self: center;
    }

    .badge-grid {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      align-items: center;
      gap: 0.75rem;
    }

    /* custom badge style — clean, consistent, techy */
    .tech-badge {
      display: inline-flex;
      align-items: center;
      justify-content: center;
      gap: 0.5rem;
      background-color: #0D0221;
      color: #ffffff;
      font-size: 0.85rem;
      font-weight: 500;
      padding: 0.6rem 1.2rem;
      border-radius: 40px;
      border: 1px solid rgba(0, 255, 156, 0.3);
      box-shadow: 0 0 6px rgba(0, 255, 156, 0.1);
      transition: all 0.25s ease;
      letter-spacing: 0.3px;
      background: rgba(13, 2, 33, 0.9);
      backdrop-filter: blur(2px);
      cursor: default;
      line-height: 1;
      white-space: nowrap;
    }

    /* hover animation — lift and glow */
    .tech-badge:hover {
      transform: translateY(-3px) scale(1.02);
      border-color: #00FF9C;
      box-shadow: 0 6px 16px rgba(0, 255, 156, 0.25), 0 0 10px #00FF9C33;
      color: #ffffff;
      background: #120a2e;
    }

    /* small icons via pseudo? using simple spans for icon look — but we can embed real svg? not needed. 
       Instead use emoji or simple text? But we want to keep close to original but cleaner. 
       Let's add subtle icon-like unicode or just rely on clean text. 
       Since original had logos, but to keep it zero dependency and neat, we use minimal icons. */
    .tech-badge i {
      font-style: normal;
      font-size: 1.1rem;
      filter: drop-shadow(0 0 5px #00FF9C);
    }

    /* specific color accents for icons */
    .badge-icon {
      display: inline-block;
      width: 1.2rem;
      text-align: center;
      font-weight: 400;
    }

    /* badge variants — just change border/glow slightly to differentiate categories? 
       but we keep consistent for organized look */
    .badge-grid.identity .tech-badge {
      border-color: #FF2E97;
      box-shadow: 0 0 6px rgba(255, 46, 151, 0.2);
    }
    .badge-grid.identity .tech-badge:hover {
      box-shadow: 0 6px 16px rgba(255, 46, 151, 0.4);
      border-color: #FF2E97;
    }

    .badge-grid.design .tech-badge {
      border-color: #00FF9C;
    }
    .badge-grid.hardware .tech-badge {
      border-color: #00E5FF;
    }
    .badge-grid.hardware .tech-badge:hover {
      box-shadow: 0 6px 16px rgba(0, 229, 255, 0.3);
    }

    /* footer wave */
    .wave-divider.footer {
      margin-top: 0.5rem;
      background: linear-gradient(90deg, #0D0221, #00E5FF, #FF2E97, #00FF9C, #0D0221);
      background-size: 300% 100%;
      animation: waveShift 7s infinite alternate-reverse;
    }

    /* entrance animation */
    @keyframes fadeIn {
      0% { opacity: 0; transform: scale(0.98); }
      100% { opacity: 1; transform: scale(1); }
    }

    /* spacing adjustments */
    .profile-card > * {
      animation: fadeInUp 0.6s ease-out backwards;
    }
    .profile-card > *:nth-child(1) { animation-delay: 0.05s; }
    .profile-card > *:nth-child(2) { animation-delay: 0.1s; }
    .profile-card > *:nth-child(3) { animation-delay: 0.15s; }
    .profile-card > *:nth-child(4) { animation-delay: 0.2s; }
    .profile-card > *:nth-child(5) { animation-delay: 0.25s; }
    .profile-card > *:nth-child(6) { animation-delay: 0.3s; }
    .profile-card > *:nth-child(7) { animation-delay: 0.35s; }

    @keyframes fadeInUp {
      0% { opacity: 0; transform: translateY(12px); }
      100% { opacity: 1; transform: translateY(0); }
    }

    /* for better readability on smaller screens */
    @media (max-width: 600px) {
      .tech-badge {
        font-size: 0.75rem;
        padding: 0.5rem 1rem;
        white-space: normal;
      }
      .badge-grid {
        gap: 0.5rem;
      }
      .title-glow {
        white-space: normal;
        font-size: 2rem;
        line-height: 1.2;
      }
      .typing-text {
        white-space: normal;
        border-right: none;
        animation: none;
        max-width: 100%;
      }
      .title-glow {
        animation: subtlePulse 3s infinite alternate;
      }
      .typing-text {
        display: inline;
        border-right: none;
      }
    }

    /* small extra glow for identity badges */
    .identity .tech-badge .badge-icon {
      color: #FF2E97;
    }
    .design .tech-badge .badge-icon {
      color: #00FF9C;
    }
    .hardware .tech-badge .badge-icon {
      color: #00E5FF;
    }

    /* ensure white title */
    h1 {
      color: #ffffff;
      font-weight: 600;
    }
  </style>
</head>
<body>
  <div class="profile-card">

    <!-- animated title: white, with soft glow and typing effect -->
    <div class="title-wrapper">
      <h1 class="title-glow">
        <span class="typing-text">Pritul Kumar Roy</span>
      </h1>
    </div>

    <!-- top wave (capsule-render style) -->
    <div class="wave-divider"></div>

    <!-- IDENTITY -->
    <div class="badge-section">
      <div class="section-label">IDENTITY</div>
      <div class="badge-grid identity">
        <span class="tech-badge">
          <span class="badge-icon">◆</span> Pritul Kumar Roy
        </span>
        <span class="tech-badge">
          <span class="badge-icon">◆</span> ECE Graduate
        </span>
      </div>
    </div>

    <!-- DESIGN & DEVELOPMENT -->
    <div class="badge-section">
      <div class="section-label">DESIGN & DEVELOPMENT</div>
      <div class="badge-grid design">
        <span class="tech-badge">
          <span class="badge-icon">◈</span> UI/UX Design
        </span>
        <span class="tech-badge">
          <span class="badge-icon">◈</span> UI/UX Development
        </span>
        <span class="tech-badge">
          <span class="badge-icon">◈</span> Flutter Development
        </span>
        <span class="tech-badge">
          <span class="badge-icon">◈</span> Android Development
        </span>
        <span class="tech-badge">
          <span class="badge-icon">◈</span> Web Development
        </span>
      </div>
    </div>

    <!-- DIGITAL DESIGN / HARDWARE -->
    <div class="badge-section">
      <div class="section-label">DIGITAL DESIGN / HARDWARE</div>
      <div class="badge-grid hardware">
        <span class="tech-badge">
          <span class="badge-icon">⬢</span> Digital Design
        </span>
        <span class="tech-badge">
          <span class="badge-icon">⬢</span> Verilog / HDL
        </span>
        <span class="tech-badge">
          <span class="badge-icon">⬢</span> RTL Design
        </span>
      </div>
    </div>

    <!-- footer wave -->
    <div class="wave-divider footer"></div>

  </div>
</body>
</html>
