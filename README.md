# Acoustic-radar-using-wind-turbines
Audio radar through wind turbines and their webcams

"The image is for illustrative purposes only. The appearance of the actual product may differ from the image."
<img width="1168" height="784" alt="image" src="https://github.com/user-attachments/assets/882ec7cd-e883-4789-a210-e722feca1108" />

Real Image fo Code:
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0d0574fa-b5b1-48a3-84e5-7ce54ba62753" />

Wind Turbine Doppler Radar & RPM Monitor
Overview

This project is a real-time 3D Radar and Spectrum Analyzer designed to visualize the acoustic signature of a wind turbine. It captures live audio from a YouTube stream, analyzes the frequencies, and generates a 3D radar visualization with obstacle detection and accurate RPM estimation.
Key Features

     3D Radar Visualization: Built with Three.js, featuring a rotating sweep line and dynamic obstacle spawning.
     Real-time Spectrum Analyzer: 2D frequency bar graph synchronized with the audio input.
     Acoustic RPM Estimation: Calculates the rotational speed (0-21 RPM) based on low-frequency "thump" analysis.
     Manual Gain Control: Bypasses browser Automatic Gain Control (AGC) to prevent audio saturation and dropouts.
     Live Stream Integration: Embeds a specific wind turbine YouTube live feed for real-time monitoring.

🚀 Deployment Instructions

IMPORTANT: Due to YouTube Content Security Policies (Error 153), this application cannot be run by simply opening an HTML file on your local machine (file:// protocol). The browser blocks the video embed on local origins.

To run this project:

    Navigate to the W3Schools HTML Editor.
    https://www.w3schools.com/html/html_editor.asp
    Delete all existing code in the editor.
    Paste the full content of index.html from this repository.
    Click the green "Run" button.
    In the right-hand panel ("Result"), click "1. ACTIVATE SENSORS (MIC)" and allow microphone permissions.

Why W3Schools?
The W3Schools editor acts as a secure proxy/host. YouTube allows embedding on w3schools.com, allowing the video to play correctly while the JavaScript runs locally in your browser context.
🛠 Technical Architecture

The application runs entirely in the browser using standard Web APIs. It consists of three main modules:

    Audio Pipeline: Microphone capture via getUserMedia.
    Signal Processing: Frequency analysis via AudioContext (Web Audio API).
    Visualization: 3D rendering via Three.js and 2D rendering via HTML5 Canvas.

Audio Workaround (CORS)

Direct audio extraction from the YouTube iframe is blocked by Cross-Origin Resource Sharing (CORS) policies. To solve this, the system uses a "Loopback" method:

    The YouTube video plays audio through the user's speakers.
    The system requests access to the user's Microphone.
    The microphone "hears" the speakers, allowing the AudioContext to analyze the stream in real-time.

📊 Algorithms Used
1. Fast Fourier Transform (FFT)

The core of the analysis is the FFT, which converts the raw audio waveform (time domain) into a frequency spectrum (frequency domain).

     Configuration: fftSize = 512.
     Implementation: analyser.getByteFrequencyData(dataArray).
     Output: An array where each index represents a frequency band (0 Hz to ~22kHz), and the value (0-255) represents the amplitude (loudness).

2. Energy Calculation & Thresholding

To determine the overall volume and trigger radar "blips" (obstacles):

    Calculate the Average Energy of the spectrum.
    javascript
     
      

     
     
    avgVolume = Sum(dataArray) / dataArray.length;
     
     
    Compare against a Dynamic Threshold:
         If avgVolume > sensitivityThreshold: Spawn a 3D obstacle (Red Sphere) at a random polar coordinate.
         The probability of spawning is dampened (Math.random() > 0.8) to prevent visual clutter.

3. RPM Estimation Algorithm

Wind turbines generate a rhythmic low-frequency "thump" (the Doppler effect of the blades passing the tower).

    Low-Pass Filtering: We isolate the first 10-12 frequency bins (approx. 0Hz - 400Hz), which contain the fundamental rotation frequency.
    javascript
     
      

 
 
bassSum = Sum(dataArray[0...12]);
bassEnergy = bassSum / 12;
 
 
Proportional Mapping: The RPM is calculated proportionally to the bass energy, rather than detecting individual beats (which can be error-prone).
javascript
 
  
 
 
targetRPM = (bassEnergy * Multiplier * Constant);
 
 
Smoothing: A linear interpolation (Lerp) is applied to the RPM value to simulate physical inertia and prevent jittery numbers.
javascript
 
  

     
     
    currentRPM = currentRPM + (targetRPM - currentRPM) * 0.1;
     
     
    Calibration: The output is clamped to a realistic maximum of 21.0 RPM.

4. Gain Stabilization (Anti-AGC)

Browsers often apply Automatic Gain Control (AGC), which lowers the microphone volume if the input is too loud (e.g., strong wind), causing the input to drop to near-zero after a few seconds.

     Fix: We disable AGC in the getUserMedia constraints:
    javascript
     
      

     
     
    audio: { autoGainControl: false, echoCancellation: false, noiseSuppression: false }
     
     
     Manual Gain Node: A GainNode is inserted into the audio graph to allow the user to manually boost the signal if the physical microphone is too far from the speakers.

📦 Dependencies

     Three.js (r128): Used for all 3D rendering (Grid, Sweep Line, Obstacles).
         CDN: https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js
     HTML5 Canvas: Used for the 2D Spectrum Analyzer.
     Web Audio API: Native browser API for audio processing.

🎮 Usage Guide

Once running in the browser:

    Activate Sensors: Click the button to start the microphone.
    Adjust Sensitivity:
         Mic Gain: Use the slider in the left panel if the "Input Level" is stuck at 0 or if you see a "SIGNAL CLIPPED" warning.
         RPM Boost: Use the slider in the right panel if the RPM reading is too low compared to the video reality.
    Visualization:
         Bottom Panel: Shows the live frequency spectrum (Green/Purple bars).
         Main View: Shows the 3D radar. Red spheres appear when loud sounds (cars, strong wind gusts) are detected.
         Top Right: Shows the calculated Turbine RPM with a decimal precision.

📝 License

This project is an educational demonstration of Web Audio API and Three.js capabilities.
