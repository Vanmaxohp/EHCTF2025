# Đề bài

```
import os
import random
from hashlib import md5

from Crypto.Cipher import AES
from Crypto.Util.number import long_to_bytes, getPrime

FLAG = os.getenvb(b"FLAG", b"FAKE{THIS_IS_NOT_THE_FLAG!!!!!!}")


def encrypt(m: bytes, key: int) -> bytes:
    iv = os.urandom(16)
    key = long_to_bytes(key)
    key = md5(key).digest()
    cipher = AES.new(key, AES.MODE_CBC, iv=iv)
    return iv + cipher.encrypt(m)


def f(s, p):
    u = 0
    for i in range(p):
        u += p - i
        u *= s
        u %= p

    return u 


p = getPrime(1024)
s = random.randint(1, p - 1)

A = f(s, p)
ciphertext = encrypt(FLAG, s).hex()
```

# Phân tích

 Mấu chốt của bài toán nằm ở hàm `f(s, p)`, đề bài sử dụng `s` để mã hóa, nhưng lại sử dụng hàm này để biến đổi `s` thành `A` thông qua p rồi in ra. Nhiệm vụ của ta tìm lại được `s` từ `p` và `A`

 Phân tích hàm `f(s, p)`:

   - `i` sẽ chạy lần lượt từ 0 đến p - 1:
       + i = 0: $u = 0$ (do ps chia hết cho p)
       + i = 1: $u = -s   \mod p$
       + i = 2: $u = -s^{2} - 2s   \mod p$
       + ...
       + i = p - 1: $u = -s^{p-1} - 2s^{p-2} - 3s^{p-3} - ... - (p-2)s^{2} - (p-1)s$
   - Sau khi chạy xong có:
   - 
$$A =  -s^{p-1} - 2s^{p-2} - 3s^{p-3} - ... - (p-2)s^{2} - (p-1)s \mod p$$

Hay:

$$A = - ((p-1)s + (p-2)s^{2} + (p-3)s^{3} + ... + 2s^{p-2} + s^{p-1}) \mod p$$

Sử dụng công thức tổng cấp số nhân nâng cao [này](https://brilliant.org/wiki/arithmetic-geometric-progression/), ta biến đổi A:

Áp dụng công thức Arithmetic-Geometric Progression (AGP), một tổng với các số hạng có dạng:
$$a,(a+d)r,(a+2d)r^{2},(a+3d)r^{3},…,(a+(n−1)d)r^{n−1}$$
với a là số hạng đầu, d là công sai, r là công bội.

Với A, ta áp dụng công thức AGP với $a = p$, $d = -1$, $r = s$ và số số hạng $n = p + 1$

$$A = - (\frac{p - (p + (p - 1 - 1).(-1)).s^{p+1}}{1-s} + \frac{(-1).s.(1-s^{p})}{(1-s)^{2}}) \mod p$$

$$A = - (\frac{p - (p - p).s^{p+1}}{1 - r} + \frac{-s + s^{p+1}}{(1-s)^{2}}) \mod p$$

$$A = - \frac{-s + s^{p+1}}{(1-s)^{2}} \mod p$$

$$A = \frac{s - s^{p} + s^{p} - s^{p+1}}{(1-s)^{2}} \mod p$$

$$A = \frac{s - s^{p}}{(1 - s)^{2}} + \frac{s^{p}(a-s)}{(1 - s)^{2}} \mod p$$

Sử dụng Fermat nhỏ, có $s - s^{p} = 0 \mod p$

$$A = \frac{s^{p}}{1 - s} \mod p$$

Tiếp tục dùng Fermat nhỏ, có $s^{p} = s \mod p$

$$A = \frac{s}{1 - s} \mod p$$

$$A(1 − s) = s \mod p$$

$$A − As = s \mod p$$

$$A = s + As \mod p$$

$$A = s(1 + A) \mod p$$

$$s = \frac{A}{1 + A} \mod p$$

Hay nói cách khác `s = A * inverse(A + 1, p) % p`

Từ đầy, ta có thể dễ dàng tìm được s.

# Lời giải

```
from Crypto.Cipher import AES
from Crypto.Util.Padding import unpad
from hashlib import md5
from Crypto.Util.number import *

def decrypt(ciphertext, key):
    iv = ciphertext[:16]
    key = long_to_bytes(key)
    key = md5(key).digest()
    cipher = AES.new(key, AES.MODE_CBC, iv=iv)
    decrypted = cipher.decrypt(ciphertext[16:])
    return decrypted


p = 92312188914142360474066286224339324440181806796523048114746920398390756279711999762364569924622420503415195122282411340659689200248585150351540032232268273608225348909421646387385316405303107251663054225407939758352872101164975105841853252075683980013253269682954932572824845285181513176664309336275681144241
A = 43603943407644578764026311545078176198432640692379532883054355849876121132015230469548822514186430067810230731623849702814562877397973829147426633895586067619945975553306096986308377571998549541653327666015443276943123956229964478322515350086315697249757386277103060792954710065515171578481258211723179979325
ciphertext = 'd6b681f922f2ae8f8566468442e8f2ae367447644bc646b65d885f4cafb2afdcb817575d5c0b14dbd35b77c848d469952318270317f29d28f25839491e77a58c'
s = A * inverse(A + 1, p) % p
ciphertext_bytes = bytes.fromhex(ciphertext)
decrypted_flag = decrypt(ciphertext_bytes, s)
print(f"Decrypted FLAG: {decrypted_flag}")
```

# Flag

`EHCTF{M4y_7h3_4r1thm3t1c_b3_w1th_y0u_h4ck3rm4n!}`
