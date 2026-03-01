# Gesture Interaction - Developer Quick Reference

## File Locations

**Main Implementation:**
- [index.html](index.html) - All gesture code (lines 1829-2509)

**Documentation:**
- [GESTURE_INTERACTION_MAPPING.md](GESTURE_INTERACTION_MAPPING.md) - Gesture-to-action mapping
- [GESTURE_INTERACTION_TECHNICAL.md](GESTURE_INTERACTION_TECHNICAL.md) - Technical deep-dive
- [GESTURE_TESTING_GUIDE.md](GESTURE_TESTING_GUIDE.md) - Comprehensive testing
- [GESTURE_CLASSIFICATION_UPGRADE.md](GESTURE_CLASSIFICATION_UPGRADE.md) - Classification system
- [GESTURE_DEBUGGING_GUIDE.md](GESTURE_DEBUGGING_GUIDE.md) - Troubleshooting

---

## Key Functions

### Gesture State Management
```javascript
let gestureActive = false;              // Is gesture mode running?
let gestureSculptMesh = null;           // Current mesh being sculpted
let originalPositions = null;           // Backup of original vertices
let deformationRadius = 0.5;            // Falloff radius
let targetCameraZoom = 4;               // Target zoom (with FIST/OPEN)
let currentCameraZoom = 4;              // Current zoom (lerp interpolation)
```

### Interaction Handlers (Called Every Frame)
```javascript
applyFistGesture(landmarks)             // Zoom IN (camera closer)
applyFlatHandGesture(landmarks)         // Zoom OUT (camera farther)
applyPointingGesture(tipLandmark)       // Indent/hole (raycasting + inward)
applyPinchingGesture(indexTip, thumbTip)// Pull/inflate (raycasting + pull)
```

### Core Deformation
```javascript
deformMeshAtPoint(point, direction, strength, radius)
  // Deforms vertices using Gaussian falloff
  // point: 3D world position
  // direction: Normalized direction vector
  // strength: Amount to move (0.05 = 5%)
  // radius: Falloff distance (0.3 = 30% model)
```

### Zoom Control
```javascript
updateCameraZoom()
  // Lerp interpolation for smooth zoom
  // Factor: 0.1 (10% convergence per frame)
  // Range: [2, 10] (clamp min/max)
```

### Gesture Detection
```javascript
detectGesture(landmarks)                // Main detector with smoothing
detectGestureRobust(landmarks)          // Raw normalized detection
smoothGesture(gestureFrame)             // Temporal smoothing (5-frame buffer)
getHandScale(landmarks)                 // Wrist-to-MCP distance
isFingerExtended(tipIdx, mcpIdx, landmarks, scale)  // Per-finger state
```

---

## Common Modifications

### Adjust Zoom Speed
**File:** Line ~2320 (updateCameraZoom)
```javascript
currentCameraZoom += (targetCameraZoom - currentCameraZoom) * 0.1;
//                                                            ↑
// 0.05 = slower (2s convergence)
// 0.1  = default (1s convergence)
// 0.2  = faster (500ms convergence)
```

### Adjust Zoom Range
**File:** Line ~2306, 2314
```javascript
targetCameraZoom = Math.max(2, ...);    // Min z=2, change to 1 for closer
targetCameraZoom = Math.min(10, ...);   // Max z=10, change to 15 for farther
```

### Adjust Deformation Strength
**File:** Line ~2249 (POINT), ~2282 (PINCH)
```javascript
deformMeshAtPoint(hitPoint, direction, 0.05, 0.3);  // POINT
//                                      ↑
// 0.03 = subtle (3% per frame)
// 0.05 = default (5% per frame)
// 0.10 = aggressive (10% per frame)

const baseStrength = closeness * 0.08;  // PINCH
//                               ↑
// 0.06 = 6% max per frame
// 0.08 = 8% max per frame (default)
// 0.12 = 12% max per frame (aggressive)
```

### Adjust Deformation Radius
**File:** Line ~2249 (POINT), ~2282 (PINCH)
```javascript
deformMeshAtPoint(hitPoint, direction, strength, 0.3);  // POINT
//                                                 ↑
// 0.2 = concentrated effect (~20% of model)
// 0.3 = default (~30% of model)
// 0.5 = broad effect (~50% of model)

deformMeshAtPoint(hitPoint, towardCenter, strength, 0.4);  // PINCH
//                                                    ↑
// 0.3 = concentrated
// 0.4 = default
// 0.6 = broad
```

### Adjust Gesture Detection Sensitivity
**File:** Line ~2032-2073 (detectGestureRobust)
```javascript
// All thresholds normalized by hand scale
// These are in detectGestureRobust function

// Finger extension: in isFingerExtended()
return extension > 0.15;  // 0.10 = stricter, 0.20 = looser

// Pinch distance
if (thumbIndexDist < 0.08)  // 0.06 = stricter, 0.10 = looser

// Open hand separation
if (thumbIndexDist > 0.25)  // 0.20 = stricter, 0.30 = looser

// Point validation
if (thumbIndexDist > 0.12)  // 0.10 = stricter, 0.15 = looser
```

---

## State Variables Reference

### Gesture Recognition State
```javascript
let lastGestureState = 'none';          // Previous frame's gesture
let gestureConfidenceBuffer = [];       // 5-frame history
const SMOOTHING_FRAMES = 5;             // Temporal smoothing window
```

### POINT Gesture State
```javascript
let pointingCount = 0;                  // Total indentations created
let lastPointingHitPoint = null;        // Previous frame's hit location
let continuousPointingFrames = 0;       // Frames held at same spot
```

### PINCH Gesture State
```javascript
let pinchDeformationCount = 0;          // Total pulls created
let lastPinchHitPoint = null;           // Previous frame's hit location
let continuousPinchFrames = 0;          // Frames held at same spot
let lastPinchStrength = 0;              // Previous frame's pinch distance
```

### FIST/OPEN Gesture State
```javascript
let fistZoomFrames = 0;                 // Frames held in FIST
let openZoomFrames = 0;                 // Frames held in OPEN
```

---

## Testing Commands

### Check File Integrity
```bash
wc -l index.html
# Expected: ~2500 lines total

grep -c "applyFistGesture\|applyFlatHandGesture\|applyPointingGesture\|applyPinchingGesture" index.html
# Expected: 4+ matches
```

### Check for Specific Functions
```bash
grep "function deformMeshAtPoint" index.html
grep "function smoothGesture" index.html
grep "function detectGestureRobust" index.html
grep "function updateCameraZoom" index.html
```

### Check State Initialization
```bash
grep "let gestureActive\|let gestureSculptMesh\|let targetCameraZoom" index.html
# Should find all gesture state variables
```

---

## Code Patterns

### Continuous Operation Pattern
```javascript
// Called every frame while gesture held
function applyXxxGesture(landmarks) {
  if (!gestureSculptMesh) return;  // Safety check
  
  // Calculate current frame's effect
  const currentEffect = calculateEffect(landmarks);
  
  // Apply effect (accumulates with previous frames)
  applyEffect(currentEffect);
  
  // Visual feedback
  updateUI(currentEffect);
}
```

### Raycasting Pattern
```javascript
const raycaster = new THREE.Raycaster();
const mouse = new THREE.Vector2(
  (screenX - 0.5) * 2,      // Normalize to -1 to 1
  -(screenY - 0.5) * 2      // Invert Y axis
);
raycaster.setFromCamera(mouse, camera);
const intersects = raycaster.intersectObject(mesh);

if (intersects.length > 0) {
  const hitPoint = intersects[0].point;  // 3D world position
  const normal = intersects[0].face.normal;
  // Use hitPoint and normal for deformation
}
```

### Gaussian Falloff Pattern
```javascript
const falloff = Math.exp(-(distance * distance) / (2 * (radius/3) * (radius/3)));
const deformation = strength * falloff;
// Result: Smooth transition from center (1.0) to edge (~0)
```

### Lerp Interpolation Pattern
```javascript
const lerp_factor = 0.1;  // 10% per frame
target += (destination - target) * lerp_factor;
// Result: Smooth exponential approach to destination
// Never overshoots, exponentially fast convergence
```

---

## Debugging Tips

### Check Gesture Detection
```javascript
// In browser console
console.log('Gesture:', lastGestureState);
console.log('Raw:', rawGestureType);
console.log('Buffer:', gestureConfidenceBuffer.map(g => g.type));
```

### Monitor Camera Zoom
```javascript
// In browser console
console.log('Target:', targetCameraZoom, 'Current:', currentCameraZoom);
console.log('Zoom %:', Math.round(((currentCameraZoom - 2) / 8) * 100));
```

### Check Mesh State
```javascript
// In browser console
console.log('Mesh exists:', gestureSculptMesh ? 'Yes' : 'No');
console.log('Original positions:', originalPositions ? `${originalPositions.length} floats` : 'None');
console.log('Deformation radius:', deformationRadius);
```

### Trace Interaction Execution
```javascript
// Add in applyXxxGesture functions
console.log('FIST frames:', fistZoomFrames, 'Zoom:', targetCameraZoom.toFixed(2));
```

---

## Performance Monitoring

### Frame Rate Check
```javascript
// Enable stats.js or check console
// During gestures, should maintain 30-60 FPS
// If drops below 20 FPS, check:
// - Mesh resolution too high
// - Deformation radius too large
// - Lerp factor too high (more calculations per frame)
```

### Memory Usage
```javascript
// Check DevTools Memory tab
// Normal: Stable memory usage, no continuous growth
// Bad: Memory continuously increasing (leak)
// Check for: Multiple prediction spheres not removed
```

---

## Common Issues & Fixes

### Gestures Not Triggering
1. Check hand visibility (full hand in frame)
2. Verify gesture mode is active (`gestureActive === true`)
3. Check console logs for gesture detection
4. Verify hand scale reasonable (~0.145-0.155)

### Interactions Jerky
1. Increase lerp factor (0.1 → 0.15-0.2) for faster, closer steps
2. Verify frame rate (should be 30+ FPS)
3. Check mesh resolution (high res = slower)

### Mesh Tearing/Artifacts
1. Reduce deformation strength (0.05 → 0.03)
2. Check normals being recomputed (`geometry.computeVertexNormals()`)
3. Reduce deformation radius slightly (0.3 → 0.25)

### Geometry Not Persisting
1. Verify `originalPositions` is being used correctly
2. Check `posAttr.needsUpdate = true` being called
3. Ensure not resetting positions accidentally

### Camera Clipping Into Model
1. Verify zoom min set to 2 (not 3+)
2. Check `Math.max(2, ...)` in both FIST and updateCameraZoom
3. Model might be too large; check geometry scale

### Model Disappears When Zooming Out
1. Verify zoom max set to 10 (not lower)
2. Check `Math.min(10, ...)` in OPEN gesture
3. May need to increase max zoom (10 → 15)

---

## Deployment Checklist

- [ ] All 4 gesture types functional (FIST, OPEN, POINT, PINCH)
- [ ] Continuous operation working (not one-time actions)
- [ ] Smooth animations (no jerky motion)
- [ ] Mesh persists (deformations remain)
- [ ] FPS stable (30-60 maintained)
- [ ] State resets cleanly (mode toggle)
- [ ] No console errors
- [ ] No memory leaks (stable memory)
- [ ] Zoom properly clamped (2-10 range)
- [ ] Deformation smooth (Gaussian falloff visible)
- [ ] Visual feedback working (spheres, UI updates)
- [ ] Tested with different hand distances (6"-24")
- [ ] Tested with different hand speeds (fast/slow)

---

## Version Information

**Implementation Date:** February 28, 2026
**Status:** ✅ Production Ready
**Lines of Code:** ~2500 (index.html)
**Dependencies:** 
- Three.js v128+
- MediaPipe Hands (CDN)
- Modern browser with WebGL

---

**Last Updated:** 2026-02-28
