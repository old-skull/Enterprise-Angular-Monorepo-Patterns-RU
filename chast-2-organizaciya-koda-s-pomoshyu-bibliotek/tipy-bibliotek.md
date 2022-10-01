# Типы библиотек

В рабочем пространстве существует множество различных типов библиотек. Для поддержания определенного порядка мы рекомендуем иметь только четыре (4) типа библиотек:

* **Feature**: Разработчики должны рассматривать **feature** библиотеки как библиотеки, которые реализуют "умный" UI (с инжектированными сервисами) для конкретных **сценариев использования в бизнесе** или страниц приложения.
* **UI**: Библиотека пользовательского интерфейса содержит только <mark style="color:red;">`презентационные`</mark> компоненты.
* **Data-access**: Data-access библиотека содержит сервисы и утилиты для взаимодействия с бэкэндом. Она также включает весь код, связанный с управлением состоянием.
* **Utility**: Библиотека утилит содержит **общие утилиты и сервисы**, используемые многими библиотеками и приложениями.

Прежде чем мы рассмотрим все типы библиотек, давайте быстро пройдемся по некоторым базовым понятиям о типах компонентов пришедшими из Flux, чтобы мы могли понять что такое "Умные" (Smart) и "Презентационные" (Presentational) компоненты.

### Заметка: Умные и презентационные компоненты

При создании приложений с однонаправленным потоком данных существует два типа компонентов: те, которые могут взаимодействовать с остальной частью приложения, получая и отправляя данные (известные как "умные" компоненты), и те, которые только получают данные (известные как "презентационные" или " глупые" компоненты).

#### Умные компоненты

Эти компоненты управляют или делегируют бизнес-логику и используют DI для внедрения сервисов. Они могут посылать обновления остальной части приложения путем dispatch'a экшенов. У них есть дочерние экземпляры презентационных компонентов: умные компоненты передают данные дочерним презентационным компонентам и реагируют на события, исходящими из дочерних компонентов, чтобы затем обработать их по мере необходимости в приложении.

#### Презентационные компоненты

Эти компоненты имеют очень мало или вообще не имеют бизнес-логики. Они полагаются **только** на _Inputs_ и _Outputs_ для связи с внешним миром. Их единственная цель - отображать данные и принимать пользовательский ввод, но не обрабатывать его. Скорее, эти компоненты передают события через Outputs родительским компонентам, которые знают, как их обрабатывать.

Они очень удобны для многократного использования, их легче всего тестировать, они могут быть полностью общими/доменными (например, data-table) или иметь домен-контекст (например, log-book-table).

Теперь, когда мы знаем терминологию для Умных и Презентационных компонентов, давайте рассмотрим типы библиотек, которые их содержат. Feature библиотеки содержат умные компоненты, а ui библиотеки - презентационные компоненты.

### Feature библиотеки

#### Что это такое?

Feature библиотека содержит набор файлов, которые конфигурируют бизнес-сценарий или страницу в приложении. Эти библиотеки содержат ngModule, который определяет поведение этой части приложения (он может содержать фрагмент Store, управлять собственным роутингом в пределах определенного раздела приложения и может быть лениво загружен в приложение).&#x20;

Большинство компонентов в такой библиотеке - это умные компоненты, которые взаимодействуют с NgRx Store. Этот тип библиотеки также содержит большую часть UI логики, валидацию форм и т.д. Feature библиотеки почти всегда являются специфичны для конкретного приложения и часто загружаются в ленивом режиме.

#### Соглашение об именовании

<mark style="color:red;">`feature`</mark> (если вложенный) или <mark style="color:red;">`feature-*`</mark> (например, <mark style="color:red;">`feature-shell`</mark>).

```
libs/
  booking/
    feature-shell/ (1)
      src/
        index.ts
        lib/
          booking-feature-shell.module.ts
```

1. <mark style="color:red;">`feature-shell`</mark> - это feature библиотека конкретного приложения (в данном случае приложения "booking").

_Пример 1. Рассмотрим использование feature компонента_

> В нашем приложении nrwl-airlines мы хотим начать работу над UI для приложения booking. Мы хотим предоставить пользователю три экрана: поиск рейса, информация о пассажире и карта мест.

Давайте представим, что мы размещаем роутинг для страниц непосредственно в приложении <mark style="color:red;">`booking/desktop`</mark>.

{% code overflow="wrap" %}
```typescript
export routes = [
  {
    path: '',
    pathMatch: 'full',
    component: 'FlightSearchComponent'
  },
  {
    path: '/passenger',
    pathMatch: 'full',
    loadChildren: '@nrwl-airlines/booking/feature-passenger-info#BookingFeaturePassengerInfoModule'
  },
  {
    path: '/seatmap',
    pathMatch: 'full',
    loadChildren: '@nrwl-airlines/shared/seatmap/feature-seat-listing#SharedSeatmapFeatureSeatListingModule'
  }
]
```
{% endcode %}

Если роутинг будет помещен в <mark style="color:red;">`apps/booking/desktop`</mark>, нам нужно будет продублировать его в <mark style="color:red;">`apps/booking/mobile`</mark>, и поддерживать их синхронизацию в дальнейшем. Чтобы следовать принципу DRY, нам нужно вынести роутинг в общий файл.

Мы можем вынести роутинг в отдельную библиотеку. Эта библиотека будет содержать структуру роутинга для <mark style="color:red;">`booking`</mark>, а также выполнять любую инициализацию. Модуль в этой библиотеке также может быть лениво загружен в оба родительских приложения <mark style="color:red;">`booking/desktop`</mark> и <mark style="color:red;">`booking/mobile`</mark>.

Соответствующее соглашение об именовании - это <mark style="color:red;">`BookingFeatureShellModule`</mark>, расположенный в <mark style="color:red;">`libs/booking/feature-shell`</mark>, как показано в примере выше. Теперь у нас есть четыре feature библиотеки: <mark style="color:red;">`booking/feature-shell`</mark>, <mark style="color:red;">`booking/feature-flight-search`</mark>, <mark style="color:red;">`booking/feature-passenger-info`</mark> и <mark style="color:red;">`shared/seatmap/feature-seat-listing`</mark>. Каждая из них соответствует экрану в приложении.

#### Пример feature модуля библиотеки

{% code overflow="wrap" %}
```typescript
import { NgModule } from '@angular/core';
import { RouterModule } from '@angular/router';
import { CommonModule } from '@angular/common';

import { FlightSearchComponent } from './components/flight-search/flight-search.component';

@NgModule({
  imports: [
    CommonModule,
    RouterModule.forChild([ // (1)
      {
        path: '',
        component: FlightSearchComponent,
        pathMatch: 'full'
      },
      {
        path: '/passenger',
        pathMatch: 'full',
        loadChildren: '@nrwl-airlines/booking/feature-passenger-info#BookingFeaturePassengerInfoModule'
      },
      {
        path: '/seatmap',
        pathMatch: 'full',
        loadChildren: '@nrwl-airlines/shared/seatmap/feature-seat-listing#SharedSeatmapFeatureSeatListingModule'
      }
    ])
  ],
  declarations: [FlightSearchComponent]
})
export class BookingFeatureShellModule {} // (2)
```
{% endcode %}

1. Обратите внимание на использование <mark style="color:red;">`.forChild`</mark>. Мы никогда не будем использовать <mark style="color:red;">`.forRoot`</mark> в feature библиотеке - это вариант используется только для приложений.
2. Путь к этому модулю будет следующим <mark style="color:red;">`libs/booking/feature-shell/src/booking-feature-shell.module.ts`</mark>

См. раздел "Параметры командной строки" для обсуждения опций командной строки, доступных для генерации лениво загружаемого шаблона для библиотек функций.&#x20;

Теперь, когда мы рассмотрели, где размещать умные компоненты, давайте рассмотрим где размещать презентационные компоненты.
