const sky = document.getElementById("sky");
const heartField = document.getElementById("heartField");
const sparkles = document.getElementById("sparkles");

function random(min, max) { return Math.random() * (max - min) + min; }

// Layered stars: small, warm points of light in the deep blue sky.
for (let i = 0; i < 175; i++) {
  const star = document.createElement("i");
  star.className = "star";
  const size = random(1, 2.8);
  star.style.width = size + "px";
  star.style.height = size + "px";
  star.style.left = random(0, 100) + "%";
  star.style.top = random(1, 75) + "%";
  star.style.setProperty("--t", random(2.5, 6) + "s");
  star.style.animationDelay = random(0, 5) + "s";
  sky.appendChild(star);
}

// Decorative hearts float slowly; outlined ones softly pulse.
const heartLayout = [
  [17, 25, 52, false], [51, 34, 25, false], [90, 29, 43, true],
  [17, 44, 31, true], [89, 45, 34, true], [6, 62, 27, true],
  [71, 59, 28, true], [55, 72, 21, true], [14, 83, 31, false],
  [90, 87, 14, true]
];
heartLayout.forEach(([x, y, size, ring], index) => {
  const el = document.createElement("div");
  if (ring) {
    el.className = "heart-ring";
    el.style.width = size + 19 + "px";
    el.style.height = size + 19 + "px";
    el.style.setProperty("--fs", size + "px");
  } else {
    el.className = "heart";
    el.innerHTML = '<svg viewBox="0 0 24 24" aria-hidden="true"><path d="M12 21s-9-5.8-9-12.1C3 3.7 9.1 2.1 12 7c2.9-4.9 9-3.3 9 1.9C21 15.2 12 21 12 21z"/></svg>';
    el.style.width = size + "px";
    el.style.height = size + "px";
    el.style.setProperty("--speed", random(4, 7) + "s");
    el.style.setProperty("--delay", random(0, 2) + "s");
  }
  el.style.left = x + "%";
  el.style.top = y + "%";
  heartField.appendChild(el);
});

// Fine fireflies gather around the flowers.
for (let i = 0; i < 42; i++) {
  const dot = document.createElement("i");
  dot.className = "spark" + (i % 3 === 0 ? " green" : "");
  dot.style.left = random(13, 87) + "%";
  dot.style.top = random(54, 94) + "%";
  dot.style.setProperty("--t", random(2.5, 5.5) + "s");
  dot.style.animationDelay = random(0, 5) + "s";
  sparkles.appendChild(dot);
}

// Random blades add depth to the grass without image assets.
function addGrass(containerId, count, front) {
  const container = document.getElementById(containerId);
  for (let i = 0; i < count; i++) {
    const blade = document.createElement("i");
    blade.className = "blade";
    blade.style.left = random(-2, 102) + "%";
    blade.style.height = random(front ? 14 : 22, front ? 42 : 62) + "%";
    blade.style.transform = `rotate(${random(-34, 34)}deg)`;
    blade.style.width = random(1.5, 3.5) + "px";
    blade.style.opacity = random(.35, .9);
    container.appendChild(blade);
  }
}
addGrass("grassBack", 28, false);
addGrass("grassFront", 35, true);


// Tap any floating heart (or anywhere in the sky) to release a little heart burst.
const burstLayer = document.getElementById("heartBurstLayer");
const hint = document.getElementById("hint");

function makeHeartBurst(x, y) {
  for (let i = 0; i < 9; i++) {
    const heart = document.createElement("span");
    heart.className = "burst-heart";
    heart.textContent = i % 3 === 0 ? "♡" : "♥";
    heart.style.left = x + "px";
    heart.style.top = y + "px";
    heart.style.setProperty("--dx", random(-75, 75) + "px");
    heart.style.setProperty("--dy", random(45, 135) + "px");
    heart.style.setProperty("--scale", random(.65, 1.35));
    heart.style.setProperty("--rot", random(-45, 45) + "deg");
    heart.style.animationDelay = random(0, .16) + "s";
    burstLayer.appendChild(heart);
    heart.addEventListener("animationend", () => heart.remove(), { once: true });
  }
}
document.querySelector(".garden").addEventListener("pointerdown", (event) => {
  // Ignore the music control so its tap only toggles audio.
  if (event.target.closest(".music-toggle")) return;
  makeHeartBurst(event.clientX, event.clientY);
  if (hint) hint.style.opacity = "0";
});

// A gentle, original ambient melody made in the browser. No external audio files.
const musicButton = document.getElementById("musicToggle");
let audioContext = null;
let musicTimer = null;
let musicOn = false;
let noteIndex = 0;
const notes = [261.63, 329.63, 392.00, 329.63, 293.66, 349.23, 440.00, 349.23];

function playSoftNote(frequency, startTime, duration, volume) {
  const oscillator = audioContext.createOscillator();
  const gain = audioContext.createGain();
  const filter = audioContext.createBiquadFilter();
  oscillator.type = "sine";
  oscillator.frequency.setValueAtTime(frequency, startTime);
  filter.type = "lowpass";
  filter.frequency.setValueAtTime(1200, startTime);
  gain.gain.setValueAtTime(0.0001, startTime);
  gain.gain.exponentialRampToValueAtTime(volume, startTime + 0.12);
  gain.gain.exponentialRampToValueAtTime(0.0001, startTime + duration);
  oscillator.connect(filter);
  filter.connect(gain);
  gain.connect(audioContext.destination);
  oscillator.start(startTime);
  oscillator.stop(startTime + duration + 0.04);
}

function scheduleMelody() {
  if (!musicOn || !audioContext) return;
  const now = audioContext.currentTime;
  const frequency = notes[noteIndex % notes.length];
  playSoftNote(frequency, now, 1.7, 0.035);
  // A quiet lower note gives the melody a warm, dreamy bed.
  if (noteIndex % 2 === 0) playSoftNote(frequency / 2, now, 2.2, 0.012);
  noteIndex++;
}

async function toggleMusic() {
  if (!audioContext) {
    const AudioContextClass = window.AudioContext || window.webkitAudioContext;
    if (!AudioContextClass) {
      musicButton.querySelector(".music-label").textContent = "NOT SUPPORTED";
      return;
    }
    audioContext = new AudioContextClass();
  }
  if (musicOn) {
    musicOn = false;
    clearInterval(musicTimer);
    musicTimer = null;
    await audioContext.suspend();
    musicButton.classList.remove("playing");
    musicButton.querySelector(".music-label").textContent = "MUSIC OFF";
    musicButton.setAttribute("aria-pressed", "false");
  } else {
    await audioContext.resume();
    musicOn = true;
    scheduleMelody();
    musicTimer = setInterval(scheduleMelody, 1250);
    musicButton.classList.add("playing");
    musicButton.querySelector(".music-label").textContent = "MUSIC ON";
    musicButton.setAttribute("aria-pressed", "true");
  }
}
musicButton.addEventListener("click", toggleMusic);
