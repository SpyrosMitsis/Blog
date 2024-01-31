---
title: "Using the minimax algorithm for Tic Tac Toe "
date: 2024-01-31T13:56:01+02:00
draft: false
toc: false
images: 
tags:
  - Machine learning
  - Minimax
  - Python
  - algorithm
---


  This week in my uni I was tasked to create a simple tic-tac-toe application in Python so that we can familiarize 
  ourselves with the notion of two-dimensional arrays and OOP in python.

  Creating the board itself was very simple alongside the method for displaying the array was easy.


  ## Making the tic-tac-toe game  in Python

  ```python
  class TicTacToe:
    def __init__(self) -> None:
        self.board = [
            ['-', '-', '-'],
            ['-', '-', '-'],
            ['-', '-', '-']
        ]

    def display_board(self):
        for i in range(3):
            for j in range(3):
                print(self.board[i][j], end=' ')
            print()

  ```


The next step of this application was creating a method for letting the human play.

  ```python
      def human_play(self, player):

        while True:

            row = int(input("Row: "))
            col = int(input("Col: "))

            if row < 0 or row > 2 or col < 0 or col > 2:
                print("Wrong row or col")
            elif self.board[row][col] == '-':
                self.board[row][col] = player
                return row, col
            else:
                print("Error, position is occupied")
  ```
This simple function simply prompts the users for the x and y coordinates inside the 3X3 Matrix that is the tic-tac-toe board.
I understand that playing while keeping in mind that arrays are indexed 0 is not the most gamer-friendly approach but the focus of this
exercise was implementing the minimax algorithm.


Before we can start implementing the algorithm we must first a couple of pre-requisites:
  - make a method that gets the winner
  - make a method that sees if there is a tie

Let's start with making a function that finds a winner.

```python
    def is_winner(self, player):
        for i in range(3):
            if all(self.board[i][j] == player for j in range(3)) or all(self.board[j][i] == player for j in range(3)):
                return True

        if all(self.board[i][i] == player for i in range(3)) or all(self.board[i][2 - i] == player for i in range(3)):
            return True
        return False
```

This method has two `if` statements.
The first one is responsible for finding horizontal and vertical wins.

Let's break this if statement in two so that we can have a better understanding of what's going on.

```python
      for i in range(3):
            if all(self.board[i][j] == player for j in range(3)):
                return True
```
This first part checks if all the elements inside the specific row `i` are equal to the specific player's symbol.
`for j in range(3)` loops inside each column inside the `i` row.
`self.board[i][j] == player` checks if the element is comparatively equal to the player's symbol and if it's true it returns True.
The `all()`  function takes an iterable (in this case, a generator expression) and if all the values return `True` then it also returns true.

The same is later done in the second part of this `if` statement but the `self.board[i][j]` become `self.board[j][i]` so that it scans vertically
instead of horizontally.


The second if statement functions essentially the same as the first one but this time it loops only a single time and `i` is put
inside both idices `self.board[i][i] == player for i in range(3)` to get the first diagonal.
The second diagonal a negative number is used to wrap the index at the beginning of the list.


The last piece of the puzzle before getting into the algorithm is finding when a tie occurs.
To do that we can simply check if the board is full, and if it is and no winner is found that means we have a draw in our hands.

``` python
    def is_board_full(self):
        for i in range(3):
            for j in range(3):
                if self.board[i][j] == '-':
                    return False
        return True
```
The implementation is pretty simple we just check if a space exists `"-"` if there is, then the loop breaks returning false
meaning that the board is not full yet.



## The Algorithm

Before showing the implementation it is important to understand how the minimax algorithm works.

The minimax algorithm is a backtracking algorithm used in game theory for finding the optimal move in a turn-based game assuming optimal play.
The algorithm has two players the maximizer and the minimizer.
The maximizer tries to get the highest score possible while the minimizer tries the opposite.

Every board has a state associated with a value, the maximizer tries to move the game toward a game state with the highest score possible.

The game state is represented as a decision tree where each corresponds to a possible game position.
The root of the tree represents the current game state, and each child node (branch) represents a possible move.

To have a better idea of this tree I drew up a tic-tac-toe example.
The reason why the root of this example is mid-game is because tic-tac-toe has 9 tiles making the possible combinations
of the tree 9! which is equal to 362880.
Way too many combinations for me to draw lol. 


![my image](/decision_tree.jpg)

To algorithm recursively expands the game tree, considering all moves for both players, up to a certain 
depth (because tic-tac-toe is a simple game the computer always reaches the terminal state (win, lose, draw)).

At the leaf node of the terminal state (the end of the tree), an evaluation function is applied to determine
if the outcome is desirable for the current user.
Since tic-tac-toe is simple our function can simply be:

- win = + 1
- lose = - 1
- draw = 0

The evaluation score then gets propagated back up the tree to the root, alternating between maximizing and minimizing players.
The maximizing player will try to maximize the score by choosing a decision that maximizes the evaluation score whilst the
minimizing player will try to minimize the score.

Once the tree is evaluated, the algorithm selects the move that leads to the highest score if it's the maximizing player's 
turn or the move that leads to the lowest score if it's the minimizer player's turn.



Let's look at our example image.

After the tree's root, it's the maximizing player's turn to play.
In this case, it can make 3 moves.
The first one grants it an immediate victory, setting the evaluation function to +1, while the second and third ones are not terminal states (win, lose, draw).

Now it's the minimizer's turn.

Since one outcome was terminal, the minimizing player can only go after the other 2 states.
Each of these states has two child nodes.
In both cases, the left node led to the minimizing player's victory so the evaluation function is set to -1.

Now let's look in depth at how the algorithm chooses the best move.

![backpropagation](/backpropagation.jpg)

Because this algorithm assumes optimal play, this means both players always make the best decision given their current position,
this means that we can deduce the evaluation score before reaching a terminal state.

![backpropagation_first_step](/backpropagation_first_step.jpg)

More specifically we know that if it's the minimizer's turn, then it can make two plays.
One will lead it to victory and the other one will not be a terminal state.
But because after its move it's the maximizing player's turn and they only have one option leading them to victory we can assume 
that choosing the left option in both cases will lead to the minimizer player's loss.

This essentially means that we can take the `+1` state that we get at the end and bring it back to the nonterminal state.

Now let's look at the next layer up so we see how the maximizing player behaves.

![backpropagation_second_step](/backpropagation_second_step.jpg)

This time it's the maximizing player's turn, and it has 3 choices.
It can either win immediately (`+1`) or have 2 nonterminal states.

Because the algorithm always assumes optimal play we know, given the change between `-1` and `+1` the minimizing player will always 
choose the play that gets them the lowest score.
In essence, this means that out of the three choices one will grant the maximizing player the `+1` and the other nonterminal states will become 
`-1` because after its turn the minimizing player has the opportunity to get the `-1` and minimize the score.

This means that the maximizing player will choose the first option that has an evaluation function = `+1`.


## Implementing it in code

It's finally time to implement this theory into code.

First things first, let's make an evaluation function.

```python
    def evaluate(self):


        if self.is_winner("O"):
            return 1
        elif self.is_winner("X"):
            return -1
        elif self.is_board_full():
            return 0
        else:
            return None
```

As already mentioned tic-tac-toe is a very easy game to make an evaluation function.


Now let's actually implement the minimax algorithm.

```python
    def minimax(self, depth, max_player):

        score = self.evaluate()

        if score is not None:
            return score
        
        if max_player:
            max_value = -2
            for i in range(3):
                for j in range(3):
                    if self.board[i][j] == '-':
                        self.board[i][j] = 'O'
                        eval = self.minimax(depth + 1, False)
                        self.board[i][j] = '-'
                        max_value = max(max_value, eval)
            return max_value

        else:
            min_value = 2
            for i in range(3):
                for j in range(3):
                    if self.board[i][j] == '-':
                        self.board[i][j] = 'X'
                        eval = self.minimax(depth + 1, True)
                        self.board[i][j] = "-"
                        min_value = min(min_value, eval)
            return min_value

```

The first thing this method does is evaluate the score.

By looking at the evaluation method we can see that unless we reach a terminal state the
method will return `None` , in the minimax function we check if we have reached a terminal state and if so
we return the score.

The algorithm revolves around two players, and so we have a big `if` statement.

If max_player is true, we are the maximizing player and so we must set the value at something as low as possible.
In this case, I went `-2` which is lower than the lowest possible value.

Then we perform two loops to loop inside the entire two-dimensional array.
If we find a free spot we place our `"O"` in that spot.
at this time we iterate by calling the `minimax()` again, increasing the depth value and changing the `max_player` to False.
then we turn the board back to its original state and call the `max()` function.
This keeps track of the maximum value encountered among the possible moves.
What we did here is called the method again but this time as the minimizing player because the maximizing player just played
their turn.

Now that the `max_player` variable is false, we fall into the else clause.
Similar to the maximizing player's block, it iterates through each 
empty cell on the board, places an 'X', recursively calls the minimax function for the next 
level of the game tree with max_player set to True, and then reverts the move, keeping track of
the minimum value encountered among the possible moves.

Finally, this function returns the maximum or minimum value, depending on whether it's the turn of the maximizing or minimizing player, respectively.



























