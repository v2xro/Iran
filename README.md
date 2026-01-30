
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
        
        html, body {
            width: 100%;
            height: 100%;
            overflow: hidden;
            background: #000;
        }
        
        body {
            display: flex;
            align-items: center;
            justify-content: center;
            font-family: system-ui, -apple-system, sans-serif;
        }
        
        .video-wrapper {
            width: 100vw;
            height: 100vh;
            position: relative;
            background: #000;
        }
        
        video {
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: block;
            background: #000;
        }
        
        /* صفحه شروع */
        .start-screen {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.9);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            z-index: 100;
            transition: opacity 0.5s ease;
        }
        
        .start-screen.hidden {
            opacity: 0;
            pointer-events: none;
        }
        
        .start-button {
            width: 120px;
            height: 120px;
            background: linear-gradient(135deg, #ff3366, #ff9933);
            border-radius: 50%;
            border: none;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            margin-bottom: 30px;
            transition: all 0.3s ease;
            box-shadow: 0 10px 30px rgba(255, 51, 102, 0.4);
        }
        
        .start-button:hover {
            transform: scale(1.1);
            box-shadow: 0 15px 40px rgba(255, 51, 102, 0.6);
        }
        
        .start-button i {
            font-size: 50px;
            color: white;
            margin-left: 5px;
        }
        
        .loading {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 60px;
            height: 60px;
            border: 4px solid rgba(255, 255, 255, 0.1);
            border-top: 4px solid #ff3366;
            border-radius: 50%;
            animation: spin 1s linear infinite;
            z-index: 50;
            display: none;
        }
        
        @keyframes spin {
            0% { transform: translate(-50%, -50%) rotate(0deg); }
            100% { transform: translate(-50%, -50%) rotate(360deg); }
        }
        
        /* نوار کنترل */
        .control-bar {
            position: fixed;
            bottom: 0;
            left: 0;
            width: 100%;
            background: rgba(0, 0, 0, 0.7);
            padding: 15px;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 20px;
            z-index: 10;
            opacity: 0;
            transition: opacity 0.3s ease;
        }
        
        .control-bar.visible {
            opacity: 1;
        }
        
        .control-button {
            background: none;
            border: none;
            color: white;
            font-size: 24px;
            cursor: pointer;
            padding: 10px;
            border-radius: 50%;
            transition: all 0.2s ease;
        }
        
        .control-button:hover {
            background: rgba(255, 255, 255, 0.1);
            transform: scale(1.1);
        }
        
        .time-display {
            color: white;
            font-family: monospace;
            font-size: 14px;
            min-width: 100px;
            text-align: center;
        }
        
        /* پیام خطا */
        .error-message {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0, 0, 0, 0.9);
            color: white;
            padding: 30px;
            border-radius: 15px;
            text-align: center;
            max-width: 400px;
            display: none;
            z-index: 200;
            border: 1px solid rgba(255, 51, 102, 0.3);
        }
        
        .retry-button {
            background: #ff3366;
            color: white;
            border: none;
            padding: 10px 25px;
            border-radius: 25px;
            cursor: pointer;
            margin-top: 20px;
            font-size: 14px;
            transition: background 0.3s ease;
        }
        
        .retry-button:hover {
            background: #ff0044;
        }
    </style>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
</head>
<body>
    <!-- صفحه شروع -->
    <div class="start-screen" id="startScreen">
        <button class="start-button" id="startButton">
            <i class="fas fa-play"></i>
        </button>
        <div style="color: rgba(255, 255, 255, 0.7); font-size: 14px; margin-top: 10px;">
            برای شروع کلیک کنید
        </div>
    </div>
    
    <!-- لودینگ -->
    <div class="loading" id="loading"></div>
    
    <!-- ویدیو -->
    <div class="video-wrapper">
        <video id="videoPlayer" preload="auto" playsinline>
            <source src="https://biaupload.com/static/files-2026-01/org-288e6239fa591.mp4" type="video/mp4">
            مرورگر شما از پخش ویدیو پشتیبانی نمی‌کند.
        </video>
    </div>
    
    <!-- کنترل‌ها -->
    <div class="control-bar" id="controlBar">
        <button class="control-button" id="playPauseBtn">
            <i class="fas fa-pause"></i>
        </button>
        <button class="control-button" id="restartBtn">
            <i class="fas fa-redo"></i>
        </button>
        <div class="time-display" id="timeDisplay">00:00 / 00:00</div>
        <button class="control-button" id="fullscreenBtn">
            <i class="fas fa-expand"></i>
        </button>
    </div>
    
    <!-- پیام خطا -->
    <div class="error-message" id="errorMessage">
        <i class="fas fa-exclamation-triangle" style="font-size: 40px; margin-bottom: 15px; color: #ff3366;"></i>
        <div>مشکلی در بارگذاری ویدیو پیش آمده است</div>
        <button class="retry-button" id="retryButton">تلاش مجدد</button>
    </div>

    <script>
        // عناصر DOM
        const video = document.getElementById('videoPlayer');
        const startScreen = document.getElementById('startScreen');
        const startButton = document.getElementById('startButton');
        const loading = document.getElementById('loading');
        const controlBar = document.getElementById('controlBar');
        const playPauseBtn = document.getElementById('playPauseBtn');
        const restartBtn = document.getElementById('restartBtn');
        const fullscreenBtn = document.getElementById('fullscreenBtn');
        const timeDisplay = document.getElementById('timeDisplay');
        const errorMessage = document.getElementById('errorMessage');
        const retryButton = document.getElementById('retryButton');
        
        // حالت‌ها
        let isPlaying = false;
        let isFullscreen = false;
        let hideControlsTimeout;
        
        // فرمت زمان
        function formatTime(seconds) {
            const mins = Math.floor(seconds / 60);
            const secs = Math.floor(seconds % 60);
            return `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
        }
        
        // به‌روزرسانی زمان
        function updateTime() {
            timeDisplay.textContent = `${formatTime(video.currentTime)} / ${formatTime(video.duration)}`;
        }
        
        // حالت تمام صفحه
        function toggleFullscreen() {
            if (!isFullscreen) {
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
                isFullscreen = true;
            } else {
                if (document.exitFullscreen) {
                    document.exitFullscreen();
                } else if (document.mozCancelFullScreen) {
                    document.mozCancelFullScreen();
                } else if (document.webkitExitFullscreen) {
                    document.webkitExitFullscreen();
                } else if (document.msExitFullscreen) {
                    document.msExitFullscreen();
                }
                isFullscreen = false;
            }
        }
        
        // شروع پخش
        function startPlayback() {
            loading.style.display = 'block';
            
            video.play().then(() => {
                startScreen.classList.add('hidden');
                loading.style.display = 'none';
                isPlaying = true;
                controlBar.classList.add('visible');
                
                // ورود به حالت تمام صفحه
                setTimeout(() => {
                    if (!document.fullscreenElement) {
                        toggleFullscreen();
                    }
                }, 500);
                
                // مخفی کردن کنترل‌ها پس از 3 ثانیه
                hideControlsTimeout = setTimeout(() => {
                    controlBar.classList.remove('visible');
                }, 3000);
                
            }).catch(error => {
                console.error('خطا در پخش:', error);
                loading.style.display = 'none';
                errorMessage.style.display = 'block';
            });
        }
        
        // پخش مجدد
        function restartVideo() {
            video.currentTime = 0;
            video.play();
        }
        
        // کنترل‌های ماوس و تاچ
        function showControls() {
            controlBar.classList.add('visible');
            clearTimeout(hideControlsTimeout);
            
            if (isPlaying) {
                hideControlsTimeout = setTimeout(() => {
                    controlBar.classList.remove('visible');
                }, 3000);
            }
        }
        
        // رویدادها
        startButton.addEventListener('click', startPlayback);
        
        playPauseBtn.addEventListener('click', () => {
            if (video.paused) {
                video.play();
                playPauseBtn.innerHTML = '<i class="fas fa-pause"></i>';
                isPlaying = true;
            } else {
                video.pause();
                playPauseBtn.innerHTML = '<i class="fas fa-play"></i>';
                isPlaying = false;
            }
            showControls();
        });
        
        restartBtn.addEventListener('click', () => {
            restartVideo();
            showControls();
        });
        
        fullscreenBtn.addEventListener('click', () => {
            toggleFullscreen();
            showControls();
        });
        
        retryButton.addEventListener('click', () => {
            errorMessage.style.display = 'none';
            video.load();
            setTimeout(startPlayback, 500);
        });
        
        // رویدادهای ویدیو
        video.addEventListener('play', () => {
            playPauseBtn.innerHTML = '<i class="fas fa-pause"></i>';
            isPlaying = true;
        });
        
        video.addEventListener('pause', () => {
            playPauseBtn.innerHTML = '<i class="fas fa-play"></i>';
            isPlaying = false;
            showControls();
        });
        
        video.addEventListener('timeupdate', updateTime);
        
        video.addEventListener('loadedmetadata', () => {
            updateTime();
        });
        
        video.addEventListener('ended', () => {
            setTimeout(() => {
                restartVideo();
            }, 500);
        });
        
        video.addEventListener('waiting', () => {
            loading.style.display = 'block';
        });
        
        video.addEventListener('playing', () => {
            loading.style.display = 'none';
        });
        
        video.addEventListener('error', (e) => {
            console.error('خطای ویدیو:', e);
            loading.style.display = 'none';
            errorMessage.style.display = 'block';
        });
        
        // رویدادهای صفحه
        document.addEventListener('fullscreenchange', () => {
            isFullscreen = !!document.fullscreenElement;
            fullscreenBtn.innerHTML = isFullscreen ? 
                '<i class="fas fa-compress"></i>' : 
                '<i class="fas fa-expand"></i>';
        });
        
        document.addEventListener('click', showControls);
        document.addEventListener('mousemove', showControls);
        document.addEventListener('touchstart', showControls);
        
        document.addEventListener('keydown', (e) => {
            if (e.code === 'Space') {
                e.preventDefault();
                if (video.paused) {
                    video.play();
                } else {
                    video.pause();
                }
                showControls();
            }
            
            if (e.code === 'Escape' && isFullscreen) {
                toggleFullscreen();
            }
        });
        
        // شروع خودکار پس از بارگذاری
        video.addEventListener('canplaythrough', () => {
            // ویدیو آماده پخش است
        });
        
        // بارگذاری اولیه
        video.load();
        
        // پیش‌بارگذاری ویدیو
        setTimeout(() => {
            video.load();
        }, 1000);
        
        // جلوگیری از کلیک راست
        document.addEventListener('contextmenu', (e) => {
            e.preventDefault();
        });
        
        console.log('پخش کننده ویدیو آماده است!');
    </script>
</body>
</html>
