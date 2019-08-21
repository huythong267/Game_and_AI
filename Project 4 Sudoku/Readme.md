# Sudoku

Not much object inside this game, at least non is moving, but still easier if I divide into smaller cube. It sounds like the first project on snake by the way.

## Pygame functions

Here, let's do the timer for the sudoku game play

```python

```

### Draw all the Soduku grids

Initialize the sudoku grids:
```python
class Sudoku():
    def __init__(self, WIDTH):
        self.WIDTH = WIDTH
        self.X, self.Y = self.WIDTH*9+4, self.WIDTH*10+5
        self.win = pygame.display.set_mode([self.X, self.Y])
        
    def render(self):
        self.draw_grid()
        pygame.display.update()
        
    def draw_grid(self):
        pygame.draw.rect(self.win, (255,255,255), (0, 0, self.X, self.Y))
        for k in range(10):
            draw = [k*self.WIDTH+2] if k%3 != 0 else [k*self.WIDTH+p for p in range(4)]
            for d in draw:
                pygame.draw.line(self.win, (0,0,0), (d, 0), (d, self.X-2))
                pygame.draw.line(self.win, (0,0,0), (0, d), (self.X, d))    
```

The sudoku window is as follows:
![Image1]()

## Display the number (Fixed and Tentative) on the board

Not the clean code at the moment, but it works

```python
pygame.font.init()
FIX_FONT = pygame.font.SysFont("comicsans", 50)
TEN_FONT = pygame.font.SysFont("comicsans", 20)

class Sudoku():
    def __init__(self, WIDTH):
        self.WIDTH = WIDTH
        self.X, self.Y = self.WIDTH*9+4, self.WIDTH*10+5
        self.win = pygame.display.set_mode([self.X, self.Y])
        self.fixed     = {(x,y): 1 if x%2 == 0 else None for x in range(9) for y in range(9)}
        self.tentative = {(x,y): 2 for x in range(9) for y in range(9)}
        
        
    def render(self):
        self.draw_grid()
        self.update_sudoku_table()
        pygame.display.update()
                        
                        
    ################## Helper function to draw on the pygame board ##################
    
    def update_sudoku_table(self):
        for x in range(9):
            for y in range(9):
                if   self.fixed[(x,y)]    : self.draw_number(x,y, self.fixed[(x,y)])
                elif self.tentative[(x,y)]: self.draw_number(x,y, self.tentative[(x,y)], tentative = True)
        
    def draw_number(self, x, y, num, tentative = False):
        # The location of the text and the font need optimization according to the self.WIDTH
        p = 10 if tentative else int(self.WIDTH//3)
        if tentative: remark = TEN_FONT.render(str(num), 1, (0,0,0))
        else:         remark = FIX_FONT.render(str(num), 1, (0,0,0))
        self.win.blit(remark, (x*self.WIDTH+p, y*self.WIDTH+6))
        #pygame.display.update()
    
    def draw_red_border(self, x, y):
        draw = [(x*self.WIDTH+p, y*self.WIDTH+p) for p in range(4)]
        for X,Y in draw:
            pygame.draw.line(self.win, (255,0,0), (X, Y), (X+self.WIDTH, Y))
            pygame.draw.line(self.win, (255,0,0), (X, Y), (X, Y+self.WIDTH))
            pygame.draw.line(self.win, (255,0,0), (X+self.WIDTH, Y+self.WIDTH), (X+self.WIDTH, Y))
            pygame.draw.line(self.win, (255,0,0), (X+self.WIDTH, Y+self.WIDTH), (X, Y+self.WIDTH))
        pygame.display.update()
        
    def draw_grid(self):
        pygame.draw.rect(self.win, (255,255,255), (0, 0, self.X, self.Y))
        for k in range(10):
            draw = [k*self.WIDTH+2] if k%3 != 0 else [k*self.WIDTH+p for p in range(4)]
            for d in draw:
                pygame.draw.line(self.win, (0,0,0), (d, 0), (d, self.X-2))
                pygame.draw.line(self.win, (0,0,0), (0, d), (self.X, d))    
```

The sudoku window is as follows:
![Image2]()

## Get user input data:
Overall, I need 2 steps: (1) select an area + (2) perform actions (a) delete (b) change tentative number (c) enter if has tentative

(1) When I press on an area, highlight that area in red (If we move to other area, change the square)
(2)(a) If `box` + `del`: delete the (either fixed or tentative) number and return
(2)(b) When I key in a number, adding it in the top-left corner --> `tentative number` and return
(3)(c) If `enter` + `tentative number` then assert the number to the box + return

Now I want to adding the hover for the mouse click and get the input data

**Well, I make a mistake here: To check if I press 'a', I should use *event.key==pygame.K_a*. Instead I use *event.type* :(    
Now it works... **

Not very clean, but well I have the interface for now

```python
pygame.font.init()
FIX_FONT = pygame.font.SysFont("comicsans", 50)
TEN_FONT = pygame.font.SysFont("comicsans", 20)
number_list = [pygame.K_1,pygame.K_2,pygame.K_3,pygame.K_4,pygame.K_5,pygame.K_6,pygame.K_7,pygame.K_8,pygame.K_9]
move_dict = {pygame.K_LEFT: (-1,0), 
             pygame.K_RIGHT: (1,0), 
             pygame.K_UP: (0, -1), 
             pygame.K_DOWN: (0,1)}

class Sudoku():
    def __init__(self, WIDTH):
        self.WIDTH = WIDTH
        self.X, self.Y = self.WIDTH*9+4, self.WIDTH*10+5
        self.win = pygame.display.set_mode([self.X, self.Y])
        self.fixed     = {(x,y): 1 if x%2 == 0 else None for x in range(9) for y in range(9)}
        self.tentative = {(x,y): 2 for x in range(9) for y in range(9)}
        self.box_x, self.box_y = -5,-5
        self.start_time = time.time()
        
    def render(self):
        self.draw_grid()
        self.draw_red_border(self.box_x,self.box_y)
        self.update_sudoku_table()
        self.display_time()
        self.draw_auto_fill()
        pygame.display.update()
#         pygame.time.delay(200)
        
    def step(self):
        run = self.collect_agent_inputs()
        self.box_x, self.box_y = -5,-5
        return run
    
    ##################### Interacting with human inputs #####################
    
    def collect_agent_inputs(self):
        while True:
            self.render()
            for event in pygame.event.get():
                if event.type == pygame.QUIT:       
                    return False
                elif event.type == pygame.KEYDOWN:
                    if event.key in move_dict:
                            dx, dy = move_dict[event.key]
                            self.box_x, self.box_y = (self.box_x+dx)%9 , (self.box_y+dy)%9
                            
                    elif event.key == pygame.K_DELETE:
                            self.fixed[(self.box_x, self.box_y)]     = None
                            self.tentative[(self.box_x, self.box_y)] = None
                            return True
                            
                    elif event.key == pygame.K_RETURN:
                            self.fixed[(self.box_x, self.box_y)]  = self.tentative[(self.box_x, self.box_y)]
                            return True
                        
                    elif event.key in [pygame.K_1 + i for i in range(9)]:
                            self.tentative[(self.box_x, self.box_y)] = event.key - pygame.K_0
                            return True
                
                elif event.type == pygame.MOUSEBUTTONDOWN:  
                    x, y = ((event.pos[0])//self.WIDTH, (event.pos[1])//self.WIDTH)
                    if 0<=x<9 and 0<=y<9:
                        self.box_x, self.box_y = x, y
                    elif y>8:
                        X, Y = event.pos[0], event.pos[1]
                        center_X, center_Y = int(self.WIDTH*3.5), self.Y - int(self.WIDTH*0.5)
                        width_X, width_Y, pad   =  int(self.WIDTH*2.5), int(self.WIDTH*0.4), 5

    ################## Helper function to draw on the pygame board ##################
        
    def draw_number(self, x, y, num, tentative = False):
        p = 10 if tentative else int(self.WIDTH//3)
        if tentative: 
            remark = TEN_FONT.render(str(num), 1, (0,0,0))
            X, Y = x*self.WIDTH + int(self.WIDTH/8), y*self.WIDTH + int(self.WIDTH/8)
        else:         
            remark = FIX_FONT.render(str(num), 1, (0,0,0))
            X, Y = int((x+0.5)*self.WIDTH - remark.get_width()/2), int((y+0.55)*self.WIDTH - remark.get_height()/2)
        self.win.blit(remark, (X,Y))
        
    def draw_auto_fill(self):
        center_X, center_Y = int(self.WIDTH*3.5), self.Y - int(self.WIDTH*0.5)
        width_X, width_Y, pad   =  int(self.WIDTH*2.5), int(self.WIDTH*0.4), 2
        pygame.draw.rect(self.win, (0,0,0), (center_X-width_X,center_Y-width_Y,width_X*2, width_Y*2))
        pygame.draw.rect(self.win, (200,200,200), (center_X-width_X+pad,center_Y-width_Y+pad,
                                                   width_X*2-pad*2, width_Y*2-pad*2))
        remark = FIX_FONT.render('AUTOFILL', 1, (0,0,0))
        self.win.blit(remark, (center_X-int(remark.get_width()/2), center_Y - int(remark.get_height()/2)))
    
    def display_time(self):
        play_time = round(time.time() - self.start_time)
        Min, Sec = str(int(play_time//60)), str(int(play_time%60))
        Sec = '0' + Sec if len(Sec) == 1 else Sec
        remark = FIX_FONT.render(Min+':'+Sec, 1, (0,0,0))
        self.win.blit(remark, (self.X-remark.get_width()-5, self.Y - remark.get_height()-2))
    
    def draw_red_border(self, x, y):
        draw = [(x*self.WIDTH+p, y*self.WIDTH+p) for p in range(4)]
        for X,Y in draw:
            pygame.draw.line(self.win, (255,0,0), (X, Y), (X+self.WIDTH, Y))
            pygame.draw.line(self.win, (255,0,0), (X, Y), (X, Y+self.WIDTH))
            pygame.draw.line(self.win, (255,0,0), (X+self.WIDTH, Y+self.WIDTH), (X+self.WIDTH, Y))
            pygame.draw.line(self.win, (255,0,0), (X+self.WIDTH, Y+self.WIDTH), (X, Y+self.WIDTH))
        
    def draw_grid(self):
        pygame.draw.rect(self.win, (255,255,255), (0, 0, self.X, self.Y))
        for k in range(10):
            draw = [k*self.WIDTH+2] if k%3 != 0 else [k*self.WIDTH+p for p in range(4)]
            for d in draw:
                pygame.draw.line(self.win, (0,0,0), (d, 0), (d, self.X-2))
                pygame.draw.line(self.win, (0,0,0), (0, d), (self.X, d))    
    
    def update_sudoku_table(self):
        for x in range(9):
            for y in range(9):
                if   self.fixed[(x,y)]    : self.draw_number(x,y, self.fixed[(x,y)])
                elif self.tentative[(x,y)]: self.draw_number(x,y, self.tentative[(x,y)], tentative = True)


sudoku = Sudoku(50)
run = True
while run:
#     sudoku.render()
    run = sudoku.step()
    
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            run = False
    if not run:
        pygame.image.save(sudoku.win,os.path.join('save_imgs','check5.png'))
        pygame.display.quit()
        break
```
