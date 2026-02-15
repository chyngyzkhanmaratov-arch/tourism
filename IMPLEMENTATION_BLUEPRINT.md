# Toursim — системный blueprint (WordPress + Elementor Pro)

## 1) Целевая архитектура

### Технологический контур
- **CMS**: WordPress (актуальная stable-ветка).
- **Page Builder**: Elementor Pro (Theme Builder + Loop/Grid templates).
- **Данные**: Custom Post Types + ACF (PRO).
- **Мультиязычность**: WPML (основной), fallback Polylang только при жёстком budget-limit.
- **SEO**: Rank Math (или Yoast, но фиксируем один стек на проект).
- **Бронирование**: WP Travel Engine (основной), fallback WooCommerce + WooTour.
- **B2B кабинет**: Dokan + WooCommerce + AffiliateWP (процентные комиссии).

### Доменные сущности (CPT)
1. `tour` — турпакет.
2. `guide` — гид.
3. `agency` — партнёр/агент.
4. `review` — отзыв.

### Таксономии
- `tour_region` (Иссык-Көл, Нарын, Алай, etc)
- `tour_type` (Adventure, Cultural, Combo)
- `tour_duration` (3 days, 7 days, 10+)
- `tour_difficulty` (Easy, Moderate, Hard) — рекомендуется добавить сразу.

## 2) Data model для `tour` (ACF)

### Базовые поля
- `price_from` (number) — цена от.
- `price_per` (select: per_person / per_group).
- `duration_days` (number).
- `short_description` (textarea).
- `featured_video_url` (url, YouTube/Vimeo).
- `guide_reference` (post_object -> `guide`).
- `gallery` (gallery).

### Контентные блоки
- `included_items` (repeater: item_text).
- `excluded_items` (repeater: item_text).
- `accommodation` (wysiwyg).
- `meals` (wysiwyg).
- `transport` (wysiwyg).
- `faq` (repeater: question, answer).
- `itinerary_days` (repeater):
  - `day_number` (number)
  - `day_title` (text)
  - `day_route` (text)
  - `day_description` (wysiwyg)
  - `day_gallery` (gallery, optional)

### Служебные поля (рекомендуется)
- `is_featured` (true/false).
- `booking_external_id` (text, если есть внешний engine ID).
- `starting_point` / `ending_point` (text).
- `group_size_min` / `group_size_max` (number).

## 3) URL и routing
- Архив туров: `/tours/`
- Карточка тура: `/tours/{slug}`
- Языковые префиксы WPML: `/en/`, `/ru/`, `/ar/`, `/tr/`, `/zh/`
- Пример: `/en/tours/issyk-kul-3-days`

**Важно**: не менять slug после индексации, использовать 301 при любых миграциях URL.

## 4) Шаблоны Elementor (Theme Builder)

### Home (`front-page.php` + Elementor template)
1. Hero (video/slider + CTA: Explore Tours, Book Now).
2. Floating messenger buttons (WhatsApp/Telegram).
3. Блок «Почему Кыргызстан?» (3–4 USP карточки).
4. Популярные туры (динамический loop из `tour`, лимит 6, сортировка featured -> recent).
5. Отзывы (`review` slider).
6. Финальный CTA block «Start your adventure».
7. Footer (контакты, соцсети, quick links, language switcher).

### Tours archive
- Sticky filter bar:
  - duration
  - price range
  - region
  - tour type
- Grid карточек: image, title, location, price, duration, button.
- Пагинация + canonical для filter pages.

### Single Tour
- Hero: title, short description, price, Book Now.
- Tabs: Overview / Itinerary / Included / Accommodation / Gallery / FAQ.
- Itinerary accordion (Day 1, Day 2...).
- Sticky sidebar (desktop):
  - price
  - date picker
  - people count
  - Book Now

## 5) Booking flow

### Базовый сценарий
1. Пользователь выбирает дату.
2. Указывает количество человек.
3. Видит пересчёт total.
4. Переходит к checkout.
5. Платёж (Stripe/PayPal).
6. Получает email confirmation.

### Технические правила
- Все booking events логировать (view_tour, begin_checkout, purchase).
- Валидация availability до оплаты и после webhook.
- Идемпотентная обработка webhook (чтобы не дублировать заказы).

## 6) Мультиязычность и локализация
- Primary язык: **EN**.
- Переводы: RU, AR, TR, ZH.
- Для каждого `tour` обязателен complete translation set.
- Переводить:
  - SEO title/description
  - FAQ
  - Itinerary days
  - CTA labels
- Для AR включить RTL стили в Elementor/теме.

## 7) Партнёрский кабинет (B2B)

### Минимальный scope (MVP)
- Регистрация агента с moderation.
- Dashboard:
  - список бронирований
  - начисленная комиссия
  - статус выплат
- Загрузка документов (KYC/contract).

### Phase 2
- Реферальные ссылки (AffiliateWP).
- Разные комиссионные планы по агентствам.
- Отчёты по периодам (CSV export).

## 8) Security + performance baseline
- VPS + Cloudflare (WAF + CDN).
- Object cache (Redis).
- Page cache (WP Rocket).
- Media optimization (Imagify/WebP).
- Daily backups + monthly restore test.
- 2FA для админов и партнёров.
- Ограничение login attempts + reCAPTCHA.

## 9) Дизайн-система
- Стиль: premium minimal, акцент на визуал.
- Цвета:
  - Navy `#0F2742`
  - Sand `#D9C3A3`
  - White `#FFFFFF`
- Типографика:
  - Headings: Playfair Display
  - Body/UI: Montserrat
- Правила:
  - крупные hero-изображения
  - много whitespace
  - короткие смысловые блоки

## 10) Контентная стартовая матрица (каталог)
- Issyk-Kul 3 Days
- Naryn — Son-Kol
- Alay — Sary-Mogol
- Uzbekistan + Kyrgyzstan Combo
- Almaty — Charyn — Bishkek

## 11) Delivery roadmap (без лишнего)

### Sprint 1 (Core)
- CPT + taxonomies + ACF schema.
- Базовые Elementor templates (Home, Archive, Single).
- SEO setup + URL structure.

### Sprint 2 (Commerce)
- Booking engine integration.
- Checkout + payments.
- Transactional emails.

### Sprint 3 (B2B)
- Partner onboarding.
- Commission tracking.
- Document management.

### Sprint 4 (Scale)
- Performance hardening.
- Analytics dashboards.
- CRO итерации (A/B для CTA/hero).

## 12) Definition of done (проверка качества)
- Любой тур редактируется из админки без правки шаблона.
- Фильтры архива работают без 404/дубликатов.
- Все 5 языков имеют корректные hreflang.
- Booking проходит end-to-end с реальным webhook.
- Lighthouse mobile >= 80 на Home/Tours/Single Tour.
- Core Web Vitals в зелёной зоне на целевых страницах.
