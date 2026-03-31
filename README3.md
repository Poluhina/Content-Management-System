# Лабораторная работа №3
# Разработка простой темы WordPress 
# Цель работы
Научиться создавать собственную тему WordPress, разобраться в её минимальной структуре и принципах работы шаблонов.

# Инструкции по запуску проекта
1) Установить и запустить локальный сервер (xampp)
2) Запустить модули Apache и MySQL
3) Скопировать папку в htdocs
4) Открыть в браузере

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

В этом файле размещаются структура и содержание верхней части сайта, которая отображается на всех страницах. Подключение осуществляется с помощью функции get_header(), что позволяет централизованно управлять содержимым и не дублировать код на каждой странице. 

Создала файл footer.php, прописала там код подвала сайта 
```
<footer>
    <p>© 2026 Моя тема</p>
</footer>

<?php wp_footer(); ?>
</body>
</html>
```

В этом файле размещается содержимое нижней части сайта, которое отображается на всех страницах. Подключение осуществляется с помощью функции get_footer(), что позволяет централизованно управлять содержимым и не дублировать код на каждой странице.
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
В файл добавлены функции для подключения стилей темы с помощью wp_enqueue_style(), что обеспечивает корректное подключение CSS файлов и их загрузку на всех страницах сайта.



# Шаг 5. Дополнительные шаблоны
Создала файл single.php для отображения отдельного поста
```
<?php get_header(); ?>

<div class="container">
    <?php if (have_posts()) : while (have_posts()) : the_post(); ?>

        <h1><?php the_title(); ?></h1>
        <p><?php the_content(); ?></p>

        <?php comments_template(); ?>

    <?php endwhile; endif; ?>
</div>

<?php get_sidebar(); ?>
<?php get_footer(); ?>
```

Создала файл page.php для отображения страниц
```
<?php get_header(); ?>

<div class="container">
    <?php if (have_posts()) : while (have_posts()) : the_post(); ?>

        <h1><?php the_title(); ?></h1>
        <p><?php the_content(); ?></p>

    <?php endwhile; endif; ?>
</div>

<?php get_footer(); ?>
```

Создала файл sidebar.php для боковой панели и подключилаего в нужных шаблонах с помощью get_sidebar()
```
<div class="sidebar">
    <h3>Боковая панель</h3>
    <p>Тут может быть меню или виджеты</p>
</div>

<div class="sidebar">
    <h3>Боковая панель</h3>

    <?php if ( is_active_sidebar( 'sidebar-1' ) ) : ?>
        <?php dynamic_sidebar( 'sidebar-1' ); ?>
    <?php else : ?>
        <p>Тут может быть меню или виджеты</p>
    <?php endif; ?>
</div>
```

Создала файл comments.php для отображения комментариев и подключила его в single.php и page.php
```
<div class="comments">
    <h3>Комментарии</h3>

    <?php if (have_comments()) : ?>
        <ul>
            <?php wp_list_comments(); ?>
        </ul>
    <?php endif; ?>

    <?php comment_form(); ?>
</div>
```

Создала файл archive.php для отображения архивов записей.
```
<?php get_header(); ?>

<div class="container">
    <h1>Архив</h1>

    <?php if (have_posts()) : while (have_posts()) : the_post(); ?>

        <h2><?php the_title(); ?></h2>
        <p><?php the_excerpt(); ?></p>

    <?php endwhile; endif; ?>
</div>

<?php get_footer(); ?>
```

# Шаг 6. Стилизация темы
Добавила стили для основных элементов темы 
```
.sidebar {
    flex: 1;
    background: white;
    padding: 20px;
    border-radius: 10px;
}

/* пост */
.post {
    margin-bottom: 20px;
}

.post h2 {
    margin-bottom: 10px;
}

.post a {
    display: inline-block;
    margin-top: 10px;
    color: white;
    background: #3498db;
    padding: 8px 12px;
    border-radius: 5px;
    text-decoration: none;
}

header {
    background: #2c3e50;
    color: white;
    padding: 20px;
    text-align: center;
}

footer {
    background: #2c3e50;
    color: white;
    text-align: center;
    padding: 15px;
    margin-top: 20px;
}
```

# Активация темы

В админ-панели WordPress перешла в раздел Appearance → Themes.

Нашла свою тему и активировала её.

Проверила, как отображается сайт с моей темой.

# Контрольные вопросы
1) Какие два файла являются обязательными для любой темы WordPress?

Любая тема в WordPress должна содержать: 1)style.css - это основной файл стилей темы. Также, в нем прописывают методанные темы.  Второй файл, 2)index.php - это основной шаблон темы. Его надо подключить и тогда WordPress будет использовать этот файл для отображения всех страниц и постов.

2) Как подключаются общие части шаблонов (header, footer, sidebar)?

В WordPress используются специальные функции для подключения повторяющихся блоков:

header - ```<?php get_header(); ?>```

footer - ```<?php get_footer(); ?>```

sidebar - ```<?php get_sidebar(); ?>```

Получается, вместо дублирования кода на каждой странице, создаются необходимые файлы и они подключаются с помощью этих функций.

3) Чем отличаются index.php, single.php и page.php?

index.php - это главный шаблон темы. Он используется по умолчанию, если нет других шаблонов.

single.php - это шаблон для отображения одного поста.

page.php - это шаблон для отображения одной страницы.


4) Зачем нужен файл functions.php в теме?

Это файл для добавления PHP-функций и настроек темы.

