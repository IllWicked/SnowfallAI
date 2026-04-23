# Snowfall AI

Desktop-приложение для потоковой генерации SEO-корпусов через Claude Code.

Версия `0.1.2` — скелет проекта, Шаг 1 из плана в `MAGNUM-AI-DESIGN.md` (файл остался под старым именем — переименуем когда дойдём до правок диздока).

## Статус

На текущий момент готов каркас:

- Tauri 2.0 проект со структурой под Windows (NSIS installer)
- **`tauri = { features = ["unstable"] }`** — нужно для multi-webview API (`WindowBuilder`, `WebviewBuilder`, `get_webview`)
- **Окно создаётся программно** через `WindowBuilder`: 1000×600 дефолт, минимум 800×500, при старте `maximized(true) + center()` (паттерн из APM)
- **Workaround "шакализации" иконки** — иконка извлекается из EXE ресурса через WinAPI (`ExtractIconW` + `WM_SETICON`) и ставится на окно после создания
- **Настоящая иконка** из `favicon.svg` (pearl-градиент с 4 кругами), все размеры 16-256
- Frontend-каркас: topbar (brand + usage bar + статус авторизации), пустой main-экран
- Дизайн-система зафиксирована в `docs/DESIGN-SYSTEM.md` — токены, компоненты, Unicode-иконки, layout
- Rust backend: заглушки всех ключевых команд (auth, pipeline, task, deps, settings, app_version)
- Структура папок под будущие модули: `claude/`, `pipeline/`, `project/`, `queue/`, `deps/`, `skills/`, `utils/`

Функционала ещё **нет** — приложение запускается, показывает пустое окно в maximized режиме.

## Требования

- **Windows 10/11**
- **Rust 1.75+** с `cargo` (через `rustup`)
- **Tauri CLI 2.x**: `cargo install tauri-cli --version "^2.0" --locked`
- **Node.js не нужен** — фронтенд на vanilla JS, билд не требуется

## Запуск в dev-режиме

```bash
cd snowfall-ai/src-tauri
cargo tauri dev
```

При первом запуске Cargo скачает и скомпилирует зависимости. Окно откроется само.

## Production-сборка

```bash
cd snowfall-ai/src-tauri
cargo tauri build
```

NSIS-инсталлятор будет создан в `src-tauri/target/release/bundle/nsis/`.

## Запуск тестов

Шаг 3.1 добавил Rust-модуль `pipeline::*` (парсер APM-пайплайнов + генерация Claude Code slash-команд) с unit-тестами на фикстуре `tests/fixtures/bet-pillar-main-v7.json`. Запустить тесты:

```bash
cd snowfall-ai/src-tauri
cargo test
```

Ожидаемый вывод: `test result: ok. 6 passed; 0 failed`.

## Структура проекта

```
snowfall-ai/
├── src-tauri/                  Rust backend
│   ├── Cargo.toml              Зависимости (tauri + unstable feature, windows-sys)
│   ├── tauri.conf.json         Конфиг Tauri (окно создаётся программно)
│   ├── build.rs
│   ├── capabilities/           Разрешения Tauri v2
│   ├── icons/                  Иконки приложения (pearl-градиент)
│   └── src/
│       ├── main.rs             Точка входа, WindowBuilder, workaround иконки
│       ├── state.rs            AppState (Settings)
│       ├── commands/           Tauri commands для фронта
│       ├── claude/             ClaudeCodeRunner (TODO)
│       ├── pipeline/           PipelineCompiler (TODO)
│       ├── project/            ProjectManager (TODO)
│       ├── queue/              QueueScheduler (TODO)
│       ├── deps/               DepsBundler (TODO)
│       ├── skills/             SkillsManager (TODO)
│       └── utils/
│           └── platform.rs     WinAPI workaround для иконки
├── dist/                       Frontend (vanilla JS, HTML, CSS)
│   ├── index.html
│   ├── css/                    base/layout/components
│   ├── js/                     app.js
│   └── assets/
├── resources/                  Bundled в инсталлятор
│   ├── skills/
│   ├── tools/
│   └── dictionaries/
├── docs/
│   └── DESIGN-SYSTEM.md        Pearl-дизайн-система
└── README.md
```

## Следующие шаги

По плану из диздока:

- **Шаг 2:** pearl-дизайн-система, локальные шрифты Manrope + Fira Code, vanilla JS UI-хелперы
- **Шаг 3:** PipelineCompiler (JSON → slash-commands + subagents)
- **Шаг 4:** stream-json парсер + ClaudeCodeRunner
- **Шаг 5:** bundling Nuspell/Voikko/словарей, SkillsManager
- **Шаг 6:** end-to-end склейка
- **Шаг 7:** авторизация через `claude login`, deps check
- **Шаг 8:** смоук-тест на реальном пайплайне
