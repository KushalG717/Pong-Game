import pygame
import random

# Initialize Pygame
pygame.init()

# Set up the screen
screen_width = 1000
screen_height = 800
screen = pygame.display.set_mode((screen_width, screen_height))
pygame.display.set_caption("Pong")

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)

# Ball properties
ball_radius = 10
ball_x = screen_width // 2
ball_y = screen_height // 2
ball_speed_x = 5 * random.choice((1, -1))
ball_speed_y = 5 * random.choice((1, -1))

####Paddle
paddle_width = 20
paddle_height = 150
paddle_speed = 7
left_paddle_y = (screen_height - paddle_height) // 2
right_paddle_y = (screen_height - paddle_height) // 2
paddle_gap = 50

#Define scores
score1 = 0
score2 = 0

####Scoreboard
def show_score(score1, score2):
    font = pygame.font.SysFont("freesans", 35)
    score_text1 = font.render(str(score1), True, (255,0,0))
    score_text2 = font.render(str(score2), True, (255,0,0))
    screen.blit(score_text1, (50, 50))
    screen.blit(score_text2, (screen_width - 50, 50))


clock = pygame.time.Clock()
running = True
while running:
    screen.fill(BLACK)
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    #Paddle Movement
    keys = pygame.key.get_pressed()
    if keys[pygame.K_w]:
        left_paddle_y -= paddle_speed
    if keys[pygame.K_s]:
        left_paddle_y += paddle_speed
    if keys[pygame.K_UP]:
        right_paddle_y -= paddle_speed
    if keys[pygame.K_DOWN]:
        right_paddle_y += paddle_speed

    #Paddle boundaries
    left_paddle_y = max(0, min(left_paddle_y, screen_height - paddle_height))
    right_paddle_y = max(0, min(right_paddle_y, screen_height - paddle_height))

    # Move ball
    ball_x += ball_speed_x
    ball_y += ball_speed_y

    #Collisions w/ paddles
    if ball_x - ball_radius <= paddle_width and left_paddle_y <= ball_y <= left_paddle_y + paddle_height:
        ball_speed_x *= -1
    if ball_x + ball_radius >= screen_width - paddle_width and right_paddle_y <= ball_y <= right_paddle_y + paddle_height:
        ball_speed_x *= -1

    ####Wall collisions
    if ball_y - ball_radius <= 0 or ball_y + ball_radius >= screen_height:
        ball_speed_y *= -1

    ####Out of bounds
    if ball_x - ball_radius < 0:
        score2 += 1
        ball_x = screen_width // 2
        ball_y = screen_height // 2
        ball_speed_x *= random.choice((1, -1))
        ball_speed_y *= random.choice((1, -1))
    elif ball_x + ball_radius > screen_width:
        score1 += 1
        ball_x = screen_width // 2
        ball_y = screen_height // 2
        ball_speed_x *= random.choice((1, -1))
        ball_speed_y *= random.choice((1, -1))
        
    ####Draw paddles and ball
    pygame.draw.rect(screen, WHITE, pygame.Rect(0, left_paddle_y, paddle_width, paddle_height))
    pygame.draw.rect(screen, WHITE, pygame.Rect(screen_width - paddle_width, right_paddle_y, paddle_width, paddle_height))
    pygame.draw.circle(screen, WHITE, (ball_x, ball_y), ball_radius)

    ####Scorebard
    show_score(score1, score2)

    pygame.display.flip()
    clock.tick(60)

pygame.quit()

