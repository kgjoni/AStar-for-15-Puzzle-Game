import sys
import psutil
import random
import itertools
import time
import math
import os
import copy


# Define list of nodes
class N_List:

    # Initialize class attributes
    def __init__(x):
        
        x.States = {}
        x.List = []
		
    # Add node to list (ordered)
    def order(x, value):
        
        # Range
        small = 0
        big = len(x.List)
        
        while (small < big):
            central = ((small+big)//2)
            
            if ((x.List[central].combined()) <= (value.combined())): 
                small = central + 1
            
            else: 
                big = central
        
        x.List.insert(small, value)

    # Empty (no nodes)
    def noElements(x):
        
        return len(x.List) == 0

    # Insert nodes to list
    def plus(x, value):
        
        # Same copy
        if (str(value.place()) in x.States):
            return
        
        x.States[str(value.place())] = True
        x.order(value)
	
    # Pop node from list
    def pop(x):
        
        return x.List.pop(0)

# Define structure of nodes
class N_Structure:

    # Decribe all possible moves
    def description(x, upOrdown, leftOrright):
        
        # Move down
        if upOrdown == 1:
            return 'D'
        
        # Move right
        if leftOrright == 1:
            return 'R'
        
        # Move up
        if upOrdown == -1:
            return 'U'
        
        # Move left
        if leftOrright == -1:
            return 'L'


    def next_children(x, val):
        
        M = []
        x_coor, y_coor = val.axis(0)
		
        # Outer loop
        for n in range(-1, 2):
            # Inner loop
            for m in range(-1, 2):
                
                if ((n == m) or ((n * m) != 0)):
                    # Continue
                    continue
                
                x_shift, y_shift = x_coor + n, y_coor + m
                
                if ((not (0 <= x_shift <= 3)) or (not (0 <= y_shift <= 3))):
                    # Continue
                    continue
                
                place = val.place()
                place[x_coor][y_coor] = place[x_shift][y_shift]
                place[x_shift][y_shift] = 0
                
                turn = val.set_shift()
                turn.append(x.description(n, m))
                e = Node(place, turn, val.choose_h())
                
                # Append children
                M.append(e)

        # Return list of children
        return M


# Class Node
class Node:

    # Initialize class attributes
    def __init__(x, p, m, h):
        
        x.distance = 0
        x.location = p
        x.shift = m
        x.h_chosen = h
		
    # Return a copy of the moves     
    def set_shift(x):
        
        return copy.copy(x.shift)
		
    # Get position from the values
    def axis(x, y):
        
        a = 0

        # Outer loop
        for n in x.location:
            b = 0

            # Inner loop
            for m in n:
                if (m == y):
                    
                    # return position
                    return [a, b]
                b = b + 1

            a = a + 1

    # Return distance
    def node_length(x):
        
        return len(x.shift)

    # Combine distances
    def combined(x):
        
        return x.node_length() + x.node_heuristic()

    # Choose heuristic
    def choose_h(x):
        
        return x.h_chosen
		
    # Return node location
    def place(x):
        
        return copy.deepcopy(x.location)
		
    def node_heuristic(x):
        
        # Check if is equal to 0
        if (x.distance == 0):
            x.distance = x.h_chosen.find(x)

        # Return
        return x.distance
		

# H2 : Manhattan Distance
class h2_manhattan_distance:

    # Initialize class attributes
    def __init__(x):
        
        # What the final board should look like
        x.target = Node([[1, 2, 3, 4], [5, 6, 7, 8], [9, 10, 11, 12], [13, 14, 15, 0]], [], x)


    # Find manhattan distance
    def find(x, y):
        
        distance = 0
        i = 1
        
        while(i < 16):
            x_final, y_final = x.target.axis(i)
            x_current, y_current = y.axis(i)
            distance = distance + abs(x_final - x_current) + abs(y_final - y_current)
            i += 1

        # Return distance of nodes
        return distance


# H1 : Misplaced tiles
class h1_misplaced_tiles:

    # Initialize class attributes
    def __init__(x):
        
        # What the final board should look like
        x.target = Node([[1, 2, 3, 4], [5, 6, 7, 8], [9, 10, 11, 12], [13, 14, 15, 0]], [], x)

    
    # Find number of misplaced tiles
    def find(x, y):
        
        placed_tiles = 0
        misplaced_tiles = 0
        i = 1
        
        while(i < 16):
            x_final, y_final = x.target.axis(i)
            x_current, y_current = y.axis(i)
            if ((x_final == x_current) and (y_final == y_current)):
                placed_tiles += 1
            i += 1

        # Get the difference    
        misplaced_tiles = 15 - placed_tiles;
        return misplaced_tiles
		

# A star search using two heuristics
class run_a_star:

    # Initialize class attributes
    def __init__(x, h_chosen):
        
        x.List = N_List()
        x.Structure = N_Structure()
        x.h_chosen = h_chosen

    # Add first element from the position
    def set_search(x, place):
        
        val = Node(place, [], x.h_chosen)
        x.List.plus(val)
		
    # Search for a solution
    # Return list if found, None if not found
    def run_search(x, place):
        
        x.set_search(place)
        while not x.List.noElements():
            
            # Remove element
            temp = x.List.pop()
            
            if (temp.node_heuristic() == 0):

                # Found
                return temp.set_shift()
            
            S = x.Structure.next_children(temp)
        
            for i in S:
                x.List.plus(i)
        
        # Not found
        return None
		

def main():

    while True:

        # Memory calculation
        process = psutil.Process(os.getpid())
        initial_memory = process.memory_info().rss / 1024.0

        # Get input
        user_input = input("Enter numbers:\n")

        # Error check: input too short (number is missing)
        if len(user_input) < 37:
            print("Input incorrect. Try again...")
            continue

        # Get input from user and convert it into a list
        user_input = user_input.replace(" ", ",")
        new_user_input = [str(k) for k in user_input.split(',')]
        new_user_input = list(map(int, new_user_input))
        new_list = []

        # Convert input into a list of lists
        for i in range(0, len(new_user_input), 4):
            new_list.append(new_user_input[i:i+4])

        # Set initial board
        initial_board = new_list

#-----------------------------------------------------------------------------------------------------------------------------
        # Implementation of the two heuristics
        # Run only one heuristic at a time to solve the puzzle

        #h_chosen = h1_misplaced_tiles()
        h_chosen = h2_manhattan_distance()
#-----------------------------------------------------------------------------------------------------------------------------
        
        # Run A* search with chosen heuristic
        search = run_a_star(h_chosen)
	
        # Start timer
        starting_time = time.time()

        # Get number of nodes expanded
        node_expanded = h_chosen.find(Node(initial_board, [], None))

        # Get moves needed to find solution
        moves = search.run_search(initial_board)

        # End timer
        ending_time = time.time()

        #Calculate total time (rounded in milliseconds, ms)
        total_time = (ending_time - starting_time)
        total_time = int(round(total_time * 1000))
        final_memory = process.memory_info().rss / 1024.0

        # Solution found, print results
        if len(moves) != 0:
            print("Moves:", moves)
            print("Node expanded:", node_expanded)
            print("Time taken:", total_time, "ms (milliseconds)")
            print("Memory used:", str(final_memory-initial_memory) + " KB")
            break

        # No solution
        else:
            print("Solution not found.")
            break

if __name__=="__main__":main()
