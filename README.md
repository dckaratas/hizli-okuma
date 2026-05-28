# 📖 Hızlı Okuma

Vue 3 + Vite ile geliştirilmiş hızlı okuma egzersiz uygulaması.

## Özellikler

- **Paragraf Modu** — Paragraflar yukarıdan aşağıya kayar. Anlama ve hız geliştirmek için idealdir.
- **Ticker Modu** — Kelimeler sağdan sola akar. Göz refleksi ve dikkat odaklaması için idealdir.
- Kategori filtresi (Bilim, Felsefe, Tarih, Doğa, Teknoloji, Psikoloji, Edebiyat, Matematik)
- Kelime/dakika hız ayarı + hızlı presetler
- Yazı boyutu ayarı
- İlerleme çubuğu

## Kurulum

```bash
# Bağımlılıkları yükle
npm install

# Geliştirme sunucusunu başlat (http://localhost:5173)
npm run dev

# Production build
npm run build
```

## Paragraf Ekleme

`src/data/paragraphs.js` dosyasına yeni paragraflar ekleyebilirsin:

```js
{
  tag: "Kategori",
  text: "Paragraf metni buraya gelecek...",
}
```

## Proje Yapısı

```
hizli-okuma/
├── src/
│   ├── components/
│   │   ├── CategoryPills.vue   # Kategori filtre butonları
│   │   ├── ControlPanel.vue    # Hız ve boyut kontrolleri
│   │   ├── ScrollReader.vue    # Yukarıdan aşağı paragraf modu
│   │   └── TickerReader.vue    # Sağdan sola ticker modu
│   ├── data/
│   │   └── paragraphs.js       # Tüm paragraf içerikleri
│   ├── App.vue                 # Ana uygulama ve mod seçimi
│   ├── main.js                 # Vue giriş noktası
│   └── style.css               # Global stiller ve CSS değişkenleri
├── index.html
├── vite.config.js
└── package.json
```
