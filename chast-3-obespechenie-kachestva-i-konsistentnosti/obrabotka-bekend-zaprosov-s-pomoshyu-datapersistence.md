# Обработка бэкенд запросов с помощью DataPersistence

Управление состоянием - сложная задача. Нам необходимо координировать работу нескольких бэкендов, веб воркеров и компонентов пользовательского интерфейса, которые одновременно обновляют состояние.

Nx DataPersistence - это набор вспомогательных функций, которые позволяют разработчику управлять синхронизацией состояния с помощью явной стратегии и обрабатывать ошибки.

Обратитесь к статье [Использование NgRx 4 для управления состоянием в приложениях Angular](https://blog.nrwl.io/using-ngrx-4-to-manage-state-in-angular-applications-64e7a1f84b7b) для детального рассмотрения проблемы состояния, которую решает DataPersistence.

### Зачем использовать библиотеку DataPersistence?

Эта библиотека была разработана для абстрагирования некоторой логики, характерной для эффектов(NgRx Effects):

1. Она извлекает состояние из хранилища для получения одного или нескольких значений во время асинхронной операции.
2. Она заставляет нас выбирать между пессимистическим и оптимистическим обновлением состояния приложения.
3. Он помогает справиться с несколькими вызовами и осмысленно упорядочить их.
4. Он также помогает быть явным в отношении обработки ошибок: об этом часто забывают при использовании оператора <mark style="color:red;">`switchMap`</mark>, что приводит к завершению работы эффекта при возникновении ошибки (и, следовательно, к невозможности реагировать на дальнейшие экшены, которые его вызывают).
5. Она помогает инициировать асинхронную логику, когда происходит изменение роута для определенного компонента.

Давайте рассмотрим некоторые из функций, которые он предоставляет:

* оптимистичные и пессимистичные обновления
* надежная выборка произвольных данных
* инициализация асинхронных рабочих процессов при изменении роута (к определенному компоненту)

### Оптимистичные обновления

Для улучшения пользовательского опыта метод <mark style="color:red;">`optimisticUpdate`</mark> обновляет состояние клиентского приложения непосредственно перед обновлением данных на стороне сервера. Хотя он решает задачи получения данных по порядку, устранения гонки состояний и обработки ошибок, он оптимистичен в отношении отсутствия сбоев при обновлении данных на сервере. В случае сбоя при использовании <mark style="color:red;">`optimisticUpdate`</mark> локальное состояние на клиенте уже обновлено. Разработчик должен предусмотреть действие отмены для восстановления предыдущего состояния, чтобы оно соответствовало состоянию сервера. Обработка ошибок должна быть выполнена в обратном вызове или с помощью отмены экшена.

{% code overflow="wrap" %}
```typescript
import { DataPersistence } from '@nrwl/nx';
// ...

class TodoEffects {
  @Effect() updateTodo = this.dataPersistence.optimisticUpdate('UPDATE_TODO', {
    // provides an action and the current state of the store
    run: (a: UpdateTodo, state: TodosState) => {
      return this.backend(state.user, a.payload);
    },

    undoAction: (a: UpdateTodo, e: any) => {
      // dispatch an undo action to undo the changes in the client state
      return ({
        type: 'UNDO_UPDATE_TODO',
        payload: a
      });
    }
  });

  constructor(
    private dataPersistence: DataPersistence<TodosState>,
    private backend: Backend
  ) {}
}
```
{% endcode %}

### Пессимистические обновления

Для достижения более надежной синхронизации данных метод <mark style="color:red;">pessimisticUpdate</mark> сначала обновляет данные сервера перед обновлением пользовательского интерфейса. Когда изменение отражается в состоянии сервера, происходит изменение в состоянии клиента путем диспатча экшена. Метод <mark style="color:red;">`pessimisticUpdate`</mark> обеспечивает соблюдение порядка выполнения операций и обработку ошибок.

{% code overflow="wrap" %}
```typescript
import { DataPersistence } from '@nrwl/nx';
// ...

@Injectable()
class TodoEffects {
  @Effect() updateTodo = this.dataPersistence.pessimisticUpdate('UPDATE_TODO', { 
    // provides an action and the current state of the store
    run: (a: UpdateTodo, state: TodosState) => {
      // update the backend first, and then dispatch an action that
      // updates the client side
      return this.backend(state.user, a.payload).map(updated => ({
        type: 'TODO_UPDATED',
        payload: updated
      }));
    },
    
    onError: (a: UpdateTodo, e: any) => {
      // we don't need to undo the changes on the client side.
      // we can dispatch an error, or simply log the error here and return `null`
      return null;
    }
  });
  
  constructor(
    private dataPersistence: DataPersistence<TodosState>,
    private backend: Backend
  ) {}
}
```
{% endcode %}

### Получение данных

Метод fetch в DataPersistence обеспечивает последовательность при получении данных. Если для одного и того же экшена запланировано несколько запросов, выполняется только последний.

<pre class="language-typescript" data-overflow="wrap"><code class="lang-typescript">import { DataPersistence } from '@nrwl/nx';
// ...

@Injectable()
class TodoEffects {
  @Effect() loadTodos = this.dataPersistence.fetch('GET_TODOS', {
    // provides an action and the current state of the store
    run: (a: GetTodos, state: TodosState) => {
      return this.backend(state.user, a.payload).map(r => ({ type: 'TODOS',
        payload: r
      }));
    },
    
<strong>    onError: (a: GetTodos, e: any) => {
</strong>    // dispatch an undo action to undo the changes in the client state return null;
    }
  });

  constructor(
    private dataPersistence: DataPersistence&#x3C;TodosState>,
    private backend: Backend
  ) {}
}</code></pre>

Это правильно, но мы можем улучшить производительность, предоставляя id данных с помощью функции доступа и добавляя параллелизм в действие выборки для разных ToDo.

{% code overflow="wrap" %}
```typescript
@Injectable()
class TodoEffects {
  @Effect() loadTodo = this.dataPersistence.fetch('GET_TODO', {
    id: (a: GetTodo, state: TodosState) => {
      return a.payload.id;
    },
    
    // provides an action and the current state of the store
    run: (a: GetTodo, state: TodosState) => {
      return this.backend(state.user, a.payload).map(r => ({
        type: 'TODO',
        payload: r
      }));
    },
    
    onError: (a: GetTodo, e: any) => {
      // dispatch an undo action to undo the changes in the client state
      return null;
    }
  });
  
  constructor(
    private dataPersistence: DataPersistence<TodosState>,
    private backend: Backend
  ) {}
}
```
{% endcode %}

При такой настройке запросы для Todo выполняются одновременно с запросами для Todo 2. Последовательные вызовы для Todo ставятся в очередь за первым.

### Получение данных при навигации по роутеру

Поскольку пользователь всегда может взаимодействовать с URL напрямую, мы должны рассматривать роутер как источник истины и инициализатор экшенов. Другими словами, роутер должен вызывать редьюсер, а не наоборот.

Когда наше состояние зависит от навигации, мы не можем считать, что изменение роута произошло при появлении нового URL, но тогда когда мы действительно знаем, что пользователь смог перейти к этому URL. Метод <mark style="color:red;">`navigation`</mark> из DataPersistence проверяет, содержит ли активированное состояние роутера переданный тип компонента, и, если содержит, запускает обратный вызов <mark style="color:red;">`run`</mark>. Он предоставляет активированный снимок (snapshot), связанный с компонентом, и текущее состояние. И запускается только последний запрос.

{% code overflow="wrap" %}
```typescript
import { DataPersistence } from '@nrwl/nx';
// ...

@Injectable()
class TodoEffects {
  @Effect() loadTodo = this.dataPersistence.navigation(TodoComponent, {
    run: (a: ActivatedRouteSnapshot, state: TodosState) => {
      return this.backend.fetchTodo(a.params['id']).map(todo => ({
        type: 'TODO_LOADED',
        payload: todo
      }));
    },
    
    onError: (a: ActivatedRouteSnapshot, e: any) => {
      // we can log and error here and return null
      // we can also navigate back
      return null;
    }
  });
  
  constructor(
    private dataPersistence: DataPersistence<TodosState>,
    private backend: Backend
  ) {}
}
```
{% endcode %}

Библиотека DataPersistence является полезным инструментом для стандартизации способа взаимодействия наших приложений с бэкендами, вместо того, чтобы каждый раз писать код вручную, что приводит к различию подходов во многих приложениях.
