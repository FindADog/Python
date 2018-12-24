 小规模爬虫爬取知乎一万个用户信息 
===============
一.爬虫简介
------
+ **网络爬虫(又被称为网页蜘蛛，网络机器人，在FOAF社区中间，更经常的称为网页追逐者)，是一种按照一定的规则，自动的抓取万维网信息的程序或者脚本。另外一些不常使用的名字还有蚂蚁，自动索引，模拟程序或者蠕虫。**<br>

二.为什么python适合编写爬虫
-----
**说多了都是扯谈，，python为什么适合写爬虫，这个问题网络上一大堆回答，因为兴趣就够了。而且python语言越来愈火热，专业没有开设python这门语言，便利用爬虫来学习一下python.**



三.准备工作
----------
学习一门语言需要学习语法吗？用我们web程序设计余老师的话来说，“语法不需要学，完全就是查字典嘛，大学生还学要学习如何查字典吗？？”虽然在Web程序设计和架构这门课上不喜欢听余老师上课（不是老师教的不好，只是我没调整过来，不过貌似我上其他的课程也不认真）

<img src="https://github.com/FindADog/Python/blob/master/gtihub%E5%9B%BE%E7%89%87/%E5%9B%BE%E7%89%87.jpg"></img>

**但是**学习语言一直抱着语法书在那里啃意义不大，上网课了解了一下大致的python语法，列表，字典，元组啥的就开始了Python爬虫的编写，从图书馆借了一本书，学习了一个星期，现在终于能编写最简单的爬虫了，以下便是大致流程。**（软件python3.6加pycharm）**



四.编写流程
--

### 1.爬虫的工作流程示意图
<img src="https://github.com/FindADog/Python/blob/master/gtihub%E5%9B%BE%E7%89%87/%E7%88%AC%E8%99%AB%E8%BF%90%E8%A1%8C%E6%88%AA%E5%9B%BE.PNG"></img>


### 2.过程介绍
+ **Url管理器（UrlManger)**    ---从爬虫调度端获取入口爬虫，然后进行新的url的获取和存储，以及旧的url的保存，并且需要给下载器提供可用的url。
+ **Url下载器（HtmlDownload）**---从爬虫调度端获取网页然后将网页的内容下载下来，交给调度端然后进行解析。
+  **Html解析器（HtmlParser)**---从爬虫调度端获得需要解析的网页内容，解析出需要的新的url和需要的特定数据。
+  **Html输出其器（DataOutPut)**---获取完数据将其输出为html格式（小量数据保存）保存到本地。
+  **爬虫调度端（Spider)**---负责控制整个爬虫爬取数据过程的数据调度和控制。


五.所用的（在以前的学习中未见过的）工具解读
---
### 1.requests库：
  + python最让我耳目一新的地方就是引入大量的库了，requests库也是利用pip install 下载安装的，利用requests可以简单的对url网站进行请求，并利用respose变量获取响应
### 2. codecs模块
+ python编码是将其他的编码先利用decode将一种编码转化为unicode然后利用encode将unicode转化为另一种编码，codecs模块便是来进行转码的
### 3.beautifulsoup（美丽汤）
+ 用在解析器中，是一个相当强大，简单的分析html页面元素的库，可以直接对DOM结构进行抓取，抓取标签，div，类名，文本，很简单易学，看个几分钟就了解基本用法了。
### 4.re（正则表达式模块）
+ 利用正则表达式，进行字符串的解析，得到自己想要的结果，个人觉得，正则表达式很重要，但感觉还是有一点绕的，想要熟练掌握含需要记住字符匹配规则



六.源代码编写
-----
### 1.UrlManager

**声明俩个url集合为set()类型达到自动去重的功能，以免进入死循环**



    class UrlManager(object):
        def __init__(self):
            '''
            初始化俩个去重的set（）
            '''
            self.new_urls=set()
            self.old_urls=set()

        def add_new_url(self,url):
            if url is None:
                return
            if url not in self.new_urls and url not in self.old_urls:
                self.new_urls.add(url)


        def  add_new_urls(self,urls):
            '''
            添加url集合
            :param urls:
            :return:
            '''
            if urls is None or len(urls) == 0:
                return
            for url in urls:
                self.add_new_url(url)


        def get_new_url(self):
            url=self.new_urls.pop()
            self.old_urls.add(url)
            return url




        def old_urls_size(self):
            return len(self.old_urls)


        def has_new_urls(self):
            return len(self.new_urls) != 0




### 2. HtmlDownload




    #coding  = uf-8

    import requests


    class HtmlDownload(object):
        def download(self,url):
            if url is None:
                print("crawl fail")
                return
            user_agent = 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) '
            'Chrome/51.0.2704.63 Safari/537.36'
            headers = {"User-Agent": user_agent}
            r = requests.get(url, headers=headers)
            if r.status_code == 200:
                r.encoding = 'utf-8'
                return r.text
            return None


### 3.HtmlParser

**返回需要的新的url和页面数据**



    from bs4  import BeautifulSoup
    import urllib

    class HtmlParser(object):
        def parser(self,url,content):
            '''
            返回下载页面的urls，和内容
            :param url:
            :param content:
            :return:
            '''
            if url is None or content is None:
                return None
            soup=BeautifulSoup(content,"html.parser",from_encoding="utf-8")
            urls=self.get_urls(url,soup)
            datas=self.get_datas(url,soup)
            return urls,datas


        def get_urls(self,url,soup):
            '''
            解析页面的内容 ，解析出新的url
            :param url:
            :param soup:
            :return:
            '''
            new_urls=set()
            url_chip="http:"
            links=soup.find_all('a',class_="UserLink-link")
            for link in links:
                link=link["href"]
                full_link=url_chip+link+"/following"
                new_urls.add(full_link)
            return new_urls



        def get_datas(self,url,soup):
            '''
            返回指定页面的数据，用户名啥的
            :param url:
            :param soup:
            :return:
            '''
            data={}
            if soup.find(class_="ProfileHeader-tips") is not None:#如果个人信息不存在
                if soup.find("span", class_="ProfileHeader-name") is not None:#名字不存在或个人宣言不存在
                    data["Username"] = soup.find("span", class_="ProfileHeader-name").get_text()
                else:
                    data["Username"] = "无信息"
                if soup.find("span", class_="RichText ztext ProfileHeader-headline") is not None:
                    data["Says"]=soup.find("span",class_="RichText ztext ProfileHeader-headline").get_text()
                else:
                    data["Says"] = "无信息"
                return data
            if soup.find("span",class_="ProfileHeader-name") is not None:
                data["Username"] = soup.find("span",class_="ProfileHeader-name").get_text()
            else:
                data["Username"] = "无信息"

            if soup.find("span",class_="RichText ztext ProfileHeader-headline") is not None:
                data["Says"] = soup.find("span",class_="RichText ztext ProfileHeader-headline").get_text()
            else:
                data["Says"] = "无信息"
            items=soup.find_all(class_="ProfileHeader-infoItem")
            i=1
            for item in items:
                key="关键字"+str(i)+":"
                i=i+1
                value=item.get_text()
                data[key]=value
            return data



### 4.DataOutPut（以html格式输出数据）


    #coding = utf-8


    import codecs

    class DataOutPut(object):
        def __init__(self):
            self.data=[]

        def store_data(self,datas):
            '''
            存储信息
            :param datas:
            :return:
            '''
            if datas is not None and len(datas)>0:
                self.data.append(datas)

        def output_html(self):
            '''
            将页面输出为html形式
            :return:
            '''
            fout=codecs.open("output.html","w",encoding="utf-8")
            fout.write("<html>")
            fout.write("<body>")
            fout.write("<table>")
            for te in self.data:
                fout.write("<tr>")
                fout.write("<td>用户名：%s</td>"%te.pop("Username"))
                fout.write("<td>个人宣言：%s</td>" % te.pop("Says"))
                for key,value in te.items():
                    fout.write("<td>%(key)s %(value)s</td>" %{'key':key,'value':value})
                fout.write("</tr>")
            fout.write("</table>")
            fout.write("</body>")
            fout.write("</html>")


### 5.爬虫调度器（Spider）


    # coding = utf-8
    from UrlManager import UrlManager
    from htmldownload import HtmlDownload
    from htmlparser import HtmlParser
    from dataoutput import DataOutPut
    import winsound


    class SpiderMain(object):
        def __init__(self):
            '''
            初始化，四个管理器
            '''
            self.urlmanager = UrlManager()
            self.download = HtmlDownload()
            self.parser = HtmlParser()
            self.output=DataOutPut()

        def Crawl(self,root_url):
            '''
            爬虫调度程序
            :param root_url: 入口url
            :return:
            '''
            i=1
            self.urlmanager.add_new_url(root_url)
            while(self.urlmanager.has_new_urls() and self.urlmanager.old_urls_size()<50):
                try:
                    url=self.urlmanager.get_new_url()
                    content=self.download.download(url)
                    print("正在解析第%s条数据."%i)
                    i=i+1
                    urls,data=self.parser.parser(url,content)
                    self.output.store_data(data)
                    self.urlmanager.add_new_urls(urls)
                except:
                    print("Crawl fail.")

            print("已解析", self.urlmanager.old_urls_size(), "条数据完成!")
            winsound.Beep(500, 5000)
            self.output.output_html()







    if __name__ == "__main__":
        spidermain=SpiderMain()
        spidermain.Crawl("https://www.zhihu.com/people/da-tou-44-93/following")




七.运行截图：
--
<img src="https://github.com/FindADog/Python/blob/master/gtihub%E5%9B%BE%E7%89%87/%E8%BF%90%E8%A1%8C%E6%88%AA%E5%9B%BE.PNG"></img>

爬虫已经成功开始工作了！！



八.总结
---
+ 过程中还是遇到不少的问题的，整个程序利用上课时间敲得（-.-)一节课就敲完了，但出现了很多错误，大概花了俩个小时去解决错误，主要是刚刚接触这个项目，这种语言，只要是问题，基本一脸茫然(唉），好在最后看到结果了，还是很开心的。
+ 在爬取的过程中，我是利用用户界面的此人关注他人的方式来抓取url的，但这种方式可能会导致有的人并没有关注他人，导致不能爬完想要的数据量
+ 在爬取时，用户资料更详细的需要点击一个按钮才能得到，抱歉我还不会处理那个东西，导致抓的信息并不全，这说明路还很远啊，要学的还有很多。
+ 最后在爬取一万条信息时，爬取到2839条信息时程序就由于**url管理器**没有url停止了运行，打开看了后，发现只有2500条左右有效数据，其余的都是空信息（自己设置的），开始我以为是不是我的爬取方法（上文提到的）有问题，但我再次运行爬取程序时发现程序竟然不能运行了，只能爬取起始链接而且也显示的空信息，直到今天才恢复能成功运行了。原因大概已经很明朗了




