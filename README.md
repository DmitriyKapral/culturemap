# culturemap

## Компоненты
Главный компонент под названием App.vue находиться в папке src\
Все дочерние компоненты находяться по пути: src/components\
В главном компоненте App находятся запросы к серверу, инициализация карты с помощью библиотеки Leaflet, строка поиска, общие стили css, связь с дочерними компонентами и передача данных в них\
Компонент GetMarkers предназначен для вывода маркеров на карту с помощью библиотеки Leaflet\
Компонент infoPanel предназначен вывода информации об объекте, а также вывода мероприятий в этом объекте\
Компонент EventsPanel предназначен для вывода подробной информации об выбранном мероприятии\
Компоненты FilterObject и FilterEvents предназначены для вывода возможности фильтрации данных\
В AnalysisPanel выводиться панель с графиком для анализа\
Собственные UI находяться в папке src/components/UI. Среди них есть кнопка - Btn, всплывающий список - MySelect и всплывающий список с возможностью выбрать множество - MyMultiSelect. MyFilter предназначен для вывода модального окна\
В папке public/icons храняться картинки для отображения мест досуга



## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
