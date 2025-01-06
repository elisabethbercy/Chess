# Installation and usage instructions
You can get the code in Pharo 12 by installing the following baseline code:

```
Metacello new
	repository: 'github://elisabethbercy/Chess:main';
	baseline: 'MygChess';
	onConflictUseLoaded;
	load.
```

You can open the Chess Game with the following expression:
```
	board := MyChessGame freshGame.
	board size: 800@600.
	space := BlSpace new.
	space root addChild: board.
	space pulse.
	space resizable: true.
	space show.
```

# Our Katas
We chose the following Katas:

## Kata 1
Fix pawn moves!
Goal: Practice debugging and testing
Pawns are one of the most complicated pieces of chess to implement. They move forward, one square at a time, except for their first movement. However, they can move diagonally to capture other pieces. And in addition, there is the (in)famous "En passant" move that complicates everything (see https://en.wikipedia.org/wiki/En_passant, and the FEN documentation for ideas on how to encode this information https://www.chessprogramming.org/Forsyth-Edwards_Notation#En_passant_target_square). As any complicated feature, the original developer (Guille P) left this for the end, and then left the project. But you can do it.
Questions and ideas that can help you in the process:
- Can you write tests showing the bugs?
- What kind of tools can you use to spot the bug?
- Can you approach this incrementally? This is, splitting this task in many subtasks. How would you prioritize them?

## Difficulties encountered with fix Pawn Moves
- Complex Movement Rules: Pawns have unique rules (one-square forward, two-square initial move, diagonal capture, "En passant") requiring distinct logic and checks.
- State Management: "En passant" relies on tracking the board state and previous moves, adding complexity.
- FEN Encoding: Correctly representing "en passant target square" in FEN and updating it after moves.
- Interdependencies: Pawn rules must integrate smoothly with check, promotion, and capturing logic.
- Edge Cases: Handling scenarios like "En passant" at the board edge or pawn promotion.
- Debugging Tools: Visualizing and debugging pawn states and board interactions in Pharo can be challenging.


## Kata 2
Restrict legal moves
Goal: Practice code understanding, refactorings and debugging
In chess, when we are not in danger we can move any piece we want in general, as soon as we follow the rules. However, when the king gets threatened, we must protect it! The only legal moves in that scenario are the ones that save the king (or otherwise we lose). What are moves that protect the king? The ones that capture the attacker, block the attack, or move the king out of danger. Another way to see it is: A move protects the king if it moves it out of check.
The current implementation does not support this restriction. As any complicated feature, the original developer (Guille P) left this for the end, and then left the project. But you can do it.
Questions and ideas that can help you in the process:
- What tools help you finding the right place to put this new code?
- How do you avoid repeating all the existing code computing legal moves and checks?

##Difficulties encountered with Restrict Legal Moves
- Simulating Moves Safely: Testing potential moves without permanently altering the game state requires a reliable mechanism to temporarily modify and then restore the board state. This is crucial to check if a move leaves the king in danger.
- King Safety Validation: Determining whether a move leaves the king in check involves accurately tracking the king’s position and verifying if it remains under attack after the move. This requires precise evaluation of threats from all opponent pieces.
- Handling Special Rules: Rules like castling, en passant, and pawn promotion introduce additional complexities. For example, castling is only valid if the king is not in, passing through, or moving into check, which demands extra checks.
- Opponent Threat Calculation: Identifying squares attacked by opponent pieces is fundamental. This involves simulating their potential moves and ensuring that their attacks are correctly calculated for every board configuration.


# Kata 3
Refactor piece rendering
Goal: Practice refactorings, double dispatch and table dispatch
The game renders pieces with methods that look like these:
MyChessSquare >> renderKnight: aPiece

```
	^ aPiece isWhite
		  ifFalse: [ color isBlack
				  ifFalse: [ 'M' ]
				  ifTrue: [ 'm' ] ]
		  ifTrue: [
			  color isBlack
				  ifFalse: [ 'N' ]
				  ifTrue: [ 'n' ] ]
```

As any project done in stress during a short period of time (a couple of evenings when the son is sick), the original developer (Guille P) was not 100% following coding standards and quality recommendations. We would like you to clean up this rendering logic and remove as much conditionals as possible, for the sake of it. You can do it.
Questions and ideas that can help you in the process:
- Can you do an implementation with double dispatch?
- Can you do an implementation with table dispatch?
- What are the good and bad parts of them in this scenario? Do you understand why?


## Difficulties encountered with Refactor piece Rendering

