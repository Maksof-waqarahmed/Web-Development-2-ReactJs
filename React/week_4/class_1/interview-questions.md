# 🧠 **Interview Questions**

| ❓ Question                                   | 💡 Short Answer                                                |
| -------------------------------------------- | -------------------------------------------------------------- |
| What are the Rules of Hooks?                 | 1️⃣ Call at top level 2️⃣ Call only from React functions       |
| Why can’t we call Hooks inside conditions?   | It breaks React’s Hook call order tracking                     |
| Can we call Hooks inside loops?              | ❌ No                                                           |
| Can Hooks be called in class components?     | ❌ No, only functional components                               |
| What is a Custom Hook?                       | A reusable function starting with “use” that uses other hooks  |
| Why must Custom Hooks start with “use”?      | So React can identify them and apply the Rules of Hooks        |
| What happens if Rules of Hooks are violated? | React throws “Invalid Hook Call” error                         |
| Which ESLint plugin checks Hook rules?       | `eslint-plugin-react-hooks`                                    |
| How does React know the order of hooks?      | Based on the **sequential order of hook calls** in each render |
| Can I use a Hook inside a nested function?   | ❌ No, only top-level                                           |


# 💬 Interview Questions

| ❓ Question                                          | 💡 Short Answer                                                            |
| --------------------------------------------------- | -------------------------------------------------------------------------- |
| What is the difference between `fetch` and `axios`? | `fetch` is native; `axios` is third-party with more features.              |
| Does `fetch` throw error on 404?                    | ❌ No, you must check manually using `res.ok`.                              |
| Can `axios` automatically parse JSON?               | ✅ Yes, it does automatically.                                              |
| What are interceptors in axios?                     | Middleware functions for modifying requests/responses globally.            |
| What is `useEffect` used for?                       | For handling side effects like API calls, timers, or subscriptions.        |
| When does `useEffect` run?                          | After render, and again when dependencies change.                          |
| What is cleanup in `useEffect`?                     | Function returned to remove listeners, stop intervals, or cancel requests. |
| Why can’t we use async directly in useEffect?       | Because useEffect expects a cleanup or void return, not a Promise.         |
| What happens if dependency array is empty?          | Effect runs only once (mount).                                             |
| What is the role of AbortController?                | To cancel ongoing fetch requests if a component unmounts.                  |