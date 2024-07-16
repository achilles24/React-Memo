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
