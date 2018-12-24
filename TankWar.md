# &#160; &#160; &#160; &#160; Python完成自制坦克大战游戏设计

> 个人日记：时间过的真快啊，今天是学长们考研，明年的这个时候就到了我考研，说实话还真有点紧张，哈哈。这俩天，学校要求的俩个课设也都已经做完了，复习也提不起劲来（主要是学校考试题目太简单，原题多），回去就准备好好准备考研了。一定得好好准备了，我想读研啊啊啊啊啊
----------------------------------


## 一、哪来的动力？

&#160; &#160; &#160; &#160;小时候一直喜欢玩游戏机，我爸是老师，也比较抠-。-，向他提了几次便一直得不到回应，想想我也是厉害，小学时候徒步俩里路跑去朋友家里玩游戏机，在双人可以联机玩的游戏中，坦克大战不用说，自然是大大的好玩，前天在寝室无聊，但又想学习，便打开各大程序员学习网站，在腾讯课堂找到了利用pygame做游戏的视频（[点击查看视频](https://ke.qq.com/course/359196)），里面大致介绍了一些原理、功能（但远远不够），想到了大二时利用c++做的坦克大战的游戏，大致是类似俄罗斯方块的模型
精彩吗？好看吗？
<a href = "https://github.com/FindADog/Python/blob/master/TankWar/photo/%E7%86%8A%E7%8C%AB.jpg"></a>
很显然不好看，而且其中坦克的绘制很丑，炮弹发射还不知道怎么设计。当初是想利用MFC的绘图功能来画炮弹（各位不要笑-。-），当时画坦克就用了在下一天的时间，真的难受啊！<br>
&#160; &#160; &#160; &#160;很明显，做为一个想有作为的程序员这是不能接受的啊!因此，我开始了坦克大战游戏的制作。（[所有的资源，点击查看](https://github.com/FindADog/Python/tree/master/TankWar)）<br>
----

## 二、怎么设计？
+ 1.环境：python3.5.4（已安装pygame），其中pygame直接在控制台输入 pip install pygame即可。
+ 2.IDE：PyCharm
+ 3.目标：简易坦克大战游戏设计
+ **重点：下面的视频知识说了大致的原理，一定要查阅pygame的官方文档！**
<br>[点击查看官方pygame文档](http://www.pygame.org/docs/ref/pygame.html)

## 三、运行截图
+ 1.开始界面没有具体设计，但加个开始界面也很简单，为了去了解更多的计算机知识，在此省略
<img src = "https://github.com/FindADog/Python/blob/master/TankWar/photo/%E7%95%8C%E9%9D%A2.PNG">
+ 2.操作控制，按w，s,a,d 进行上下左右控制
<img src = "https://github.com/FindADog/Python/blob/master/TankWar/photo/%E4%B8%8A%E4%B8%8B%E7%A7%BB%E5%8A%A8.PNG"></a>
+ 3.M键生成敌机
<img src = "https://github.com/FindADog/Python/blob/master/TankWar/photo/%E7%94%9F%E6%88%90%E6%95%8C%E6%9C%BA.PNG"></a>
+ 4.F键发射子弹
<img src = "https://github.com/FindADog/Python/blob/master/TankWar/photo/%E7%94%9F%E6%88%90%E6%95%8C%E6%9C%BA.PNG"></a>
+ 5.最后皮了一下，借用了马老师的图片和语音来进行我方坦克的死亡画面
<img src = "https://github.com/FindADog/Python/blob/master/TankWar/photo/%E6%AD%BB%E4%BA%A1%E7%94%BB%E9%9D%A2.PNG"></a>
+ 6.文件结构图
<img src = "https://github.com/FindADog/Python/blob/master/TankWar/photo/%E6%96%87%E4%BB%B6%E5%B8%83%E5%B1%80.PNG"></a>
-----
## 四、原理讲解
***再次提醒：一定要关注官方文档！！！***
+ 1.游戏画面就是由一帧帧的画面组成的，只要将显示的画面写到死循环里，一帧帧的画面便能播放
+ 2.游戏类的每个角色由面向对象的思想，都可以看成一个精灵类（后面具体讲解）
+ 3.声音，背景有具体的模块控制，很简单
+ 4.发射子弹有具体的碰撞函数
+ 5.按键效果是由python的event模块来实现的，由event.type来判断是否是KEYDOWN
----
## 五、具体实现
### 1.游戏死循环加显示帧数（主函数显示）

    #主函数，开始游戏的初始化
        def main(self):
            #初始化
            pygame.init()
            #设置窗口大小
            screen = pygame.display.set_mode(size)
            #设置背景图片
            background = pygame.image.load(r"C:\Users\YY\PycharmProjects\Game1\Resouce\BackGround.png").convert_alpha()
            bgrect = background.get_rect()
            #设置程序名字
            pygame.display.set_caption("TankFighting")

            while True:
                for event in pygame.event.get():
                    if event.type == pygame.QUIT:
                        sys.exit()
                    
                # 更新背景
                screen.blit(background, bgrect)

                # 加载界面
                pygame.display.flip()

                #时钟
                pygame.time.Clock().tick(60)
上面的代码是由有删减的完整的代码后面给出，，，具体的功能就是只有一个背景图片一直显示。按下程序右上角的关闭，退出程序。其中的代码功能:<br>
+ a.display模块：set_mode(size)根据size的长宽构建游戏界面的大小，screen即为当前游戏界面的屏幕句柄（类似）flip（）更新整个界面<br>
+ b.time模块，clock().tick(int),一秒显示多少帧图像，即可以刷新界面<br>
+ c.event模块，接收事件类型，具体事件类型如文档所述,可自行查阅：
<a href = ""></a>

### 2.坦克的绘制与移动
#### 坦克的绘制
+ 正如前面所说。坦克的绘制是采用一张一张的图片通过播放实现的动态图片，所以，你首先还是得找到坦克的素材图片，而且，其中的关键关键点，你必须将图片绘制成这样：
<a href = ""></a>
即不留下画布，这可以采用PhotoShop实现，将其调整为合适的大小再进行裁剪画布。否则，坦克的图片的画布会算入你的坦克的实际尺寸，对后面的移动边界判定以及子弹的发射以及子弹的击中判定会造成大大的影响
+ 在得到裁剪好的图片后，采用如下方式导入：首先利用现成的模块导入：
<br> img =pygame.image.load(r"C:\Users\YY\PycharmProjects\Game1\Resouce\Play1_Right.png")
<br> 再利用rect = img.get_rect()
<br> 是什么意思呢，load即将照片导入进来，这是一个特定的类型，查阅文档得到更多关于它的信息。get_rect()可以得到其中的一个属性，即这张图片的属性，里面有这张图片的宽高，位置，可以通过赋值改变它的属性
#### 坦克的移动
同样的，通过事件循环监听，得到按键，然后将参数传给玩家的电脑（期末在即，功能全都没问题，这块的代码还可以更简洁）

 		while True:
            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    sys.exit()
                if event.type == pygame.KEYDOWN:
                    # 控制我方坦克移动
                    if event.key in(pygame.K_w,pygame.K_s,pygame.K_a,pygame.K_d):
                        self.args["key"] = event.key
                        self.args['position'] = self.play_tank.rect
                        self.play_tank.update(self.args)
                        

移动函数，我写在了tank类里，如下，tank类里的成员变量如下：已作了注释。***移动方法***：先得到方向，先得到按键标识的方向，修改之，然后更新self.img,***并得到此时的位置***，便可在原地完成华丽的转身，当然，你的移动不能超过边界，其中的注释便有边界判定的方法。<br>
***类的初始化定义：***

    class p_tank(pygame.sprite.Sprite):
        def __init__(self):
            pygame.sprite.Sprite.__init__(self)
            pygame.init()
            self.img = pygame.image.load(r"C:\Users\YY\PycharmProjects\Game1\Resouce\Play1_Right.png")
            self.img_u = pygame.image.load(r"C:\Users\YY\PycharmProjects\Game1\Resouce\Play1_Up.png")
            self.img_d = pygame.image.load(r"C:\Users\YY\PycharmProjects\Game1\Resouce\Play1_Down.png")
            self.img_l = pygame.image.load(r"C:\Users\YY\PycharmProjects\Game1\Resouce\Play1_Left.png")
            self.img_r = pygame.image.load(r"C:\Users\YY\PycharmProjects\Game1\Resouce\Play1_Right.png")
            self.ponit = {1:self.img_u,2:self.img_d,3:self.img_l,4:self.img_r}
            #当前位置由此得出
            self.rect = self.img.get_rect()
            # 定义速度
            self.speed = [3,0]
            # 定义坦克出生的坐标
            self.position = (10,10)
            # 方向标识 1:up 2:down...默认向右
            self.flag = 4
            
***得到转向后的更新：***

    #坦克的转向与显示
        def update(self, args):
            '''
            调用此函数，完成坦克的显示与转向
            :param args: 传递坦克需要显示的position，转向指令，为字典
            :return:
            '''
            if "key" in args:
                #按下w：up键，将flag改为1
                if args["key"] == pygame.K_w:
                    self.flag = 1
                    self.img = self.img_u
                    self.speed = [0, -3]
                #按下s:down 将flag改为2
                elif args["key"] == pygame.K_s:
                    self.img = self.img_d
                    self.flag = 2
                    self.speed = [0,3]
                elif args["key"] == pygame.K_a:
                    self.flag = 3
                    self.speed = [-3,0]
                    self.img = self.img_l
                elif args["key"] == pygame.K_d:
                    self.flag = 4
                    self.speed = [3,0]
                    self.img = self.img_r
                # 调整位置
                self.rect.center = args["position"].center
                
***这里就又可以看出我说的多余的代码了，args这个参数可以简写为只传递一个转向就行，args["position"]即为坦克此时的rect，不改了，留着以后反省***

***移动函数，简单更新界面即可当然要判断是否超过界面的边界***

        def move(self,screen):
            '''
            控制坦克的移动
            :return:
            '''
            #判断边界
            #上下移动
            if self.rect.top <= 0:
                self.rect.top = 0
            elif self.rect.top >= SCREEN_HEIGHT-self.rect.height:
                self.rect.top = SCREEN_HEIGHT-self.rect.height
            #左右移动
            if self.rect.left <= 0:
                self.rect.left = 0
            elif self.rect.left >= SCREEN_WIDTH-self.rect.width:
                self.rect.left = SCREEN_WIDTH - self.rect.width
            self.rect = self.rect.move(self.speed)
            #调整rgb
            self.img = self.img.convert_alpha()
            #显示图片
            screen.blit(self.img,self.rect)
---
### 3.坦克攻击
既然是坦克大战肯定坦克需要发射子弹同样的步骤如下：
+ 1.按照上面的方向绘制好，便可以导入开始制作了
+ 2.每一颗子弹就是一个子弹对象，管理肯定是不好管理的，这时候就体现了pygame的简便，利用pygame下的sprite模块，即可创建一个精灵组，里面的方法不限于我下面代码里用到的建立精灵组，添加一个精灵，删除一个精灵。老话，看文档。
+ 同样的，对子弹也要进行边界判定，超出边界就删掉（节约空间，不删也可以，小规模也不卡），击中判定后面讲
#### 子弹类的设计
	
        #子弹类的编写
    SCREEN_WIDTH = 1000
    SCREEN_HEIGHT = 747

    class bullet(pygame.sprite.Sprite):
        def __init__(self):
            pygame.sprite.Sprite.__init__(self)
            self.img = pygame.image.load(r"C:\Users\YY\PycharmProjects\Game1\Resouce\Bullet1_Up.png").convert_alpha()
            self.img_Right = pygame.image.load(r"C:\Users\YY\PycharmProjects\Game1\Resouce\Bullet1_Right.png").convert_alpha()
            self.rect = self.img.get_rect()
            #定义速度
            self.speed = [1,0]
            #坦克的位置
            self.position = None
            #定义发射的方向字典
            self.ponit = {1: [0,-2], 2: [0,2], 3: [-2,0], 4: [2,0]}
            #方向
            self.flag = 1
            #生存标志
            self.status = 0
        def update(self,args):
            '''
            #初始化每个子弹的方向，位置
            :param args:
            :return:
            '''
            #调整发射的位置
            self.position = args["position"]
            #调整图片方向
            #向上
            if args['flag'] == 1:
                self.img = self.img
            #向下
            elif args['flag'] == 2:
                self.img = pygame.transform.flip(self.img,False,True)
            #向左
            elif args['flag'] == 3:
                self.img = pygame.transform.flip(self.img_Right,True,False)
            #向右
            elif args['flag'] == 4:
                self.img = self.img_Right
            self.flag = args["flag"]
            #调整子弹发射方向
            self.speed = self.ponit[args['flag']]
            #调整矩形
            self.rect = self.img.get_rect()


        def move(self,screen):
            '''
            子弹的移动
            flag:射出为1
            :return:
            '''
            if self.status == 0:
                self.rect.center = self.position.center
                self.rect = self.rect.move([self.speed[0]*20,self.speed[1]*20])
                self.status =1
            else:
                self.rect = self.rect.move(self.speed)
            screen.blit(self.img, self.rect)


        def bullet_die(self):
            '''
            当子弹越过边界放回值，将其从精灵组中删除
            :return: bool
            '''
            if self.rect.top < 0:
                return True
            elif self.rect.top > SCREEN_HEIGHT-self.rect.height:
                return True
            #左右移动
            if self.rect.left < 0:
                return True
            elif self.rect.left > SCREEN_WIDTH-self.rect.width:
                return True
            else:
                return False

可以看到和坦克的一些函数也写得很像，基本功能都是差不多的，我就不多bb了

### 4.敌方坦克的设计
一切为了简介，敌方坦克我只是随便写了一点，加入了随机数判断移动，发射子弹<br>
***敌方坦克类的设计***


       #敌方坦克

    SCREEN_WIDTH = 1000
    SCREEN_HEIGHT = 747

    class etank(pygame.sprite.Sprite):
        def __init__(self):
            pygame.sprite.Sprite.__init__(self)
            pygame.init()
            self.img = pygame.image.load(r"C:\Users\YY\PycharmProjects\Game1\Resouce\Play2_Right.png")
            self.img_u = pygame.image.load(r"C:\Users\YY\PycharmProjects\Game1\Resouce\Play2_Up.png")
            self.img_d = pygame.image.load(r"C:\Users\YY\PycharmProjects\Game1\Resouce\Play2_Down.png")
            self.img_l = pygame.image.load(r"C:\Users\YY\PycharmProjects\Game1\Resouce\Play2_Left.png")
            self.img_r = pygame.image.load(r"C:\Users\YY\PycharmProjects\Game1\Resouce\Play2_Right.png")
            self.ponit = {1:self.img_u,2:self.img_d,3:self.img_l,4:self.img_r}
            #当前位置由此得出
            self.rect = self.img.get_rect()
            # 定义速度
            self.speed = [1,0]
            # 定义敌方坦克出生的坐标
            self.position = [random.randint(300,500),random.randint(300,500)]
            self.rect.center = self.position
            # 方向标识 1:up 2:down...默认向右
            self.flag = 4
            #是否转向标识
            self.isturn = 1
            #是否攻击标识
            self.isattack = 0

        #坦克的转向与显示与攻击,敌机让其自己随机移动，但不能超过边界
        def update(self, screen,ebullet_group):
            '''
            调用此函数，完成坦克的显示与转向
            :param screen:display,主屏幕,ebullet_group 敌方坦克子弹类
            :return:
            '''
            #定义一个随机方向，让坦克移动,并控制什么时候转向,定义一个数字让其等于它时转向
            self.flag = random.randint(1,4)
            turnStatus = random.randint(0,20)
            attackStatus = random.randint(0,10)
            #如果attckStatus等于攻击标识，攻击
            if attackStatus == self.isattack and turnStatus == self.isturn:
                #将此精灵加入敌军子弹精灵组
                ebullet_group.add(self.fire())
            #如果等于self.status 转向
            if(turnStatus == self.isturn):
                if self.flag == 1:
                    self.img = self.img_u
                    self.speed = [0,-1]
                elif self.flag == 2:
                    self.img = self.img_d
                    self.speed = [0,1]
                elif self.flag == 3:
                    self.img = self.img_l
                    self.speed = [-1,0]
                else:
                    self.img = self.img_r
                    self.speed = [1,0]
            # 调整位置
            #移动
            self.move(screen)

        def move(self,screen):
            '''
            控制坦克的移动
            :return:
            '''
            #判断边界
            #上下移动
            if self.rect.top <= 0:
                self.top = 0
            elif self.rect.top >= SCREEN_HEIGHT-self.rect.height:
                self.rect.top = SCREEN_HEIGHT-self.rect.height
            #左右移动
            if self.rect.left <= 0:
                self.rect.left = 0
            elif self.rect.left >= SCREEN_WIDTH-self.rect.width:
                self.rect.left = SCREEN_WIDTH - self.rect.width
            self.rect = self.rect.move(self.speed)
            self.img = self.img.convert_alpha()
            screen.blit(self.img,self.rect)


***这个坦克和上面的坦克可以看到还是很像。不过在update中加入了这些元素。***<br>
+ 让坦克自动转向标志，随机数正确了就转向
+ 让坦克自动攻击标记，转向和攻击标识同时正确了就攻击

### 5.双方坦克攻击设计
我反按下f键生成一个子弹对象，往我方子弹精灵组加入，敌方也是如此<br>
***坦克开火设计，双方一样***

    def fire(self):
        '''
        坦克开火，返回一个Bullet对象1
        :return:
        '''
        bu = bullet()
        #初始化这颗子弹
        args = {}
        args["position"] = self.rect
        args["flag"] = self.flag
        bu.update(args)
        return bu
      
***我方发射子弹片段代码***

    #按下f键控制我方坦克发射子弹
    if event.key == pygame.K_f:
   		bu = self.play_tank.fire()
    	self.bullet_group.add(bu)
***俩边坦克的中弹判断

     # 敌方坦克行动
                for eachtank in self.etank_group.sprites():
                    # 碰撞检测，被我方坦克打中的敌方坦克消失
                    target = eachtank.rect.collidelist(self.bullet_group.sprites())
                    if target  > 0:
                        # 移除坦克,移除子弹，加载爆炸精灵
                        self.bullet_group.remove(self.bullet_group.sprites().pop(target))
                        bm = boom(eachtank.rect)
                        self.boom_group.add(bm)
                        self.etank_group.remove(eachtank)
                    else:
                        eachtank.update(screen,self.ebullet_group)


                 # 玩家坦克移动,是否被击中
                target = self.play_tank.rect.collidelist(self.ebullet_group.sprites())
                if target > 0:
                    # 移除坦克,移除子弹，加载爆炸精灵
                    # self.ebullet_group.remove(self.ebullet_group.sprites().pop(target))
                    # bm = boom(self.play_tank.rect)
                    image_rect = image.get_rect()
                    image_rect.center = self.play_tank.rect.center
                    screen.blit(image,image_rect)
                    # 加载界面
                    pygame.display.flip()

                    pygame.mixer.music.play()
                    pygame.time.wait(10000)
                else:
                    self.play_tank.move(screen)
                    
其中，爆炸也采用了一个爆炸精灵，用三张图片循环播放，我就不演示了，要想完美演示，需要调慢播放速度。另外当玩家坦克被击中时调用了马老师的图片和音乐-。-

# 六、小结

图形界面是违反了程序员的天性的，计算机只认识0，1图片绝对是不认识的，这也是为什么大牛们不用windows的原因（个人猜想），但不得不说，当图形界面的程序，做出来是很很很舒服的，看到自己的代码在电脑上跑是很享受的。对这个Demo我有如下的感受：
+ 1.pygame刷新了而我对游戏制作的认识，其实看起来那么复杂的电脑游戏，也是由一部分一部分组成的，而且制作起来也是有理可寻，并不是虚无缥缈的，这给了我往下学习更多高深东西的信心，做完真爽。
+ 2.真切的了解了动态是真么从图片过度的，真的神奇。
+ 3.其中也用到了一些PS，让我也对它产生了兴趣，又多了一些可以平常自己专研的东西
+ 4.学习新知识的过程是痛苦的，但这也是有收获，所以，学习新知识最好是给自己提示一下，想象做好了以后的效果，那样学起来会很轻松，愉快
+ 5.这是第一次学习新知识查阅官方文档，学习起来别提多快乐了，最官方的解释都在上面。不用去千遍百遍的去百度，走的路线也是最正确的！


# 七、资源下载
[点击此处查看代码](https://github.com/FindADog/Python/blob/master/TankWar/Game1.rar)<br>
[点击此处查看图片，资源](https://github.com/FindADog/Python/tree/master/TankWar/Resouce)
