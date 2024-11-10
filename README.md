import pygame
from pygame.locals import *
from OpenGL.GL import *
from OpenGL.GLUT import *
from OpenGL.GLU import *
import random

# Asetukset
WIDTH, HEIGHT = 800, 600
FPS = 60
player_size = 1
player_speed = 0.1
jump_strength = 0.2

# Pygame alustus
pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT), DOUBLEBUF | OPENGL)
pygame.display.set_caption("3D Super Mario Tyylinen Peli")

# Kameran ja perspektiivin asetukset
glEnable(GL_DEPTH_TEST)
glMatrixMode(GL_PROJECTION)
glLoadIdentity()
gluPerspective(45, (WIDTH / HEIGHT), 0.1, 50.0)
glTranslatef(0.0, 0.0, -10)

# Pelaajan asetukset
player_pos = [0, 0, 0]  # Pelaajan alkuperäinen sijainti
player_velocity_y = 0  # Hypyn voimakkuus
on_ground = False  # Tarkistaa, onko pelaaja maassa

# Maapinnan asetukset
platforms = [
    [-3, -2, -5, 6, 0.1, 2],
    [2, -2, -5, 6, 0.1, 2],
    [-2, -2, -8, 4, 0.1, 4]
]

# Piirrä alustat (maakappaleet)
def draw_platforms():
    for plat in platforms:
        glPushMatrix()
        glTranslatef(plat[0], plat[1], plat[2])
        glBegin(GL_QUADS)
        glColor3fv((0.5, 0.5, 0.5))
        glVertex3f(-plat[3] / 2, 0, plat[5] / 2)
        glVertex3f(plat[3] / 2, 0, plat[5] / 2)
        glVertex3f(plat[3] / 2, 0, -plat[5] / 2)
        glVertex3f(-plat[3] / 2, 0, -plat[5] / 2)
        glEnd()
        glPopMatrix()

# Piirrä pelaaja (kappale)
def draw_player():
    glPushMatrix()
    glTranslatef(player_pos[0], player_pos[1], player_pos[2])
    glColor3fv((0, 0, 1))  # Sininen väri pelaajalle
    glutSolidCube(player_size)
    glPopMatrix()

# Liikkuminen ja hyppääminen
def move_player():
    global player_pos, player_velocity_y, on_ground

    keys = pygame.key.get_pressed()
    # Liikkuminen vasemmalle ja oikealle
    if keys[K_LEFT]:
        player_pos[0] -= player_speed
    if keys[K_RIGHT]:
        player_pos[0] += player_speed
    # Hyppy
    if keys[K_SPACE] and on_ground:
        player_velocity_y = jump_strength
        on_ground = False
    
    # Painovoima ja liikkuminen
    player_velocity_y -= 0.01  # Painovoima
    player_pos[1] += player_velocity_y
    
    # Maahan osuminen
    if player_pos[1] <= -2.0:  # Maahan osuminen
        player_pos[1] = -2.0
        player_velocity_y = 0
        on_ground = True

# Päälooppi
def main():
    clock = pygame.time.Clock()
    running = True

    while running:
        screen.fill((0, 0, 0))  # Musta tausta
        glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT)
        
        # Tapahtumat
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
        
        # Liikkeet ja pelilogiikka
        move_player()
        
        # Piirrä ympäristö
        draw_platforms()
        draw_player()
        
        pygame.display.flip()
        clock.tick(FPS)
    
    pygame.quit()

# Aloita peli
main()
![lataus (1)](https://github.com/user-attachments/assets/6b8f2f89-f726-4afb-8e7b-fdd4f16edd0b)
