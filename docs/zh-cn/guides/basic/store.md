---
title: Store
order: 10
---

`Store`是一个包含状态和行为的单一对象，也是一个普通的服务，创建`Store`只需继承`Store<TState>`，并通过泛型传入当前`Store`存储的状态类型，同时通过构造函数传入初始化状态。

```ts
import { Injectable } from '@angular/core';
import { Action, Store } from '@tethys/store';
import { of } from 'rxjs';
import { tap } from 'rxjs/operators';

interface CounterState {
    count: number;
}

@Injectable()
export class CounterStore extends Store<CounterState> {

    constructor() {
        super({ count: 0 });
    }
}
```

## setState

当需要更新状态时可以通过调用`setState()`方法传入新的状态。

```ts
@Injectable()
export class CounterStore extends Store<CounterState> {
    
    ...

    @Action()
    increase() {
        this.setState({ count: this.snapshot.count + 1 });
    }
}
```
`setState` 可以支持多种参数类型
- 完整的`State`新对象
- 部分`State`更新的对象
- 返回新`State`的函数

```ts
@Injectable()
export class CounterStore extends Store<CounterState> {
    @Action()
    increase() {
        this.setState((state)=> {
          return { count: state.count + 1 };
        });
    }
}
```

## snapshot
提供当前`Store`存储状态的快照，方便在模版中通过`store.snapshot`表达式直接访问, 在用`snapshot`的时候注意:
- 尽量只在模版中使用，因为数据变化后可以通过变更检测实时响应
- 在代码中使用时确定当前时刻一定是你需要的值, 且是一次性使用, 数据变更后不影响当前逻辑
- 严禁在`Store`外直接修改`snapshot`中的数据


## select
一个`Store`可以存储多个数据集，当某个组件只需要获取部分数据时，可以通过`select()`函数过滤需要的数据，这样只有过滤的数据变化才会收到变化订阅，提高应用程序的性能。

```ts
@Component({
    selector: 'thy-store-counter',
    templateUrl: './counter.component.html',
    styleUrls: ['./counter.component.scss']
})
export class ThyStoreCounterExampleComponent implements OnInit {
    count$: Observable<number>;

    constructor(public counter: CounterStore) {}

    ngOnInit(): void {
      this.count$ = this.counter.select((state)=> { return state.count; }))
    }
}
```

注意事项：
- 可以不需要订阅就不订阅, 直接使用`select`返回需要的`Observable`, 在模版中使用`async`管道订阅使用, 如果模版中多处使用可以使用`as`关键字保存为临时对象
- 一旦订阅, 一定要取消订阅, 推荐`takeUntil`操作符取消订阅
- `selector`推荐统一定义到`Store`的静态函数中
