齐治科技-面试
--------------

**自我介绍**

*鉴于这个问题非常常见，而且往往是第一个问题，所以应事先准备一份(中英文)模板化的自我简介*

**选择一个项目经历进行介绍**

*这个要非常慎重，因为面试官很可能会针对你介绍的项目经历详细询问其中的技术细节，以及可能存在的问题。所以应事先熟悉一遍你简历上的项目经历，并重点挑一个做准备。*

**程序设计题：有一文件，里面有各种单词，请统计每个单词的数量，并按数量从大到小排序。**

	def sortFile(filename):
		contentList = list()
		with open(filename) as fh:
			for line in fh:			#本程序假设一行一个单词
				line = line.strip('\n')
				contentList.append(line)
		countingDict = dict()
		for word in contentList:
			if word in countingDict:
				countingDict[word] +=1 
			else:
				countingDict[word] = 1
		items = countingDict.items()
		reverseItems = [(v[1], v[0]) for v in items]
		reverseItems.sort()			#默认从小到大排序
		items = reverseItems[::-1]		#从大到小排序
		for (value, key) in items:
			print key, value

假设现在有多个文件，总大小为100GB，有什么方法可以在一个小时内完成对这些文件内容的排序。(mapreduce)

**电梯算法**

**哲学家筷子问题**

**ls * 与 echo * 的区别(另一个问题：命令行实现对一个文件的内容按行排序，并将排序好的内容存入同一个文件)**

**当你在浏览器导航栏中输入 http://www.google.com.hk 并回车后，直到你完全看到网页内容，这之间发生了哪些事情(浏览器做了哪些事情？以及网络等)**

**HTTP请求消息头以及响应消息头**

**你对公司有什么要求？(工作类型，公司地点，薪资待遇等)**

*1.工作类型：必须是编程的*

**你还有什么想问我们的？**

*1.应该问一些与面试公司相关的问题，比如涉及的某项技术; 2.应该就前面面试中一些你不懂的问题进行询问。*
