---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "【Python 筆記】Thread Process"
subtitle: ""
summary: ""
authors: []
tags: []
categories: []
date: 2020-03-16T14:59:11+08:00
lastmod: 2020-03-16T14:59:11+08:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

# Thread vs Process

![image](/post_images/0318-1.png)
- - -
### Thread
首先，先看一個範例，這段程式會對urls中的所有url，發起Request，並將Response存入results

    import urllib.request
    import timeit

    urls = [
      'http://www.python.org',
      'https://docs.python.org/3/',
      'https://docs.python.org/3/whatsnew/3.7.html',
      'https://docs.python.org/3/tutorial/index.html',
      'https://docs.python.org/3/library/index.html',
      'https://docs.python.org/3/reference/index.html',
      'https://docs.python.org/3/using/index.html',
      'https://docs.python.org/3/howto/index.html',
      'https://docs.python.org/3/installing/index.html',
      'https://docs.python.org/3/distributing/index.html',
      'https://docs.python.org/3/extending/index.html',
      'https://docs.python.org/3/c-api/index.html',
      'https://docs.python.org/3/faq/index.html'
      ]


    def send_request_and_save(url, results):
        start_time = timeit.default_timer()
        with urllib.request.urlopen(url) as src:
            print(f"request {url} ...", end=" ")
            results.append(src)

        stop_time = timeit.default_timer()
        print(f"cost: {stop_time - start_time} seconds")


    def main():
        start_time = timeit.default_timer()

        results = []
        for url in urls:
            send_request_and_save(url, results)

        stop_time = timeit.default_timer()
        print(f"Total time cost: {stop_time - start_time} seconds")

        print(results)


    if __name__ == "__main__":
        main()
##### 執行結果
    request http://www.python.org ... cost: 0.315345333 seconds
    request https://docs.python.org/3/ ... cost: 0.13963293499999996 seconds
    request https://docs.python.org/3/whatsnew/3.7.html ... cost: 0.29755077100000005 seconds
    request https://docs.python.org/3/tutorial/index.html ... cost: 0.22821731700000003 seconds
    request https://docs.python.org/3/library/index.html ... cost: 0.19573783899999997 seconds
    request https://docs.python.org/3/reference/index.html ... cost: 0.17742557400000014 seconds
    request https://docs.python.org/3/using/index.html ... cost: 0.1689271779999999 seconds
    request https://docs.python.org/3/howto/index.html ... cost: 0.32504800899999986 seconds
    request https://docs.python.org/3/installing/index.html ... cost: 0.20428261099999978 seconds
    request https://docs.python.org/3/distributing/index.html ... cost: 0.18745772300000008 seconds
    request https://docs.python.org/3/extending/index.html ... cost: 0.20186852600000016 seconds
    request https://docs.python.org/3/c-api/index.html ... cost: 0.2613879410000002 seconds
    request https://docs.python.org/3/faq/index.html ... cost: 0.15429975399999973 seconds
    Total time cost: 2.857409493 seconds
    [<http.client.HTTPResponse object at 0x102c83c90>, <http.client.HTTPResponse object at 0x102c83e50>, ...]

觀察結果發現，每個Request都需等到Response，程式才會繼續往後執行，嘗試使用multi-threads改寫這段程式
    
    def send_request_and_save():
        start_time = timeit.default_timer()
        with ThreadPoolExecutor(8) as executor:
            results = executor.map(urllib.request.urlopen, urls)

        stop_time = timeit.default_timer()
        print(f"cost: {stop_time - start_time} seconds")

        return results


    def main():
        start_time = timeit.default_timer()

        results = send_request_and_save()

        stop_time = timeit.default_timer()
        print(f"Total time cost: {stop_time - start_time} seconds")

        print([result for result in results])

##### 執行結果
    cost: 0.43964078500000003 seconds
    Total time cost: 0.43971204099999994 seconds
    [<http.client.HTTPResponse object at 0x10b857050>, <http.client.HTTPResponse object at 0x10b850650>,  ...]
由於原本的作法，大部分的時間用在等待Response，透過ThreadPoolExecutor異步執行後，大幅縮短了時間  

### Process
接下來看一段大量計算的程式，其中會呼叫3次large_sum，並將他們加總起來

    import timeit

    def large_calculate(magic_num):
        start_time = timeit.default_timer()

        total = 0
        for i in range(magic_num):
            for j in range(magic_num):
                for k in range(magic_num):
                    total += k

        print(f"when magic_num is {magic_num}, total = {total}")
        stop_time = timeit.default_timer()
        print(f"cost: {stop_time - start_time} seconds")

        return total


    def main():
        start_time = timeit.default_timer()

        large_one = large_calculate(300)
        large_two = large_calculate(400)
        large_three = large_calculate(500)

        print(f"large sum = {large_one + large_two + large_three}")

        stop_time = timeit.default_timer()
        print(f"Total time cost: {stop_time - start_time} seconds")


    if __name__ == "__main__":
        main()
##### 執行結果
    when magic_num is 300, total = 4036500000
    cost: 1.0685170080000002 seconds
    when magic_num is 400, total = 12768000000
    cost: 2.7581088620000003 seconds
    when magic_num is 500, total = 31187500000
    cost: 5.491697975 seconds
    large sum = 47992000000
    Total time cost: 9.318362431 seconds

接下來用multi-processes的方式改寫

    def main():
        start_time = timeit.default_timer()
        with Pool(4) as p:
            result = p.map(large_calculate, (300, 400, 500))

        print(f"large sum = {sum(result)}")
        stop_time = timeit.default_timer()
        print(f"Total time cost: {stop_time - start_time} seconds")

##### 執行結果
    when magic_num is 300, total = 4036500000
    cost: 1.163287602 seconds
    when magic_num is 400, total = 12768000000
    cost: 2.883095961 seconds
    when magic_num is 500, total = 31187500000
    cost: 5.718001267 seconds
    large sum = 47992000000
    Total time cost: 5.772997638 seconds

我們可以發現，三個計算是同時開始進行的，因此可以省下大量的時間  

#### 比較

|                      | 針對      |  使用情境 |
|----------------------|-----------|-----------|
| Thread(concurrency)  | I/O bound | 爬蟲      |
| Process(parallelism) | CPU bound | 大量計算  |

- - -
#### 延伸關鍵字
_lock_, _GIL_, *Queue*, *How they share memory and communicate*  

### 參考資料
1. [The Why, When, and How of Using Python Multi-threading and Multi-Processing](https://medium.com/towards-artificial-intelligence/the-why-when-and-how-of-using-python-multi-threading-and-multi-processing-afd1b8a8ecca)
2. [concurrent.futures — 創立非同步任務 — 你所不知道的 Python 標準函式庫用法 06](https://blog.louie.lu/2017/08/01/%E4%BD%A0%E6%89%80%E4%B8%8D%E7%9F%A5%E9%81%93%E7%9A%84-python-%E6%A8%99%E6%BA%96%E5%87%BD%E5%BC%8F%E5%BA%AB%E7%94%A8%E6%B3%95-06-concurrent-futures/)
3. [Multiprocessing Vs. Threading In Python: What You Need To Know.](https://timber.io/blog/multiprocessing-vs-multithreading-in-python-what-you-need-to-know/)