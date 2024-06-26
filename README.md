# appsec_cloud_test

##  Вопросы для разогрева
1. Security code review, анализ уязвимостей, моделирование угроз, тестирование на проникновение
2. В пет-проектах сталкивался с тем, что приходилось проводить security code review для защиты кофид. информации, например, защитить форму от sql иньекций.
3. -
4. Мне кажется это круто изучать что-то на стыке, особенно таких направлений, как ифнобез и по, эти навыки будут полезныими для разработчиков в любой области. Также стажировка это возможность показать себя, чтобы в дальнейшем быть в компании и помогать ей расти и самому развиваться вместе с ней.

## 2. Security code review

### Часть 1. Go
- query := fmt.Sprintf("SELECT * FROM products WHERE name LIKE '%%%s%%'", searchQuery.
Параметр принимаемый через get запрос, может содержать sql иньекцию, и может дайти до того, что все данные будут удалены.
Исправление. Лучше всего, использовать параметры в запросе:
query := fmt.Sprintf("SELECT * FROM %s WHERE name=$1", productsTable)
row := r.db.QueryRow(query, searchQuery)

- db, err = sql.Open("mysql", "user:password@/dbname")
Пароль хранится в открытом виде, если хакер получит доступ к логам, то может получит доступ и к самой бд.
Исправление: Вынести пароль и имя дб в конфиг файл или в файл окружения .env

### Часть 2. Python
2.1)
-output = Template('Hello ' + name + '! Your age is ' + age + '.').render() 
Исправление. стоит использовать параметры и лучше всего экранировать их:
output = Template('Hello {{ name }}! Your age is {{ age }}.').render(name=name, age=age)
output = Template('Hello {{ name|escape }}! Your age is {{ age|escape }}.').render(name=name, age=age) - Предпочтительный способ, который гарантирует что html код будет преобразован в безопасный вид.

2.2)
= output = subprocess.check_output(cmd, shell=True, text=True)
Юзер с помощью спец символ может провести командную инъекцию
Исправление. Можно использовать параметр shell=False, в таком случае команда будет интерпретироваться системой,
а т.к. система не обрабатывает спец. символы, то сложно будет провести командную инъекцию

## 3. Моделирование угроз

Потенциальные проблемы безопасности и их последствия:
1) Отсутсвие фильтрации запросов на микрофронте может привести к успешной DDoS атаке. Следствия: упал сервер, бизнес не получает деньги.
2) Изображения могут быть заражены вирусами или вредоносными программами, поэтому остутвие их проверки может привести к проблемам. Следствия: от кражи данных до падения сервера.
3) SQL иньекции также возможны. Следствия: Потеря данных в БД, получение данных других пользователей.
4) Остутвие шифрования чувствительных данных.  Следствия: кража чувствительных данных и их дальнейшая манипуляция.
5) Угрозы безопасности на уровне пользователя, пользователи могут получить несанкционированный доступ к настройкам отправки уведомлений или загруженным изображениям других пользователей. Следствия: манипулирование этими данными , отток клиентов бизнеса
6) Внешние сервисы могут иметь уязвимости или недостаточные механизмы безопасности. Следствия: утечки данных, поломка работы веб-приложения

Расскажите, какие способы исправления уязвимостей и смягчения рисков вы можете предложить по отмеченным вами проблемам безопасности?
1) Обеспечение строгой аутентификации и авторизации, включая множественную факторную аутентификацию и ограничение доступа только к необходимым ресурсам и функциям.
2) Регулярная проверка и обновление микрофронта и его зависимостей для исправления уязвимостей.
3) Проверка безопасности внешних сервисов и использование только тех, которые имеют надежные механизмы защиты.
4) Защита базы данных с помощью строгих прав доступа, шифрования данных, обновления ПО для исправления уязвимостей.
5) Регулярное проведение аудита безопасности.

Напишите список уточняющих вопросов, которые вы бы задали разработчикам данного сервиса?
1) Какие механизмы аутентификации и авторизации используются в сервисе?
2) Какие меры безопасности принимаются для защиты микрофронта от уязвимостей, таких как XSS и CSRF?
3) Как фильтруются запросы и есть ли ограничение запросов от одного клиента?
4) Какие механизмы защиты используются для внешних сервисов, таких как сервис аутентификации(Auth) и объектное хранилище (S3)?
5) Как обеспечивается безопасность PostgreSQL?
6) Какие меры безопасности предусмотрены для предотвращения несанкционированного доступа к системе и изменения пользовательских конфигураций?
7) Проводится ли регулярная проверка безопасности и обновление ПО?
8) Проверяются ли изображения на "зараженность"?. Если да, то как?
