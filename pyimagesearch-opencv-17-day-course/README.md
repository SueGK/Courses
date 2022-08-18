The course is from PyimageSearch.

[FREE OpenCV, Computer Vision, Deep Learning, and Python Crash Course | PyImageSearch](https://pyimagesearch.com/free-opencv-computer-vision-deep-learning-crash-course/)

There are my notes during studying. [OpenCV-Notes](https://github.com/SueGK/Courses/blob/main/pyimagesearch-opencv-17-day-course/OpenCV-Mynotes)

# Day1: Face detection with OpenCV and deep learning
[Face detection with OpenCV and deep learning - PyImageSearch](https://pyimagesearch.com/2018/02/26/face-detection-with-opencv-and-deep-learning/)

Run the model in terminal
```python
# run the image face detect python file
$ python detect_faces.py --image rooster.jpg --prototxt deploy.prototxt.txt \
	--model res10_300x300_ssd_iter_140000.caffemodel
$ python detect_faces.py --image iron_chic.jpg --prototxt deploy.prototxt.txt \
	--model res10_300x300_ssd_iter_140000.caffemodel
# quit the image window by typing any key

# run the video face detect python file
$ python detect_faces_video.py --prototxt deploy.prototxt.txt --model res10_300x300_ssd_iter_140000.caffemodel
# quit the video frame by typing **q**
```

https://user-images.githubusercontent.com/71711489/185243914-d6c6d441-d68f-4f41-a62e-39f5404df601.mp4

Useful links during learning:
- [Deep learning: How OpenCV's blobFromImage works - PyImageSearch](https://pyimagesearch.com/2017/11/06/deep-learning-opencvs-blobfromimage-works/)
- [os.path — Common pathname manipulations — Python 3.10.6 documentation](https://docs.python.org/3/library/os.path.html)
- [numpy.set_printoptions](https://numpy.org/doc/stable/reference/generated/numpy.set_printoptions.html)
- [OpenCV: Drawing Functions](https://docs.opencv.org/3.4/d6/d6e/group__imgproc__draw.html#ga07d2f74cadcf8e305e810ce8eed13bc9)
- [OpenCV: Flags used for image file reading and writing](https://docs.opencv.org/3.4/d8/d6a/group__imgcodecs__flags.html#ga61d9b0126a3e57d9277ac48327799c80)
- [My Notes on how OpenCV read images in BGR order](https://github.com/SueGK/Courses/blob/main/pyimagesearch-opencv-17-day-course/OpenCV-Mynotes/opencv_BGR_color.ipynb)

```python
	net.setInput(blob)
	detections = net.forward()
	# This is what forward() returns.
		# conf = inference_results[0, 0, i, 2] # extract the confidence (i.e., probability)
		# idx = int(inference_results[0, 0, i, 1]) # extract the index of the class label
		# boxPoints = inference_results[0, 0, i, 3:7]
```  
