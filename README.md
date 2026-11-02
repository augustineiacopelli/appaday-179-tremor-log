# Tremor Log

AppADay Day 179. Category I (Interactive). No Claude API.

A seismograph for your phone. Lay it on a table, tap Start, and it listens to the motion sensors, detects every spike, and rates the shaking on a six level scale with peak hold. Every reading is translated into units that mean something: acceleration, seismology gals, Mercalli intensity, and a Richter style magnitude.

Live: https://augustineiacopelli.github.io/appaday-179-tremor-log/

Portfolio: https://augustineiacopelli.github.io/appaday/

## How it works

Tapping Start asks for motion access where the browser requires it, which is every iPhone, then runs a three second calibration. Keep the phone still on a firm surface while the bar fills. Calibration measures the sensor's own noise, sets a floor of the mean plus four standard deviations (never below 0.03 m/s2), and rejects the run if anything moved more than 0.5 m/s2. Recalibrate any time from the trace card.

If the phone reports gravity free acceleration, that is used directly. Otherwise the app takes the gravity included reading and subtracts a gravity vector that it learns only from quiet samples, so a spike can never leak into the estimate. The reading is the magnitude of that dynamic acceleration in m/s2.

## The spike detector

A short average (0.1 second) is compared with a long average (5 seconds) in the style of seismic STA over LTA triggering. A spike starts when the short average clears the trigger, which is the noise floor times the sensitivity setting (4 low, 2.5 normal, 1.5 high), and is at least three times the long average. It ends when the short average falls below 60 percent of the trigger for 400 ms. Each spike is logged with its time, peak, duration, level, and Mercalli intensity. The log keeps the latest 200 and survives reloads.

## The six level scale

The scale is tied to the Mercalli bands so each level means the same thing in the real world. Peak hold keeps the highest reading on screen for 3, 8, or 20 seconds, then lets it ease back to the live value. The segmented bar shows the live level filling and the peak level outlined.

Still is below the noise floor. Faint runs from the floor to 0.137 m/s2 (Mercalli I to III, a rumble through the floor). Light is up to 0.382 m/s2 (IV, a passing truck or a closing door). Moderate is up to 0.902 m/s2 (V, dishes rattle). Strong is up to 3.334 m/s2 (VI to VII, a stomp or a slammed door). Violent is anything above that (VIII and up, a drop or a hard impact).

## The translations

Peak acceleration is shown four ways. In m/s2 and in g, in gals (1 gal is 1 cm/s2, the unit seismologists use), and as a Mercalli intensity from a peak ground acceleration table in the style of Wald and colleagues (1999), with its plain word label from Not felt to Extreme.

The Richter style figure is the honest part to read carefully. A phone on a table cannot measure true Richter magnitude, which describes the energy released at an earthquake's source. What the app can do is invert the Joyner and Boore (1981) attenuation relation, log10 y = -1.02 + 0.249 M - log10 r - 0.00255 r, where y is peak acceleration in g and r is the square root of distance squared plus 7.3 squared in km. That gives the magnitude of quake that would shake the ground this hard at 10, 25, 50, or 100 km, your choice in Settings. The relation was fitted for magnitudes 5 and up, so smaller answers are marked as extrapolated estimates. Use it for a feel of scale, not as a measurement.

## The trace

A canvas draws the last 20 seconds of peak acceleration per pixel column on a log scale from 0.003 to 30 m/s2, colored by level, with the level thresholds drawn as lines and the held peak as a dashed line.

## Exports

Save PNG makes a 1080 by 1350 report card with the session peak, its translations, the trace, and the last seven events. On phones it opens the share sheet, and on desktop it downloads. Events CSV lists every logged spike with level, m/s2, g, gal, Mercalli, the Richter style value at the chosen distance, and duration. Trace CSV gives the last 60 seconds at 20 samples per second.

## Other details

The screen wake lock is held while listening and re-requested when the tab returns to the foreground. Try demo signal feeds synthetic taps through the same pipeline, which is handy on a desktop with no sensors. If no motion data arrives within about two seconds, the app stops and says why. Settings and the event log are stored in one localStorage key, appaday179, and the app still runs if storage is blocked. It is a single file of inline CSS and JS with no dependencies, designed for a 375 pixel phone with 44 pixel touch targets.

If you run it from the home screen on iPhone, test the PNG and CSV buttons there too, since downloads from standalone web apps can behave differently from Safari.
