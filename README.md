<html lang="fa" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>پخش ویدیو</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            background: black;
            overflow: hidden;
            height: 100vh;
            width: 100vw;
        }
        
        .video-container {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 1;
            background: #000;
        }
        
        video {
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: block;
        }
        
        /* کنترل‌های مخفی */
        .controls {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: black;
            z-index: 2;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: opacity 0.3s ease;
        }
        
        .play-button {
            width: 100px;
            height: 100px;
            background: rgba(255, 0, 128, 0.7);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            border: none;
            outline: none;
        }
        
        .play-button i {
            font-size: 40px;
            color: white;
        }
        
        .controls.hidden {
            opacity: 0;
            pointer-events: none;
        }
        
        /* انیمیشن لودینگ */
        .loader {
            width: 60px;
            height: 60px;
            border: 4px solid rgba(255, 255, 255, 0.1);
            border-top: 4px solid #ff0080;
            border-radius: 50%;
            animation: spin 1s linear infinite;
            display: none;
        }
        
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
    </style>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
</head>
<body>
    <!-- لودینگ -->
    <div class="loader" id="loader"></div>
    
    <!-- ویدیو -->
    <div class="video-container">
        <video id="videoPlayer" preload="auto" playsinline webkit-playsinline loop>
            <source src="https://rpfile.com/uploads/guest/2026/01/30/176974799956111.mp4" type="video/mp4">
        </video>
    </div>
    
    <!-- کنترل‌های پخش -->
    <div class="controls" id="controls">
        <button class="play-button" id="playButton">
            <i class="fas fa-play"></i>
        </button>
    </div>

    <script>
        // عناصر DOM
        const video = document.getElementById('videoPlayer');
        const playButton = document.getElementById('playButton');
        const controls = document.getElementById('controls');
        const loader = document.getElementById('loader');
        
        // فعال‌سازی حالت تمام صفحه
        function enableFullscreen() {
            const elem = document.documentElement;
            
            if (elem.requestFullscreen) {
                elem.requestFullscreen();
            } else if (elem.mozRequestFullScreen) {
                elem.mozRequestFullScreen();
            } else if (elem.webkitRequestFullscreen) {
                elem.webkitRequestFullscreen();
            } else if (elem.msRequestFullscreen) {
                elem.msRequestFullscreen();
            }
        }
        
        // پخش ویدیو
        function playVideo() {
            loader.style.display = 'block';
            
            video.play().then(() => {
                loader.style.display = 'none';
                controls.classList.add('hidden');
                enableFullscreen();
            }).catch(error => {
                console.log('تلاش مجدد برای پخش...');
                loader.style.display = 'none';
                setTimeout(() => {
                    video.play();
                }, 1000);
            });
        }
        
        // رویداد کلیک دکمه
        playButton.addEventListener('click', playVideo);
        
        // پخش خودکار هنگام آماده شدن ویدیو
        video.addEventListener('loadeddata', function() {
            playVideo();
        });
        
        // رویداد پایان ویدیو - پخش مجدد
        video.addEventListener('ended', function() {
            video.currentTime = 0;
            video.play();
        });
        
        // رویداد خطا
        video.addEventListener('error', function(e) {
            console.error('خطا در ویدیو:', e);
            loader.style.display = 'none';
            controls.classList.remove('hidden');
            playButton.innerHTML = '<i class="fas fa-redo"></i>';
            playButton.style.background = '#ff0000';
        });
        
        // تغییر آیکون هنگام پخش
        video.addEventListener('play', function() {
            playButton.innerHTML = '<i class="fas fa-pause"></i>';
        });
        
        video.addEventListener('pause', function() {
            playButton.innerHTML = '<i class="fas fa-play"></i>';
        });
        
        // کلیک روی صفحه برای نمایش کنترل‌ها
        document.addEventListener('click', function(e) {
            if (!e.target.closest('.play-button')) {
                if (video.paused) {
                    controls.classList.remove('hidden');
                } else {
                    // نمایش موقت کنترل‌ها
                    controls.classList.remove('hidden');
                    setTimeout(() => {
                        if (!video.paused) {
                            controls.classList.add('hidden');
                        }
                    }, 2000);
                }
            }
        });
        
        // کلید اسپیس برای پخش/توقف
        document.addEventListener('keydown', function(e) {
            if (e.code === 'Space') {
                e.preventDefault();
                if (video.paused) {
                    video.play();
                } else {
                    video.pause();
                    controls.classList.remove('hidden');
                }
            }
        });
        
        // حفظ حالت تمام صفحه
        document.addEventListener('fullscreenchange', function() {
            if (!document.fullscreenElement && !video.paused) {
                setTimeout(enableFullscreen, 100);
            }
        });
        
        // جلوگیری از کلیک راست
        document.addEventListener('contextmenu', function(e) {
            e.preventDefault();
        });
        
        // شروع خودکار پس از بارگذاری
        window.addEventListener('load', function() {
            setTimeout(playVideo, 500);
        });
    </script>
</body>
</html>
