# Quick Start Guide

## How to Run Your Rubik's Cube Solver

### Option 1: Using Eclipse (Recommended)

1. **Open Eclipse** and import this project
2. **Right-click** on `src/MainFrame.java`
3. **Select** "Run As" → "Java Application"
4. **Two windows will appear:**
   - **Camera Window** (650x490): Shows live webcam feed with detected stickers
   - **Cube Display Window** (800x700): Shows captured cube state and solution

### Option 2: Command Line (if OpenCV is configured)

```bash
# Compile (requires OpenCV in classpath)
javac -cp ".:path/to/opencv.jar" src/*.java -d bin

# Run (requires OpenCV native library)
java -cp "bin:path/to/opencv.jar" -Djava.library.path=/path/to/opencv/lib MainFrame
```

## What You'll See

### Camera Window (Left)
- Live video feed from your webcam
- White rectangles drawn around detected stickers
- Updates 30 times per second

### Cube Display Window (Right)
- 54 colored buttons arranged in cube net layout
- Status messages at top (green/blue/red)
- Instructions panel on right side
- Solution display at bottom

## How to Use

### Step 1: Position Your Cube
- Hold cube 6-12 inches from camera
- Ensure good lighting (bright, even)
- All 9 stickers should be visible
- You'll see white rectangles around detected stickers

### Step 2: Capture Faces (in order)
Press **SPACE** to capture each face:

1. **TOP** (white center)
2. **LEFT** (orange center)  
3. **FRONT** (green center)
4. **RIGHT** (red center)
5. **BACK** (blue center)
6. **BOTTOM** (yellow center)

### Step 3: Get Solution
- After capturing all 6 faces, solution appears automatically
- Solution shown in standard notation (R, U, L, D, F, B)
- Move count displayed

### Step 4: Solve Your Cube!
- Follow the moves shown
- R = Right clockwise
- R' = Right counter-clockwise
- R2 = Right 180°

## Keyboard Controls

| Key | Action |
|-----|--------|
| **SPACE** | Capture current face |
| **R** | Reset and start over |
| **X** | Exit application |

## Status Messages

### 🔵 Blue = Success
"Captured! Move to the next side"

### 🔴 Red = Error
- "Only detected X stickers! Need 9. Improve lighting and try again."
- "Detected X stickers! Need exactly 9. Adjust cube position."

### 🟢 Green = Solution Ready
Shows the move sequence and count

## Troubleshooting

### Camera doesn't open
- Check camera permissions
- Close other apps using camera
- Try changing camera index in `VideoCap.java` line 20

### Detection fails
- Improve lighting
- Remove shadows
- Hold cube steady
- Ensure cube colors are distinct

### "Invalid cube configuration"
- Retake photos with better lighting
- Ensure you scanned in correct order
- Check all 9 stickers were detected each time

## What the Algorithm Does

1. **Detects stickers** using contour detection
2. **Extracts colors** using LAB color space
3. **Clusters colors** using k-means (6 clusters)
4. **Solves cube** using 4-stage lookup table algorithm
5. **Optimizes moves** by combining consecutive turns

## Expected Performance

- **Detection**: ~30 FPS
- **Solving**: ~5 seconds
- **Solution length**: ≤28 moves (average ~25)

Enjoy solving your cube! 🎲
