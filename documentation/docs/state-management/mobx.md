---
id: mobx
title: Mobx
sidebar_label: Mobx
---

*Simple, scalable state management.*

*Anything that can be derived from the application state, should be. Automatically.*

MobX is a battle tested library that makes state management simple and scalable by transparently applying functional reactive programming.  
[Refer to official documentation for detailed usage. &#8594](https://mobx.js.org/README.html)

### Observable state

Mobx uses observables for store values. Properties, entire objects, arrays, Maps and Sets can all be made observable.  
[Refer to official documentation on observable state for detailed usage. &#8594](https://mobx.js.org/observable-state.html)


#### Making observable stores with classes

- Make a counter store that holds a `count` state

```ts title="src/mobx/stores/counter/index.ts"
import { makeAutoObservable } from "mobx";

import { icounter } from "./counter";

export class CounterStore implements icounter {
  count = 0;

  constructor() {
    makeAutoObservable(this);
  }
}
```

```ts title="src/mobx/stores/counter/counter.d.ts"
export interface icounter {
  count: number;
}
```
:::info
`makeAutoObservable` and its cousin `makeObservable` trap existing object properties and make them observable. `makeAutoObservable` is like `makeObservable` on steroids, as it infers all the properties by default
:::

- Make a root store that holds the counter store

```ts title="src/mobx/stores/index.ts"
import { iroot } from "./store";
import { CounterStore } from "./counter";
import { icounter } from "./counter/counter";

export class RootStore implements iroot {
    counterStore: icounter;

    constructor() {
        this.counterStore = new CounterStore();
    }
}
```

```ts title="src/mobx/stores/store.d.ts"
import { icounter } from "./stores/counter/counter";

export interface iroot {
  counterStore: icounter;
}
```

Before starting to read data from the store, let's add some action.

### Actions

An action is any piece of code that modifies the state.

- Add actions to counter store.

```ts title="src/mobx/stores/counter/index.ts"
import { makeAutoObservable } from "mobx";

import { icounter } from "./counter";

export class CounterStore implements icounter {
  count = 0;

  constructor() {
    makeAutoObservable(this);
  }

// highlight-start
  increase = () => {
    this.count++;
  };

  decrease = () => {
    this.count--;
  };
// highlight-end
}
```

```ts title="src/mobx/stores/counter/counter.d.ts"
export interface icounter {
  count: number;
// highlight-start
  increase: () => void;
  decrease: () => void;
// highlight-end
}
```

### Using store in components
Firstly store must be made accessible to components. It can be done with `React.useContext`. Then with a custom hook, store can be read from components.

- Make a context to hold store.

```ts title="src/mobx/index.tsx"
import React from "react";

const StoreContext = React.createContext<RootStore | undefined>(undefined);
```

- Use its provider to make store accessible to all components.

```ts title="src/mobx/index.tsx"
import React from "react";
// highlight-start
import { iroot } from "./stores/store";
// highlight-end

// highlight-start
let store: iroot;
// highlight-end

const StoreContext = React.createContext<RootStore | undefined>(undefined);

// highlight-start
export const RootStoreProvider = ({
  children,
}: {
  children: React.ReactNode;
}) => {
  const root = store ?? new RootStore();

  return <StoreContext.Provider value={root}>{children}</StoreContext.Provider>;
};
// highlight-end
```

- Components can read from store via a custom hook.

```ts title="src/mobx/index.tsx"
import React from "react";
import { iroot } from "./stores/store";

let store: iroot;

const StoreContext = React.createContext<RootStore | undefined>(undefined);

export const RootStoreProvider = ({
  children,
}: {
  children: React.ReactNode;
}) => {
  const root = store ?? new RootStore();

  return <StoreContext.Provider value={root}>{children}</StoreContext.Provider>;
};

// highlight-start
export const useRootStore = () => {
  const context = React.useContext(StoreContext);
  if (context === undefined) {
    throw new Error("useRootStore must be used within RootStoreProvider");
  }

  return context;
};
// highlight-end
```

- Wrap your component with `observer` HOC.

```tsx title="Your Component"
// highlight-start
import { observer } from "mobx-react";
// highlight-end
import { useRootStore } from "@mobx";

// highlight-start
export const MobxExample: React.FC = observer(() => {
// highlight-end
  const { counterStore } = useRootStore();
  const { count, increase, decrease } = counterStore;

  return (
    <div>
      <div>
        <h2>Counter</h2>
        <button
          type="button"
          onClick={increase}
        >
          +
        </button>
        <span>{count}</span>
        <button
          type="button"
          onClick={decrease}
        >
          -
        </button>
      </div>
    </div>
  );
});
```

[Refer to official documentation on React integration for detailed usage. &#8594](https://mobx.js.org/react-integration.html)

:::tip
You might consider using mobx-react-lite instead of mobx-react if you're not using class components.
:::

### Adding mobx to your project later

If you didn't choose the plugin during project creation phase, you can follow the instructions below to add it.

- install `mobx` and `mobx-react` packages.

```bash
npm install mobx && npm install mobx-react
```

- [Follow insctructions begining from here](#making-observable-stores-with-classes)

[Refer to official documentation on installation for detailed usage. &#8594](https://mobx.js.org/installation.html)
