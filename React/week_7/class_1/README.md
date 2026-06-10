# 🧠 Quiz App — React Project

## 📚 Topics Covered
- Fetching quiz questions from a public API
- Managing question index with `useState`
- Handling option selection and validation
- Calculating and displaying score
- Conditional rendering for results screen
- `Options` component with props
- Preventing question skipping
- Shuffling answer options
- Project: Full Quiz App with score and feedback

---

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

5. **Randomize Options:**

   * Shuffle **correct and incorrect answers** so the correct answer is not always last.

---

## 🎯 Interview Questions

**Q1: How do you manage question navigation in a quiz app?**

> Use an index state (`currentIndex`) that increments on "Next" click. Render the question at `questions[currentIndex]`. Check `currentIndex >= questions.length - 1` to show the results screen instead.

**Q2: How do you shuffle an array in JavaScript for randomizing options?**

> Use the Fisher-Yates shuffle or the common `.sort(() => Math.random() - 0.5)` pattern. For quiz options, spread all options into an array and shuffle before rendering.

**Q3: How do you prevent selecting an answer after one is chosen?**

> Store the selected answer in state. In the option's `onClick`, check if an answer is already selected — if yes, do nothing. Disable the option buttons once a selection is made.

**Q4: How do you calculate the score?**

> Maintain a `score` state. Each time `onAnswer` is called, compare the selected answer with `correctAnswer`. If they match, call `setScore(prev => prev + 1)`.

**Q5: How do you display conditional results messages like "Excellent!" vs "Try Again"?**

> Calculate the percentage: `(score / total) * 100`. Use a conditional: if `> 80` return "Excellent!", else if `> 50` return "Good effort!", else return "Try again!".

---