CMM GeoCam - Prototype 12.2
MULTI-CORRIDOR ROAD NETWORK
========================================

DATABASE
--------
STA_Tamer.csv
- Mainline validated alignment
- 14,558 reference points

Corridor_Tamer.csv
- RAMP / GATE / ACCESS runtime database
- 7,660 reference points
- 91 validated corridors

Total runtime reference points:
- approximately 22,218

NETWORK STATES
--------------
READY · MAINLINE
READY · RAMP
READY · GATE
READY · ACCESS

GPS LOW
OUTSIDE NETWORK
DATABASE ERROR

MATCHING LOGIC
--------------
1. Match GPS against STA_Tamer.csv.
2. Match GPS against Corridor_Tamer.csv using a spatial grid.
3. Select valid candidate within 45 m.
4. Keep the current network at junctions using route persistence.
5. Another network must be at least 7 m closer and remain dominant
   for 3 GPS fixes before switching.

This is intended to reduce flicker such as:
MAINLINE -> RAMP -> MAINLINE -> RAMP
at interchange overlap points.

WATERMARK
---------
MAINLINE:
STA 56+350
JALUR A
Segmen Bitung - Cikupa

JALUR A/B is intentionally larger than Prototype 12.1.

RAMP example:
BITUNG
RAMP B OFF
+175 m

GATE example:
CIKUPA
GATE PLAZA
ENTRANCE

ACCESS example:
SERANG TIMUR
ACCESS PETAK PAPAT
ENTRANCE

ROAD-FIRST layout remains compact.

VIDEO / CAMERA FEATURES PRESERVED
---------------------------------
- Landscape-only capture
- Gyro-EIS / native stabilization detection
- 1920x1080 ideal camera input for EIS overscan
- 1280x720 recording output
- requestVideoFrameCallback frame synchronization
- MP4/H.264 first
- MP4 seek-safe finalize from Prototype 12.1
- MMS logo burn-in
- local-only output

CAPTURE
-------
Capture is allowed on every READY state:
- READY · MAINLINE
- READY · RAMP
- READY · GATE
- READY · ACCESS

Filename context is captured when recording starts.

GITHUB UPDATE
-------------
For existing CMMGeoCam repository upload/replace:

1. index.html
2. service-worker.js
3. manifest.webmanifest
4. Corridor_Tamer.csv

Keep existing:
- STA_Tamer.csv
- logo-mms.png
- icon-192.png
- icon-512.png

IMPORTANT AFTER DEPLOY
----------------------
1. Wait for GitHub Pages deployment.
2. Fully close old PWA/browser tab.
3. Reopen.
4. If stale, clear site data / PWA cache and reopen.

FIELD TEST CHECKLIST
--------------------
Test a continuous route such as:

MAINLINE
-> RAMP
-> GATE
-> ACCESS
-> GATE
-> RAMP
-> MAINLINE

Confirm:
- recording does not stop during network changes
- watermark changes automatically
- network state does not flicker excessively near junctions
- mainline JALUR A/B is clearly readable
- RAMP watermark order is Location -> Ramp/Direction/Movement -> +distance
- OUTSIDE NETWORK only appears outside all registered road networks

DATA NOTE
---------
STA_Tamer.csv remains the validated mainline alignment reference.

Corridor_Tamer.csv is a supplementary operational corridor reference
generated from reviewed Google Routes geometry and sampled every 5 m.
