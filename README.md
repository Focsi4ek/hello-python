# hello-python 🐍

Учебное CLI-приложение на Python с тестами, сборкой через PyInstaller и автоматической публикацией релизов в GitHub Actions.

[![Python CI/CD](https://github.com/Focsi4ek/hello-python/actions/workflows/ci.yml/badge.svg)](https://github.com/Focsi4ek/hello-python/actions/workflows/ci.yml)

## Возможности

- Вывод версии, операционной системы и архитектуры.
- Приветствие и вычисление суммы чисел от 1 до 10.
- Вывод аргументов командной строки.
- Готовые исполняемые файлы, не требующие установки Python.

## Скачать программу

Бинарники доступны в [Releases](https://github.com/Focsi4ek/hello-python/releases).

| Платформа | Файл |
| --- | --- |
| Linux x64 | `hello-python-linux-x64` |
| macOS Apple Silicon | `hello-python-macos-arm64` |
| Windows x64 | `hello-python-windows-x64.exe` |

Для macOS Apple Silicon:

```bash
curl -fL https://github.com/Focsi4ek/hello-python/releases/download/v1.0.0/hello-python-macos-arm64 -o hello-python-macos-arm64
chmod +x hello-python-macos-arm64
./hello-python-macos-arm64 Привет
```

Пример вывода:

```text
hello-python version v1.0.0
Hello from Python! 🐍📦
OS: darwin
Arch: arm64
Hello, GitHub!
Sum 1..10 = 55
Аргументы:
  1: Привет
```

## Проверка через Docker

Команды выполняются из корня проекта. Требуется запущенный Docker.

```bash
docker build -t hello-python-builder .

docker run --rm \
  -u "$(id -u):$(id -g)" \
  -e HOME=/tmp \
  -v "$PWD":/app \
  -w /app \
  hello-python-builder \
  sh -c 'python -m ruff check . && python -m ruff format --check . && python -m pytest -v && python main.py Привет'
```

## Локальная сборка через Docker

После создания образа:

```bash
docker run --rm \
  -u "$(id -u):$(id -g)" \
  -e HOME=/tmp \
  -v "$PWD":/app \
  -w /app \
  hello-python-builder \
  python -m PyInstaller --noconfirm --clean --onefile --name hello-python main.py

docker run --rm \
  -v "$PWD/dist":/dist:ro \
  debian:bookworm-slim \
  /dist/hello-python Привет
```

Результат находится в `dist/hello-python`. Docker создаёт Linux-бинарник для архитектуры контейнера.

## CI/CD

При push в `main` и pull request выполняются проверки Ruff и тесты pytest.

При отправке тега `v*` после проверок:

1. Приложение собирается отдельно для Linux, macOS и Windows.
2. В бинарники записывается версия из тега.
3. Каждый бинарник проверяется запуском.
4. Три файла публикуются в GitHub Releases.

## Структура проекта

- `main.py` — точка входа CLI.
- `hello/` — версия и функции приложения.
- `tests/` — тесты.
- `requirements.txt` — зависимости.
- `pyproject.toml` — настройки проекта.
- `Dockerfile` — окружение для проверок и сборки.
- `.github/workflows/ci.yml` — автоматизация CI/CD.
