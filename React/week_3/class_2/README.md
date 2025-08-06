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
| **State lifting**     | Managing the state in a **common parent** and sharing it with children via props. |

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

## **Common Gotchas**

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

## Quiz App

```jsx
import { useState } from "react";
import Options from "./questions";

// Main Component
function App() {
  const [mcqs, setMcqs] = useState([]);
  const [quizStarted, setQuizStarted] = useState(false);
  const [currentQuestionIndex, setCurrentQuestionIndex] = useState(0);
  const [score, setScore] = useState(0);
  const [showResultButton, setShowResultButton] = useState(false);
  const [showResult, setShowResult] = useState(false);

  const fetchMcqs = async () => {
    try {
      const response = await fetch(
        "https://opentdb.com/api.php?amount=5&category=9&difficulty=easy&type=multiple"
      );
      const mcqsData = await response.json();
      setMcqs(mcqsData.results);
      setQuizStarted(true);
      setScore(0);
      setCurrentQuestionIndex(0);
      setShowResult(false);
      setShowResultButton(false);
    } catch (error) {
      console.error("Error fetching MCQs:", error);
    }
  };

  const handleAnswer = (selectedAnswer) => {
    const currentQuestion = mcqs[currentQuestionIndex];
    if (selectedAnswer === currentQuestion.correct_answer) {
      setScore((prev) => prev + 10);
    }
  };

  const nextQuestion = () => {
    if (currentQuestionIndex < mcqs.length - 1) {
      setCurrentQuestionIndex((prev) => prev + 1);
    } else {
      setQuizStarted(false);
      setShowResultButton(true);
    }
  };

  return (
    <>
      <h1>Quiz App</h1>
      {!quizStarted && !showResultButton && (
        <button onClick={fetchMcqs}>Start Quiz</button>
      )}

      {quizStarted && mcqs.length > 0 && (
        <div>
          <h2>
            Question {currentQuestionIndex + 1}:{" "}
            {mcqs[currentQuestionIndex].question}
          </h2>

          <Options
            key={currentQuestionIndex}
            inCorrectAnswer={mcqs[currentQuestionIndex].incorrect_answers}
            correctAnswer={mcqs[currentQuestionIndex].correct_answer}
            onAnswer={handleAnswer}
          />

          <button onClick={nextQuestion}>Next</button>
        </div>
      )}

      {showResultButton && (
        <div>
          <img
            src="https://wallpapers-clan.com/wp-content/uploads/2022/08/meme-gif-pfp-18.gif"
            alt="Quiz Ended"
          />
          <br />
          <button onClick={() => { setShowResult(true); setShowResultButton(false) }}>Show Result</button>
        </div>
      )}

      {showResult && (
        <div>
          {score > 40 ? (
            <div>
              <h2>✅ You Passed!</h2>
              <h3>🎯 Your Score: {score}</h3>
              <img
                src="https://gifdb.com/images/high/happy-steve-carell-187f3vn0ul880c3q.gif"
                alt="Happy"
              />
            </div>
          ) : (
            <div>
              <h2>❌ You Failed</h2>
              <h3>💔 Your Score: {score}</h3>
              <img
                src="https://media.tenor.com/G_6Rpef99_IAAAAM/crying-sad-shayari-life.gif"
                alt="Sad"
              />
            </div>
          )}
        </div>
      )}
    </>
  );
}

export default App;
```

```jsx
// Options Componnet
const Options = ({ inCorrectAnswer, correctAnswer, onAnswer }) => {
    const allOptions = [...inCorrectAnswer, correctAnswer];
  
    // Shuffle options randomly
    const shuffledOptions = allOptions.sort(() => Math.random() - 0.5);
  
    const handleOptionChange = (answer) => {
      onAnswer(answer);
    };
  
    return (
      <div>
        {shuffledOptions.map((answer, index) => (
          <div key={index}>
            <input
              type="radio"
              name="option"
              value={answer}
              onClick={() => handleOptionChange(answer)}
            />
            <label>{answer}</label>
          </div>
        ))}
      </div>
    );
  };
  
  export default Options;
```