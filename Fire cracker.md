import pygame
import random
import math

pygame.init()

WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))

pygame.display.set_caption('Abhishek Fireworks')

WHITE = (255, 255, 255)
BLACK = (0, 0, 0)

font = pygame.font.SysFont('Arial', 72)

text = font.render('CrimsonAbhishek', True, WHITE)
text_rect = text.get_rect(center=(WIDTH // 2, HEIGHT // 2))

pygame.mixer.music.load(r'C:\Users\MSI\Desktop\we_fell_in_love_in_october.mp3')

clock = pygame.time.Clock()

class Firework:
    def __init__(self, x, y):
        self.x = x
        self.y = y
        self.angle = random.uniform(0, 2 * math.pi)
        self.speed = random.uniform(1, 3)
        self.radius = random.randint(3, 6)
        self.color = [random.randint(0, 255) for _ in range(3)]
        self.life = 60

    def update(self):
        self.x += math.cos(self.angle) * self.speed
        self.y += math.sin(self.angle) * self.speed
        self.life -= 1
        self.radius *= 0.97

    def draw(self, screen):
        pygame.draw.circle(screen, self.color, (int(self.x), int(self.y)), int(self.radius))

class StarFirework(Firework):
    def draw(self, screen):
        for i in range(5):
            angle = self.angle + (i * (2 * math.pi / 5))
            x2 = self.x + math.cos(angle) * self.radius * 3
            y2 = self.y + math.sin(angle) * self.radius * 3
            pygame.draw.line(screen, self.color, (self.x, self.y), (x2, y2), 2)

fireworks = []

running = True
music_started = False

r, g, b = 255, 0, 0
change_speed = 1
phase = 0

while running:
    if phase == 0:
        g += change_speed
        if g >= 255:
            g = 255
            phase = 1
    elif phase == 1:
        r -= change_speed
        if r <= 0:
            r = 0
            phase = 2
    elif phase == 2:
        b += change_speed
        if b >= 255:
            b = 255
            phase = 3
    elif phase == 3:
        g -= change_speed
        if g <= 0:
            g = 0
            phase = 4
    elif phase == 4:
        r += change_speed
        if r >= 255:
            r = 255
            phase = 5
    elif phase == 5:
        b -= change_speed
        if b <= 0:
            b = 0
            phase = 0

    screen.fill((r, g, b))
    
    screen.blit(text, text_rect)
    
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN:
            mouse_pos = pygame.mouse.get_pos()
            
            if not text_rect.collidepoint(mouse_pos):
                for _ in range(5):
                    fireworks.append(Firework(mouse_pos[0], mouse_pos[1]))
                
                if not music_started:
                    pygame.mixer.music.play()
                    music_started = True

    for firework in fireworks[:]:
        firework.update()
        firework.draw(screen)
        
        if firework.life <= 0:
            fireworks.remove(firework)

    pygame.display.flip()
    
    clock.tick(60)

pygame.quit()