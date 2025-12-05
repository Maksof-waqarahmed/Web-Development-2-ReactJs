# Quiz App

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