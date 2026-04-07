<script setup>
import { ref, onMounted, onUnmounted, nextTick } from "vue";

/* ─── State ─── */
const isMobileMenuOpen = ref(false);
const isScrolled = ref(false);
const navVisible = ref(false);

/* ─── Rotating Hero Text ─── */
const rotatingWords = [
  "RPG Adventure",
  "Dungeon Hunter",
  "Guild Master",
  "Boss Slayer",
  "Epic Loot",
];
const currentWordIndex = ref(0);
const textVisible = ref(true);
let wordInterval = null;

function startRotatingText() {
  wordInterval = setInterval(() => {
    textVisible.value = false;
    setTimeout(() => {
      currentWordIndex.value =
        (currentWordIndex.value + 1) % rotatingWords.length;
      textVisible.value = true;
    }, 400);
  }, 3000);
}

/* ─── Scroll Handler for Navbar Morph ─── */
function handleScroll() {
  isScrolled.value = window.scrollY > 50;
}

/* ─── Scroll Reveal via IntersectionObserver ─── */
let observer = null;
function setupScrollReveal() {
  observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          entry.target.classList.add("revealed");
        }
      });
    },
    { threshold: 0.1, rootMargin: "0px 0px -60px 0px" },
  );
  document
    .querySelectorAll(".reveal-on-scroll")
    .forEach((el) => observer.observe(el));
}

/* ─── Lifecycle ─── */
onMounted(() => {
  window.addEventListener("scroll", handleScroll, { passive: true });
  handleScroll();
  startRotatingText();
  // Stagger nav item entrance
  setTimeout(() => {
    navVisible.value = true;
  }, 100);
  // Setup scroll reveal after DOM renders
  nextTick(() => {
    setTimeout(setupScrollReveal, 200);
  });
});

onUnmounted(() => {
  window.removeEventListener("scroll", handleScroll);
  if (wordInterval) clearInterval(wordInterval);
  if (observer) observer.disconnect();
});

/* ─── Nav Links ─── */
const navLinks = [
  { label: "Gameplay", href: "#gameplay" },
  { label: "Fitur", href: "#fitur" },
  { label: "Kelas", href: "#kelas" },
  { label: "Leaderboard", href: "#leaderboard" },
];

const gameplayFeatures = [
  {
    icon: "sword",
    title: "Dungeon Gate",
    desc: "Masuki dungeon berlantai dengan monster yang semakin kuat. Kalahkan boss di setiap 10 lantai untuk mendapatkan loot legendaris.",
    stat: "100 Lantai",
  },
  {
    icon: "shield",
    title: "Sistem Battle",
    desc: "Pertarungan turn-based dengan skill combo, elemental weakness, dan critical hit system yang mendebarkan.",
    stat: "PvE & PvP",
  },
  {
    icon: "gem",
    title: "Gacha & Craft",
    desc: "Summon equipment dari banner gacha atau craft langsung dari material dungeon. Ratusan item unik tersedia.",
    stat: "500+ Items",
  },
  {
    icon: "users",
    title: "Guild War",
    desc: "Bentuk guild bersama teman, serang guild lawan, dan rebut territory untuk bonus EXP dan Gold harian.",
    stat: "Real-time",
  },
  {
    icon: "star",
    title: "Schenario System",
    desc: "Memasuki dunia Dungeon gate Yurizaki dengan cerita yang menarik dan penuh petualangan dan misteri.",
    stat: "50+ Schenario",
  },
  {
    icon: "zap",
    title: "Daily Quest",
    desc: "Selesaikan misi harian untuk mendapatkan reward eksklusif, termasuk summon ticket dan rare materials.",
    stat: "Auto Reset",
  },
];

const classes = [
  {
    name: "Warrior",
    emoji: "⚔️",
    color: "from-red-500 to-orange-500",
    hp: 95,
    atk: 80,
    def: 90,
    desc: "Tank & DPS dengan armor tebal dan serangan brutal.",
  },
  {
    name: "Mage",
    emoji: "🔮",
    color: "from-violet-500 to-blue-500",
    hp: 60,
    atk: 95,
    def: 50,
    desc: "Master sihir dengan damage area yang menghancurkan.",
  },
  {
    name: "Assassin",
    emoji: "🗡️",
    color: "from-emerald-500 to-cyan-500",
    hp: 70,
    atk: 90,
    def: 55,
    desc: "Silent killer dengan critical rate dan evasion tinggi.",
  },
  {
    name: "Healer",
    emoji: "✨",
    color: "from-amber-400 to-yellow-500",
    hp: 75,
    atk: 40,
    def: 70,
    desc: "Support terbaik dengan skill heal dan buff party.",
  },
];

const leaderboard = [
  {
    rank: 1,
    name: "ShadowKing",
    level: 99,
    cls: "Warrior",
    power: "1.2M",
    medal: "🥇",
  },
  {
    rank: 2,
    name: "ArcMage",
    level: 97,
    cls: "Mage",
    power: "1.1M",
    medal: "🥈",
  },
  {
    rank: 3,
    name: "PhantomX",
    level: 95,
    cls: "Assassin",
    power: "980K",
    medal: "🥉",
  },
  {
    rank: 4,
    name: "LightBringer",
    level: 93,
    cls: "Healer",
    power: "920K",
    medal: "",
  },
  {
    rank: 5,
    name: "DragonSlayer",
    level: 91,
    cls: "Warrior",
    power: "870K",
    medal: "",
  },
];

const commands = [
  { cmd: ".register", desc: "Daftar akun baru" },
  { cmd: ".hunt", desc: "Berburu monster" },
  { cmd: ".dungeon", desc: "Masuk dungeon gate" },
  { cmd: ".inventory", desc: "Lihat item" },
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
  document.querySelector(href)?.scrollIntoView({ behavior: "smooth" });
}
</script>

<template>
  <!-- NAVBAR (morphing: full-width → rounded pill on scroll) -->
  <div
    class="fixed top-0 left-0 right-0 z-50 transition-all duration-500"
    :class="isScrolled ? 'px-4 pt-3' : 'px-0 pt-0'"
  >
    <header
      class="mx-auto transition-all duration-500 backdrop-blur-xl"
      :class="[
        isScrolled
          ? 'max-w-4xl rounded-2xl border border-purple-500/20 bg-[#0f0d1a]/90 shadow-lg shadow-purple-500/5'
          : 'max-w-full rounded-none border-b border-purple-900/30 bg-[#07060e]/80',
      ]"
    >
      <div
        class="flex items-center justify-between px-6 py-3 transition-all duration-500"
        :class="isScrolled ? 'py-2.5' : 'py-4'"
      >
        <a
          href="#"
          class="flex items-center gap-2 font-extrabold transition-all duration-300"
          :class="isScrolled ? 'text-xl' : 'text-2xl'"
        >
          <span
            class="transition-all duration-300"
            :class="isScrolled ? 'text-2xl' : 'text-3xl'"
            >⚔️</span
          >
          <span
            class="bg-gradient-to-r from-purple-400 to-pink-400 bg-clip-text text-transparent"
            >Yurizaki</span
          >
          <span
            class="text-slate-500 font-medium transition-all duration-300"
            :class="isScrolled ? 'text-sm' : 'text-lg'"
            >RPG</span
          >
        </a>
        <nav class="hidden md:flex items-center gap-6">
          <a
            v-for="(l, i) in navLinks"
            :key="l.label"
            :href="l.href"
            @click.prevent="scrollTo(l.href)"
            class="nav-item text-sm font-medium text-slate-400 hover:text-white transition-all duration-300"
            :class="navVisible ? 'nav-item-visible' : ''"
            :style="{ transitionDelay: navVisible ? i * 80 + 'ms' : '0ms' }"
            >{{ l.label }}</a
          >
        </nav>
        <div class="hidden md:flex items-center gap-4">
          <a
            href="#"
            class="nav-item rounded-lg bg-gradient-to-r from-purple-600 to-pink-600 px-5 py-2 text-sm font-semibold text-white shadow-lg shadow-purple-500/25 hover:shadow-purple-500/40 transition-all duration-300"
            :class="navVisible ? 'nav-item-visible' : ''"
            :style="{ transitionDelay: navVisible ? '320ms' : '0ms' }"
            >Mulai Main</a
          >
        </div>
        <button
          @click="isMobileMenuOpen = !isMobileMenuOpen"
          class="flex flex-col gap-1.5 md:hidden"
        >
          <span
            class="block h-0.5 w-6 rounded bg-slate-400 transition-all duration-300"
            :class="isMobileMenuOpen ? 'translate-y-2 rotate-45' : ''"
          ></span>
          <span
            class="block h-0.5 w-6 rounded bg-slate-400 transition-all duration-300"
            :class="isMobileMenuOpen ? 'opacity-0' : ''"
          ></span>
          <span
            class="block h-0.5 w-6 rounded bg-slate-400 transition-all duration-300"
            :class="isMobileMenuOpen ? '-translate-y-2 -rotate-45' : ''"
          ></span>
        </button>
      </div>
      <transition
        enter-active-class="transition duration-300 ease-out"
        enter-from-class="-translate-y-4 opacity-0"
        enter-to-class="translate-y-0 opacity-100"
        leave-active-class="transition duration-200 ease-in"
        leave-from-class="translate-y-0 opacity-100"
        leave-to-class="-translate-y-4 opacity-0"
      >
        <div
          v-if="isMobileMenuOpen"
          class="border-t border-purple-900/30 bg-[#07060e]/95 backdrop-blur-xl md:hidden"
          :class="isScrolled ? 'rounded-b-2xl' : ''"
        >
          <nav class="flex flex-col gap-1 px-6 py-4">
            <a
              v-for="l in navLinks"
              :key="l.label"
              :href="l.href"
              @click.prevent="scrollTo(l.href)"
              class="rounded-lg px-4 py-3 text-sm font-medium text-slate-300 hover:bg-purple-900/20 hover:text-white transition-colors"
              >{{ l.label }}</a
            >
            <a
              href="#"
              class="mt-2 rounded-lg bg-gradient-to-r from-purple-600 to-pink-600 px-4 py-3 text-center text-sm font-semibold text-white"
              >Mulai Main</a
            >
          </nav>
        </div>
      </transition>
    </header>
  </div>

  <!-- HERO -->
  <section class="relative overflow-hidden pt-32 pb-20 md:pt-44 md:pb-32">
    <div class="pointer-events-none absolute inset-0">
      <div
        class="absolute left-1/2 top-0 -translate-x-1/2 h-[700px] w-[900px] rounded-full bg-purple-600/10 blur-[150px]"
      ></div>
      <div
        class="absolute right-0 top-1/4 h-[300px] w-[300px] rounded-full bg-pink-600/8 blur-[100px]"
      ></div>
    </div>
    <div class="relative mx-auto max-w-5xl px-6 text-center">
      <div
        class="mx-auto mb-8 inline-flex items-center gap-2 rounded-full border border-purple-500/20 bg-purple-500/10 px-4 py-1.5 text-xs font-medium text-purple-400"
      >
        <span class="relative flex h-2 w-2"
          ><span
            class="absolute inline-flex h-full w-full animate-ping rounded-full bg-purple-400 opacity-75"
          ></span
          ><span
            class="relative inline-flex h-2 w-2 rounded-full bg-purple-500"
          ></span
        ></span>
        🎮 Season 4 Update — New Dungeon & Boss!
      </div>
      <h1
        class="text-4xl font-extrabold leading-tight tracking-tight text-white sm:text-5xl md:text-6xl lg:text-7xl"
      >
        Bot WhatsApp
        <span class="block relative h-[1.2em] overflow-hidden">
          <span
            class="absolute inset-0 bg-gradient-to-r from-purple-400 via-pink-400 to-amber-400 bg-clip-text text-transparent transition-all duration-500 ease-out"
            :class="
              textVisible
                ? 'translate-y-0 opacity-100'
                : 'translate-y-6 opacity-0'
            "
            >{{ rotatingWords[currentWordIndex] }}</span
          >
        </span>
      </h1>
      <p
        class="mx-auto mt-6 max-w-2xl text-base leading-relaxed text-slate-400 sm:text-lg"
      >
        Jelajahi dungeon, kalahkan monster, kumpulkan item legendaris, dan
        jadilah hunter terkuat — semuanya langsung dari WhatsApp! 🗡️
      </p>
      <div
        class="mt-10 flex flex-col items-center justify-center gap-4 sm:flex-row"
      >
        <a
          href="#"
          class="group relative inline-flex items-center gap-2 rounded-xl bg-gradient-to-r from-purple-600 to-pink-600 px-8 py-4 text-base font-semibold text-white shadow-xl shadow-purple-500/25 hover:shadow-purple-500/40 transition-all animate-pulse-glow"
        >
          ⚔️ Mulai Petualangan
        </a>
        <a
          href="#gameplay"
          @click.prevent="scrollTo('#gameplay')"
          class="inline-flex items-center gap-2 rounded-xl border border-slate-700 px-8 py-4 text-base font-semibold text-slate-300 hover:border-purple-500/50 hover:text-white transition-all"
        >
          📖 Lihat Gameplay
        </a>
      </div>
      <!-- Stats -->
      <div
        class="mx-auto mt-16 grid max-w-3xl grid-cols-2 gap-8 sm:grid-cols-4"
      >
        <div
          v-for="s in [
            { v: '50K+', l: 'Players' },
            { v: '100', l: 'Dungeon Floors' },
            { v: '500+', l: 'Items' },
            { v: '24/7', l: 'Online' },
          ]"
          :key="s.l"
          class="text-center"
        >
          <div class="text-2xl font-extrabold text-white sm:text-3xl">
            {{ s.v }}
          </div>
          <div class="mt-1 text-xs font-medium text-slate-500 sm:text-sm">
            {{ s.l }}
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- GAMEPLAY FEATURES -->
  <section id="gameplay" class="reveal-on-scroll relative py-24">
    <div class="mx-auto max-w-7xl px-6">
      <div class="mx-auto max-w-2xl text-center">
        <span
          class="text-sm font-semibold uppercase tracking-widest text-purple-400"
          >Gameplay</span
        >
        <h2
          class="mt-3 text-3xl font-extrabold tracking-tight text-white sm:text-4xl"
        >
          Dunia RPG di
          <span
            class="bg-gradient-to-r from-purple-400 to-pink-400 bg-clip-text text-transparent"
            >Genggaman Tangan</span
          >
        </h2>
        <p class="mt-4 text-slate-400">
          Ratusan fitur RPG lengkap yang bisa kamu mainkan langsung di chat
          WhatsApp.
        </p>
      </div>
      <div class="mt-16 grid gap-6 sm:grid-cols-2 lg:grid-cols-3">
        <div
          v-for="f in gameplayFeatures"
          :key="f.title"
          class="group rounded-2xl border border-purple-900/30 bg-[#0f0d1a]/80 p-6 backdrop-blur-sm transition-all duration-300 hover:-translate-y-1 hover:border-purple-500/30 hover:shadow-xl hover:shadow-purple-500/5"
        >
          <div class="flex items-center justify-between mb-4">
            <div
              class="flex h-12 w-12 items-center justify-center rounded-xl bg-purple-500/10 text-2xl group-hover:bg-purple-500/20 transition-colors"
            >
              <span v-if="f.icon === 'sword'">⚔️</span>
              <span v-else-if="f.icon === 'shield'">🛡️</span>
              <span v-else-if="f.icon === 'gem'">💎</span>
              <span v-else-if="f.icon === 'users'">👥</span>
              <span v-else-if="f.icon === 'star'">🐉</span>
              <span v-else>⚡</span>
            </div>
            <span
              class="rounded-full bg-purple-500/10 px-3 py-1 text-xs font-bold text-purple-400"
              >{{ f.stat }}</span
            >
          </div>
          <h3 class="text-lg font-bold text-white">{{ f.title }}</h3>
          <p class="mt-2 text-sm leading-relaxed text-slate-400">
            {{ f.desc }}
          </p>
        </div>
      </div>
    </div>
  </section>

  <!-- FITUR / COMMAND PREVIEW -->
  <section id="fitur" class="reveal-on-scroll relative py-24">
    <div class="mx-auto max-w-7xl px-6">
      <div class="grid items-center gap-12 lg:grid-cols-2">
        <div>
          <span
            class="text-sm font-semibold uppercase tracking-widest text-purple-400"
            >Mudah Dimainkan</span
          >
          <h2
            class="mt-3 text-3xl font-extrabold leading-tight text-white sm:text-4xl"
          >
            Ketik Command,
            <span
              class="bg-gradient-to-r from-purple-400 to-pink-400 bg-clip-text text-transparent"
              >Mulai Bertualang</span
            >
          </h2>
          <p class="mt-4 text-base leading-relaxed text-slate-400">
            Cukup kirim command di chat WhatsApp untuk memulai. Tanpa download
            aplikasi, tanpa registrasi ribet.
          </p>
          <div class="mt-8 space-y-3">
            <div
              v-for="c in commands"
              :key="c.cmd"
              class="flex items-center gap-4 rounded-xl border border-purple-900/30 bg-[#0f0d1a] px-4 py-3 transition-all hover:border-purple-500/30"
            >
              <code
                class="rounded-md bg-purple-500/10 px-3 py-1 text-sm font-bold text-purple-400"
                >{{ c.cmd }}</code
              >
              <span class="text-sm text-slate-400">{{ c.desc }}</span>
            </div>
          </div>
        </div>
        <!-- Chat mockup -->
        <div class="relative">
          <div
            class="overflow-hidden rounded-2xl border border-purple-900/30 bg-[#0f0d1a] shadow-2xl"
          >
            <div
              class="flex items-center gap-3 border-b border-purple-900/30 bg-[#1a1528] px-4 py-3"
            >
              <div
                class="h-10 w-10 rounded-full bg-gradient-to-br from-purple-500 to-pink-500 flex items-center justify-center text-lg"
              >
                ⚔️
              </div>
              <div>
                <div class="text-sm font-bold text-white">Yurizaki RPG</div>
                <div class="text-xs text-emerald-400">● Online</div>
              </div>
              <button
                @click="copyCmd"
                class="ml-auto flex items-center gap-1.5 rounded-md border border-purple-800/50 px-3 py-1 text-xs font-medium text-slate-400 hover:border-purple-500/50 hover:text-purple-400 transition-all"
              >
                {{ copied ? "✅ Copied!" : "📋 Copy" }}
              </button>
            </div>
            <div class="p-4 space-y-3 text-sm">
              <!-- User msg -->
              <div class="flex justify-end">
                <div
                  class="rounded-2xl rounded-tr-sm bg-purple-600/30 px-4 py-2 text-purple-200 max-w-[70%]"
                >
                  .register
                </div>
              </div>
              <!-- Bot reply -->
              <div class="flex justify-start">
                <div
                  class="rounded-2xl rounded-tl-sm bg-[#1a1528] border border-purple-900/30 px-4 py-3 max-w-[85%] space-y-2"
                >
                  <p class="text-emerald-400 font-bold">
                    ✅ Registrasi Berhasil!
                  </p>
                  <p class="text-slate-300">Selamat datang, Hunter! 🎮</p>
                  <div class="bg-[#0f0d1a] rounded-lg p-3 space-y-1 text-xs">
                    <div class="flex justify-between">
                      <span class="text-slate-500">Nama</span
                      ><span class="text-white font-medium">NewPlayer</span>
                    </div>
                    <div class="flex justify-between">
                      <span class="text-slate-500">Level</span
                      ><span class="text-amber-400 font-bold">1</span>
                    </div>
                    <div class="flex justify-between">
                      <span class="text-slate-500">Kelas</span
                      ><span class="text-purple-400">— Belum Dipilih —</span>
                    </div>
                    <div class="flex justify-between">
                      <span class="text-slate-500">HP</span
                      ><span class="text-red-400">100/100</span>
                    </div>
                    <div class="flex justify-between">
                      <span class="text-slate-500">Gold</span
                      ><span class="text-amber-400">500 🪙</span>
                    </div>
                  </div>
                  <p class="text-slate-500 text-xs">
                    Ketik <code class="text-purple-400">.class</code> untuk
                    memilih kelas.
                  </p>
                </div>
              </div>
              <!-- User msg -->
              <div class="flex justify-end">
                <div
                  class="rounded-2xl rounded-tr-sm bg-purple-600/30 px-4 py-2 text-purple-200 max-w-[70%]"
                >
                  .hunt
                </div>
              </div>
              <!-- Bot reply -->
              <div class="flex justify-start">
                <div
                  class="rounded-2xl rounded-tl-sm bg-[#1a1528] border border-purple-900/30 px-4 py-3 max-w-[85%] space-y-2"
                >
                  <p class="text-amber-400 font-bold">
                    ⚔️ Pertarungan Dimulai!
                  </p>
                  <p class="text-slate-300">
                    Kamu menemukan
                    <span class="text-red-400 font-bold"
                      >Goblin Warrior Lv.5</span
                    >
                  </p>
                  <div class="bg-[#0f0d1a] rounded-lg p-3 text-xs space-y-2">
                    <div>
                      <span class="text-slate-500">Monster HP</span>
                      <div
                        class="mt-1 h-2 rounded-full bg-slate-800 overflow-hidden"
                      >
                        <div
                          class="h-full w-[60%] rounded-full bg-gradient-to-r from-red-500 to-red-400"
                        ></div>
                      </div>
                    </div>
                    <div class="text-emerald-400">
                      ✅ Kamu menyerang:
                      <span class="text-white font-bold">-45 DMG</span>
                    </div>
                    <div class="text-red-400">
                      💥 Monster menyerang:
                      <span class="text-white font-bold">-12 DMG</span>
                    </div>
                  </div>
                  <p class="text-emerald-400 text-xs font-bold">
                    🎉 Victory! +50 EXP, +25 Gold, Goblin Tooth x1
                  </p>
                </div>
              </div>
            </div>
          </div>
          <div
            class="pointer-events-none absolute -inset-4 -z-10 rounded-3xl bg-purple-500/5 blur-2xl"
          ></div>
        </div>
      </div>
    </div>
  </section>

  <!-- KELAS -->
  <section id="kelas" class="reveal-on-scroll relative py-24">
    <div class="mx-auto max-w-7xl px-6">
      <div class="mx-auto max-w-2xl text-center">
        <span
          class="text-sm font-semibold uppercase tracking-widest text-purple-400"
          >Pilih Kelasmu</span
        >
        <h2
          class="mt-3 text-3xl font-extrabold tracking-tight text-white sm:text-4xl"
        >
          4 Kelas dengan
          <span
            class="bg-gradient-to-r from-purple-400 to-pink-400 bg-clip-text text-transparent"
            >Gaya Bertarung Unik</span
          >
        </h2>
      </div>
      <div class="mt-16 grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
        <div
          v-for="c in classes"
          :key="c.name"
          class="group rounded-2xl border border-purple-900/30 bg-[#0f0d1a]/80 p-6 text-center transition-all duration-300 hover:-translate-y-2 hover:border-purple-500/30 hover:shadow-xl hover:shadow-purple-500/5"
        >
          <div
            class="mx-auto mb-4 flex h-20 w-20 items-center justify-center rounded-2xl text-5xl animate-float"
            :class="`bg-gradient-to-br ${c.color} bg-opacity-10`"
            style="background: rgba(139, 92, 246, 0.08)"
          >
            {{ c.emoji }}
          </div>
          <h3 class="text-xl font-extrabold text-white">{{ c.name }}</h3>
          <p class="mt-2 text-xs text-slate-400">{{ c.desc }}</p>
          <!-- Stat bars -->
          <div class="mt-5 space-y-3 text-left">
            <div
              v-for="st in [
                { l: 'HP', v: c.hp, cl: 'bg-red-500' },
                { l: 'ATK', v: c.atk, cl: 'bg-amber-500' },
                { l: 'DEF', v: c.def, cl: 'bg-blue-500' },
              ]"
              :key="st.l"
            >
              <div class="flex justify-between text-xs mb-1">
                <span class="text-slate-500">{{ st.l }}</span
                ><span class="text-slate-400 font-bold">{{ st.v }}</span>
              </div>
              <div class="h-1.5 rounded-full bg-slate-800 overflow-hidden">
                <div
                  class="h-full rounded-full transition-all duration-500"
                  :class="st.cl"
                  :style="{ width: st.v + '%' }"
                ></div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- LEADERBOARD -->
  <section id="leaderboard" class="reveal-on-scroll relative py-24">
    <div class="mx-auto max-w-4xl px-6">
      <div class="mx-auto max-w-2xl text-center">
        <span
          class="text-sm font-semibold uppercase tracking-widest text-purple-400"
          >Leaderboard</span
        >
        <h2
          class="mt-3 text-3xl font-extrabold tracking-tight text-white sm:text-4xl"
        >
          Top
          <span
            class="bg-gradient-to-r from-amber-400 to-yellow-400 bg-clip-text text-transparent"
            >Hunters</span
          >
          🏆
        </h2>
      </div>
      <div
        class="mt-12 overflow-hidden rounded-2xl border border-purple-900/30 bg-[#0f0d1a]/80 backdrop-blur-sm"
      >
        <div
          class="grid grid-cols-5 gap-4 border-b border-purple-900/30 px-6 py-3 text-xs font-bold uppercase tracking-wider text-slate-500"
        >
          <span>Rank</span><span>Player</span><span>Level</span
          ><span>Class</span><span class="text-right">Power</span>
        </div>
        <div
          v-for="p in leaderboard"
          :key="p.rank"
          class="grid grid-cols-5 gap-4 items-center px-6 py-4 border-b border-purple-900/20 transition-colors hover:bg-purple-900/10"
          :class="p.rank <= 3 ? 'bg-purple-900/5' : ''"
        >
          <span
            class="text-lg font-extrabold"
            :class="
              p.rank === 1
                ? 'text-amber-400'
                : p.rank === 2
                  ? 'text-slate-300'
                  : p.rank === 3
                    ? 'text-amber-600'
                    : 'text-slate-500'
            "
            >{{ p.medal || "#" + p.rank }}</span
          >
          <span class="font-bold text-white text-sm">{{ p.name }}</span>
          <span class="text-amber-400 font-bold text-sm">Lv.{{ p.level }}</span>
          <span class="text-purple-400 text-sm">{{ p.cls }}</span>
          <span class="text-right text-sm font-bold text-emerald-400">{{
            p.power
          }}</span>
        </div>
      </div>
    </div>
  </section>

  <!-- CTA -->
  <section class="reveal-on-scroll relative py-24">
    <div class="mx-auto max-w-4xl px-6 text-center">
      <div
        class="relative overflow-hidden rounded-3xl border border-purple-900/30 bg-[#0f0d1a]/80 px-8 py-16 backdrop-blur-sm sm:px-16"
      >
        <div class="pointer-events-none absolute inset-0">
          <div
            class="absolute left-1/2 top-0 -translate-x-1/2 h-[300px] w-[500px] rounded-full bg-purple-500/10 blur-[100px]"
          ></div>
        </div>
        <div class="relative">
          <div class="text-6xl mb-6 animate-float">⚔️</div>
          <h2
            class="text-3xl font-extrabold tracking-tight text-white sm:text-4xl"
          >
            Siap Menjadi
            <span
              class="bg-gradient-to-r from-purple-400 to-pink-400 bg-clip-text text-transparent"
              >Hunter Terkuat?</span
            >
          </h2>
          <p class="mx-auto mt-4 max-w-xl text-slate-400">
            Bergabung dengan 50.000+ player yang sudah bertualang di dunia
            Yurizaki RPG. Gratis, tanpa download!
          </p>
          <a
            href="#"
            class="mt-8 inline-flex items-center gap-2 rounded-xl bg-gradient-to-r from-purple-600 to-pink-600 px-8 py-4 text-base font-semibold text-white shadow-xl shadow-purple-500/25 hover:shadow-purple-500/40 transition-all"
          >
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
          <span
            class="bg-gradient-to-r from-purple-400 to-pink-400 bg-clip-text text-transparent"
            >Yurizaki</span
          >
          <span class="text-slate-600 text-sm font-medium">RPG</span>
        </a>
        <div class="flex items-center gap-6">
          <a
            v-for="l in ['Gameplay', 'Fitur', 'Kelas', 'Kontak']"
            :key="l"
            href="#"
            class="text-sm text-slate-600 hover:text-slate-300 transition-colors"
            >{{ l }}</a
          >
        </div>
      </div>
      <div class="mt-8 border-t border-purple-900/20 pt-6 text-center">
        <p class="text-sm text-slate-700">
          &copy; 2025 ZenBot RPG. All rights reserved. Made with ❤️
        </p>
      </div>
    </div>
  </footer>
</template>

<style scoped>
/* ─── Nav Item Entrance ─── */
.nav-item {
  opacity: 0;
  transform: translateY(-10px);
}
.nav-item-visible {
  opacity: 1;
  transform: translateY(0);
}

/* ─── Scroll Reveal ─── */
.reveal-on-scroll {
  opacity: 0;
  transform: translateY(40px);
  transition:
    opacity 0.8s cubic-bezier(0.16, 1, 0.3, 1),
    transform 0.8s cubic-bezier(0.16, 1, 0.3, 1);
}
.reveal-on-scroll.revealed {
  opacity: 1;
  transform: translateY(0);
}
</style>
