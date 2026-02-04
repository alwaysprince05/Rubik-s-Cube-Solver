# Design Document

## Overview

This design refactors the existing Rubik's Cube solver to address code quality issues, improve reliability, and enhance user experience. The system maintains its core architecture of computer vision-based cube state capture and lookup table-based solving, while introducing better error handling, cleaner code organization, and improved user feedback.

The refactoring focuses on:
- Fixing bugs in color detection and contour processing
- Improving exception handling and resource management
- Modernizing the UI with better feedback mechanisms
- Cleaning up code structure and removing dead code
- Enhancing documentation and maintainability

## Architecture

The application follows a layered architecture:

### Presentation Layer
- **MainFrame**: Main application window with camera feed and keyboard controls
- **DisplayWindow**: Secondary window showing cube state visualization and solution output

### Processing Layer
- **VideoCap**: Camera capture and frame management
- **AnalyzeFrame**: Image processing, contour detection, and color analysis
- **Mat2Image**: OpenCV Mat to BufferedImage conversion

### Business Logic Layer
- **SolveCube**: Cube solving algorithm and move optimization
- **TableGenerator**: Cube state manipulation and hashing for lookup tables

### Data Layer
- **ColorAndIndex**: Color data structure for k-means clustering
- **SortColors**: Sticker sorting by position
- Lookup table files (stage0.txt through stage3.txt, testMoves.txt)

## Components and Interfaces

### MainFrame
**Responsibilities:**
- Initialize application windows
- Capture keyboard input
- Coordinate between VideoCap and DisplayWindow
- Manage application lifecycle

**Key Methods:**
```java
public void runMainTasks() // Initialize UI and event listeners
public void callDisplayWindow() // Launch cube display window
public void paint(Graphics g) // Render camera feed
```

**Improvements:**
- Add proper resource cleanup in shutdown hook
- Extract keyboard handling to separate class
- Add configuration for camera selection

### VideoCap
**Responsibilities:**
- Manage VideoCapture instance
- Provide frames to MainFrame
- Coordinate with AnalyzeFrame for processing

**Key Methods:**
```java
public BufferedImage getOneFrame() // Get current frame with or without processing
```

**Improvements:**
- Add camera availability check
- Implement proper VideoCapture release
- Add frame rate configuration

### AnalyzeFrame
**Responsibilities:**
- Detect stickers using contour analysis
- Extract and sort colors from detected regions
- Convert colors to LAB space and perform k-means clustering
- Generate cube state and request solution

**Key Methods:**
```java
public Mat captureFrame(Mat frame, boolean captured) // Process frame
private void findContours(Mat dilated, List<MatOfPoint> finalContours) // Detect stickers
private void getColors(Mat img, Rect roi) // Extract color from region
private void k_means(double[][] labArray, ColorAndIndex[] colors) // Cluster colors
private double[] RGB2Lab(Color rgb) // Color space conversion
```

**Improvements:**
- Fix squareness calculation threshold
- Add validation for 9-sticker detection
- Improve error messages for failed captures
- Add configurable detection parameters
- Fix memory leaks in Mat objects

### SolveCube
**Responsibilities:**
- Load lookup tables from files
- Map cube orientation to standard configuration
- Apply 4-stage solving algorithm
- Optimize move sequences

**Key Methods:**
```java
public String solver(SolveCube c, TableGenerator g) // Main solving entry point
private String solve(TableGenerator obj, TableGenerator saved, String testMoves, boolean print) // Execute solve
private String readSingleTurn(String solution) // Optimize move sequence
private String combineTurns(String first, String second) // Merge consecutive moves
public String mapOrientation(byte[][] cube, SolveCube c) // Normalize cube orientation
```

**Improvements:**
- Add file existence validation
- Improve error handling for invalid cubes
- Add progress callback for long-running solves
- Extract move optimization to separate class
- Add unit tests for move combination logic

### DisplayWindow
**Responsibilities:**
- Visualize current cube state
- Display capture status and instructions
- Show solution and move count

**Key Methods:**
```java
public void displayCube() // Create and show window
public void setColor(Color color, JButton button) // Update sticker color
private void layDownButtons(JButton[] buttons, ...) // Position buttons in cube net layout
```

**Improvements:**
- Use modern Swing components
- Add better layout management
- Improve text readability
- Add color-blind friendly mode option

## Data Models

### Cube Representation
```java
byte[][] cube = new byte[6][9]
// 6 faces, 9 stickers each
// Values 0-5 represent colors: White, Orange, Green, Red, Blue, Yellow
// Standard orientation: White top, Green front
```

### Color Data
```java
class ColorAndIndex {
    double[] labArray;      // LAB color space values
    int index;              // Position in 54-sticker array
    double distance;        // Distance to cluster center
    String colorString;     // Human-readable color name
    int numberRepresentation; // 0-5 color code
}
```

### Sticker Position
```java
class SortColors implements Comparable<SortColors> {
    int x, y;              // Screen coordinates
    Color color;           // RGB color
    // Sorted by y-coordinate, then x-coordinate
}
```

## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system-essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*


### Property Reflection

After reviewing all testable properties from the prework, several can be consolidated:
- Properties about error messages (3.3, 10.5) can be combined into one comprehensive error handling property
- Properties about UI state consistency (3.4, 7.3, 7.4) relate to the same concern of display accuracy
- Platform-specific properties (9.1, 9.2, 9.3) can be combined into one cross-platform property
- Resource cleanup properties (5.3, 5.4) can be merged into comprehensive resource management

The following properties provide unique validation value:

Property 1: Contour detection count
*For any* captured frame with exactly 9 square stickers, the contour detection algorithm should identify exactly 9 contours.
**Validates: Requirements 1.1**

Property 2: Squareness filtering
*For any* detected contour, if its squareness ratio is between 0.7 and 0.9 and area >= 900, it should be included in final contours; otherwise it should be filtered out.
**Validates: Requirements 1.4**

Property 3: Visual feedback consistency
*For any* detected contour, a white rectangle should be drawn at the contour's bounding box coordinates when not in capture mode.
**Validates: Requirements 1.5, 7.3**

Property 4: RGB to LAB conversion accuracy
*For any* RGB color value, converting to LAB color space and applying color distance calculations should produce consistent results within numerical precision.
**Validates: Requirements 2.1**

Property 5: K-means produces 6 clusters
*For any* set of 54 sticker colors from a valid cube, k-means clustering should produce exactly 6 color clusters.
**Validates: Requirements 2.2**

Property 6: CIE2000 distance formula
*For any* two LAB color values, the calculated distance using de_CIE2000 should match the standard CIE2000 formula output.
**Validates: Requirements 2.3**

Property 7: Color count invariant
*For any* valid cube configuration, each of the 6 colors should appear exactly 9 times in the 54-sticker array.
**Validates: Requirements 2.4**

Property 8: Display state consistency
*For any* cube state, the colors displayed in the UI buttons should exactly match the internal colorArray representation.
**Validates: Requirements 3.4, 7.4**

Property 9: Solution length constraint
*For any* valid cube state, the generated solution should contain 28 moves or fewer.
**Validates: Requirements 4.2**

Property 10: Move optimization combines consecutive moves
*For any* move sequence containing consecutive moves of the same face (e.g., "R R" or "U U'"), the optimization should combine them into a single move or eliminate them if they cancel.
**Validates: Requirements 4.3**

Property 11: Invalid cube rejection
*For any* cube state that violates cube validity rules (wrong color counts, impossible edge/corner combinations), the validation should reject it before attempting to solve.
**Validates: Requirements 5.2**

Property 12: Resource cleanup
*For any* execution path (normal or exceptional), allocated resources (VideoCapture, Mat objects, file handles) should be properly released before program termination.
**Validates: Requirements 5.3, 5.4**

Property 13: Reset restores initial state
*For any* cube state during scanning, pressing R should reset the state to be equivalent to the initial application state (currentIndex = 0, colorArray cleared).
**Validates: Requirements 6.2**

Property 14: Cross-platform file paths
*For any* file operation, the system should use platform-independent path construction that works on Windows, macOS, and Linux.
**Validates: Requirements 9.4**

Property 15: Error messages for all failure modes
*For any* error condition (missing files, invalid cube, no camera, etc.), the system should display a specific, actionable error message explaining the problem.
**Validates: Requirements 3.3, 10.5**

## Error Handling

### Exception Hierarchy
- **IOException**: File operations (lookup tables, configuration)
- **IllegalStateException**: Invalid cube states, missing resources
- **RuntimeException**: Unexpected errors during processing

### Error Recovery Strategies

**Camera Initialization Failure:**
- Check if camera index 0 is available
- Try alternative camera indices (1-4)
- Display error with instructions to check camera permissions
- Exit gracefully if no camera found

**Lookup Table Loading Failure:**
- Validate all required files exist at startup
- Display specific missing file names
- Provide download/setup instructions
- Prevent solve attempts without tables

**Color Detection Failure:**
- Validate sticker count before processing
- Check color distribution for validity
- Allow user to retry capture
- Provide lighting/positioning tips

**Invalid Cube Configuration:**
- Validate color counts (9 of each)
- Check for impossible configurations
- Display which validation rule failed
- Allow full reset to restart

**Memory Issues:**
- Release Mat objects immediately after use
- Implement proper dispose() calls
- Monitor memory usage in long sessions
- Provide clear error if OutOfMemoryError occurs

## Testing Strategy

### Unit Testing
The refactored code will include JUnit 5 tests for:

**Color Conversion:**
- RGB to LAB conversion accuracy
- LAB distance calculations
- Edge cases (black, white, primary colors)

**Move Optimization:**
- Single move combinations (R + R = R2)
- Inverse cancellations (R + R' = empty)
- Complex sequences with multiple faces
- Edge cases (empty sequences, single moves)

**Cube Validation:**
- Valid cube configurations
- Invalid color counts
- Impossible edge/corner combinations

**Contour Detection:**
- Squareness calculation
- Area filtering
- Hierarchy validation

### Property-Based Testing
We will use **JUnit-Quickcheck** for property-based testing with a minimum of 100 iterations per property.

Each property-based test will:
- Be tagged with a comment referencing the design document property
- Use format: `// Feature: rubiks-cube-solver-refactor, Property {number}: {property_text}`
- Generate random valid inputs within the domain
- Verify the property holds for all generated inputs

**Property Test Examples:**

1. **Contour Squareness**: Generate shapes with various aspect ratios and verify filtering
2. **Color Count Invariant**: Generate valid cube states and verify each color appears 9 times
3. **Move Optimization**: Generate random move sequences and verify optimization rules
4. **RGB-LAB Round Trip**: Generate random RGB colors and verify conversion consistency
5. **Solution Length**: Generate random valid cubes and verify solution <= 28 moves

### Integration Testing
- End-to-end cube capture and solve workflow
- Camera initialization and frame capture
- UI updates and user interactions
- File I/O and resource management

### Manual Testing
- Test on different platforms (Windows, macOS, Linux)
- Test with different cameras and lighting conditions
- Verify UI responsiveness and visual feedback
- Test keyboard controls and window management

## Implementation Notes

### Code Quality Improvements

**Remove Dead Code:**
- Unused imports and variables
- Commented-out code blocks
- TestEnvironment.java (if unused)
- Imshow.java (if redundant with Mat2Image)

**Fix Warnings:**
- Suppress only necessary warnings with explanations
- Fix raw type usage in ArrayList
- Add @Override annotations consistently
- Remove unused variables (counter, etc.)

**Improve Naming:**
- Rename `takeFrame` to `frameAnalyzer`
- Rename `allColors` to `displayColors`
- Use descriptive variable names instead of single letters
- Follow Java naming conventions consistently

**Extract Magic Numbers:**
- Define constants for detection parameters (0.7, 0.9, 900)
- Define constants for color indices (0-5)
- Define constants for cube dimensions (6 faces, 9 stickers)
- Define constants for timing (5 second timeout, 30ms refresh)

### Performance Optimizations

**Memory Management:**
- Implement try-with-resources for Mat objects
- Release Mat objects immediately after use
- Reuse Mat objects where possible
- Clear collections after processing

**Frame Processing:**
- Process frames only when needed
- Skip processing during non-capture mode
- Optimize contour detection parameters
- Cache converted images

**Solving Algorithm:**
- Maintain current 5-second timeout
- Consider parallel testing of move sequences
- Optimize move combination algorithm
- Cache lookup table data

### UI/UX Improvements

**Better Feedback:**
- Progress indicator during solving
- Clear face-by-face instructions
- Visual confirmation of successful captures
- Animated transitions between states

**Error Messages:**
- Specific, actionable error text
- Suggestions for fixing common issues
- Visual indicators (color-coded messages)
- Help button with troubleshooting guide

**Accessibility:**
- Keyboard shortcuts displayed in UI
- High contrast mode option
- Larger text size option
- Color-blind friendly palette option

## Dependencies

- **OpenCV 3.3.0+**: Computer vision and image processing
- **Java 8+**: Core language features
- **Swing**: GUI framework
- **JUnit 5**: Unit testing framework
- **JUnit-Quickcheck**: Property-based testing framework

## Migration Path

The refactoring will be done incrementally:

1. **Phase 1**: Fix critical bugs (contour detection, color processing, error handling)
2. **Phase 2**: Improve code quality (remove dead code, fix warnings, improve naming)
3. **Phase 3**: Add tests (unit tests, property tests, integration tests)
4. **Phase 4**: Enhance UI/UX (better feedback, error messages, accessibility)
5. **Phase 5**: Update documentation (README, JavaDoc, setup guides)

Each phase will maintain backward compatibility with existing functionality.
