# cogniquest2 
src/
  App.jsx
  main.jsx
index.html
package.json
vite.config.js
import { useEffect, useState } from 'react';

const lessons = [
  {
    question: 'What is photosynthesis?',
    answer: 'Plants convert sunlight into energy.',
    explanation:
      'Photosynthesis allows plants to take in sunlight, carbon dioxide, and water to create glucose, which is their food.'
  },
  {
    question: 'What gas do plants absorb?',
    answer: 'Carbon dioxide',
    explanation:
      'Plants absorb carbon dioxide from the air through tiny holes in their leaves called stomata.'
  }
];

export default function App() {
  const [index, setIndex] = useState(0);
  const [showAnswer, setShowAnswer] = useState(false);
  const [quizMode, setQuizMode] = useState(false);
  const [score, setScore] = useState(0);
  const [dark, setDark] = useState(true);
  const [streak, setStreak] = useState(0);
  const [progress, setProgress] = useState(0);

  // Load saved progress
  useEffect(() => {
    const saved = JSON.parse(localStorage.getItem('cogniquest'));
    if (saved) {
      setIndex(saved.index);
      setScore(saved.score);
      setStreak(saved.streak);
      setDark(saved.dark);
      setProgress(saved.progress);
    }
  }, []);

  // Save progress
  useEffect(() => {
    localStorage.setItem(
      'cogniquest',
      JSON.stringify({ index, score, streak, dark, progress })
    );
  }, [index, score, streak, dark, progress]);

  const lesson = lessons[index];

  function nextLesson(correct = false) {
    setShowAnswer(false);
    if (correct) {
      setScore(score + 1);
      setStreak(streak + 1);
    }
    setProgress(Math.min(100, progress + 100 / lessons.length));
    setIndex((index + 1) % lessons.length);
  }

  return (
    <div style={styles(dark).container}>
      <h1>🧠 CogniQuest</h1>

      <button onClick={() => setDark(!dark)} style={styles(dark).btn}>
        {dark ? '☀ Light Mode' : '🌙 Dark Mode'}
      </button>

      <p>🔥 Streak: {streak} | ⭐ Score: {score}</p>
      <p>Progress: {Math.round(progress)}%</p>

      {!quizMode ? (
        <>
          <div
            style={styles(dark).card}
            onClick={() => setShowAnswer(!showAnswer)}
          >
            {showAnswer ? lesson.answer : lesson.question}
          </div>

          {showAnswer && (
            <div style={styles(dark).aiBox}>
              🤖 <b>AI Tutor:</b> {lesson.explanation}
            </div>
          )}

          <button
            style={styles(dark).btn}
            onClick={() => nextLesson(true)}
          >
            Next
          </button>

          <button
            style={styles(dark).btnAlt}
            onClick={() => setQuizMode(true)}
          >
            Quiz Me
          </button>
        </>
      ) : (
        <>
          <div style={styles(dark).card}>
            {lesson.question}
          </div>

          <button
            style={styles(dark).btn}
            onClick={() => {
              nextLesson(true);
              setQuizMode(false);
            }}
          >
            ✅ Correct
          </button>

          <button
            style={styles(dark).btnAlt}
            onClick={() => {
              nextLesson(false);
              setQuizMode(false);
            }}
          >
            ❌ Wrong
          </button>
        </>
      )}
    </div>
  );
}

const styles = (dark) => ({
  container: {
    minHeight: '100vh',
    background: dark ? '#0f172a' : '#f8fafc',
    color: dark ? '#fff' : '#000',
    display: 'flex',
    flexDirection: 'column',
    alignItems: 'center',
    justifyContent: 'center',
    gap: '10px',
    fontFamily: 'Arial',
    padding: '20px'
  },
  card: {
    background: dark ? '#1e293b' : '#e2e8f0',
    padding: '30px',
    borderRadius: '14px',
    cursor: 'pointer',
    textAlign: 'center',
    maxWidth: '320px',
    boxShadow: '0 10px 25px rgba(0,0,0,0.3)'
  },
  btn: {
    padding: '10px 16px',
    borderRadius: '10px',
    border: 'none',
    cursor: 'pointer',
    background: '#7c3aed',
    color: '#fff'
  },
  btnAlt: {
    padding: '10px 16px',
    borderRadius: '10px',
    border: 'none',
    cursor: 'pointer',
    background: '#64748b',
    color: '#fff'
  },
  aiBox: {
    background: dark ? '#020617' : '#cbd5f5',
    padding: '15px',
    borderRadius: '10px',
    maxWidth: '320px',
    fontSize: '0.9rem'
  }
});
