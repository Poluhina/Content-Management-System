# Лабораторная работа №3
# Разработка простой темы WordPress 
# Цель работы
Научиться создавать собственную тему WordPress, разобраться в её минимальной структуре и принципах работы шаблонов.

# Условие
# Шаг 1. Подготовка среды
В локальной установке WordPress перешла в папку wp-content/themes.

Создал директорию для своей темы, назвала usm-theme.

# Шаг 3. Общие части шаблонов

Создала файл header.php, прописала там код шапки сайта
```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title><?php bloginfo('name'); ?></title>
    <?php wp_head(); ?>
</head>
<body>

<header>
    <h1><?php bloginfo('name'); ?></h1>
</header>
```

Создала файл footer.php, прописала там код подвала сайта 
```
<footer>
    <p>© 2026 Моя тема</p>
</footer>

<?php wp_footer(); ?>
</body>
</html>
```
В index.php подключите header.php и footer.php с помощью функций get_header() и get_footer()
```
<?php get_header(); ?>
<?php get_footer(); ?>
```

На главной странице выведите список последних записей (5 штук) с помощью цикла WordPress.

# Шаг 4. Файл функций
Создала файл functions.php в папке темы.

В functions.php добавила функцию для подключения стилей темы с помощью wp_enqueue_style().
```
<?php
function usm_theme_styles() {
    wp_enqueue_style('main-style', get_stylesheet_uri());
}

add_action('wp_enqueue_scripts', 'usm_theme_styles');
```

# Шаг 5. Дополнительные шаблоны
Создала файл single.php для отображения отдельного поста

Создала файл page.php для отображения страниц

Создала файл sidebar.php для боковой панели и подключилаего в нужных шаблонах с помощью get_sidebar()

Создала файл comments.php для отображения комментариев и подключила его в single.php и page.php

Создала файл archive.php для отображения архивов записей.






# Контрольные вопросы
Какие два файла являются обязательными для любой темы WordPress?

Как подключаются общие части шаблонов (header, footer, sidebar)?

Чем отличаются index.php, single.php и page.php?

Зачем нужен файл functions.php в теме?

