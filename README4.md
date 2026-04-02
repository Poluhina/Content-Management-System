# Лабораторная работа №4. Разработка плагина для WordPress
# Цель работы
Освоить расширяемую модель данных WordPress: создать CPT (Custom Post Type), пользовательскую таксономию, метаданные с метабоксом в админ-панели, а также реализовать виджет для отображения данных на сайте.

# Инструкции по запуску проекта
1) Установить и запустить локальный сервер (xampp)
2) Запустить модули Apache и MySQL
3) Скопировать папку в htdocs
4) Открыть в браузере

# Условие
Соберите учебный плагин USM Notes, который добавляет в сайт раздел «Заметки» с приоритетами и датой напоминания.

Примечание: студент может выбрать свою тему для плагина, но функционал должен быть аналогичным.

# Шаг 1. Подготовка среды
В локальной установке WordPress перешла в папку wp-content/plugins.

Создала директорию для своего плагина, (usm-notes).

Включила отладку в wp-config.php, установив define('WP_DEBUG', true);.  Это нужно чтобы видеть ошибки.

# Шаг 2. Создание основного файла плагина
В папке плагина создала файл usm-notes.php.

Добавила в него метаданные плагина (название, описание, версию, автора).

Активировала плагин в админ-панели WordPress.

Убедилась, что плагин активен и ошибок нет.

# Шаг 3. Регистрация Custom Post Type (CPT)
Добавила функцию для регистрации CPT «Заметки» (Notes) с помощью register_post_type().
```
 register_post_type
```

Есть параметры CPT:

- публичный (public),
```
 'public' => true,
```

- поддержка заголовка, редактора, автора, миниатюры,
```
'supports' => array('title', 'editor', 'author', 'thumbnail')
```

- наличие архивной страницы,
```
'has_archive' => true,
```

- иконка в админке.
```
'menu_icon' => 'dashicons-edit',
```

- метки (labels) для удобства использования.
```
 'labels' => array(
            'name' => 'Заметки',
            'singular_name' => 'Заметка',
            'add_new' => 'Добавить заметку',
            'add_new_item' => 'Добавить новую заметку',
            'edit_item' => 'Редактировать заметку',
            'new_item' => 'Новая заметка',
            'view_item' => 'Просмотр заметки',
            'search_items' => 'Поиск заметок'
        ),
```

CPT при инициализации WordPress с помощью хука init.
```
add_action('init', 'usm_register_notes_cpt');
```

Код файла usm-notes.php
```
<?php
/*
Plugin Name: USM Notes
Description: Плагин заметок
Author: Настя
*/

function usm_register_notes_cpt() {

    register_post_type('usm_note', array(
        'labels' => array(
            'name' => 'Заметки',
            'singular_name' => 'Заметка',
            'add_new' => 'Добавить заметку',
            'add_new_item' => 'Добавить новую заметку',
            'edit_item' => 'Редактировать заметку',
            'new_item' => 'Новая заметка',
            'view_item' => 'Просмотр заметки',
            'search_items' => 'Поиск заметок'
        ),
        'public' => true,
        'has_archive' => true,
        'menu_icon' => 'dashicons-edit',
        'supports' => array('title', 'editor', 'author', 'thumbnail')
    ));
}

add_action('init', 'usm_register_notes_cpt');
```

# Шаг 4. Регистрация пользовательской таксономии
Для расширениея функциональности плагина была добавлена пользовательская таксономия "Приоритет", которая предназначена для классификации записей типа "Заметки".

Для создания таксономии была реализована функция ``` usm_register_priority_taxonomy``` , в которой используется встроенная функция WordPress ```register_taxonomy```

Таксономия "Приоритет" была связана с пользовательским типомм записей "Заметки". Это позволяет назначать каждой заметке определенный уровень приоритета.

Параметры таксономии

- иерархическая (как категории)

Параметр ```'hierarchial' => true``` позволяет использовать таксономию аналогично категориям WordRress, создавая структуру приоритетов (High, Medium, Low)

- публичная

Параметр ```'public' => true``` обеспечивает доступность таксономии в административной панели и возможность ее использования на сайте.

- метки (labels) для удобства использования
Для удобства работы в административной панели были добавлены подписи пользователей: добавление, редактирование и поиск приоритетов.

Регистрация через хук init
Функция регистрации таксономии подключена к хуку ```init`` , что обеспечивает ее выполнение при инициализации WordPress

```
add_action('init', 'usm_register_priority_taxonomy');
```

Весь код: 
```
function usm_register_priority_taxonomy() {

    $labels = array(
        'name' => 'Приоритеты',
        'singular_name' => 'Приоритет',
        'search_items' => 'Искать приоритеты',
        'all_items' => 'Все приоритеты',
        'edit_item' => 'Редактировать приоритет',
        'update_item' => 'Обновить приоритет',
        'add_new_item' => 'Добавить новый приоритет',
        'new_item_name' => 'Название нового приоритета',
        'menu_name' => 'Приоритеты'
    );

    register_taxonomy('priority', 'usm_note', array(
        'hierarchical' => true,    // как категории
        'labels' => $labels,
        'public' => true
    ));
}

add_action('init', 'usm_register_priority_taxonomy');
```
Получается что в разделе "Заметки" появился подраздел "Приоритеты". Пользователь может  создавать значения приоритетов и назначать их каждой заметке.

<img width="167" height="162" alt="image" src="https://github.com/user-attachments/assets/dd12daa1-e39b-498a-8022-b1949a3b7253" />

# Шаг 5. Добавление метабокса для даты напоминания
Сдесь создан функционал добавления даты напоминания для пользовательского типа записей "Заметки".

C помощью метабокса add_meta_box() был добавлен пользовательский метабокс.

Метабокс размещен в боковой панели редактора.

<img width="299" height="153" alt="image" src="https://github.com/user-attachments/assets/60e89c42-5d30-440c-a52c-2beaff027f1d" />

Поле выбора даты
```
input type="date"
```
Это поле позволяет пользователю удобно выбрать дату с помощью втроенного календаря. 

Сохранение данных

```
add_action('save_post', 'usm_save_due_date');
```
Это функция обрабатывает данные при сохранении записи.

Обработка ошибок
Если пользователь вводит дату в прошлом, выводится сообщение об ошибке в административной панели.

```
admin_notices
```

<img width="319" height="83" alt="image" src="https://github.com/user-attachments/assets/7a250302-ec50-4ec4-adca-6a5bbd375b71" />

Сообщение когда неь ошибки и дата заметки выбрана верно

<img width="195" height="84" alt="image" src="https://github.com/user-attachments/assets/3aca1f9d-0e9f-4650-b196-83bca4c9a06f" />

# Шаг 6. Создание шорткода для отображения заметок
Создание шоркода позволяет выводить список заметок на сайте с возможностью фильтрацции по приоритету и дате напоминания.

Щорткод поддерживает два параметра: 

- ```priority="X"``` это фильт по приоритету заметки

- ```before_data='YYYY-MM-DD" это фильтр по дате напоминания
  
Функция шорткода
```
function usm_notes_shortcode($atts) {
    $atts = shortcode_atts(array(
        'priority' => '',
        'before_date' => ''
    ), $atts, 'usm_notes');

    $meta_query = array();
    $tax_query = array();

    // Фильтр по дате
    if (!empty($atts['before_date'])) {
        $meta_query[] = array(
            'key' => '_usm_due_date',
            'value' => $atts['before_date'],
            'compare' => '<=',
            'type' => 'DATE'
        );
    }

    // Фильтр по приоритету
    if (!empty($atts['priority'])) {
        $tax_query[] = array(
            'taxonomy' => 'priority',
            'field' => 'slug',
            'terms' => strtolower($atts['priority'])
        );
    }

    $args = array(
        'post_type' => 'usm_note',
        'posts_per_page' => -1,
        'meta_query' => $meta_query,
        'tax_query' => $tax_query
    );

    $query = new WP_Query($args);

    $output = '<div class="usm-notes">';

    if ($query->have_posts()) {
        while ($query->have_posts()) {
            $query->the_post();
            $date = get_post_meta(get_the_ID(), '_usm_due_date', true);
            $terms = get_the_terms(get_the_ID(), 'priority');
            $priority = ($terms && !is_wp_error($terms)) ? $terms[0]->name : '';

            $output .= '<div class="note">';
            $output .= '<h3>' . get_the_title() . '</h3>';
            $output .= '<p>' . get_the_content() . '</p>';
            $output .= '<p><strong>Приоритет:</strong> ' . esc_html($priority) . '</p>';
            $output .= '<p><strong>Дата напоминания:</strong> ' . esc_html($date) . '</p>';
            $output .= '</div>';
        }
    } else {
        $output .= '<p>Нет заметок с заданными параметрами</p>';
    }

    $output .= '</div>';

    wp_reset_postdata();
    return $output;
}

add_shortcode('usm_notes', 'usm_notes_shortcode');
```

Шорткод выводит список заметок на сайте. Можно фильтровать заметки по приоритету и дате напоминания. Заметки отображаются в аккуратных блоках с названием, текстом, приоритетом и датой.


# Шаг 7. Тестирование плагина

# Контрольные вопросы
1) Чем пользовательская таксономия принципиально отличается от метаполя? Приведи пример, когда выбрать таксономию, а когда - метаданные

2) Зачем нужен nonce при сохранении метаполей и что произойдёт, если его не проверять?

3) Какие аргументы register_post_type() и register_taxonomy() чаще всего важны для фронтенда и UX (назови минимум три и объясни почему).
