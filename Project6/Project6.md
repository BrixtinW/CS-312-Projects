# Algorithmic Problem Solving 

## Code
- [x] Include your source code for the 6 problems

### 1 - Two Sum

```
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        
        n = len(nums)
        for i in range(n):
          
            for j in range(n):
                if i == j:
                    continue
                
                if nums[i] + nums[j] == target:
                    return [i, j]
```

### 39 - Combination Sum

```
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        result = []
        sortedCandidates = sorted(candidates)
        lenOfCan = len(candidates)

        def helper(listSoFar, currSum, index):

            if currSum == target:
                result.append(listSoFar)

            else:

                for i in range(index, lenOfCan):
                    if currSum+candidates[i] <= target:
                        helper(listSoFar+[candidates[i]], currSum+candidates[i], i)


        helper([], 0, 0)

        return result
```

### 1584 - Min Cost to Connect All Points

```
import math
import heapq

class Solution:
    def minCostConnectPoints(self, points: List[List[int]]) -> int:
        
        def manhattanDistance(point1, point2):
            return abs(point1[0] - point2[0]) + abs(point1[1] - point2[1])

        # Initialization
        n = len(points)
        min_heap = []
        visited = set()  # To track visited nodes
        cost = 0
        visited.add(0)
        
        for i in range(1, n):
            dist = manhattanDistance(points[0], points[i])
            heapq.heappush(min_heap, (dist, i))




        # Prims algorithm
        while len(visited) < n:

            smallest_cost, next_node = heapq.heappop(min_heap)
            if next_node in visited:
                continue
            
            cost += smallest_cost
            visited.add(next_node)

            for i in range(n):
                if i not in visited:
                    dist = manhattanDistance(points[next_node], points[i])
                    heapq.heappush(min_heap, (dist, i))
        
        return cost
```

### 102 - Binary Tree Level-order Traversal

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        res = []

        if root == None:
            return res


        stack = [root]
        count = 0
        exp = 1
        levelArr = []
        pruned = 0

        while stack:
            currNode = stack.pop(0)

            levelArr.append(currNode.val)

            if currNode.left != None:
                stack.append(currNode.left)
                count += 1
            else:
                pruned +=1

            if currNode.right != None:
                stack.append(currNode.right)
                count += 1
            else:
                pruned +=1

            if (count + pruned) == (2**exp):
                exp += 1
                count = 0
                res.append(levelArr)
                levelArr = []
                pruned = pruned*2


        return res
```

### 279 - Perfect Squares

```
import math

class Solution:
    def numSquares(self, n: int) -> int:
        if n < 4:
            return n
        
        solution = [0, 1, 2, 3] + ([math.inf] * (n-3))
 
        for capacity in range(4, n + 1):
            squareLimit = math.floor(math.sqrt(n)) + 1 
            for root in range(1, squareLimit):
                square = (root * root)
                if square <= n:
                    solution[capacity] = min(solution[capacity], solution[capacity - square] + 1)

        return solution[n]
```

### 406 - Queue Reconstruction by Height

```
class Solution:
    def reconstructQueue(self, people: List[List[int]]) -> List[List[int]]:
        
        sortedPeople = sorted(people, key=lambda x: x[0])
        result = [-1] * len(people)
        n = len(people)

        for nextMin in sortedPeople:

            count = nextMin[1]

            for i in range(n):

                
                if result[i] == -1:
                    if count > 0:
                        count -= 1 
                        continue
                    else:
                        result[i] = nextMin
                        break
                    
                elif result[i][0] < nextMin[0]:
                    continue
                elif result[i][0] >= nextMin[0]:
                    if count > 0:
                        count -= 1
                        continue
                    else:
                        result.insert(i, nextMin)
                        result.pop(-1)
                        break

        return result
```

## Time and Space Complexity analysis
- [x] Provide a brief analysis of the time and space complexity of your solution and which Problem Solving Tools from the class you used.

### 1 - Two Sum

### 39 - Combination Sum

### 1584 - Min Cost to Connect All Points

### 102 - Binary Tree Level-order Traversal

### 279 - Perfect Squares

### 406 - Queue Reconstruction by Height

## Compare Solutions
- [x] Meet with another student in the class that solved the same problem and compare your solutions and problem solving strategies.

I met with Jackson Steed, Tristan Bitter, and MaiLia Pohahau to discuss solutions. I was able to compare solutions to each problem except 406 Queue Reconstruction by Height. 

### 1 - Two Sum

I used a nested for loop and a greedy approach to solve the problem. Jackson and MaiLia used hash maps with the number they were considering for fast access and were able to avoid the second for loop. Tristan did something similar but used a normal map instead of a hashed map. Everyone applied a greedy approach. 

### 39 - Combination Sum

I sorted the candidate array and applied a branch and bound approach where I would prune values based on how they were sorted. Tristan also sorted the list before, and both Tristan and Jackson subtracted values rather than added values. Their starting node was the target and my starting node was zero. MaiLia did an approach similar to mine except she didn't prune any branches but built down starting at the top.  

### 1584 - Min Cost to Connect All Points

I applied Prim's algorithm using heapq's binary min-heap just like we did in class. Tristan and MaiLia both used Prim's algorithm as well, but implemented it with queues. Tristan implmented his own queue, and Jackson did not do this problem. 

### 102 - Binary Tree Level-order Traversal

I used one while loop and a stack that would push nodes onto it to do a breadth first search. Tristan and MaiLia used for loops with queues, and Jackson used recursion with a level counter to determine which nodes to check. 

### 279 - Perfect Squares

I used a dynamic programming approach and implemented it very similiarly to the knapsack problem. I only iterated to the square root of the number I was looking for to save computations. Tristan did it the same way I did. Jackson also did a dynamic programming approach, but he also stored his perfect squares in an array which made the algorithm much faster. He used more memory, but concluded much faster. I also talked to Thomas about his solution, and he did a dynamic programming approach but initialized all entries to infinity instead of filling the dp-array slowly. 

### 406 - Queue Reconstruction by Height

I was not able to meet anyone who did this problem. I had a double nested for loop that would sort each person's height one by one using some tricks that take advantage of the setup of the problem.

## Conclusion
- [x] highlight the overall lessons learned from this project.

One important lesson I learned from this project was the importance of Time Complexity. I was often scared of writing more lines of code down because I was afraid it would increase the space complexity of the problem. This resulted in making all my solutions be the 75 percentile of space complexity, but in the lower 25 percentile of time complexity. I was far behind most solutions as far as speed, and using Dynamic Programming to speed up solutions such as Jackson did in the perfect squares problem was very helpful in speeding up my solutions. I think for this reason Dynamic Programming was a very helpful topic that I think I will use a lot in future problems like these. 

