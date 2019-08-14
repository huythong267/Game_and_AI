# Create a Chess enviroment

Credited to initial starting point:
[Tech With Tim - Chess Game](https://github.com/techwithtim/Online-Chess-Game)

Disclaimer: This repository is not for any comercial use

With the image importing lessoned learnt, I would immediately move into the objects and main enviroment of the class.

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
```python

display:

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

First, we need to have a top-level data to manage all the objects in the chess game

```python
class Chess():
    def __init__(self, WIDTH):        
        self.board = [[None for _ in range(8)] for _ in range(8)]
        x1,y1 = 3,3
        x2,y2 = 5,5
        self.board[x1][y1] = Bishop(self,'b',(x1,y1))  #Example
        self.board[x2][y2] = Bishop(self,'b',(x2,y2))  #Example
        
    def render(self):
        self.win.blit(self.board_img, (0,0))
        self.draw_board()
        pygame.display.update()
    
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

# Env: Chess


```python

```