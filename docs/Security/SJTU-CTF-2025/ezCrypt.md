# ezCrypt

## 题目描述

This text looks suspiciously like Base64… but something’s not quite right. It seems a bit… compressed?

Flag格式：0ops{...}

* 如果你没有看到以上格式的内容，说明你没有找到正确的flag，请不要相信大模型的幻觉：）

## 题目解答

其给出了一串字符串，末尾有一个`=`，看起来类似于 `Base64` 编码的字符串。放到[赛博厨子](https://cyberchef.org/)出来是ASCII编码的字符串，看起来不太对。

由于是纯小白和新手，又去找了一些工具，首先便是[Ciphey](https://github.com/bee-san/ciphey)，配置其环境又花费了一些功夫，其对 `Python` 版本有比较严格的要求，新版本的 `Python` 会报错，最后安装了`Python 3.9`，剩下的就是跟着教程安装好其余的依赖，然后简单学了学用法。

直接在命令行中运行后，拿到了第一次解码后的结果，是从 `Base64` 到 `gzip`："0ops, looks like we have a really long number. Can you figure out what this is? (这里跟了一串二进制数字)"

然后再把这串二进制喂给Ciphey，从 `binary` 到 `utf8` 得到的结果是："Time to choose! There are two blocks of cipher, the flag is in one of them. Choose wisely! (其后跟了两段)"

先解码第一段，依旧是喂给Ciphey，得到的结果是："The flag is Yjkl{E=ake fl@G oF CRyPTO ChaEE!}! I'm kidding. April Fool! There is no flag in this one~ But I can provide you some hint for the other block!\n Maybe it is a compact representation of IPvG addresses, at least according to some funny RFCs I read earlier. After that, you might want to learn about
RSA, and the extreme difficulty to factor huge numbers, especially when they are the product of two large primes, unless someone had published the result of the factorization. Good luck!"

啊，看来是要和 `RSA` 有关了，去找了 `RSA` 的相关知识，是一个非对称加密算法，简单看了一下留了一下印象之后就去解码第二段了，从 `base85` 到 `utf8`，解出来为：
”
I'm so sorry, I forgot to save the private key to decode the flag. But some supercomputer 
have already cracked it and uploaded to an online db. Can you find the flag?

n = 0x771b68deea7e2ecd0fa15099ae9085e1a6b163c415bde56c61ec811201d52e456e4a876db6da7af2695e206d9e3b23de02a16f675ad087c4bef3acc6c4e16ab3

e = 65537

c = 0x5641d8b05fda28c9af355a488bb6d97d9fe21ea645bc25814db317f04faa84a6fd93fa383396523f050b968e197f89febad840614840eebd675a3f917324f9d0
”

给出了 `n` 和 `e`，以及密文 `c`，我应该先找到 `n` 的两个质因数，这里发生的一个事情是，可能是操作失误，我用赛博厨子将十六进制转换为十进制的时候，其给出了一个错误的十进制，故而后面的过程全部发生了错误，我还查了半天后面的问题，最后用`Python` 直接转换为十进制，然后去质因数分解，然后丢进脚本里就顺利拿下了flag "Flag: 0ops{bR@v0_y0u_rEAl1Y_90oD_a7_CRyPTO}"。下附脚本：

```python
from Crypto.Util.number import long_to_bytes

# 已知 RSA 参数
n = 0x771b68deea7e2ecd0fa15099ae9085e1a6b163c415bde56c61ec811201d52e456e4a876db6da7af2695e206d9e3b23de02a16f675ad087c4bef3acc6c4e16ab3
print(n)
e = 65537
c = 0x5641d8b05fda28c9af355a488bb6d97d9fe21ea645bc25814db317f04faa84a6fd93fa383396523f050b968e197f89febad840614840eebd675a3f917324f9d0

# 已计算的 p 和 q
p = 66720953144911165998838491049270049821121906475512246576323412599571011308613
q = 93496017058652140120451192281187268387402942550918512435321834788719825835671

assert p * q == n, "p*q不等于n，因子分解错误"

print("求得因子 p =", p)
print("求得因子 q =", q)

phi = (p - 1) * (q - 1)

def egcd(a, b):
    if b == 0:
        return a, 1, 0
    g, x, y = egcd(b, a % b)
    return g, y, x - (a // b) * y

def modinv(a, m):
    g, x, _ = egcd(a, m)
    if g != 1:
        raise Exception("模反元素不存在")
    return x % m

d = modinv(e, phi)
assert (d * e) % phi == 1, "私钥计算错误"
print("[*] 计算得到私钥 d:", d)

m = pow(c, d, n)
flag_bytes = long_to_bytes(m)
try:
    flag = flag_bytes.decode()
except UnicodeDecodeError:
    flag = flag_bytes
print("Flag:", flag)
```

## 解题感想

作为纯萌新除了签到题外的第一题，还是花了一定时间的，当时在感觉里不算很简单:cry:，但是还是有众多的人拿下了此题，当时还很疑惑为什么感觉不简单的层层解密的题怎么大家都做出来了，但在做了后面的题后，发现这道题确实很友善了，至少其给出了明确的提示和指引，后面的一些题目就没有这么友好了。