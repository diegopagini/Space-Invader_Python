# Space-Invader_Python

```python
import pygame
import random
import math
from pygame import mixer

# Init pygame
pygame.init()
# Create the screen
screen = pygame.display.set_mode((800, 600))
# Title and icon
pygame.display.set_caption("Space invader")
icon = pygame.image.load("ovni.png")
pygame.display.set_icon(icon)
# Background
background = pygame.image.load("Fondo.jpg")
# Music
mixer.music.load("MusicaFondo.mp3")
mixer.music.set_volume(0.3)
mixer.music.play(-1)
# Score
score = 0
font = pygame.font.Font("freesansbold.ttf", 32)


class GameObject:
    def __init__(self, img: str, position_x: int, position_y: int, position_x_change: float, position_y_change: int, visible: bool = True):
        self.img = img
        self.position_x = position_x
        self.position_y = position_y
        self.position_x_change = position_x_change
        self.position_y_change = position_y_change
        self.visible = visible


# Player
player_obj = GameObject( pygame.image.load("cohete.png"), 368, 500, 0, 0)
# Enemy
enemy_obj = GameObject(pygame.image.load("enemigo.png"), random.randint(0, 736), random.randint(50, 200), 0.5, 50)
# Bullet
bullet_obj = GameObject(pygame.image.load("bala.png"), 0, 500 , 0 , 2, False)


def player(x: int, y: int):
    screen.blit(player_obj.img, (x, y))


def enemy(x: int, y: int):
    screen.blit(enemy_obj.img, (x, y))


def shoot(x: int, y: int):
    bullet_obj.visible = True
    screen.blit(bullet_obj.img, (x + 16, y + 10))


def there_is_a_collision(x_1: int, y_1: int, x_2: int, y_2: int):
    distance = math.sqrt(math.pow(x_1 - x_2, 2) + math.pow(y_2 - y_1, 2))
    if distance < 27:
        return True
    else:
        return False


def show_score(x: int, y: int):
    text = font.render(f"Score: {score}", True, (255, 255, 255))
    screen.blit(text, (x, y))


def end_text():
    final_font = pygame.font.Font("freesansbold.ttf", 42)
    end_font = final_font.render("GAME OVER", True, (255, 255, 255))
    screen.blit(end_font, (60, 200))


def init_game():
    global score
    score = 0
    is_executing = True
    # Game loop
    while is_executing:
        # Background
        screen.blit(background, (0, 0))
        # Game events
        for event in pygame.event.get():
            # Quit
            if event.type == pygame.QUIT:
                is_executing = False
            # Keypress
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                    player_obj.position_x_change = -1
                if event.key == pygame.K_RIGHT:
                    player_obj.position_x_change = 1
                if event.key == pygame.K_SPACE and not bullet_obj.visible:
                    bullet_sound = mixer.Sound("disparo.mp3")
                    bullet_sound.play()
                    bullet_x = player_obj.position_x
                    shoot(bullet_x, bullet_obj.position_y)
            # Keyup
            if event.type == pygame.KEYUP:
                if event.key == pygame.K_LEFT or event.key == pygame.K_RIGHT:
                    player_obj.position_x_change = 0

        # modify player position
        player_obj.position_x += player_obj.position_x_change
        # keep player in screen
        if player_obj.position_x <= 0:
            player_obj.position_x = 0
        elif player_obj.position_x >= 736:
            player_obj.position_x = 736

        # end game
        if enemy_obj.position_y > 500:
            enemy_obj.position_y = 1000
            end_text()
            break

        # modify enemy position
        enemy_obj.position_x += enemy_obj.position_x_change
        # keep enemy in screen
        if enemy_obj.position_x <= 0:
            enemy_obj.position_x_change = 1
            enemy_obj.position_x += enemy_obj.position_x_change
        elif enemy_obj.position_x >= 736:
            enemy_x_change = -1
            enemy_obj.position_y += enemy_x_change

        # bullet movement
        if bullet_obj.position_y <= -64:
            bullet_obj.position_y = 500
            bullet_obj.visible = False

        if bullet_obj.visible:
            shoot(bullet_obj.position_x, bullet_obj.position_y)
            bullet_obj.position_y -= bullet_obj.position_y_change

        # collision
        collision = there_is_a_collision(enemy_obj.position_x, enemy_obj.position_y, bullet_obj.position_x, bullet_obj.position_y)
        if collision:
            collision_sound = mixer.Sound("golpe.mp3")
            collision_sound.play()
            bullet_obj.position_y = 500
            bullet_obj.visible = False
            score += 1
            enemy_obj.position_x = random.randint(0, 736)
            enemy_obj.position_y = random.randint(50, 200)

        player(player_obj.position_x, player_obj.position_y)
        enemy(enemy_obj.position_x, enemy_obj.position_y)
        show_score(0, 10)

        # update
        pygame.display.update()


init_game()
```
