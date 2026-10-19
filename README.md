# DataConverter — учебный проект для лабораторной по CI/CD

Консольная утилита на C++17 для преобразования табличных данных между форматами CSV, JSON и YAML.

## Структура

- `src/` — исходный код приложения и библиотеки конвертации
- `tests/` — модульные тесты (GoogleTest)
- `examples/` — примеры входных файлов
- `third_party/nlohmann/` — заголовок nlohmann/json
- `.github/workflows/ci.yml` — CI-конвейер GitHub Actions

## Зависимости

| Зависимость | Как подключается |
|---|---|
| nlohmann/json | локальный header-only (`third_party`) |
| yaml-cpp 0.9.0 | CMake `FetchContent` |
| GoogleTest 1.14.0 | CMake `FetchContent` (если включены тесты) |

Требования: CMake ≥ 3.20, компилятор с поддержкой C++17 (GCC/Clang/MSVC).

## Локальная сборка

```bash
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release --parallel
ctest --test-dir build -C Release --output-on-failure
```

На Windows с MinGW из MSYS2 укажите компилятор явно:

```bash
cmake -S . -B build -G Ninja -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_COMPILER=C:/msys64/mingw64/bin/g++.exe
cmake --build build --parallel
ctest --test-dir build --output-on-failure
```

Бинарник: `build/dataconv` (или `build/Release/dataconv.exe` при генераторе Visual Studio).

Пример запуска:

```bash
./build/dataconv examples/people.csv people.json
```

## CI (GitHub Actions)

Workflow запускается на `push` и `pull_request`, собирает и тестирует проект на:

- `ubuntu-latest`
- `macos-latest`
- `windows-latest`

Также кэширует `build/_deps` и публикует артефакты: JUnit-отчёт тестов и собранный бинарник.
