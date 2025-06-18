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

- Cần phân chia bí mật $\displaystyle S$ cho $\displaystyle n$ người lưu giữ $\displaystyle ( P_{1} ,P_{2} ,...,P_{n})$, mỗi người $\displaystyle P_{i}$ giữ mootj phần thông tin $\displaystyle x_{i}$ có quan hệ với $\displaystyle S$, sao cho bất kì $\displaystyle t( t\leqslant n)$ trong số $\displaystyle n$ người có thể khôi phục được bí mật $\displaystyle S$ nếu như $\displaystyle t\geqslant k$, ở đây $\displaystyle k$ gọi là ngưỡng (threshold). Thế nhưng số lượng thành viên liên kết nhỏ hơn $\displaystyle k$ thì không thể khôi phục được cho dù giữa họ có một tài nguyên lớn để tính toán. 

- Vào năm 1979, Shamir đã cho ra mắt lược đồ Shamir’s Secret Sharing Scheme (SSSS) dựa vào ý tưởng trên. 

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



Đặt $\displaystyle h( x) =q( x) -p( x)$ thì $\displaystyle h( x)$ có 3 nghiệm phân biệt là $\displaystyle 1,2,3$ cho nên $\displaystyle \deg h( x) \geqslant 3$ trong khi đó $\displaystyle \deg( q( x) -p( x)) \leqslant \max\{\deg p,\deg q\} =2$ cho nên ta có điều mâu thuẫn 

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


## Tham khảo 

[1] https://people.eecs.berkeley.edu/~daw/teaching/cs276-s04/22.pdf

[2] https://web.mit.edu/6.857/OldStuff/Fall03/ref/Shamir-HowToShareASecret.pdf
