
同步/异步日志系统
===============
同步/异步日志系统主要涉及了两个模块，一个是日志模块，一个是阻塞队列模块,其中加入阻塞队列模块主要是解决异步写入日志做准备.
> * 自定义阻塞队列
> * 单例模式创建日志
> * 同步日志
> * 异步日志
> * 实现按天、超行分类

使用单例模式创建日志系统，对服务器运行状态、错误信息和访问数据进行记录，该系统可以实现按天分类，超行分类功能，可以根据实际情况分别使用同步和异步写入两种方式。

异步写入方式：将生产者-消费者模型封装为阻塞队列，创建一个写线程，工作线程将要写的内容push进队列，写线程从队列中取出内容，写入日志文件。

## 单例模式
实现思路：私有化构造函数，以防止外界创建单例类的对象；使用类的私有静态指针变量指向类的唯一实例，并用一个公有的静态方法获取该实例。

```cpp
class single{
    private:
    static single *p;
    static pthread_mutex_t lock;
    single(){
        pthread_mutex_init(&lock,NULL);
    }
    ~single(){}
    public:
    static single* getinstance();
};

pthread_mutex_t single::lock;

// 懒汉模式
single* single::p = NULL;
single* single::getinstance(){
    // 双检测：仅在第一次创建单例的时候加锁，其他时候直接返回p
    if(NULL == p){
        pthread_mutex_lock(&lock);
        if(NULL==p){
            p = new single;
        }
        pthread_mutex_unlock(&lock);
    }
    return p;
}
```