# ShifoTop — Klinika Lending Sahifasi

Klinikalarni **ShifoTop** platformasiga jalb qilish uchun mo'ljallangan bir sahifali (single-page) lending. Ikki tilda (UZ/RU), forma orqali arizalar to'g'ridan-to'g'ri Google Sheets'ga tushadi va Telegram'ga darhol xabar keladi.

**Jonli sayt:** `https://artcodersgrup.github.io/ShifoTop-lending/`

---

## 1. Loyiha haqida

**ShifoTop** — O'zbekiston uchun tibbiy 2-tomonlama bozor (healthcare marketplace):

- **Bemor (User)** — mobil ilovada klinika qidiradi, reyting va izohlarni ko'radi, navbatga yoziladi
- **Klinika (Clinic)** — web panelda buyurtmalarni boshqaradi, bemor bilan yozishadi, jadval va narxlarni yuritadi
- **Admin** — klinika arizalarini tekshiradi va tasdiqlaydi

Platformaning yuragi — **buyurtma hayotiy sikli**: `YANGI → TASDIQLANGAN → BAJARILDI` (yoki `RAD / BEKOR / KELMADI`). Har holat o'zgarishi bemor va klinikaga avtomatik xabar yuboradi.

## 2. Bu lendingning vazifasi

Bu sahifa **sotish sahifasi emas — ishonch tekshiruv nuqtasi**. Klinika egasi bizdan xabar olgach, "ShifoTop" deb qidiradi va shu sahifaga tushadi. Uning vazifasi bitta savolga javob berish: *"Bular haqiqiymi va vaqtimga arziydimi?"*

Konversiya voronkasi:

```
Sahifa → Forma (konsultatsiya so'rovi) → Qo'ng'iroq/Demo → Shartnoma
```

Shuning uchun sahifadagi **har bir tugma formaga olib boradi** — "hoziroq sotib oling" emas, "gaplashaylik" mantiqidа.

Sahifa ikkita taklifni yetkazadi:
1. **Ilovada bemorlar oqimi** — klinika profili, qidiruvda ko'rinish, 24/7 navbat qabuli
2. **Klinikani avtomatlashtirish** — panelni sozlash, xodimlarni o'qitish, qo'llab-quvvatlash (qamrov ataylab shu bilan cheklangan — tashqi tizimlarga integratsiya va'da qilinmaydi)

## 3. Dizayn tizimi

Lending **ShifoTop mobil ilovasining dizayn tiliga** qat'iy mos — foydalanuvchi ilovani ochganda bir xil brendni ko'rishi kerak.

| Token | Qiymat | Vazifasi |
|---|---|---|
| `teal-600` | `#0D9488` | Asosiy brend rangi, urg'u, havolalar |
| `teal-700` | `#0F766E` | Tugma foni (oq matn bilan, WCAG AA) |
| `teal-900` | `#0C302E` | Hero va footer to'q foni |
| Shrift | **Inter** (400–700) | Barcha matn, `-0.02em` letter-spacing sarlavhalarda |
| Grid | **8pt** | Bazaviy spacing qadamlari: 4·8·12·16·24·32·48·64 px (komponentga qarab boshqa oraliq qiymatlar ham uchraydi) |
| Radius | `8px` (default), `12px` karta, `20px` katta blok | |
| Motion | `cubic-bezier(0.4, 0, 0.2, 1)` · 200ms | Hover, o'tishlar |
| Status ranglar | success `#15803D` · warning `#B45309` · danger `#B91C1C` | Holat piltalari (badge) |

### E'tibor berilgan tamoyillar

- **Signature element** — hero ostida logodagi EKG/puls chizig'i o'zini chizadi (2.6s, `stroke-dashoffset`). Brendning o'z shaklidan olingan, bezak emas.
- **Halollik** — soxta ijtimoiy isbot yo'q ("50+ klinika ishonadi" kabi). O'rniga taklif faktlari: 30 kun bepul, 24/7 qabul, 0 so'm sozlash to'lovi. Real raqamlar pilotdan keyin qo'shiladi.
- **Ishonch = odam** — forma yonida asoschi bloki (ism, telefon, Telegram). O'zbek B2B bozorida shartnoma saytdan emas, odamdan boshlanadi.
- **Narx anchoring** — 3 tarif (290k / 590k / 1 190k so'm), o'rtadagi "Ommabop" belgili. Yillik shartnomaga 2 oy bepul bandi.
- **Accessibility** — kontrast ≥ 4.5:1, fokus halqasi, bosish maydoni ≥ 48px, `prefers-reduced-motion` hurmat qilinadi, forma maydonlari `<label>` bilan bog'langan.
- **Animatsiya intizomi** — bitta orkestrlangan moment (puls) + mayin scroll-reveal va hisoblagichlar. Ortiqcha harakat yo'q.

## 4. Texnologiyalar va ularning vazifasi

Ataylab **framework'siz, build'siz** — bitta HTML fayl. Sabab: lending uchun React/Vue ortiqcha yuk; bitta fayl har qanday hostingda ishlaydi, saqlash oson.

| Texnologiya | Vazifasi |
|---|---|
| **HTML5 (semantik)** | Sahifa tuzilishi: `header/main/section/footer`, `<details>` bilan FAQ (JS'siz accordion) |
| **CSS3 (vanilla)** | Butun dizayn: CSS custom properties (design tokenlar), Grid/Flex layout, animatsiyalar (`@keyframes`, `transition`) |
| **JavaScript (vanilla)** | 4 vazifa: i18n (UZ/RU lug'at almashtirish), scroll-reveal (`IntersectionObserver`), raqam hisoblagichlar, forma yuborish |
| **Google Fonts (Inter)** | Yagona tashqi bog'liqlik — brend shrifti |
| **Google Apps Script** | Backend qatlami: formadan POST qabul qiladi, Sheets'ga yozadi, Telegram'ga yuboradi. Serverless, bepul, token'larni yashiradi |
| **Google Sheets** | Arizalar bazasi + oddiy CRM (Holat ustuni: Yangi → Qo'ng'iroq → Demo → Mijoz) |
| **Telegram Bot API** | Yangi ariza haqida **darhol** xabar — Sheets bildirishnoma bermaydi, bu bo'shliqni yopadi |
| **GitHub Pages** | Bepul static hosting, `git push` bilan yangilanadi |

### Arxitektura

```
Brauzer (index.html)
   │  fetch POST (FormData)
   ▼
Google Apps Script  (/exec endpoint)
   ├─→ Google Sheets   — qator qo'shadi (telefon matn formatida, takror belgisi)
   └─→ Telegram Bot    — asoschiga darhol xabar
```

**Nega Apps Script oraliq qatlam?** Telegram tokeni to'g'ridan-to'g'ri HTML'da tursa, sahifa manbasini ochgan har kim uni o'g'irlaydi. Apps Script tokenni Google serverida yashiradi — frontend faqat ochiq `/exec` URL'ni biladi.

### Frontend'dagi muhim detallar

- **i18n** — barcha matn `data-i18n` atributi bilan belgilangan; UZ matnlar DOM'dan bazaviy lug'at sifatida o'qiladi, RU lug'ati JS'da. Tanlov `localStorage`'da saqlanadi.
- **Forma himoyasi** — yashirin honeypot maydon (`website`): odam ko'rmaydi, bot to'ldiradi → Apps Script bunday arizani tashlab yuboradi.
- **CORS fallback** — avval oddiy `fetch`, bloklansa `no-cors` rejimda qayta yuboriladi. Shu sababli haqiqiy muvaffaqiyat belgisi — Sheets'da qator paydo bo'lishi.
- **Telefon `#ERROR!` muammosi** — `+998...` Sheets'da formula deb o'qilardi; backend raqam oldiga `'` qo'shadi va D ustuni matn (`@`) formatida.

## 5. Fayl tuzilishi

```
ShifoTop-lending/
├── index.html                    # butun lending: HTML + CSS + JS bitta faylda
├── shifotop-intro.mp4            # tanishtiruv videosi (video bo'limida ishlatiladi)
├── shifotop-intro-poster.jpg     # video uchun poster/preview rasmi
└── README.md                     # shu hujjat
```

Backend kodi (`.gs`) repo'da **saqlanmaydi** — u Google Apps Script muharririda turadi (token'lar shu yerda).

## 6. Ishga tushirish

### Lokal ko'rish
Faylni brauzerda ochish kifoya — server kerak emas.

### Backend ulash (bir marta)
1. Google Sheets yarating → ID'ni oling
2. Apps Script'da backend kodini joylashtiring, `SHEET_ID`, `TG_TOKEN`, `TG_CHAT_ID` to'ldiring
3. **Deploy → Web app** (`Execute as: Me`, `Access: Anyone`) → `/exec` URL oling
4. `index.html`'dagi `SCRIPT_URL` o'zgaruvchisiga qo'ying
5. Kod o'zgarsa — **qayta deploy** (Manage deployments → New version), aks holda eski versiya ishlaydi

### Deploy (GitHub Pages)
```bash
git add -A
git commit -m "yangilanish"
git push
```
Repo **Public** bo'lishi shart (bepul rejada). Settings → Pages → `main` / `(root)` → Save.

## 7. Ishga tushirishdan oldingi tekshiruv

- [ ] `SCRIPT_URL` haqiqiy `/exec` havolaga almashtirilgan
- [ ] Asoschi blokidagi telefon (`+998 90 000 00 00`) va Telegram (`@shifotop`) haqiqiy manzillarga almashtirilgan
- [ ] Tarif narxlari tasdiqlangan (`class="amt"` qidiring)
- [ ] Jonli domenda forma sinovdan o'tgan: Sheets'da qator **va** Telegram'da xabar keldi
- [ ] RU tugmasi bosib tekshirilgan — barcha bo'lim tarjima qilinadi
- [ ] Sinov qatorlari Sheets'dan o'chirilgan

## 8. Yo'l xaritasi (lending doirasida)

- [ ] Bemor lendingini ochish (klinikalar yig'ilgach — bo'sh marketpleys bemorni qaytaradi)
- [ ] `shifotop.uz` domenini ulash
- [ ] Pilot natijalaridan keyin real raqamlar bilan ijtimoiy isbot qo'shish
- [ ] Birinchi 5–10 qo'ng'iroqdan keyin RU/UZ nisbatini ko'rib, til strategiyasini aniqlash

---

**Aloqa:** klinika@shifotop.uz · Telegram: @shifotop
