import cv2
import numpy as np
import sys
import matplotlib.pyplot as plt

img = cv2.imread(str(sys.argv[1]))

shp = img[:,:,0].shape
print(shp)

print(img[0][0][0]) #Red
#print(img[0][0][1]) #Green
#print(img[0][0][2]) #Blue


imgt = cv2.equalizeHist(img[:,:,0])
plt.imshow(imgt, 'gray')
plt.show()



median = cv2.medianBlur(imgt, 5)



kernel = np.ones((11,11),np.uint8) 

dilated = cv2.dilate(im, kernel)
eroded = cv2.erode(img,kernel,iterations = 1)


##thresholding??
threshold = 50
ret, thresh_img = cv2.threshold(median, threshold,255 ,cv2.THRESH_BINARY)



th2 = cv2.adaptiveThreshold(median,255,cv2.ADAPTIVE_THRESH_MEAN_C,cv2.THRESH_BINARY,11,2)
th3 = cv2.adaptiveThreshold(median,255,cv2.ADAPTIVE_THRESH_GAUSSIAN_C,cv2.THRESH_BINARY,11,2)

'''
for i in range(0,255, 20):
        print(i)
        threshold = i
        ret, thresh_img = cv2.threshold(median, threshold,255 ,cv2.THRESH_BINARY)
        plt.imshow(thresh_img, 'gray')
        plt.show()
'''

#NOTICE THAT PUPIL IS ALWAYS RIGHT Centre OF IMAGE
'''
o_img = thresh_img
#draw line across image centre
cv2.line(o_img, (0, 0), (shp[1], shp[0]), (100,100,100), 3) #image, centre x/y, radius, color,  
cv2.line(o_img, (0, shp[0]), (shp[1], 0), (100,110,100), 3) #image, centre x/y, radius, color,  
plt.imshow(o_img, 'gray')
plt.show()
'''

####Create a whitemask for the left side of the image
mask_array = np.zeros((shp[0], shp[1]), np.uint8)
mask_array[:, shp[1]/2:] +=255
plt.imshow(mask_array, 'gray')
plt.show()

masked_img = cv2.multiply(thresh_img, mask_array)
masked_img[:, :shp[1]/2] +=255
plt.imshow(masked_img)
plt.show()


masked_img2 = cv2.bitwise_and(thresh_img, thresh_img, mask=mask_array)
masked_hist = cv2.calcHist([masked_img2], [0], None, [256], [0,256])
plt.plot(masked_hist)

plt.show()
plt.imshow(masked_img2)
plt.show()
'''

no_of_black_pixels = hist[0]
print('Number of Black pixels: '+str(no_of_black_pixels))
print('Number of White pixels: '+str(hist[-1]))


res = 10

ac = []
for i in range(0, shp[1], res):
	s = sum(masked_img[:,i])
	ac.append(s)


dw = []
for i in range(0, shp[0], res):
	s = sum(masked_img[i,:])
        dw.append(s)
        #print(str(i)+": "+str(s))

plt.plot(range(len(ac)), ac, 'k.')
plt.plot(range(len(dw)), dw, 'r.')

plt.plot(ac.index(min(ac)), min(ac), 'ko')
plt.plot(dw.index(min(dw)), min(dw), 'ro')
'''

acf = np.polyfit(range(len(ac)),ac, 2)
dwf = np.polyfit(range(len(dw)),dw, 2)
f1 = np.poly1d(acf)
f2 = np.poly1d(dwf)

plt.plot(range(len(ac)), f1(range(len(ac))), 'k-')
plt.plot(range(len(dw)), f2(range(len(dw))), 'r-')
'''

plt.show()


rd = np.sqrt(no_of_black_pixels/np.pi)


cv2.circle(masked_img, (ac.index(min(ac))*res, dw.index(min(dw))*res), rd, (100,100,100), 3) #image, centre x/y, radius, color, ?? 


plt.imshow(masked_img, 'gray')
plt.show()


cv2.destroyAllWindows()

