# Requirements Document

## Introduction

This document outlines the requirements for refactoring and improving an existing 3x3x3 Rubik's Cube solver application. The system uses OpenCV for computer vision to capture cube state via webcam, processes the color data, and generates an optimal solution using a custom solving algorithm. The refactoring aims to fix existing bugs, improve code quality, modernize the codebase, and enhance user experience while maintaining the core functionality.

## Glossary

- **System**: The Rubik's Cube Solver application
- **Cube State**: The current configuration of colors on all six faces of the Rubik's cube
- **Sticker**: An individual colored square on the cube (54 total)
- **Face**: One side of the cube containing 9 stickers
- **Solution**: A sequence of moves that solves the cube from its current state
- **Capture**: The process of taking a photo of one face of the cube
- **LAB Color Space**: A color space designed to approximate human vision, used for accurate color detection
- **Contour**: A boundary detected in an image, used to identify individual stickers
- **Lookup Table**: Pre-computed solution data stored in stage files (stage0.txt through stage3.txt)

## Requirements

### Requirement 1

**User Story:** As a user, I want the application to reliably detect all 9 stickers on each cube face, so that I can successfully capture my cube's state without repeated attempts.

#### Acceptance Criteria

1. WHEN the user captures a cube face THEN the System SHALL detect exactly 9 square-shaped contours
2. WHEN lighting conditions vary moderately THEN the System SHALL maintain detection accuracy above 85%
3. IF the System detects fewer or more than 9 stickers THEN the System SHALL display a clear error message and prompt for recapture
4. WHEN contour detection parameters are applied THEN the System SHALL filter shapes based on squareness ratio between 0.7 and 0.9
5. WHEN the user positions the cube within camera view THEN the System SHALL provide visual feedback showing detected sticker boundaries

### Requirement 2

**User Story:** As a user, I want accurate color recognition across different lighting conditions, so that the solver produces correct solutions.

#### Acceptance Criteria

1. WHEN the System processes captured sticker colors THEN the System SHALL convert RGB values to LAB color space for comparison
2. WHEN the System identifies sticker colors THEN the System SHALL use k-means clustering with 6 color centers
3. WHEN color centers are determined THEN the System SHALL use CIE2000 color difference formula for accurate matching
4. WHEN the user captures all 6 faces THEN the System SHALL validate that each color appears exactly 9 times
5. IF color detection produces an invalid cube configuration THEN the System SHALL notify the user and request recapture

### Requirement 3

**User Story:** As a user, I want clear instructions and feedback during the capture process, so that I know which face to scan next and whether my capture was successful.

#### Acceptance Criteria

1. WHEN the application starts THEN the System SHALL display the face scanning order: TOP, LEFT, FRONT, RIGHT, BACK, BOTTOM
2. WHEN a face is successfully captured THEN the System SHALL display a confirmation message and indicate the next face to scan
3. WHEN a capture fails THEN the System SHALL display the specific reason for failure
4. WHEN the user views the display window THEN the System SHALL show the current cube state with colored buttons representing each sticker
5. WHEN all faces are captured THEN the System SHALL display the complete cube visualization before solving

### Requirement 4

**User Story:** As a user, I want the solver to generate optimal solutions quickly, so that I can solve my cube efficiently.

#### Acceptance Criteria

1. WHEN the System receives a valid cube state THEN the System SHALL generate a solution within 5 seconds
2. WHEN the System calculates a solution THEN the System SHALL produce move sequences of 28 moves or fewer
3. WHEN the System optimizes move sequences THEN the System SHALL combine consecutive moves of the same face
4. WHEN the System applies lookup tables THEN the System SHALL use 4-stage solving algorithm with pre-computed tables
5. WHEN the solution is complete THEN the System SHALL display the move sequence and total move count

### Requirement 5

**User Story:** As a developer, I want clean, maintainable code with proper error handling, so that the application is reliable and easy to extend.

#### Acceptance Criteria

1. WHEN exceptions occur during file operations THEN the System SHALL handle IOException gracefully with user-friendly messages
2. WHEN the System encounters invalid cube states THEN the System SHALL validate input before attempting to solve
3. WHEN resources are allocated THEN the System SHALL properly release camera and file resources
4. WHEN the application terminates THEN the System SHALL clean up OpenCV resources and close all windows
5. WHEN code is organized THEN the System SHALL follow single responsibility principle with clear separation of concerns

### Requirement 6

**User Story:** As a user, I want keyboard controls that are intuitive and responsive, so that I can efficiently operate the application.

#### Acceptance Criteria

1. WHEN the user presses SPACE THEN the System SHALL capture the current camera frame
2. WHEN the user presses R THEN the System SHALL reset all captured data and restart the scanning process
3. WHEN the user presses X THEN the System SHALL exit the application cleanly
4. WHEN keyboard input is received THEN the System SHALL respond within 100 milliseconds
5. WHEN the main window has focus THEN the System SHALL accept keyboard input

### Requirement 7

**User Story:** As a user, I want a modern, user-friendly interface, so that the application is pleasant to use.

#### Acceptance Criteria

1. WHEN the application launches THEN the System SHALL display two windows: camera view and cube state display
2. WHEN the camera window updates THEN the System SHALL maintain at least 30 frames per second
3. WHEN stickers are detected THEN the System SHALL draw white rectangles around detected areas in real-time
4. WHEN the display window shows the cube THEN the System SHALL arrange stickers in standard cube net layout
5. WHEN solution text is displayed THEN the System SHALL use readable fonts with appropriate sizing

### Requirement 8

**User Story:** As a developer, I want comprehensive documentation, so that new contributors can understand and extend the project.

#### Acceptance Criteria

1. WHEN the README is viewed THEN the System documentation SHALL include clear setup instructions for all supported platforms
2. WHEN the README describes features THEN the System documentation SHALL explain the solving algorithm approach
3. WHEN code files are reviewed THEN the System SHALL include JavaDoc comments for public methods
4. WHEN the project structure is examined THEN the System documentation SHALL explain the purpose of each source file
5. WHEN dependencies are listed THEN the System documentation SHALL specify exact version requirements

### Requirement 9

**User Story:** As a user, I want the application to work consistently across Windows, macOS, and Linux, so that I can use it on my preferred platform.

#### Acceptance Criteria

1. WHEN the System runs on Windows THEN the System SHALL detect and use the correct OpenCV native libraries
2. WHEN the System runs on macOS THEN the System SHALL detect and use the correct OpenCV native libraries
3. WHEN the System runs on Linux THEN the System SHALL detect and use the correct OpenCV native libraries
4. WHEN file paths are constructed THEN the System SHALL use platform-independent path separators
5. WHEN the camera is accessed THEN the System SHALL enumerate available cameras and select the default device

### Requirement 10

**User Story:** As a user, I want the application to handle edge cases gracefully, so that unexpected situations don't crash the program.

#### Acceptance Criteria

1. WHEN no camera is detected THEN the System SHALL display an error message and exit gracefully
2. WHEN lookup table files are missing THEN the System SHALL display a clear error indicating which files are required
3. WHEN the camera feed is interrupted THEN the System SHALL attempt to reconnect or notify the user
4. WHEN memory is insufficient THEN the System SHALL handle OutOfMemoryError and display appropriate message
5. WHEN invalid cube configurations are detected THEN the System SHALL explain why the configuration is invalid
