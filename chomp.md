## Background

Chomp is a very simple game played with a chocolate
bar. This
[Wikipedia article](https://en.wikipedia.org/wiki/Chomp)
describes the rules of the game.

Chomp is a good game to build a computer player for. Chomp
is a two-player perfect-information terminating game, so a
straightforward implementation of the Negamax algorithm can
play perfectly on small boards.

The basic idea is that in a given position a best move can
be found by exploring all possible moves, all possible
responses, etc. The resulting move selected is one that
gives the opponent the worst possible end-of-game result.

In this assignment, you will write a library crate that
implements a Chomp AI (`src/lib.rs`), and a binary crate
that allows the game to be played on the command line
(`src/main.rs`).

## The library

The game state should be represented by a `struct Board` you
define. The most important component of the state is the
representation of the board itself:

* One way: use an array

  * Use a fixed-size five-by-four (five columns, four rows)
    array of `bool`s to represent the game state. The element
    at index `i, j` should be `false` if the square at `i, j`
    has been eaten, and `true` otherwise.

  * Your program should support multiple board sizes. Since
    the size of a Rust array is fixed at compile time, your
    board type needs to have fields representing its logical
    width and height.

* Another way: use a set

  * You can make a `HashSet` of tuples, where tuple `(i, j)`
    being in the set means that position is not yet
    eaten. You can remove elements from the set as they are
    eaten.

Your `Board` type should support the following operations
via `impl`:

- Create a board with a given width and height.

- Print a graphical representation of a board.

- Chomp a given square, removing all squares below it and to
  the right of it.

- Return a winning move for the board, if any (see the next section).

- Clone a board (the `Clone` trait).

### The AI

The negamax algorithm solves any zero-sum
perfect-information two-player game (like Chomp). It takes
as input a board state and outputs a winning move, if one
exists.

<!-- This pseudocode translated from winningmove.pseu by pseuf -->

> *winning-move*(*posn*):  
> &nbsp;&nbsp;&nbsp;&nbsp;**for**&nbsp;each&nbsp;remaining&nbsp;row&nbsp;*r*  
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**for**&nbsp;each&nbsp;remaining&nbsp;column&nbsp;*c*&nbsp;**in**&nbsp;*r*  
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**if**&nbsp;*r*&nbsp;=&nbsp;0&nbsp;and&nbsp;*c*&nbsp;=&nbsp;0  
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**continue**  
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*p*&nbsp;&#8592;&nbsp;copy&nbsp;of&nbsp;*posn*  
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*chomp*&nbsp;*r*,&nbsp;*c*&nbsp;from&nbsp;*p*  
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*m*&nbsp;&#8592;&nbsp;*winning-move*(*p*)  
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**if**&nbsp;no&nbsp;winning&nbsp;move&nbsp;is&nbsp;returned  
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**return**&nbsp;the&nbsp;move&nbsp;*r*,&nbsp;*c*  
> &nbsp;&nbsp;&nbsp;**return**&nbsp;no&nbsp;winning&nbsp;move  

<!-- End of pseuf translation of winningmove.pseu -->


Understand this pseudocode as follows:

1. Check whether the board state is already lost. If so,
   then there is no winning move.

2. Otherwise, for each possible move `m`:

  1. Create a new board `p`.

  2. Perform the move `m` on `p`.

  3. Call `winning_move` recursively at `p`. (Since one player
     has just made a move, we are now trying to find a
     winning move for the *other* player.)

  4. If `winning_move` outputs a winning move for `p`, then `m`
     is *not* a winning move for the current player. (Why?)
     Continue on to the next move.

  5. Otherwise, `m` is a winning move. Return it.

For Chomp:

- The board state is lost if the upper-left square is the
  only one left.

- You can represent the move "chomp at position `i, j`" by a
  tuple `(i, j)`. There is one such possible move for each
  uneaten square (other than the top left one).

## The program

Your program will be a simple terminal interface for playing
Chomp against your AI. It should perform the following
sequence:

1. Ask the user for a board size.

2. Repeatedly:

  1. Print the board.

  2. Ask the user to input a move and perform it.

  3. Try to find a winning move. If there is one, perform
     it. Otherwise, stall by chomping as little as
     possible. (You can implement this by chomping the
     furthest-right piece in the lowermost nonempty row.)

If the user ever gives invalid input, simply ask again until
they give valid input.


## Hints

- The `prompted` crate from `crates.io` may be useful to get
  a line of user input.

- Several of your library functions should fail under
  certain conditions. (For example, creating a board with a
  width or height larger than the maximum should fail.) Use
  `assert!` to deal with such situations, or return a
  `Result` that can be checked.

- Your `winning_move` function should return some kind of
  `Option`, since there may not be a winning move.

## Suggestions

- Edit `Cargo.toml` to provide your authorship
  information.

- Document both your crates with Rustdoc comments for each
  datatype, function and method.

- Your crates should build with current `stable` Rust.

- Your crates might contain adequate tests (implemented
  using `#[test]` unit-testing) and assertions (implemented
  using `assert!()` and related macros) for you to be
  comfortable that everything is working correctly.

- Your code should be formatted according to the official
  Rust formatting style --- use `cargo fmt` to reformat your
  code in-place.

- Your code should produce no compiler warnings, and `cargo
  clippy` should also produce no warnings. Please do not
  disable warnings except in the most unusual circumstances:
  fix them instead.
