---
name: preview-slide
description: Визуально проверить слайд(ы) презентации на движке Shower через agent-browser — снять скриншот слайда или всего дека и посмотреть, как он рендерится. Использовать, когда пользователь просит "визуально проверь", "покажи слайд N", "как выглядит", "сними скриншот слайда", "прогони весь дек" или проверить вёрстку после правки HTML/CSS/SVG.
allowed-tools: Bash(curl:*), Bash(npm run serve:*), Bash(npx agent-browser:*), Read
---

# preview-slide

Снять скриншот слайда презентации (движок **Shower**, файл `index.html`, соотношение **16:9**) через `agent-browser` и посмотреть результат глазами. Браузер уже установлен в системе (`agent-browser install` сделан); CLI ставится через `npx agent-browser`.

## Рецепт

### 1. Поднять / проверить dev-сервер
Сервер `shower serve` слушает **:8080**. Не поднимай свой `python -m http.server`.
```bash
curl -s -o /dev/null -w "%{http_code}\n" http://localhost:8080/   # 200 → уже работает
npm run serve                                                     # если не 200 — подними в фоне
```

### 2. Выставить вьюпорт под слайд (обязательно — иначе чёрные поля)
Соотношение слайда 16:9, ширина 1024 → высота 576.
```bash
npx agent-browser set viewport 1024 576
```

### 3. Открыть нужный URL
- Один слайд в полный экран: `http://localhost:8080/index.html?full#<N>` (N с 1)
- Обзор-сетка всех слайдов: `http://localhost:8080/index.html`

```bash
npx agent-browser open "http://localhost:8080/index.html?full#2"
npx agent-browser wait 1000                          # дать слайду отрендериться
npx agent-browser screenshot /tmp/slide.png          # для длинных страниц добавь --full
```

### 4. Посмотреть результат
Прочитай PNG инструментом **Read** — изображение покажется визуально. Найди баги (наезды текста, обрезку, кривое выравнивание) → правь HTML/CSS/SVG → повтори шаги 3–4.

## Прогнать весь дек
Узнай число слайдов и сними каждый:
```bash
npx agent-browser open "http://localhost:8080/index.html"
npx agent-browser get count ".slide"      # сколько слайдов
```
Затем цикл по `?full#1..N`, снимая `/tmp/slide-<N>.png`, и читай их через Read.

## Заметки
- Refs `@eN` устаревают после каждого изменения страницы — пере-снимай `snapshot` перед взаимодействием.
- Полный гайд по agent-browser: `npx agent-browser skills get core --full`.
- Контекст движка и сервера — в `CLAUDE.md` в корне проекта.
