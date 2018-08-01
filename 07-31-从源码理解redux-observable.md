
阅读前提：
1. 有rxjs基础，对`Observable`, `Subject`,`pipe`和一些操作符(例如`filter`,`map`,`merge`,`mergeMap`)执行流程有基本了解，最起码遇到不清楚有去查阅的动力
2. 比较熟悉`redux`中间件的写法，否则像`createEpicMiddleware.js`中的这一段会搞不清楚
    ```
    return next => {
      return action => {
    ```
    关于redux也可以查阅我之前写的[redux源码注释](https://github.com/stonehank/sourcecode-analysis/tree/master/source-code.redux)
    
-------------
正式开始，先看目录结构
```
src/
├──utils/
    ├──console.js
├── ActionsObservable.js    // 自定义的类，继承Observable, 绑定了操作符的链式调用
├── combineEpics.js         // 
├── createEpicMiddleware.js // 调用可生成redux的中间件, 通过run绑定需要执行的流
├── index.js                // 对外接口
├── operators.js            // 自定义的流操作方法, 目前只有ofType
├── StateObservable.js      // 自定义的类，继承Observable, 用于保存当前state
```


```
3. lift函数作用是什么(在ActionObservable内部出现)

```

接下来一个一个的说：

-------
* index.js：公开接口，略
-------------

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

----------------
* combineEpics.js

```js
// 引入merge
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
--------------
* createEpicMiddleware.js

一、提示目前参数不在接受`rootEpic`，而是使用`epicMiddleware.run(rootEpic)`，这里`epicMiddleware`就是执行`createEpicMiddleware`的返回值
```js
export function createEpicMiddleware(options = {}) {
  if (process.env.NODE_ENV !== 'production' && typeof options === 'function') {
    throw new TypeError('Providing your root Epic to `createEpicMiddleware(rootEpic)` is no longer supported, instead use `epicMiddleware.run(rootEpic)`\n\nLearn more: https://redux-observable.js.org/MIGRATION.html#setting-up-the-middleware');
  }
  /*...*/
}
```
二、这一定义了几个重要变量(流)，这里一个重要问题
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
  // 作为redux的中间件，其中epicMiddleware是返回值作为外部API
  const epicMiddleware = _store => {
    // 当在开发环境并且多次使用不同的 createEpicMiddleware返回值，会提出警告(避免重复执行多次)
    if (process.env.NODE_ENV !== 'production' && store) {
      // https://github.com/redux-observable/redux-observable/issues/389
      require('./utils/console').warn('this middleware is already associated with a store. createEpicMiddleware should be called for every store.\n\nLearn more: https://goo.gl/2GQ7Da');
    }
    store = _store;
    // 定义一个Subject，绑定了队列调度器 (后面这个用来绑定所有操作流)
    const actionSubject$ = new Subject().pipe(
      // todo 调度器，看了一些资料，还有有点模糊
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
三、这里是核心，所有流和操作符的绑定就是在此内部进行
```js
export function createEpicMiddleware(options = {}) {
  /*...*/
  
  // pipe操作符
const result$ = epic$.pipe(
  // 对发射源逐个处理
  map(epic => {
    // 如果配置有 dependencies 就放置到第三个参数中
    const output$ = 'dependencies' in options
      ? epic(action$, state$, options.dependencies)
      : epic(action$, state$);
    // 无返回值，报错，应该要返回一个不同的流
    if (!output$) {
      throw new TypeError(`Your root Epic "${epic.name || '<anonymous>'}" does not return a stream. Double check you\'re not missing a return statement!`);
    }
    // output$ 是一个ActionObservable类型的流
    return output$;
  }),
  // 对所有外部流，绑定队列调度并且使用mergeMap重新subscribe
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
四、epic执行主要流程，`dispatch(action$)`--> `actionSubject$.next(action)`-->经过一系列操作符-->最后到达底层`dispatch(state)`
```js
export function createEpicMiddleware(options = {}) {
/*...*/

// 订阅 dispatch，此处将dispath作为流执行的最底层
result$.subscribe(store.dispatch);

// 返回的格式是按照redux中间件的格式
// 里面的内容都是运行时redux里dispatch后会执行的流程
return next => {
  return action => {
    // 此处先截取next(action)
    const result = next(action);
    // 先执行 stateSubject$.next 可以保证state的更新
    stateSubject$.next(store.getState());
    // 操作符按步骤执行，此时的actionSubject$已经绑定了所有的操作符
    actionSubject$.next(action);
    return result;
  };
};
// run方法，通过epic$的next方法初始化action(见第三)，从而绑定用户自定义的操作符
epicMiddleware.run = rootEpic => {
  if (process.env.NODE_ENV !== 'production' && !store) {
    require('./utils/console').warn('epicMiddleware.run(rootEpic) called before the middleware has been setup by redux. Provide the epicMiddleware instance to createStore() first.');
  }
    // rootEpic就是用户自定义的操作符
  epic$.next(rootEpic);
};
return epicMiddleware;
}
```
------------------
* operators.js

定义了`ofType`，其实就是一个filter
```js
// 引入filter
import { filter } from 'rxjs/operators';
// 这里type就是传给epic的action的type的值，key就是用户自定义需要过滤的值
const keyHasType = (type, key) => {
  return type === key || typeof key === 'function' && type === key.toString();
};
// source就是调用ofType的流，也就是createEpicMiddleware里的action$
// 结果只有返回true才会继续链式调用
export const ofType = (...keys) => (source) => source.pipe(
  filter(({ type }) => {
    const len = keys.length;
    if (len === 1) {
      return keyHasType(type, keys[0]);
    } else {
      for (let i = 0; i < len; i++) {
        if (keyHasType(type, keys[i])) {
          return true;
        }
      }
    }
    return false;
  })
);
```
------------------
* StateObservable.js

继承`Observable`，定义了一个保存状态的类，里面改写了父类(`Observable`)的`_subscribe`(作用不太清楚)，
并且定义了一个保存当前数据状态的函数，通过stateSubject(也就是createEpicMiddleware里面的`stateSubject$`)的`subscribe`绑定到底层
```js
import { Observable, Subject } from 'rxjs';

export class StateObservable extends Observable {
  constructor(stateSubject, initialState) {
    // 调用父类构造函数，改写父类的_subscribe
    // todo 作用？
    super(subscriber => {
      const subscription = this.__notifier.subscribe(subscriber);
      if (subscription && !subscription.closed) {
        subscriber.next(this.value);
      }
      return subscription;
    });
    
    this.value = initialState;
     this.__notifier = new Subject();
      // 绑定一个引用比较的状态函数
     this.__subscription = stateSubject.subscribe(value => {
       if (value !== this.value) {
         this.value = value;
         this.__notifier.next(value);
       }
     });
   }
 }
```

源码就到此分析完了，看到这里可能还是一头雾水，知道是什么也只是概念上的知道，对整个流程还是没有头绪，
接着，我将对几个关键流程进行导图分析