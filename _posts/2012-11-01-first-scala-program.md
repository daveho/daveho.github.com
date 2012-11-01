---
title: First Scala Program
layout: post
tags: [scala, sevenlanguagesinsevenweeks, java, programming]
---

In [the programming languages course I'm teaching](http://faculty.ycp.edu/~dhovemey/fall2012/cs340/index.html),
I'm using [Seven Languages in Seven Weeks](http://pragprog.com/book/btlang/seven-languages-in-seven-weeks)
as the textbook.  So far, I've been very impressed.  It delivers on its goal of going just
far enough into a new language that you feel like you can do interesting things
with it.  As a longtime Java programmer, it was a much-needed kick in the pants to
start learning some new languages.

I've finally reached the chapter on [Scala](http://www.scala-lang.org), which
is the language I'm most excited about learning.

First impressions of Scala - it's a lot like Ruby.  It supports the "let's apply
an anonymous code block to this collection of values" style of programming,
which is refreshing coming from Java, where doing anything seems to require
explicit loops.  It's statically-typed, but with type inference, so code is much
less cluttered with noise about variable types.  (You do tend to get cryptic
error messages when type inference fails, but such is life.)

The book suggests Tic Tac Toe as a good first program, so I gave it a whirl.
Here's my first-ever Scala program:

{% highlight scala %}
class Board(rows : List[List[Char]]) {
	def isWinner(piece : Char) : Boolean = {
	  rows.count( r => win(r, piece) ) > 0 ||
	    (0 until 3).count( i => win(column(i), piece) ) > 0 ||
	    win(diag(0, 0, 1, 1), piece) ||
	    win(diag(2, 0, -1, 1), piece)
	}
	
	def column(index : Int) : List[Char] = {
	  rows.map( r => r(index) )
	}
	
	def diag(x : Int, y : Int, dx : Int, dy : Int) : List[Char] = {
	  (0 until 3).map( i => rows(y + i*dy)(x + i*dx) ).toList
	}
	
	def win(seq : List[Char], piece : Char) : Boolean = {
	  seq.count( c => c == piece ) == 3
	}
	
	def subst(row : List[Char], rowIndex : Int, x : Int, y : Int, piece : Char) : List[Char] = {
	  (0 until 3).map( i => 
	    if (i == x && rowIndex == y)
	      piece
	    else
	      row(i)
	  ).toList
	}
	
	def move(x : Int, y : Int, piece : Char) : Board = {
	  val updatedRows = (0 until 3).map( i => subst(rows(i), i, x, y, piece) ).toList
	  return new Board(updatedRows)
	}
	
	def row(index : Int) = rows(index)
}

object PlayTicTacToe {
	def main(args: Array[String]) {
	  var board = new Board(List(
	    List(' ', ' ', ' '),
	    List(' ', ' ', ' '),
	    List(' ', ' ', ' ')))

	  var count = 0
	  var turn = 'X'
	  while (count < 9) {
	    printBoard(board)
	    
	    println(turn + "'s turn, enter row and column")
	    val row = readLine.toInt
	    val col = readLine.toInt
	    
	    board = board.move(col, row, turn)
	    
	    if (board.isWinner(turn)) {
	      println(turn + " wins!")
	      return
	    }
	    
	    count = count + 1
	    turn = if (turn == 'X') 'O' else 'X' 
	  }
	  
	  println("Draw!")
	}
	
	def printBoard(board : Board) = {
	  (0 until 3).foreach( i => println(board.row(i).mkString) )
	}
}

PlayTicTacToe.main(args)
{% endhighlight %}

As you can see, I have some mutable variables in the **PlayTicTacToe**
singleton's **main** method.  The actual gameplay, however, is done by computing new game
boards nondestructively.

Overall, my first experiences with Scala have been positive, and I'm looking
forward to using it more in the future.
