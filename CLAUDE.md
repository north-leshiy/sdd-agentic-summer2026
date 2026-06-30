# CLAUDE.md

## Что это

Презентация на движке **[Shower](https://github.com/shower/shower)** (`@shower/core` + тема `@shower/ribbon`).
Весь дек — это один HTML-файл, слайды — обычная разметка. Соотношение слайда — **16:9**
(задаётся в `index.html` через `--slide-ratio: calc(16 / 9)`).

Структура:
- `index.html` — весь дек: каждый слайд это `<section class="slide">`.
- `node_modules/@shower/core`, `node_modules/@shower/ribbon` — движок и тема, не редактируем.
- `package.json` — npm-скрипты shower (`serve`, `bundle`, `archive`, `pages`, `pdf`).

## Dev-сервер

`shower serve` слушает **:8080**. Перед визуальной проверкой убедись, что сервер поднят, и
**не поднимай свой `python -m http.server`**.
```bash
curl -s -o /dev/null -w "%{http_code}\n" http://localhost:8080/   # 200 → уже работает
npm run serve                                                     # иначе подними в фоне
```

URL-схема:
- Один слайд в полный экран: `http://localhost:8080/index.html?full#<N>` (N — номер слайда, с 1).
- Обзор-сетка всех слайдов: `http://localhost:8080/index.html`.

Сборка/экспорт: `npm run bundle` (статика), `npm run pdf` (PDF), `npm run archive` (zip).

## Визуальная проверка слайдов

**Визуальный вид слайдов нужно тестировать через agent-browser** — текст в HTML может выглядеть
нормально, но рендериться с багами (наезды, обрезка, кривое выравнивание, переполнение). После
любой правки вёрстки снимай скриншот слайда и смотри на него глазами.

- Рецепт снятия скриншота — в скиле **`preview-slide`** (`.claude/skills/preview-slide/SKILL.md`).
- Низкоуровневый гайд по CLI — в скиле **`agent-browser`** (`.claude/skills/agent-browser/SKILL.md`),
  либо `npx agent-browser skills get core --full`.

Коротко:
```bash
npx agent-browser set viewport 1024 576                       # 16:9, иначе чёрные поля
npx agent-browser open "http://localhost:8080/index.html?full#2"
npx agent-browser wait 1000
npx agent-browser screenshot /tmp/slide.png                   # затем Read этот PNG
```

agent-browser установлен в системе (Chrome через `agent-browser install` уже стоит); в проекте он
подключён как devDependency, поэтому работает через `npx agent-browser`.
