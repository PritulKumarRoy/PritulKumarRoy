<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Upgraded Animated Hero Section</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Times New Roman', serif;
            background-color: #0a0a0a;
            overflow-x: hidden;
        }

        .hero-container {
            width: 100%;
            height: 20vh;
            position: relative;
            overflow: hidden;
            margin: 0;
            padding: 0;
        }

        /* Background Image */
        .hero-bg {
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: block;
            position: absolute;
            top: 0;
            left: 0;
            z-index: 1;
        }

        /* Overlay for text readability */
        .hero-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(135deg, rgba(0, 0, 0, 0.3) 0%, rgba(0, 0, 0, 0.1) 100%);
            z-index: 2;
        }

        /* Greeting Text */
        .hero-greeting {
            position: absolute;
            top: 1.5rem;
            left: 2rem;
            z-index: 3;
            font-size: 3rem;
            font-weight: 300;
            color: #ffffff;
            letter-spacing: 0.05em;
            font-family: 'Times New Roman', serif;
            animation: slideInLeft 0.8s ease-out;
            text-shadow: 2px 2px 8px rgba(0, 0, 0, 0.4);
        }

        /* Language Switcher Icon */
        .language-switcher {
            position: absolute;
            top: 1.2rem;
            right: 2rem;
            z-index: 4;
            background: rgba(255, 255, 255, 0.15);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.3);
            border-radius: 50%;
            width: 3rem;
            height: 3rem;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.3s ease;
            font-size: 1.2rem;
            color: #ffffff;
        }

        .language-switcher:hover {
            background: rgba(255, 255, 255, 0.25);
            border-color: rgba(255, 255, 255, 0.5);
            transform: scale(1.1);
        }

        .language-switcher:active {
            transform: scale(0.95);
        }

        /* Language Dropdown Menu */
        .language-menu {
            position: absolute;
            top: 4.5rem;
            right: 2rem;
            background: rgba(20, 20, 20, 0.95);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
            border-radius: 0.5rem;
            min-width: 150px;
            z-index: 5;
            display: none;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
            overflow: hidden;
            animation: slideDown 0.3s ease-out;
        }

        .language-menu.active {
            display: block;
        }

        .language-option {
            padding: 0.8rem 1.2rem;
            cursor: pointer;
            color: #ffffff;
            font-size: 0.9rem;
            transition: all 0.2s ease;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
        }

        .language-option:last-child {
            border-bottom: none;
        }

        .language-option:hover {
            background: rgba(255, 255, 255, 0.1);
            padding-left: 1.5rem;
        }

        .language-option.active {
            background: rgba(100, 200, 255, 0.2);
            color: #64c8ff;
            font-weight: bold;
        }

        /* Animations */
        @keyframes slideInLeft {
            from {
                opacity: 0;
                transform: translateX(-40px);
            }
            to {
                opacity: 1;
                transform: translateX(0);
            }
        }

        @keyframes slideDown {
            from {
                opacity: 0;
                transform: translateY(-10px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        /* Responsive Design */
        @media (max-width: 768px) {
            .hero-greeting {
                font-size: 2rem;
                top: 1rem;
                left: 1rem;
            }

            .language-switcher {
                top: 1rem;
                right: 1rem;
                width: 2.5rem;
                height: 2.5rem;
                font-size: 1rem;
            }

            .language-menu {
                top: 3.8rem;
                right: 1rem;
            }
        }
    </style>
</head>
<body>

    <div class="hero-container">
        <!-- Background Image -->
        <img 
            src="./bg1.gif"
            class="hero-bg"
            alt="Background Animation"
        />

        <!-- Overlay for text readability -->
        <div class="hero-overlay"></div>

        <!-- Greeting Text -->
        <div class="hero-greeting" id="greeting">Hello,</div>

        <!-- Language Switcher Icon -->
        <div class="language-switcher" id="languageSwitcher">
            <i class="fas fa-globe"></i>
        </div>

        <!-- Language Dropdown Menu -->
        <div class="language-menu" id="languageMenu">
            <div class="language-option active" data-lang="en">English</div>
            <div class="language-option" data-lang="es">Español</div>
            <div class="language-option" data-lang="fr">Français</div>
            <div class="language-option" data-lang="de">Deutsch</div>
            <div class="language-option" data-lang="it">Italiano</div>
            <div class="language-option" data-lang="pt">Português</div>
            <div class="language-option" data-lang="ja">日本語</div>
            <div class="language-option" data-lang="zh">中文</div>
            <div class="language-option" data-lang="ar">العربية</div>
            <div class="language-option" data-lang="ru">Русский</div>
            <div class="language-option" data-lang="ko">한국어</div>
        </div>
    </div>

    <script>
        // Language translations
        const translations = {
            en: { greeting: "Hello," },
            es: { greeting: "Hola," },
            fr: { greeting: "Bonjour," },
            de: { greeting: "Hallo," },
            it: { greeting: "Ciao," },
            pt: { greeting: "Olá," },
            ja: { greeting: "こんにちは," },
            zh: { greeting: "你好," },
            ar: { greeting: "مرحبا،" },
            ru: { greeting: "Привет," },
            ko: { greeting: "안녕하세요," }
        };

        const greetingElement = document.getElementById('greeting');
        const languageSwitcher = document.getElementById('languageSwitcher');
        const languageMenu = document.getElementById('languageMenu');
        const languageOptions = document.querySelectorAll('.language-option');

        let currentLanguage = 'en';

        // Toggle language menu
        languageSwitcher.addEventListener('click', () => {
            languageMenu.classList.toggle('active');
        });

        // Close menu when clicking outside
        document.addEventListener('click', (e) => {
            if (!e.target.closest('.language-switcher') && !e.target.closest('.language-menu')) {
                languageMenu.classList.remove('active');
            }
        });

        // Language selection
        languageOptions.forEach(option => {
            option.addEventListener('click', () => {
                const lang = option.getAttribute('data-lang');
                currentLanguage = lang;

                // Update greeting text with fade effect
                greetingElement.style.opacity = '0';
                setTimeout(() => {
                    greetingElement.textContent = translations[lang].greeting;
                    greetingElement.style.opacity = '1';
                }, 150);

                // Update active state in menu
                languageOptions.forEach(opt => opt.classList.remove('active'));
                option.classList.add('active');

                // Close menu
                languageMenu.classList.remove('active');

                // Store preference in localStorage
                localStorage.setItem('preferredLanguage', lang);
            });
        });

        // Load saved language preference
        window.addEventListener('load', () => {
            const savedLanguage = localStorage.getItem('preferredLanguage') || 'en';
            if (savedLanguage !== 'en') {
                currentLanguage = savedLanguage;
                greetingElement.textContent = translations[savedLanguage].greeting;
                
                // Update active indicator
                languageOptions.forEach(opt => {
                    if (opt.getAttribute('data-lang') === savedLanguage) {
                        opt.classList.add('active');
                    } else {
                        opt.classList.remove('active');
                    }
                });
            }
        });
    </script>

</body>
</html>
