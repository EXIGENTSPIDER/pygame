import pygame
import random

# Initialize Pygame
pygame.init()

# Screen dimensions
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600

# Colors
WHITE = (255, 255, 255)
RED = (255, 0, 0)

# Create the screen
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Rocket Shooter")

# Load images
rocket_img = pygame.image.load("rocket.png")
rocket_img = pygame.transform.scale(rocket_img, (50, 50))

enemy_img = pygame.image.load("enemy_rocket.png")
enemy_img = pygame.transform.scale(enemy_img, (50, 50))

bullet_img = pygame.Surface((10, 30))
bullet_img.fill(RED)

# Clock for controlling the frame rate
clock = pygame.time.Clock()

# Rocket properties
rocket_x = SCREEN_WIDTH // 2
rocket_y = SCREEN_HEIGHT - 100
rocket_speed = 5

# Bullets
bullets = []

# Enemy properties
enemies = []

def spawn_enemy():
    enemy = {
        "x": random.randint(0, SCREEN_WIDTH - 50),
        "y": random.randint(0, SCREEN_HEIGHT // 2),
        "speed": random.randint(1, 3)
    }
    enemies.append(enemy)

# Game loop
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        rocket_x -= rocket_speed
    if keys[pygame.K_RIGHT]:
        rocket_x += rocket_speed
    if keys[pygame.K_SPACE]:
        bullets.append({"x": rocket_x + 20, "y": rocket_y, "speed": 5})

    # Update bullets
    for bullet in bullets:
        bullet["y"] -= bullet["speed"]
        if bullet["y"] < 0:
            bullets.remove(bullet)

    # Update enemies
    for enemy in enemies:
        enemy["y"] += enemy["speed"]
        if enemy["y"] > SCREEN_HEIGHT:
            enemies.remove(enemy)
            spawn_enemy()

    # Collision detection
    for enemy in enemies:
        for bullet in bullets:
            if (
                enemy["x"] < bullet["x"] < enemy["x"] + 50
                and enemy["y"] < bullet["y"] < enemy["y"] + 50
            ):
                bullets.remove(bullet)
                enemies.remove(enemy)
                spawn_enemy()

    # Draw everything
    screen.fill(WHITE)
    screen.blit(rocket_img, (rocket_x, rocket_y))

    for bullet in bullets:
        pygame.draw.rect(screen, RED, (bullet["x"], bullet["y"], 10, 30))

    for enemy in enemies:
        screen.blit(enemy_img, (enemy["x"], enemy["y"]))

    pygame.display.flip()
    clock.tick(60)

pygame.quit()

