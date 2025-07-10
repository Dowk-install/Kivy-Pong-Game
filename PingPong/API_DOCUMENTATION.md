# PingPong Game API Documentation

## Overview

The PingPong project is a classic Pong game implementation built with the Kivy framework. It features two paddles controlled by touch input, a bouncing ball, and score tracking. The game is designed to run on both desktop and mobile platforms (with Android APK generation support).

## Project Structure

```
PingPong/
├── main.py           # Main game logic and classes
├── pong.kv          # Kivy UI layout definitions
└── KivyToAndroid.txt # Android deployment instructions
```

## Classes and Components

### 1. PongPaddle

A widget representing a game paddle that can interact with the ball.

**Inheritance:** `Widget` → `PongPaddle`

#### Properties

- **`score`** (`NumericProperty`): The current score for this paddle
  - Type: `int`
  - Default: `0`
  - Usage: Automatically increments when opposing player misses the ball

#### Methods

##### `bounce_ball(ball)`

Handles collision detection and ball bouncing mechanics.

**Parameters:**
- `ball` (`PongBall`): The ball object to check collision against

**Returns:** `None`

**Behavior:**
- Checks if the paddle collides with the ball using `collide_widget()`
- If collision detected, reverses and slightly increases ball's horizontal velocity by factor of 1.1

**Example:**
```python
paddle = PongPaddle()
ball = PongBall()
paddle.bounce_ball(ball)  # Will bounce ball if collision detected
```

### 2. PongBall

A widget representing the game ball with physics-based movement.

**Inheritance:** `Widget` → `PongBall`

#### Properties

- **`velocity_x`** (`NumericProperty`): Horizontal velocity component
  - Type: `float`
  - Default: `0`
  - Units: pixels per frame

- **`velocity_y`** (`NumericProperty`): Vertical velocity component
  - Type: `float`
  - Default: `0`
  - Units: pixels per frame

- **`velocity`** (`ReferenceListProperty`): Combined velocity vector
  - Type: `tuple(float, float)`
  - References: `(velocity_x, velocity_y)`

#### Methods

##### `move()`

Updates the ball's position based on its current velocity.

**Parameters:** None

**Returns:** `None`

**Behavior:**
- Calculates new position using vector addition: `new_pos = velocity + current_pos`
- Updates the widget's `pos` property

**Example:**
```python
ball = PongBall()
ball.velocity_x = 5
ball.velocity_y = 3
ball.move()  # Ball position updated by (5, 3) pixels
```

### 3. PongGame

The main game widget that orchestrates all game mechanics and logic.

**Inheritance:** `Widget` → `PongGame`

#### Properties

- **`ball`** (`ObjectProperty`): Reference to the game ball
  - Type: `PongBall`
  - Default: `None`
  - Bound to: `pong_ball` in `.kv` file

- **`player1`** (`ObjectProperty`): Reference to left paddle
  - Type: `PongPaddle`
  - Default: `None`
  - Bound to: `player_left` in `.kv` file

- **`player2`** (`ObjectProperty`): Reference to right paddle
  - Type: `PongPaddle`
  - Default: `None`
  - Bound to: `player_right` in `.kv` file

#### Methods

##### `serve_ball()`

Initializes or resets the ball with a random direction.

**Parameters:** None

**Returns:** `None`

**Behavior:**
- Sets ball velocity to a vector with magnitude 4
- Random rotation between 0-360 degrees
- Called at game start and after scoring

**Example:**
```python
game = PongGame()
game.serve_ball()  # Ball starts moving in random direction
```

##### `update(dt)`

Main game loop update function called every frame.

**Parameters:**
- `dt` (`float`): Delta time since last update (typically 1/60 seconds)

**Returns:** `None`

**Behavior:**
1. Moves the ball
2. Handles top/bottom wall collisions (reverses Y velocity)
3. Handles left/right scoring (increments scores, reverses X velocity)
4. Processes paddle-ball collisions

**Example:**
```python
from kivy.clock import Clock

game = PongGame()
Clock.schedule_interval(game.update, 1.0/60.0)  # 60 FPS
```

##### `on_touch_move(touch)`

Handles touch input for paddle movement.

**Parameters:**
- `touch` (`Touch`): Kivy touch event object with position information

**Returns:** `None`

**Behavior:**
- Left quarter of screen (x < width/4): Controls player1 (left paddle)
- Right quarter of screen (x > width*3/4): Controls player2 (right paddle)
- Paddle center_y follows touch.y position

**Example:**
```python
# Called automatically by Kivy when user touches and drags
# No manual implementation needed
```

### 4. PongApp

The main application class that sets up and runs the game.

**Inheritance:** `App` → `PongApp`

#### Methods

##### `build()`

Creates and configures the game instance.

**Parameters:** None

**Returns:** `PongGame` - The root widget for the application

**Behavior:**
1. Creates new `PongGame` instance
2. Serves the initial ball
3. Schedules game updates at 60 FPS
4. Returns game widget as root

**Example:**
```python
app = PongApp()
app.run()  # Starts the game application
```

## UI Components (Kivy Layout)

### PongBall Visual

**Size:** 50x50 pixels  
**Shape:** Circular (Ellipse)  
**Rendering:** Canvas drawing with position and size bound to widget properties

### PongPaddle Visual

**Size:** 25x200 pixels  
**Shape:** Rectangular  
**Rendering:** Canvas Rectangle with position and size bound to widget properties

### PongGame Layout

**Background:** Center dividing line (10px wide)  
**Scoreboard:** Two large labels (font size 70) positioned at top-left and top-right  
**Ball Position:** Starts at center of game area  
**Paddle Positions:**
- Left paddle: At left edge, vertically centered
- Right paddle: At right edge, vertically centered

## Usage Examples

### Basic Game Setup

```python
from kivy.app import App
from kivy.clock import Clock

# Create and run the game
app = PongApp()
app.run()
```

### Custom Game Configuration

```python
# Create game with custom settings
game = PongGame()

# Set initial scores
game.player1.score = 5
game.player2.score = 3

# Start the ball
game.serve_ball()

# Run at 30 FPS instead of 60
Clock.schedule_interval(game.update, 1.0/30.0)
```

### Accessing Game State

```python
# Get current game state
current_scores = (game.player1.score, game.player2.score)
ball_position = game.ball.pos
ball_velocity = game.ball.velocity

print(f"Scores: Player 1: {current_scores[0]}, Player 2: {current_scores[1]}")
print(f"Ball at: {ball_position}, moving: {ball_velocity}")
```

## Installation and Setup

### Prerequisites

```bash
pip install kivy
```

### Running the Game

```bash
cd PingPong
python main.py
```

### File Requirements

Ensure both `main.py` and `pong.kv` are in the same directory. The Kivy framework automatically loads the `.kv` file based on the App class name.

## Android Deployment

The project includes instructions for converting to an Android APK using Buildozer.

### Quick Setup

1. Install buildozer: `pip install buildozer`
2. Initialize project: `buildozer init`
3. Build APK: `buildozer android debug`

### Detailed Instructions

See `KivyToAndroid.txt` for complete step-by-step deployment guide including:
- Digital Ocean setup for cloud building
- Environment configuration
- File transfer methods
- APK installation on Android devices

## Game Controls

- **Touch/Mouse Left Side:** Move left paddle up/down
- **Touch/Mouse Right Side:** Move right paddle up/down
- **Automatic:** Ball physics, scoring, and collisions

## Technical Notes

### Frame Rate
- Game runs at 60 FPS (configurable in `PongApp.build()`)
- Physics calculations are frame-independent

### Collision Detection
- Uses Kivy's built-in `collide_widget()` method
- Ball speed increases slightly (×1.1) on paddle hits

### Coordinate System
- Origin (0,0) at bottom-left corner
- Positive Y axis points upward
- Positive X axis points rightward

### Performance Considerations
- Minimal object creation during gameplay
- Vector operations for efficient position calculations
- Canvas drawing for optimal rendering performance