# Copilot instructions

## Project shape

This is a dependency-free static web app. The entire application lives in
`index.html`, including:

- The semantic HTML shell and the `#quiz` mount point.
- All responsive CSS, theme variables, mission-control visual styling, and
  reduced-motion handling.
- The quiz data and browser-side state/rendering logic.

There is no build system, package manager configuration, server, or test
framework. Open the file directly with a `file://` URL or use the integrated
browser preview.

## Build, test, and lint

There are no repository-defined build, test, or lint commands. Do not add a
toolchain or dependencies for routine changes.

For browser-level validation, open `index.html` in the integrated browser and
exercise the complete flow:

1. Confirm the initial question, progress bar, score badge, and answer buttons.
2. Activate answers with both pointer and keyboard; verify focus-visible
   states, correct/incorrect styling, feedback text, and score updates.
3. Advance through all 10 questions and verify the results/debrief screen.
4. Verify the review lists the selected answer and, for misses, the correct
   answer; verify “Explore again” resets the quiz.

For a focused manual check, use the browser's page evaluation against the
current page, for example:

```js
document.querySelector('.answer[data-index="1"]').click()
```

Then inspect `.score strong`, `#feedback`, `.next.ready`, and the rendered
results elements. Since there is no automated test runner, a single test is a
single browser interaction/assertion rather than a CLI command.

## Architecture and state flow

`questions` is the single source of truth. Each question has `q`, `a`,
`correct`, and `fact` fields; answer indexes are zero-based and display as
letters A–D.

The app uses a small state machine:

- `current` tracks the active question index.
- `score` tracks correct answers.
- `locked` prevents multiple selections for one question.
- `selectedAnswers` preserves every choice for the results review.

`renderQuestion()` replaces the contents of `#quiz` and attaches listeners to
the newly created answer and next buttons. `selectAnswer()` locks the current
question, records the answer, updates the score, marks correct/incorrect
answers, and reveals the next action. `nextQuestion()` advances or calls
`renderResults()`. `renderResults()` builds the score summary and mission
debrief, then wires the restart button to reset all state.

When changing quiz behavior, update the state transitions and the rendered
accessibility attributes together. The results screen assumes every question
has a recorded selection.

## Conventions specific to this app

- Keep the app dependency-free and contained in `index.html` unless the user
  explicitly requests a structural change.
- Reuse the existing CSS custom properties for colors and surfaces so
  `prefers-color-scheme: dark` stays visually coherent with the light theme.
- Preserve the mission-control visual language: telemetry labels, monospace
  uppercase metadata, purple/teal accents, green success states, and red
  error states.
- Keep answer controls as native `<button>` elements. Preserve keyboard
  activation, `:focus-visible` styling, disabled states after selection, and
  status updates through the existing `aria-live` regions.
- Decorative emoji and visual indicators use `aria-hidden="true"`; meaningful
  status text remains in nearby semantic content.
- Respect `prefers-reduced-motion: reduce` when adding animations or
  transitions.
- Keep question and answer text in the `questions` array rather than
  duplicating it in templates. Use the existing zero-based answer indexes and
  letter mapping.
- Prefer the existing render-and-rebind pattern over introducing a framework,
  global event delegation, or a second state-management approach.
- Keep result review content readable and bounded by the existing scrollable
  `.review-list`; do not let a longer debrief break the narrow-column layout.
