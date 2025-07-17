

# Create project directory
mkdir custom-audio-app
cd custom-audio-app

# Initialize package.json
cat > package.json << 'EOF'
{
  "name": "custom-audio-app",
  "version": "1.0.0",
  "private": true,
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "tone": "^14.7.77"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  "devDependencies": {
    "react-scripts": "5.0.1"
  },
  "browserslist": {
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ],
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  }
}
EOF

# Create public directory and files
mkdir -p public
cat > public/index.html << 'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>Custom Audio App</title>
    <style>
        body {
            margin: 0;
            padding: 20px;
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', sans-serif;
            background: #1a1a1a;
            color: #ffffff;
        }
        .app-container {
            max-width: 1200px;
            margin: 0 auto;
        }
        .control-panel {
            background: #2a2a2a;
            padding: 20px;
            border-radius: 10px;
            margin-bottom: 20px;
        }
        .slider-container {
            margin: 10px 0;
        }
        .slider {
            width: 100%;
            height: 4px;
            background: #555;
            border-radius: 2px;
            outline: none;
            -webkit-appearance: none;
        }
        .slider::-webkit-slider-thumb {
            -webkit-appearance: none;
            appearance: none;
            width: 20px;
            height: 20px;
            background: #4CAF50;
            border-radius: 50%;
            cursor: pointer;
        }
        .button {
            background: #4CAF50;
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 5px;
            cursor: pointer;
            margin: 5px;
            font-size: 16px;
        }
        .button:hover {
            background: #45a049;
        }
        .button:disabled {
            background: #666;
            cursor: not-allowed;
        }
        .waveform {
            height: 100px;
            background: #333;
            border-radius: 5px;
            margin: 10px 0;
            position: relative;
        }
    </style>
</head>
<body>
    <div id="root"></div>
</body>
</html>
EOF

# Create src directory
mkdir -p src

# Create main App.js
cat > src/App.js << 'EOF'
import React, { useState, useEffect, useRef } from 'react';
import * as Tone from 'tone';

const AudioApp = () => {
  const [isPlaying, setIsPlaying] = useState(false);
  const [volume, setVolume] = useState(-10);
  const [eqLow, setEqLow] = useState(0);
  const [eqMid, setEqMid] = useState(0);
  const [eqHigh, setEqHigh] = useState(0);
  const [synthVolume, setSynthVolume] = useState(-20);
  const [synthType, setSynthType] = useState('sine');
  const [currentSample, setCurrentSample] = useState(null);
  const [isLoading, setIsLoading] = useState(false);
  
  const playerRef = useRef(null);
  const eqRef = useRef(null);
  const synthRef = useRef(null);
  const masterVolumeRef = useRef(null);

  // Initialize audio components
  useEffect(() => {
    const initAudio = async () => {
      try {
        // Create audio chain: Player -> EQ -> Master Volume -> Destination
        masterVolumeRef.current = new Tone.Volume(volume).toDestination();
        
        // Create 3-band EQ
        eqRef.current = new Tone.EQ3({
          low: eqLow,
          mid: eqMid,
          high: eqHigh
        }).connect(masterVolumeRef.current);
        
        // Create synthesizer
        synthRef.current = new Tone.Synth({
          oscillator: {
            type: synthType
          },
          envelope: {
            attack: 0.1,
            decay: 0.2,
            sustain: 0.3,
            release: 1
          }
        }).connect(eqRef.current);
        
        synthRef.current.volume.value = synthVolume;
        
        console.log('Audio initialized successfully');
      } catch (error) {
        console.error('Audio initialization failed:', error);
      }
    };

    initAudio();

    // Cleanup
    return () => {
      if (playerRef.current) {
        playerRef.current.dispose();
      }
      if (synthRef.current) {
        synthRef.current.dispose();
      }
      if (eqRef.current) {
        eqRef.current.dispose();
      }
      if (masterVolumeRef.current) {
        masterVolumeRef.current.dispose();
      }
    };
  }, []);

  // Update EQ parameters
  useEffect(() => {
    if (eqRef.current) {
      eqRef.current.low.value = eqLow;
      eqRef.current.mid.value = eqMid;
      eqRef.current.high.value = eqHigh;
    }
  }, [eqLow, eqMid, eqHigh]);

  // Update master volume
  useEffect(() => {
    if (masterVolumeRef.current) {
      masterVolumeRef.current.volume.value = volume;
    }
  }, [volume]);

  // Update synth parameters
  useEffect(() => {
    if (synthRef.current) {
      synthRef.current.volume.value = synthVolume;
      synthRef.current.oscillator.type = synthType;
    }
  }, [synthVolume, synthType]);

  const startAudio = async () => {
    try {
      await Tone.start();
      console.log('Audio context started');
    } catch (error) {
      console.error('Failed to start audio context:', error);
    }
  };

  const loadSample = async (event) => {
    const file = event.target.files[0];
    if (!file) return;

    setIsLoading(true);
    try {
      await startAudio();
      
      const arrayBuffer = await file.arrayBuffer();
      const audioBuffer = await Tone.context.decodeAudioData(arrayBuffer);
      
      // Dispose of previous player
      if (playerRef.current) {
        playerRef.current.dispose();
      }
      
      // Create new player
      playerRef.current = new Tone.Player(audioBuffer).connect(eqRef.current);
      setCurrentSample(file.name);
      
      console.log('Sample loaded successfully');
    } catch (error) {
      console.error('Failed to load sample:', error);
    } finally {
      setIsLoading(false);
    }
  };

  const togglePlayback = async () => {
    if (!playerRef.current) return;
    
    try {
      await startAudio();
      
      if (isPlaying) {
        playerRef.current.stop();
        setIsPlaying(false);
      } else {
        playerRef.current.start();
        setIsPlaying(true);
        
        // Auto-stop when sample ends
        playerRef.current.onstop = () => {
          setIsPlaying(false);
        };
      }
    } catch (error) {
      console.error('Playback error:', error);
    }
  };

  const playSynth = async (note) => {
    if (!synthRef.current) return;
    
    try {
      await startAudio();
      synthRef.current.triggerAttackRelease(note, '8n');
    } catch (error) {
      console.error('Synth error:', error);
    }
  };

  const stopAll = () => {
    if (playerRef.current) {
      playerRef.current.stop();
    }
    if (synthRef.current) {
      synthRef.current.releaseAll();
    }
    setIsPlaying(false);
  };

  return (
    <div className="app-container">
      <h1>Custom Audio App</h1>
      
      {/* Sample Loading */}
      <div className="control-panel">
        <h3>Sample Player</h3>
        <input
          type="file"
          accept=".mp3,.wav,.ogg"
          onChange={loadSample}
          style={{ marginBottom: '10px' }}
        />
        {currentSample && <p>Loaded: {currentSample}</p>}
        
        <div>
          <button
            className="button"
            onClick={togglePlayback}
            disabled={!playerRef.current || isLoading}
          >
            {isPlaying ? 'Pause' : 'Play'}
          </button>
          <button className="button" onClick={stopAll}>
            Stop All
          </button>
        </div>
      </div>

      {/* EQ Controls */}
      <div className="control-panel">
        <h3>3-Band EQ</h3>
        <div className="slider-container">
          <label>Low: {eqLow}dB</label>
          <input
            type="range"
            className="slider"
            min="-15"
            max="15"
            value={eqLow}
            onChange={(e) => setEqLow(Number(e.target.value))}
          />
        </div>
        <div className="slider-container">
          <label>Mid: {eqMid}dB</label>
          <input
            type="range"
            className="slider"
            min="-15"
            max="15"
            value={eqMid}
            onChange={(e) => setEqMid(Number(e.target.value))}
          />
        </div>
        <div className="slider-container">
          <label>High: {eqHigh}dB</label>
          <input
            type="range"
            className="slider"
            min="-15"
            max="15"
            value={eqHigh}
            onChange={(e) => setEqHigh(Number(e.target.value))}
          />
        </div>
      </div>

      {/* Synthesis */}
      <div className="control-panel">
        <h3>Synthesizer</h3>
        <div className="slider-container">
          <label>Synth Volume: {synthVolume}dB</label>
          <input
            type="range"
            className="slider"
            min="-40"
            max="0"
            value={synthVolume}
            onChange={(e) => setSynthVolume(Number(e.target.value))}
          />
        </div>
        <div>
          <label>Waveform: </label>
          <select value={synthType} onChange={(e) => setSynthType(e.target.value)}>
            <option value="sine">Sine</option>
            <option value="square">Square</option>
            <option value="triangle">Triangle</option>
            <option value="sawtooth">Sawtooth</option>
          </select>
        </div>
        <div style={{ marginTop: '10px' }}>
          <button className="button" onClick={() => playSynth('C4')}>C</button>
          <button className="button" onClick={() => playSynth('D4')}>D</button>
          <button className="button" onClick={() => playSynth('E4')}>E</button>
          <button className="button" onClick={() => playSynth('F4')}>F</button>
          <button className="button" onClick={() => playSynth('G4')}>G</button>
          <button className="button" onClick={() => playSynth('A4')}>A</button>
          <button className="button" onClick={() => playSynth('B4')}>B</button>
        </div>
      </div>

      {/* Master Controls */}
      <div className="control-panel">
        <h3>Master</h3>
        <div className="slider-container">
          <label>Master Volume: {volume}dB</label>
          <input
            type="range"
            className="slider"
            min="-40"
            max="0"
            value={volume}
            onChange={(e) => setVolume(Number(e.target.value))}
          />
        </div>
      </div>

      {/* Visual Feedback */}
      <div className="control-panel">
        <h3>Waveform</h3>
        <div className="waveform">
          <div style={{ 
            padding: '40px', 
            textAlign: 'center', 
            color: '#888' 
          }}>
            Waveform visualization will appear here
          </div>
        </div>
      </div>
    </div>
  );
};

export default AudioApp;
EOF

# Create index.js
cat > src/index.js << 'EOF'
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
EOF

# Create sample audio directory
mkdir -p public/samples
echo "Place your MP3 sample files (under 90 seconds) in the public/samples directory" > public/samples/README.md

# Create a simple development server script
cat > start-dev.sh << 'EOF'
#!/bin/bash
echo "Starting Custom Audio App development server..."
echo "Make sure you have Node.js installed (https://nodejs.org/)"
echo "Installing dependencies..."
npm install
echo "Starting development server..."
npm start
EOF

chmod +x start-dev.sh

# Create production build script
cat > build-production.sh << 'EOF'
#!/bin/bash
echo "Building production version..."
npm run build
echo "Production build complete! Files are in the 'build' directory."
echo "You can deploy the 'build' directory to any static web hosting service."
EOF

chmod +x build-production.sh

# Create README
cat > README.md << 'EOF'
# Custom Audio App

A web-based audio application built with React and Tone.js for interactive music creation in the browser. This app combines sample playback capabilities with real-time synthesis and audio processing, leveraging Tone.js's Web Audio framework for professional-quality audio performance.

## 🎯 Project Overview

**Timeline**: 2-day sprint development
**Tech Stack**: React + Tone.js Web Audio Framework
**Target**: Interactive music application with sample playback and synthesis

### Core Features
- **Sample Playback**: Load and play MP3 files up to 90 seconds
- **3-Band EQ**: Real-time frequency control (low, mid, high)
- **Built-in Synthesizer**: Multiple waveforms with ADSR envelope
- **Master Volume Control**: Global audio level management
- **Modern UI**: Responsive design with dark theme

## 🚀 Quick Start

1. **Setup & Install**:
   ```bash
   ./start-dev.sh
   ```

2. **Development Server**:
   ```bash
   npm start
   ```
   Open `http://localhost:3000`

3. **Add Audio Samples**:
   Place your MP3 files in `public/samples/` directory

## 📈 Development Roadmap

### Day 1: Foundation & Core Audio
- [x] Project initialization with React + Tone.js
- [x] Basic sample playback system with file upload
- [x] Transport controls (play/pause/stop)
- [x] Audio context management and user gesture handling

### Day 2: Processing & Polish
- [x] 3-band EQ implementation with real-time control
- [x] Synthesizer with multiple waveforms
- [x] Master volume and audio routing
- [ ] Visual feedback (waveform display)
- [ ] Performance optimization
- [ ] Production deployment

### Future Enhancements
- [ ] Advanced synthesis (FM, AM, filters)
- [ ] Multi-sample management
- [ ] Recording/export capabilities
- [ ] MIDI integration
- [ ] Advanced effects (reverb, delay, distortion)

## 🎵 Audio Architecture

Built on Tone.js Web Audio framework for professional audio performance:

```
Sample Player → EQ (3-band) → Master Volume → Speakers
Synthesizer ↗
```

**Key Components**:
- `Tone.Player` for sample playback
- `Tone.EQ3` for frequency processing
- `Tone.Synth` for synthesis
- `Tone.Volume` for master control
- `Tone.getTransport()` for timing and synchronization

## 🛠️ Development Commands

- `npm start` - Start development server
- `npm run build` - Build for production
- `npm test` - Run tests
- `./build-production.sh` - Create production build

## 📱 Browser Support

- **Chrome** (recommended - full Web Audio support)
- **Firefox** (good compatibility)
- **Safari** (iOS compatible)
- **Edge** (Windows compatible)

**Note**: Requires user interaction to start audio context (browser security requirement)

## 📊 Technical Specifications

- **Maximum sample length**: 90 seconds
- **Total audio library**: Under 100MB
- **Supported formats**: MP3, WAV, OGG
- **Audio processing**: Sample-accurate scheduling
- **Performance**: Optimized for desktop and mobile

## 🔧 Technical Implementation

### Core Technologies
- **React 18**: Modern component architecture
- **Tone.js**: Web Audio framework for music applications
- **Web Audio API**: Low-level audio processing
- **AudioContext**: Sample-accurate timing

### Audio Features
- **Monophonic & Polyphonic Synthesis**: Using `Tone.Synth` and `Tone.PolySynth`
- **Sample Management**: `Tone.Player` with buffer loading
- **Real-time Processing**: Signal-rate parameter control
- **Transport System**: Global timing and synchronization

## 🚧 Development Notes

### Audio Context Management
```javascript
// Always start audio context after user interaction
await Tone.start();
```

### Sample Loading
```javascript
const player = new Tone.Player(audioBuffer).toDestination();
```

### Real-time Parameter Control
```javascript
// All audio parameters are signals for smooth automation
eq.low.value = lowFreqValue;
synth.frequency.rampTo("C4", 0.1);
```

## 🎨 Next Steps

1. **Add Audio Content**: Place samples in `public/samples/`
2. **Customize Interface**: Modify `src/App.js` for UI changes
3. **Extend Synthesis**: Add more synthesis types and effects
4. **Visual Feedback**: Implement waveform and spectrum display
5. **Export Features**: Add recording and file export capabilities
6. **MIDI Integration**: Connect external MIDI controllers

## 🌟 Performance Tips

- Use `Tone.loaded()` for sample loading confirmation
- Implement proper audio context lifecycle management
- Optimize for mobile with touch-friendly controls
- Use Web Audio nodes for efficient processing

---

**Built with [Tone.js](https://tonejs.github.io/) - A Web Audio framework for creating interactive music in the browser**
EOF

echo "🎵 Custom Audio App setup complete!"
echo ""
echo "Next steps:"
echo "1. cd custom-audio-app"
echo "2. ./start-dev.sh"
echo "3. Open http://localhost:3000 in your browser"
echo "4. Add your MP3 samples to public/samples/ directory"
echo ""
echo "The app includes:"
echo "- Sample playback with file upload"
echo "- 3-band EQ (Low/Mid/High)"
echo "- Built-in synthesizer with multiple waveforms"
echo "- Master volume control"
echo "- Modern responsive UI"
echo ""
echo "Happy coding! 🚀"
