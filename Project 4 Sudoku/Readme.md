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

Now I want to adding the hover for the mouse click and get the input data
