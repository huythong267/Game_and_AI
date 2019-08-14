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
        
        for dx, dy, color in move_pawn:
            this_x, this_y = x+dx,y+dy
            if -1<this_x<8 and -1<this_y<8:
                if dx == 0 and (board[this_x][this_y] == None or  board[this_x][this_y].color != self.color): 
                    moves.append((this_x, this_y))
                elif dx!= 0  and board[this_x][this_y] != None and board[this_x][this_y].color != self.color:
                    moves.append((this_x, this_y))                
        return moves
    
    def draw(self,win):
    
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
