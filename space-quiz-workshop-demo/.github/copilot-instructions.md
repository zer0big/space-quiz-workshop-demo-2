# ORBIT space quiz

## Project scope and execution

The project is the Korean-language space exploration quiz in `index.html` at the repository root.

Keep the delivered application in one self-contained `index.html`: inline CSS, JavaScript, and SVG, with system fonts and emoji. It must run from a `file:` URL without a server, network requests, external dependencies, or a build step.

Open it from PowerShell:

```powershell
Start-Process .\index.html
```

## Application structure

The inline script owns a fixed set of ten questions, shuffled into a new order (Fisher–Yates `order` array) on page load and on restart. Each `questions` entry has `category` (no number prefix; the displayed number is the position in the shuffled order), four `options`, a zero-based `answer` index, and `explanation`. Only question order is shuffled, never options or `answer`. Scoring is ten points per correct answer, up to 100; several labels, the progress maximum, and result thresholds also assume ten questions.

State is held in `current`, `correct`, `answered`, and `scoreAnimation`. `renderQuestion()` creates the answer buttons; `chooseAnswer()` locks the current answer, updates score and progress, and reveals the explanation. Navigation is explicit through the next button, not automatic. `showResult()` swaps the question/result panels using `hidden`. Restart cancels the score animation, clears state and counters, and renders the first question.

Result reactions depend on the correct-answer count: exactly 10, at least 7, at least 4, or fewer than 4. The displayed final score counts up over 1,200 ms using `requestAnimationFrame` and cubic ease-out. The animation updates the first text node of `#result-score`; its trailing span contains the score denominator. Preserve that DOM relationship when changing result markup.

## UI and interaction conventions

- Keep user-facing quiz text in Korean; English category and mission labels are part of the current visual design.
- Theme colors are semantic CSS custom properties in `:root`, overridden by `prefers-color-scheme: dark`. Use the existing tokens for theme-dependent UI.
- Responsive layout changes at 800 px and 620 px. Result emoji scale with `clamp(72px, 10vw, 88px)`.
- Correct answers use `.correct` and green feedback; wrong selections use `.wrong` and red shaking feedback. Textual check/cross labels convey the result independently of color.
- Answer buttons use `aria-disabled="true"` after selection rather than native `disabled`, preserving the selected button's focus. The `answered` guard prevents repeat scoring; setting answer `tabIndex` to `-1` makes the next button the following Tab stop. Preserve these behaviors together.
- Questions and the result heading have `tabindex="-1"` for programmatic focus on navigation. Feedback uses a polite, atomic status region. Keep the skip link, visible focus indicators, native buttons, and labeled progress element.
- Respect reduced motion in both CSS and JavaScript: CSS disables animations/transitions, while the score counter immediately shows its final value when `prefers-reduced-motion: reduce` matches.
- Dynamic question, answer, and explanation text is inserted with `textContent` or text nodes. Existing `innerHTML` assignments are limited to application-controlled markup.

## Focused browser checks

Exercise the file directly in a browser; browser automation should also use its `file:` URL.

- For a single interaction check, select one answer with Enter or Space, activate it again, and verify that score/progress change only once. Tab must then reach the next button.
- Complete a run and restart: check all ten questions, score/progress reset, and focus on the next question or result heading.
- For result-only checks in the browser console, use `correct = 7; showResult()` to exercise a 70-point result without answering every question. Reload afterward to restore normal state. Check counts of 0, 4, 7, and 10 for the four reaction tiers.
- Check an intermediate count-up value and the exact final score after 1,200 ms; restart during the animation must cancel it. Reduced-motion mode must show the final score immediately.
- Check light/dark modes and narrow screens down to 320 px, including the enlarged emoji result screen, for horizontal overflow. The page must make no HTTP(S) requests.
