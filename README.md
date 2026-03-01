<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Verbes Irréguliers — Jeu d'Apprentissage</title>
<link href="https://fonts.googleapis.com/css2?family=Fredoka+One&family=Nunito:wght@400;600;700;800&display=swap" rel="stylesheet">
<style>
  :root {
    --c-sky: #6ECFF6;
    --c-sun: #FFD93D;
    --c-coral: #FF6B6B;
    --c-mint: #6BCB77;
    --c-purple: #A78BFA;
    --c-bg: #FFF8EC;
    --c-dark: #2D2D2D;
    --c-card: #FFFFFF;
    --radius: 20px;
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    font-family: 'Nunito', sans-serif;
    background: var(--c-bg);
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* Floating bubbles BG */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background:
      radial-gradient(circle at 10% 20%, rgba(110,207,246,0.18) 0%, transparent 40%),
      radial-gradient(circle at 85% 70%, rgba(167,139,250,0.18) 0%, transparent 40%),
      radial-gradient(circle at 50% 95%, rgba(107,203,119,0.15) 0%, transparent 40%);
    pointer-events: none;
    z-index: 0;
  }

  .container {
    position: relative;
    z-index: 1;
    max-width: 820px;
    margin: 0 auto;
    padding: 24px 16px 60px;
  }

  /* ── HEADER ── */
  header {
    text-align: center;
    margin-bottom: 32px;
  }
  header h1 {
    font-family: 'Fredoka One', cursive;
    font-size: clamp(2rem, 6vw, 3.2rem);
    color: var(--c-dark);
    line-height: 1.1;
  }
  header h1 span { color: var(--c-coral); }
  header p { color: #666; margin-top: 6px; font-size: 1rem; }

  /* ── SCORE BAR ── */
  .score-bar {
    display: flex;
    justify-content: center;
    gap: 16px;
    margin-bottom: 28px;
    flex-wrap: wrap;
  }
  .pill {
    background: white;
    border-radius: 50px;
    padding: 8px 20px;
    font-weight: 800;
    font-size: 1rem;
    box-shadow: 0 3px 12px rgba(0,0,0,0.1);
    display: flex; align-items: center; gap: 8px;
  }
  .pill .dot { width: 12px; height: 12px; border-radius: 50%; }
  .pill.correct .dot { background: var(--c-mint); }
  .pill.wrong .dot { background: var(--c-coral); }
  .pill.streak .dot { background: var(--c-sun); }

  /* ── MODE TABS ── */
  .modes {
    display: flex;
    justify-content: center;
    gap: 10px;
    margin-bottom: 28px;
    flex-wrap: wrap;
  }
  .mode-btn {
    border: none;
    border-radius: 50px;
    padding: 10px 22px;
    font-family: 'Nunito', sans-serif;
    font-weight: 800;
    font-size: 0.9rem;
    cursor: pointer;
    transition: transform 0.15s, box-shadow 0.15s;
    background: white;
    color: #888;
    box-shadow: 0 2px 8px rgba(0,0,0,0.08);
  }
  .mode-btn:hover { transform: translateY(-2px); box-shadow: 0 6px 16px rgba(0,0,0,0.13); }
  .mode-btn.active { color: white; transform: translateY(-2px); box-shadow: 0 6px 16px rgba(0,0,0,0.2); }
  .mode-btn[data-mode="qcm"].active { background: var(--c-sky); }
  .mode-btn[data-mode="type"].active { background: var(--c-coral); }
  .mode-btn[data-mode="table"].active { background: var(--c-mint); }
  .mode-btn[data-mode="flash"].active { background: var(--c-purple); }

  /* ── CARD ── */
  .game-card {
    background: white;
    border-radius: 28px;
    padding: 36px 28px;
    box-shadow: 0 8px 40px rgba(0,0,0,0.10);
    min-height: 340px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    transition: transform 0.3s;
  }

  .category-badge {
    font-size: 0.78rem;
    font-weight: 800;
    text-transform: uppercase;
    letter-spacing: 1.5px;
    background: var(--c-sun);
    color: #6a4f00;
    border-radius: 50px;
    padding: 4px 14px;
    margin-bottom: 18px;
  }

  .verb-display {
    font-family: 'Fredoka One', cursive;
    font-size: clamp(2.8rem, 9vw, 5rem);
    color: var(--c-dark);
    line-height: 1;
    margin-bottom: 6px;
    text-align: center;
  }

  .verb-translation {
    color: #aaa;
    font-size: 1rem;
    font-weight: 600;
    margin-bottom: 24px;
  }

  .question-label {
    font-size: 1rem;
    font-weight: 700;
    color: #555;
    margin-bottom: 16px;
    text-align: center;
  }

  /* QCM OPTIONS */
  .options-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 12px;
    width: 100%;
    max-width: 460px;
  }
  .option-btn {
    border: 3px solid #EEE;
    background: white;
    border-radius: 16px;
    padding: 14px 10px;
    font-family: 'Fredoka One', cursive;
    font-size: 1.4rem;
    color: var(--c-dark);
    cursor: pointer;
    transition: all 0.15s;
    box-shadow: 0 3px 0 #ddd;
    position: relative;
    overflow: hidden;
  }
  .option-btn:hover:not(:disabled) {
    border-color: var(--c-sky);
    transform: translateY(-3px);
    box-shadow: 0 6px 0 #b5e5f8;
  }
  .option-btn.correct {
    border-color: var(--c-mint);
    background: #f0fff2;
    box-shadow: 0 3px 0 #4aad58;
    animation: pop 0.3s;
  }
  .option-btn.wrong {
    border-color: var(--c-coral);
    background: #fff0f0;
    box-shadow: 0 3px 0 #d94545;
    animation: shake 0.4s;
  }

  /* TYPE INPUT */
  .type-grid {
    display: flex;
    flex-direction: column;
    gap: 12px;
    width: 100%;
    max-width: 360px;
  }
  .type-row {
    display: flex;
    flex-direction: column;
    gap: 4px;
  }
  .type-row label {
    font-size: 0.82rem;
    font-weight: 800;
    color: #888;
    text-transform: uppercase;
    letter-spacing: 1px;
  }
  .type-row input {
    border: 3px solid #EEE;
    border-radius: 14px;
    padding: 12px 16px;
    font-family: 'Fredoka One', cursive;
    font-size: 1.5rem;
    color: var(--c-dark);
    outline: none;
    transition: border-color 0.15s;
    width: 100%;
    background: #fafafa;
  }
  .type-row input:focus { border-color: var(--c-sky); background: white; }
  .type-row input.correct { border-color: var(--c-mint); background: #f0fff2; }
  .type-row input.wrong { border-color: var(--c-coral); background: #fff0f0; }

  .submit-btn {
    background: var(--c-coral);
    color: white;
    border: none;
    border-radius: 50px;
    padding: 14px 40px;
    font-family: 'Fredoka One', cursive;
    font-size: 1.3rem;
    cursor: pointer;
    margin-top: 16px;
    box-shadow: 0 4px 0 #c44;
    transition: all 0.15s;
  }
  .submit-btn:hover { transform: translateY(-2px); box-shadow: 0 6px 0 #c44; }
  .submit-btn:active { transform: translateY(2px); box-shadow: 0 1px 0 #c44; }

  /* FEEDBACK */
  .feedback {
    font-size: 1rem;
    font-weight: 800;
    margin-top: 16px;
    padding: 10px 20px;
    border-radius: 14px;
    text-align: center;
    display: none;
  }
  .feedback.show { display: block; }
  .feedback.good { background: #e8ffe8; color: #2a7a2a; }
  .feedback.bad { background: #ffe8e8; color: #aa2222; }

  .next-btn {
    display: none;
    background: var(--c-dark);
    color: white;
    border: none;
    border-radius: 50px;
    padding: 12px 36px;
    font-family: 'Fredoka One', cursive;
    font-size: 1.2rem;
    cursor: pointer;
    margin-top: 14px;
    transition: all 0.15s;
    box-shadow: 0 4px 0 #111;
  }
  .next-btn:hover { transform: translateY(-2px); box-shadow: 0 6px 0 #111; }
  .next-btn.show { display: inline-block; }

  /* FLASHCARD */
  .flash-card {
    width: 100%;
    max-width: 400px;
    perspective: 1000px;
    cursor: pointer;
    margin: 0 auto;
  }
  .flash-inner {
    position: relative;
    width: 100%;
    height: 220px;
    transform-style: preserve-3d;
    transition: transform 0.5s cubic-bezier(.23,1,.32,1);
    border-radius: 24px;
  }
  .flash-inner.flipped { transform: rotateY(180deg); }
  .flash-front, .flash-back {
    position: absolute;
    inset: 0;
    backface-visibility: hidden;
    border-radius: 24px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 24px;
    box-shadow: 0 6px 24px rgba(0,0,0,0.12);
  }
  .flash-front { background: linear-gradient(135deg, var(--c-sky), var(--c-purple)); color: white; }
  .flash-back { background: linear-gradient(135deg, var(--c-mint), var(--c-sun)); color: var(--c-dark); transform: rotateY(180deg); }
  .flash-front .f-word, .flash-back .f-word {
    font-family: 'Fredoka One', cursive;
    font-size: 3rem;
    line-height: 1;
  }
  .flash-front .f-label, .flash-back .f-label {
    font-size: 0.8rem;
    font-weight: 800;
    text-transform: uppercase;
    letter-spacing: 2px;
    opacity: 0.75;
    margin-bottom: 6px;
  }
  .flash-back .forms {
    display: flex;
    gap: 12px;
    margin-top: 10px;
    flex-wrap: wrap;
    justify-content: center;
  }
  .flash-back .form-chip {
    background: rgba(0,0,0,0.1);
    border-radius: 12px;
    padding: 6px 14px;
    font-family: 'Fredoka One', cursive;
    font-size: 1.1rem;
  }
  .flash-tap { font-size: 0.78rem; opacity: 0.6; margin-top: 10px; }

  .flash-nav {
    display: flex;
    gap: 14px;
    margin-top: 20px;
    justify-content: center;
  }
  .flash-nav button {
    border: none;
    border-radius: 50px;
    padding: 10px 28px;
    font-family: 'Fredoka One', cursive;
    font-size: 1rem;
    cursor: pointer;
    transition: all 0.15s;
    box-shadow: 0 3px 0 #aaa;
  }
  .flash-nav .prev-btn { background: #EEE; color: #555; }
  .flash-nav .next-flash { background: var(--c-purple); color: white; box-shadow: 0 3px 0 #7c5ac2; }
  .flash-progress { color: #aaa; font-weight: 700; font-size: 0.9rem; align-self: center; }

  /* TABLE MODE */
  .table-search {
    width: 100%;
    max-width: 340px;
    border: 3px solid #EEE;
    border-radius: 50px;
    padding: 12px 20px;
    font-family: 'Nunito', sans-serif;
    font-size: 1rem;
    outline: none;
    margin-bottom: 20px;
    transition: border-color 0.15s;
  }
  .table-search:focus { border-color: var(--c-mint); }

  .filter-pills {
    display: flex;
    gap: 8px;
    flex-wrap: wrap;
    justify-content: center;
    margin-bottom: 16px;
  }
  .filter-pill {
    border: none;
    border-radius: 50px;
    padding: 6px 16px;
    font-family: 'Nunito', sans-serif;
    font-weight: 800;
    font-size: 0.8rem;
    cursor: pointer;
    transition: all 0.15s;
    background: #EEE;
    color: #777;
  }
  .filter-pill.active { background: var(--c-mint); color: white; }

  .verb-table {
    width: 100%;
    border-collapse: separate;
    border-spacing: 0 6px;
    font-size: 0.95rem;
  }
  .verb-table thead th {
    font-size: 0.72rem;
    text-transform: uppercase;
    letter-spacing: 1.5px;
    color: #aaa;
    font-weight: 800;
    padding: 4px 10px;
    text-align: left;
  }
  .verb-table tbody tr {
    background: white;
    border-radius: 12px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.05);
    transition: transform 0.15s;
  }
  .verb-table tbody tr:hover { transform: translateX(4px); }
  .verb-table tbody td {
    padding: 10px 12px;
    font-family: 'Fredoka One', cursive;
    font-size: 1.05rem;
    color: var(--c-dark);
  }
  .verb-table tbody td:first-child { border-radius: 12px 0 0 12px; }
  .verb-table tbody td:last-child { border-radius: 0 12px 12px 0; font-family: 'Nunito', sans-serif; font-size: 0.85rem; font-weight: 700; color: #888; }
  .verb-table tbody td .cat-dot {
    display: inline-block;
    width: 8px; height: 8px;
    border-radius: 50%;
    margin-right: 6px;
    vertical-align: middle;
  }

  /* PROGRESS BAR */
  .progress-wrap {
    width: 100%;
    max-width: 460px;
    background: #EEE;
    border-radius: 50px;
    height: 10px;
    margin-bottom: 24px;
    overflow: hidden;
  }
  .progress-fill {
    height: 100%;
    border-radius: 50px;
    background: linear-gradient(90deg, var(--c-sky), var(--c-purple));
    transition: width 0.5s;
  }

  /* ANIMATIONS */
  @keyframes pop {
    0% { transform: scale(1); }
    50% { transform: scale(1.08); }
    100% { transform: scale(1); }
  }
  @keyframes shake {
    0%,100% { transform: translateX(0); }
    20% { transform: translateX(-8px); }
    40% { transform: translateX(8px); }
    60% { transform: translateX(-5px); }
    80% { transform: translateX(5px); }
  }
  @keyframes confetti-fall {
    to { transform: translateY(100vh) rotate(720deg); opacity: 0; }
  }
  .confetti-piece {
    position: fixed;
    top: -20px;
    border-radius: 4px;
    animation: confetti-fall linear forwards;
    z-index: 999;
    pointer-events: none;
  }

  /* STARS */
  .stars {
    font-size: 1.6rem;
    margin-bottom: 8px;
    letter-spacing: 4px;
  }

  /* Responsive */
  @media(max-width:480px) {
    .options-grid { grid-template-columns: 1fr 1fr; }
    .game-card { padding: 24px 16px; }
  }
</style>
</head>
<body>
<div class="container">

  <header>
    <h1>I Bet You Can Learn<br><span>Irregular Verbs!</span> 🎯</h1>
    <p>Choisis ton mode de jeu et apprends en t'amusant !</p>
  </header>

  <div class="score-bar">
    <div class="pill correct"><div class="dot"></div>✓ <span id="score-correct">0</span></div>
    <div class="pill wrong"><div class="dot"></div>✗ <span id="score-wrong">0</span></div>
    <div class="pill streak"><div class="dot"></div>🔥 <span id="score-streak">0</span></div>
  </div>

  <div class="modes">
    <button class="mode-btn active" data-mode="qcm">🎮 QCM</button>
    <button class="mode-btn" data-mode="type">⌨️ Écrire</button>
    <button class="mode-btn" data-mode="flash">🃏 Flashcards</button>
    <button class="mode-btn" data-mode="table">📋 Tableau</button>
  </div>

  <!-- QCM MODE -->
  <div id="mode-qcm" class="game-card">
    <div class="category-badge" id="qcm-cat">Catégorie</div>
    <div class="progress-wrap"><div class="progress-fill" id="qcm-progress" style="width:0%"></div></div>
    <div class="verb-display" id="qcm-verb">fly</div>
    <div class="verb-translation" id="qcm-trans">voler</div>
    <div class="question-label" id="qcm-question">Quelle est la forme au <strong>Prétérit</strong> ?</div>
    <div class="options-grid" id="qcm-options"></div>
    <div class="feedback" id="qcm-feedback"></div>
    <button class="next-btn" id="qcm-next">Suivant →</button>
  </div>

  <!-- TYPE MODE -->
  <div id="mode-type" class="game-card" style="display:none">
    <div class="category-badge" id="type-cat">Catégorie</div>
    <div class="progress-wrap"><div class="progress-fill" id="type-progress" style="width:0%"></div></div>
    <div class="verb-display" id="type-verb">fly</div>
    <div class="verb-translation" id="type-trans">voler</div>
    <div class="type-grid">
      <div class="type-row">
        <label>Prétérit (Past Simple)</label>
        <input type="text" id="input-pret" placeholder="..." autocomplete="off" spellcheck="false">
      </div>
      <div class="type-row">
        <label>Participe Passé (Past Participle)</label>
        <input type="text" id="input-pp" placeholder="..." autocomplete="off" spellcheck="false">
      </div>
    </div>
    <button class="submit-btn" id="type-submit">Vérifier ✓</button>
    <div class="feedback" id="type-feedback"></div>
    <button class="next-btn" id="type-next">Suivant →</button>
  </div>

  <!-- FLASH MODE -->
  <div id="mode-flash" class="game-card" style="display:none">
    <div class="stars" id="flash-stars">⭐⭐⭐⭐⭐</div>
    <div class="flash-card" id="flash-card">
      <div class="flash-inner" id="flash-inner">
        <div class="flash-front">
          <div class="f-label">Base Verb</div>
          <div class="f-word" id="flash-bv">fly</div>
          <div class="f-label" style="margin-top:8px" id="flash-ftrans">voler</div>
          <div class="flash-tap">Cliquer pour retourner 👆</div>
        </div>
        <div class="flash-back">
          <div class="f-label">Formes complètes</div>
          <div class="f-word" id="flash-back-bv">fly</div>
          <div class="forms">
            <div class="form-chip" id="flash-pret">flew</div>
            <div class="form-chip" id="flash-pp">flown</div>
          </div>
          <div class="f-label" style="margin-top:10px" id="flash-btrans">voler</div>
        </div>
      </div>
    </div>
    <div class="flash-nav">
      <button class="prev-btn" id="flash-prev">← Précédent</button>
      <span class="flash-progress" id="flash-prog">1 / 50</span>
      <button class="next-flash" id="flash-next">Suivant →</button>
    </div>
  </div>

  <!-- TABLE MODE -->
  <div id="mode-table" class="game-card" style="display:none;min-height:auto">
    <input class="table-search" id="table-search" placeholder="🔍 Chercher un verbe...">
    <div class="filter-pills" id="filter-pills"></div>
    <table class="verb-table">
      <thead>
        <tr>
          <th>Base Verb</th>
          <th>Prétérit</th>
          <th>Participe Passé</th>
          <th>Traduction</th>
        </tr>
      </thead>
      <tbody id="table-body"></tbody>
    </table>
  </div>

</div>

<script>
// ═══════════════════════════════════════════
//  DATA
// ═══════════════════════════════════════════
const CATEGORIES = {
  "t-endings": { label: "Terminaison -t", color: "#6ECFF6" },
  "ow-sound": { label: "Son /əʊ/", color: "#A78BFA" },
  "ew-own": { label: "-ew / -own", color: "#FFD93D" },
  "aught-ought": { label: "AUGHT/OUGHT", color: "#FF6B6B" },
  "no-change": { label: "Pas de changement", color: "#6BCB77" },
  "d-ending": { label: "Terminaison -d", color: "#F97316" },
  "vowel-change": { label: "Changement voyelle", color: "#EC4899" },
  "irregular": { label: "Vraiment irréguliers", color: "#8B5CF6" },
};

const VERBS = [
  // t-endings
  {bv:"build", pr:"built", pp:"built", tr:"construire", cat:"t-endings"},
  {bv:"burn", pr:"burnt", pp:"burnt", tr:"brûler", cat:"t-endings"},
  {bv:"dream", pr:"dreamt", pp:"dreamt", tr:"rêver", cat:"t-endings"},
  {bv:"feed", pr:"fed", pp:"fed", tr:"nourrir", cat:"t-endings"},
  {bv:"feel", pr:"felt", pp:"felt", tr:"(se) sentir / éprouver", cat:"t-endings"},
  {bv:"keep", pr:"kept", pp:"kept", tr:"garder", cat:"t-endings"},
  {bv:"learn", pr:"learnt", pp:"learnt", tr:"apprendre", cat:"t-endings"},
  {bv:"lose", pr:"lost", pp:"lost", tr:"perdre", cat:"t-endings"},
  {bv:"mean", pr:"meant", pp:"meant", tr:"vouloir dire, signifier", cat:"t-endings"},
  {bv:"meet", pr:"met", pp:"met", tr:"rencontrer", cat:"t-endings"},
  {bv:"send", pr:"sent", pp:"sent", tr:"envoyer", cat:"t-endings"},
  {bv:"sleep", pr:"slept", pp:"slept", tr:"dormir", cat:"t-endings"},
  {bv:"spell", pr:"spelt", pp:"spelt", tr:"épeler", cat:"t-endings"},
  {bv:"spend", pr:"spent", pp:"spent", tr:"dépenser", cat:"t-endings"},
  // ow-sound
  {bv:"break", pr:"broke", pp:"broken", tr:"casser", cat:"ow-sound"},
  {bv:"choose", pr:"chose", pp:"chosen", tr:"choisir", cat:"ow-sound"},
  {bv:"sell", pr:"sold", pp:"sold", tr:"vendre", cat:"ow-sound"},
  {bv:"speak", pr:"spoke", pp:"spoken", tr:"parler", cat:"ow-sound"},
  {bv:"steal", pr:"stole", pp:"stolen", tr:"voler, dérober", cat:"ow-sound"},
  {bv:"tell", pr:"told", pp:"told", tr:"dire", cat:"ow-sound"},
  {bv:"wake", pr:"woke", pp:"woken", tr:"(se) réveiller", cat:"ow-sound"},
  // ew-own
  {bv:"fly", pr:"flew", pp:"flown", tr:"voler", cat:"ew-own"},
  {bv:"grow", pr:"grew", pp:"grown", tr:"grandir / faire pousser", cat:"ew-own"},
  {bv:"know", pr:"knew", pp:"known", tr:"connaître, savoir", cat:"ew-own"},
  {bv:"throw", pr:"threw", pp:"thrown", tr:"jeter / lancer", cat:"ew-own"},
  // aught-ought
  {bv:"bring", pr:"brought", pp:"brought", tr:"apporter", cat:"aught-ought"},
  {bv:"buy", pr:"bought", pp:"bought", tr:"acheter", cat:"aught-ought"},
  {bv:"catch", pr:"caught", pp:"caught", tr:"attraper", cat:"aught-ought"},
  {bv:"fight", pr:"fought", pp:"fought", tr:"(se) battre, combattre", cat:"aught-ought"},
  {bv:"teach", pr:"taught", pp:"taught", tr:"enseigner", cat:"aught-ought"},
  {bv:"think", pr:"thought", pp:"thought", tr:"penser", cat:"aught-ought"},
  // no-change
  {bv:"cost", pr:"cost", pp:"cost", tr:"coûter", cat:"no-change"},
  {bv:"cut", pr:"cut", pp:"cut", tr:"couper", cat:"no-change"},
  {bv:"hit", pr:"hit", pp:"hit", tr:"frapper", cat:"no-change"},
  {bv:"let", pr:"let", pp:"let", tr:"laisser / permettre / louer", cat:"no-change"},
  {bv:"put", pr:"put", pp:"put", tr:"mettre", cat:"no-change"},
  {bv:"shut", pr:"shut", pp:"shut", tr:"fermer", cat:"no-change"},
  // d-ending
  {bv:"do", pr:"did", pp:"done", tr:"faire", cat:"d-ending"},
  {bv:"have", pr:"had", pp:"had", tr:"avoir", cat:"d-ending"},
  {bv:"hear", pr:"heard", pp:"heard", tr:"entendre", cat:"d-ending"},
  {bv:"hide", pr:"hid", pp:"hidden", tr:"cacher", cat:"d-ending"},
  // vowel-change
  {bv:"begin", pr:"began", pp:"begun", tr:"commencer", cat:"vowel-change"},
  {bv:"drink", pr:"drank", pp:"drunk", tr:"boire", cat:"vowel-change"},
  {bv:"drive", pr:"drove", pp:"driven", tr:"conduire", cat:"vowel-change"},
  {bv:"ride", pr:"rode", pp:"ridden", tr:"aller à cheval / à vélo", cat:"vowel-change"},
  {bv:"sing", pr:"sang", pp:"sung", tr:"chanter", cat:"vowel-change"},
  {bv:"swim", pr:"swam", pp:"swum", tr:"nager", cat:"vowel-change"},
  {bv:"write", pr:"wrote", pp:"written", tr:"écrire", cat:"vowel-change"},
  // irregular
  {bv:"be", pr:"was/were", pp:"been", tr:"être", cat:"irregular"},
  {bv:"come", pr:"came", pp:"come", tr:"venir", cat:"irregular"},
  {bv:"draw", pr:"drew", pp:"drawn", tr:"dessiner", cat:"irregular"},
  {bv:"eat", pr:"ate", pp:"eaten", tr:"manger", cat:"irregular"},
  {bv:"fall", pr:"fell", pp:"fallen", tr:"tomber", cat:"irregular"},
  {bv:"find", pr:"found", pp:"found", tr:"trouver", cat:"irregular"},
  {bv:"forget", pr:"forgot", pp:"forgotten", tr:"oublier", cat:"irregular"},
  {bv:"go", pr:"went", pp:"gone", tr:"aller", cat:"irregular"},
  {bv:"hold", pr:"held", pp:"held", tr:"tenir, serrer", cat:"irregular"},
  {bv:"make", pr:"made", pp:"made", tr:"faire, fabriquer", cat:"irregular"},
  {bv:"pay", pr:"paid", pp:"paid", tr:"payer", cat:"irregular"},
  {bv:"run", pr:"ran", pp:"run", tr:"courir", cat:"irregular"},
  {bv:"say", pr:"said", pp:"said", tr:"dire", cat:"irregular"},
  {bv:"see", pr:"saw", pp:"seen", tr:"voir", cat:"irregular"},
  {bv:"shoot", pr:"shot", pp:"shot", tr:"tirer", cat:"irregular"},
  {bv:"sit", pr:"sat", pp:"sat", tr:"s'asseoir", cat:"irregular"},
  {bv:"take", pr:"took", pp:"taken", tr:"prendre", cat:"irregular"},
  {bv:"understand", pr:"understood", pp:"understood", tr:"comprendre", cat:"irregular"},
  {bv:"win", pr:"won", pp:"won", tr:"gagner", cat:"irregular"},
];

// ═══════════════════════════════════════════
//  STATE
// ═══════════════════════════════════════════
let state = {
  mode: 'qcm',
  correct: 0,
  wrong: 0,
  streak: 0,
  qcmIndex: 0,
  qcmShuffled: [],
  qcmAskField: 'pr',
  typeIndex: 0,
  typeShuffled: [],
  flashIndex: 0,
};

function shuffle(arr) {
  const a = [...arr];
  for(let i=a.length-1;i>0;i--){const j=Math.floor(Math.random()*(i+1));[a[i],a[j]]=[a[j],a[i]];}
  return a;
}

// ═══════════════════════════════════════════
//  SCORE
// ═══════════════════════════════════════════
function updateScore() {
  document.getElementById('score-correct').textContent = state.correct;
  document.getElementById('score-wrong').textContent = state.wrong;
  document.getElementById('score-streak').textContent = state.streak;
}

function addCorrect() {
  state.correct++;
  state.streak++;
  if(state.streak > 0 && state.streak % 5 === 0) launchConfetti();
  updateScore();
}
function addWrong() {
  state.wrong++;
  state.streak = 0;
  updateScore();
}

// ═══════════════════════════════════════════
//  CONFETTI
// ═══════════════════════════════════════════
function launchConfetti() {
  const colors = ['#6ECFF6','#FFD93D','#FF6B6B','#6BCB77','#A78BFA'];
  for(let i=0;i<40;i++){
    const el = document.createElement('div');
    el.classList.add('confetti-piece');
    el.style.cssText = `
      left:${Math.random()*100}vw;
      width:${8+Math.random()*8}px;
      height:${8+Math.random()*8}px;
      background:${colors[Math.floor(Math.random()*colors.length)]};
      animation-duration:${1+Math.random()*1.5}s;
      animation-delay:${Math.random()*0.5}s;
      transform:rotate(${Math.random()*360}deg);
    `;
    document.body.appendChild(el);
    el.addEventListener('animationend',()=>el.remove());
  }
}

// ═══════════════════════════════════════════
//  MODE SWITCHING
// ═══════════════════════════════════════════
document.querySelectorAll('.mode-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('.mode-btn').forEach(b=>b.classList.remove('active'));
    btn.classList.add('active');
    const mode = btn.dataset.mode;
    state.mode = mode;
    ['qcm','type','flash','table'].forEach(m => {
      document.getElementById('mode-'+m).style.display = m===mode ? 'flex' : 'none';
    });
    if(mode==='qcm') initQCM();
    if(mode==='type') initType();
    if(mode==='flash') initFlash();
    if(mode==='table') initTable();
  });
});

// ═══════════════════════════════════════════
//  QCM MODE
// ═══════════════════════════════════════════
function initQCM() {
  state.qcmShuffled = shuffle(VERBS);
  state.qcmIndex = 0;
  renderQCM();
}

function renderQCM() {
  if(state.qcmIndex >= state.qcmShuffled.length) {
    state.qcmShuffled = shuffle(VERBS);
    state.qcmIndex = 0;
  }
  const v = state.qcmShuffled[state.qcmIndex];
  // alternate between pr and pp question
  state.qcmAskField = Math.random() > 0.5 ? 'pr' : 'pp';
  const askLabel = state.qcmAskField === 'pr' ? 'Prétérit' : 'Participe Passé';

  document.getElementById('qcm-cat').textContent = CATEGORIES[v.cat].label;
  document.getElementById('qcm-cat').style.background = CATEGORIES[v.cat].color + '44';
  document.getElementById('qcm-cat').style.color = CATEGORIES[v.cat].color.replace('FF','AA') || '#666';
  document.getElementById('qcm-verb').textContent = v.bv;
  document.getElementById('qcm-trans').textContent = v.tr;
  document.getElementById('qcm-question').innerHTML = `Quelle est la forme au <strong>${askLabel}</strong> ?`;
  document.getElementById('qcm-feedback').classList.remove('show');
  document.getElementById('qcm-next').classList.remove('show');

  const progress = (state.qcmIndex / state.qcmShuffled.length * 100);
  document.getElementById('qcm-progress').style.width = progress + '%';

  // Build options: 1 correct + 3 distractors
  const correct = v[state.qcmAskField];
  const others = shuffle(VERBS.filter(x => x.bv !== v.bv)).slice(0,3).map(x => x[state.qcmAskField]);
  const options = shuffle([correct, ...others]);

  const grid = document.getElementById('qcm-options');
  grid.innerHTML = '';
  options.forEach(opt => {
    const btn = document.createElement('button');
    btn.className = 'option-btn';
    btn.textContent = opt;
    btn.addEventListener('click', () => onQCMAnswer(opt, correct, grid));
    grid.appendChild(btn);
  });
}

function onQCMAnswer(chosen, correct, grid) {
  const btns = grid.querySelectorAll('.option-btn');
  btns.forEach(b => b.disabled = true);
  const fb = document.getElementById('qcm-feedback');

  if(chosen === correct) {
    btns.forEach(b => { if(b.textContent===chosen) b.classList.add('correct'); });
    fb.textContent = '🎉 Bravo ! C\'est correct !';
    fb.className = 'feedback show good';
    addCorrect();
  } else {
    btns.forEach(b => {
      if(b.textContent===chosen) b.classList.add('wrong');
      if(b.textContent===correct) b.classList.add('correct');
    });
    fb.textContent = `❌ La bonne réponse était : ${correct}`;
    fb.className = 'feedback show bad';
    addWrong();
  }
  document.getElementById('qcm-next').classList.add('show');
}

document.getElementById('qcm-next').addEventListener('click', () => {
  state.qcmIndex++;
  renderQCM();
});

// ═══════════════════════════════════════════
//  TYPE MODE
// ═══════════════════════════════════════════
function initType() {
  state.typeShuffled = shuffle(VERBS);
  state.typeIndex = 0;
  renderType();
}

function renderType() {
  if(state.typeIndex >= state.typeShuffled.length) {
    state.typeShuffled = shuffle(VERBS);
    state.typeIndex = 0;
  }
  const v = state.typeShuffled[state.typeIndex];
  document.getElementById('type-cat').textContent = CATEGORIES[v.cat].label;
  document.getElementById('type-verb').textContent = v.bv;
  document.getElementById('type-trans').textContent = v.tr;
  document.getElementById('input-pret').value = '';
  document.getElementById('input-pp').value = '';
  document.getElementById('input-pret').className = '';
  document.getElementById('input-pp').className = '';
  document.getElementById('type-feedback').classList.remove('show');
  document.getElementById('type-next').classList.remove('show');
  document.getElementById('type-submit').style.display = 'inline-block';
  document.getElementById('input-pret').focus();
  const progress = (state.typeIndex / state.typeShuffled.length * 100);
  document.getElementById('type-progress').style.width = progress + '%';
}

document.getElementById('type-submit').addEventListener('click', () => {
  const v = state.typeShuffled[state.typeIndex];
  const pretVal = document.getElementById('input-pret').value.trim().toLowerCase();
  const ppVal = document.getElementById('input-pp').value.trim().toLowerCase();
  const pretOk = pretVal === v.pr.toLowerCase();
  const ppOk = ppVal === v.pp.toLowerCase();

  document.getElementById('input-pret').className = pretOk ? 'correct' : 'wrong';
  document.getElementById('input-pp').className = ppOk ? 'correct' : 'wrong';

  const fb = document.getElementById('type-feedback');
  if(pretOk && ppOk) {
    fb.textContent = '🎉 Parfait ! Les deux formes sont correctes !';
    fb.className = 'feedback show good';
    addCorrect();
  } else {
    let msg = '❌ ';
    if(!pretOk) msg += `Prétérit: ${v.pr}  `;
    if(!ppOk) msg += `Participe Passé: ${v.pp}`;
    fb.textContent = msg;
    fb.className = 'feedback show bad';
    addWrong();
  }
  document.getElementById('type-submit').style.display = 'none';
  document.getElementById('type-next').classList.add('show');
});

document.addEventListener('keydown', e => {
  if(e.key==='Enter') {
    const submitVisible = document.getElementById('type-submit').style.display !== 'none';
    if(state.mode==='type' && submitVisible) document.getElementById('type-submit').click();
  }
});

document.getElementById('type-next').addEventListener('click', () => {
  state.typeIndex++;
  renderType();
});

// ═══════════════════════════════════════════
//  FLASHCARD MODE
// ═══════════════════════════════════════════
const FLASH_ORDER = shuffle([...VERBS]);

function initFlash() {
  state.flashIndex = 0;
  renderFlash();
}

function renderFlash() {
  const v = FLASH_ORDER[state.flashIndex];
  document.getElementById('flash-inner').classList.remove('flipped');
  document.getElementById('flash-bv').textContent = v.bv;
  document.getElementById('flash-ftrans').textContent = v.tr;
  document.getElementById('flash-back-bv').textContent = v.bv;
  document.getElementById('flash-pret').textContent = v.pr;
  document.getElementById('flash-pp').textContent = v.pp;
  document.getElementById('flash-btrans').textContent = v.tr;
  document.getElementById('flash-prog').textContent = `${state.flashIndex+1} / ${FLASH_ORDER.length}`;

  // stars based on streaks
  const s = Math.min(5, Math.floor(state.streak/2)+1);
  document.getElementById('flash-stars').textContent = '⭐'.repeat(s) + '☆'.repeat(5-s);
}

document.getElementById('flash-card').addEventListener('click', () => {
  document.getElementById('flash-inner').classList.toggle('flipped');
});

document.getElementById('flash-next').addEventListener('click', () => {
  state.flashIndex = (state.flashIndex+1) % FLASH_ORDER.length;
  renderFlash();
});
document.getElementById('flash-prev').addEventListener('click', () => {
  state.flashIndex = (state.flashIndex-1+FLASH_ORDER.length) % FLASH_ORDER.length;
  renderFlash();
});

// ═══════════════════════════════════════════
//  TABLE MODE
// ═══════════════════════════════════════════
let tableFilter = 'all';
let tableSearch = '';

function initTable() {
  // filter pills
  const fp = document.getElementById('filter-pills');
  fp.innerHTML = '<button class="filter-pill active" data-f="all">Tous</button>';
  Object.entries(CATEGORIES).forEach(([k,v]) => {
    fp.innerHTML += `<button class="filter-pill" data-f="${k}" style="--fc:${v.color}">${v.label}</button>`;
  });
  fp.querySelectorAll('.filter-pill').forEach(b => {
    b.addEventListener('click', () => {
      fp.querySelectorAll('.filter-pill').forEach(x=>x.classList.remove('active'));
      b.classList.add('active');
      tableFilter = b.dataset.f;
      renderTable();
    });
  });
  document.getElementById('table-search').addEventListener('input', e => {
    tableSearch = e.target.value.toLowerCase();
    renderTable();
  });
  renderTable();
}

function renderTable() {
  let filtered = VERBS;
  if(tableFilter !== 'all') filtered = filtered.filter(v=>v.cat===tableFilter);
  if(tableSearch) filtered = filtered.filter(v =>
    v.bv.includes(tableSearch) || v.pr.includes(tableSearch) || v.pp.includes(tableSearch) || v.tr.toLowerCase().includes(tableSearch)
  );

  const tbody = document.getElementById('table-body');
  tbody.innerHTML = filtered.map(v => {
    const cat = CATEGORIES[v.cat];
    return `<tr>
      <td><span class="cat-dot" style="background:${cat.color}"></span>${v.bv}</td>
      <td>${v.pr}</td>
      <td>${v.pp}</td>
      <td>${v.tr}</td>
    </tr>`;
  }).join('');
}

// ═══════════════════════════════════════════
//  INIT
// ═══════════════════════════════════════════
initQCM();
</script>
</body>
</html>
