# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?


- List at least two concrete bugs you noticed at the start  
  (for example: "the hints were backwards").

  When I first ran the game, it appeared to work visually but the hints were incorrect and misleading. The score went negative after several guesses, and the New Game button did not reset the game. The guess history was also incomplete, not showing all guesses made during the session.

- **Bug 1:** The hints were reversed — guessing a number lower than the secret showed "Go Lower" instead of "Go Higher"
- **Bug 2:** The New Game button did not work — clicking it had no effect and the game state never reset


**Bug Reproduction Log**

| Input | Expected Behavior | Actual Behavior | Console Output / Error |
|-------|-------------------|-----------------|------------------------|
| Guess 91, secret is 82 | "Too High" / "Go Lower" | "Go Higher" shown instead | none |
| Click "New Game" button | Game resets, new secret generated | Nothing happened, old state persisted | none |
| After 4 guesses on Hard | All 4 shown in history (1–4) | Only 3 shown, last guess missing | none |
| Type "abc" in guess input | Error shown, attempt not counted | Error shown but attempt was still counted | none |


---

## 2. How did you use AI as a teammate?

- I used Claude Code (Anthropic) as my AI assistant throughout this project to help identify and fix bugs in the Streamlit app.
- One example where the AI was correct: it identified that the hints were reversed because `check_guess` returned "Go HIGHER!" when the guess was too high. I verified this by opening the Developer Debug Info panel, checking the secret number, then guessing a known lower number and confirming the hint now correctly said "Go Higher".
- One example where the AI gave a suggestion I had to reject: early on, when I reported that the attempt count wasn't decrementing, the AI immediately tried to change `attempts` from initializing at `1` to `0`. I rejected this edit twice because the app was still running with the old session state in the browser. The AI wasn't accounting for the fact that the fix wouldn't take effect until I hard-refreshed the browser to clear the existing session. I had to point this out before the AI acknowledged it and explained the browser refresh step.

---

## 3. Debugging and testing your fixes

- I decided a bug was fixed by reproducing the exact same steps that caused it originally and confirming the new behavior matched what was expected. For example, after fixing the hint logic I guessed a number I knew was lower than the secret and checked that "Go Higher" appeared.
- One manual test I ran was for input validation: I typed letters, a decimal, and the number 0 into the guess box after the fix and confirmed each one showed an error message without decrementing the attempt counter. Before the fix, all three would silently consume an attempt.
- The AI helped me understand why the Developer Debug Info was always one render behind — it explained that Streamlit renders the page top-to-bottom once per interaction, so any element rendered before the submit block runs will show the state from before that submit. This helped me understand why moving the expander to the bottom of the page fixed the missing last entry.

---

## 4. What did you learn about Streamlit and state?

- Streamlit re-runs the entire Python script from top to bottom every time a user interacts with the app (clicks a button, changes a dropdown, etc.). This means normal Python variables reset on every interaction — they don't remember their value between clicks. `st.session_state` is a special dictionary that persists across these re-runs, which is how the app remembers the secret number, attempt count, and score. I would explain it to a friend like this: imagine the app is a whiteboard that gets completely erased and redrawn every time you click anything — `session_state` is the sticky note on the side that survives the erase.

---

## 5. Looking ahead: your developer habits

- One habit I want to reuse is testing one fix at a time and verifying it in the running app before moving on. Several bugs in this project were interconnected (attempt count, rerender timing, session state), and fixing them all at once would have made it hard to know which change actually solved which problem.
- Next time I work with AI on a coding task, I would test each suggested fix in the live app before accepting it, rather than approving edits based on the explanation alone. A few times the AI's reasoning was correct but the fix didn't visually take effect until I cleared the browser session, which I only discovered by actually running it.
- This project changed how I think about AI-generated code because it showed me that AI can produce code that looks correct and even passes a quick read-through, but still contains subtle logic bugs that only appear when you actually use the app. AI is a fast first draft, not a finished product.
