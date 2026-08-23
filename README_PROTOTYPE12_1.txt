CMM GeoCam - Prototype 12.1
MP4 SEEK / DURATION FIX
================================

Problem
-------
A long recording can play from beginning to end, but:
- Android Gallery shows only a few seconds
- video editors also read only a few seconds
- forward / backward seeking does not work

Likely compatibility issue
--------------------------
Prototype 12 used:

    mediaRecorder.start(1000)

which asks MediaRecorder to deliver periodic blobs.

On some Android/browser MP4 implementations this can leave the final
file sequentially playable while Gallery/editor software cannot read
a correct duration / seek index.

Change
------
MP4 now uses:

    mediaRecorder.start()

No timeslice is supplied. The browser finalizes the MP4 when Stop is
pressed, producing one final recording Blob.

WEBM continues to use:

    mediaRecorder.start(1000)

The recording toast for MP4 should include:

    seek-safe finalize

Important for future long recording/cloud
-----------------------------------------
Do not solve cloud recording by concatenating tiny MP4 blobs again.

Use finalized rolling segments instead:

    record segment
    -> stop/finalize MP4
    -> upload
    -> start next segment

Each segment then has its own valid duration and seek metadata.

GitHub update
-------------
Replace:
- index.html
- service-worker.js

Test
----
Record a new 2-3 minute MP4 and confirm:
1. Gallery duration is correct
2. timeline can be scrubbed
3. editor reads the full duration
4. forward/backward works
