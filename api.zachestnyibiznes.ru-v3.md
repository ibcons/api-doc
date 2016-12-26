Техническое описание
API v3 (“интеграция”)
=====================================================
портала https://zachestnyibiznes.ru

###Подключение
Для подключения к API v3 (“интеграция”) необходимо зарегистрировать приложение в личном кабинете (https://zachestnyibiznes.ru/user) и связаться с представителями портала для активации доступа.

Для активации доступа необходимо предоставить ip с которого будут отправляться запросы.

###Взаимодействие
Взаимодействие происходит по протоколу https в виде POST запросов.

В API v3 (“интеграция”) доступны три метода:
* Получение списка компаний по строке запроса
* Получение карточки компании по id
* Получение финансовой отчётности (ф1, ф2) компании по id

Обязательные параметры:
* api_key (POST) - ключ api (доступен в личном кабинете)
* ip_client (POST) - ip клиента (посетителя сайта)
* string (POST) - строка запроса (только для получения списка)
* id (POST) - id компании (только для получения карточки и фин. отчетности)

Необязательные параметры:
* page (GET) - страница списка (только для получения списка)

Формат ответа представлен в HTML.

CSS стили API не отдаёт, предполагается стилизация HTML на стороне клиента.

В вёрстке прописаны классы для корректного отображения при подключенном 
bootstrap 3+ (js и css)

###Ограничения
* Лимит rps = 1 (в рамках одного ip_client.)
* Метод запроса - только POST метод
* Доступ к API v3 - только по api_key и ip
* Формат ответа - HTML
* Наличие в ответе рекламных материалов, требуемых к размещению
* Максимальная страница - 10
* CSS классы прописаны в HTML ответа по усмотрению администрации портала
* Ошибки возвращаются в формате JSON, например:

```json
{
  "name": "Too Many Requests",
  "message": "Превышен лимит запросов.",
  "code": 0,
  "status": 429
}
```

###Примеры
Пример получения списка компаний:
> Запрос (POST)
> https://api.zachestnyibiznes.ru/v3/data  
> Параметры  
> 	api_key : test_api_key  
> 	ip_client : 127.0.0.1  
> 	string : сбербанк москва  
> Ответ (HTML)

Пример получения карточки компании:
> Запрос (POST)
> https://api.zachestnyibiznes.ru/v3/data/card  
> Параметры  
> 	api_key : test_api_key  
> 	ip_client : 127.0.0.1  
> 	id : 1027700132195  
> Ответ (HTML)

Пример получения финансовой отчётности компании:
> Запрос (POST)
> https://api.zachestnyibiznes.ru/v3/data/fs  
> Параметры  
> 	api_key : test_api_key  
> 	ip_client : 127.0.0.1  
> 	id : 1027700132195  
> Ответ (HTML)
	
###Пример реализации на php

```php
<?php

if (@$_GET['id']) {
	$params['id'] = $_GET['id'];
	if (@$_GET['fs'])
		$url = 'https://api.zachestnyibiznes.ru/v3/data/fs';
	else
		$url = 'http://api.zachestnyibiznes.ru/v3/data/card';
} elseif (@$_GET['q']) {
	$params['string'] = $_GET['q'];
	$url = 'https://api.zachestnyibiznes.ru/v3/data';
} else {
	$params['string'] = '';
	$url = 'https://api.zachestnyibiznes.ru/v3/data';
}

$params['api_key'] 	= 'test_api_key';
$params['ip_client'] = $_SERVER['REMOTE_ADDR'];

$opts = ['http' =>
    [
        'method'  => 'POST',
        'header'  => 'Content-type: application/x-www-form-urlencoded',
        'content' => http_build_query($params)
    ]
];

try {
	echo file_get_contents($url, false, stream_context_create($opts));
} catch (Exception $e) {
	echo 'Выброшено исключение: ',  $e->getMessage(), "\n";
}

?>
```
