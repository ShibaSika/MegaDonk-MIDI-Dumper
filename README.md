# MegaDonk MIDI Dumper

**Download for Windows:** [MegaDonk-MIDI-Dumper-Windows.zip](https://github.com/ShibaSika/MegaDonk-MIDI-Dumper/releases/latest/download/MegaDonk-MIDI-Dumper-Windows.zip) (version 1.2). Unzip it anywhere and keep the folder together. Windows may say it protected your PC because the program is not signed: choose **More info**, then **Run anyway**.

Turns TabIt files (`.tbt`) into standard MIDI files, as many as you like at once. TabIt does not need to be
installed or opened: each `.tbt` is read directly into the MIDI TabIt itself would export (checked event for
event against TabIt's own exports), and every written file is read back and checked.

## Use

1. Drop `.tbt` files, or folders of them, on **MegaDonk MIDI Dumper.exe**. Folders are searched with their
   subfolders. Each song is written as `<name>.mid` into a **MegaDonk MIDI** folder next to it, with one
   **MegaDonk report.txt** per folder. A message says how it went.
2. Or start the exe without files for the window: add files or folders, pick how pitch bends are written,
   choose an output folder if you want one, and press **Dump to MIDI**.

Two songs with the same name never overwrite each other: the second gets its folder's name added, or a number.

## Pitch bends

- **Ready for any program** (the default): every bend of more than half a semitone is played inside the notes.
  The notes sounding there move by its whole semitones and the wheel keeps at most 50 cents, so a bend of
  whole semitones sounds right whatever bend range the instrument has. A note held through a bend is struck
  again there on its new pitch. Drum bends are clipped to 2 semitones, since a drum's note number picks the drum.
- **On the pitch wheel**: bends stay on the wheel for an instrument with the bend range you pick (2, 12, 24 or
  another). Steps wider than that go inside the notes.
- **Exactly as TabIt exports it**: bends for a 24-semitone range, declared with RPN 0. Most programs ignore that
  declaration and play these bends 1/12 as wide.

## Volume

In the first two modes, volume changes also reach the notes already ringing, as in TabIt's own playback. Each
track's volume as set in TabIt becomes its starting expression (CC11), and every volume change the expression
from there on. Both keep TabIt's own numbers, which run to 127 like a MIDI controller, so a volume of 96 plays at
96/127 of full loudness wherever it appears. The channel volume (CC7) is set to full, and the notes are struck at
the track volume. Untick the Volume option, or pass
`--tabit-volume`, to keep TabIt's own export: there a volume change only sets the loudness of the notes after it.

## Tempo and time signatures

In the first two modes, each tempo change keeps the old tempo up to the moment of the change. Programs that
draw a line between tempo points (Mixcraft's master tempo, for one) would otherwise slide the tempo towards
each change bar by bar. Bars that are not 4/4 in TabIt get their own time signature, where TabIt's export
declares 4/4 throughout, so a program's bar grid stays on TabIt's bar lines. **Exactly as TabIt exports it**
keeps TabIt's tempo events and 4/4 as they are.

If a program still slides into the tempo changes, untick **Tempo changes mid-song** (or pass
`--no-tempo-changes`). Only the starting tempo is then written. Each change is marked "Tempo N BPM" on the
timeline, and the report lists where they go (for example "bar 67, beat 1 to 95 BPM"), so you can add them
back in the program by hand.

## The report

Every line is PASS or FAIL. The written file is read back and compared with the TabIt song: the sounding pitch
at every moment, the notes, controllers, tempo and texts. A FAIL line means that file should not be trusted.

## Command line

    "MegaDonk MIDI Dumper.exe" [--mode any|wheel|tabit] [--range N] [--tabit-volume] [--out FOLDER] files or folders

Without files the window opens; `--gui` opens it with the files listed. Exit code 0 when every file passed,
1 when any failed, 2 when no `.tbt` was found.
