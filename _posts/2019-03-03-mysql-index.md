---
layout: post
title: MySQL索引探究
date: 2019-03-03 12:49:38 +0800
categories: databse,mysql,innodb
tags: [mysql]
---

### 什么是索引

索引，就是能帮助你快速查找到你想要的数据的数据结构。

能帮助我们快速查找到数据的数据结构非常多，比如有序数据使用二分查找，比如二叉树结构查找，但包括mysql在内的大部分数据存储系统都选择了使用B树或其进化的B+树作为索引的数据结构，下面我们就先认识一下B树，再讨论一下为什么B树是适合数据存储场景的索引数据结构。

### 什么是B树

B树(B-tree，又名B-树，B-树===B树，不存在“B减树”)，又名Balance树，顾名思义，这种树可以维持自身平衡，同时还具有二叉树及其变种不具备的特点。

我们先来看一张B树的示例图，对照图片，认识B树的特性：

![](http://mcace.me/assets/images/2019/mysql-index/8.jpg)

B树有几个特性：

1. B树是一种多叉树结构，相对于二叉树，每个节点可以保存1个以上的关键字以及拥有2个或2个以上的孩子。
2. 一个非叶子节点的孩子数等于关键字个数+1，因为非叶子节点中伴随着每个关键字都会有左右两个子节点的指针。在图中非叶子节点只有根节点这一个，可以看到其内部保存了2条数据[20,data\]和[40,data\]，也就是拥有2个关键字，同时在数据之间还保存了指向每个关键字左右子节点的指针，合起来是3个指针，指向3个子节点。
3. B树中的每个节点都会保存数据，也就是节点里的每个关键字都有对应的数据保存在一起，这一点和B+树是不同的，后面会讲。
4. 所有叶子节点都在同一层，并且指向下一层的指针为null
5. 这一条看看就好：一个m阶(m>=2)的B树，每个节点最多有m个子节点，m-1个关键字；其中根节点至少有1个关键字；除根节点外，其他节点至少有ceil(m/2)（m/2向上取值）个子节点，也就是其他节点一定有(ceil(m/2)-1)~(m-1)个关键字；叶子节点虽然没有子节点，但同样一定有(ceil(m/2)-1)~(m-1)个关键字。举例来说，图中展示一个3阶的树，根节点有3个子节点，2个关键字，而叶子节点则有ceil(3/2)-1=1~2个关键字。
6. 同样也是看看就好：上面是按阶来描述B树，还有一种按度来描述B树的方法。一个t度(t>1)的树，每个非叶子节点有t~2t个子节点，也就是有(t-1)~(2t-1)个关键字；每个叶子节点至少包含1个关键字和2个指针，指针的值为null。一个t度的树，也可以称为2t阶的树。
7. 所有节点数据都按照关键字从小到大排列，对于同一个节点中如果有关键字k1 < k2,则对于k1和k2之间的子树所包含的所有关键字kn都有k1 < kn < k2。在图中这一点也很清楚地展示了。
8. 我们可以总结出，B树中的一个节点，其内容就是m+1条数据和m个指针。
9. 一个B树在存储管理系统里所具有的m阶，或t度，由节点大小及数据和指针大小所影响，节点的空间越大，数据、指针越小，阶就越大。第8条我们总结了一个节点的内容，这里我们可以假设存储管理系统里，分配给一个节点的空间为K，一条数据大小为N，一个指针大小为M，那么可以算出阶m就等于(k-M)/(M+N)+1，也就是空间大小减去一个指针的大小，再除以指针和数据之和，然后再加1。而m越大，树就可以在越低的高度下储存更多的数据。但还要注意，节点空间变大了，一次加载到内存里的数据量也会变大，因此节点设置大小是一个需要讨论的事情，这涉及到硬盘读取数据的问题，会在后面讲。
10. 附加一条提示，阶m代表一个节点最多有m个子节点，有的资料会称该节点“扇出为m”，是一个意思。

举例说明，在前面的示意图中，我要找关键字为22的数据，因此我先去根节点获取到关键字的线性表，然后开始遍历这个线性表，每次比对关键字的值，当小于22时就继续向后找，遍历到40时，由于它大于22，因此我就拿到该节点的左子节点指针，接着我继续遍历左子节点的关键字，找到了关键字等于22的那条数据，这样我就得到了数据。同理我找关键字为23的数据时，当找到36这条数据时，由于其左子节点指针为null，因此这次查找就等于没有找到23这条数据。

总结一下：

1. 从数据结构来讲，B树是一种多叉树，每个节点内包含了很多条数据，因此也可以在很低的高度容纳很多的节点，他的查找既包含了树形结构的查找，也包含了线性表的查找。
2. 从数据存储角度来定义的话，B树是一种广泛用于大规模数据存储的数据结构，它可以有效地降低低速IO的次数，同时维持高效的查找。

至于B树的各种操作，这里就不详细叙述了，在下面的外链里有相关资料。

### 为什么比二叉树好？

如果只用一个字形容，那就是“快”。为什么快，就是本段要解决的内容。

前面我从数据存储的角度定义了B树，进一步扩展来说：由于磁盘中的数据是无序的，因此需要单独维护一个索引表文件才能保证相关数据结构的查找，而随着数据越来越多，索引表也会越来越大，一次把整个索引表读入内存进行查找是不现实的，因此就涉及到索引读取到内存的IO效率。而对于B树、B+树来说，这种数据结构能使树的高度维持在一定范围内，同时每个节点既可以存储数据，也可以是树的节点，并且每个节点都可以构成多叉树结构，使其查询效率和磁盘IO的综合表现高于其他数据结构。

精炼地讲，之所以B树能降低IO，是因为其高扇出性和高效利用磁盘IO，之所以能维持高效的查找，是因为其拥有树形结构查询的效率。

先谈索引表：

不只是二叉树，我们有很多种方法来快速地得到一个数据，比如说在一个有序的数据结构里，用二分查找法，可以在O(logN)的时间复杂度内找到数据，同理二叉树也一样。

但稍微分析一下即可得知，首先二分查找法是不可能应用的，因为数据存储必然是无序的，即使是有序，二分查找法的IO也会非常频繁。二叉树也同理，单靠数据本身维护结构，对于这两种方式来说是不可能的。

因此，在这种情况下，我们要维护一个索引表，使用时把索引表加载到内存中进行查找，然后再进行磁盘IO。

这里盗用文章[MySQL索引背后的数据结构及算法原理](http://blog.codinglabs.org/articles/theory-of-mysql-index.html)的一张图来说明这个情况：

![](http://mcace.me/assets/images/2019/mysql-index/7.jpg)

于是就会有一个问题，数据越来越多，索引表也越来越大，一次将索引全部加载到内存中再进行查找，是十分浪费内存空间的一种低效率的行为，而多次加载又涉及到多次IO，和B树对比就显得很差了。

为什么B树能有效降低IO次数？简单讲，就是设计者利用了操作系统页加载机制，巧妙的设计数据节点的大小，结合B树高扇出性的特性，使得每次磁盘IO都可以得到最大化的效率。

详细点说就是这样：

1. 首先，操作系统文件管理会将磁盘按页为单位划分成一块一块的区域，磁盘和内存的交换单位是页，每次从磁盘里读数据到内存时，读出的数据大小必然是页大小的倍数，即使你只想取其中的某一条只有1B的数据。
2. 一般操作系统划分的页大小是4KB。
3. 同时，每次读入时会有一定的预读，并非只是单纯地读一页，一般会预读3页，也就是一次磁盘IO会读取4页的数据到内存中，合计16KB。预读是利用局部性原理，作为提高IO效率的一种优化手段。
4. 设计者将一个B树节点定为16KB，并且每次新建一个节点时，都直接申请16KB的空间给节点存储数据，这样就能保证一个节点的数据在磁盘里是按16KB整齐连续地存放着，因此每次IO都可以正好加载一个节点到内存中。
5. 于是在根节点常驻内存的情况下，一个高度为H的树，索引到叶子节点的IO次数就为H-1。

现在你应该明白为什么B树更适合数据存储管理了吧，我们可以通过简单地计算来看看B树的威力：

设主键id为4字节，数据为4字节，指针大小在InnoDB一般为4字节。

一个节点16kb=16384B，那么这个树的阶我们就很好算了，上一节有讲过：(16384-4)/(4+4+4)+1=1366，也就是这个树只有根节点时，能存1365条数据。

这是高度H为1的树，当高度为2时，能存多少条数据也很好算：1365 + 1366 * 1365 = 1865955

如果只是估算的话，也可以直接用1366+1366^H来算，当高度为3时，这个数字是2548897262，这已经是25亿条数据了，要查到叶子节点，其IO也不过是3-1=2次，而把节点加载到内存后查找指针的速度相对IO的速度来说要快得多，所以整体上速度优化点还是在IO上。看到B树的威力了吧，如果是用索引页+二叉树，找到某个叶子节点估计要到猴年马月去啊！

反过来也能通过数据量和阶来算树高，假如一个B树的阶为m，数据量为N，则树高H=logm(N)，也就是m ^ H = N。

### 进化：B+树

也许看到上面的计算，你会有一个疑问：你在这里把主键和数据都设置得太小了，假如每条数据占了几十几百甚至上千字节，那么这颗B树能容纳的节点数在相同高度下也会相应变少，这时还能体现出效率了吗？

没错，正因为有这一点存在，所以MySQL并没有采用B树作为索引的数据结构，而是其变种B+树，我们简单地看看B+树有什么不同，就能解决这个问题了：

1. B+树中，数据只存在叶子节点中，非叶子节点只存关键字和指针。
2. 非叶子节点中，每个关键字对应一个指针，并且该关键字是其对应子节点的起始关键字。（也就是上层存放的关键字是下层的最小值）（读不明白看一下下面的图就明白了）（此处有一些争议，在[wiki](https://zh.wikipedia.org/wiki/B%2B%E6%A0%91)上，B+树的结构和B树一样，每个关键字都有左右子节点，但MySQL实现的B+树是每个关键字对应一个子节点，因此这里以MySQL为准）
3. 所有叶子节点构成一个双向链表，以方便地在叶子节点之间遍历，而不用再回到父节点取指针。

就这么简单地变动，使得B+树在存储索引方面比B树更加出色，我们来看一下B+树的示例图：

![](http://mcace.me/assets/images/2019/mysql-index/9.jpg)

B+树除了能索引更多数据节点外，还有一些其他特性：

1. 由于只有叶子节点存储数据，并且叶子节点的高度是一样的，因此B+树定位到任意一个叶子节点锁消耗的时间是相等的。
2. 由于叶子节点间构成链表，因此遍历所有叶子节点更加简单快速。B树由于没有链表结构，遍历节点效率非常低。因此B+树在范围内遍历的效率也是非常高的。
3. 在MySQL中，如果索引使用了组合索引（即索引的列大于1个），则会使用最左匹配规则，按照索引列从左到右开始建立索引，详细内容可以参考[《Mysql联合索引最左匹配原则》](https://segmentfault.com/a/1190000015416513)和知乎问题[mysql索引最左匹配原则的理解?](https://www.zhihu.com/question/36996520)

虽然B+树有诸多优点，但B树也有其自身的优点，由于每个节点都可以储存数据，因此节点越接近根节点，查询该节点的效率也就更高，试想在B树中有某个热点数据距离根节点很近，那么整体上来看B树的效率是会比B+树要高的。

### B+树的实现：聚集索引，非聚集索引

我们前面讲B树相对其他数据结构的优点时，有提到快速检索数据的两种方式：数据本身的结构有序，或是维护一个索引表。实际上在投射到B树索引的具体实现时，也按照这两种类型分为：聚集索引(Clustered Index，又译为聚合索引、聚簇索引)，非聚集索引（Nonclustered Index，又称非聚合索引、非聚簇索引）。

因此，聚集索引和非聚集索引，不只是数据的索引，也是数据存储的方式。聚集索引就相当于数据本身结构有序，而非聚集索引就相当于维护了一个索引表。

因此，他们最大的不同就在于聚集索引的节点即是索引的一部分，也会用于存储数据，而非聚集索引的节点不会存储数据。

在MySQL中，InnoDB使用聚集索引组织，MyISAM使用非聚集索引组织。本文在聚集索引部分会着墨较多，非聚集索引则一笔带过。

### 什么是聚集索引

在字面上来讲，这里“聚集”的含义就是数据按照一定顺序排列存储，而聚集索引，就是可以使数据按照一定顺序排列存储的索引。

一个聚集索引有下面这些特性：

1. 聚集索引的叶子节点用于保存数据，非叶子节点只存键和节点指针。
2. 聚集索引可以通过主键约束(Primary Key)或唯一性约束(Unique Index)创建，一般使用主键约束。这是B+树中节点里键的来源。
3. 聚集索引会使数据在物理上按照B+树的排列顺序存储，也因此一张表只能创建一个聚集索引。
4. 虽然聚集索引定义了数据在物理存储上要和B+树顺序是一致的，但实际存储时，不可能真正使数据在物理层面严格按照B+树的顺序存储，所以数据库实现聚集索引时，会通过在各个节点保存各种指针来保证逻辑上的有序。具体来说，InnoDB在存储时，索引页及索引页中各数据行的存储有可能上是物理上无序而通过指针来维持逻辑上有序的，然，如果数据是按键的单调顺序插入，并且保证不更新数据，同时删除也不是真正删除行数据而是采用标记删除时，则数据必然是物理上有序的。

在mysql里，想要避免经常变动数据导致聚集索引效率变差，可以定期使用optimize table这个命令，它可以整理表空间文件里的各种碎片，移除各种mysql标记删除的列，释放无用空间，不过这个命令也有一些特性，也不是随便用的，可以参考一下[案例 - optimize table 的一些坑](http://blog.51cto.com/dadaman/1957229)。

### InnoDB的聚集索引

我们前面讲过的，InnoDB以页为单位管理数据，数据页有不同的类型，其中最常见的就是索引页，这里的索引页，对应到B+树中就是每个节点。

下面是一个InnoDB聚集索引实现的示意图：

![](http://mcace.me/assets/images/2019/mysql-index/14.jpg)

可以看到叶子节点包含了完整的数据，非叶子节点则只有键和页指针，并且页与页之间通过双向链表连接起来。

InnoDB的聚集索引有如下几个特性：

1. 在创建表时，如果定义了一个主键(Primary Key)，则会在主键上创建聚集索引。
2. 如果没有定义主键，则会在第一个唯一索引(Unique Index)上创建聚集索引。
3. 如果主键和唯一索引都没有定义，则InnoDB会自动添加一个隐藏的字段"GEN_CLUST_INDEX",该字段使用包含行ID的内容作为表的主键，在其上创建聚集索引。

也就是说，InnoDB表会强制创建聚集索引。

好……到这里终于回到正题了，之所以我会从索引讲到B树，再讲到聚集索引，是因为InnoDB中，每个ibd文件里所包含的索引页，就是使用聚集索引的方式来排列存储的。

下面来验证InnoDB创建的隐藏字段"GEN_CLUST_INDEX"，首先创建一个InnoDB表，定义主键id：

![](http://mcace.me/assets/images/2019/mysql-index/17.jpg)

查看InnoDB的索引，查看SQL如下：

``` SQL
SELECT
    t. NAME AS table_name,
    f. NAME AS field_name,
    i. NAME AS index_name
FROM
    information_schema.INNODB_SYS_TABLES AS t,
    information_schema.INNODB_SYS_INDEXES AS i,
    information_schema.INNODB_SYS_FIELDS AS f
WHERE
    t.table_id = i.table_id
AND t. NAME = 'demos/innodb_demo'
AND f.INDEX_ID = i.INDEX_ID
```

结果如图：

![](http://mcace.me/assets/images/2019/mysql-index/19.jpg)

接着将主键取消掉，这时InnoDB会自动创建一个隐藏字段"GEN_CLUST_INDEX"，并创建相关的索引，由于该字段不会出现在INNODB_SYS_FIELDS表中，因此将该表查询相关语句去掉后，结果如下图：

![](http://mcace.me/assets/images/2019/mysql-index/20.jpg)

验证结束，InnoDB确实创建了这个隐藏字段。

### 什么是非聚集索引

前面讲了“聚集”的意义，那么对于一个索引来说，只要数据的排列存储顺序不受其影响，它就是“非聚集”的。

非聚集索引相对于聚集索引来说，具有以下几个特性：

1. 其叶子节点不会保存数据，而是保存指向对应数据的地址。
2. 数据存储在物理上不需要连续，而是通过索引保持逻辑上的连续。
3. 因为不会影响数据存储结构，所以一个表可以创建多个非聚集索引。
4. 非聚集索引的键不强制具有唯一性。
5. 在插入和更新上的效率比聚集索引要高。

### MyISAM的非聚集索引

MyISAM没有聚集的概念，它的数据存储方式永远不会受索引控制。前面我们讲了，一个索引可以通过主键、具有唯一性的列创建，也可以通过普通列创建，在MyISAM中，这几种索引创建出来都是非聚集的。也正因此，MyISAM表可以一个索引都没有（主键也是索引，所以也可以没有）。

下面是一个MyISAM的主键索引（这里我盗图了），以列col1作为主键：

![](http://mcace.me/assets/images/2019/mysql-index/10.jpg)

可以看到，叶子节点存放的数据是真实数据的地址。

并且MyISAM将表空间数据和表空间索引分成两个文件存储，分别为后缀MYD和MYI。

下面我们创建一张以MyISAM为引擎的表，并且插入2000条数据，观察生成的文件：

![](http://mcace.me/assets/images/2019/mysql-index/12.jpg)

![](http://mcace.me/assets/images/2019/mysql-index/13.jpg)

其中MYD文件是表空间数据文件，MYI文件是表空间索引文件。

接着我们在name上创建索引，观察MYI文件大小变化来验证索引保存在MYI文件中：

![](http://mcace.me/assets/images/2019/mysql-index/15.jpg)

前面说了InnoDB表会强制使用聚集索引，但MyISAM表并不强制使用索引，当我取消主键并删除索引后，MYI文件大小变成下面这样：

![](http://mcace.me/assets/images/2019/mysql-index/16.jpg)

### 辅助索引(Secondary Indexes)

辅助索引是相对聚集索引的概念，所有的辅助索引都是非聚集索引。有的文章也会称之为“二级索引”，是一个东西。

_有的文章把MyISAM的Primary Key和Secondary Key称为主索引和辅助索引，我个人持保留意见，因为真的没有找到MyISAM Secondary Index相关的信息，我个人觉得把主索引和辅助索引的概念套到MyISAM中是错误的。_

对于一个强制使用聚集索引的数据库存储引擎（比如InnoDB），所有其他的索引都会基于这个聚集索引建立。

辅助索引的特点如下：

1. 节点的键是列中的数据。
2. 叶子节点中存放的是主索引的键值。
3. 创建辅助索引会复制对应列的数据。
4. 在辅助索引上搜索得到结果后，会拿着这些键值再去主索引搜索对应数据（从辅助索引到主索引这个过程被称为回表）。
5. 如果查询的列在辅助索引里就可以得到，那么就可以直接返回值，省去回表过程，充分利用辅助索引，这种情况属于辅助索引把要查询的列覆盖到了，这种方式使用索引获取数据叫做覆盖索引。
6. 一个表可以有多个辅助索引。

下图左半部分展示了一个辅助索引的示例：

![](http://mcace.me/assets/images/2019/mysql-index/21.jpg)

_这里有一个我也暂时没办法解答的问题，就是创建辅助索引时，是按照什么样的规则对字段进行排序的？比如int类型可以由大到小，单个字符可以按照字的字节码排序，那如果复杂一点，一个长度不定的字符数组，比如"abcd""c""fgh"，又是怎样排序的？甚至对于变长类型，一个长度为1的字符和长度为500、800甚至1000的字符，又是怎么排序的？这个问题我暂时没有答案。_

辅助索引会有一些缺点：

1. 辅助索引创建涉及到复制对应列的数据，这需要很大的开销，这种开销可以通过创建快速索引来规避。
2. 通过辅助索引查询相对MyISAM的索引查询来说，多了一次从主索引再次检索的步骤，但这也不算什么缺点吧，只能算一个特性。

创建一个辅助索引，需要考虑覆盖多少列、覆盖的列字段长度、覆盖列是否会被经常查询，要避免在过长的字段上创建辅助索引引起的存储资源浪费，要权衡维护辅助索引对表整体修改性能的影响。

### 加餐：B树分裂

在B树插入时，假如被插入的节点数据达到了上限，那么就会触发这个节点的分裂，这就是B树分裂，这里有一篇[从MySQL Bug#67718浅谈B+树索引的分裂优化](http://hedengcheng.com/?p=525)，作者是搞数据库开发的大牛，文章写得通俗易懂，可以轻松理解B树的分裂。

### 后记

本来我只是想通过写聚集索引来描述它是怎么影响InnoDB存储数据的，结果没想到顺着这个杆越爬越高了，其实聚集索引的概念很简单，只是我想展开来讲，把相关的知识都整合起来，才好真正做到理解融汇贯通。