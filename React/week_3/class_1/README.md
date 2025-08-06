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

* **Unidirectional**: Data flows **from parent to child** only.
* Props are **read-only**. (inside the child. You should not modify props directly.)
* You **cannot modify** props inside the child component.
* Props are like **parameters in functions**.
* They make components **configurable** and **reusable**.

## 2. **Why and When We Use Props**

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

### Simple User Info Display Card

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
export function CardComponent({ userInfo }) {
  return (
    <div className="card-component" style={cardStyle}>
      <h1>{userInfo.name}</h1>
      <p>Email: {userInfo.email}</p>
      <p>Phone: {userInfo.phone}</p>
      <p>Address: {userInfo.address}</p>
      <p>City: {userInfo.city}</p>
      <p>State: {userInfo.state}</p>
      <p>ZIP: {userInfo.zip}</p>
      <p>Country: {userInfo.country}</p>
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

## Destructuring Props

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

### Products List

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
      <div style={{ display: "flex", flexWrap: "wrap" }}>
        {products.map(product => (
          <CardComponent key={product.id} product={product} />
        ))}
      </div>
    </div>
  );
}

export function CardComponent({ product }) {
  const { name, price, description, image, category, quantity } = product;
  return (
    <div
      className="card-component"
      style={{
        border: "1px solid #ccc",
        padding: "15px",
        margin: "10px",
        width: "220px",
        borderRadius: "8px",
        boxShadow: "0 2px 6px rgba(0,0,0,0.1)",
        background: "#fff",
        display: "flex",
        flexDirection: "column",
        gap: "8px"
      }}
    >
      <img
        src={image}
        alt={name}
        style={{ width: "100%", height: "120px", objectFit: "cover", borderRadius: "4px" }}
      />
      <h2 style={{ margin: 0, fontSize: "1.1rem" }}>{name}</h2>
      <p style={{ margin: 0, fontSize: "0.9rem" }}>{description}</p>
      <p style={{ margin: 0, fontWeight: "bold" }}>Price: ₹{price}</p>
      <p style={{ margin: 0 }}>Category: {category}</p>
      <p style={{ margin: 0 }}>In stock: {quantity}</p>
    </div>
  );
}
```