import subprocess
def install_pygame():
    try:
        # subprocess.check_call(["pip", "install", "--upgrade pip"])
        subprocess.check_call(["pip", "install", "pygame"])
        print("Pygame installed successfully.")
    except subprocess.CalledProcessError as e:
        print(f"Error during Pygame installation: {e}")

# Call the function to install Pygame
install_pygame()
# Check if Pygame is installed
try:
    import pygame
except ImportError:
    install_pygame()
import pygame
import sys


# Initialize Pygame
pygame.init()

# Constants
WIDTH, HEIGHT = 800, 600
WHITE = (255, 255, 255)
GRAY = (169, 169, 169)  # Changed blue to gray
RED = (255, 0, 0)
YELLOW = (255, 255, 0)

PADDLE_WIDTH, PADDLE_HEIGHT = 15, 100
BALL_RADIUS = 15
PADDLE_SPEED = 13
BALL_SPEED = 4
font = pygame.font.SysFont(None, 36)

# Initialize screen
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Two-Player Pong")

# Initialize paddles and ball
player1 = pygame.Rect(50, HEIGHT // 2 - PADDLE_HEIGHT // 2, PADDLE_WIDTH, PADDLE_HEIGHT)
player2 = pygame.Rect(WIDTH - 50 - PADDLE_WIDTH, HEIGHT // 2 - PADDLE_HEIGHT // 2, PADDLE_WIDTH, PADDLE_HEIGHT)
ball = pygame.Rect(WIDTH // 2 - BALL_RADIUS // 2, HEIGHT // 2 - BALL_RADIUS // 2, BALL_RADIUS, BALL_RADIUS)

# Initialize game variables
player1_speed = 0
player2_speed = 0
ball_speed_x = BALL_SPEED
ball_speed_y = BALL_SPEED
player1_score = 0
player2_score = 0
clock = pygame.time.Clock()

def show_start_screen1():
    screen.fill(GRAY)  # Change BLUE to GRAY
    start_text = font.render("Press Enter to Start", True, WHITE)
    controls_text = font.render("Player 1: UP/DOWN or W/S arrows", True, WHITE)
    controls_text1 = font.render("Press SPACE to Pause - Press R to restart game", True, WHITE)
    exit_text = font.render("Press ESC to exit", True, WHITE)
    start_text1 = font.render("INSTRUCTIONS", True, WHITE)
    screen.blit(start_text1, (WIDTH // 2 - start_text1.get_width() // 2, HEIGHT // 2 - start_text1.get_height()// 2-50))
    screen.blit(controls_text, (WIDTH // 2 - controls_text.get_width() // 2, HEIGHT // 2))
    screen.blit(controls_text1, (WIDTH // 2 - controls_text1.get_width() // 2, HEIGHT // 2 + 50))
    screen.blit(exit_text, (WIDTH // 2 - exit_text.get_width() // 2, HEIGHT // 2 + 100))
    screen.blit(start_text, (WIDTH // 2 - start_text.get_width() // 2, HEIGHT // 2+150))
    pygame.display.flip()
    waiting = True
    while waiting:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN and event.key == pygame.K_RETURN:
                waiting = False
            if event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE:
                pygame.quit()
                sys.exit()

def show_start_screen():
    screen.fill(GRAY)  # Change BLUE to GRAY
    start_text = font.render("Enter Your Name and Press Enter to Start", True, WHITE)
    screen.blit(start_text, (WIDTH // 2 - start_text.get_width() // 2, HEIGHT // 2 - start_text.get_height() // 2))
    pygame.display.flip()

    user_name = ""
    user_name_input = "User"
    name_input_rect = pygame.Rect(WIDTH // 2 - 200, HEIGHT // 2 + 50, 400, 40)

    waiting = True
    while waiting:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_RETURN:
                    waiting = False
                elif event.key == pygame.K_BACKSPACE:
                    user_name_input = user_name_input[:-1]
                else:
                    user_name_input += event.unicode

        screen.fill(GRAY)  # Change BLUE to GRAY
        pygame.draw.rect(screen, WHITE, name_input_rect, 2)
        start_text = font.render("Enter Your Name and Press Enter to Start", True, WHITE)
        input_text = font.render(user_name_input, True, WHITE)
        screen.blit(start_text, (WIDTH // 2 - start_text.get_width() // 2, HEIGHT // 2 - start_text.get_height() // 2))
        screen.blit(input_text, (name_input_rect.x + 5, name_input_rect.y + 5))
        pygame.display.flip()
        clock.tick(30)

    return user_name_input

def show_end_screen(user_name, player1_score, player2_score):
    screen.fill(GRAY)  # Change BLUE to GRAY
    end_text = font.render(f"{user_name}: {player1_score}  Player AI_Agent: {player2_score}", True, WHITE)
    screen.blit(end_text, (WIDTH // 2 - end_text.get_width() // 2, HEIGHT // 2 - end_text.get_height() // 2))
    restart_text = font.render("Press R to restart", True, WHITE)
    exit_text = font.render("Press ESC to exit", True, WHITE)
    screen.blit(restart_text, (WIDTH // 2 - restart_text.get_width() // 2, HEIGHT // 2 + 50))
    screen.blit(exit_text, (WIDTH // 2 - exit_text.get_width() // 2, HEIGHT // 2 + 100))
    pygame.display.flip()
    waiting = True
    while waiting:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_r:
                    restart_game()
                    return True
                elif event.key == pygame.K_ESCAPE:
                    pygame.quit()
                    sys.exit()
    return False

def restart_game():
    global player1_score, player2_score, player1_speed, player2_speed, ball_speed_x, ball_speed_y
    player1_score = 0
    player2_score = 0
    player1_speed = 0
    player2_speed = 0
    ball_speed_x = BALL_SPEED
    ball_speed_y = BALL_SPEED
    ball.center = (WIDTH // 2, HEIGHT // 2)

def select_difficulty():
    global PADDLE_SPEED, BALL_SPEED  # Update global variables
    screen.fill(GRAY)  # Change BLUE to GRAY
    title_text = font.render("Select Difficulty", True, WHITE)
    easy_text = font.render("1. Easy", True, WHITE)
    hard_text = font.render("2. Hard", True, WHITE)
    difficult_text = font.render("3. Difficult", True, WHITE)
    screen.blit(title_text, (WIDTH // 2 - title_text.get_width() // 2, HEIGHT // 2 - 100))
    screen.blit(easy_text, (WIDTH // 2 - easy_text.get_width() // 2, HEIGHT // 2 - 50))
    screen.blit(hard_text, (WIDTH // 2 - hard_text.get_width() // 2, HEIGHT // 2))
    screen.blit(difficult_text, (WIDTH // 2 - difficult_text.get_width() // 2, HEIGHT // 2 + 50))
    pygame.display.flip()
    waiting = True
    selected_difficulty = None
    while waiting:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            elif event.type == pygame.KEYDOWN:
                if event.key == pygame.K_1:
                    selected_difficulty = "easy"
                    PADDLE_SPEED = 10  # Update paddle speed for easy difficulty
                    BALL_SPEED = 5     # Update ball speed for easy difficulty
                    waiting = False
                elif event.key == pygame.K_2:
                    selected_difficulty = "hard"
                    PADDLE_SPEED = 20  # Update paddle speed for hard difficulty
                    BALL_SPEED = 8     # Update ball speed for hard difficulty
                    waiting = False
                elif event.key == pygame.K_3:
                    selected_difficulty = "difficult"
                    PADDLE_SPEED = 30      # Update paddle speed for difficult difficulty
                    BALL_SPEED = 10         # Update ball speed for difficult difficulty
                    waiting = False
    return selected_difficulty

# Game loop
difficulty = select_difficulty()
show_start_screen1()
user_name = show_start_screen()
restart_game()
running = True
paused = True # Flag to track whether the game is paused
while running:
    # Game logic remains the same
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:
                paused = not paused  # Toggle pause state when the space bar is pressed
            elif event.key == pygame.K_r:
                restart_game()

    if not paused:  # Execute game logic only if the game is not paused
        keys = pygame.key.get_pressed()
        if keys[pygame.K_UP] or keys[pygame.K_w]:
            player1_speed = -PADDLE_SPEED
        elif keys[pygame.K_DOWN] or keys[pygame.K_s]:
            player1_speed = PADDLE_SPEED
        else:
            player1_speed = 0

        # Move paddles
        player1.y += player1_speed

        # AI player movement logic
        if ball.centery < player2.centery:
            player2_speed = -PADDLE_SPEED
        elif ball.centery > player2.centery:
            player2_speed = PADDLE_SPEED
        else:
            player2_speed = 0
        player2.y += player2_speed

        # Ensure paddles stay within the screen boundaries
        player1.y = max(0, min(HEIGHT - PADDLE_HEIGHT, player1.y))
        player2.y = max(0, min(HEIGHT - PADDLE_HEIGHT, player2.y))

        # Move the ball
        ball.x += ball_speed_x
        ball.y += ball_speed_y

        # Ball collisions with walls
        if ball.top <= 0 or ball.bottom >= HEIGHT:
            ball_speed_y = -ball_speed_y

        # Ball collisions with paddles
        if ball.colliderect(player1):
            ball_speed_x = BALL_SPEED
            player1_score += 1
        elif ball.colliderect(player2):
            ball_speed_x = -BALL_SPEED
            player2_score += 1

        # Check for scoring
        if ball.left <= 0 or ball.right >= WIDTH:
            if not show_end_screen(user_name, player1_score, player2_score):
                show_end_screen(user_name, player1_score, player2_score)  # Restart the game

    screen.fill(GRAY)  # Change BLUE to GRAY

    # Draw paddles and ball
    pygame.draw.rect(screen, YELLOW, player1)
    pygame.draw.rect(screen, YELLOW, player2)
    pygame.draw.ellipse(screen, RED, ball)

    # Draw scores on the top of the screen
    score_text = font.render(f"{user_name}: {player1_score} AI-Agent: {player2_score}", True, WHITE)
    screen.blit(score_text, (WIDTH // 2 - score_text.get_width() // 2, 10))

    if paused:
        pause_text = font.render("Paused", True, WHITE)
        screen.blit(pause_text, (WIDTH // 2 - pause_text.get_width() // 2, HEIGHT // 2 - pause_text.get_height() // 2))

    pygame.display.flip()
    clock.tick(60)
