# Python-codes
This project combines a fireworks display with music in Python using Pygame. It features colorful animations triggered by mouse clicks and plays background music. Users can easily add their own music files. I aimed to enhance my coding skills while creating a visually engaging experience. This marks my first step into the world of game development!

***MOST IMPORTANTLY THE MUSIC USED HERE WAS PRESENT ON MY PC THAT MEANS I'VE DOWNLOADED THE MUSIC AS MP3 FILE***

import pygame
import random
import math

# Initialize Pygame
pygame.init()

# Screen dimensions
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))

# Set title
pygame.display.set_caption('Abhishek Fireworks')

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)

# Font
font = pygame.font.SysFont('Arial', 72)

# Text settings
text = font.render('CrimsonAbhishek', True, WHITE)
text_rect = text.get_rect(center=(WIDTH // 2, HEIGHT // 2))

# Load the music
pygame.mixer.music.load(r'C:\Users\MSI\Desktop\we_fell_in_love_in_october.mp3')  # Ensure this file is in the specified directory

# Clock for controlling frame rate
clock = pygame.time.Clock()

# Firework class to handle the particles
class Firework:
    def __init__(self, x, y):  # Accept x and y position
        self.x = x
        self.y = y
        self.angle = random.uniform(0, 2 * math.pi)
        self.speed = random.uniform(1, 3)  # Slowed down speed
        self.radius = random.randint(3, 6)  # Varying size of particles
        self.color = [random.randint(0, 255) for _ in range(3)]
        self.life = 60  # Number of frames the firework lives

    def update(self):
        self.x += math.cos(self.angle) * self.speed
        self.y += math.sin(self.angle) * self.speed
        self.life -= 1
        self.radius *= 0.97  # Shrink over time

    def draw(self, screen):
        pygame.draw.circle(screen, self.color, (int(self.x), int(self.y)), int(self.radius))

# Star-shaped firework effect
class StarFirework(Firework):
    def draw(self, screen):
        for i in range(5):  # Draw a 5-pointed star
            angle = self.angle + (i * (2 * math.pi / 5))  # 5 evenly spaced points
            x2 = self.x + math.cos(angle) * self.radius * 3
            y2 = self.y + math.sin(angle) * self.radius * 3
            pygame.draw.line(screen, self.color, (self.x, self.y), (x2, y2), 2)

# List to store fireworks
fireworks = []

# Flags and state variables
running = True
music_started = False  # Track if music has started

# Variables to manage the RGB gradient background
r, g, b = 255, 0, 0  # Start with red color
change_speed = 1  # Speed of color change
phase = 0  # To track which part of the RGB transition we are in

# Main loop
while running:
    # Gradually change RGB values through the full color spectrum
    if phase == 0:  # Red to Yellow (increase green)
        g += change_speed
        if g >= 255:
            g = 255
            phase = 1
    elif phase == 1:  # Yellow to Green (decrease red)
        r -= change_speed
        if r <= 0:
            r = 0
            phase = 2
    elif phase == 2:  # Green to Cyan (increase blue)
        b += change_speed
        if b >= 255:
            b = 255
            phase = 3
    elif phase == 3:  # Cyan to Blue (decrease green)
        g -= change_speed
        if g <= 0:
            g = 0
            phase = 4
    elif phase == 4:  # Blue to Magenta (increase red)
        r += change_speed
        if r >= 255:
            r = 255
            phase = 5
    elif phase == 5:  # Magenta to Red (decrease blue)
        b -= change_speed
        if b <= 0:
            b = 0
            phase = 0  # Loop back to the start

    # Fill the background with the changing gradient color
    screen.fill((r, g, b))
    
    # Draw the centered text
    screen.blit(text, text_rect)
    
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN:
            mouse_pos = pygame.mouse.get_pos()
            
            # Check if the mouse is not over the text
            if not text_rect.collidepoint(mouse_pos):
                # Generate fireworks at the mouse position
                for _ in range(5):  # Create 5 fireworks at a time
                    fireworks.append(Firework(mouse_pos[0], mouse_pos[1]))  # Pass mouse position
                
                # Start playing music on the first click
                if not music_started:
                    pygame.mixer.music.play()
                    music_started = True

    # Update and draw fireworks
    for firework in fireworks[:]:
        firework.update()
        firework.draw(screen)
        
        # Remove the firework if its life is over
        if firework.life <= 0:
            fireworks.remove(firework)

    # Update the display
    pygame.display.flip()
    
    # Frame rate control
    clock.tick(60)

# Quit Pygame
pygame.quit()
