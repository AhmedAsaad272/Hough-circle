import numpy as np
import cv2 
import sys

def ontrackbar(max_slider1):
    global Img
    global dst
    global gray    
    #copy original image to use it in further commands,==
    #==cause the there will be changes on the original one due to drawing circles on it
    dst =np.copy(Img)
    
    #give the parameters of Canny and Hough circle values fron the trackers
    th1=max_slider1
    th2=cv2.getTrackbarPos('threshold2','Result Image')
    r1=cv2.getTrackbarPos('MinRadius','Result Image')
    r2=cv2.getTrackbarPos('MaxRadius','Result Image')
   

    #get the edge map for the image 
    edges= cv2.Canny(Img,th1,th2)
    
    #apply Hough Circle Transform
    circles = cv2.HoughCircles(edges, cv2.HOUGH_GRADIENT, 1, 100, param1=300, param2=30,minRadius=r1, maxRadius=r2)
    circles = np.uint16(np.around(circles))
    #Draw the detected circles:
    for i in circles[0, :]:
        center = (i[0], i[1])
       # circle center
        cv2.circle(dst, center, 1, (0, 100, 100), 3)
        # circle outline
        radius = i[2]
        cv2.circle(dst, center, radius, (255, 0, 255), 3)
        
    #show the result of edges and original image with detected circles on it    
    cv2.imshow("Result Image",dst)
    cv2.imshow("Edges",edges)
    
    #The main code
if __name__=="__main__":
    #read an image
    Img=cv2.imread("BY.jpg")
    
    #creaate two windows to place images and trackbars in  
    cv2.namedWindow("Edges")
    cv2.namedWindow("Result Image")   
    
    #creeate four trackbars and assign the current value and the range for each
    inith=0
    maxth=800
    rmin=0
    rmac=800
    r1=0
    mr1=5
    r2=0
    mr2=100
    cv2.createTrackbar("threshold1","Result Image",inith,maxth,ontrackbar)
    cv2.createTrackbar("threshold2","Result Image",rmin,rmac,ontrackbar)
    cv2.createTrackbar("MinRadius","Result Image",r1,mr1,ontrackbar)
    cv2.createTrackbar("MaxRadius","Result Image",r2,mr2,ontrackbar)
    
    #call the fuction that will perform Hough circle transform and show the circles on the main img
    ontrackbar(inith)
    
    #quit the windows if pressing the Exc button
    while True:
        if cv2.waitKey(1)==27:
            break
    cv2.destroyAllWindows()
