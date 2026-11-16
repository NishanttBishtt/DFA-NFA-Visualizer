# DFA/NFA String Processing Visualizer

**Course:** CSET302 – Automata Theory and Computability  
**Project:** Automata Theory Simulator Development Project  
**University:** Bennett University  
**Group ID:** [Enter Group ID]

## 1. Overview

The DFA/NFA String Processing Visualizer is a browser-based educational application for defining and simulating Deterministic Finite Automata (DFA) and Nondeterministic Finite Automata (NFA). Users enter states, alphabet symbols, start and final states, and transitions. The application validates the automaton, draws its state-transition graph, and processes an input string step by step. It displays active states, execution history, and the final Accepted/Rejected result.

The application runs on the client side using HTML, CSS, JavaScript and SVG. The supplied implementation does not require a backend or database.

## 2. Features

- Select DFA or NFA.
- Enter states, alphabet, start state, final states and transitions.
- Validate automaton definitions and input symbols.
- Enforce exactly one destination for each DFA state-symbol pair.
- Allow multiple destinations for an NFA state-symbol pair.
- Generate a state-transition graph using SVG.
- Display start/final states, arrows, transition labels and self-loops.
- Highlight active states during execution.
- Process input strings using **Next Step**.
- Display progress, execution trace and final acceptance/rejection.
- Reset a simulation while retaining the validated automaton.
- Load the built-in sample DFA.

## 3. Technology Stack and Dependencies

| Technology | Purpose |
|---|---|
| HTML | Page structure and input controls |
| CSS | Layout and styling |
| Vanilla JavaScript | Parsing, validation, simulation and UI updates |
| SVG | State-transition graph rendering |
| Modern web browser | Runs the application |

The supplied source uses native browser technologies and does not import third-party JavaScript packages or frameworks. No package installation, database, API key or backend is required. Use a modern browser with JavaScript and SVG support. The application can run on Windows, macOS or Linux.

Record the exact browser and version used during your final testing.

## 4. Prerequisites

- `index.html`, `style.css` and `script.js` in the project directory.
- A modern web browser.
- JavaScript enabled.

## 5. Project Structure

```text
DFA-NFA-String-Processing-Visualizer/
├── index.html   # Main page structure, fields and controls
├── style.css    # Application layout and styling
├── script.js    # Parsing, validation, simulation and graph logic
└── README.md    # Project documentation
```

## 6. Installation and Run Instructions

### Option A: Open directly

1. Download or clone the project.
2. Keep `index.html`, `style.css` and `script.js` in the same directory.
3. Check that the stylesheet and script paths in `index.html` match the filenames.
4. Open `index.html` in a modern browser.

### Option B: Use a local static server

Open a terminal in the project directory and run:

```bash
python3 -m http.server 8000
```

Then visit:

```text
http://localhost:8000
```

This is an optional way to serve the static files; Python is not part of the application's runtime logic.

## 7. How to Use

1. Open the application.
2. Select **DFA** or **NFA**.
3. Enter states separated by commas, e.g. `q0,q1,q2`.
4. Enter alphabet symbols separated by commas, e.g. `0,1`.
5. Enter the start state and final state(s).
6. Enter transitions one per line using `from,symbol,to`.
7. Click **Validate & Draw**.
8. Enter an input string containing only symbols from the declared alphabet.
9. Click **Next Step** repeatedly to inspect execution.
10. Review the active states, progress, graph and trace table.
11. After the last symbol, review the Accepted/Rejected result.
12. Click **Reset** to clear the current simulation while retaining the automaton.

### Transition format

Each line must contain exactly three comma-separated values:

```text
source_state,input_symbol,destination_state
```

Example:

```text
q0,0,q1
```

## 8. Sample Automata and Inputs

### Example 1: DFA accepting binary strings ending in `01`

- Type: DFA
- States: `q0,q1,q2`
- Alphabet: `0,1`
- Start state: `q0`
- Final state: `q2`
- Input: `101`

Transitions:

```text
q0,0,q1
q0,1,q0
q1,0,q1
q1,1,q2
q2,0,q1
q2,1,q0
```

Expected trace:

```text
Initial: q0
Read 1:  q0
Read 0:  q1
Read 1:  q2
```

Expected result: **ACCEPTED**, because the final state is `q2`.

Additional check: input `110` should be **REJECTED**. Its state sequence is `q0 → q0 → q0 → q1`.

### Example 2: NFA accepting strings over `{a,b}` containing substring `ab`

- Type: NFA
- States: `q0,q1,q2`
- Alphabet: `a,b`
- Start state: `q0`
- Final state: `q2`
- Input: `aab`

Transitions:

```text
q0,a,q0
q0,a,q1
q0,b,q0
q1,b,q2
q2,a,q2
q2,b,q2
```

Expected active-state trace:

```text
Initial: {q0}
Read a:  {q0,q1}
Read a:  {q0,q1}
Read b:  {q0,q2}
```

Expected result: **ACCEPTED**, because at least one active state (`q2`) is final.

Additional check: input `bbb` should be **REJECTED**. The active-state set remains `{q0}`.

## 9. Internal Design and Function Documentation

### Global variables

- `machine`: Stores the validated automaton definition.
- `simulation`: Stores the input, calculated steps, final active states and acceptance result.
- `currentStep`: Index of the step currently displayed.
- `$`: Helper that retrieves an HTML element by its ID.

### Main functions

| Function | Purpose | Input / output and behavior |
|---|---|---|
| `csv(value)` | Converts comma-separated form text into an array. | Input: string; output: trimmed array with empty entries removed. |
| `showError(message)` | Displays or hides an error message. | Input: message string. |
| `hideResult()` | Hides and resets the result display. | No arguments. |
| `parseMachine()` | Reads and validates the automaton definition. | Reads form fields; returns the machine object or throws an error. |
| `makeSimulation(m, input)` | Calculates the execution trace. | Inputs: machine and input string; returns simulation data. |
| `validateAndDraw()` | Validates the current automaton and draws its graph. | Updates machine state and UI. |
| `prepareSimulation()` | Creates a simulation for the entered input. | Uses the current machine and input-string field. |
| `renderStep()` | Displays the selected step, progress, trace and final result. | Uses `simulation` and `currentStep`. |
| `drawGraph(m, active)` | Draws the SVG automaton graph. | Inputs: machine and active-state array. |

### Data representation

The machine object contains its type, states, alphabet, start state, final states and transitions. Transitions are stored in a nested object indexed by source state and input symbol; each entry contains an array of destination states. For a DFA, each such array must contain exactly one destination. For an NFA, it may contain multiple destinations.

### Simulation behavior

For a DFA, the active-state array contains one state. For an NFA, the simulator gathers all destinations reachable from every active state for the current symbol. A JavaScript `Set` prevents duplicate destination states.

The complete execution trace is calculated by `makeSimulation()` and stored in `simulation.steps`. The **Next Step** button advances `currentStep`; `renderStep()` displays the corresponding stored step. Thus, the trace is calculated before it is revealed incrementally in the interface.

### Validation behavior

The application checks that:
- At least one state and alphabet symbol are provided.
- State names and alphabet symbols are unique.
- The start state and all final states are defined.
- Each transition follows `from,symbol,to`.
- Transition source and destination states are defined.
- Transition symbols belong to the alphabet.
- A DFA has exactly one transition for every state-symbol pair.
- Every input-string symbol belongs to the alphabet.

For an NFA, a missing transition for an active state and symbol contributes no destination. If no active states remain, the input is rejected after processing is complete.

## 10. Testing

Run the following tests in the final application. Record the **actual output**, Pass/Fail status and screenshot/log evidence after executing each test. Do not mark a test as passed before verification.

| ID | Test scenario | Expected result | Actual result / status |
|---|---|---|---|
| T01 | DFA, input `101` (Example 1) | Accepted | To be recorded |
| T02 | DFA, input `110` (Example 1) | Rejected | To be recorded |
| T03 | NFA, input `aab` (Example 2) | Accepted | To be recorded |
| T04 | NFA, input `bbb` (Example 2) | Rejected | To be recorded |
| T05 | Binary DFA input `10x` | Input-symbol validation error | To be recorded |
| T06 | Remove a required transition from the DFA | Incomplete-DFA validation error | To be recorded |
| T07 | Empty input for either supplied example | Rejected because start state `q0` is not final | To be recorded |
| T08 | NFA with no available move for a consumed symbol | Active set can become empty; final result rejected if no final state remains active | To be recorded |

For each test, document the machine definition, input, expected result, observed result and screenshot or log filename.

## 11. Known Limitations

- Epsilon (`ε`) transitions and epsilon-closure computation are not supported.
- Automatic **Run All** execution is not included.
- DFA–NFA conversion is not implemented.
- Automaton minimization is not implemented.
- Regular-expression conversion is not implemented.
- The graph layout is generated automatically and is not a drag-and-drop editor.

The interface may display `ε` to represent an empty input string. This does not mean epsilon transitions are supported.

## 12. Team Contributions

Complete the entries below so they accurately reflect the work performed by each member.

| Team member | Contribution |
|---|---|
| Nishant Singh Bisht (S24CSEU1446) | Implemented the JavaScript functionality for automaton validation, input processing and state-transition handling. |
| Mayank Tripathi (S24CSEU1460) | Implemented JavaScript functionality for step-by-step execution, graph visualization, execution history and result presentation. |
| Aryan Raj (S24CSEU1441) | Designed and developed the user interface using HTML and CSS, including the application layout, input forms, buttons and overall visual presentation. |
| Amandeep Pant (S24CSEU1469) | Worked on the theoretical logic of DFA and NFA, designed the automata processing approach and developed the corresponding pseudocode. |
| Krish Verma (S24CSEU2353) | Contributed to algorithm design, DFA/NFA transition logic, state-processing methodology and pseudocode development. |

All members contributed to preparing, reviewing and refining the project report and documentation.

## 13. Acknowledgements and Attribution

List external code, images, icons, libraries, tutorials or other resources actually used. Identify reused material and comply with its licence and attribution requirements. Verify the complete project assets before making a final statement about third-party material.

No third-party JavaScript package is imported by the supplied `script.js` implementation.

## 14. Troubleshooting

**Graph does not appear**
- Click **Validate & Draw**.
- Check the automaton definition.
- Confirm that the `graph` SVG element exists in the HTML.
- Check the browser developer console.

**DFA validation fails**
- Confirm that every state has exactly one transition for every alphabet symbol.
- Check for missing or duplicate transitions.

**Input symbol error**
- Ensure every character in the input string belongs to the declared alphabet.

**Transition format error**
- Use one transition per line in `from,symbol,to` format.
- Ensure source and destination states are defined.

**Changes to automaton fields do not appear**
- Click **Validate & Draw** again after changing the fields. This refreshes the machine object stored by the application.

**Next Step is disabled**
- Validate the automaton first.
- The button is disabled after the final input symbol is processed. Reset or validate a new automaton to start another simulation.

## 15. Submission Checklist

- [ ] Include `index.html`, `style.css` and `script.js`.
- [ ] Include this `README.md`.
- [ ] Verify that the application runs on a clean setup.
- [ ] Execute and record all applicable test cases.
- [ ] Attach screenshots for accepted, rejected and validation-error cases.
- [ ] Complete the group ID and team-contribution details.
- [ ] Acknowledge any external resources or reused material.
- [ ] Ensure no passwords, API keys or personal credentials are included.
