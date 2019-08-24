# My first game

Interestingly, a snake game is written in python

## Pygame tutorial

Render a pygame enviroment or initialize the pygame video system

```python
self.win = pygame.display.set_mode((self.width, self.width))  #initialize a surface, we will draw on this surface
pygame.display.update()
pygame.display.quit()
```

Drawing
```python
pygame.draw.circle(self.win, (0,0,0), circle1, radius)
pygame.draw.rect(self.win, color, (x*size+1, y*size+1, size-2, size-2))
pygame.draw.line(self.win, (255,255,255), (d, 0), (d, self.width))
```

User input
```python
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            run = False
            
        if event.type == pygame.MOUSEBUTTONDOWN:
            x,y = event.pos
        
        if event.type==pygame.KEYDOWN :
            if event.key==pygame.K_r:
            if event.key==pygame.K_q:
```

# Snake environment
I want to follow the `gym enviroment`, thus this environment must has following function:   
(1) env.render()    
(2) env.reset()     
(3) env.step() 

First, write some very basic drawings for this environment, implying grids for the snake to move. 
No object is inside the env at the moment.

```python
class SnakeGame():    
    def __init__(self, width, nx, ny, 
                 color_food = (100,0,0), color_head = (0,150,0), color_snake = (0,0,150)):
        self.width = width
        self.nx, self.ny = nx, ny
        self.X, self.Y = self.width*self.nx, self.width*self.ny
        self.win = pygame.display.set_mode((self.width*self.nx, self.width*(self.ny+2)))
        
    def render(self):
        self.draw_grid()
        pygame.display.update()
    
    ################ Helper function to draw ###########
    def draw_grid(self):
        pygame.draw.rect(self.win, (255,255,255), (0, 0, self.X, self.Y+2*self.width))
        for y in range(self.ny+1):
            pygame.draw.line(self.win, (0,0,0), (0, y*self.width), (self.X, y*self.width))
        for x in range(self.nx+1):
            pygame.draw.line(self.win, (0,0,0), (x*self.width,0), (x*self.width,self.Y))
```

Code to view the pygame window:

```python
snake = SnakeGame(35,15,10)
clock = pygame.time.Clock()
run = True
while run:
    clock.tick(5)
    snake.render()
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            run = False
            pygame.image.save(snake.win,os.path.join('save_imgs','check1.png'))
            #pygame.display.quit()
            break
```

## Objects
We have 2 objects inside the game.   
(1) Snake: a list of cubes where the head should be drawn differently   
(2) Food: random location on the window

Static objects need 2 functions: (1)__init__() and (2) draw()   
Dynamic object needs the 3rd: (3) move()


```python
class SnakeGame():    
    def __init__(self, width, nx, ny, 
                 color_food = (100,0,0), color_head = (0,150,0), color_snake = (0,0,150)):
        
        self.snake = Snake(self,color_head, color_snake)
        self.food = Food(self,color_food)
        
        
    def render(self):
        self.snake.draw()
        self.food.draw()
        
    def step(self):
        self.snake.move([1,0])
        
    ################ Helper function to draw ###########
    def draw_grid(self):

class Food():
    def __init__(self,env,color):
        self.win, self.width, self.color = env.win, env.width, color
        self.nx, self.ny = env.nx, env.ny
        self.x, self.y = 2,1  #need to be random later
        
    def draw(self):
        pygame.draw.rect(self.win, self.color, 
                         (self.x*self.width+1, self.y*self.width+1, self.width-1, self.width-1))
        
class Snake():
    def __init__(self, env, color_head, color_snake):
        self.win, self.width = env.win, env.width
        self.nx, self.ny = env.nx, env.ny
        self.color_head, self.color_snake = color_head, color_snake
        
        self.body = [(3,3),(3,4),(3,5)]
    
    def move(self, action):
        dx, dy = action
        x,y = self.body[0]
        next_head_x, next_head_y = (x+dx)%self.nx, (y+dy)%self.ny
        self.body = [(next_head_x, next_head_y)] + self.body[:-1]
    
    def draw(self):
        for i,(x,y) in enumerate(self.body):
            if i==0:
                pygame.draw.rect(self.win, self.color_head, (x*self.width+1, y*self.width+1, self.width-1, self.width-1))
            else:
                pygame.draw.rect(self.win, self.color_snake,(x*self.width+1, y*self.width+1, self.width-1, self.width-1))
```

![image 1](https://github.com/huythong267/Game_and_AI/blob/master/Project%201%20Snake/save_imgs/check1.png)

```python

```


```python

```

```python

```
