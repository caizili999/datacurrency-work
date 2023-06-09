# howtrader框架使用过程中遇到的常见问题


1. 如何启动和运行 howtrader？
   不建议直接下载源码来安装，直接创建虚拟环境，这里最好用python3.9版本。然后通过``pip
   install git+https://github.com/51bitquant/howtrader`` 方式来安装。

   安装完成后，创建一个python项目，然后把github代码仓库的``main_window.py``拷贝进去，
   然后设置你的解析器为刚才安装的howtrader的解析器。然后运行``main_window.py``就可以了。
 
2. 为啥安装/更新 howtrader 后，为啥提示没有安装howtrader或者版本还是旧的版本？

    这个问题很多人遇到，主要还是没有搞清楚虚拟环境或者解析器的问题。你可能安装了多个解析器，你要确认下你用的是哪个解析器，可以通过
    conda activate 来激活你的虚拟解析器，然后再安装 howtrader. 最后在 pycharm 
    编辑器 中设置你的解析器。

3. 为啥下载的数据回测时提示没有数据？ 

    这个问题是很多遇到的问题，这个首先检查要看你运行的脚本是否和存放数据的howtrader文件是否处于同一个层级，
    只有处于同个层级下载的数据才保存到howtrader/database.db文件里面。另外你运行的回测文件也要和howtrader文件夹处于同个层级，
    只有这样，它加载的数据才是从howtrader/database.db文件读取数据。如果检查这个两个步骤没有问题，就要检查你database.db文件是否有你加载的交易对的信息。
    简单就是用pycharm打开database.db文件，或者下载一个sqlite数据库软件，例如navicat等。

4. 为啥交易所仓位和实盘的仓位不相等？

    首先要确保你使用的howtrader更新到最新的版本，目前最新版本是3.3.0，
    不然websocket断开的时候，如果下单有成交，那么websocket收不到成交的推送，就会导致仓位会出错。另外我们要理解howtrader里面的仓位机制，
    为了方便多个策略可以同时交易同个交易对，howtrader采用谁下单，仓位的归结于哪个策略。假设你有A、B、C三个策略，A策略下单做多3个ETH,B策略做空2个ETH，
    C策略做空一个ETH，那么此时交易所是没有仓位，但是每个策略都是有自己的仓位的。如果他们要平仓，策略记录的self.pos值分别为3，-2，-1。对于CTA策略来说，策略的仓位记录在self.pos变量中，
    有变化的时候会写入howtrader/cta_strategy_data.json文件中，如果你的策略重启，或者想改变这个值，可以打开这个文件，然后修改pos的变量。记得修改后，要重启软件才生效。

5. 为啥网格策略没法进行回测？

    由于策略里面下单用到实盘行情tick数据，所以没法回测，如果要回测，可以参考课程的回测讲解，把代码修改下就可以进行回测。

6. 如何添加自己的策略代码？

    在howtrader文件夹的旁边，或者是启动的文件(main_window.py)旁边创建strategies文件，该文件夹和启动文件是属同个层级，然后把你策略文件放进strategies文件里面，
    需要注意的是你策略的类名不要跟系统的策略类名相同，不然加载不出你的策略。

7. howtrader
   是单向持仓，多个策略交易如何交易同个交易对呢？比如跑ethusdt这个币种，有策略A
   策略B、策略C、3个策略一起跑，那得设置双向持仓吧？ 否则 不同策略有时候会
   一个开多，一个开空，这个问题如何解决？
   
   howtrader 里面，每个策略下单，它成交的记录和回报都是发给对应的策略。A
   策略下单，那么成交都是通过 on_order, on_trade 的方法推送给 A 策略，跟
   B策略没有任何关系。也就是谁下单，谁负责维护自己的仓位，假设A策略下单做多(buy)3个ETH,
   成交了两个后，撤单，那么A策略的self.pos 变量就是2， 如果B策略做空2个ETH,
   完全成交后，B策略的self.pos变量是-2，
   此时交易所是没有任何仓位的，如果你此时手动下单，那么A和B策略也是不会改变它的self.pos变量的。那么如果你想平仓，策略自己平仓就可以了。完全没有任何影响。

