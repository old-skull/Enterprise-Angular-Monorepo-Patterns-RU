# Подведем итоги

В этом разделе мы кратко рассмотрели некоторые общие проблемы, с которыми сталкиваются команды при переходе на монорепозиторий.

* Некоторые стратегии для решения проблемы дублирования кода между командами - это меньшие PR, актуальность PR были для целевой ветки, и использование трехступенчатого плана при обновлении общего кода: создание новой версии и использование feature переключателей при работе над ней, списание старой версии и работа с владельцами кода для перехода на новую версию, удаление старой версии.
* Установка владельцев кода для различных библиотек накладывает ответственность как за слияние кода в эту библиотеку, так и за ответственное продвижение изменений среди затронутых команд.
* Feature переключатели позволяют командам тестировать свой код в окружениях, не затрагивая и не подвергаясь влиянию других команд. Переключатели во время сборки полезны при работе с настройками, которые требуются во время инициализации приложения, а переключатели во время выполнения могут быть изменены после запуска приложения конечным пользователем.
* Trunk-based разработка предпочтительна при работе в монорепозитории, чтобы избежать больших усилий, когда приходит время объединять ветки.
* [trunkbaseddevelopment.com](https://trunkbaseddevelopment.com/) является ценным ресурсом.
