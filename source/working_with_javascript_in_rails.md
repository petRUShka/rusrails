Работа с JavaScript в Rails
===========================

Это руководство раскрывает опции для интегрирования функционала JavaScript в ваше приложение Rails, включая опции с использованием внешних пакетов JavaScript, а также как использовать Turbo с Rails.

После прочтения этого руководства вы узнаете:

* Как использовать Rails без необходимости Node.js, Yarn или сборщика JavaScript.
* Как создать новое приложение Rails с помощью карт импорта, esbuild, rollup или webpack, чтобы собрать свой JavaScript.
* Что такое Turbo, и как его использовать.
* Как использовать хелперы Turbo HTML, предоставленные Rails.

--------------------------------------------------------------------------------

(Import maps) Карты импорта
---------------------------

[Карты импорта](https://github.com/rails/importmap-rails) позволяют импортировать модули JavaScript с помощью логических имен, которые направляют к версионированным файлам непосредственно из браузера. Карты импорта включены по умолчанию, начиная с Rails 7, позволяя каждому создавать современный приложения JavaScript с помощью большинства пакетов NPM без необходимости транспиляции или сборки.

Приложениям, использующим карты импорта, для функционирования не нужен [Node.js](https://nodejs.org/en/) или [Yarn](https://yarnpkg.com/). Если планируете использовать Rails с `importmap-rails` для управления зависимостями JavaScript, не нужно устанавливать Node.js или Yarn.

При использовании карт импорта не нужен отдельный процесс для сборки, просто запустите свой сервер с помощью `bin/rails server`, и все заработает.

### Добавление пакетов NPM с помощью importmap-rails

Чтобы добавить новые пакеты в приложение с картой импорта, запустите команду `bin/importmap pin` из терминала:

```bash
$ bin/importmap pin react react-dom
```

Затем импортируйте пакет в `application.js` как обычно:

```javascript
import React from "react"
import ReactDOM from "react-dom"
```

Добавление пакетов NPM с помощью сборщиков JavaScript
-----------------------------------------------------

Карты импорта по умолчанию в новом приложении Rails, но если предпочитаете традиционную сборку JavaScript, можете создать новые приложения Rails с [esbuild](https://esbuild.github.io/), [webpack](https://webpack.js.org/) или [rollup.js](https://rollupjs.org/guide/en/) на ваш выбор.

Для использования сборщика вместо карт импорта в новом приложении Rails, передайте опцию `—javascript` или `-j` в `rails new`:

```bash
$ rails new my_new_app --javascript=webpack
ИЛИ
$ rails new my_new_app -j webpack
```

Каждая из опций сборки поставляется с простой конфигурацией и интеграцией в файлопровод с помощью гема [jsbundling-rails](https://github.com/rails/jsbundling-rails).

При использовании опции сборки, используйте `bin/dev` для запуска сервера Rails и создания JavaScript для development.

### Установка Node.js и Yarn

Если вы используете сборщик JavaScript в своем приложении Rails, должны быть установлены Node.js и Yarn.

Инструкции по установке можно найти на [веб-сайте Node.js](https://nodejs.org/en/download/), а проверить, что он установлен корректно можно с помощью следующей команды:

```bash
$ node --version
```

Должна быть выведена версия вашего Node.js runtime. Убедитесь, что она выше, чем `8.16.0`.

Чтобы установить Yarn, следуйте инструкциям по установке на [веб-сайте Yarn](https://classic.yarnpkg.com/en/docs/install). Запуск этой команды должен вывести версию Yarn:

```bash
$ yarn --version
```

Если она сообщит что-то вроде `1.22.0`, Yarn был установлен корректно.

Выбор между картами импорта и сборщиком JavaScript
--------------------------------------------------

Когда создаете новое приложение Rails, нужно выбрать между картами импорта и решением со сборкой JavaScript. У каждого приложения имеются разные требования, и вам следует тщательно рассмотреть эти требования перед выбором варианта JavaScript, так как миграция от одной опции к другой может быть затратной по времени для больших, сложных приложений.

Карты импорта являются опцией по умолчанию, так как команда Rails верит в потенциал карт импорта для уменьшения сложности, улучшения опыта разработки и улучшения производительности.

Для многих приложений, особенно тех, что в основном полагаются на стек [Hotwire](https://hotwired.dev/) для нужд JavaScript, карты импорта будут правильной опцией в долгосрочном плане. О причинах, лежащих в основе решения сделать карты импорта опцией по умолчанию в Rails 7, можно прочитать [здесь](https://world.hey.com/dhh/rails-7-will-have-three-great-answers-to-javascript-in-2021-8d68191b).

Другим приложениям может все еще быть необходим традиционный сборщик JavaScript. Требования, сигнализирующие, что следует выбрать традиционный сборщик, включают:

* Если ваш код включает шаг транспиляции, такой как JSX или TypeScript.
* Если вам нужны библиотеки JavaScript: включающие CSS, или иным образом полагающиеся на [загрузчики Webpack](https://webpack.js.org/loaders/).
* Если вы абсолютно уверены, что вам необходим [tree-shaking](https://webpack.js.org/guides/tree-shaking/).
* Если вы установили Bootstrap, Bulma, PostCSS или Dart CSS с помощью [гема cssbundling-rails](https://github.com/rails/cssbundling-rails). Все опции, предоставляемые этим гем, кроме Tailwind, автоматически установят `esbuild`, если не указать другую опцию в `rails new`.

Turbo
-----

Выберите ли вы карты импорта или традиционный сборщик, Rails поставляется с [Turbo](https://turbo.hotwired.dev/) для ускорения приложения, значительно уменьшая объем JavaScript, который нужно было бы написать.

Turbo позволяет серверу доставлять непосредственно HTML, в качестве альтернативы превалирующим фронтенд фреймворкам, что уменьшает серверную часть вашего приложения Rails почти до JSON API.

### Turbo Drive

[Turbo Drive](https://turbo.hotwired.dev/handbook/drive) ускоряет загрузки страниц, избегая закрытия и пересоздания на каждом запросе навигации. Turbo Drive это улучшение и замена Turbolinks.

### Turbo Frames

[Turbo Frames](https://turbo.hotwired.dev/handbook/frames) позволяет предопределенным частям страницы быть обновленными по запросу, не влияя на остальное содержимое страницы.

Turbo Frames можно запросто использовать для встроенного редактирования без какого-либо пользовательского JavaScript, ленивой загрузки содержимого или создания интерфейса с вкладками, создаваемыми на сервере.

Rails предоставляет хелперы HTML для упрощения использования Turbo Frames с помощью гема [turbo-rails](https://github.com/hotwired/turbo-rails).

Используя этот гем, можно добавить Turbo Frame в ваше приложение с помощью хелпера `turbo_frame_tag` как тут:

```erb
<%= turbo_frame_tag dom_id(post) do %>
  <div>
     <%= link_to post.title, post_path(path) %>
  </div>
<% end %>
```

### Turbo Streams

[Turbo Streams](https://turbo.hotwired.dev/handbook/streams) доставляет изменения страницы как фрагменты HTML, обернутые в самовыполняемые элементы `<turbo-stream>`. Turbo Streams позволяют транслировать изменения, сделанные другими пользователями, по WebSockets и обновлять части страницы после отправки формы, без запроса загрузки полной страницы.

Rails предоставляет хелперы HTML и серверной части для упрощения использования Turbo Streams с помощью гема [turbo-rails](https://github.com/hotwired/turbo-rails).

Используя этот гем, можно добавить Turbo Streams из экшна контроллера:

```ruby
def create
  @post = Post.new(post_params)

  respond_to do |format|
    if @post.save
      format.turbo_stream
    else
      format.html { render :new, status: :unprocessable_entity }
    end
  end
end
```

Rails будет автоматически искать файл вью `.turbo_stream.erb` и рендерить эту вью после нахождения.

Отклики Turbo Stream также могут рендериться внутри экшна контроллера:

```ruby
def create
  @post = Post.new(post_params)

  respond_to do |format|
    if @post.save
      format.turbo_stream { render turbo_stream: turbo_stream.prepend('posts', partial: 'post') }
    else
      format.html { render :new, status: :unprocessable_entity }
    end
  end
end
```

Наконец, Turbo Streams могут быть инициализированы из модели или фоновой задачи с помощью встроенных хелперов. Эти трансляции могут быть использованы для обновления содержимого с помощью соединения WebSocket всем пользователям, сохраняя содержимое страницы актуальным и оживляя ваше приложение.

Чтобы транслировать Turbo Stream из модели, комбинируйте колбэк модели, наподобие:

```ruby
class Post < ApplicationRecord
  after_create_commit { broadcast_append_to('posts') }
end
```

С настроенным соединением WebSocket на странице, вы должны получить обновление наподобие:

```erb
<%= turbo_stream_from "posts" %>
```

Замены для функционала Rails/UJS
--------------------------------

Rails 6 поставлялся с инструментом с именем UJS, который позволял разработчикам переопределять метод тэгов `<a>`, чтобы выполнять не-GET запросы после нажатия гиперссылки и добавлять диалоги подтверждения до запуска действия. Это было по умолчанию до Rails 7, но теперь рекомендуется использовать Turbo вместо этого.

### Метод

Нажатие ссылок всегда приводит к запросу HTTP GET. Если приложение [RESTful](https://en.wikipedia.org/wiki/Representational_State_Transfer), некоторые ссылки фактически действия, изменяющие данные на сервере, и должны выполняться с помощью не-GET запросов. Этот атрибут позволяет помечать такие ссылки явным методом, таким как "post", "put" или "delete".

Turbo просканирует теги `<a>` в приложении на атрибут данных `turbo-method` и использует указанный метод при его наличии, переопределив действие GET по умолчанию.

Например:

```erb
<%= link_to "Delete post", post_path(post), data: { turbo_method: "delete" } %>
```

Это создаст:

```html
<a data-turbo-method="delete" href="...">Delete post</a>
```

Альтернативой изменения метода ссылки с помощью `data-turbo-method` является использование хелпера Rails `button_to`. По причинам доступности, фактические кнопки и формы предпочтительнее для любых не-GET действий.

### Подтверждения

Можно спросить дополнительное подтверждение у пользователя, добавив атрибут `data-turbo-confirm` на ссылки и формы. Пользователю будет представлен диалог JavaScript `confirm()`, содержащий текст атрибута. Если пользователь выберет отмену, действие не произойдет.

Добавление этого атрибута на ссылках вызовет диалог при нажатии, а добавление его на формы вызовет его при отправке. Например:

```erb
<%= link_to "Delete post", post_path(post), data: { turbo_method: "delete", turbo_confirm: "Are you sure?" } %>
```

Это создаст:

```html
<a href="..." data-confirm="Are you sure?" data-turbo-method="delete">Delete post</a>
```
