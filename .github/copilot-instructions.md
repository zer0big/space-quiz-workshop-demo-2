# ORBIT space quiz

The app is a Korean-language space exploration quiz: a single self-contained `space-quiz-workshop-demo\index.html` (inline CSS, JS, SVG; system fonts and emoji). More detailed guidance lives in `space-quiz-workshop-demo\.github\copilot-instructions.md`; read it before changing the app.

## Build, test, lint

There is no build, package manager, test suite, or linter. It must run from a `file:` URL with no server, network requests, or external dependencies.

```powershell
Start-Process .\space-quiz-workshop-demo\index.html
```

Verification is manual in a browser (or browser automation via `file:` URL). To check a result screen without answering everything, run `correct = 7; showResult()` in the console, then reload. Check counts 0, 4, 7, 10 for the four reaction tiers.

## Architecture

- A fixed ten-question `questions` array; each entry has `category`, `question`, four `options`, zero-based `answer`, and `explanation`. Scoring is 10 points per correct answer (max 100); labels, the progress maximum, and result thresholds all assume exactly ten questions.
- State: `current`, `correct`, `answered`, `scoreAnimation`. `renderQuestion()` builds answer buttons; `chooseAnswer()` locks the answer, updates score/progress, reveals the explanation; the next button advances explicitly (no auto-advance); `showResult()` toggles panels via `hidden`; restart cancels the score animation, resets state, and renders question 1.
- Result reactions depend on correct count: 10, >=7, >=4, <4. The final score counts up over 1,200 ms with `requestAnimationFrame` and cubic ease-out, updating the first text node of `#result-score`; its trailing span holds the denominator. Keep that DOM relationship when editing result markup.

## Conventions

- User-facing quiz text is Korean; English category/mission labels are intentional.
- Theme colors are semantic CSS custom properties in `:root`, overridden under `prefers-color-scheme: dark`. Use existing tokens.
- Breakpoints at 800 px and 620 px; result emoji use `clamp(72px, 10vw, 88px)`. No horizontal overflow down to 320 px.
- Correct = `.correct` (green); wrong = `.wrong` (red, shaking). Text check/cross labels must convey result without color.
- Answer buttons use `aria-disabled="true"` after selection, not native `disabled`, to keep focus; the `answered` guard prevents double scoring; answer `tabIndex = -1` makes the next button the following Tab stop. Keep these together.
- Questions and the result heading have `tabindex="-1"` for programmatic focus. Feedback uses a polite, atomic status region. Keep the skip link, visible focus styles, native buttons, and labeled progress element.
- Reduced motion: CSS disables animations/transitions, and JS shows the final score immediately when `prefers-reduced-motion: reduce` matches.
- Insert dynamic question/answer/explanation text with `textContent` or text nodes; `innerHTML` only for application-controlled markup.
