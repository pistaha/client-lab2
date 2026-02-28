## Развёртывание сайта на Hugo 

### Общая информация

В рамках лабораторной работы был создан и развёрнут статический сайт с использованием генератора **Hugo** и автоматической публикацией через **GitHub Pages**.
Проект размещён в репозитории GitHub и автоматически собирается при каждом push в ветку `main`.

---

## Используемые технологии

* Генератор статических сайтов: **Hugo 0.157.0 (extended)**
* Система контроля версий: **Git**
* Хостинг: **GitHub Pages**
* CI/CD: **GitHub Actions**
* Тема оформления: Blowfish (через submodule)

---

## Что было сделано

### 1. Установка Hugo

Hugo установлен на macOS через Homebrew:

```bash
brew install hugo
hugo version
```

Используется версия `0.157.0 extended`, необходимая для корректной работы темы.

---

### 2. Создание проекта

Создан новый проект:

```bash
hugo new site client-lab2
cd client-lab2
```

---

### 3. Подключение темы через Git Submodule

Тема подключена как submodule для возможности обновления:

```bash
git init
git submodule add https://github.com/nunocoracao/blowfish.git themes/blowfish
```

В `config.toml` указана тема:

```toml
theme = "blowfish"
baseURL = "https://pistaha.github.io/client-lab2/"
title = "Client Lab 2"
```

---

### 4. Создание страниц контента

Были созданы следующие страницы:

```bash
hugo new content/posts/my-projects.md
hugo new content/posts/my-skills.md
hugo new content/about.md
```

Добавлены:

* Главная страница
* Страница "Обо мне"
* Раздел "Мои проекты"
* Раздел "Навыки"

Контент оформлен в формате Markdown.

---

### 5. Локальная проверка сайта

Перед публикацией сайт запускался локально:

```bash
hugo server -D
```

Сайт был доступен по адресу:

```
http://localhost:1313
```

Проверена корректность:

* навигации
* ссылок
* отображения постов
* работы темы

---

### 6. Загрузка проекта на GitHub

Репозиторий:


Основные команды:

```bash
git add .
git commit -m "Initial Hugo setup"
git branch -M main
git push -u origin main
```

---

## Настройка GitHub Actions

Для автоматического деплоя создан файл:

```
.github/workflows/hugo.yml
```

### Полный YAML-файл:

```yaml
name: Deploy Hugo site to Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: '0.157.0'
          extended: true

      - name: Build
        run: hugo --minify

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

---

## Как работает автоматический деплой

При каждом `push` в ветку `main` происходит:

1. **Checkout**
   Клонируется репозиторий вместе с submodule (темой)

2. **Setup Hugo**
   Устанавливается Hugo extended версии 0.157.0

3. **Build**
   Выполняется команда:

   ```bash
   hugo --minify
   ```

   Генерируются статические файлы в папке `public`

4. **Upload artifact**
   Папка `public` загружается как артефакт сборки

5. **Deploy**
   Артефакт публикуется в GitHub Pages

---

## Итоговый результат

Развёрнутый сайт доступен по ссылке:
https://pistaha.github.io/client-lab2/

Сайт успешно:

* автоматически собирается
* публикуется при каждом обновлении
* корректно отображает Markdown-контент
* работает на GitHub Pages

---

## Вывод

В результате работы:

* Освоен генератор статических сайтов Hugo
* Реализовано подключение темы через submodule
* Настроена автоматическая CI/CD сборка
* Развёрнут полноценный сайт на GitHub Pages
* Получен практический опыт работы с Git, GitHub Actions и статической генерацией сайтов

---
