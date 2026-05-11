# IDE Navigator

Расширение для Visual Studio Code, которое добавляет навигацию по исходному коду на основе статического анализа AST. Поддерживает шесть языков сразу: Python, Java, C++, Go, JavaScript, TypeScript.

<p align="center">
  <img src="https://media1.tenor.com/m/mxhNvaln24cAAAAC/pox3d.gif" width="600"/>
</p>

## Возможности

| Функция | Описание |
|---------|----------|
| Document Outline | Иерархическое дерево классов, функций и методов в боковой панели |
| Go to Definition | Переход к определению по `Ctrl+Click` или `F12`, включая cross-file через импорты |
| Find All References | Кастомная панель всех вхождений символа, вызывается по `Shift+F12` |
| Hover Info | Тултип с типом символа, сигнатурой и цикломатической сложностью |
| Workspace Symbols | Поиск символов по всему проекту через `Ctrl+T` |
| CodeLens | Счётчики использований над каждой функцией и классом |
| Call Graph | Интерактивный граф вызовов с детекцией циклов и мёртвого кода |

## Установка

Скачайте `.vsix` для своей платформы со [страницы релизов](https://github.com/Ademma2222/IDENAVIGATOR/releases/latest):

- **Windows x64** — `ide-navigator-win32-x64-<версия>.vsix`
- **macOS Apple Silicon** — `ide-navigator-darwin-arm64-<версия>.vsix`

Установка:

```bash
code --install-extension ide-navigator-<target>-<версия>.vsix
```

Или через VS Code: `Ctrl+Shift+P` → `Extensions: Install from VSIX...`.

Никаких внешних зависимостей (Python, Node) ставить не требуется — сервер собран PyInstaller-ом и упакован в `.vsix`.

## Настройки

| Параметр | Значения | Описание |
|----------|----------|----------|
| `ideNavigator.logLevel` | `debug` / `info` / `warning` / `error` | Уровень логирования сервера |
| `ideNavigator.cacheSize` | `1..256` (по умолчанию `32`) | Максимум AST-деревьев в кэше |
| `ideNavigator.enableCallGraph` | `true` / `false` | Включает команду `Show Call Graph` |

## Архитектура

```
VS Code Extension (TypeScript)
        │
        │  LSP через stdio
        ▼
Python Language Server (pygls + tree-sitter)
```

Клиент на TypeScript — тонкий адаптер: запускает Python-процесс и управляет WebView-панелями. Вся логика статического анализа — на сервере. `BaseLanguage` собран как фасад из пяти миксинов (`ParseCacheMixin`, `DefinitionMixin`, `ReferencesMixin`, `HoverMixin`, `CallGraphMixin`); каждый языковой модуль определяет только два метода — выбор парсера и извлечение символов.

## Сборка из исходников

```bash
git clone https://github.com/Ademma2222/IDENAVIGATOR.git
cd IDENAVIGATOR/ide-navigator

cd server
python -m venv venv
source venv/Scripts/activate    # Windows (git-bash)
source venv/bin/activate        # macOS / Linux
pip install -r requirements.txt

cd ../extension
npm install
npm run compile
```

Откройте `ide-navigator/extension` в VS Code и нажмите `F5` — запустится Extension Development Host с локальным сервером.

Запуск тестов:

```bash
cd ide-navigator/server
pip install -r requirements-dev.txt
pytest
```
