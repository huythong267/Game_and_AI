# Create a FlappyBird enviroment

Same as other games in this repository, Pygame is the toolbox to render background video.
In the snake game, I simply draw rectangular and line to visualize the movement of snake and snack. Pygame receives the keyboard values from human and performs actions on the snake_v0 environment.
I take one step further to utilize images and text display rather than simply Pygame drawing to render environment in this game.

### Import images and Pygame code to display

I learnt to visualize an image, rotate an image, generate "flappy" effect, and display a text on a Pygame window.

<details><summary>Codes and Techniques</summary>
<p>

To access the images in other folder: *os.path.join('name1', 'name2')*.
*pygame.image.load(dir)* to load an image - its reversed function is *pygame.image.save(dir)* to save an image.
Several useful transforms are

```python
pipe_img = pygame.image.load(os.path.join('imgs','pipe.png'))
pipe_img = pygame.transform.scale(pipe_img, (int(WIN_WIDTH//8), WIN_HEIGHT))
pipe_img_flip = pygame.transform.flip(pipe_img,False, True)

bird_imgs = [pygame.image.load(os.path.join('imgs','bird{}.png'.format(x))) for x in range(1,4)]
```

**To display a loaded image**
```python
win.blit(self.img, (self.x, self.y))
```

**To rotate a loaded image**
```python
topleft, angle = (self.x, self.y), self.tilt
rotated_image = pygame.transform.rotate(image, angle)
new_rect = rotated_image.get_rect(center = image.get_rect(topleft = topleft).center)

win.blit(rotated_image, new_rect.topleft)
```

**Interesting code to visualize the "flappy" movements** 

The code can be shorter by writing some mapping functions, however, I prefer the code written this way for better clarity as I immediately understand the code in the first time I see it.

```python
self.img_count += 1
if   self.img_count< 1*self.flip_every: index = 0
elif self.img_count< 2*self.flip_every: index = 1
elif self.img_count< 3*self.flip_every: index = 2
elif self.img_count< 4*self.flip_every: index = 1
else:
    index = 0
    self.img_count = 0

image = self.imgs[index]
```
* Text box as an image *

Similar to display an image, 

```python
pygame.font.init()
STAT_FONT = pygame.font.SysFont("comicsans", 50)
score_label = STAT_FONT.render('Scores: ' + str(int(self.score)), 2, (255,255,255))
self.win.blit(score_label, (WIN_WIDTH - score_label.get_width()-15, 10))
pygame.display.update()
```

</p>
</details>

### Environment Objects

FlappyBird environment comprises of (1) background + moving base (2) a series of pipes (3) a bird or a number of birds

<details><summary>Background and Moving Bases</summary>
<p>

```python
class Base():
    def __init__(base):
        base.img = base_img
        base.x, base.y  = 0, WIN_HEIGHT- int(WIN_HEIGHT/5)
        
    def move(base):
        base.x += -VEL if base.x>-WIN_WIDTH else -VEL + WIN_WIDTH

    def draw(base, win):
        for i in [0,1]:
            win.blit(base_img, (base.x + i*WIN_WIDTH,base.y))

```
</p>
</details>
    
<details><summary>A series of Pipes</summary>
<p>
    
A pipe class consists of 2 pipes in the same horizontal location, one flipped on top and other in the bottom.

```python
class Pipe():
    GAP = int(WIN_HEIGHT//4)
    RANGE_LOW, RANGE_HIGH = int(WIN_HEIGHT//16), WIN_HEIGHT- int(WIN_HEIGHT/5)
    PIPE_WIDTH, PIPE_HEIGHT = pipe_img.get_width(), pipe_img.get_height()

    def __init__(self, x):
        self.img, self.img_flip = pipe_img, pipe_img_flip

        self.x = x
        self.y = random.randrange(self.GAP + self.RANGE_LOW, self.RANGE_HIGH-self.RANGE_LOW)
        self.y_flip = self.y - self.GAP - self.PIPE_HEIGHT
        self.isValid = 1

    def move(self):
        self.x += -VEL
        if self.x<-self.PIPE_WIDTH: self.isValid = 0

    def draw(self, win):
        win.blit(self.img, (self.x, self.y))
        win.blit(self.img_flip, (self.x, self.y_flip))  
```

In the main environment, we need to create a series of pipes that keep running over the Pygame window

```python
            
class FlappyBird():
    def __init__(self, nbirds = 1):
        self.pipe_dist = int(WIN_WIDTH/3)
        self.pipes = [Pipe(self.pipe_dist*i) for i in range(2,6)]
        
    def reset(self):
        self.pipes = [Pipe(self.pipe_dist*i) for i in range(2,6)]
    
    def render(self):
        self.win.blit(bg_img, (0,0))
        self.base.draw(self.win)
        for pipe in self.pipes:
            pipe.draw(self.win)
    
     def step(self, action):
        #redraw the pipe if it goes out of screen
        if self.pipes[0].isValid == 0:
            new_pipe = Pipe(self.pipes[-1].x+self.pipe_dist)
            self.pipes = self.pipes[1:] +[new_pipe]
        self.base.move()
        for pipe in self.pipes:
            pipe.move()
```

</p>
</details>
    
    
    
    
    
