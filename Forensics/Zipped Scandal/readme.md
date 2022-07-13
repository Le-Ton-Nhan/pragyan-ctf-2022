# Zipped Scandal
## Description
> With the disappearance of the spider menace, John Jonah Jameson needs an alternative story to report on. One of his informants in the Midwest claims to have uncovered a scandal worth reporting on. Help JJJ reveal the hidden headline.               
> P.S. The entire flag is in lowercase and is of the format p_ctf{...}

File [Aa.zip](https://github.com/Butterflies4/pragyan-ctf-22/edit/main/Forensics/Zipped%20Scandal/Aa.zip)
## Solution
Chúng ta nhận được 1 file zip, và file zip này được nén bởi rất, rất nhiều lần. Như vậy, để có thể giải nén được, mình sử dụng code sau
```bash
#!/bin/bash
while test -f *.zip
do
for file in *.zip
do
unzip "$file"
rm "$file"
done
done
```
Thực thi đoạn code, ta được       
![image](https://user-images.githubusercontent.com/62021009/157289979-7f1bc7ea-e8e0-49d2-b878-b920d912a5a4.png)          
File `Checkpoint.zip` cho ta 1 file `Image.jpeg` và `Final.zip` - được bảo vệ bằng mật khẩu. Như vậy, có vẻ hình ảnh này liên quan đến mật khẩu của file Final                 
![image](https://user-images.githubusercontent.com/62021009/157290505-1234f2e4-ffba-4dd5-92d5-c443119b76d4.png)         
Ta tiến hành `exiftool` file image thì ta được
```
┌──(kali㉿kali)-[~/Documents/pragyan2022/Zipped Scandal/wu]
└─$ exiftool Image.jpeg 
ExifTool Version Number         : 12.36
File Name                       : Image.jpeg
Directory                       : .
File Size                       : 8.9 KiB
File Modification Date/Time     : 2022:01:07 14:18:58+01:00
File Access Date/Time           : 2022:03:08 18:15:00+01:00
File Inode Change Date/Time     : 2022:03:08 18:15:00+01:00
File Permissions                : -rw-rw-r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Exif Byte Order                 : Big-endian (Motorola, MM)
X Resolution                    : 1
Y Resolution                    : 1
Resolution Unit                 : None
Y Cb Cr Positioning             : Centered
GPS Version ID                  : 2.3.0.0
GPS Latitude Ref                : North
GPS Longitude Ref               : West
Comment                         : b3abe5d8c69b38733ad57ea75e83bcae42bbbbac75e3a5445862ed2f8a2cd677
Image Width                     : 312
Image Height                    : 161
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 312x161
Megapixels                      : 0.050
GPS Latitude                    : 36 deg 20' 21.49" N
GPS Longitude                   : 96 deg 48' 10.32" W
GPS Position                    : 36 deg 20' 21.49" N, 96 deg 48' 10.32" W
```
Comment có là 1 đoạn SHA256, và ngoài ra ta còn nhận được GPS. Nội dung của bức hình là Where Am I, nên ta ngay lập tức nghĩ đến tìm địa điểm dựa trên toạ độ được cung cấp này.    
Sử dụng [Google Map](https://www.google.com/maps/) để tìm toạ độ, ta tìm được địa điểm [Meurer Abstract & Title Co](https://www.google.com/maps/place/Meurer+Abstract+%26+Title+Co/@36.3393073,-96.8029828,20.74z/data=!4m5!3m4!1s0x0:0xc53eb1cf3defc418!8m2!3d36.3392946!4d-96.8029745?hl=en).
Lấy địa điểm này nhập vào password để giải nén, tuy nhiên không đúng. Nhớ lại đoạn SHA256, tiến hành encode SHA256 nhưng vẫn không đúng. Sau đó, mình đã liên hệ admin, và xác định được địa điểm mà tác giả muốn đó là địa điểm ngay bên cạnh `Pawnee City Hall`
Mã hoá SHA256 địa điểm này, ta giải nén được file `Final.zip` được thêm 1 file `image.png`      
Tưởng rằng chỉ cần mở file hình ảnh này là sẽ có được flag, nhưng đã nhầm, đây là một hình đen thui. Sử dụng `stegsolve` thì ta được một mã QR          
![image](https://user-images.githubusercontent.com/62021009/157292295-8ba7d839-2750-4db1-af5b-bf4d5ebf766a.png)            
Quét QR ta được link drive: https://drive.google.com/file/d/1DM1HHXJ0gWg-r1Zm3VfGAryCHNhR_Pwt, ta được 1 file mp3          
Nghe qua thì có vẻ đây là mã morse, mình sử dụng [trang này](https://morsecode.world/international/decoder/audio-decoder-adaptive.html) để tiến hành decode, nhưng lại bắt gặp một số kí tự không giải mã được.
Sau một hồi loay hoay thì mình reverse đoạn audio lại, sau đó decode morse và tada, ta được flag.
### Flag
`p_ctf{ic3t0wnc0stsic3cl0wnhist0wncr0wn}`


