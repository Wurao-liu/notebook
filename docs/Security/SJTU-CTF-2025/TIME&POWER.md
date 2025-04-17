# TIME&POWER

## 题目描述

我发现我的设备功耗出现了异常，似乎有人在爆破我的密码……但首先，我要先登录上我的账号。

## 解题过程

正如题目描述所言，首先我们需要登录进去账号。

### 登录

我们需要正确的密码才能进去，密码全是小写字母，在尝试了几个长度之后，都是 `invalid length`，这个说起来也是运气有点好的，没尝试几次，就直接输了一堆 `a` 看了看效果，结果出来之后显示为 *checking... wrong passowrd.*。明显与之前的输出不同，发现这是十四个字母，于是有尝试了别的几个相同长度的不同字符，都为 `wrong password`。

看来长度就是十四个字母了，但我又在尝试过程中发现，如果某一个位置上的字母是正确的，那么其在 *checking* 过程中，会显示出 *(x/14)*，如果不正确，那么会直接跳过。而之前的输入了14个字母的 `a`，在 *checking* 过程中一直显示到了 *(10/14)* 都没有跳过，说明前十个字母都是正确的。所以我们确实是有点运气在的，就这样确定了前十个字符。

对于后面的几个字符，尝试写脚本，但是不太对，其给出的结果都不对，于是再次手动尝试，运气加身，试了十几分钟左右，试出来是 `admi`，于是合理猜测最后一个是 `n`，于是最后的密码就是 `aaaaaaaaaadmin`。

### 抓取

成功输入密码之后，其给出了一大段 `base64` 的字符串，太长以至于终端翻页，于是直接脚本抓取下来。

```python
import socket
import time

def capture_server_output(host, port, password):
    """登录并捕获服务器返回的所有数据"""
    try:
        # 创建连接
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.settimeout(60)  # 设置较长的超时时间，因为数据可能很大
        s.connect((host, port))
        
        print(f"成功连接到 {host}:{port}")
        
        # 接收密码提示
        initial_prompt = s.recv(1024).decode()
        print(f"服务器提示: {initial_prompt}")
        
        # 发送正确密码
        print(f"发送密码: {password}")
        s.sendall((password + '\n').encode())
        
        # 持续接收数据
        all_data = b""
        data_chunks = []
        
        print("开始接收数据...")
        
        try:
            while True:
                chunk = s.recv(8192)  # 使用大一点的缓冲区
                if not chunk:
                    print("连接关闭，数据接收完毕")
                    break
                    
                data_chunks.append(chunk)
                all_data += chunk
                print(f"已接收 {len(all_data)} 字节的数据")
                
                # 如果超过一定大小，可以停止接收
                if len(all_data) > 10 * 1024 * 1024:  # 10MB
                    print("数据超过10MB，停止接收")
                    break
        except socket.timeout:
            print("接收超时，可能数据已传输完毕")
        
        # 关闭连接
        s.close()
        
        # 保存所有接收到的数据
        with open("server_output.txt", "wb") as f:
            f.write(all_data)
            
        print(f"已将数据保存到 server_output.txt，总大小: {len(all_data)} 字节")
        
        # 尝试提取一部分文本形式查看
        try:
            text_preview = all_data[:1000].decode('utf-8', errors='replace')
            print("\n数据预览(前1000字节):")
            print(text_preview)
        except:
            print("无法解码数据为文本")
        
        return True
    
    except Exception as e:
        print(f"捕获数据时出错: {str(e)}")
        return False

def main():
    host = "instance.penguin.0ops.sjtu.cn"
    port = 18173
    password = "aaaaaaaaaadmin"
    
    print(f"=== 开始从 {host}:{port} 捕获数据 ===")
    success = capture_server_output(host, port, password)
    
    if success:
        print("\n成功捕获数据! 你可以在 server_output.txt 文件中查看完整内容")
        print("如果需要提取 base64 编码的部分，可以手动查找 'cat data.npz | base64' 标记")
    else:
        print("捕获数据失败")

if __name__ == "__main__":
    main()
```

### 解密

先将其转换为 `npz` 文件

```python
try:
    binary_data = base64.b64decode(base64_str)
                
    with open("data.npz", "wb") as f:
        f.write(binary_data)
                
    print(f"成功解码并保存为 data.npz, 大小: {len(binary_data)} 字节")
    return True
except Exception as e:
    print(f"Base64 解码失败: {str(e)}")
    return False
```

看了一下这个里面存了些什么，然后就不知所措了。感觉上没什么方向了。

然后去搜索了一下，发现这原来是一类题型，叫做 *侧信道攻击*，我就去找了一下相关的资料，发现有一个叫做 *功耗分析* 的东西。我感觉和本题比较符合，看了一下相关的解法，但是按照上面的思路去做，去找最特殊的那个峰或者谷，发现其为多条线重合在一起，并且有时候并没有明显的峰或者谷。
然后又去想各种数据特征，比如最大值最小值均值等等，甚至在对比的时候发现最大值的最小值提取出来前几个字符就是 `0ops{`，整体提取出来为 `0ops{bo}er_1q_a11_y0u_n5_d}`，虽然看着奇怪，但是也能由于那几个特殊字符都对上了，并且甚至能看出意思为 *power is all you need* 的意思，看着真的很合理哈哈！

但是就是不对，还问了一下出题人，出题人说让我再对比看看。

之后转换思路，由于开头确定为 `0ops{`，去对比这几个字符和别的字符间的差异的时候，本来那晚还是没有想法的，实在看不出来于是睡觉。第二天早上一起来再这么细看的时候，终于发现了！

其表现出来的特征是其会在一个峰下面单独出来一段，故而可以直接看见，又对比了后面几个字符，发现是合适的。于是我给每一位上的每一个字符都与其他字符做了对比画了图，然后对每一位就去肉眼判断观察。

最终得到 flag 为 `0ops{power_1s_a11_y0u_n55d}`。

下附画图脚本（有些长，并且里面有些错误的部分懒得修改，保留了找最大值的最小值的一部分，但是不影响其它）：

```python
import numpy as np
import matplotlib.pyplot as plt
import os
from collections import defaultdict
import matplotlib
matplotlib.rcParams['font.sans-serif'] = ['SimHei', 'DejaVu Sans', 'Arial Unicode MS']  # 设置字体以支持中文和特殊字符
matplotlib.rcParams['axes.unicode_minus'] = False  # 用来正常显示负号

def draw_power_comparison_by_id(npz_file_path, output_dir="power_analysis_plots"):
    """
    为每个input_id绘制不同input值对应的power曲线对比图
    
    参数:
    npz_file_path - NPZ文件路径
    output_dir - 输出图像的目录
    """
    # 创建输出目录
    if not os.path.exists(output_dir):
        os.makedirs(output_dir)
        
    # 加载NPZ文件
    try:
        data = np.load(npz_file_path, allow_pickle=True)
        print(f"成功加载NPZ文件: {npz_file_path}")
        print(f"NPZ文件中的键: {list(data.keys())}")
        
        # 提取数据
        inputs = data['input']
        input_ids = data['input_id']
        powers = data['power']
        
        # 按input_id分组
        id_to_powers = defaultdict(list)
        id_to_inputs = defaultdict(list)
        
        for i in range(len(input_ids)):
            input_id = input_ids[i]
            input_char = inputs[i]
            power_array = powers[i]
            
            if isinstance(power_array, np.ndarray) and power_array.size > 0:
                id_to_powers[input_id].append(power_array)
                id_to_inputs[input_id].append(input_char)
        
        # 为每个input_id绘制图表
        for input_id in sorted(id_to_powers.keys()):
            # 在一张图上绘制该ID所有输入字符的功耗曲线
            plt.figure(figsize=(15, 10))
            
            # 获取该ID的所有功耗曲线和输入
            power_curves = id_to_powers[input_id]
            input_chars = id_to_inputs[input_id]
            
            # 转换输入字符，确保可以显示特殊字符
            display_chars = []
            for char in input_chars:
                # 将字符转换为可读形式
                if isinstance(char, bytes):
                    try:
                        # 尝试UTF-8解码
                        display_char = char.decode('utf-8')
                    except UnicodeDecodeError:
                        # 如果解码失败，显示十六进制表示
                        display_char = f"0x{char.hex()}"
                elif isinstance(char, (int, np.integer)):
                    # 可能是ASCII码
                    try:
                        display_char = chr(char)
                    except (ValueError, OverflowError):
                        display_char = f"#{char}"
                else:
                    display_char = str(char)
                
                # 对于特殊字符，添加描述
                if display_char in '{':
                    display_char = '{' 
                elif display_char in '}':
                    display_char = '}'
                elif display_char == '_':
                    display_char = '_'
                
                display_chars.append(display_char)
            
            # 绘制每个输入字符的功耗曲线
            for i, (power_curve, display_char) in enumerate(zip(power_curves, display_chars)):
                # 确保所有曲线有相同的长度
                plt.plot(power_curve, label=f'Input: {display_char}', alpha=0.7)
            
            # 找出Power_Max最小的输入字符
            min_power_max = float('inf')
            min_power_max_input = None
            min_power_max_display = None
            
            for i, (power_curve, input_char, display_char) in enumerate(zip(power_curves, input_chars, display_chars)):
                power_max = np.max(power_curve)
                if power_max < min_power_max:
                    min_power_max = power_max
                    min_power_max_input = input_char
                    min_power_max_display = display_char
            
            plt.title(f'ID {input_id} - Power曲线对比 (最佳输入: {min_power_max_display})')
            plt.xlabel('Sample Index')
            plt.ylabel('Power')
            plt.grid(True, linestyle='--', alpha=0.7)
            
            # 由于输入字符可能很多，使用自定义的图例处理
            if len(display_chars) > 20:
                # 创建一个带颜色块的表格来显示所有输入字符
                from matplotlib.lines import Line2D
                
                # 所有可能的字符集
                alphabet = sorted(set(display_chars))
                
                # 创建自定义图例元素
                legend_elements = []
                for char in alphabet:
                    # 获取该字符在display_chars中的索引
                    indices = [i for i, dc in enumerate(display_chars) if dc == char]
                    if indices:
                        first_idx = indices[0]
                        # 获取该曲线的颜色
                        color = plt.gca().lines[first_idx].get_color()
                        legend_elements.append(Line2D([0], [0], color=color, lw=2, 
                                                     label=f"'{char}'" + (" (Best)" if char == min_power_max_display else "")))
                
                # 创建多列图例
                plt.legend(handles=legend_elements, loc='upper center', bbox_to_anchor=(0.5, -0.05),
                          fancybox=True, shadow=True, ncol=min(10, len(alphabet)))
            else:
                # 如果输入字符不多，直接使用常规图例
                plt.legend(loc='best')
            
            # 保存图表
            output_file = os.path.join(output_dir, f"id_{input_id}_power_comparison.png")
            plt.savefig(output_file, dpi=150, bbox_inches='tight')
            plt.close()
            
            print(f"已保存ID {input_id}的功耗对比图到: {output_file}")
            
            # 额外创建一个只显示最佳输入（Power_Max最小）的图表
            plt.figure(figsize=(10, 6))
            
            # 找到最佳输入的索引
            best_indices = [i for i, char in enumerate(input_chars) if char == min_power_max_input]
            if best_indices:
                best_index = best_indices[0]
                best_power = power_curves[best_index]
                
                plt.plot(best_power, 'g-', linewidth=2, label=f'最佳输入: {min_power_max_display}')
                
                # 为了对比，随机选择几个其他输入
                import random
                other_indices = [i for i in range(len(input_chars)) if input_chars[i] != min_power_max_input]
                if other_indices:
                    sample_size = min(5, len(other_indices))
                    sampled_indices = random.sample(other_indices, sample_size)
                    
                    for idx in sampled_indices:
                        plt.plot(power_curves[idx], 'r-', alpha=0.3, linewidth=1, 
                                label=f'其他输入: {display_chars[idx]}')
                
                plt.title(f'ID {input_id} - 最佳输入 "{min_power_max_display}" 的功耗曲线')
                plt.xlabel('Sample Index')
                plt.ylabel('Power')
                plt.grid(True, linestyle='--', alpha=0.7)
                plt.legend(loc='best')
                
                # 保存最佳输入图表
                best_output_file = os.path.join(output_dir, f"id_{input_id}_best_input.png")
                plt.savefig(best_output_file, dpi=150)
                plt.close()
                
                print(f"已保存ID {input_id}的最佳输入功耗图到: {best_output_file}")
        
        print("所有功耗对比图绘制完成！")
        
        # 输出可能的flag
        print("\n可能的flag:")
        flag = ""
        for input_id in sorted([int(id) for id in id_to_powers.keys() if str(id).isdigit()]):
            power_curves = id_to_powers[input_id]
            input_chars = id_to_inputs[input_id]
            
            # 找出Power_Max最小的输入字符
            min_power_max = float('inf')
            min_power_max_input = None
            
            for power_curve, input_char in zip(power_curves, input_chars):
                power_max = np.max(power_curve)
                if power_max < min_power_max:
                    min_power_max = power_max
                    min_power_max_input = input_char
            
            # 确保字符正确显示
            if isinstance(min_power_max_input, bytes):
                try:
                    char_display = min_power_max_input.decode('utf-8')
                except UnicodeDecodeError:
                    char_display = repr(min_power_max_input)
            else:
                char_display = str(min_power_max_input)
                
            flag += char_display
            print(f"ID {input_id}: {char_display}")
        
        print(f"\n完整flag: {flag}")
        
    except Exception as e:
        print(f"处理NPZ文件时出错: {e}")
        import traceback
        traceback.print_exc()

def draw_individual_char_comparison(npz_file_path, output_dir="power_analysis_plots"):
    """
    为每个input_id的每个可能的字符绘制单独的对比图,
    突出显示当前字符的曲线，其他字符用相同颜色显示为背景
    """
    # 创建输出目录
    if not os.path.exists(output_dir):
        os.makedirs(output_dir)
        
    # 加载NPZ文件
    try:
        data = np.load(npz_file_path, allow_pickle=True)
        print(f"成功加载NPZ文件: {npz_file_path}")
        print(f"NPZ文件中的键: {list(data.keys())}")
        
        # 提取数据
        inputs = data['input']
        input_ids = data['input_id']
        powers = data['power']
        
        # 按input_id分组
        id_to_powers = defaultdict(list)
        id_to_inputs = defaultdict(list)
        
        for i in range(len(input_ids)):
            input_id = input_ids[i]
            input_char = inputs[i]
            power_array = powers[i]
            
            if isinstance(power_array, np.ndarray) and power_array.size > 0:
                id_to_powers[input_id].append(power_array)
                id_to_inputs[input_id].append(input_char)
        
        # 处理每个input_id
        for input_id in sorted(id_to_powers.keys()):
            # 获取该ID的所有功耗曲线和输入
            power_curves = id_to_powers[input_id]
            input_chars = id_to_inputs[input_id]
            
            # 创建该ID的子目录
            id_dir = os.path.join(output_dir, f"id_{input_id}")
            if not os.path.exists(id_dir):
                os.makedirs(id_dir)
            
            # 转换输入字符，确保可以显示特殊字符
            display_chars = []
            for char in input_chars:
                # 将字符转换为可读形式
                if isinstance(char, bytes):
                    try:
                        display_char = char.decode('utf-8')
                    except UnicodeDecodeError:
                        display_char = f"0x{char.hex()}"
                elif isinstance(char, (int, np.integer)):
                    try:
                        display_char = chr(char)
                    except (ValueError, OverflowError):
                        display_char = f"#{char}"
                else:
                    display_char = str(char)
                
                # 对于特殊字符，确保正确显示
                if display_char in '{':
                    display_char = '{' 
                elif display_char in '}':
                    display_char = '}'
                elif display_char == '_':
                    display_char = '_'
                
                display_chars.append(display_char)
            
            # 找出Power_Max最小的输入字符
            min_power_max = float('inf')
            min_power_max_input = None
            min_power_max_display = None
            min_power_max_idx = -1
            
            for i, (power_curve, input_char, display_char) in enumerate(zip(power_curves, input_chars, display_chars)):
                power_max = np.max(power_curve)
                if power_max < min_power_max:
                    min_power_max = power_max
                    min_power_max_input = input_char
                    min_power_max_display = display_char
                    min_power_max_idx = i
            
            # 为每个可能的字符创建一个单独的图
            unique_chars = sorted(set(display_chars))
            for char in unique_chars:
                plt.figure(figsize=(12, 8))
                
                # 保存当前字符对应的索引列表
                current_char_indices = [i for i, c in enumerate(display_chars) if c == char]
                other_char_indices = [i for i, c in enumerate(display_chars) if c != char]
                
                # 先绘制其他字符的曲线（作为背景）
                for idx in other_char_indices:
                    plt.plot(power_curves[idx], color='lightgray', alpha=0.5, linewidth=1)
                
                # 然后绘制当前字符的曲线（突出显示）
                for idx in current_char_indices:
                    plt.plot(power_curves[idx], color='blue', linewidth=2, 
                           label=f'Input: {char}')
                
                # 为最佳输入（Power_Max最小的）添加标记
                if char == min_power_max_display:
                    plt.scatter([np.argmax(power_curves[min_power_max_idx])], 
                               [np.max(power_curves[min_power_max_idx])], 
                               color='green', s=100, marker='*', 
                               label="最小Power_Max点")
                
                # 设置图表标题和标签
                is_best = "(最佳输入)" if char == min_power_max_display else ""
                plt.title(f'ID {input_id} - 输入 "{char}" 的功耗曲线 {is_best}')
                plt.xlabel('Sample Index')
                plt.ylabel('Power')
                plt.grid(True, linestyle='--', alpha=0.7)
                
                # 添加图例
                if char == min_power_max_display:
                    plt.legend(loc='best')
                
                # 添加Power最大值和最小值的文字说明
                if current_char_indices:
                    idx = current_char_indices[0]  # 使用第一个匹配的索引
                    power_curve = power_curves[idx]
                    power_min = np.min(power_curve)
                    power_max = np.max(power_curve)
                    plt.figtext(0.02, 0.02, 
                               f"字符: {char}\nPower Min: {power_min:.6f}\nPower Max: {power_max:.6f}", 
                               bbox=dict(facecolor='white', alpha=0.8))
                
                # 保存图表
                safe_char = "".join([c if c.isalnum() else f"_{ord(c)}_" for c in char])
                output_file = os.path.join(id_dir, f"char_{safe_char}.png")
                plt.savefig(output_file, dpi=150, bbox_inches='tight')
                plt.close()
                
                print(f"已保存ID {input_id}的字符 '{char}' 功耗曲线图到: {output_file}")
            
            # 额外创建一个所有曲线的汇总图，以便对比
            plt.figure(figsize=(15, 10))
            
            # 使用明显不同的颜色
            colors = plt.cm.rainbow(np.linspace(0, 1, len(unique_chars)))
            color_map = {char: colors[i] for i, char in enumerate(unique_chars)}
            
            # 绘制所有曲线
            for i, (power_curve, display_char) in enumerate(zip(power_curves, display_chars)):
                plt.plot(power_curve, color=color_map[display_char], alpha=0.7, linewidth=1)
            
            # 为图例创建唯一的条目
            from matplotlib.lines import Line2D
            legend_elements = [
                Line2D([0], [0], color=color_map[char], lw=2, label=f"'{char}'" + 
                      (" (Best)" if char == min_power_max_display else ""))
                for char in unique_chars
            ]
            
            # 突出显示最佳输入
            if min_power_max_idx >= 0:
                plt.scatter([np.argmax(power_curves[min_power_max_idx])], 
                           [np.max(power_curves[min_power_max_idx])], 
                           color='green', s=100, marker='*', 
                           label="最小Power_Max点")
            
            plt.title(f'ID {input_id} - 所有输入字符的功耗曲线对比')
            plt.xlabel('Sample Index')
            plt.ylabel('Power')
            plt.grid(True, linestyle='--', alpha=0.7)
            plt.legend(handles=legend_elements, loc='upper center', 
                      bbox_to_anchor=(0.5, -0.05), fancybox=True, shadow=True, 
                      ncol=min(10, len(unique_chars)))
            
            # 保存汇总图
            summary_file = os.path.join(id_dir, "all_chars_summary.png")
            plt.savefig(summary_file, dpi=150, bbox_inches='tight')
            plt.close()
            
            print(f"已保存ID {input_id}的所有字符功耗曲线汇总图到: {summary_file}")
        
        print("所有字符对比图绘制完成！")
        
        # 输出可能的flag
        print("\n可能的flag:")
        flag = ""
        for input_id in sorted([int(id) for id in id_to_powers.keys() if str(id).isdigit()]):
            power_curves = id_to_powers[input_id]
            input_chars = id_to_inputs[input_id]
            
            # 找出Power_Max最小的输入字符
            min_power_max = float('inf')
            min_power_max_input = None
            
            for power_curve, input_char in zip(power_curves, input_chars):
                power_max = np.max(power_curve)
                if power_max < min_power_max:
                    min_power_max = power_max
                    min_power_max_input = input_char
            
            # 确保字符正确显示
            if isinstance(min_power_max_input, bytes):
                try:
                    char_display = min_power_max_input.decode('utf-8')
                except UnicodeDecodeError:
                    char_display = repr(min_power_max_input)
            else:
                char_display = str(min_power_max_input)
                
            flag += char_display
            print(f"ID {input_id}: {char_display}")
        
        print(f"\n完整flag: {flag}")
        
    except Exception as e:
        print(f"处理NPZ文件时出错: {e}")
        import traceback
        traceback.print_exc()

if __name__ == "__main__":
    # 设置文件路径
    npz_directory = r'e:\wurao\ctf\0ops_2025\Time_Power'
    
    # 查找目录中的所有NPZ文件
    npz_files = [f for f in os.listdir(npz_directory) if f.endswith('.npz')]
    
    if not npz_files:
        print(f"在 {npz_directory} 中没有找到NPZ文件")
    else:
        # 处理每个找到的NPZ文件
        for npz_file in npz_files:
            npz_file_path = os.path.join(npz_directory, npz_file)
            output_dir = os.path.join(npz_directory, f"{os.path.splitext(npz_file)[0]}_power_plots")
            
            print(f"处理文件: {npz_file}")
            draw_power_comparison_by_id(npz_file_path, output_dir)
            
        print("所有NPZ文件处理完成")

    if not npz_files:
        print(f"在 {npz_directory} 中没有找到NPZ文件")
    else:
        # 处理每个找到的NPZ文件
        for npz_file in npz_files:
            npz_file_path = os.path.join(npz_directory, npz_file)
            output_dir = os.path.join(npz_directory, f"{os.path.splitext(npz_file)[0]}_char_comparison")
            
            print(f"处理文件: {npz_file}")
            draw_individual_char_comparison(npz_file_path, output_dir)
            
        print("所有NPZ文件处理完成")
```

## 感受

这道题还是很有波折的，尽管在第一阶段已经有了运气成分，但后面还是没有很好地解决，最后算是比较波折地拿到了 flag。