# 🎯 React Props (Properties)

## 📚 Topics Covered
- What are props and why they are read-only
- Passing multiple props to a component
- Destructuring props
- Default props
- Props can be any data type (string, number, array, object, function)
- Passing functions as props — child to parent communication
- State lifting up pattern
- `children` prop
- Prop spreading
- Prop drilling problem and Context API as solution
- `PropTypes` — runtime type checking

---

## 🔹 **What Are Props?**

In React, **props** (short for **properties**) are used to **pass data from one component to another**, usually from a **parent component → to → child component**.

They make your components **dynamic**, **configurable**, and **reusable**.

---

### 🔸 **Simple Example**

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}!</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Rana" />
      <Welcome name="Ali" />
    </div>
  );
}
```

🧠 **Explanation:**

* `name` is a **prop**.
* `Welcome` component receives `props` as an **object**.
* You access it using `props.name`.
* The parent (`App`) sends data to the child (`Welcome`) as an attribute.

---

## 💡 **Why Props Are Important**

| Reason                     | Description                                                  |
| -------------------------- | ------------------------------------------------------------ |
| 🧩 **Reusability**         | You can reuse the same component with different data.        |
| 🔄 **Data Flow**           | They allow communication from **parent to child**.           |
| 🧠 **Customization**       | You can modify a component’s content or behavior externally. |
| 🔍 **Separation of Logic** | Keeps data logic separate from presentation.                 |

---

## 🔹 **Props Are Read-Only**

Props are **immutable** (you cannot change them inside the child component).

```jsx
function Student(props) {
  // ❌ Not allowed
  // props.name = "Ali";  // Error!
  return <h3>{props.name}</h3>;
}
```

✅ You can only **read** props — not modify them.

---

## 🔹 **How Props Work (Behind the Scenes)**

1. Parent component defines attributes (`name="Ali"`)
2. React converts those into an **object** → `{ name: "Ali" }`
3. That object is passed as an argument to the child component.
4. You can then use it inside the child via `props`.

---

### 🔧 **Visual Flow**

```
Parent Component
    ↓ (passes props)
Child Component
    ↓
Uses props to display or perform actions
```

---

## 🔹 **Why and When We Use Props**

### Why:

* To **customize** a component without rewriting it.
* To **pass data** (text, numbers, objects, functions) from one component to another.
* To enable **communication** and composition: parent supplies the behavior or content, child renders it.

### When:

* You have a generic component (e.g., `Button`, `Card`, `ListItem`) and you want to display different content or behavior.
* You want a child to trigger something in the parent via a callback (see later).
* You want to pass configuration, style flags, IDs, handlers, etc.

You can pass any JavaScript value: strings, numbers, arrays, objects, functions, even JSX.
---

## 🧩 **Example: Passing Multiple Props**

```jsx
function UserCard(props) {
  return (
    <div>
      <h2>{props.name}</h2>
      <p>Age: {props.age}</p>
      <p>City: {props.city}</p>
    </div>
  );
}

function App() {
  return (
    <div>
      <UserCard name="Ali" age={25} city="Karachi" />
      <UserCard name="Rana" age={21} city="Lahore" />
    </div>
  );
}
```

```jsx
import React from "react";

// ✅ Sample user object
const userInfo = {
  name: "John Doe",
  email: "john.doe@example.com",
  age: 30,
  isAdmin: true,
  destination: "New York",
  phone: "+1234567890",
  address: "123 Main St, Anytown, USA",
  city: "Anytown",
  state: "CA",
  zip: "12345",
  country: "USA",
};

export default function App() {
  return (
    <div>
      <CardComponent userInfo={userInfo} />
    </div>
  );
}

// ✅ Card Component
export function CardComponent(props) {
  return (
    <div className="card-component" style={cardStyle}>
      <h1>{props.userInfo.name}</h1>
      <p>Email: {props.userInfo.email}</p>
      <p>Phone: {props.userInfo.phone}</p>
      <p>Address: {props.userInfo.address}</p>
      <p>City: {props.userInfo.city}</p>
      <p>State: {props.userInfo.state}</p>
      <p>ZIP: {props.userInfo.zip}</p>
      <p>Country: {props.userInfo.country}</p>
    </div>
  );
}

// ✅ Optional Inline Style (for demo)
const cardStyle = {
  border: "1px solid #ccc",
  padding: "20px",
  width: "300px",
  borderRadius: "10px",
  background: "#f9f9f9",
};
```
---

## 💬 **Destructuring Props**

Instead of using `props.name` again and again, you can extract props directly using **object destructuring**.

### ✅ Without Destructuring

```jsx
function UserCard(props) {
  return <p>{props.name} - {props.email}</p>;
}
```

### ✅ With Destructuring

```jsx
function UserCard(props) {
  const { name, email } = props;
  return <p>{name} - {email}</p>;
}
```

```jsx
function UserCard({ name, email }) {
  return <p>{name} - {email}</p>;
}
```

🧠 **Why use destructuring?**

* Cleaner code
* Better readability
* Avoids repetition of `props.` everywhere

---

## 🧩 **Default Props**

You can assign default values if a prop is not passed.

```jsx
function Greeting({ name = "Guest" }) {
  return <h2>Hello, {name} 👋</h2>;
}

function App() {
  return (
    <>
      <Greeting name="Rana" />
      <Greeting /> {/* default prop will be used */}
    </>
  );
}
```

🧠 **Explanation:**
If `name` is missing, `"Guest"` will be used automatically.

## ✅ You can also **combine rest props**:

```jsx
function Input({ label, ...rest }) {
  return (
    <div>
      <label>{label}</label>
      <input {...rest} />
    </div>
  );
}

function App() {
  return (
    <Input label="Name" type="text" />
    <Input label="Email" type="email" />
  );
}
```

---

## 🎁 **Props Can Be Any Type of Data**

Props can hold **any type of data**:

* String
* Number
* Boolean
* Array
* Object
* Function
* Even another component (JSX)

### ✅ Example

```jsx
function Display({ text, count, data, showMessage }) {
  return (
    <div>
      <h2>{text}</h2>
      <p>Count: {count}</p>
      <p>{data.title}</p>
      <button onClick={showMessage}>Click Me</button>
    </div>
  );
}

function App() {
  const info = { title: "Learning React Props" };

  return (
    <Display
      text="Hello Props!"
      count={10}
      data={info}
      showMessage={() => alert("Function as prop called!")}
    />
  );
}
```

## 🧠 **Example: Passing Props to Components in a List**

```jsx
import React from "react";

const products = [
  {
    id: 1,
    name: "Wireless Earbuds",
    price: 1999,
    quantity: 50,
    category: "Electronics",
    description: "Noise-cancelling wireless earbuds with long battery life and touch controls.",
    image: "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
  },
  {
    id: 2,
    name: "Smartwatch Pro",
    price: 4999,
    quantity: 30,
    category: "Wearables",
    description: "Fitness tracking smartwatch with heart rate monitor, GPS, and customizable watch faces.",
    image: "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
  },
  {
    id: 3,
    name: "Gaming Laptop",
    price: 89999,
    quantity: 15,
    category: "Computers",
    description: "High-performance gaming laptop with RTX GPU, 16GB RAM, and 1TB SSD.",
    image: "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
  },
  {
    id: 4,
    name: "Leather Wallet",
    price: 1299,
    quantity: 100,
    category: "Accessories",
    description: "Genuine leather slim wallet with multiple card slots and coin pocket.",
    image: "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
  },
  {
    id: 5,
    name: "Bluetooth Speaker",
    price: 2499,
    quantity: 60,
    category: "Audio",
    description: "Portable waterproof Bluetooth speaker with deep bass and 12-hour playback.",
    image: "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
  },
  {
    id: 6,
    name: "Yoga Mat",
    price: 899,
    quantity: 200,
    category: "Fitness",
    description: "Eco-friendly non-slip yoga mat with cushioning for joint support.",
    image: "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
  },
  {
    id: 7,
    name: "Organic Skincare Set",
    price: 3599,
    quantity: 80,
    category: "Beauty",
    description: "Daily skincare set with cleanser, toner, moisturizer made from natural ingredients.",
    image: "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
  },
  {
    id: 8,
    name: "Wireless Charger",
    price: 1499,
    quantity: 120,
    category: "Accessories",
    description: "Fast wireless charging pad compatible with all Qi-enabled phones.",
    image: "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
  },
  {
    id: 9,
    name: "Kids’ Backpack",
    price: 2199,
    quantity: 90,
    category: "Bags",
    description: "Durable and colorful backpack with multiple compartments for school kids.",
    image: "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
  },
  {
    id: 10,
    name: "Stainless Steel Water Bottle",
    price: 799,
    quantity: 150,
    category: "Home & Kitchen",
    description: "Insulated water bottle keeps drinks cold for 24h or hot for 12h.",
    image: "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
  }
];

export default function App() {
  return (
    <div>
      <h1>Products List</h1>
      <div>
        {products.map((product) => (
          <CardComponent key={product.id} product={product} />
        ))}
      </div>
    </div>
  );
}

export function CardComponent({ product }) {
  const { name, price, description, image, category, quantity } = product;
  return (
    <div>
      <img
        src={image}
        alt={name}
      />
      <h2>{name}</h2>
      <p>{description}</p>
      <p>Price: ₹{price}</p>
      <p>Category: {category}</p>
      <p>In stock: {quantity}</p>
    </div>
  );
}
```

---

## 🔄 **Passing Function as a Prop (Child → Parent Communication)**

Props are **one-way** (parent → child), but we can send data **from child → parent** by passing a **callback function** as a prop.
---

### ✅ Example

#### Parent Component

```jsx
import { useState } from "react";
import Child from "./Child";

function Parent() {
  const [message, setMessage] = useState("");

  const receiveFromChild = (data) => {
    setMessage(data);
  };

  return (
    <div>
      <h2>Parent Component</h2>
      <p>Message from Child: {message}</p>
      <Child sendData={receiveFromChild} />
    </div>
  );
}

export default Parent;
```

#### Child Component

```jsx
function Child({ sendData }) {
  return (
    <div>
      <h3>Child Component</h3>
      <button onClick={() => sendData("Hello from Child!")}>
        Send to Parent
      </button>
    </div>
  );
}

export default Child;
```

🧠 **Explanation:**

* Parent passes `sendData` function as a prop.
* Child calls `sendData()` and passes data back.
* Parent receives it and updates state.

---

### **State Lifting Up**

State lifting up is a pattern where we move the state to the common parent component and share it with child components via props.

---

### 🧠 Example of “State Lifting Up”:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  return (
    <>
      <ChildA count={count} />
      <ChildB setCount={setCount} />
    </>
  );
}

function ChildA({ count }) {
  return <h2>Count: {count}</h2>;
}

function ChildB({ setCount }) {
  return <button onClick={() => setCount(c => c + 1)}>Increment</button>;
}
```

---

## 🔄 **Props vs State**

| Feature        | Props                 | State                        |
| -------------- | --------------------- | ---------------------------- |
| Data Flow      | Parent → Child        | Component itself             |
| Mutability     | Immutable             | Mutable                      |
| Used For       | Passing external data | Managing internal data       |
| Ownership      | Parent                | Component itself             |
| Change Trigger | From outside          | From inside (via `setState`) |

---

### 📌 Important Notes

| Concept               | Meaning                                                                           |
| --------------------- | --------------------------------------------------------------------------------- |
| **Props**             | Passed from parent to child. Cannot be modified in child.                         |
| **Callback function** | A function passed as a prop that allows the child to communicate with the parent. |
| **State Lifting Up**  | Managing the state in a **common parent** and sharing it with children via props. |

---

### 1️⃣ **Children Prop**

Each component automatically gets a special prop named `children`, which contains any nested JSX.

```jsx
function Wrapper({ children }) {
  return <div className="box">{children}</div>;
}

function App() {
  return (
    <Wrapper>
      <h2>Hello World</h2>
      <p>This is inside Wrapper</p>
    </Wrapper>
  );
}
```

🧠 **Use Case:** For reusable layout components like modals, cards, or wrappers.

---

### 2️⃣ **Prop Spreading**

You can forward all received props to a child element using `{...props}`.

```jsx
function Button(props) {
  return <button {...props}>{props.label}</button>;
}

<Button label="Click" onClick={() => alert("Clicked")} className="btn" />;
```

🧠 **Tip:** Use carefully — avoid spreading unnecessary props.

---

### 3️⃣ **Prop Drilling**

When props are passed **through multiple levels of components** unnecessarily.

```jsx
function App() {
  const userName = "Rana";
  return <Parent userName={userName} />;
}

function Parent({ userName }) {
  return <Child userName={userName} />;
}

function Child({ userName }) {
  return <GrandChild userName={userName} />;
}

function GrandChild({ userName }) {
  return <h1>Hello, {userName}</h1>;
}
```

🧠 **Problem:** Becomes difficult to manage for deeply nested structures.

🩵 **Solution:** Use **Context API** to avoid prop drilling.

---

## 🎯 Interview Questions

**Q1: What are props in React?**

> Props (properties) are read-only data passed from a parent component to a child component. They are the primary way to pass data down the component tree.

**Q2: Why are props read-only?**

> Props being read-only enforces a one-directional data flow (parent → child). If a child could modify props, it would create unpredictable shared mutable state. To send data back up, pass a callback function as a prop.

**Q3: What is the difference between props and state?**

> State is internal, mutable data owned by a component. Props are external, read-only data passed from outside. State changes trigger re-renders; props are updated when the parent re-renders with new values.

**Q4: What is prop drilling and why is it a problem?**

> Prop drilling is passing props through multiple intermediate components that don't need them, just to reach a deeply nested child. It creates unnecessary coupling and makes refactoring painful. Solution: Context API or state management libraries.

**Q5: What is the `children` prop?**

> `children` is a special prop that contains whatever JSX is written between a component's opening and closing tags. It's used for wrapper/layout components like modals, cards, and containers.

---

---

## 🔍 **PropTypes — Runtime Type Checking**

PropTypes provide **runtime warnings** in development when a component receives wrong prop types.

```bash
npm install prop-types
```

```jsx
import PropTypes from "prop-types";
```

### ✅ Basic Example

```jsx
import PropTypes from "prop-types";

function UserCard({ name, age, isAdmin, scores, address, onDelete }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>Age: {age}</p>
      <p>Admin: {isAdmin ? "Yes" : "No"}</p>
      <p>Scores: {scores.join(", ")}</p>
      <p>City: {address.city}</p>
      <button onClick={onDelete}>Delete</button>
    </div>
  );
}

UserCard.propTypes = {
  name: PropTypes.string.isRequired,
  age: PropTypes.number.isRequired,
  isAdmin: PropTypes.bool,
  scores: PropTypes.arrayOf(PropTypes.number),
  address: PropTypes.shape({
    city: PropTypes.string,
    country: PropTypes.string,
  }),
  onDelete: PropTypes.func,
};

UserCard.defaultProps = {
  isAdmin: false,
  scores: [],
  address: { city: "Unknown", country: "Unknown" },
  onDelete: () => {},
};
```

🧠 **Explanation:**
- `PropTypes.string.isRequired` → prop must be a string and is required.
- `PropTypes.arrayOf(PropTypes.number)` → array of numbers.
- `PropTypes.shape({})` → object with a specific structure.
- `.isRequired` → warn if the prop is not provided.

---

### 📋 **All Common PropTypes Rules**

| Rule | Description |
| ---- | ----------- |
| `PropTypes.string` | String value |
| `PropTypes.number` | Number value |
| `PropTypes.bool` | Boolean value |
| `PropTypes.func` | Function |
| `PropTypes.array` | Any array |
| `PropTypes.object` | Any object |
| `PropTypes.arrayOf(type)` | Array of a specific type |
| `PropTypes.shape({})` | Object with specific keys/types |
| `PropTypes.oneOf([...])` | One of a set of specific values |
| `PropTypes.node` | Anything renderable (string, number, JSX) |
| `PropTypes.element` | A React element |
| `.isRequired` | Makes the prop mandatory |

```jsx
Button.propTypes = {
  label: PropTypes.string.isRequired,
  variant: PropTypes.oneOf(["primary", "secondary", "danger"]),
  onClick: PropTypes.func.isRequired,
  disabled: PropTypes.bool,
  children: PropTypes.node,
};
```

> **Note:** PropTypes only run in development mode. They are automatically stripped from production builds.

---

## 🏠 Home Task

Build a **Reusable Card System**:
1. `UserCard` component with props: `name`, `email`, `avatar`, `role`
2. `ProductCard` component with props: `title`, `price`, `image`, `rating`
3. A `Badge` component receiving `label` and `color` as props with a default color
4. Parent `App.jsx` renders a list of 5 UserCards and 5 ProductCards by mapping over arrays
5. Pass a `onDelete` callback from App to each card — clicking "Remove" calls it with the item's id
6. Demonstrate state lifting: clicking a card selects it, selected card shows a different border color
7. Add `PropTypes` validation to all three components (`UserCard`, `ProductCard`, `Badge`)