# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

- What did the game look like the first time you ran it?
- List at least two concrete bugs you noticed at the start  
  (for example: "the secret number kept changing" or "the hints were backwards").
- The Hint seems to be giving the oposite reccomendation to what it's suppsoed to. Instead of suggesting to go higher when the guessed number is lower than the secret. The Hint recommends the opposite.
- WHen the Game either won or lost, the app won't start a new game when the "New Game" button is clicked.
- When submitting the answer, the guess is added into a jistory stack only after the second guess submission, consecuetively every following submission, inserts and compares the previous submission agains the key, instead of doing it during the actual submission

---

## 2. How did you use AI as a teammate?

I used Claude Code (Claude Opus) as my primary AI tool for this project. I asked it to investigate the entire codebase and identify all the bugs before I started fixing anything. One correct suggestion was when Claude explained that the hints were swapped — it traced through the logic step by step showing that when `guess > secret`, the message said "Go HIGHER!" instead of "Go LOWER!", and I verified this by running the app and checking the Developer Debug Info panel against the hints. One example where I had to think critically was the string comparison bug — Claude identified that on even attempts the secret was converted to a string, but understanding *why* that caused wrong results (alphabetical comparison where "5" > "42") required me to reason through it myself to fully grasp the impact.

---

## 3. Debugging and testing your fixes

I decided a bug was fixed by running the app and manually testing the specific scenario that triggered the bug. For example, after fixing the swapped hints, I opened the Developer Debug Info to see the secret number, then entered a guess I knew was too high and confirmed the hint now correctly said "Go LOWER!". I also ran `python -m pytest tests/ -v` after refactoring the logic into `logic_utils.py` — all three tests (`test_winning_guess`, `test_guess_too_high`, `test_guess_too_low`) passed, which confirmed that `check_guess()` was returning the correct outcomes. Claude helped me understand that the existing tests expected a single string return but `check_guess()` actually returned a tuple, so the test assertions needed to unpack the result with `outcome, _ = check_guess(...)` before comparing.

---

## 4. What did you learn about Streamlit and state?

The secret number appeared to change because Streamlit reruns the entire script from top to bottom every time a user interacts with a widget (like clicking a button). Without `st.session_state`, any variable defined in the script just gets re-initialized on every rerun, so calling `random.randint()` outside of session state would generate a new secret each time. I would explain it to a friend like this: "Imagine every time you click a button, the entire page reloads and all your variables reset to their defaults — `session_state` is like a sticky note that survives those reloads and remembers values between clicks." The app already used `st.session_state` to initialize the secret, but the New Game handler also needed to properly reset all session state values (status, score, history) to fully restart the game.

---

## 5. Looking ahead: your developer habits

One habit I want to reuse is systematically annotating bugs with comments before fixing them. In this project, I went through the entire codebase and added `# BUG #N:` comments explaining what was wrong and how to fix it — this made it easy to attack each issue one by one without losing track. Next time I work with AI on a coding task, I would ask it to explain the *underlying logic* of each bug rather than just applying fixes, because understanding why `str("5") > str("42")` evaluates to `True` was more valuable than just knowing to remove the string conversion. This project showed me that AI-generated code can look clean and complete on the surface while hiding subtle bugs — like the even/odd string conversion — that you would only catch by carefully reading the logic or testing edge cases.
