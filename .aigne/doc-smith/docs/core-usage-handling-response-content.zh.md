# 处理响应内容

发出请求后，`Response` 对象会持有服务器的回复。处理响应的第一步通常是检查其主体。Requests 提供了几种便捷的方式来访问响应主体，以适应不同类型的内容。

本指南将介绍如何将响应读取为原始字节、解码后的文本以及结构化的 JSON。有关发出初始请求的入门知识，请参阅[发出请求](./core-usage-making-a-request.md)。

## 二进制响应内容

你可以使用 `response.content` 属性以字节序列的形式访问服务器的响应。这是原始的有效载荷，非常适合处理非文本内容，如图像、PDF 或其他二进制文件。

```python 下载一张图片 icon=logos:python
import requests

r = requests.get('https://httpbin.org/image/png')

# 内容是字节类型
print(r.content[:20])
# 预期输出可能以 b'\x89PNG\r\n\x1a\n\x00\x00\x00\rIHDR...' 开头

# 你可以直接将这些字节保存到文件中
with open('image.png', 'wb') as f:
    f.write(r.content)
```

输出中的 `b''` 前缀表示你正在处理一个 `bytes` 对象。以二进制写入模式（`'wb'`）将此内容写入文件，可以确保文件被正确保存，不受任何文本编码的干扰。

## 文本响应内容

对于文本数据，如 HTML 或纯文本，`response.text` 属性更为方便。它会自动将 `response.content` 中的原始字节解码为 Python 字符串。

```python 读取文本内容 icon=logos:python
import requests

r = requests.get('https://httpbin.org/html')

# 内容是一个 unicode 字符串
print(r.text)
# 预期输出：
# <!DOCTYPE html>
# <html>
#   <head>
#   </head>
#   <body>
#       <h1>Herman Melville - Moby-Dick</h1>
# ...
```

Requests 会智能地确定编码。它首先检查 `Content-Type` HTTP 标头中是否有 `charset`。如果没有指定，它会使用像 `chardet` 这样的库从内容本身猜测编码。你可以在访问 `.text` 之前检查所选的编码，并在必要时覆盖它：

```python 覆盖编码 icon=logos:python
import requests

r = requests.get('https://httpbin.org/get')

# 检查自动检测的编码
print(f"自动检测的编码: {r.encoding}")

# 如果需要，可以用不同的编码覆盖
r.encoding = 'ISO-8859-1'

print(f"手动设置的编码: {r.encoding}")
print(r.text)
```

## JSON 响应内容

许多 Web API 以 JSON 格式返回数据。Requests 包含一个内置的 JSON 解码器，即 `response.json()` 方法，它将响应主体解析为一个 Python 对象（通常是 `dict` 或 `list`）。

```python 解析 JSON 响应 icon=logos:python
import requests
from requests.exceptions import JSONDecodeError

r = requests.get('https://httpbin.org/json')

try:
    # .json() 方法返回一个 Python 字典
    data = r.json()
    slideshow_title = data['slideshow']['title']
    print(f"幻灯片标题: {slideshow_title}")
except JSONDecodeError:
    print("响应无法被解码为 JSON。")
except KeyError:
    print("JSON 结构与预期不符。")

# 预期输出：
# 幻灯片标题: Showcase
```

如果服务器返回的响应不是有效的 JSON，调用 `.json()` 将会引发 `requests.exceptions.JSONDecodeError`。将该调用包装在 `try...except` 块中以优雅地处理这种情况是一种很好的做法。

## 流式传输大型响应

默认情况下，当你访问 `.content` 或 `.text` 时，整个响应主体会一次性下载并加载到内存中。对于非常大的文件，这可能会引发问题。为了避免这种情况，你可以在请求中设置 `stream=True` 来流式传输响应。

当进行流式传输时，响应内容只在你对其进行迭代时才会被下载。

```python 流式传输并保存文件 icon=logos:python
import requests

# 使用 stream=True 来延迟下载响应主体
with requests.get('https://httpbin.org/stream/10', stream=True) as r:
    r.raise_for_status() # 检查请求是否成功
    with open('streamed_data.jsonl', 'wb') as f:
        # 以 8192 字节的块大小迭代响应
        for chunk in r.iter_content(chunk_size=8192):
            # chunk 将是一个字节对象
            f.write(chunk)
```

使用 `with` 语句可以确保即使发生错误，连接也能被正确关闭。`iter_content()` 方法允许你将有效载荷分块处理。对于基于行的文本流，你还可以使用便捷的 `iter_lines()` 方法。

---

你现在已经了解了处理响应主体的主要方式，从简单的字节和文本访问到强大的 JSON 解析和高效的流式传输。有了这个基础，你可以自信地处理来自任何 API 的数据。

要了解如何*向*服务器发送数据，请继续阅读下一节：[POST 数据](./core-usage-posting-data.md)。