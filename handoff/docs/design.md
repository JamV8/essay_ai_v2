# CargoOS: дизайн фронтенда, направление «Workpaper»

Финальный бриф для W3 (фронтенд, Джамал). Собран из семи отчётов исследователей, ревью брифа (44 правки) и спеки *CargoOS Build Spec* (разделы 3, 5–9, 11). Где бриф расходился со спекой, побеждает спека: типы, имена, документы, события и why-строки взяты из неё. Сейчас суббота 24 октября, около 15:30 PT; фичи замораживаются в 23:00 без исключений.

---

## 1. TL;DR

- **«Workpaper»:** рабочий лист аудитора, нейтральная серая подложка, белые листы, чернила, деньги как типографика. Цвет только для статуса. Синий `#1F4A8C` = всё, что ведёт к источнику (ссылки, фокус, выбор, одна primary-кнопка). Жёлтый `#FFD54A` с подчёркиванием только у точной цитаты в документе.
- **Данные только из контракта спеки** (`lib/types.ts`, 5.1). Фронт владеет адаптером `lib/ui/view-model.ts`. Why-строка = `Finding.explanation` как есть; итоги = `caseTotals()` Дева; What changed = payload события `recomputed`.
- **Шрифты:** Public Sans (UI), Source Serif 4 (письма, ответ, вопрос, тело диспута), IBM Plex Mono только внутри печатных документов (quote, invoice, proforma).
- **Экран 1280×640** (Chrome с тулбаром): полоса доверия, header, уравнение `Billed = Agreed + At stake + Waiting on answers`; слева три группы со своим скроллом, справа инспектор; Source view закрывает инспектор целиком.
- **Статусы различаются заливкой:** To dispute сплошной, Waiting светлый с пунктиром, Cleared без заливки. Читается в grayscale и на проекторе.
- **Демо-момент: revision player.** Новую ревизию не рисуем сразу, а сравниваем группы на экране с новыми и двигаем строки по одной: 4 в Cleared, 6 в To dispute, At stake $300 → $700, затем What changed.
- **План:** фичи 15:30–23:00 по ценности для демо, 23:00–01:00 полировка, репетиция на двух устройствах до 23:00.
- **Честность в рамке:** `Synthetic data. Nothing is sent or paid automatically.`, `Replay`, `Demo: no login`, `Agent permissions`. Ни процентов уверенности, ни sparkles. Решения подписаны `Decided by rule R2`, факты `Extracted by Claude`.

---

## 2. Процесс

Mobbin и Refero хороши для паттернов и микрокопи, не для стиля; фаза референсов закрыта (раздел 9). «Сделано AI» получается от дрейфа, когда агент собирает экран «по умолчанию». Против дрейфа: токены до первой строки UI, документы Scenario A дословно, типографика денег, одно авторское движение. Не используем v0, Stitch, Figma Make, Subframe, Magic Patterns, Claude Design, блоки и стили (shadcn, Refero), скилл `refero-design` и плагин `frontend-design`.

| Когда | Шаг |
|---|---|
| 15:30 | Файл в `docs/design.md`, правила в `app/CLAUDE.md`, сообщение Пранаву (8.2). |
| Каждый компонент | План агента (ASCII на 1280px, токены, что элемент сообщает клерку) → код против фикстуры → component test (спека 10.4: всё, что показывает числа или закрывает действие) → `npm run lint:slop` → коммит в свою ветку. |
| Три чекпойнта | `npm run shots` на 1280×640 и 1280×720: после плеера (~18:45), после инбокса (~21:25), в 23:00–01:00. Агент разбирает скриншоты по списку Never. |
| ~18:45 | Запись переезда, просмотр на 0.5× с тиммейтом. |
| До 23:00 | Репетиция на двух устройствах на деплое (6.3). |
| 23:00–01:00 | Hallway-тест 10 минут: «Что оспорить и почему?», «Откуда $4,500? Покажи», «Чего ждёт система?». Правим копирайт, не layout. |
| Утро | Проектор: `data-gray`, статусы различимы без цвета. |

Если застрял, открой ссылку из раздела 9, не больше 10 минут.

---

## 3. Арт-дирекшн: «Workpaper»

### 3.1 Концепт
CargoOS как аккуратный младший аудитор: каждое число связано с источником (tick and tie, [AccountingTools](https://www.accountingtools.com/articles/what-are-audit-tick-marks.html)). Берём идею связи без галочек: связь это подчёркнутая ссылка и подсвеченная цитата в полном документе. Три голоса: **бумага** (документы: письма Source Serif 4, бланки Plex Mono, как в оригинале), **система** (Public Sans: решения и деньги), **карандаш** (синий: связи с источником). Визуальная идея из настоящих бумаг: ledger pad, bill of lading, [CBP Form 7501](https://www.CBP.gov/sites/default/files/assets/documents/2023-Nov/CBP%20Form%207501.pdf).

### 3.2 Шрифты
Все из `next/font/google` (часть Next, зависимость не нужна).

| Роль | Шрифт | Размер / интерлиньяж / вес |
|---|---|---|
| UI | **Public Sans** | meta 13/18 (500); why 14/20; строки 15/22 (описание 500); группы 15/20 (600); title 17/24 (600); figures 22/28 (500); At stake 34/40 (600, −0.01em) |
| Деньги, коды правил, локаторы, время в чате | Public Sans `tabular-nums lining-nums` | суммы 15/22 (600), коды 12/600 |
| email, reply, вопрос ops lead, тело диспута | **Source Serif 4** | 15/24; вопрос в инбоксе 17/26 |
| Текст quote, freight_invoice, proforma | **IBM Plex Mono** 400 | 12/18, `white-space: pre` |
| Чип типа события в History | IBM Plex Mono | 12/18 |

- Только 400/500/600, без uppercase и letter-spacing; `tabular-nums` глобально на `body` (не проверено, переживает ли `tnum` сабсеттинг Google: сверь $4,800 и $650).
- Mono не идёт на подписи UI: «моноширинный шрифт для мелких data-лейблов» [SKILL.md Anthropic](https://github.com/anthropics/claude-code/blob/main/plugins/frontend-design/skills/frontend-design/SKILL.md) называет признаком AI (trait 5). Plex Mono в бланках другое: тексты спеки 7.1 набраны фиксированной шириной с колонками на пробелах, шрифт воспроизводит документ.

### 3.3 Цветовые токены (только светлая тема)
Контраст по WCAG: на белом, дальше на указанном фоне.

| Токен | Hex | Роль | Контраст |
|---|---|---|---|
| `--bg` | `#F4F5F7` | фон страницы | — |
| `--surface` / `--paper` | `#FFFFFF` | список, инспектор, лист документа | ink 18.0 |
| `--sunken` | `#E9ECF0` | полосы групп, холст документа, полоса доверия, Replay, табы, скелетон | ink 15.2; ink-2 7.6 |
| `--hover` | `#F6F7F9` | hover строки | — |
| `--rule` / `--frame` | `#D0D5DC` / `#A3ABB5` | разделители / рамки списка и инспектора | декор |
| `--control` | `#76808C` | рамки input, badge, secondary | 4.0; 3.7 bg; 3.4 sunken |
| `--ink` | `#14171A` | основной текст | 18.0; 16.5 bg |
| `--ink-2` | `#434A53` | why, вторичный текст | 9.0; 8.2 bg; 7.6 sunken |
| `--ink-3` | `#59616B` | мета, не мельче 13px | 6.3; 5.8 bg; 5.3 sunken; 4.9 link-tint |
| `--link` | `#1F4A8C` | ссылки, фокус, выбор, primary | 8.7; 8.0 bg; 6.8 link-tint; белый на нём 8.7 |
| `--link-tint` | `#D9E4F5` | выбранная строка, wash переехавшей | ink 14.0 |
| `--highlight` / `--highlight-line` | `#FFD54A` / `#8A6400` | **только** цитата в документе / её подчёркивание | ink 12.7 / 5.4 |
| `--selection` | `#C9D8EE` | `::selection` | — |
| `--dispute` | `#9A2318` | дельта, At stake, сплошной чип, полоса | 8.0; белый на нём 8.0 |
| `--waiting` / `--waiting-tint` | `#7A5200` / `#FDF1D3` | текст и фон чипа Waiting, баннер extraction failed | 6.9; 6.2 на tint |
| `--waiting-bar` | `#A87300` | пунктир полосы, чипа, карточки вопроса | 4.1 |
| `--cleared` | `#2C6444` | только глиф ✓ | 7.0 |

Прежний фон `#F3F4EE` был в ΔE 1.7 от «тёплого крема», который SKILL.md называет признаком AI (trait 1); серые от него в ΔE 5.0 и держат проектор, поэтому **Present mode удалён**. Подсветка темнее и с подчёркиванием: прежние `#FFE58F` (1.20:1) и link-tint (1.18:1) на проекторе пропадали.

### 3.4 Статус без опоры на цвет
Раньше чипы dispute и waiting различались по яркости в 1.15 раза (при дейтеранопии ΔE 5.6), а переезд строки 6 между ними и есть демо-момент. Теперь пять каналов: группа, заливка чипа, глиф, узор полосы, слово.

| Статус | Чип группы | Глиф (SVG 14px, stroke 1.5) | Полоса у строки | Дельта |
|---|---|---|---|---|
| To dispute | `bg-dispute text-white` (8.0) | ⊗ | 3px сплошная `--dispute` | `+$300`, 600, `--dispute` |
| Waiting on answers | `bg-waiting-tint text-waiting border border-dashed border-waiting-bar` (6.2) | `?` в пунктирном круге | 3px пунктир `--waiting-bar` | `unconfirmed`, 13px, `--waiting` |
| Cleared | без заливки, `text-ink` | ✓ `--cleared` | прозрачные 3px | `—` |
| Dismissed by clerk | — | ✓ `--ink-3` | прозрачные 3px | `—` и тег |

### 3.5 Форма и сетка
- **Радиусы:** контейнеры 6px, кнопки 4px, теги 3px, лист 2px; шкала shadcn зажата в `@theme`. Ни пилюль, ни нулевого радиуса (SKILL.md числит «broadsheet» без радиуса клише).
- **Линии и тени:** всё 1px; border или shadow, никогда оба; тень только у hover-card, tooltip, toast; `backdrop-blur` обнулён.
- **Сетка 4px:** поля 24px, gap 16px; полоса доверия 24, header 48, overview 88, табы 40, заголовок группы 36, шапка колонок 28; паддинг строки `8px 16px`.
- **Одна сетка колонок на весь список:** `--cols: 3px 28px minmax(0,1fr) 172px 96px 104px` для шапки, заголовков групп и строк. Своя сетка с `auto` у каждой строки разводит бейджи и описания, это классический вид навайбкоженного UI.
- Один белый список с рамкой `--frame` и полосами групп; строки не карточки.

### 3.6 Числа и даты
- Целые центы везде; формат `formatUSD(cents)` Дева (`lib/engine/money.ts`, спека: `$4,800`). Знак дельты добавляет обёртка `lib/ui/format.ts`: `+$300`, минус U+2212, ноль в колонке `—`, в уравнении `$0` цветом `--ink-3`.
- Точность на уровне кейса: хоть одна сумма с центами (сценарий судьи), и весь кейс в `$4,800.00`; не умеет `formatUSD`, оберни `Intl.NumberFormat`. Суммы вправо, Billed на одной x во всех группах.
- Даты в why уже отформатированы движком (`Sep 5`). Мета и история: `Oct 24, 2:05 PM PT`, всегда `timeZone: "America/Los_Angeles"` (иначе Vercel рендерит в UTC и ловит hydration mismatch). Время события это `CaseEvent.at`.
- Цитаты в `“…”`, неразрывный пробел в `Sep 5` и после кода правила.

### 3.7 Выделение цитаты
`<mark>`: фон `#FFD54A`, `box-shadow: inset 0 -2px 0 #8A6400`, радиус 2px, паддинг 0 2px. Ищет `findSpan` (8.6): точное вхождение, затем с нормализацией пробелов. Офсетов в контракте нет, Citations API бэкенд не использует (спаны проверяет код, правило 4), так что поиск по тексту и есть основной путь. Цитата встаёт на треть высоты области документов (у бланков ещё `scrollLeft`), без `scrollIntoView`. Не нашли: фейковой подсветки нет, `Quote not found in this document. Nothing is highlighted.` Метки на полях нет (холсту 12px негде), связь со строкой показывают чипы.

### 3.8 Rule badge
22px, рамка `--control`, радиус 3px, паддинг 0 6px: код `R2` (12/600, `tabular-nums`) и название `Rate mismatch` (12/500, `--ink-2`); колонка 172px вмещает самое длинное. HoverCard (300ms, открывается и по фокусу): `R2 Rate mismatch` / определение одной фразой / `Decided by code, not by the model.` Названия и определения в 8.4, это предложения фронта.

### 3.9 Иконки
Свои SVG (14px, stroke 1.5) только для статусов ⊗, `?`, ✓; литеральные символы ✓ и ⊗ падают в системный шрифт. Lucide (16px, stroke 1.5) только для хрома и типов источников: `ChevronRight`, `X`, `Lock`, `TriangleAlert`, `FileText`, `Mail`, `MessageSquare`, `Reply`. Иконки у каждого лейбла нет; `Sparkles`, `Wand`, `Bot` запрещены.

### 3.10 Signature: каждое число привязано к источнику
После `Finding.explanation` стоят ссылки из `Finding.evidence` (13px): `Email, Sep 5`, `Chat, Sep 8`; свой спан строки инвойса не показываем. Стиль `text-link underline decoration-1 underline-offset-3 decoration-link/60`, подчёркивание всегда сплошное: пунктир пропадает на проекторе, а синий по яркости почти равен `--ink-2` (1.01:1). Клик открывает Source view на спане. Галочек в тексте нет: ✓ значит только Cleared (`Agreed $4,500✓` в спорной строке читался как «одобрено»).

### 3.11 CSS: готово к вставке (Tailwind v4)
```css
/* app/globals.css: @import-строки и сгенерированный shadcn блок @theme inline (--color-background: var(--background) …)
   оставь; сгенерированные :root, .dark и @custom-variant dark замени этим. Наш @theme ниже дописывает и перекрывает */
:root {
  --bg: #F4F5F7; --surface: #FFFFFF; --paper: #FFFFFF; --sunken: #E9ECF0; --hover: #F6F7F9;
  --rule: #D0D5DC; --frame: #A3ABB5; --control: #76808C;
  --ink: #14171A; --ink-2: #434A53; --ink-3: #59616B;
  --link: #1F4A8C; --link-tint: #D9E4F5; --selection: #C9D8EE;
  --highlight: #FFD54A; --highlight-line: #8A6400;                 /* только цитата в документе */
  --dispute: #9A2318; --waiting: #7A5200; --waiting-tint: #FDF1D3; --waiting-bar: #A87300; --cleared: #2C6444;
  --bar-w: 3px; --radius: 6px;
  --cols: 3px 28px minmax(0, 1fr) 172px 96px 104px;
  --shadow-float: 0 1px 2px rgb(20 23 26 / 0.10), 0 8px 24px rgb(20 23 26 / 0.12);
  /* семантика shadcn */
  --background: var(--bg); --foreground: var(--ink); --card: var(--surface); --card-foreground: var(--ink);
  --popover: var(--surface); --popover-foreground: var(--ink); --primary: var(--link); --primary-foreground: #FFFFFF;
  --secondary: var(--surface); --secondary-foreground: var(--ink); --muted: var(--sunken); --muted-foreground: var(--ink-2);
  --accent: var(--hover); --accent-foreground: var(--ink); --destructive: var(--dispute);
  --border: var(--rule); --input: var(--control); --ring: var(--link);
}
html[data-gray] { filter: grayscale(1); }                           /* dev: статус без цвета */

@theme inline {
  --color-surface: var(--surface); --color-paper: var(--paper); --color-sunken: var(--sunken); --color-hover: var(--hover);
  --color-rule: var(--rule); --color-frame: var(--frame); --color-control: var(--control);
  --color-ink: var(--ink); --color-ink-2: var(--ink-2); --color-ink-3: var(--ink-3);
  --color-link: var(--link); --color-link-tint: var(--link-tint); --color-highlight: var(--highlight);
  --color-dispute: var(--dispute); --color-waiting: var(--waiting); --color-waiting-tint: var(--waiting-tint);
  --color-waiting-bar: var(--waiting-bar); --color-cleared: var(--cleared);

  --font-sans: var(--font-public-sans), ui-sans-serif, system-ui, sans-serif;
  --font-serif: var(--font-source-serif), ui-serif, Georgia, serif;
  --font-mono: var(--font-plex-mono), ui-monospace, SFMono-Regular, Menlo, monospace;

  --text-meta: 13px; --text-meta--line-height: 18px;     --text-why: 14px; --text-why--line-height: 20px;
  --text-body: 15px; --text-body--line-height: 22px;     --text-title: 17px; --text-title--line-height: 24px;
  --text-figure: 22px; --text-figure--line-height: 28px;
  --text-hero: 34px; --text-hero--line-height: 40px; --text-hero--letter-spacing: -0.01em;
  --text-doc: 15px; --text-doc--line-height: 24px;       --text-typed: 12px; --text-typed--line-height: 18px;

  --radius-xs: 2px; --radius-sm: 3px; --radius-md: 4px; --radius-lg: 6px;
  --radius-xl: 6px; --radius-2xl: 6px; --radius-3xl: 6px; --radius-4xl: 4px;
  --shadow-2xs: 0 0 #0000; --shadow-xs: 0 0 #0000; --shadow-sm: 0 0 #0000;
  --shadow-md: var(--shadow-float); --shadow-lg: var(--shadow-float); --shadow-xl: var(--shadow-float); --shadow-2xl: var(--shadow-float);
  --blur-xs: 0px; --blur-sm: 0px; --blur-md: 0px; --blur-lg: 0px;
  --ease-ledger: cubic-bezier(0.2, 0, 0, 1);
}
@theme {
  --animate-moved: moved 2400ms cubic-bezier(0.2, 0, 0, 1) both;
  @keyframes moved {
    0%, 70% { background-color: var(--link-tint); outline-color: var(--link); }
    100%    { background-color: transparent; outline-color: transparent; }
  }
}
@layer base {
  * { @apply border-border; scrollbar-width: thin; scrollbar-color: var(--frame) transparent; }
  html { background: var(--bg); }
  body { @apply bg-background text-foreground font-sans text-body; font-variant-numeric: tabular-nums lining-nums; }
  :focus-visible { outline: 2px solid var(--link); outline-offset: 2px; }
  ::selection { background: var(--selection); color: var(--ink); }
  mark { background: var(--highlight); color: var(--ink); box-shadow: inset 0 -2px 0 var(--highlight-line);
    border-radius: 2px; padding: 0 2px; }
}
@utility money { font-variant-numeric: tabular-nums lining-nums; text-align: right; white-space: nowrap; }
@utility row-grid { display: grid; grid-template-columns: var(--cols); column-gap: 8px; }
@utility row-outline { outline: 2px solid transparent; outline-offset: -2px; }   /* для data-focus и animate-moved */
@utility bar-dispute { border-left: var(--bar-w) solid var(--dispute); }
@utility bar-waiting { border-left: var(--bar-w) dashed var(--waiting-bar); }
@utility bar-none { border-left: var(--bar-w) solid transparent; }
```

---

## 4. Layout

**Ширина 1280:** поля 24px, gap 16px, список около 760px, инспектор 456px, шире 1440 `max-w-[1440px] mx-auto`. Узкие экраны не делаем, кроме инбокса на телефоне (390px).

**Высота: целимся в 1280×640.** 1920×1080 с масштабом 150% даёт 1280×720, но тулбар Chrome съедает около 85px. Поэтому демо идёт в полноэкранном Chrome (6.4), скриншоты снимаем на `1280, 720` и `1280, 640`, ревизия 1 укладывается в 620px (иначе заголовок Cleared, куда приземляется строка 4, уходит за край), а у колонки списка свой скролл: `<motion.div layoutScroll className="min-h-0 overflow-y-auto">` вокруг `<ul>`.

| Ревизия 1, клерк | px |
|---|---|
| Полоса доверия 24 + header 48 + overview 88 (без полосы-пропорции) + отступ 12 | 172 |
| Шапка колонок 28 + To dispute (заголовок 36 + строка 1 с why в две строки 80) | 144 |
| Waiting: заголовок 36 + карточка вопроса 84 + строки 4 и 6 по 68 | 256 |
| Заголовок Cleared 36 + отступ 12 | 48 |
| **Итого** | **620** |

Why строк 4 и 6 (около 510px) в жизни в одну строку: под why колонки описания и правила (около 630px), справа 200px под кнопками; на ASCII переносы из-за ширины схемы.

Тексты и номера на схемах из спеки 7; вопрос ops lead и тема диспута это примеры (их пишет Claude); время условное (настоящее из `CaseEvent.at`). Обозначения: `■` сплошной чип, `┆?…┆` светлый чип с пунктиром, `┃` сплошная полоса, `┆` пунктирная, `▓…▓` `<mark>`, `~~x~~` зачёркнуто, `▐x▌` активный сегмент.

**W1. Клерк, ревизия 1, строка не выбрана (инспектор: Timeline):**
```
┌────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│ Synthetic data. Nothing is sent or paid automatically.                                         Agent permissions    Demo: no login │
├────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ CargoOS │ Bayline 40HC Ningbo-LB                                         Add evidence    View as  ▐Clerk▌  Controller   Ops lead 1 │
│         │ Invoice PCL-INV-1012, FOB Ningbo.   [Awaiting answer]                                                                    │
├────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ Billed            Agreed            At stake                 Waiting on answers                                  [ Draft dispute ] │
│ $7,500      =     $6,600      +     $300 ← 34px, dispute   +  $600                                  Revision 1, Oct 24, 1:30 PM PT │
│ 1 to dispute · 2 waiting on answers · 5 cleared.    Checked before you pay.                                                        │
├──────────────────────────────────────────────────────────────────────────────────────┬─────────────────────────────────────────────┤
│    #  Line                                 Rule                 Billed     Over by   │ ▁Timeline▁   What changed   History         │
│ ┌──────────────────────────────────────────────────────────────────────────────────┐ │ ──────────────────────────────────────────  │
│ │ [■ To dispute]  1 line                                        $4,800       +$300 │ │ Sep 2   Quote Q-PCL-0902                    │
│ ├──────────────────────────────────────────────────────────────────────────────────┤ │         ~~Ocean freight $4,800~~            │
│ │┃ 1  Ocean freight 40HC Ningbo-Long Beach [R2 Rate mismatch]   $4,800       +$300 │ │           replaced by Email, Sep 5          │
│ │┃    Billed $4,800. Agreed $4,500 (email, Sep 5, if       [Agree] [Not an issue]  │ │         Destination THC $650                │
│ │┃    booked by Sep 10; booked Sep 8). Over by $300.                               │ │         Customs entry $125                  │
│ │┃    Email, Sep 5   Chat, Sep 8   ← ссылки с подчёркиванием                       │ │         Drayage $850                        │
│ ├──────────────────────────────────────────────────────────────────────────────────┤ │         Documentation fee $75               │
│ │ [┆? Waiting on answers┆]  2 lines                               $600 unconfirmed │ │         PSS allowed by the quote            │
│ │   ┌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┐   │ │         Valid until Sep 30                  │
│ │   ╎ Invoice line 4 bills a $200 congestion fee and line 6 bills $400 rush    ╎   │ │ Sep 5   Email: Ocean freight $4,500,        │
│ │   ╎ handling. Neither is in the quote or emails; the WhatsApp chat only…     ╎   │ │         if booked by Sep 10                 │
│ │   ╎ Asked the ops lead, Oct 24, 1:31 PM. $600 waiting.  Chat, Sep 28    Open ╎   │ │ Sep 8   Chat: Booked Sep 8                  │
│ │   └╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┘   │ │ Oct 12  Invoice PCL-INV-1012, $7,500        │
│ │┆ 4  Port congestion fee                  [R6 No evidence]       $200 unconfirmed │ │ Oct 24  Question to the ops lead,           │
│ │┆    Billed $200. Nothing in the quote, emails or chats   [Agree] [Not an issue]  │ │         waiting for a reply                 │
│ │┆    agrees to this charge.                                                       │ │                                             │
│ │┆ 6  Rush handling                        [R6 No evidence]       $400 unconfirmed │ │                                             │
│ │┆    Billed $400. Nothing in the quote, emails or chats   [Agree] [Not an issue]  │ │                                             │
│ │┆    agrees to this charge.                                                       │ │                                             │
│ ├──────────────────────────────────────────────────────────────────────────────────┤ │                                             │
│ │ [✓ Cleared]  5 lines    ▸ Show                                $2,100           — │ │                                             │
│ └──────────────────────────────────────────────────────────────────────────────────┘ │                                             │
└──────────────────────────────────────────────────────────────────────────────────────┴─────────────────────────────────────────────┘
```
- Мета header: `shipmentRef`, `incoterm` и тег из `Case.status`. Описание строки из `Charge.description`, why из `Finding.explanation`, ссылки под why из `Finding.evidence`.
- `Agree` и `Not an issue` видны всегда на To dispute и Waiting, только клерку.
- Состав Timeline зависит от фактов в фикстуре. Отдельного документа booking нет: «booked Sep 8» это факт из чата.

**W2. Клерк, Source view строки 1** (список как на W1, строка 1 подсвечена `--link-tint`, `j`/`k` листают строки):
```
┌──────────────────────────────────────────────┐
│ Line 1, Ocean freight…     Edit line   Esc ✕ │
│ Decided by rule R2 Rate mismatch. Facts      │
│ extracted by Claude.      ← в жизни 1 строка │
│ [Email, Sep 5] [Chat, Sep 8] [Invoice, …] →  │
│  ┌─────────────────────────────────────────┐ │
│  │ Email, Sep 5, 2:21 PM PT. Word for word.│ │
│  │ ┌─────────────────────────────────────┐ │ │
│  │ │ Subject: RE: Q-PCL-0902 revised     │ │ │
│  │ │ Hi Maya, we can do better on        │ │ │
│  │ │ ocean. ▓Ocean freight reduced to    │ │ │
│  │ │ $4,500 if booked by Sep 10▓.        │ │ │
│  │ │ Everything else unchanged. - Derek  │ │ │
│  │ └─────────────────────────────────────┘ │ │
│  │ Chat, WhatsApp export. Word for word.   │ │
│  │ ┌─────────────────────────────────────┐ │ │
│  │ │ Sep 8, 9:12 AM   Derek PCL          │ │ │
│  │ │   ▓Booking confirmed for your       │ │ │
│  │ │   40HC, booked today Sep 8▓         │ │ │
│  │ │ Sep 8, 9:15 AM   Sam Bayline        │ │ │
│  │ │   great thanks                      │ │ │
│  │ └─────────────────────────────────────┘ │ │
│  │ Invoice PCL-INV-1012. Plex Mono, pre    │ │
│  │ ┌─────────────────────────────────────┐ │ │
│  │ │ 1  ▓Ocean freight 40HC Ningbo-Lo →  │ │ │
│  └─────────────────────────────────────────┘ │
└──────────────────────────────────────────────┘
```
- Шапка 40px с `Edit line` и `Esc`; одна строка 24px провенанса; одна строка чипов 36px по спану на чип в порядке `Finding.evidence` (не влезают, строка скроллится вбок). Заменённый факт (`Fact.supersedes`) даёт зачёркнутый чип с tooltip `Replaced by Email, Sep 5`.
- Ниже одна область прокрутки: документы в порядке чипов, у каждого подпись (тип, дата, дословность) и свои `<mark>`. Клик по чипу прокручивает к подсветке. При открытии прокручиваем к `primarySpan` (8.4): для строки 1 это письмо Sep 5, как в демо на 0:20.
- Холст: паддинг 12px, лист 16px 20px, документу остаётся около 400×330px. Блока key-value, списка доказательств и `‹ ›` нет: why уже всё сказала.

**W3. Ops lead, инбокс** (approve и dispute нет; на телефоне одна колонка):
```
┌────────────────────────────────────────────────────────────────────────────────────────────────────┐
│ CargoOS │ Questions for the ops lead                       View as  Clerk  Controller ▐Ops lead 1▌ │
├──────────────────────────────┬─────────────────────────────────────────────────────────────────────┤
│ ▁Open 1▁   Answered 0        │ Question about Bayline 40HC Ningbo-LB                   Open        │
│ ┌──────────────────────────┐ │ From CargoOS agent, Oct 24, 1:31 PM PT                              │
│ ┃ Bayline 40HC Ningbo-LB   │ │                                                                     │
│ ┃ Lines 4 and 6, $600      │ │ Invoice line 4 bills a $200 congestion fee and line 6               │
│ ┃ Asked 1:31 PM            │ │ bills $400 rush handling. Neither is in the quote or                │
│ └──────────────────────────┘ │ emails; the WhatsApp chat only says congestion 'may be              │
│                              │ extra'. Did anyone agree to either charge?  ← Serif 17px            │
│                              │                                                                     │
│                              │ Sources   Chat, Sep 28                                              │
│                              │ Waiting on this answer                                              │
│                              │    4   Port congestion fee                         $200             │
│                              │    6   Rush handling                               $400             │
│                              │ ┌─────────────────────────────────────────────────────────────┐     │
│                              │ │ Answer in your own words.                                   │     │
│                              │ └─────────────────────────────────────────────────────────────┘     │
│                              │ Your answer is added to the case as evidence, and lines             │
│                              │ 4 and 6 are rechecked. Nothing is paid or sent.  [ Reply ]          │
└──────────────────────────────┴─────────────────────────────────────────────────────────────────────┘
```

**W4. Контролёр, ревизия 2, вкладка Dispute** (полоса доверия и header как на W1, активен сегмент Controller, статус `Ready for approval`):
```
┌────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│ Billed            Agreed            At stake                 Waiting on answers                                                    │
│ $7,500      =     $6,800      +     $700   was $300      +   $0  ← серый ноль                                                      │
│ 2 to dispute · 0 waiting on answers · 6 cleared.    Checked before you pay.                         Revision 2, Oct 24, 2:05 PM PT │
├──────────────────────────────────────────────────────────────────────────────────────┬─────────────────────────────────────────────┤
│    #  Line                                 Rule                 Billed     Over by   │ What changed  History  Timeline  ▁Dispute▁  │
│ ┌──────────────────────────────────────────────────────────────────────────────────┐ │ ──────────────────────────────────────────  │
│ │ [■ To dispute]  2 lines                                       $5,200       +$700 │ │ Dispute draft to the forwarder              │
│ ├──────────────────────────────────────────────────────────────────────────────────┤ │ ✓ Drafted by the clerk, Oct 24, 2:09 PM     │
│ │┃ 1  Ocean freight 40HC Ningbo-Long Beach [R2 Rate mismatch]   $4,800       +$300 │ │ ✓ Approval requested, 2:10 PM               │
│ │┃    Billed $4,800. Agreed $4,500 (email, Sep 5, if            Finding confirmed  │ │ ● Controller approval   ← you               │
│ │┃    booked by Sep 10; booked Sep 8). Over by $300.                     by clerk  │ │ ○ Not sent. CargoOS never sends email.      │
│ │┃    Email, Sep 5   Chat, Sep 8                                                   │ │ [ Approve ]    Copy text                    │
│ │┃ 6  Rush handling                        [R9 Rejected]          $400       +$400 │ │ Approval only marks this draft approved.    │
│ │┃    Billed $400. The ops lead said this was not              Moved from Waiting  │ │ Nothing is sent.                            │
│ │┃    agreed on Oct 24: “nobody agreed to $400 rush                                │ │ ──────────────────────────────────────────  │
│ │┃    handling”. Over by $400.   Reply, Oct 24                                     │ │ To       Forwarder                          │
│ ├──────────────────────────────────────────────────────────────────────────────────┤ │ Subject  PCL-INV-1012: 2 charges            │
│ │ [┆? Waiting on answers┆]  0 lines                                  —           — │ │          disputed, $700                     │
│ │    Nothing waiting on answers. Answered by the ops lead, Oct 24, 2:05 PM.        │ │          ← пример: тему пишет Claude        │
│ │    See what changed                                                              │ │ Covers   Line 1 +$300   Line 6 +$400        │
│ ├──────────────────────────────────────────────────────────────────────────────────┤ │ Total    $700                               │
│ │ [✓ Cleared]  6 lines    Show 5 more                           $2,300           — │ │ ┌────────────────────────────────────────┐  │
│ │  4  Port congestion fee                  [R8 Approved]          $200           — │ │ │ Тело письма: Source Serif 4 на листе   │  │
│ │     Billed $200. Approved by the ops lead on Oct 24:               Just cleared  │ │ │ paper. Слова пишет Claude, все         │  │
│ │     “approved the $200 congestion fee on WhatsApp”.                              │ │ │ числа вставляет код.                   │  │
│ └──────────────────────────────────────────────────────────────────────────────────┘ │ └────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────────────────────┴─────────────────────────────────────────────┘

```
- Agreed $6,800 это итог спеки 7.2: 6,800 + 700 + 0 = 7,500.
- Строка 4 видна под свёрнутым Cleared, потому что она в `changes` последнего `recomputed`: её видит и контролёр, открывший страницу на ревизии 2.
- Approve стоит сразу под цепочкой статусов, тело письма ниже, иначе кнопка уходит за край. У клерка на её месте `Request approval` и `[🔒 Approve]` с `Requires controller` (5.12). Agree, Not an issue и Edit line контролёр не видит.

---

## 5. UX-спецификация по компонентам

Общие правила: одна primary-кнопка (`--link`) на область; модальных окон нет, всё inline или поверх инспектора; каждое действие человека пишет событие; disabled через `aria-disabled` с видимой причиной ([CSS-Tricks о disabled](https://css-tricks.com/?p=339960)); «·» только в count line. Клерк флагует, правит и готовит диспут; контролёр читает и одобряет; ops lead видит только инбокс.

### 5.1 Полоса доверия, header, role switcher
- **Полоса доверия** (24px, `bg-sunken text-ink`, 13px, граница `--rule`; тёмная полоса перетянула бы взгляд с At stake на дисклеймер). Слева `Synthetic data. Nothing is sent or paid automatically.`, справа `Agent permissions` (HoverCard) и тег `Demo: no login`. В Replay в начале сплошной тег `Replay` (`bg-ink text-white`) и `Replay. A recorded run of this case, not live extraction.`; цвет полосы тот же, Replay не статус, текст только если он правда.
- **`Agent permissions`:** `CargoOS agent` / `Can: read this case's documents, ask the ops lead one question and one follow-up, draft a dispute.` / `Cannot: send email, approve, pay, or change the invoice.` / `Line statuses are decided by rules, not by the model.` / `Can be wrong when a document is a scan or a charge was agreed by phone. Lines without written evidence wait for a person.` / `Demo: anyone can switch roles here. The server still refuses approval from any role but controller.`
- **Header** (48px): `CargoOS` (15/600), `Case.title` (17/600), мета 13px `--ink-2` из `shipmentRef` и `incoterm` и тег `Case.status` (`Open`, `Awaiting answer`, `Ready for approval`, `Approved`). Справа `Add evidence` (клерк) и role switcher.
- **Role switcher:** `View as` (13px `--ink-3`) и `ToggleGroup type="single"` 32px `Clerk | Controller | Ops lead`; активный сегмент `bg-ink text-white` (смена роли не действие, синяя заливка остаётся действиям); `onValueChange={(v) => v && setRole(v)}`, иначе сегмент можно «отжать»; счётчик 18px (у Ops lead открытые вопросы, у Controller драфты `pending_approval`); tooltip `Flags lines and drafts disputes. Can't approve.` / `Approves dispute drafts.` / `Answers questions about what was agreed.` с именем из сида, если оно есть. Роль в `localStorage` с откатом на клерка, `?role=ops_lead` один раз перезаписывает её (8.7); смена crossfade 150ms.

### 5.2 Overview: уравнение
- Все числа из `caseTotals()`. Член **`+ Waived by clerk $X`** только при X > 0 (X = `deltaCents` спорных строк с `false_positive`): по спеке такая строка выпадает из At stake, а Agreed берёт её `expectedCents`, и без этого члена уравнение после Not an issue не сходится. В dev-режиме `console.warn`, если `Billed ≠ Agreed + At stake + Waiting + Waived`.
- Подписи 13/500 `--ink-3`; Billed, Agreed, Waiting 22/500; **At stake 34/600 `--dispute`**; `=` и `+` 22/400 `--ink-3`; ноль `$0` в `--ink-3`.
- Третья строка: `1 to dispute · 2 waiting on answers · 5 cleared.` (14px `--ink-2`, числа скроллят к группе) и `Checked before you pay.` (`--ink-3`).
- Справа primary `Draft dispute` (только клерк; при At stake = $0 `aria-disabled` и `Nothing to dispute. At stake is $0.`; при существующем драфте secondary `Open dispute draft`) и `Revision 1, Oct 24, 1:30 PM PT`. После ревизии рядом с At stake `was $300`.
- **Recomputing** (`isRecomputing`): спиннер 14px и `Rechecking 8 lines with the ops lead's reply…` (или `…with the new evidence…`), числа на 60%, `Draft dispute` недоступна с `Wait for the recheck to finish.`, спиннер не короче 800ms.

### 5.3 Шапка колонок и группы
- Шапка 28px, 12/500 `--ink-3`: `#`, `Line`, `Rule`, `Billed`, `Over by`.
- Заголовок группы (36px, `--sunken`, граница `--frame`) это подитог: чип (3.4), `2 lines`, сумма Billed, `+$300` / `unconfirmed` / `—`. Порядок всегда To dispute, Waiting on answers, Cleared.
- Cleared свёрнут (`▸ Show`). Строки из `justMoved` (= `movedKeys(events)` ∪ отклонённые клерком в этой сессии ∪ `player.arrived`) видны под свёрнутым заголовком с тегом `Just cleared`, кнопка тогда `Show 5 more`. Так строку 4 видит и контролёр, открывший страницу на ревизии 2.
- Пустая группа: `Nothing to dispute.` / `Nothing waiting on answers.`, при отвеченном вопросе `Answered by the ops lead, Oct 24, 2:05 PM.` и `See what changed`.

### 5.4 Строка позиции
- Первая строка по `--cols`: полоса, номер (13px `--ink-3`), `Charge.description` (15/500, `…`), rule badge, Billed (15/600 `money`), дельта.
- Вторая: `Finding.explanation` (14/20 `--ink-2`) в колонках описания и правила, затем ссылки-источники. Под Billed и дельтой у клерка на To dispute и Waiting `Agree` и `Not an issue` (secondary 28px, 13px), у остальных теги; если нужны и кнопки, и тег, тег идёт третьей строкой.
- Why из движка для Scenario A (спека 6 и W1.4), по ним сверяй фикстуру и тесты: строка 1 `Billed $4,800. Agreed $4,500 (email, Sep 5, if booked by Sep 10; booked Sep 8). Over by $300.`; строки 4 и 6 до ответа `Billed $200. Nothing in the quote, emails or chats agrees to this charge.` (и `$400`); строка 4 после `Billed $200. Approved by the ops lead on Oct 24: “approved the $200 congestion fee on WhatsApp”.`; строка 6 после `Billed $400. The ops lead said this was not agreed on Oct 24: “nobody agreed to $400 rush handling”. Over by $400.` Лучший копирайт это просьба к `explain.ts`, не правка на фронте.
- Теги (12/500, `--link-tint` / `--link`): `Moved from Waiting`, `Just cleared`, `Finding confirmed by clerk`, `Edited by clerk` (`Charge.editedBy`), `Dismissed by clerk`.
- Состояния: hover `--hover`; selected `--link-tint`, номер `--link` 600, `aria-current="true"`; focus outline 2px `--link` внутрь; в плеере `data-focus`, затем `animate-moved`, остальные 0.55; dismissed: why `--ink-3`, тег и причина.
- `li`: `first:rounded-t-[5px] last:rounded-b-[5px]`; `ul`: `relative` (offset parent для `popLayout`). Клик открывает Source view, клик по ссылке открывает его на спане, `j`/`k` листают.

### 5.5 Source view
Раскладка в заметках к W2, шрифты по типу документа в 3.2; чат без зелёных пузырей (ни бренд-мимикрии, ни «чат-UI»). Над письмом `sender` и `sentAt` (13px `--ink-3`). Подпись у каждого документа: `Quote found word for word.` / `Quote found. Spacing differs from the document.` / `Quote not found in this document. Nothing is highlighted.` `Esc` возвращает фокус на строку и прежнюю вкладку.

### 5.6 Agree / Not an issue
- Только клерк, каждая строка To dispute и Waiting, кнопки видны всегда (спека: «per line»).
- **Agree** (`confirmed`, `finding_confirmed`) клерк может прочитать как «согласен оплатить»: tooltip `Agree with this finding. Keeps the line in the dispute.` (на Waiting `… The line keeps waiting on answers.`), после клика кнопку заменяет тег `Finding confirmed by clerk`, в History `Confirmed the finding on line 1 (+$300).`
- **Not an issue** (`false_positive`, `finding_dismissed`): строка за 280ms едет в `Just cleared` с тегом `Dismissed by clerk`. На To dispute сначала inline-поле `Why is this not an issue? Saved to case history.` (Enter / Esc): иначе контролёр скажет, что клерк просто списал. Tooltip `Moves the line to Cleared. At stake $300 → $0.` (тот же `caseTotals` с подставленным вердиктом). Toast `Line 1 moved to Cleared. Dismissed by clerk.` Undo нет: для отмены в спеке нет события (раздел 12). Без модалок, по принципу «approval fatigue» ([Anthropic](https://www.anthropic.com/research/claude-code-auto-mode)).
- Если новые доказательства изменили строку, движок сам сбрасывает вердикт и дописывает в why `Your earlier verdict was cleared because new evidence changed this line.`

### 5.7 Agreement timeline (вкладка `Timeline`)
Простой список фактов по `effectiveAt`: дата 56px, тип документа и факт одной строкой (`Ocean freight $4,800`, `PSS allowed by the quote`, `Booked Sep 8`), клик открывает Source view. Заменённый факт зачёркнут, за ним `replaced by Email, Sep 5`; последний узел `Question to the ops lead, waiting for a reply`. Вкладка по умолчанию; если её вырезали, по умолчанию What changed.

### 5.8 What changed
- `revisionsOf(events)`, новые сверху; старые свёрнуты в строку (`Revision 1. First check: 1 to dispute, 2 waiting on answers, 5 cleared.`).
- Карточка: `Revision 2`, `After the ops lead replied, Oct 24, 2:05 PM PT.`, `At stake $300 → $700`, `Waiting on answers $600 → $0` (снимок «до» у плеера).
- Запись на изменение с `from`: `Line 4: Waiting on answers → Cleared` (чипы), ниже `explanation` новой находки и ссылка из `causedByFactIds` (`Reply, Oct 24`), у To dispute `+$400`. Своё «because …» не сочиняем: текст движка уже содержит цитату. Тест W3.5: ровно две записи с цитатой ответа.
- Клик по записи выделяет строку. После проигрывания вкладка открывается сама; при правках клерка только точка `What changed •`.

### 5.9 Карточка вопроса
В Waiting под заголовком: белый фон, рамка 1px пунктиром `--waiting-bar`, радиус 4px, не выше 84px; `Question.text` Source Serif 4 14/20 `line-clamp-2`; одна строка меты `Asked the ops lead, Oct 24, 1:31 PM. $600 waiting.`, ссылки из `citations`, статус справа.

| Состояние | Что видно |
|---|---|
| `open` | `Open`; у клерка ссылка `View as Ops lead` |
| `open` + `followupOf` | `Follow-up question`, `The first answer didn't settle this: “I think we discussed something?”` (прошлый ответ), `CargoOS asks one follow-up, then stops.` |
| `answered` | `Answered`, `Answer: “approved the $200 congestion fee on WhatsApp, nobody agreed to $400 rush handling”`; после плеера карточка сворачивается в строку пустой группы |
| `closed_unresolved` | `Couldn't settle this from the answers. Lines 4 and 6 stay waiting.` (номера из `findingKeys`) |

### 5.10 Инбокс ops lead
Раскладка на W3. Кнопка `Reply`, не «Send»: ничего не отправляется. Ответ сохраняется документом `reply` и запускает пайплайн; toast `Reply added. Rechecking lines 4 and 6.`, после пересчёта `Rechecked. Line 4 cleared. Line 6 moved to To dispute.` (из `changes`) и `View case as Clerk`. Follow-up приходит новым открытым вопросом с пометкой `Follow-up`, `closed_unresolved` уходит в Answered. Пусто: `No open questions.` / `Answered questions stay in the case history.`

### 5.11 Add evidence и line editor (must-ship)
- **Add evidence** поверх инспектора: `Email | Chat | Other` (DocType `email` / `chat` / `other`), `Title (optional)`, textarea на 8 строк `Paste the email, chat or note exactly as written.`, подсказка `Quotes are kept word for word. Nothing is paraphrased.`, primary `Add and recheck`. Затем `Reading with Claude… 4s` и итог: `Added “Chat, Oct 24”. 1 line changed.` + `See what changed`, или `No agreed amounts found in this text. Saved to the case anyway.`, или баннер extraction failed. Пересчёт плеер проигрывает полностью.
- **Line editor:** `Edit line` в шапке Source view (клерк). Поля на месте строки: описание и сумма (`tabular-nums`, вправо), `Save` (Enter), `Cancel` (Esc), несохранённое на `--link-tint`. Сумма через `parseMoneyToCents` Дева (без `$` и пробелов), ошибка `Enter an amount like 4,800.` Затем `charge_edited`, пересчёт, быстрый переезд (`expectQuick`), тег `Edited by clerk`.

### 5.12 Dispute draft и approval gate (вкладка `Dispute`)
- `Draft dispute` вызывает действие с `draftDispute` Арпана (`Drafting with Claude…`), затем открывается вкладка.
- Сверху цепочка по `Draft.status`: `Drafted by the clerk, 2:09 PM` → `Approval requested, 2:10 PM` → `Controller approval` (у контролёра `you`) → `Not sent. CargoOS never sends email.`; при `approved` `Approved by the controller, Oct 24, 2:12 PM. Not sent.` в рамке `--cleared`. `rejected` только показываем: ни кнопки, ни события для отклонения в спеке нет.
- Сразу под цепочкой кнопки (иначе Approve уходит за край). Клерк: primary `Request approval` (toast `Approval requested.` / `The controller will see it. Nothing has been sent.`) и `[🔒 Approve]` с `aria-disabled`, видимым `Requires controller` и строкой `The person who prepares a dispute can't approve it.`; клик зовёт ту же мутацию с `role: "clerk"`, сервер отказывает и сам пишет `approval_blocked` (спека 5.2, 10.5), toast `Approval blocked. Requires controller.` / `Logged in case history.` Контролёр: primary `Approve` (toast `Dispute draft approved.` / `Nothing was sent.`), подвал `Approval only marks this draft approved. Nothing is sent.`
- Ниже `To` (`Forwarder` из `Draft.to`), `Subject`, `Covers` (`Line 1 +$300`, `Line 6 +$400`), `Total` из `totalDisputedCents`; при отклонённых спорных строках `Not disputed: line 6, $400, waived by the clerk: “…”`. Тело на листе, Source Serif 4: слова от Claude, числа от кода. `Copy text`. Кнопки Send нет нигде.

### 5.13 Case history (вкладка `History`)
Новые сверху, разделители по дням: время 13px `--ink-3`, актор 14/600 (имя из сида и роль, `Sam Bayline (ops lead)`, или `Clerk`; `system` = `CargoOS rules`, `agent` = `CargoOS agent`), предложение, чип типа (Plex Mono 12px на `--sunken`, у `approval_blocked` текст `--dispute`). Типы ровно из спеки; поля payload, кроме `changes`, согласуй с командой (без них предложение короче).

| `type` | Предложение | `type` | Предложение |
|---|---|---|---|
| `doc_added` | `Added Invoice PCL-INV-1012.` | `reply_received` | `Replied to the question about lines 4 and 6.` |
| `facts_extracted` | `Read Email, Sep 5 with Claude.` | `finding_confirmed` | `Confirmed the finding on line 1 (+$300).` |
| `extraction_failed` | `Couldn't read part of Chat, Sep 28.` | `finding_dismissed` | `Dismissed line 6 as not an issue: “…”.` |
| `charge_edited` | `Edited line 6: $400 → $350.` | `draft_created` | `Drafted a dispute to the forwarder: $700, lines 1 and 6.` |
| `recomputed` | `Rechecked 8 lines. 2 moved.` | `approval_requested` | `Requested controller approval for the dispute draft.` |
| `question_asked` | `Asked the ops lead about lines 4 and 6.` | `approval_blocked` | `Tried to approve the dispute draft. Blocked: requires controller.` |
| | | `approved` | `Approved the dispute draft. Nothing was sent.` |

### 5.14 Системные состояния
- **Skeleton** (он же `fallback` у `<Suspense>`): полоса доверия и header настоящие, остальное по геометрии экрана на `--sunken`, подпись `Opening Bayline 40HC Ningbo-LB…`; при появлении данных ничего не сдвигается.
- **Extraction failed:** баннер между уравнением и списком, `--waiting-tint`, текст `--waiting`, `TriangleAlert`, без полосы слева: `Couldn't read part of this document; affected lines are waiting on answers.` (спека), документ из payload, если есть, и `Paste the text instead` (Add evidence с типом Chat). Янтарный честен: строки действительно уходят в Waiting.
- **Empty case:** `No invoice in this case yet.` / `Add the forwarder's invoice and the quote to start checking.`, primary `Load Scenario A` (reset Пранава), secondary `Paste your own`.

### 5.15 Toasts, клавиатура, dev bar
- Toasts: sonner `position="bottom-right"` (по центру снизу закрыли бы заголовок Cleared), фон `--ink`, белый текст. Только Not an issue, approval blocked / requested / approved, reply added, evidence added, copy.
- Клавиатура: только `j`, `k`, `Esc`, без `<kbd>`-подсказок (случайный `x` в живом демо двигал бы деньги).
- Dev bar (`?dev=1`, слева снизу, фон `--ink`): `Rev 1 | Rev 2 | Play reply | Failed | Empty | Loading | Replay | Gray`. `Play reply` держит recomputing 1200ms и переключает на ревизию 2.

---

## 6. Демо-момент

### 6.1 Хореография
Темп с ответа ops lead (спека 11, 1:20–1:35): сначала причина, потом одно движение за раз, потом объяснение. T от момента, когда Convex отдал ревизию 2.

| Время | Что происходит | Куда смотрит зал |
|---|---|---|
| −Δ … 0 (Claude читает ответ) | Карточка вопроса `Open → Answered`, цитата ответа с `<mark>`; в уравнении спиннер `Rechecking 8 lines with the ops lead's reply…`, числа на 60% | Причина |
| T+0–200 | Source view закрывается, если открыт; числа к 100%, незатронутые строки гаснут до 0.55 | — |
| T+400 | Строка 4: outline `--link` и wash | Строка 4 |
| T+700–1260 | **Строка 4 едет** из Waiting в `Just cleared` (560ms, ease `[0.2,0,0,1]`, без пружины); список прокручивается к ней, если она ниже края | Движение |
| T+1260–1860 | Agreed $6,600 → $6,800, Waiting $600 → $400 (600ms), Cleared 5 → 6 | Числа |
| T+2360 | Строка 6: outline и wash | Строка 6 |
| T+2660–3220 | **Строка 6 едет** из Waiting в To dispute, появляется `+$400`; Waiting пустеет: `Nothing waiting on answers.` | Движение |
| T+3220–3820 | At stake $300 → **$700**, Waiting → **$0** (серым), рядом `was $300` | Главное число |
| T+3820 | Строки к 100%, карточка вопроса сворачивается, инспектор открывает What changed (карточка въезжает: translateY −8px → 0 и opacity, 240ms). Скрытый `aria-live="polite"`: `Revision 2. Line 4 moved to Cleared. Line 6 moved to To dispute. At stake $700.` | Объяснение |
| T+4000…6200 | Wash гаснет (2400ms, «yellow fade» из [37signals](https://signalvnoise.com/archives/000558), только синий); теги `Moved from Waiting` и `Just cleared` до следующей ревизии | Следующее действие |

Reduced motion (`MotionConfig reducedMotion="user"`): переезды мгновенные, тайминги × 0.4, outline, wash, теги и What changed остаются. Собственные правки клерка (Not an issue, Edit line): один переезд за 280ms без затемнения, What changed получает только точку.

### 6.2 Реализация (код в 8.5)
1. `useQuery` перерисовывает всё в одном кадре ([Convex React](https://docs.convex.dev/client/react)), поэтому плеер держит отображаемые строки отдельно от данных и сравнивает группы **на экране** с новыми по `Finding.key` (равен `chargeId`, стабилен между ревизиями). От порядка `changes` в payload это не зависит, и собственный dismiss клерка второй раз не анимируется. Уравнение получает `moneyRows` после посадки и перебалансируется шаг за шагом.
2. Один плоский `<ul>`, где заголовки, карточка вопроса, пустые состояния и строки соседи с `motion.li layout="position"` и стабильным `key`: переезд это переупорядочивание в одном родителе (FLIP, [Motion layout](https://motion.dev/docs/react-layout-animations)), без `layoutId` и без `overflow-hidden` у предков.
3. Числа: `@number-flow/react` с `NumberFlowGroup` ([NumberFlow](https://number-flow.barvian.me)); в 0.6.2 есть `transformTiming`, `spinTiming`, `respectMotionPreference`.
4. Плеер в провайдере кейса с `active = role !== "ops_lead"`: ревизия, пришедшая, пока открыт инбокс, проиграется при возврате (демо в одном окне). `Show again` перезапускает сценарий от снимка «до»; словом `Replay` не называем, оно занято fallback-режимом.

### 6.3 Живое демо на двух устройствах
- Пранав играет ops lead (W4.10): телефон с `/?role=ops_lead` на production-URL, ответ набирает сам, без заготовки. Ноутбук презентера на `/?role=clerk` меняется сам: реактивность Convex для трека Convex, разделение ролей для Hyphenate.
- **Репетиция до 23:00:** минимум один полный прогон пути спеки 3 на деплое ($300 → ответ → $700 → клерк заблокирован → контролёр одобряет), затем `reset demo`.
- Без сети или без Claude: Replay Пранава (W4.7), тег `Replay` горит постоянно.

### 6.4 Видео и демо-ноутбук
- Видео открывается на моменте 1:20 (спека): 1280×720 CSS px (1080p с масштабом 150%, зум 100%), курсор не на строках, старт за 2 секунды до отправки ответа, телефон в PiP, кадр на What changed около 1.5 секунды, зум на At stake при монтаже; увеличенный курсор macOS, Do Not Disturb, чистый профиль Chrome, один таб.
- Ноутбук: Chrome на весь экран (⌃⌘F), тулбар скрыт, зум 100%; macOS Settings → Accessibility → Display → **Reduce motion OFF** (иначе строки прыгают, а не едут); Do Not Disturb; `reset demo` перед прогоном; запасной хотспот для телефона.

---

## 7. Anti-AI чеклист

| Признак «сделано AI» | Что делаем вместо |
|---|---|
| Ряд KPI-карточек с иконками в кружках | Одно уравнение, которое сходится |
| `rounded-2xl`, мягкие тени, карточки в карточках | Одна поверхность и разделители; радиусы 6/4/3px; тени только у парящего |
| Inter, Geist; mono для мелких data-лейблов | Public Sans и Source Serif 4; Plex Mono только внутри бланков и в чипе типа события |
| Индиго, фиолет, градиенты, glow; крем + терракота; чёрный + неон | Чернила на нейтральном сером `#F4F5F7`, три статусных цвета, синий карандаш; только светлая тема |
| ✨, «AI-powered», робот-аватар, проценты уверенности | `Extracted by Claude` у фактов, `Decided by rule R2` у решений, дословная цитата |
| Чат как главный интерфейс, typewriter-стриминг решений | Рабочий список; агент задаёт один вопрос конкретному человеку; решения появляются после пересчёта |
| Иконка у каждого лейбла, суп бейджей, ALL CAPS, «01 / 02 / 03», «·» везде, `border-l-4` | Иконки только у статуса, источника и замка; sentence case; «·» только в count line; статус кодируют заливка и узор полосы |
| Fade-up на секциях, hover-lift, bounce, тикеры везде | Одно авторское движение; NumberFlow только в уравнении |
| «Submit», «Something went wrong», выдуманные метрики, Acme и lorem | Глаголы из спеки; ошибки называют документ и последствие; документы Scenario A дословно (Pacific Crest Logistics, Q-PCL-0902, PCL-INV-1012) |
| Дефолтный shadcn: zinc, ореол `ring/50`, Dialog на всё, белый Sonner | Своя тема, чёткий ring `--link`, inline-панели, тёмные тосты справа снизу |
| Disabled без причины, спиннер без подписи, «No data», нетронутый focus | `aria-disabled` с причиной; подписанные состояния; пустые состояния с действием; focus, selection, scrollbar на токенах |

---

## 8. Тулкит

### 8.1 Версии (npm, проверены агентами 24 октября)
Свежие на 24 октября: next 16.4.3 (вышел 23 окт; если у Пранава что-то странно, откат на 16.4.2), react 19.3.1, tailwindcss 4.3.3, convex 1.48.0, @playwright/test 1.64.1; что реально стоит, смотри в `package.json`. Просим: **motion 14.3.1** (пин: в [14.3.1](https://github.com/motiondivision/motion/blob/main/CHANGELOG.md) исправлены `popLayout` children и layout-анимации после mount, на них держится плеер), @number-flow/react 0.6.2, компоненты shadcn CLI 4.22.1 (sonner 2.0.8 приходит с ними). **Не ставим:** nuqs, react-hotkeys-hook, cmdk, react-resizable-panels, графики, framer-motion отдельно.

### 8.2 Сообщение Пранаву (отправить в 15:30)
Зависимости по спеке добавляет только Пранав; корневой `CLAUDE.md`, `scripts/` и `package.json` его зона.
```text
Pranav, frontend asks (W3), when you have 5 min:
1) Deps, pinned: npm i -E motion@14.3.1 @number-flow/react@0.6.2
   and: npx shadcn@4.22.1 add tabs tooltip hover-card textarea input toggle-group sonner skeleton
2) One line in root CLAUDE.md: "Frontend rules: app/CLAUDE.md. Read it before editing app/ or components/."
3) I'm adding lib/ui/ (frontend-only view-model over lib/types.ts, no new shared types). OK?
4) Is fixtures/scenarioA.ui.json in main? Names of the case query and of the mutations
   (confirm, dismiss, editCharge, addDocument, reply, draftDispute, requestApproval, approve)?
5) OK to add scripts/slop-lint.sh + scripts/shots.sh and package.json scripts lint:slop, shots?
```

### 8.3 Темизация поверх того, что сделал Пранав
Проект уже создан (Next.js + Convex + shadcn с нулевого часа): `create-next-app` и `shadcn init` не запускаем.
1. Посмотри `components.json` (style, путь к css), `app/layout.tsx` (обёртку Convex не ломай), `app/globals.css`, `components/ui`.
2. `app/layout.tsx`: убери Geist, подключи `Public_Sans` (`--font-public-sans`), `Source_Serif_4` (`axes: ["opsz"]`, `--font-source-serif`), `IBM_Plex_Mono` (`weight: ["400"]`, `--font-plex-mono`), все `subsets: ["latin"], display: "swap"`, переменные на `<html>`; в `body` `<Toaster position="bottom-right" />`.
3. `app/globals.css`: блок из 3.11.
4. `components/ui` (зона фронта; паттерны стиля Nova, в другом стиле ищи такие же):
   - фокус не удаляй, а сделай чётким: `ring-ring/50` → `ring-ring`, `ring-3` → `ring-2`; иначе `outline-none` в компонентах побеждает базовый outline;
   - Tabs: `<TabsList variant="line">`, на триггере `data-active:border-b-2 data-active:border-b-ink data-active:text-ink` (активный таб Nova помечен `shadow-sm`, а токены тень обнуляют);
   - Toggle: `data-[state=on]:bg-ink data-[state=on]:text-white` (у Nova `bg-muted`, около 1.17:1 к белому); Tooltip: `text-meta` вместо 12px;
   - Button `secondary` = `bg-surface border border-control text-ink hover:bg-hover`;
   - `sonner.tsx`: без `next-themes`, `theme="light"`, `toastOptions={{ classNames: { toast: "bg-ink text-white border-0 rounded-md shadow-lg font-sans text-body", description: "text-white/80" } }}`.
5. `rg -n "rounded-full|shadow-|backdrop" components/ui`: что не гасят токены, правь руками.

### 8.4 Адаптер: типы спеки → UI (`lib/ui/view-model.ts`)
```ts
import type { Case, CaseEvent, Charge, Draft, Fact, Finding, Question, Reply, RuleId,
  SourceDoc, SourceSpan, Status } from "@/lib/types";
import { caseTotals } from "@/lib/engine/totals";            // путь и имена полей сверь с Девом (W1.6)

// CaseState спеки 5.3 + replies, drafts, events. Ту же форму отдают фикстура и запрос Convex.
export type CaseInput = { case: Case; documents: SourceDoc[]; facts: Fact[]; charges: Charge[]; findings: Finding[];
  questions: Question[]; replies: Reply[]; drafts: Draft[]; events: CaseEvent[] };
export type UiGroup = "dispute" | "waiting" | "cleared";
export const DISCREPANCY: Status[] = ["potential_discrepancy", "invoice_noncompliance"];

export function uiGroup(f: Pick<Finding, "status" | "userVerdict">): UiGroup {
  if (f.userVerdict === "false_positive") return "cleared";            // + тег "Dismissed by clerk"
  if (DISCREPANCY.includes(f.status)) return "dispute";
  return f.status === "needs_review" ? "waiting" : "cleared";          // supported, outside_quote_scope
}

// Названия и определения: предложения фронта, подтвердить с Девом
export const RULES: Record<RuleId, { code: string; name: string; def: string }> = {
  R0_MATCH: { code: "R0", name: "Matches agreed rate", def: "Billed is at or below the latest agreed rate whose conditions were met." },
  R1_DUPLICATE: { code: "R1", name: "Duplicate", def: "An earlier line on this invoice has the same charge and amount." },
  R2_RATE_MISMATCH: { code: "R2", name: "Rate mismatch", def: "Billed is above the latest agreed rate whose conditions were met." },
  R3_MATH_CURRENCY: { code: "R3", name: "Currency", def: "Billed in another currency, and no exchange rate was agreed." },
  R4_PERMITTED_SURCHARGE: { code: "R4", name: "Allowed by quote", def: "A quote clause allows this surcharge." },
  R5_EXCLUDED_SCOPE: { code: "R5", name: "Outside quote scope", def: "The quote excludes this charge but says it is billable." },
  R6_NO_EVIDENCE: { code: "R6", name: "No evidence", def: "Nothing in the quote, emails or chats agrees to this charge." },
  R7_CONFLICT: { code: "R7", name: "Sources disagree", def: "Two sources stated at the same time disagree." },
  R8_APPROVED: { code: "R8", name: "Approved", def: "Someone on the buyer side approved this charge." },
  R9_REJECTED: { code: "R9", name: "Rejected", def: "Someone on the buyer side said this charge was not agreed." },
};

// spanLabel(span, doc), локаторы спеки: "line 4" → "Invoice, line 4"; "msg 2026-09-08 09:12" → "Chat, Sep 8";
// "email body", "page 1" и прочие → тип и дата документа: "Email, Sep 5", "Quote, Sep 2"
// (Intl.DateTimeFormat month: "short", day: "numeric", timeZone: "America/Los_Angeles")

export type RowVM = { key: string; lineNo: number; description: string; group: UiGroup; rule: (typeof RULES)[RuleId];
  why: string; links: { span: SourceSpan; label: string }[]; finding: Finding; charge?: Charge };
export function toRows(x: CaseInput): RowVM[] {
  const charges = new Map(x.charges.map((c) => [c._id, c])), docs = new Map(x.documents.map((d) => [d._id, d]));
  return x.findings.filter((f) => f.revision === x.case.revision).sort((a, b) => a.lineNo - b.lineNo).map((f) => {
    const ch = charges.get(f.chargeId);
    const own = (s: SourceSpan) => !!ch && s.docId === ch.span.docId && s.quote === ch.span.quote;
    return { key: f.key, lineNo: f.lineNo, description: ch?.description ?? `Line ${f.lineNo}`, group: uiGroup(f),
      rule: RULES[f.ruleId], why: f.explanation,                       // как есть, никогда не собираем
      links: f.evidence.filter((s) => !own(s)).map((s) => ({ span: s, label: spanLabel(s, docs.get(s.docId)) })),
      finding: f, charge: ch };
  });
}

// Итоги только из caseTotals; Waived = отклонённые спорные строки, которых нет ни в At stake, ни в Waiting
export function totalsOf(findings: Finding[]) {
  const waivedCents = findings.filter((f) => f.userVerdict === "false_positive" && DISCREPANCY.includes(f.status))
    .reduce((s, f) => s + (f.deltaCents ?? 0), 0);
  return { ...caseTotals(findings), waivedCents };
}
```
Ещё четыре хелпера там же:
- `primarySpan(row, facts)`: спан первого факта из `factIds`, который не `booking_date` и не заменён; иначе `links[0]`, иначе `evidence[0]`.
- `isRecomputing(events)`: последний `doc_added` / `reply_received` / `charge_edited` / `facts_extracted` новее последнего `recomputed` / `extraction_failed`.
- `revisionsOf(events)`: события `recomputed`, новые сверху, `payload.changes as FindingChange[]`.
- `movedKeys(events)`: ключи из `changes` последнего `recomputed` с `from` и сменой группы. Без проверки `from` в «Just cleared» попала бы вся ревизия 1.

Тест W3.2: ревизия 1 даёт 1 / 2 / 5 и $300, ревизия 2 даёт 2 / 0 / 6 и $700.

### 8.5 Revision player
```ts
// components/case/use-revision-player.ts
"use client";
import { useEffect, useRef, useState } from "react";
import type { RowVM, UiGroup } from "@/lib/ui/view-model";

const T = { lead: 400, ring: 300, move: 560, hold: 1100, settle: 600, quick: 280 };   // мс, темп демо
const diff = (shown: RowVM[], next: RowVM[]) => {                     // группы на экране против новых
  const m = new Map<string, UiGroup>(shown.map((r) => [r.key, r.group]));
  return next.filter((r) => m.has(r.key) && m.get(r.key) !== r.group).sort((a, b) => a.lineNo - b.lineNo);
};

export function useRevisionPlayer(rows: RowVM[] | undefined, revision: number | undefined, active: boolean,
  on: { start(): void; done(quick: boolean): void }) {               // start: закрыть Source view; done: What changed
  const [shownRows, setShown] = useState<RowVM[]>(rows ?? []);
  const [moneyRows, setMoney] = useState<RowVM[]>(rows ?? []);        // уравнение отстаёт на перелёт
  const [focusKey, setFocus] = useState<string | null>(null);
  const [arrived, setArrived] = useState<ReadonlySet<string>>(new Set());
  const [playing, setPlaying] = useState(false);
  const shown = useRef<RowVM[]>([]), before = useRef<RowVM[]>([]), timers = useRef<number[]>([]);
  const shownRev = useRef<number | undefined>(undefined), quickNext = useRef(false);
  const latest = useRef(rows); latest.current = rows;
  const show = (r: RowVM[]) => { shown.current = r; setShown(r); };
  const stop = () => { timers.current.forEach(clearTimeout); timers.current = []; };

  function play(from: RowVM[], next: RowVM[], quick: boolean) {
    stop();
    const steps = diff(from, next);
    if (!steps.length) { show(next); setMoney(next); return; }
    const k = matchMedia("(prefers-reduced-motion: reduce)").matches ? 0.4 : 1;
    const at = (ms: number, fn: () => void) => { timers.current.push(window.setTimeout(fn, ms * k)); };
    let cur = from, t = quick ? 0 : T.lead;
    before.current = from; show(from); setMoney(from); setArrived(new Set());
    if (!quick) { setPlaying(true); on.start(); }
    for (const s of steps) {
      if (!quick) { at(t, () => setFocus(s.key)); t += T.ring; }     // outline + wash
      at(t, () => { cur = cur.map((r) => (r.key === s.key ? s : r)); show(cur); setArrived((a) => new Set(a).add(s.key)); });
      t += quick ? T.quick : T.move;
      at(t, () => setMoney(cur));                                     // уравнение после посадки
      if (!quick) t += T.hold;
    }
    at(quick ? t : t - T.hold + T.settle, () => {                     // финал: самые свежие данные
      const fresh = latest.current ?? next; show(fresh); setMoney(fresh);
      setFocus(null); setPlaying(false); timers.current = []; on.done(quick);
    });
  }

  useEffect(() => {
    if (!rows || revision === undefined) return;
    if (shownRev.current === undefined) { shownRev.current = revision; show(rows); setMoney(rows); return; }
    if (timers.current.length) return;                                // идёт проигрывание: финал возьмёт latest
    const newRev = revision !== shownRev.current;
    if (newRev && !active) return;                                    // окно в инбоксе: проиграем при возврате
    shownRev.current = revision;
    play(shown.current, rows, !newRev || quickNext.current);         // та же ревизия = своё действие клерка
    quickNext.current = false;
  }, [rows, revision, active]); // eslint-disable-line react-hooks/exhaustive-deps
  useEffect(() => stop, []);

  return { rows: shownRows, moneyRows, focusKey, arrived, playing,
    expectQuick: () => { quickNext.current = true; },                // line editor вызывает перед мутацией
    showAgain: () => { if (before.current.length && latest.current) play(before.current, latest.current, false); } };
}
```
**Список** (`components/case/line-list.tsx`): `buildItems(rows, justMoved, clearedOpen, hasQuestion)` даёт один плоский массив (по группам заголовок, карточка вопроса в Waiting, пустое состояние кроме Cleared, строки; у свёрнутого Cleared только `justMoved`). `motion.li layout="position"` с переходом 0.56s `[0.2, 0, 0, 1]` под `AnimatePresence mode="popLayout"`, классы `row-outline relative bg-surface first:rounded-t-[5px] last:rounded-b-[5px] data-[focus]:bg-link-tint data-[focus]:outline-link data-[arrived]:animate-moved`; обёртка `motion.div layoutScroll data-list-scroll`, всё в `MotionConfig reducedMotion="user"`.

Итоги: `useMemo(() => totalsOf(p.moneyRows.map((r) => r.finding)))`. В уравнении `<NumberFlowGroup>`, у каждой фигуры `<NumberFlow value={cents / 100} locales="en-US" format={{ style: "currency", currency: "USD", maximumFractionDigits: 0 }} transformTiming={{ duration: 600, easing: "cubic-bezier(0.2,0,0,1)" }} spinTiming={...} />`. Приземлившийся `li` ниже края `[data-list-scroll]`: выставь `scrollTop` контейнера, страницу не трогай.

### 8.6 Цитата внутри полного документа
```tsx
// components/source/document-text.tsx (фрагменты)
const esc = (s: string) => s.replace(/[.*+?^${}()|[\]\\]/g, "\\$&");
// Точная подстрока, затем с нормализацией пробелов, как validateSpans (W2.2). Регистр и пунктуацию не трогаем.
export function findSpan(text: string, quote: string) {
  const i = text.indexOf(quote);
  if (i >= 0) return { start: i, end: i + quote.length, exact: true };
  const words = quote.trim().split(/\s+/).filter(Boolean);
  const m = words.length ? new RegExp(words.map(esc).join("\\s+")).exec(text) : null;
  return m ? { start: m.index, end: m.index + m[0].length, exact: false } : null;
}

type Hit = { id: string; start: number; end: number };                // id = spanId(span), hits отсортированы
function marked(str: string, at: number, hits: Hit[], refs: Map<string, HTMLElement>): ReactNode[] {
  const out: ReactNode[] = []; let pos = at;
  for (const h of hits) {
    const a = Math.max(h.start, pos), b = Math.min(h.end, at + str.length);
    if (a >= b) continue;
    if (a > pos) out.push(str.slice(pos - at, a - at));
    out.push(<mark key={`${h.id}@${a}`} ref={a === h.start ? (el) => { if (el) refs.set(h.id, el); } : undefined}>
      {str.slice(a - at, b - at)}</mark>);
    pos = b;
  }
  if (pos < at + str.length) out.push(str.slice(pos - at));
  return out;
}

// WhatsApp export: "9/28/26, 10:02 AM - Derek PCL: heads up, …"; \s ловит и U+202F перед AM/PM
const CHAT = /^(\d{1,2})\/(\d{1,2})\/(\d{2,4}), (\d{1,2}:\d{2})\s?([AP]M) - ([^:]+): /;
```
- **Рендер:** quote / freight_invoice / proforma в `<pre className="relative overflow-x-auto whitespace-pre font-mono text-typed">`; email, reply, other в `<div className="whitespace-pre-wrap font-serif text-doc">`; chat режется по `\n` с бегущим офсетом: совпавшая с `CHAT` строка это сетка `[7.5rem_1fr]` (время `Sep 28, 10:02 AM` в `text-meta tabular-nums text-ink-3`, отправитель 600, `marked(текст, start + m[0].length, …)`), строка без префикса продолжает сообщение.
- **Скролл к цитате:** `box.scrollTo({ top: y − box.clientHeight / 3 })`, где `y` это смещение `<mark>` внутри области (`behavior: "auto"` при reduced motion); в `pre` ещё `scrollLeft = el.offsetLeft − 16`. Подпись дословности берёт `exact`, `null` даёт `Quote not found…` без подсветки.

### 8.7 Слой данных, роль, клавиши
**Провайдер** `components/case/case-data.tsx` отдаёт `{ input?: CaseInput; actions: CaseActions; mode }`.
- **Fixture** (`?fixture=1` или `NEXT_PUBLIC_DATA=fixture`): `fixtures/scenarioA.ui.json` Пранава (W3.1; ключи сверь с его файлом). Dev-переключатель ставит `case.revision`, обрезает события по `recomputed` этой ревизии, `reply` и ответные факты только в ревизии 2, вопрос в ревизии 1 `open`; действия меняют локальную копию. Опаздывает файл: напиши его сам в форме спеки (типы 5.1, документы 7.1 дословно, ожидания 7.2).
- **Live:** `useStableQuery(api.<запрос Пранава>, { caseId })` (держит последнее значение, пока `useQuery` на миг отдаёт `undefined`, чтобы не мигал skeleton) и `useMutation` на каждое действие, `role` в аргументах всегда. Optimistic updates не нужны: смена группы в той же ревизии и так едет быстрым переездом. Флаг фикстуры остаётся (W3.8).
```ts
export type CaseActions = {
  confirm(key: string): Promise<void>; dismiss(key: string, reason?: string): Promise<void>;
  editCharge(chargeId: string, p: { description?: string; amountCents?: number }): Promise<void>;
  addEvidence(p: { type: "email" | "chat" | "other"; text: string; title?: string }): Promise<void>;
  reply(questionId: string, text: string): Promise<void>; draftDispute(): Promise<void>;
  requestApproval(draftId: string): Promise<void>;
  approve(draftId: string): Promise<{ ok: boolean }>;   // клерк тоже зовёт: сервер проверяет роль и сам пишет approval_blocked
};
```
**Роль** (`lib/ui/role.ts`): `useSyncExternalStore` поверх `localStorage["cargoos.role"]`; чтение и запись в `try/catch`, при ошибке или чужом значении `"clerk"`, серверный снимок `"clerk"` (нет hydration mismatch). Если в URL валидный `?role=`, хук один раз пишет его в `localStorage` и убирает параметр через `router.replace`, так что дальше работает переключатель. `useSearchParams` на пререндеренной странице требует `<Suspense>`: оберни страницу в `<Suspense fallback={<CaseSkeleton />}>`, это и есть skeleton.

**Клавиши:** один `keydown` на `window`; выходим при модификаторах и если `e.target.closest("input, textarea, select, [contenteditable='true']")`; `j`/`k` выбирают строку, `Escape` закрывает Source view. **Approve:** у ops lead кнопки нет; у клерка `secondary`, `aria-disabled`, `aria-describedby` на видимый `Requires controller`, иконка `Lock`, клик всё равно зовёт `actions.approve`, toast по ответу (5.12).

### 8.8 Скрипты контроля
```bash
# scripts/slop-lint.sh: каждое срабатывание разобрать руками
P="app components lib/ui"; hits=0
chk() { rg -n --glob '!components/ui/**' "$@" $P && hits=1; }
chk 'bg-gradient|bg-linear|bg-radial|bg-conic|bg-clip-text|backdrop-blur|drop-shadow'
chk 'shadow-(sm|md|lg|xl|2xl)|rounded-(xl|2xl|3xl|full)'
chk '\b(bg|text|border|ring|fill|stroke|from|to|via)-(slate|gray|zinc|neutral|stone|red|orange|amber|yellow|lime|green|emerald|teal|cyan|sky|blue|indigo|violet|purple|fuchsia|pink|rose)-[0-9]{2,3}\b'
chk --glob '!app/globals.css' '#[0-9a-fA-F]{3,8}\b'
chk 'uppercase|tracking-(wide|wider|widest)|transition-all|animate-(bounce|ping)'
chk 'Sparkles|Wand|\bBot\b|BrainCircuit|\bInter\b|Geist|nuqs|react-hotkeys-hook'
chk --glob '!components/source/**' --glob '!components/history/**' 'font-mono'
chk '[✓⊗✕]'                                          # только SVG-глифы
chk -i 'ai-powered|\bsmart\b|magic|seamless|insights|lorem|acme|john doe|welcome back|something went wrong'
chk '>\s*Submit\s*<|"Submit"'                         # с учётом регистра, onSubmit не ловим
rg -nP '[\x{1F300}-\x{1FAFF}\x{2728}]' $P && hits=1
exit $hits
```
```bash
# scripts/shots.sh: только на трёх чекпойнтах
mkdir -p shots
for vp in "1280, 640" "1280, 720"; do for q in "rev=1" "rev=2" "role=ops_lead" "role=controller&rev=2&tab=dispute"; do
  npx playwright screenshot --viewport-size "$vp" --wait-for-timeout 800 \
    "http://localhost:3000/?fixture=1&dev=1&$q" "shots/${vp/, /x}_${q//[&=]/_}.png"
done; done
```

---

## 9. Референсы (по компонентам)

Ссылки собраны исследователями через Mobbin и Refero MCP. Берём поведение и структуру текста, не скин.

| Компонент | Ссылка | Что берём |
|---|---|---|
| Полоса доверия | [Stripe Test mode bar](https://mobbin.com/screens/d7461782-27a7-41e9-aa99-ac528bcd4fca) | Режим в рамке экрана, полоса на всю ширину |
| Case title | [Stripe invoice detail](https://refero.design/pages/b176825e-b144-431f-aa4f-8dcf47104c65) | Заголовок, заголовки колонок, суммы вправо |
| Role switcher | [Base44 «acting as»](https://mobbin.com/screens/74ff85dd-dbad-4f03-8248-294e40499dff), [Mercury header](https://refero.design/pages/c32485f8-bc77-4aa7-b7aa-40d96f779bb3) | Роль видна всегда, сегменты вместо dropdown |
| Уравнение | [YNAB balance equation](https://mobbin.com/screens/7b0f7603-db2f-4274-9f9d-bcbb9d73d993), [Mercury Accounts](https://refero.design/pages/c7f36c02-98ce-4c9d-ae19-e025e04a0ffc), [Mercury Bill Pay](https://refero.design/pages/82b4731f-3cf9-4790-9b3f-671b7d875fba) | Числа, которые складываются; одно главное; серые нули |
| Recomputing | [ClickUp loading](https://mobbin.com/screens/e37f9235-7899-474c-a6de-02f68f982ecf) | Старые числа на экране, подпись |
| Группы | [Linear grouped list](https://refero.design/pages/1e0851c0-5302-485c-ab4a-98a6df5771fd), [Linear (Mobbin)](https://mobbin.com/screens/be6c4ee4-aa93-42b4-89b3-dcfc8386f022), [Linear project statuses](https://refero.design/pages/176eb554-d134-47c1-acc9-9f589fb67cbe) | Полоса-заголовок, счётчик, один контейнер |
| Why-строка | [Navan «exceeded by»](https://mobbin.com/screens/e77648b3-82f6-4db8-9b83-99c281da6ce4), [Xero Expenses](https://mobbin.com/screens/f0e0352c-2bfb-46ff-b23c-eb56b2fdd9c0) | Причина прямо в строке, без hover |
| Rule badge | [TravelPerk «Why is this out of policy?»](https://mobbin.com/screens/a4af80bc-a86b-4fe8-8454-7402d27edf84) | Правило одной фразой во всплывающей карточке |
| Source view | [Mercury wire review + invoice](https://refero.design/pages/efc545db-3e3e-4034-a051-1fa4be599384), [Mercury «Reviewing a bill»](https://mobbin.com/flows/332881d8-7f08-46ec-bc6d-5a669ea56e0d) | Документ как лист на сером холсте рядом с решением |
| Подсветка цитаты | [Matter reader](https://refero.design/pages/96f6af6b-0535-49a3-afb4-4d467d2f1865), [Grammarly match](https://mobbin.com/screens/b092ad71-b236-451c-b28b-b0893f3ff6e2), [Dropbox Dash chips](https://mobbin.com/screens/fc54be30-29ff-430c-9554-7858c47373e8) | Маркер в полном тексте; чипы источников |
| Agree / Not an issue | [Mercury approval drawer](https://refero.design/pages/c32485f8-bc77-4aa7-b7aa-40d96f779bb3), [Mercury approval flow](https://refero.design/flows/11062), [Linear toast](https://mobbin.com/screens/14cbb61d-2120-46fa-b29c-b7678e4b85cc) | Действие, затем счётчики и тост |
| Timeline | [Mercury receipt timeline](https://refero.design/pages/ee763e2a-d6f1-406f-92f2-1edcf22099c6), [Programa strike-through](https://mobbin.com/screens/cf1627aa-16a0-416b-a0d9-7b6d66e4b6fd), [Cal.com canceled](https://refero.design/pages/d3ab9988-09ca-4232-97ed-940f67076b9f) | Старое значение зачёркнуто, новое рядом |
| What changed | [Doppler activity](https://refero.design/pages/d7900804-3627-4629-b469-705ee6bda874), [Attio activity](https://mobbin.com/screens/297adfeb-8312-496b-8acf-41839a6cf775), [Linear Pulse](https://mobbin.com/screens/b4daf53d-35b6-4c7c-a237-91793e4e241d) | Ревизия с from → to и причиной |
| Вопрос и инбокс | [Fernand conversation](https://refero.design/pages/ef647030-eacf-4419-8abc-12b201240d21), [Fiverr Resolution Center](https://mobbin.com/screens/80dd72be-5298-4633-9051-08e096d40817), [Linear Inbox](https://mobbin.com/screens/18c6955a-c42f-4cc1-9aa2-0276456720fb), [пустой](https://mobbin.com/screens/5e4be052-1838-45d0-b850-fd7306cd4bff), [Front consequence text](https://mobbin.com/screens/ec4bc765-c692-40ec-90ad-cd65322d43d4) | Вопрос, ставка, ответ, текст о последствиях |
| Add evidence | [Supabase «Paste text»](https://mobbin.com/screens/aba1ad77-9a4d-4924-a8f7-bd8e1a8e943e), [NotebookLM paste](https://mobbin.com/screens/7b363f63-caa2-4688-93ed-d72e19d9ae30) | Сегменты типа и правило «word for word» |
| Line editor | [YNAB inline edit](https://mobbin.com/screens/465d24c6-4d11-4743-ba8c-b50c922dbd7f), [Neon dirty state](https://mobbin.com/screens/0062305a-7fb4-49ec-a10f-8438892eb329) | Правка на месте, подсветка несохранённого |
| Dispute и gate | [Front draft strip](https://mobbin.com/screens/abc9a482-7fee-45e1-8dd4-b504f4529915), [Remote blocked approval](https://mobbin.com/screens/5a02d53a-4cc4-411a-ba45-3d0b7c1f0f54), [Airwallex current step](https://mobbin.com/screens/a21ef4c4-38e3-475e-b8b0-2618f40df869), [Wittl send disabled](https://refero.design/pages/ac3d7ada-f282-4fec-8cc4-e5ba574f8dd3) | Причина блокировки на месте, цепочка |
| History | [PlanetScale audit log](https://mobbin.com/screens/d7448333-6400-49dd-b11f-e48e4e229200), [Mercury user activity](https://refero.design/pages/da0ff7bb-32ed-4295-a044-4edbcc3b0745) | Предложение и код события |
| Состояния | [Intercom import + banner](https://refero.design/pages/27a17a87-0d85-475d-8636-d8660b5eacc6), [Remote errors](https://mobbin.com/screens/3a5decfb-5aa1-4283-a804-2c9d9866d852), [Neon skeleton](https://mobbin.com/screens/9b5b1374-c015-495b-abcb-9cce659c741b) | Ошибка с местом; skeleton по геометрии |
| Перемещение | [Acctual «Pay a bill»](https://refero.design/flows/8846) | Счётчики меняются одновременно со списком |
| Мир документов | [CBP Form 7501](https://www.CBP.gov/sites/default/files/assets/documents/2023-Nov/CBP%20Form%207501.pdf), [audit tick marks](https://www.accountingtools.com/articles/what-are-audit-tick-marks.html) | Нумерованные поля; практика tick and tie |
| Принципы | [Microsoft HAX G9, G11, G16, G18](https://www.microsoft.com/en-us/research/wp-content/uploads/2019/01/Guidelines-for-Human-AI-Interaction-camera-ready.pdf), [Anthropic frontend-design SKILL.md](https://github.com/anthropics/claude-code/blob/main/plugins/frontend-design/skills/frontend-design/SKILL.md), [Impeccable craft-floor](https://raw.githubusercontent.com/pbakaus/impeccable/main/.agents/skills/impeccable/reference/craft-floor.md) | Объяснимость, лёгкая коррекция, список запретов |

Для Devpost: why-строка это HAX G11, Agree / Not an issue G9, What changed G18.

---

## 10. План сборки (суббота, PT)

По ценности для демо. Шаг закрыт, когда его тест из спеки W3 проходит локально и в CI.

| Время | Шаг | Готово, когда |
|---|---|---|
| 15:30–16:00 | **1. Тема и токены:** сообщение Пранаву, шрифты, `globals.css`, `components/ui` (8.3) | Страница на токенах, `lint:slop` зелёный |
| 16:00–16:35 | **2. Адаптер на фикстуре** (`lib/ui/view-model.ts`, fixture-провайдер, `Rev 1 / Rev 2`) | W3.2 на адаптере: 1 / 2 / 5 и $300; 2 / 0 / 6 и $700 |
| 16:35–17:35 | **3. Overview, группы, строки:** уравнение с Waived, count line, общая сетка, why и ссылки, rule badge, Just cleared, пустые группы | Ревизия 1 ≤ 620px на 1280×640; component test на числа |
| 17:35–18:45 | **4. Revision player** (6.1, NumberFlow, закрытие Source view, aria-live). **Демо-момент, защищаем.** Чекпойнт 1, запись на 0.5× | `Play reply` проигрывает сценарий без скролла страницы |
| 18:45–19:30 | **5. Source view:** `findSpan`, три рендера, чипы, скролл, `j`/`k`/`Esc` | W3.3: строка 1 подсвечивает цитату письма |
| 19:30–19:55 | **6. Agree / Not an issue** с причиной и быстрым переездом | W3.4: Not an issue даёт Cleared, `Dismissed by clerk`, меньший At stake |
| 19:55–20:40 | **7. Роли, dispute draft, approval gate** | W3.6: клерк видит `Requires controller`, контролёр одобряет, у ops lead кнопки нет |
| 20:40–21:25 | **8. What changed, карточка вопроса (4 состояния), инбокс ops lead.** Чекпойнт 2 | W3.5: ровно две записи с цитатой ответа |
| 21:25–21:55 | **9. Convex** (`useStableQuery`, мутации с `role`, флаг фикстуры остаётся). Запрос Пранава готов раньше: делай на ближайшей границе шагов | Ответ с телефона двигает строки на ноутбуке |
| 21:55–22:10 | **10. Case history** | События с актором и временем |
| 22:10–22:40 | **11. Add evidence и line editor** (must-ship) | W3.7: нужная мутация с нужными аргументами (mock) |
| 22:40–23:00 | **12. Репетиция на двух устройствах** на деплое, чиним только блокеры | Путь спеки 3 прошёл вживую |
| **23:00** | **Feature freeze** | Ни одной новой фичи |
| 23:00–01:00 | Полировка и состояния (skeleton, recomputing, extraction failed, empty, Replay), чекпойнт 3, hallway-тест, копирайт | Скриншоты всех состояний чистые |

Agreement timeline отдельным шагом не стоит: если шаги 3–8 идут с опережением, добавь его простым списком (около 15 минут) в шаге 8.

**Cut list**, сверху вниз: `was $300`; `Show again` (для дублей есть `Play reply`); счётчики на сегментах роли; зачёркнутые чипы заменённых фактов; разбор чата по строкам (остаётся `pre-wrap` с подсветкой); Agreement timeline; старые ревизии в What changed; причина у Not an issue; NumberFlow; поле описания в line editor.

**Никогда не режем:** revision player с переездом; уравнение из `caseTotals` с Waived; подсветку цитаты и Quote not found; Agree / Not an issue с событиями; approval gate с `approval_blocked`; карточку вопроса с follow-up и `closed_unresolved`; ответ в инбоксе; What changed с цитатой ответа; case history; paste box и line editor; полосу доверия и метку Replay; живой Convex и репетицию до 23:00.

---

## 11. Правила для агента

Правила лежат в `app/CLAUDE.md` на английском. Claude Code подхватывает вложенный `CLAUDE.md` при работе с файлами в `app/`; для `components/` нужен указатель в корневом `CLAUDE.md`, а это файл Пранава (8.2). При расхождении прав этот файл.

---

## 12. Открытые вопросы для команды

У каждого вопроса решение по умолчанию: фронт строится по нему, пока команда не решит иначе.

1. **Итоги (Дев).** Отклонённая needs_review строка по формуле остаётся в Waiting, хотя UI показывает её в Cleared: предлагаем исключать её из Waiting и класть billed в Agreed. Попадают ли строки outside_quote_scope (R5) в Agreed? Иначе уравнение не сходится на A-excl. Подтверди член `+ Waived by clerk $X`. *По умолчанию `caseTotals` как есть плюс Waived и dev-предупреждение.*
2. **Названия и определения R0–R9 (Дев).** *По умолчанию таблица 8.4.*
3. **Фикстура (Пранав).** `fixtures/scenarioA.ui.json` в main? Ключи? Есть ли документ `reply`, ответные факты и `recomputed` с `changes` для обеих ревизий? *Опаздывает: фронт пишет её сам в форме спеки.*
4. **Запрос и мутации (Пранав, Дев).** Один запрос со всем `CaseInput`; имена и аргументы мутаций; что возвращает approve после `approval_blocked` (`{ ok: false }` или `ConvexError`). *По умолчанию `CaseActions` (8.7).*
5. **Причина Not an issue (Дев).** Принимает ли `dismissFinding` `reason` в payload события? *По умолчанию да; иначе пункт cut list.*
6. **Undo для Not an issue.** Нужен новый тип события (например `finding_restored`), то есть правка `lib/types.ts` и сообщение команде. *По умолчанию Undo нет.*
7. **Payload событий (Дев, Арпан, Пранав).** Для History нужны `lineNo` в `finding_confirmed` / `finding_dismissed`, `docId` в `doc_added` / `facts_extracted` / `extraction_failed`, старое и новое значение в `charge_edited`.
8. **Время событий (Пранав).** `at` пишет сервер в момент события, сид не зашивает время событий (даты документов остаются из сценария). Иначе в воскресенье History смешает 24 и 25 октября и будет выглядеть постановкой.
9. **Порядок `Finding.evidence` (Дев).** Можно класть решающий спан первым? *По умолчанию `primarySpan` в адаптере.*
10. **Сид-пользователи (Пранав).** Имена трёх пользователей. *По умолчанию в копирайте роли (`the ops lead`), имена только в tooltip и History; ops lead, вероятно, Sam Bayline из чата; Maya это получатель письма у покупателя, её имя у клерка только если так решил сид.*
11. **Replay (Пранав).** Как UI узнаёт, что это replay, и что записано, а что живое. *Текст тега только по факту.*
12. **Тексты (Дев, по желанию).** Лучший копирайт why это просьба к `explain.ts`, фронт текст не меняет. What changed показывает `explanation` новой находки вместо примера фразы из спеки 8.6: подтвердите, что так можно.
13. **Продуктовая идея, необязательно.** Строка 4 очищается со слов: вопрос говорит, что в чате WhatsApp сбора нет, ответ говорит «approved … on WhatsApp», R8 очищает строку. Финансовый судья может это заметить. Вариант: why R8 отмечает, что упомянутого сообщения нет в кейсе (`That WhatsApp message is not in the case.`), плюс ссылка `Add it` в paste box. Это движок и `explain.ts`, а для трека Anthropic готовый момент «знаем, где можем ошибиться». Фронт сейчас ничего не меняет.
14. **Демо (все).** Телефон ops lead у Пранава, запасной хотспот, репетиция до 23:00.
