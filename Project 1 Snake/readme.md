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

Display text
```python
pygame.font.init()
STAT_FONT = pygame.font.SysFont("comicsans", 50)
score_label = STAT_FONT.render('Scores: ' + str(int(self.score)), 2, (255,255,255))
self.win.blit(score_label, (WIN_WIDTH - score_label.get_width()-15, 10))
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

# Step() function

(1) receive action: LEFT, RIGHT, UP, DOWN   
(2) check if snake hit itself   
(3) check if the snake eats the food --> generate a new food

```python
pygame.font.init()
FONT = pygame.font.SysFont("comicsans", 30)

class SnakeGame():    
    def __init__(self, width, nx, ny, 
                 color_food = (100,0,0), color_head = (0,150,0), color_snake = (0,0,150)):

        self.score = 0
        self.snake = Snake(self,color_head, color_snake)
        self.add_food()
        
    def render(self):
        self.draw_grid()
        self.snake.draw()
        self.food.draw()
        self.update_score()
        pygame.display.update()
        
    def step(self,action):
        self.snake.move(self,action)
        
        #check if snake eats the food, then create new food
        if (self.food.x,self.food.y) == self.snake.body[0]:  self.add_food()
            
        #check if the snake hit itself
        if len(self.snake.body) != len(set(self.snake.body)):  return False
        else: return True
        
    def add_food(self):
        valid = {(x,y) for x in range(self.nx) for y in range(self.ny)} - set(self.snake.body)
        loc = random.sample(valid,1)[0]
        self.food = Food(self,self.color_food, loc)
        
    ################ Helper function to draw ###########
    def update_score(self):
        self.score = len(self.snake.body) - 3
        remark = FONT.render('Scores: ' + str(self.score), 2, (0,0,0))
        self.win.blit(remark, (10, self.Y+10))
    
    def draw_grid(self):
            
class Food():
        
class Snake():
    def __init__(self, env, color_head, color_snake):
        
        self.dirx, self.diry = 1,0
        self.body = [(3,3),(3,4),(3,5)]
    
    def move(self,env, action):
        if action != (0,0):
            self.dirx, self.diry = action
        x,y = self.body[0]
        next_head_x, next_head_y = (x+self.dirx)%self.nx, (y+self.diry)%self.ny
        
        # if hit the food then keep its tail, else remove
        if (next_head_x, next_head_y) == (env.food.x, env.food.y):
            self.body = [(next_head_x, next_head_y)] + self.body
        else:
            self.body = [(next_head_x, next_head_y)] + self.body[:-1]
    
    def draw(self):

```
![image 2](https://github.com/huythong267/Game_and_AI/blob/master/Project%201%20Snake/save_imgs/check2.png)

Well, the game is ready now!

# Human interaction

```python
def action_forward(env, mode = 'random'):
    key_map = {'LEFT': (-1,0), 'RIGHT': (1,0), 'UP': (0,-1), 'DOWN': (0,1), 'NONE': (0,0)}
    
    if mode == 'human':
        ans  = 'NONE'
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()

            keys = pygame.key.get_pressed()
            for key in keys:
                if keys[pygame.K_LEFT]:    ans = 'LEFT'
                elif keys[pygame.K_RIGHT]: ans = 'RIGHT'
                elif keys[pygame.K_UP]:    ans = 'UP'
                elif keys[pygame.K_DOWN]:  ans = 'DOWN'
        return key_map[ans]
    
    if mode == 'random':
        choices = []
        head_x, head_y = env.snake.body[0]
        locations = set([(x,y) for x,y in env.snake.body])
        for k, (dx,dy) in key_map.items():
            next_head = ((head_x+dx)%env.nx,(head_y+dy)%env.ny)
            if next_head not in locations:
                choices.append(k)
        ans =  random.choice(choices) if choices else 'NONE'
        return key_map[ans]
```

```python

```
