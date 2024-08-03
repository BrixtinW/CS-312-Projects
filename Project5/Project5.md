# Travelling Salesperson (Branch and Bound)

## 1 - Code for Greedy and Branch and Bound Algorithms
- [x] Include your self-documenting code for both greedy and branch and bound.

```

	''' <summary>
		This is the entry point for the greedy solver, which you must implement for
		the group project (but it is probably a good idea to just do it for the branch-and
		bound project as a way to get your feet wet).  Note this could be used to find your
		initial BSSF.
		</summary>
		<returns>results dictionary for GUI that contains three ints: cost of best solution,
		time spent to find best solution, total number of solutions found, the best
		solution found, and three null values for fields not used for this
		algorithm</returns>
	'''

	def greedy( self,time_allowance=60.0 ):
		results = {}
		cities = self._scenario.getCities()
		ncities = len(cities)
		foundTour = False
		best_cost = np.inf
		best_solution = None
		count = 0
		start_time = time.time()

		while not foundTour and time.time() - start_time < time_allowance:
			for start_index in range(ncities):
				visited = [False] * ncities
				route = []
				totalEdgeCost = 0
				currCity = cities[start_index]
				visited[start_index] = True
				route.append(currCity)

				for _ in range(ncities - 1):
					nextCity = None
					minCost = np.inf
					
					# Find the nearest unvisited city
					for i in range(ncities):
						if not visited[i]:
							cost = currCity.costTo(cities[i])
							if cost < minCost:
								minCost = cost
								nextCity = cities[i]
					
					# Move to the nearest city
					if nextCity is None:
						# if the last node is a sink (has no leaving edges) 
						totalEdgeCost = np.inf
						break
					route.append(nextCity)
					totalEdgeCost += minCost
					visited[cities.index(nextCity)] = True
					currCity = nextCity

				# Create the solution object
				if totalEdgeCost < best_cost:
					best_cost = totalEdgeCost
					best_solution = TSPSolution(route)

				count += 1

		
				if time.time() - start_time >= time_allowance:
					break

			foundTour = best_solution is not None

		end_time = time.time()

		results['cost'] = best_cost if best_solution is not None else math.inf
		results['time'] = end_time - start_time
		results['soln'] = best_solution
		results['count'] = count
		results['max'] = None # This is supposed to be the max size of the queue
		results['total'] = None # this is the total number of nodes created?
		results['pruned'] = None # this is the number of pruned branches
		return results

```
```

	''' <summary>
		This is the entry point for the branch-and-bound algorithm that you will implement
		</summary>
		<returns>results dictionary for GUI that contains three ints: cost of best solution,
		time spent to find best solution, total number solutions found during search (does
		not include the initial BSSF), the best solution found, and three more ints:
		max queue size, total number of states created, and number of pruned states.</returns>
	'''

	def branchAndBound( self, time_allowance=60.0 ):
		### INITIALIZATION ###
		results = {}
		self.cities = self._scenario.getCities()
		self.ncities = len(self.cities)
		maxSizeOfStack = 1
		numPrunedBranches = 0
		self.numNodes = 1
		start_time = time.time()



		startingEdgeArray = []
		for i in range(self.ncities):
			rowArray = []
			for j in range(self.ncities):
				rowArray.append(self.cities[i].costTo(self.cities[j]))
			
			startingEdgeArray.append(rowArray)


		startingLowerBound, startingEdgeArray  = self.normalize(startingEdgeArray)
		root = self.State(startingLowerBound, startingEdgeArray, [], 0)

		stack = deque([root])

		foundTour = False
		count = 0
		bssf = None
		lowestBoundSoFar = np.inf
		### INITIALIZATION ###

		
		# Branch and Bound algorithm #
		while len(stack) != 0 and time.time() - start_time < time_allowance: 
			if len(stack) > maxSizeOfStack:
				maxSizeOfStack = len(stack)

			# if a table is filled, see if its lowerbound is lower than the lowest so far and make it your best solution
			if len(stack[-1].vistedNodes) == self.ncities:
				if stack[-1].lowerBound < lowestBoundSoFar:
					leafNode = stack.pop()

					route = []
					for i in range( self.ncities ):
						route.append( self.cities[ leafNode.vistedNodes[i] ] )

					lowestBoundSoFar = leafNode.lowerBound
					bssf = TSPSolution(route, leafNode.lowerBound)
					count += 1
					foundTour = True
					continue



			# if the lowerbound of the top state is less than the previous lowest bound so far, then we continue. if it is above, then we can simply pop it off and continue
			# if the lower bound is negative, then you can pop it off becuase it is crossing lines somewhere
			while len(stack) > 0: # this while loop may be unneccessary, but I think the time complexity is slightly faster because you don't have the if statmenet above in it as well? may be negligible.
				if stack[-1].lowerBound > lowestBoundSoFar:
					stack.pop()
					numPrunedBranches += 1
					continue
				else:
					stack.extend(self.expand(stack.pop()))
					break
		# Branch and Bound Algorithm

		# Population results with the solution. this is outside the Branch and Bound algorithm #
		end_time = time.time()
		results['cost'] = bssf.cost if foundTour else math.inf
		results['time'] = end_time - start_time
		results['count'] = count
		results['soln'] = bssf
		results['max'] = maxSizeOfStack # This is supposed to be the max size of the queue
		results['total'] = self.numNodes # this is the total number of nodes created?
		results['pruned'] = numPrunedBranches # this is the number of pruned branches
		return results

	class State:
		# State object made to hold the values of each state for comparing state together. 
		def __init__(self, lowerBound, edgeArray, vistedNodes, lastNode):
			self.vistedNodes = vistedNodes
			self.vistedNodes.append(lastNode)
			self.lowerBound = lowerBound
			self.edgeArray = edgeArray
			self.lastNode = lastNode


	# This should return an ordered list of state objects (reverse ordered with the last being the smallest lower bound) which are extended onto the stack. 
	def expand(self, parentState):

		numChildStates = len(parentState.edgeArray[0]) - len(parentState.vistedNodes)
		self.numNodes += numChildStates 

		listOfStates = []
		for col in range(self.ncities):
			# if the edge to a certain node is infinity or has already been visted
			if col in parentState.vistedNodes or parentState.edgeArray[parentState.lastNode][col] == np.inf:
				continue
			# copy the parents edgeArray for each child state
			childEdgeArray = [row[:] for row in parentState.edgeArray]

			# get the preliminary lower bound of this state
			childLowerBound = parentState.lowerBound + childEdgeArray[parentState.lastNode][col]

			#  set everything on the row and col equal to infinity, as well as the back edge
			for i in range(self.ncities):
				childEdgeArray[parentState.lastNode][i] = np.inf
				childEdgeArray[i][col] = np.inf
			childEdgeArray[col][parentState.lastNode] = np.inf

			# normalize the child array
			lowerBoundAdditional, childEdgeArray = self.normalize(childEdgeArray)

			# add the child state to a list of all child states
			listOfStates.append(self.State((childLowerBound + lowerBoundAdditional), childEdgeArray, parentState.vistedNodes[:], col))





		if len(listOfStates) != 0:
			# could just return here
			return sorted(listOfStates, key=lambda state: state.lowerBound, reverse=True)
		else:
			# could just return here
			return listOfStates


	# this function is used for Debugging purposes. It just prints out the 2d state arrays in a format that is easily readable. 
	def printEdgeArray(self, startingEdgeArray):

    # Determine the maximum width needed for each entry

		# Format string for printing
		format_str = f"{{:<{4}}}"

		# Print the edge array
		for i in range(self.ncities):
			printString = ''
			for j in range(self.ncities):
				value = startingEdgeArray[i][j]
				if np.isinf(value):
					value_str = "inf"
				else:
					value_str = str(int(value))  # Convert to string, truncate to integer if needed
				printString += format_str.format(value_str) + " | "
			
			print('| ' + printString) 

		input("^ - - edge array - - ^ ")

	# This function subtracts all columns and rows by their lowest value and returns the new lowerBound
	def normalize(self, edgeArray):
		lowerBound = 0
		minForCol = edgeArray[0][:]
		for i in range(self.ncities):
			lowestLeavingEdge = min(edgeArray[i])
			if np.isinf(lowestLeavingEdge):
				continue

			lowerBound += lowestLeavingEdge

			for j in range(self.ncities):
				
				# this is for skipping a row if it is infinity or zero
				if edgeArray[i][j] == 0:
					minForCol[j] = edgeArray[i][j]
					continue

				# this if for subtracting everything by the min of the row
				edgeArray[i][j] = (edgeArray[i][j] - lowestLeavingEdge)

				# this is for finding the smallest value in the col
				if edgeArray[i][j] < minForCol[j]:
					minForCol[j] = edgeArray[i][j]

		# this is for subtracting columns by their lowest amount
		for i in range(self.ncities):

			if minForCol[i] != 0 and not np.isinf(minForCol[i]):
				lowerBound += minForCol[i]

			for j in range(self.ncities):
				if minForCol[j] == np.inf or minForCol[j] == 0:
					continue
				edgeArray[i][j] = (edgeArray[i][j] - minForCol[j])

		return lowerBound, edgeArray

```

      
## 2 - Time and Space Complexity
- [x] Discuss the time and space complexity of your algorithms.

## 3 - State Data Structure
- [x] Describe the data structures you used to represent the states.

## 4 - Priority Data Structure
- [x] Describe the priority data structure you use and how it works. 


## 5 - Initial BSSF
- [x] Describe your approach for the initial BSSF.


## 6 - Data Table
- [x] Include a table containing the specified columns.

| # of Cities |  Seed  | Running Time (Sec) | Cost of Best Tour Found | Max # of Stored States at a Given Time | # of BSSF updates | Total # of states created | Total # of States Pruned | Running Time | Tour Cost for Greedy |
|-------------|--------|--------------------|-------------------------|----------------------------------------|-------------------|---------------------------|--------------------------|--------------|----------------------|
|             |    |    |    |    |    |    |    |
|         |    |    |    |    |    |    |    |
|        |    |    |    |    |    |    |    |
|       |    |    |    |    |    |    |    |
|      |   |   |   |   |   |    |    |
|             |    |    |    |    |    |    |    |
|         |    |    |    |    |    |    |    |
|        |    |    |    |    |    |    |    |
|       |    |    |    |    |    |    |    |
|      |   |   |   |   |   |    |    |

## 7 - Experimental Results
- [x] Discuss the results in the table and why you think the numbers are what they are.

## 8 - Branch and Bound Methodology
- [x] Discuss the mechanisms you tried and how effective they were in getting the state space search to dig deeper and find more solutions early.



