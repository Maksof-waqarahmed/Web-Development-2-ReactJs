# 📝 React Forms & React Hook Form Library

## 📚 Topics Covered
- Manual form handling vs React Hook Form library
- Installation and setup of React Hook Form
- `register`, `handleSubmit`, `errors` — core API
- Built-in validation rules (required, minLength, pattern, etc.)
- Displaying error messages
- `watch` — live field monitoring
- `reset` — clearing the form
- Default values for edit forms
- Nested objects and arrays in forms
- Conditional fields
- File uploads with React Hook Form
- Zod schema validation with `@hookform/resolvers`
- Multi-step forms with React Hook Form
- Project: Student Registration Form

---

## **1️⃣ What is a React Form?**

React forms are used to **collect user input** like name, email, password, etc., in a web app.

### 🔹 How it works:

* React **controls the form data** via **state**.
* Input changes are captured using **`onChange`**.
* Form submission is handled via **`onSubmit`**.

### 🔹 Example (Without Library):

```jsx
import { useState } from "react";

function SimpleForm() {
  const [name, setName] = useState("");
  const [email, setEmail] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();
    alert(`Name: ${name}, Email: ${email}`);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Name"
      />
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email"
      />
      <button type="submit">Submit</button>
    </form>
  );
}

export default SimpleForm;
```

✅ **Key Points**:

* You need `useState` for **each input**.
* Validation or error messages need to be written **manually**.
* Complex forms become **hard to manage**.

---

## **2️⃣ What is React Hook Form?**

**React Hook Form** is a **library** that simplifies forms in React.

### 🔹 Why use it?

* No need to manage separate `useState` for each field.
* Built-in **validation** and error handling.
* Better **performance** for large forms (less re-rendering).

### 🔹 Installation:

```bash
npm install react-hook-form
```

---

## **3️⃣ Basic Example Using React Hook Form**

```jsx
import { useForm } from "react-hook-form";

function HookFormExample() {
  const { register, handleSubmit, formState: { errors } } = useForm();

  const onSubmit = (data) => {
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input
        {...register("name", { required: "Name is required" })}
        placeholder="Name"
      />
      {errors.name && <p style={{ color: "red" }}>{errors.name.message}</p>}

      <input
        {...register("email", {
          required: "Email is required",
          pattern: { value: /\S+@\S+\.\S+/, message: "Invalid email" }
        })}
        placeholder="Email"
      />
      {errors.email && <p style={{ color: "red" }}>{errors.email.message}</p>}

      <button type="submit">Submit</button>
    </form>
  );
}

export default HookFormExample;
```

### ✅ Explanation:

1. **`useForm()`** — Hook to manage the form.
2. **`register`** — Connects input to form state.
3. **`handleSubmit`** — Handles form submission.
4. **`errors`** — Displays validation errors.

---

## **4️⃣ Features of React Hook Form**

### **1. Automatic State Management**

* No need for `useState` for each input.
* Keeps **form state internally**.

```jsx
const { register } = useForm();
<input {...register("username")} />
```

---

### **2. Validation**

* Required fields
* Min/Max length
* Pattern matching (regex)
* Custom validation function

```jsx
<input {...register("password", { required: true, minLength: 6 })} />
{errors.password && <span>Password must be at least 6 chars</span>}
```

---

### **3. Error Handling**

* Use `formState.errors` to show messages.

```jsx
{errors.email && <p>{errors.email.message}</p>}
```

---

### **4. Default Values**

* Provide default values for form fields.

```jsx
const { register } = useForm({ defaultValues: { name: "John" } });
```

---

### **5. Nested Objects & Arrays**

* Handles complex forms easily.

```jsx
<input {...register("address.city")} placeholder="City" />
<input {...register("friends[0].name")} placeholder="Friend Name" />
```

---

### **6. Watch & Get Values**

* Watch input changes in real-time.

```jsx
const { watch } = useForm();
const nameValue = watch("name");
console.log(nameValue);
```

---

### **7. Reset Form**

* Reset form fields programmatically.

```jsx
const { reset } = useForm();
reset({ name: "", email: "" });
```

---

### **8. Handling File Uploads**

* Can handle file input without extra state.

```jsx
<input type="file" {...register("profilePic")} />
```

---

### **9. Conditional Fields**

* Show/hide fields based on other values.

```jsx
{watch("subscribe") && (
  <input {...register("email")} placeholder="Enter Email" />
)}
```

---

## **5️⃣ Difference: Using Library vs Without**

| Feature              | Without Library              | With React Hook Form          |
| -------------------- | ---------------------------- | ----------------------------- |
| State Management     | Manual (`useState`)          | Automatic via `register`      |
| Validation           | Manual (if/else)             | Built-in, declarative         |
| Error Handling       | Manual, verbose              | Automatic, easy to display    |
| Performance          | Re-renders on every change   | Optimized, minimal re-renders |
| Complex/Nested Forms | Hard to manage               | Easy with object/array syntax |
| File Uploads         | Needs `useState` or `useRef` | Works with `register`         |

---

## **6️⃣ Example: Advanced Form**

```jsx
import { useForm } from "react-hook-form";

function AdvancedForm() {
  const { register, handleSubmit, watch, formState: { errors }, reset } = useForm();

  const onSubmit = data => {
    console.log(data);
    reset();
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("username", { required: "Username required" })} placeholder="Username" />
      {errors.username && <p>{errors.username.message}</p>}

      <input {...register("email", { pattern: { value: /\S+@\S+\.\S+/, message: "Invalid email" } })} placeholder="Email" />
      {errors.email && <p>{errors.email?.message}</p>}

      <input type="password" {...register("password", { minLength: { value: 6, message: "6+ chars" } })} placeholder="Password" />
      {errors.password && <p>{errors.password.message}</p>}

      <input type="file" {...register("profilePic")} />

      <button type="submit">Submit</button>
    </form>
  );
}
```

✅ **Explanation**:

* Handles multiple validations.
* Shows error messages.
* Includes file upload.
* Resets form after submission.

---

## **7️⃣ Best Practices**

1. Always **register your fields**.
2. Use **validation messages** for better UX.
3. **Watch** fields if you need live changes.
4. **Use defaultValues** for edit forms.
5. Reset form after submission if needed.
6. Combine **nested objects** for complex forms.
7. Avoid unnecessary state updates – rely on React Hook Form state.

---

## **8️⃣ Summary for Beginners**

* React Hook Form = **Easy, Fast, Minimal Re-render**
* Handles:

  * Input fields
  * Validation
  * Errors
  * Nested fields
  * File uploads
  * Reset, watch, conditional rendering
* Better than manual state management for **medium to large forms**
* Makes code **clean, readable, and maintainable**

---

---

## **9️⃣ Zod — Schema Validation with React Hook Form**

**Zod** is a TypeScript-first schema validation library. Combined with React Hook Form, it replaces built-in validation rules with a powerful, reusable schema.

### 🔹 Installation

```bash
npm install zod @hookform/resolvers
```

---

### 🔹 Basic Zod Schema

```jsx
import { z } from “zod”;

const schema = z.object({
  name: z.string().min(2, “Name must be at least 2 characters”),
  email: z.string().email(“Invalid email address”),
  age: z.number({ invalid_type_error: “Age must be a number” }).min(18, “Must be 18+”),
  password: z.string().min(6, “Password must be at least 6 characters”),
  confirmPassword: z.string(),
}).refine((data) => data.password === data.confirmPassword, {
  message: “Passwords do not match”,
  path: [“confirmPassword”],
});
```

---

### 🔹 Connecting Zod to React Hook Form

```jsx
import { useForm } from “react-hook-form”;
import { zodResolver } from “@hookform/resolvers/zod”;
import { z } from “zod”;

const schema = z.object({
  name: z.string().min(2, “Name must be at least 2 characters”),
  email: z.string().email(“Invalid email”),
  password: z.string().min(6, “Password must be 6+ characters”),
});

function RegisterForm() {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm({
    resolver: zodResolver(schema),  // connect Zod schema here
  });

  const onSubmit = (data) => {
    console.log(“Valid data:”, data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <input {...register(“name”)} placeholder=”Full Name” />
        {errors.name && <p style={{ color: “red” }}>{errors.name.message}</p>}
      </div>

      <div>
        <input {...register(“email”)} placeholder=”Email” />
        {errors.email && <p style={{ color: “red” }}>{errors.email.message}</p>}
      </div>

      <div>
        <input type=”password” {...register(“password”)} placeholder=”Password” />
        {errors.password && <p style={{ color: “red” }}>{errors.password.message}</p>}
      </div>

      <button type=”submit”>Register</button>
    </form>
  );
}
```

---

### 🔹 Common Zod Validators

```jsx
z.string()                          // any string
z.string().min(3)                   // min 3 chars
z.string().max(50)                  // max 50 chars
z.string().email()                  // valid email
z.string().url()                    // valid URL
z.string().regex(/^[A-Z]/)         // starts with uppercase
z.number().min(0).max(100)         // number between 0-100
z.boolean()                         // true or false
z.array(z.string()).min(1)         // non-empty string array
z.enum([“admin”, “user”, “guest”]) // one of these values
z.optional()                        // field is optional
z.nullable()                        // can be null
```

---

## **🔟 Multi-step Forms**

A **multi-step form** breaks a long form into smaller steps — easier for users to fill out without feeling overwhelmed.

```
Step 1: Personal Info    →    Step 2: Account Info    →    Step 3: Review & Submit
```

---

### 🔹 Multi-step Form with React Hook Form + Zod

```jsx
import { useState } from “react”;
import { useForm } from “react-hook-form”;
import { zodResolver } from “@hookform/resolvers/zod”;
import { z } from “zod”;

// Step schemas
const step1Schema = z.object({
  firstName: z.string().min(2, “First name required”),
  lastName: z.string().min(2, “Last name required”),
  age: z.coerce.number().min(18, “Must be 18 or older”),
});

const step2Schema = z.object({
  email: z.string().email(“Invalid email”),
  password: z.string().min(6, “Minimum 6 characters”),
  confirmPassword: z.string(),
}).refine((d) => d.password === d.confirmPassword, {
  message: “Passwords do not match”,
  path: [“confirmPassword”],
});

const schemas = [step1Schema, step2Schema];

const STEPS = [“Personal Info”, “Account Setup”, “Review”];

export default function MultiStepForm() {
  const [step, setStep] = useState(0);
  const [formData, setFormData] = useState({});

  const {
    register,
    handleSubmit,
    formState: { errors },
    reset,
  } = useForm({
    resolver: zodResolver(schemas[step]),
  });

  const onNext = (data) => {
    const merged = { ...formData, ...data };
    setFormData(merged);

    if (step < STEPS.length - 2) {
      setStep((s) => s + 1);
      reset();
    } else {
      // Final submission
      console.log(“Final data:”, merged);
      alert(“Form submitted! Check console.”);
      setStep(0);
      setFormData({});
      reset();
    }
  };

  return (
    <div style={{ maxWidth: 480, margin: “40px auto”, padding: 24, border: “1px solid #ddd”, borderRadius: 8 }}>
      {/* Step Indicator */}
      <div style={{ display: “flex”, gap: 8, marginBottom: 24 }}>
        {STEPS.map((label, i) => (
          <div
            key={i}
            style={{
              flex: 1,
              textAlign: “center”,
              padding: “6px 0”,
              borderRadius: 4,
              background: i === step ? “#2196f3” : i < step ? “#4caf50” : “#eee”,
              color: i <= step ? “#fff” : “#999”,
              fontSize: 13,
            }}
          >
            {i < step ? “✓” : i + 1} {label}
          </div>
        ))}
      </div>

      <form onSubmit={handleSubmit(onNext)}>
        {/* Step 1 — Personal Info */}
        {step === 0 && (
          <>
            <h3>Personal Information</h3>
            <input {...register(“firstName”)} placeholder=”First Name” style={{ display: “block”, width: “100%”, padding: 8, marginBottom: 4 }} />
            {errors.firstName && <p style={{ color: “red”, fontSize: 13 }}>{errors.firstName.message}</p>}

            <input {...register(“lastName”)} placeholder=”Last Name” style={{ display: “block”, width: “100%”, padding: 8, marginBottom: 4 }} />
            {errors.lastName && <p style={{ color: “red”, fontSize: 13 }}>{errors.lastName.message}</p>}

            <input {...register(“age”)} type=”number” placeholder=”Age” style={{ display: “block”, width: “100%”, padding: 8, marginBottom: 4 }} />
            {errors.age && <p style={{ color: “red”, fontSize: 13 }}>{errors.age.message}</p>}
          </>
        )}

        {/* Step 2 — Account Setup */}
        {step === 1 && (
          <>
            <h3>Account Setup</h3>
            <input {...register(“email”)} placeholder=”Email” style={{ display: “block”, width: “100%”, padding: 8, marginBottom: 4 }} />
            {errors.email && <p style={{ color: “red”, fontSize: 13 }}>{errors.email.message}</p>}

            <input type=”password” {...register(“password”)} placeholder=”Password” style={{ display: “block”, width: “100%”, padding: 8, marginBottom: 4 }} />
            {errors.password && <p style={{ color: “red”, fontSize: 13 }}>{errors.password.message}</p>}

            <input type=”password” {...register(“confirmPassword”)} placeholder=”Confirm Password” style={{ display: “block”, width: “100%”, padding: 8, marginBottom: 4 }} />
            {errors.confirmPassword && <p style={{ color: “red”, fontSize: 13 }}>{errors.confirmPassword.message}</p>}
          </>
        )}

        {/* Step 3 — Review */}
        {step === 2 && (
          <>
            <h3>Review Your Details</h3>
            <p><b>Name:</b> {formData.firstName} {formData.lastName}</p>
            <p><b>Age:</b> {formData.age}</p>
            <p><b>Email:</b> {formData.email}</p>
            <p style={{ color: “#999”, fontSize: 13 }}>Click Submit to complete registration.</p>
          </>
        )}

        {/* Navigation Buttons */}
        <div style={{ display: “flex”, justifyContent: “space-between”, marginTop: 20 }}>
          {step > 0 && (
            <button type=”button” onClick={() => setStep((s) => s - 1)} style={{ padding: “8px 20px” }}>
              ← Back
            </button>
          )}
          <button type=”submit” style={{ padding: “8px 20px”, background: “#2196f3”, color: “#fff”, border: “none”, borderRadius: 4, marginLeft: “auto” }}>
            {step === STEPS.length - 1 ? “Submit ✓” : “Next →”}
          </button>
        </div>
      </form>
    </div>
  );
}
```

---

### ✅ Key Concepts Summary

| Concept | Purpose |
| ------- | ------- |
| `zod` | Define validation schema with type safety |
| `@hookform/resolvers` | Bridge between Zod and React Hook Form |
| `zodResolver(schema)` | Pass to `useForm({ resolver })` |
| `z.refine()` | Custom cross-field validation (e.g., password match) |
| `z.coerce.number()` | Convert string input to number automatically |
| Multi-step state | `useState` to track current step |
| Accumulated data | Merge each step's data before final submit |

---

💡 **Homework / Practice Project:**

**”Student Registration Form”**

* Fields: Name, Age, Email, Gender, Profile Picture
* Validate all fields using **Zod schema**
* Preview profile picture
* Reset form after submission
* Make it a **3-step form**: Step 1 (Personal Info), Step 2 (Account), Step 3 (Review)
* Bonus: Add conditional fields (e.g., show “School Name” if Age < 18)

---

## 🎯 Interview Questions

**Q1: Why use React Hook Form instead of manual state management?**

> React Hook Form uses uncontrolled inputs with refs internally — causing far fewer re-renders than controlled inputs with `useState`. It also provides built-in validation, error handling, and a much cleaner API. For complex forms it reduces code by 50%+.

**Q2: What does `register` do in React Hook Form?**

> `register(“fieldName”, validationRules)` connects an input to React Hook Form. It returns props (`ref`, `onChange`, `onBlur`, `name`) that are spread onto the input element.

**Q3: How do you show validation error messages?**

> Access `formState.errors.fieldName` — it contains the error object with a `message` property. Render `{errors.email && <p>{errors.email.message}</p>}` below the input.

**Q4: What is the `watch` function in React Hook Form?**

> `watch(“fieldName”)` returns the current live value of a field. Useful for conditional fields (show “other” input when user selects “Other”) or real-time previews.

**Q5: How do you populate a form with existing data for editing?**

> Pass `defaultValues` to `useForm({ defaultValues: existingData })`. React Hook Form sets all field values without `useState`. When the data changes, use `reset(newData)` to repopulate.
