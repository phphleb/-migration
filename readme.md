# Базовые миграции для фреймворка HLEB
Эти миграции реализуют минимальный набор стандартных действий, основное их предназначение - выполнить заготовленные запросы и зафиксировать в базе данных факт их выполнения, предотвращая повторное.
Для этого во фреймворке должна быть подключена база данных.

Поддержка  __MySQL__ / __MariaDB__ / __PostgreSQL__

#### Установка
```bash
$ composer require phphleb/migration
```
#### Развертывание
```bash
$ php console phphleb/migration --add
```

Далее можно создать шаблон миграции при помощи консольной команды:
```bash
$ php console migration/create example_name
```

Эта команда создаст в папке `database/migrations` (или `migrations` при её отсутствии) проекта новую миграцию с именем 'Migration_XXX_example_name.php' (где индекс ХХХ - текущее UNIX-время в миллисекундах). Если папки `database/migrations` или `migrations` нет в
корневой директории проекта, то нужно создать её вручную. Если эта папка вас не устраивает, нужно будет переписать классы консольных команд с указанием директории
и использовать собственные, развертывание библиотеки при этом можно не применять. Но если используется одна база данных, этих двух вариантов расположения должно хватить.

В классе 'Migration_XXX_example_name' файла миграции метод **up**(), в который можно добавить один или более SQL-запросов с помощью метода **addSql**(...). Аналогичным
образом в методе **down**() содержатся запросы, котрые откатывают выполнение метода **up**(). С последним методом нужно быть осторожным, многие его оставляют пустым и не используют,
но если он обязателен в проекте, нужно следить за его актуальностью.

```php
<?php

class Migration_XXX_example_name extends \Phphleb\Migration\Src\StandardMigration
{
   public function up()
   {
      $this->addSql("CREATE TABLE table_name");
   }

   public function down()
   {
      $this->addSql("DROP TABLE table_name");
   }

}

```

Также есть команда для выполнения миграций:

```bash
$ php console migration/run
```

Выполнит все незафиксированные миграции (ранее не выполненные) и зафиксирует их в таблице `migrations` базы данных, последняя указана в настройках проекта (конфигурации БД) как основная. 
Соответственно, все SQL-запросы миграций могут быть выполнены только к этой БД. Для второстепенной базы данных из конфигурации также можно дополнительно создать свои консольные
команды на создание и выполнение с указанием конкретного названия БД.

Перед выполнением миграций эта команда поможет уточнить список незафиксированных миграций в проекте, не выполняя сами миграции:

```bash
$ php console migration/status
```

Следующая команда предназначена для отката миграций. Если при выполнении использовались запросы из метода **up**(), то теперь выполнятся из метода **down**(). Порядок выполнения
миграций будет в обратном порядке, то есть сначала выполнится последняя зафиксированная, потом предпоследняя и тд. Если указать число в виде аргумента команды, то на это количество миграций произойдёт откат.
По умолчанию на одну. Если нужно откатить все миграции, то нужно аргументом указать '--all', будет применено ко всем _существующим_ миграциям.

```bash
$ php console migration/rollback 2
```

При выполнении указанной команды будет произведён откат на две миграции с конца. Отсчет идёт с зафиксированных миграций, уже выполненных.


_В случае, если вы хотите добавить миграции на уже существующий проект с базой данных некоторого размера, создайте первую миграцию из дампа структуры этой БД, 
добавив к созданию таблиц выражение 'IF NOT EXISTS', после чего выполните миграции._

#### Обновление

```bash
$ composer update phphleb/migration
$ php console phphleb/migration --add
$ composer dump-autoload
```

-----------------------------------


[![License: MIT](https://img.shields.io/badge/License-MIT%20(Free)-brightgreen.svg)](https://github.com/phphleb/draft/blob/main/LICENSE) ![PHP](https://img.shields.io/badge/PHP-^7.1.0-blue) ![PHP](https://img.shields.io/badge/PHP-8-blue)
