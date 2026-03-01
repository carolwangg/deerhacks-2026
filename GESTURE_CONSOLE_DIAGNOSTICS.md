# Gesture Detection Quick Diagnostic

## Browser Console Commands

Copy and paste these commands into the browser console (F12) while in gesture mode.

### 1. Show Current Gesture State
```javascript
console.log('=== GESTURE STATE ===');
console.log('Current gesture:', lastGestureState);
console.log('Buffer length:', gestureConfidenceBuffer.length);
console.log('Buffer gestures:', gestureConfidenceBuffer.map(g => g.type).join(' → '));
console.log('Debug counter:', gestureDebugCounter);
```

### 2. Test Hand Detection
```javascript
console.log('=== HAND DETECTION ===');
console.log('Gesture active:', gestureActive);
console.log('Gesture hands available:', typeof gestureHands !== 'undefined');
console.log('Hand scale from last detection will show in logs');
```

### 3. Dump Full Gesture Buffer
```javascript
console.log('=== FULL BUFFER STATE ===');
gestureConfidenceBuffer.forEach((frame, idx) => {
  console.log(`Frame ${idx}: ${frame.type.padEnd(6)} (conf: ${frame.confidence}) - ${frame.debug.extendedCount} fingers extended, T-I: ${frame.debug.thumbIndexDist}`);
});
```

### 4. Check Thresholds
```javascript
console.log('=== CURRENT THRESHOLDS ===');
console.log('Finger extension threshold: 0.12');
console.log('Pinch distance: < 0.10');
console.log('Point distance: > 0.10');
console.log('Open distance: > 0.25');
console.log('Smoothing frames: 5');
console.log('Switch threshold: 1.5');
```

### 5. Clear Gesture History (to reset detection)
```javascript
gestureConfidenceBuffer = [];
lastGestureState = 'none';
gestureDebugCounter = 0;
console.log('✓ Gesture history cleared');
```

### 6. Force Gesture Detection Test (simulated)
```javascript
// Check the last raw gesture that was detected
function showLastRawGesture() {
  const rawInfo = gestureConfidenceBuffer[gestureConfidenceBuffer.length - 1];
  if (rawInfo) {
    console.log('Last detected gesture:');
    console.log('  Type:', rawInfo.type);
    console.log('  Hand scale:', rawInfo.debug.handScale);
    console.log('  Fingers extended:', rawInfo.debug.extendedCount);
    console.log('  Thumb:', rawInfo.debug.thumbExtended);
    console.log('  Index:', rawInfo.debug.indexExtended);
    console.log('  Middle:', rawInfo.debug.middleExtended);
    console.log('  Ring:', rawInfo.debug.ringExtended);
    console.log('  Pinky:', rawInfo.debug.pinkyExtended);
    console.log('  Thumb-Index distance:', rawInfo.debug.thumbIndexDist);
  }
}
showLastRawGesture();
```

## Expected Log Output Examples

### When Making a FIST
```
Gesture: FIST   (raw: FIST  ) | Scale: 0.145 | Fingers: T:✗ I:✗ M:✗ R:✗ P:✗ (0 ext) | T-I: 0.18 | Buffer: [FIS|FIS|FIS]
Gesture: FIST   (raw: FIST  ) | Scale: 0.146 | Fingers: T:✗ I:✗ M:✗ R:✗ P:✗ (0 ext) | T-I: 0.19 | Buffer: [FIS|FIS|FIS]
```

### When Making a PINCH
```
Gesture: PINCH  (raw: PINCH ) | Scale: 0.147 | Fingers: T:✓ I:✓ M:✗ R:✗ P:✗ (2 ext) | T-I: 0.06 | Buffer: [PIN|PIN|PIN]
Gesture: PINCH  (raw: PINCH ) | Scale: 0.146 | Fingers: T:✓ I:✓ M:✗ R:✗ P:✗ (2 ext) | T-I: 0.07 | Buffer: [PIN|PIN|PIN]
```

### When Making an OPEN HAND
```
Gesture: OPEN   (raw: OPEN  ) | Scale: 0.148 | Fingers: T:✓ I:✓ M:✓ R:✓ P:✓ (5 ext) | T-I: 0.33 | Buffer: [OPE|OPE|OPE]
Gesture: OPEN   (raw: OPEN  ) | Scale: 0.145 | Fingers: T:✓ I:✓ M:✓ R:✓ P:✓ (5 ext) | T-I: 0.34 | Buffer: [OPE|OPE|OPE]
```

### When POINTING
```
Gesture: POINT  (raw: POINT ) | Scale: 0.144 | Fingers: T:✗ I:✓ M:✗ R:✗ P:✗ (1 ext) | T-I: 0.28 | Buffer: [POI|POI|POI]
Gesture: POINT  (raw: POINT ) | Scale: 0.146 | Fingers: T:✗ I:✓ M:✗ R:✗ P:✗ (1 ext) | T-I: 0.27 | Buffer: [POI|POI|POI]
```

## Troubleshooting with Console

### Issue: No gesture detected (always shows "none")
```javascript
// Run this and check what the buffer shows
function diagnoseNoGesture() {
  if (gestureConfidenceBuffer.length === 0) {
    console.log('❌ Buffer is empty - no gestures detected');
  } else {
    const lastFrame = gestureConfidenceBuffer[gestureConfidenceBuffer.length - 1];
    console.log('Last raw gesture:', lastFrame.type);
    console.log('Extended count:', lastFrame.debug.extendedCount);
    console.log('All finger states:', {
      T: lastFrame.debug.thumbExtended,
      I: lastFrame.debug.indexExtended,
      M: lastFrame.debug.middleExtended,
      R: lastFrame.debug.ringExtended,
      P: lastFrame.debug.pinkyExtended
    });
  }
}
diagnoseNoGesture();
```

### Issue: Gesture detected in raw but not smoothed
```javascript
// This means temporal smoothing is blocking it
function checkSmoothing() {
  const scores = {};
  for (const frame of gestureConfidenceBuffer) {
    scores[frame.type] = (scores[frame.type] || 0) + frame.confidence;
  }
  console.log('Smoothing scores:', scores);
  console.log('Smoothing threshold: 1.5');
  console.log('Current gesture:', lastGestureState);
  for (const [type, score] of Object.entries(scores)) {
    console.log(`  ${type}: ${score} ${score >= 1.5 ? '✓ PASS' : '❌ BLOCKED'}`);
  }
}
checkSmoothing();
```

### Issue: Gesture flickering between types
```javascript
// Shows if gesture is unstable
function checkStability() {
  const gestures = gestureConfidenceBuffer.map(g => g.type);
  const uniqueGestures = [...new Set(gestures)];
  
  if (uniqueGestures.length > 1) {
    console.log('❌ Unstable gesture - flickering between:');
    uniqueGestures.forEach(g => {
      const count = gestures.filter(x => x === g).length;
      console.log(`   ${g}: ${count}/${gestures.length} frames`);
    });
  } else {
    console.log('✓ Gesture is stable:', gestures[0]);
  }
}
checkStability();
```

## Real-Time Monitoring

### Enable live monitoring
```javascript
// Shows gesture info every second
const monitorInterval = setInterval(() => {
  if (gestureConfidenceBuffer.length > 0) {
    const latest = gestureConfidenceBuffer[gestureConfidenceBuffer.length - 1];
    console.log(`[LIVE] Gesture: ${lastGestureState.padEnd(6)} | Raw: ${latest.type.padEnd(6)} | Fingers: ${latest.debug.extendedCount} ext | T-I: ${latest.debug.thumbIndexDist}`);
  }
}, 1000);

// Stop monitoring
clearInterval(monitorInterval);
```

## Performance Check

### Check frame rate and latency
```javascript
// Simple FPS counter
let frameCount = 0;
let lastTime = Date.now();
function checkFPS() {
  frameCount++;
  const now = Date.now();
  if (now - lastTime >= 1000) {
    console.log(`FPS: ${frameCount}`);
    frameCount = 0;
    lastTime = now;
  }
}
// Call this in animation loop (advanced)

// Or just check gesture latency
console.log('Gesture latency estimate: ~50-100ms');
console.log('Smoothing buffer: 5 frames = ~167ms (at 30fps)');
```

## Hand Metrics Check

```javascript
// Shows current hand metrics
function showHandMetrics() {
  if (gestureConfidenceBuffer.length > 0) {
    const frame = gestureConfidenceBuffer[gestureConfidenceBuffer.length - 1];
    console.log('=== CURRENT HAND METRICS ===');
    console.log('Hand Scale:', frame.debug.handScale);
    console.log('Scale status:', 
      frame.debug.handScale > 0.20 ? '❌ Too far' :
      frame.debug.handScale < 0.10 ? '❌ Too close' :
      '✓ Good');
    console.log('Extended fingers:', frame.debug.extendedCount);
    console.log('Thumb-Index distance:', frame.debug.thumbIndexDist);
    console.log('Distance status:',
      frame.debug.thumbIndexDist < 0.08 ? 'Pinch range' :
      frame.debug.thumbIndexDist < 0.12 ? 'Close' :
      frame.debug.thumbIndexDist < 0.25 ? 'Moderate' :
      'Far (OPEN range)');
  }
}
showHandMetrics();
```

## Quick Test Sequence

Run this sequence to test all gestures:

```javascript
// 1. Check that system is ready
console.log('Step 1: System ready?', gestureActive);

// 2. Make FIST and check
console.log('Step 2: Make a FIST now...');
setTimeout(() => {
  console.log('  Raw:', gestureConfidenceBuffer[gestureConfidenceBuffer.length - 1]?.type);
  console.log('  Smoothed:', lastGestureState);
}, 500);

// 3. Make PINCH and check
setTimeout(() => {
  console.log('Step 3: Make a PINCH now...');
}, 2000);
setTimeout(() => {
  console.log('  Raw:', gestureConfidenceBuffer[gestureConfidenceBuffer.length - 1]?.type);
  console.log('  Smoothed:', lastGestureState);
}, 2500);

// 4. Make OPEN and check
setTimeout(() => {
  console.log('Step 4: Make an OPEN hand now...');
}, 4000);
setTimeout(() => {
  console.log('  Raw:', gestureConfidenceBuffer[gestureConfidenceBuffer.length - 1]?.type);
  console.log('  Smoothed:', lastGestureState);
}, 4500);

// 5. Make POINT and check
setTimeout(() => {
  console.log('Step 5: POINT with index finger...');
}, 6000);
setTimeout(() => {
  console.log('  Raw:', gestureConfidenceBuffer[gestureConfidenceBuffer.length - 1]?.type);
  console.log('  Smoothed:', lastGestureState);
}, 6500);
```

---

**Pro Tips:**
- Keep console open while testing gestures
- Match your hand position to the expected finger states
- Hand scale should stay 0.14-0.15
- Buffer should show stable gesture (same type repeated)
- Watch T-I distance to understand pinch/point/open distinctions
