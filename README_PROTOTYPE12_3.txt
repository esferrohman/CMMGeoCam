CMM GeoCam - Prototype 12.3
UNIFIED GRID + ROLLING FINALIZED VIDEO
======================================

CHANGE 1 - UNIFIED SPATIAL GRID
-------------------------------
Prototype 12.2:
- MAINLINE used a binary nearest-point search
- RAMP/GATE/ACCESS used a spatial grid

Prototype 12.3:
- MAINLINE and CORRIDOR use the SAME nearestFromGrid() method
- binary-search / unimodal-distance assumption has been removed
- both datasets use NETWORK_GRID_SIZE = 0.0015
- both search neighboring 3x3 cells
- a full scan exists only as a safety fallback

Reason:
At interchanges the mainline can curve near itself and near corridor
geometry. A robust mainline distance is important because the network
hysteresis compares MAINLINE vs RAMP/GATE/ACCESS distances.

CHANGE 2 - ROLLING FINALIZED RECORDING
--------------------------------------
The user still sees one continuous REC session.

Internally:
Segment 001 -> max 2 minutes -> STOP/finalize -> download
Segment 002 -> max 2 minutes -> STOP/finalize -> download
Segment 003 -> ...

The camera stream and compositing loop remain active between segments.
Only MediaRecorder is rotated.

Why:
- bounds long-recording memory use
- every MP4 receives its own stop/finalization
- avoids returning to MP4 timeslice concatenation
- better preparation for future direct cloud upload

Filename example:
CMMGeoCam_VIDEO_SEG001_STA_56-350_TO_BITUNG_RAMP_B_OFF_20260823_210000.mp4

The start and end context can differ if a segment crosses network types.

IMPORTANT ANDROID NOTE
----------------------
Some browsers may ask permission for multiple automatic downloads.
Allow multiple downloads for the CMM GeoCam GitHub Pages site if Android
shows that prompt.

SEEK FIX STATUS
---------------
The rolling design is safer, but actual MP4 seekability still needs
a real-device output test.

Recommended:
1. Record 2-4 minutes.
2. Let at least one automatic segment boundary occur.
3. Check EACH generated MP4 in Gallery:
   - duration
   - seek forward/back
   - editor compatibility
4. Keep one original output file for technical inspection if needed.

PRESERVED
---------
- Multi-Corridor matching
- network hysteresis
- MAINLINE/RAMP/GATE/ACCESS watermark
- large JALUR A/B text
- Gyro-EIS
- strict landscape
- 1280x720 output
- H.264/MP4-first
- road-first watermark
- MMS logo
- local-only recording

GITHUB UPDATE
-------------
Replace:
- index.html
- service-worker.js

Other Prototype 12.2 files remain valid:
- Corridor_Tamer.csv
- STA_Tamer.csv
- manifest.webmanifest
- icons
- logo
