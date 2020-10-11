---
title: CSAW Red 2020 Writeups - Alert
date: 2020-09-27
categories:
- CTF Writeups
---

An image steganography challenge I wrote for CSAW Red 2020  

The picture probably needs to be squared away...  
Point: 150  
![image](/assets/images/posts/2020/alert.png)

The goal of this challenge is to display the full image and find the hidden flag. The "square" in the description is a hint that the original picture is square.     

\* A brief [explanation](https://stackoverflow.com/a/30551737) of PNG file structure.  

Solution:  
1. Use a hex editor to modify the image's height. In the IHDR chunk, we can see that the current height of the image is "01 18" and the width is "02 58". Since this image is square, we need to change the height to "02 58".   
2. Run "pngcheck -vf alert.png".   
3. There is an error message: "CRC error in chunk IHDR (computed be6698dc, expected 80d6882d)".   
4. Open the hex editor again. In the IHDR chunk, find the number "80d6882d" and change it to "be6698dc".   
5. Open the PNG and find the flag.   