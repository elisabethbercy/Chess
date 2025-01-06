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

# Kata 1: Correction of Pawn Movement

## Objective  
Practice debugging and testing.

---

## Implementation Summary  
This kata focused on implementing the rules for pawn movements, including:  
- Moving one square straight ahead.  
- Moving two squares on the first move.  
- Capturing diagonally.  

An incremental approach and the **State Design Pattern** were used to structure the movement logic.

---

## Key Steps  

### 1. Writing Tests  
- Verifying standard and initial movements.  
- Testing diagonal captures.  
- Validating illegal cases.  

### 2. Managing States with the State Pattern  
- **InitialForwardState:** Handles the two-square move on the first turn.  
- **NormalForwardState:** Manages single-square movements.  
- **DiagonalCaptureState:** Handles diagonal captures.  

Each state calculates possible moves, simplifying the logic within the `Pawn` class.

---

## Tests and Coverage  
- Unit tests for each movement rule.  
- Manual checks using a simplified graphical interface.

---

## Design Decisions  

### 1. Prioritization of Essential Features  
Basic movement rules were implemented first, before adding advanced rules like *En passant*.  

### 2. Reducing Complexity  
Dividing logic into state subclasses resulted in clearer, more modular code.  

### 3. Focus on Testability  
Thorough testing ensured the reliability of all implemented features.


---


## Kata 2
Restrict legal moves
Goal: Practice code understanding, refactorings and debugging
In chess, when we are not in danger we can move any piece we want in general, as soon as we follow the rules. However, when the king gets threatened, we must protect it! The only legal moves in that scenario are the ones that save the king (or otherwise we lose). What are moves that protect the king? The ones that capture the attacker, block the attack, or move the king out of danger. Another way to see it is: A move protects the king if it moves it out of check.
The current implementation does not support this restriction. As any complicated feature, the original developer (Guille P) left this for the end, and then left the project. But you can do it.
Questions and ideas that can help you in the process:
- What tools help you finding the right place to put this new code?
- How do you avoid repeating all the existing code computing legal moves and checks?

## Difficulties encountered with Restrict Legal Moves
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


# Refactoring Report: Piece Rendering in Pharo

## Objective

The goal of this kata is to simplify the piece rendering logic by removing unnecessary conditionals. The previous implementation relied heavily on complex checks, making the code harder to read and maintain. We applied **double dispatch**, **inheritance**, and **polymorphism** to achieve cleaner, more maintainable code while preserving functionality.

---

## Key Steps in the Refactoring Process

1. **Identifying the Problem**:  
   The original piece rendering logic contained conditionals that checked both the piece type and the square color, making the code difficult to maintain.

2. **Applying Double Dispatch**:  
   We eliminated conditionals by splitting the rendering logic into specific methods for each combination of piece and square color, using **double dispatch** to delegate the rendering behavior to the appropriate method based on both piece type and square color.

3. **Creating Specific Methods**:  
   Each piece class (e.g., `BlackBishop`, `WhiteBishop`) now has methods to render on black and white squares:
   ```smalltalk
   BlackBishop >> renderPieceOnBlackSquare [ ^ 'v' ]
   BlackBishop >> renderPieceOnWhiteSquare [ ^ 'V' ]
   ```

4. **Utilizing Inheritance**:  
   The `BlackBishop` and `WhiteBishop` classes inherit common behavior, reducing duplication and promoting code reuse, while still allowing for future extension.

5. **Using Polymorphism**:  
   Each class implements its own rendering behavior, making the code **flexible** and **extensible**. New pieces can be added without modifying the existing logic.

6. **Eliminating Conditionals**:  
   We removed complex conditionals, simplifying the code and improving readability.

7. **Ensuring Easy Extensibility**:  
   This structure allows the other pieces to be added easily by implementing their own rendering methods without touching other parts of the code.

---

## Rendered Symbols

Here’s how each bishop is displayed based on the square color:

| Bishop Type     | Square Color | Rendered Symbol |  
|-----------------|--------------|-----------------|  
| Black Bishop    | Black        | v               |  
| Black Bishop    | White        | V               |  
| White Bishop    | Black        | b               |  
| White Bishop    | White        | B               |  

---

## Why This is Better

- **No Conditionals**: The rendering logic is now handled by specific methods for each piece and square color combination, eliminating the need for conditional checks.
- **Simpler Logic**: The code is cleaner and more maintainable, with each piece class directly handling its rendering.
- **Easy to Extend**: New pieces can be added without altering existing code.

This refactor uses **double dispatch**, **inheritance**, and **polymorphism** to:
- **Double Dispatch**: Dispatch behavior based on both piece type and square color.
- **Inheritance**: Shared behavior is inherited, reducing duplication.
- **Polymorphism**: Each class can implement its specific rendering logic, allowing for easy extension.

This refactor improves the code by :
- ** Simplifying the piece rendering logic.
- ** Semoving complex conditionals, and making the codebase more maintainable and extensible.
- ** By leveraging double dispatch, inheritance, and polymorphism, the solution is flexible and scalable, allowing for easy adaptation to future changes.





---

## Conclusion  
- ** Kata 1 successfully structured pawn movement rules using an incremental approach and the State design pattern. Comprehensive testing helped detect and fix bugs efficiently.
- ** Kata 2 successfully restricted legal movement rules to protect the king
- ** kata 3 This refactoring exercise demonstrates how applying object-oriented principles, like double dispatch and polymorphism, can significantly improve the structure and maintainability of code.
By shifting the rendering logic from MyChessSquare to the individual piece classes, we’ve made the code cleaner, easier to understand, and more flexible for future changes.
