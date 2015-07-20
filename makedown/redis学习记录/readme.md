# Redis读书记录

        ----

        ## redis简介
        >redis是个开源的高性能键值对数据库，通过提供多种键值数据类型来适应不同场景下的存储需求，并借助许多高层及的接口使其可以胜任如缓存、队列系统等不同的角色。（[详情猛戳这里](http://baike.baidu.com/link?url=iaJhNbvLm4Nrf7kwtZeiEB_lUfBdHWN6tlY1HoME842GmpsAWVslG9mBaUfhJ_1rQZ97Q_DoFEpcxnTBWNJWU_)）

        ----

        ## redis下载和安装
        ####下载：
        >redis官方下载地址：http://redis.io/download 。
        redis约定第一个小数点后的数字为偶数的版本是稳定版，如2.6，奇数是非稳定版如2.7，推荐使用稳定版进行开发和生产环境使用。（[猛戳下载稳定版](http://download.redis.io/releases/redis-3.0.2.tar.gz)）

        #### 官网安装方式：
        ![Alt text](res/redis-install.png)

        ####mac平台上可以用`brew install redis`命令来进行自动下载和安装。
        ####brew是mac上安装homebrew后才又的命令，原生mac os不自带。
        ####mac上安装homebrew的方式：
        `ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`
        ####homebrew： 通过它可以在终端里方便的使用它的brew命令安装管理苹果没有自带的UNIX相关工具软件，是程序员神器啊。

        ----

        ## redis使用介绍
        ######redis的一些可执行文件
        ```
        文件名						  说明
        redis-server				Redis服务器
        redis-cli					Redis 命令行客户端
        redis-benchmark				Redis 性能测试工具
        redis-check-aof				Redis AOF文件修复工具
        redis-check-dump			RDB   文件检查工具
        ```
        - 启动redis：
        启动redis有直接启动和通过初始化脚本启动两种方式，分别用于开发环境和生产环境，下面只说直接启动。
        直接启动 ：运行`redis-server`
        ![Alt text](res/redis-start1.png)

        - 停止redis
        考虑到redis可能正在将内存中的数据同步到硬盘中，强行终止redis进程可能导致数据丢失，正确定制redis的方式应该是向redis发送shutdown命令。
        方法为：`redis-cli SHUTDOWN`
        当redis受到SHUTDOWN命令后，会先断开所有客户端连接，然后根据配置执行持久化，最后完成退出。redis可以妥善处理SIGTERM信号，所以使用“kill redis   进程的PID”也可以正常结束redis，效果和发送SHUTDOWN命令一样。

        - redis命令行客户端
        redis-cli程序是redis自带的基于命令行的redis客户端，也是我们学习和测试redis的工具，后面使用他来讲解redis的命令用法。

        - 发送redis命令
        通过redis-cli向redis发送命令有两种方式：
        1、将命令作为redis-cli的参数执行，比如redis-cli SHUTDOWN。
        2、直接运行redis-cli，这样会进入交互模式，可以自由的输入命令。
        ![Alt text](res/redis-cli.png)
        注意：
        redis-cli的前提是运行redis-server启动了reids。
        运行redis客户端带上--raw参数，可以让中文数据正常显示。否则客户端中的中文数据都显示这样的字符 :`"\xe6\x88\x91\xe4\xbb\xac"  `
        `redis-cli --raw`
        --raw            Use raw formatting for replies (default when STDOUT is not a tty)

        - 命令的返回值
        大多数情况下执行命令我们都会关心命令的返回值，命令返回值有5种类型，对于每种类型redis-cli的展现结果都不同。
        1、 状态回复
        状态回复（status reply）是最简单的一种回复，比如发送set命令设置某个键的值时，redis会回复状态OK表示设置成功,另外对PING命令的回复PONG也是状态回复，状态回复直接显示状态信息。
        ![Alt text](res/state-reply.png)
        2、 错误回复
        出现命令不存在或错误等情况，redis会返回错误回复error reply，错误回复以error开头，并在后面跟上错误信息。
        如果执行一个不存在的命令：
        ![Alt text](res/error-reply.png)
        3、整数回复
        redis没有整数类型，却提供了一些用于整数操作的命令，如递增键值的INCR命令会以整数形式返回递增后的键值，	除此外，一些其他命令也会返回整数，如可以获取当前数据库中键的数量的DBSIZE命令等，整数回复以(integer)开头，
        并在后面跟上整数数据。
        ![Alt text](res/integer-reply.png)
        4、字符串回复
        这是最常见的回复类型，当请求一个字符串类型键的键值或一个其他的类型键中的某个元素时就会得到一个字符串回复，字符串回复以双引号包裹。
        ![Alt text](res/string-reply.png)
        5、 多行字符串回复
        多行字符串回复multi-bulk replay很常见，如当请求一个非字符串类型键的元素列表时就会收到多行字符串回复。多行字符串回复中的每行字符串都以一个序号开头。
        ![Alt text](res/strings-reply.png)

        ----

        ## redis一些基本命令
        ####获得符合规则的键名列表
        `keys pattern `
        ![Alt text](res/all-keys.png)
        pattern支持glob风格通配符格式

        ####判断一个键是否存在
        `exists key `存在返回整数类型1，否则返回0
        ![Alt text](res/all-keys.png)

        ####删除键
        `del key [key ...] `可以删除一到多个键，返回值是删除的键的个数
        ![Alt text](res/del-key.png)
        >技巧： del不支持通配符，但可以结合linux的管道和xargs命令自己实现删除所有符合规则的键，比如删除所有以user:开头的键盘，可以`redis-cli KEYS "user:*" | xargs	 redis-cli del` ,另外由于del支持多个键作为参数，所以还可以执行`redis-cli del 'redis-cli keys "user:"'`达到同样效果，性能还更好。

        ####获得键值的数据类型
        ` type key`
        返回值可能是string hash散列类型 list列表 set集合 zset有序集合
        ![Alt text](res/type.png)

        ----

        ## redis数据类型介绍
        ####字符串类型

        ######字符串类型能存储任何形式的字符串，包括二进制数据，包括二进制数据，一个字符串类型的键存储的数据最大容量是512MB。该类型是其他4种类型的基础，其他类型某种意义上来讲只是组织字符串的形式不同，比如列表类型是以列表形式组织字符串。

        ######------操作字符串类型的命令------
        1. **赋值与取值**
        `set key value 和 get key`
        ![Alt text](res/get-set.png)
        2. **递增数字**：
        `INCR key`
        前面说过字符串类型可以存储任何形式的字符串，当存储的字符串是整数形式时，redis提供`incr`命令，作用是让当前值递增，并返回递增后的值。
        ![Alt text](res/incr.png)
        当键不存在的时候执行incr 键，则默认从0开始，所以第一次执行incr 不存储在键，结果是1，也就是初始值是1，如果键存在，但是键的初始值是纯字符串，则会报错。
        ![Alt text](res/incr-detail.png)
        3. **增加指定的整数**：
        `incrby key increment`
        和incr命令一样是递增，不过他不是一次递增1，而是increment代表的数值地增量，比如incrby num 4，就是一次加4。
        ![Alt text](res/incrby.png)
        4. **增加指定浮点数**：
        `incrbyfloat key increment`
        `incrby`增加浮点数会报错，而incrbyfloat能增加浮点数。
        ![Alt text](res/by-float.png)
        5. **向尾部追加值**：
        `append key value`
        `incrby`增加浮点数会报错，而incrbyfloat能增加浮点数。
        ![Alt text](res/append.png)
        6. **同时获得/设置多个键值**：
        `mget key1 [key2 ...]`
        `mset key1 value1 [key2 value2 ...]`
        ![Alt text](res/mgs.png)

        ####散列类型
        ######redis是采用字典结构以键值对的形式存储数据的，而散列类型(hash）的键值也是一种字典结构，其存储了字段和字段值的映射，而字段值只能是字符串，不支持其他数据类型，也就是说散列类型不能嵌套其他的数据类型，一个散列类型可以包含最多2的32次方 - 1个字段。

        ######注意：

        ######除了散列类型，redis其他数据类型都不支持数据类型嵌套，比如集合类型的每个元素都只能是字符串，不能是另一个散列表或集合。

        ######散列类型适合存储对象：使用对象类别和ID构成键名，使用字段表示对象的属性，而字段值则存储属性值。

        ######------操作散列类型的命令------
        1. **赋值与取值**
        `hset key field value
        hget key field
        hmset key field value [field value ...]
        hmget key field [field ...]
        hgetall key`
        hset不区分插入和更新操作，意味着修改数据时，不需要判断字段是否存在来执行插入或更新，当执行插入操作时候，hset会返回1，当执行更新操作（原来字段存在），hset返回0，当键不存在，hset会自动建立他。
        ![Alt text](res/hset.png)
        redis中每个键都属于一个明确的数据类型，通过hset命令建立的键是散列类型，set命令建立的时字符串类型，使用一种数据类型的命令去操作另一种数据类型的键会报错。
        ![Alt text](res/hset-error.png)
        一次设置多个字段的值和取多个字段的值：
        ![Alt text](res/hgsList.png)
        2. **一次查看散列类型的键中所有字段的值**
        `hgetall key`
        ![Alt text](res/all-filed.png)
        3. **判断字段是否存在**
        `hexists key field`
        存在返回1，否则0，如果键不存在也是0
        ![Alt text](res/hexists.png)
        4. **当字段不存在时赋值**
        `hsetnx key field value`
        他和hset类似，区别是如果事先如果字段field已经存在，该命令将不执行任何操作(nx表示if not exists如果不存在)
        ![Alt text](res/hexistsSet.png)
        5. **增加数字**
        `hincrby key field increment`
        前面介绍了字符串类型的incrby命令，用于递增数字，散列类型通过hincrby来实现。如果字段不存在，则该命令会创建这个字段，字段在执行hincrby命令前的初始值是0，返回值则是增值后的字段值。
        ![Alt text](res/hincrby.png)
        6. **删除字段**
        `hdel key field [field ...]`
        可以删除一个或多个字段，返回值是删除的字段个数
        ![Alt text](res/hdel.png)
        7. **只获取字段名或字段值**
        `hkeys key` 只获取key中所有的字段名
        `hvals key` 只获取key中所有的字段值
        ![Alt text](res/hkeys-hvals.png)
        8. **获取字段数量**
        `hlen key`
        ![Alt text](res/hlen.png)

        ####列表类型
        ######列表类型list可以存储一个有序的字符串列表，常用的操作是想列表两端添加元素或者获得列表的某个片段。列表类型内部都是使用双向链表实现的，所以向列表两端添加元素的时间复杂度为O(1),获取越接近两端的元素速度越快，意味着即使是一个有几千万元素的列表，获取头尾的10条记录也是很快的，这和从一个20个元素的列表获取头尾的10条记录是一样的。使用链表的代价是通过索引访问元素比较慢，比如访问第50个元素，要从0数到50很慢。使用列表类型存储，即使有几千万数据，获取其中最新的100条也是很快的，同样在两端插入数据的时间复杂度是O(1),列表类型适合记录日志，可保证加入新日志速度不会受已有日志数量影响。借助列表类型，redis还可做队列使用，后面介绍。与散列类型键最多能容纳的字段数量相同，一个列表类型最多能容纳2的32次方 - 1个元素。

        ######------列表类型的命令------
        1. **向列表两端增加元素**
        `lpush key value [value ...] `
        列表左边添加元素，返回值表示添加元素后，列表的长度
        `rpush key value [value ...]`
        列表右边添加元素，返回值表示添加元素后，列表的长度
        ![Alt text](res/lpush.png)
        2. **从列表两端弹出元素**
        `lpop key`
        `rpop key`
        ![Alt text](res/lpop.png)
        lpop命令执行两个步骤：第一将列表左边的元素从列表中移除，二是返回被移除的元素值，rpop和lpop同理，只是方向改为了右边。结合上面4个命令可以模拟栈和队列的操作，如果想把列表当做栈，则搭配使用lpush和lpop或rpush和rpop，如果想当成队列，则搭配使用lpush和rpop或rpush和lpop。
        3. **获取列表中元素的个数**
        `llen key`
        当key不存在或者元素个数为0都是返回0。
        ![Alt text](res/llen.png)
        4. **获得列表片段**
        `lrange key start stop`
        lrange能获得列表中的某一片段，他将返回索引从start到stop之间的所有元素（包含两端的元素），redis列表的起始索引为0。
        ![Alt text](res/lrange.png)
        lrange支持负号索引，-2表示从右边数下标为2的元素开始。比如 lrange numbers 0 -1 可以取到所有元素。
        5. **删除列表中指定的值**
        `lrem key conunt value`
        当：
        count > 0 : 命令会从列表左边开始删除前count个值为value的元素
        count < 0 : 命令会从列表右边开始删除前count个值为value的元素
        count = 0 : 命令会删除所有值为value的元素
        ![Alt text](res/lrem.png)
        6. **获得/设置指定索引的元素值**
        `lindex key index`
        `lset key index value`
        ![Alt text](res/lindex.png)
        7. **只保留列表指定片段**
        `ltrim key start end`
        该命令可以删除start 到 end范围之外的所有元素。
        ![Alt text](res/ltrim.png)
        8. **向列表中插入元素**
        `linsert key before|after pivot value`
        该命令会在列表中从左到右查找值为pivot的元素，然后根据before还是after来决定将value插入到该元素的前面还是后面。命令返回值是插入元素后列表的元素个数。
        ![Alt text](res/linsert.png)

        ####集合类型
        ######集合中每个元素都是不同的，且无序，一个集合类型set键可以存2的32次方 -1 个字符串。集合类型常用操作是向集合中加入或删除元素、判断某个元素是否存在等，由于集合类型在redis内部是使用值为空的散列表（hash table）实现的，所以这些操作的时间复杂度都是O(1),最方便的是多个集合类型键之间还可以进行并集、交集、和差集运算。
        ######------集合类型的命令------
        1. **增加/删除元素**
        `sadd key member [member ...]`
        `sren key member [member ...]`
        sadd 用来向集合中添加一到多个成员，如果键不存在会自动创建，因为一个集合中不能有相同的元素，所以如果加入的元素已经存在，就会忽略这个元素，命令返回值是成功加入的元素数量（忽略的元素是不计算的）。
        sren 从集合中删除一个或多个元素，并返回删除成功的个数。
        ![Alt text](res/sadd-srem.png)
        2. **获得集合中的所有元素**
        `smembers key`
        ![Alt text](res/smembers.png)
        3. **判断元素是否存在集合中**
        `sismember key member`
        这个操作是一个时间复杂度为O（1）的操作，无论集合中有多少元素, SISMEMBER 命令始终可以极快的返回结果，存在返回1，否则0。
        ![Alt text](res/sismember.png)
        4. **集合运算**
        (1) 差集运算
        `sdiff key [key ...]`
        sdiff命令用来对多个集合执行差集运算，集合A与集合B的差集表示为A-B，代表所有属于A不属于B的元素构成的集合。比如 {1,2,3} - {2,3,4} = {1}
        ![Alt text](res/sdiff.png)
        (2) 差集运算
        `sinter key [key ...]`
        SINTER用来对多个集合进行交集运算。
        ![Alt text](res/sinter.png)
        (3) 差集运算
        `sunion key [key ...]`
        sunion命令用来对多个集合执行并运算,该运算代表所有属于A或者属于B的元素构成的集合。{1，2，3} ∪ {2，3，4} = {1, 2，3, 4}
        ![Alt text](res/sunion.png)
        5. **获得集合中元素个数**
        `scard key`
        ![Alt text](res/scard.png)
        6. **进行集合运算并将结果存储**
        `sdiffstore destination key [key ...]`
        `sinterstore destination key [key ...]`
        `sunionstore destination key [key ...]`
        三个命令的功能和sdiff、sinter、sunion相同，区别是回把返回的结果存储在destination中。destination是存储结果的新key，destination后面的key是要进行集合运算的键。
        ![Alt text](res/AandB.png)
        7. **随机获得集合中的元素**
        `srandmember key [count]`
        该命令用来随机从集合中获取一个元素，count可以不指定，指定了，就是随机获取count个元素。
        ![Alt text](res/srandmember.png)
        8. **从集合中弹出一个元素**
        `spop key`
        他的作用和之前学的什么lpop命令类似，返回一个元素值并把它删除，因为集合是无序的，所以该命令会从集合中随机选择一个元素弹出。
        ![Alt text](res/spop.png)

        ####有序集合类型
        ######有序集合类型的特点从他的名字中就可以知道和上节的集合类型区别就是有序二字。在集合类型的基础上有序集合类型集合为集合中的每个元素都关联了一个分数，这使得我们不仅可以完成插入、删除、和判断元素是否存在等类型的操作，还能获得分数最高（或最低）的前N个元素、获得指定分数范围内的元素等与分数有关的操作。虽然集合中每个元素都是不同的.但他们的分数可以相同。
        ######有续集合类型在某些方面和列表类型有些相似。
        1）二者都是有序的
        2）二者都可以获得某一范围的元素。
        ######区别很大，使得他们的应用场景也是不同的
        1)列表类型是通过链表实现的，获取靠近两端的数据速度极快，而当元素增多后，访问中间数据的速度会较慢，所以他更适合实现如“新鲜事”或“日志“这样很少访问中间元素的应用。
        2）有序集合类型是使用散列表和跳跃表skip list来实现的，所以即使读取位于中间部分的数据速度会很快（时间复杂度是O(log(N))）。
        3)列表中不能简单的调用某个元素的位置，但是有序集合可以（通过更改这个元素的分数）。
        4) 有序集合要比列表类型更耗费内存。
        ######------有序集合类型的命令------
        1. **增加元素**
        `zadd key score member [score member ...]`
        zadd 命令用来向有序集合中加入一个元素和该元素的分数，如果该元素已经存在则会用新的分数替换原有的分数。 zadd的返回值是新加入到集合中的元素个数（不包括之前已经存在的元素）。
        ![Alt text](res/zadd.png)
        2. **获得元素的分数**
        `zscore key member`
        ![Alt text](res/zscore.png)
        3. **获得排名在某个范围的元素列表**
        `zrange key start stop [WITHSCROES]`
        `zrevrange key start stop [WITHSCROES]`
        zrange命令会按照元素分数从小到大的顺序返回索引从start到stop之间的所有元素（包含两端的元素）。zrange命令与lrange命令十分相似,比如索引都是从0开始，负数代表从后向前查找（-1表示最后一个元素）。
        如果要同时获得元素的分数可以在最后加上withscroes参数，这是返回值就从元素1，元素2。。。编程元素1，分数1，元素2，分数2。。。元素n，分数n。
        ![Alt text](res/zrange.png)
        zrevrange 命令和zrange 的唯一不同的地方在于zrevrange命令式按照元素分数从大到小的顺序给出结果的。
        4. **获得指定分数范围的元素**
        `zrangebyscore key min max [withscores] [limit offset count]`
        ![Alt text](res/zrangebyscore.png)
        5. **增加某个元素的分数**
        `zincrby key increment member`
        ![Alt text](res/zrangebyscore.png)![Alt text](res/zincrby.png)
        6. **获取集合中元素的数量**
        `zcard key`
        ![Alt text](res/zcard.png)
        7. **获得指定分数范围内的元素个数**
        `zcount key min max`
        ![Alt text](res/zcount.png)
        8. **删除一个或多个元素**
        `zrem key memeber [member ...]`
        返回值是成功删除的元素数量（不包含本来就不存在的元素）
        ![Alt text](res/zrem.png)
        9. **按照排名范围删除元素**
        `zremrangebyrank key start stop`
        该命令会按照元素分数从小到大的顺序（即索引0表示最小的值）删除在指定排名范围内的所有元素，并返回删除的元素数量。就是从下标start开始一直删到stop为止。
        ![Alt text](res/zremrangebyrank.png)
        10. **按照分数范围删除元素**
        `zremrangebyscore key min max`
        该命令删除指定分数范围的所有元素，min和max参数和前面的zrangebyscore中的main、max参数特性一样，返回值是删除的元素数量。
        ![Alt text](res/zremrangebyscore.png)
        11. **获得元素的排名**
        `zrank key member`
        `zrevrank key member`
        zrank按照元素分数大小从小到大的顺序获得指定的元素的排名（从0开始，即分数最小的元素的排名为0）。
        zrevrank是分数最大的元素排名为0。也就是一个升序，一个降序。
        ![Alt text](res/zrank.png)

        ----

        ## 生存时间
        ####实际开发中会遇到一些有时效的数据，比如限时优惠、缓存、验证码等。过一定时间就得删除他们。在关系型数据库中需要额外的一个字段记录到期时间，然后定期检测删除过期的数据。redis中可以使用expire命令设置一个键的生存时间，到时间后redis会自动删除它。
        `命令语法：expire key seconds`
        ####seconds表示键的生存时间，单位是秒，要想然session:29e3d键在15分后被删除：

        ![Alt text](res/expire.png)
        ####expire返回1表示设置成功，0表示键不存在或设置失败。
        ####如果想知道一个键还有多久时间被删除，用ttl命令，返回值是键的剩余时间，单位是秒：
        ![Alt text](res/ttl.png)
        ####ttl命令注意：如果键不存在了，则返回-2 ，如果键存在，但是没有设置生存时间，返回-1，否则返回剩余时间。

        ----
        #好了，暂时就介绍到这里，剩下的如果有时间，会持续更新，谢谢观看。