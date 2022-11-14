Attempt 1:

1.1) When first starting this I was using the eightQueensRevisited code to try and solve the sudoku problem. I Changed the possible values form a 2d array to a 3d array, where x,y referred to the row and column, and z referred to the specific value in the list of values for current row column.

1.2) After creating a more suitable grid for sudoku, I then made a constraint function;
	- The function would remove values in the current row, column, and 3*3 grid
	- The function would also remove other values from the current position so that It is only left with single value.

As this is a recursive function, it would call itself each time an update is made to the grid. This way, after entering the given value for a grid, for most sudokus in very_easy, easy, and medium . The algorithm would solve without having to search.

1.3) To do this I made a function called checkSingle:
	- The function would for through the all the grid values and check if they had a single possible value,
	- if there was only 1 possible value, then put this as the value and call constraint function to check if it is valid, and to also git rid of this value from gird positions in the same column, row or 3*3 grid.
	-

1.4) By passing all the give values into the function constraint, it would:
	1) Put the value into the grid position
	2) remove all other possible values from the position
	3) remove this value from all gird positions that have a relationship to the current position
	4) call checkSingle if there are any values that can be put in without searching
	5) if there are then call constraint with this value and repeat from step 1

1.5) As some of the grids are unsolvable, I had to first check if the grids where valid to being with. These are grids, that may have overlapping values, or values where when constrain is applied, there is no possible value that can be put in that position.
	- Thus I made one function that checked all the grid values and their related positions to check if their where any contradictions
	-Another function to check if any position has no possible values

I soon came to realise this was really inefficient, so Implemented this check if no possible function with the function in 1.4. This reduced the time complexity by O(n^2)

1.6) After having dealt with the majority of very_easy, easy, and medium puzzles just from using constraint and checking positions with single values. I now needed to to implement back tracking.

Although I used the code from eightQueensRevisited, I was not creating an object, this meant that I was not using a saving a copy of the parent state to back track too. Instead, I would start from the top left and try a value for each position, if there was a contradiction, I would go back a position and try a different value.


The problem with doing it this way, was it was taking way too long since it was basically trying 81^9 attempts in the worst case scenario. Thus I had to do something to make it faster.

1.7) The first thing I tried was to remove the amount of possible values that was being tested for each of the positions. Currently it was trying any value from 1 to 9. But in 1.4, during the constraint function I am already removing possible values, so I only need to try the remaining possible values instead of trying all value 1-9. This made the function a lot faster, but it still wasn’t fast enough.

I thus wanted to remove possible values as I was assigning a value to each grid position. The problem with this, was that since I wasn’t creating a copy of the parent, If I path failed I wouldn’t be able to track track since some of the possible values  have been removed. As this was the case I tried creating an object so that I could keep a copy of a branch.

As I was trying to do this I had come to realise, a large part of the problem I had was that I was using too many for loops, and the fact that even though I was going from top left to bottom right, The process of picking the next grid position was basically random.  Going in the order was actually making it slower. Picking random positions would most likely have been faster than doing it this way. Thus I needed to go to a grid position with the leat amount of possible values.

This is as far as my first attempt went.
- I could run all sudokus,  but the hard ones took much too long since I was trying too many values, and had no real structure in picking the next grid position.


Attempt 2:

For this attempt I was looking around for a better way to solve sudoku and saw the DLX approach (Dancing links). I did not use this approach as I found it quite difficult to understand but I did consider some of the points ist maid.
	1. Using lists is inefficient as I sometimes I need to loop through the entire 2d list and this takes too long
	2. Thus, I decided to use a dictionary instead to store my grid values as I know the possible values of each position and can be searched for much easier.	3. Store all related information about a grid location in a dictionary too. This way you already know which positions changing a value will effect, you don’t need to work it out.


I done some more reading and came across Peter norvigs approach. This basically used the DLX approach but in a much easier to understand format.

2.1) First I needed to create variables to hold all the information needed to store my grid values.
	1) Columns are represented with values 1 to 9
	2) rows are represented with values A to I
	3) possible values is the string “123456789” - Strings behave like lists so you can apply the same functions you can do to lists
	4) UnitList = every combination of potential grid relationships. Every row, column, and 3*3 grid relatiohisp
	5) Units = dictionary where the key is the grid position and the value is all related grid positions
	5) peers = same as unites but without the current position in the value

2.2) now that there is all the grid variables needed we can start building the actual grid for sudoku. To do this there are 2 functions :

	parse_grid - Creates dictionary values which is used to store the each grid position and its possible values. Takes in a string of values, that correspond to one of the 81 positions in the grid
		1) to start with the each key has the value 123456789. As at the start there is no information
		2) For each char in the string input. We need to put these values into the grid.
		3) thus for each value in this string, check if it is valid and if it is go to the next value, else return false
		4) if false, then this grid is not solvable

	grid_values: this is the function that is called by parse_grid in order to check if the value given by the string at the start is possible
		1) the value is only possible if it is one of the possibleValues or 0
		2) Do this for every key in the dictionary gridPostion

Doing this you basically have a sudoku grid with all possible values, and are starting to remove values based on the grid input. The next step is to check if the values are valid and if they comply with the constraint.


2.3) Next I need a function that makes sure the given values are valid and remove value from the the current passions peers if they are. Also if a position only has 1 possible value left, Put this as the value for that position

	valid: This function is used to remove to test if if the given value is possible or not.
		1) it creates at tmp array with all the possible values other than the given value
		2) then call constraint and test these values
		3) if any of the cases return false, then the gird is unsolvable


	constraint: this is the main chuck of code for validation potential values. This section can be broken into 3 main parts

		1) If the value passed from valid is not in the possible values list, then there is nothing to do, return values

		2.1) if this is not the case then remove the value from the possible values.
		2.2) if by removing this value the list of possible values for the positions is empty. Then there is contradiction somewhere and the grid is unsolvable
		2.3) If there is only 1 possible value left, then call constraints with the given value and position  and test if it is valid

		3.1) if the value return true, along as the length of possible values != 0 then call valid, and repeat this process again for the new value


These 2 function are called recursively but themselves, and each other. Basically all it is doing is when given a value and position. Check if this value fits into the position. If it does, remove this value from all of its peers, since none of these values can be this value. Then if by removing these potential values in its peers, if their list of possible values == 1, then check if this is valid and if it is make this the value for that position. This repeats over and over and the same as in attempt 1, it solves almost all very_easy, easy, and medium sudokus.



2.4) Now I need to actually implement the back tracking search . This is only really 1 main function that calls itself recursively. 
	search:
	1) if values is ever false the return false, This is for the case where there is a contradiction or if the puzzle is not solvable in this branch
	2) if all values in the dictionary only have 1 possible value, then the puzzle is solved.

	if it is neither of the cases above
	3) Pick the key value pair with the least number of possible values > 1 and pick these as the next to search.
	4) try a value and if it fits, repeat step 3
	5) if it doesn’t fit go back to the parent and try a different value.


2.5) Since algorithm uses strings and dictionaries instead of an array, I need 2 final functions. One to convert the numpy 2d array given into a string, so that it an be parsed into a dictionary, and the other to connect the dictionary into a 2d array so that it can be compared to the solution.


As you can see the 2 attempts are pretty similar in the actual idea behind solving it. They both use constraint search but the main difference is the way the values are stored and the way the next position to check is worked out. In attempt 1 most of the time complexity came from using too many for loops to work out positions., removing values, checking if values had contradictions. This meant for each grid position I had a complexity of 3*9^9, and this is just for the constraint, not including the time required for the search position . Thus storing the peers in a dictionary is a much better approach as you dont need to search for them, and you already know what possible values so its easy to remove them.  You could probably do the same thing with an array and have the possible values as a sting. However, the main thing that made this method better was storing the peers. The “Back tracking” method is much the same.
