# ChoralRoot+ integration: KEY scales and live drums

Status: design ready for implementation; no firmware or emulator build yet.
Base: Quixotic7/ChoralRootFM1 (GPL-3.0-only). Keep upstream copyright, licensing and attribution.

## Design principles
- Preserve the existing ChoralRoot 0.27 chord block, root keys, KEY/BASS/PERF/FX/LOOP workflow.
- Tap retains the existing action; hold opens the existing layer.
- No new top-level button or long menus.
- Drum performance is an explicitly entered submode so playing roots never accidentally triggers drums.
- Preserve OCT- + OCT+ panic and OCT- / HOME navigation.

## KEY layer: scales
- KEY tap continues to toggle Key Mode.
- KEY hold opens its existing layer.
- Add a SCALE page alongside existing root/key settings; SELECT moves pages, KNOB 1 selects root, KNOB 2 scale, KNOB 3 chord harmonization (diatonic triad / seventh / original chord shape), KNOB 4 strict scale off/on.
- Include Ionian/Major, Dorian, Phrygian, Lydian, Mixolydian, Aeolian/Natural Minor, Locrian, Harmonic Minor, Melodic Minor (ascending form), Major/Minor Pentatonic, Blues, Whole Tone, Diminished, Phrygian Dominant, Hungarian Minor.
- When harmonization is enabled, derive chord tones by stacking scale degrees; existing MAJ/MIN and extensions remain available in original chord-shape mode. Do not silently replace explicitly held chords.
- Persist settings with global save and expose them in the emulator.

## DRUM layer: use existing controls
- Add DRUMS as a page in the existing PERF layer, reached by holding PERF and turning SELECT to DRUMS; do not repurpose the regular PERF tap.
- DRUMS page has four knob cells: STYLE, COMPLEXITY, SWING, LEVEL.
- On DRUMS page, tap SEQ to enter/exit DRUM LIVE (only while PERF layer is open); show prominent DRUM LIVE label.
- In DRUM LIVE, first eight root keys (C4 through G4, counting chromatic keys) control:
  C4 kick mute, C#4 snare mute, D4 hats mute, D#4 percussion mute,
  E4 A/B variation, F4 short fill, F#4 break, G4 start/stop.
- Holding F4 repeats a fill until released; tapping queues a fill for the final beat of the current bar.
- Holding F#4 and tapping F4 queues a one-bar bridge/build, then restores the prior groove.
- Holding E4 and tapping F4 selects a stronger fill; release E4 leaves selected variation unchanged.
- Muting occurs on the next sixteenth or beat (configurable); variations at the next bar.
- OCT- leaves DRUM LIVE without stopping accompaniment; HOME returns to normal chord view.
- Drum live mode consumes the mapped keys only; outside the mode every key remains a chord/root key.
- All mappings appear on the 240x240 screen as a 2x4 labeled grid; never require memorizing combos.

## Auto drums
- Styles: Ambient, Lo-fi, House, Techno, Breakbeat, DnB, Minimal.
- Four voices: kick, snare/clap, closed/open hats, percussion; use Felucca eng_drum/drum_voice as the DSP starting point.
- Clock follows existing ChoralRoot internal/external MIDI clock. Auto start is opt-in, first chord starts the transport at next bar boundary; auto stop is optional.
- Fill timing: beat/bar-quantized; bridge 1 or 2 bars; fill and bridge do not overwrite the underlying pattern.
- Keep independent volume and CPU/voice budget; drum DSP must not steal sustained chord notes unpredictably.
- Add on-screen metering for each drum part, mute states, scene, bar and fill queue.
- Project save includes style, variation, mutes, fill defaults, swing and auto-start preferences.

## Verification gates
1. Fork upstream into a distinct ChoralRoot+ repo or branch before touching firmware.
2. Unit tests: 16 scale masks, harmonized chords, no stuck notes on mode switches.
3. Sequencer tests: start/stop, clock drift, mute quantization, hold+tap fills, bridge restoration.
4. Emulation: audio, button combos, save/restore and 240x240 labels.
5. FM-1 hardware testing: CPU usage, audio underruns, recovery and install compatibility.
