# Emergency

## 题目描述

你知道如何快速响应零日漏洞吗？

## 解题过程

一眼扫过去，比较有问题的感觉是这里

```ts
if ("0" === 1)
{
    try {
        const filename = req.url
        const data = await fs.promises.readFile(filename)
        res.setHeader('Content-Type', 'application/octet-stream')
        res.end(data)
    } catch (error) {
        res.end(error.message)
    }
}
```

这里是恒为假的，很可疑，但是尝试过后发现并没有什么用。别的东西但是又看上去没什么问题。思绪又回到了这道题本身，*零日漏洞*和静态页面中给出的内容。

发现这是用 vite 构建的，然后就去搜索 vite 的洞。不搜不知道，一艘吓一跳。

==[CVE-2025-30208](https://cloud.tencent.com/announce/detail/2039)==

> 据官方描述，在 Vite 开发服务器中，当启用 --host 或配置 server.host，即暴露到公网上时，攻击者可通过构造特殊的 URL（如 ?raw?? 或 ?import&raw??）绕过 @fs 路径访问限制，从而读取服务器上的任意文件（如 /etc/passwd、项目外的敏感配置文件等）

于是直接根据此，访问 `/flag?raw??`，拿到了 flag 为 `0ops{26efadbf-70bc-4858-b0fc-b3ec4c02498b}`。