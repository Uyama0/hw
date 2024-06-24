# Тулинг

использовать URL: https://www.rigla.ru

- [Стандартный режим](#standart-mode)
- [Замедление CPU 4x slowdown и эмуляцию сети Slow 3G](#slow-mode)

Дополнительные ссылки на профили (network, performance ) в гугл диск:

- [Стандартный network](https://drive.google.com/file/d/1W8SWDehQ99rdOwDQta36ox3esXq0RCnn/view?usp=sharing)
- [Стандартный performance](https://drive.google.com/file/d/1nk8-uDWKTgwe9LebWGxHWVkVxD6IAyKn/view?usp=sharing)
- [Замедленный network](https://drive.google.com/file/d/19sO2X6XrIZuZ4apqLMqeGUQ3Sdx-uEUo/view?usp=sharing)
- [Замедленный performance](https://drive.google.com/file/d/18hbbPSU9JXHLpP1mEjeZ_nb5hvp6bs9i/view?usp=sharing)

> Perfomance профиль с замедлением не загрузился на гитхаб.

<h1 id="standart-mode">Стандартный режим</h2>

## На вкладке Network:

### Записать и сохранить в HAR архив профиль загрузки ресурсов при открытии страницы:

[rigla-standart.ru.har](files\www.rigla-standart.ru.har)

### Дублирование ресурсов:

Запросы возвращают одинаковое тело ответа:

![alt text](screenshots/image1.png)

У двух из трех POST запросов одинаковое тело:

![alt text](screenshots/image3.png)

### Лишний размер ресурсов:

![alt text](screenshots/image6.png)

Ресурсы, у которых resource size = 0, а вес transferred over network заняли заголовки и т.п. (причем вес transferred over network не пропорционален весу заголовков).

![alt text](screenshots/image4.png)
![alt text](screenshots/image5.png)

На самом деле, кроме этих двух запросов особо отметить нечего. Большинство ресурсов имеют заголовок для сжатия (gzip, br), либо их размер слишком мал.

> Я не стал добавлять сюда картинки и шрифты, потому что, по своей идее, их форматы уже должны быть сжаты. Повторное сжатие (gzip, br) часто не дает значительного уменьшения размера или даже может его увеличить.

> В качестве еще одного способа уменьшить количество передаваемой информации можно отметить редукцию (объединение нескольких условных JS файлов в один). Однако в этом случае нужно лучше понимать клиентский код.

### Медленно загружающие ресурсы

Ресурсы с наибольшим временем загрузки (DNS, TCP, SSL, wait, TTFB, TTLB)

![alt text](screenshots/image7.png)

### Блокирующие ресурсы

Ресурсы блокирующие поток загрузки остальных ресурсов (скрипты загружающиеся в head, отсутствуют async/defer атрибуты):

В сочетании с вкладками coverage и lighthouse можно предположить, что `app.js?_t=01971f7a` и `/js/chunk-vendors.js?_t=9f02fabd` являются блокирующими источниками. Кроме того, визуально на графике видно, что именно эти запросы являются блокирующими.

![alt text](screenshots/image34.png)

![alt text](screenshots/image35.png)

А также присутствует один запрос, который падает в ошибку. Потенциально этот запрос мог что-то поломать.

![alt text](screenshots/image8.png)

### Что то еще:

Дополнительно можно обратить внимание на запросы c редиректом 302:

![alt text](screenshots/image10.png)

Здесь редирект повторился дважды на 302:

![alt text](screenshots/image11.png)

301 вероятно следует обновить адрес:

![alt text](screenshots/image24.png)

> Ну и конечно куча аналитики и различных метрик который делают несколько запросов с разными query параметрами 🤷, а некоторые из них вызывают цепочку 302 запросов.

## На вкладке Performance:

### Записать и сохранить в файл профиль загрузки страницы

[Trace-standart.json](files\Trace-standart.json)

### Измерить время в миллисекундах от начала навигации до событий

First Paint (FP): 3635ms

First Contentful Paint (FCP): 3635ms

![alt text](screenshots/image12.png)

Largest Contentful Paint (LCP): 7833ms

![alt text](screenshots/image13.png)

DOM Content Loaded (DCL): 4262ms

![alt text](screenshots/image14.png)

Load (L): 4406ms

![alt text](screenshots/image15.png)

### На каком DOM-элементе происходит LCP:

На элементе img:js-image

![alt text](screenshots/image18.png)
![alt text](screenshots/image16.png)

### Измерить, сколько времени в миллисекундах тратится на разные этапы обработки документа (Loading, Scripting, Rendering, Painting):

- Loading: 23ms
- Scripting: 2533ms
- Rendering: 273ms
- Painting: 53ms

![alt text](screenshots/image17.png)

## На вкладке Coverage:

### Cохранить скриншот вкладки после загрузки страницы:

![alt text](screenshots/image19.png)

![alt text](screenshots/image22.png)

### Измерить в килобайтах объём неиспользованного CSS в ходе загрузки страницы:

~45.4KB

![alt text](screenshots/image20.png)

### Измерить в килобайтах объём неиспользованного JS в ходе загрузки страницы:

~5900KB

![alt text](screenshots/image21.png)

<h1 id="slow-mode">Замедление CPU 4x slowdown и эмуляцию сети Slow 3G</h2>

## На вкладке Network:

### Записать и сохранить в HAR архив профиль загрузки ресурсов при открытии страницы:

[rigla-slow.ru.har](files\www.rigla-slow.ru.har)

Значимых изменений по сравнению со стандартным режимом нет.

## На вкладке Perfomance:

### Записать и сохранить в файл профиль загрузки страницы

[Trace-slow.json](https://drive.google.com/file/d/18hbbPSU9JXHLpP1mEjeZ_nb5hvp6bs9i/view?usp=sharing)

> Гитхабу не понравился большой профиль 😞, поэтому гугл диск 😉.

### Измерить время в миллисекундах от начала навигации до событий

First Paint (FP): 24510ms

First Contentful Paint (FCP): 24510ms

![alt text](screenshots/image26.png)

Largest Contentful Paint (LCP): 110414ms

![alt text](screenshots/image28.png)

DOM Content Loaded (DCL): 35861ms

![alt text](screenshots/image27.png)

Load (L): 119725ms

![alt text](screenshots/image29.png)

### На каком DOM-элементе происходит LCP:

На элементе img.popup-metadata-type-slider\_\_img

![alt text](screenshots/image31.png)
![alt text](screenshots/image30.png)

### Измерить, сколько времени в миллисекундах тратится на разные этапы обработки документа (Loading, Scripting, Rendering, Painting):

- Loading: 140ms
- Scripting: 17475ms
- Rendering: 2281ms
- Painting: 2211ms

![alt text](screenshots/image25.png)

## На вкладке Coverage:

Значимых изменений по сравнению со стандартным режимом нет.

### Cохранить скриншот вкладки после загрузки страницы:

![alt text](screenshots/image32.png)
