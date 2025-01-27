---
title: Getting Started
order: 20
---

## Installation

```bash
npm i @tethys/store --save
// or
yarn add @tethys/store
```

## Usage

```
interface CounterState {
    count: number;
}

@Injectable()
export class CounterStore extends Store<CounterState> {
    static countSelector(state: CounterState) {
        return state.count;
    }

    constructor() {
        super({
            count: 0
        });
    }

    @Action()
    increase() {
        return of(true).pipe(
            tap(() => {
                this.setState((state) => {
                    return {
                        count: state.count + 1
                    };
                });
            })
        );
    }

    @Action()
    decrease() {
        return of(true).pipe(
            tap(() => {
                this.setState((state) => {
                    return {
                        count: state.count - 1
                    };
                });
            })
        );
    }
}
```


