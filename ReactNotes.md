# Notes

## React

### Hooks

- Hook provide a more ergonomic way to update the state instad of classes
- useState()

  - declared by using [state, setState] = useState();
  - sets state of variables in react
  - _already know_

- useEffect()

  - this is going to be rough
  - component life cycle: component is added to life cycle (can only happen once), component is updated (can happen multiple times), component is destroyed (can only happen once)
  - allows for the abliity to impliment logic for all component events within a single function api
  - useEffect() takes a callback fuction
  - declaration: useEffect(() => { your function code here })
  - will run useEffect() after DOM is updated

    ```javascript
    useEffect(() => {
      alert('hello, side effect!');
    });
    ```

    - Good for fetching data when a component is initalized and fetching data in a async fasion
    - useEffect() will run everytime state changes within an app
    - you can add dependiencs in a useEffect() function that only changes when the state of the dependcies change

    ```javascript
    const [count, setCount] = useState(0);
    const [loaded, setLoaded] = useState(false);

    useEffect(() => {
      fetch('some/data').then(() => setLoaded(true));
    }, [count]);
    ```

    - This code will run only when the state of count changes because we added it as a dependency at the end
    - So everytime count changes it grabs data from 'some/data' and then runs 'setLoaded(true)'
    - one more thing you will want to do with useEffect(), is run tear-down code when a component is destroyed. This runs a function when a component is removed or destoryed. Will run on any component unless given a dependency component
      Tear-Down example

    ```javascript
    useEffect(() => {
      alert('Hello side effect!');

      //this is the tear down part
      return () => alert('Goodbye side effect!');
    });
    ```

  - To Sum up: useEffect() will run a callback function everytime state changes in a application. You can pass a dependency at the end of the function within brackets [] to set the spicific useEffect() function to that one piece of state

- useContext()

  - allows you to share or scope values throughout your entire component tree
  - imagine you have a object of moods

  ```javascript
  const moods = {
    happy: '😊',
    sad: '😢',
  };
  ```

  - to be able to share the mood across multiple **connected** components, you have to use useContext()
  - Ex:

  ```javascript
  const moods = {
    happy: '😊',
    sad: '😢',
  };

  const MoodContext = useContext(moods);
  ```

  - Use context takes in a variable and assigns to another variable
  - to use on parts of application you would use '.Provider' tag

  ```javascript
  function App() {
    const moods = {
      happy: '😊',
      sad: '😢',
    };

    const MoodContext = useContext(moods);

    return (
      <MoodContext.Provider value={moods.happy}>
        <MoodEmoji />
      </MoodContext.Provider>
    );
  }
  ```

  - useContext() also allows us to access the current value from the context provider
  - Ex: (refer to the code above for context)

  ```javascript
  function MoodEmoji() {
    const mood = useContext(MoodContext);
    return <p>{mood}</p>;
  }
  ```

  - this allows for the function to grab the value of MoodContext
  - if the parent changes from happy to sad in the parent provider the function will return the correct thing
  - use context is a cleaner replacement for the '.Consumer' hook

- useRef()

  - allows you to create a mutable object that persists between reneders but will keep the same reference between renders
  - it can be used when you have a value that changes (like setState()) but it does **NOT** re-render the UI when the value changes
  - if you were to build a counter using useRef():

  ```javascript
  function App() {
    const myBtn = useRef(null);
    const clickIt = () => myBtn.current.click();

    return <button ref={myBtn}></button>;
  }
  ```

  - it would not work because the UI would not re-render like useState() would instead the function above grabs the DOM elements and can carryout DOM functions like HTML
  - useRef() can be useful when you need a mutable value but a more common use is grabbing HTML elements from the DOM
  - bottom-line: when you need to grab an element from the DOM, useRef() is the hook you use

- useReducer()

  - kind of like setState() but goes about it in the Redux pattern
  - ![Redux Pattern](https://www.google.com/url?sa=i&url=https%3A%2F%2Fcommons.wikimedia.org%2Fwiki%2FFile%3ANgrx-redux-pattern-diagram.png&psig=AOvVaw3alS4F3gxNtEyioHGQeqgV&ust=1681183729995000&source=images&cd=vfe&ved=0CBAQjRxqFwoTCLC6-8mvnv4CFQAAAAAdAAAAABAE)
  - instead of updating the state directly you dispatch actions that goes to a reducer function and that reducer function determines how to compute the next state
  - useReducer() returns an array of two values
  - instad of it giving you a function that updates the state, you are given a function that can dispatch an action
  - arguments are type: string, payload: any
  - the reducer function is something you define and pass as the argument to the useReducer() hook and is usually handled inside of a switch statement
  - why would you use this: "it helps your code manage more complexity as it grows"

  ```javascript
  function reducer(state, action) {
    switch (action.type) {
      case 'increment':
        return state + 1;
      case 'decrement':
        return state - 1;
        defualt: throw new Error();
    }
  }

  function App() {
    const [state, dispatch] = useReducer();
    return (
      <>
        Count: {state}
        <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
      </>
    );
  }
  ```

- useMemo()

  - can help you optimise computation cost and improve performance
  - **YOU SHOULD NOT PREMATURALLY OPTIMISE PERFORMANCE**
  - think of useMemo() as a opt-in tool to deal with expensive computations that are hurting performance
  - if we have an expensive function, instead of re-computing it, we can memoize the value
  - useMemo() also takes in a dependency variable that is tied to state and this tells React when the function should run
  - the useMemo() will run when the dependicies state changes
  - only memoizes return values
  - Example:

  ```javascript
  function App() {
    const [count, setCount] = useState(60);

    const expensiveCount = useMemo(() => {
      return count ** 2;
    }, [count]);
  }

  return (
    <>
      {count}
      <button onClick={expensiveCount}>
        Click Me for Expensive Computation!
      </button>
    </>
  );
  ```

- useCallback()

  - useCallback() is used for memoizing an entire function
  - when you define a function in a component, a new function object is created everytime the component is re-rendered
  - typically not a big deal for performance but sometimes you might want to memoize the function
  - a common use case is when you pass a function down to mulitple child components, especally with big lists
  - by wrapping the function in a callback, we prevent unnessarry re-renders of the children a they will always be using the same function object
  - the hook takes in a dependency and changes when the state of the dependency variable changes
  - Example Code:

  ```javascript

  function App() {
    const [count, setCount] = useState(60);

    const showCount = useCallback(() => {
      alert(`count ${count}`)
    }, [count])

    return (
      <>
        <SomeChild handler={showCount}>
      </>
    )
  }
  ```

others:

- useImperativeHandle():
  - used for when you have built a reusable component libaray and you need to get the underlying DOM elements
- useLayoutEffect()
  - same as useLayout()? but will run after the render but before painting the screen
- useDebugValue()
  - debuggin for building custom hooks
  - works with react dev tools to add custom labels
