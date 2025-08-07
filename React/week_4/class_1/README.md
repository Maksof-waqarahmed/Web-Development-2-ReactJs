## 📝 Forms and Inputs in React

### 🔹 1. **What is a Controlled Form?**

A **controlled component** in React is a form element (like `<input>`, `<textarea>`, `<select>`) whose value is **controlled by React state**.

**Controlled** means React becomes the single source of truth for the input’s value.

#### ✅ Example:

```jsx
import { useState } from 'react';

function ControlledForm() {
  const [name, setName] = useState("");

  const handleChange = (e) => {
    setName(e.target.value);
  };

  return (
    <form>
      <input type="text" value={name} onChange={handleChange} />
    </form>
  );
}
```

---

## 🔸 What is `e`?

`e` stands for the **event object**.

Whenever you trigger an event (like `onChange`, `onClick`, `onSubmit`) on an input or form, React (or plain JavaScript) provides an **event object** which contains:

* The element that triggered the event
* Its `value`
* Its `name`
* And a lot of other useful info

💡 **Most common use**: `e.target` → refers to the input where the event occurred.

---

### ✅ Benefits of Controlled Components

* React has **complete control** over form data
* Easy to **validate**, **disable**, or **format** input
* Allows for easy **form resets**
* Great for building **dynamic forms** (show/hide inputs based on values)

---

### 🔹 2. `onSubmit` & `preventDefault()`

#### 🔸 `onSubmit`:

This event fires when a form is submitted (usually when clicking a `submit` button).

#### 🔸 `preventDefault()`:

This function prevents the browser’s default behavior — which is **refreshing the page** on form submission.

#### ✅ Example:

```jsx
function MyForm() {
  const [email, setEmail] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault(); // prevents page reload
    console.log("Form submitted with email:", email);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
      />
      <button type="submit">Submit</button>
    </form>
  );
}
```

---

### 🔹 3. Basic Form Validation

**Validation** ensures that users enter proper data before submitting.

#### 🔸 Types of Validation:

* ✅ Required fields
* 🔢 Min/Max length
* 📧 Email format
* 🔐 Password rules
* 🧠 Custom rules (like confirm password matching)

#### ✅ Example:

```jsx
function ValidatedForm() {
  const [email, setEmail] = useState("");
  const [error, setError] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!email.includes("@")) {
      setError("Please enter a valid email.");
      return;
    }
    setError("");
    alert("Submitted successfully!");
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Enter email"
      />
      {error && <p style={{ color: "red" }}>{error}</p>}
      <button type="submit">Submit</button>
    </form>
  );
}
```

---

### 🔹 4. Useful Related Concepts

#### 📌 `value` vs `defaultValue`:

| Concept        | Description                           |
| -------------- | ------------------------------------- |
| `value`        | Controlled input (connected to state) |
| `defaultValue` | Uncontrolled input (initial only)     |

#### 📌 `onChange`:

Runs **every time** the user types or changes the input. You use it to update state.

---

### 🔹 5. Optional but Helpful Techniques

#### 📌 Resetting Form:

```jsx
setName("");
setEmail("");
```

#### 📌 Handling Multiple Inputs with One State Object:

```jsx
const [form, setForm] = useState({ name: "", email: "" });

const handleChange = (e) => {
  setForm({ ...form, [e.target.name]: e.target.value });
};

<input name="name" value={form.name} onChange={handleChange} />
<input name="email" value={form.email} onChange={handleChange} />
```

---

## 🔍 Understanding This Key Line:

```js
setForm({ ...form, [e.target.name]: e.target.value });
```

### 🔹 Assume this state:

```js
const [form, setForm] = useState({
  name: '',
  email: '',
  password: ''
});
```

### 🔹 `e.target.name`

* Refers to the `name` attribute of the input field.
* Example: `<input name="email" />` → `e.target.name === "email"`

### 🔹 `e.target.value`

* Represents the actual value typed by the user.
* Example: `"hello@example.com"`

### 🔹 `[e.target.name]: e.target.value`

This is called a **computed property name**.
It dynamically creates a key using the input’s name and updates its value.

If:

```js
e.target.name = "email"
e.target.value = "hello@example.com"
```

Then:

```js
{ email: "hello@example.com" }
```

### 🔹 What does `...form` do?

This is the **spread operator**.
It copies all existing key-value pairs in the `form` state, **preserving old values**.

#### ❌ Bad way (overwrites all values):

```js
setForm({ email: "hello@example.com" });
```

This will **remove** the previous values like `name` and `password`.

#### ✅ Correct way:

```js
setForm({
  ...form,
  [e.target.name]: e.target.value
});
```

This way, only the updated field changes, and other data remains intact.

---

### 🧠 Real Example:

```js
<input name="email" onChange={(e) => setForm({ ...form, [e.target.name]: e.target.value })} />
```

Let’s say:

* Current state: `{ name: '', email: '', password: '' }`
* User types `"rana@gmail.com"` in the email input

Then:

* `e.target.name` → `"email"`
* `e.target.value` → `"rana@gmail.com"`

So, new state becomes:

```js
{
  name: '',
  email: 'rana@gmail.com',
  password: ''
}
```

✅ Only `email` was updated. All other values were preserved.

---

## ❓ What is this technique called?

👉 It’s called **dynamic input handling using computed property names** in React.

It’s a **very common pattern** for handling multiple form fields using a single state object.

```js
<input name="username" />
<input name="email" />
<input name="password" />
```

All can be handled by **one state object and one handler function**.

---

### 🔹 6. Best Practices for Forms in React

✅ Always use `onSubmit` on the `<form>` element
✅ Prefer controlled components over uncontrolled
✅ Separate validation logic (optional: use form libraries)
✅ Disable submit button until form is valid
✅ Display meaningful, user-friendly error messages


### Student Feedback Portal

```jsx
import { useState } from "react";
import FeedbackList from "./list-feedback";

function App() {
  const [feedback, setFeedback] = useState({
    name: '',
    email: '',
    course: '',
    semester: '',
    department: '',
    instructor: '',
    rating: '',
    feedbackText: '',
  });

  const [allFeedback, setAllFeedback] = useState([]); // 🔁 This stores all submitted feedback
  const [submitted, setSubmitted] = useState(false);

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFeedback(prev => ({
      ...prev,
      [name]: value,
    }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();

    // Add current feedback to the list
    setAllFeedback(prev => [...prev, feedback]);

    // Show success message
    setSubmitted(true);
    setTimeout(() => setSubmitted(false), 3000);

    // Clear form after submission
    setFeedback({
      name: '',
      email: '',
      course: '',
      semester: '',
      department: '',
      instructor: '',
      rating: '',
      feedbackText: '',
    });
  };

  console.log("first", allFeedback);

  return (
    <>
      <h1>Student Feedback Portal</h1>

      {submitted && (
        <p style={{ color: 'green', fontWeight: 'bold' }}>
          ✅ Feedback submitted successfully!
        </p>
      )}

      <form onSubmit={handleSubmit}>
        <label htmlFor="name">Name:</label> <br />
        <input type="text" name="name" id="name" placeholder="John" required value={feedback.name} onChange={handleChange} /> <br /><br />

        <label htmlFor="email">Email:</label> <br />
        <input type="email" name="email" id="email" placeholder="john@example.com" required value={feedback.email} onChange={handleChange} /> <br /><br />

        <label htmlFor="course">Course:</label> <br />
        <input type="text" name="course" id="course" placeholder="Course Name" required value={feedback.course} onChange={handleChange} /> <br /><br />

        <label htmlFor="semester">Semester:</label> <br />
        <select name="semester" id="semester" value={feedback.semester} onChange={handleChange} required>
          <option value="">Select your Semester</option>
          <option value="one">One</option>
          <option value="two">Two</option>
          <option value="three">Three</option>
          <option value="four">Four</option>
          <option value="five">Five</option>
          <option value="six">Six</option>
          <option value="seven">Seven</option>
          <option value="eight">Eight</option>
        </select> <br /><br />

        <label htmlFor="department">Department:</label> <br />
        <select name="department" id="department" value={feedback.department} onChange={handleChange} required>
          <option value="">Select your Department</option>
          <option value="computer science">CS</option>
          <option value="software engineering">SE</option>
          <option value="artificial intelligence">AI</option>
        </select> <br /><br />

        <label htmlFor="instructor">Instructor:</label> <br />
        <input type="text" name="instructor" id="instructor" placeholder="Instructor Name" required value={feedback.instructor} onChange={handleChange} /> <br /><br />

        <p>Give Rating:</p>
        <div>
          {[1, 2, 3, 4, 5].map((num) => (
            <span key={num}>
              <input
                type="radio"
                id={`rating-${num}`}
                name="rating"
                value={num}
                checked={feedback.rating === String(num)}
                onChange={handleChange}
              />
              <label htmlFor={`rating-${num}`}>{num}</label>
            </span>
          ))}
        </div> <br /><br />

        <label htmlFor="feedbackText">Feedback:</label> <br />
        <textarea
          name="feedbackText"
          id="feedbackText"
          placeholder="Your feedback here..."
          required
          cols={50}
          rows={10}
          value={feedback.feedbackText}
          onChange={handleChange}
        ></textarea> <br /><br />

        <button type="submit">Submit</button>
      </form>

      <hr />
      <h2>Feedback List</h2>
      {allFeedback.length === 0 ? <p>No feedback submitted yet.</p> : <FeedbackList feedbacks={allFeedback} />}
    </>
  );
}

export default App;
```

```jsx
// list-feedback.jsx
function FeedbackList({ feedbacks }) {
    if (!feedbacks || feedbacks.length === 0) {
        return <p>No feedback available.</p>;
    }

    return (
        <div>
            {feedbacks.map((fb, index) => (
                <div key={index} style={{ border: '1px solid #ccc', padding: '1rem', marginBottom: '1rem' }}>
                    <p><strong>Name:</strong> {fb.name}</p>
                    <p><strong>Email:</strong> {fb.email}</p>
                    <p><strong>Course:</strong> {fb.course}</p>
                    <p><strong>Semester:</strong> {fb.semester}</p>
                    <p><strong>Department:</strong> {fb.department}</p>
                    <p><strong>Instructor:</strong> {fb.instructor}</p>
                    <p><strong>Rating:</strong> {fb.rating}</p>
                    <p><strong>Feedback:</strong> {fb.feedbackText}</p>
                </div>
            ))}
        </div>
    );
}

export default FeedbackList;
```
---
## 🧑‍🎓 Student Tasks – Feedback App Project

Please complete the following tasks to enhance your React-based Student Feedback App. These features will improve usability, functionality, and user interaction.

---

### ✅ 1. Rating System (Stars or Emojis)

* Add a **rating system** to each feedback submission (1 to 5).
* Use **star icons (⭐)** or **emojis** like:

  ```
  😡 😐 🙂 😃 🤩
  ```
* Make it user-friendly and visually appealing.
* This helps in expressing the quality of feedback in a fun and clear way.

---

### ✅ 2. Filter Feedback by Rating

* Add a **dropdown** or **button group** to filter feedbacks.
* Allow options like:

  * Show all feedback
  * Show only 4-star and above
  * Show only 5-star
* This makes it easier to analyze high-rated feedback quickly.

---

### ✅ 3. Editable Feedback

* Each feedback card should include an **"Edit" button**.
* On clicking edit:

  * The feedback data should load back into the form.
  * User can update and re-submit.
* Ensure that the updated feedback replaces the old one (do not add it as new).

---

### ✅ 4. Delete Feedback

* Add a **"Delete" button** to each feedback card.
* On clicking, remove that feedback from the list.
* Optional: Show a confirmation popup before deletion.

---

### ✅ 5. Total Feedback Count

* Display a live count of total feedbacks submitted.
* Example:

  ```
  You have submitted 5 feedbacks.
  ```
* Update this count in real-time as feedbacks are added or deleted.
