# 使用 SeleniumBase 的代理

[![Promo](https://github.com/luminati-io/Rotating-Residential-Proxies/blob/main/50%25%20off%20promo.png)](https://www.bright.cn/proxy-types/residential-proxies) 

本指南讲解如何在 SeleniumBase 中配置带身份验证的代理，以绕过访问限制并提升您的网页抓取成功率。

- [为什么需要 SeleniumBase](#为什么需要-seleniumbase)
- [入门](#入门)
- [运行测试](#运行测试)
  - [不使用代理](#不使用代理)
  - [代理配置](#代理配置)
    - [免费代理](#免费代理)
    - [带身份验证的代理](#带身份验证的代理)
    - [带身份验证的代理类型](#带身份验证的代理类型)
  - [使用代理运行](#使用代理运行)
  - [控制您的地理位置](#控制您的地理位置)
  - [轮换代理](#轮换代理)
- [总结](#总结)

## 为什么需要 SeleniumBase

Selenium 本身并不支持带身份验证的代理。由于 SeleniumWire 已经被弃用超过一年，并且两年内没有发布任何更新，如今的解决方案是使用 [SeleniumBase](https://seleniumbase.com/)。最初设计 SeleniumBase 的目的，是基于 Selenium 做 Python 的 Web 自动化和测试。但对我们而言，它允许通过带身份验证的代理来运行 Selenium。

## 入门

首先安装 SeleniumBase：

```bash
pip install seleniumbase
```

接下来编写一个测试用例来控制 Selenium 并运行 webdriver 实例。以下代码会向 [IPinfo API](https://ipinfo.io/json) 发送请求。一旦脚本收到 JSON 响应，就会解析内容并将其打印到控制台。

```python
from seleniumbase import BaseCase
from selenium.webdriver.common.by import By
import json

class ProxyTest(BaseCase):
    def test_proxy(self):
        # 前往目标网站
        self.driver.get("https://ipinfo.io/json")
        
        # 加载 JSON 响应
        location_info = json.loads(self.driver.find_element(By.TAG_NAME, "body").text)

        # 遍历字典并打印其中的内容
        for k, v in location_info.items():
            print(f"{k}: {v}")
```

## 运行测试

我们使用 `pytest` 来运行，而不是直接通过 `python`。

### 不使用代理

如果想不使用代理来运行测试脚本，可以执行以下命令：

```bash
pytest proxy_test.py -s
```

输出结果类似如下：

```
=================================================== test session starts ===================================================
platform linux -- Python 3.10.12, pytest-8.3.4, pluggy-1.5.0
rootdir: /home/nultinator/clients/bright-data/seleniumbase-proxies
plugins: html-4.0.2, metadata-3.1.1, anyio-4.0.0, seleniumbase-4.34.6, ordering-0.6, rerunfailures-15.0, xdist-3.6.1
collected 1 item                                                                                                          

proxy_test.py ip: 23.28.108.255
hostname: d28-23-255-108.dim.wideopenwest.com
city: Westland
region: Michigan
country: US
loc: 42.3242,-83.4002
org: AS12083 WideOpenWest Finance LLC
postal: 48185
timezone: America/Detroit
readme: https://ipinfo.io/missingauth
.

==================================================== 1 passed in 1.01s ====================================================
```

### 代理配置

使用 `--proxy` 标志并紧跟代理 URL。格式如下：

```
--proxy=your_proxy_url:your_proxy_port
```

#### 免费代理

下面是一个使用 Bright Data [免费代理](https://www.bright.cn/solutions/free-proxies) 的示例。此处 IP 地址为 `155.54.239.64`，端口为 `80`：

```
--proxy=155.54.239.64:80
```

#### 带身份验证的代理

对于带身份验证的代理，配置方式相同。只需在 URL 中包含用户名和密码即可：

```
--proxy=<YOUR_USERNAME>:<YOUR_PASSWORD>@<PROXY_URL>:<PROXY_PORT>
```

#### 带身份验证的代理类型

常用的带身份验证代理主要有：

- [住宅代理](https://www.bright.cn/proxy-types/residential-proxies)：使用真实用户 IP，适合绕过机器人检测。  
- [数据中心代理](https://www.bright.cn/proxy-types/datacenter-proxies)：速度快且成本低，但更容易被识别。  
- [ISP 代理](https://www.bright.cn/proxy-types/isp-proxies)：兼具两者优势，速度快且信任度高。  

### 使用代理运行

下面的示例展示了如何使用 Bright Data 的代理。请将其中的用户名、zone 名称以及密码替换成您自己的。

```bash
pytest proxy_test.py --proxy=brd-customer-<YOUR-USERNAME>-zone-<YOUR-ZONE-NAME>:<YOUR-PASSWORD>@brd.superproxy.io:33335 -s
```

运行后输出结果如下：

```
=================================================== test session starts ===================================================
platform linux -- Python 3.10.12, pytest-8.3.4, pluggy-1.5.0
rootdir: /home/nultinator/clients/bright-data/seleniumbase-proxies
plugins: html-4.0.2, metadata-3.1.1, anyio-4.0.0, seleniumbase-4.34.6, ordering-0.6, rerunfailures-15.0, xdist-3.6.1
collected 1 item

proxy_test.py ip: 144.202.4.246
hostname: 144-202-4-246.lum-int.io
city: Piscataway
region: New Jersey
country: US
loc: 40.4993,-74.3990
org: AS20473 The Constant Company, LLC
postal: 08854
timezone: America/New_York
readme: https://ipinfo.io/missingauth
.

==================================================== 1 passed in 3.25s ====================================================
```

### 控制您的地理位置

使用 Bright Data 代理时，可以通过 `country` 参数加上两位字母的国家代码来指定地理位置。例如：

```bash
pytest proxy_test.py --proxy=brd-customer-<YOUR-USERNAME>-zone-<YOUR-ZONE-NAME>:<YOUR-PASSWORD>-country-es@brd.superproxy.io:33335 -s
```

当设置为 `es`（西班牙）时，请求会通过西班牙的代理节点。可以通过如下输出来验证：

```
=================================================== test session starts ===================================================
platform linux -- Python 3.10.12, pytest-8.3.4, pluggy-1.5.0
rootdir: /home/nultinator/clients/bright-data/seleniumbase-proxies
plugins: html-4.0.2, metadata-3.1.1, anyio-4.0.0, seleniumbase-4.34.6, ordering-0.6, rerunfailures-15.0, xdist-3.6.1
collected 1 item

proxy_test.py ip: 176.119.14.158
city: Paracuellos de Jarama
region: Madrid
country: ES
loc: 40.5035,-3.5278
org: AS203020 HostRoyale Technologies Pvt Ltd
postal: 28860
timezone: Europe/Madrid
readme: https://ipinfo.io/missingauth
.

==================================================== 1 passed in 3.98s ====================================================
```

更多详情请参考 [地理位置文档](https://docs.brightdata.com/api-reference/proxy/geolocation-targeting)。

### 轮换代理

下面的示例使用了一组国家代码，您也可以使用具体的代理 IP 来替代。`countries` 列表中定义了国家代码，然后我们遍历该列表，对所有四个国家依次进行代理测试。

- `us`：美国  
- `es`：西班牙  
- `il`：以色列  
- `gb`：英国  

```python
import subprocess

# 国家代码列表
countries = [
    "us",
    "es",
    "il",
    "gb",
]

# 遍历每个国家代码并构建对应的 shell 命令
for country in countries:
    command = f"pytest proxy_test.py --proxy=brd-customer-<YOUR-USERNAME>-<YOUR-ZONE-NAME>-country-{country}:[email protected]:33335 -s"

    # 运行 shell 命令
    subprocess.run(command, shell=True)
```

之后直接用下面的命令来运行：

```bash
python rotate_proxies.py
```

输出类似这样：

```
(Linux uses --headless by default. To override, use --headed / --gui. For Xvfb mode instead, use --xvfb. Or you can hide this info by using --headless / --headless2 / --uc.)
=================================================== test session starts ===================================================
platform linux -- Python 3.10.12, pytest-8.3.4, pluggy-1.5.0
rootdir: /home/nultinator/clients/bright-data/seleniumbase-proxies
plugins: html-4.0.2, metadata-3.1.1, anyio-4.0.0, seleniumbase-4.34.6, ordering-0.6, rerunfailures-15.0, xdist-3.6.1
collected 1 item                                                                                                          

proxy_test.py ip: 164.90.142.33
city: Clifton
region: New Jersey
country: US
loc: 40.8344,-74.1377
org: AS14061 DigitalOcean, LLC
postal: 07014
timezone: America/New_York
readme: https://ipinfo.io/missingauth
.

==================================================== 1 passed in 3.84s ====================================================
(Linux uses --headless by default. To override, use --headed / --gui. For Xvfb mode instead, use --xvfb. Or you can hide this info by using --headless / --headless2 / --uc.)
=================================================== test session starts ===================================================
platform linux -- Python 3.10.12, pytest-8.3.4, pluggy-1.5.0
rootdir: /home/nultinator/clients/bright-data/seleniumbase-proxies
plugins: html-4.0.2, metadata-3.1.1, anyio-4.0.0, seleniumbase-4.34.6, ordering-0.6, rerunfailures-15.0, xdist-3.6.1
collected 1 item                                                                                                          

proxy_test.py ip: 5.180.9.15
city: Madrid
region: Madrid
country: ES
loc: 40.4066,-3.6724
org: AS203020 HostRoyale Technologies Pvt Ltd
postal: 28007
timezone: Europe/Madrid
readme: https://ipinfo.io/missingauth
.

==================================================== 1 passed in 3.60s ====================================================
(Linux uses --headless by default. To override, use --headed / --gui. For Xvfb mode instead, use --xvfb. Or you can hide this info by using --headless / --headless2 / --uc.)
=================================================== test session starts ===================================================
platform linux -- Python 3.10.12, pytest-8.3.4, pluggy-1.5.0
rootdir: /home/nultinator/clients/bright-data/seleniumbase-proxies
plugins: html-4.0.2, metadata-3.1.1, anyio-4.0.0, seleniumbase-4.34.6, ordering-0.6, rerunfailures-15.0, xdist-3.6.1
collected 1 item                                                                                                          

proxy_test.py ip: 64.79.233.151
city: Tel Aviv
region: Tel Aviv
country: IL
loc: 32.0809,34.7806
org: AS9009 M247 Europe SRL
timezone: Asia/Jerusalem
readme: https://ipinfo.io/missingauth
.

==================================================== 1 passed in 3.36s ====================================================
(Linux uses --headless by default. To override, use --headed / --gui. For Xvfb mode instead, use --xvfb. Or you can hide this info by using --headless / --headless2 / --uc.)
=================================================== test session starts ===================================================
platform linux -- Python 3.10.12, pytest-8.3.4, pluggy-1.5.0
rootdir: /home/nultinator/clients/bright-data/seleniumbase-proxies
plugins: html-4.0.2, metadata-3.1.1, anyio-4.0.0, seleniumbase-4.34.6, ordering-0.6, rerunfailures-15.0, xdist-3.6.1
collected 1 item                                                                                                          

proxy_test.py ip: 185.37.3.107
city: London
region: England
country: GB
loc: 51.5085,-0.1257
org: AS9009 M247 Europe SRL
postal: E1W
timezone: Europe/London
readme: https://ipinfo.io/missingauth
.

==================================================== 1 passed in 2.90s ====================================================
```

## 总结

SeleniumBase 为 Selenium 提供了更多的网页抓取能力。结合 Bright Data 的[行业领先代理服务](https://www.bright.cn/proxy-types)，您可以充分释放基于 Selenium 的采集潜力。立即开始免费试用吧！
