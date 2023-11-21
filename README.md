# Image Stitching
## Topologi
![Topologi](https://github.com/RafiRiz74/ImageStitching/blob/main/Topologi.png)

## 1. Install MPI dan Python
```sh
sudo apt install python3-pip
```
```sh
pip install mpi4py
```
## 2. Konfigurasi
### 2.1 Konfigurasi hosts <br>
Dapat dicek alamat ip dengan menggunakan perintah `ip a` `if config` `hostname -  I` <br>
**MASTER**
```sh
192.168.41.190 master
192.168.41.31 slave1
192.168.41.180 slave2
```
**SLAVE1**
```sh
192.168.41.190 master
192.168.41.31 slave1
```
**SLAVE2**
```sh
192.168.41.190 master
192.168.41.180 slave2
```
### 2.2 Konfigurasi SSH <br>
Ini dapat dilakukan diberbagai hosts, seperti master dapat melakukannya pada slave1 dan slave2, sedangkan slave1 dan slave2 dapat mengkonfigurasi  master. Ini dilakukan agar dapat mengetahui apakah ssh multinode telah berjalan dengan baik atau belum
```sh
ssh <nama user>@<hosts>
```

## 3. Install module yang diperlukan
### 3.1 numpy
    pip install numpy
### 3.2 imutils
    pip install imutils
### 3.3 opencv
    pip install opencv-python

## 4. Copy file pada slave
```sh
scp kelompokpp/* mpiuser@slave1:/home/uaskelpp/
scp kelompokpp/* mpiuser@slave2:/home/uaskelpp/
```

## 5. Proses penjalanan program
### 5.1 Program
```sh
# USAGE
# python image_stitching_simple.py --images images/scottsdale --output output.p>
# import the necessary packages
from imutils import paths
import numpy as np
import argparse
import imutils
import cv2

# construct the argument parser and parse the arguments
ap = argparse.ArgumentParser()
ap.add_argument("-i", "--images", type=str, required=True,
        help="path to input directory of images to stitch")
ap.add_argument("-o", "--output", type=str, required=True,
        help="path to the output image")
args = vars(ap.parse_args())

# grab the paths to the input images and initialize our images list
print("[INFO] loading images...")
imagePaths = sorted(list(paths.list_images(args["images"])))
images = []

# loop over the image paths, load each one, and add them to our
# images to stich list
for imagePath in imagePaths:
        image = cv2.imread(imagePath)
        images.append(image)

# initialize OpenCV's image sticher object and then perform the image
# stitching
print("[INFO] stitching images...")
stitcher = cv2.createStitcher() if imutils.is_cv3() else cv2.Stitcher_create()
(status, stitched) = stitcher.stitch(images)

# if the status is '0', then OpenCV successfully performed image
# stitching
if status == 0:
        # write the output stitched image to disk
        cv2.imwrite(args["output"], stitched)
```

### 5.2 Gambar yang digunakan
![Foto1](https://github.com/RafiRiz74/ImageStitching/blob/main/Foto1.jpg)
![Foto2](https://github.com/RafiRiz74/ImageStitching/blob/main/Foto2.jpg)
![Foto3](https://github.com/RafiRiz74/ImageStitching/blob/main/Foto3.jpg)
### 5.3 Running mpi4python
    mpiexec -n 3 -host master,slave1,slave2 python3 /home/uaskelpp/kelompokpp/ImageStitching.py -i /home/uaskelpp/kelompokpp/images -o OutputImageStitching.png
![Running](https://github.com/RafiRiz74/ImageStitching/blob/main/Running.png)
### 5.4 Output
![OutputImageStitching](https://github.com/RafiRiz74/ImageStitching/blob/main/OutputImageStitching.jpg)
