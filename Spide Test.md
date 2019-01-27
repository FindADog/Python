练习-爬取段子并以csv文件保存
===
> 爬取百思不得姐段子的作者，段子，链接，并以csv文件保存（2019.1.27）

# 一.主要流程原理
&#8195;采用Lock的生产者消费者模式（多线程）来爬取网页上的内容。
主要流程如下图：
<img src="https://github.com/FindADog/Python/blob/master/gtihub%E5%9B%BE%E7%89%87/%E6%B5%81%E7%A8%8B%E5%9B%BE.PNG"></img>

# 二.实验环境
&#8195;python3.5.4，pycharm

# 三.知识储备
+ Queue是线程安全的数据结构，可以在多线程爬虫中放心使用
+ requests的get函数可以请求需要的url页面，利用其text属性便可以得到其html代码
+ lxml下的xpath便可以实现节点元素的定位，利用get可以获取其属性，还可以利用find对其进行再次定位
+ 写文件线程不安全需要加锁
# 四.具体实现
### 1.先完成主函数，再去完成细节。
主函数需要完成以下的事情：

+ 1.初始化线程安全的Queue队列page_queue(页面url集合),text_queue(生产者已保存好的内容)
+ 2.提供需要操作页面的足量的url，存储至page_url
+ 3.初始化适量的生产者，消费者线程开始工作
代码如下：

        def main():
        # 获取需要下载页面的网址
        page_queue = Queue(20)
        # 设置需要一次最大下载段子长度
        text_queue = Queue(2000)
        # 得到需要下载的页面的全部url
        for page in range(1,20):
            url = "http://www.budejie.com/text/%d" % page
            page_queue.put(url)
    
        # 生产者
        for x in range(0,10):
            producer = Producer(page_queue=page_queue,text_queue=text_queue)
            producer.start()
    
        # 消费者
        for x in range(0,10):
            consumer = Consumer(page_queue=page_queue,text_queue=text_queue)
            consumer.start()
### 2.生产者实现
生产者需要完成以下的事情：

+ 从page_queue中取出url
+ 请求到需要下载保存段子的页面的html代码
+ 利用xpath定位需要的元素节点或内容
+ 保存需要的内容
+ 存储至text_queue
+ 当page_queue为空了，线程退出循环
代码：

    class Producer(Thread):
        def __init__(self, page_queue, text_queue, *args, **kwargs):
            '''
            生产者类，生产需要下载的段子的link与文字交付给text_queue
            :param args:
            :param kwargs:
            '''
            super(Producer, self).__init__(*args, **kwargs)
            self.page_queue = page_queue
            self.text_queue = text_queue
    
        def run(self):
            '''
            开始循环，生产任务完成停止工作
            :return:
            '''
            while True:
                if self.page_queue.empty() is True:
                    print("OK")
                    break
                self.html_parser(self.page_queue.get())
    
        def html_parser(self,url):
            '''
            解析页面信息
            :param url: 需要解析页面的html
            :return:
            '''
            # 请求页面
            response = requests.get(url)
            # 获得页面的html
            html = response.text
            # 构建lxml对象
            text = etree.HTML(html)
            # xpath得到所有的条目
            datas = text.xpath('''//div[@class="j-r-list"]/ul/li''')
            for data in datas:
                # xpath解析出作者名字
                author = data.find('''.//div[@class="u-txt"]/a''').text
                # xpath解析出段子
                duanzi = data.find('''.//div[@class="j-r-list-c-desc"]/a''')
                joke = duanzi.text
                # 得到链接
                link = "http://www.budejie.com" + duanzi.get("href")
                # 将得到的内容传入text_queue
                self.text_queue.put({"author":author,"joke":joke,"link":link})

### 3.消费者实现
消费者主要需要完成以下事情：

+ 先判断page_queue与text_queue是否为空，为空直接退出
+ 从img_queue中得到需要下载到本地的csv源数据，取30一次存储。
+ 给文件存储操作加锁。记得释放

代码如下：

    class Consumer(Thread):
        def __init__(self,page_queue,text_queue,*args,**kwargs):
            super(Consumer, self).__init__(*args,**kwargs)
            self.page_queue = page_queue
            self.text_queue = text_queue
    
        def run(self):
            '''
            开始保存未csv文件
            :return:
            '''
            if self.page_queue.empty() == True and self.text_queue.empty() == True:
                print("消费者" + threading.current_thread().getName() + "完成任务！")
                return
            headers = {"author","joke","link"}
            data = []
            for x in range(0,30):
                data.append(self.text_queue.get())
            while True:
    
                #开始保存，打开文件为线程不安全操作,需要加锁
                qLock.acquire()
                try:
                    with open("123.csv","a",encoding="utf-8") as fw:
                        writer = csv.DictWriter(fw,headers)
                        writer.writeheader()
                        writer.writerows(data)
                finally:
                    qLock.release()
                    

# 五.效果截图
<img src=""></img>


# 六.小结
+ GIL即Global Interpreter Lock，全局解释锁，是用于python多线程编程中的工具。在python自带的编译器CPython中便使用了GIL，它是为了保证多线程的线程安全。当使用了GIL后，同一个时间只有一个线程在运行，并没有实现线程的并发操作，cpu在很短的时间不断切换线程执行，使之**看起来**像是并发操作，但实际上是cpu运行速度快到我们感觉不出来。**但在爬虫的使用过程中使用多线程确可以提高运行速度**。因为爬虫在运行过程中需要频繁的进行internet访问，也可能需要进行io读写操作，这些操作是很慢的（相对cpu），而cpu在线程间切换确几乎不需要什么时间与资源，因此，在一个线程卡在了网络请求或者io操作上，cpu可以快速切换线程，达到提高运算速度的效果。但是如果是计算机操作密集型的程序，如需要大量的计算，多线程是起不到什么作用的，这时便需要多进程来操作。一个进程便需要一个核来操作，但要注意，重开进程是需要消耗大量的资源的，可能会造成得不偿失的效果。因此，**在io或网络请求密集型的程序多线程可以起到很好的作用，但计算密集型的操作程序便不能祈祷好的效果，需要多进程来实现**
+ <font color=#FF0000>工欲善其事，必先利其器</font>以前写爬虫都是用beautifulsoup来写，这次换了xpath，即使用lxml来用定位元素。配合chrome的xpath使用的很方便。但有时候，chrome的xpath”会出错“,如果出错，直接应该对照原HTML改正。偏偏我xpath也不熟悉，搞了半天，，，。
+ 利用xpath定位了一个节点后，还可以再利用xpath来定位节点后面的，但必须要以./ or .//来定位。最后xpath定位得到的是一个列表。find得到的是第一个可用节点！
