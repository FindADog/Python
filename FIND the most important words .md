Python 练习-找出在一个月的日记中的每篇文章的关键词（英文）
=================

### **问题起因**
>最近做完了数据库的课设，课也都快结课，考试感觉问题也不大，想着怎么提升一下Python基础知识，碰巧看到了这题。开始我以为是找到一篇文章中出现次数最多的词语，但发现在普通一篇文章里面，is，are,the,a,这种类型的词也是很多的，甚至在统计过程中可能会超过真正的关键词，百度了一下，发现了一个名叫TF-IDF的算法，可以找出一个词语对一个词料库或是文件集中的一份文件的重要程度....

### **大致思路**
>首先，可以有专门的库可以去调用，这里，我采用的是观看了别人的代码后，自己根据思路写的

**1.** 将文件夹遍历，查找日记的txt文件（可能有不是txt的文件的，可以用正则表达式匹配，可能文件夹有多层，可以递归遍历）
**2.** 依此操作每个文本txt，用正则表达式将每个单词分割开，对其中的每个单词求词频（即每个单词在txt中出现的次数），为了避免长的文本出现的单词频率较短文本要高，这里将TF（**词频**）定为 TF = 单词出现的词频/文本中的单词总数
**3.** 对已经完成的每个文本中的单词求IDF（**逆向文件频率**），IDF = log(文件总数/某个单词在文件中出现文件数)，其中对数底数取多少不影响最终结果，当每个文件txt中都存在该单词时，我们有理由认为，该单词是常见词，不应该被认为是具有重要性的词，因此减少该单词的权重（例如在所有的文本中都存在该单词时，IDF值为0）
**4.** 求TF*IDF，比较一个文件中该值的大小，取最大值即可得到该文件中可能的最重要的单词

### **具体实现**
+ 先将文件夹中的文件提取出来，利用函数 os.walk(address) 在文档树中游走就能提取文件。
'''
    代码           for parent , dirnames , filenames in os.walk(address)
parent即是当前正在遍历的这个文件夹本身，dirnames 为当前文件夹下的目录的名字，filenames为当前文件夹里面的文件的名字
'''
由于我的文件夹中的文件大致是这种排列
<img  src = "https://github.com/FindADog/Python/blob/master/gtihub%E5%9B%BE%E7%89%87/%E6%96%87%E4%BB%B6%E6%8E%92%E5%88%97.PNG "></img>
因此不需要递归调用

+ 将每个txt中的单词匹配出来。正则表达式模板：word_pattren = r'\b[a-zA-Z]+\b'
 函数data = re.findall(word_pattren,data)--data为每个txt中的内容
+ 遍历每个文件夹调用求TF-IDF方法

        def travel(address,filenames):
            '''
            遍历文件夹，调用函数算出词频
            :param address: 文件夹的目录
            :param filenames: 里面的文件
            :return:
            '''
            # 匹配txt文本
            pattren = r'.*\.txt'
            for filename in filenames:
                if re.match(pattren, filename):
                    file.append(filename)
            for sigle in file:
                dic[sigle] = {}
                func(address,sigle)
                
依此遍历每个文件txt调用函数完成操作。其中dic为一个全局的空字典，预想是把它的键设为文件名称，值为一个字典，该子字典中，键为每个文件中的每个单词，值为每个词的TF-IDF值。**其中的func即为求出每个txt中的每个单词的TF-IDF值**
+ 完成求TF-IDF值的函数。

       def func(address,filename):
            '''
            求单词的词频,单词在文件中出现的总次数除以文件中单词的总词数
            :param address:文件夹的目录
            :param filename: 单个txt文件
            :return:
            '''
            real_address = os.path.join(address,filename)
            #开始读取文件的单词
            with open(real_address,'r') as fileReader:
                data = fileReader.read()
            fileReader.close()
            data = re.findall(word_pattren,data)
            Data = []
            #不区分大小写
            for word in data :
                word1 = word.lower()
                data.remove(word)
                Data.append(word1)
            data = Data
            #开始计算单词出现的次数
            for word in data:
                try:
                    dic[filename][word] += 1
                except KeyError:
                    dic[filename][word] = 1
            #文件中单词的总词数
            num = len(data)
            #计算词频
            for k,v in dic[filename].items():
                dic[filename][k] = v/num
                
            

该函数主要为了计算每个txt文件的单词的TF。


+ 最后写主函数，计算出TF*IDF并排序，得出每个文章最关键的词，然后写到excel中




### **完整代码**


        '''
        1.先对文件夹进行遍历，满足dirary.txt形式的文件夹才能被选中
        2.对txt文件进行正则匹配，利用TF_IDF算法找出关键字
        '''
        import re
        import os
        import os.path
        from math import log as log
        import xlwt
        
        #单词匹配的正则表达式
        word_pattren = r'\b[a-zA-Z]+\b'
        #txt文本文件
        #键为文件夹名字
        dic = {}
        
        def travel(address,filenames):
            '''
            遍历文件夹，调用函数算出词频
            :param address: 文件夹的目录
            :param filenames: 里面的文件
            :return:
            '''
            # 匹配txt文本
            pattren = r'.*\.txt'
            for filename in filenames:
                if re.match(pattren, filename):
                    file.append(filename)
            for sigle in file:
                dic[sigle] = {}
                func(address,sigle)
        
        
        def func(address,filename):
            '''
            求单词的词频,单词在文件中出现的总次数除以文件中单词的总词数
            :param address:文件夹的目录
            :param filename: 单个txt文件
            :return:
            '''
            real_address = os.path.join(address,filename)
            #开始读取文件的单词
            with open(real_address,'r') as fileReader:
                data = fileReader.read()
            fileReader.close()
            data = re.findall(word_pattren,data)
            Data = []
            #不区分大小写
            for word in data :
                word1 = word.lower()
                Data.append(word1)
            data = Data
            #开始计算单词出现的次数
            for word in data:
                try:
                    dic[filename][word] += 1
                except KeyError:
                    dic[filename][word] = 1
            #文件中单词的总词数
            num = len(data)
            #计算词频
            for k,v in dic[filename].items():
                dic[filename][k] = v/num
        
        
        def out_excel(Data):
        
            import xlwt
        
            args = {}
            args['host'] = '(local)'
            args['user'] = 'sa'
            args['password'] = '123456'
            args['database'] = 'Library_Manager'
        
        
            workbook = xlwt.Workbook(encoding='utf-8')
            booksheet = workbook.add_sheet('Sheet 1', cell_overwrite_ok=True)
            item = ('文件夹',"单词","tf*idf")
            Data.insert(0, item)
            for i, row in enumerate(Data):
                for j, col in enumerate(row):
                    booksheet.write(i, j, col)
            workbook.save(r'C:\Users\YY\Desktop\TF-IDF.xls')
        
        if __name__ == "__main__":
            address = r'C:\Users\YY\Desktop\temp\新建文件夹'
            #找出文件夹中的文件
            filenames = []
            for parent, dirnames, filenames in os.walk(address):
                pass
            travel(address,filenames)
            #已经得出文件中每个单词的词频，现在开始计算IDF
            #IDF = log(总的文件数/单词出现的文件数)
            #最终的最重要的单词将要存放在word中
            word = ''
            #z总文件数
            file_num = len(file)
            max_freq = 0
            Data = []
            data_1 = []
            #开始计算每个单词的idf并进行排序
            for k , v in dic.items() :
                for k1 , v1 in v.items() :
                    i=0
                    for filename in file :
                        if dic[filename].get(k1) is not None:
                            i = i + 1
                    if i ==0 :
                        i = 1
                    idf = log(file_num/i)
                    #计算其tf*idf
                    v[k1] = v1*idf
                    if v[k1] > max_freq :
                        word = k1
                        max_freq = v[k1]
                data_1.append(k)
                data_1.append(word)
                data_1.append(max_freq)
                Data.append(data_1)
                data_1 = []
                max_freq = 0
            print(Data)
            out_excel(Data)file = []
            
            
            
+ 最后excel中的结果：
<img src = "https://github.com/FindADog/Python/blob/master/gtihub%E5%9B%BE%E7%89%87/excel.PNG"></img>


### **总结**

每篇文章应该都有一个侧重点这样分出的关键字才比较准确，当一个词语在一篇文章中多次出现，而在其他的文章中很少出现，这样的词语TF*IDF值就会很大，也会更满足关键字的条件。










