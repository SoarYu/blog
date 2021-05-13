# Filted-BLOG
基于Vue + SpringBoot实现前后端分离,带有全站敏感词过滤的极简个人博客系统。敏感词过滤是每个带有用户输入的系统都需要构建的一个功能，当用户提交了一句话或是一篇博客，都需要在后端进行敏感词过滤。当检测到敏感词后，要么提示用户输入包含敏感词要求重新输入，要么把敏感词替换为 “×” 这种特殊字符。
# 主要技术：
## 前端 :
- 核心框架：Vue

- 数据请求和响应：Axios

- 富文本编辑器：mavon-editor
## 后端 :
- 核心框架：SpringBoot 2.2.6

- 数据校验：Hibernate validatior

- 持久层框架：Mybatis

- 数据库：MySQL 5.7

- 密码加密：MD5
# 功能
-  全站敏感词过滤

-  登录验证

-  注册验证

-  文章展示

-  文章添加

-  文章删除

-  文章修改
# 敏感词过滤的方案

## 关于敏感词库
  构建敏感词过滤系统的第一步是需要有一个敏感词库。一般企业都会构建自己的敏感词库，可以在管理后台增删改查这些敏感词。本系统为了敏感词过滤的实现，建立了一份词库并进行了维护，放在了 resources 目录下。

## 敏感词过滤方案：建立索引
  建立敏感词索引的方式类似建立英文词典中的首字母索引。
  
  **敏感词节点数组nodes构建过程:** 按照每个敏感词的头两个字符来计算该敏感词的index和hash，以index值作为该敏感词在数组中的位置下标。因此，头两个字符相同的敏感词index和hash都相同，并保存在同一个节点的集合words中。若头两个字符不同的敏感词计算出来的index相同，此时发生了index相同的冲突，则采用哈希桶(拉链法)来解决哈希冲突，此时根据index相同的两个敏感词hash不同，并组成一个哈希桶。
  可以简单理解为index和hash相同的敏感词头两个字符相同；反之index相同，而hash不同的敏感词头两个字符不同。
  
  **敏感词节点数组结构如下图：** 
    ![SensitiveNodes](https://user-images.githubusercontent.com/51791348/115273040-a665d500-a171-11eb-9922-38425b4e0bdb.png)

  **敏感词过滤过程：** 过滤时按逐个字符遍历输入内容。计算输入内容的头两个字符的index，如果nodes[index]为null,则该字符为非敏感字符。 如果在敏感词节点数组nodes中能找到这个字符的index，则取出该节点的hash进行匹配，若符合则说明头两个字符相同，并对节点中的值集合进行匹配，不符合则通过next来寻找该index哈希桶的下一个节点，直至为空。

比如有这几个敏感词：色情视频、色情电影、色情激情电影观看，那么生成的节点为 {"色情" :["色情视频","色情电影","色情激情电影观看"]}

    如果输入内容为"色情电影在线一起观看"，根据头两个字符"色情"取出敏感词库中的节点的值集合，按敏感词长度由长到短的"色情激情电影观看"开始匹配，直到"色情电影"匹配成功，用指定的内容进行替代，并跳过对应长度，再去看下一个字符，直到输入内容结尾。得到输出过滤后的内容为"××××在线一起观看"。

   这种建立敏感词索引的方式优缺点如下：

优点：缩小待匹配敏感词的范围，能快速找到匹配的敏感词。

缺点：不会命中的敏感词也会进行匹配运算

# 预览
    账号admin 密码123456
  
**1.注册页面**

![register](https://user-images.githubusercontent.com/51791348/115271752-3e62bf00-a170-11eb-8a69-f02f70e47241.png)

**2.登录页面**

![login](https://user-images.githubusercontent.com/51791348/115271873-605c4180-a170-11eb-8500-1cf40028d705.png)

**3.个人文章管理页面**

![user](https://user-images.githubusercontent.com/51791348/115293391-d620d700-a189-11eb-8292-22a4044d0252.png)


**4.博客主页**

![index](https://user-images.githubusercontent.com/51791348/115293441-e638b680-a189-11eb-9556-8e64247bb8b1.png)



**5.文章详细内容**

![details](https://user-images.githubusercontent.com/51791348/115293518-02d4ee80-a18a-11eb-90d9-a84988c258be.png)


**6.文章编辑界面**

![edit](https://user-images.githubusercontent.com/51791348/115293563-11bba100-a18a-11eb-845b-cb63e7ec73df.png)


# 本地运行该项目
**前端:** 

进入blog-vue，打开cmd窗口输入

   npm install

   npm run serve

**后端 :**

使用sql脚本生成对应的数据库

用java1.8以上的idea打开blog-springboot

配置application.yml，

修改对应的数据库连接参数，redis的端口号

