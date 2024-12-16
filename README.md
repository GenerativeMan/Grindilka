# Grindilka
Grandilka
import pygame
import random
import sys

# Ініціалізація Pygame
pygame.init()

# Параметри вікна
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Grind Adventure")

# Кольори
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
GREEN = (0, 255, 0)
RED = (255, 0, 0)
GOLD = (255, 215, 0)

# FPS і таймер
FPS = 60
clock = pygame.time.Clock()

# Гравець
player_size = 50
player_x = WIDTH // 2
player_y = HEIGHT // 2
player_speed = 5
player_health = 100
coins_collected = 0
kills = 0

# Монети
coin_size = 20
coins = [pygame.Rect(random.randint(0, WIDTH - coin_size), random.randint(0, HEIGHT - coin_size), coin_size, coin_size) for _ in range(10)]

# Монстри
monster_size = 40
monsters = []
for _ in range(5):
    monster_x = random.randint(0, WIDTH - monster_size)
    monster_y = random.randint(0, HEIGHT - monster_size)
    monsters.append(pygame.Rect(monster_x, monster_y, monster_size, monster_size))

monster_speed = 2

# Покращення
upgrade_cost = 10
player_damage = 10

# Шрифти
font = pygame.font.SysFont(None, 35)

def display_stats():
    stats_text = font.render(f"Health: {player_health}  Coins: {coins_collected}  Kills: {kills}", True, BLACK)
    screen.blit(stats_text, (10, 10))

# Основний цикл гри
def game_loop():
    global player_x, player_y, coins_collected, player_health, kills, player_speed, player_damage, upgrade_cost

    running = True
    while running:
        screen.fill(WHITE)

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()

        # Керування гравцем
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT]:
            player_x -= player_speed
        if keys[pygame.K_RIGHT]:
            player_x += player_speed
        if keys[pygame.K_UP]:
            player_y -= player_speed
        if keys[pygame.K_DOWN]:
            player_y += player_speed

        # Обмеження руху гравця
        if player_x < 0:
            player_x = 0
        if player_x > WIDTH - player_size:
            player_x = WIDTH - player_size
        if player_y < 0:
            player_y = 0
        if player_y > HEIGHT - player_size:
            player_y = HEIGHT - player_size

        # Перевірка на зіткнення з монетами
        player_rect = pygame.Rect(player_x, player_y, player_size, player_size)
        for coin in coins[:]:
            if player_rect.colliderect(coin):
                coins.remove(coin)
                coins_collected += 1
                coins.append(pygame.Rect(random.randint(0, WIDTH - coin_size), random.randint(0, HEIGHT - coin_size), coin_size, coin_size))

        # Перевірка на зіткнення з монстрами
        for monster in monsters[:]:
            if player_rect.colliderect(monster):
                player_health -= 1

        # Рух монстрів до гравця
        for monster in monsters:
            if monster.x < player_x:
                monster.x += monster_speed
            elif monster.x > player_x:
                monster.x -= monster_speed

            if monster.y < player_y:
                monster.y += monster_speed
            elif monster.y > player_y:
                monster.y -= monster_speed

        # Атака монстрів (натисніть пробіл)
        if keys[pygame.K_SPACE]:
            for monster in monsters[:]:
                if player_rect.colliderect(monster):
                    monsters.remove(monster)
                    kills += 1
                    monsters.append(pygame.Rect(random.randint(0, WIDTH - monster_size), random.randint(0, HEIGHT - monster_size), monster_size, monster_size))

        # Покращення (натисніть "U")
        if keys[pygame.K_u] and coins_collected >= upgrade_cost:
            coins_collected -= upgrade_cost
            player_speed += 1
            player_damage += 2
            upgrade_cost += 5

        # Кінець гри, якщо здоров'я = 0
        if player_health <= 0:
            running = False

        # Малювання гравця
        pygame.draw.rect(screen, GREEN, player_rect)

        # Малювання монет
        for coin in coins:
            pygame.draw.rect(screen, GOLD, coin)

        # Малювання монстрів
        for monster in monsters:
            pygame.draw.rect(screen, RED, monster)

        # Відображення статистики
        display_stats()

        # Оновлення екрану
        pygame.display.flip()
        clock.tick(FPS)

    # Кінець гри
    screen.fill(WHITE)
    game_over_text = font.render("Game Over! Press R to Restart or Q to Quit", True, BLACK)
    screen.blit(game_over_text, (WIDTH // 2 - 200, HEIGHT // 2))
    pygame.display.flip()

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_r:
                    main()
                if event.key == pygame.K_q:
                    pygame.quit()
                    sys.exit()

def main():
    game_loop()

if __name__ == "__main__":
    main()
