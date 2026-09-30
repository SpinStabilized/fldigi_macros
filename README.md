# fldigi_macros

A collection of macro definition files (`.mdf`) for [fldigi](http://www.w1hkj.com/),
the amateur radio digital modem application.

## Contents

- `contest/` - Macros for contest operating
- `routine/` - Macros for routine/general operating

## Usage

1. Download the `.mdf` file(s) you want to use.
2. In fldigi, go to `File > Macros > Open...`
3. Select the macro file and click **Open**.

Macro files are typically stored in the `$HOME/.fldigi/macros/` directory
(Linux/Mac) or `C:\Users\<username>\fldigi.files\macros\` (Windows).

## FLDigi Macro Notes

Some things I've discovered while developing macros by hand.

### Macro File Overwrite

If you work on your macros outside of `fldigi` and then make updates within
`fldigi`, `fldigi` will overwrite any extra comments or annotations in your
files, copying the macros themselves into a base template. Use caution.

### Idle Macro Behavior

For some reason, when I was new to RTTY, I thought some idle diddles at the end
of a transmission would be helpful but really, spaces are good. And, as it turns
out, FLDigi really doesn't like seeing the idle macro in a macro that doesn't
have a `<TX>` in it as well. It will cause the macro execution to lock up and
the transeiver to stay keyed up transmitting the idle pattern. For the longest
time I thought I had issues with the FSK setup through FLRig but it
was just these extra `<IDLE:n>` calls.

That being said, at least for RTTY, don't use the `<IDLE:n>` macro at all. If
you are working FSK RTTY through `flrig` like I am, you can set a number of
idle characters to be sent at the start of a transmission. If you are using
`fldigi` AFSK mode, in the TTY modem "Tx" configuration, you can set a number of
`LTRS` characters to transmit at the start of every transmission. This is a
nice clean way of handling those idle diddles, which are really just repeated
`LTRS` characters.

For more information on why diddles are good at the start of your RTTY macros:
[Diddles by W7AY](https://www.aa5au.com/rtty/diddles-by-w7ay/)

### Macro Tags Are Case Inseneitive, Except For One

Every macro tag is matched with a function that uppercases both sides before
comparing, so `<wx>`, `<Wx>`, and `<WX>` are all identical. The one exception:
`</EXEC>`, and this is specifically the closing tag, must be uppercase.
It's located with a plain, case-sensitive string search, not the same
case-insensitive matcher. `<EXEC>`ls`</exec>` opens fine and then never closes.

### A Newline Isn't Implicit

The newline must be explicit in your macros if you want to have a break. If a
macro body spans several physical lines in the file and you forget the `\n`
escape at the end of a line, fldigi doesn't insert a space or a line break by
default. It concatenates the two lines with nothing between them.

`...MYCALL` on one line and `de N1ABC` on the next, without `\n`, transmits as
`...MYCALLde N1ABC`.

### Only the last `\n` Counts

If a single physical line has more than one `\n` escape on it, only the final
escape becomes a real line break. Every earlier one is transmitted literally as
the two characters backslash-n. And anything typed after the last `\n` on that
line is dropped, never transmitted.

### A Failed `<MACROS:>` Regenerates The Default Macros

This is the one worth genuinely worrying about. If the path in `<MACROS:>` fails
to open (bad path, ~ not expanded, file moved), fldigi doesn't skip the tag
quietly. It falls back to regenerating fldigi's stock default macro set and
saving it to disk, overwriting your macros file. A single typo'd path in a macro
button can silently replace your real macro file with the factory defaults the
next time you trigger that macro.

### Empty Call Field And The `<LOG:>`/`<LNW:>` Macros

The underlying save function requires the Call field to be non-empty. If it's
blank, the function returns immediately: no log entry, no error. Any note text
you appended to the Notes field before that point still sticks around, so it can
look like the macro "half worked."

### Duplicate Macro Numbers

If you're working on a macro file by hand, this is an important one. A duplicate
macro number doesn't overwrite, it concatenates. If two `/$ N` blocks in the
same file use the same number, fldigi doesn't replace the first with the second.
It appends the second body onto the end of the first. The macro's label does get
replaced, so the button text changes, but the transmitted text becomes both
bodies run together. This is an easy mistake to make after copy-pasting a macro
block and forgetting to renumber it.

## License

See repository for license details.
