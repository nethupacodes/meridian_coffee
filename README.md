<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Meridian Coffee Roasters — Coffee, timed to the day.</title>
<meta name="description" content="Meridian Coffee Roasters in Ashcombe. Small-batch coffee roasted twice a week, poured to match the hour. Drag the Day Dial to see what we'd pour right now.">

<!-- Fonts: Fraunces (display, warm & characterful), Inter (body), Space Mono (time/data) -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Fraunces:ital,wght@0,400;0,500;0,600;0,700;1,500&family=Inter:wght@400;500;600;700&family=Space+Mono:wght@400;700&display=swap" rel="stylesheet">

<style>
/* =========================================================
   MERIDIAN COFFEE ROASTERS
   Concept: a coffee bar that runs on the sun, not the clock.
   The hero re-skins itself to the time of day via a
   draggable "Day Dial." Everything below stays calm and
   constant — the one bold move lives in one place.
   ========================================================= */

:root{
  /* ---- Core palette ---- */
  --ink:        #1c140d;   /* dark roast — primary text */
  --ink-soft:   #4a3b2d;   /* secondary text on paper */
  --paper:      #f6ede1;   /* warm cream — page base */
  --paper-soft: #eee0cb;   /* slightly deeper cream, alt sections */
  --ember:      #b8451f;   /* burnt rust — primary accent / CTA */
  --ember-dark: #8f3417;
  --brass:      #c99a4b;   /* aged brass — hairlines, ticks */
  --line:       rgba(28,20,13,0.14);
  --line-soft:  rgba(28,20,13,0.08);

  /* ---- Sky pairs (hero/nav only) ---- */
  --sky-morning-1:#ffd9a0; --sky-morning-2:#ff9a5c;
  --sky-midday-1: #bfe6ff; --sky-midday-2: #fff6df;
  --sky-evening-1:#ff7b54; --sky-evening-2:#5a3a8f;
  --sky-night-1:  #0c1024; --sky-night-2:  #241c47;

  /* ---- Type ---- */
  --font-display:'Fraunces', serif;
  --font-body:'Inter', sans-serif;
  --font-mono:'Space Mono', monospace;

  /* ---- Layout ---- */
  --max-width: 1180px;
  --section-pad: clamp(4rem, 9vw, 7.5rem);
  --radius-lg: 26px;
  --radius-md: 16px;
  --radius-sm: 9px;
}

/* ---------- Reset ---------- */
*, *::before, *::after{ box-sizing:border-box; }
html{ scroll-behavior:smooth; }
body{
  margin:0;
  background:var(--paper);
  color:var(--ink);
  font-family:var(--font-body);
  line-height:1.6;
  -webkit-font-smoothing:antialiased;
  overflow-x:hidden;
}
img{ max-width:100%; display:block; }
a{ color:inherit; text-decoration:none; }
ul, dl{ list-style:none; margin:0; padding:0; }
h1,h2,h3,h4{ font-family:var(--font-display); margin:0; font-weight:600; }
p{ margin:0; }
button{ font-family:inherit; cursor:pointer; border:none; background:none; }
input{ font-family:inherit; }

.visually-hidden{
  position:absolute; width:1px; height:1px; padding:0; margin:-1px;
  overflow:hidden; clip:rect(0,0,0,0); white-space:nowrap; border:0;
}
.skip-link{
  position:absolute; left:-999px; top:0;
  background:var(--ember); color:var(--paper);
  padding:0.75rem 1.25rem; z-index:999;
  border-radius:0 0 var(--radius-sm) 0; font-weight:600;
}
.skip-link:focus{ left:0; }
:focus-visible{ outline:2px solid var(--ember); outline-offset:3px; border-radius:4px; }

/* ---------- Glass-case utility ----------
   A nod to a bakery display case: frosted cream glass with a
   brass-toned hairline, used sparingly for cards that "hold"
   something (a suggestion, a bean, a compass, a signup). */
.glass-panel{
  background:rgba(255,251,244,0.62);
  border:1px solid var(--line);
  backdrop-filter:blur(14px);
  -webkit-backdrop-filter:blur(14px);
  border-radius:var(--radius-lg);
}

/* =========================================================
   SKY ZONE — confined to header + hero only
   ========================================================= */
.sky-zone{ position:relative; }
.sky{ position:absolute; inset:0; z-index:-1; overflow:hidden; }
.sky-layer{
  position:absolute; inset:0;
  opacity:0;
  transition:opacity 1.3s ease;
}
.sky-layer.is-active{ opacity:1; }
.sky-morning{ background:linear-gradient(160deg, var(--sky-morning-1), var(--sky-morning-2) 70%); }
.sky-midday{  background:linear-gradient(160deg, var(--sky-midday-1), var(--sky-midday-2) 70%); }
.sky-evening{ background:linear-gradient(160deg, var(--sky-evening-1), var(--sky-evening-2) 78%); }
.sky-night{   background:linear-gradient(160deg, var(--sky-night-1), var(--sky-night-2) 78%); }
.sky-grain{
  position:absolute; inset:0;
  background-image:radial-gradient(rgba(28,20,13,0.05) 1px, transparent 1px);
  background-size:3px 3px;
  mix-blend-mode:multiply;
  opacity:0.35;
}

/* Text/nav flips to light on darker skies */
body[data-period="evening"] .navbar,
body[data-period="night"] .navbar{ color:var(--paper); }
body[data-period="evening"] .navbar.scrolled,
body[data-period="night"] .navbar.scrolled{ background:rgba(12,8,5,0.45); border-bottom-color:rgba(246,237,225,0.16); }
body[data-period="evening"] .status-pill,
body[data-period="night"] .status-pill{ border-color:rgba(246,237,225,0.28); }

/* =========================================================
   NAVIGATION
   ========================================================= */
.navbar{
  position:fixed; top:0; left:0; right:0; z-index:100;
  padding:1.15rem 0;
  color:var(--ink);
  transition:background 0.3s ease, color 0.3s ease, border-color 0.3s ease, padding 0.3s ease;
  border-bottom:1px solid transparent;
}
.navbar.scrolled{
  background:rgba(246,237,225,0.72);
  backdrop-filter:blur(14px);
  -webkit-backdrop-filter:blur(14px);
  border-bottom-color:var(--line);
  padding:0.75rem 0;
}
.nav-inner{
  max-width:var(--max-width); margin:0 auto; padding:0 1.5rem;
  display:flex; align-items:center; gap:1.6rem;
}
.brand{ display:flex; align-items:center; gap:0.6rem; margin-right:auto; }
.brand-mark{ width:26px; height:26px; color:var(--ember); flex-shrink:0; }
.brand-mark svg{ width:100%; height:100%; }
.brand-word{
  font-family:var(--font-display); font-weight:700; font-size:1.05rem;
  letter-spacing:0.08em;
}
.nav-links{ display:flex; gap:1.8rem; }
.nav-links a{ font-size:0.92rem; font-weight:500; position:relative; padding:0.3rem 0; }
.nav-links a::after{
  content:''; position:absolute; left:0; bottom:-2px; width:0; height:1.5px;
  background:var(--ember); transition:width 0.25s ease;
}
.nav-links a:hover::after{ width:100%; }

.nav-status{ display:flex; align-items:center; gap:0.9rem; }
.status-pill{
  display:inline-flex; align-items:center; gap:0.45rem;
  font-family:var(--font-mono); font-size:0.72rem; letter-spacing:0.04em;
  padding:0.35rem 0.75rem; border-radius:999px;
  border:1px solid var(--line);
}
.status-dot{ width:6px; height:6px; border-radius:50%; background:#4c9a5f; box-shadow:0 0 6px #4c9a5f; }
.status-pill.is-closed .status-dot{ background:var(--ember); box-shadow:0 0 6px var(--ember); }
.nav-clock{ font-family:var(--font-mono); font-size:0.82rem; min-width:5.4em; text-align:right; }

.nav-toggle{
  display:none; flex-direction:column; justify-content:center; gap:5px;
  width:32px; height:32px;
}
.nav-toggle span{ display:block; height:2px; background:currentColor; border-radius:2px; transition:transform 0.3s ease, opacity 0.3s ease; }
.nav-toggle.open span:nth-child(1){ transform:translateY(7px) rotate(45deg); }
.nav-toggle.open span:nth-child(2){ opacity:0; }
.nav-toggle.open span:nth-child(3){ transform:translateY(-7px) rotate(-45deg); }

.mobile-menu{
  display:none;
  flex-direction:column;
  max-width:var(--max-width); margin:0 auto; padding:0 1.5rem;
  overflow:hidden; max-height:0;
  transition:max-height 0.3s ease;
}
.mobile-menu a{ padding:0.85rem 0; border-top:1px solid var(--line); font-size:0.98rem; }
.mobile-menu.open{ max-height:220px; }

/* =========================================================
   HERO
   ========================================================= */
.hero{
  position:relative;
  min-height:100svh;
  display:flex; align-items:center;
  padding:7.5rem 1.5rem 5rem;
}
.hero-inner{
  max-width:var(--max-width); margin:0 auto; width:100%;
  display:grid; grid-template-columns:1.05fr 0.95fr; gap:3rem; align-items:center;
}
.eyebrow{
  font-family:var(--font-mono); font-size:0.78rem; letter-spacing:0.12em;
  text-transform:uppercase; color:var(--ember-dark); margin-bottom:1.1rem;
}
.hero-copy{ padding:2.4rem 2.2rem; }
.hero-heading{
  font-size:clamp(2.5rem, 5.4vw, 4rem);
  line-height:1.04; letter-spacing:-0.01em;
  margin-bottom:1.1rem;
}
.hero-heading em{ font-style:italic; font-weight:500; color:var(--ember); }
.hero-desc{
  color:var(--ink-soft); font-size:1.05rem; max-width:46ch; margin-bottom:2rem;
}
.hero-actions{ display:flex; gap:1rem; flex-wrap:wrap; }

.btn{
  display:inline-flex; align-items:center; gap:0.5rem;
  padding:0.9rem 1.7rem; border-radius:999px;
  font-weight:600; font-size:0.94rem;
  transition:transform 0.2s ease, box-shadow 0.2s ease, background 0.2s ease, border-color 0.2s ease;
}
.btn-primary{ background:var(--ember); color:var(--paper); }
.btn-primary:hover{ transform:translateY(-2px); box-shadow:0 10px 24px -8px rgba(184,69,31,0.5); background:var(--ember-dark); }
.btn-secondary{ border:1.5px solid var(--ink); color:var(--ink); }
.btn-secondary:hover{ transform:translateY(-2px); background:rgba(28,20,13,0.06); }

/* ---- Day Dial ---- */
.hero-dial-wrap{ display:flex; flex-direction:column; align-items:center; gap:1.1rem; }
.day-dial{
  position:relative;
  width:100%; max-width:340px; aspect-ratio:320/190;
  touch-action:none; cursor:grab;
}
.day-dial:active{ cursor:grabbing; }
.dial-track{ width:100%; height:100%; overflow:visible; }
.dial-arc-bg{ fill:none; stroke:rgba(28,20,13,0.14); stroke-width:3; stroke-linecap:round; }
.dial-arc-fill{ fill:none; stroke:var(--ember); stroke-width:3; stroke-linecap:round; }

.dial-tick{
  position:absolute; width:6px; height:6px; border-radius:50%;
  background:var(--brass); transform:translate(-50%,-50%);
  box-shadow:0 0 0 3px rgba(255,251,244,0.7);
}
.dial-sun{
  position:absolute; width:38px; height:38px; border-radius:50%;
  transform:translate(-50%,-50%);
  background:radial-gradient(circle at 35% 30%, #fff2d6, var(--brass) 75%);
  border:2px solid var(--paper);
  box-shadow:0 4px 14px rgba(28,20,13,0.28);
  display:flex; align-items:center; justify-content:center;
  font-size:1.05rem;
  transition:box-shadow 0.2s ease;
}
.day-dial:focus-visible .dial-sun{ box-shadow:0 0 0 4px rgba(184,69,31,0.4), 0 4px 14px rgba(28,20,13,0.28); }

.dial-readout{ text-align:center; }
.dial-period{
  display:block; font-family:var(--font-mono); font-size:0.78rem;
  letter-spacing:0.18em; color:var(--ember-dark); margin-bottom:0.2rem;
}
.dial-time{ font-family:var(--font-mono); font-size:1.4rem; font-weight:700; }

.dial-suggestion{
  width:100%; max-width:340px; padding:1.1rem 1.4rem; text-align:center;
}
.suggestion-label{ font-family:var(--font-mono); font-size:0.72rem; letter-spacing:0.1em; text-transform:uppercase; color:var(--ink-soft); margin-bottom:0.3rem; }
.dial-suggestion h3{ font-size:1.2rem; margin-bottom:0.15rem; }
.dial-suggestion p:last-child{ color:var(--ink-soft); font-size:0.9rem; }

.scroll-cue{
  position:absolute; bottom:1.8rem; left:50%; transform:translateX(-50%);
  width:22px; height:36px; border:1.5px solid var(--line); border-radius:12px;
  display:flex; justify-content:center; padding-top:6px;
}
.scroll-cue span{ width:3px; height:8px; background:var(--ember); border-radius:2px; animation:cue-move 1.6s ease-in-out infinite; }
@keyframes cue-move{ 0%{ transform:translateY(0); opacity:1; } 100%{ transform:translateY(10px); opacity:0; } }

/* =========================================================
   SECTION SHARED
   ========================================================= */
.section{ padding:var(--section-pad) 1.5rem; }
.section-alt{ background:var(--paper-soft); }
.section-inner{ max-width:var(--max-width); margin:0 auto; }
.section-eyebrow{
  font-family:var(--font-mono); font-size:0.78rem; letter-spacing:0.14em;
  text-transform:uppercase; color:var(--ember-dark); margin-bottom:0.8rem;
}
.section-heading{ font-size:clamp(1.9rem, 4vw, 2.7rem); margin-bottom:0.9rem; letter-spacing:-0.01em; }
.section-lead{ color:var(--ink-soft); max-width:60ch; margin-bottom:3rem; font-size:1.03rem; }

.reveal{ opacity:0; transform:translateY(22px); transition:opacity 0.7s ease, transform 0.7s ease; }
.reveal.in-view{ opacity:1; transform:translateY(0); }

/* =========================================================
   MENU (dayparts — a real sequence, so numbering/order matters)
   ========================================================= */
.daypart{ margin-bottom:3rem; }
.daypart:last-child{ margin-bottom:0; }
.daypart-head{
  display:flex; justify-content:space-between; align-items:baseline;
  flex-wrap:wrap; gap:0.4rem 1rem;
  border-bottom:1px solid var(--line); padding-bottom:0.8rem; margin-bottom:1.2rem;
}
.daypart-head h3{ font-size:1.3rem; display:flex; align-items:baseline; gap:0.7rem; }
.daypart-hours{ font-family:var(--font-mono); font-size:0.78rem; color:var(--ink-soft); font-weight:400; }
.daypart-head p{ color:var(--ink-soft); font-size:0.94rem; }

.daypart.is-current .daypart-head h3::before{
  content:'●'; color:var(--ember); font-size:0.6rem; margin-right:0.2rem; vertical-align:middle;
}

.menu-list{ display:grid; gap:0.9rem; }
.menu-item{
  display:flex; justify-content:space-between; align-items:center; gap:1rem;
  padding:1.1rem 1.3rem;
  border:1px solid var(--line); border-radius:var(--radius-md);
  background:rgba(255,255,255,0.4);
  transition:transform 0.2s ease, border-color 0.2s ease, background 0.2s ease;
}
.menu-item:hover{ transform:translateX(4px); border-color:rgba(184,69,31,0.35); background:rgba(255,255,255,0.65); }
.menu-item-main h4{ font-size:1.03rem; margin-bottom:0.2rem; }
.menu-item-main p{ color:var(--ink-soft); font-size:0.88rem; }
.menu-item-meta{ display:flex; align-items:center; gap:1.1rem; flex-shrink:0; }
.brew-time{ font-family:var(--font-mono); font-size:0.78rem; color:var(--brass); background:rgba(201,154,75,0.14); padding:0.25rem 0.55rem; border-radius:6px; }
.price{ font-family:var(--font-mono); font-size:0.94rem; font-weight:700; min-width:3.4em; text-align:right; }

/* =========================================================
   THE ROAST
   ========================================================= */
.roast-grid{ display:grid; grid-template-columns:repeat(auto-fit, minmax(260px, 1fr)); gap:1.4rem; }
.roast-card{ padding:1.7rem; }
.roast-swatch{
  height:12px; border-radius:999px; margin-bottom:1.1rem;
  background:linear-gradient(90deg, var(--from), var(--to));
}
.roast-level{ font-family:var(--font-mono); font-size:0.74rem; letter-spacing:0.08em; text-transform:uppercase; color:var(--ink-soft); margin-bottom:0.4rem; }
.roast-card h4{ font-size:1.2rem; margin-bottom:0.35rem; }
.roast-notes{ color:var(--ink-soft); font-size:0.92rem; margin-bottom:1.2rem; }
.roast-meta{ display:grid; gap:0.55rem; border-top:1px solid var(--line); padding-top:1rem; }
.roast-meta div{ display:flex; justify-content:space-between; font-size:0.85rem; }
.roast-meta dt{ color:var(--ink-soft); }
.roast-meta dd{ font-weight:600; }

/* =========================================================
   VISIT
   ========================================================= */
.visit-grid{ display:grid; grid-template-columns:1.1fr 0.9fr; gap:2.5rem; align-items:stretch; margin-bottom:2.5rem; }
.visit-info{ display:flex; flex-direction:column; gap:1.3rem; }
.visit-info address{ font-style:normal; font-size:1.3rem; font-family:var(--font-display); }
.hours-grid{ display:grid; gap:0.5rem; font-family:var(--font-mono); font-size:0.9rem; }
.hours-grid div{ display:flex; justify-content:space-between; max-width:280px; }
.hours-grid dt{ color:var(--ink-soft); }
.visit-contact{ font-size:0.95rem; }
.social-row{ display:flex; gap:1.4rem; }
.social-row a{ font-size:0.88rem; border-bottom:1px solid var(--line); padding-bottom:2px; }
.social-row a:hover{ border-color:var(--ember); color:var(--ember-dark); }

.visit-compass{ display:flex; align-items:center; justify-content:center; padding:2rem; min-height:220px; }
.visit-compass svg{ width:100%; max-width:220px; }

.newsletter{
  display:flex; justify-content:space-between; align-items:center; gap:2rem;
  flex-wrap:wrap; padding:2rem 2.2rem;
}
.newsletter h4{ font-size:1.15rem; margin-bottom:0.35rem; }
.newsletter p{ color:var(--ink-soft); font-size:0.9rem; max-width:38ch; }
.newsletter-field{ display:flex; gap:0.6rem; flex-wrap:wrap; }
.newsletter-field input{
  padding:0.8rem 1.1rem; border-radius:999px; border:1px solid var(--line);
  background:rgba(255,255,255,0.6); font-size:0.92rem; min-width:220px;
}
.newsletter-field input:focus-visible{ outline:2px solid var(--ember); outline-offset:2px; }
.newsletter-note{ width:100%; font-family:var(--font-mono); font-size:0.8rem; color:var(--ember-dark); margin-top:0.6rem; min-height:1.2em; }

/* =========================================================
   FOOTER
   ========================================================= */
.site-footer{ border-top:1px solid var(--line); padding:3rem 1.5rem 2.2rem; background:var(--ink); color:var(--paper); }
.footer-inner{ max-width:var(--max-width); margin:0 auto; text-align:center; display:flex; flex-direction:column; align-items:center; gap:0.9rem; }
.footer-brand{ display:flex; align-items:center; gap:0.6rem; }
.footer-brand .brand-mark{ color:var(--brass); }
.footer-brand span:last-child{ font-family:var(--font-display); font-weight:700; letter-spacing:0.08em; }
.footer-tagline{ font-family:var(--font-mono); font-size:0.8rem; color:var(--brass); letter-spacing:0.05em; }
.footer-links{ display:flex; gap:1.6rem; }
.footer-links a{ font-size:0.88rem; opacity:0.85; }
.footer-links a:hover{ opacity:1; }
.footer-copy{ font-size:0.78rem; opacity:0.6; margin-top:0.6rem; }

.back-to-top{
  position:fixed; bottom:1.6rem; right:1.6rem; width:44px; height:44px;
  border-radius:50%; background:rgba(255,251,244,0.85);
  border:1px solid var(--line); color:var(--ember-dark); font-size:1.1rem;
  display:flex; align-items:center; justify-content:center;
  opacity:0; pointer-events:none; transform:translateY(10px);
  transition:opacity 0.3s ease, transform 0.3s ease; z-index:90;
  backdrop-filter:blur(10px);
}
.back-to-top.visible{ opacity:1; pointer-events:auto; transform:translateY(0); }
.back-to-top:hover{ border-color:var(--ember); }

/* =========================================================
   RESPONSIVE
   ========================================================= */
@media (max-width:960px){
  .hero-inner{ grid-template-columns:1fr; }
  .visit-grid{ grid-template-columns:1fr; }
  .hero-copy{ padding:2rem 1.6rem; }
}
@media (max-width:760px){
  .nav-links{ display:none; }
  .nav-toggle{ display:flex; }
  .mobile-menu{ display:flex; }
  .nav-clock{ display:none; }
  .hero{ padding-top:6.5rem; }
  .hero-actions{ flex-direction:column; align-items:stretch; }
  .hero-actions .btn{ justify-content:center; }
  .daypart-head{ flex-direction:column; align-items:flex-start; }
  .menu-item{ flex-direction:column; align-items:flex-start; gap:0.6rem; }
  .menu-item-meta{ width:100%; justify-content:space-between; }
  .newsletter{ flex-direction:column; align-items:stretch; }
  .newsletter-field{ flex-direction:column; }
  .newsletter-field input{ min-width:0; }
}
@media (max-width:480px){
  .section{ padding:3.2rem 1.2rem; }
  .hours-grid div{ max-width:none; }
}

/* ---------- Reduced motion ---------- */
@media (prefers-reduced-motion:reduce){
  *, *::before, *::after{
    animation-duration:0.01ms !important;
    animation-iteration-count:1 !important;
    transition-duration:0.01ms !important;
    scroll-behavior:auto !important;
  }
}
</style>
</head>
<body data-period="morning">

<a class="skip-link" href="#main">Skip to main content</a>

<div class="sky-zone">
  <div class="sky" aria-hidden="true">
    <div class="sky-layer sky-morning is-active" id="skyMorning"></div>
    <div class="sky-layer sky-midday" id="skyMidday"></div>
    <div class="sky-layer sky-evening" id="skyEvening"></div>
    <div class="sky-layer sky-night" id="skyNight"></div>
    <div class="sky-grain" aria-hidden="true"></div>
  </div>

  <!-- ============ NAVIGATION ============ -->
  <header class="navbar" id="navbar">
    <div class="nav-inner">
      <a href="#home" class="brand">
        <span class="brand-mark" aria-hidden="true">
          <svg viewBox="0 0 32 32"><circle cx="16" cy="16" r="6" fill="currentColor"/><g stroke="currentColor" stroke-width="2" stroke-linecap="round"><line x1="16" y1="1" x2="16" y2="6"/><line x1="16" y1="26" x2="16" y2="31"/><line x1="1" y1="16" x2="6" y2="16"/><line x1="26" y1="16" x2="31" y2="16"/><line x1="5.5" y1="5.5" x2="9" y2="9"/><line x1="23" y1="23" x2="26.5" y2="26.5"/><line x1="26.5" y1="5.5" x2="23" y2="9"/><line x1="9" y1="23" x2="5.5" y2="26.5"/></g></svg>
        </span>
        <span class="brand-word">MERIDIAN</span>
      </a>

      <nav class="nav-links" aria-label="Primary">
        <a href="#menu">Menu</a>
        <a href="#roast">The Roast</a>
        <a href="#visit">Visit</a>
      </nav>

      <div class="nav-status">
        <span class="status-pill" id="openPill"><span class="status-dot" aria-hidden="true"></span><span id="openText">Open</span></span>
        <span class="nav-clock" id="navClock" aria-hidden="true">—:—</span>
      </div>

      <button class="nav-toggle" id="navToggle" aria-label="Open menu" aria-expanded="false" aria-controls="mobileMenu">
        <span></span><span></span><span></span>
      </button>
    </div>

    <div class="mobile-menu" id="mobileMenu">
      <a href="#menu">Menu</a>
      <a href="#roast">The Roast</a>
      <a href="#visit">Visit</a>
    </div>
  </header>

  <!-- ============ HERO ============ -->
  <section class="hero" id="home">
    <div class="hero-inner">
      <div class="hero-copy glass-panel reveal">
        <p class="eyebrow">Meridian Coffee Roasters — Ashcombe</p>
        <h1 class="hero-heading">Coffee, timed<br><em>to the day.</em></h1>
        <p class="hero-desc">
          We don't run one menu all day. Bright, fast pours in the morning; slow
          decaf by night. Drag the dial to see exactly what we'd pour right now
          — or whenever you're planning to stop by.
        </p>
        <div class="hero-actions">
          <a href="#menu" class="btn btn-primary">See the menu</a>
          <a href="#visit" class="btn btn-secondary">Visit the roastery</a>
        </div>
      </div>

      <div class="hero-dial-wrap reveal">
        <div class="day-dial" id="dayDial" role="slider" tabindex="0"
             aria-label="Time of day" aria-valuemin="5" aria-valuemax="23"
             aria-valuenow="12" aria-valuetext="12:00 PM, Midday">
          <svg class="dial-track" viewBox="0 0 320 190" aria-hidden="true">
            <path class="dial-arc-bg" d="M20,170 A140,140 0 0 1 300,170"></path>
            <path class="dial-arc-fill" id="dialArcFill" d="M20,170 A140,140 0 0 1 300,170"></path>
          </svg>
          <div class="dial-sun" id="dialSun" aria-hidden="true">☀</div>
        </div>

        <div class="dial-readout">
          <span class="dial-period" id="dialPeriod">MIDDAY</span>
          <span class="dial-time" id="dialTime">12:00 PM</span>
        </div>

        <div class="dial-suggestion glass-panel">
          <p class="suggestion-label">Right now, we'd pour</p>
          <h3 id="suggestionName">Meridian Espresso</h3>
          <p id="suggestionDesc">House blend · dark cherry &amp; cocoa</p>
        </div>
      </div>
    </div>

    <a href="#menu" class="scroll-cue" aria-label="Scroll to menu"><span></span></a>
  </section>
</div>

<main id="main">

  <!-- ============ MENU ============ -->
  <section class="section" id="menu">
    <div class="section-inner">
      <p class="section-eyebrow">What's Brewing</p>
      <h2 class="section-heading reveal">A menu that follows the sun</h2>
      <p class="section-lead reveal">
        Four dayparts, four different moods. Brew times are pulled straight
        off our bar tickets — minutes:seconds for filter, seconds for espresso.
      </p>

      <div class="daypart" data-period-block="morning">
        <div class="daypart-head reveal">
          <h3>Morning <span class="daypart-hours">5:00 – 11:00</span></h3>
          <p>Bright, fast, built for momentum.</p>
        </div>
        <ul class="menu-list">
          <li class="menu-item reveal">
            <div class="menu-item-main"><h4>Foundry Drip</h4><p>Ethiopia Guji · floral &amp; citrus</p></div>
            <div class="menu-item-meta"><span class="brew-time">4:00</span><span class="price">$4.00</span></div>
          </li>
          <li class="menu-item reveal">
            <div class="menu-item-main"><h4>Brass Pour-Over</h4><p>Colombia Huila · caramel &amp; red apple</p></div>
            <div class="menu-item-meta"><span class="brew-time">3:30</span><span class="price">$5.50</span></div>
          </li>
          <li class="menu-item reveal">
            <div class="menu-item-main"><h4>Honey Oat Latte</h4><p>House honey syrup, oat milk</p></div>
            <div class="menu-item-meta"><span class="brew-time">2:15</span><span class="price">$5.75</span></div>
          </li>
          <li class="menu-item reveal">
            <div class="menu-item-main"><h4>Cascara Fizz</h4><p>Cold brew, coffee-cherry tonic, soda</p></div>
            <div class="menu-item-meta"><span class="brew-time">cold</span><span class="price">$5.25</span></div>
          </li>
        </ul>
      </div>

      <div class="daypart" data-period-block="midday">
        <div class="daypart-head reveal">
          <h3>Midday <span class="daypart-hours">11:00 – 15:00</span></h3>
          <p>Short, strong, back-to-work.</p>
        </div>
        <ul class="menu-list">
          <li class="menu-item reveal">
            <div class="menu-item-main"><h4>Meridian Espresso</h4><p>House blend · dark cherry &amp; cocoa</p></div>
            <div class="menu-item-meta"><span class="brew-time">0:28</span><span class="price">$3.50</span></div>
          </li>
          <li class="menu-item reveal">
            <div class="menu-item-main"><h4>Iced Yuzu Cascara</h4><p>Espresso, yuzu, cascara syrup, soda</p></div>
            <div class="menu-item-meta"><span class="brew-time">0:45</span><span class="price">$5.75</span></div>
          </li>
          <li class="menu-item reveal">
            <div class="menu-item-main"><h4>Cortado</h4><p>Equal parts espresso &amp; steamed milk</p></div>
            <div class="menu-item-meta"><span class="brew-time">0:40</span><span class="price">$4.25</span></div>
          </li>
          <li class="menu-item reveal">
            <div class="menu-item-main"><h4>Affogato</h4><p>Vanilla bean gelato, double shot</p></div>
            <div class="menu-item-meta"><span class="brew-time">0:28</span><span class="price">$6.00</span></div>
          </li>
        </ul>
      </div>

      <div class="daypart" data-period-block="evening">
        <div class="daypart-head reveal">
          <h3>Evening <span class="daypart-hours">15:00 – 19:00</span></h3>
          <p>The slow-down stretch.</p>
        </div>
        <ul class="menu-list">
          <li class="menu-item reveal">
            <div class="menu-item-main"><h4>Half-Caf Flat White</h4><p>50% Swiss-water decaf blend</p></div>
            <div class="menu-item-meta"><span class="brew-time">0:35</span><span class="price">$5.00</span></div>
          </li>
          <li class="menu-item reveal">
            <div class="menu-item-main"><h4>Maple Cardamom Latte</h4><p>House maple-cardamom syrup</p></div>
            <div class="menu-item-meta"><span class="brew-time">2:00</span><span class="price">$5.75</span></div>
          </li>
          <li class="menu-item reveal">
            <div class="menu-item-main"><h4>Nitro Cold Brew</h4><p>18-hour steep, nitrogen poured</p></div>
            <div class="menu-item-meta"><span class="brew-time">cold</span><span class="price">$5.50</span></div>
          </li>
          <li class="menu-item reveal">
            <div class="menu-item-main"><h4>Spanish Latte</h4><p>Condensed milk, double shot</p></div>
            <div class="menu-item-meta"><span class="brew-time">0:35</span><span class="price">$5.50</span></div>
          </li>
        </ul>
      </div>

      <div class="daypart" data-period-block="night">
        <div class="daypart-head reveal">
          <h3>Night <span class="daypart-hours">19:00 – 23:00</span></h3>
          <p>For the decaf-curious and the still-working.</p>
        </div>
        <ul class="menu-list">
          <li class="menu-item reveal">
            <div class="menu-item-main"><h4>Decaf Old Fashioned</h4><p>Swiss-water decaf, slow pour</p></div>
            <div class="menu-item-meta"><span class="brew-time">4:00</span><span class="price">$4.75</span></div>
          </li>
          <li class="menu-item reveal">
            <div class="menu-item-main"><h4>Chicory Nightcap</h4><p>Coffee-chicory blend, oat milk, cinnamon</p></div>
            <div class="menu-item-meta"><span class="brew-time">3:00</span><span class="price">$5.25</span></div>
          </li>
          <li class="menu-item reveal">
            <div class="menu-item-main"><h4>Hot Cascara Tea</h4><p>Brewed coffee-cherry tea, no caffeine crash</p></div>
            <div class="menu-item-meta"><span class="brew-time">4:00</span><span class="price">$4.00</span></div>
          </li>
          <li class="menu-item reveal">
            <div class="menu-item-main"><h4>Warm Milk &amp; Honey</h4><p>For the fully decaf-curious</p></div>
            <div class="menu-item-meta"><span class="brew-time">n/a</span><span class="price">$3.75</span></div>
          </li>
        </ul>
      </div>
    </div>
  </section>

  <!-- ============ THE ROAST ============ -->
  <section class="section section-alt" id="roast">
    <div class="section-inner">
      <p class="section-eyebrow">The Roast</p>
      <h2 class="section-heading reveal">Small batches, twice a week</h2>
      <p class="section-lead reveal">
        We roast six bags at a time on a 1962 drum roaster we call Bertha —
        enough to keep the bar stocked for a few days, never enough to sit
        around and go stale.
      </p>

      <div class="roast-grid">
        <article class="roast-card glass-panel reveal">
          <div class="roast-swatch" style="--from:#e9cd9d; --to:#c9924f;"></div>
          <p class="roast-level">Light Roast</p>
          <h4>Ethiopia · Guji</h4>
          <p class="roast-notes">Floral, citrus, black tea</p>
          <dl class="roast-meta">
            <div><dt>Process</dt><dd>Washed</dd></div>
            <div><dt>Altitude</dt><dd>1,950m</dd></div>
            <div><dt>Farm</dt><dd>Halo Beriti coop</dd></div>
          </dl>
        </article>

        <article class="roast-card glass-panel reveal">
          <div class="roast-swatch" style="--from:#c9924f; --to:#7a4a26;"></div>
          <p class="roast-level">Medium Roast</p>
          <h4>Colombia · Huila</h4>
          <p class="roast-notes">Caramel, red apple, brown sugar</p>
          <dl class="roast-meta">
            <div><dt>Process</dt><dd>Washed</dd></div>
            <div><dt>Altitude</dt><dd>1,700m</dd></div>
            <div><dt>Farm</dt><dd>Finca Buenavista</dd></div>
          </dl>
        </article>

        <article class="roast-card glass-panel reveal">
          <div class="roast-swatch" style="--from:#7a4a26; --to:#2c1a10;"></div>
          <p class="roast-level">Dark Roast</p>
          <h4>Sumatra · Lintong</h4>
          <p class="roast-notes">Cedar, dark chocolate, low acidity</p>
          <dl class="roast-meta">
            <div><dt>Process</dt><dd>Wet-hulled</dd></div>
            <div><dt>Altitude</dt><dd>1,400m</dd></div>
            <div><dt>Farm</dt><dd>Lintong smallholders</dd></div>
          </dl>
        </article>
      </div>
    </div>
  </section>

  <!-- ============ VISIT ============ -->
  <section class="section" id="visit">
    <div class="section-inner">
      <p class="section-eyebrow">Visit</p>
      <h2 class="section-heading reveal">Find us at the corner of Foundry &amp; 9th</h2>

      <div class="visit-grid">
        <div class="visit-info reveal">
          <address>14 Foundry Row<br>Ashcombe</address>
          <dl class="hours-grid">
            <div><dt>Mon – Fri</dt><dd>6:00 AM – 8:00 PM</dd></div>
            <div><dt>Sat – Sun</dt><dd>7:00 AM – 9:00 PM</dd></div>
          </dl>
          <p class="visit-contact">hello@meridian.coffee · (555) 014-2891</p>
          <div class="social-row">
            <a href="#">Instagram</a>
            <a href="#">Weekly roast notes</a>
          </div>
        </div>

        <div class="visit-compass glass-panel reveal" aria-hidden="true">
          <svg viewBox="0 0 200 200">
            <circle cx="100" cy="100" r="92" fill="none" stroke="var(--line)" stroke-width="1.5"/>
            <circle cx="100" cy="100" r="68" fill="none" stroke="var(--brass)" stroke-width="1" stroke-dasharray="2 6"/>
            <g stroke="var(--ink)" stroke-width="1.5" stroke-linecap="round">
              <line x1="100" y1="14" x2="100" y2="30"/>
              <line x1="100" y1="170" x2="100" y2="186"/>
              <line x1="14" y1="100" x2="30" y2="100"/>
              <line x1="170" y1="100" x2="186" y2="100"/>
            </g>
            <text x="100" y="26" text-anchor="middle" font-family="Space Mono, monospace" font-size="10" fill="var(--ink)">N</text>
            <text x="100" y="182" text-anchor="middle" font-family="Space Mono, monospace" font-size="10" fill="var(--ink)">S</text>
            <line x1="100" y1="100" x2="140" y2="60" stroke="var(--ember)" stroke-width="2.5" stroke-linecap="round"/>
            <circle cx="100" cy="100" r="4" fill="var(--ember)"/>
          </svg>
        </div>
      </div>

      <form class="newsletter glass-panel reveal" id="newsletterForm">
        <div>
          <h4>Get the weekly roast note</h4>
          <p>One email, every Sunday: this week's beans, brew ratio, and what's new on the dial.</p>
        </div>
        <div>
          <div class="newsletter-field">
            <label for="newsletterEmail" class="visually-hidden">Email address</label>
            <input type="email" id="newsletterEmail" placeholder="you@example.com" required>
            <button type="submit" class="btn btn-primary">Sign up</button>
          </div>
          <p class="newsletter-note" id="newsletterNote" role="status" aria-live="polite"></p>
        </div>
      </form>
    </div>
  </section>

</main>

<!-- ============ FOOTER ============ -->
<footer class="site-footer">
  <div class="footer-inner">
    <div class="footer-brand">
      <span class="brand-mark" aria-hidden="true">
        <svg viewBox="0 0 32 32"><circle cx="16" cy="16" r="6" fill="currentColor"/><g stroke="currentColor" stroke-width="2" stroke-linecap="round"><line x1="16" y1="1" x2="16" y2="6"/><line x1="16" y1="26" x2="16" y2="31"/><line x1="1" y1="16" x2="6" y2="16"/><line x1="26" y1="16" x2="31" y2="16"/></g></svg>
      </span>
      <span>MERIDIAN</span>
    </div>
    <p class="footer-tagline">Coffee, timed to the day.</p>
    <nav class="footer-links" aria-label="Footer">
      <a href="#menu">Menu</a><a href="#roast">The Roast</a><a href="#visit">Visit</a>
    </nav>
    <p class="footer-copy">© 2026 Meridian Coffee Roasters, Ashcombe. All beans roasted on purpose.</p>
  </div>
</footer>

<button class="back-to-top" id="backToTop" aria-label="Back to top">↑</button>

<script>
/* =========================================================
   MERIDIAN — SCRIPT
   Handles: sky/period theming, the Day Dial (drag + keyboard),
   live nav clock & open/closed pill, reveal-on-scroll,
   mobile nav, newsletter, back-to-top.
   ========================================================= */
document.addEventListener('DOMContentLoaded', () => {
  const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;

  /* ---------- Navbar scroll state ---------- */
  const navbar = document.getElementById('navbar');
  const onScroll = () => navbar.classList.toggle('scrolled', window.scrollY > 20);
  onScroll();
  window.addEventListener('scroll', onScroll, { passive: true });

  /* ---------- Mobile nav toggle ---------- */
  const navToggle = document.getElementById('navToggle');
  const mobileMenu = document.getElementById('mobileMenu');
  navToggle.addEventListener('click', () => {
    const isOpen = mobileMenu.classList.toggle('open');
    navToggle.classList.toggle('open', isOpen);
    navToggle.setAttribute('aria-expanded', String(isOpen));
  });
  mobileMenu.querySelectorAll('a').forEach(a => a.addEventListener('click', () => {
    mobileMenu.classList.remove('open');
    navToggle.classList.remove('open');
    navToggle.setAttribute('aria-expanded', 'false');
  }));

  /* ---------- Reveal-on-scroll ---------- */
  const revealEls = document.querySelectorAll('.reveal');
  if (prefersReducedMotion) {
    revealEls.forEach(el => el.classList.add('in-view'));
  } else {
    const revealObserver = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          entry.target.classList.add('in-view');
          revealObserver.unobserve(entry.target);
        }
      });
    }, { threshold: 0.12 });
    revealEls.forEach(el => revealObserver.observe(el));
  }

  /* ---------- Back to top ---------- */
  const backToTop = document.getElementById('backToTop');
  window.addEventListener('scroll', () => {
    backToTop.classList.toggle('visible', window.scrollY > 600);
  }, { passive: true });
  backToTop.addEventListener('click', () => {
    window.scrollTo({ top: 0, behavior: prefersReducedMotion ? 'auto' : 'smooth' });
  });

  /* =========================================================
     PERIOD DATA — dayparts, sky theme, suggestion, boundaries
     ========================================================= */
  const PERIODS = [
    { id: 'morning', label: 'MORNING', start: 5,  suggestion: { name: 'Foundry Drip', desc: 'Ethiopia Guji · floral & citrus' } },
    { id: 'midday',  label: 'MIDDAY',  start: 11, suggestion: { name: 'Meridian Espresso', desc: 'House blend · dark cherry & cocoa' } },
    { id: 'evening', label: 'EVENING', start: 15, suggestion: { name: 'Maple Cardamom Latte', desc: 'House maple-cardamom syrup' } },
    { id: 'night',   label: 'NIGHT',   start: 19, suggestion: { name: 'Decaf Old Fashioned', desc: 'Swiss-water decaf, slow pour' } },
  ];
  const DIAL_MIN = 5, DIAL_MAX = 23;

  const getPeriodForHour = (hour) => {
    let current = PERIODS[0];
    for (const p of PERIODS) { if (hour >= p.start) current = p; }
    return current;
  };

  const formatClock = (hourFloat) => {
    let h = Math.floor(hourFloat);
    const m = Math.round((hourFloat - h) * 60);
    const ampm = h >= 12 ? 'PM' : 'AM';
    let h12 = h % 12; if (h12 === 0) h12 = 12;
    return `${h12}:${String(m).padStart(2, '0')} ${ampm}`;
  };

  /* ---------- Sky layers ---------- */
  const skyLayers = {
    morning: document.getElementById('skyMorning'),
    midday: document.getElementById('skyMidday'),
    evening: document.getElementById('skyEvening'),
    night: document.getElementById('skyNight'),
  };
  const setSky = (periodId) => {
    Object.entries(skyLayers).forEach(([id, el]) => el.classList.toggle('is-active', id === periodId));
    document.body.setAttribute('data-period', periodId);
  };

  /* =========================================================
     DAY DIAL
     ========================================================= */
  const dial = document.getElementById('dayDial');
  const dialSun = document.getElementById('dialSun');
  const dialArcFill = document.getElementById('dialArcFill');
  const dialPeriodEl = document.getElementById('dialPeriod');
  const dialTimeEl = document.getElementById('dialTime');
  const suggestionName = document.getElementById('suggestionName');
  const suggestionDesc = document.getElementById('suggestionDesc');

  const CENTER = { x: 160, y: 170 };
  const RADIUS = 140;
  const arcLength = dialArcFill.getTotalLength();
  dialArcFill.style.strokeDasharray = String(arcLength);

  let currentPeriodId = null;

  const positionForFraction = (f) => {
    const angleDeg = 180 - f * 180; // 180deg = left (min), 0deg = right (max)
    const rad = (angleDeg * Math.PI) / 180;
    const x = CENTER.x + RADIUS * Math.cos(rad);
    const y = CENTER.y - RADIUS * Math.sin(rad);
    return { x, y };
  };

  const renderDial = (value) => {
    const clamped = Math.min(DIAL_MAX, Math.max(DIAL_MIN, value));
    const fraction = (clamped - DIAL_MIN) / (DIAL_MAX - DIAL_MIN);
    const { x, y } = positionForFraction(fraction);

    dialSun.style.left = `${(x / 320) * 100}%`;
    dialSun.style.top = `${(y / 190) * 100}%`;
    dialArcFill.style.strokeDashoffset = String(arcLength * (1 - fraction));

    const period = getPeriodForHour(clamped);
    dialPeriodEl.textContent = period.label;
    dialTimeEl.textContent = formatClock(clamped);
    suggestionName.textContent = period.suggestion.name;
    suggestionDesc.textContent = period.suggestion.desc;

    dial.setAttribute('aria-valuenow', clamped.toFixed(2));
    dial.setAttribute('aria-valuetext', `${formatClock(clamped)}, ${period.label.charAt(0)}${period.label.slice(1).toLowerCase()}`);

    if (period.id !== currentPeriodId) {
      currentPeriodId = period.id;
      setSky(period.id);
      document.querySelectorAll('.daypart').forEach(el => {
        el.classList.toggle('is-current', el.dataset.periodBlock === period.id);
      });
    }
    return clamped;
  };

  const valueFromPointer = (clientX, clientY) => {
    const rect = dial.getBoundingClientRect();
    const px = ((clientX - rect.left) / rect.width) * 320;
    const py = ((clientY - rect.top) / rect.height) * 190;
    let angle = Math.atan2(CENTER.y - py, px - CENTER.x) * (180 / Math.PI);
    angle = Math.min(180, Math.max(0, angle));
    const fraction = 1 - angle / 180;
    return DIAL_MIN + fraction * (DIAL_MAX - DIAL_MIN);
  };

  let dialValue = renderDial((() => {
    const now = new Date();
    return now.getHours() + now.getMinutes() / 60;
  })());

  let dragging = false;
  const startDrag = (clientX, clientY) => {
    dragging = true;
    dialValue = renderDial(valueFromPointer(clientX, clientY));
  };
  const moveDrag = (clientX, clientY) => {
    if (!dragging) return;
    dialValue = renderDial(valueFromPointer(clientX, clientY));
  };
  const endDrag = () => { dragging = false; };

  dial.addEventListener('pointerdown', (e) => { dial.setPointerCapture(e.pointerId); startDrag(e.clientX, e.clientY); });
  dial.addEventListener('pointermove', (e) => moveDrag(e.clientX, e.clientY));
  dial.addEventListener('pointerup', endDrag);
  dial.addEventListener('pointercancel', endDrag);

  dial.addEventListener('keydown', (e) => {
    const step = 0.25;
    if (e.key === 'ArrowRight' || e.key === 'ArrowUp') { e.preventDefault(); dialValue = renderDial(dialValue + step); }
    else if (e.key === 'ArrowLeft' || e.key === 'ArrowDown') { e.preventDefault(); dialValue = renderDial(dialValue - step); }
    else if (e.key === 'Home') { e.preventDefault(); dialValue = renderDial(DIAL_MIN); }
    else if (e.key === 'End') { e.preventDefault(); dialValue = renderDial(DIAL_MAX); }
  });

  /* =========================================================
     LIVE NAV CLOCK + OPEN/CLOSED PILL
     ========================================================= */
  const navClock = document.getElementById('navClock');
  const openPill = document.getElementById('openPill');
  const openText = document.getElementById('openText');

  const updateNavClock = () => {
    const now = new Date();
    navClock.textContent = formatClock(now.getHours() + now.getMinutes() / 60);

    const day = now.getDay(); // 0 Sun ... 6 Sat
    const hour = now.getHours() + now.getMinutes() / 60;
    const isWeekend = day === 0 || day === 6;
    const openHour = isWeekend ? 7 : 6;
    const closeHour = isWeekend ? 21 : 20;
    const isOpen = hour >= openHour && hour < closeHour;

    openPill.classList.toggle('is-closed', !isOpen);
    openText.textContent = isOpen ? 'Open now' : 'Closed';
  };
  updateNavClock();
  setInterval(updateNavClock, 30000);

  /* =========================================================
     NEWSLETTER (no backend — friendly confirmation only)
     ========================================================= */
  const newsletterForm = document.getElementById('newsletterForm');
  const newsletterNote = document.getElementById('newsletterNote');
  newsletterForm.addEventListener('submit', (e) => {
    e.preventDefault();
    newsletterNote.textContent = "You're on the list — look out for Sunday's roast note.";
    newsletterForm.reset();
  });
});
</script>
</body>
</html>
