import { useState, useEffect, useRef } from "react";

// ─── COLOUR PALETTE & GLOBAL STYLES ────────────────────────────────────────
const STYLES = `
  @import url('https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=DM+Sans:wght@300;400;500;600;700&display=swap');

  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --navy:   #0B2545;
    --blue:   #1A56DB;
    --sky:    #3B82F6;
    --teal:   #0891B2;
    --green:  #059669;
    --amber:  #D97706;
    --red:    #DC2626;
    --slate:  #475569;
    --mist:   #F1F5F9;
    --white:  #FFFFFF;
    --border: #E2E8F0;
    --card:   #FFFFFF;
    --shadow: 0 2px 12px rgba(11,37,69,0.08);
    --shadow-lg: 0 8px 32px rgba(11,37,69,0.12);
    --radius: 14px;
    --font-display: 'DM Serif Display', Georgia, serif;
    --font-body: 'DM Sans', system-ui, sans-serif;
  }

  body {
    font-family: var(--font-body);
    background: var(--mist);
    color: var(--navy);
    min-height: 100vh;
    -webkit-font-smoothing: antialiased;
  }

  button { cursor: pointer; font-family: var(--font-body); border: none; background: none; }
  a { color: inherit; text-decoration: none; }
  textarea, input, select { font-family: var(--font-body); }

  .app-shell {
    display: flex;
    flex-direction: column;
    min-height: 100vh;
  }

  /* ── TOP NAV ── */
  .topnav {
    background: var(--navy);
    color: white;
    position: sticky; top: 0; z-index: 100;
    box-shadow: 0 2px 16px rgba(0,0,0,0.18);
  }
  .topnav-inner {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 24px;
    display: flex;
    align-items: center;
    gap: 32px;
    height: 60px;
  }
  .topnav-brand {
    display: flex; flex-direction: column; line-height: 1;
    margin-right: 8px;
  }
  .topnav-brand span:first-child {
    font-family: var(--font-display);
    font-size: 20px;
    letter-spacing: 0.5px;
  }
  .topnav-brand span:last-child {
    font-size: 10px;
    color: rgba(255,255,255,0.55);
    letter-spacing: 1.5px;
    text-transform: uppercase;
    margin-top: 2px;
  }
  .topnav-tabs {
    display: flex; gap: 4px; flex: 1;
  }
  .topnav-tab {
    padding: 8px 16px;
    border-radius: 8px;
    font-size: 13px;
    font-weight: 500;
    color: rgba(255,255,255,0.6);
    transition: all 0.15s;
    display: flex; align-items: center; gap: 6px;
  }
  .topnav-tab:hover { color: white; background: rgba(255,255,255,0.08); }
  .topnav-tab.active { color: white; background: rgba(255,255,255,0.14); }
  .topnav-tab svg { width: 15px; height: 15px; }

  /* ── PAGE WRAPPER ── */
  .page { max-width: 1200px; margin: 0 auto; padding: 28px 24px 60px; }
  .page-wide { max-width: 100%; padding: 28px 24px 60px; }

  /* ── HERO ── */
  .hero {
    background: linear-gradient(135deg, var(--navy) 0%, #1A3A6B 100%);
    color: white;
    border-radius: var(--radius);
    padding: 36px 40px;
    margin-bottom: 28px;
    position: relative;
    overflow: hidden;
  }
  .hero::before {
    content: '';
    position: absolute; inset: 0;
    background: url("data:image/svg+xml,%3Csvg width='60' height='60' viewBox='0 0 60 60' xmlns='http://www.w3.org/2000/svg'%3E%3Cg fill='none' fill-rule='evenodd'%3E%3Cg fill='%23ffffff' fill-opacity='0.03'%3E%3Cpath d='M36 34v-4h-2v4h-4v2h4v4h2v-4h4v-2h-4zm0-30V0h-2v4h-4v2h4v4h2V6h4V4h-4zM6 34v-4H4v4H0v2h4v4h2v-4h4v-2H6zM6 4V0H4v4H0v2h4v4h2V6h4V4H6z'/%3E%3C/g%3E%3C/g%3E%3C/svg%3E");
    pointer-events: none;
  }
  .hero-eyebrow {
    font-size: 11px;
    letter-spacing: 2.5px;
    text-transform: uppercase;
    color: rgba(255,255,255,0.5);
    margin-bottom: 8px;
  }
  .hero-title {
    font-family: var(--font-display);
    font-size: 32px;
    line-height: 1.15;
    margin-bottom: 8px;
  }
  .hero-sub {
    font-size: 14px;
    color: rgba(255,255,255,0.65);
    margin-bottom: 24px;
  }
  .hero-stats {
    display: flex; gap: 20px; flex-wrap: wrap;
  }
  .hero-stat {
    background: rgba(255,255,255,0.1);
    border: 1px solid rgba(255,255,255,0.12);
    border-radius: 8px;
    padding: 10px 16px;
    display: flex; flex-direction: column;
  }
  .hero-stat-val { font-size: 22px; font-weight: 700; }
  .hero-stat-lbl { font-size: 11px; color: rgba(255,255,255,0.55); margin-top: 1px; }

  /* ── CARDS ── */
  .card {
    background: var(--card);
    border-radius: var(--radius);
    box-shadow: var(--shadow);
    border: 1px solid var(--border);
    overflow: hidden;
  }
  .card-body { padding: 20px; }

  /* ── BLOCK GRID ── */
  .blocks-controls {
    display: flex; gap: 12px; margin-bottom: 20px; flex-wrap: wrap; align-items: center;
  }
  .search-box {
    flex: 1; min-width: 200px;
    display: flex; align-items: center; gap: 8px;
    background: white;
    border: 1.5px solid var(--border);
    border-radius: 10px;
    padding: 9px 14px;
  }
  .search-box input {
    border: none; outline: none; flex: 1;
    font-size: 14px; color: var(--navy);
    background: transparent;
  }
  .search-box svg { color: var(--slate); flex-shrink: 0; }
  .filter-chips { display: flex; gap: 6px; flex-wrap: wrap; }
  .chip {
    padding: 6px 14px;
    border-radius: 20px;
    font-size: 12px;
    font-weight: 500;
    border: 1.5px solid var(--border);
    background: white;
    color: var(--slate);
    transition: all 0.15s;
    display: flex; align-items: center; gap: 5px;
  }
  .chip:hover { border-color: var(--blue); color: var(--blue); }
  .chip.active { background: var(--blue); border-color: var(--blue); color: white; }

  .blocks-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
    gap: 16px;
  }
  .block-card {
    background: white;
    border-radius: var(--radius);
    border: 1.5px solid var(--border);
    box-shadow: var(--shadow);
    padding: 20px;
    cursor: pointer;
    transition: all 0.18s;
    display: flex; flex-direction: column; gap: 12px;
  }
  .block-card:hover {
    transform: translateY(-2px);
    box-shadow: var(--shadow-lg);
    border-color: var(--sky);
  }
  .block-card-top { display: flex; gap: 14px; align-items: flex-start; }
  .block-icon {
    width: 48px; height: 48px; border-radius: 12px;
    background: rgba(26,86,219,0.08);
    display: flex; align-items: center; justify-content: center;
    flex-shrink: 0;
    font-size: 22px;
  }
  .block-info { flex: 1; }
  .block-name { font-size: 16px; font-weight: 700; color: var(--navy); line-height: 1.3; }
  .block-sub { font-size: 13px; color: var(--slate); margin-top: 3px; }
  .block-meta { display: flex; gap: 8px; flex-wrap: wrap; align-items: center; }
  .diff-badge {
    font-size: 11px; font-weight: 600; padding: 3px 9px; border-radius: 20px;
    display: flex; align-items: center; gap: 4px;
  }
  .diff-beginner     { background: #D1FAE5; color: #065F46; }
  .diff-intermediate { background: #FEF3C7; color: #92400E; }
  .diff-advanced     { background: #FEE2E2; color: #991B1B; }
  .meta-pill {
    font-size: 11px; color: var(--slate);
    display: flex; align-items: center; gap: 4px;
  }
  .add-btn {
    background: var(--blue);
    color: white;
    border-radius: 10px;
    padding: 10px 20px;
    font-size: 14px; font-weight: 600;
    display: flex; align-items: center; gap: 7px;
    transition: background 0.15s;
    flex-shrink: 0;
  }
  .add-btn:hover { background: #1446B8; }

  /* ── DETAIL VIEW ── */
  .detail-back {
    display: flex; align-items: center; gap: 6px;
    font-size: 13px; font-weight: 500; color: var(--blue);
    margin-bottom: 20px;
    width: fit-content;
  }
  .detail-back:hover { text-decoration: underline; cursor: pointer; }
  .detail-hero {
    background: linear-gradient(135deg, var(--navy) 0%, #1A3A6B 100%);
    border-radius: var(--radius);
    color: white;
    padding: 32px 36px;
    margin-bottom: 24px;
    display: flex; gap: 24px; align-items: flex-start;
    position: relative; overflow: hidden;
  }
  .detail-hero::before {
    content: '';
    position: absolute; inset: 0;
    background: url("data:image/svg+xml,%3Csvg width='40' height='40' viewBox='0 0 40 40' xmlns='http://www.w3.org/2000/svg'%3E%3Cg fill='%23ffffff' fill-opacity='0.03' fill-rule='evenodd'%3E%3Cpath d='M0 40L40 0H20L0 20M40 40V20L20 40'/%3E%3C/g%3E%3C/svg%3E");
    pointer-events: none;
  }
  .detail-hero-icon {
    font-size: 44px; flex-shrink: 0;
    background: rgba(255,255,255,0.1);
    width: 72px; height: 72px;
    border-radius: 16px;
    display: flex; align-items: center; justify-content: center;
  }
  .detail-hero-body { flex: 1; }
  .detail-hero-title {
    font-family: var(--font-display);
    font-size: 28px; line-height: 1.15;
    margin-bottom: 4px;
  }
  .detail-hero-sub {
    font-size: 14px; color: rgba(255,255,255,0.65);
    margin-bottom: 16px;
  }
  .detail-hero-pills { display: flex; gap: 10px; flex-wrap: wrap; }
  .hero-pill {
    background: rgba(255,255,255,0.12);
    border: 1px solid rgba(255,255,255,0.18);
    border-radius: 20px; padding: 5px 12px;
    font-size: 12px; display: flex; align-items: center; gap: 5px;
  }
  .detail-hero-actions {
    display: flex; flex-direction: column; gap: 8px; flex-shrink: 0;
  }
  .detail-action-btn {
    padding: 9px 18px; border-radius: 9px;
    font-size: 13px; font-weight: 600;
    display: flex; align-items: center; gap: 6px;
    transition: all 0.15s; white-space: nowrap;
  }
  .btn-light {
    background: rgba(255,255,255,0.15);
    border: 1px solid rgba(255,255,255,0.25);
    color: white;
  }
  .btn-light:hover { background: rgba(255,255,255,0.22); }
  .btn-quiz {
    background: white;
    color: var(--navy);
  }
  .btn-quiz:hover { background: #EFF6FF; }

  /* ── DETAIL SECTIONS ── */
  .detail-layout {
    display: grid;
    grid-template-columns: 1fr 340px;
    gap: 20px;
    align-items: start;
  }
  @media (max-width: 900px) {
    .detail-layout { grid-template-columns: 1fr; }
    .detail-hero { flex-direction: column; }
    .detail-hero-actions { flex-direction: row; flex-wrap: wrap; }
  }
  .detail-main { display: flex; flex-direction: column; gap: 16px; }
  .detail-sidebar { display: flex; flex-direction: column; gap: 16px; }

  .section-card {
    background: white;
    border-radius: var(--radius);
    border: 1px solid var(--border);
    box-shadow: var(--shadow);
    overflow: hidden;
  }
  .section-header {
    display: flex; align-items: center; gap: 9px;
    padding: 14px 20px;
    border-bottom: 1px solid var(--border);
    cursor: pointer; user-select: none;
  }
  .section-header-icon {
    width: 30px; height: 30px; border-radius: 8px;
    display: flex; align-items: center; justify-content: center;
    font-size: 15px; flex-shrink: 0;
  }
  .section-title {
    font-size: 14px; font-weight: 700; flex: 1;
    color: var(--navy);
  }
  .section-chevron {
    color: var(--slate); font-size: 12px;
    transition: transform 0.2s;
  }
  .section-chevron.open { transform: rotate(180deg); }
  .section-body { padding: 18px 20px; }

  /* ── BULLETS / STEPS ── */
  .bullet-list { display: flex; flex-direction: column; gap: 7px; }
  .bullet-item { display: flex; gap: 9px; align-items: flex-start; }
  .bullet-dot { width: 7px; height: 7px; border-radius: 50%; flex-shrink: 0; margin-top: 6px; }
  .bullet-text { font-size: 14px; color: var(--slate); line-height: 1.5; }

  .steps-list { display: flex; flex-direction: column; gap: 14px; }
  .step-item { display: flex; gap: 14px; align-items: flex-start; }
  .step-num {
    width: 30px; height: 30px; border-radius: 50%;
    background: var(--blue); color: white;
    display: flex; align-items: center; justify-content: center;
    font-size: 13px; font-weight: 700; flex-shrink: 0;
  }
  .step-body { flex: 1; }
  .step-title { font-size: 14px; font-weight: 600; color: var(--navy); margin-bottom: 3px; }
  .step-detail { font-size: 13px; color: var(--slate); line-height: 1.6; }

  /* ── DRUG DOSE CARDS ── */
  .dose-cards { display: flex; flex-direction: column; gap: 10px; }
  .dose-card {
    background: #F0FDFA;
    border: 1px solid #99F6E4;
    border-radius: 10px;
    padding: 14px;
  }
  .dose-card-top { display: flex; justify-content: space-between; align-items: center; margin-bottom: 8px; }
  .dose-drug { font-size: 14px; font-weight: 700; color: var(--navy); }
  .dose-conc {
    background: var(--teal); color: white;
    border-radius: 20px; padding: 2px 10px; font-size: 12px; font-weight: 600;
  }
  .dose-row { display: flex; gap: 20px; margin-bottom: 6px; flex-wrap: wrap; }
  .dose-pair { display: flex; flex-direction: column; }
  .dose-lbl { font-size: 10px; color: var(--slate); text-transform: uppercase; letter-spacing: 0.5px; }
  .dose-val { font-size: 13px; font-weight: 600; color: var(--navy); }
  .dose-note { font-size: 12px; color: var(--slate); font-style: italic; }

  .last-warning {
    background: #FEF2F2;
    border: 1px solid #FECACA;
    border-radius: 10px;
    padding: 12px 14px;
    display: flex; gap: 9px; align-items: flex-start;
    margin-top: 12px;
  }
  .last-warning-icon { color: var(--red); flex-shrink: 0; margin-top: 1px; font-size: 16px; }
  .last-warning-text { font-size: 12px; color: #7F1D1D; line-height: 1.5; }

  /* ── US CARD ── */
  .us-placeholder {
    background: #F8FAFC;
    border: 2px dashed var(--border);
    border-radius: 10px;
    height: 140px;
    display: flex; flex-direction: column; align-items: center; justify-content: center;
    gap: 6px; color: #94A3B8; font-size: 13px;
    margin-bottom: 12px;
  }
  .landmark-list { display: flex; flex-direction: column; gap: 5px; margin-top: 10px; }
  .landmark-item { font-size: 13px; color: var(--slate); display: flex; gap: 7px; }
  .landmark-dot { color: var(--teal); flex-shrink: 0; margin-top: 2px; }

  /* ── RESOURCE LINKS ── */
  .resource-list { display: flex; flex-direction: column; gap: 8px; }
  .resource-item {
    display: flex; align-items: center; gap: 12px;
    padding: 11px 14px;
    background: #F8FAFC;
    border-radius: 10px;
    border: 1px solid var(--border);
    transition: border-color 0.15s;
    cursor: pointer;
  }
  .resource-item:hover { border-color: var(--blue); }
  .resource-icon { font-size: 20px; flex-shrink: 0; }
  .resource-info { flex: 1; }
  .resource-title { font-size: 13px; font-weight: 600; color: var(--navy); }
  .resource-type { font-size: 11px; color: var(--slate); }
  .resource-arrow { color: var(--slate); font-size: 12px; }

  /* ── TIP ROWS ── */
  .tip-item { display: flex; gap: 8px; align-items: flex-start; margin-bottom: 8px; }
  .tip-icon { color: #F59E0B; flex-shrink: 0; margin-top: 1px; }
  .tip-text { font-size: 13px; color: var(--slate); line-height: 1.5; }

  /* ── QUIZ ── */
  .quiz-overlay {
    position: fixed; inset: 0; z-index: 200;
    background: var(--mist);
    display: flex; flex-direction: column;
    overflow-y: auto;
  }
  .quiz-topbar {
    background: var(--navy); color: white;
    padding: 16px 24px;
    display: flex; align-items: center; gap: 16px;
    position: sticky; top: 0; z-index: 10;
  }
  .quiz-progress-bar {
    flex: 1; height: 6px;
    background: rgba(255,255,255,0.2);
    border-radius: 3px; overflow: hidden;
  }
  .quiz-progress-fill {
    height: 100%; background: white; border-radius: 3px;
    transition: width 0.4s ease;
  }
  .quiz-counter { font-size: 13px; font-weight: 500; color: rgba(255,255,255,0.7); white-space: nowrap; }
  .quiz-close-btn {
    background: rgba(255,255,255,0.15);
    color: white;
    border-radius: 7px; padding: 6px 12px;
    font-size: 13px; font-weight: 600;
    flex-shrink: 0;
  }
  .quiz-body { max-width: 680px; margin: 0 auto; padding: 40px 24px 60px; width: 100%; }
  .quiz-question {
    font-family: var(--font-display);
    font-size: 22px; line-height: 1.4;
    color: var(--navy); text-align: center;
    margin-bottom: 32px;
  }
  .quiz-options { display: flex; flex-direction: column; gap: 10px; margin-bottom: 24px; }
  .quiz-option {
    padding: 16px 18px; border-radius: 12px;
    border: 2px solid var(--border);
    background: white; font-size: 15px; color: var(--navy);
    text-align: left; transition: all 0.15s;
    display: flex; align-items: center; gap: 12px;
  }
  .quiz-option:not(:disabled):hover { border-color: var(--blue); background: #EFF6FF; }
  .quiz-option.correct { border-color: var(--green); background: #ECFDF5; }
  .quiz-option.wrong   { border-color: var(--red);   background: #FEF2F2; }
  .quiz-option.dimmed  { opacity: 0.45; }
  .quiz-option-letter {
    width: 28px; height: 28px; border-radius: 50%;
    background: var(--mist); flex-shrink: 0;
    display: flex; align-items: center; justify-content: center;
    font-size: 12px; font-weight: 700;
  }
  .quiz-explanation {
    padding: 16px; border-radius: 12px;
    margin-bottom: 20px; font-size: 14px; line-height: 1.6;
    display: flex; gap: 10px; align-items: flex-start;
  }
  .quiz-explanation.correct { background: #ECFDF5; border: 1px solid #A7F3D0; color: #065F46; }
  .quiz-explanation.wrong   { background: #FEF2F2; border: 1px solid #FECACA; color: #991B1B; }
  .quiz-next-btn {
    width: 100%; padding: 16px;
    background: var(--blue); color: white;
    border-radius: 12px; font-size: 16px; font-weight: 700;
    transition: background 0.15s;
  }
  .quiz-next-btn:hover { background: #1446B8; }

  .quiz-result { text-align: center; padding: 40px 0; }
  .quiz-result-ring {
    width: 160px; height: 160px;
    border-radius: 50%; margin: 0 auto 28px;
    display: flex; flex-direction: column; align-items: center; justify-content: center;
    background: conic-gradient(var(--blue) var(--pct), #E2E8F0 0);
    box-shadow: 0 0 0 10px white, 0 0 0 12px var(--border);
  }
  .quiz-result-score { font-size: 42px; font-weight: 800; color: var(--navy); }
  .quiz-result-grade { font-size: 14px; font-weight: 600; margin-top: 4px; }
  .quiz-result-title { font-family: var(--font-display); font-size: 28px; margin-bottom: 8px; }
  .quiz-result-sub { color: var(--slate); font-size: 15px; margin-bottom: 32px; }
  .quiz-result-btns { display: flex; gap: 12px; justify-content: center; flex-wrap: wrap; }
  .btn-outline {
    padding: 12px 24px; border-radius: 10px;
    border: 2px solid var(--blue); color: var(--blue);
    font-size: 14px; font-weight: 600; transition: all 0.15s;
  }
  .btn-outline:hover { background: var(--blue); color: white; }
  .btn-primary {
    padding: 12px 24px; border-radius: 10px;
    background: var(--blue); color: white;
    font-size: 14px; font-weight: 600; transition: background 0.15s;
  }
  .btn-primary:hover { background: #1446B8; }

  /* ── EDIT MODAL ── */
  .modal-overlay {
    position: fixed; inset: 0; z-index: 300;
    background: rgba(11,37,69,0.5);
    display: flex; align-items: flex-start; justify-content: center;
    padding: 24px;
    overflow-y: auto;
  }
  .modal {
    background: white;
    border-radius: 18px;
    box-shadow: 0 24px 80px rgba(0,0,0,0.18);
    width: 100%; max-width: 760px;
    min-height: min-content;
    margin: auto;
  }
  .modal-header {
    padding: 20px 24px;
    border-bottom: 1px solid var(--border);
    display: flex; align-items: center; justify-content: space-between;
    position: sticky; top: 0; background: white; z-index: 10;
    border-radius: 18px 18px 0 0;
  }
  .modal-title { font-size: 18px; font-weight: 700; }
  .modal-close {
    width: 32px; height: 32px; border-radius: 50%;
    background: var(--mist); font-size: 18px; color: var(--slate);
    display: flex; align-items: center; justify-content: center;
  }
  .modal-close:hover { background: var(--border); }
  .modal-body { padding: 24px; display: flex; flex-direction: column; gap: 20px; }
  .modal-footer {
    padding: 16px 24px;
    border-top: 1px solid var(--border);
    display: flex; justify-content: flex-end; gap: 10px;
  }
  .form-section-title {
    font-size: 11px; font-weight: 700; text-transform: uppercase;
    letter-spacing: 1px; color: var(--slate); margin-bottom: 10px;
    padding-bottom: 8px; border-bottom: 1px solid var(--border);
  }
  .form-row { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
  .form-group { display: flex; flex-direction: column; gap: 5px; }
  .form-label { font-size: 12px; font-weight: 600; color: var(--navy); }
  .form-input, .form-select, .form-textarea {
    border: 1.5px solid var(--border);
    border-radius: 8px; padding: 9px 12px;
    font-size: 14px; color: var(--navy);
    outline: none; transition: border-color 0.15s;
    width: 100%; background: white;
  }
  .form-input:focus, .form-select:focus, .form-textarea:focus { border-color: var(--blue); }
  .form-textarea { resize: vertical; min-height: 80px; line-height: 1.5; }
  .dynamic-list { display: flex; flex-direction: column; gap: 6px; }
  .dynamic-item { display: flex; gap: 7px; }
  .dynamic-item input { flex: 1; }
  .remove-btn {
    color: var(--red); font-size: 18px; line-height: 1;
    width: 30px; flex-shrink: 0; display: flex; align-items: center; justify-content: center;
  }
  .remove-btn:hover { color: #7F1D1D; }
  .add-item-btn {
    font-size: 13px; color: var(--blue); font-weight: 500;
    display: flex; align-items: center; gap: 5px;
    padding: 6px 0; width: fit-content;
  }
  .add-item-btn:hover { text-decoration: underline; }
  .step-editor {
    background: var(--mist); border-radius: 10px; padding: 12px;
    display: flex; flex-direction: column; gap: 8px;
    border: 1px solid var(--border);
  }
  .step-editor-header { display: flex; align-items: center; gap: 8px; }
  .dose-editor {
    background: #F0FDFA; border: 1px solid #99F6E4;
    border-radius: 10px; padding: 12px;
    display: flex; flex-direction: column; gap: 8px;
  }
  .quiz-editor {
    background: #EFF6FF; border: 1px solid #BFDBFE;
    border-radius: 10px; padding: 12px;
    display: flex; flex-direction: column; gap: 8px;
  }
  .quiz-option-editor { display: flex; gap: 7px; align-items: center; }
  .correct-toggle {
    width: 22px; height: 22px; border-radius: 50%; flex-shrink: 0;
    border: 2px solid var(--border);
    display: flex; align-items: center; justify-content: center;
    font-size: 11px; color: transparent; transition: all 0.15s;
  }
  .correct-toggle.selected { background: var(--green); border-color: var(--green); color: white; }
  .resource-editor { display: grid; grid-template-columns: 2fr 2fr 1fr; gap: 8px; }

  /* ── QUICK REF ── */
  .qr-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(360px, 1fr)); gap: 16px; }
  .qr-card {
    background: white; border-radius: var(--radius);
    border: 1px solid var(--border); box-shadow: var(--shadow);
    padding: 18px;
  }
  .qr-card-header {
    display: flex; align-items: center; gap: 10px;
    margin-bottom: 14px; padding-bottom: 12px;
    border-bottom: 1px solid var(--border);
  }
  .qr-card-icon { font-size: 20px; }
  .qr-card-title { font-size: 15px; font-weight: 700; }
  .qr-dose-row {
    display: flex; justify-content: space-between; align-items: flex-start;
    padding: 8px 0; border-bottom: 1px solid #F1F5F9;
  }
  .qr-dose-row:last-of-type { border-bottom: none; }
  .qr-dose-left { flex: 1; }
  .qr-dose-name { font-size: 13px; font-weight: 600; color: var(--navy); }
  .qr-dose-note { font-size: 11px; color: var(--slate); }
  .qr-dose-right { text-align: right; }
  .qr-dose-vol { font-size: 13px; font-weight: 700; color: var(--teal); }
  .qr-dose-max { font-size: 11px; color: var(--slate); }
  .qr-timing {
    display: flex; gap: 16px; margin-top: 10px;
    padding-top: 10px; border-top: 1px solid var(--border);
  }
  .qr-timing-item { display: flex; flex-direction: column; }
  .qr-timing-lbl { font-size: 10px; color: var(--slate); text-transform: uppercase; letter-spacing: 0.5px; }
  .qr-timing-val { font-size: 13px; font-weight: 600; }

  .last-card {
    background: #FEF2F2; border: 1.5px solid #FECACA;
    border-radius: var(--radius); padding: 20px;
    margin-bottom: 20px;
  }
  .last-card-header {
    display: flex; align-items: center; gap: 10px;
    margin-bottom: 12px; cursor: pointer;
  }
  .last-card-title { font-size: 16px; font-weight: 700; color: #991B1B; flex: 1; }
  .last-steps { display: flex; flex-direction: column; gap: 8px; margin-top: 8px; }
  .last-step { display: flex; gap: 10px; align-items: flex-start; }
  .last-step-num {
    width: 24px; height: 24px; border-radius: 50%;
    background: #FCA5A5; color: #7F1D1D;
    font-size: 12px; font-weight: 700; flex-shrink: 0;
    display: flex; align-items: center; justify-content: center;
  }
  .last-step-text { font-size: 13px; color: #7F1D1D; line-height: 1.5; }

  /* ── ABOUT ── */
  .about-stats { display: grid; grid-template-columns: repeat(3,1fr); gap: 12px; margin-bottom: 24px; }
  .about-stat {
    background: white; border-radius: 12px; padding: 18px;
    text-align: center; border: 1px solid var(--border); box-shadow: var(--shadow);
  }
  .about-stat-val { font-size: 32px; font-weight: 800; color: var(--blue); }
  .about-stat-lbl { font-size: 12px; color: var(--slate); margin-top: 2px; }
  .info-cards { display: flex; flex-direction: column; gap: 12px; }
  .info-card {
    background: white; border-radius: 12px; padding: 18px;
    border: 1px solid var(--border); box-shadow: var(--shadow);
    display: flex; gap: 14px;
  }
  .info-card-icon { font-size: 24px; flex-shrink: 0; }
  .info-card-title { font-size: 14px; font-weight: 700; margin-bottom: 5px; }
  .info-card-body { font-size: 13px; color: var(--slate); line-height: 1.6; }

  /* ── UTILS ── */
  .tag {
    font-size: 11px; font-weight: 600; padding: 3px 9px; border-radius: 20px;
    border: 1px solid; display: inline-flex; align-items: center; gap: 4px;
  }
  .divider { height: 1px; background: var(--border); margin: 14px 0; }
  .prose { font-size: 14px; color: var(--slate); line-height: 1.7; }
  .section-sub-title { font-size: 12px; font-weight: 700; text-transform: uppercase; letter-spacing: 0.5px; color: var(--slate); margin: 12px 0 8px; }

  @media (max-width: 600px) {
    .topnav-inner { padding: 0 16px; gap: 12px; }
    .topnav-tab span { display: none; }
    .hero { padding: 24px 20px; }
    .page { padding: 20px 16px 50px; }
    .hero-title { font-size: 24px; }
    .detail-hero { padding: 20px; }
    .form-row { grid-template-columns: 1fr; }
    .about-stats { grid-template-columns: repeat(3,1fr); }
    .resource-editor { grid-template-columns: 1fr; }
    .blocks-grid { grid-template-columns: 1fr; }
    .qr-grid { grid-template-columns: 1fr; }
  }
`;

// ─── SAMPLE DATA ────────────────────────────────────────────────────────────
const INITIAL_BLOCKS = [
  {
    id: "1", name: "Fascia Iliaca Block", subtitle: "Landmark & ultrasound-guided hip analgesia",
    category: "Lower Limb", difficulty: "Beginner", time: "10–15 min",
    icon: "🦵",
    overview: "The fascia iliaca compartment block (FICB) is a widely used technique in emergency medicine for hip fracture analgesia. Local anaesthetic is deposited beneath the fascia iliaca, spreading to block the femoral, lateral femoral cutaneous, and obturator nerves.",
    indications: ["Hip fracture (neck of femur, intertrochanteric, subtrochanteric)","Femoral shaft fracture","Knee surgery (multimodal analgesia)","Anterior thigh lacerations/burns","Positioning for spinal anaesthesia"],
    contraindications: ["Patient refusal","Infection at injection site","Known allergy to local anaesthetic"],
    relativeContraindications: ["Anticoagulation therapy","Previous surgery/scarring in region","Unable to cooperate / communicate"],
    anatomy: "The fascia iliaca is a dense fascial layer covering the iliacus muscle in the iliac fossa. It extends from the iliac crest to the inguinal ligament, creating a compartment that communicates with the femoral nerve sheath. The femoral nerve (L2–L4) lies lateral to the femoral artery beneath this fascia.",
    anatomyKeyPoints: ["Femoral nerve: L2, L3, L4 posterior divisions","Lies lateral to femoral artery at the inguinal ligament","Fascia iliaca separates the nerve from overlying structures","Lateral femoral cutaneous nerve (L2–L3) — pure sensory","Obturator nerve variably blocked (30–50% of cases)"],
    technique: [
      { order:1, title:"Patient Positioning", detail:"Position patient supine. Expose the groin and upper thigh. Identify the anterior superior iliac spine (ASIS) and pubic tubercle." },
      { order:2, title:"Equipment Preparation", detail:"Prepare: high-frequency linear probe (10–15 MHz), 80mm blunt-tip block needle, 30–40ml local anaesthetic, sterile gloves, drape, and ultrasound gel." },
      { order:3, title:"Ultrasound Identification", detail:"Place probe transversely below inguinal ligament. Identify the femoral artery (pulsatile, anechoic). The femoral nerve lies lateral — hyperechoic triangular structure. Identify fascia iliaca as the bright fascial layer above iliacus muscle." },
      { order:4, title:"Needle Insertion", detail:"Insert needle in-plane from lateral to medial. Target the plane deep to fascia iliaca, lateral to femoral nerve. Use hydrodissection with 1–2ml saline to confirm correct plane." },
      { order:5, title:"Injection", detail:"After negative aspiration, inject 30–40ml LA with real-time visualisation. Observe spread beneath fascia iliaca tracking medially." },
      { order:6, title:"Documentation", detail:"Document: consent, indication, drug/dose, batch number, technique, any complications, post-procedure neurovascular assessment." }
    ],
    equipment: ["Ultrasound machine with linear probe (10–15 MHz)","80mm 21G blunt-tip block needle","20ml syringe × 2","0.9% sodium chloride for hydrodissection","Local anaesthetic (see doses)","Sterile gloves, drape, and gel","Monitoring: SpO₂, ECG, NIBP","IV access and resuscitation equipment"],
    ultrasoundViews: [
      { title:"Transverse Inguinal View", description:"Standard view for FICB. Probe placed transversely 1–2cm below inguinal ligament, lateral to femoral pulse.", landmarks:["Femoral artery (FA) — pulsatile anechoic circle","Femoral vein — medial to artery, compressible","Femoral nerve — lateral to FA, hyperechoic triangle","Fascia iliaca — bright fascial line over iliacus","Iliacus muscle — hypoechoic, fan-shaped"] }
    ],
    drugDoses: [
      { drug:"Levobupivacaine 0.25%", conc:"0.25%", vol:"30–40ml", max:"2mg/kg", notes:"Preferred agent. Onset 15–30 min, duration 8–12h" },
      { drug:"Bupivacaine 0.25%", conc:"0.25%", vol:"30–40ml", max:"2mg/kg", notes:"Alternative. Max 150mg total" },
      { drug:"Ropivacaine 0.375%", conc:"0.375%", vol:"30ml", max:"3mg/kg", notes:"Less cardiotoxic. Duration 6–10h" }
    ],
    onsetTime: "15–30 minutes", duration: "8–16 hours",
    complications: ["Intravascular injection / LAST","Femoral nerve injury (rare with US guidance)","Haematoma","Infection","Failure of block (10–20%)","Quadriceps weakness — fall risk"],
    tips: ["Inject slowly in 5ml increments, aspirate every 5ml","Lateral approach reduces vascular risk","Ensure spread beneath fascia iliaca, not above","Consider dexamethasone 4mg IV to prolong duration","Document quadriceps motor block and advise on fall risk"],
    resources: [
      { title:"RCEM FICB Guideline 2023", url:"https://rcem.ac.uk", type:"Guideline" },
      { title:"NYSORA Fascia Iliaca Block", url:"https://www.nysora.com/fascia-iliaca-compartment-block", type:"Article" }
    ],
    quiz: [
      { question:"Which nerve is NOT reliably blocked by a fascia iliaca compartment block?", options:["Femoral nerve","Lateral femoral cutaneous nerve","Sciatic nerve","Obturator nerve (partially)"], correct:2, explanation:"The sciatic nerve is not within the fascia iliaca compartment and is not blocked by FICB. The femoral and lateral femoral cutaneous nerves are reliably blocked; the obturator is inconsistently covered." },
      { question:"What is the maximum safe dose of levobupivacaine for a 70kg patient?", options:["50mg","100mg","140mg","200mg"], correct:2, explanation:"Maximum dose of levobupivacaine is 2mg/kg. For a 70kg patient: 70 × 2 = 140mg. At 0.25% (2.5mg/ml), this equates to 56ml — sufficient for a standard 30–40ml FICB." },
      { question:"On ultrasound, the femoral nerve appears as:", options:["A pulsatile anechoic structure","A hyperechoic triangular structure lateral to the femoral artery","A hypoechoic oval structure medial to the femoral vein","An anechoic structure deep to iliacus"], correct:1, explanation:"The femoral nerve appears as a hyperechoic (bright), triangular or oval structure lateral to the femoral artery, lying on the surface of the iliacus muscle beneath the fascia iliaca." }
    ]
  },
  {
    id: "2", name: "Femoral Nerve Block", subtitle: "Targeted femoral nerve anaesthesia",
    category: "Lower Limb", difficulty: "Intermediate", time: "10 min",
    icon: "🦴",
    overview: "A femoral nerve block provides targeted analgesia to the anterior thigh and knee by blocking the femoral nerve (L2–L4) as it passes beneath the inguinal ligament. It is more targeted than FICB, with a lower volume requirement.",
    indications: ["Femoral shaft fracture","Knee replacement / arthroscopy","Anterior thigh surgery","Hip surgery (combined with other blocks)","Patella fracture"],
    contraindications: ["Patient refusal","Infection at injection site","Allergy to local anaesthetic"],
    relativeContraindications: ["Pre-existing femoral neuropathy","Anticoagulation","Obesity (technical difficulty)"],
    anatomy: "The femoral nerve (L2–L4) is the largest branch of the lumbar plexus. It exits the pelvis beneath the inguinal ligament, lateral to the femoral artery, within the femoral triangle. It divides almost immediately into anterior (sensory) and posterior (motor + saphenous) divisions.",
    anatomyKeyPoints: ["Femoral nerve: L2, L3, L4","Lateral to femoral artery at inguinal ligament","Within femoral sheath (loose areolar tissue)","Divides 3–4cm below inguinal ligament","Saphenous nerve is the terminal sensory branch"],
    technique: [
      { order:1, title:"Setup & Positioning", detail:"Patient supine, leg slightly externally rotated. Identify femoral pulse. High-frequency linear probe placed transversely at the inguinal crease." },
      { order:2, title:"Identify Structures", detail:"Identify femoral artery (pulsatile), femoral vein (medial, compressible), and femoral nerve (lateral to artery — hyperechoic, triangular)." },
      { order:3, title:"Needle Placement", detail:"Insert needle in-plane, lateral to medial. Advance tip to the lateral aspect of the femoral nerve, between the nerve and the fascia iliaca. Avoid intraneural placement." },
      { order:4, title:"Injection", detail:"Inject 15–20ml local anaesthetic with real-time visualisation, watching spread circumferentially around the nerve." }
    ],
    equipment: ["Linear ultrasound probe (10–15 MHz)","50–80mm 22G block needle","15–20ml syringe","Local anaesthetic","Sterile preparation"],
    ultrasoundViews: [
      { title:"Femoral Triangle View", description:"Probe transverse at inguinal crease. Femoral artery central, nerve lateral.", landmarks:["Femoral artery — pulsatile anechoic","Femoral vein — medial, collapsible","Femoral nerve — lateral, hyperechoic","Fascia iliaca — superficial to nerve","Iliopsoas muscle — deep"] }
    ],
    drugDoses: [
      { drug:"Levobupivacaine 0.25%", conc:"0.25%", vol:"15–20ml", max:"2mg/kg", notes:"Onset 10–20 min, duration 8–12h" },
      { drug:"Ropivacaine 0.5%", conc:"0.5%", vol:"15–20ml", max:"3mg/kg", notes:"Faster onset. Duration 6–10h" }
    ],
    onsetTime: "10–20 minutes", duration: "8–12 hours",
    complications: ["LAST — intravascular injection","Intraneural injection — nerve injury","Haematoma (femoral vessels adjacent)","Quadriceps weakness and fall risk","Infection"],
    tips: ["Never inject if high resistance felt — may indicate intraneural placement","Visualise spread around nerve in real-time","Warn patient about quadriceps weakness — falls risk"],
    resources: [{ title:"NYSORA Femoral Nerve Block", url:"https://www.nysora.com/femoral-nerve-block", type:"Article" }],
    quiz: [
      { question:"The femoral nerve is derived from which nerve roots?", options:["L1–L3","L2–L4","L3–L5","L4–S1"], correct:1, explanation:"The femoral nerve arises from the posterior divisions of L2, L3, and L4 of the lumbar plexus." },
      { question:"Compared to FICB, femoral nerve block typically requires:", options:["More local anaesthetic volume","The same volume","Less local anaesthetic volume","No local anaesthetic"], correct:2, explanation:"Femoral nerve block targets the nerve directly and requires 15–20ml, whereas FICB needs 30–40ml to achieve adequate compartment spread." }
    ]
  },
  {
    id: "3", name: "Serratus Anterior Plane Block", subtitle: "Lateral thoracic wall analgesia",
    category: "Truncal", difficulty: "Intermediate", time: "10–15 min",
    icon: "🫁",
    overview: "The Serratus Anterior Plane Block (SAPB) provides excellent analgesia to the lateral chest wall by targeting the long thoracic and thoracodorsal nerves, plus the lateral cutaneous branches of intercostal nerves T2–T9. It is particularly valuable for rib fractures and thoracic trauma.",
    indications: ["Multiple rib fractures","Thoracic trauma","Pneumothorax pain","Post-thoracotomy analgesia","Breast surgery","PICC line insertion analgesia"],
    contraindications: ["Patient refusal","Infection at injection site","Allergy to local anaesthetic"],
    relativeContraindications: ["Coagulopathy","Chest wall tumour at injection site","Inability to position appropriately"],
    anatomy: "The serratus anterior muscle overlies the lateral chest wall, originating from ribs 1–9. The long thoracic nerve (C5–C7) runs on its superficial surface. LA deposited either superficial or deep to serratus anterior spreads to block lateral cutaneous branches of intercostal nerves (T2–T9).",
    anatomyKeyPoints: ["Serratus anterior: originates ribs 1–9, inserts medial border of scapula","Long thoracic nerve (C5–C7): superficial to serratus anterior","Thoracodorsal nerve: superficial to serratus anterior","Lateral cutaneous branches of T2–T9: blocked by spread","Deep injection (between serratus and ribs) is more reliable for rib fractures"],
    technique: [
      { order:1, title:"Positioning", detail:"Patient in lateral decubitus (affected side up) or supine with ipsilateral arm elevated. Mid-axillary line approach at the 4th–5th rib level." },
      { order:2, title:"Probe Placement", detail:"High-frequency linear probe placed in the mid-axillary line at the 4th rib level in a sagittal orientation. Identify rib shadows, intercostal muscles, and serratus anterior." },
      { order:3, title:"Identify Layers", detail:"Superficial to deep: skin → subcutaneous fat → latissimus dorsi → serratus anterior → ribs/intercostals. Target plane is deep (between serratus and ribs)." },
      { order:4, title:"Needle Insertion", detail:"Insert 80mm needle in-plane, cephalad to caudad. Advance to target fascial plane. Hydrodissect with 2ml saline to confirm correct plane." },
      { order:5, title:"Injection", detail:"Inject 20–30ml of local anaesthetic slowly with visualisation. Aim for extensive craniocaudal spread for maximal rib coverage." }
    ],
    equipment: ["Linear probe (10–15 MHz)","80–100mm 21G block needle","30ml syringe","Local anaesthetic 30–40ml total","Sterile preparation","Monitoring and IV access"],
    ultrasoundViews: [
      { title:"Mid-Axillary Sagittal View", description:"Sagittal probe in mid-axillary line at 4th–5th rib level.", landmarks:["Rib shadowing — bright echogenic lines with posterior shadow","Intercostal muscles — between rib shadows","Serratus anterior — fan-shaped hypoechoic muscle","Latissimus dorsi — superficial layer","Pleural line — deep, bright, sliding movement"] }
    ],
    drugDoses: [
      { drug:"Levobupivacaine 0.25%", conc:"0.25%", vol:"30ml", max:"2mg/kg", notes:"Standard choice. Duration 8–16h. Consider catheter for continuous infusion" },
      { drug:"Ropivacaine 0.2%", conc:"0.2%", vol:"30–40ml", max:"3mg/kg", notes:"Suitable for continuous infusion if catheter placed" }
    ],
    onsetTime: "15–20 minutes", duration: "8–16 hours",
    complications: ["Pneumothorax (rare with good technique)","LAST","Haematoma","Infection","Inadequate analgesia (wrong plane)"],
    tips: ["Deep injection is more reliable for rib fractures than superficial","Visualise pleural line throughout — stop if needle approaches pleura","Large volume improves craniocaudal spread","Catheter placement allows 48–72h infusion for major rib fractures"],
    resources: [
      { title:"RCEM Pain Management Toolkit", url:"https://rcem.ac.uk/pain", type:"Guideline" },
      { title:"ESRA SAPB Consensus", url:"https://esraeurope.org/sapb", type:"Article" }
    ],
    quiz: [
      { question:"Which intercostal nerve levels does the SAPB typically cover?", options:["T1–T6","T2–T9","T4–T12","T1–T4"], correct:1, explanation:"The SAPB blocks lateral cutaneous branches of intercostal nerves T2–T9 by spreading in the fascial plane around serratus anterior." },
      { question:"For rib fractures, the preferred injection plane for SAPB is:", options:["Superficial to serratus anterior","Deep to serratus anterior (between serratus and ribs)","Within the serratus muscle","Above latissimus dorsi"], correct:1, explanation:"Deep injection is generally preferred for rib fracture analgesia as it provides more consistent spread to intercostal nerve branches." },
      { question:"Which nerve runs on the superficial surface of serratus anterior?", options:["Phrenic nerve","Long thoracic nerve","Thoracodorsal nerve","Intercostobrachial nerve"], correct:1, explanation:"The long thoracic nerve (C5–C7) runs on the superficial (outer) surface of serratus anterior, innervating it." }
    ]
  },
  {
    id: "4", name: "Erector Spinae Plane Block", subtitle: "Posterior thoracic multilevel analgesia",
    category: "Thoracic", difficulty: "Intermediate", time: "15–20 min",
    icon: "🦾",
    overview: "The Erector Spinae Plane Block (ESPB) is a paraspinal interfascial block where local anaesthetic is deposited deep to the erector spinae muscle, anterior to the transverse process. It provides multilevel thoracic analgesia through spread to dorsal and ventral rami.",
    indications: ["Rib fractures (multiple / bilateral)","Thoracic trauma","Post-thoracotomy / VATS analgesia","Thoracic back pain","Breast surgery","Abdominal surgery (T6–T9 level)"],
    contraindications: ["Patient refusal","Infection at injection site","Allergy to local anaesthetic"],
    relativeContraindications: ["Spinal deformity (scoliosis)","Coagulopathy","Previous spinal surgery at target level"],
    anatomy: "The erector spinae muscle group (iliocostalis, longissimus, spinalis) lies in the paraspinal groove posterior to the transverse processes. The fascial plane deep to erector spinae communicates with the paravertebral space via the costotransverse foramen.",
    anatomyKeyPoints: ["Erector spinae: iliocostalis, longissimus, spinalis","Transverse process: key bony landmark — hyperechoic with deep shadowing","Target plane: between erector spinae and transverse process","T5 injection → coverage T3–T9 (variable)","Spread via costotransverse foramen into paravertebral space"],
    technique: [
      { order:1, title:"Positioning", detail:"Patient sitting, lateral decubitus, or prone. Identify midline (spinous processes) and count to target level (usually T5 for thoracic coverage)." },
      { order:2, title:"Probe Orientation", detail:"High-frequency linear or curvilinear probe in a paramedian sagittal orientation, 3cm lateral to midline. Identify transverse processes (squared, hyperechoic)." },
      { order:3, title:"Identify Layers", detail:"Superficial to deep: skin → trapezius → rhomboid → erector spinae → transverse process. Pleural line visible deeper, lateral to transverse process." },
      { order:4, title:"Needle Insertion", detail:"Insert 80–100mm needle in-plane. Advance tip to contact the transverse process — between transverse process and erector spinae muscle." },
      { order:5, title:"Confirm & Inject", detail:"Hydrodissect with 2–3ml saline — fluid should lift erector spinae off transverse process. Inject 20–30ml LA slowly with visualisation of cranio-caudal spread." }
    ],
    equipment: ["Linear (10–15 MHz) or curved probe","80–100mm 21G Tuohy or block needle","30ml syringe","Saline for hydrodissection","Local anaesthetic 20–30ml per side","Catheter set (if continuous block planned)"],
    ultrasoundViews: [
      { title:"Paramedian Sagittal View", description:"3cm lateral to midline, sagittal probe. Shows characteristic transverse process pattern.", landmarks:["Transverse processes — flat, squared, hyperechoic","Erector spinae muscle — over transverse processes","Costotransverse membrane — between transverse processes","Pleural line — deep, lateral, sliding","Target: deep to erector spinae, superficial to TP"] },
      { title:"TP vs Rib Distinction", description:"Key skill: transverse processes are flat and squared; ribs are rounded.", landmarks:["Transverse process — flat top, squared shoulders","Rib — rounded, semicircular top","Erector spinae group — overlying muscle"] }
    ],
    drugDoses: [
      { drug:"Levobupivacaine 0.25%", conc:"0.25%", vol:"20–30ml/side", max:"2mg/kg total", notes:"Standard choice. Bilateral blocks require careful dose calculation. Duration 10–16h" },
      { drug:"Ropivacaine 0.375%", conc:"0.375%", vol:"20ml/side", max:"3mg/kg total", notes:"Preferred for bilateral blocks — lower cardiotoxicity" }
    ],
    onsetTime: "20–30 minutes", duration: "12–24 hours",
    complications: ["LAST (bilateral — cumulative dose)","Pneumothorax (rare — keep needle medial)","Haematoma","Infection","Epidural/intrathecal spread (rare)","Incomplete block"],
    tips: ["Always calculate total bilateral dose before proceeding","Hydrodissection with saline confirms correct plane","Aim for craniocaudal fluid spread — confirms good plane","If fluid moves anteriorly past TP, reposition","Catheter at T5 enables 48–72h infusion for major rib fractures"],
    resources: [
      { title:"ESRA ESPB Guidelines 2023", url:"https://esraeurope.org/espb", type:"Guideline" },
      { title:"Forero et al. — Original ESPB Description", url:"https://pubmed.ncbi.nlm.nih.gov/27501016", type:"Article" }
    ],
    quiz: [
      { question:"LA spread in the ESPB accesses the paravertebral space via:", options:["The intervertebral foramen","The costotransverse foramen","The epidural space","Direct diffusion through muscle"], correct:1, explanation:"LA deposited deep to erector spinae spreads medially through the costotransverse foramen into the paravertebral space, blocking both ventral and dorsal rami." },
      { question:"On ultrasound, transverse processes can be distinguished from ribs by:", options:["Rounded shape — transverse process","Flat with squared shoulders — transverse process; rounded — rib","Both appear identical","Only ribs produce posterior acoustic shadowing"], correct:1, explanation:"Transverse processes appear flat on top with squared shoulders. Ribs appear rounded/semicircular. Both produce posterior shadowing, but the flat appearance is the key distinguishing feature." },
      { question:"An injection at T5 would be expected to provide coverage from approximately:", options:["T1–T5","T3–T9","T5–T12","T1–T12"], correct:1, explanation:"An injection of adequate volume (20–30ml) at T5 typically spreads 2–4 dermatomal levels cranially and caudally, providing coverage from approximately T3 to T9." }
    ]
  }
];

const RESOURCE_ICONS = { Video:"🎬", Article:"📄", Guideline:"✅", Image:"🖼️" };
const DIFF_LABELS = { Beginner:"diff-beginner", Intermediate:"diff-intermediate", Advanced:"diff-advanced" };

// ─── HELPERS ────────────────────────────────────────────────────────────────
function useLocalStorage(key, initial) {
  const [val, setVal] = useState(() => {
    try {
      const stored = localStorage.getItem(key);
      return stored ? JSON.parse(stored) : initial;
    } catch { return initial; }
  });
  const set = (v) => {
    setVal(v);
    try { localStorage.setItem(key, JSON.stringify(typeof v === "function" ? v(val) : v)); }
    catch {}
  };
  return [val, set];
}

function uid() { return Math.random().toString(36).slice(2); }

// ─── COLLAPSIBLE SECTION ────────────────────────────────────────────────────
function Section({ icon, title, color, bg, defaultOpen = true, children }) {
  const [open, setOpen] = useState(defaultOpen);
  return (
    <div className="section-card">
      <div className="section-header" onClick={() => setOpen(o => !o)}>
        <div className="section-header-icon" style={{ background: bg }}>
          <span>{icon}</span>
        </div>
        <span className="section-title">{title}</span>
        <span className={`section-chevron ${open ? "open" : ""}`}>▼</span>
      </div>
      {open && <div className="section-body">{children}</div>}
    </div>
  );
}

// ─── QUIZ ───────────────────────────────────────────────────────────────────
function QuizModal({ block, onClose }) {
  const [idx, setIdx] = useState(0);
  const [answers, setAnswers] = useState({});
  const [chosen, setChosen] = useState(null);
  const [done, setDone] = useState(false);

  const qs = block.quiz || [];
  const q = qs[idx];
  const total = qs.length;
  const score = qs.filter((q, i) => answers[i] === q.correct).length;

  function pick(i) {
    if (chosen !== null) return;
    setChosen(i);
    setAnswers(a => ({ ...a, [idx]: i }));
  }
  function next() {
    if (idx < total - 1) { setIdx(i => i + 1); setChosen(null); }
    else setDone(true);
  }
  function retry() { setIdx(0); setAnswers({}); setChosen(null); setDone(false); }

  const pct = total ? score / total : 0;
  const grade = pct >= 0.9 ? "Excellent 🏆" : pct >= 0.7 ? "Good 👍" : pct >= 0.5 ? "Fair 📚" : "Keep Practising 💪";
  const gradeColor = pct >= 0.9 ? "#059669" : pct >= 0.7 ? "#1A56DB" : pct >= 0.5 ? "#D97706" : "#DC2626";
  const deg = Math.round(pct * 360);

  return (
    <div className="quiz-overlay">
      <div className="quiz-topbar">
        {!done && <>
          <div className="quiz-progress-bar">
            <div className="quiz-progress-fill" style={{ width: `${((idx + (chosen !== null ? 1 : 0)) / total) * 100}%` }} />
          </div>
          <span className="quiz-counter">{idx + 1} / {total}</span>
        </>}
        <button className="quiz-close-btn" onClick={onClose}>✕ Close</button>
      </div>
      <div className="quiz-body">
        {done ? (
          <div className="quiz-result">
            <div className="quiz-result-ring" style={{ "--pct": `${deg}deg`, background: `conic-gradient(${gradeColor} ${deg}deg, #E2E8F0 0)` }}>
              <div className="quiz-result-score">{score}/{total}</div>
              <div className="quiz-result-grade" style={{ color: gradeColor }}>{Math.round(pct * 100)}%</div>
            </div>
            <div className="quiz-result-title">Quiz Complete!</div>
            <div className="quiz-result-sub" style={{ color: gradeColor, fontWeight: 600 }}>{grade}</div>
            <div className="quiz-result-sub">{block.name}</div>
            <div className="quiz-result-btns">
              <button className="btn-outline" onClick={retry}>Try Again</button>
              <button className="btn-primary" onClick={onClose}>Back to Block</button>
            </div>
          </div>
        ) : q ? (
          <>
            <div className="quiz-question">{q.question}</div>
            <div className="quiz-options">
              {q.options.map((opt, i) => {
                let cls = "quiz-option";
                if (chosen !== null) {
                  if (i === q.correct) cls += " correct";
                  else if (i === chosen && chosen !== q.correct) cls += " wrong";
                  else cls += " dimmed";
                }
                return (
                  <button key={i} className={cls} disabled={chosen !== null} onClick={() => pick(i)}>
                    <span className="quiz-option-letter">{String.fromCharCode(65 + i)}</span>
                    {opt}
                  </button>
                );
              })}
            </div>
            {chosen !== null && (
              <>
                <div className={`quiz-explanation ${chosen === q.correct ? "correct" : "wrong"}`}>
                  <span style={{ fontSize: 20 }}>{chosen === q.correct ? "✅" : "❌"}</span>
                  <span>{q.explanation}</span>
                </div>
                <button className="quiz-next-btn" onClick={next}>
                  {idx < total - 1 ? "Next Question →" : "See Results"}
                </button>
              </>
            )}
          </>
        ) : <p>No questions available.</p>}
      </div>
    </div>
  );
}

// ─── EDIT MODAL ─────────────────────────────────────────────────────────────
function EditModal({ block, onSave, onClose }) {
  const isNew = !block;
  const [b, setB] = useState(block ? JSON.parse(JSON.stringify(block)) : {
    id: uid(), name: "", subtitle: "", category: "Lower Limb", difficulty: "Beginner",
    time: "10–15 min", icon: "💉", overview: "",
    indications: [""], contraindications: [""], relativeContraindications: [""],
    anatomy: "", anatomyKeyPoints: [""],
    technique: [{ order:1, title:"", detail:"" }],
    equipment: [""],
    ultrasoundViews: [],
    drugDoses: [{ drug:"", conc:"", vol:"", max:"", notes:"" }],
    onsetTime: "", duration: "",
    complications: [""], tips: [""],
    resources: [{ title:"", url:"", type:"Article" }],
    quiz: [{ question:"", options:["","","",""], correct:0, explanation:"" }]
  });

  const upd = (key, val) => setB(prev => ({ ...prev, [key]: val }));
  const updList = (key, i, val) => { const a = [...b[key]]; a[i] = val; upd(key, a); };
  const addItem = (key, empty) => upd(key, [...b[key], empty]);
  const removeItem = (key, i) => upd(key, b[key].filter((_, j) => j !== i));

  function StringList({ field, placeholder }) {
    return (
      <div className="dynamic-list">
        {b[field].map((item, i) => (
          <div key={i} className="dynamic-item">
            <input className="form-input" value={item} placeholder={placeholder}
              onChange={e => updList(field, i, e.target.value)} />
            <button className="remove-btn" onClick={() => removeItem(field, i)}>×</button>
          </div>
        ))}
        <button className="add-item-btn" onClick={() => addItem(field, "")}>＋ Add item</button>
      </div>
    );
  }

  return (
    <div className="modal-overlay" onClick={e => e.target === e.currentTarget && onClose()}>
      <div className="modal">
        <div className="modal-header">
          <span className="modal-title">{isNew ? "Add New Block" : `Edit — ${b.name}`}</span>
          <button className="modal-close" onClick={onClose}>×</button>
        </div>
        <div className="modal-body">
          {/* Identity */}
          <div>
            <div className="form-section-title">Block Identity</div>
            <div className="form-row" style={{ marginBottom: 10 }}>
              <div className="form-group">
                <label className="form-label">Block Name *</label>
                <input className="form-input" value={b.name} onChange={e => upd("name", e.target.value)} placeholder="e.g. Fascia Iliaca Block" />
              </div>
              <div className="form-group">
                <label className="form-label">Subtitle</label>
                <input className="form-input" value={b.subtitle} onChange={e => upd("subtitle", e.target.value)} placeholder="Brief description" />
              </div>
            </div>
            <div className="form-row" style={{ marginBottom: 10 }}>
              <div className="form-group">
                <label className="form-label">Category</label>
                <select className="form-select" value={b.category} onChange={e => upd("category", e.target.value)}>
                  {["Lower Limb","Upper Limb","Thoracic","Truncal"].map(c => <option key={c}>{c}</option>)}
                </select>
              </div>
              <div className="form-group">
                <label className="form-label">Difficulty</label>
                <select className="form-select" value={b.difficulty} onChange={e => upd("difficulty", e.target.value)}>
                  {["Beginner","Intermediate","Advanced"].map(d => <option key={d}>{d}</option>)}
                </select>
              </div>
            </div>
            <div className="form-row">
              <div className="form-group">
                <label className="form-label">Icon (emoji)</label>
                <input className="form-input" value={b.icon} onChange={e => upd("icon", e.target.value)} placeholder="🩺" maxLength={2} />
              </div>
              <div className="form-group">
                <label className="form-label">Estimated Time</label>
                <input className="form-input" value={b.time} onChange={e => upd("time", e.target.value)} placeholder="10–15 min" />
              </div>
            </div>
          </div>

          {/* Overview */}
          <div>
            <div className="form-section-title">Overview</div>
            <textarea className="form-textarea" value={b.overview} onChange={e => upd("overview", e.target.value)} placeholder="Brief overview of the block..." rows={3} />
          </div>

          {/* Indications */}
          <div>
            <div className="form-section-title">Indications</div>
            <StringList field="indications" placeholder="Add indication" />
          </div>
          <div>
            <div className="form-section-title">Absolute Contraindications</div>
            <StringList field="contraindications" placeholder="Add contraindication" />
          </div>
          <div>
            <div className="form-section-title">Relative Contraindications</div>
            <StringList field="relativeContraindications" placeholder="Add relative contraindication" />
          </div>

          {/* Anatomy */}
          <div>
            <div className="form-section-title">Anatomy</div>
            <textarea className="form-textarea" value={b.anatomy} onChange={e => upd("anatomy", e.target.value)} placeholder="Anatomical description..." rows={3} />
            <div style={{ marginTop: 10 }}>
              <div className="form-label" style={{ marginBottom: 6 }}>Key Points</div>
              <StringList field="anatomyKeyPoints" placeholder="Add key anatomical point" />
            </div>
          </div>

          {/* Technique */}
          <div>
            <div className="form-section-title">Technique Steps</div>
            <div className="dynamic-list">
              {b.technique.map((step, i) => (
                <div key={i} className="step-editor">
                  <div className="step-editor-header">
                    <span className="step-num" style={{ width:26, height:26, fontSize:12 }}>{i+1}</span>
                    <input className="form-input" value={step.title} placeholder="Step title"
                      style={{ flex:1 }}
                      onChange={e => { const a=[...b.technique]; a[i]={...a[i],title:e.target.value}; upd("technique",a); }} />
                    <button className="remove-btn" onClick={() => removeItem("technique", i)}>×</button>
                  </div>
                  <textarea className="form-textarea" value={step.detail} placeholder="Step detail..." rows={2}
                    onChange={e => { const a=[...b.technique]; a[i]={...a[i],detail:e.target.value}; upd("technique",a); }} />
                </div>
              ))}
              <button className="add-item-btn" onClick={() => addItem("technique", { order: b.technique.length+1, title:"", detail:"" })}>＋ Add Step</button>
            </div>
          </div>

          {/* Equipment */}
          <div>
            <div className="form-section-title">Equipment Required</div>
            <StringList field="equipment" placeholder="Add equipment item" />
          </div>

          {/* Drug Doses */}
          <div>
            <div className="form-section-title">Drug Doses</div>
            <div className="dynamic-list">
              {b.drugDoses.map((dose, i) => (
                <div key={i} className="dose-editor">
                  <div className="form-row">
                    <input className="form-input" value={dose.drug} placeholder="Drug name"
                      onChange={e => { const a=[...b.drugDoses]; a[i]={...a[i],drug:e.target.value}; upd("drugDoses",a); }} />
                    <div style={{ display:"flex", gap:6 }}>
                      <input className="form-input" value={dose.conc} placeholder="Conc." style={{ flex:1 }}
                        onChange={e => { const a=[...b.drugDoses]; a[i]={...a[i],conc:e.target.value}; upd("drugDoses",a); }} />
                      <button className="remove-btn" onClick={() => removeItem("drugDoses", i)}>×</button>
                    </div>
                  </div>
                  <div className="form-row">
                    <input className="form-input" value={dose.vol} placeholder="Volume (e.g. 30–40ml)"
                      onChange={e => { const a=[...b.drugDoses]; a[i]={...a[i],vol:e.target.value}; upd("drugDoses",a); }} />
                    <input className="form-input" value={dose.max} placeholder="Max dose (e.g. 2mg/kg)"
                      onChange={e => { const a=[...b.drugDoses]; a[i]={...a[i],max:e.target.value}; upd("drugDoses",a); }} />
                  </div>
                  <input className="form-input" value={dose.notes} placeholder="Notes / onset / duration"
                    onChange={e => { const a=[...b.drugDoses]; a[i]={...a[i],notes:e.target.value}; upd("drugDoses",a); }} />
                </div>
              ))}
              <button className="add-item-btn" onClick={() => addItem("drugDoses", { drug:"", conc:"", vol:"", max:"", notes:"" })}>＋ Add Drug</button>
            </div>
          </div>

          {/* Timing */}
          <div>
            <div className="form-section-title">Timing</div>
            <div className="form-row">
              <div className="form-group">
                <label className="form-label">Onset Time</label>
                <input className="form-input" value={b.onsetTime} onChange={e => upd("onsetTime", e.target.value)} placeholder="e.g. 15–30 minutes" />
              </div>
              <div className="form-group">
                <label className="form-label">Duration</label>
                <input className="form-input" value={b.duration} onChange={e => upd("duration", e.target.value)} placeholder="e.g. 8–16 hours" />
              </div>
            </div>
          </div>

          {/* Complications & Tips */}
          <div>
            <div className="form-section-title">Complications</div>
            <StringList field="complications" placeholder="Add complication" />
          </div>
          <div>
            <div className="form-section-title">Clinical Tips</div>
            <StringList field="tips" placeholder="Add clinical tip" />
          </div>

          {/* Resources */}
          <div>
            <div className="form-section-title">Resources & Links</div>
            <div className="dynamic-list">
              {b.resources.map((r, i) => (
                <div key={i} className="resource-editor" style={{ gridTemplateColumns:"2fr 2fr 1fr auto", gap:6, alignItems:"center" }}>
                  <input className="form-input" value={r.title} placeholder="Title"
                    onChange={e => { const a=[...b.resources]; a[i]={...a[i],title:e.target.value}; upd("resources",a); }} />
                  <input className="form-input" value={r.url} placeholder="https://..."
                    onChange={e => { const a=[...b.resources]; a[i]={...a[i],url:e.target.value}; upd("resources",a); }} />
                  <select className="form-select" value={r.type}
                    onChange={e => { const a=[...b.resources]; a[i]={...a[i],type:e.target.value}; upd("resources",a); }}>
                    {["Article","Video","Guideline","Image"].map(t => <option key={t}>{t}</option>)}
                  </select>
                  <button className="remove-btn" onClick={() => removeItem("resources", i)}>×</button>
                </div>
              ))}
              <button className="add-item-btn" onClick={() => addItem("resources", { title:"", url:"", type:"Article" })}>＋ Add Resource</button>
            </div>
          </div>

          {/* Quiz */}
          <div>
            <div className="form-section-title">Quiz Questions</div>
            <div className="dynamic-list">
              {b.quiz.map((q, qi) => (
                <div key={qi} className="quiz-editor">
                  <div style={{ display:"flex", gap:8, alignItems:"flex-start" }}>
                    <textarea className="form-textarea" value={q.question} placeholder="Question..." rows={2} style={{ flex:1 }}
                      onChange={e => { const a=[...b.quiz]; a[qi]={...a[qi],question:e.target.value}; upd("quiz",a); }} />
                    <button className="remove-btn" onClick={() => removeItem("quiz", qi)}>×</button>
                  </div>
                  {q.options.map((opt, oi) => (
                    <div key={oi} className="quiz-option-editor">
                      <button className={`correct-toggle ${q.correct === oi ? "selected" : ""}`}
                        onClick={() => { const a=[...b.quiz]; a[qi]={...a[qi],correct:oi}; upd("quiz",a); }}
                        title="Mark as correct answer">✓</button>
                      <input className="form-input" value={opt} placeholder={`Option ${oi+1}`} style={{ flex:1 }}
                        onChange={e => { const a=[...b.quiz]; const opts=[...a[qi].options]; opts[oi]=e.target.value; a[qi]={...a[qi],options:opts}; upd("quiz",a); }} />
                    </div>
                  ))}
                  <textarea className="form-textarea" value={q.explanation} placeholder="Explanation for the correct answer..." rows={2}
                    onChange={e => { const a=[...b.quiz]; a[qi]={...a[qi],explanation:e.target.value}; upd("quiz",a); }} />
                </div>
              ))}
              <button className="add-item-btn" onClick={() => addItem("quiz", { question:"", options:["","","",""], correct:0, explanation:"" })}>＋ Add Question</button>
            </div>
          </div>
        </div>
        <div className="modal-footer">
          <button className="btn-outline" onClick={onClose}>Cancel</button>
          <button className="btn-primary" disabled={!b.name.trim()} onClick={() => onSave(b)}>
            {isNew ? "Add Block" : "Save Changes"}
          </button>
        </div>
      </div>
    </div>
  );
}

// ─── BLOCK DETAIL ────────────────────────────────────────────────────────────
function BlockDetail({ block, onBack, onEdit }) {
  const [quiz, setQuiz] = useState(false);
  const diffClass = DIFF_LABELS[block.difficulty] || "diff-beginner";

  return (
    <div className="page">
      <button className="detail-back" onClick={onBack}>← Back to all blocks</button>

      <div className="detail-hero">
        <div className="detail-hero-icon">{block.icon}</div>
        <div className="detail-hero-body">
          <div className="detail-hero-title">{block.name}</div>
          <div className="detail-hero-sub">{block.subtitle}</div>
          <div className="detail-hero-pills">
            <span className="hero-pill">⏱ {block.time}</span>
            <span className="hero-pill">⚡ Onset: {block.onsetTime}</span>
            <span className="hero-pill">🌙 Duration: {block.duration}</span>
            <span className="hero-pill">📍 {block.category}</span>
          </div>
        </div>
        <div className="detail-hero-actions">
          <button className="detail-action-btn btn-quiz" onClick={() => setQuiz(true)}>
            🧠 Take Quiz ({(block.quiz||[]).length} Qs)
          </button>
          <button className="detail-action-btn btn-light" onClick={onEdit}>
            ✏️ Edit Block
          </button>
        </div>
      </div>

      <div className="detail-layout">
        <div className="detail-main">
          {/* Overview */}
          <Section icon="📋" title="Overview" color="#1A56DB" bg="#EFF6FF">
            <p className="prose">{block.overview}</p>
            <div style={{ marginTop: 12 }}>
              <span className={`diff-badge ${diffClass}`}>
                {block.difficulty === "Beginner" ? "①" : block.difficulty === "Intermediate" ? "②" : "③"} {block.difficulty}
              </span>
            </div>
          </Section>

          {/* Indications */}
          <Section icon="✅" title="Indications & Contraindications" color="#059669" bg="#ECFDF5">
            {block.indications?.length > 0 && <>
              <div className="section-sub-title" style={{ color:"#059669" }}>Indications</div>
              <div className="bullet-list">
                {block.indications.map((item, i) => (
                  <div key={i} className="bullet-item">
                    <div className="bullet-dot" style={{ background:"#059669" }} />
                    <span className="bullet-text">{item}</span>
                  </div>
                ))}
              </div>
            </>}
            {block.contraindications?.length > 0 && <>
              <div className="divider" />
              <div className="section-sub-title" style={{ color:"#DC2626" }}>Absolute Contraindications</div>
              <div className="bullet-list">
                {block.contraindications.map((item, i) => (
                  <div key={i} className="bullet-item">
                    <div className="bullet-dot" style={{ background:"#DC2626" }} />
                    <span className="bullet-text">{item}</span>
                  </div>
                ))}
              </div>
            </>}
            {block.relativeContraindications?.length > 0 && <>
              <div className="divider" />
              <div className="section-sub-title" style={{ color:"#D97706" }}>Relative Contraindications</div>
              <div className="bullet-list">
                {block.relativeContraindications.map((item, i) => (
                  <div key={i} className="bullet-item">
                    <div className="bullet-dot" style={{ background:"#D97706" }} />
                    <span className="bullet-text">{item}</span>
                  </div>
                ))}
              </div>
            </>}
          </Section>

          {/* Anatomy */}
          <Section icon="🧠" title="Anatomy & Landmarks" color="#7C3AED" bg="#F5F3FF">
            <p className="prose">{block.anatomy}</p>
            {block.anatomyKeyPoints?.length > 0 && <>
              <div className="divider" />
              <div className="section-sub-title" style={{ color:"#7C3AED" }}>Key Points</div>
              <div className="bullet-list">
                {block.anatomyKeyPoints.map((p, i) => (
                  <div key={i} className="bullet-item">
                    <div className="bullet-dot" style={{ background:"#7C3AED" }} />
                    <span className="bullet-text">{p}</span>
                  </div>
                ))}
              </div>
            </>}
          </Section>

          {/* Technique */}
          <Section icon="📝" title="Step-by-Step Technique" color="#1A56DB" bg="#EFF6FF">
            <div className="steps-list">
              {(block.technique||[]).sort((a,b)=>a.order-b.order).map((step, i) => (
                <div key={i} className="step-item">
                  <div className="step-num">{step.order}</div>
                  <div className="step-body">
                    <div className="step-title">{step.title}</div>
                    <div className="step-detail">{step.detail}</div>
                  </div>
                </div>
              ))}
            </div>
            {block.equipment?.length > 0 && <>
              <div className="divider" />
              <div className="section-sub-title">Equipment Required</div>
              <div className="bullet-list">
                {block.equipment.map((e, i) => (
                  <div key={i} className="bullet-item">
                    <div className="bullet-dot" style={{ background:"#1A56DB" }} />
                    <span className="bullet-text">{e}</span>
                  </div>
                ))}
              </div>
            </>}
          </Section>

          {/* Ultrasound */}
          {block.ultrasoundViews?.length > 0 && (
            <Section icon="📡" title="Ultrasound Guidance" color="#0891B2" bg="#ECFEFF">
              {block.ultrasoundViews.map((v, i) => (
                <div key={i} style={{ marginBottom: i < block.ultrasoundViews.length - 1 ? 20 : 0 }}>
                  <div className="us-placeholder">
                    <span style={{ fontSize: 36 }}>📡</span>
                    <span>{v.title}</span>
                    <span style={{ fontSize: 11 }}>Upload image to replace this placeholder</span>
                  </div>
                  <div style={{ fontWeight: 600, fontSize: 14, marginBottom: 4 }}>{v.title}</div>
                  <p className="prose" style={{ marginBottom: 10 }}>{v.description}</p>
                  <div className="section-sub-title" style={{ color:"#0891B2" }}>Key Landmarks</div>
                  <div className="landmark-list">
                    {v.landmarks.map((lm, j) => (
                      <div key={j} className="landmark-item">
                        <span className="landmark-dot">▶</span>
                        <span>{lm}</span>
                      </div>
                    ))}
                  </div>
                </div>
              ))}
            </Section>
          )}

          {/* Complications & Tips */}
          <Section icon="⚠️" title="Complications & Clinical Tips" color="#D97706" bg="#FFFBEB">
            {block.complications?.length > 0 && <>
              <div className="section-sub-title" style={{ color:"#D97706" }}>Potential Complications</div>
              <div className="bullet-list">
                {block.complications.map((c, i) => (
                  <div key={i} className="bullet-item">
                    <div className="bullet-dot" style={{ background:"#D97706" }} />
                    <span className="bullet-text">{c}</span>
                  </div>
                ))}
              </div>
            </>}
            {block.tips?.length > 0 && <>
              <div className="divider" />
              <div className="section-sub-title" style={{ color:"#059669" }}>Clinical Tips</div>
              {block.tips.map((t, i) => (
                <div key={i} className="tip-item">
                  <span className="tip-icon">💡</span>
                  <span className="tip-text">{t}</span>
                </div>
              ))}
            </>}
          </Section>
        </div>

        <div className="detail-sidebar">
          {/* Drug Doses */}
          <Section icon="💉" title="Drug Doses & Volumes" color="#0891B2" bg="#ECFEFF" defaultOpen={true}>
            <div className="dose-cards">
              {(block.drugDoses||[]).map((dose, i) => (
                <div key={i} className="dose-card">
                  <div className="dose-card-top">
                    <span className="dose-drug">{dose.drug}</span>
                    <span className="dose-conc">{dose.conc}</span>
                  </div>
                  <div className="dose-row">
                    <div className="dose-pair">
                      <span className="dose-lbl">Volume</span>
                      <span className="dose-val">{dose.vol}</span>
                    </div>
                    <div className="dose-pair">
                      <span className="dose-lbl">Max Dose</span>
                      <span className="dose-val">{dose.max}</span>
                    </div>
                  </div>
                  {dose.notes && <p className="dose-note">{dose.notes}</p>}
                </div>
              ))}
            </div>
            <div style={{ display:"flex", gap:16, marginTop:14, padding:"12px 0", borderTop:"1px solid #E2E8F0" }}>
              <div className="dose-pair">
                <span className="dose-lbl">Onset</span>
                <span className="dose-val">{block.onsetTime}</span>
              </div>
              <div className="dose-pair">
                <span className="dose-lbl">Duration</span>
                <span className="dose-val">{block.duration}</span>
              </div>
            </div>
            <div className="last-warning">
              <span className="last-warning-icon">⚠️</span>
              <span className="last-warning-text">Always calculate weight-based maximum dose. Have Intralipid 20% available and follow your trust LAST protocol.</span>
            </div>
          </Section>

          {/* Resources */}
          {block.resources?.length > 0 && (
            <Section icon="🔗" title="Videos & Links" color="#4F46E5" bg="#EEF2FF">
              <div className="resource-list">
                {block.resources.map((r, i) => (
                  <a key={i} href={r.url} target="_blank" rel="noopener noreferrer" className="resource-item">
                    <span className="resource-icon">{RESOURCE_ICONS[r.type] || "🔗"}</span>
                    <div className="resource-info">
                      <div className="resource-title">{r.title}</div>
                      <div className="resource-type">{r.type}</div>
                    </div>
                    <span className="resource-arrow">↗</span>
                  </a>
                ))}
              </div>
            </Section>
          )}
        </div>
      </div>

      {quiz && <QuizModal block={block} onClose={() => setQuiz(false)} />}
    </div>
  );
}

// ─── BLOCKS LIST ─────────────────────────────────────────────────────────────
function BlocksList({ blocks, onSelect, onAdd, onDelete }) {
  const [search, setSearch] = useState("");
  const [cat, setCat] = useState("All");
  const cats = ["All", "Lower Limb", "Upper Limb", "Thoracic", "Truncal"];
  const CAT_ICONS = { All:"🔲", "Lower Limb":"🦵", "Upper Limb":"✋", Thoracic:"🫁", Truncal:"🫀" };

  const filtered = blocks.filter(b => {
    const matchCat = cat === "All" || b.category === cat;
    const matchSearch = !search || b.name.toLowerCase().includes(search.toLowerCase()) || b.subtitle.toLowerCase().includes(search.toLowerCase());
    return matchCat && matchSearch;
  });

  return (
    <div className="page">
      <div className="hero">
        <div className="hero-eyebrow">RCEM Course</div>
        <div className="hero-title">Regional Anaesthetic Blocks</div>
        <div className="hero-sub">Emergency Medicine — Course Reference App</div>
        <div className="hero-stats">
          <div className="hero-stat"><span className="hero-stat-val">{blocks.length}</span><span className="hero-stat-lbl">Blocks</span></div>
          <div className="hero-stat"><span className="hero-stat-val">{blocks.flatMap(b => b.quiz||[]).length}</span><span className="hero-stat-lbl">Quiz Questions</span></div>
          <div className="hero-stat"><span className="hero-stat-val">{blocks.flatMap(b => b.drugDoses||[]).length}</span><span className="hero-stat-lbl">Drug Doses</span></div>
        </div>
      </div>

      <div className="blocks-controls">
        <div className="search-box">
          <svg width="16" height="16" fill="none" stroke="currentColor" strokeWidth="2" viewBox="0 0 24 24">
            <circle cx="11" cy="11" r="8"/><path d="m21 21-4.35-4.35"/>
          </svg>
          <input value={search} onChange={e => setSearch(e.target.value)} placeholder="Search blocks..." />
          {search && <button onClick={() => setSearch("")} style={{ color:"#94A3B8", fontSize:16 }}>×</button>}
        </div>
        <div className="filter-chips">
          {cats.map(c => (
            <button key={c} className={`chip ${cat === c ? "active" : ""}`} onClick={() => setCat(c)}>
              {CAT_ICONS[c]} {c}
            </button>
          ))}
        </div>
        <button className="add-btn" onClick={onAdd}>＋ Add Block</button>
      </div>

      {filtered.length === 0 ? (
        <div style={{ textAlign:"center", padding:"60px 0", color:"#94A3B8" }}>
          <div style={{ fontSize:48, marginBottom:12 }}>🔍</div>
          <div style={{ fontSize:18, fontWeight:600 }}>No blocks found</div>
          <div style={{ fontSize:14, marginTop:4 }}>Try adjusting your search or filter</div>
        </div>
      ) : (
        <div className="blocks-grid">
          {filtered.map(block => (
            <div key={block.id} className="block-card" onClick={() => onSelect(block)}>
              <div className="block-card-top">
                <div className="block-icon">{block.icon}</div>
                <div className="block-info">
                  <div className="block-name">{block.name}</div>
                  <div className="block-sub">{block.subtitle}</div>
                </div>
              </div>
              <div className="block-meta">
                <span className={`diff-badge ${DIFF_LABELS[block.difficulty] || "diff-beginner"}`}>
                  {block.difficulty}
                </span>
                <span className="meta-pill">⏱ {block.time}</span>
                <span className="meta-pill">📍 {block.category}</span>
                <span className="meta-pill">🧠 {(block.quiz||[]).length} Qs</span>
              </div>
            </div>
          ))}
        </div>
      )}
    </div>
  );
}

// ─── QUICK REFERENCE ─────────────────────────────────────────────────────────
function QuickRef({ blocks }) {
  const [lastOpen, setLastOpen] = useState(true);
  return (
    <div className="page">
      <div style={{ marginBottom: 24 }}>
        <h1 style={{ fontFamily:"var(--font-display)", fontSize:28, marginBottom:4 }}>Quick Drug Reference</h1>
        <p style={{ color:"var(--slate)", fontSize:14 }}>At-a-glance doses for all blocks. Always calculate weight-based maximum.</p>
      </div>

      {/* LAST Card */}
      <div className="last-card">
        <div className="last-card-header" onClick={() => setLastOpen(o => !o)}>
          <span style={{ fontSize:22 }}>🚨</span>
          <span className="last-card-title">LAST — Local Anaesthetic Systemic Toxicity Protocol</span>
          <span style={{ color:"#991B1B" }}>{lastOpen ? "▲" : "▼"}</span>
        </div>
        {lastOpen && <div className="last-steps">
          {[
            "Stop injecting local anaesthetic immediately",
            "Call for help — get LAST kit / Intralipid 20%",
            "Airway: 100% O₂, consider intubation",
            "Seizures: benzodiazepine first line (avoid propofol if cardiovascular compromise)",
            "Cardiac arrest: modified ALS — avoid lignocaine, vasopressin, β-blockers, Ca²⁺ channel blockers",
            "Intralipid 20%: 1.5ml/kg bolus IV over 1 min → 0.25ml/kg/min infusion",
            "Consider ECMO if refractory cardiac arrest. Follow your trust LAST protocol."
          ].map((s, i) => (
            <div key={i} className="last-step">
              <div className="last-step-num">{i + 1}</div>
              <div className="last-step-text">{s}</div>
            </div>
          ))}
        </div>}
      </div>

      <div className="qr-grid">
        {blocks.map(block => (
          <div key={block.id} className="qr-card">
            <div className="qr-card-header">
              <span className="qr-card-icon">{block.icon}</span>
              <div>
                <div className="qr-card-title">{block.name}</div>
                <span className={`diff-badge ${DIFF_LABELS[block.difficulty]}`} style={{ fontSize:10, marginTop:3, display:"inline-flex" }}>{block.difficulty}</span>
              </div>
            </div>
            {(block.drugDoses||[]).length === 0
              ? <p style={{ fontSize:13, color:"var(--slate)", fontStyle:"italic" }}>No drug doses added yet.</p>
              : (block.drugDoses||[]).map((dose, i) => (
                <div key={i} className="qr-dose-row">
                  <div className="qr-dose-left">
                    <div className="qr-dose-name">{dose.drug}</div>
                    <div className="qr-dose-note">{dose.notes}</div>
                  </div>
                  <div className="qr-dose-right">
                    <div className="qr-dose-vol">{dose.vol}</div>
                    <div className="qr-dose-max">max {dose.max}</div>
                  </div>
                </div>
              ))
            }
            <div className="qr-timing">
              <div className="qr-timing-item">
                <span className="qr-timing-lbl">Onset</span>
                <span className="qr-timing-val">{block.onsetTime || "—"}</span>
              </div>
              <div className="qr-timing-item">
                <span className="qr-timing-lbl">Duration</span>
                <span className="qr-timing-val">{block.duration || "—"}</span>
              </div>
            </div>
          </div>
        ))}
      </div>
    </div>
  );
}

// ─── ABOUT ───────────────────────────────────────────────────────────────────
function About({ blocks }) {
  return (
    <div className="page" style={{ maxWidth: 720 }}>
      <div style={{ textAlign:"center", marginBottom: 28 }}>
        <div style={{ fontSize:56, marginBottom:12 }}>💉</div>
        <h1 style={{ fontFamily:"var(--font-display)", fontSize:28, marginBottom:4 }}>RCEM Regional Blocks</h1>
        <p style={{ color:"var(--slate)", fontSize:14 }}>Emergency Medicine Course Reference App · v1.0</p>
      </div>

      <div className="about-stats">
        <div className="about-stat">
          <div className="about-stat-val">{blocks.length}</div>
          <div className="about-stat-lbl">Blocks</div>
        </div>
        <div className="about-stat">
          <div className="about-stat-val">{blocks.flatMap(b=>b.quiz||[]).length}</div>
          <div className="about-stat-lbl">Quiz Questions</div>
        </div>
        <div className="about-stat">
          <div className="about-stat-val">{blocks.flatMap(b=>b.drugDoses||[]).length}</div>
          <div className="about-stat-lbl">Drug Doses</div>
        </div>
      </div>

      <div className="info-cards">
        <div className="info-card">
          <span className="info-card-icon">🛡️</span>
          <div>
            <div className="info-card-title">Clinical Disclaimer</div>
            <div className="info-card-body">This app is an educational resource for the RCEM Regional Anaesthetic Blocks Course. All clinical decisions must be made by a suitably trained clinician following local protocols and current guidelines. Drug doses must be verified against current BNF/local guidance and calculated per patient weight.</div>
          </div>
        </div>
        <div className="info-card">
          <span className="info-card-icon">✏️</span>
          <div>
            <div className="info-card-title">How to Update Content</div>
            <div className="info-card-body">Use the + Add Block button on the Blocks tab to create new entries. Click any block then Edit Block to update all content. All data is saved in your browser's local storage — it persists between sessions on the same device.</div>
          </div>
        </div>
        <div className="info-card">
          <span className="info-card-icon">👥</span>
          <div>
            <div className="info-card-title">Course Information</div>
            <div className="info-card-body">The RCEM Regional Anaesthetic Blocks Course is designed for Emergency Medicine trainees and practitioners. Content should be reviewed and updated by course faculty to reflect current evidence and local practice.</div>
          </div>
        </div>
      </div>
    </div>
  );
}

// ─── APP ROOT ─────────────────────────────────────────────────────────────────
export default function App() {
  const [blocks, setBlocks] = useLocalStorage("rcem_blocks", INITIAL_BLOCKS);
  const [tab, setTab] = useState("blocks");
  const [selected, setSelected] = useState(null);
  const [editing, setEditing] = useState(null); // null = closed, false = new, block = edit

  function saveBlock(b) {
    setBlocks(prev => {
      const exists = prev.find(x => x.id === b.id);
      return exists ? prev.map(x => x.id === b.id ? b : x) : [...prev, b];
    });
    setEditing(null);
    // If we just saved the block we're viewing, update the selection
    setSelected(s => s && s.id === b.id ? b : s);
  }

  function deleteBlock(id) {
    if (!window.confirm("Delete this block?")) return;
    setBlocks(prev => prev.filter(b => b.id !== id));
    setSelected(null);
  }

  const liveSelected = selected ? blocks.find(b => b.id === selected.id) || selected : null;

  const NAV = [
    { key:"blocks", label:"Blocks", icon:<svg width="15" height="15" fill="none" stroke="currentColor" strokeWidth="2" viewBox="0 0 24 24"><rect x="3" y="3" width="7" height="7"/><rect x="14" y="3" width="7" height="7"/><rect x="14" y="14" width="7" height="7"/><rect x="3" y="14" width="7" height="7"/></svg> },
    { key:"quickref", label:"Quick Ref", icon:<svg width="15" height="15" fill="none" stroke="currentColor" strokeWidth="2" viewBox="0 0 24 24"><path d="M13 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V9z"/><polyline points="13 2 13 9 20 9"/></svg> },
    { key:"about", label:"About", icon:<svg width="15" height="15" fill="none" stroke="currentColor" strokeWidth="2" viewBox="0 0 24 24"><circle cx="12" cy="12" r="10"/><path d="M12 16v-4M12 8h.01"/></svg> },
  ];

  return (
    <>
      <style>{STYLES}</style>
      <div className="app-shell">
        <nav className="topnav">
          <div className="topnav-inner">
            <div className="topnav-brand">
              <span>RCEM Blocks</span>
              <span>Regional Anaesthesia Course</span>
            </div>
            <div className="topnav-tabs">
              {NAV.map(n => (
                <button key={n.key} className={`topnav-tab ${tab === n.key && !liveSelected ? "active" : ""}`}
                  onClick={() => { setTab(n.key); setSelected(null); }}>
                  {n.icon}<span>{n.label}</span>
                </button>
              ))}
            </div>
          </div>
        </nav>

        <main style={{ flex:1 }}>
          {liveSelected ? (
            <BlockDetail
              block={liveSelected}
              onBack={() => setSelected(null)}
              onEdit={() => setEditing(liveSelected)}
            />
          ) : tab === "blocks" ? (
            <BlocksList
              blocks={blocks}
              onSelect={setSelected}
              onAdd={() => setEditing(false)}
              onDelete={deleteBlock}
            />
          ) : tab === "quickref" ? (
            <QuickRef blocks={blocks} />
          ) : (
            <About blocks={blocks} />
          )}
        </main>
      </div>

      {editing !== null && (
        <EditModal
          block={editing || null}
          onSave={saveBlock}
          onClose={() => setEditing(null)}
        />
      )}
    </>
  );
}
