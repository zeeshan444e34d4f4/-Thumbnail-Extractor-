<html lang="en">
 <head></head>
 <body> 
  <meta charset="UTF-8"> 
  <meta name="viewport" content="width=device-width, initial-scale=1.0"> 
  <title>YT Thumbnail, Logo &amp; Banner Downloader</title> 
  <style>
    * {
      box-sizing: border-box;
    }

    body {
      font-family: 'Segoe UI', sans-serif;
      margin: 0;
      padding: 40px;
      display: flex;
      justify-content: center;
      background: linear-gradient(135deg, #ffe2e2, #e1f0ff, #e5ffe1);
      background-size: 600% 600%;
      animation: gradientBG 20s ease infinite;
    }

    @keyframes gradientBG {
      0% { background-position: 0% 50%; }
      50% { background-position: 100% 50%; }
      100% { background-position: 0% 50%; }
    }

    .card {
      background: #fff;
      padding: 30px;
      border-radius: 15px;
      box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
      width: 100%;
      max-width: 600px;
      text-align: center;
    }

    h2 {
      margin-bottom: 20px;
      color: #d32f2f;
    }

    input, button {
      padding: 12px;
      margin: 10px 0;
      width: 100%;
      border-radius: 8px;
      font-size: 16px;
      border: 1px solid #ccc;
    }

    button {
      background-color: #d32f2f;
      color: white;
      border: none;
      cursor: pointer;
      transition: 0.3s;
    }

    button:hover {
      background-color: #b71c1c;
    }

    .media-section img {
      max-width: 100%;
      margin-top: 20px;
      border-radius: 10px;
      animation: fadeIn 0.8s ease;
      box-shadow: 0 4px 15px rgba(0,0,0,0.2);
    }

    a.download-link {
      display: inline-block;
      margin-top: 10px;
      background: #388e3c;
      color: white;
      padding: 10px 16px;
      border-radius: 8px;
      text-decoration: none;
    }

    @keyframes fadeIn {
      from { opacity: 0; }
      to { opacity: 1; }
    }

    .footer-bar {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-top: 20px;
    }

    .dots-menu {
      background: none;
      border: none;
      font-size: 24px;
      cursor: pointer;
      padding: 0;
    }

    .info-popup {
      display: none;
      margin-top: 10px;
      background-color: #fff8e1;
      padding: 15px;
      border-radius: 10px;
      font-size: 14px;
      text-align: left;
      animation: fadeIn 0.5s ease;
      border: 1px solid #fdd835;
    }

    @media (max-width: 600px) {
      body {
        padding: 20px;
      }

      .card {
        padding: 20px;
        width: 100%;
        max-width: 100%;
      }

      h2 {
        font-size: 20px;
      }

      input, button {
        font-size: 14px;
      }

      .info-popup {
        font-size: 13px;
      }

      .dots-menu {
        font-size: 20px;
      }
    }
  </style> 
  <div class="card"> 
   <h2>YouTube Thumbnail, Logo &amp; Banner Downloader</h2> 
   <input type="text" id="ytLink" placeholder="Paste YouTube Video URL"> <button onclick="fetchAll()">Get Media</button> 
   <div class="media-section" id="thumbnailSection"></div> 
   <div class="media-section" id="logoSection"></div> 
   <div class="media-section" id="bannerSection"></div> 
   <div class="footer-bar"> <span><strong>© Zeeshan Hamid</strong></span> <button id="moreBtn" class="dots-menu">⋮</button> 
   </div> 
   <div id="moreInfo" class="info-popup"> 
    <p><strong>Users:</strong> 40M+</p> 
    <p><strong>Ratings:</strong> 1.8M+</p> 
    <p>This tool helps you download thumbnails, logos, and banners from YouTube in one click. Fast, simple, and free to use!</p> 
   </div> 
  </div> 
  <script>
    const API_KEY = "YOUR_YOUTUBE_API_KEY"; // Replace with your actual YouTube API key

    function getVideoID(url) {
      const regex = /(?:youtube\.com.*(?:\?v=|\/embed\/|\/v\/)|youtu\.be\/)([a-zA-Z0-9_-]{11})/;
      const match = url.match(regex);
      return match ? match[1] : null;
    }

    async function fetchAll() {
      const url = document.getElementById('ytLink').value;
      const videoID = getVideoID(url);
      const thumbSection = document.getElementById('thumbnailSection');
      const logoSection = document.getElementById('logoSection');
      const bannerSection = document.getElementById('bannerSection');

      thumbSection.innerHTML = '';
      logoSection.innerHTML = '';
      bannerSection.innerHTML = '';

      if (!videoID) {
        thumbSection.innerHTML = `<p style="color:red;">Invalid YouTube URL</p>`;
        return;
      }

      // Show Thumbnail
      const thumbnailURL = `https://img.youtube.com/vi/${videoID}/maxresdefault.jpg`;
      thumbSection.innerHTML = `
        <h3>Thumbnail</h3>
        <img src="${thumbnailURL}" alt="Thumbnail" />
        <a class="download-link" href="${thumbnailURL}" download="thumbnail.jpg">Download Thumbnail</a>
      `;

      try {
        // Fetch video details to get channelId
        const videoRes = await fetch(`https://www.googleapis.com/youtube/v3/videos?part=snippet&id=${videoID}&key=${API_KEY}`);
        const videoData = await videoRes.json();
        const channelId = videoData.items[0].snippet.channelId;

        // Fetch channel details
        const channelRes = await fetch(`https://www.googleapis.com/youtube/v3/channels?part=snippet,brandingSettings&id=${channelId}&key=${API_KEY}`);
        const channelData = await channelRes.json();
        const channel = channelData.items[0];

        const logoURL = channel.snippet.thumbnails.default.url;
        const bannerURL = channel.brandingSettings.image?.bannerExternalUrl;

        // Show Logo
        logoSection.innerHTML = `
          <h3>Channel Logo</h3>
          <img src="${logoURL}" alt="Channel Logo" />
          <a class="download-link" href="${logoURL}" download="logo.jpg">Download Logo</a>
        `;

        // Show Banner if available
        if (bannerURL) {
          bannerSection.innerHTML = `
            <h3>Channel Banner</h3>
            <img src="${bannerURL}" alt="Channel Banner" />
            <a class="download-link" href="${bannerURL}" download="banner.jpg">Download Banner</a>
          `;
        }

      } catch (error) {
        console.error('Error fetching YouTube data:', error);
        logoSection.innerHTML = `<p style="color:red;">Error fetching channel info</p>`;
      }
    }

    // Handle More Info Toggle
    document.getElementById("moreBtn").addEventListener("click", () => {
      const info = document.getElementById("moreInfo");
      info.style.display = info.style.display === "block" ? "none" : "block";
    });
  </script> 
 </body>
</html># -Thumbnail-Extractor-
