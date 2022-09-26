# Вкратце

Приложения имеют тенденцию увеличиваться в размере и сложности с течением времени, и эта тенденция усиливается, когда есть несколько приложений, которым необходимо совместно использовать код. Крупные организации и бизнес-подразделения должны рассмотреть способы разделения приложений по размеру и сложности, а также повторного использования частей приложений, чтобы обеспечить консистентность в их реализации.

Библиотеки, которые представляют собой просто набор связанных файлов, выполняющих определенную задачу, помогают в модульной разработке. Они поставляются с четко определенным публичным API в файле <mark style="color:red;">`index.ts`</mark>, который диктует, как их использовать. Разработчики также должны включать README для документирования поведения библиотеки и иметь определенного владельца кода (более подробную информацию см. в разделе "Владение кодом" в пятой части книги).

В рабочем пространстве Nx библиотеки требуют классификаторов для описания их содержимого и предполагаемого назначения. Эти классификаторы помогают организовать библиотеки и обеспечить способ их визуального различения. Существует два основных классификатора: "scope" и "type". Могут быть дополнительные классификаторы, чтобы помочь при различных сценариях в конкретной организации, например, "platform".

### Scope

Область применения относится к логической группировке, сценарию использования в бизнесе или домену. Примерами областей применения в нашем примере приложения являются карта мест (<mark style="color:red;">`seatmap`</mark>), бронирование (<mark style="color:red;">`booking`</mark>) и регистрация (<mark style="color:red;">`check-in`</mark>). Они содержат библиотеки, которые управляют субдоменом логики приложения.

Для обозначения области применения рекомендуется использовать структуру папок.&#x20;

Следующая структура папок является примером иерархии карты мест (seatmap):

```
shared/
  seatmap/
    feature/
```

Здесь "shared" и "seatmap" - это группирующие папки, а "feature" - это библиотека, вложенная на два уровня вглубь. Это четко показывает, что данная фича принадлежит домену <mark style="color:red;">`seatmap`</mark>, который является поддоменом <mark style="color:red;">`shared`</mark>.

Тег, используемый в этой библиотеке, будет записан как <mark style="color:red;">`scope:shared`</mark>, так как это домен верхнего уровня.

### Type

Type относится к содержимому библиотеки и указывает на ее назначение и цель использование. Примерами type являются: <mark style="color:red;">`ui`</mark>, <mark style="color:red;">`data-access`</mark> и <mark style="color:red;">`feature`</mark>. Более подробное обсуждение см. ниже.

Мы рекомендуем использовать **префиксы и теги для обозначения type**. Рекомендуется ограничить количество типов только четырьмя, описанными в последующих разделах.

Имя папки для этой функции будет <mark style="color:red;">`feature-shell`</mark>, чтобы использовать префикс для типа библиотеки.

Тег для фичи библиотеки <mark style="color:red;">`seatmap`</mark>, как в предыдущем примере, теперь будет <mark style="color:red;">`scope:shared,type:feature`</mark>.

### Platform

Для разделения похожих библиотек (например, между серверными, мобильными и настольными) могут использоваться и другие классификаторы. **Platform** - один из таких классификаторов.&#x20;

Мы рекомендуем использовать **теги для обозначения платформы**.

Конечным тегом для фичи seatmap будет <mark style="color:red;">`scope:shared,type:feature,platform:desktop`</mark>.

Каждая библиотека должна быть расположена в дереве папок по области видимости, иметь теги в формате <mark style="color:red;">`scope:SCOPE,type:TYPE,platform:PLATFORM`</mark>, как указано выше, и иметь префикс по type. Дополнительную информацию об использовании тегов см. в разделе Соблюдение ограничений в зависимостях библиотек (Enforce Restrictions In Library Dependencies) в третьей части.

{% hint style="warning" %}
_Не упорядочивайте по типу файлов!_

Мы настоятельно не рекомендуем организовывать файлы по типу, например, <mark style="color:red;">`directives/`</mark>, <mark style="color:red;">`services/`</mark> и т.д. Причина в том, что когда мы хотим внести изменения, нам часто требуется изменить сразу несколько связанных файлов. При организации по типу файлов нам приходится многократно просматривать дерево папок, чтобы найти нужные файлы.

Вместо этого мы предлагаем организовать кодовую базу по **доменам** и включить все связанные файлы вместе, например, <mark style="color:red;">`airline`</mark>, который включает state, ui компоненты и т.д. в одну группирующую папку. Это позволит разработчику работать над связанными файлами без необходимости часто перемещаться по дереву папок.
{% endhint %}