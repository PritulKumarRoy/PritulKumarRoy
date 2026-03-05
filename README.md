<div style="position: relative; width: 100%;">
  <!-- Hello Text & Language Switcher -->
  <div style="display: flex; justify-content: space-between; align-items: center; padding: 15px 20px; background: #f6f8fa;">
    <span style="font-family: 'Times New Roman', serif; font-size: 24px; font-weight: bold; color: #000;">Hello,</span>
    <a href="#" onclick="toggleLanguage(event)" style="font-size: 18px; cursor: pointer; text-decoration: none;" title="Change Language">🌐</a>
  </div>

  <!-- Background GIF -->
  <div style="width: 100%; height: 20vh; position: relative; overflow: hidden; margin: 0; padding: 0;">
    <img 
      src="./bg1.gif"
      style="
        width: 100%;
        height: 100%;
        object-fit: cover;
        display: block;
        position: absolute;
        top: 0;
        left: 0;
      "
      alt="Background Animation"
    />
  </div>
</div>

<script>
  let currentLanguage = 'en';
  
  const translations = {
    en: { hello: 'Hello,' },
    es: { hello: 'Hola,' },
    fr: { hello: 'Bonjour,' },
    de: { hello: 'Hallo,' },
    zh: { hello: '你好,' },
    ja: { hello: 'こんにちは,' }
  };
  
  function toggleLanguage(e) {
    e.preventDefault();
    const langs = Object.keys(translations);
    const currentIndex = langs.indexOf(currentLanguage);
    currentLanguage = langs[(currentIndex + 1) % langs.length];
    
    const helloEl = document.querySelector('span');
    if (helloEl) {
      helloEl.textContent = translations[currentLanguage].hello;
    }
  }
</script>
