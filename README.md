> **Note:** The source code of this project is kept private. If you are a recruiter or a developer interested in reviewing it, feel free to contact me through my website: [mcampano.dev](https://mcampano.dev).

# MagicPitch App

Real-time pitch-detection piano app that displays a visual representation of detected pitch in a piano roll with customizable per-note colors. The main goal is to serve as a study and practice tool for soloist instruments, allowing musicians to visualize their performance or others' in real-time.

## Features

- **Real-time pitch detection** using TarsosDSP FFP_YIN algorithm with advanced filtering
- **Interactive piano roll** with 7 octaves (C1-C8) and multi-touch zoom/scroll capabilities
- **Customizable note colors** for each of the 12 chromatic notes with preset palettes
- **Pitch trace visualization** creating a continuous melody line with pause/scroll functionality
- **Integrated metronome** synchronized with the tempo grid
- **Configurable grid visibility** through Settings panel

## Technical Implementation

This project is built in **Kotlin** as a custom Android View (`PianoRollView`) and relies on [TarsosDSP](https://github.com/JorenSix/TarsosDSP) for real-time pitch detection.

### Tarsos Algorithm

Uses FFP_YIN algorithm with two filtering layers to eliminate false positives:

- Native "getProbability()" method filters low-confidence notes stored in "val probability: Float"
- Custom spike filter removes false positives when note changes exceed 10 semitones (using "semitoneDiff") and duration is under 900ms (using "durationMs")

These filters allow generous probability values while surgically targeting spikes without compromising accurate pitches.

### Piano Roll View

Custom Android view providing real-time pitch visualization with interactive piano roll interface:

- Real-time pitch plotting using "log2(pitchInHz / 440.0) * 12" conversion from "pitchInHz"
- Continuous melody line via "tracePoints" with horizontal scrolling when "isPaused" is true
- Color-coded note bands using "chromaPaints" array with customizable colors from SharedPreferences
- Multi-touch zoom (1x-5x) via "scaleFactor" and smooth scrolling through "scrollOffset"
- **Tempo-synchronized grid lines** ("drawTempoGrid") that visually align with the metronome and BPM set in TempoManager

### Tempo Manager

`TempoManager` controls the metronome and tempo synchronization:

- Converts BPM to time intervals ("secondsPerBeat = 60f / bpm")
- Manages beat playback through coroutines, with distinct accent and click sounds
- Provides timing data ("secondsPerBeat", "secondsPerBar") to "PianoRollView" for tempo-aligned grid rendering
- Enables both auditory and visual tempo synchronization for real-time rhythmic accuracy

### Note Converter

`NoteConverter.kt` contains the mathematical logic for converting detected frequencies into readable musical notes:

- "hzToNoteIndex(frequency: Float)" converts Hz to piano key index using A4 (440 Hz) as reference
- "hzToNote(frequency: Float)" returns note name, octave, and formatted frequency string

### Settings Activity

Configuration interface for visualization customization:

- Color customization system using "showColorSelectionDialog()" with predefined "fixedColors" palette
- Toggle for showing/hiding the tempo grid and its subdivisions
- Real-time color preview through class PianoOctaveView() , which synchronizes visual changes with the main piano roll
- Settings stored via SharedPreferences and automatically applied when returning to the main activity

## Requirements

- **Android SDK**: API level 21+ (Android 5.0)
- **Kotlin**: 1.8+
- **Dependencies**: TarsosDSP library for audio processing

## Dependencies

Add to `build.gradle`:
```gradle
implementation 'be.tarsos.dsp:core:2.4'
implementation 'androidx.core:core-ktx:1.9.0'
implementation 'com.google.android.material:material:1.8.0'
```

## Usage

1. Launch app and grant microphone permissions
2. Play instrument, audio file, or sing near device microphone
3. Observe pitch and tempo grid synchronized with the metronome
4. Customize note colors and toggle grid visibility in Settings
5. Use pinch-to-zoom and scroll to examine specific octaves
6. Pause to review pitch trace history with horizontal scrolling

### Testing Audio File

A saxophone solo audio file is included in `app/src/main/res/test-audio/` for testing pitch detection accuracy.  
Play this file near your device microphone to test the app functionality.

## License

All rights reserved.

## Project Notes

The code documentation and comments have been written in English following industry best practices for international software development and to demonstrate proficiency in technical English terminology.  
The latest version introduces a **metronome**, a **tempo management system** handled by `TempoManager`, and **tempo-synchronized grid lines** in `PianoRollView`.  
The app now provides both **visual and auditory rhythm guidance**, expanding its functionality from pitch visualization to a full practice environment.

