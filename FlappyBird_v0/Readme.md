# Create a FlappyBird enviroment

Same as other games in this repository, Pygame is the toolbox to render background video.
In the snake game, I simply draw rectangular and line to visualize the movement of snake and snack. Pygame receives the keyboard values from human and performs actions on the snake_v0 environment.
I take one step further to utilize images and text display rather than simply Pygame drawing to render environment in this game.

### Import images and Pygame code to display

**os.path.join('name1', 'name2')** to access the images in other folder.
**pygame.image.load(dir)** to load an image - its reversed function is **pygame.image.save(dir)** to save an image.
Several useful transforms are

```python
pipe_img = pygame.image.load(os.path.join('imgs','pipe.png'))
pipe_img = pygame.transform.scale(pipe_img, (int(WIN_WIDTH//8), WIN_HEIGHT))
pipe_img_flip = pygame.transform.flip(pipe_img,False, True)

bird_imgs = [pygame.image.load(os.path.join('imgs','bird{}.png'.format(x))) for x in range(1,4)]
```

*To display a loaded image*
```python
win.blit(self.img, (self.x, self.y))
```

*To rotate a loaded image*
```python
topleft, angle = (self.x, self.y), self.tilt
rotated_image = pygame.transform.rotate(image, angle)
new_rect = rotated_image.get_rect(center = image.get_rect(topleft = topleft).center)

win.blit(rotated_image, new_rect.topleft)
```

* Interesting code to visualize the "flappy" movements*

The code can be shorter by writing some mapping functions, however, I prefer the code written this way for better clarity as I immediately understand what the code does in the first time I see it.

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
