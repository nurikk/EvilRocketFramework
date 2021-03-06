В данном хранилище находятся разработки, ориентированные на решение типичных задач.

Мы, разработчики, вкладывающие лепту в расширение данного склада, стараемся сделать универсальные, эффективные и удобные средства для преодоления самых разных преград и задач.

Сейчас я проведу краткий экскурс по этому хранилищу, чтобы Вы, разработчики, сталкивающиеся с различными проблемами, знали, где можно найти помощь и куда складывать свои наработки.

Итак, приступим.

== Evil ==
Сюда мы складываем свои наработки, которые могут пригодиться не только нам. Как правило, это малосвязные классы, которые позволяют решать как конкретные задачи, так и целые комплексы задач.

Так как это единственное, по чему нет (пока что) нормальной документации, я пройдусь по этому складу идей и решений более подробно.
Чтобы никого не обижать, повествование будет вестись в алфавитном порядке.

== A ==

=== Evil_Access ===
Гибкое, настраиваемое (вообще-то эти слова «гибкое», «настраиваемое», «конфигурируемое» можно отнести ко всему в директории Evil, поэтому далее я не буду повторять их вновь и вновь) средство для определения прав доступа.

На данный момент реализовано две конкретики:

* ''Evil_Access_RBAC''

Определение прав доступа по ролям. Автор Artemy, за что ему огромное спасибо.
Используя простой удобный конфиг, можно очень тонко настраивать права доступа.

* ''Evil_Access_Weighted''

Также доступ по ролям, но в этом решении можно указывать ещё и вес для каждого правила.
Автор BreathLess.

'''Пример'''

Для работы потребуется следующая таблица (указан минимальный набор полей):

(Про префикс см. [[#Evil_Auth]])

'''prefix_users''' — для хранения пользователей.

Поля:
* id,
* role (integer).

С этим плагином рекомендуется использовать Evil_Auth.

После того, как таблица создана, добавьте в application.ini следующую строчку:

 bootstrap.plugins[] = "Evil_Access"

Если Вы используете Evil_Auth, то он должен вызываться раньше Evil_Access.

Конец примера

=== Evil_Auth ===
Авторизация. <br />
Думаю, с подобной задачей сталкивается каждый разработчик. Используя богатый опыт, общими усилиями мы создали этот продукт. Данная авторизация уже сейчас поддерживает Basic, Digest, OpenId, Native авторизации, и этот список постоянно растёт.<br />
Автор BreathLess<br />
Соавторы Se#, Nur

'''Пример'''

Для работы потребуется следующая таблица (указан минимальный набор полей):

Сразу скажу, что «prefix» нужно заменить на префикс Вашей БД. Если у Вашей БД нет префикса, то я настоятельно рекомендую его всё же завести. Указывается он в application.ini:

 resources.db.prefix  = "prefix_"

'''prefix_tickets''' — для хранения тикетов или, лучше сказать, билетиков.

Для аналогии представьте, что Ваше приложение — это автобус (или любой другой общественный транспорт), а посетитель — пассажир. Когда человек заходит, он получает билетик, который идентифицирует его в текущем транспорте. Если пассажир вышел из автобуса, то билетик теряет свою силу — так же и здесь.

Дамп таблицы:

 CREATE TABLE IF NOT EXISTS `prefix_tickets` (
  `id` char(64) COLLATE utf8_unicode_ci NOT NULL,
  `seal` char(128) COLLATE utf8_unicode_ci NOT NULL,
  `created` int(11) NOT NULL,
  `user` int(11) NOT NULL,
  PRIMARY KEY (`id`),
  KEY `seal` (`seal`),
  KEY `user` (`user`)
 ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;

В качестве идентификатора пользователя при создании тикета используется Zend_Registry::get('userid').

После того, как таблица создана, добавьте в application.ini следующую строчку:

 bootstrap.plugins[] = "Evil_Auth"

Конец примера

=== Evil_Action ===
Средство, позволяющее писать «внешние» экшены для контроллеров. Таким образом, один раз создав экшен, вы можете использовать его в любом контроллере.
Автор BreathLess

== B ==

=== Evil_Basencoder ===
Кодирование по базе, от 1 до 58 <br />
Автор BreathLess

=== Evil_Bootstrap ===
Универсальный бутстрап, подключающий плагины и прочие вкусняшки. Создавался на личном опыте, проверен в бою.<br />
Автор BreathLess<br />
Соавтор Se#

'''Пример'''

Чтобы использовать это решение, необходимо создать (если по каким-то причинам Вы его ещё не создали) файл по адресу^

 Application/Bootstrap.php

со следующим содержанием:

 <?php class Bootstrap extends Evil_Bootstrap {}

Всё. Теперь, чтобы, например, использовать плагины из текущей библиотеки (Evil), Вам достаточно в своём конфиге application.ini добавить строчку вида:

 bootstrap.plugins[] = "Evil_Auth"

== C ==

=== Evil_Captcha ===
Капча. На данный момент реализовано средство для удобного использования ре-капчи.<br />
Автор Se#

'''Пример'''

* В контроллере:

 // Ключи можно получить на официальном сайте ре-капчи
 $this->view->pub = публичный_ключ;
 $this->view->pri = приватный_ключ;
 $post = POST_данные_из_формы; 
 
 if(null != ($capError = Evil_Captcha_Recaptcha::challenge(приватный_ключ, $post)))
 	$this->view->captchaError = $capError;
	
* В шаблоне:

 <pre> <div class="captcha"> 
 	<?php new Evil_Captcha_Recaptcha($this->pub, $this->pri, true, false, $this->captchaError) ?>
 </div>
 </pre>

=== Evil_Controller ===
Позволяет использовать Evil_Action.<br />
Автор BreathLess

'''Пример'''

Для использования достаточно заэкстендить Ваш контроллер:

 class MyController extends Evil_Controller{}

=== Evil_Composite ===
Коллекция. Используется для работы с наборами данных. На текущий момент созданы следующие:
* Флюидная (позволяет использовать динамическое число гибких полей)
* Фиксированная.
* Гибридная (объединяет две предыдущих)<br />
Автор BreathLess

'''Пример'''

 // Параметром передаётся имя таблицы, без префикса и постфикса
 $composite = new Evil_Composite_Fixed('tableName');
 // Получаем все строки, в которых key = value (весь список селекторов можно найти в самом классе)
 $data = $composite->where('key', '=', 'value'); 

Однако, рекомендуется использовать данный класс через Evil_Structure:

 $composite = Evil_Structure::getComposite('tableName');

В этом случае, руководствуясь конфигом, коллекция будет создана требуемого для данной таблицы типа.

== D ==

=== Evil_DB ===
ORM для работы с БД. Поддерживает профилирование, логирование, кеширование.<br />
Автор BreathLess

=== Evil_Defense ===
Комплекс защитных средств. На данный момент содержит реализацию имитозащиты, которая позволяет избежать дублирования данных из внешних источников.<br />
Автор Se#

=== Evil_Event ===
Логирование событий с бешеной скоростью. Поддерживает фильтры по событиям, свёртки и прочие фичи.<br />
Для работы требуется Redis.<br />
Автор BreathLess

=== Evil_Exception ===
Исключения. Позволяет гибко настраивать поведение при эксепшенах. На данный момент реализованы:
* редирект (страница для редиректа настраивается в конфиге);
* вывод сообщения.<br />
Автор BreathLess

'''Пример'''

 public function someAction()
 {
       if('goAway' == $this->_getParam('do'))
             throw new Evil_Exception('Go away!', 345);
 }

В конфиге указываем

 "345" : "RedirectToAuth"

В итоге при эксепшене с кодом 345 мы получим редирект на страницу /auth

== F ==

=== Evil_Factory ===
Простая, удобная фабрика классов и методов, позволяющая регистрировать синглтоны.<br />
Автор BreathLess

Пример

 <?php 
 // Создаём класс для управления конфигами, и передаём сразу путь до настроек
 $config = Evil_Factory::make('Zend_Config_' . ucfirst($extension), $pathToConfig);
 
 // Создадим синглтон:
 $appConfig = Evil_Factory::singleton('Evil_' . ucfirst($extension), $pathToConfig);

=== Evil_Fn ===
Порт Кодеина на Зенд.

== H ==

=== Evil_Html ===
Простое, удобное средство для создания HTML-кода в нотации SimpleXMLElement.<br />
Автор Se#

== I ==

=== Evil_I18N ===
Работа с переводами.<br />
Автор BreathLess

=== Evil_IP ===
Геоайпи и регистрация айпишника.<br />
Автор BreathLess

=== Evil_Identity ===
Идентификаторы. Позволяет удобно оперировать различными идентификаторами пользователей.<br />
Автор Se#

== J ==

=== Evil_Json ===
Простой и удобный механизм для работы с JSON. Позволяет добавлять комментарии в JSON-конфиги, а также удобно обновлять/сохранять конфиги в файл. Имеется возможность запретить изменять конфиг.<br />
Автор Se#

'''Пример'''

Пусть у нас есть конфиг

 /configs/user_manager.json

со следующим содержанием:

 {
 "moderation" :
    {
        "#" : "If moderation is off, than all options, which use this option, will be ignored",
        "fields" :
        {
            "#owner"  : "user uid",
            "owner"   : "string",
 
            "#field"  : "user property, for example 'nickname'",
            "field"   : "string",
 
            "#oldValue" : "previous field value",
            "oldValue"  : "string",
 
            "#newValue" : "new field value",
            "newValue"  : "string"
        },
        "createIfNotExist" : "Yes",
        "on" : "true"
    }
 }

Получаем конфиг (во втором параметре передаём массив с настройками для класса):

 <?php
 // cm - Comment Marker: маркер комментариев;
 // readOnly - думаю, понятно.
 $config = new Evil_Json(APPLICATION_PATH.'/configs/user_manager.json', array('cm'=>'#', 'readOnly'=>false));

При обработке конфига строк с комментариями уже нет, поэтому можно не беспокоиться, что они повредят Вашему алгоритму.

Получим настройку «oldValue»:

 $config->moderation->fields->oldValue

Изменим настройку «on» (т.е. выключим модерацию):

 $config->moderation->on = 'off'

Нам точно нужен массив:

 $array = $config->toArray();
 // или хотим получить строкой:
 $string = $config->toString();

Теперь сохраним изменения:

 $config->save();

К слову, в метод save() можно передать новый путь:

 $config->save(APPLICATION_PATH . '/configs/um_moderation_off.json');

Кроме этого, можно посмотреть все комментарии из конфига

 $config->getComments();

а также комментарий к конкретному полю:

 $config->getComment('moderation.fields.#field')

Вернёт комментарий к полю field.

== L ==

=== Evil_Layout ===
Управление шаблонами. Позволяет удобно назначать шаблоны для контроллеров.<br />
Автор BreathLess

=== Evil_Locator ===
Поиск файлов.<br />
Автор BreathLess

=== Evil_Log ===
Логгер. Может писать куда угодно. Имеет монитор, позволяющий отслеживать любые передвижения по сайту.<br />
Автор BreathLess<br />
Соавтор Se#

== M ==

=== Evil_Moderation ===
Модерация как пользователей в целом, так и любых полей.<br />
Автор Se#

== O ==

=== Evil_Object ===
Объекты различного рода для использования в коллекциях.<br />
Автор BreathLess

== S ==

=== Evil_Sensor ===
Позволяет использовать сенсоры — фиксаторы изменений каких-либо значений — и отображать красивые графики.<br />
Автор BreathLess

=== Evil_Structure ===
Фабрика структур данных. <br />
Автор BreathLess

== T ==

=== Evil_Template ===
Шаблонизатор. <br />
Автор BreathLess <br />
Соавтор Artemy

Этот список постоянно обновляется, пополняется, совершенствуется и развивается.
Огромное спасибо всем, кто принимает участие в развитии данного хранилища.

Хранилище доступно на чтение всем сотрудникам.

Данную статью буду пополнять по мере изменений.
Также планируется добавить наглядные примеры для каждого из упомянутых средств.

Напоследок:
Если у Вас возникли затруднения с использованием того или иного средства,
смело обращайтесь к его авторам (либо ко мне) за помощью.
