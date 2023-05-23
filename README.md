import pygame
pygame.init()

color_fon = (200, 255, 255)
mw = pygame.display.set_mode((500, 500))
mw.fill(color_fon)
clock = pygame.time.Clock()

class Area():
  def __init__(self, x, y, width, height):
      self.rect = pygame.Rect(x, y, width, height)
      self.fill_color=color_fon
  def create(self):
      pygame.draw.rect(mw, color_fon, self.rect)

class Picture(Area):
  def __init__(self, filename, x, y, width, height):
      self.rect = pygame.Rect(x, y, width, height)
      self.fill_color=color_fon
      self.image = pygame.image.load(filename)
    
  def draw(self):
      mw.blit(self.image, (self.rect.x, self.rect.y))

class Label(Area):
  def set_text(self, text, fsize=12, text_color=(0, 0, 0)):
      self.image = pygame.font.SysFont('verdana', fsize).render(text, True, text_color)

  def draw(self, shift_x=0, shift_y=0):
      self.create()
      mw.blit(self.image, (self.rect.x + shift_x, self.rect.y + shift_y))

ball = Picture('ball.png', 160, 200, 50, 50)
platform = Picture('platform.png', 200, 430, 100, 30)

monsters = []
x = 5
y = 5
count = 9
for i in range(3):
    for m in range (count):
        m = Picture('enemy.png',x, y, 50, 50)
        monsters.append(m)
        x = x + 55
    count -= 1
    x = 27*(9-count)
    y = 55*(9-count)

move_right = False
move_left = False
game_over = False
dx = 3
dy = 3    

while game_over != True:
    ball.create()
    platform.create()
    for event in pygame.event.get():
      
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_RIGHT:
                move_right = True 
            if event.key == pygame.K_LEFT:
                move_left = True 
    
        elif event.type == pygame.KEYUP:
            if event.key == pygame.K_RIGHT:
                move_right = False
            if event.key == pygame.K_LEFT:
                move_left = False
    
    if move_right:
        platform.rect.x +=5
    if move_left:
        platform.rect.x -=5
    
    ball.rect.x += dx
    ball.rect.y += dy
    if  ball.rect.y < 0:
        dy *= -1 
    if ball.rect.x > 450 or ball.rect.x < 0:
        dx *= -1
    if platform.rect.colliderect(ball.rect):
        dy *= -1

    if ball.rect.y > 450:
        time_text = Label(150,150,50,50)
        time_text.set_text('YOU LOSE',60, (255,0,0))
        time_text.draw(10, 10)
        game_over = True
        
    if len(monsters) == 0:
        time_text = Label(150,150,50,50)
        time_text.set_text('YOU WIN',60, (0,200,0))
        time_text.draw(10, 10)
        game_over = True
    
    
    for m in monsters:
        m.draw()
        if m.rect.colliderect(ball.rect):
            monsters.remove(m)
            m.create()
            dy *= -1

      
    platform.draw()
    ball.draw()
    pygame.display.update()
    clock.tick(40)
