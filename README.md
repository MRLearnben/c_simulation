# Brace: a C++ course workbench

Brace is a browser-based C++ course for beginners. It has 24 short lessons covering the basics, object-oriented programming and a few "beyond the basics" topics. Each lesson gives you a definition, annotated syntax, key points, runnable code snippets and a real-world problem you can check your answer against. Everything runs in your browser. There is no server, no compiler install and no account.

> Brace is a **simulator**, not a real C++ compiler. It runs a large, teaching-focused subset of C++ with a built-in interpreter. See [Limitations](#limitations).

## Features

- **24 lessons** in three groups: Basics, Object-oriented programming and Beyond the basics.
- **Definition, syntax and key points** in every lesson. The syntax is annotated part by part, so you can see what each piece does.
- **77 runnable snippets.** Open one, edit it, run it.
- **24 real-world problems**, one per lesson (a tip calculator, an electricity bill, an ATM note counter, a library book, a shopping cart, a high-scores file and more). Each has a starter file, a solution and a **Check answer** button that compares your program's output with the expected output.
- **Code editor** with syntax highlighting, line numbers, auto-indent (Tab, Enter and `}`), a Copy button and a Reset button. Press `Esc`, then `Tab`, to leave the editor with the keyboard.
- **Input box for `cin`.** Type the input your program should read.
- **Step-through Memory view.** Move a slider or press play to walk through the run. The current line is highlighted, and variables that changed are marked.
- **Compiler-style errors.** Compile errors and runtime errors show a message and the line, in the style of g++ (missing `;`, undeclared name, private member access, abstract class instantiation, and so on).
- **Virtual files.** `ofstream` and `ifstream` work on an in-memory file system, and the files a run created are listed under the output.
- **Progress saved in your browser.** Completed lessons, your edits and solved problems are kept in `localStorage`.
- **Search** across lessons (press `/`), **light and dark themes**, and a **responsive layout** with a drawer for the lesson list on small screens.

## Lessons

**Basics**
Program structure, Variables and data types, Input and output, Operators, Conditions, Loops, Functions, Arrays and vectors, Strings, Pointers and references, Enumerations

**Object-oriented programming**
Classes and objects, Constructors and destructors, Encapsulation, Static members, Composition, Inheritance, Polymorphism, Abstraction

**Beyond the basics**
Exceptions, Templates, Algorithms and iterators, Map (keys and values), File input and output

## Quick start

Brace is a single static page. You do not need Node to use it.

1. Build the page (see below), or take a prebuilt `dist/index.html`.
2. Open `dist/index.html` in any modern browser.

To host it, put `dist/index.html` on any static host, such as GitHub Pages.

### Build

You need Node.js (any recent version) only to build and to run the tests.

```bash
node build.mjs
```

This inlines the CSS, HTML and all scripts into:

- `dist/index.html`: the single self-contained page.
- `dist/preview.html`: the same page with a doctype, for local testing.

The page loads IBM Plex Sans and IBM Plex Mono from Google Fonts. If the fonts cannot load, it falls back to system fonts. Nothing else uses the network.

## How it works

There is no backend and no real compiler. `interp.js` contains a small C++ lexer, parser and tree-walking interpreter. When you press **Run**:

1. The source is parsed and checked. Errors are reported like g++ would report them, and no output is shown for a program that does not compile.
2. The program runs. `cin` reads from the input box, and `cout` goes to the output pane.
3. Each executed statement is recorded (line, function, variable values, output length). The Memory view replays this trace. The trace is capped at 1500 steps.

The interpreter is exposed as `window.Cpp.run(source, stdin, options)` and returns:

```js
{
  ok, output, error: { kind: 'compile' | 'runtime', message, line },
  trace, truncated, exitCode, files
}
```

## Project structure

```
app.html      Page markup
app.css       Styles (light and dark tokens, layout)
app.js        UI: lesson view, editor, run, step-through, problems, progress
interp.js     C++ lexer, parser and interpreter
lessons.js    The first 16 lessons (window.COURSE)
lessons2.js   Annotated syntax (window.SYN) and the newer lessons (window.NEW_LESSONS)
problems.js   Real-world problems and solutions (window.PROBLEMS)
course.js     Merges lessons, syntax and problems into the final course
build.mjs     Builds dist/index.html
```

## Adding a lesson

Add an object to `NEW_LESSONS` in `lessons2.js`, then add its id to the group order in `course.js`.

```js
{
  id: 'recursion',
  group: 'Basics',            // 'Basics' | 'OOP' | 'More C++'
  title: 'Recursion',
  kw: 'recursion factorial base case',   // search keywords
  definition: 'A function that calls itself ...',
  syntax: [{ label: 'Shape', code: 'int f(int n) { ... }' }],
  points: ['Every recursive function needs a base case.'],
  snippets: [
    { title: 'Factorial', note: 'Short note', code: '#include <iostream>\n...', stdin: '' }
  ],
  tryIt: '#include <iostream>\n...'      // starter code in the editor
}
```

## Adding a real-world problem

Add an entry to `problems.js`, keyed by the lesson id:

```js
PROBLEMS['recursion'] = {
  title: 'Countdown',
  brief: 'Print a countdown from n to 1 using recursion.',
  starter: '// your code here\n',
  solution: '#include <iostream>\n...',
  stdin: '5'                  // optional
};
```

**Check answer** runs your program, runs the solution on the same input, and compares the two outputs ignoring extra whitespace.

## Supported C++

- Types: `int`, `long`, `short`, `char`, `bool`, `float`, `double`, `string`, `auto`, enums and `enum class`
- `cin`, `cout`, `getline`, `endl`, formatting with `fixed`, `setprecision`, `setw`
- `if` / `else`, `switch`, `for`, `while`, `do while`, `break`, `continue`
- Functions: overloading, references, default arguments, recursion
- Arrays, 2D arrays, `vector`, `string` methods, `map`
- Pointers, `new` / `delete`, references
- Classes: constructors, destructors, initializer lists, access control, static members, inheritance, `virtual`, pure virtual, `override`, abstract classes, composition
- Exceptions: `try` / `catch` / `throw`, standard exception types, custom exception classes
- Function and class templates
- `sort`, `reverse`, `find`, `count`, `accumulate`, iterators
- `ofstream` and `ifstream` on virtual files

## Limitations

Brace is for learning, so it is honest about what it does not do.

- It is an interpreter, not a compiler. Some programs that a real compiler accepts will not run here, and a few that a real compiler rejects may run.
- No pointer arithmetic, namespaces (other than `using namespace std`), `typedef`, or multi-file programs.
- `string::find` returns `-1` when nothing is found, not `string::npos`.
- Files are virtual. They exist only during one run and are never written to your disk.
- Destructors are not simulated while an exception unwinds the stack.
- The step-through trace stops after 1500 steps. Long loops still run, but you cannot step through all of them.
- Progress is stored in your browser only. Clearing site data clears it.
- Unsupported features show a clear message instead of silently giving wrong output.

## Contributing

Issues and pull requests are welcome. For a new lesson or problem, run the build and confirm the snippets and the solution run without errors before you open a pull request.

## License

Add a license of your choice before publishing (for example MIT) and put it in a `LICENSE` file. This README does not choose one for you.
