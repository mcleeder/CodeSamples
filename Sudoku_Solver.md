# Sudoku Solver


## Introduction

This was a side project of mine while I was learning C# at boot camp. At boot camp I always tried to have one project going on the side where I could try to apply what I was learning, or often just get ahead of the game. Boot camp moves so fast that I rarely had the chance to complete any of them, this is one of the rare ones that ended up as a finished product.

Back End:

-[The solver](https://github.com/mcleeder/CodeSamples/blob/main/README.md#the-solver)

-[The checker](https://github.com/mcleeder/CodeSamples/blob/main/README.md#admin-overlay)



### The solver

This method is the main event. I learned most of this by watching a youtube video (in Python though). It came with a couple of challenges. The first was wrapping my head around recursion and what the heck is happening. Once I started to understand recursion, I realized that the method doesn't want to stop and wants to find every last solution. Not only that, but it cycles extra times as is spins down so if you just wait until the end, the board doesn't have a solution on it. So I had to figure out where to to pull out my solution.

My solution was to use a couple of fields. One to to indicate if we had a solution, and one to hold the solution itself. The latter was necessry because the recurision will keep messing with the board as it spins down.


```c#
        private void Solver(int[,] board)
        {
            if (solved)
            {
                return; //stop early pls
            }
            for (int y = 0; y < 9; y++)
            {
                for (int x = 0; x < 9; x++)
                {
                    if (board[y,x] == 0)
                    {
                        for (int n = 1; n < 10; n++)
                        {
                            if (CanItBe(y, x, n, board))
                            {
                                board[y,x] = n;
                                Solver(board);
                                board[y,x] = 0;
                            }
                        }
                        return;
                    }
                }
            }
            if (SolutionCheck(board) && !solved) //only one output
            {
                solved = true;
                solutionboard = (int[,]) board.Clone();
            }
        }
```


### The checker

