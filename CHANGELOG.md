## 26A09 beta

### Changelog

#### MDX-DERIVED FEATURES AND FIXES

Credit to [MDX](https://github.com/jmamma/MDX) (~X.14) for the following features and fixes, which are included in this firmware:

- New machines: TRX-S2, GND-SW and GND-PU, with improvements to GND-SN, including additional oscillators, relative tuning and UNISON
- Per-track DEFAULT and TONAL tuning
- New LFO shapes: REV-LINEAR, REV-EXP, SINE, NOTCH and STEP
- Trig preview
- SDS-receive UI lockup fix
- External MIDI-trigger phase/polarity fix
- SONG row-0 repeat fix for MIDI Start
- Incoming Note/CC processing and scheduling improvements
- Long-running CC freeze fix
- High-load MIDI byte retention
- External-clock timer re-arming safeguards
- Unsaved-workspace request/receive SysEx commands

#### MACHINES / TONAL

- Added note-name display for pitch related params while TONAL is active, including quarter-sharp and three-quarter-sharp symbols
- Added relative interval display for PTC2 / PTC3 / PTC4 on relevant machines: includes `Q` for quarter tones, and `OFF`
- Moved GND-NS DEC from parameter slot 1 to parameter slot 2 (encoder B) to match convention of other machines
  - Existing values, parameter locks, LFO destinations and explicit CTR-8P destinations are migrated automatically on startup or snapshot load
  - Stock-format SysEx retains the original parameter layout, with automatic conversion during send and receive
- Rearranged EDIT KIT panel to accommodate TONAL, shortened the machine summary, and adjusted navigation and the TRIG / MUTE group display to fit the revised layout

#### LFO

- Added per-track LFO speed division: 1, 1/2, 1/4, 1/8, 1/16, 1/32, 1/64 and 1/128
- Hold FUNC + YES and turn SPEED in the LFO editor to change the division. The SPEED label shows the selected division

#### LEVEL DISPLAY

- Added a temporary numeric LEV display when turning or pressing LEV
- LEV is displayed persistently in SONG mode and follows the selected track

#### TRACK / PATTERN SETUP

- Replaced SCALE SETUP with TRACK SETUP while GRID RECORD or LIVE RECORD is active, and PTTRN SETUP when recording is off
- Added independent track lengths and track speeds of 1/8x, 1/4x, 1/2x in addition to the stock 3/4x, 1x, 3/2x and 2x
- Added WRAP AFTER to control when the pattern's tracks restart together. Options are 2–1024 steps, INF, GLOBAL, LONGEST and SHORTEST. INF allows tracks to continue looping independently
- Added CHAIN AFTER to control when a queued pattern change takes place. Options are 2–64 steps, GLOBAL, LONGEST and SHORTEST
- LONGEST / SHORTEST follows the track with the longest / shortest duration, taking both length and speed into account
- Added GLOBAL > SYNC > PATTERN SETUP, with separate WRAP AFTER and CHAIN AFTER defaults for patterns set to GLOBAL
- Added independent LONG/SHORT MODE settings for WRAP and CHAIN: ALL TRKS, IGNORE MUTED
- Added TRACK CONDITION to TRACK SETUP, setting the default trig condition for the selected track
- Added LEV-push acceleration and FUNC + LEV adjustment in 16-step increments for numeric WRAP / CHAIN values, including their GLOBAL defaults
- Pressing REC switches between PTTRN SETUP and TRACK SETUP
- Added setup-specific copy, paste, clear and undo:
  - FUNC + COPY/PASTE/CLEAR while PTTRN SETUP is open affects the PTTRN SETUP params
  - PAGE + COPY/PASTE/CLEAR while TRACK SETUP is open affects the selected track's TRACK SETUP params
  - PAGE + COPY/PASTE/CLEAR while PTTRN SETUP is open affects the TRACK SETUP params of all sixteen tracks
    - Copying a single TRACK SETUP and then pasting on PTTRN SETUP applies it to every track

#### TRIG CONDITIONS

- Added per-trig conditions. Hold a populated TRIG and turn LEV to select a condition; TRIG + click LEV or TRIG + FUNC + CLEAR to return it to the TRACK CONDITION default
- Added these condition values:
  - L1: every loop
  - L2 / L3 / L4: the last loop of each two-, three- or four-loop group
  - !L2 / !L3 / !L4: the first loop of each two-, three- or four-loop group
  - 1S: the first loop only
  - !1S: every loop after the first
  - P1 / P2 / P5 / P7 / P9: 10%, 20%, 50%, 70% or 90% probability
  - xP1 / xP2 / xP5 / xP7 / xP9: the same probabilities, but a failed condition still applies parameter locks and slides without retriggering the sound or sending a MIDI note
- Conditions that differ from TRACK CONDITION are shown in an inverted box (like a param lock). Changing the track default through the value of an existing explicit condition retains that condition for subsequent default changes
- Up to 64 explicit trig conditions can be stored per pattern

#### MICROTIMING

- Added per-trig microtiming. While recording is enabled, hold a TRIG and press LEFT or RIGHT to open its timing popup; subsequent presses move the trig earlier or later
- Timing ranges from -7 to +7 in increments of one eighth of the track's local step duration
- Press LEFT + RIGHT to reset a trig to inherited timing
- Inherited timing follows the original SWING setting. Explicit timing overrides SWING for that trig, including explicit zero for a straight, unswung trig.
  - Microtimed values that differ from SWING are shown in an inverted box, like a param lock
- Up to 64 explicit microtiming values can be stored per pattern

#### PATTERN MANAGEMENT

- Added shortcuts to copy, paste, clear and undo patterns other than the one currently loaded: BANK A-H + FUNC + TRIG + COPY/PASTE/CLEAR/UNDO
- Added a persistent `MANAGE BANK A` / `MANAGE BANK B` etc. message while BANK + FUNC is held
- Added closing of the temporary PATTERN bank window by pressing the same BANK button again in addition to stock EXIT/NO
- Added CLONE PATTERN (BANK + FUNC + YES), which copies the current pattern into the next adjacent pattern slot and saves a copy of the live kit into a destination kit slot. The adjacent pattern slot is then queued
  - The destination is the next numbered pattern, including bank boundaries and H16 → A01. An occupied destination pattern is replaced
  - Kit allocation prefers an empty kit, then an unassigned kit
- Added automatic naming of the cloned kit. A trailing number is incremented, preserving leading zeroes where possible. Otherwise, `2` is appended without a space: `TEST` → `TEST2`. If the name is full, its last character is replaced with `2`
- Added 'empty pattern insertion' (BANK + FUNC + LEFT / RIGHT). This moves the neighbouring occupied block towards an available gap to make room on that side of the current pattern, carrying complete pattern data and updating SONG references and pending pattern targets. It does not wrap past A01 or H16
- Sequencer-page COPY/PASTE/CLEAR/UNDO while SWING, SLIDE or ACCENT is open now affects only that trig type and uses the corresponding message, such as `COPY SLIDE PAGE` or `COPY SWING PAGE`

#### KIT / PARAMETER EDITING

- Added track swapping in EDIT KIT with FUNC + LEFT / RIGHT, including wrapping between tracks 1 and 16
  - Swaps the machine, parameters, LFO relationships, TONAL state, routing, trig/mute relationships and live mute state
  - Updates the corresponding tracks in every pattern using the kit, including locks, timing, conditions, microtiming and page history
  - Updates SONG mute masks referring to those patterns
  - This operation saves and reloads the kit
- Added copying, pasting and initialising the eight parameters on the current SYNTHESIS, EFFECTS or ROUTING page with the parameter PAGE button + COPY/PASTE/CLEAR
  - SYNTHESIS page pasting supports same machine or compatible ROM machines
- Added parameter PAGE + YES to randomise the current page's eight parameters, and parameter PAGE + NO to move each parameter independently by 4–8 steps up or down
- Added FUNC + COPY/PASTE/CLEAR in MASTER FX to copy, paste or initialise all 32 REV, ECHO, EQ and DYN parameters
- Added parameter PAGE + COPY/PASTE/CLEAR in MASTER FX to affect only the visible eight-parameter tab. Repeating the matching CLEAR or PASTE on the same kit and scope undoes it
- Added parameter PAGE + YES / NO randomise and small-change actions for the visible MASTER FX tab
- Added FUNC + NO backspace in name editors
- Added FUNC + CLEAR to clear the whole name in name editors

#### ROUTING / INPUT MONITORING

- Added per-track output routing. Hold FUNC + YES and turn VOL on the ROUTING page to select GLOBAL, MAIN, or individual output A–F
- The VOL label shows the routing selection
- GLOBAL follows the selected GLOBAL's output map
- Added GLOBAL > ROUTING > INPUT menu, containing A+B LEVEL for direct monitoring of input A/B through output A/B without assigning an INP machine

#### MONOMACHINE SONG LINK

- Added synchronised external SONG following from a Monomachine running supported firmware
- A single checked SysEx command carries the destination pattern, row position and target MIDI-clock boundary, allowing pattern and offset changes to apply together during playback or on Start / stopped Continue
  - The MD must stay in PATTERN mode and follow the Monomachine's clock and transport
  - Make sure to set the MD's PRG CHANGE IN to OFF so ordinary Program Changes do not compete with the song-link command

#### STORAGE / SYSEX

- Increased kit capacity from 64 to 128
- Reduced song capacity from 32 to 12 to provide storage for the additional kits and extended pattern data. **Back up or move songs from those slots before installing.** Existing snapshots on +Drive are not rewritten until saved
- Added additional SysEx messages for all custom data backups. Stock data retains original versioning.
  - SYSEX SEND, SYSEX RECV and direct SysEx dump requests all support extended data messages
  - Loading stock messages reset absent custom fields to their defaults

#### UW SAMPLE TRANSFERS

- Added lossless compressed sample backups through SAMPLE MGR > SEND and SEND ALL
- Added GLOBAL > FILE > +DRIVE SETTINGS > COMPRESS UW SEND. ON sends compressed backups; OFF sends ordinary SDS
- SAMPLE MGR > RECV accepts both compressed backups and normal SDS regardless of the send setting
- Added an overall SEND ALL progress bar alongside the current-sample progress display

#### SYSTEM / DISPLAY

- Replaced the animated startup splash with a static display and project date/channel versioning
- Added an exception screen showing device/build identity, current pattern and kit, sequencer state, exception details, program counter, stack pointer and CPU registers
