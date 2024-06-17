# Virtual-paint-on-the-screen
#This the image processing project which is made by using openCV library of python and image processsing concepts

import cv2
import numpy as np

# Initialize video capture
cap = cv2.VideoCapture(0)

# Set frame dimensions and brightness
frameWidth = 640
frameHeight = 480
cap.set(3, frameWidth)
cap.set(4, frameHeight)
cap.set(10, 150)

# Define HSV color ranges and corresponding BGR values for drawing
myColors = [
    [5, 107, 0, 19, 255, 255],    # Orange
    [133, 56, 0, 159, 156, 255],  # Purple
    [57, 76, 0, 100, 255, 255]    # Green
]
myColorValues = [
    [51, 153, 255],   # BGR for orange
    [255, 0, 255],    # BGR for purple
    [0, 255, 0]       # BGR for green
]

myPoints = []  # List to store detected points

def findcolor(img, myColors, myColorValues):
    imgHSV = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)
    newPoints = []
    for color, colorVal in zip(myColors, myColorValues):
        lower = np.array(color[0:3])
        upper = np.array(color[3:6])
        mask = cv2.inRange(imgHSV, lower, upper)
        x, y = getContours(mask)
        if x != 0 and y != 0:
            newPoints.append([x, y, colorVal])
        cv2.circle(imgResult, (x, y), 10, colorVal, cv2.FILLED)
    return newPoints

def getContours(img):
    contours, _ = cv2.findContours(img, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
    x, y, w, h = 0, 0, 0, 0
    maxArea = 0
    for cnt in contours:
        area = cv2.contourArea(cnt)
        if area > 500:
            peri = cv2.arcLength(cnt, True)
            approx = cv2.approxPolyDP(cnt, 0.02 * peri, True)
            x, y, w, h = cv2.boundingRect(approx)
    return x + w // 2, y

while True:
    success, img = cap.read()
    if not success:
        print("Failed to capture frame from camera")
        break

    imgResult = img.copy()
    newPoints = findcolor(img, myColors, myColorValues)
    if newPoints:
        for newPoint in newPoints:
            myPoints.append(newPoint)
    
    for point in myPoints:
        cv2.circle(imgResult, (point[0], point[1]), 10, point[2], cv2.FILLED)

    cv2.imshow("Video", imgResult)
    
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

# Release resources
cap.release()
cv2.destroyAllWindows()
