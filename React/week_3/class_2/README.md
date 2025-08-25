## ⚔️ `fetch` vs `axios` in React

### 🔹 1. `fetch`

#### ✅ Pros:

* Built-in in browser (no need to install)
* Lightweight
* Returns a **Promise**

#### ❌ Cons:

* Default **doesn’t throw errors** for HTTP errors (like 404)
* You must **manually convert** response to `.json()`
* Slightly verbose syntax

#### 📦 Example:

```js
useEffect(() => {
  fetch("https://opentdb.com/api.php?amount=5&type=multiple")
    .then((res) => res.json())
    .then((data) => console.log(data))
    .catch((err) => console.error(err));
}, []);
```

---

### 🔸 2. `axios`

#### ✅ Pros:

* Throws error automatically on bad HTTP responses (404, 500)
* Automatically parses JSON
* Can set headers and base URL easily
* Supports request/response interceptors

#### ❌ Cons:

* Requires installation (`npm install axios`)
* Slightly larger bundle size than `fetch`

#### 📦 Example:

```js
import axios from "axios";

useEffect(() => {
  axios.get("https://opentdb.com/api.php?amount=5&type=multiple")
    .then((res) => console.log(res.data))
    .catch((err) => console.error(err));
}, []);
```

---

## 🔍 Comparison Table

| Feature         | `fetch`           | `axios`                    |
| --------------- | ----------------- | -------------------------- |
| Installation    | Not required      | `npm install axios`        |
| JSON parsing    | Manual: `.json()` | Automatic                  |
| Error handling  | Manual            | Automatic                  |
| Interceptors    | ❌                 | ✅                          |
| Base URL config | ❌                 | ✅                          |
| Request cancel  | Complex           | Easy (`axios.CancelToken`) |
| Browser support | Modern browsers   | All modern (same)          |

---

## 🔰 When to Use What?

| Use Case                        | Recommended |
| ------------------------------- | ----------- |
| Small project / beginner        | `fetch`     |
| Larger apps / advanced features | `axios`     |
| Need interceptors/auth headers  | `axios`     |
| Need native browser only        | `fetch`     |

---
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
| **State lifting Up**     | Managing the state in a **common parent** and sharing it with children via props. |

---

## **Advanced Patterns & Details**

### **Children Prop**

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

### **Prop Spreading**

You can forward all props from one component to another:

```jsx
function Button(props) {
  return <button {...props} />; // passes all props (like onClick, className, etc.)
}
```

### **Default Props**

You can define defaults so a prop isn’t required:

```jsx
function Title({ text = "Untitled" }) {
  return <h1>{text}</h1>;
}
```

### **Prop Drilling**

Prop drilling means **passing props through multiple levels of components** just to get data from a parent component to a deeply nested child component.

For example:
If `App → Parent → Child → GrandChild` and you want to send data from `App` to `GrandChild`, you may need to pass the same prop through `Parent` and `Child` even if they don’t actually need it. This makes the code **messy and hard to maintain**.

```js
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
  return <h1>Hello {userName}</h1>;
}
```
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

## Quiz App

```jsx
import { useState } from "react";
import { Options } from "./options";

export function QuizApp() {
  const [mcqs, setMcqs] = useState([]);
  const [quizStarted, setQuizStarted] = useState(false);
  const [currentQuestionIndex, setCurrentQuestionIndex] = useState(0);
  const [score, setScore] = useState(0);
  const [answer, setAnswer] = useState("");
  const [showResult, setShowResult] = useState(false);

  async function getQuizQuestions() {
    const data = await fetch(
      "https://opentdb.com/api.php?amount=10&category=9&difficulty=easy&type=multiple"
    );
    const questions = await data.json();
    setMcqs(questions.results);
    setQuizStarted(true);
    setCurrentQuestionIndex(0);
    setScore(0);
    setShowResult(false);
  }

  function nextQuestion() {
    if (currentQuestionIndex < mcqs.length - 1) {
      setCurrentQuestionIndex(currentQuestionIndex + 1);
    } else {
      if (mcqs[currentQuestionIndex].correct_answer === answer) {
        setScore(score + 10);
      }
      setQuizStarted(false);
      setShowResult(true);
    }
  }

  function handleAnswer(answer) {
    setAnswer(answer);
  }

  return (
    <div>
      <h1 className="text-green-500 font-bold">Quiz App</h1>
      {!quizStarted && (
        <button onClick={getQuizQuestions}>Start Quiz</button>
      )}
      {quizStarted && (
        <h2>Question: {currentQuestionIndex + 1}</h2>
        <h2>{mcqs[currentQuestionIndex].question}</h2>
        <Options
          correctAnswer={mcqs[currentQuestionIndex].correct_answer}
          incorrectAnswers={mcqs[currentQuestionIndex].incorrect_answers}
          onAnswer={handleAnswer}
        />
        <button onClick={nextQuestion}>Next Question</button>
      )}
      {showResult && (
        <h2>Score: {score}</h2>
        {score > 50 ? (
          <img src="https://gifdb.com/images/high/happy-steve-carell-shaqari-life-gif-alt.gif" alt="happy" />
        ) : (
          <img src="https://media.tenor.com/G_6RpeT99_IAAAA/crying-sad-shayari-life.gif" alt="sad" />
        )}
      )}
    </div>
  );
}
```

```jsx
// Options Componnet
export const Options = ({ correctAnswer, incorrectAnswer, onAnswer }) => {
  const allOptions = [...incorrectAnswer, correctAnswer];

  const handleOption = (value) => {
    onAnswer(value);
  };

  return (
    <div>
      {allOptions.map((option, index) => {
        return (
          <div key={index}>
            <input
              type="radio"
              name="option"
              value={option}
              onClick={() => handleOption(option)}
            />
            <label>{option}</label>
          </div>
        );
      })}
    </div>
  );
};
```

--- 
### 🏠 Home Task for Todo App
. **Reset Option Selection:**

   * Ensure that **no option is pre-selected** when moving to the next question.

2. **Prevent Skipping Questions:**

   * The user **cannot go to the next question** without selecting an answer.

3. **Improve UI:**

   * Make the quiz visually appealing using **buttons, spacing, colors, and hover effects**.
   * Highlight the selected option and provide **visual feedback**.

4. **Display Final Result with Message:**

   * Optionally enhance final results:

     * “Excellent!” for scores > 80
     * “Good effort!” for scores 50–80
     * “Try again!” for scores < 50
   * Add a **retry button** to restart the quiz.

5. **Randomize Options (Optional Advanced Task):**

   * Shuffle **correct and incorrect answers** so the correct answer is not always last.
---