# Inaudible

## 题目描述

给了一张频谱图和一个提示：音频内容是三个英文单词，请用“-”连接起来，套在 flag 格式里提交，例如 `0ops{a-test-flag}`。

## 解题过程

看到给了个图片，先用 silenteye 看了看有没有隐写信息，又手动看了看图片相关信息，看来感觉是没有。那么根据提示，就很明显了，那就是根据频谱图转换为音频，然后根据音频听出三个英文单词。

目标明确，方向清晰，但脚本属实是搞了好长时间，感觉是 AI 比较喜欢加戏，加了很多乱七八糟看着很高端的恢复算法。
最终经过手动调整，就使用了朴素直接的转换方法，虽然最后转出来听起来比较糊，但是在做此题前瞄过了 Emergency，查过一些零日漏洞的信息，所以当听出来前两个字母的时候，基本就能确定了，虽然那个复数刚开始没听出来（

下附脚本（处理的还不是很完善，其上面还是有一层噪声，但是声音放大之后可以听见里面的 zero-day-vulnerbilities）：

```python
import numpy as np
from PIL import Image
import soundfile as sf

# ===== 参数 =====
# 请修改为实际的频谱图文件路径
image_path = r"inaudible/Inaudible.png"
duration = 2.0            # 音频总时长，单位秒
fs = 44100                # 采样率
num_samples = int(duration * fs)

# ===== 加载图像并预处理 =====
# 转为灰度图（0~255）; 注意PIL读入时原点在左上，需翻转让底部对应0Hz
img = Image.open(image_path).convert('L')
spect = np.array(img)     # shape: (height, width)
height, width = spect.shape
spect = np.flipud(spect)  # 翻转，使得图像底部对应低频

# 每一列对应的时间点（线性均布于[0, duration]）
time_control = np.linspace(0, duration, num=width)
t = np.linspace(0, duration, num_samples, endpoint=False)

# ===== 合成各频率分量 =====
signal = np.zeros(num_samples)

# 遍历每一频率分量（图像每一行）
for j in range(height):
    # 频率从 0kHz 到 6kHz（底部到顶部）
    freq = j / (height - 1) * 6000  
    # 为该分量随机选取一个初相位
    phase = np.random.uniform(0, 2 * np.pi)
    
    # 读取当前频率下的包络（横向像素，代表不同时间点下的响度）
    pixel_values = spect[j, :]  # 值范围0~255

    # 将像素值线性映射到分贝值：0 -> -60 dB, 255 -> 60 dB
    dB = (pixel_values / 255.0) * 120 - 60
    # 将dB转为线性响度：响度 = 10^(dB/20)
    amp_control = 10 ** (dB / 20)
    
    # 对当前频率的包络进行时间插值，获得2秒内的连续变化
    envelope = np.interp(t, time_control, amp_control)
    
    # 生成当前频率的正弦波，并乘上包络和随机相位
    comp = envelope * np.sin(2 * np.pi * freq * t + phase)
    
    # 叠加到最终信号中
    signal += comp

# ===== 归一化并写入音频文件 =====
signal /= np.max(np.abs(signal))  # 避免溢出，将幅度归一化到[-1, 1]
sf.write("output.wav", signal, fs)
print("音频已保存到 output.wav")
```