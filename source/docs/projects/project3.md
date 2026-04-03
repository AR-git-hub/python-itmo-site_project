# Проект 3. DevOps

## Цель
Реализовать принципы непрерывной интеграции и деплоя, а именно: настроить автоматическую сборку и публикацию статического сайта (MkDocs) при каждом пуше в ветку *main* на двух платформах: GitHub Pages и SourceCraft.

## Результат автоматизации

**Событие-триггер**: 
push в [репозиторий с сайтом](https://github.com/AR-git-hub/python-itmo-site_project) в ветку *main* на GitHub.

**Действия**:

- зеркалирование пуша в [репозиторий с сайтом на SourceCraft](https://sourcecraft.dev/05252/python-itmo-site-project)
- сборка и деплой сайта из репозитория на Github с помощью артефактов и GitHub Actions через платформу GitHub Pages
- сборка в ветку *release* и деплой сайта из репозитория на SourceCraft

**Результат**:

- **Сайт** (на платформе _GitHub Pages_) доступен **по адресу:** [https://ar-git-hub.github.io/python-itmo-site_project](https://ar-git-hub.github.io/python-itmo-site_project)
- **Сайт** (на платформе _SourceCraft_) доступен **по адресу:** [https://05252.sourcecraft.site/python-itmo-site-project](https://05252.sourcecraft.site/python-itmo-site-project)
- **Репозиторий** сайта на *Github*: https://github.com/AR-git-hub/python-itmo-site_project?tab=readme-ov-file
- **Репозиторий** сайта на *SourceCraft*: https://sourcecraft.dev/05252/python-itmo-site-project

## Организация репозитория

```
├── .github/  
│ └── workflows/  
│ ├── deploy.yml – сборка и деплой на GitHub Pages  
│ └── mirror-to-sourcecraft.yml # зеркалирование + деплой на SourceCraft  
├── .sourcecraft/  
│ └── sites.yaml # настройка публикации на SourceCraft  
├── source/ # исходники MkDocs  
│ ├── docs/ # страницы  
│ ├── mkdocs.yml  
│ └── .nojekyll  
├── requirements.txt # зависимости (mkdocs, mkdocs-material)  
└── README.md
```
## Настройки GitHub Pages
1. В репозитории GitHub включена публикация через Actions (Settings → Pages → Source: GitHub Actions).
2. Workflow `deploy.yml`:
   - Устанавливает Python, зависимости из `requirements.txt`
   - Запускает `mkdocs build --config-file source/mkdocs.yml`
   - Загружает собранный сайт (папку `site`) как артефакт
   - Публикует артефакт на GitHub Pages (встроенное действие `actions/deploy-pages`)

## Настройки SourceCraft
1. Сгенерирована пара SSH-ключей для доступа из GitHub Actions.
   - Публичный ключ добавлен в настройках SourceCraft (Settings → SSH Keys).
   - Приватный ключ сохранён в секретах GitHub (`SOURCECRAFT_SSH_KEY`).
2. Workflow `mirror-to-sourcecraft.yml`:
   - Собирает сайт аналогично.
   - Переключается на ветку `release`, очищает её и копирует содержимое *site* в корень.
   - Форсированно пушит ветку `release` на SourceCraft.
1. Файл `.sourcecraft/sites.yaml`:
```yaml
site:
    root: "."
    ref: "release"
```

SourceCraft публикует сайт из ветки `release`.


## Вывод

В ходе работы был реализован полноценный CI/CD‑пайплайн, позволяющий одним `git push` обновлять статический сайт одновременно на двух платформах. Использованы:

- GitHub Actions для сборки и деплоя на GitHub Pages
- SSH‑ключи и отдельный workflow для зеркалирования на SourceCraft с дальнейшей публикацией из ветки *release*
- Конфигурационные файлы для MkDocs и SourceCraft Sites

Такой подход к разработке минимизирует ручную работу, обеспечивая возможность сосредоточить свои усилия на кодинге и работе над архитектурой продуктов.
