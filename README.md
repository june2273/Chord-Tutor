# Chord-Tutor

Browser drills for harmony and ear training: intervals, scales, key signatures and the circle of fifths. Answers come from a MIDI keyboard, the computer keyboard, or typed names.

## Problem

Music theory drills are usually on paper, and keyboard-based ear-training apps tend to check only pitch class. Writing intervals, scales and key signatures correctly depends on the **spelling**, though (C♯ is not D♭), and MIDI does not carry spelling. So an app that takes keyboard input has to handle enharmonics on purpose.

## Approach

- **Spelling from letters, not from MIDI.** Scales are built by stepping the tonic's letter up one degree at a time and writing the difference to the target pitch as an accidental. That way `D E F♯ G A B C♯` comes out right, and a pitch-class table could never produce it.
- **Key signatures indexed by fifths (−7…+7), not pitch class.** That keeps C♭/B, D♭/C♯ and G♭/F♯ as separate keys, which is the point the circle-of-fifths drill teaches.
- **Session is a plain state machine** with no React. Graders and question generators are strategies chosen by the stage, so adding a stage leaves the session untouched.
- **Input safety:** a single accept gate with a 200 ms lockout between questions, so a key held over from the last question can't answer the next one. The pause screen keeps mistake counts, so pressing ESC can't reset them.
- Notation is rendered with verovio. Audio is a sampled upright piano that falls back to a sine synth.

## Results

- 6 of the 16 catalog activities work: single-note ear training, intervals (read and build, compound intervals, bass clef), scales (major plus three minors, all 12 keys), key signatures, circle of fifths, and a free-play piano.
- 222 tests. Some of them pin hand-written tables to rules:
  - The textbook interval table is checked against computed intervals for all 92 diatonic pairs from C4 to C6.
  - All 48 scales (12 keys × 4 types) are checked to stay within double accidentals.
  - The 15-key signature table is re-derived from the two textbook rules (last sharp is the leading tone; the second-to-last flat is the tonic).

## Tech stack

- TypeScript, React 19, Vite, vitest
- verovio for notation, Web Audio (sampled piano with sine fallback), Web MIDI

## Run

```bash
cd web
npm install
npm run dev      # http://localhost:5173
npm test
```

Piano samples are optional. Without them playback is a sine synth:

```bash
python3 web/scripts/download_assets.py
python3 web/scripts/prepare_web_piano.py   # needs ffmpeg
```

MIDI input works in Chrome and Edge (Web MIDI). In Safari and Firefox the computer keyboard (`A W S E D F T G Y H U J K` = C4–C5) or the on-screen keyboard is used instead.

## Lessons learned

- Not built yet: chord ear training, diatonic chords, tensions, modes, EQ/loudness/pan ear training, and a rhythm/chord-play mode. Each one's requirements are listed in the app's catalog screen.
- No progress history is stored yet.
- **Lesson:** two bugs came from React closures reading stale input (an Enter key handler saw an empty answer). Submit decisions now go through a reducer. React StrictMode also exposed a double-created audio backend, which is now held in a ref.

## References

- verovio (LGPL-3.0) for notation; Upright Piano KW sample set (CC0)
- Design references (no code used): Analytic Piano, KDE Minuet, LenMus
- Studied with J. Nam's public course materials: CTP431 / GCT731.

Full sources and licenses: [citation.md](citation.md).

---
한국어 문서: [README.ko.md](README.ko.md)
