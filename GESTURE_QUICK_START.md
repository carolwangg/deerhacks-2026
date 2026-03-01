# Gesture Sculpting Mode - Quick Start Guide

## 🎯 Three Powerful Gestures

### 1. 👉 POINTING - Create Holes
```
┌─────────────────┐
│    👉 INDEX    │
│  Other fingers  │  → Impact point shows as red glow
│    curled      │  → Create indentations, carve details
│                 │
└─────────────────┘
Confidence: 80% | Distance: 0
```

**How to do it:**
- Extend only your index finger
- Keep other fingers curled
- Point at the model from different angles
- Move your hand to sculpt different areas

---

### 2. 🤌 PINCHING - Deform Mesh
```
┌─────────────────┐
│ THUMB & INDEX   │
│   touching      │  → Strength = 0-100% (distance-based)
│   very close    │  → Pull to deform, release to stop
│                 │
└─────────────────┘
Confidence: 85% | Distance: 0.02 (close = strong effect)
```

**How to do it:**
- Bring thumb and index finger together
- The closer they are, the stronger the deformation
- Move pinched hand to apply deformation across model
- Release to stop (fingers separate)

---

### 3. ✋ FLAT HAND - Rotate Model
```
┌─────────────────┐
│  ✋ ALL FINGERS  │
│    EXTENDED    │  → Swipe LEFT = rotate left
│   Palm OPEN     │  → Swipe RIGHT = rotate right
│                 │
└─────────────────┘
Confidence: 80% | Movement: horizontal
```

**How to do it:**
- Open all fingers (spread them out)
- Keep palm facing camera
- Swipe left or right slowly
- Watch the model rotate smoothly

---

## 🚀 Getting Started - 5 Easy Steps

1. **Open the Gesture Tab**
   - Click the "✋ Gesture Sculpt" tab in ForgeAI

2. **Have a Model Ready**
   - Scan an object first (recommended), OR
   - Click "Start" to use default cube

3. **Allow Camera Access**
   - Browser will ask for webcam permission
   - Click "Allow"

4. **Start Gesture Mode**
   - Click the blue "▶ Start Gesture Mode" button
   - Status shows: "Ready — show your hand to start"

5. **Sculpt Away!**
   - Show your hand to camera
   - Use all three gestures to modify the model
   - Watch real-time changes on the 3D viewer

---

## 💡 Pro Tips

### Lighting
- ✅ Good lighting = better hand detection
- ❌ Dark rooms = hand detection fails
- 💡 Position yourself facing a light source

### Distance from Camera
- 📏 Optimal: 2-3 feet away
- 📏 Too close: Hand detection unreliable
- 📏 Too far: Hand appears too small

### Hand Position
- 📍 Keep hand in center of camera frame
- 📍 Avoid hiding fingers behind body
- 📍 Face palm toward camera for flat hand

### Gesture Speed
- 🐢 Slow gestures = more reliable detection
- 🐇 Fast movements = may be missed
- ⏱️ Aim for 1-2 second gesture motions

---

## 🎮 Control Summary

| Gesture | Action | Speed | Range |
|---------|--------|-------|-------|
| 👉 POINTING | Create holes | Slow | Any direction |
| 🤌 PINCHING | Deform mesh | Medium | Up/Down/Forward |
| ✋ FLAT HAND | Rotate model | Slow | Left/Right swiping |

---

## ⚙️ Gesture Sensitivity Levels

### Current Settings (Balanced)
- **Detection Confidence**: 50% (will detect less perfect gestures)
- **Tracking Confidence**: 50% (smooth tracking)
- **Pinch Threshold**: 0.05 (fairly sensitive)
- **Swipe Threshold**: 15% screen width

### To Make Gestures More Sensitive:
Lower these values in the code:
- `minDetectionConfidence: 0.3` (more detection)
- `pinch threshold: < 0.03` (easier to pinch)
- `swipeThreshold: 0.10` (easier to swipe)

### To Make Gestures Stricter:
Raise these values:
- `minDetectionConfidence: 0.7` (only clear gestures)
- `pinch threshold: < 0.08` (must be very close)
- `swipeThreshold: 0.25` (must swipe farther)

---

## 🐛 Common Issues & Fixes

### "Show your hand to start" - Hand not detected
**Solutions:**
1. Check lighting - should be bright
2. Move hand to center of camera
3. Make sure no fingers are hidden
4. Refresh the page and try again

### Gestures detected but not working
**Solutions:**
1. Make sure model is loaded (not placeholder)
2. Clear any browser errors (check console)
3. Try the gesture more slowly
4. Ensure hand is fully visible

### Camera won't turn on
**Solutions:**
1. Browser needs permission - click "Allow"
2. Close other camera apps
3. Check browser privacy settings
4. Try a different browser

### Choppy/slow performance
**Solutions:**
1. Close extra browser tabs
2. Use Chrome (best performance)
3. Check GPU acceleration is enabled
4. Reduce other running apps

---

## 📊 Status Display Breakdown

```
✓ Right Hand Detected — Gesture: PINCHING (75%)
     ↑                    ↑           ↑
   Hand found        Gesture type   Strength %
   
Thumb: (412, 240) | Pinch distance: 45.2px
  ↑                    ↑
Hand location      Distance metric
```

### What each metric means:
- **Status**: Which hand detected (Left/Right)
- **Gesture**: Current gesture type (POINTING/PINCHING/FLAT_HAND)
- **Percentage**: Gesture strength (how much it's applied)
- **Position**: Where hand is on screen (x, y pixels)
- **Distance**: Gesture intensity metric

---

## 🎬 Video Example Flow

```
1. Point at sphere → Create indent
   👉 ←→ ⚫ (with hole)

2. Pinch and drag → Deform shape
   🤌 ←→ 🟒 (stretched)

3. Flat hand swipe → Rotate to see
   ✋ ←→ 🔄 (rotated 45°)
```

---

## 🎯 Practice Exercises

### Exercise 1: Precision Pointing (30 seconds)
- Create exactly 5 holes in different spots
- Focus on control and placement
- Goal: All holes visible on final model

### Exercise 2: Smooth Deformation (1 minute)
- Pinch and hold for smooth deformation
- Gradually release and re-pinch
- Create wavy organic shapes

### Exercise 3: Full Rotation (30 seconds)
- Use flat hand to rotate 360 degrees
- Make it smooth (no jerking)
- Goal: View all sides of model

### Exercise 4: Free Sculpting (5 minutes)
- Combine all three gestures
- Create your own design
- Save the result to library!

---

## 📱 Mobile/Tablet Support

✅ **iPad**: Works great (use landscape mode)
✅ **Android Tablets**: Full support
⚠️ **Phone**: Awkward angle, works but not ideal
❌ **iPhone**: Limited by Safari restrictions (may require special setup)

---

## 🔧 Keyboard Shortcuts (Coming Soon)

- `R` - Reset to default position
- `S` - Save current sculpture
- `Z` - Undo last gesture
- `C` - Clear all deformations
- `D` - Toggle demo mode

---

## 📞 Support & Feedback

If you encounter issues:
1. Check the **System Log** in the UI (scroll through it)
2. Open **Browser Console** (F12) for detailed errors
3. Try a different browser
4. Ensure you're using HTTPS or localhost
5. Check that your **camera works** in other apps

**Gesture Mode Status:**
- Hand Detection: MediaPipe Hands ✓
- 3D Rendering: Three.js ✓
- Real-time Performance: 60 FPS target
- Browser Support: Chrome, Firefox, Safari, Edge

---

**Happy Sculpting! 🎨✨**

For advanced customization, see `GESTURE_MODE_DOCS.md`
