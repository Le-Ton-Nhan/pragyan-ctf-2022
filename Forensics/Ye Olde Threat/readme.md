# Ye Olde Threat
## Description
> One of your spies has obtained intel about the resurgence of an old threat to the planet. He was only able to get this text file to you. Find the nature of the threat to help stop it.    

File [intel.txt](https://github.com/Butterflies4/pragyan-ctf-22/edit/main/Forensics/Ye%20Olde%20Threat/intel.txt)
## Solution
Decode base64 đoạn text trong file `intel.txt`, ta được một đoạn link: https://drive.google.com/file/d/1AEkzbjWRCdUp4F7f2wtLgId2wdSFY0Qq     
Vào link và tải file hình ảnh về, tuy nhiên không mở được file. Sử dụng `HxD` để kiểm tra các bit của file, thì phát hiện có rất nhiều bit sai ở đây      
![image](https://user-images.githubusercontent.com/62021009/157281195-00fd0391-eb34-4b8f-8e25-2a67304a6251.png)         
Tiến hành sửa file như sau:
- Đầu tiên sửa 8 byte header của png: 89 49 6C 47 53 6D 1A 0A-> 89 50 4E 47 0D 0A 1A 0A
- Chunk type HIDR từ byte 13-16: 31 48 44 72 -> 49 48 44 52
- Chunk type IDAT từ byte 0x26-0x29: 31 64 61 37 -> 49 44 41 54

Sử dụng `pngcheck -v` để kiểm tra xem file còn lỗi gì không        
![image](https://user-images.githubusercontent.com/62021009/157286001-d6875406-f04f-4a21-9053-f266a621cfb3.png)
![image](https://user-images.githubusercontent.com/62021009/157283830-292092a9-185c-43f3-9c9b-4df53f78ccab.png)            
Ta nhận được "EOF while reading IDAT data". Như vậy ta cần chỉnh lại length của chunk IDAT cho phù hợp. Cấu trúc của một chunk là như sau:         
![image](https://user-images.githubusercontent.com/62021009/157284368-b7617d01-a835-4302-801e-789fa76884e7.png)             
Địa chỉ của các chunk:              
```
┌──(kali㉿kali)-[~/Documents/pragyan2022/Ye Olde Threat/intel]
└─$ binwalk -R "IDAT" Just_A_Plane_Image.png

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
37            0x25            Raw signature (IDAT)
8241          0x2031          Raw signature (IDAT)
16445         0x403D          Raw signature (IDAT)
24649         0x6049          Raw signature (IDAT)
32853         0x8055          Raw signature (IDAT)
41057         0xA061          Raw signature (IDAT)
49261         0xC06D          Raw signature (IDAT)
```

Địa chỉ ở đây sẽ bắt đầu từ Chunk type, cái ta cần tính chính là độ dài của chunk data. Như vậy độ dài của chunk IDAT thứ hai chính là: `0x403D - 8 - 0x2035 = 0x2000`     
Như vậy ta sẽ sửa length nằm ở byte 0x202E - 0x2031 thành 00 00 20 00     
Sau khi sửa lại, dùng `pngcheck` để kiểm tra       
```
┌──(kali㉿kali)-[~/Documents/pragyan2022/Ye Olde Threat/intel]
└─$ pngcheck -v Just_A_Plane_Image.png
File: Just_A_Plane_Image.png (49358 bytes)
  chunk IHDR at offset 0x0000c, length 13
    348 x 348 image, 32-bit RGB+alpha, non-interlaced
  chunk IDAT at offset 0x00025, length 8192
    zlib: deflated, 32K window, default compression
  chunk IDAT at offset 0x02031, length 8192
  chunk IDAT at offset 0x0403d, length 8192
  CRC error in chunk IDAT (computed a89b381a, expected f64839a4)
ERRORS DETECTED in Just_A_Plane_Image.png
```
Chương trình báo lỗi checksum không đúng, và chương trình đã tính cho ta. Ta chỉ cần chỉnh sửa giá trị f64839a4 thành a89b381a.
```
┌──(kali㉿kali)-[~/Documents/pragyan2022/Ye Olde Threat/intel]
└─$ pngcheck -v Just_A_Plane_Image.png
File: Just_A_Plane_Image.png (49358 bytes)
  chunk IHDR at offset 0x0000c, length 13
    348 x 348 image, 32-bit RGB+alpha, non-interlaced
  chunk IDAT at offset 0x00025, length 8192
    zlib: deflated, 32K window, default compression
  chunk IDAT at offset 0x02031, length 8192
  chunk IDAT at offset 0x0403d, length 8192
  chunk IDAT at offset 0x06049, length 8192
  chunk IDAT at offset 0x08055, length 8192
  chunk IDAT at offset 0x0a061, length 8192
  chunk IDAT at offset 0x0c06d, length 77
  chunk IEND at offset 0x0c0c6, length 0
No errors detected in Just_A_Plane_Image.png (9 chunks, 89.8% compression).
```
Và như vậy, chúng ta đã có thể mở được file png này.
Sử dụng `stegsolve` ta được flag :>    
![image](https://user-images.githubusercontent.com/62021009/157287495-09070f8f-da5d-4d41-a1be-e75f4e236aa0.png)
### Flag
`p_ctf{K@nye_w@nt5_To_Buy_Th3_3n71r3_E4rth}`
## Reference
https://0x90p0wned.wordpress.com/category/capturetheflag/          
[Not able to read IHDR chunk of a PNG file](https://stackoverflow.com/questions/54845745/not-able-to-read-ihdr-chunk-of-a-png-file)            
[GCK'S FILE SIGNATURES TABLE](https://www.garykessler.net/library/file_sigs.html)
