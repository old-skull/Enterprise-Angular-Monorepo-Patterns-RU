# Параметры командной строки

При использовании схем Nx для создания библиотек у разработчиков есть несколько **мощных** возможностей. Большинство из этих опций идентичны тем, которые поддерживаются библиотекой CLI по умолчанию, но следующие являются новыми или отличными.

Флаг <mark style="color:red;">`--lazy`</mark> особенно важен для создания библиотек с ленивой загрузкой.

_Параметры при использовании_ <mark style="color:red;">`ng generate lib mylib`</mark>

| Флаги                                                                               | Результат                                                                                                                                                                                                                                                                                                                                                                                     |
| ----------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <mark style="color:red;">`--directory=myteam`</mark>                                | Создайть новую библиотеку с путем <mark style="color:red;">`libs/myteam/mylib`</mark>                                                                                                                                                                                                                                                                                                         |
| <mark style="color:red;">`--routing`</mark>                                         | Настройка NgModule библиотеки для подключения роутинга, чтобы библиотека загружалась в при инициализации (eagerly).                                                                                                                                                                                                                                                                           |
| <mark style="color:red;">`--parent -module=apps/myapp/src/app/app.module.ts`</mark> | Настроика роутинга <mark style="color:red;">`AppModule`</mark> по указанному пути и включение роута для загрузки этой библиотеки. Если используется в сочетании с <mark style="color:red;">`--lazy`</mark>, роут загружается с помощью <mark style="color:red;">`loadChildren`</mark> для ленивой загрузки роута.                                                                             |
| <mark style="color:red;">`--publishable`</mark>                                     | Создать несколько дополнительных файлов, настраивающих ng- packagr. Затем вы можете <mark style="color:red;">`ng build mylib`</mark> для создания пакета npm, который вы можете опубликовать в реестре npm. Это очень редко требуется при разработке в монорепозитории. В этом случае пользователи библиотеки находятся в одном репозитории, поэтому этап упаковки и публикации не требуется. |
| <mark style="color:red;">`--tags=scope:shared,type:feature`</mark>                  | Создать запись в <mark style="color:red;">`nx.json`</mark>, чтобы связать созданную библиотеку с двумя тегами, которые могут быть использованы для расширенного анализа кода.                                                                                                                                                                                                                 |

При создании библиотек с ленивой загрузкой необходимо добавить запись в файл <mark style="color:red;">`tsconfig.app.json`</mark> родительского модуля приложения, чтобы TypeScript знал, что нужно собрать и его:

```json
{
  "include": [
    "**/*.ts"
    /* add all lazy-loaded libraries here: "../../../libs/my-lib/index.ts" */
    
    , "../../../libs/mymodule/src/index.ts" 
  ]
}
```

В большинстве случаев Nx делает это по умолчанию. Иногда необходимо добавить эту запись вручную.