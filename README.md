# ImageStitching-MPI
Operating System yang digunakan adalah ubuntu server 22.04

## Topologi
![Topologi](https://github.com/feliana444/ImageStitching-MPI/assets/145323449/263a5fa1-220e-46f2-8959-34ba6c33901f)

## 1. Install MPI
```sh
sudo apt-get install -y mpich-doc mpich
```
```sh
pip install mpi4py
```
output <br>
![MPI](https://github.com/feliana444/ImageStitching-MPI/assets/145323449/0a4b646e-f73b-4060-9364-49c5f43f4f2a)
## 2. Konfigurasi
### 2.1 Konfigurasi /etc/hosts <br>
cek alamat ip komputer menggunakan perintah `ip a` `if config` `hostname -  I` <br>
**MASTER**
```sh
192.168.135.181 master
192.168.135.39 slave1
192.168.135.75 slave2
192.168.135.168 slave3
```
**SLAVE**
```sh
192.168.135.181 master
192.168.135.39 slave1
```
### 2.2 Konfigurasi SSH <br>
Lakukan pada semua slave
```sh
ssh-copy-id <nama user>@<slave>
```

## 3. Install numpy, imutils dan opencv
### 3.1 numpy
    pip install numpy
### 3.2 imutils
    pip install imutils
### 3.3 opencv
    pip install opencv-python

## 4. Input bahan projek melalui github
```sh
git clone https://github.com/kaliadi/image-Stitching.git\
```

## 5. Copy Seluruh File ke dalam Slave
### 5.1 Copy File
```sh
scp image-Stiching/* mpiuser@slave1:/home/mpiuser/
scp image-Stiching/* mpiuser@slave2:/home/mpiuser/
scp image-Stiching/* mpiuser@slave3:/home/mpiuser/
```
### 5.2 Cek Path File pada Master dan Slave <br>
**MASTER** <br>
![master](https://github.com/feliana444/ImageStitching-MPI/assets/145323449/99b8c0c3-d4fe-4737-ab2d-5d7cf5d3c67a) <br>
**SLAVE** <br>
![slave](https://github.com/feliana444/ImageStitching-MPI/assets/145323449/0e2a4f5a-fa9b-430f-ae80-e35f5889f397)

## 6. Program
```sh
# USAGE
# python image_stitching_simple.py --images images/scottsdale --output output.png
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

## 7. Jalankan Image Stitching dengan MPI dan Multinode
### 7.1 Image yang digunakan
![WhatsApp Image 2023-11-14 at 9 08 26 PM](https://github.com/feliana444/ImageStitching-MPI/assets/145323449/683ac523-28c9-4ff9-9ebf-318ba52b3577)
![WhatsApp Image 2023-11-14 at 9 08 26 PM (1)](https://github.com/feliana444/ImageStitching-MPI/assets/145323449/ec3cfff3-5c5d-4488-b932-39097f1245bb)
![WhatsApp Image 2023-11-14 at 9 08 26 PM (2)](https://github.com/feliana444/ImageStitching-MPI/assets/145323449/9b9d8ae3-9f0e-484a-99c6-c485242b49d7)
### 7.2 Waktu Running
    mpiexec -n <jumlah slave> -host master,slave1,slave2,slave3 python3 /home/mpiuser/image-Stitching/image_s.py -i /home/mpiuser/image-Stitching/images -o outputmulti.png
![image](https://github.com/feliana444/ImageStitching-MPI/assets/145323449/8f9214f2-bd1b-4822-ba67-5e0c13d328ad)
### 7.3 Melihat Hasil Output <br>
Tampilan jika output sudah tersimpan
![image](https://github.com/feliana444/ImageStitching-MPI/assets/145323449/3448576c-6cd8-45e4-80cd-17398e9847ad)
### 7.4 Output
![image](https://github.com/feliana444/ImageStitching-MPI/assets/145323449/2abddfe2-001c-493a-965d-871f6e180928)
