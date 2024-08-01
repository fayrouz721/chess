# chess

# Chess Game

This is a two-player chess game implemented using Python and Pygame. The game features basic chess rules, piece movements, and capturing.

## Requirements

- Python 3.x
- Pygame

You can install Pygame using pip:

```bash
bashCopy code
pip install pygame

```

## Game Setup

### Screen and Fonts

The game initializes a screen of size 800x720 pixels and sets up various fonts for different text sizes:

```python
pythonCopy code
import pygame
pygame.init()

# Screen dimensions
WIDTH, HEIGHT = 800, 720
screen = pygame.display.set_mode((WIDTH, HEIGHT))

# Fonts
font = pygame.font.Font(None, 16)
medium_font = pygame.font.Font(None, 32)
big_font = pygame.font.Font(None, 40)

```

### Game Variables and Images

The game initializes various variables to manage the state of the game:

```python
pythonCopy code
# Piece positions
white_pieces = []
black_pieces = []
white_locations = [ ... ]  # List of initial white piece positions
black_locations = [ ... ]  # List of initial black piece positions
captured_pieces_white = []
captured_pieces_black = []

# Turn management
turn_step = 0  # 0 for white's turn, 1 for black's turn
selection = None
valid_moves = []

# Load and scale piece images
piece_images = { ... }  # Dictionary to hold piece images
for piece, path in piece_images.items():
    piece_images[piece] = pygame.transform.scale(pygame.image.load(path), (80, 80))

```

## Drawing Functions

### `draw_board()`

This function draws the main game board, the grid lines, and the status text indicating whose turn it is.

```python
pythonCopy code
def draw_board():
    screen.fill((255, 255, 255))
    for row in range(8):
        for col in range(8):
            color = (209, 139, 71) if (row + col) % 2 else (255, 206, 158)
            pygame.draw.rect(screen, color, pygame.Rect(col * 80, row * 80, 80, 80))
    turn_text = big_font.render("White's Turn" if turn_step % 2 == 0 else "Black's Turn", True, (0, 0, 0))
    screen.blit(turn_text, (10, 650))

```

### `draw_pieces()`

This function draws the pieces on the board at their respective positions.

```python
pythonCopy code
def draw_pieces():
    for piece in white_pieces + black_pieces:
        screen.blit(piece_images[piece.type], piece.rect.topleft)

```

### `draw_valid(moves)`

This function draws small circles on the board to indicate valid moves for the selected piece.

```python
pythonCopy code
def draw_valid(moves):
    for move in moves:
        pygame.draw.circle(screen, (0, 255, 0), (move[0] * 80 + 40, move[1] * 80 + 40), 10)

```

### `draw_captured()`

This function draws the captured pieces on the side of the screen.

```python
pythonCopy code
def draw_captured():
    for i, piece in enumerate(captured_pieces_white):
        screen.blit(piece_images[piece.type], (650, i * 40))
    for i, piece in enumerate(captured_pieces_black):
        screen.blit(piece_images[piece.type], (700, i * 40))

```

### `draw_check()`

This function highlights the king in red or blue if it is in check.

```python
pythonCopy code
def draw_check():
    for king in white_pieces + black_pieces:
        if king.type == 'king' and king.in_check:
            color = (255, 0, 0) if king.color == 'white' else (0, 0, 255)
            pygame.draw.rect(screen, color, king.rect, 5)

```

### `draw_game_over()`

This function displays a game over message and the winner.

```python
pythonCopy code
def draw_game_over(winner):
    game_over_text = big_font.render("Game Over!", True, (0, 0, 0))
    winner_text = big_font.render(f"{winner} Wins!", True, (0, 0, 0))
    screen.blit(game_over_text, (WIDTH // 2 - 100, HEIGHT // 2 - 40))
    screen.blit(winner_text, (WIDTH // 2 - 100, HEIGHT // 2))

```

## Piece Movement and Valid Moves

### `check_options(pieces, locations, turn)`

This function checks all valid moves for all pieces of the specified player (white or black).

```python
pythonCopy code
def check_options(pieces, locations, turn):
    valid_moves = []
    for piece in pieces:
        if piece.color == ('white' if turn % 2 == 0 else 'black'):
            valid_moves.extend(piece.valid_moves(locations))
    return valid_moves

```

### Piece-Specific Move Functions

- `check_king(position, color)`
- `check_queen(position, color)`
- `check_bishop(position, color)`
- `check_rook(position, color)`
- `check_knight(position, color)`
- `check_pawn(position, color)`

These functions calculate valid moves for each type of piece based on their current position and color.

```python
pythonCopy code
def check_king(position, color):
    moves = [ ... ]  # List of valid king moves
    return moves

def check_queen(position, color):
    moves = [ ... ]  # List of valid queen moves
    return moves

# Similarly define check_bishop, check_rook, check_knight, check_pawn

```

### `check_valid_moves()`

This function checks the valid moves for the currently selected piece.

```python
pythonCopy code
def check_valid_moves(piece, locations):
    if piece.type == 'king':
        return check_king(piece.position, piece.color)
    elif piece.type == 'queen':
        return check_queen(piece.position, piece.color)
    # Similarly for other piece types

```

## Main Game Loop

The main game loop runs the game by continuously drawing the board, pieces, valid moves, and handling user input.

### `timer.tick(fps)`

This function sets the frame rate of the game to 60 frames per second.

```python
pythonCopy code
timer = pygame.time.Clock()
fps = 60

while not game_over:
    timer.tick(fps)
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            game_over = True
        # Handle other events like mouse clicks and key presses
    draw_board()
    draw_pieces()
    if turn_step % 2 == 0 and selection is not None:
        draw_valid(valid_moves)
    draw_captured()
    draw_check()
    pygame.display.flip()

```

### `pygame.event.get()`

This function handles user input events such as mouse clicks and key presses.

### Mouse Clicks

- Select a piece by clicking on it.
- Move a piece by clicking on a valid move location.
- Forfeit the game by clicking the "FORFEIT" button.

```python
pythonCopy code
for event in pygame.event.get():
    if event.type == pygame.MOUSEBUTTONDOWN:
        mouse_pos = event.pos
        # Check for piece selection or movement

```

### Key Presses

- Restart the game by pressing `ENTER` after a game over.

```python
pythonCopy code
if event.type == pygame.KEYDOWN:
    if event.key == pygame.K_RETURN and game_over:
        reset_game()

```

### Game State Management

The game state is managed using variables like `turn_step`, `selection`, `valid_moves`, and lists of piece locations. The game checks for valid moves, captures pieces, and checks for checkmate conditions.

```python
pythonCopy code
def reset_game():
    # Reset all game variables to initial state

```

## How to Play

1. Run the game script.
2. Click on a piece to select it.
3. Click on a valid move location to move the piece.
4. Capture opponent's pieces by moving onto their position.
5. The game ends when a king is captured. The winner is displayed, and the game can be restarted by pressing `ENTER`.

## Notes

- The game does not currently implement all chess rules (e.g., castling, en passant).
- The game is played in a window, and the board is drawn in an 8x8 grid.
- Pieces are scaled to fit the grid and are drawn using images loaded from files.

---