
<html lang="nl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Quran Reader</title>

<!-- Tailwind -->
<script src="https://cdn.tailwindcss.com"></script>
<script>
  tailwind.config = { darkMode: 'class' }
</script>

<!-- Fonts -->
<link href="https://fonts.googleapis.com/css2?family=Amiri&family=Poppins:wght@400;600&display=swap" rel="stylesheet">

<style>
  body { font-family: Poppins, sans-serif; }
  .arabic {
    font-family: Amiri, serif;
    font-size: clamp(1.6rem, 5vw, 2.2rem);
    direction: rtl;
  }
</style>
</head>

<body class="bg-gray-100 text-gray-900 dark:bg-gray-900 dark:text-gray-100 min-h-screen flex flex-col">

<header class="bg-white dark:bg-gray-800 shadow p-4 flex justify-between items-center">
  <h1 class="text-2xl font-bold">Quran</h1>
  <button id="themeBtn"
    class="px-3 py-1 border rounded border-gray-400 dark:border-gray-600">
    🌙 / ☀️
  </button>
</header>

<main class="flex-grow p-4">

  <!-- SURAH LIST -->
  <div id="surahList"
       class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4">
  </div>

  <!-- SURAH PAGE -->
  <div id="surahPage" class="hidden">

    <button id="backBtn"
      class="mb-4 w-full sm:w-auto px-4 py-2 rounded
             bg-gray-200 text-black
             dark:bg-gray-700 dark:text-white">
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
  <a href="mailto:elmouchania@hotmail.com"
     class="underline text-black dark:text-white">
    elmouchania@hotmail.com
  </a>
</footer>

<script>
// DARK MODE
document.getElementById("themeBtn").addEventListener("click", () => {
  document.documentElement.classList.toggle("dark");
});

// LOAD SURAH LIST
fetch("https://api.alquran.cloud/v1/surah")
  .then(r => r.json())
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

// OPEN SURAH
function openSurah(id) {
  surahList.classList.add("hidden");
  surahPage.classList.remove("hidden");

  fetch(`https://api.alquran.cloud/v1/surah/${id}`)
    .then(r => r.json())
    .then(res => {
      const s = res.data;

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

// BACK
document.getElementById("backBtn").addEventListener("click", () => {
  surahPage.classList.add("hidden");
  surahList.classList.remove("hidden");
});
</script>

</body>
</html>
