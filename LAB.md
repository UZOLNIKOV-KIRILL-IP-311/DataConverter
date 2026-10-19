# Лабораторная № 6 — наблюдения

Репозиторий: https://github.com/UZOLNIKOV-KIRILL-IP-311/DataConverter

## История коммитов

1. Исходный проект DataConverter (код, тесты, примеры)
2. CI на матрице ОС + `fail-fast: false`
3. Кэш `build/_deps` (FetchContent)
4. Артефакты: JUnit-отчёт и бинарник

## Часть 4. fail-fast

Контролируемая ошибка: `EXPECT_TRUE(false)` в `Converter.CsvToJson`.

| Режим | Run | Ubuntu | macOS | Windows |
|---|---|---|---|---|
| `fail-fast: false` | [39195505909](https://github.com/UZOLNIKOV-KIRILL-IP-311/DataConverter/actions/runs/39195505909) | failure | failure | failure |
| `fail-fast: true` | [39195625834](https://github.com/UZOLNIKOV-KIRILL-IP-311/DataConverter/actions/runs/39195625834) | failure | cancelled | cancelled |

**Вывод:** при `false` все jobs матрицы доходят до конца; при `true` после первой ошибки остальные отменяются.

После эксперимента тесты восстановлены, в workflow снова `fail-fast: false`.

## Часть 5. Кэширование

Кэшируется `build/_deps`, ключ: `${{ runner.os }}-deps-${{ hashFiles('CMakeLists.txt') }}`.

- Первый прогон после добавления кэша заполняет cache.
- Повторный прогон без изменений быстрее за счёт восстановления зависимостей.
- Изменение исходников без смены `CMakeLists.txt` сохраняет cache hit по deps, но сама перекомпиляция проекта всё равно нужна — ускорение не гарантировано на каждый запуск.

## Часть 6. Артефакты

После тестов загружаются:

- `test-report-<os>` — JUnit XML
- `dataconv-<os>` — собранный бинарник

**Кэш vs артефакты:** кэш ускоряет следующие CI-запуски внутри workflow; артефакты — результат, который можно скачать вручную после завершения job.

## Почему успех на одной ОС не гарантирует успех на других

Разные компиляторы (GCC/Clang/MSVC), файловые пути, переводы строк, доступность инструментов и особенности ABI/линковки. Код и тесты одни, окружение — разное.
