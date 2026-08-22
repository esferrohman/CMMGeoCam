CMM GeoCam - Prototype 12
GYRO EIS / GITHUB PWA
==============================

STATUS
------
Experimental field-test build for camera stabilization.
Deployment remains GitHub Pages / PWA.

PRIORITY
--------
Native camera stabilization if exposed and accepted
        ↓
Gyroscope / DeviceMotion stabilization
        ↓
Stabilizer OFF, camera continues normally

CAMERA
------
Requested input:
- ideal 1920 × 1080
- ideal/max 30 fps

Recorded output:
- 1280 × 720
- real-frame requestVideoFrameCallback pipeline
- MediaRecorder bitrate request remains 4 Mbps
- MP4/H.264 first, WEBM fallback

Why request 1080p?
Not to produce a 1080p file.
The extra source resolution is used as overscan workspace for EIS.

GYRO EIS
--------
- target gyro request: 100 Hz
- actual gyro rate is measured and displayed
- high-frequency rotational vibration is separated from slow pan
- source crop shifts according to the filtered gyro signal
- watermark is rendered after camera-frame stabilization
- no optical-flow / frame-image analysis is used

Gyro-EIS only activates when actual camera input has enough overscan.
Current threshold:
- at least ~115% of 1280 × 720

Current EIS crop ratio:
- 0.84 of the available 16:9 source region

DIAGNOSTIC
----------
Tap:
STAB: ...

The panel shows:
- Camera Input
- Camera FPS
- Gyroscope availability/source
- Gyro Rate
- Native EIS
- Output
- Active Stabilizer

For A/B comparison:
1. Tap STAB once -> opens diagnostics.
2. Tap STAB again -> closes panel and toggles software stabilizer.
3. Record one clip with STAB GYRO and one with STAB OFF.

LIMITATIONS
-----------
- Browser gyro timestamps are not synchronized at camera-HAL level.
- This is lightweight browser EIS, not phone-vendor hardware EIS.
- It corrects small rotational shake, not full rolling-shutter "jelly".
- Mounting quality still matters.
- Live <video> preview remains raw; stabilization is applied in the
  canvas used for photo/video output.

GITHUB UPDATE
-------------
For the existing CMMGeoCam repository, replace only:
- index.html
- service-worker.js

Then:
1. Commit.
2. Wait for GitHub Pages deployment.
3. Fully close the old PWA/tab.
4. Open again.
5. Tap STAB and record the diagnostic values before the field test.

No need to replace:
- STA_Tamer.csv
- logo-mms.png
- manifest.webmanifest
- icons
