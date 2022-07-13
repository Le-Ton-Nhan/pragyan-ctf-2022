# Marcel and the iPad
## Description
> After Ross sends marcel away, marcel is angry with his new master Steve. One day he steals Steve's one and only ONE iPAD, clicks a photo and hides it somewhere. Being a smart capuchin, he hides a hint in the image he clicks, can you help Steve find the hint.                   
> Flag format - p_ctf{hint}

File [marcel.png](https://github.com/Butterflies4/pragyan-ctf-22/blob/main/Forensics/Marcel%20and%20the%20iPad/marcel.png)
## Solution
Sử dụng `exiftool` ta được
```
┌──(kali㉿kali)-[~/Documents/pragyan2022/Marcel and the iPad]
└─$ exiftool marcel.png 
ExifTool Version Number         : 12.36
File Name                       : marcel.png
Directory                       : .
File Size                       : 4.2 MiB
File Modification Date/Time     : 2022:03:06 09:31:09+01:00
File Access Date/Time           : 2022:03:06 09:31:09+01:00
File Inode Change Date/Time     : 2022:03:06 09:31:09+01:00
File Permissions                : -rwxrw-rw-
File Type                       : PNG
File Type Extension             : png
MIME Type                       : image/png
Image Width                     : 2048
Image Height                    : 2048
Bit Depth                       : 8
Color Type                      : RGB with Alpha
Compression                     : Deflate/Inflate
Filter                          : Adaptive
Interlace                       : Noninterlaced
SRGB Rendering                  : Perceptual
Significant Bits                : 8 8 8 8
Comment                         : Steve_loves_apple
Image Size                      : 2048x2048
Megapixels                      : 4.2
```
Ta thấy "Comment: Steve_loves_apple".         
Sử dụng `stegsolve` và ta nhận được `1h@ww_e0op$_0b_t0y`            
![image](https://user-images.githubusercontent.com/62021009/157008311-ba1b3884-73d7-42fd-8be0-3370b022f89a.png)        
Thử nộp flag nhưng không đúng.      
Ngoài 2 thông tin này ra thì sau khi thử binwalk, zsteg,... thì vẫn không tìm được thông tin gì khác       
Sau khi đọc wu, thì mình mới biết đây là kiểu mã hoá [One Time Pad](http://rumkin.com/tools/cipher/otp.php)       
![image](https://user-images.githubusercontent.com/62021009/157008846-17a0f1ca-955c-4b95-b392-866735b40c17.png)
Và đây cũng chính là flag
### Flag
`p_ctf{1p@ds_j0ke$_0n_y0u}`
