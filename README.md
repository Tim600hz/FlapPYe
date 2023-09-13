# FlapPYe
Uma versão única do Flappy Bird em Python, com troca de skins ao longo dos pontos

import sys
import pygame
import random
from pygame.locals import QUIT

pygame.init()

SCREEN_HEIGHT = 512
SCREEN_WIDTH = 288

score = 0
pipe_jumped = 0
score_milestone = 10

score_font = pygame.font.Font(None, 48)
score_colour = (255, 139, 47)  #Laranja

DISPLAYSURF = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))

pygame.display.set_caption('FloppyBirds')

bg = pygame.image.load("bg.png").convert()
imp = pygame.image.load("bird1.png").convert()
base = pygame.image.load("base.png").convert()
pipe = pygame.image.load("pipe.png").convert()

#Classe Passaro

class Bird:
    def __init__(self):
      self.wings_picture = [
          pygame.image.load("bird1.png"),
          pygame.image.load("bird2.png"),
          pygame.image.load("bird3.png"),
      ]
      
      self.current_image = 0
      self.rect = self.wings_picture[self.current_image].get_rect()
      self.rect.centerx = SCREEN_WIDTH //2 
      self.rect.centery = SCREEN_HEIGHT //2
      self.velocidade_y = 0
      self.frame_counter = 0 # Contabiliza os frames para troca de imagens
      
    def refresh(self):
      self.speed_y += 1
      self.rect.centery += self.speed_y
      self.frame_counter += 1 # troca as asas

# Faz a troca das asas a cada 10 frames
      if self.frame_counter %10 == 0:
        self.current_image = (self.current_image + 1) % 3
        self.rect = self.wings_picture[self.current_image].get_rect()
        self.rect.centerx = SCREEN_WIDTH // 2
        
    def jump(self):
      self.speed_y = -10

#Classe dos canos

class Pipe:

  def __init__(self):
    self.image = pygame.image.load("pipe.png")
    self.rect = self.image.get_rect()
    self.rect.x = SCREEN_WIDTH
    self.rect.y = random.randint(200, 400)  #altura aleatória do cano.
    self.velocidade_x = -5

  def atualizar(self):
    self.rect.x += self.velocidade_x

  def out_of_bounds(self):
    return self.rect.right < 0

##############################################

# Using blit to copy content from one surface to other
bird = Bird()
Pipes = []
while True:
  for event in pygame.event.get():

    DISPLAYSURF.blit(bg, (0, 0))
    DISPLAYSURF.blit(imp, (40, 40))

    def restart():
      bird.rect.centerx = SCREEN_WIDTH // 2
      bird.rect.centery = SCREEN_HEIGHT // 2
      bird.speed_y = 0
      Pipes.clear()

    if event.type == QUIT:
      pygame.quit()
      sys.exit()
    elif event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE:
      bird.jump()
###################################
Pipes = []
bird.refresh()
clock = pygame.time.Clock()

###################################

DISPLAYSURF.fill((255, 255, 255))
DISPLAYSURF.blit(bird.image, bird.rect)
pygame.display.update()

# Cria um novo obstáculo a cada 100 frames

if pygame.time.get_ticks() % 100 == 0:
  Pipe = Pipe()
  Pipes.append(Pipe)

# Atualiza a posição dos obstáculos e remove-os se estiverem fora da tela
for Pipe in Pipes:
  Pipe.atualizar()
  if Pipe.fora_da_tela():
    Pipes.remove(Pipe)

# Verifica colisões com obstáculos
for Pipe in Pipes:
  if bird.rect.colliderect(Pipe.rect):
    bird.current_image = pygame.image.load("FlappyDead.png").convert()

    restart()
    # Colisão detectada, você pode adicionar a lógica de fim de jogo aqui
    pass

# Cano superado, contabilizando a pontuação
  if Pipe.rect.right < bird.rect.left:
    pipe_jumped += 1

    # Verifique os marcos de pontuação
    if pipe_jumped == 5:
      score += 70
    elif pipe_jumped % 10 == 0:
      score += 100

score_display = score_colour.render(f'score{score}', True, score_colour)
DISPLAYSURF.blit(score_display, (10, 10))
# Exiba a mensagem de "Parabéns" após superar 10 obstáculos
if pipe_jumped % 10 == 0:
  print("Uhuu! 10 canos pulados, arrombado!")

pygame.display.update()
