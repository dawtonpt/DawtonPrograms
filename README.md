<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>My Video Library</title>
  <style>
    body {
      background: #181818;
      color: #eee;
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 0;
    }
    header {
      background: #232323;
      padding: 1rem 0;
      text-align: center;
      font-size: 2rem;
      font-weight: bold;
      color: #00ffcc;
      letter-spacing: 2px;
      box-shadow: 0 2px 8px #00ffcc22;
    }
    #search-bar {
      display: flex;
      justify-content: center;
      margin: 1.5rem 0 0.5rem 0;
    }
    #search-input {
      width: 320px;
      max-width: 90vw;
      padding: 0.7rem 1rem;
      border-radius: 5px;
      border: none;
      font-size: 1rem;
      background: #222;
      color: #eee;
      box-shadow: 0 0 8px #00ffcc22;
      outline: none;
      margin-bottom: 0.5rem;
    }
    #library {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      gap: 2rem;
      padding: 2rem;
    }
    .video-card {
      background: #222;
      border-radius: 10px;
      box-shadow: 0 0 10px #00ffcc33;
      width: 320px;
      padding: 1rem;
      display: flex;
      flex-direction: column;
      align-items: center;
      transition: transform 0.2s;
    }
    .video-card:hover {
      transform: scale(1.03);
      box-shadow: 0 0 20px #00ffcc66;
    }
    .video-thumb {
      width: 100%;
      border-radius: 8px;
      margin-bottom: 0.5rem;
      object-fit: cover;
      height: 180px;
      background: #111;
    }
    .video-title {
      font-size: 1.2rem;
      font-weight: bold;
      margin-bottom: 0.3rem;
      color: #00ffcc;
      text-align: center;
    }
    .video-desc {
      font-size: 1rem;
      margin-bottom: 0.5rem;
      color: #ccc;
      text-align: center;
    }
    .play-btn {
      background: #00ffcc;
      color: #181818;
      border: none;
      border-radius: 5px;
      font-weight: bold;
      padding: 0.5rem 1.5rem;
      cursor: pointer;
      font-size: 1rem;
      margin-top: 0.5rem;
      transition: background 0.2s;
    }
    .play-btn:hover {
      background: #00c9a7;
    }
    #player-modal {
      display: none;
      position: fixed;
      z-index: 1000;
      left: 0; top: 0; right: 0; bottom: 0;
      background: rgba(0,0,0,0.85);
      align-items: center;
      justify-content: center;
    }
    #player-content {
      background: #232323;
      border-radius: 10px;
      padding: 2rem;
      box-shadow: 0 0 30px #00ffcc55;
      display: flex;
      flex-direction: column;
      align-items: center;
      max-width: 90vw;
      max-height: 90vh;
    }
    #player-content video {
      width: 640px;
      max-width: 80vw;
      max-height: 60vh;
      border-radius: 8px;
      background: #111;
    }
    #close-player {
      margin-top: 1rem;
      background: #ff5252;
      color: #fff;
      border: none;
      border-radius: 5px;
      font-weight: bold;
      padding: 0.5rem 1.5rem;
      cursor: pointer;
      font-size: 1rem;
      transition: background 0.2s;
    }
    #close-player:hover {
      background: #c90000;
    }
    @media (max-width: 700px) {
      #player-content video {
        width: 98vw;
        max-width: 98vw;
      }
      .video-card {
        width: 95vw;
      }
      #search-input {
        width: 95vw;
      }
    }
  </style>
</head>
<body>
  <header>
    🎬 My Video Library
  </header>
  <div id="search-bar">
    <input type="text" id="search-input" placeholder="Search movies or videos..." />
  </div>
  <div id="library"></div>

  <div id="player-modal">
    <div id="player-content">
      <div id="player-title" class="video-title"></div>
      <video id="player-video" controls></video>
      <button id="close-player">Close</button>
    </div>
  </div>

  <script>
    // Example video data (replace/add with your own)
    const videos = [
      {
        title: "Big Buck Bunny",
        desc: "A short animated film by Blender Foundation.",
        thumb: "https://peach.blender.org/wp-content/uploads/title_anouncement.jpg?x11217",
        src: "https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4"
      },
      {
        title: "Sintel",
        desc: "A fantasy short film by Blender Foundation.",
        thumb: "https://upload.wikimedia.org/wikipedia/commons/7/75/Sintel_poster.jpg",
        src: "https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/Sintel.mp4"
      },
      {
        title: "Tears of Steel",
        desc: "A live-action/CGI short film by Blender Foundation.",
        thumb: "https://mango.blender.org/wp-content/themes/mango/images/project_images/tears_of_steel_poster.jpg",
        src: "https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/TearsOfSteel.mp4"
      }
      // Add more videos here!
    ];

    const libraryDiv = document.getElementById('library');
    const playerModal = document.getElementById('player-modal');
    const playerVideo = document.getElementById('player-video');
    const playerTitle = document.getElementById('player-title');
    const closePlayer = document.getElementById('close-player');
    const searchInput = document.getElementById('search-input');

    function renderLibrary(filter = "") {
      libraryDiv.innerHTML = '';
      const filterLower = filter.trim().toLowerCase();
      let filtered = videos;
      if (filterLower) {
        filtered = videos.filter(
          v =>
            v.title.toLowerCase().includes(filterLower) ||
            v.desc.toLowerCase().includes(filterLower)
        );
      }
      if (filtered.length === 0) {
        libraryDiv.innerHTML = `<div style="color:#ff5252;font-size:1.2rem;">No videos found.</div>`;
        return;
      }
      filtered.forEach((vid, idx) => {
        const card = document.createElement('div');
        card.className = 'video-card';
        card.innerHTML = `
          <img class="video-thumb" src="${vid.thumb}" alt="${vid.title}">
          <div class="video-title">${vid.title}</div>
          <div class="video-desc">${vid.desc}</div>
          <button class="play-btn" data-idx="${videos.indexOf(vid)}">Play</button>
        `;
        libraryDiv.appendChild(card);
      });
    }

    libraryDiv.addEventListener('click', function(e) {
      if (e.target.classList.contains('play-btn')) {
        const idx = e.target.getAttribute('data-idx');
        const vid = videos[idx];
        playerTitle.textContent = vid.title;
        playerVideo.src = vid.src;
        playerVideo.currentTime = 0;
        playerVideo.play();
        playerModal.style.display = 'flex';
      }
    });

    closePlayer.onclick = function() {
      playerVideo.pause();
      playerModal.style.display = 'none';
      playerVideo.src = '';
    };

    playerModal.onclick = function(e) {
      if (e.target === playerModal) {
        closePlayer.onclick();
      }
    };

    searchInput.addEventListener('input', function() {
      renderLibrary(this.value);
    });

    renderLibrary();
  </script>
</body>
</html>
