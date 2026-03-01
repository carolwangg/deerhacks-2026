# Gesture Classification Debugging Guide

## Console Output Reference

### Sample Console Logs

```
Gesture: FIST (raw: FIST) | Hand Scale: 0.148 | T:false I:false M:false R:false P:false | TI Dist: 0.15
Gesture: FIST (raw: FIST) | Hand Scale: 0.146 | T:false I:false M:false R:false P:false | TI Dist: 0.16
Gesture: FIST (raw: FIST) | Hand Scale: 0.147 | T:false I:false M:false R:false P:false | TI Dist: 0.15
```
✓ **Good**: All fingers false (folded), consistent hand scale

```
Gesture: POINT (raw: POINT) | Hand Scale: 0.145 | T:false I:true M:false R:false P:false | TI Dist: 0.28
Gesture: POINT (raw: POINT) | Hand Scale: 0.144 | T:false I:true M:false R:false P:false | TI Dist: 0.29
```
✓ **Good**: Only index extended, thumb-index distance > 0.12

```
Gesture: PINCH (raw: PINCH) | Hand Scale: 0.149 | T:true I:true M:false R:false P:false | TI Dist: 0.07
Gesture: PINCH (raw: PINCH) | Hand Scale: 0.148 | T:true I:true M:false R:false P:false | TI Dist: 0.06
```
✓ **Good**: Thumb and index true, others false, distance < 0.08

```
Gesture: OPEN (raw: OPEN) | Hand Scale: 0.150 | T:true I:true M:true R:true P:true | TI Dist: 0.35
Gesture: OPEN (raw: OPEN) | Hand Scale: 0.151 | T:true I:true M:true R:true P:true | TI Dist: 0.34
```
✓ **Good**: All fingers true, distance > 0.25

### Common Issues and Solutions

#### Issue 1: Gesture Keeps Flickering Between Two Types

**Symptom**:
```
Gesture: POINT (raw: POINT)
Gesture: none (raw: none)
Gesture: POINT (raw: POINT)
Gesture: none (raw: none)
```

**Cause**: Hand landmarks unstable or just at gesture boundary

**Solution**: Check browser console for smoothing buffer, verify hand is fully visible in frame

#### Issue 2: Hand Scale Varies Wildly

**Symptom**:
```
Hand Scale: 0.092 | ...
Hand Scale: 0.156 | ...
Hand Scale: 0.138 | ...
```

**Cause**: MediaPipe losing hand landmarks or hand moving in/out of frame

**Solution**: Keep hand fully visible, ensure good lighting, steady hand position

#### Issue 3: Fingers Always Show as Folded (all false)

**Symptom**:
```
T:false I:false M:false R:false P:false | (even when fingers extended)
Gesture: FIST (raw: FIST)  (even with hand open)
```

**Cause**: MediaPipe not detecting full hand pose or fingers too close to camera

**Solution**: 
- Move hand back 12-18 inches from camera
- Ensure hand is fully visible (not cropped at edges)
- Check lighting - ensure hand is well-lit

#### Issue 4: PINCH Detected as POINT

**Symptom**:
```
Gesture: POINT (raw: POINT) | T:false I:true M:false R:false P:false | TI Dist: 0.09
```

**Cause**: Thumb not detected as extended (landmark quality poor)

**Solution**:
- Ensure thumb is fully extended during pinch
- Better lighting
- Clearer camera feed

#### Issue 5: POINT Detected as PINCH

**Symptom**:
```
Gesture: PINCH (raw: PINCH) | T:true I:true M:false R:false P:false | TI Dist: 0.07
```

**Cause**: Thumb too close to index during pointing gesture

**Solution**:
- When pointing, move thumb away from index finger
- Spread hand open to avoid thumb coming forward

---

## UI Display Reference

### Gesture Info Panel Output

```
✓ Right Hand — Gesture: PINCH (raw: PINCH)
Hand Scale: 0.148 | Thumb: ✓ | Index: ✓ | Middle: ✗ | Ring: ✗ | Pinky: ✗ | T-I Distance: 0.06
```

**Field Breakdown**:
- `✓ Right Hand` - Hand detected successfully
- `Gesture: PINCH` - Current smoothed gesture (what triggers actions)
- `(raw: PINCH)` - Raw detection before smoothing (debugging only)
- `Hand Scale: 0.148` - Normalized hand size (0.14-0.16 typical)
- `Thumb: ✓` - Thumb extended (true/false)
- `Index: ✓` - Index extended (true/false)
- `Middle: ✗` - Middle folded
- `Ring: ✗` - Ring folded
- `Pinky: ✗` - Pinky folded
- `T-I Distance: 0.06` - Thumb-index distance normalized

### Expected Values by Gesture

#### FIST Gesture
```
Gesture: FIST (raw: FIST)
Hand Scale: 0.145-0.155 | Thumb: ✗ | Index: ✗ | Middle: ✗ | Ring: ✗ | Pinky: ✗ | T-I Distance: 0.10-0.20
```
✓ All fingers false ✓ Distance irrelevant

#### OPEN Gesture
```
Gesture: OPEN (raw: OPEN)
Hand Scale: 0.145-0.155 | Thumb: ✓ | Index: ✓ | Middle: ✓ | Ring: ✓ | Pinky: ✓ | T-I Distance: 0.28-0.40
```
✓ All fingers true ✓ T-I distance > 0.25

#### POINT Gesture
```
Gesture: POINT (raw: POINT)
Hand Scale: 0.145-0.155 | Thumb: ✗ | Index: ✓ | Middle: ✗ | Ring: ✗ | Pinky: ✗ | T-I Distance: 0.15-0.40
```
✓ Only index true ✓ T-I distance > 0.12

#### PINCH Gesture
```
Gesture: PINCH (raw: PINCH)
Hand Scale: 0.145-0.155 | Thumb: ✓ | Index: ✓ | Middle: ✗ | Ring: ✗ | Pinky: ✗ | T-I Distance: 0.04-0.10
```
✓ Thumb and index true ✓ Others false ✓ T-I distance < 0.08

---

## Real-time Monitoring Tips

### 1. **Verify Hand Scale Stability**
   - Good: Consistently 0.145-0.155 (varies ±0.005)
   - Bad: Jumps between 0.10 and 0.18
   - Fix: Better lighting, steadier hand, move back slightly

### 2. **Check Finger State Accuracy**
   - Open your hand flat and verify all ✓
   - Make a fist and verify all ✗
   - Point with index only and verify only index ✓
   - If wrong, hand may be too close or partially out of frame

### 3. **Monitor Gesture Stability**
   - When holding steady gesture, "raw" should match "Gesture" for most frames
   - If "Gesture" changes but "raw" stays same, smoothing is working
   - If "Gesture" keeps changing, hand position or lighting needs adjustment

### 4. **Test Boundary Cases**
   - Pinch with thumb and index very close (distance should approach 0.04-0.05)
   - Point with hand fully open (distance should be 0.25-0.40)
   - Transition slowly between gestures (should smoothly switch, no flicker)

---

## Performance Monitoring

### Typical Performance Metrics

- **Detection Frequency**: ~30-60 Hz (matches camera)
- **Detection Latency**: 2-5ms (MediaPipe processing)
- **Smoothing Buffer**: ~167ms (5 frames at 30 Hz)
- **Total Latency**: ~170-175ms from gesture to mesh deformation

### Acceptable Performance
- Gesture responds within 200-250ms of hand movement
- No stuttering or frame drops
- Console logs appear smoothly (not in bursts)
- UI updates smoothly as hand moves

---

## Advanced Debugging

### Add More Console Logging

To log every frame (not just 20%), modify `detectGesture()`:

```javascript
// Change this:
if (Math.random() < 0.2) {
  console.log(...)
}

// To this (log every frame):
console.log(...)
```

### Log Full Landmarks

Add to `onGestureResults()`:

```javascript
console.table({
  wrist: landmarks[0],
  thumbTip: landmarks[4],
  indexTip: landmarks[8],
  middleTip: landmarks[12],
  // etc...
});
```

### Monitor Gesture State History

Create this helper to see smoothing buffer:

```javascript
function showGestureBuffer() {
  console.log('Gesture Buffer:', gestureConfidenceBuffer.map(g => g.type).join(' → '));
  console.log('Last Gesture State:', lastGestureState);
}

// Call in console when needed
showGestureBuffer();
```

---

## Testing Checklist

### Basic Functionality
- [ ] FIST gesture zooms camera in (hand gets closer)
- [ ] OPEN gesture zooms camera out (hand gets farther)
- [ ] POINT creates visible indent/hole in mesh
- [ ] PINCH pulls mesh inward at pinch location

### Reliability
- [ ] Hold each gesture steady for 3 seconds, should not flicker
- [ ] Transition between gestures, should smoothly switch
- [ ] Move hand closer to camera (6"), gestures still work
- [ ] Move hand farther from camera (24"), gestures still work

### Hand Scale Normalization
- [ ] Close hand to camera (6"), detect all gestures
- [ ] Far from camera (24"), same gestures work equally well
- [ ] Hand scale in UI shows reasonable variation (±0.005)

### Finger State Detection
- [ ] Open hand: all 5 fingers show ✓
- [ ] Make fist: all 5 fingers show ✗
- [ ] Point with index: only index shows ✓
- [ ] Pinch: thumb+index show ✓, others show ✗

### Temporal Smoothing
- [ ] Gesture type stays stable when holding position
- [ ] No flickering between nearby gestures
- [ ] Gesture changes within 100-200ms of actual hand change
