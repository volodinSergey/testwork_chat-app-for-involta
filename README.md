# Тестовое задание "Chat-app" для компании Involta
 
 Выполнил небольшое тестовое задание для компании Involta. Необходимо было разработать маленькое приложение-чат на основе данных, которые приходят с сервера с возможностью подгрузки более старых сообщений и добавлением новых.
 
 ## Стэк: 
 - Vue 3
 - Typesctipt
 - Axios

## Powered by Feature-sliced design.
 
 ## Запуск:
 
 - Скачать репозиторий: 
 
 - Установить зависимости:
  ```
    yarn 
  ```
 - Запустить проект:
  ```
    yarn start
  ```
  
  ## Таск-трэкинг
  Настраивать jira долго для тестового задания, ставил себе задачи в [issues](https://github.com/volodinSergey/testwork_chap-app-for-involta/issues?q=is%3Aissue+is%3Aclosed)
  
  ## Анализ и решение проблем:
  - Эмуляция отображения сообщений как в реальном чате. Я захотел сделать внешний вид чата, как в любом нормальном чате, где sms собеседника слева, а того,кто пишет - справа. Так как нам с сервера приходит обычный массив с сообщениями, эту проблему решил стилями. Если index сообщения чётный - просто применяем класс my message, в котором меняется задний фон сообщения и оно прижимается к краю.
  
  - Бесконечная прокрутка сообщений вверх. Истинную бесконечную прокрутку как в ВК и телеграмме не хватило времени реализовать. Для этого необходимо было потратить много времени на изучение техники virtualized list. Взял на вооружение, буду практиковать. Как компромисс, сделал некое подобие бесконечной прокрутки вверх. При загрузке страницы и при дальнейших скроллах наверх, ползунок после загрузки сообщений будет смещаться немного вниз, что позволит опять проскроллить наверх. Таким образом, после скролла будет индикатор загрузки, подгрузка новых сообщений, маленькое смещение скролла вниз  и опять можно скроллить вверх.

- Эмуляция общения с собеседником. Так как мы взялись эмулировать поведение реального чата, хорошо было бы эмулировать саму переписку. Всё, что у нас есть - это форма добавления сообщения и сам список сообщений, с этим можно работать. Так как я стилизовал, что сообщения с чётным индексом уходят вправо, а с нечётным - влево, то при отправлении сообщения они будут по очереди улетать то в правый, то в левый край, создавая иллюзию того, что общаются два человека
 
 - Общая архитектура приложения: об этом ниже
 
  ## Основные архитектура, концепции и подходы, которые использовал при разработке
  
  Несмотря на то, что это маленькое приложение, я решил разработать его с оглядкой на будущую возможность расширения, это очень важно в разработке и в постоянно меняющихся бизнес-условиях.
  
  В качестве базиса я использовал архитектурную методологию Feature-sliced design. Более подробно можно ознакомиться [здесь](https://feature-sliced.design/ru/)
  Если в кратце, методология позволяет нам применить гибкий подход к разработке приложений и низкую связанность компонентов и модулей системы, разбив приложение на сущности, фичи, виджеты, общие части системы, используемые во всех компонентах. Методология как и Vue - прогресивная , то есть нет необходимости добавлять все слои сразу, можно наращивать по необходимости. Я не использую Feature-sliced design в её чистом виде, только основываюсь на ней.
  
## Доменное проектирование 
  Доменный дизайн в приложении следующий:
  - PL (Presentation layer) - слой отображения. Им занимается тот, кто делает это хорошо и должен им заниматься и облегчать на работу - Vue
  - BLL (Business logic layer) -слой бизнес-логики. Все хотелки бизнеса обрабатываются тут
  - DAL (Data access layer) - слой работы с данными. По аналогии с бэкендом, этой слой отвечает за работу с внешними данными (CRUD и так далее). Ничего не знает о бизнесе, только работа с внешним миром и данными
  - Входные и выходные адаптеры. Это overkill для такого приложения, не использовал.

Разберём на примере сущности чата в приложении: 
- Chat repository. Здесь идёт работа с данными. В этом задании мне нужно только получить данные, этим как раз и займётся репозиторий чата. Если бы нам нужно было отправить сообщение на сервер, непосредственно запросы проводились бы в репозитории

- Chat service. Здесь живёт бизнес-хотелка приложения. Здесь тоже есть метод получения сообщений, как и в репозитории, похоже на повторение. Но это не совсем так. Получение сообщений в сервисе - это чисто бизнесовая задача. То есть пришёл заказчик и сказал: мне нужно, чтобы сайт получал сообщения и показывал их юзеру. И ключевое отличие сервиса от репозитория в том , что сервису неважно, каким образом получить сообщения, этим займется репозиторий. Так же данную тему легко будет протестировать, так как мы сможем замокать оригинальный репозиторий, ведь общение классов происходит через интерфейсы.

- Composables . Vue 3 и composition api позволяют вынести stateful логику компонентов в отдельный файл, что я и сделал, упаковав логику получения и работы с сообщениями в кастомный композабл useMessages. На месте этого слоя мог бы быть слой store, если бы мы использовали Vuex, Pinia.

- UI . Не относится напрямую к чистой бизнес-логике, это особенность Feature-sliced design. В каждой отдельной сущности, мы храним ui-отображения, отосящиеся к этой сущности.

 ## Components naming convention
 
 Я стремлюсь к семантически-выверенной архитектуре не долько самого домена и чистой бизнес-логике , но и компонентам. Так же я прокачиваю Feature-sliced design под себя, поэтому вывел для себя следующие наименования компонентов (на примере этого приложения):
 
 - Message.entity.vue : ui-отображение сущности сообщения
 - SendMessage.feature.vue : форма добавления сообщения - это фича, use-case
 - Navbar.widget.vue : большой связный блок приложения. Может компоновать в себе фичи и сущности
 - Loader.ui.vue : переипользуемый ui компонент (ui kit) . Здесь может быть Button.ui.vue, Checkbox.ui.vue and so on...
 - Products.view.vue: пример страниц. Их нет в этом приложении, добавил для информативности
 - Auth.layout.vue: пример лэйаутов. Их нет в этом приложении, добавил для информативности

Подводя итоги, завершил тестовое задание и заложил необходимую базу для дальнейшего расширения приложения.
