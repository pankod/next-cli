---
id: testing-library
title: Testing Library
sidebar_label: Testing Library
---

The [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/) is a very light-weight solution for testing React components. It provides light utility functions on top of `react-dom` and `react-dom/test-utils`, in a way that encourages better testing practices.

**superplate**'s plugin of React Testing Library is built on top of [superplate's Jest plugin](jest) and automatically includes necessary wrappers and imports to run your component tests.

## Implementation

You can see how **superplate**'s React Testing Library plugin is implemented below.

:::tip

Configuration for Jest is not included. Please check out [Jest Plugin](jest) to learn more about our Jest configuration.

:::

### Dependencies

First, we need to add dependencies to get started using React Testing Library to run our tests.

```json title="package.json"
{
    "devDependencies": {
        "@testing-library/react": "^11.2.3",
        "@testing-library/react-hooks": "^5.0.0"
    }
}
```

### Custom Render

We may need to wrap our test components to context providers, data stores etc. It's a good practice to make this wrappers globally available. We will create a custom render and re-export everything from `React Testing Library` in `test/index.tsx` file. 

```ts title="test/index.tsx"
import React, { ReactElement } from "react";
import {
    render as baseRender,
    RenderOptions,
    RenderResult,
} from "@testing-library/react";
/*
import { ThemeProvider } from 'my-ui-lib'
import { TranslationProvider } from 'my-i18n-lib'
*/

export const AllTheProviders = ({ children }) => {
    return (
        // <ThemeProvider theme="light">
        //     <TranslationProvider>
                {children}
        //     </TranslationProvider>
        // </ThemeProvider>
    );
};

const render = (ui: ReactElement, options?: Omit<RenderOptions, "queries">) =>
    baseRender(ui, { wrapper: AllTheProviders, ...options }) as RenderResult;

// re-export everything
export * from "@testing-library/react";

// override render method
export { render };
```

### Example Test

```tsx title="MyComponent.spec.tsx"
import { fireEvent, render } from "@test"; // <root>/test/index.tsx
import { MyComponent } from "./MyComponent";

describe("MyComponent", () => {
    it("button is clickable", () => {
        const mockFn = jest.fn();
        const { getByTestId } = render(<MyComponent onClick={mockFn} />);

        const btn = getByTestId("btn");
        fireEvent.click(btn);

        expect(mockFn).toHaveBeenCalledTimes(1);
    });
});
```

```tsx title="MyComponent.tsx"
import React from "react";

export const MyComponent: React.FC<{ onClick: () => void }> = ({
    onClick,
}) => {
    return (
        <div>
            <button data-testid="btn" onClick={onClick}>
                Click Me!
            </button>
        </div>
    );
};
```

### Running Tests

We will use Jest as our test runner. If Jest is already set up; you can simply run `test` script with `npm run test` or `yarn test`.

## Adding Testing Library to an existing project

If you want to add React Testing Library to your existing project please check out;

- React Testing Library [documentation](https://testing-library.com/docs/react-testing-library/intro/)
- Next.js with Jest and Testing Library [example repository](https://github.com/vercel/next.js/tree/canary/examples/with-typescript-eslint-jest)
