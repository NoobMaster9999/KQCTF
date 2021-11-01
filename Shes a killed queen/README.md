# KQCTF
These are the writeups for the KQCTF 2021
# Shes a killed queen

We have an image which is corrupted!

First of all, when we try to open the image, it gives error as somebody tampered with the height and width of the image!
The crc of the image is wrong too!
so we find the crc by PCRT - 
https://github.com/sherlly/PCRT
commands are - 
```bash
git clone https://github.com/sherlly/PCRT
cd /PCRT
chmod +x PCRT.py
```
Now we move the curropted image to the same folder then we do - 
```bash
./PCRT.py queen.png
```
it will give  - 


So, we find the right dimensions by bruteforcing!
The code for the same:

```py
#!/usr/bin/env python3
from progress.bar import IncrementalBar
#import subprocess
from pwn import *
from zlib import crc32
#width = 0
#height = 0
bar = IncrementalBar('Brute forcing dimesnsions', max=2000*2000)
required_crc = 0x3B8B7C12
for width in range(2000):
    for height in range(2000):
        ihdr = b"\x49\x48\x44\x52" + p32(width, endian='big') + p32(height, endian='big') + b"\x08\x06\x00\x00\x00"
        # print(ihdr.hex())
        # php = subprocess.Popen(['php', 'php-calc-crc.php', ihdr.hex()], stdout=subprocess.PIPE)
        crc = crc32(ihdr)
        # crc = php.stdout.read()
        bar.next()
        # print(int(crc), crc)
        if crc == required_crc:
            print()
            print(width, height)
bar.finish()
```


after running the code we get the height and width as - 
Width: 1200, Height 675
so when we change the dimensions from hex editor/modsize we get this image -
![queen2jpeg.jpeg](./queen2jpeg.jpeg)

now we use stegsolve on this and find a queen's cipher
![queen-cipher.jpg](./queencipher.jpg)
now we can decode it online from -
https://www.dcode.fr/mary-stuart-code

 and we get the flag!
## flag kqctf{SHES_A_KILLED_QUEEN_BY_THE_GUILLOTINE_RANDOMCHRSIADHFKILIHASDKFHQIFPXKRL}
