# Rubik's Cube Solver

An intelligent 3x3x3 Rubik's Cube solver that uses computer vision to capture cube state via webcam and generates optimal solutions averaging 25 moves or less.

## 🎨 Visual Demo

**[Click here to view the interactive demo](file:///Users/princemaurya/3x3x3-Rubiks-Cube-Solver/VISUAL_DEMO.html)**

See what the application looks like with a visual mockup showing the camera feed and cube display windows.

## Features

- **Computer Vision Detection**: Automatically detects and captures cube colors using OpenCV
- **Optimal Solving**: Custom algorithm generates solutions in 28 moves or fewer
- **Real-time Feedback**: Visual indicators show detected stickers and capture status
- **Cross-platform**: Works on Windows, macOS, and Linux
- **Interactive UI**: Dual-window interface for camera feed and cube visualization

## System Requirements

- Java 8 or higher
- OpenCV 3.3.0 or higher
- Webcam
- 1GB disk space

## Quick Start

### 1. Install OpenCV

**Windows:**
1. Download OpenCV from [opencv.org](https://opencv.org/releases/)
2. Extract the archive
3. Locate the `build` folder in the extracted directory

**macOS:**
```bash
brew install opencv
```

**Linux:**
```bash
sudo apt-get update
sudo apt-get install libopencv-dev
```

### 2. Configure Eclipse

1. Open Eclipse and go to **Window → Preferences**
2. Navigate to **Java → User Libraries**
3. Click **New** and name it `OpenCV-3.3.0`
4. Select the library and click **Add External JARs**
5. Navigate to `opencv/build/java/` and select `opencv-330.jar`
6. Click on **Native Library Location** and set it to `opencv/build/lib`
7. Click **OK** to save

### 3. Import Project

1. Clone this repository
2. In Eclipse: **File → Import → Existing Projects into Workspace**
3. Select the project directory
4. Right-click project → **Build Path → Add Libraries → User Library**
5. Select your OpenCV library and click **Finish**

### 4. Run the Application

1. Run `MainFrame.java`
2. Two windows will appear: camera feed and cube display
3. Follow the on-screen instructions

## How to Use

### Keyboard Controls

- **SPACE**: Capture current face
- **R**: Reset and start over
- **X**: Exit application

### Scanning Your Cube

Scan faces in this order: **TOP → LEFT → FRONT → RIGHT → BACK → BOTTOM**

**Tips for best results:**
- Use consistent, bright lighting
- Avoid shadows on the cube
- Hold the cube steady when capturing
- Ensure all 9 stickers are visible

### Reading the Solution

After scanning all faces, the solution appears in standard cube notation:
- **R**: Right face clockwise
- **R'**: Right face counter-clockwise  
- **R2**: Right face 180 degrees
- **U, D, L, F, B**: Up, Down, Left, Front, Back faces

Learn more about [cube notation](https://ruwix.com/the-rubiks-cube/notation/).

## Project Structure

```
src/
├── MainFrame.java       # Main application window and controls
├── DisplayWindow.java   # Cube visualization window
├── VideoCap.java        # Camera capture management
├── AnalyzeFrame.java    # Image processing and color detection
├── SolveCube.java       # Solving algorithm
├── TableGenerator.java  # Cube state manipulation
├── Mat2Image.java       # OpenCV to Java image conversion
├── ColorAndIndex.java   # Color clustering data structure
└── SortColors.java      # Sticker position sorting
```

## Algorithm Overview

The solver uses a 4-stage algorithm with pre-computed lookup tables:

1. **Stage 0**: Solve 2x2x2 block
2. **Stage 1**: Extend to 2x2x3 block
3. **Stage 2**: Solve bow-tie pattern
4. **Stage 3**: Complete the cube

Solutions are optimized by combining consecutive moves and testing multiple starting sequences.

## Troubleshooting

**Camera not detected:**
- Check camera permissions
- Try changing camera index in `VideoCap.java` (line 20)
- Ensure no other application is using the camera

**Detection fails:**
- Improve lighting conditions
- Reduce glare and shadows
- Hold cube closer to camera
- Ensure cube colors are distinct

**"False cube" error:**
- Retake photos with better lighting
- Ensure you scanned faces in correct order
- Check that all stickers were detected (9 per face)

## Contributing

Contributions are welcome! Feel free to:
- Report bugs
- Suggest features
- Improve detection accuracy
- Enhance the UI
- Add tests

## License

MIT License - feel free to use and modify for your projects.

## Acknowledgments

This project builds upon concepts from cube solving algorithms and computer vision techniques.
