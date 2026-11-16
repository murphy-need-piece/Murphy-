<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Ada yang mau aku bilang</title>
<style>
:root {
  --c1: #ffb3cf; --c2: #ff7aa8; --c3: #e0457b; --card: rgba(255,255,255,.16);
  --sans: "Nunito", system-ui, -apple-system, "Segoe UI", sans-serif;
  padding-top: env(safe-area-inset-top, 0px); padding-bottom: env(safe-area-inset-bottom, 0px);
}
* { box-sizing: border-box; }
html, body { margin: 0; min-height: 100%; }
body {
  font-family: var(--sans); color: #fff; min-height: 100vh; overflow-x: hidden;
  background: linear-gradient(180deg, var(--c1), var(--c2) 55%, var(--c3));
  display: flex; justify-content: center;
}
.hearts { position: fixed; inset: 0; z-index: 0; pointer-events: none; overflow: hidden; }
.hearts span { position: absolute; bottom: -40px; opacity: .45; animation: rise linear infinite; }
@keyframes rise { to { transform: translateY(-110vh) rotate(25deg); } }

.app { position: relative; z-index: 1; width: 100%; max-width: 420px; padding: 28px 22px 36px; display: flex; flex-direction: column; min-height: 100vh; }
.meter-top { display: flex; justify-content: space-between; font-weight: 700; margin-bottom: 8px; }
.bar { height: 12px; background: rgba(255,255,255,.3); border-radius: 999px; overflow: hidden; }
.fill { height: 100%; width: 0; background: #fff; border-radius: 999px; transition: width .6s ease; }

.stage { flex: 1; display: flex; flex-direction: column; justify-content: center; text-align: center; padding: 20px 0; }
.icon { font-size: 88px; margin: 8px 0 14px; animation: bob 2.4s ease-in-out infinite; }
@keyframes bob { 50% { transform: translateY(-8px); } }
.card { background: var(--card); border: 1px solid rgba(255,255,255,.3); border-radius: 22px; padding: 20px 22px; backdrop-filter: blur(6px); margin-bottom: 18px; min-height: 150px; display: flex; align-items: center; justify-content: center; }
.line { font-size: 1.07rem; line-height: 1.8; margin: 0; opacity: 0; transform: translateY(8px); transition: opacity .6s, transform .6s; }
.line.show { opacity: 1; transform: none; }

button {
  font: 700 1rem var(--sans); color: var(--c3); background: #fff; border: 0; border-radius: 999px;
  padding: 15px 22px; width: 100%; cursor: pointer; box-shadow: 0 10px 24px rgba(120,0,60,.3);
  touch-action: manipulation; transition: transform .15s;
}
button:active { transform: scale(.97); }
button:focus-visible { outline: 3px solid #fff; outline-offset: 3px; }
.ghost { background: transparent; color: #fff; border: 2px solid rgba(255,255,255,.7); box-shadow: none; margin-top: 10px; }
.hidden { display: none !important; }

.final h1 { font-size: 2rem; line-height: 1.2; margin: 6px 0 12px; }
.qs { font-size: 1.25rem; font-weight: 700; margin: 12px 0 22px; }
.note { font-size: .92rem; opacity: .9; margin-top: 12px; min-height: 1.4em; }
.thanks .big { font-size: 80px; animation: bob 2s ease-in-out infinite; }
.burst { position: fixed; z-index: 5; pointer-events: none; font-size: 26px; animation: up 1.6s ease-out forwards; }
@keyframes up { to { transform: translate(var(--dx), -60vh) scale(1.4); opacity: 0; } }
@media (prefers-reduced-motion: reduce) { .hearts, .burst { display: none; } .icon, .thanks .big { animation: none; } }
</style>
</head>
<body>
<div class="hearts" id="hearts" aria-hidden="true"></div>

<main class="app">
  <div>
    <div class="meter-top"><span>Perasaanku</span><span id="pct">0%</span></div>
    <div class="bar"><div class="fill" id="fill"></div></div>
  </div>

  <section class="stage" id="stage">
    <div class="icon" aria-hidden="true">💌</div>
    <div class="card"><p class="line" id="line"></p></div>
    <button type="button" id="next">Lanjut 🤍</button>
  </section>

  <section class="stage final hidden" id="final">
    <h1>Jadi begini...</h1>
    <p class="line show">Aku sudah lama memendam ini, dan sekarang aku nggak mau menyimpannya lagi.</p>
    <div class="qs">Mau nggak kamu jadi pacarku? 💖</div>
    <button type="button" id="yes">Mau, aku mau! 💖</button>
    <button type="button" id="no" class="ghost">Belum sekarang</button>
    <div class="note" id="note"></div>
  </section>

  <section class="stage thanks hidden" id="thanks" role="status" aria-live="polite">
    <div class="big" aria-hidden="true">🥰</div>
    <h1 style="font-size:2rem;margin:8px 0">Yeay, makasih!</h1>
    <p class="line show">Aku janji bakal bikin kamu senyum tiap hari. Mulai sekarang, kamu ada di hatiku dan aku bangga bisa jadi pacarmu. 🤍</p>
  </section>
</main>

<script>
(function () {
  var steps = [
    "Hai. Aku bingung mulai dari mana, jadi aku ngomong apa adanya aja ya.",
    "Sejak kenal kamu, hari-hariku jadi lebih berwarna. Hal kecil kayak obrolan random pun jadi favoritku.",
    "Aku suka cara kamu ketawa, sampai kadang aku pengen bikin lucu cuma biar bisa dengar tawamu lagi.",
    "Aku suka cara kamu peduli sama hal-hal kecil, dan cara kamu bikin orang di sekitarmu merasa nyaman.",
    "Aku sadar, aku nggak mau cuma jadi teman yang nemenin kamu. Aku mau jadi orang yang kamu pilih.",
    "Aku mungkin belum sempurna. Aku bisa salah, bisa lupa, bisa kelewat cuek. Tapi aku janji bakal belajar dan berusaha lebih baik.",
    "Aku mau pelan-pelan kenal kamu lebih dalam: mimpi kamu, hal yang bikin kamu sedih, dan hal yang bikin kamu bahagia.",
    "Aku mau jadi orang yang ada waktu kamu cerita, dan orang yang kamu tuju waktu butuh peluk.",
    "Aku juga pengen bikin kamu merasa aman, dihargai, dan nggak pernah sendirian lagi.",
    "Kalau ada hari kamu capek, aku mau jadi tempat kamu pulang. Bukan buat nyelesaiin semuanya, tapi biar kamu nggak ngerasa sendiri.",
    "Aku tahu ini bukan keputusan kecil. Jadi kamu nggak perlu jawab sekarang kalau belum siap, dan aku bakal tetap ngehargai apa pun jawabanmu.",
    "Tapi kalau boleh aku jujur, aku sayang sama kamu. Aku ingin kita mulai ini bareng. Mau nggak kamu jadi pacarku?"
  ];
  var index = 0;
  var line = document.getElementById("line");
  var fill = document.getElementById("fill");
  var pct = document.getElementById("pct");
  var stage = document.getElementById("stage");
  var next = document.getElementById("next");

  function setMeter(p) { fill.style.width = p + "%"; pct.textContent = p + "%"; }

  function showStep() {
    line.classList.remove("show");
    line.textContent = steps[index];
    requestAnimationFrame(function () { line.classList.add("show"); });
    setMeter(Math.round(((index + 1) / steps.length) * 100));
  }

  next.addEventListener("click", function () {
    index++;
    if (index < steps.length) { showStep(); return; }
    stage.classList.add("hidden");
    document.getElementById("final").classList.remove("hidden");
    setMeter(100);
  });

  var yes = document.getElementById("yes");
  var no = document.getElementById("no");
  var note = document.getElementById("note");
  var replies = [
    "Nggak apa-apa, aku tetap di sini kok.",
    "Aku ngerti, santai aja, aku nggak akan maksa.",
    "Kalau kamu berubah pikiran, aku masih di sini ya. 🤍"
  ];
  var tries = 0;
  no.addEventListener("click", function () {
    note.textContent = replies[tries % replies.length];
    tries++;
  });

  yes.addEventListener("click", function () {
    document.getElementById("final").classList.add("hidden");
    document.getElementById("thanks").classList.remove("hidden");
    var set = ["💖", "💕", "🌹", "✨", "💗"];
    for (var i = 0; i < 26; i++) {
      (function (k) {
        var b = document.createElement("div");
        b.className = "burst";
        b.textContent = set[k % set.length];
        b.style.left = (20 + Math.random() * 60) + "vw";
        b.style.top = (60 + Math.random() * 30) + "vh";
        b.style.setProperty("--dx", (Math.random() * 160 - 80) + "px");
        b.style.animationDelay = (Math.random() * 0.5) + "s";
        document.body.appendChild(b);
        setTimeout(function () { b.remove(); }, 2400);
      })(i);
    }
  });

  var hearts = document.getElementById("hearts");
  var icons = ["💗", "💕", "🤍", "🌸"];
  for (var h = 0; h < 12; h++) {
    var s = document.createElement("span");
    s.textContent = icons[h % icons.length];
    s.style.left = (Math.random() * 100) + "vw";
    s.style.fontSize = (14 + Math.random() * 14) + "px";
    s.style.animationDuration = (10 + Math.random() * 10) + "s";
    s.style.animationDelay = (-Math.random() * 16) + "s";
    hearts.appendChild(s);
  }

  showStep();
})();
</script>
</body>
</html>
