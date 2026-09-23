# MedPuls — Klinika Lending Sahifasi

Klinikalarni **MedPuls** platformasiga jalb qilish uchun mo'ljallangan bir sahifali (single-page) lending. Ikki tilda (UZ/RU), forma orqali arizalar to'g'ridan-to'g'ri MedPuls CRM backend'iga tushadi va Telegram'ga darhol xabar keladi.

**Jonli sayt:** `https://medpuls.uz/` (GitHub Pages, custom domain)

---

## 1. Loyiha haqida

**MedPuls** — klinikalar uchun operatsion boshqaruv paneli (Clinic OS): registratura, shifokor qabuli, kassa, xodimlar, muolaja/in'eksiya kuzatuvi va rahbar hisobotlari — bitta panelda. Real modullar to'liq ro'yxati: `medpuls_crm` repozitoriyasidagi `backend/app/modules/*` (queue, visits, billing, patients, doctors, staff, followups, appointments, schedule, attendance, procedures, analytics, audit).

Bemor mobil ilovasi orqali klinika qidirish/reyting/onlayn yozilish (marketpleys qatlami) **hozircha mustaqil rejalashtirilgan yo'nalish, bu repo doirasida qurilmagan** — buyurtma modelidagi `source=medpuls_booking` maydoni kelajakda shunday integratsiyaga tayyor, xolos. Landing ushbu haqiqatga mos ravishda faqat panelning real imkoniyatlarini sotadi.

## 2. Bu lendingning vazifasi

Bu sahifa **sotish sahifasi emas — ishonch tekshiruv nuqtasi**. Klinika egasi bizdan xabar olgach, "MedPuls" deb qidiradi va shu sahifaga tushadi. Uning vazifasi bitta savolga javob berish: *"Bular haqiqiymi va vaqtimga arziydimi?"*

Konversiya voronkasi:

```
Sahifa → Forma (konsultatsiya so'rovi) → Qo'ng'iroq/Demo → Shartnoma
```

Shuning uchun sahifadagi **har bir tugma formaga olib boradi** — "hoziroq sotib oling" emas, "gaplashaylik" mantiqidа.

Sahifa ikkita real qatlamni yetkazadi (CRM feature-inventarizatsiyasiga asoslangan, [`medpuls_crm`](https://github.com/ArtCodersGrup/medpuls_crm) kodi bilan tasdiqlangan):
1. **Kundalik operatsiyalar** — navbat, qabul yozuvi, kassa, bemorlar bazasi (har bir tarifda bor, "Boshlang'ich"dan boshlab)
2. **O'sish va nazorat** — oldindan yozuv, qayta aloqa, analitika, muolaja/in'eksiya nazorati, xodimlar davomati, audit jurnali ("Biznes" va "Premium" tariflarida bosqichma-bosqich ochiladi)

Ko'p filial uchun bitta umumiy boshqaruv oynasi hali yo'q — har bir filial alohida obuna/panel bilan ishlaydi (batafsil: sahifadagi "Bir nechta filialimiz bo'lsa-chi?" savoli).

## 3. Dizayn tizimi

Lending **MedPuls mobil ilovasining dizayn tiliga** qat'iy mos — foydalanuvchi ilovani ochganda bir xil brendni ko'rishi kerak.

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
| **MedPuls CRM backend** (`POST /api/leads`) | Formadan so'rovni qabul qiladi, `leads` jadvaliga yozadi, Telegram'ga yuboradi. Google Sheets/Apps Script o'rnini bosadi — [`medpuls_crm`](https://github.com/ArtCodersGrup/medpuls_crm)ning `app/modules/leads/` moduli |
| **Telegram Bot API** | Yangi ariza haqida **darhol** xabar — endi CRM backend tomonidan to'g'ridan-to'g'ri yuboriladi (`app/core/telegram.py`), Apps Script orqali emas |
| **GitHub Pages** | Bepul static hosting, `git push` bilan yangilanadi |

### Arxitektura

```
Brauzer (index.html)
   │  fetch POST (JSON)
   ▼
MedPuls CRM backend   (POST /api/leads, autentifikatsiyasiz ochiq endpoint)
   ├─→ Postgres `leads` jadvali  — ariza saqlanadi (Yangi → Qo'ng'iroq → Demo → Mijoz)
   └─→ Telegram Bot              — asoschiga darhol xabar (background task)
```

Arizalar CRM'ning platform-admin panelida (`/admin/leads`) ko'rinadi va boshqariladi — alohida Google Sheets'ga kirish shart emas.

### Frontend'dagi muhim detallar

- **i18n** — barcha matn `data-i18n` atributi bilan belgilangan; UZ matnlar DOM'dan bazaviy lug'at sifatida o'qiladi, RU lug'ati JS'da. Tanlov `localStorage`'da saqlanadi.
- **Forma himoyasi** — yashirin honeypot maydon (`website`): odam ko'rmaydi, bot to'ldiradi → backend bunday arizani jim tarzda tashlab yuboradi (baza yozuvi ham, Telegram xabari ham bo'lmaydi).
- **CORS** — backend'ning `CORS_ORIGINS` sozlamasida shu sahifaning manzili (hozircha GitHub Pages, keyinchalik `medpuls.uz`) ro'yxatda bo'lishi shart, aks holda brauzer so'rovni bloklaydi.

## 5. Fayl tuzilishi

```
MedPuls-lending/
├── index.html                    # butun lending: HTML + CSS + JS bitta faylda
├── medpuls-intro.mp4             # tanishtiruv videosi (video bo'limida ishlatiladi)
├── medpuls-intro-poster.jpg      # video uchun poster/preview rasmi
└── README.md                     # shu hujjat
```

Backend kodi bu repo'da **saqlanmaydi** — u `medpuls_crm`ning o'z repozitoriyasida (`backend/app/modules/leads/`), token'lar CRM serverining `.env` faylida turadi.

## 6. Ishga tushirish

### Lokal ko'rish
Faylni brauzerda ochish kifoya — server kerak emas. Forma yuborish uchun `LEADS_API_URL`ga ko'rsatilgan CRM backend ishlab turishi kerak (lokal test uchun `http://localhost:8000/api/leads`ga vaqtincha almashtiring).

### Backend ulash (bir marta, CRM tomonida)
1. `medpuls_crm` serverida `.env`ga `TELEGRAM_BOT_TOKEN` va `TELEGRAM_CHAT_ID` qo'shing (bot @BotFather orqali, chat ID — botni guruhga/kanalga qo'shib yoki shaxsiy xabar orqali olinadi)
2. Backend'ning `CORS_ORIGINS`iga shu landing sahifaning manzilini qo'shing (masalan `https://artcodersgrup.github.io`, keyinchalik `https://medpuls.uz`)
3. Backend qayta ishga tushiriladi (`systemctl restart shifotop-backend` — server tomonida hali eski nom, medpuls_crm/docs/infrastructure.md'dagi TODO'ga qarang)
4. `index.html`'dagi `LEADS_API_URL`ni CRM'ning haqiqiy manziliga moslang

### Deploy (GitHub Pages)
```bash
git add -A
git commit -m "yangilanish"
git push
```
Repo **Public** bo'lishi shart (bepul rejada). Settings → Pages → `main` / `(root)` → Save.

## 7. Ishga tushirishdan oldingi tekshiruv

- [ ] `LEADS_API_URL` CRM'ning haqiqiy (domen ulangandan keyingi) manziliga almashtirilgan
- [ ] Backend `CORS_ORIGINS`ida shu sahifaning manzili bor
- [ ] Backend `.env`ida `TELEGRAM_BOT_TOKEN`/`TELEGRAM_CHAT_ID` sozlangan
- [ ] Asoschi blokidagi telefon (`+998 90 000 00 00`) va Telegram (`@medpuls`) haqiqiy manzillarga almashtirilgan
- [ ] Tarif narxlari tasdiqlangan (`class="amt"` qidiring)
- [ ] Jonli domenda forma sinovdan o'tgan: CRM'ning `/admin/leads` sahifasida yozuv **va** Telegram'da xabar keldi
- [ ] RU tugmasi bosib tekshirilgan — barcha bo'lim tarjima qilinadi
- [ ] Sinov arizalari CRM'ning `/admin/leads` sahifasidan tozalangan (yoki e'tiborsiz qoldirilgan)

## 8. Yo'l xaritasi (lending doirasida)

- [ ] Bemor lendingini ochish (klinikalar yig'ilgach — bo'sh marketpleys bemorni qaytaradi)
- [ ] `medpuls.uz` domenini ulash
- [ ] Pilot natijalaridan keyin real raqamlar bilan ijtimoiy isbot qo'shish
- [ ] Birinchi 5–10 qo'ng'iroqdan keyin RU/UZ nisbatini ko'rib, til strategiyasini aniqlash

---

**Aloqa:** klinika@medpuls.uz · Telegram: @medpuls
