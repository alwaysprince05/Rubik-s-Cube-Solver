# Project Overview - Rubik's Cube Solver

## Visual Layout

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         YOUR DESKTOP                                     │
│                                                                          │
│  ┌──────────────────────────┐    ┌─────────────────────────────────┐  │
│  │   CAMERA WINDOW          │    │   CUBE DISPLAY WINDOW           │  │
│  │   (650x490)              │    │   (800x700)                     │  │
│  │                          │    │                                 │  │
│  │  ┌────────────────────┐  │    │  Status: "Captured! Next side" │  │
│  │  │                    │  │    │                                 │  │
│  │  │  [Live Video Feed] │  │    │      ┌─┬─┬─┐                   │  │
│  │  │                    │  │    │      │W│W│W│  ← TOP             │  │
│  │  │  With white boxes  │  │    │      ├─┼─┼─┤                   │  │
│  │  │  around detected   │  │    │      │W│W│W│                   │  │
│  │  │  stickers          │  │    │      ├─┼─┼─┤                   │  │
│  │  │                    │  │    │      │W│W│W│                   │  │
│  │  │                    │  │    │  ┌─┬─┬─┼─┼─┼─┬─┬─┬─┬─┬─┬─┐     │  │
│  │  └────────────────────┘  │    │  │O│O│O│G│G│G│R│R│R│B│B│B│     │  │
│  │                          │    │  ├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤     │  │
│  │  Press SPACE to capture  │    │  │O│O│O│G│G│G│R│R│R│B│B│B│     │  │
│  │                          │    │  ├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤     │  │
│  └──────────────────────────┘    │  │O│O│O│G│G│G│R│R│R│B│B│B│     │  │
│                                   │  └─┴─┴─┼─┼─┼─┴─┴─┴─┴─┴─┴─┘     │  │
│                                   │      ┌─┼─┼─┐                   │  │
│                                   │      │Y│Y│Y│  ← BOTTOM         │  │
│                                   │      ├─┼─┼─┤                   │  │
│                                   │      │Y│Y│Y│                   │  │
│                                   │      ├─┼─┼─┤                   │  │
│                                   │      │Y│Y│Y│                   │  │
│                                   │      └─┴─┴─┘                   │  │
│                                   │                                 │  │
│                                   │  Instructions:                  │  │
│                                   │  • SPACE: Capture               │  │
│                                   │  • R: Reset                     │  │
│                                   │  • X: Exit                      │  │
│                                   │                                 │  │
│                                   │  Solution:                      │  │
│                                   │  R2 U' R B2 U' R2 U' B R2...    │  │
│                                   │  Number of moves: 25            │  │
│                                   └─────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
```

## How It Works - Step by Step

### 1. Application Starts
```
MainFrame.main()
    ↓
Creates two windows
    ↓
Initializes camera (VideoCap)
    ↓
Starts video thread (30 FPS)
```

### 2. Live Detection (Continuous)
```
Every 30ms:
    ↓
Capture frame from camera
    ↓
Convert to grayscale → Blur → Edge detection
    ↓
Find contours (shapes)
    ↓
Filter for square shapes (squareness 0.7-0.9)
    ↓
Draw white rectangles around detected stickers
    ↓
Display in camera window
```

### 3. User Presses SPACE
```
Capture triggered
    ↓
Check: Are there exactly 9 stickers?
    ↓
YES: Extract color from each sticker
    ↓
Sort stickers by position (top-left to bottom-right)
    ↓
Add to colorArray[0-53]
    ↓
Update display window with colors
    ↓
Show success message (blue)
```

### 4. After 6 Faces Captured
```
All 54 stickers captured
    ↓
Convert RGB → LAB color space
    ↓
K-means clustering (6 colors)
    ↓
Map to cube notation (0-5)
    ↓
Normalize orientation (white top, green front)
    ↓
Apply 4-stage solving algorithm
    ↓
Optimize move sequence
    ↓
Display solution (green)
```

## File Structure & Responsibilities

```
src/
├── MainFrame.java          ⭐ Entry point, keyboard controls
│   └── Creates windows, handles user input
│
├── VideoCap.java           📹 Camera management
│   └── Opens camera, provides frames
│
├── AnalyzeFrame.java       🔍 Image processing (BIGGEST FILE)
│   ├── Contour detection
│   ├── Color extraction
│   ├── LAB conversion
│   ├── K-means clustering
│   └── Triggers solving
│
├── DisplayWindow.java      🖼️ Cube visualization
│   └── 54 buttons in cube net layout
│
├── SolveCube.java          🧩 Solving algorithm
│   ├── Loads lookup tables
│   ├── 4-stage solving
│   └── Move optimization
│
├── TableGenerator.java     🔄 Cube manipulation
│   └── Apply moves, hash states
│
├── Mat2Image.java          🖼️ OpenCV → Java conversion
├── ColorAndIndex.java      📊 Color clustering data
└── SortColors.java         📍 Position sorting

Data Files:
├── stage0.txt              📋 Lookup table (2x2x2 block)
├── stage1.txt              📋 Lookup table (2x2x3 block)
├── stage2.txt              📋 Lookup table (bow-tie)
├── stage3.txt              📋 Lookup table (final)
└── testMoves.txt           📋 Starting sequences
```

## Color Detection Process

```
Camera RGB → LAB Color Space → K-means Clustering → Cube Colors

Example:
RGB(255,255,255) → LAB(100, 0, 0)   → Cluster 0 → White
RGB(255,165,0)   → LAB(75, 23, 78)  → Cluster 1 → Orange
RGB(0,255,0)     → LAB(88, -86, 83) → Cluster 2 → Green
RGB(255,0,0)     → LAB(53, 80, 67)  → Cluster 3 → Red
RGB(0,0,255)     → LAB(32, 79, -108)→ Cluster 4 → Blue
RGB(255,255,0)   → LAB(97, -21, 94) → Cluster 5 → Yellow
```

## Solving Algorithm (4 Stages)

```
Stage 0: Solve 2x2x2 block (white-orange-green corner)
    ↓
Stage 1: Extend to 2x2x3 block
    ↓
Stage 2: Create bow-tie pattern
    ↓
Stage 3: Complete the cube
    ↓
Optimize: Combine consecutive moves (R R → R2)
```

## Performance Metrics

| Metric | Value |
|--------|-------|
| Frame Rate | 30 FPS |
| Detection Time | ~33ms per frame |
| Solving Time | ~5 seconds |
| Average Solution | 25 moves |
| Max Solution | 28 moves |
| Memory Usage | ~100MB |

## What Makes This Project Cool? 🌟

1. **Computer Vision**: Real-time sticker detection using OpenCV
2. **Color Science**: LAB color space for accurate color matching
3. **Machine Learning**: K-means clustering for color classification
4. **Algorithm**: Custom 4-stage solving with lookup tables
5. **Optimization**: Move sequence optimization
6. **UI**: Dual-window interface with live feedback

## Technologies Used

- **Java 8+**: Core language
- **OpenCV 3.3+**: Computer vision library
- **Swing**: GUI framework
- **LAB Color Space**: Perceptual color matching
- **K-means**: Unsupervised clustering
- **Lookup Tables**: Pre-computed solutions

Ready to see it in action? Run `MainFrame.java` in Eclipse! 🚀
