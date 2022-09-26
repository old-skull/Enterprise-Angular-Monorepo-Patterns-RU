# Взаимодействие с Nx

Nx настраивается для рабочего пространства с помощью следующих конфигурационных файлов:

* <mark style="color:red;">`nx.json`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> - который специфичен для Nx
* <mark style="color:red;">`package.json`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> - который предоставляется npm
* <mark style="color:red;">`angular.json`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> - который предоставляется Angular CLI.&#x20;

Nx также предоставляет команды для работы с вашим рабочим пространством, приложениями и библиотеками. Их можно вводить в терминале или вызывать из Angular Console - графического инструмента для взаимодействия с Angular CLI и Nx. Консоль Angular Console рассматривается в Приложении A.

В этой книге мы демонстрируем консольные команды используя npm в каждом разделе по мере их изучения. Мы предоставляем инструкции для Angular Console, где это уместно. Все команды также перечислены в Приложении B.

### &#x20; <mark style="color:red;">`yarn`</mark> vs. <mark style="color:red;">`npm`</mark>&#x20;

В этой книге мы демонстрируем примеры кода с использованием <mark style="color:red;">`npm`</mark>. Все эти команды также работают с <mark style="color:red;">`yarn`</mark>. Но есть некоторые различия между ними:

1. Команды npm могут принимать параметры для самой команды и для базовой команды. Например, <mark style="color:red;">`npm run task1 --watch - --param1=val1`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> передает параметр <mark style="color:red;">`watch`</mark> самому npm, а <mark style="color:red;">`param1`</mark> - базовой задаче <mark style="color:red;">`task1`</mark>.  <mark style="color:red;">`--`</mark> сам по себе указывает на разделитель, после которой все параметры передаются базовой задаче. <mark style="color:red;">`yarn`</mark>, с другой стороны, не делает этого и передает все параметры базовой задаче, поэтому ему не нужен разделитель <mark style="color:red;">`--`</mark>.
2. Команды <mark style="color:red;">`npm`</mark> запускаются с помощью <mark style="color:red;">`npm run <команда>`</mark>, а команды <mark style="color:red;">`yarn`</mark> запускаются с помощью <mark style="color:red;">`yarn <команда>`</mark>.
3. <mark style="color:red;">`npm install`</mark> устанавливает пакеты, и вам нужно указать <mark style="color:red;">`--save`</mark> или <mark style="color:red;">`--save-dev`</mark> для сохранения зависимости; в то время как <mark style="color:red;">`yarn`</mark> устанавливает пакеты с помощью <mark style="color:red;">`yarn add`</mark> и добавляет их в package.json по умолчанию.

В книге описано, как можно преобразовать скрипт npm для работы с yarn используя следующий метод:

1. Если команда начинается с <mark style="color:red;">`npm run`</mark>, вы можете заменить <mark style="color:red;">`npm run`</mark> на <mark style="color:red;">`yarn`</mark>. В противном случае, если команда это <mark style="color:red;">`npm install`</mark>, вы можете использовать <mark style="color:red;">`yarn add`</mark> (если это <mark style="color:red;">`npm install -g`</mark> или <mark style="color:red;">`npm install --global`</mark>, используйте <mark style="color:red;">`yarn add global`</mark>). В большинстве других случаев вы можете просто заменить <mark style="color:red;">`npm`</mark> на <mark style="color:red;">`yarn`</mark>.
2. Если команда содержит символ <mark style="color:red;">`--`</mark>, удалите используя <mark style="color:red;">`yarn`</mark>.
