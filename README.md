# myRIO Embedded C Labs: Keypad Input and LCD Output

Embedded C programs for the National Instruments myRIO that take numbers from a
keypad and show them on a character LCD. Lab 0 builds a validated number-entry
routine on top of the course's keypad and LCD functions. Lab 2 goes one level
down and writes the keypad driver itself: a buffered, character-at-a-time reader
that edits input as it is typed.

## Lab 0: validated number entry

[`lab0-validated-number-input/main.c`](lab0-validated-number-input/main.c)

`double_in(prompt)` shows a prompt, reads a line from the keypad with
`fgets_keypad`, and loops until the text is a valid number:

- empty input prints "Short. Try Again"
- the up and down arrow keys (`[` and `]`) print "Bad Key"
- a `-` anywhere except the first character prints "Bad Key"
- text that `sscanf("%lf")` cannot parse prints "Invalid Number"

`main` uses it to read a velocity and a pressure, then prints both on the LCD with
`printf_lcd`, using `\f` to clear the screen and `\v` to rewrite the prompt line.

## Lab 2: keypad driver

[`lab2-keypad-driver/main.c`](lab2-keypad-driver/main.c)

`getchar_keypad` replaces the course library version with a two-state machine:

- **Input state.** Reads raw keys with `getkey()` into a 60-character static
  buffer and echoes each accepted key to the LCD. Digits are always accepted, a
  minus sign only as the first character, and a decimal point only once.
  Delete (`DEL`, 8) removes the last character, erases it on screen with
  `"\b \b"`, and re-allows the decimal point if that is what was deleted.
- **Output state.** After Enter, it hands the buffered characters back one per
  call, then returns `EOF` and resets for the next entry.

`fgets_keypad` is rebuilt on top of it, so the rest of the program reads keypad
input the way C reads a line from a file. `main` reads two numbers and echoes
each one back on the LCD.

## Hardware and toolchain

- NI myRIO with a keypad and a character LCD
- C, built with the NI myRIO Eclipse toolchain
- `MyRio.h` and `T1.h` come from the course's myRIO support package and are not
  included here, so these files build only inside that project template.

## Background

Labs for an embedded computing course, January and February 2025. Lab 1 extended
the same Lab 0 program, so it is not repeated here.
