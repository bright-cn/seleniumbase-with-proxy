# 在 SeleniumBase 中使用代理

[![推广](https://github.com/bright-cn/Rotating-Residential-Proxies/blob/main/50%25%20off%20promo%20%281%29.png)](https://www.bright.cn/proxy-types/residential-proxies) 

本指南介绍如何为 SeleniumBase 配置身份验证代理，以绕过限制并提高网页爬虫的成功率。

- [为什么需要 SeleniumBase](#为什么需要-seleniumbase)
- [开始使用](#开始使用)
- [调用测试](#调用测试)
    - [不使用代理](#不使用代理)
    - [代理配置](#代理配置)
    - [使用代理运行](#使用代理运行)
    - [控制你的位置](#控制你的位置)
    - [动态代理](#动态代理)
- [总结](#总结)

## 为什么需要 SeleniumBase

Selenium 不支持身份验证代理。SeleniumWire 已弃用一年多，并且已有两年多没有更新，因此解决方案是使用 [SeleniumBase](https://seleniumbase.com/)。该项目最初设计为一个封装器，用于运行 Selenium 实例，以通过 Python 执行网页自动化和测试。不过，它也允许我们通过身份验证代理运行 Selenium。

## 开始使用

首先安装 SeleniumBase：

```bash
pip install seleniumbase
```

现在编写一个测试用例来控制 Selenium 并运行 WebDriver 实例。以下代码会向 [IPinfo API](https://ipinfo.io/json) 发出请求。脚本收到 JSON 响应后，会解析响应并将其内容输出到控制台。

```python
from seleniumbase import BaseCase
from selenium.webdriver.common.by import By
import json

class ProxyTest(BaseCase):
   def test_proxy(self):
        #go to the site
        self.driver.get("https://ipinfo.io/json")
      
        #load the json response
        location_info = json.loads(self.driver.find_element(By.TAG_NAME, "body").text)

        #iterate through the dict and print its contents
        for k,v in location_info.items():
            print(f"{k}: {v}")
```

## 调用测试

让我们使用 `pytest` 而不是 `python` 来运行测试。

### 不使用代理

要在不使用代理的情况下运行测试脚本，请使用以下命令：

```bash
pytest proxy_test.py -s
```

响应将与此类似：

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

使用 `--proxy` 标志，后面跟上代理 URL。模板如下：

```
--proxy=your_proxy_url:your_proxy_port
```

#### 免费代理

以下示例使用 Bright Data 提供的[免费代理](https://brightdata.com/solutions/free-proxies)。IP 地址为 `155.54.239.64`，连接端口为 `80`。

```
--proxy=155.54.239.64:80
```

#### 身份验证代理

身份验证代理的处理方式相同。只需在 URL 中加入你的用户名和密码：

```
proxy=<YOUR_USERNAME>:<YOUR_PASSWORD>@<PROXY_URL>:<PROXY_PORT>
```

#### 身份验证代理类型

最佳的身份验证代理选项包括：

- [**住宅代理**](https://www.bright.cn/proxy-types/residential-proxies)：使用真实用户 IP，非常适合绕过机器人检测。
- [**数据中心代理**](https://www.bright.cn/proxy-types/datacenter-proxies)：速度更快且更具成本效益，但也更容易被检测。
- [**ISP 代理**](https://www.bright.cn/proxy-types/isp-proxies)：兼具两者的优势，在提供高速连接的同时保持较高的可信度。

### 使用代理运行

以下示例已配置为使用 Bright Data 的代理运行。请务必将用户名、区域名称和密码替换为你自己的凭据。

```bash
pytest proxy_test.py --proxy=brd-customer-<YOUR-USERNAME>-zone-<YOUR-ZONE-NAME>:<YOUR-PASSWORD>@brd.superproxy.io:33335 -s
```

运行后会得到以下输出：

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

### 控制你的位置

使用 Bright Data 的代理时，你可以通过 `country` 标志和两位国家/地区代码来选择位置：

```bash
pytest proxy_test.py --proxy=brd-customer-<YOUR-USERNAME>-zone-<YOUR-ZONE-NAME>:<YOUR-PASSWORD>-country-es@brd.superproxy.io:33335 -s
```

将 `es` （西班牙）用作国家/地区代码时，流量会通过位于西班牙的代理进行路由。你可以在以下输出中验证这一点。

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

详情请参阅[地理位置文档](https://docs.brightdata.com/api-reference/proxy/geolocation-targeting)。

### 动态代理

以下代码使用了一组国家/地区代码，但你也可以轻松地将它们替换为实际的代理 IP。 `countries` 保存国家/地区代码列表。接下来，我们遍历该列表，并使用全部四个国家/地区代码运行代理测试。

- `us`：美国
- `es`：西班牙
- `il`：以色列
- `gb`：英国

```python
import subprocess

#list of country codes
countries = [
    "us",
    "es",
    "il",
    "gb",
]

#iterate through the countries and make a shell command for each one
for country in countries:
    command = f"pytest proxy_test.py --proxy=brd-customer-<YOUR-USERNAME>-zone-<YOUR-ZONE-NAME>-country-{country}:<YOUR-PASSWORD>@brd.superproxy.io:33335 -s"

    #run the shell command
    subprocess.run(command, shell=True)
```

你可以将其作为常规 Python 文件运行：

```bash
python rotate_proxies.py
```

输出应与此类似：

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

SeleniumBase 为 Selenium 带来了丰富的网页爬虫功能。借助 Bright Data [行业领先的代理服务](https://www.bright.cn/proxy-types)，充分释放基于 Selenium 的抓取潜力。今天就开始免费试用！
