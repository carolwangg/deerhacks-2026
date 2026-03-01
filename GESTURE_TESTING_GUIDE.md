# Gesture-to-Interaction Testing & Validation Guide

## Quick Start Testing

### Environment Setup
1. Open `index.html` in Chrome or Edge (WebGL required)
2. Click **"✋ Gesture Sculpt"** tab
3. Click **"▶ Start Gesture Mode"** button
4. Allow camera access
5. Show hand to camera (12-18 inches away)

### Expected UI Elements
- ✓ Green hand skeleton overlay on canvas
- ✓ Gesture status panel shows detected gesture
- ✓ Finger states visible (✓ extended / ✗ folded)
- ✓ Visual sphere at interaction point (red/orange)
- ✓ 3D model visible and manipulable

---

## Test Case: FIST Gesture (Zoom IN)

### Visual Verification
```
Gesture Status: ✊ Fist — Zooming IN (NF) [ZZ%]
                                         └─ Zoom percentage
```

### Continuous Operation Test
1. **Make tight fist**
2. **Hold for 5 seconds**
3. **Observe**:
   - ✓ All 5 fingers show as ✗ (folded)
   - ✓ Camera smoothly moves forward (model gets larger)
   - ✓ Frame counter increments (1F, 2F, 3F...)
   - ✓ Zoom % increases (0% → 100%)
   - ✓ Motion is smooth (not jerky)

### Detailed Metrics to Check

| Metric | Expected | What It Means |
|--------|----------|---------------|
| **Gesture Type** | FIST | Correct classification |
| **Finger States** | T:✗ I:✗ M:✗ R:✗ P:✗ | All fingers truly folded |
| **Hand Scale** | 0.145-0.155 | Normal hand size |
| **Frame Counter** | Increments: 1F, 2F, 3F... | Continuous operation |
| **Zoom %** | Increases 0→100% | Smooth progression |
| **Model Size** | Grows smoothly | Camera moving closer |
| **Animation** | No stuttering | Lerp working correctly |

### Edge Cases to Test

**Test 1: Rapid Fist Clenching**
- Repeatedly open/close hand
- Expected: Gesture switches smoothly (temporal smoothing prevents flicker)
- Watch: Frame counter resets each time fist is made

**Test 2: Fist Released Suddenly**
- Make fist, hold 2 seconds, release
- Expected: Camera stops moving when hand opens (no overshoot)
- Watch: Zoom % stays at final position

**Test 3: Hold Until Max Zoom**
- Make fist, hold until model fills screen
- Expected: Zoom % hits 100%, stops (clamped at z=2)
- Watch: Console shows "Math.max(2, ...)" preventing further movement

**Test 4: Partial Hand Visibility**
- Move hand partially out of frame
- Expected: Gesture might flicker or stop (MediaPipe needs full hand)
- Watch: Hand scale might vary; gesture still works

---

## Test Case: OPEN Gesture (Zoom OUT)

### Visual Verification
```
Gesture Status: ✋ Open hand — Zooming OUT (NF) [ZZ%]
                                               └─ Zoom percentage
```

### Continuous Operation Test
1. **Spread all fingers wide** (palm flat)
2. **Hold for 5 seconds**
3. **Observe**:
   - ✓ All 5 fingers show as ✓ (extended)
   - ✓ Thumb-index distance shown as ~0.28+
   - ✓ Camera smoothly moves backward (model gets smaller)
   - ✓ Frame counter increments
   - ✓ Zoom % increases (0% → 100%)
   - ✓ Motion is smooth

### Detailed Metrics

| Metric | Expected | Validation |
|--------|----------|------------|
| **Gesture Type** | OPEN | Correct classification |
| **Finger States** | T:✓ I:✓ M:✓ R:✓ P:✓ | All extended |
| **T-I Distance** | > 0.25 | Fingers well separated |
| **Frame Counter** | Increments: 1F, 2F, 3F... | Continuous |
| **Zoom %** | Decreases 100→0% | Gets farther |
| **Model Size** | Shrinks smoothly | Camera moving back |
| **Max Distance** | Model stays visible | Clamped at z=10 |

### Edge Cases

**Test 1: Fingers Not Fully Extended**
- Partially open hand (some fingers bent)
- Expected: Gesture not detected or detected as different gesture
- Watch: Finger states show mixed ✓/✗

**Test 2: Thumb Too Close**
- Spread fingers but keep thumb near index
- Expected: OPEN not detected, might detect PINCH instead
- Watch: T-I distance shows < 0.25

**Test 3: Hold Until Max Distance**
- Spread hand, hold until model shrinks away
- Expected: Zoom % hits 100%, stops (clamped at z=10)
- Watch: Model stays visible (minimum size achieved)

**Test 4: Oscillate Between FIST and OPEN**
- Quickly switch: fist → open → fist → open
- Expected: Smooth zoom transitions (smoothing buffer prevents jerking)
- Watch: Zoom level changes smoothly despite rapid hand changes

---

## Test Case: POINT Gesture (Indent/Hole)

### Visual Verification
```
Gesture Status: 👉 Pointing — 5 indents (4F held)
                                              └─ Frames held at same spot
```

### Continuous Deformation Test
1. **Extend ONLY index finger** (other fingers folded)
2. **Point at model surface**
3. **Hold steady for 3 seconds**
4. **Observe**:
   - ✓ Only index shows ✓, others show ✗
   - ✓ Thumb-index distance > 0.12
   - ✓ Red glow sphere appears at impact point
   - ✓ Visible indentation forms in model
   - ✓ Indent gets progressively deeper (continuous)
   - ✓ Frame counter increments (1F, 2F, 3F...)

### Detailed Metrics

| Metric | Expected | Notes |
|--------|----------|-------|
| **Gesture Type** | POINT | Index extended, others folded |
| **Index State** | ✓ Extended | Must be true |
| **Other Fingers** | ✗ Folded | M, R, P must be false |
| **T-I Distance** | > 0.12 | Thumb away from index |
| **Visual Feedback** | Red sphere | Marks interaction point |
| **Deformation** | Progressive indent | Cumulative per frame |
| **Frame Counter** | Continuous | Shows frames held |
| **Indent Count** | Increases | Logged every 5 indents |

### Movement Tests

**Test 1: Point in Same Spot (5 seconds)**
- Hold index at same location
- Expected: Deep hole/indent forms
- Watch: Indent gets progressively deeper
- Metric: Frame counter shows 1F, 2F, ..., 150F (5 sec @ 30FPS)

**Test 2: Draw Pattern**
- Move pointing finger to trace path (slowly)
- Expected: Trail of indents along path
- Watch: Multiple impact spheres light up
- Result: Visible sculpture texture

**Test 3: Multi-Pass Sculpting**
- Point at surface, create small indent
- Move to adjacent spot, create another indent
- Repeat 5 times
- Expected: 5 separate indents visible
- Watch: Indent count increments

**Test 4: Quick Poke**
- Rapidly touch and release (0.5 sec touches)
- Expected: Small indents where it contacts
- Watch: Frame counter resets with each new contact
- Result: Shallow multiple indents

---

## Test Case: PINCH Gesture (Pull/Inflate)

### Visual Verification
```
Gesture Status: 🤌 Pinching (75%) — Pull #3 (8F held)
                           └─ Tightness       └─ Frames held
```

### Continuous Deformation Test
1. **Pinch thumb + index together** (very close)
2. **Point at model surface**
3. **Hold pinch for 3 seconds**
4. **Observe**:
   - ✓ Thumb shows ✓, index shows ✓, others show ✗
   - ✓ Thumb-index distance < 0.08
   - ✓ Orange glow sphere appears at pull point
   - ✓ Surface pulls inward progressively
   - ✓ Percentage shows tightness (75% = very tight)
   - ✓ Frame counter increments

### Detailed Metrics

| Metric | Expected | Range |
|--------|----------|-------|
| **Gesture Type** | PINCH | Correct classification |
| **Thumb State** | ✓ Extended | Must be true |
| **Index State** | ✓ Extended | Must be true |
| **Other Fingers** | ✗ Folded | M, R, P false |
| **T-I Distance** | < 0.08 | Very close |
| **Pinch %** | 75-100% | Tightness indicator |
| **Visual Feedback** | Orange sphere | Marks pull point |
| **Deformation** | Progressive pull | Cumulative effect |
| **Frame Counter** | Continuous | How long held |

### Strength Scaling Tests

**Test 1: Loose Pinch (Distance 0.15)**
- Slightly touch thumb to index
- Expected: Pinch % shows ~20-30%
- Watch: Weak pull (less deformation)
- Deformation Rate: ~2% per frame

**Test 2: Medium Pinch (Distance 0.10)**
- Pinch moderately (not too tight)
- Expected: Pinch % shows ~60-70%
- Watch: Moderate pull deformation
- Deformation Rate: ~5% per frame

**Test 3: Tight Pinch (Distance 0.05)**
- Pinch very tightly
- Expected: Pinch % shows ~95-100%
- Watch: Strong pull deformation
- Deformation Rate: ~8% per frame (max)

**Test 4: Variable Pressure (Pulse)**
- Pinch tight, then relax, then tight again
- Expected: Deformation rate pulses with pressure
- Watch: Pull strength varies with gesture intensity
- Result: Interesting sculpted shapes

### Movement Tests

**Test 1: Pinch and Drag**
- Start pinch at one spot, drag while pulling
- Expected: Trail of pulled/inflated surface
- Watch: Multiple impact spheres along drag path
- Result: Carved/sculpted groove

**Test 2: Multi-Point Pinch**
- Pinch 3 different areas sequentially
- Expected: All 3 areas show pulled deformations
- Watch: Pull count increments (Pull #1, #2, #3)
- Result: Complex sculpted shape

**Test 3: Hold Pinch at Limit**
- Pinch and hold (very tight) for 10+ seconds
- Expected: Surface heavily inflated at pinch point
- Watch: Frame counter shows 300+ frames (10 sec @ 30FPS)
- Result: Major surface deformation

---

## Continuous Interaction Validation

### Frame-by-Frame Behavior

**Test 1: Constant Position**
- Make gesture and hold at exact same spot
- Expected: Frame counter increments continuously
- Validation: 1F, 2F, 3F... without gaps
- Proof: Effect accumulates (deeper indent, stronger pull)

**Test 2: Smooth Movement**
- Move hand slowly between gesture positions
- Expected: Smooth transition in effect
- Watch: Impact sphere moves smoothly
- Result: No "jumping" or "warping"

**Test 3: Rapid Repositioning**
- Move gesture point rapidly around model
- Expected: Updates follow movement with ~200ms latency
- Watch: Red/orange sphere tracks finger smoothly
- Result: Can "paint" sculpted designs

### Smoothness Validation

**Camera Zoom Smoothness:**
```
✓ No jerky jumps
✓ Lerp interpolation visible (gradual convergence)
✓ Even at 60FPS, can see smooth motion
✓ Clamping doesn't create sudden stops
```

**Mesh Deformation Smoothness:**
```
✓ Gaussian falloff creates gradual gradient
✓ No sharp creases or tearing
✓ Vertices near center: most deformation
✓ Vertices at edge: smooth transition to no change
✓ Surface normals recomputed → correct lighting
```

**Gesture Smoothness:**
```
✓ 5-frame buffer prevents flicker
✓ Requires confidence ≥ 2.0 to switch
✓ Smooth transitions between gesture types
✓ No single-frame false positives cause actions
```

---

## Persistence Validation

### Test: Geometry Changes Persist

1. **Create indent** with POINT gesture
2. **Release gesture** (open hand)
3. **Make different gesture** (e.g., FIST to zoom)
4. **Return to original spot**
5. **Expected**: Original indent still visible
6. **Validation**: ✓ Geometry persists between gestures

### Test: Cumulative Sculpting

1. **Create 5 indents** at different spots
2. **Create 3 pinch pulls** at other spots
3. **Zoom out** to see whole model
4. **Expected**: All 8 deformations visible
5. **Validation**: ✓ All changes accumulated and persistent

### Test: Geometry Not Reset on Gesture Change

1. **Make POINT gesture**, create indent
2. **Switch to PINCH gesture** immediately
3. **Expected**: Indent remains while starting pinch
4. **Validation**: ✓ Geometry persists across gesture type changes

---

## Mesh Integrity Validation

### Test: No Tearing or Artifacts

1. **Create heavy deformation** (hold PINCH 20 seconds)
2. **Rotate model** (with mouse/existing controls)
3. **Observe from different angles**
4. **Expected**: 
   - ✓ Surface looks smooth (not torn)
   - ✓ Normals correct (proper lighting)
   - ✓ No visible artifacts or glitches
   - ✓ Topology intact (no floating vertices)

### Test: Falloff Gradient Visible

1. **Create POINT indent**
2. **Rotate to see surface at angle**
3. **Expected**: Smooth Gaussian falloff
   - ✓ Deepest at center
   - ✓ Gradually decreases outward
   - ✓ No sharp edges
   - ✓ Natural smooth curve

### Test: No Clipping or Collapse

1. **Hold PINCH gesture 30+ seconds** (extreme deformation)
2. **Observe model**
3. **Expected**:
   - ✓ Model doesn't collapse into singularity
   - ✓ Vertices don't clip through each other
   - ✓ Surface remains valid 3D structure
   - ✓ Can still see clear geometry

---

## Performance Validation

### Console Monitoring

Open browser DevTools (F12) and check:
- **FPS Counter**: Should stay 30-60 FPS
- **Memory**: Should not continuously grow (no leak)
- **Console Logs**: Gesture logs appear regularly (~every 5 frames)

### Frame Rate Test

1. **Monitor FPS** while performing gestures
2. **Expected**: Constant 30-60 FPS
3. **During Heavy Deformation**: Slight dip acceptable (50+ FPS)
4. **Not Acceptable**: 
   - ✗ Drops below 20 FPS
   - ✗ Stuttering/jank visible
   - ✗ Long frame pauses

### Latency Test

1. **Gesture at hand**
2. **Count frames until action visible**
3. **Expected**: ~6-12 frames latency (200-400ms @ 30FPS)
4. **Factors**:
   - MediaPipe detection: ~50ms
   - Gesture classification: ~1ms
   - Deformation: ~2ms
   - Smoothing buffer: ~170ms (5 frames)
   - Render: ~16ms

---

## State Management Validation

### Test: Mode Toggle Reset

1. **Create multiple indents/pulls**
2. **Click "Stop Gesture Mode"**
3. **Wait 1 second**
4. **Click "Start Gesture Mode"** again
5. **Expected**:
   - ✓ UI state cleared
   - ✓ Counters reset (Pointing = 0, Pinching = 0, etc.)
   - ✓ Gesture buffer cleared
   - ✓ Mesh deformations PERSIST (geometry preserved)
   - ✓ Camera position PERSISTS
   - ✓ Clean restart without artifacts

### Test: Session Continuity

1. **Sculpt model for 2 minutes** (multiple gestures)
2. **Switch to different input mode** (not gesture)
3. **Switch back to Gesture mode**
4. **Expected**:
   - ✓ All previous sculpting visible
   - ✓ State cleanly separated
   - ✓ No corruption or artifacts
   - ✓ Ready to continue sculpting

---

## Clamping Validation

### Zoom Minimum Test

1. **Make FIST gesture**
2. **Hold until camera very close**
3. **Expected**: 
   - ✓ Model visible (not clipped into)
   - ✓ Camera stops at z=2 minimum
   - ✓ Zoom % hits 100%
   - ✓ No further movement

### Zoom Maximum Test

1. **Make OPEN gesture**
2. **Hold until camera very far**
3. **Expected**:
   - ✓ Model still visible (not off-screen)
   - ✓ Camera stops at z=10 maximum
   - ✓ Zoom % hits 0%
   - ✓ No further movement

---

## Quick Checklist

### Before Deployment
- [ ] FIST gesture continuous zoom IN
- [ ] OPEN gesture continuous zoom OUT
- [ ] POINT gesture creates progressive indents
- [ ] PINCH gesture creates progressive pulls
- [ ] All effects smooth (no jerky motion)
- [ ] Geometry persists after gesture ends
- [ ] Mesh remains intact (no tearing)
- [ ] FPS stays 30+ during interactions
- [ ] Camera properly clamped (2-10)
- [ ] All counters and frames display correctly
- [ ] Mode toggle cleanly resets state
- [ ] Temporal smoothing prevents flicker

### Success Criteria
✅ All checks pass
✅ No visual artifacts
✅ No performance degradation
✅ Intuitive hand-to-interaction mapping
✅ Smooth continuous operation
✅ Persistent geometry sculpting

---

**Status**: Ready for user testing and deployment.
