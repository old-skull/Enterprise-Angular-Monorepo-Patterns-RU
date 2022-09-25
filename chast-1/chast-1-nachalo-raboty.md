# Часть 1: Начало работы

## Почему монорепозиторий?

Многие крупные организации и бизнес-подразделения внутри организации предпочитают разрабатывать весь свой код (включая многочисленные внешние приложения, а также внутренние приложения) в едином репозитории. Такой подход преследует несколько целей:

### Повышение наглядности

Команды внутри организации или бизнес-подразделения часто не знают об изменениях, которые вносят другие команды, и это оказывает большое влияние на процесс интеграции. Можно сэкономить много времени, если обнаружить проблемы интеграции сразу после проверки кода. Мы обсудим некоторые стратегии работы с этими изменениями в пятой части книги.&#x20;

Кроме того, контракты API легко доступны в монорепозитории и могут напрямую использоваться как фронтендом, так и бэкендом. Типы могут генерироваться из контрактов и использоваться как фронтендом, так и бэкендом, чтобы гарантировать, что ошибки будут отловлены во время компиляции, а не во время более дорогостоящей интеграции.

### Прямое переиспользование кода

Традиционный способ модульности и совместного использования кода заключается в создании пакета, развертывании его в частном репозитории npm и добавлении его в зависимости проекта. Внесение изменений сопряжено с большими накладными расходами из-за времени, которое требуется для упаковки и развертывания зависимого кода. Также возникает проблема с версионированием, поскольку нам приходится ссылаться на правильный номер версии.&#x20;

В качестве альтернативы разработчики могут использовать <mark style="color:red;">`npm link`</mark> или другие способы имитации управления зависимостями для локальной разработки, но это также громоздко в настройке и использовании: может быть много зависимостей, которые нам нужно установить подобным образом, и мы должны не забыть удалить ссылки, когда закончим работу.&#x20;

Разработка в монорепозитории позволяет ссылаться на зависимость напрямую (используя  относительные пути рабочего пространства, в случае Nx). Код также доступен разработчику для непосредственной работы, и мы обсудим способы интеграции изменений в общий код разделе Trunk-based разработка (Trunk-based Development) в пятой части этой книги.

### Обеспечение консистентной версии зависимостей

Контроль версий в монорепозитории становится намного проще - организации или бизнес-подразделения могут выбрать единую версию зависимостей для всех проектов или политику "последняя-минус-Х" (latest-minus-X) для обеспечения актуальности всех проектов. Это снижает вероятность того, что в коде будут использоваться устаревшие зависимости и уязвимые версии. См. раздел Политика единой версии (Single Version Policy) в пятой части книги.

Все эти цели достижимы при использовании Nx.