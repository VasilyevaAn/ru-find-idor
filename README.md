# Библиотека приемов по поиску IDOR(BOLA)

# Что такое IDOR?

IDOR (Insecure direct object references), что переводится как небезопасная прямая ссылка на объект. Уязвимость со стороны бэкенда: возможность манипулировать сущностями.

# Приемы

* Если есть несколько параметров, то проверить для каждого из них. Например, удаление файла из сделки осуществляется по deal_id, phone, file_id. Добавив deal_id, phone своей сделки, а file_id чужой, можно удалить чужой файл
```
DELETE /api/file?deal_id=X&phone=Y&file_id=Z
Host: test
Content-Type: application/json-patch+json
```
* Если метод возвращает ошибку, то проверьте что сущность дествительно не создалась и не удалилась. Бывали случае, когда метод возвращал ошибку, но сущность все равно удалялась.
* Измените метод. Нельзя создать сущность через POST, попробуйте через PUT, PATCH
* Попробуйте изменить версию метода. Часто старые версии v1 не отключают, но они не содержат всех проверок.
* Попробуйте скопировать в тело ответ метода - иногда все что пришло на вход записывется напрямую в MongoDB
* Если id = GUID и его не предсказать, проверьте можно ли его получить. Например файлы доступны всем.
```
DELETE /api/file?file_id=17f53ad7-e5ee-4fe3-8c6f-05ed5be9ffd0
Host: test
Content-Type: application/json-patch+json
``` 
* id может выглядеть не предсказуемо, но это не так. Например, в старой версии монги(до 3.4 можно было предсказать ObjectID). Подробнее **[IDOR through MongoDB Object IDs Prediction](https://techkranti.com/idor-through-mongodb-object-ids-prediction/)**
``` 
A BSON ObjectID is a 12-byte value consisting of a 4-byte timestamp (seconds since epoch), a 3-byte machine id, a 2-byte process id, and a 3-byte counter
``` 
* Самые интересные IDOR находятся не в одном методе, а в сценарии. **[Account takeover in cups.mail.ru](https://medium.com/kminthein/account-takeover-in-cups-mail-ru-bdab1483f92c)**
* Попробовать изменить Content-type
``` 
application/xml -> application/json
``` 

* Бывает данные могут быть доступны благодаря кешированию на веб-сервере.
* Попробуйте заменить число строкой
``` 
"account_id": "198442" -> "account_id": 198442
``` 
# Инструменты

* IDOR легко тестируется: любым инструментом для тестирования api. Для анализа и blackbox тестирования рекомендую -  Burp Suite Community
* Можно добавлять в сценарии api-автотестов

# Расширения для Burp Suite для поиска IDOR

* Authorize - самый удобный на мой взгляд инструмента. Очень простой и с подсветкой
* AuthMatrix
* AutoRepeater

# Где искать IDOR?

* Во всех методах CRUD: создание (англ. create), чтение (read), модификация (update), удаление (delete)

# На чем потренироватся

* **[OWASP Juice Shop](https://github.com/juice-shop/juice-shop)**
* **[web security academy](https://portswigger.net/web-security)**
* На своем рабочем проекте)
