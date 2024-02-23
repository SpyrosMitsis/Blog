---
title: "Conway's Game Of Life In Python"
date: 2024-02-06T00:11:52+02:00
draft: false
toc: true
images:
tags:
  - Python
  - Conway's Game of Life
---


Conway's Game of Life is a cellular automaton devised by mathematician John Conway in 1970.
Despite its simplicity, the Game of Life exhibits complex and unpredictable behavior. 

Conway's Game of Life is a zero-player game that takes place on a two-dimensional grid of cells.
Each cell can be either alive or dead. 
The game evolves over discrete time steps based on a set of rules. 
The rules determine the state of each cell in the next generation based on its current state and the state of its eight neighbors.


1. Any live cell with fewer than two live neighbors dies, as if by underpopulation.
2. Any live cell with two or three live neighbors survives to the next generation.
3. Any live cell with more than three live neighbors dies, as if by overpopulation.
4. Any dead cell with exactly three live neighbors becomes a live cell, as if by reproduction.


## Python Implementation

The following is a Python implementation of Conway's Game of Life.

```python

class Game_of_life:

    def __init__(self, board_scale):
        self.board_scale = board_scale
        self.board = [[" " for _ in range(self.board_scale)] for _ in range(self.board_scale)]
        self.updated_board = [[" " for _ in range(board_scale)] for _ in range(board_scale)]
```

The `__init__` method initializes the board and the updated board.
The board is a two-dimensional array of `board_scale` size by using the `board_scale` parameter.
Both the board and the updated board are initialized with empty strings, more specifically:
```python
self.board = [[" " for _ in range(self.board_scale)] for _ in range(self.board_scale)]

```
`[" " for _ in range(self.board_scale)]:`
This is a list comprehension that creates a list with empty spaces (" ") repeated self.board_scale times.
The variable _ is a throwaway variable because we are not using it in the comprehension.

`for _ in range(self.board_scale): `This outer list comprehension repeats the inner list comprehension self.board_scale times,
 creating a two-dimensional list with self.board_scale rows and self.board_scale columns. 
 Each element of the outer list is a list representing a row in the two-dimensional grid.

 ```python
     def random_board(self, n_of_cells):
        for i in range (n_of_cells):
            row = random.randint(0, self.board_scale - 1)
            col = random.randint(0, self.board_scale - 1)
            self.board[row][col] = "X"

        return self
 ```

 This function randomly places `n_of_cells` number of cells on the board.

 ```python
     def neighbor_counter(self, row, col):

            count = 0 

            for i in range(-1, 2):
                for j in range(-1, 2):
                    if 0 <= row + i < self.board_scale and 0 <= col + j < self.board_scale:
                        if not (i == 0 and j == 0) and self.board[row + i][col + j] == 'X':
                            count += 1
            return count
```

This function is the core of the game. It counts the number of neighbors of a cell.
A neighbor is a cell that lives next to the cell in question.

{{< image src="/neighbor.png" alt="neighbor_example" position="center" style="border-radius: 8px;" >}}

To count them we perform two loops.
The outer loop iterates over the rows of the board.
The inner loop iterates over the columns of the board.
because we want to count the neighbors of the cell in question, we range between -1 and 2 and the relative distance from the cell in question.
Also, we must skip the cell itself.

To better understand the relative values I made this example:
{{< image src="/relative_distance.jpg" alt="relative_distance" position=" center" style="border-radius: 8px;" >}}
By adding `i` and `j` we can move the cursor to the relative position of the cell in question.


All that's left now is to update the board.

```python
    def next_board(self, row, col):
        cell = self.board[row][col]
        counter = self.neighbor_counter(row, col)

        if cell == " " and counter == 3:
            self.updated_board[row][col] = "X"
        elif cell == "X" and (counter < 2 or counter > 3):
            self.updated_board[row][col] = " "
        else:
            self.updated_board[row][col] = cell 
```
This method is just a row of if statements that update the board based on the rules of the game.

Now, to actually "play" the game and make:


```python
if __name__ == "__main__":
    game = Game_of_life(50)
    game.random_board(300)

    for _ in range(100):

        os.system('cls')
        print(game)
        for i in range(game.board_scale):
            for j in range(game.board_scale):
                game.next_board(i, j)

        game.board = [row[:] for row in game.updated_board]
        time.sleep(0.5) 
        print(game)
```

To make the game a bit more visually appealing I implemented `os.system('cls')`, which clears the screen. (*Note*: only in Windows if you are using
a Unix-based system change the `os.system('cls')` to `os.system(clear)`)
Then we print the board using a list comprehension, wait for 0.5 seconds and then print the board again.

