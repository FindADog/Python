# &#160; &#160; &#160; &#160;你的礼物

> 每次想到送给亲爱的家人礼物的时候，学过过计算机的我，总想要送一份能够体现我是计算机学生的礼物，去年元旦节临近，便利用Pyqt（小部分）加Pygame给我的女朋友写了一个小的exe，如图的样式：

登陆界面：<br>
<img src="https://github.com/FindADog/Python/blob/master/Gift/123.gif"></img>

运行界面<br>
<img src="https://github.com/FindADog/Python/blob/master/Gift/%E8%BF%90%E8%A1%8C.gif"></img>


> 以下是制作过程，2个月前写的，细节记得不是很清了【文章所有的资料在下方有链接】：





## 一.主要流程原理

+ 1.利用Pyqt设计一个大致的登陆界面。
+ 2.登陆界面跳转到pygame界面
+ 3.开启无限不循环模式
+ **打包为.exe**

项目文件截图说明：<br>
<img src="https://github.com/FindADog/Python/blob/master/Gift/%E6%96%87%E4%BB%B6%E6%8E%92%E5%88%97.PNG"></img>

+ angles 为精灵哆啦A梦和花朵精灵的类
+ MouseClick 为点击哆啦A梦后出现的烟花类
+ build，dist为打包后出现的文件不予理会
+ windows为Eric6生成的窗口文件
+ windows_control 为窗口控制文件
+ Gift.py pygame界面控制文件
+ LJ_Gift.py 启动文件

## 二.实验环境

+ 1.**Python3.5.4**（版本以上应该也没问题）+**Pygame**(pip install pygame)
+ 2.**Pyqt5**(直接下载安装，文章后面链接)+**Eric6**(个人觉得比较好用)


## 三.具体实现

### 1.Pyqt界面实现
界面大致的样式见上图。Pyqt部分只需要完成如下的任务：

+ 设计出一个登陆交互界面
  [点击此处查看优质教程 v55a](https://pan.baidu.com/s/1L_5zVoPygLwFdpEqNYFYkQ)<br>
 + 将已经设计好的界面文件粘贴到对应的pycharm的项目下面开始代码的编写(我得界面有俩个按钮，按下第一个按钮会验证密码，正确的话直接点击按钮便可以进入主界面，否则礼物按钮自动移动)
  
   点击验证按钮验证槽函数(Pyqt里面的信号槽机制，不复杂)：
  	
        @pyqtSlot()
        def on_pushButton_clicked(self):
            """
            Slot documentation goes here.
            """
            if self.flag == False:
                self.pushButton.setVisible(False)
                self.label_4.setText("密码不对，按钮都被你气跑了")
            else:
                from Gift import main
                self.hide()
                ma = main()
                ma.Main()
  
   点击礼物按钮进入主界面槽函数：
   
       @pyqtSlot()
        def on_pushButton_2_clicked(self):
            """
            Slot documentation goes here.
            """

            if self.name.text() == "陈丽君" and self.password.text() == "520lj":
                self.flag = True
                self.pushButton.setGeometry(QtCore.QRect(220, 360, 91, 51))
                self.pushButton.setVisible(True)
                #self.pushButton.setText("礼物")
                self.label_4.setText("恭喜你，去看看你的礼物吧！")
            else:
                self.label_4.setText("密码错啦")
  
  
  礼物按钮循环移动函数
  
          def eventFilter(self, object , event):
          if object == self.pushButton:
              if event.type() == QEvent.Enter and self.flag == False:
                  self.pushButton.setGeometry(QtCore.QRect(randint(50,600),randint(300,450),91, 51))
                  if self.num == 0:
                      self.label_4.setText("你太天真了-。-")
                  elif self.num %2 == 0:
                      self.label_4.setText("密码错误是得不到的,哈哈")
                  elif self.num %5 == 0:
                      self.label_4.setText("还在努力啊？？")
                  elif self.num %3 == 0:
                      self.label_4.setText("放弃吧，孩子")
                  self.num = self.num + 1
          return QWidget.eventFilter(self, object, event)

	给你的界面加入淡入淡出效果（调用代码，后面给出）：

      def doShow(self):
          try:
              # 尝试先取消动画完成后关闭窗口的信号
              self.animation.finished.disconnect(self.close)
          except:
              pass
          self.animation.stop()
          # 透明度范围从0逐渐增加到1
          self.animation.setStartValue(0)
          self.animation.setEndValue(1)
        self.animation.start()
  
   在界面的类中加入界面控制代码和QSS代码（使界面更美观），在后面给出
   
   
   
  ### 2.Pygame部分
  pygame部分是重头戏，不要前面的Pyqt部分也很美观了-。-<br>
  此阶段的一些主要任务：
  
  + 初始化你的pygame界面
  可以查看我以前写的详细的pygame文章[点击此处查看](https://github.com/FindADog/Python/blob/master/TankWar.md)，这篇文章少量讲解以前的旧知识，重点说以前没做过的.(看完了再看这篇会更加了解这篇的思想)
  + 初始化精灵组

        #哆啦A梦精灵组生成,默认整个屏幕小于5个
              self.ADream_group = pygame.sprite.Group()
              #花朵精灵组
              self.Flower_group = pygame.sprite.Group()
              #烟花绽放精灵组
              self.BoomFire_group = pygame.sprite.Group()
  + 编写你的精灵类，主要有哆啦A梦，花朵，爆炸烟花精灵
 #### a.哆啦A梦精灵编写(程序开始自动生成，花朵精灵也是如此)
 
     class ADream(pygame.sprite.Sprite):
        def __init__(self):
            pygame.sprite.Sprite.__init__(self)
            #加载哆啦A梦的图片
            ADream = data[str(randint(1,5))]
            self.img = pygame.image.fromstring(ADream[0],ADream[1],"ARGB").convert_alpha()

            #获得图片矩形
            self.rect = self.img.get_rect()
            #定义哆啦A梦的出生地点,生成地点在屏幕上方随即生成（0，800)
            self.position = [randint(30, 760),-160]
            self.rect.center = self.position
            #哆啦A梦的速度:默认向下
            self.speed = [0,1]
            #为了避免程序运行过快一次生成全部的A梦，设置一个生成标志
            self.isCreate = 5


此处的data是我已经存储的哆啦A梦的\[尺寸，base64](此处填啥后面解释)

精灵的更新：

      def update(self,screen):
          '''
          开始显示与降落哆啦A梦
          :return:
          '''

          self.rect = self.rect.move(self.speed)
        screen.blit(self.img,self.rect)
        
 精灵的死亡处理：
 
    def isAlive(self):
          '''
          是否死亡主要是由于哆啦A梦直接掉落到屏幕外边，为了节约空间，将其删除
          :return:
          '''
          if self.rect.top >= SCREEN_HEIGHT:
              return False
          else:
            return True
            

                          
  #### b.对重要精灵哆啦A梦的处理
  
  **随机生成哆啦A梦**
  
        #开始更新哆啦A梦,个数小于5,不能与其他的精灵相交并且满足isCreate条件
                isCreate = randint(0, 20)
                aDream = ADream()
                #判断是否与其他的精灵相交
                if aDream.rect.collidelist(self.ADream_group.sprites()) >= 0 or aDream.rect.collidelist(self.Flower_group.sprites()) >= 0:
                    del aDream
                else:
                    if len(self.ADream_group.sprites())<13 and aDream.isCreate == isCreate:
                        self.ADream_group.add(aDream)
                    else:
                        del aDream
  
  **哆啦A梦行为处理**
  
     #哆啦A梦判活处理加判断点击，鼠标移动处理
              for eachADream in self.ADream_group.sprites():
                  #判断是否出界
                  if eachADream.isAlive():
                      #判断鼠标是否放在了哆啦A梦上
                      #先获得鼠标位置
                      position = pygame.mouse.get_pos()
                      if eachADream.rect.collidepoint(position):
                          #鼠标移动到了哆啦A梦上将其停止
                          eachADream.speed = [0,0]
                          #如果点击，显示照片
                          if pygame.mouse.get_pressed()[0]:
                             #让所有的花朵爆炸
                              for eachflower in self.Flower_group:
                                  fire = BoomFire(eachflower.rect.center, FireImg)
                                  self.BoomFire_group.add(fire)
                                  self.Flower_group.remove(eachflower)
                              #同时随机生成80朵花
                              i = 0
                              while i< 80:
                                  fire = BoomFire([randint(10,750),randint(10,560)],FireImg)
                                  self.BoomFire_group.add(fire)
                                  i = i+ 1
                              self.ADream_group.remove(eachADream)
                              if self.imgNo == 21:
                                 self.imgNo = 0
                              #加载人的照片
                              f = BytesIO(PeopleImg[self.imgNo % 21])
                              self.PeopleImg = pygame.image.load(f)
                              self.imgNo = self.imgNo + 1
                              self.flag = 0

                          else:
                              eachADream.update(screen)
                      else:
                          eachADream.speed = [0,1]
                          eachADream.update(screen)
                  else:
                      self.ADream_group.remove(eachADream)
                      

  #### d.烟花爆炸的连续处理
  
  主要思想，即在pygame每帧动画切换时，换上不同的烟花图片循环即可。
  
  **烟花爆炸BoomFire类**
  
      #烟花爆炸特效
    class BoomFire(pygame.sprite.Sprite):
        def __init__(self,position,FireImg):
            pygame.sprite.Sprite.__init__(self)
            #直接传递预加载的烟花爆炸图片数组
            self.img = FireImg
            #当前应该显示的照片从第六张到第21
            self.imgNo = 6
            #应该开放的地方
            self.position = position


        def update(self,screen):
            '''
            逐帧显示烟花爆炸
            :return:
            '''

            img = self.img[self.imgNo-6]
            img = pygame.transform.smoothscale(img,(80,88))
            rect = img.get_rect()
            rect.center = self.position
            screen.blit(img,rect)
            self.imgNo = self.imgNo + 1
            
   注意此处的烟花图片加载，就算你不打包成exe也要提前保存到程序里，因为在很短的时间加载21张图片我得电脑反应的很卡顿。
   
  **主函数循环内的烟花图片加载**
  
    #更新爆炸烟花
    for eachfire in self.BoomFire_group.sprites():
    if eachfire.imgNo > 21:
    self.BoomFire_group.remove(eachfire)
    else:
                    eachfire.update(screen)
                    
  #### e.人物图片加载，显示
  
    # 更新人物图片

              if self.PeopleImg is not None:
                  #设置图片持续时间
                  if self.flag < 400:
                      #设置图片淡入
                      if i < 350:
                          i = i + 4
                          temp = i
                          self.PeopleImg.set_alpha(i)
                          screen.blit(self.PeopleImg, [200, 35])
                      else:
                          #设置图片淡出
                          temp -=  4
                          self.PeopleImg.set_alpha(temp)
                          screen.blit(self.PeopleImg, [200, 35])
                      self.flag = self.flag + 1
                  else:
                      self.flag = 1
                      self.PeopleImg = None
#### f.循环播放音乐

     #加载音乐开始播放
            # 读出二进制文件
            from Resource_Base64 import Get_music
            pygame.mixer.music.load(Get_music.get_music())
            pygame.mixer.music.set_volume(0.5)
            pygame.mixer.music.play(loops= -1)
            
大致的需要的功能就是如此，剩下的只需要依样画葫芦即可。

## 四.打包成唯一的一个.exe
利用**pyinstaller**.(由于网上教程多，且打包很简单 此处不再详述 貌似在该项目下输入 pyinstaller 文件名.py -f -w 即可，自行百度)<br>
既然做出来了想要的效果，便要打包成一个exe来方便在任何环境下查（zhaung）看（x），上面的代码我花了三天写，打包又花了三天。主要总得不到想要的效果，咨询了一下大佬们，都说要打包成一个文件夹压缩文件，包含图片,音乐文件的，甚至还需要一些.dll文件一起打包，改写spec文件，我试过，不好用，也需要额外文件。我觉得过于累赘，最后，我是用先将一些小图片，小文件，转为byte类型，直接输出在控制台，再复制在几个py文件中直接作为常量调用。有时候，文件过大，在控制台接收不到，便使用先将byte流转为base64格式，写到text文件中再转为byte使用：

    import base64
    with open(r"C:\Users\YY\Desktop\platforms\qwindows.dll","rb") as fr:
        data = fr.read()
    data = base64.b64encode(data)
    with open(r"C:\Users\YY\Desktop\1.txt","wb") as fw:
        fw.write(data)
 
 **如何使用这些base64,byte？**
 <br> 在程序中使用ByteIO即可，如：
 
 f = BytesIO(PeopleImg[self.imgNo % 21])
 
 便可像调用一个地址一样调用byte。Base64，貌似直接使用pygame里面的fromString加载即可（见代码）,qss我是直接把代码粘贴进去了(网上的代码模块)
 <br>最后一切完成后，报错**缺少Qt缺少platform啥的**，最后如法炮制，到目录下找到提及的platform的三个.dll文件，写入写出，问题解决，文件大小大概37M！
 <br>  由于，有的地方不能使用byteio，platform也需要在运行时写入当前目录，最后为了使整体美观，程序结束后将写入保存的文件全部遍历删除。
 
                 if event.type == pygame.QUIT:
                    try:
                        os.remove("A0.png")
                        os.remove("A2.png")
                        files = os.listdir("platforms")
                        for file in files:
                            os.remove("platforms/"+file)
                        os.removedirs("platforms")
                    except:
                        sys.exit()
                    sys.exit()
 
 ## 五.小结
文章下面的照片部分已删除，文中用的背景图片，精灵图片均为photoshop制的，尺寸固定。因此不能直接跑通，在本地跑可以直接不用照片的base64 ，byte之类的，另外程序很多地方都写死了，没有抽象出来，大家使用时，记得修改代码。由于是第一次在CSDN写文章，这程序以前完成的，再加上是学生，知识的欠缺，代码肯定有大量冗余的地方，还有关于打包的方法有好办法的请在下面留言，请大家多多包涵。
 
## 六.资料链接
[点击此处查看下载代码压缩包(美女图片已删除，自行添加)](https://github.com/FindADog/Python/blob/master/Gift/Gift.rar)<br>
[点击此处查看详细的我得pygame文章](https://github.com/FindADog/Python/blob/master/TankWar.md)<br>
[pygame入门视频 侵删](https://ke.qq.com/course/359196)<br>
[优质pyqt教程 v55a 侵删](https://pan.baidu.com/s/1L_5zVoPygLwFdpEqNYFYkQ)<br>
