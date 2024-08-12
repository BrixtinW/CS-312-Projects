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

## Compare Solutions
- [x] Meet with another student in the class that solved the same problem and compare your solutions and problem solving strategies.








Jackson steed
Two sum - used a hashed list (with the hash being the number) to avoid a nested for loop
Combined sum - subtracted every number from the target created a tree with every subtraction. Built down rather than up
Binary tree level order - passed in level counter. Used recursion. 
Perfect squares - stored all perfect squares for faster access. Dynamic programming. Like knapsack. Used more memory to make it faster.


Tristan bitter
Two sum - used a map with number as index
Combined sum - sorted the list before and subtracted each number from the target until it equaled 0. Built down rather than up
Min cost to all points - used a queue. Used prims.
Binary tree level order - used a queue. Used a for loop.
Perfect squares - did it like knapsack. Looped until the square root of the number 




MaiLia Pohahau
Two sum - used a hash map
Combined sum - filled out the whole tree and built down. 
Min cost to all points - used a queue. Used prims
Binary tree level order - used a queue



Thomas
Perfect squares - used dynamic programming, initialized to infinity, 









## Conclusion
- [x] highlight the overall lessons learned from this project.



