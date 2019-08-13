# Create a FlappyBird enviroment

Same as other games in this repository, Pygame is the toolbox to render background video.
In the snake game, I simply draw rectangular and line to visualize the movement of snake and snack. Pygame receives the keyboard values from human and performs actions on the snake_v0 environment.
I take one step further to utilize images and text display rather than simply Pygame drawing to render environment in this game.

### Import images and Pygame code to display (Need various images to demonstrates the concepts)

I learnt to visualize an image, rotate an image, generate "flappy" effect, and display a text on a Pygame window.

<details><summary>Codes and Techniques</summary>
<p>

To access the images in other folder: **os.path.join('name1', 'name2')**. 
to load an image: **pygame.image.load(dir)** - its reversed function is *pygame.image.save(dir)* to save an image.
Several useful transforms are **scale, flip, rotate

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

</p>
</details>



**To rotate a loaded image**

<details><summary>Codes and Techniques</summary>
<p>
```python
topleft, angle = (self.x, self.y), self.tilt
rotated_image = pygame.transform.rotate(image, angle)
new_rect = rotated_image.get_rect(center = image.get_rect(topleft = topleft).center)

win.blit(rotated_image, new_rect.topleft)
```
</p>
</details>

**Interesting code to visualize the "flappy" movements** 

The code can be shorter by writing some mapping functions, however, I prefer the code written this way for better clarity as I immediately understand the code in the first time I see it.

<details><summary>Codes and Techniques</summary>
<p>
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
</p>
</details>

* Text box as an image *

Similar to display an image, we can **render** from a **pygame.Sysfont**

<details><summary>Codes and Techniques</summary>
<p>

```python
pygame.font.init()
STAT_FONT = pygame.font.SysFont("comicsans", 50)
score_label = STAT_FONT.render('Scores: ' + str(int(self.score)), 2, (255,255,255))
self.win.blit(score_label, (WIN_WIDTH - score_label.get_width()-15, 10))
pygame.display.update()
```

</p>
</details>




## Environment Objects

FlappyBird environment comprises of (1) background + moving base (2) a series of pipes (3) a bird or a number of birds

#### Background and Moving Bases

We need a background image with moving bases. For the movement, we need to create a new object.  

<details><summary>Codes</summary>
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

In the upper FlappyBird object:

```python
class FlappyBird():
    def __init__(self, nbirds = 1):
        self.win = pygame.display.set_mode((WIN_WIDTH, WIN_HEIGHT))
        self.base = Base()
        
    def render(self):
        self.win.blit(bg_img, (0,0))  #Draw background
        self.base.draw(self.win)
```

</p>
</details>



#### A series of pipes

A pipe class consists of 2 pipes in the same horizontal location, one flipped on top and other in the bottom.
    
<details><summary>Codes</summary>
<p>
    
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
    
    
#### A number of birds

A bird class consists of a single bird. The movement of the bird must follow Newton's equations, so it adds some levels of complexity here. Not quite sure the code I wrote here is correct, yet the displayed movement looks reasonable for me.

(1) Movement for locations + movement for rotations - Take some time to find tune all the parameters.  
(2) Need to check of the bird and the pipe area overlap for the collision. This is an interesting algorithm problem.  
[LeetCode Rectangle Overlap](https://leetcode.com/problems/rectangle-overlap/). I would prefer to even calculate the overlaping area as well [LeetCode Rectangle Area](https://leetcode.com/problems/rectangle-area/)    
(3) To build a controller to play this game, we need the information of the location of one bird with regard to the locations of the pipes. So I write an additional function cal_bird_states() that sometimes draw the lines to the end of the pipes as well.  
    
<details><summary>Codes for Bird class</summary>
<p>

```python
class Bird():
    BIRD_WIDTH, BIRD_HEIGHT = bird_imgs[0].get_width(), bird_imgs[0].get_height()
    
    def __init__(self, x, y):
        self.imgs = bird_imgs
        self.img_count = 0
        self.flip_every = 1
        
        self.x, self.y = x, y
        self.tilt = 0  # degrees to tilt
        
        #Newton equations
        self.velocity, self.rotation_velocity = 0, 0
        self.gravity, self.rotation_gravity  = 4.5, 5
        self.isCollided = False
        
    def jump(self):
        self.velocity = -self.gravity
        self.rotation_velocity = 5
        
        self.y += self.velocity
        self.tilt = 10
        #self.tilt = self.tilt + self.rotation_velocity if self.tilt>-70 and self.tilt<30 else self.tilt
        
    def move(self):
        self.velocity += self.gravity
        self.rotation_velocity = -self.rotation_gravity
        
        self.y += self.velocity
        self.tilt = self.tilt + self.rotation_velocity if self.tilt>-70 and self.tilt<30 else self.tilt
        
        
    def draw(self, win):
        self.img_count += 1
        if   self.img_count< 1*self.flip_every: index = 0
        elif self.img_count< 2*self.flip_every: index = 1
        elif self.img_count< 3*self.flip_every: index = 2
        elif self.img_count< 4*self.flip_every: index = 1
        else:
            index = 0
            self.img_count = 0
        
        image = self.imgs[index]
        
        topleft, angle = (self.x, self.y), self.tilt
        rotated_image = pygame.transform.rotate(image, angle)
        new_rect = rotated_image.get_rect(center = image.get_rect(topleft = topleft).center)
        
        win.blit(rotated_image, new_rect.topleft)
        
    def collision(self, pipes):
        for pipe in pipes:
            x = min(self.x + self.BIRD_WIDTH - pipe.x, pipe.x + pipe.PIPE_WIDTH - self.x)
            y = max(self.y + self.BIRD_HEIGHT-pipe.y, pipe.y_flip + pipe.PIPE_HEIGHT - self.y)
            if x>0 and y>0:
                self.isCollided = True
                return True
        self.isCollided = False
        return False
    
    def cal_bird_states(self, win, pipes, draw = False):
        middle = self.y + int(self.BIRD_WIDTH/2)
        if draw: pygame.draw.rect(win, (255,0,0), (self.x, self.y,10,10))
        
        for pipe in pipes:
            if draw: pygame.draw.line(win, (255,0,0), (self.x+self.BIRD_WIDTH, middle), (pipe.x+ pipe.PIPE_WIDTH, pipe.y-pipe.GAP))
            if draw: pygame.draw.line(win, (255,0,0), (self.x+self.BIRD_WIDTH, middle), (pipe.x+ pipe.PIPE_WIDTH, pipe.y))
            if pipe.x + pipe.PIPE_WIDTH> self.x:
                if draw: pygame.display.update()
                return np.array([middle, pipe.y, pipe.y - pipe.GAP, pipe.x + pipe.PIPE_WIDTH])
#         if draw: pygame.display.update()
        return np.array([middle, pipe.y, pipe.y - pipe.GAP, pipe.x + pipe.PIPE_WIDTH-self.x])
    
```

</p>
</details>
    
In the bigger FlappyBird class, initialize a number of birds
In the step() function, check if the bird is collided with the pipes, and move the collided bird.

<details><summary>Codes for Bird in FlappyBird class</summary>
<p>

```python
class FlappyBird():
    def __init__(self, nbirds = 1):
        self.nbirds = nbirds
        self.birds = [Bird(random.randrange(int(WIN_WIDTH*0.05),int(WIN_WIDTH*0.7)),
                           random.randrange(int(WIN_HEIGHT/8),int(WIN_HEIGHT*4/5))) 
                      for i in range(self.nbirds)]
                      
    def reset(self):
        self.birds = [Bird(random.randrange(int(WIN_WIDTH*0.05),int(WIN_WIDTH*0.7)),
                           random.randrange(int(WIN_HEIGHT/8),int(WIN_HEIGHT*4/5))) 
                      for i in range(self.nbirds)]

    def render(self):
        for bird in self.birds:
            bird.draw(self.win)
    
    def step(self, action):
  
        # move the birds and check collision
        remaining_birds = []
        for i,bird in enumerate(self.birds):
            if action[i] == 0: bird.move()
            else:              bird.jump()
            
            done = bird.collision(self.pipes)
           
            if not done:
                remaining_birds.append(bird)
 
        self.birds = remaining_birds
```

</p>
</details>
    
    
    
#### Score, Generation, States, Rewards, and render(), step(), reset()

Several variables for storing score/generation/reward.

Also, following the standard in gym environment, I would prefer to return states for reset(), and next_states, rewards, dones for reset().

I also need variables for the action_space and environment_space, which I did not follow the standard for now...
For env.states, I simply calculate the states of the first bird.

<details><summary>Codes for state/action/reward in FlappyBird </summary>
<p>

```python
class FlappyBird():
    def __init__(self, nbirds = 1):
        self.states = self.birds[0].cal_bird_states(self.win, self.pipes)
        self.score, self.gen = 0, 0
    
    def reset(self):
        self.score = 0
        self.gen += 1        
        self.states = self.birds[0].cal_bird_states(self.win, self.pipes)
        return self.states
        
    def render(self):
        self.update_score()
        pygame.display.update()
    
    def update_score(self):
        score_label = STAT_FONT.render('Scores: ' + str(int(self.score)), 2, (255,255,255))
        self.win.blit(score_label, (WIN_WIDTH - score_label.get_width()-15, 10))
        
        score_label = STAT_FONT.render('Gen: ' + str(self.gen), 1, (255,255,255))
        self.win.blit(score_label, (10,10))
        
        score_label = STAT_FONT.render('Alive: ' + str(len(self.birds)), 1, (255,255,255))
        self.win.blit(score_label, (10, 50))
            
    def step(self, action):
        self.score += VEL*1.0/self.pipe_dist
        
        states, rewards, dones = [], [], []
        for i,bird in enumerate(self.birds):
                
            state = bird.cal_bird_states(self.win, self.pipes)
            done = bird.collision(self.pipes)
            
            reward = -2 if done else 1.0/self.pipe_dist
            states.append(state)
            rewards.append(reward)
            dones.append(done)

        self.states = states[0]
        return np.array(states), np.array(rewards), np.array(dones)

```

</p>
</details>




# Get actions and play one episode



<details><summary>Codes for Calculate  in FlappyBird class</summary>
<p>

```python


```

</p>
</details>

### Shows gif images of the play here.

# Smart agent learning to play FlappyBirds

The intelligent of the agent here is built from the famous Q-learning equations based on the relationship between next_states, rewards, dones. Every actions and results are learnt, the the Q-learning equations give higher score for the action that maximizes the discounted rewards. (Sounds simple?/amazing?/hard-to-believe?...)

<details><summary>Codes for Calculate  in FlappyBird class</summary>
<p>

```python


```

</p>
</details>



<details><summary>Codes for Calculate  in FlappyBird class</summary>
<p>

```python


```

</p>
</details>
