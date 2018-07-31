先看目录结构
```
src/
├──utils/
    ├──console.js
├── ActionsObservable.js    // 自定义的类，继承Observable, 绑定了操作符的链式调用
├── combineEpics.js         // 
├── createEpicMiddleware.js // 调用可生成redux的中间件, 通过run绑定需要执行的流
├── index.js                // 对外接口
├── operators.js            // 自定义的流操作方法, 目前只有ofType
├── StateObservable.js      // 自定义的类，继承Observable, 用于对state检查
```


```
1. rxjs内部的source是什么(在ActionObservable内部出现)

2. rxjs内部的operator是什么(在ActionObservable内部出现)

3. lift函数作用是什么(在ActionObservable内部出现)

```

接下来一个一个的说：

* index.js：公开接口，略

* ActionObservable.js

继承了`Observable`类
```js
export class ActionsObservable extends Observable
```

定义了两个静态函数，用调用的对象包装了原来的`of`和`from`
```js
  static of(...actions) {
    return new this(of(...actions));
  }
  static from(actions, scheduler) {
    return new this(from(actions, scheduler));
  }
```

构造函数，定义了`source`属性为参数，这个属性用来绑定操作符的链式调用
```js
  constructor(actionsSubject) {
    super();
    this.source = actionsSubject;
  }
```
这里重写了父类(Observable)的`lift`，先看一下父类的`lift`是怎样的
```js
var observable = new Observable();
observable.source = this;
observable.operator = operator;
return observable;
```
可以看到改动就在于原来的用`new Observable`，这里使用`new ActionObservable()`，其他都是一模一样，
封装成`ActionObservable`类的意义，统一类型，方便后面的链式绑定

`lift`在`pipe`的时候会用到，其实这都是rxjs源码调用的方式
```js
lift(operator) {
 const observable = new ActionsObservable(this);
 observable.operator = operator;
 return observable;
}
```
定义了一个操作方法`ofType`，具体见operators
```js
  ofType(...keys) {
    return ofType(...keys)(this);
  }
```
* combineEpics.js

```js
import { merge } from 'rxjs';

/**
  Merges all epics into a single one.
 */
export const combineEpics = (...epics) => {
  const merger = (...args) => merge(
    ...epics.map(epic => {
      const output$ = epic(...args);
      if (!output$) {
        throw new TypeError(`combineEpics: one of the provided Epics "${epic.name || '<anonymous>'}" does not return a stream. Double check you\'re not missing a return statement!`);
      }
      return output$;
    })
  );

  // Technically the `name` property on Function's are supposed to be read-only.
  // While some JS runtimes allow it anyway (so this is useful in debugging)
  // some actually throw an exception when you attempt to do so.
  try {
    Object.defineProperty(merger, 'name', {
      value: `combineEpics(${epics.map(epic => epic.name || '<anonymous>').join(', ')})`,
    });
  } catch (e) {}

  return merger;
};
```
* createEpicMiddleware.js

提示目前参数不在接受`rootEpic`，而是使用`epicMiddleware.run(rootEpic)`，这里`epicMiddleware`就是执行`createEpicMiddleware`的返回值
```js
export function createEpicMiddleware(options = {}) {
  if (process.env.NODE_ENV !== 'production' && typeof options === 'function') {
    throw new TypeError('Providing your root Epic to `createEpicMiddleware(rootEpic)` is no longer supported, instead use `epicMiddleware.run(rootEpic)`\n\nLearn more: https://redux-observable.js.org/MIGRATION.html#setting-up-the-middleware');
  }
  /*...*/
}
```
这一定义了几个重要变量(流)，这里一个重要问题
```
1. rxjs内部的source是什么(在ActionObservable内部出现)
2. rxjs内部的operator是什么(在ActionObservable内部出现)
A：source定义了操作符执行的流向，operator定义了操作符是什么操作符，这两者结合使用来进行链式绑定
```
接着看注释
```js
export function createEpicMiddleware(options = {}) {
  /*...*/
  // 定义一个Subject，绑定内部操作流，通过调用epic$.next()，也就是`epicMiddleware.run`来初始化action$的绑定
  const epic$ = new Subject();
  let store;
  // 作为redux的中间件
  const epicMiddleware = _store => {
    // 当在开发环境并且多次使用不同的 createEpicMiddleware返回值，会提出警告(避免重复执行多次)
    if (process.env.NODE_ENV !== 'production' && store) {
      // https://github.com/redux-observable/redux-observable/issues/389
      require('./utils/console').warn('this middleware is already associated with a store. createEpicMiddleware should be called for every store.\n\nLearn more: https://goo.gl/2GQ7Da');
    }
    store = _store;
    // 定义一个Subject，绑定了队列调度器 (后面这个用来绑定所有操作流)
    const actionSubject$ = new Subject().pipe(
      observeOn(queueScheduler)
    );
    //  定义一个Subject，绑定了队列调度器 (后面这个用来对比当前store，防止重复渲染)
    const stateSubject$ = new Subject().pipe(
      observeOn(queueScheduler)
    );
    // 定义一个ActionsObservable，用来绑定用户定义的操作流
    const action$ = new ActionsObservable(actionSubject$);
    // 定义一个StateObservable，内部改写了Observable的_subscribe方法，并且让stateSubject$绑定了value对比操作，就是简单的引用对比`===`
    const state$ = new StateObservable(stateSubject$, store.getState());
    
    /*...*/
  }
}
```

```js

export function createEpicMiddleware(options = {}) {
  /*...*/
  // pipe操作符
const result$ = epic$.pipe(
  // 对发射源逐个处理
  map(epic => {
    console.log(epic)
    const output$ = 'dependencies' in options
      ? epic(action$, state$, options.dependencies)
      : epic(action$, state$);

    // 无返回值，应该要返回一个流
    if (!output$) {
      throw new TypeError(`Your root Epic "${epic.name || '<anonymous>'}" does not return a stream. Double check you\'re not missing a return statement!`);
    }

    return output$;
  }),
  mergeMap(output$ =>
    from(output$).pipe(
      subscribeOn(queueScheduler),
      observeOn(queueScheduler)
    )
  )
);
    /*...*/
}

```


```js

export function createEpicMiddleware(options = {}) {
  if (process.env.NODE_ENV !== 'production' && typeof options === 'function') {
    throw new TypeError('Providing your root Epic to `createEpicMiddleware(rootEpic)` is no longer supported, instead use `epicMiddleware.run(rootEpic)`\n\nLearn more: https://redux-observable.js.org/MIGRATION.html#setting-up-the-middleware');
  }

  const epic$ = new Subject();
  let store;

  // 作为redux的中间件
  const epicMiddleware = _store => {
    if (process.env.NODE_ENV !== 'production' && store) {
      // https://github.com/redux-observable/redux-observable/issues/389
      require('./utils/console').warn('this middleware is already associated with a store. createEpicMiddleware should be called for every store.\n\nLearn more: https://goo.gl/2GQ7Da');
    }
    store = _store;
    // actionSubject$.source=<Subject>
    const actionSubject$ = new Subject().pipe(
      observeOn(queueScheduler)
    );
    const stateSubject$ = new Subject().pipe(
      observeOn(queueScheduler)
    );
    const action$ = new ActionsObservable(actionSubject$);
    // 一个保存当前状态的发射源，通过__subscription.next改变内部value(引用对比)
    // todo constructor??
    const state$ = new StateObservable(stateSubject$, store.getState());


    const result$ = epic$.pipe(
      // 对发射源逐个处理
      map(epic => {
        console.log(epic)
        const output$ = 'dependencies' in options
          ? epic(action$, state$, options.dependencies)
          : epic(action$, state$);

        // 无返回值，应该要返回一个流
        if (!output$) {
          throw new TypeError(`Your root Epic "${epic.name || '<anonymous>'}" does not return a stream. Double check you\'re not missing a return statement!`);
        }

        return output$;
      }),
      mergeMap(output$ =>
        from(output$).pipe(
          subscribeOn(queueScheduler),
          observeOn(queueScheduler)
        )
      )
    );
    // 订阅 dispatch，一旦有发射源的流进入，先经过上面pipe的过程，再dispatch($output)
    result$.subscribe(x=>console.log(x));
    // result$.subscribe(store.dispatch);

    return next => {
      return action => {
        // Downstream middleware gets the action first,
        // which includes their reducers, so state is
        // updated before epics receive the action
        const result = next(action);

        // It's important to update the state$ before we emit
        // the action because otherwise it would be stale
        // todo
        stateSubject$.next(store.getState());
        // console.log(store.getState())
        // todo
        actionSubject$.next(action);

        return result;
      };
    };
  };

  epicMiddleware.run = rootEpic => {
    if (process.env.NODE_ENV !== 'production' && !store) {
      require('./utils/console').warn('epicMiddleware.run(rootEpic) called before the middleware has been setup by redux. Provide the epicMiddleware instance to createStore() first.');
    }
    //
    epic$.next(rootEpic);
  };

  return epicMiddleware;
}

```