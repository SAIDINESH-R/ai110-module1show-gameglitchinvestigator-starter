# 🎮 Game Glitch Investigator: The Impossible Guesser

## 🚨 The Situation

You asked an AI to build a simple "Number Guessing Game" using Streamlit.
It wrote the code, ran away, and now the game is unplayable. 

- You can't win.
- The hints lie to you.
- The secret number seems to have commitment issues.

## 🛠️ Setup

1. Install dependencies: `pip install -r requirements.txt`
2. Run the broken app: `python -m streamlit run app.py`

## 🕵️‍♂️ Your Mission

1. **Play the game.** Open the "Developer Debug Info" tab in the app to see the secret number. Try to win.
2. **Find the State Bug.** Why does the secret number change every time you click "Submit"? Ask ChatGPT: *"How do I keep a variable from resetting in Streamlit when I click a button?"*
3. **Fix the Logic.** The hints ("Higher/Lower") are wrong. Fix them.
4. **Refactor & Test.** - Move the logic into `logic_utils.py`.
   - Run `pytest` in your terminal.
   - Keep fixing until all tests pass!

## 📝 Document Your Experience

- [x] Describe the game's purpose. **Glitchy Guesser is a number guessing game where the player tries to guess a secret number within a limited number of attempts. The game offers three difficulty levels (Easy, Normal, Hard) that control the range of the secret number and the number of attempts allowed. After each guess, the game provides directional hints ("Go Higher" or "Go Lower") to help the player zero in on the answer. The player wins by guessing the correct number before running out of attempts.**

- [x] Detail which bugs you found.
  1. **New Game button not resetting the game** — Clicking "New Game" only reset the secret number and attempt count, but did not reset the game status, score, or history. This meant a finished game could not be restarted properly.
  2. **Attempt count not decrementing after the first guess** — After submitting the first guess, the "Attempts left" counter did not decrease. It only started updating from the second guess onward.
  3. **History in Developer Debug Info showed 0-based indexing and missed the last entry** — The guess history was displayed starting from index 0 instead of 1. Also, when the final guess triggered "Out of attempts", that last guess never appeared in the debug panel because the history was rendered before the submit logic ran.
  4. **Any input was accepted as a valid guess** — The game accepted letters, symbols, decimals, and numbers outside the 1–100 range as valid guesses, counting them as attempts even though they were invalid.
- [x] Explain what fixes you applied.
  1. **New Game button** — Updated the reset logic to also clear `status`, `score`, and `history` in addition to `attempts` and `secret`, so clicking New Game fully restores a fresh game state.
  2. **Attempt count not updating after first guess** — Added `st.rerun()` at the end of every submit so the page always re-renders immediately after a guess, ensuring the "Attempts left" counter updates on the very first guess.
  3. **History indexing and missing last entry** — Changed the history display to use 1-based indexing. Moved the Developer Debug Info expander to below the submit block so it always reflects the most up-to-date state, including the final guess.
  4. **Input validation** — Added a check that rejects any input that is not an integer between 1 and 100. Invalid inputs show an error message and do not count as an attempt or appear in the history.

## 📸 Demo Walkthrough

Describe your fixed game in numbered steps so a reader can follow along without watching a video:

1. Open the app and select a difficulty (Easy, Normal, or Hard) from the sidebar. The sidebar shows the number range and attempts allowed for that difficulty.
2. The main area displays "Attempts left" and a text input box. Enter a number between 1 and 100 and click "Submit Guess". If you enter a letter or a number outside 1–100, an error appears and the attempt is not counted.
3. After each valid guess, the "Attempts left" counter decrements immediately and a hint appears — "Go Higher" if your guess is too low, or "Go Lower" if it is too high.
4. Open the "Developer Debug Info" panel at any time to see the secret number, current attempt count, score, and the full history of your guesses with 1-based numbering.
5. Continue guessing based on the hints. If you guess correctly, a success message shows your final score. If you run out of attempts, the game reveals the secret number and your score.
6. Click "New Game" at any point to fully reset the game — attempts, score, history, and status all clear so you can start fresh.

**Screenshot** *(optional)*: <!-- Insert a screenshot of your fixed, winning game here -->

## 🧪 Test Results

```
# Paste your pytest output here, e.g.:
# pytest tests/
# ========================= X passed in 0.XXs =========================
```

## 🚀 Stretch Features

- [ ] [If you choose to complete Challenge 4, describe the Enhanced UI changes here — a screenshot is optional]
