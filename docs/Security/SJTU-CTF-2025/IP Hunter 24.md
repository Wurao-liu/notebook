# IP Hunter 24
## IP Hunter 24_flag1

### 题目描述

使用32个不同/8网段的IPv4地址访问任务网址可以获得flag1！

### 解题过程

看到了这个题，懵了一下，试了试伪造 http 头，发现不可行，感觉得要真实ip。于是对于 flag1，我直接使用了最朴素暴力的方法，那就是掏出自己各种机场的多个节点，硬生生凑了32个。不知道这算不算预期解（

## IP Hunter 24_flag2

### 题目描述

使用128+1个不同/8网段的IPv4地址访问任务网址可以获得flag2!

### 解题过程

对于如此多的网段，用自己的节点是不太现实的。但是之前的尝试已经表明伪造 http 头是不可行的，所以我想到了代理池，直接用了一个免费公开的[代理池](https://proxy.scdn.io/)，然后根据其 API 接口文档，写了一个脚本来自动拉取并访问目标网站，由于其代理池的 IP 地址是随机的，但是大多数的 IP 地址都是在同一个网段的，所以效率到后期比较低下，最终试了试，拿了156个网段后，感觉很难增加了，遂结束。下附脚本：

```python
import requests
import time
import re
import random
import threading
import datetime
import os
from typing import List, Set, Tuple, Dict
from concurrent.futures import ThreadPoolExecutor, as_completed

# 禁用SSL警告
import urllib3
urllib3.disable_warnings()

# 全局变量
tried_proxies = set()
occupied_networks = set()
PROGRESS_FILE = "progress.txt"
successful_networks = set()

def save_progress():
    """保存当前进度到文件"""
    now = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    with open(PROGRESS_FILE, "w", encoding="utf-8") as f:
        f.write(f"已占领网段: {sorted(list(occupied_networks))}\n")
        f.write(f"数量: {len(occupied_networks)}\n")
        f.write(f"保存时间: {now}\n")

def load_progress() -> Set[int]:
    """从文件加载进度"""
    if not os.path.exists(PROGRESS_FILE):
        return set()
    
    try:
        with open(PROGRESS_FILE, "r", encoding="utf-8") as f:
            content = f.read()
            match = re.search(r'已占领网段: \[([\d, ]+)\]', content)
            if match:
                networks_str = match.group(1)
                networks = [int(n.strip()) for n in networks_str.split(',') if n.strip().isdigit()]
                return set(networks)
    except Exception as e:
        print(f"加载进度失败: {e}")
    
    return set()

def generate_direct_ips(network_segments: List[int], count_per_segment: int = 10) -> List[str]:
    """为未占领网段生成随机IP进行尝试"""
    direct_ips = []
    # 优先处理前20个网段，生成更多IP
    priority_segments = network_segments[:20]
    
    # 给每个优先网段生成更多IP
    for segment in priority_segments:
        for _ in range(count_per_segment):
            second = random.randint(0, 255)
            third = random.randint(0, 255)
            fourth = random.randint(1, 254)
            ip = f"{segment}.{second}.{third}.{fourth}"
            for port in [80, 8080, 3128, 8888, 9999, 1080, 8081, 3000, 8000]:
                direct_ips.append(f"{ip}:{port}")
    
    # 随机选择一些剩余网段
    remaining = [n for n in network_segments[20:] if n not in priority_segments]
    if remaining:
        sample_size = min(30, len(remaining))
        for segment in random.sample(remaining, sample_size):
            for _ in range(5):  # 为非优先网段生成少量IP
                second = random.randint(0, 255)
                third = random.randint(0, 255)
                fourth = random.randint(1, 254)
                ip = f"{segment}.{second}.{third}.{fourth}"
                for port in [80, 8080, 3128]:
                    direct_ips.append(f"{ip}:{port}")
    
    random.shuffle(direct_ips)
    return direct_ips

def test_proxy(proxy: str, target_url: str, params: dict) -> dict:
    """测试代理"""
    proxies = {
        'http': f'http://{proxy}',
        'https': f'http://{proxy}'
    }
    
    try:
        network = int(proxy.split(':')[0].split('.')[0])
    except Exception:
        network = 0
    
    try:
        headers = {
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36',
            'Accept': 'text/html,application/xhtml+xml,application/xml',
            'Accept-Language': 'zh-CN,zh;q=0.9,en;q=0.8',
            'Connection': 'keep-alive'
        }
        
        response = requests.get(
            target_url, 
            params=params, 
            proxies=proxies, 
            headers=headers,
            timeout=2.5,  # 固定超时时间，避免过长等待
            verify=False
        )
        
        if "占领的/8网段数量" in response.text:
            occupied, count = parse_occupied_networks(response.text)
            return {'success': True, 'ip': proxy, 'network': network, 'occupied': occupied, 'count': count}
        else:
            return {'success': False, 'error': "响应内容不包含预期内容", 'network': network}
    except Exception:
        return {'success': False, 'error': "请求失败", 'network': network}

def parse_occupied_networks(html_content: str) -> Tuple[List[int], int]:
    """从HTML响应中解析已占领的网段和总数"""
    try:
        count_match = re.search(r'占领的/8网段数量：(\d+) / 256', html_content)
        count = int(count_match.group(1)) if count_match else 0
        
        unoccupied_match = re.search(r'未占领的/8网段：([\d, ]+)', html_content)
        if unoccupied_match:
            unoccupied_str = unoccupied_match.group(1)
            unoccupied = [int(n.strip()) for n in unoccupied_str.split(',') if n.strip().isdigit()]
            occupied = [n for n in range(256) if n not in unoccupied]
            return occupied, count
    except Exception as e:
        print(f"解析HTML失败: {e}")
    
    return [], 0

def get_current_status(url: str, params: dict) -> Set[int]:
    """获取当前已占领的网段"""
    try:
        headers = {
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36',
        }
        response = requests.get(url, params=params, headers=headers, timeout=10)
        occupied, _ = parse_occupied_networks(response.text)
        return set(occupied)
    except Exception as e:
        print(f"获取当前状态失败: {e}")
        return set()

def parallel_proxy_testing(proxies: List[str], url: str, params: dict, max_workers: int = 80) -> Tuple[Set[int], int]:
    """并行测试多个代理，使用ThreadPoolExecutor优化线程管理"""
    global occupied_networks, tried_proxies
    if not proxies:
        return occupied_networks, 0

    success_count = 0
    lock = threading.Lock()

    def worker(proxy: str):
        nonlocal success_count
        global occupied_networks
        
        # 若已尝试则跳过
        with lock:
            if proxy in tried_proxies:
                return
            tried_proxies.add(proxy)
        
        result = test_proxy(proxy, url, params)
        if result.get('success'):
            with lock:
                new_occupied = set(result.get('occupied', []))
                if len(new_occupied) > len(occupied_networks):
                    added = new_occupied - occupied_networks
                    success_count += 1
                    print(f"代理 {proxy} 成功，新增 {len(added)} 个网段！当前已占领 {len(new_occupied)} 个网段")
                    
                    if added:
                        print(f"新增网段: {sorted(list(added))}")
                        for net in added:
                            successful_networks.add(net)
                    
                    occupied_networks = new_occupied
                    # 只在新增5个以上网段时保存进度，减少I/O操作
                    if len(added) >= 2:
                        save_progress()

    # 使用线程池并行执行
    with ThreadPoolExecutor(max_workers=max_workers) as executor:
        futures = [executor.submit(worker, p) for p in proxies]
        for _ in as_completed(futures):
            pass

    return occupied_networks, success_count

def main():
    global occupied_networks, tried_proxies
    
    url = "http://141.148.189.179:40529/collect.php"
    params = {"id": "ppwwuxNH7OxT7bEL"}
    
    saved_networks = load_progress()
    if saved_networks:
        print(f"从文件加载了 {len(saved_networks)} 个已占领网段")
    
    # 获取当前状态
    current_networks = get_current_status(url, params)
    occupied_networks = saved_networks.union(current_networks) if current_networks else saved_networks
    
    print(f"当前已占领 {len(occupied_networks)}/256 个网段")
    print(f"已占领网段: {sorted(list(occupied_networks))}")
    
    target_count = 256
    consecutive_failures = 0
    max_failures = 3
    thread_count = 80  # 直接使用较高线程数
    
    while len(occupied_networks) < target_count:
        print(f"\n当前进度: {len(occupied_networks)}/{target_count}")
        unoccupied = [n for n in range(256) if n not in occupied_networks]
        unoccupied.sort()  # 按顺序排列未占领网段
        print(f"剩余未占领网段: {len(unoccupied)} 个")
        
        if consecutive_failures >= max_failures:
            tried_proxies.clear()  # 清空已尝试代理记录
            print(f"连续{max_failures}次未新增网段，清空已尝试代理记录")
            consecutive_failures = 0
        
        # 为未占领网段生成大量IP直接尝试
        proxies = generate_direct_ips(unoccupied, count_per_segment=15)
        if not proxies:
            print("生成代理失败，等待1秒后重试...")
            time.sleep(1)
            consecutive_failures += 1
            continue
        
        print(f"生成了 {len(proxies)} 个直接IP尝试，使用 {thread_count} 个线程")
        _, success_count = parallel_proxy_testing(proxies, url, params, max_workers=thread_count)
        
        if success_count == 0:
            consecutive_failures += 1
            print(f"本轮未新增网段，连续失败次数: {consecutive_failures}/{max_failures}")
            
            # 每隔几次失败检查一下当前实际状态
            if consecutive_failures % 2 == 0:
                print("检查当前实际占领状态...")
                current_occupied = get_current_status(url, params)
                if current_occupied and len(current_occupied) > len(occupied_networks):
                    diff = len(current_occupied) - len(occupied_networks)
                    print(f"实际状态检测到 {diff} 个新增网段")
                    occupied_networks = current_occupied
                    print(f"当前实际已占领 {len(occupied_networks)}/256 个网段")
                    print(f"已占领网段: {sorted(list(occupied_networks))}")
                    save_progress()
                    consecutive_failures = 0
        else:
            consecutive_failures = 0
        
        # 固定短暂等待时间
        wait_time = 0.5
        print(f"等待 {wait_time:.1f} 秒后继续...")
        time.sleep(wait_time)

if __name__ == "__main__":
    try:
        main()
    except KeyboardInterrupt:
        print("\n程序被中断，保存当前进度...")
        save_progress()
```

