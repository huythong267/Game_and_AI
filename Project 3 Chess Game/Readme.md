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
        
        if event.type==pg.KEYDOWN :
            if event.key==pg.K_r:
            if event.key==pg.K_q:
            if event.type==pg.KEYDOWN:

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
# Chess: Render(), Reset(), Step()

First, we need to render the full chess table first. Generating Objects on the board tables:

```python
class Chess():
    def __init__(self, WIDTH):
        self.board = [[None for _ in range(8)] for _ in range(8)]
        
        rooks = [(0,0,'b'),(7,0,'b'),(0,7,'w'),(7,7,'w')]
        for x,y,color in rooks:
            self.board[x][y] = Rook(self,color,(x,y))
        
        knights = [(1,0,'b'),(6,0,'b'),(1,7,'w'),(6,7,'w')]
        for x,y,color in knights:
            self.board[x][y] = Knight(self,color,(x,y))
        
        bishops = [(2,0,'b'),(5,0,'b'),(2,7,'w'),(5,7,'w')]
        for x,y,color in bishops:
            self.board[x][y] = Bishop(self,color,(x,y))
        
        queens = [(4,0,'b'),(3,7,'w')]
        for x,y,color in queens:
            self.board[x][y] = Queen(self,color,(x,y))
            
        kings = [(3,0,'b'),(4,7,'w')]
        for x,y,color in kings:
            self.board[x][y] = King(self,color,(x,y))
        
        pawns = [(x,y,color) for x in range(8) for y,color in [(1,'b'),(6,'w')]]
        for x,y,color in pawns:
            self.board[x][y] = Pawn(self,color,(x,y))
```

Next, we need to implement the basic movement of the chess game
(1) collect the input from the user on how to move the chess game with start, end
(2) update the board & change the turn ('b' or 'w')

```python
class Chess():
    def __init__(self, WIDTH):
        
    def render(self):

    def step(self):
        start, end = self.collect_agent_inputs()
        if start and end:
            self.update_move(start, end)
            self.turn = 'b' if self.turn == 'w' else 'w'
    
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
    
    def draw_circle(self, start, possible_moves):
        x, y = start
        circle = (int((x+0.5)*self.WIDTH),int((y+0.5)*self.WIDTH))
        pygame.draw.circle(self.win, (150,0,0), circle, self.RADIOUS)
        for x1, y1 in possible_moves:
            circle = (int((x1+0.5)*self.WIDTH),int((y1+0.5)*self.WIDTH))
            pygame.draw.circle(self.win, (0,255,0), circle, self.RADIOUS)
        pygame.display.update()
        self.render()
                
    def update_move(self, start, end):
        x1, y1 = start
        x2, y2 = end
        self.board[x1][y1].x, self.board[x1][y1].y = x2, y2
        self.board[x2][y2], self.board[x1][y1]  = self.board[x1][y1], None
```

Several auxiliary functions
(1) change the pawn to queen/rock/knight/bishop if it goes to the end of the line

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

```python

```

```python

```

```python

```

```python

```
