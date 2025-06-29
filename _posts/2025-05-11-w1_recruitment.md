---
title: 'W1 Recruitment 2025'
date: 2025-05-11 
categories: [ctf]
tags: [Crypto]     
published: true
description: "Thi tuyển CLB"
---
## Crypto/heartbreak
Source code của bài:
```python
from Crypto.Util.number import getPrime, bytes_to_long
FLAG = "W1{???}"

FLAG_PART1, FLAG_PART2 = FLAG[:len(FLAG)//2], FLAG[len(FLAG)//2:]

f =  open("output.txt", "w")

def part1():
    p = getPrime(2048)
    q = getPrime(2048)
    e = 0x10001
    n = p * q
    d = pow(e, -1, (p-1)*(q-1))

    m = bytes_to_long(FLAG_PART1.encode())

    c = pow(m, e, n)

    f.write("ct = " + str(c))

    hints = [p, q, e, n, d]
    for _ in range(len(hints)):
        hints[_] = (hints[_] * getPrime(1024)) % n
        if hints[_] == 0: hints[_] = (hints[_] - 1) % n

    f.write("\nHints = " + str(hints) + "\n")


def part2():
    e = getPrime(10)
    p = getPrime(256)
    q = getPrime(256)
    n = p * q
    # print(e)
    m1 = bytes_to_long(FLAG_PART2.encode())
    m2 = m1 >> 8


    c1, c2 = pow(m1, e, n), pow(m2, e, n)
    f.write(f"n = {n}\nc1 = {c1}\nc2 = {c2}\n")

if __name__ == "__main__":
    part1()
    part2()
```

Part 1:

```python
def part1():
    p = getPrime(2048)
    q = getPrime(2048)
    e = 0x10001
    n = p * q
    d = pow(e, -1, (p-1)*(q-1))

    m = bytes_to_long(FLAG_PART1.encode())

    c = pow(m, e, n)

    f.write("ct = " + str(c))

    hints = [p, q, e, n, d]
    for _ in range(len(hints)):
        hints[_] = (hints[_] * getPrime(1024)) % n
        if hints[_] == 0: hints[_] = (hints[_] - 1) % n

    f.write("\nHints = " + str(hints) + "\n")
```
Ở part 1 các số $p,q,n,e,d$ đã được làm nhiều bằng cách nhân thêm vào các số nguyên tố $r$ ngẫu nhiên có độ dài 1024 bit sau đó lấy theo modulo $n$. 

Em thấy rằng do $p$ chỉ có độ dài 2024 bit nên sau khi nhân với số nguyên tố $r$ thì nó vẫn bé hơn $n$ vì $n$ có độ lớn 4096 bit. Điều này chứng tỏ sau khi lấy theo modulo $n$ thì nó vẫn giữ nguyên giá trị. 
Tương tự với $q,e,d$. 

Còn đối với $n$ thì sau khi làm nhiễu sẽ trở thành $n-1$. Vậy ý tưởng giải part 1 của em là khôi phục lại $n$, sau đó lấy ước chung với các giá trị bị làm nhiễu còn lại để khôi phục lại $p,q$ và tính được $d$. 


Part 2:

Ở bài này bài cho mình hai giá trị $m2$ và $m1$. Toán tử `>>` là toán tử dịch dịch phải bit. Như vậy ta có $\displaystyle m_{1} =256m_{2} +r$ trong đó $\displaystyle r\in [ 0,255)$.

Nhưng em biết phần còn lại của flag có đuôi là `}` nên e có thể lấy $\displaystyle r=125$ là giá trị ASCII của `}`. Sau đó em lấy 2 đa thức $\displaystyle f( x) =( 256x+125)^{e} -c_{1}$ và $\displaystyle g( x) =x^{e} -c_{2}$ và tìm ước chung của nó. $\displaystyle e$ là 1 số nguyên tố 10 bit nên nó sẽ thuộc khoảng $\displaystyle 2^{9} +1< e< 2^{10}$. Có thể chạy nhanh hơn bằng cách lấy bước nhảy = 2. 

Script:

```python
from Crypto.Util.number import *
from sage.all import *


ct = 239991743627005761506047553716973180857493049128968395824678613535924041735819278721655197652704368009118731671080782572692443257002266295841054097811995343407149181564647568019524547331554506022380795516159222363510661688595308307174873885160951837722610012918052195448795081291878933355634383798002056753336540546915811592763747343189324926404600658482137848658910189331650916354541907427173491308413908173314104508974384232290785538938623142120477030045742266779693627293755590884412082209151425384896460777577066084111556036719259982254175935197376972307183776259868229411302259648873045160120795060467866459055693698198316577983136619062944244317116994863470942099523485902299419458583301056211340627830237050622364646501838811516544340499168319955128200158195905283972429746772105746244910156671549456233908152186037286726530314472293814226978595268877619521165090870514287104577960355240428728213124348138646047728851553209042359051265045752603864312856768918350064549850618348693037041311112677351368226231458377933846664981185928405481697006968220556167073996713389716367133156065980195285148700027809062253416860922839857907535460170132744912543758918516134641462581544039400881675553681819294266618981791250077585566821053
Hints = [1659380349228980310793195740551091998951133377142727433181233112954301485314646349955561783455759149036476737520702967988760310760312391176774501840210477308343796277178701715703164651184747756453236376960884981254635386807663657355175214655034193946682205904113897156938926175413312324159809831274187894029251371896829385517428693915588566248998565348483747234270329027561433356156355227738138379418717200316600111392671357267757409316691191187929104999221832963378673907868493499459459256894553552195468110517716115678897171373484791903085022119845347228569870830569321957438966030560930098761455953418479618027428739560761186901956751224602703471828674970337530768801203531827467185690264637238603523292728974955840936711823826949100349569805942227432262275344597592991937770320032450419910426614638156263968483514069904464463402987714698220412063100772910040673056263954301063853666792319306234733964442731206579494701823, 2473062479389297534384652365580702456631745761133091301459488546735676124432184165907268360691972078275036508838070797476235444908532100862886958054816201240359777405719375503276188588243722655521225159899315126844306119104997283731460041142142861614319514182297180409656236066011186834375282437648078334082114411988739789392926471318552017202390239258487258487037639778148532362214152519137264282830808294108610004403460587358629486534247288993860831438267841520573973916758902637404855712721811250301202405252158590233975917681798839166192235032424495289048875626437256513981936177292903742966118351026329401662519672777610159422234703124064225710846129876359778079390437505753932101501472252131924073957349012610362952003934494293369977045255000415066563914471132856401071867358687200638320541179048296074882945411664474157308113874183763200469775985802906031999934356545932354932945475129644344782759659758587119586377699, 10811389778781749507848369001995006527965136627134898173336798777178617924322548317218123003648199959431162146218350234488676047952720517043381973357960494027353001493321216082118308994614655309535481054291078777020047697030175144901965025751169815034274454689657181813765988172412194436669592236745329922983348687, 302270795345262652787049603034608860428203534578338699389744017410286806560707153186173595970011317749221953776227374491075684575774328458397778789729965544452010822737225229627108698874874960910965283322537095645241316756015118860396297708799504427117968454341778975073542738740964812664233075630257595196115893498655587491091126391722042679460013562303363850840912825755508680428829444388842951146482726568654418889598972824414109879370032604627990857015975495697530048397401132619555923308480516678981072029040306410523264480829688927798146165580384683689546853438820269495274502502143436234210825345362323042888891473423488116618918996618482754420372481893339222050268599796400452566639228520072261554594477336671016198833620427171498256007475831231524217372147783123617237105584613811522861590112133798309937116024168109605161059186745904038968516182034441583819409524470833832445513001764653452246905494902592731421744692331296978104865554556383417129664683066891729999411036376957753554228948901872115141778394712481069999501808719302346670855531871069366050528261442730651167165455031776639195754458920584910014448480393554851269958179362536781346169282601003523441289868134223507188615919092337658908484201829621953970243126, 292884935929549246643624576832991010496540593142708150947518571090956754050658797955786741707549303442499542376031718493862827439931968289137063776270385766876597819025272388601563339731026897291119786098658019861515084557432779618732711248671531045529170282246847559014298960824095511448557742476351035447816620523706582563446012744989424367197536652910056457067991830630881476241325631891896455745762634189112349619740103725259844111996043190764424736601828139403024375351372351763169837210283497390177391509068689722596094161210899552103572279045474893618035695053640281885367691987326728488743356438444158731410968935525788220429173044772188634219137052045984284351149340419125628533953328666109489959225736888258255952666488686965689900369280599239198825803111830614815595803322570878359992207842211026468967074229662081167123002174445268278747577520774432781623852071789431202030569577689851124794177654088403580945598530502231516822280930459410373890257129917535480957183190120541342731664708767982764672591340622307503858396934095635519503588136203556304925670734741505400274178108097309035870578129722954864083937813328351751442713938490297629659866083082398939022316220801556516785394083566880368038684968532814902807429836]
n = 3942322022657678598973964668297464188690492529227912763243818849286024502988170927049337618748813366973108404930787214396933140242919629931061653981563183
c1 = 711628464933911477721875076362382562533209928505813633932265201230108117662370018036520970813120568151605229871541865330790006742825947411425842775387464
c2 = 199593868713063917388131306750677766968978918100656918000052209242691143624414286755644005020481267183225454134184719235765273635594459821952870709463733


# part 1
a=gcd(Hints[3]+1,Hints[0])
b=gcd(Hints[3]+1,Hints[1])
assert a*b==Hints[3]+1
phi=(a-1)*(b-1)
e=0x10001
d=pow(e,-1,phi)
m1=pow(int(ct),int(d),int(Hints[3]+1))
print(long_to_bytes(m1))

# part 2

F = PolynomialRing(Zmod(n), 'm')
m = F.gen()

def poly_gcd(a, b):
    while b:
        a, b = b, a % b
    return a, b
for e in range(2**9 + 1, 2**10, 2): 
    f1=(m*256+125)**e-c1
    f2=(m)**e-c2
    r1, r2 = poly_gcd(f1, f2)
    assert r2 == 0
    if r1.degree()==1:
        b, a = r1.coefficients()
        flag = (-b) * pow(a, -1, n) % n 
        print(long_to_bytes(m1)+long_to_bytes(int(flag))+b'}')
        

# W1{https://www.youtube.com/results?search_query=p0lyn0m1als+9c4+is+good+isn%27t+it+?flag=tru4}
```


## Crypto/Ánh sáng vĩnh hằng
Source code của bài:
```python
from base64 import b64encode as mã_hóa_base64
from bí_mật import thông_điệp_bí_mật, khóa
def giải_mã_chuỗi(x): return x.decode()
def mã_hóa_chuỗi(x): return x.encode()
from pwn import xor as hiệu_đối_xứng
xuất_ra = print

thông_điệp_đã_mã_hóa = hiệu_đối_xứng(mã_hóa_chuỗi(thông_điệp_bí_mật), mã_hóa_chuỗi(khóa))
xuất_ra(giải_mã_chuỗi(mã_hóa_base64(thông_điệp_đã_mã_hóa)))

#kOKO1fZJVp1Im90chpvtwdnCHwArCEEdJSt41fZJTLlaIMdCR+Ppj9ODA+PmBwZPslNBm/OqkzaGm92P3LEhwdfWs5QpSRWOfwOPm+WImvmIm9oBCUdoiXgDHkhrSQ8HAiDB0/AAADUAAR4aR0OpWhzCUE2m0vgb5FRwWir6Tj/Bw9xAR2sgiJtg0U4vSRKsZf6Gm/2qijbB1c6t00lkwdlgw04gSRWOfwGIm/2Imu+P3IkChpr1wdjLkZvaSaX+JStC0r1JTjAnC4kDhpvRlZvNF+b3iNryrbCDWivITniV00jUwE1ojVoZx04gSQKOfzeAm+UBweN80okJDkEmz5voGElnC6LcqvfBz3DSsTHB1c6P3JFolclCy6trSaLOqvjByFLITj/B0MGt004vwdNCy6FnCwiOfi+Pm/yImv2Vl4kDpIBoIhnOUFQviNvIqbAnC1fITXiMWhPJCgxogtNCy71nrfCOfzOIm/oBT7lbGMcGR0sgAAEMEwCD+IDUR7CDWirCTj/B12rECQAlAAECHkhnBIDVebCJfTAHADqA1IkJDsHzfJvLkZr4HU9PhlNMm/yImvWVm9+P3KFokloYwQAzAYDVaeTB2HDShznBzWzHR1Q6AAAGUEsvqtUBo7CR03DTgzHB12rOR+Ppj9ODA+PmBwZPrPGYm/OqkzaGm92P3LEhzZvOs4BnBaLP5OMAACBJRzGA1IkGhpvrkZvEGcH8xgBPp/giAf8ODHiG0kjV9k4vwdXLtpBnGQmsbeDBz/6qgTbB08CP3Kc9wX8ykZvWAEEXJStI1fZJCACu6YBOEcHyTMKDkqDUSQ+pZfnB1lLJACsAABhOE8HzctWDBMH9yAhPp3FaHPBJTblaIt1OBePij5vIGOPzBwZPqP8AATAAACyTWhLFR0KLS9WDG0kmRUECBzDBz3DTgTfB1WrECQAlAAA6BAA0iNre5PMiGf9JQrlbCscJR1Qhj9ODBMH91kEZBzDB2VLEALlbEsdAR3aLQZvABcH8+AhPp1NY1fZJTJtBm8qP3L1yweySC2h2WhQwoKaI5MkcTmG+3stZX0Z60IzBFUIiXlZY8aKD3vMU
```
### Ý tưởng
Về ý tưởng giải bài này e có tham khảo được các bài viết dưới đây sau giải và sau khi đọc hint của bài: 
https://hmcguinn.com/posts/cryptopals-set-1/#:~:text=The%20Hamming%20distance%20is%20just,result%20by%20dividing%20by%20KEYSIZE.

https://crypto.stackexchange.com/questions/8115/repeating-key-xor-and-hamming-distance#:~:text=I%20recently%20started%20the%20Matasano,size%20of%20the%20cipher%20key


Bài cho ta một bản mã nhưng ta không hề được cho biết về key cũng như độ dài của nó. 

Vậy để giải được bài này ta cần thực hiện 2 bước: Bước đầu tiên là tìm cách khôi phục lại key length và bước thứ 2 là thử tất cả các key có thể có. 


**Bước 1**: Khôi phục key length

**Hiệu đối xứng.** Cho hai tập hợp $\displaystyle A,B$. Khi đó hiệu đối xứng của hai tập hợp được định nghĩa như sau

$$\begin{equation*}
A\Delta B=( A\backslash B) \cup ( B\backslash A)
\end{equation*}$$

Kết quả $\displaystyle A\Delta B$ là một tập hợp bao gồm các phần tử không thuộc vào cả hai tập hợp $\displaystyle A$ và $\displaystyle B$. 

Phép XOR giữa 2 xâu nhị phân chính là phép lấy hiệu đối xứng giữa hai tập hợp chứa vị trí các bit 1 của chúng. 

Đối với trường hợp hai xâu nhị phân thì ta còn có khái niệm gọi là khoảng cách Hamming. Khoảng cách Hamming chính là số bit sai khác nhau của hai xâu nhị phân có độ dài bằng nhau. 

![image](https://github.com/user-attachments/assets/c8f5462f-6eea-4261-bcce-ce9343f85ead)

```python
def hamming(string1:bytes,string2:bytes)->int:
    assert len(string1) == len(string2)
    d = 0 
    for (byte1,byte2) in zip(string1,string2):
        d += bin(byte1^byte2).count('1')
    return d
s1 = "this is a test".encode()
s2 = "wokka wokka!!!".encode()
dist = hamming(s1, s2)
assert dist == 37
```
Tiếp theo ta sẽ tìm cách khôi phục lại key length bằng việc so sánh các khoảng cách hamming. Cụ thể, khoảng cách hamming trung bình giữa 2 bytes random sẽ rơi vào khoảng 4, trong khi đó đối với các kí tự lowercase ASCII từ 97 tới 122 sẽ là 2.5. Đối với $X,Y$ là hai từ tiếng anh thì khoảng cách sẽ rời vào khoảng 2-3. 


![image](https://github.com/user-attachments/assets/eb8001ab-6b46-410a-b83d-96fab04b208c)

Giả sử ta đoán được chính xác key length và ta chia plaintext thành các khối có độ dài bằng nhau. Khi đó hai khối bất kì sẽ có dạng $\displaystyle X\oplus K$ và $\displaystyle Y\oplus K$. Ta sẽ tính được khoảng cách Hamming giữa $\displaystyle X$ và $\displaystyle Y$ vì : $\displaystyle d( X\oplus K,Y\oplus K) =d( X\oplus Y)$. Lúc này $\displaystyle d( X\oplus Y)$ sẽ xấp xỉ khoảng $\displaystyle ( 2-3) *len( X)$. Còn nếu ngược lại thì ta sẽ rơi vào trường hợp $\displaystyle d( X\oplus K,Y\oplus K') \geqslant d( X\oplus Y)$ trong trường hợp ta đoán sai keylength.

![image](https://github.com/user-attachments/assets/9e75cb79-6e50-43c8-927d-6cebfaf59b2f)


Vậy cách làm của ta bây giờ sẽ là tạo các cặp ciphertext có cùng độ dài và tính hamming distance của chúng. Sau đó ta sẽ lấy trung bình các hamming distance này thì sẽ ra được độ dài (có khả năng có) của key. 



```python
def hamming(string1:bytes,string2:bytes)->int:
    assert len(string1) == len(string2)
    d = 0 
    for (byte1,byte2) in zip(string1,string2):
        d += bin(byte1^byte2).count('1')
    return d
s1 = "this is a test".encode()
s2 = "wokka wokka!!!".encode()
dist = hamming(s1, s2)
assert dist == 37
# có thể tăng khoảng bruteforce keysize lên tùy vào trường hợp
def guess_key_length(ciphertext):
    key_length=0
    min_dist=len(ciphertext)
    for keysize in range(2,40):
        blocks = [ciphertext[i:i+keysize] for i in range(0,len(ciphertext)-keysize+1,keysize)]
        pair_num=len(list(combinations(blocks,2)))
        average_dist=(sum(hamming(a,b) for a,b in combinations(blocks,2))/pair_num)/keysize
        if average_dist<min_dist:
            min_dist=average_dist
            key_length=keysize
    return key_length
key_size = guess_key_length(b64decode(msg))
print(key_size)
# 24
```
Ở bài này keylength = 24

**Bước 2**: Khôi phục lại plaintext.

Ta chuyển từ bài repeated key XOR cipher về single byte XOR cipher. 

**Single byte XOR Cipher:** Khóa của ta là một byte duy nhất. 
Cách thám mã cho trường hợp này đó là ta sẽ sử dụng frequency analysis. Cụ thể ta sẽ thử tất cả các key có thể có, do key chỉ là 1 byte nên việc này sẽ khả thi. Sau đó, với mỗi một chuỗi đưa ra, chúng ta cần check xem tần số các chữ cái có giống với [bảng tần số](https://en.wikipedia.org/wiki/Letter\_frequency) hay không.

**Repeated key XOR cipher:** 
Vì key có độ dài lớn và được lặp lại, nên ta sẽ chia block thành các khối có độ dài bằng với key length. Bytes thứ $\displaystyle n$ của key sẽ XOR với bytes thứ $\displaystyle n$ của plaintext nên ta có thể nhóm các bytes ở cùng vị trí lại với nhau trong mỗi block và giải bài Single byte XOR Cipher riêng lẻ. 

Tham khảo: 
https://www.youtube.com/watch?v=tE2iW_QAU1AFull
https://www.youtube.com/watch?v=YTA6sXNgMRE
script:

```python
from itertools import *
from base64 import b64decode
import string
from pwn import xor
from math import log
msg = "kOKO1fZJVp1Im90chpvtwdnCHwArCEEdJSt41fZJTLlaIMdCR+Ppj9ODA+PmBwZPslNBm/OqkzaGm92P3LEhwdfWs5QpSRWOfwOPm+WImvmIm9oBCUdoiXgDHkhrSQ8HAiDB0/AAADUAAR4aR0OpWhzCUE2m0vgb5FRwWir6Tj/Bw9xAR2sgiJtg0U4vSRKsZf6Gm/2qijbB1c6t00lkwdlgw04gSRWOfwGIm/2Imu+P3IkChpr1wdjLkZvaSaX+JStC0r1JTjAnC4kDhpvRlZvNF+b3iNryrbCDWivITniV00jUwE1ojVoZx04gSQKOfzeAm+UBweN80okJDkEmz5voGElnC6LcqvfBz3DSsTHB1c6P3JFolclCy6trSaLOqvjByFLITj/B0MGt004vwdNCy6FnCwiOfi+Pm/yImv2Vl4kDpIBoIhnOUFQviNvIqbAnC1fITXiMWhPJCgxogtNCy71nrfCOfzOIm/oBT7lbGMcGR0sgAAEMEwCD+IDUR7CDWirCTj/B12rECQAlAAECHkhnBIDVebCJfTAHADqA1IkJDsHzfJvLkZr4HU9PhlNMm/yImvWVm9+P3KFokloYwQAzAYDVaeTB2HDShznBzWzHR1Q6AAAGUEsvqtUBo7CR03DTgzHB12rOR+Ppj9ODA+PmBwZPrPGYm/OqkzaGm92P3LEhzZvOs4BnBaLP5OMAACBJRzGA1IkGhpvrkZvEGcH8xgBPp/giAf8ODHiG0kjV9k4vwdXLtpBnGQmsbeDBz/6qgTbB08CP3Kc9wX8ykZvWAEEXJStI1fZJCACu6YBOEcHyTMKDkqDUSQ+pZfnB1lLJACsAABhOE8HzctWDBMH9yAhPp3FaHPBJTblaIt1OBePij5vIGOPzBwZPqP8AATAAACyTWhLFR0KLS9WDG0kmRUECBzDBz3DTgTfB1WrECQAlAAA6BAA0iNre5PMiGf9JQrlbCscJR1Qhj9ODBMH91kEZBzDB2VLEALlbEsdAR3aLQZvABcH8+AhPp1NY1fZJTJtBm8qP3L1yweySC2h2WhQwoKaI5MkcTmG+3stZX0Z60IzBFUIiXlZY8aKD3vMU"
def hamming(string1:bytes,string2:bytes)->int:
    assert len(string1) == len(string2)
    d = 0 
    for (byte1,byte2) in zip(string1,string2):
        d += bin(byte1^byte2).count('1')
    return d
# test , source: cryptopal
# s1 = "this is a test".encode()
# s2 = "wokka wokka!!!".encode()
# dist = hamming(s1, s2)
# assert dist == 37
# có thể tăng khoảng bruteforce keysize lên tùy vào trường hợp
def guess_key_length(ciphertext):
    key_length=0
    min_dist=len(ciphertext)
    for keysize in range(2,40):
        blocks = [ciphertext[i:i+keysize] for i in range(0,len(ciphertext)-keysize+1,keysize)]
        pair_num=len(list(combinations(blocks,2)))
        average_dist=(sum(hamming(a,b) for a,b in combinations(blocks,2))/pair_num)/keysize
        if average_dist<min_dist:
            min_dist=average_dist
            key_length=keysize
    return key_length
key_size = guess_key_length(b64decode(msg))
print(key_size)
# https://en.wikipedia.org/wiki/Letter_frequency#Relative_frequencies_of_letters_in_the_English_language
# có nhiều phiên bản khác nhau của bảng này, nhưng nhìn chung cũng không chênh lệch nhau nhiều về xác suất
eng_frequencies = {
    'a': 8.167, 'b': 1.492, 'c': 2.782, 'd': 4.253, 'e': 12.702,
    'f': 2.228, 'g': 2.015, 'h': 6.094, 'i': 6.966, 'j': 0.153,
    'k': 0.772, 'l': 4.025, 'm': 2.406, 'n': 6.749, 'o': 7.507,
    'p': 1.929, 'q': 0.095, 'r': 5.987, 's': 6.327, 't': 9.056,
    'u': 2.758, 'v': 0.978, 'w': 2.361, 'x': 0.150, 'y': 1.974,
    'z': 0.074, ' ': 18.3
}
def score_text(text:bytes)-> float:
    score = 0.0
    l=len(text)
    for letter, frequencies in eng_frequencies.items():
        frequency_actual=text.count(ord(letter))/l
        err = abs(frequencies-frequency_actual)
        score += err   
    return score # về hàm này thì nếu điểm trả về càng thấp thì chứng tỏ decrypted text của ta có khả năng cao là plaintext gốc
def single_key_xor(ciphertext):
    best_guess=(float('inf'),None)
    key=None
    for cand_key in range(256):
        full_key=bytes([cand_key])*len(ciphertext)
        plaintext=bytes([b1 ^ b2 for b1,b2 in zip(full_key,ciphertext)]) 
        score = score_text(plaintext)
        curr_guess=(score,plaintext)
        if curr_guess < best_guess:
            best_guess=curr_guess
            key=cand_key
    if best_guess[1] is None:
        exit("không giải được")
    return best_guess[0],bytes([key]),best_guess[1]
# test : https://cryptopals.com/sets/1/challenges/3
# ciphertext='1b37373331363f78151b7f2b783431333d78397828372d363c78373e783a393b3736'
# print(single_key_xor(bytes.fromhex(ciphertext))) 
def repeated_key_xor(ciphertext,keysize):
    blocks = [ciphertext[i:i+keysize] for i in range(0, len(ciphertext), keysize)]
    cracks = [bytes([block[i] for block in blocks if i < len(block)]) for i in range(keysize)]
    key = b""
    for block in cracks:
        key += single_key_xor(block)[1]
        print(key)
    return key
ciphertext = b64decode(msg)
key=repeated_key_xor(ciphertext,key_size)
print(xor(ciphertext,key))
```

![image](https://github.com/user-attachments/assets/42d67af5-2afd-42ef-b313-7a7847b12c8f)

Chỗ kia thì mình đoán là X/x nên chỉnh lại thành flag như sau:
`W1{H13u_d6i_Xun9_eb78f217bebe77752beb}`

Mọi người có thể xem thêm phiên bản hiệu quả hơn tại đây: https://cypelf.fr/articles/easy-xor/

