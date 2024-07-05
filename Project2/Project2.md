# Convex Hull

## 1 - Code
- [x] Correct functioning code to solve the Convex Hull problem using the nlogn divide and conquer algorithm presented in class, with appropriate comments.
```
import math

from which_pyqt import PYQT_VER
if PYQT_VER == 'PYQT5':
	from PyQt5.QtCore import QLineF, QPointF, QObject
elif PYQT_VER == 'PYQT4':
	from PyQt4.QtCore import QLineF, QPointF, QObject
elif PYQT_VER == 'PYQT6':
	from PyQt6.QtCore import QLineF, QPointF, QObject
else:
	raise Exception('Unsupported Version of PyQt: {}'.format(PYQT_VER))




import time

# Some global color constants that might be useful
RED = (255,0,0)
GREEN = (0,255,0)
BLUE = (0,0,255)

# Global variable that controls the speed of the recursion automation, in seconds
PAUSE = 0.25

#
# This is the class you have to complete.
#
class ConvexHullSolver(QObject):

# Class constructor
	def __init__( self):
		super().__init__()
		self.pause = False
	
	# Some helper methods that make calls to the GUI, allowing us to send updates
	# to be displayed.
	
	def showTangent(self, line, color):
		self.view.addLines(line,color)
		if self.pause:
			time.sleep(PAUSE)
	
	def eraseTangent(self, line):
		self.view.clearLines(line)
	
	def blinkTangent(self,line,color):
		self.showTangent(line,color)
		self.eraseTangent(line)
	
	def showHull(self, polygon, color):
		self.view.addLines(polygon,color)
		if self.pause:
			time.sleep(PAUSE)
	
	def eraseHull(self,polygon):
		self.view.clearLines(polygon)
	
	def showText(self,text):
		self.view.displayStatusText(text)
	
	# this sorts the points clockwise
	def sort_points_clockwise(self, points):
		points_with_angles = [(point, (math.atan2(point.y(), point.x()))) for point in points]
		points_with_angles.sort(key=lambda x: x[1], reverse=True)
		return [point for point, angle in points_with_angles]
		
	class Line:
		
		def __init__(self, pt1, pt2):
			
			if pt1.x() < pt2.x():
				self.leftPoint = pt1
				self.rightPoint = pt2
			else:
				self.leftPoint = pt2
				self.rightPoint = pt1

			self.slope = self.calculate_slope()
			self.intercept = self.calculate_intercept()
			
		# this finds the slope between the two points
		def calculate_slope(self):
			return (self.rightPoint.y() - self.leftPoint.y()) / (self.rightPoint.x() - self.leftPoint.x())
			
		# function to find the y-intercept of a line	
		def calculate_intercept(self):
			return self.leftPoint.y() - (self.slope * self.leftPoint.x())
			
		# this returns the y value on the line using the x value of the point you want to compare to the line
		def findPointOnLine(self, differentHullPoint):
			return (self.slope * differentHullPoint.x()) + self.intercept
		
		# this returns if a point is below the line or above the line. Points should never be equal.
		def isPointBelowLine(self, differentHullPoint):
			if self.findPointOnLine(differentHullPoint) < differentHullPoint.y():
				return False
			# this is false if the point is higher than the point on the line at value x
			elif self.findPointOnLine(differentHullPoint) > differentHullPoint.y():
				return True
			else:
				print("Points were equal!!! Error!")
	
	# This function merges two different hulls and returns a list of all the points in the greater hull.
	def mergeHulls(self, hullL, hullR):
		
		######################
		##find upper tangent##
		######################
		

		# this suggests that each hull is sorted 
		rightAnchor = hullR[0]
		leftAnchor = hullL[len(hullL) - 1]
		
		#this gives the slope of the line between the two points
		currentUpperTan = self.Line(leftAnchor, rightAnchor)
		upperTanFound = False
		
		while upperTanFound == False:
			upperTanFound = True
			
			# move up left hull
			for i in range(len(hullL)):
				if hullL[i] == leftAnchor:
					continue
	  
				if currentUpperTan.isPointBelowLine(hullL[i]) == False:
					
					leftAnchor = hullL[i]
					currentUpperTan = self.Line(leftAnchor, rightAnchor)
					upperTanFound = False  
			
			# move up right hull
			for j in range(len(hullR)):
				
				if hullR[j] == rightAnchor:
					continue
				
				if currentUpperTan.isPointBelowLine(hullR[j]) == False:
					
					rightAnchor = hullR[j]

					currentUpperTan = self.Line(leftAnchor, rightAnchor)
					upperTanFound = False  
					
			# Upper tangent found at this point
	
		######################
		##find lower tangent##
		######################
	

		# this suggests that each hull is sorted 
		rightAnchor = hullR[0]
		leftAnchor = hullL[len(hullL) - 1]
		
		#this gives the slope of the line between the two points
		currentLowerTan = self.Line(leftAnchor, rightAnchor)
		lowerTanFound = False
		
		while lowerTanFound == False:
			lowerTanFound = True
			
			# move up left hull
			for i in range(len(hullL)):
				if hullL[i] == leftAnchor:
					continue
	  
				if currentLowerTan.isPointBelowLine(hullL[i]) == True:
					
					leftAnchor = hullL[i]
					currentLowerTan = self.Line(leftAnchor, rightAnchor)
					lowerTanFound = False  
			
			# move up right hull
			for j in range(len(hullR)):
				if hullR[j] == rightAnchor:
					continue
	  
				if currentLowerTan.isPointBelowLine(hullR[j]) == True:
					
					rightAnchor = hullR[j]
					currentLowerTan = self.Line(leftAnchor, rightAnchor)
					lowerTanFound = False  
			
			# Lower tangent found at this point

		######################
		##  MERGE THE HULL  ##
		######################
					
		mergedHull = []

		# adding relevant points from the left hull
		if currentUpperTan.leftPoint == currentLowerTan.leftPoint:
			mergedHull.append(currentUpperTan.leftPoint)

		else:
			leftHullBisectLine = self.Line(currentUpperTan.leftPoint, currentLowerTan.leftPoint)

			for i in range(len(hullL)):

				if (hullL[i] == currentUpperTan.leftPoint or hullL[i] == currentLowerTan.leftPoint):
					mergedHull.append(hullL[i])
				
				elif leftHullBisectLine.slope < 0 and leftHullBisectLine.isPointBelowLine(hullL[i]) == True:
					mergedHull.append(hullL[i])
					
				elif leftHullBisectLine.slope > 0 and leftHullBisectLine.isPointBelowLine(hullL[i]) == False:
					mergedHull.append(hullL[i])
			
			


		# adding relevant points from the right hull
		if currentUpperTan.rightPoint == currentLowerTan.rightPoint:
			mergedHull.append(currentUpperTan.rightPoint)

		else:
			rightHullBisectLine = self.Line(currentUpperTan.rightPoint, currentLowerTan.rightPoint)
			
		
			for j in range(len(hullR)):
				if hullR[j] == currentUpperTan.rightPoint or hullR[j] == currentLowerTan.rightPoint:
					mergedHull.append(hullR[j])
			
				elif rightHullBisectLine.slope < 0 and rightHullBisectLine.isPointBelowLine(hullR[j]) == False:
					mergedHull.append(hullR[j])
					
				elif rightHullBisectLine.slope > 0 and rightHullBisectLine.isPointBelowLine(hullR[j]) == True:
					mergedHull.append(hullR[j])
			
			
			
		# sort the points clockwise before returning!!!
		mergedHull = self.sort_points_clockwise(mergedHull)

		#we want to return a list of all the points in the convext hull in clockwise order.
		return mergedHull
	

		
	#This is my Divide and Conquer algorithm to solve the Convex Hull problem
	# points is a list of qpointf points. 
	def DCHull(self, points):
		
		#base case. if there are less than three points, then all of them are in its own hull. 
		if len(points) <= 3:
			return points
	
		middleIndex = len(points) // 2
		
		hullL = self.DCHull(points[:middleIndex])
		hullR = self.DCHull(points[middleIndex:])
		
		return self.mergeHulls(hullL, hullR)
	

	
	# This is the method that gets called by the GUI and actually executes
	# the finding of the hull
	def compute_hull( self, points, pause, view):
		self.pause = pause
		self.view = view
		assert( type(points) == list and type(points[0]) == QPointF )
	
		t1 = time.time()



		sortedPoints = sorted(points, key=lambda point: point.x())
		print("Sorted points:")
		i = 0
		for point in sortedPoints:
			i += 1
			print(i, ": ", point.x(), point.y())



		t2 = time.time()
	
		t3 = time.time()
		
		############################################## MY CODE #############################
		
		listOfHullPoints = self.DCHull(sortedPoints)
		polygon = [QLineF(listOfHullPoints[i], listOfHullPoints[(i+1) % len(listOfHullPoints) ] ) for i in range(len(listOfHullPoints))]
		
		############################################## MY CODE #############################

		t4 = time.time()
	
	# when passing lines to the display, pass a list of QLineF objects.  Each QLineF
	# object can be created with two QPointF objects corresponding to the endpoints
		self.showHull(polygon,RED)
		self.showText('Time Elapsed (Convex Hull): {:3.3f} sec'.format(t4-t3))

	
		
```
## 2 - Time and Space Complexity
- [x] Discuss the time and space complexity of your algorithm.



## 3 - Experimental Outcomes

## 4 - Differences between theoretical and empirical analyses

## 5 - Examples
