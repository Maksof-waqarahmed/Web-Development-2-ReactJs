## 🔹 What are Props?

**Props** (short for *properties*) are used to **pass data from one component to another**, typically from a **parent** to a **child**.

### 🔸 Example:

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}!</h1>;
}

function App() {
  return <Welcome name="Ali" />;
}
```

### 🧠 Key Points:

* Props are **read-only**.
* You **cannot modify** props inside the child component.
* Props are like **parameters in functions**.

---

### 🔄 Can We Pass Props from Child to Parent?

⛔ **No, props are unidirectional** — they only flow from **parent to child**.

### ✅ How Can the Child Communicate with the Parent?

By using a **callback function** — the parent provides a function as a prop, and the child calls it when needed.

---

### 🔧 Example (Step-by-Step):

#### 1️⃣ Parent Component:

```jsx
import { useState } from "react";
import Child from "./Child";

function Parent() {
  const [message, setMessage] = useState("");

  const receiveFromChild = (dataFromChild) => {
    setMessage(dataFromChild);
  };

  return (
    <div>
      <h2>Parent Component</h2>
      <p>Message from child: {message}</p>
      <Child sendData={receiveFromChild} />
    </div>
  );
}

export default Parent;
```

---

#### 2️⃣ Child Component:

```jsx
function Child({ sendData }) {
  const handleClick = () => {
    sendData("Hello Parent! This is from the Child.");
  };

  return (
    <div>
      <h3>Child Component</h3>
      <button onClick={handleClick}>Send Data to Parent</button>
    </div>
  );
}

export default Child;
```

---

## 🔄 Props vs State — The Difference

| Feature    | Props                       | State                                 |
| ---------- | --------------------------- | ------------------------------------- |
| Data Flow  | From parent to child        | Local to the component                |
| Mutability | Immutable                   | Mutable (via `setState`)              |
| Used For   | Passing static/dynamic data | Managing dynamic values and changes   |
| Created In | Parent component            | Inside the component using `useState` |
| Ownership  | Parent                      | The component itself                  |

---

### 📌 Important Notes

| Concept               | Meaning                                                                           |
| --------------------- | --------------------------------------------------------------------------------- |
| **Props**             | Passed from parent to child. Cannot be modified in child.                         |
| **Callback function** | A function passed as a prop that allows the child to communicate with the parent. |
| **State lifting**     | Managing the state in a **common parent** and sharing it with children via props. |

---



Great—let’s unpack **props** in depth: what they are, why and when we use them, how to pass them, how to destructure them, patterns around them, and some common gotchas and alternatives.

---

## 1. **What are Props?**

**Props** (short for *properties*) are the way React components **receive data from their parent**. They are the inputs to a component, like function arguments. Props allow components to be **dynamic and reusable**.

### Basic example:

```jsx
function Greeting(props) {
  return <h1>Hello, {props.name}!</h1>;
}

function App() {
  return (
    <>
      <Greeting name="Ali" />
      <Greeting name="Sara" />
    </>
  );
}
```

Here, `name="Ali"` and `name="Sara"` are props passed from `App` (parent) to `Greeting` (child).

### Key characteristics:

* **Unidirectional**: Data flows **from parent to child** only.
* **Read-only** inside the child. You should not modify props directly.
* They make components **configurable** and **reusable**.

---

## 2. **Why and When We Use Props**

### Why:

* To **customize** a component without rewriting it.
* To **pass data** (text, numbers, objects, functions) from one component to another.
* To enable **communication** and composition: parent supplies the behavior or content, child renders it.

### When:

* You have a generic component (e.g., `Button`, `Card`, `ListItem`) and you want to display different content or behavior.
* You want a child to trigger something in the parent via a callback (see later).
* You want to pass configuration, style flags, IDs, handlers, etc.

---

## 3. **Passing Props**

Props are passed like HTML attributes:

```jsx
<MyComponent title="Dashboard" count={5} />
```

Inside `MyComponent`:

```jsx
function MyComponent(props) {
  return (
    <div>
      <h2>{props.title}</h2>
      <p>Count: {props.count}</p>
    </div>
  );
}
```

You can pass any JavaScript value: strings, numbers, arrays, objects, functions, even JSX.

---

## 4. **Destructuring Props**

Destructuring extracts needed fields directly, improving readability:

```jsx
// Instead of:
function UserCard(props) {
  return <p>{props.name} - {props.email}</p>;
}

// Destructure:
function UserCard({ name, email }) {
  return <p>{name} - {email}</p>;
}
```

You can also give default values during destructuring:

```jsx
function Badge({ label = "New", color = "blue" }) {
  return <span style={{ background: color }}>{label}</span>;
}
```

Or combine rest props:

```jsx
function Input({ label, ...rest }) {
  return (
    <div>
      <label>{label}</label>
      <input {...rest} />
    </div>
  );
}
```

---

## 5. **Props That Are Functions (Callback Props)**

Common pattern: parent passes a function so that the child can notify or send data back.

```jsx
function Parent() {
  const handleClick = (value) => {
    console.log("Child sent:", value);
  };

  return <Child onAction={handleClick} />;
}

function Child({ onAction }) {
  return <button onClick={() => onAction("clicked")}>Click me</button>;
}
```

This is how child-to-parent communication happens indirectly.

---

## 6. **Advanced Patterns & Details**

### a. **Children Prop**

Every component automatically receives a special prop called `children`—anything placed between its opening and closing tags:

```jsx
function Wrapper({ children }) {
  return <div className="wrapper">{children}</div>;
}

// Usage:
<Wrapper>
  <p>This is inside wrapper</p>
</Wrapper>
```

### b. **Prop Spreading**

You can forward all props from one component to another:

```jsx
function Button(props) {
  return <button {...props} />; // passes all props (like onClick, className, etc.)
}
```

### c. **Default Props**

You can define defaults so a prop isn’t required:

```jsx
function Title({ text = "Untitled" }) {
  return <h1>{text}</h1>;
}
```

(In older class components or patterns you might see `Component.defaultProps = { ... }`, but with function components default parameters are idiomatic.)

### d. **Prop Validation (Optional but Helpful)**

In plain React you can use `prop-types` library to declare expected types (adds runtime warnings):

```jsx
import PropTypes from "prop-types";

function Profile({ name, age }) {
  return <p>{name} is {age} years old.</p>;
}

Profile.propTypes = {
  name: PropTypes.string.isRequired,
  age: PropTypes.number,
};
```

In TypeScript, you’d instead type the props interface:

```tsx
type ProfileProps = {
  name: string;
  age?: number;
};

function Profile({ name, age }: ProfileProps) {
  return <p>{name} is {age} years old.</p>;
}
```

---

## 7. **Common Gotchas**

* **Mutating props** inside a component is incorrect. Always treat them as immutable.

* **Over-destructuring** can make it harder to see where props come from if too deep.

* Passing new object/array literals inline can cause child re-renders unless memoized:

  ```jsx
  // This creates new object each render -> might re-render child
  <Child config={{ theme: "dark" }} />
  ```

* **Prop drilling**: passing props through many layers just to reach a deep child can become messy. Solutions: context API or state management.

---

## 8. **Summary**

* Props are how parent components give data or behavior to children.
* They’re read-only for children—React treats them as immutable.
* Destructuring makes usage cleaner.
* Callback props enable child-to-parent communication.
* `children` is a special prop for nested content.
* Default values and validation make components safer and easier to use.
* Watch out for prop drilling and unnecessary re-renders.

---

Would you like this explanation turned into a standalone `README.md` file with examples or a small practice worksheet you can give to students?
