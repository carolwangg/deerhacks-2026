# Gesture Classification System - Quick Reference Card

## 🎯 Gesture Detection Quick Start

### Open Browser Console
```javascript
// Every gesture detection logs once per 5 frames
// Look for logs like:
Gesture: POINT (raw: POINT) | Hand Scale: 0.146 | T:false I:true M:false R:false P:false | TI Dist: 0.28
```

---

## 📊 Gesture Patterns

### FIST ✊
```
Finger States: T:false I:false M:false R:false P:false
Hand Scale: 0.145-0.155
Effect: ZOOM IN
Look For: All fingers shown as ✗ in UI panel
```

### OPEN ✋
```
Finger States: T:true I:true M:true R:true P:true
Thumb-Index Distance: > 0.25
Hand Scale: 0.145-0.155
Effect: ZOOM OUT
Look For: All fingers shown as ✓ in UI panel
```

### POINT 👉
```
Finger States: T:false I:true M:false R:false P:false
Thumb-Index Distance: > 0.12
Hand Scale: 0.145-0.155
Effect: CREATE HOLE
Look For: Only "Index: ✓" in UI panel
```

### PINCH 🤌
```
Finger States: T:true I:true M:false R:false P:false
Thumb-Index Distance: < 0.08
Hand Scale: 0.145-0.155
Effect: PULL INWARD
Look For: "Thumb: ✓ Index: ✓" + others "✗" in UI panel
```

---

## 🐛 Troubleshooting Flowchart

**Gesture not recognized?**
1. ✓ Hand fully visible in camera feed?
2. ✓ Good lighting on hand?
3. ✓ Hand 12-18 inches from camera?
4. ✓ Check console logs for finger states

**Wrong gesture detected?**
1. Check console log finger states
2. Compare with "Look For" patterns above
3. Move hand slightly to clarify gesture
4. Check thumb-index distance in UI

**Gesture keeps flickering?**
1. Move hand slower/steadier
2. Check "Hand Scale" - should be ~0.145-0.155
3. Better lighting may help
4. System smooths over 5 frames - hold position

---

## 🔍 Console Log Reference

### What Each Value Means

```
Gesture: POINT          # Current gesture (after smoothing)
(raw: POINT)            # Raw detection (before smoothing)
Hand Scale: 0.148       # Hand size reference - should be 0.145-0.155
T:false                 # Thumb: false=folded, true=extended
I:true                  # Index: false=folded, true=extended
M:false                 # Middle: false=folded, true=extended
R:false                 # Ring: false=folded, true=extended
P:false                 # Pinky: false=folded, true=extended
TI Dist: 0.28           # Thumb-Index distance (normalized)
```

### Good vs Bad Hand Scale

| Value | Status | Action |
|-------|--------|--------|
| 0.145-0.155 | ✓ Good | Normal operation |
| 0.10-0.14 | ⚠️ Low | Hand too close or landmarks poor |
| 0.16-0.20 | ⚠️ High | Hand too far or unusual posture |
| Jumps around | ✗ Bad | Bad camera feed, poor lighting |

---

## 📱 UI Panel Reference

### Normal Display
```
✓ Right Hand — Gesture: POINT (raw: POINT)
Hand Scale: 0.146 | Thumb: ✗ | Index: ✓ | Middle: ✗ | Ring: ✗ | Pinky: ✗ | T-I Distance: 0.28
```

### What Each Part Means
- `✓ Right Hand` - Hand detected successfully
- `Gesture: POINT` - Current gesture (smoothed, controls actions)
- `(raw: POINT)` - Raw detection (debugging, may differ briefly)
- `Hand Scale: 0.146` - Hand size reference
- `Thumb: ✗` - Thumb state (✓=extended, ✗=folded)
- `Index: ✓` - Index state
- `Middle: ✗` - Middle state
- `Ring: ✗` - Ring state
- `Pinky: ✗` - Pinky state
- `T-I Distance: 0.28` - Thumb-Index distance (normalized)

---

## ⚡ Quick Testing Steps

### Step 1: Verify Hand Detection
```
1. Show hand to camera
2. Look for "✓ Right Hand" in UI
3. If not appearing: check lighting, move hand back
```

### Step 2: Test Each Gesture

**FIST Test:**
1. Make tight fist
2. UI should show: `T:false I:false M:false R:false P:false`
3. Model should zoom IN
4. Console should show `Gesture: FIST`

**OPEN Test:**
1. Spread all fingers wide
2. UI should show: `T:true I:true M:true R:true P:true | T-I Distance: 0.28+`
3. Model should zoom OUT
4. Console should show `Gesture: OPEN`

**POINT Test:**
1. Extend only index finger
2. UI should show: `T:false I:true M:false R:false P:false | T-I Distance: 0.25+`
3. Model should show indent
4. Console should show `Gesture: POINT`

**PINCH Test:**
1. Pinch thumb + index together
2. UI should show: `T:true I:true M:false R:false P:false | T-I Distance: 0.06`
3. Model should pull inward
4. Console should show `Gesture: PINCH`

---

## 🎮 Normal Operation Checklist

- [ ] Hand 12-18 inches from camera
- [ ] Good lighting on hand
- [ ] Gesture UI panel visible
- [ ] Finger states match visual observation
- [ ] Hand scale ~0.145-0.155
- [ ] Gestures respond within 200-250ms
- [ ] No flickering between gestures
- [ ] Console logs appearing regularly

---

## 🔧 If Something Goes Wrong

### Hand Not Detected
1. Check camera permission granted
2. Ensure hand fully visible in frame
3. Check lighting - hand should be well-lit
4. Move hand back 12-18 inches

### Finger States Wrong
1. Move hand back slightly
2. Ensure good lighting
3. Open hand flat, then make fist - should update
4. Check if hand partially out of frame

### Gesture Not Triggering Action
1. Confirm finger states correct in UI
2. Wait 200-250ms (system latency)
3. Make gesture more deliberate (slower, clearer)
4. Check browser console for errors

### Hand Scale Jumping
1. Move to better lit area
2. Ensure hand not too close (< 6")
3. Ensure hand not too far (> 24")
4. Keep hand steady

---

## 📈 Performance Expectations

- **Detection Speed**: Logs appear every ~150-250ms (sampling)
- **Gesture Response**: 170-200ms from gesture to model change
- **Frame Rate**: 30-60 FPS (camera dependent)
- **Smoothing**: ~5 frames = 167ms buffer for stability

---

## 🎓 Understanding Thresholds

### All Thresholds Auto-Scale

The system automatically scales all thresholds based on hand size:

```javascript
// Finger Extension (all 5 fingers)
extension > 0.15 * handScale

// Pinch Detection
distance(thumb, index) < 0.08 * handScale

// Open Hand Separation
distance(thumb, index) > 0.25 * handScale

// Point Validation (thumb away)
distance(thumb, index) > 0.12 * handScale
```

**Why?** Hand closer to camera = landmarks closer together. Without scaling, gesture detection would fail. With scaling, it works at any distance.

---

## 💡 Pro Tips

1. **Keep hand steady** when making gesture - reduces noise
2. **Wait for smoothing** - gesture confirmed after ~5 frames
3. **Good lighting** - helps MediaPipe detect landmarks
4. **12-18 inches away** - sweet spot for detection
5. **Don't shake** - smoothing works, but steady is better

---

## 📞 Getting Help

1. **Check console logs** - `Gesture: X (raw: Y)` tells you raw detection
2. **Compare finger states** - UI shows each finger's state
3. **Check hand scale** - 0.145-0.155 is normal
4. **Look at T-I distance** - Key metric for pinch/point detection
5. **See GESTURE_DEBUGGING_GUIDE.md** for advanced troubleshooting

---

**System**: ForgeAI Gesture Classification v2.0
**Status**: ✅ Production Ready
**Latency**: ~200ms total (detection + smoothing + deformation)
