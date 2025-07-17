# Custom Audio App 🎵

> A professional web-based audio application built with React and Tone.js for interactive music creation and sample manipulation

[![React](https://img.shields.io/badge/React-18.2.0-61DAFB?logo=react)](https://reactjs.org/)
[![Tone.js](https://img.shields.io/badge/Tone.js-14.7.77-FF6B6B?logo=javascript)](https://tonejs.github.io/)
[![Web Audio API](https://img.shields.io/badge/Web%20Audio%20API-Enabled-4CAF50)](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

## ✨ Features

- 🎼 **Sample Playback System** - Load and play MP3 files up to 90 seconds
- 🎛️ **3-Band EQ** - Real-time frequency control (Low/Mid/High)
- 🎹 **Built-in Synthesizer** - Multiple waveforms with ADSR envelope
- 🔊 **Master Volume Control** - Global audio level management
- 📱 **Responsive Design** - Works on desktop, tablet, and mobile
- ⚡ **Sample-Accurate Timing** - Professional-grade audio precision



## 🏗️ Built With

- **[React](https://reactjs.org/)** - Modern UI framework
- **[Tone.js](https://tonejs.github.io/)** - Web Audio framework for music applications
- **[Web Audio API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API)** - Low-level audio processing
- **Modern CSS** - Responsive design with dark theme

## 🎵 Audio Architecture

```
┌─────────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  Sample Player  │───▶│   3-Band    │───▶│   Master    │───▶│  Speakers   │
│   (Tone.Player) │    │EQ (Tone.EQ3)│    │Volume (Tone │    │             │
└─────────────────┘    └─────────────┘    │   .Volume)  │    └─────────────┘
                                          └─────────────┘
┌─────────────────┐                              ▲
│  Synthesizer    │──────────────────────────────┘
│  (Tone.Synth)   │
└─────────────────┘
```

## 🛠️ Development

### Available Scripts

```bash
npm start          # Start development server
npm run build      # Build for production
npm test           # Run test suite
npm run deploy     # Deploy to production
```

### Project Structure

```
src/
├── components/
│   ├── AudioEngine.js      # Core audio processing
│   ├── SamplePlayer.js     # Sample playback controls
│   ├── EQProcessor.js      # 3-band equalizer
│   ├── Synthesizer.js      # Synth engine
│   └── MasterControls.js   # Global controls
├── hooks/
│   ├── useAudioContext.js  # Audio context management
│   └── useToneEngine.js    # Tone.js integration
├── utils/
│   └── audioHelpers.js     # Audio utility functions
└── App.js                  # Main application
```

## 🎮 Usage

### Basic Sample Playback
1. Click "Load Sample" to upload an MP3 file
2. Use play/pause controls for playback
3. Adjust master volume as needed

### EQ Processing
- **Low**: Adjust bass frequencies (20Hz - 250Hz)
- **Mid**: Control midrange (250Hz - 4kHz)
- **High**: Modify treble (4kHz - 20kHz)

### Synthesizer
- Choose waveform type (sine, square, triangle, sawtooth)
- Play notes using the virtual keyboard
- Adjust synth volume independently

## 📊 Technical Specifications

| Feature | Specification |
|---------|---------------|
| **Sample Length** | Up to 90 seconds |
| **Audio Library Size** | Under 100MB total |
| **Supported Formats** | MP3, WAV, OGG |
| **Browser Support** | Chrome, Firefox, Safari, Edge |
| **Audio Latency** | <10ms (hardware dependent) |
| **Sample Rate** | 44.1kHz standard |

## 🌐 Browser Compatibility

| Browser | Support | Notes |
|---------|---------|-------|
| Chrome | ✅ Full | Recommended for development |
| Firefox | ✅ Full | Good performance |
| Safari | ✅ Full | iOS compatible |
| Edge | ✅ Full | Windows compatible |

**Note**: Requires user interaction to start audio context (browser security requirement)

## 📱 Mobile Support

- Touch-friendly controls
- Responsive layout
- Optimized for mobile browsers
- Works on iOS and Android

## 🔧 Configuration

### Audio Settings
```javascript
// Customize audio settings in src/config/audio.js
export const AUDIO_CONFIG = {
  sampleRate: 44100,
  bufferSize: 512,
  maxSampleLength: 90, // seconds
  maxLibrarySize: 100 // MB
};
```

### UI Customization
```javascript
// Modify theme in src/styles/theme.js
export const theme = {
  colors: {
    primary: '#4CAF50',
    background: '#1a1a1a',
    surface: '#2a2a2a'
  }
};
```

## 🚧 Roadmap

### ✅ Completed
- [x] Basic sample playback system
- [x] 3-band EQ implementation
- [x] Synthesizer with multiple waveforms
- [x] Master volume control
- [x] Responsive UI design

### 🔄 In Progress
- [ ] Waveform visualization
- [ ] Performance optimization
- [ ] Unit test coverage

### 🎯 Planned Features
- [ ] Advanced synthesis (FM, AM, filters)
- [ ] Multi-sample sequencer
- [ ] Recording/export capabilities
- [ ] MIDI controller support
- [ ] Cloud sample library
- [ ] Collaboration features

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guide](CONTRIBUTING.md) for details.

### Development Setup
1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Commit changes: `git commit -m 'Add amazing feature'`
4. Push to branch: `git push origin feature/amazing-feature`
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- **[Tone.js](https://tonejs.github.io/)** - Web Audio framework
- **[Web Audio API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API)** - Browser audio capabilities
- **[React](https://reactjs.org/)** - UI framework
- Audio samples from [Freesound.org](https://freesound.org/)


---

<div align="center">
  <strong>Built with ❤️ for the web audio community</strong>
</div>
