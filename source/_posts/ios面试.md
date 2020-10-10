[TOC] 
#### 一、UI相关

1. UItableView缓存机制
2. UI数据源同步

    
#### 二、OC语言相关
#### 三、Runtime相关
#### 四、内存管理相关

1.  循环引用
    相互循环
    多循环
    考点：代理、block、NSTimer、大循环引用
    解决方法：
    a.__weak
    b.__block
    c._unsafe_unretain
    
    NSTimer的循环引用：添加中间对象解决循环引用
#### 五、Block

1. ![71715074e4aa2427a18920a46c920c12.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p10)

![fd9a2ad3a65128467f299d69152445e2.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p11)
![1bb866a06ce36f5272a0bc015af73441.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p12)
![d8ed8b8095872bf14eb4bf188e0b33fc.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p13)
![f329472bd29e55155487ecc4b31bb80e.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p14)
![3af40dc609f2418a0eaffa5dbf7de53f.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p15)

截获变量：
全局变量
全局静态变量
局部变量
局部静态变量

![3d0d5833bec820538701a193c7abda11.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p19)
![dc9f00d7e7ca21080120a6fc02ddd89d.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p17)
只有初始化的时候需要加 __Block
![5044e14bb5a30d0aa14101199b20228c.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p18)
![0107a2f05272182cc4c06f976737e5f1.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p20)
![abfd6c3a486ffd2dc504091a4013a5cb.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p21)

Block 循环引用
![911d89c327baa64be7d7858562f3fa45.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p22)
![27f38cec44c6749c726ad0c1352ea0a4.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p23)
![06a6f84b1dda88348a4f24930f59db26.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p24)
![4974c75988c0ccf9c23fbfe66e3ff7fd.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p25)
![16541de8e02aa4f49d2e84814ab422ff.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p26)
![832ae7d0f3694081efcf4f2f5f120619.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p27)
如果这个block不调用那么这个环就一直存在
![7207cf2cd48b9bdc3af05cfe7a6e812a.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p28)

#### 六、多线程
![bf29c7bf31cf3107944c32a28349ecdf.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p29)
![cb06d2eeb5d1f456d0626b55b5c47c65.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p30)
![c1770292dd4ab2c0a38d07a5dd5afb10.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p31)
![1340d9a812177f70a90e35d8bc5e36e4.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p32)
多读单写，如何实现
![59b9e9d3c70fac07dd97683c871fc493.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p33)

![ca89dc444a2d64c0ccca0fbfcd924ba2.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p34)


NSThread：
![e1ea8ec70322e1c6a512eeb3e9af3282.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p35)

锁：
![03a8a8aa16dafb8d3e76a624f552b1fd.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p36)
![d134e7afd5c00213cc67ba16e406d2dd.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p38)

![d18383fcf5229fe8a55f14ceb68e14c3.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p39)
![bd1399070cc53cb70077db383823544f.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p40)
![8ceaab5f1f54601be29b1c6b656bab60.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p41)
![1506be2e0e1ff3ef33a894c2bac0d321.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p42)
![10de1f365d1d040eb32371612002859d.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p43)
解决：递归锁
![1e8203ac1757eb92033b181bd2065ec1.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p44)
面试总结：
![14056aeffcd88516cb7bcc827215e0f5.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p45)

#### 七、Runloop

![1bd89279a6e95c47f072295a28b98ce6.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p48)
![da8bab394dc0ae91af765172425a47ae.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p47)
![cd90012ffeb00779d17bdd09e4e96ed6.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p49)

#### 八、网络相关
1.考察点：
![887fdf6c4cd4e2d2863b68ed496796ef.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p50)
![2ee4ab8fc449f7da21e996d6c1574eb1.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p51)
请求报文和响应报文的具体内容
get 和post 方式的 区别
![f0a6c8eb9429f03d6a7dfe9bd129e0f8.png](evernotecid://4A5CC5DE-71E0-4F00-84D1-654DEE4C0AD5/appyinxiangcom/10649105/ENResource/p52)

http特点：
1.持久连接

udp/tcp
  


#### 九、设计模式
#### 十、架构、框架
#### 十一、算法
#### 十二、三方库
#### 十三、工作中

1.   多线程使用场景
        1.1 分析sdk中sqlite的使用
        1.2 instalSDK中,初始化接口使用信号量同步网络接口,同时不阻塞主线程，开辟一个新的非主线程来执行网络接口，并使用信号量来同步网络接口
2.   runtime使用场景
        2.1 拦截Appdelegate,并实现拦截里面的代理方法
        2.2 分析sdk中页面自动采集，交换viewWillAppear和viewWillDisappear方法
        2.3 Install场景还原,获取app中所有注册的类，并判断类中是否实现了路径方法，并执行该方法回去路径,这里采用C++全局静态变态，app初始化时就会立即执行代码
3.   
