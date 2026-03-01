# ForgeAI v2.0 - Gesture-Based 3D Sculpting Mode

## New Feature: ✋ Gesture Sculpting

A cutting-edge hand gesture recognition system that lets you sculpt and manipulate 3D models in real-time using your webcam.

### How It Works

#### 1. **Hand Detection**
- Uses **MediaPipe Hands** library for real-time hand keypoint detection
- Tracks 21 key points per hand with high precision
- Works with standard webcams (no special hardware required)

#### 2. **Three Gesture Types**

##### 👉 **Pointing Gesture**
- **Detection**: Index finger extended, other fingers curled
- **Action**: Creates holes in the 3D model
- **Use Case**: Carve details, create indentations
- **Visual Feedback**: Red glowing sphere at impact point

##### 🤌 **Pinching Gesture**
- **Detection**: Thumb and index finger touching (distance < 0.05)
- **Action**: Pulls and deforms the mesh
- **Use Case**: Stretch, squash, sculpt organic shapes
- **Strength**: Pinch strength (0-100%) determines deformation intensity
- **Visual Feedback**: Real-time scale adjustments

##### ✋ **Flat Hand Gesture**
- **Detection**: All fingers extended, palm open and facing camera
- **Action**: Rotate the model left/right
- **Use Case**: View model from different angles
- **Control**: Swipe left/right to rotate (threshold: 15% screen width)
- **Visual Feedback**: Smooth rotational updates

### Getting Started

1. **Open ForgeAI** and navigate to the Scan page
2. **Click the "✋ Gesture Sculpt" tab** in the input mode section
3. **Start with an existing model** or create one:
   - Scan an object to generate a base model (recommended)
   - OR click "Start Gesture Mode" to sculpt from a default cube
4. **Allow camera access** when prompted
5. **Click "▶ Start Gesture Mode"** to activate hand detection
6. **Use your gestures** to sculpt:
   - Point with index finger to create holes
   - Pinch with thumb and index to deform
   - Use flat hand to rotate

### Technical Architecture

#### Hand Landmark Detection
```
Hand Skeleton (21 points):
- Wrist (0)
- Thumb: Base, Mid, Pip, Tip (1-4)
- Index: Base, Mid, Pip, Tip (5-8)
- Middle: Base, Mid, Pip, Tip (9-12)
- Ring: Base, Mid, Pip, Tip (13-16)
- Pinky: Base, Mid, Pip, Tip (17-20)
```

#### Gesture Recognition Algorithm
1. **Calculate distances** between key landmark pairs
2. **Check extension state** of each finger
3. **Compute palm openness** (distance between extremities)
4. **Match patterns** to detect gesture type
5. **Return confidence score** and gesture data

#### 3D Mesh Deformation
- **Pointing**: Uses raycasting to detect intersection points, creates hole effects
- **Pinching**: Scales object based on pinch distance/strength
- **Flat Hand**: Updates rotation based on horizontal hand movement
- **Real-time Rendering**: Three.js updates render loop with 60 FPS target

### Advanced Features

#### Real-Time Feedback
- **Status Display**: Shows detected hand, gesture type, confidence
- **Hand Data**: Displays hand position and pinch distance
- **Gesture Counter**: Tracks applied deformations
- **Visual Effects**: Glow effects at interaction points

#### Model Persistence
- Gestures are applied to your scanned model (or default)
- Changes persist until you reset or switch models
- Can save the sculpted model to your library

#### Fallback Support
- If hand detection fails, gesture system gracefully degrades
- Camera access errors are handled with user-friendly messages
- Model rendering continues even if gesture mode crashes

### Performance Considerations

| Aspect | Performance |
|--------|------------|
| Hand Detection | 30-60 FPS (depends on device) |
| Gesture Recognition | <50ms latency |
| 3D Rendering | 60 FPS (Three.js) |
| Camera Resolution | 640×480 (optimized) |
| Memory Usage | ~50-100MB (MediaPipe + Three.js) |

### Browser Compatibility

| Browser | Support | Notes |
|---------|---------|-------|
| Chrome | ✅ Full | Recommended |
| Firefox | ✅ Full | Works well |
| Safari | ✅ Full | iOS support (iPad/iPhone) |
| Edge | ✅ Full | Chromium-based |

**Requires**: Webcam access, HTTPS (or localhost)

### Customization

#### Adjust Gesture Thresholds
In the code, modify these values:

```javascript
// Pinch detection threshold (lower = more sensitive)
if (thumbIndexDist < 0.05 && palmOpenness > 0.1) { ... }

// Flat hand swipe threshold (0.0-1.0, where 1.0 = full screen)
if (Math.abs(deltaX) > 0.15 && Math.abs(deltaX) > Math.abs(deltaY)) { ... }

// Gesture confidence requirements
minDetectionConfidence: 0.5,
minTrackingConfidence: 0.5,
```

#### Change Deformation Intensity
```javascript
// Pinch deformation scale factor
const scaleFactor = 1 + deformAmount * 0.5;

// Hole size when pointing
createHoleEffect(point); // Modify sphere radius (0.15)

// Rotation speed for flat hand
const rotationAmount = deltaX > 0 ? 0.3 : -0.3;
```

### Troubleshooting

#### Hand Not Detected
- ✓ Ensure good lighting
- ✓ Keep hand in center of camera frame
- ✓ Avoid pointing directly at camera
- ✓ Check browser permissions for camera access

#### Gestures Not Registering
- ✓ Make gesture motions slowly and clearly
- ✓ Ensure fingers are fully extended/contracted
- ✓ Move hand closer to camera for pinching
- ✓ Keep hand parallel to camera for flat hand gesture

#### Performance Issues
- ✓ Close other browser tabs
- ✓ Reduce 3D model complexity
- ✓ Enable hardware acceleration in browser
- ✓ Use Chrome (best performance)

### Future Enhancements

Planned features for upcoming versions:
- 🎚️ **Multi-hand support**: Sculpt with both hands simultaneously
- 🔍 **Pressure sensitivity**: Use hand depth for intensity control
- 🎨 **Color painting**: Paint colors with hand gestures
- 📐 **Precise measurements**: Dimension control via finger separation
- 🎬 **Gesture recording**: Record and replay sculpting sequences
- 🤖 **AI suggestions**: Suggest sculpting improvements based on model
- 🔗 **Social sharing**: Share sculpting videos

### Credits

**Technology Stack**:
- **MediaPipe Hands**: Google's hand landmark detection
- **Three.js**: 3D rendering engine
- **Gemini Vision**: AI object recognition and analysis
- **Web APIs**: getUserMedia, Canvas, WebGL

**Gesture Detection Algorithms**:
- Distance-based pinch detection
- Extension state analysis
- Palm openness heuristics
- Velocity-based swipe recognition

### API Reference

#### Core Functions

```javascript
// Initialize gesture detection system
async initGestureMode()

// Start hand tracking and gesture recognition
async startGestureMode()

// Stop gesture mode and close camera
async closeGestureMode()

// Detect gesture type from hand landmarks
detectGesture(landmarks: Array<{x, y, z}>): { type, confidence, tip, distance }

// Apply pointing gesture deformation
applyPointingGesture(tipLandmark: {x, y, z})

// Apply pinching gesture deformation
applyPinchingGesture(indexTip: {x, y, z}, thumbTip: {x, y, z})

// Apply flat hand rotation
applyFlatHandGesture(landmarks: Array<{x, y, z}>)

// Update UI status display
updateGestureUI(message: string, type: string)
```

#### Gesture Data Format

```javascript
{
  type: 'POINTING' | 'PINCHING' | 'FLAT_HAND',
  confidence: number,      // 0.0 - 1.0
  tip: { x, y, z },        // Hand position (normalized 0.0-1.0)
  distance: number         // Gesture strength metric
}
```

### License

Part of ForgeAI v2.0 - 3D Object Scanner & AI Modeler

---

**Questions?** Check the system log in the UI for detailed debugging information.
