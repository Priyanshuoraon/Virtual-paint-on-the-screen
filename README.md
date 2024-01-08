# Virtual-paint-on-the-screen
#This the image processing project which is made by using openCV library of python and image processsing concepts

import cv2
import numpy as np
#frameWidth = 640
#frameHeight = 480
cap = cv2.VideoCapture(0)
cap.set(3, 640)
cap.set(4, 480)
cap.set(10, 150)

myColors =[[5,107,0,19,255,255],
           [133,56,0,159,156,255],
           [57,76,0,100,255,255]
           ]
myColorValues = [[51,153,255],
                 [255,0,255],
                 [0,255,0]]

myPoint = []

def findcolor(img,myColors,myColorValues):
    imgHSV = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)
    count =0
    newPoint=[]
    for color in myColors:
        lower = np.array(color[0:3])
        upper = np.array(color[3:6])
        mask = cv2.inRange(imgHSV, lower, upper)
        x,y=getContours(mask)
        cv2.circle(imgResult,(x,y),10,myColorValues[count],cv2.FILLED)
        if x!=0 and y!= 0:
            newPoint.append([x,y,count])
        count += 1
       
    return newPoint

def getContours (img):
    contours,hierarchy = cv2.findContours(img,cv2.RETR_EXTERNAL,cv2.CHAIN_APPROX_NONE)
    x,y,w,h =0,0,0,0
    for cnt in contours:
        area = cv2.contourArea(cnt)
        if area > 500:
           
            peri = cv2.arcLength(cnt,True)
            approx =cv2.approxPolyDP(cnt,0.02*peri,True)
            x,y,w,h =cv2.boundingRect(approx)
    return x+w//2,y

def drawOnCanvas(myPoints,myColorValues):
    for point in myPoints:
        cv2.circle(imgResult,(point[0],point[1]),10,myColorValues[point[2]],cv2.FILLED)


while True:
    success, img = cap.read()
    imgResult = img.copy()
    newPoint = findcolor(img,myColors,myColorValues)
    if len(newPoint)!=0:
        for newpoin in newPoint:
            myPoint.append(newpoin)
        if len(myPoint)!=0:
            drawOnCanvas(myPoint,myColorValues)
    cv2.imshow("video", imgResult)
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

