
<html lang="nl" class="">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Quran Reader</title>

<script src="https://cdn.tailwindcss.com"></script>
<script>
  tailwind.config = { darkMode: 'class' }
</script>

<link href="https://fonts.googleapis.com/css2?family=Amiri&family=Poppins:wght@300;400;600&display=swap" rel="stylesheet">

<style>
  body { font-family: Poppins, sans-serif; }
  .arabic {
    font-family: Amiri, serif;
    font-size: clamp(1.5rem, 4vw, 2rem);
    direction: rtl;
  }
</style>
</head>

<body class="bg-gray-100 text-gray-900 dark:bg-gray-900 dark:text-gray-100 min-h-screen flex flex-col transition-colors">

<header class="bg-white dark:bg-gray-800 shadow p-4 flex justify-between items-center sticky top-0 z-50">
  <h1 class="text-2xl font-bold">Quran</h1>
  <button id="themeBtn" class="px-3 py-1 rounded border">🌙 / ☀️</button>
</header>

<main class="flex-grow p-4">

  <div id="surahList" class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4"></div>

  <div id="surahPage" class="hidden">
    <button id="backBtn"
      class="mb-4 px-4 py-2 rounded bg-gray-200 text-black dark:bg-gray-700 dark:text-white">
      ← Terug
    </button>

    <h2 id="surahTitle" class="text-3xl font-bold mb-1"></h2>
    <p id="surahInfo" class="text-gray-600 dark:text-gray-400 mb-4"></p>

    <audio id="surahAudio" controls class="w-full mb-6"></audio>
    <div id="surahText" class="space-y-6"></div>
  </div>

</main>

<footer class="bg-white dark:bg-gray-800 border-t dark:border-gray-700 p-4 text-center text-sm text-gray-600 dark:text-gray-400">
  Support:
  <a href="mailto:elmouchania@hotmail.com" class="underline text-black dark:text-white">
    elmouchania@hotmail.com
  </a>
</footer>

<script>
document.getElementById("themeBtn").addEventListener("click", () => {
  document.documentElement.classList.toggle("dark");
});

fetch("https://api.alquran.cloud/v1/surah")
  .then(res => res.json())
  .then(data => {
    const list = document.getElementById("surahList");
    data.data.forEach(surah => {
      const card = document.createElement("div");
      card.className =
        "bg-white dark:bg-gray-800 rounded shadow p-4 cursor-pointer transition hover:shadow-lg";

      card.innerHTML = `
        <h3 class="font-bold">${surah.number}. ${surah.englishName}</h3>
        <p class="text-sm text-gray-600 dark:text-gray-400">${surah.englishNameTranslation}</p>
        <div class="arabic text-right mt-3">${surah.name}</div>
      `;

      card.addEventListener("click", () => openSurah(surah.number));
      list.appendChild(card);
    });
  });

function openSurah(id) {
  surahList.classList.add("hidden");
  surahPage.classList.remove("hidden");

  fetch(`https://api.alquran.cloud/v1/surah/${id}`)
    .then(res => res.json())
    .then(r => {
      const s = r.data;
      surahTitle.textContent = `${s.number}. ${s.englishName}`;
      surahInfo.textContent =
        `${s.englishNameTranslation} • ${s.numberOfAyahs} verzen`;

      surahAudio.src =
        `https://cdn.islamic.network/quran/audio-surah/128/ar.alafasy/${id}.mp3`;

      surahText.innerHTML = "";
      s.ayahs.forEach(a => {
        const d = document.createElement("div");
        d.innerHTML = `
          <div class="arabic">${a.text}</div>
          <div class="text-sm text-gray-600 dark:text-gray-400">(${a.numberInSurah})</div>
        `;
        surahText.appendChild(d);
      });
    });
}

backBtn.addEventListener("click", () => {
  surahPage.classList.add("hidden");
  surahList.classList.remove("hidden");
});
</script>

</body>
</html>
