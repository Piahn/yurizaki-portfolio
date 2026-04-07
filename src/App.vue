<script setup>
import { ref, onMounted, onUnmounted, nextTick } from "vue";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

/* ─── State ─── */
const isMobileMenuOpen = ref(false);
const isScrolled = ref(false);
const isLoaded = ref(false);

/* ─── Rotating Hero Text ─── */
const rotatingWords = ["RPG Adventure", "Dungeon Hunter", "Guild Master", "Boss Slayer", "Epic Loot"];
const currentWordIndex = ref(0);
const textVisible = ref(true);
let wordInterval = null;

function startRotatingText() {
  wordInterval = setInterval(() => {
    textVisible.value = false;
    setTimeout(() => {
      currentWordIndex.value = (currentWordIndex.value + 1) % rotatingWords.length;
      textVisible.value = true;
    }, 400);
  }, 3000);
}

/* ─── Scroll Handler for Navbar Morph ─── */
function handleScroll() {
  isScrolled.value = window.scrollY > 50;
}

/* ─── Custom Cursor ─── */
const cursorX = ref(-100);
const cursorY = ref(-100);
const dotX = ref(-100);
const dotY = ref(-100);
const cursorHovering = ref(false);

function onMouseMove(e) {
  dotX.value = e.clientX;
  dotY.value = e.clientY;
  gsap.to({ x: cursorX.value, y: cursorY.value }, {
    x: e.clientX, y: e.clientY, duration: 0.5, ease: "power3.out",
    onUpdate: function() {
      cursorX.value = this.targets()[0].x;
      cursorY.value = this.targets()[0].y;
    }
  });
}

function setupCursorHover() {
  document.querySelectorAll("a, button, .magnetic-btn").forEach(el => {
    el.addEventListener("mouseenter", () => cursorHovering.value = true);
    el.addEventListener("mouseleave", () => cursorHovering.value = false);
  });
}

/* ─── GSAP: Loading Intro ─── */
function playIntro() {
  const tl = gsap.timeline({
    onComplete: () => {
      isLoaded.value = true;
    }
  });

  tl.to(".loading-bar-fill", { width: "100%", duration: 0.8, ease: "power2.inOut" })
    .to(".loading-text", { opacity: 0, y: -20, duration: 0.2 }, "-=0.2")
    .to(".loading-screen", { yPercent: -100, duration: 0.6, ease: "power4.inOut" });
}

/* ─── GSAP: Hero Entrance ─── */
function playHeroEntrance() {
  const tl = gsap.timeline();

  // Navbar items stagger
  tl.fromTo(".nav-animate",
    { y: -30, opacity: 0 },
    { y: 0, opacity: 1, duration: 0.6, stagger: 0.08, ease: "power3.out" }, 0);

  // Hero badge
  tl.fromTo(".hero-badge",
    { y: 30, opacity: 0, scale: 0.9 },
    { y: 0, opacity: 1, scale: 1, duration: 0.7, ease: "back.out(1.7)" }, 0.2);

  // Hero title
  tl.fromTo(".hero-title-line",
    { y: 80, opacity: 0 },
    { y: 0, opacity: 1, duration: 0.8, stagger: 0.15, ease: "power3.out" }, 0.3);

  // Hero description
  tl.fromTo(".hero-desc",
    { y: 40, opacity: 0 },
    { y: 0, opacity: 1, duration: 0.7, ease: "power3.out" }, 0.7);

  // CTA buttons
  tl.fromTo(".hero-cta",
    { y: 30, opacity: 0, scale: 0.95 },
    { y: 0, opacity: 1, scale: 1, duration: 0.6, stagger: 0.1, ease: "back.out(1.4)" }, 0.9);

  // Stats
  tl.fromTo(".hero-stat",
    { y: 40, opacity: 0 },
    { y: 0, opacity: 1, duration: 0.5, stagger: 0.1, ease: "power3.out" }, 1.1);
}

/* ─── GSAP: Scroll-triggered Animations (safe pattern) ─── */
function setupScrollAnimations() {
  // Section headers
  gsap.utils.toArray(".gsap-section-header").forEach(el => {
    gsap.set(el, { y: 60, opacity: 0 });
    ScrollTrigger.create({
      trigger: el, start: "top 88%",
      onEnter: () => gsap.to(el, { y: 0, opacity: 1, duration: 0.8, ease: "power3.out" })
    });
  });

  // Feature cards - staggered
  gsap.utils.toArray(".gsap-card-grid").forEach(grid => {
    const cards = grid.querySelectorAll(".gsap-card");
    gsap.set(cards, { y: 80, opacity: 0, scale: 0.95 });
    ScrollTrigger.create({
      trigger: grid, start: "top 85%",
      onEnter: () => gsap.to(cards, { y: 0, opacity: 1, scale: 1, duration: 0.7, stagger: 0.12, ease: "power3.out" })
    });
  });

  // Command list items - slide in from left
  const cmdItems = document.querySelectorAll(".gsap-cmd-item");
  gsap.set(cmdItems, { x: -60, opacity: 0 });
  ScrollTrigger.create({
    trigger: cmdItems[0], start: "top 90%",
    onEnter: () => gsap.to(cmdItems, { x: 0, opacity: 1, duration: 0.6, stagger: 0.08, ease: "power3.out" })
  });

  // Chat mockup - scale up
  const chatMock = document.querySelector(".gsap-chat-mockup");
  if (chatMock) {
    gsap.set(chatMock, { x: 80, opacity: 0, scale: 0.92 });
    ScrollTrigger.create({
      trigger: chatMock, start: "top 85%",
      onEnter: () => gsap.to(chatMock, { x: 0, opacity: 1, scale: 1, duration: 1, ease: "power3.out" })
    });
  }

  // Chat messages - staggered
  const chatMsgs = document.querySelectorAll(".gsap-chat-msg");
  gsap.set(chatMsgs, { y: 30, opacity: 0 });
  ScrollTrigger.create({
    trigger: chatMsgs[0], start: "top 92%",
    onEnter: () => gsap.to(chatMsgs, { y: 0, opacity: 1, duration: 0.5, stagger: 0.15, ease: "power2.out" })
  });

  // Class cards
  const classCards = document.querySelectorAll(".gsap-class-card");
  if (classCards.length) {
    gsap.set(classCards, { y: 100, opacity: 0, rotateY: 15 });
    ScrollTrigger.create({
      trigger: classCards[0].parentElement, start: "top 85%",
      onEnter: () => gsap.to(classCards, { y: 0, opacity: 1, rotateY: 0, duration: 0.8, stagger: 0.15, ease: "power3.out" })
    });
  }

  // Leaderboard rows
  const lbRows = document.querySelectorAll(".gsap-lb-row");
  lbRows.forEach((el, i) => {
    gsap.set(el, { x: i % 2 === 0 ? -40 : 40, opacity: 0 });
  });
  ScrollTrigger.create({
    trigger: lbRows[0], start: "top 92%",
    onEnter: () => gsap.to(lbRows, { x: 0, opacity: 1, duration: 0.6, stagger: 0.1, ease: "power3.out" })
  });

  // CTA section
  const ctaBox = document.querySelector(".gsap-cta-box");
  if (ctaBox) {
    gsap.set(ctaBox, { y: 60, opacity: 0, scale: 0.95 });
    ScrollTrigger.create({
      trigger: ctaBox, start: "top 85%",
      onEnter: () => gsap.to(ctaBox, { y: 0, opacity: 1, scale: 1, duration: 0.9, ease: "power3.out" })
    });
  }

  // Stat bars
  gsap.utils.toArray(".gsap-stat-bar").forEach(bar => {
    const w = bar.dataset.width;
    gsap.set(bar, { width: "0%" });
    ScrollTrigger.create({
      trigger: bar, start: "top 92%",
      onEnter: () => gsap.to(bar, { width: w + "%", duration: 1.2, ease: "power3.out" })
    });
  });
}

/* ─── GSAP: Parallax Blobs ─── */
function setupParallax() {
  gsap.utils.toArray(".parallax-blob").forEach(blob => {
    const speed = blob.dataset.speed || 0.5;
    gsap.to(blob, {
      y: () => ScrollTrigger.maxScroll(window) * speed * -0.1,
      ease: "none",
      scrollTrigger: {
        trigger: document.body, start: "top top", end: "bottom bottom",
        scrub: 1, invalidateOnRefresh: true
      }
    });
  });
}

/* ─── GSAP: Magnetic Buttons ─── */
function setupMagneticButtons() {
  document.querySelectorAll(".magnetic-btn").forEach(btn => {
    btn.addEventListener("mousemove", (e) => {
      const rect = btn.getBoundingClientRect();
      const x = e.clientX - rect.left - rect.width / 2;
      const y = e.clientY - rect.top - rect.height / 2;
      gsap.to(btn, { x: x * 0.3, y: y * 0.3, duration: 0.4, ease: "power2.out" });
    });
    btn.addEventListener("mouseleave", () => {
      gsap.to(btn, { x: 0, y: 0, duration: 0.6, ease: "elastic.out(1, 0.3)" });
    });
  });
}

/* ─── Nav Links ─── */
const navLinks = [
  { label: "Gameplay", href: "#gameplay" },
  { label: "Fitur", href: "#fitur" },
  { label: "Kelas", href: "#kelas" },
  { label: "Leaderboard", href: "#leaderboard" },
];

const gameplayFeatures = [
  { icon: "sword", title: "Dungeon Gate", desc: "Masuki dungeon berlantai dengan monster yang semakin kuat. Kalahkan boss di setiap 10 lantai untuk mendapatkan loot legendaris.", stat: "100 Lantai" },
  { icon: "shield", title: "Sistem Battle", desc: "Pertarungan turn-based dengan skill combo, elemental weakness, dan critical hit system yang mendebarkan.", stat: "PvE & PvP" },
  { icon: "gem", title: "Gacha & Craft", desc: "Summon equipment dari banner gacha atau craft langsung dari material dungeon. Ratusan item unik tersedia.", stat: "500+ Items" },
  { icon: "users", title: "Guild War", desc: "Bentuk guild bersama teman, serang guild lawan, dan rebut territory untuk bonus EXP dan Gold harian.", stat: "Real-time" },
  { icon: "star", title: "Schenario System", desc: "Memasuki dunia Dungeon gate Yurizaki dengan cerita yang menarik dan penuh petualangan dan misteri.", stat: "50+ Schenario" },
  { icon: "zap", title: "Daily Quest", desc: "Selesaikan misi harian untuk mendapatkan reward eksklusif, termasuk summon ticket dan rare materials.", stat: "Auto Reset" },
];

const classes = [
  { name: "Warrior", emoji: "⚔️", color: "from-red-500 to-orange-500", hp: 95, atk: 80, def: 90, desc: "Tank & DPS dengan armor tebal dan serangan brutal." },
  { name: "Mage", emoji: "🔮", color: "from-violet-500 to-blue-500", hp: 60, atk: 95, def: 50, desc: "Master sihir dengan damage area yang menghancurkan." },
  { name: "Assassin", emoji: "🗡️", color: "from-emerald-500 to-cyan-500", hp: 70, atk: 90, def: 55, desc: "Silent killer dengan critical rate dan evasion tinggi." },
  { name: "Healer", emoji: "✨", color: "from-amber-400 to-yellow-500", hp: 75, atk: 40, def: 70, desc: "Support terbaik dengan skill heal dan buff party." },
];

const leaderboard = [
  { rank: 1, name: "ShadowKing", level: 99, cls: "Warrior", power: "1.2M", medal: "🥇" },
  { rank: 2, name: "ArcMage", level: 97, cls: "Mage", power: "1.1M", medal: "🥈" },
  { rank: 3, name: "PhantomX", level: 95, cls: "Assassin", power: "980K", medal: "🥉" },
  { rank: 4, name: "LightBringer", level: 93, cls: "Healer", power: "920K", medal: "" },
  { rank: 5, name: "DragonSlayer", level: 91, cls: "Warrior", power: "870K", medal: "" },
];

const commands = [
  { cmd: ".register", desc: "Daftar akun baru" },
  { cmd: ".quest", desc: "Untuk Mengerjakan Quest" },
  { cmd: ".dungeon", desc: "Masuk dungeon gate" },
  { cmd: ".wp | .armor", desc: "Lihat Inventory Perlengkapan" },
  { cmd: ".gacha", desc: "Summon equipment" },
  { cmd: ".profile", desc: "Lihat status karakter" },
];

const copied = ref(false);
function copyCmd() {
  navigator.clipboard.writeText(".register").then(() => {
    copied.value = true;
    setTimeout(() => (copied.value = false), 2000);
  });
}
function scrollTo(href) {
  isMobileMenuOpen.value = false;
  const el = document.querySelector(href);
  if (el) el.scrollIntoView({ behavior: "smooth", block: "start" });
}

/* ─── Lifecycle ─── */
onMounted(() => {
  window.addEventListener("scroll", handleScroll, { passive: true });
  window.addEventListener("mousemove", onMouseMove);
  handleScroll();
  startRotatingText();

  nextTick(() => {
    playIntro();
    // Setup all animations after DOM is ready
    setTimeout(() => {
      playHeroEntrance();
      setupScrollAnimations();
      setupParallax();
      setupMagneticButtons();
      setupCursorHover();
    }, 100);
  });
});

onUnmounted(() => {
  window.removeEventListener("scroll", handleScroll);
  window.removeEventListener("mousemove", onMouseMove);
  if (wordInterval) clearInterval(wordInterval);
  ScrollTrigger.getAll().forEach(t => t.kill());
});
</script>

<template>
  <!-- CUSTOM CURSOR -->
  <div class="custom-cursor hidden md:block" :class="{ hovering: cursorHovering }" :style="{ left: cursorX + 'px', top: cursorY + 'px' }"></div>
  <div class="custom-cursor-dot hidden md:block" :style="{ left: dotX + 'px', top: dotY + 'px' }"></div>

  <!-- LOADING SCREEN -->
  <div class="loading-screen" v-show="!isLoaded">
    <div class="loading-text text-2xl font-extrabold">
      <span class="bg-gradient-to-r from-purple-400 to-pink-400 bg-clip-text text-transparent">⚔️ Yurizaki RPG</span>
    </div>
    <div class="loading-bar-track">
      <div class="loading-bar-fill"></div>
    </div>
  </div>

  <!-- NAVBAR (morphing: full-width → rounded pill on scroll) -->
  <div class="fixed top-0 left-0 right-0 z-50 transition-all duration-500" :class="isScrolled ? 'px-3 pt-2 md:px-4 md:pt-3' : ''">
    <header
      class="mx-auto transition-all duration-500 backdrop-blur-xl"
      :class="[
        isScrolled
          ? 'max-w-full md:max-w-4xl rounded-xl md:rounded-2xl border border-purple-500/20 bg-[#0f0d1a]/95 shadow-lg shadow-purple-500/5'
          : 'max-w-full rounded-none border-b border-purple-900/30 bg-[#07060e]/80'
      ]"
    >
      <div class="flex items-center justify-between px-4 py-2.5 md:px-6 transition-all duration-500" :class="isScrolled ? 'py-2 md:py-2.5' : 'py-3 md:py-4'">
        <a href="#" class="nav-animate flex items-center gap-2 font-extrabold transition-all duration-300" :class="isScrolled ? 'text-xl' : 'text-2xl'">
          <span class="transition-all duration-300" :class="isScrolled ? 'text-2xl' : 'text-3xl'">⚔️</span>
          <span class="bg-gradient-to-r from-purple-400 to-pink-400 bg-clip-text text-transparent">Yurizaki</span>
          <span class="text-slate-500 font-medium transition-all duration-300" :class="isScrolled ? 'text-sm' : 'text-lg'">RPG</span>
        </a>
        <nav class="hidden md:flex items-center gap-6">
          <a v-for="l in navLinks" :key="l.label" :href="l.href" @click.prevent="scrollTo(l.href)"
            class="nav-animate text-sm font-medium text-slate-400 hover:text-white transition-all duration-300"
          >{{ l.label }}</a>
        </nav>
        <div class="hidden md:flex items-center gap-4">
          <a href="#" class="nav-animate magnetic-btn rounded-lg bg-gradient-to-r from-purple-600 to-pink-600 px-5 py-2 text-sm font-semibold text-white shadow-lg shadow-purple-500/25 hover:shadow-purple-500/40 transition-all duration-300">Mulai Main</a>
        </div>
        <button @click="isMobileMenuOpen = !isMobileMenuOpen" class="flex flex-col gap-1.5 md:hidden">
          <span class="block h-0.5 w-6 rounded bg-slate-400 transition-all duration-300" :class="isMobileMenuOpen ? 'translate-y-2 rotate-45' : ''"></span>
          <span class="block h-0.5 w-6 rounded bg-slate-400 transition-all duration-300" :class="isMobileMenuOpen ? 'opacity-0' : ''"></span>
          <span class="block h-0.5 w-6 rounded bg-slate-400 transition-all duration-300" :class="isMobileMenuOpen ? '-translate-y-2 -rotate-45' : ''"></span>
        </button>
      </div>
      <transition enter-active-class="transition duration-300 ease-out" enter-from-class="-translate-y-4 opacity-0" enter-to-class="translate-y-0 opacity-100" leave-active-class="transition duration-200 ease-in" leave-from-class="translate-y-0 opacity-100" leave-to-class="-translate-y-4 opacity-0">
        <div v-if="isMobileMenuOpen" class="border-t border-purple-900/30 bg-[#07060e]/95 backdrop-blur-xl md:hidden" :class="isScrolled ? 'rounded-b-2xl' : ''">
          <nav class="flex flex-col gap-1 px-6 py-4">
            <a v-for="l in navLinks" :key="l.label" :href="l.href" @click.prevent="scrollTo(l.href)" class="rounded-lg px-4 py-3 text-sm font-medium text-slate-300 hover:bg-purple-900/20 hover:text-white transition-colors">{{ l.label }}</a>
            <a href="#" class="mt-2 rounded-lg bg-gradient-to-r from-purple-600 to-pink-600 px-4 py-3 text-center text-sm font-semibold text-white">Mulai Main</a>
          </nav>
        </div>
      </transition>
    </header>
  </div>

  <!-- HERO -->
  <section class="relative overflow-hidden min-h-screen flex items-center justify-center pt-20 pb-10 md:pt-24 md:pb-16">
    <div class="pointer-events-none absolute inset-0">
      <div class="parallax-blob absolute left-1/2 top-0 -translate-x-1/2 h-[700px] w-[900px] rounded-full bg-purple-600/10 blur-[150px]" data-speed="0.3"></div>
      <div class="parallax-blob absolute right-0 top-1/4 h-[300px] w-[300px] rounded-full bg-pink-600/8 blur-[100px]" data-speed="0.6"></div>
    </div>
    <div class="relative mx-auto max-w-5xl px-6 text-center">
      <div class="hero-badge mx-auto mb-8 inline-flex items-center gap-2 rounded-full border border-purple-500/20 bg-purple-500/10 px-4 py-1.5 text-xs font-medium text-purple-400">
        <span class="relative flex h-2 w-2"><span class="absolute inline-flex h-full w-full animate-ping rounded-full bg-purple-400 opacity-75"></span><span class="relative inline-flex h-2 w-2 rounded-full bg-purple-500"></span></span>
        🎮 Season 4 Update — New Dungeon &amp; Boss!
      </div>
      <h1 class="text-4xl font-extrabold leading-tight tracking-tight text-white sm:text-5xl md:text-6xl lg:text-7xl">
        <span class="hero-title-line block">Bot WhatsApp</span>
        <span class="hero-title-line block relative h-[1.2em] overflow-hidden">
          <span
            class="absolute inset-0 bg-gradient-to-r from-purple-400 via-pink-400 to-amber-400 bg-clip-text text-transparent transition-all duration-500 ease-out"
            :class="textVisible ? 'translate-y-0 opacity-100' : 'translate-y-6 opacity-0'"
          >{{ rotatingWords[currentWordIndex] }}</span>
        </span>
      </h1>
      <p class="hero-desc mx-auto mt-6 max-w-2xl text-base leading-relaxed text-slate-400 sm:text-lg">
        Jelajahi dungeon, kalahkan monster, kumpulkan item legendaris, dan jadilah hunter terkuat — semuanya langsung dari WhatsApp! 🗡️
      </p>
      <div class="mt-8 md:mt-10 flex flex-col items-center justify-center gap-3 sm:flex-row sm:gap-4">
        <a href="#" class="hero-cta magnetic-btn group relative inline-flex items-center justify-center gap-2 rounded-xl bg-gradient-to-r from-purple-600 to-pink-600 w-full sm:w-auto px-6 py-3.5 sm:px-8 sm:py-4 text-sm sm:text-base font-semibold text-white shadow-xl shadow-purple-500/25 hover:shadow-purple-500/40 transition-all animate-pulse-glow">
          ⚔️ Mulai Petualangan
        </a>
        <a href="#gameplay" @click.prevent="scrollTo('#gameplay')" class="hero-cta magnetic-btn inline-flex items-center justify-center gap-2 rounded-xl border border-slate-700 w-full sm:w-auto px-6 py-3.5 sm:px-8 sm:py-4 text-sm sm:text-base font-semibold text-slate-300 hover:border-purple-500/50 hover:text-white transition-all">
          📖 Lihat Gameplay
        </a>
      </div>
      <!-- Stats -->
      <div class="mx-auto mt-10 md:mt-16 grid max-w-3xl grid-cols-4 gap-4 sm:gap-8">
        <div v-for="s in [
          { v: '50K+', l: 'Players' },
          { v: '100', l: 'Floors' },
          { v: '500+', l: 'Items' },
          { v: '24/7', l: 'Online' },
        ]" :key="s.l" class="hero-stat text-center">
          <div class="text-xl font-extrabold text-white sm:text-2xl md:text-3xl">{{ s.v }}</div>
          <div class="mt-0.5 text-[10px] font-medium text-slate-500 sm:text-xs md:text-sm">{{ s.l }}</div>
        </div>
      </div>
    </div>
  </section>

  <!-- GAMEPLAY FEATURES -->
  <section id="gameplay" class="relative py-24">
    <div class="mx-auto max-w-7xl px-6">
      <div class="gsap-section-header mx-auto max-w-2xl text-center">
        <span class="text-sm font-semibold uppercase tracking-widest text-purple-400">Gameplay</span>
        <h2 class="mt-3 text-3xl font-extrabold tracking-tight text-white sm:text-4xl">
          Dunia RPG di <span class="bg-gradient-to-r from-purple-400 to-pink-400 bg-clip-text text-transparent">Genggaman Tangan</span>
        </h2>
        <p class="mt-4 text-slate-400">Ratusan fitur RPG lengkap yang bisa kamu mainkan langsung di chat WhatsApp.</p>
      </div>
      <div class="gsap-card-grid mt-16 grid gap-6 sm:grid-cols-2 lg:grid-cols-3">
        <div v-for="f in gameplayFeatures" :key="f.title" class="gsap-card group rounded-2xl border border-purple-900/30 bg-[#0f0d1a]/80 p-6 backdrop-blur-sm transition-all duration-300 hover:-translate-y-1 hover:border-purple-500/30 hover:shadow-xl hover:shadow-purple-500/5">
          <div class="flex items-center justify-between mb-4">
            <div class="flex h-12 w-12 items-center justify-center rounded-xl bg-purple-500/10 text-2xl group-hover:bg-purple-500/20 transition-colors">
              <span v-if="f.icon === 'sword'">⚔️</span>
              <span v-else-if="f.icon === 'shield'">🛡️</span>
              <span v-else-if="f.icon === 'gem'">💎</span>
              <span v-else-if="f.icon === 'users'">👥</span>
              <span v-else-if="f.icon === 'star'">🐉</span>
              <span v-else>⚡</span>
            </div>
            <span class="rounded-full bg-purple-500/10 px-3 py-1 text-xs font-bold text-purple-400">{{ f.stat }}</span>
          </div>
          <h3 class="text-lg font-bold text-white">{{ f.title }}</h3>
          <p class="mt-2 text-sm leading-relaxed text-slate-400">{{ f.desc }}</p>
        </div>
      </div>
    </div>
  </section>

  <!-- FITUR / COMMAND PREVIEW -->
  <section id="fitur" class="relative py-24">
    <div class="mx-auto max-w-7xl px-6">
      <div class="grid items-center gap-12 lg:grid-cols-2">
        <div>
          <div class="gsap-section-header">
            <span class="text-sm font-semibold uppercase tracking-widest text-purple-400">Mudah Dimainkan</span>
            <h2 class="mt-3 text-3xl font-extrabold leading-tight text-white sm:text-4xl">
              Ketik Command, <span class="bg-gradient-to-r from-purple-400 to-pink-400 bg-clip-text text-transparent">Mulai Bertualang</span>
            </h2>
            <p class="mt-4 text-base leading-relaxed text-slate-400">Cukup kirim command di chat WhatsApp untuk memulai. Tanpa download aplikasi, tanpa registrasi ribet.</p>
          </div>
          <div class="mt-8 space-y-3">
            <div v-for="c in commands" :key="c.cmd" class="gsap-cmd-item flex items-center gap-4 rounded-xl border border-purple-900/30 bg-[#0f0d1a] px-4 py-3 transition-all hover:border-purple-500/30">
              <code class="rounded-md bg-purple-500/10 px-3 py-1 text-sm font-bold text-purple-400">{{ c.cmd }}</code>
              <span class="text-sm text-slate-400">{{ c.desc }}</span>
            </div>
          </div>
        </div>
        <!-- Chat mockup -->
        <div class="gsap-chat-mockup relative overflow-hidden">
          <div class="overflow-hidden rounded-2xl border border-purple-900/30 bg-[#0f0d1a] shadow-2xl">
            <div class="flex items-center gap-3 border-b border-purple-900/30 bg-[#1a1528] px-4 py-3">
              <div class="h-10 w-10 rounded-full bg-gradient-to-br from-purple-500 to-pink-500 flex items-center justify-center text-lg">⚔️</div>
              <div><div class="text-sm font-bold text-white">Yurizaki RPG</div><div class="text-xs text-emerald-400">● Online</div></div>
              <button @click="copyCmd" class="ml-auto flex items-center gap-1.5 rounded-md border border-purple-800/50 px-3 py-1 text-xs font-medium text-slate-400 hover:border-purple-500/50 hover:text-purple-400 transition-all">
                {{ copied ? "✅ Copied!" : "📋 Copy" }}
              </button>
            </div>
            <div class="p-4 space-y-3 text-sm">
              <div class="gsap-chat-msg flex justify-end"><div class="rounded-2xl rounded-tr-sm bg-purple-600/30 px-4 py-2 text-purple-200 max-w-[70%]">.register</div></div>
              <div class="gsap-chat-msg flex justify-start"><div class="rounded-2xl rounded-tl-sm bg-[#1a1528] border border-purple-900/30 px-4 py-3 max-w-[85%] space-y-2">
                <p class="text-emerald-400 font-bold">✅ Registrasi Berhasil!</p>
                <p class="text-slate-300">Selamat datang, Hunter! 🎮</p>
                <div class="bg-[#0f0d1a] rounded-lg p-3 space-y-1 text-xs">
                  <div class="flex justify-between"><span class="text-slate-500">Nama</span><span class="text-white font-medium">NewPlayer</span></div>
                  <div class="flex justify-between"><span class="text-slate-500">Level</span><span class="text-amber-400 font-bold">1</span></div>
                  <div class="flex justify-between"><span class="text-slate-500">Kelas</span><span class="text-purple-400">— Belum Dipilih —</span></div>
                  <div class="flex justify-between"><span class="text-slate-500">HP</span><span class="text-red-400">100/100</span></div>
                  <div class="flex justify-between"><span class="text-slate-500">Gold</span><span class="text-amber-400">500 🪙</span></div>
                </div>
                <p class="text-slate-500 text-xs">Ketik <code class="text-purple-400">.class</code> untuk memilih kelas.</p>
              </div></div>
              <div class="gsap-chat-msg flex justify-end"><div class="rounded-2xl rounded-tr-sm bg-purple-600/30 px-4 py-2 text-purple-200 max-w-[70%]">.hunt</div></div>
              <div class="gsap-chat-msg flex justify-start"><div class="rounded-2xl rounded-tl-sm bg-[#1a1528] border border-purple-900/30 px-4 py-3 max-w-[85%] space-y-2">
                <p class="text-amber-400 font-bold">⚔️ Pertarungan Dimulai!</p>
                <p class="text-slate-300">Kamu menemukan <span class="text-red-400 font-bold">Goblin Warrior Lv.5</span></p>
                <div class="bg-[#0f0d1a] rounded-lg p-3 text-xs space-y-2">
                  <div><span class="text-slate-500">Monster HP</span><div class="mt-1 h-2 rounded-full bg-slate-800 overflow-hidden"><div class="h-full w-[60%] rounded-full bg-gradient-to-r from-red-500 to-red-400"></div></div></div>
                  <div class="text-emerald-400">✅ Kamu menyerang: <span class="text-white font-bold">-45 DMG</span></div>
                  <div class="text-red-400">💥 Monster menyerang: <span class="text-white font-bold">-12 DMG</span></div>
                </div>
                <p class="text-emerald-400 text-xs font-bold">🎉 Victory! +50 EXP, +25 Gold, Goblin Tooth x1</p>
              </div></div>
            </div>
          </div>
          <div class="pointer-events-none absolute -inset-4 -z-10 rounded-3xl bg-purple-500/5 blur-2xl"></div>
        </div>
      </div>
    </div>
  </section>

  <!-- KELAS -->
  <section id="kelas" class="relative py-24">
    <div class="mx-auto max-w-7xl px-6">
      <div class="gsap-section-header mx-auto max-w-2xl text-center">
        <span class="text-sm font-semibold uppercase tracking-widest text-purple-400">Pilih Kelasmu</span>
        <h2 class="mt-3 text-3xl font-extrabold tracking-tight text-white sm:text-4xl">
          4 Kelas dengan <span class="bg-gradient-to-r from-purple-400 to-pink-400 bg-clip-text text-transparent">Gaya Bertarung Unik</span>
        </h2>
      </div>
      <div class="mt-16 grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
        <div v-for="c in classes" :key="c.name" class="gsap-class-card group rounded-2xl border border-purple-900/30 bg-[#0f0d1a]/80 p-6 text-center transition-all duration-300 hover:-translate-y-2 hover:border-purple-500/30 hover:shadow-xl hover:shadow-purple-500/5">
          <div class="mx-auto mb-4 flex h-20 w-20 items-center justify-center rounded-2xl text-5xl animate-float" :class="`bg-gradient-to-br ${c.color} bg-opacity-10`" style="background: rgba(139, 92, 246, 0.08)">{{ c.emoji }}</div>
          <h3 class="text-xl font-extrabold text-white">{{ c.name }}</h3>
          <p class="mt-2 text-xs text-slate-400">{{ c.desc }}</p>
          <div class="mt-5 space-y-3 text-left">
            <div v-for="st in [{ l: 'HP', v: c.hp, cl: 'bg-red-500' }, { l: 'ATK', v: c.atk, cl: 'bg-amber-500' }, { l: 'DEF', v: c.def, cl: 'bg-blue-500' }]" :key="st.l">
              <div class="flex justify-between text-xs mb-1"><span class="text-slate-500">{{ st.l }}</span><span class="text-slate-400 font-bold">{{ st.v }}</span></div>
              <div class="h-1.5 rounded-full bg-slate-800 overflow-hidden">
                <div class="gsap-stat-bar h-full rounded-full" :class="st.cl" :data-width="st.v" style="width: 0%"></div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
  <!-- LEADERBOARD -->
  <section id="leaderboard" class="relative py-16 md:py-24">
    <div class="mx-auto max-w-4xl px-4 md:px-6">
      <div class="gsap-section-header mx-auto max-w-2xl text-center">
        <span class="text-sm font-semibold uppercase tracking-widest text-purple-400">Leaderboard</span>
        <h2 class="mt-3 text-2xl font-extrabold tracking-tight text-white sm:text-3xl md:text-4xl">
          Top <span class="bg-gradient-to-r from-amber-400 to-yellow-400 bg-clip-text text-transparent">Hunters</span> 🏆
        </h2>
      </div>
      <div class="mt-8 md:mt-12 overflow-hidden rounded-xl md:rounded-2xl border border-purple-900/30 bg-[#0f0d1a]/80 backdrop-blur-sm">
        <div class="grid grid-cols-3 sm:grid-cols-5 gap-2 sm:gap-4 border-b border-purple-900/30 px-4 sm:px-6 py-2.5 sm:py-3 text-[10px] sm:text-xs font-bold uppercase tracking-wider text-slate-500">
          <span>Rank</span><span>Player</span><span class="hidden sm:block">Level</span><span class="hidden sm:block">Class</span><span class="text-right">Power</span>
        </div>
        <div v-for="p in leaderboard" :key="p.rank"
          class="gsap-lb-row grid grid-cols-3 sm:grid-cols-5 gap-2 sm:gap-4 items-center px-4 sm:px-6 py-3 sm:py-4 border-b border-purple-900/20 transition-colors hover:bg-purple-900/10"
          :class="p.rank <= 3 ? 'bg-purple-900/5' : ''"
        >
          <span class="text-base sm:text-lg font-extrabold" :class="p.rank === 1 ? 'text-amber-400' : p.rank === 2 ? 'text-slate-300' : p.rank === 3 ? 'text-amber-600' : 'text-slate-500'">{{ p.medal || "#" + p.rank }}</span>
          <span class="font-bold text-white text-xs sm:text-sm truncate">{{ p.name }}</span>
          <span class="hidden sm:block text-amber-400 font-bold text-sm">Lv.{{ p.level }}</span>
          <span class="hidden sm:block text-purple-400 text-sm">{{ p.cls }}</span>
          <span class="text-right text-xs sm:text-sm font-bold text-emerald-400">{{ p.power }}</span>
        </div>
      </div>
    </div>
  </section>

  <!-- CTA -->
  <section class="relative py-24">
    <div class="mx-auto max-w-4xl px-6 text-center">
      <div class="gsap-cta-box relative overflow-hidden rounded-3xl border border-purple-900/30 bg-[#0f0d1a]/80 px-8 py-16 backdrop-blur-sm sm:px-16">
        <div class="pointer-events-none absolute inset-0">
          <div class="parallax-blob absolute left-1/2 top-0 -translate-x-1/2 h-[300px] w-[500px] rounded-full bg-purple-500/10 blur-[100px]" data-speed="0.4"></div>
        </div>
        <div class="relative">
          <div class="text-6xl mb-6 animate-float">⚔️</div>
          <h2 class="text-3xl font-extrabold tracking-tight text-white sm:text-4xl">
            Siap Menjadi <span class="bg-gradient-to-r from-purple-400 to-pink-400 bg-clip-text text-transparent">Hunter Terkuat?</span>
          </h2>
          <p class="mx-auto mt-4 max-w-xl text-slate-400">Bergabung dengan 50.000+ player yang sudah bertualang di dunia Yurizaki RPG. Gratis, tanpa download!</p>
          <a href="#" class="magnetic-btn mt-8 inline-flex items-center gap-2 rounded-xl bg-gradient-to-r from-purple-600 to-pink-600 px-8 py-4 text-base font-semibold text-white shadow-xl shadow-purple-500/25 hover:shadow-purple-500/40 transition-all">
            🚀 Mulai Main Sekarang
          </a>
        </div>
      </div>
    </div>
  </section>

  <!-- FOOTER -->
  <footer class="border-t border-purple-900/20 bg-[#050510]">
    <div class="mx-auto max-w-7xl px-6 py-12">
      <div class="flex flex-col items-center justify-between gap-6 sm:flex-row">
        <a href="#" class="flex items-center gap-2 text-xl font-extrabold">
          <span class="text-2xl">⚔️</span>
          <span class="bg-gradient-to-r from-purple-400 to-pink-400 bg-clip-text text-transparent">Yurizaki</span>
          <span class="text-slate-600 text-sm font-medium">RPG</span>
        </a>
        <div class="flex items-center gap-6">
          <a v-for="l in ['Gameplay', 'Fitur', 'Kelas', 'Kontak']" :key="l" href="#" class="text-sm text-slate-600 hover:text-slate-300 transition-colors">{{ l }}</a>
        </div>
      </div>
      <div class="mt-8 border-t border-purple-900/20 pt-6 text-center">
        <p class="text-sm text-slate-700">&copy; 2025 Yurizaki RPG. All rights reserved. Made with ❤️</p>
      </div>
    </div>
  </footer>
</template>

<style scoped></style>
