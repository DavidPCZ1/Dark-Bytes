# lightning tempo
školní projekt hra
import pygame
import random
import sys

# Inicializace Pygame
pygame.init()

# Nastavení okna
WIDTH, HEIGHT = 600, 500
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("⚡ Lightning Tempo ⚡")

# Barvy (RGB)
BG_COLOR = (13, 13, 26)
LANE_BG = (26, 26, 58)
BORDER_COLOR = (0, 229, 255)
ACTIVE_COLOR = (255, 230, 0)
TEXT_COLOR = (255, 255, 255)
ACCENT_COLOR = (0, 229, 255)

# Fonty
font_large = pygame.font.SysFont("Segoe UI", 36, bold=True)
font_medium = pygame.font.SysFont("Segoe UI", 24, bold=True)
font_small = pygame.font.SysFont("Segoe UI", 18)

# Herní stavy
STATE_START = 0
STATE_PLAYING = 1
STATE_GAMEOVER = 2
game_state = STATE_START

# Herní proměnné
score = 0
lives = 3
speed = 1200  # Čas na reakci v ms
active_lane = None
last_spawn_time = 0

# Definice drah (klávesy a jejich pozice)
lanes = [
    {"key": pygame.K_a, "char": "A", "x": 120},
    {"key": pygame.K_s, "char": "S", "x": 210},
    {"key": pygame.K_d, "char": "D", "x": 300},
    {"key": pygame.K_f, "char": "F", "x": 390}
]

def reset_game():
    global score, lives, speed, active_lane, last_spawn_time, game_state
    score = 0
    lives = 3
    speed = 1200
    active_lane = None
    game_state = STATE_PLAYING
    spawn_lightning()

def spawn_lightning():
    global active_lane, last_spawn_time
    active_lane = random.choice(lanes)
    last_spawn_time = pygame.time.get_ticks()

# Hlavní smyčka hry
clock = pygame.time.Clock()

while True:
    current_time = pygame.time.get_ticks()
    
    # Zpracování událostí
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()
            
        if event.type == pygame.KEYDOWN:
            if game_state == STATE_START:
                if event.key == pygame.K_RETURN:
                    reset_game()
            elif game_state == STATE_PLAYING:
                # Kontrola stisknuté klávesy
                matched = False
                for lane in lanes:
                    if event.key == lane["key"]:
                        matched = True
                        if lane == active_lane:
                            # Správná klávesa!
                            score += 10
                            # Zrychlení hry každých 50 bodů
                            if score % 50 == 0 and speed > 400:
                                speed -= 100
                            spawn_lightning()
                        else:
                            # Špatná klávesa
                            lives -= 1
                            if lives <= 0:
                                game_state = STATE_GAMEOVER
                            else:
                                spawn_lightning()
                # Pokud hráč zmáčkl něco jiného, ignorujeme to
            elif game_state == STATE_GAMEOVER:
                if event.key == pygame.K_RETURN:
                    reset_game()

    # Kontrola vypršení času pro aktuální blesk
    if game_state == STATE_PLAYING:
        if current_time - last_spawn_time > speed:
            lives -= 1
            if lives <= 0:
                game_state = STATE_GAMEOVER
            else:
                spawn_lightning()

    # Vykreslování
    screen.fill(BG_COLOR)

    if game_state == STATE_START:
        title = font_large.render("⚡ LIGHTNING TEMPO ⚡", True, ACCENT_COLOR)
        prompt = font_medium.render("Stiskni ENTER pro spuštění", True, TEXT_COLOR)
        controls = font_small.render("Ovládání: Klávesy A, S, D, F", True, (150, 150, 200))
        
        screen.blit(title, (WIDTH // 2 - title.get_width() // 2, 150))
        screen.blit(prompt, (WIDTH // 2 - prompt.get_width() // 2, 240))
        screen.blit(controls, (WIDTH // 2 - controls.get_width() // 2, 320))

    elif game_state == STATE_PLAYING:
        # Horní lišta (Skóre, Životy, Tempo)
        score_text = font_medium.render(f"Skóre: {score}", True, TEXT_COLOR)
        lives_text = font_medium.render(f"Životy: {lives}", True, (255, 100, 100))
        tempo_val = round(1200 / speed, 1)
        tempo_text = font_medium.render(f"Tempo: {tempo_val}x", True, ACCENT_COLOR)
        
        screen.blit(score_text, (50, 40))
        screen.blit(lives_text, (230, 40))
        screen.blit(tempo_text, (410, 40))

        # Vykreslení drah
        for lane in lanes:
            is_active = (lane == active_lane)
            box_color = ACTIVE_COLOR if is_active else LANE_BG
            border_color = ACTIVE_COLOR if is_active else BORDER_COLOR
            
            # Vykreslení obdélníku dráhy
            pygame.draw.rect(screen, box_color, (lane["x"], 150, 70, 220), border_radius=10)
            pygame.draw.rect(screen, border_color, (lane["x"], 150, 70, 220), 2, border_radius=10)
            
            # Popisek klávesy dole
            key_surf = font_large.render(lane["char"], True, TEXT_COLOR if not is_active else (0,0,0))
            screen.blit(key_surf, (lane["x"] + 35 - key_surf.get_width() // 2, 400))

    elif game_state == STATE_GAMEOVER:
        over_title = font_large.render("KONEC HRY", True, (255, 80, 80))
        final_score = font_medium.render(f"Tvoje finální skóre: {score}", True, TEXT_COLOR)
        restart_prompt = font_small.render("Stiskni ENTER pro restart", True, ACCENT_COLOR)
        
        screen.blit(over_title, (WIDTH // 2 - over_title.get_width() // 2, 160))
        screen.blit(final_score, (WIDTH // 2 - final_score.get_width() // 2, 230))
        screen.blit(restart_prompt, (WIDTH // 2 - restart_prompt.get_width() // 2, 300))

    pygame.display.flip()
    clock.tick(60)
    }
  </script>
</body>
</html>
