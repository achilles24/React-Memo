# React-Memo
Deep comparison for non-primitive type

Performing a deep comparison for non-primitive types (objects and arrays) in React components requires a bit of manual effort, as `React.memo` by default only performs shallow comparisons. Here’s how you can approach deep comparisons in React components:

### 1. Custom Comparison Function with `React.memo`

You can provide a custom comparison function to `React.memo` as the second argument. This function receives the previous props and the new props, allowing you to implement a deep comparison logic. Here’s a basic example:

```jsx
import React from 'react';
import isEqual from 'lodash/isEqual'; // Example using lodash isEqual for deep comparison

const MyComponent = React.memo(({ complexObject }) => {
  console.log('Component rendered');
  return (
    <div>
      <pre>{JSON.stringify(complexObject, null, 2)}</pre>
    </div>
  );
}, (prevProps, nextProps) => {
  // Perform deep comparison of complexObject
  return isEqual(prevProps.complexObject, nextProps.complexObject);
});

// Usage
function App() {
  const [state, setState] = React.useState({
    data: {
      id: 1,
      name: 'Alice',
      preferences: ['JavaScript', 'React']
    }
  });

  const updatePreferences = () => {
    setState({
      ...state,
      data: {
        ...state.data,
        preferences: [...state.data.preferences, 'Redux']
      }
    });
  };

  return (
    <div>
      <MyComponent complexObject={state.data} />
      <button onClick={updatePreferences}>Add Preference</button>
    </div>
  );
}

export default App;
```

In this example:
- `MyComponent` receives `complexObject` as a prop.
- The second argument of `React.memo` is a custom comparison function that uses `isEqual` from lodash to perform a deep comparison of `complexObject`.
- `isEqual` recursively compares the properties of `complexObject` to determine if they are deeply equal.

### 2. Use Immer or Immutable Data Structures

Another approach to managing complex state and props in React components is to use libraries like Immer or immutable data structures (e.g., Immutable.js). These tools help you produce immutable data structures, making it easier to detect changes and optimize performance.

### 3. Manual Prop Comparisons

For cases where you need fine-grained control over when to re-render based on props changes, you can manually compare specific properties inside your component's `shouldComponentUpdate` method for class components or within the body of a functional component.

### Summary

Performing deep comparisons for non-primitive types in React components involves either using a custom comparison function with `React.memo`, leveraging libraries for immutable data structures, or manually implementing comparison logic within your components. Each approach has its trade-offs, so choose the one that best fits your application’s needs for performance and complexity management.


To manage state updates and perform deep comparisons effectively in a React component using `useImmer`, we'll leverage the `produce` function from the Immer library. Immer simplifies the process of working with immutable state by allowing you to directly mutate a draft state within a callback function, which it then safely applies immutably. Here’s how you can use `useImmer` and `React.memo` together to handle state updates and optimize component rendering:

### Example Using `useImmer` and `React.memo`

First, ensure you have installed Immer in your project:

```bash
npm install immer
```

Here’s an example of how to implement `useImmer` and `React.memo`:

```jsx
import React from 'react';
import { useImmer } from 'use-immer';

const MyComponent = React.memo(({ complexObject }) => {
  console.log('Component rendered');
  return (
    <div>
      <pre>{JSON.stringify(complexObject, null, 2)}</pre>
    </div>
  );
}, (prevProps, nextProps) => {
  // Perform deep comparison of complexObject
  return prevProps.complexObject === nextProps.complexObject;
});

// Usage
function App() {
  const [state, updateState] = useImmer({
    data: {
      id: 1,
      name: 'Alice',
      preferences: ['JavaScript', 'React']
    }
  });

  const updatePreferences = () => {
    updateState(draft => {
      draft.data.preferences.push('Redux');
    });
  };

  return (
    <div>
      <MyComponent complexObject={state.data} />
      <button onClick={updatePreferences}>Add Preference</button>
    </div>
  );
}

export default App;
```

### Explanation:

1. **`useImmer` Hook**: This hook from the `use-immer` library allows you to manage immutable state updates with a simpler syntax. It returns a state value and an `updateState` function similar to `setState` in React’s `useState`, but it operates with Immer's draft state concept.

2. **`MyComponent`**: This component is wrapped with `React.memo` to optimize rendering. The custom comparison function checks if `complexObject` has changed by shallow comparison, ensuring the component only re-renders when necessary.

3. **Updating State**: In `App`, `updatePreferences` uses `updateState` from `useImmer` to update `state.data.preferences` immutably. Immer manages the immutability behind the scenes, and `React.memo` ensures that `MyComponent` only re-renders if `state.data` itself is replaced with a new object reference.

### Benefits:

- **Performance Optimization**: `React.memo` combined with a custom comparison function optimizes rendering by preventing unnecessary updates when props haven't changed.
- **Immutability**: Immer simplifies working with immutable updates, reducing bugs related to accidental state mutations.
- **Readability**: Using Immer improves code readability by allowing direct state mutations within a readable, imperative style.

By leveraging `useImmer` and `React.memo` effectively, you can manage complex state updates and optimize rendering performance in your React applications. Adjust the custom comparison function in `React.memo` as needed to suit your specific deep comparison requirements for non-primitive types.
