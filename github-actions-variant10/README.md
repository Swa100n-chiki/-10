# GitHub Actions: workflow, который запускается после push

**Тема:** Docker, GitHub Actions и CI/CD  
**Вариант:** 10 — «Запуск по push»  
**Задание:** настроить workflow, который запускается после push, и указать это в README.

## Структура репозитория

```text
.
├── .github/
│   └── workflows/
│       └── push.yml        # workflow студента
├── screenshots/
│   ├── 01-workflow-yaml.png   # скриншот YAML-файла
│   └── 02-workflow-run.png    # скриншот успешного запуска (добавляется после push)
├── .gitignore
└── README.md
```

## Запуск по push

**Этот workflow запускается автоматически после каждого push в репозиторий.**
Запуск настроен в разделе `on` файла `.github/workflows/push.yml`:

```yaml
on:
  push:
```

Отдельно запускать workflow вручную не нужно: достаточно выполнить `git push`, и
GitHub Actions сам создаст запуск (run). Результат виден во вкладке **Actions**
репозитория.

## Содержимое workflow

```yaml
name: Run on push

on:
  push:

jobs:
  push-demo:
    runs-on: ubuntu-latest
    steps:
      - name: Get source code
        uses: actions/checkout@v4

      - name: Show trigger event
        run: echo "Workflow запущен событием - ${{ github.event_name }}"

      - name: Show branch and commit
        run: |
          echo "Ветка - ${{ github.ref_name }}"
          echo "Коммит - ${{ github.sha }}"

      - name: Show repository files
        run: ls -la
```

## Объяснение конфигурации

| Строка / раздел | Что означает |
| --- | --- |
| `name: Run on push` | Название workflow, которое отображается во вкладке Actions. |
| `on: push` | Событие-триггер: workflow запускается после отправки изменений (push) в репозиторий. |
| `jobs` | Раздел со списком задач (job). В этом workflow одна задача. |
| `push-demo` | Имя job. |
| `runs-on: ubuntu-latest` | Runner — виртуальная машина GitHub с Ubuntu, на которой выполняется job. |
| `steps` | Последовательность шагов job. Они выполняются сверху вниз. |
| `actions/checkout@v4` | Готовое действие, которое получает файлы репозитория на runner. |
| `run: echo ...` | Команда, которая выводит сообщение в журнал запуска. |
| `${{ github.event_name }}` | Контекст GitHub: имя события, запустившего workflow (здесь `push`). |
| `${{ github.ref_name }}` | Имя ветки, в которую сделан push. |
| `${{ github.sha }}` | Хеш коммита, после которого запустился workflow. |
| `run: ls -la` | Показывает файлы репозитория — подтверждение, что checkout сработал. |

## Как работает последовательность

```text
push → GitHub Actions запускает workflow → runner ubuntu-latest → шаги job → результат (успех / ошибка)
```

1. Студент выполняет `git push`.
2. GitHub находит файл в `.github/workflows/` и видит триггер `on: push`.
3. Создаётся запуск workflow, job `push-demo` выполняется на runner `ubuntu-latest`.
4. По порядку выполняются шаги: получение файлов, вывод события, вывод ветки и коммита, список файлов.
5. Результат отображается во вкладке **Actions**: зелёная галочка — успех, красный крест — ошибка.

## Как проверить запуск

1. Выполнить `git push` (любое изменение в репозитории).
2. Открыть репозиторий на GitHub → вкладка **Actions**.
3. Выбрать запуск **Run on push** → job `push-demo`.
4. Раскрыть шаги и убедиться, что в шаге *Show trigger event* выведено событие `push`.

Скриншоты — в папке `screenshots/`.

## Безопасность

В репозитории нет паролей, токенов и приватных ключей. В workflow используются
только публичные данные запуска (`github.event_name`, `github.ref_name`, `github.sha`).
