# Create a Chess enviroment

Credited to initial starting point:
[Tech With Tim - Chess Game](https://github.com/techwithtim/Online-Chess-Game)   
[TicTacToe](https://github.com/GunjanChhablani/Tic-Tac-Toe-with-PyGame/blob/master/tictactoe.py)   

Disclaimer: This repository is not for any comercial use

With the image importing lessoned learnt, I would immediately move into the objects and main enviroment of the class.

# Pygame tutorial

To access the mouse click information:
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


# Create chess environment with main board:

```python
board_img = pygame.image.load(os.path.join('img','board.png'))

class Chess():
    def __init__(self, WIDTH):
        self.WIDTH = WIDTH
        self.win = pygame.display.set_mode([self.WIDTH*8, self.WIDTH*8])
        self.board_img = pygame.transform.scale(board_img, (self.WIDTH*8, self.WIDTH*8))
        
    def render(self):
        self.win.blit(self.board_img, (0,0))
        pygame.display.update()
```

Function to display the object in pygame:

```python
chess = Chess(50)
run = True
while run:
    chess.render()
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            run = False
            pygame.display.quit()
            break
```

# Objects: Bishop, King, Knight, Pawn, Queen, Rook

First, we need to have a top-level data **self.board** to manage all the objects in the chess game

```python
class Chess():
    def __init__(self, WIDTH):        
        self.board = [[None for _ in range(8)] for _ in range(8)]
        x1,y1 = 3,3
        x2,y2 = 5,5
        self.board[x1][y1] = Bishop(self,'b',(x1,y1))  #Example
        self.board[x2][y2] = Bishop(self,'b',(x2,y2))  #Example
        
    def render(self):
        self.draw_board()
    
    def draw_board(self):
        for r in range(8):
            for c in range(8):
                if self.board[r][c] != None: self.board[r][c].draw(self.win)
```
## Bishop

All objects inside the chess game no longer has the dynamic "move" function, where we must keep updating its location every new time clock. Instead, it have the "valid_moves" function to indicate where it can go in the next_moves:   

```python
b_bishop = pygame.image.load(os.path.join('img','black_bishop.png'))
w_bishop = pygame.image.load(os.path.join('img','white_bishop.png'))

class Bishop():    
    def __init__(self, win, color, loc):
        self.pad = int(win.WIDTH*0.1)
        self.grid, self.width, self.color = win.WIDTH, win.WIDTH - self.pad*2, color
        if   color == 'b': self.img = pygame.transform.scale(b_bishop, (self.width, self.width))
        elif color == 'w': self.img = pygame.transform.scale(w_bishop, (self.width, self.width))
        
        self.x, self.y = loc
    
    def valid_moves(self, board):
        x, y, moves = self.x, self.y, []
        #Bishop can only move in diagonal direction where no other object block its sight
        move_diagonal = [(1,1),(1,-1), (-1,1),(-1,-1)]
        for dx, dy in move_diagonal:
            this_x, this_y = x+dx,y+dy
            while -1<this_x<8 and -1<this_y<8:
                if board[this_x][this_y] == None: 
                    moves.append((this_x, this_y))
                    this_x, this_y = this_x+ dx, this_y+ dy
                else:
                    if board[this_x][this_y].color != self.color: moves.append((this_x, this_y))
                    break
        return moves
    
    def draw(self,win):
        locx, locy = self.grid*self.x + self.pad, self.grid*self.y + self.pad
        win.blit(self.img, (locx, locy))
```
## Knight

```python
b_knight = pygame.image.load(os.path.join('img','black_knight.png'))
w_knight = pygame.image.load(os.path.join('img','white_knight.png'))

class Knight():    
    def __init__(self, win, color, loc):
        
    
    def valid_moves(self, board):
        x, y, moves = self.x, self.y, []
        move_knight = [(-2,-1),(-2,1),(-1,2),(-1,-2),(1,2),(1,-2),(2,1),(2,-1)]
        for dx, dy in move_knight:
            this_x, this_y = x+dx,y+dy
            if -1<this_x<8 and -1<this_y<8:
                if board[this_x][this_y] == None or board[this_x][this_y].color != self.color: 
                    moves.append((this_x, this_y))
        return moves
    
    def draw(self,win):
        
```

## Object: Rook


```python
b_rook = pygame.image.load(os.path.join('img','black_rook.png'))
w_rook = pygame.image.load(os.path.join('img','white_rook.png'))

class Rook():    
    def __init__(self, win, color, loc):

    
    def valid_moves(self, board):
        x, y, moves = self.x, self.y, []
        #Rock can only move in parallel direction where no other object block its sight
        move_rock = [(0,1),(0,-1), (-1,0),(1,0)]
        for dx, dy in move_rock:
            this_x, this_y = x+dx,y+dy
            while -1<this_x<8 and -1<this_y<8:
                if board[this_x][this_y] == None: 
                    moves.append((this_x, this_y))
                    this_x, this_y = this_x+ dx, this_y+ dy
                else:
                    if board[this_x][this_y].color != self.color: moves.append((this_x, this_y))
                    break
        return moves
    
    def draw(self,win):
```

## Queen

```python
b_queen = pygame.image.load(os.path.join('img','black_queen.png'))
w_queen = pygame.image.load(os.path.join('img','white_queen.png'))

class Queen():    
    def __init__(self, win, color, loc):
    
    def valid_moves(self, board):
        x, y, moves = self.x, self.y, []
        #Queen = rook + bishop
        move_rook = [(0,1),(0,-1), (-1,0),(1,0)]
        move_diagonal = [(1,1),(1,-1), (-1,1),(-1,-1)]
        for dx, dy in move_rook + move_diagonal:
            this_x, this_y = x+dx,y+dy
            while -1<this_x<8 and -1<this_y<8:
                if board[this_x][this_y] == None: 
                    moves.append((this_x, this_y))
                    this_x, this_y = this_x+ dx, this_y+ dy
                else:
                    if board[this_x][this_y].color != self.color: moves.append((this_x, this_y))
                    break
        return moves
    
    def draw(self,win):

```

## King

```python
b_king = pygame.image.load(os.path.join('img','black_king.png'))
w_king = pygame.image.load(os.path.join('img','white_king.png'))

class King():    
    def __init__(self, win, color, loc):

    def valid_moves(self, board):
        x, y, moves = self.x, self.y, []
        #King = rook + bishop in one move nearby
        move_rook = [(0,1),(0,-1), (-1,0),(1,0)]
        move_diagonal = [(1,1),(1,-1), (-1,1),(-1,-1)]
        for dx, dy in move_rook + move_diagonal:
            this_x, this_y = x+dx,y+dy
            if -1<this_x<8 and -1<this_y<8:
                if board[this_x][this_y] == None or board[this_x][this_y].color != self.color: 
                    moves.append((this_x, this_y))
        return moves
    
    def draw(self,win):
```
## Pawn

```python
b_pawn = pygame.image.load(os.path.join('img','black_pawn.png'))
w_pawn = pygame.image.load(os.path.join('img','white_pawn.png'))

class Pawn():    
    def __init__(self, win, color, loc):
    
    def valid_moves(self, board):
        x, y, moves = self.x, self.y, []
        #pawn: depends on color + nearby
        move_pawn_color = [(0,1,'b'), (1,1,'b'),(-1,1,'b'), (0,-1,'w'),(-1,-1,'w'),(1,-1,'w')]
        move_pawn = [(dx,dy) for dx, dy, color in move_pawn_color if color == self.color]

        for dx, dy in move_pawn:
            this_x, this_y = x+dx,y+dy
            if -1<this_x<8 and -1<this_y<8:
                if dx == 0 and (board[this_x][this_y] == None or  board[this_x][this_y].color != self.color): 
                    moves.append((this_x, this_y))
                elif dx!= 0  and board[this_x][this_y] != None and board[this_x][this_y].color != self.color:
                    moves.append((this_x, this_y))
        
        if (y, self.color) in [(1,'b'),(6,'w')]:
            dy = 2 if self.color == 'b' else -2
            if board[x+0][y+dy] == None or  board[x+0][y+dy].color != self.color:
                moves.append((x+0,y+dy))
                
        return moves
    
    def draw(self,win):
    
```

# Piece:

```python
img_map = { ('knight','b'): b_knight, ('knight','w'): w_knight,
            ('king',  'b'): b_king  , ('king',  'w'): w_king  ,
            ('rook',  'b'): b_rook  , ('rook',  'w'): w_rook  ,
            ('pawn',  'b'): b_pawn  , ('pawn',  'w'): w_pawn  ,
            ('queen', 'b'): b_queen , ('queen', 'w'): w_queen ,
            ('bishop','b'): b_bishop, ('bishop','w'): w_bishop,
          }

class Piece():
    def __init__(self, win, name, color, loc):
        self.pad = int(win.WIDTH*0.1)
        self.grid, self.width = win.WIDTH, win.WIDTH - self.pad*2
        self.name, self.color = name, color
        
        img = img_map[(self.name, self.color)]
        self.img = pygame.transform.scale(img, (self.width, self.width))
        
        self.x, self.y = loc
        
    def draw(self,win):
        locx, locy = self.grid*self.x + self.pad, self.grid*self.y + self.pad
        win.blit(self.img, (locx, locy))
        
    def valid_moves(self, board):
        x, y, moves = self.x, self.y, []
        
        if   self.name == 'bishop':
            #Bishop can only move in diagonal direction where no other object block its sight
            move_diagonal = [(1,1),(1,-1), (-1,1),(-1,-1)]
            for dx, dy in move_diagonal:
                this_x, this_y = x+dx,y+dy
                while -1<this_x<8 and -1<this_y<8:
                    if board[this_x][this_y] == None: 
                        moves.append((this_x, this_y))
                        this_x, this_y = this_x+ dx, this_y+ dy
                    else:
                        if board[this_x][this_y].color != self.color: moves.append((this_x, this_y))
                        break
        
        elif self.name == 'rook':
            move_rock = [(0,1),(0,-1), (-1,0),(1,0)]
            for dx, dy in move_rock:
                this_x, this_y = x+dx,y+dy
                while -1<this_x<8 and -1<this_y<8:
                    if board[this_x][this_y] == None: 
                        moves.append((this_x, this_y))
                        this_x, this_y = this_x+ dx, this_y+ dy
                    else:
                        if board[this_x][this_y].color != self.color: moves.append((this_x, this_y))
                        break
        
        elif self.name == 'queen':
            #Queen = rook + bishop
            move_rook = [(0,1),(0,-1), (-1,0),(1,0)]
            move_diagonal = [(1,1),(1,-1), (-1,1),(-1,-1)]
            for dx, dy in move_rook + move_diagonal:
                this_x, this_y = x+dx,y+dy
                while -1<this_x<8 and -1<this_y<8:
                    if board[this_x][this_y] == None: 
                        moves.append((this_x, this_y))
                        this_x, this_y = this_x+ dx, this_y+ dy
                    else:
                        if board[this_x][this_y].color != self.color: moves.append((this_x, this_y))
                        break
                        
        elif self.name == 'king':
            #King = rook + bishop in one move nearby
            move_rook = [(0,1),(0,-1), (-1,0),(1,0)]
            move_diagonal = [(1,1),(1,-1), (-1,1),(-1,-1)]
            for dx, dy in move_rook + move_diagonal:
                this_x, this_y = x+dx,y+dy
                if -1<this_x<8 and -1<this_y<8:
                    if board[this_x][this_y] == None or board[this_x][this_y].color != self.color: 
                        moves.append((this_x, this_y))
        
        elif self.name == 'knight':
            move_knight = [(-2,-1),(-2,1),(-1,2),(-1,-2),(1,2),(1,-2),(2,1),(2,-1)]
            for dx, dy in move_knight:
                this_x, this_y = x+dx,y+dy
                if -1<this_x<8 and -1<this_y<8:
                    if board[this_x][this_y] == None or board[this_x][this_y].color != self.color: 
                        moves.append((this_x, this_y))
            
            
        elif self.name == 'pawn':
            #pawn: depends on color + nearby
            move_pawn_color = [(0,1,'b'), (1,1,'b'),(-1,1,'b'), (0,-1,'w'),(-1,-1,'w'),(1,-1,'w')]
            move_pawn = [(dx,dy) for dx, dy, color in move_pawn_color if color == self.color]

            for dx, dy in move_pawn:
                this_x, this_y = x+dx,y+dy
                if -1<this_x<8 and -1<this_y<8:
                    if dx == 0 and board[this_x][this_y] == None: 
                        moves.append((this_x, this_y))
                    elif dx!= 0  and board[this_x][this_y] != None and board[this_x][this_y].color != self.color:
                        moves.append((this_x, this_y))

            if (y, self.color) in [(1,'b'),(6,'w')]:
                dy = 2 if self.color == 'b' else -2
                if board[x+0][y+dy] == None or  board[x+0][y+dy].color != self.color:
                    moves.append((x+0,y+dy))

        return moves

```

# Chess: Render(), Reset(), Step()

First, we need to render the full chess table first. Generating Objects on the board tables:

```python
rooks   = [(0,0,'b'),(7,0,'b'),(0,7,'w'),(7,7,'w')]
knights = [(1,0,'b'),(6,0,'b'),(1,7,'w'),(6,7,'w')]
bishops = [(2,0,'b'),(5,0,'b'),(2,7,'w'),(5,7,'w')]
queens  = [(3,0,'b'),(3,7,'w')]
kings   = [(4,0,'b'),(4,7,'w')]
pawns   = [(x,y,color) for x in range(8) for y,color in [(1,'b'),(6,'w')]]

pieces =  [rooks, knights, bishops, queens, kings, pawns]
names  =  ['rook','knight','bishop','queen','king','pawn']
chess_init = [(x, y, color,name) for piece, name in zip(pieces, names) for x, y, color in piece]

class Chess():
    def __init__(self, WIDTH):
        self.WIDTH, self.RADIOUS  = WIDTH, int(WIDTH//8)
        self.win = pygame.display.set_mode([self.WIDTH*8, self.WIDTH*8])
        self.board_img = pygame.transform.scale(board_img, (self.WIDTH*8, self.WIDTH*8))
        
        self.turn = 'w'
        self.board = [[None for _ in range(8)] for _ in range(8)]
        for x,y,color, name in chess_init:
            self.board[x][y] = Piece(self,name,color,(x,y))
    
    def reset(self):
        self.board = [[None for _ in range(8)] for _ in range(8)]
        for x,y,color, name in chess_init:
            self.board[x][y] = Piece(self,name,color,(x,y))
```

Next, we need to implement the basic movement of the chess game
(1) collect the input from the user on how to move the chess game with start, end       
(2) update the board & change the turn ('b' or 'w')     

```python
    def step(self, action = None):
        start, Next = self.collect_agent_inputs() if not action else action
        if start and Next:
            gameover = self.isGameOver(Next)
            self.update_move(start, Next)

            if not gameover:
                self.potential_pawn_change(Next)
                if self.isChecked(Next):
                    self.display_remark('Check')
                    self.get_user_input()
                    
                self.turn = 'b' if self.turn == 'w' else 'w'
            else:
                self.display_remark('Game Over')
                self.get_user_input()
            return gameover
        else: return True
```

Several auxiliary functions
(1) step() helper functions

```python
    ############## Some chess.step() helper functions #################################################
    ############## step() function is quite complicated for chess compared to snake or flappy bird ####
    
    def collect_agent_inputs(self):
        start, possible_moves, Next = None, None, None
        valid_objects = {(x,y) for x in range(8) for y in range(8) 
                         if self.board[x][y] != None and self.board[x][y].color == self.turn}
        while True:
            for event in pygame.event.get():
                if event.type == pygame.QUIT:             return None, None
                if event.type == pygame.MOUSEBUTTONDOWN:  Next = (event.pos[0]//self.WIDTH, event.pos[1]//self.WIDTH)
            if start and Next in possible_moves:
                return start, Next
            if Next in valid_objects:
                start = Next
                possible_moves = self.board[start[0]][start[1]].valid_moves(self.board)
                self.draw_circle(start, possible_moves)
    
    def isGameOver(self, Next):
        x, y = Next
        return self.board[x][y] and self.board[x][y].name == 'king' and self.board[x][y].color != self.turn
    
    def update_move(self, start, Next):
        x1, y1 = start
        x2, y2 = Next
        self.board[x1][y1].x, self.board[x1][y1].y = x2, y2
        self.board[x2][y2], self.board[x1][y1]  = self.board[x1][y1], None
        
    def isChecked(self, Next):
        x, y = Next
        check_locations = self.board[x][y].valid_moves(self.board)
        for x, y in check_locations:
            if self.board[x][y] and self.board[x][y].name == 'king' and self.board[x][y].color != self.turn:
                return True
        return False
    
    def potential_pawn_change(self, Next):
        x, y, color = Next[0], Next[1], self.turn
        if (y, color) in [(7,'b'),(0,'w')] and self.board[x][y] and self.board[x][y].name == 'pawn':
            self.render()
            self.display_remark('Q: Queen, K: Knight, B: Bishop, R: Rock')
            
            self.board[x][y] = None
            while True:
                for event in pygame.event.get():
                    if event.type==pygame.KEYDOWN :
                        if   event.key==pygame.K_q: self.board[x][y] = Piece(self,'queen',color,(x,y))
                        elif event.key==pygame.K_k: self.board[x][y] = Piece(self,'knight',color,(x,y))
                        elif event.key==pygame.K_b: self.board[x][y] = Piece(self,'bishop',color,(x,y))
                        elif event.key==pygame.K_r: self.board[x][y] = Piece(self,'rook',color,(x,y))
                if self.board[x][y] != None: 
                    self.render()
                    return
```

(2) Display helper function

```python
    def draw_board(self):
        for r in range(8):
            for c in range(8):
                if self.board[r][c] != None: self.board[r][c].draw(self.win)
    
    def display_remark(self, string):
        self.render()
        print(string)
        remark = STAT_FONT.render(string, 100, (0,255,0))
        self.win.blit(remark, (self.WIDTH, self.WIDTH*5))
        pygame.display.update()
    
    def draw_circle(self, start, possible_moves):
        x, y = start
        circle = (int((x+0.5)*self.WIDTH),int((y+0.5)*self.WIDTH))
        pygame.draw.circle(self.win, (150,0,0), circle, self.RADIOUS)
        for x1, y1 in possible_moves:
            circle = (int((x1+0.5)*self.WIDTH),int((y1+0.5)*self.WIDTH))
            pygame.draw.circle(self.win, (0,255,0), circle, self.RADIOUS)
        pygame.display.update()
    
    def get_user_input(self):
        move_on = False
        while not move_on:
            for event in pygame.event.get():
                if event.type in [pygame.KEYDOWN, pygame.MOUSEBUTTONDOWN]:
                    move_on = True
```

(3) Press Space to Continue

```python
    def press_space_to_continue(self):
        self.display_remark('Press Space to Continue')
        while True:
            for event in pygame.event.get():
                if event.type == pygame.KEYDOWN:
                    if event.key== pygame.K_SPACE:
                        self.reset()
                        return False
                    else:
                        self.display_remark('Stopping ... Thank you for playing')
                        self.get_user_input()
                        return True

```

# Play Chess

```python
def play_chess():
    chess = Chess(100)
    clock = pygame.time.Clock()
    gameover = False
    while not gameover:
        clock.tick(1)
        chess.render()
        gameover = chess.step()
        if gameover:
            gameover = chess.press_space_to_continue()
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                gameover = True
                pygame.display.quit()
                break    
```

```python

```

```python

```

```python

```

```python

```

```python

```

```python

```
