---
title: 'Shamir’s Secret Sharing Scheme'
date: 2025-06-18
categories: [ctf]
tags: [Crypto]     
published: true
description: "Note một số thứ về SSSS"
---

## Tình huống đặt ra
![image](https://github.com/user-attachments/assets/d547f8c9-9e55-44c0-a1dc-dfbaa3e05504)

Trên thực tế đôi khi xuất hiện bài toán chia sẻ bí mật cho một số người, với điều kiện là mỗi một người trong số đó chỉ có một phần bí mật (khóa mật). 

Và khóa mật được khôi phục khi liên kết tất cả hoặc một số lượng vừa đủ lớn các thành viên có phần bí mật. Thế nhưng bài toán này đặt ra tình huống trên thực tế, một số người trong số đó vì lí do sức khỏe hay đi công tác, số người còn lại vẫn có thể khôi phục được khóa mật để đảm nảo công việc.

Cho nên bài toán ở đây là có khả năng khôi phục được khóa mật trong trường hợp không tham gia của tất cả thành viên, nhưng chỉ khôi phục được khóa mật khi và chỉ khi số lượng thành viên tham gia khôi phục phải lớn hơn hoặc bằng ngưỡng nào đó.

Trong trường hợp tổng quát bài toán phân chia bí mật được hình thành như sau: 

- Cần phân chia bí mật $\displaystyle S$ cho $\displaystyle n$ người lưu giữ $\displaystyle ( P_{1} ,P_{2} ,...,P_{n})$, mỗi người $\displaystyle P_{i}$ giữ một phần thông tin $\displaystyle x_{i}$ có quan hệ với $\displaystyle S$, sao cho bất kì $\displaystyle t( t\leqslant n)$ trong số $\displaystyle n$ người có thể khôi phục được bí mật $\displaystyle S$ nếu như $\displaystyle t\geqslant k$, ở đây $\displaystyle k$ gọi là ngưỡng (threshold). Thế nhưng số lượng thành viên liên kết nhỏ hơn $\displaystyle k$ thì không thể khôi phục được cho dù giữa họ có một tài nguyên lớn để tính toán. 

--> Vào năm 1979, Shamir đã cho ra mắt lược đồ Shamir’s Secret Sharing Scheme (SSSS) dựa vào ý tưởng trên. 

## Công thức nội suy Lagrange

Trước tiên ta xem xét trong trường hợp tổng quát là các đa thức trên trường số thực $\displaystyle p( x) \in \mathbb{R}[ x]$. Giả sử ta biết được $\displaystyle p( x) =2x^{2} -3x+3$ và yêu cầu tính $\displaystyle p( 1) ,p( 2) ,p( 3)$. Lúc này ta được các giá trị tương ứng 

$$\begin{equation*}
p( 1) =2,\ p( 2) =5,\ p( 3) =12
\end{equation*}$$


Nhưng đặt vấn đề ngược lại, nếu ta biết được các giá trị $\displaystyle p( 1) ,p( 2) ,p( 3)$ thì liệu có thể tìm lại được đa thức $\displaystyle p( x)$ ban đầu hay không?



Để khôi phục lại đa thức $\displaystyle p( x)$ ban đầu thì đầu tiên ta cần xác định bậc của $\displaystyle p( x)$. Ta dễ thấy nếu như bậc của $\displaystyle p( x) \geqslant 3$ thì đa thức $\displaystyle p( x)$ không là đa thức duy nhất thỏa mãn điều kiện trên. Có thể xây dựng một họ các đa thức thỏa mãn điều kiện trên như sau: 

$$\begin{equation*}
p( x) =( x-3)( x-2) -5( x-1)( x-3) +6( x-2)( x-1) +( x-1)( x-2)( x-3) g( x)
\end{equation*}$$


trong đó $\displaystyle g( x)$ là đa thức bất kì.

Còn trong trường hợp $\displaystyle \deg p( x) =2$ thì ta xác định được duy nhất 1 đa thức thỏa mãn. Thật vậy, giả sử tồn tại một đa thức $\displaystyle q( x) \neq p( x)$ có $\displaystyle \deg( q) =2$ và $\displaystyle q( 1) =p( 1) ,p( 2) =q( 2) ,p( 3) =q( 3)$.



Đặt $\displaystyle h( x) =q( x) -p( x)$ thì $\displaystyle h( x)$ có 3 nghiệm phân biệt là $\displaystyle 1,2,3$ cho nên $\displaystyle \deg h( x) \geqslant 3$ trong khi đó $\displaystyle \deg( q( x) -p( x)) \leqslant \max\[\deg p,\deg q\] =2$ cho nên ta có điều mâu thuẫn 

Như vậy, ta phát biểu bài toán nội suy tổng quát như sau:

Cho $\displaystyle x_{1} ,x_{2} ,...,x_{n+1}$ là $\displaystyle n+1$ số thực phân biệt và $\displaystyle y_{1} ,y_{2} ,...,y_{n+1}$ là $\displaystyle n+1$ số thực bất kì. Khi đó ta sẽ tìm được duy nhất một đa thức hệ số thực $\displaystyle P( x)$ có bậc không vượt quá $\displaystyle n$ thỏa mãn:

$$\begin{equation*}
P( x_{i}) =y_{i} ,\forall i=1,..,n+1
\end{equation*}$$


Trong đó $\displaystyle P( x)$ sẽ được xác định bởi công thức sau đây, được gọi là công thức nội suy Lagrange: 

$$\begin{equation*}
P( x) =\sum_{i=1}^{n+1} P( x_{i})\prod_{ \begin{array}{l}
j\neq i\\
1\leqslant j\leqslant n+1
\end{array}}\frac{x-x_{j}}{x_{i} -x_{j}}
\end{equation*}$$

**Hệ quả**: Cho đa thức $\displaystyle P( x)$ nhận giá trị hữu tỉ tại $\displaystyle \deg P+1$ điểm thì $\displaystyle P( x) \in \mathbb{Q}[ x]$.


Chứng minh tính duy nhất: Giả sử ta có $\displaystyle n+1$ cặp giá trị $\displaystyle ( x_{1} ,y_{1}) ,...,( x_{n+1} ,y_{n+1})$. Do $\displaystyle \deg P\leqslant n$ nên ta có thể viết $\displaystyle P$ dưới dạng 

$$\begin{equation*}
P( x) =a_{0} +a_{1} x+...+a_{n} x^{n}
\end{equation*}$$

Xét ma trận sau

$$\begin{equation*}
\begin{pmatrix}
y_{1}\\
y_{2}\\
\vdots \\
y_{n+1}
\end{pmatrix} =\begin{bmatrix}
1 & x_{1} & x_{1}^{2} & \dotsc  & x_{1}^{n}\\
1 & x_{2} & x_{2}^{2} & \dotsc  & x_{2}^{n}\\
\vdots  & \vdots  & \vdots  & \ddots  & \vdots \\
1 & x_{n+1} & x_{n+1}^{2} & \dotsc  & x_{n+1}^{n}
\end{bmatrix}\begin{pmatrix}
a_{0}\\
a_{1}\\
\vdots \\
a_{n}
\end{pmatrix}
\end{equation*}$$


Phương trình tuyến tính $\displaystyle Y=Xa$ có nghiệm duy nhất khi và chỉ khi ma trận $\displaystyle X$ khả nghịch và nghiệm lúc này sẽ là $\displaystyle a=X^{-1} Y$. Ma trận $\displaystyle X$ chính là ma trận Vandermonde cho nên định thức của nó lúc này sẽ là 

$$\begin{equation*}
\det( X) =\prod_{x_{i} \neq x_{j}}( x_{i} -x_{j})
\end{equation*}$$

nhưng từ điều kiện $\displaystyle x_{i} ,x_{j}$ đôi một phân biệt nên ta có $\displaystyle \det( X) \neq 0$ và có điều phải chứng minh. 

## Ứng dụng vào lược đồ

Lược đồ như sau:



Chọn $\displaystyle n$ phần tử phân biệt $\displaystyle x_{1} ,x_{2} ,...,x_{n}$ trong $\displaystyle Z_{p}$ và $\displaystyle x_{i} \neq 0$ với mọi $\displaystyle i$. Mỗi thành viên $\displaystyle P_{i}$ sẽ có một giá trị $\displaystyle x_{i}$. 



Giả sử bây giờ ta muốn trao khóa bí mật $\displaystyle S\in Z_{p}$ cho các thành viên. Ta sẽ chọn ngẫu nhiên và độc lập $\displaystyle k-1$ phần tử $\displaystyle a_{1} ,a_{2} ,...,a_{k-1}$ thuộc $\displaystyle Z_{p}$. Các giá trị này được giữ bí mật. Sau đó ta sẽ thực hiện tính toán: Với $\displaystyle 1\leqslant i\leqslant n$, tính $\displaystyle y_{i} =a( x_{i})$ trong đó 

$$\begin{equation*}
a( x) =S+\sum_{j=1}^{k-1} a_{j} x^{j}(\bmod p) ,k\leqslant n
\end{equation*}$$

Ta phân phối các giá trị $\displaystyle y_{i}$ cho $\displaystyle P_{i}$ với $\displaystyle 1\leqslant i\leqslant n$. Số nguyên tố $\displaystyle p$ được chọn đảm bảo $\displaystyle p >\max\{S,n\}$.



Như vậy để tính lại $\displaystyle a( 0)$ ta sẽ dùng công thức nội suy Lagrange ở trên trong điều kiện phải có đủ $\displaystyle k$ người cùng tham gia xác thực. Lúc này

$$\begin{gather*}
S=a( 0) =\sum_{i=1}^{k} y_{i} \lambda_{i}\bmod p\\
=\sum_{i=1}^{k} y_{i}\prod_{j\neq i}\frac{-x_{j}}{x_{i} -x_{j}}\bmod p
\end{gather*}$$

Ta có thể tìm lại được $\displaystyle a( 0)$ từ các giá trị $\displaystyle y_{i} ,x_{i}$ đã biết ở trên. 

Sample:

```python
from sage.all import *
from Crypto.Util.number import *
# Lagrange trong sage
# F = GF(17)
# P = PolynomialRing(F, 'x')
# points = [(F.random_element(),F.random_element()) for _ in range(3)]
# f = P.lagrange_polynomial(points)
# print(f)

def lagrange_mod_p(x_val, y_val, p):
    assert len(x_val) == len(y_val)
    k = len(x_val)
    coeffs = [0] * k

    for i in range(k):
        xi, yi = x_val[i], y_val[i]
        li = [1]

        for j in range(k):
            if i != j:
                xj = x_val[j]
                denom = (xi - xj) % p
                denom_inv = inverse(denom, p)
                new_li = [0] * (len(li) + 1)
                for a in range(len(li)):
                    new_li[a]     = (new_li[a] + li[a] * (-xj) * denom_inv) % p
                    new_li[a + 1] = (new_li[a + 1] + li[a] * denom_inv) % p
                li = new_li

        for deg in range(len(li)):
            coeffs[deg] = (coeffs[deg] + yi * li[deg]) % p
    return coeffs
def _eval_at_0(x_val,y_val,p):
    val = 0  # f(0) = 0 
    k = len(x_val)
    assert len(x_val) == len(y_val)
    for i in range(k):
        xi, yi = x_val[i], y_val[i]
        prod = 1
        for j in range(k):
            if j != i:
                nume = (-x_val[j]) % p 
                deno = inverse(xi-x_val[j],p)
                prod *= (nume*deno)%p 
                prod %= p 
        val += yi*prod
        val %= p 
    return val

def _eval_at(poly, x, prime):
    accum = 0
    for coeff in reversed(poly):
        accum *= x
        accum += coeff
        accum %= prime
    return accum
```

Implement đầy đủ trên wiki: https://en.wikipedia.org/wiki/Shamir%27s_secret_sharing#Python_example


## Bài tập


### CryptoHack/Armory

Source code của bài:


```python
#!/usr/bin/env python3

import hashlib

FLAG = b"crypto{???????????????????????}"
PRIME = 77793805322526801978326005188088213205424384389488111175220421173086192558047


def _eval_at(poly, x, prime):
    accum = 0
    for coeff in reversed(poly):
        accum *= x
        accum += coeff
        accum %= prime
    return accum


def make_deterministic_shares(minimum, shares, secret, prime):
    if minimum > shares:
        raise ValueError("Pool secret would be irrecoverable.")

    coefs = [secret]
    for i in range(1, shares + 1):
        coef = hashlib.sha256(coefs[i-1]).digest()
        coefs.append(coef)
    coefs = [int.from_bytes(p, 'big') for p in coefs]
    poly = coefs[:minimum]

    points = []
    for i in range(1, shares + 1):
        point = _eval_at(poly, coefs[i], prime)
        points.append((coefs[i], point))

    return points


shares = make_deterministic_shares(minimum=3, shares=7, secret=FLAG, prime=PRIME)
for share in shares:
    print(share)
```

Và output:

```
(105622578433921694608307153620094961853014843078655463551374559727541051964080, 25953768581962402292961757951905849014581503184926092726593265745485300657424)
```

Phân tích soucre: Hàm `make_deterministic_shares` tạo một đa thức có bậc là `shares` và gán cho nó các hệ số bằng cách tính SHA256 của hệ số trước đó: 

```python
coef = hashlib.sha256(coefs[i-1]).digest()
coefs.append(coef)
```

Phần sau thì hơi sú một chút: Nó trả về cặp giá trị $(a_{i},f(a_{i}))$. Nhưng nhờ vậy mình có thể tạo tiếp được các hệ số từ $a_{1}$ trở đi. Sau đó từ các hệ số này ta sẽ tính lại được $a_{0}$ từ giá trị $f(a_{1})$ đã biết. 


Script:
```python
import hashlib
from sage.all import *
from Crypto.Util.number import *

PRIME = 77793805322526801978326005188088213205424384389488111175220421173086192558047
def _eval_at(poly, x, prime):
    accum = 0
    for coeff in reversed(poly):
        accum *= x
        accum += coeff
        accum %= prime
    return accum

a1 = 105622578433921694608307153620094961853014843078655463551374559727541051964080
f_a1 = 25953768581962402292961757951905849014581503184926092726593265745485300657424
a1_bytes = a1.to_bytes((a1.bit_length()+7)//8,'big')
a2_bytes = hashlib.sha256(a1_bytes).digest()
a2 = int.from_bytes(a2_bytes,'big')

a0 = (f_a1-pow(a1,2,PRIME)-a2*pow(a1,2,PRIME))% PRIME
flag = a0.to_bytes((a1.bit_length()+7)//8,'big')
print(flag)
```

### CryptoHack/ Toshi's Treasure

Bài cho một file hyper_privkey.txt

```
my_1k_wallet_privkey = "8b09cfc4696b91a1cc43372ac66ca36556a41499b495f28cc7ab193e32eadd30"
```

Tóm tắt: Bài sẽ tương tác với server sau `socket.cryptohack.org 13384`. 

![image](https://github.com/user-attachments/assets/268ec3b9-39fc-44e6-9a30-54ab044a6ed4)

Như mọi người thấy thì đây là kế hoạch của imposter. Đầu tiên gửi một fake share để thực hiện lại quá trình kết hợp shares của toàn bộ người chơi. Sau đó imposter sẽ giả mạo địa chỉ của ví bitcoin bằng cách gửi một giá trị share mà sau khi kết hợp lại thì secret sẽ là địa chỉ của ví này. Và cuối cùng là mở ví chứa 1 triệu đô, lấy tiền và hưởng thụ thôi.

Ở bài này thì ta sẽ cần tìm hiểu qua về share forgery. Mọi người có thể xem qua ở đây: https://github.com/jvdsn/crypto-attacks/blob/master/attacks/shamir_secret_sharing/share_forgery.py

SSSS trong bài này được cài đặt theo bản chuẩn ở trên wiki https://en.wikipedia.org/wiki/Shamir%27s_Secret_Sharing#Python_example với modulo là số nguyên tố $p=2^{127}-1$

Mình có đọc qua được bài này: https://crypto.stackexchange.com/questions/54578/how-to-forge-a-shamir-secret-share

Bây giờ ta đã biết được

- Share của bản thân

- Giá trị $\displaystyle x$ của mỗi người

-  Giá trị của secret ban đầu chính là $S$. 

Ta cũng biết được giá trị $\displaystyle y_{1}$ của chính bản thân mình. Secret ban đầu là $\displaystyle S$ và ta muốn giả mạo nó thành $\displaystyle S'$ bằng cách tính 

$$\begin{equation*}
y_{1} '=y_{1} +( S'-S)\prod_{j=2}^{k}\frac{x_{j} -x_{1}}{x_{j}}
\end{equation*}$$

Như vậy secret sau khi kết hợp lại sẽ được tính bởi 

$$\begin{gather*}
S=\sum_{i=1}^{k} y_{i}\prod_{j\neq i}^{k}\frac{x_{j}}{x_{j} -x_{i}}\\
=\left( y_{1} +( S'-S)\prod_{j=2}^{k}\frac{x_{j} -x_{1}}{x_{j}}\right)\prod_{j=2}^{k}\frac{x_{j}}{x_{j} -x_{i}} +\sum_{i=2}^{k} y_{i}\prod_{j\neq i}^{k}\frac{x_{j}}{x_{j} -x_{i}}\\
=y_{1}\prod_{j=2}^{k}\frac{x_{j}}{x_{j} -x_{i}} +\sum_{i=2}^{k} y_{i}\prod_{j\neq i}^{k}\frac{x_{j}}{x_{j} -x_{i}} +S'-S\\
=S+S'-S=S'
\end{gather*}$$

Ở bài này không cho ta biết secret cho nên ta phải tự recover lại.

Ta có 

$$\begin{equation*}
S=\sum_{i=1}^{k} y_{i}\prod_{j\neq i}^{k}\frac{x_{j}}{x_{j} -x_{i}}
\end{equation*}$$

Ta không biết $\displaystyle y_{2} ,y_{3} ,y_{4} ,y_{5}$ nhưng ta biết $\displaystyle y_{1}$. Ta cần khôi phục lại phần sau đó là 

$$\begin{equation*}
\sum_{i=2}^{k} y_{i}\prod_{j\neq i}^{k}\frac{x_{j}}{x_{j} -x_{i}}
\end{equation*}$$

Bằng cách ở bước đầu tiên ta gửi fake share. Sau đó nhận về secret key lỗi.

$$\begin{equation*}
S'=\sum_{i=1}^{k} y_{i}\prod_{j\neq i}^{k}\frac{x_{j}}{x_{j} -x_{i}}
\end{equation*}$$

Giả sử ta chọn $\displaystyle x=6,y=1$ thì 

$$\begin{gather*}
S'=\sum_{i=4}^{k} y_{i}\prod_{j\neq i}^{k}\frac{x_{j}}{x_{j} -x_{i}} +\prod_{j=2}^{k}\frac{x_{j}}{x_{j} -x_{i}}\\
\Longrightarrow S'-\prod_{j=2}^{k}\frac{x_{j}}{x_{j} -x_{i}} =\sum_{i=4}^{k} y_{i}\prod_{j\neq i}^{k}\frac{x_{j}}{x_{j} -x_{i}} =C
\end{gather*}$$

Sau đó ta sử dụng lại giá trị $\displaystyle C$ này, cố định khác $\displaystyle x_{i}$ là được. 


Script:

```python
from Crypto.Util.number import *
from pwn import *
import json

my_1k_wallet_privkey = "8b09cfc4696b91a1cc43372ac66ca36556a41499b495f28cc7ab193e32eadd30"
my_wallet = int(my_1k_wallet_privkey,16)
print(my_wallet)

p = 2**521-1 # 13 Mersenne prime
r = remote("socket.cryptohack.org", 13384)
res = r.recvline().decode().strip()
print(res)
res = json.loads(res)
x = res["x"]
y = res["y"]
y = int(res["y"], 16)
for _ in range(4):
    print(r.recvline().decode().strip())
payload1 = {"sender":"hyper","x":6,"y":"0x01"}
payload1 = json.dumps(payload1).encode()
r.sendline(payload1)
res = r.recvline().decode().strip()
print(res)
res = json.loads(res)
priv_fake = res["privkey"]
print(priv_fake)
s_fake = int(priv_fake,16)
const = (s_fake - 5 ) % p # chính là C mà ta đang tính
secret_key = ((y*5)+const)%p
fake_y = (y+(my_wallet-secret_key)*pow(5,-1,p))%p
payload2 = {"sender":"hyper","x":6,"y":hex(fake_y)}
payload2 = json.dumps(payload2).encode()
r.sendline(payload2)
payload3 = {"privkey":hex(secret_key)}
payload3 = json.dumps(payload3).encode()
r.sendline(payload3)
r.interactive()
```
## Tham khảo 

[1] https://people.eecs.berkeley.edu/~daw/teaching/cs276-s04/22.pdf

[2] https://web.mit.edu/6.857/OldStuff/Fall03/ref/Shamir-HowToShareASecret.pdf
