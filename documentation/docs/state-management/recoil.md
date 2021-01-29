---
id: recoil
title: Recoil Support
sidebar_label: Recoil
---

Recoil is a state management library, open-sourced by Facebook. It's offering a simple and powerful way of dealing with global, asynchronous and derived state.

We'll show basic usage of Recoil API with simple counter example.

Refer to official [documentation](https://recoiljs.org/docs/introduction/motivation) for detailed usage.


We need to wrap our code with RecoilRoot in root component.

``` jsx title="pages/__app.tsx"
import React from "react";
import { AppProps } from "next/app";
import { RecoilRoot } from "recoil";

function MyApp({ Component, pageProps }: AppProps): JSX.Element {
  return (
    <RecoilRoot>
      <Component {...pageProps} />
    </RecoilRoot>
  );
}

export default MyApp;
```

An `atom` is simply a unit of state that component can subscribe. By updating the value, each subscribed component is re-rendered with the new value.

```jsx title="recoil/atoms/index.ts"
import { atom } from "recoil";

enum Atoms {
  Counter = "Counter",
}

export const counter = atom({
  key: Atoms.Counter,
  default: 0,
});
```

To read and write an atom from a component, we use a hook called `useRecoilState`.

```tsx title="componentes/RecoilExample/index.tsx"
import { useRecoilState } from "recoil";
import { counter } from "recoil/atoms/index.ts";

 const useCounter: () => [
    number,
    { increase: () => void; decrease: () => void }
 ] = () => {
    // highlight-next-line
  const [count, setCount] = useRecoilState(counter);

  const increase = () => {
    setCount((current) => current + 1);
  };

  const decrease = () => {
    setCount((current) => current - 1);
  };

  return [count, { increase, decrease }];
};

export const RecoilExample: React.FC = () => {
      // highlight-next-line
  const [count, { increase, decrease }] = useCounter();

  return (
      <>
          <h2>Recoil Counter</h2>
          <div>
              <button onClick={increase}> + </button>
              <span>{count}</span>
              <button onClick={decrease}> - </button>
          </div>
      </>
  );
};
```
Clicking on the buttons will updates state and changes count. It's that simple.

<br/>

:::tip

We recommend to watching [Dave McCabes presentation about Recoil](https://www.youtube.com/watch?v=_ISAA_Jt9kI&feature=youtu.be&ab_channel=ReactEurope) to understand logic behind the Recoil.

:::

:::note

All required configurations will be handled automatically by CLI as long as you choose Recoil plugin during the project creation phase.

:::


### Adding Recoil to your project later

If you didn't choose Recoil plugin during project creation phase, you can follow the instructions below to add it.

```bash
npm install recoil
```

Refer to official [documentation](https://recoiljs.org/docs/introduction/installation) for installation.

