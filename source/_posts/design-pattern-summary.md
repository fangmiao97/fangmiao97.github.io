---
title: 设计模式总结
date: 2019/12/28
categories: 
    - 设计模式
    - 总结
---
# context
设计模式终于看完了，23种比较多。总结一下。

# 设计原则

* 如何同时提高一个软件系统的可维护性和可复用性是面向对象设计需要解决的核心问题之一

## 单一职责原则

Single Responsibility Principle。一个类只负责一个功能领域中的相应职责，或者可以定义为：就一个类而言，应该只有一个引起它变化的原因。

比如，迭代器模式，将迭代的功能与容器的存储分离开。java中的collection接口和Iterator接口充当抽象聚合类和抽象迭代器。各司其职。

## 开闭原则

(Open-Closed Principle, OCP)：一个软件实体应当**对扩展开放，对修改关闭**。即软件
实体应尽量在不修改原有代码的情况下进行扩展。

如果一个软件设计符合开闭原则，那么可以非常方便地对系统进行扩展，而且在扩展时无须修改现有代码，使得软件系统在拥有适应性和灵活性的同时具备较好的稳定性和延续性。

如：1.使用配置文件更改一些信息，不用在代码中改。
2.客户端针对抽象类编程，具体类继承抽象类或实现抽象接口。增添新的具体实现来扩展新的业务功能，而不需要修改抽象层。
减少代码中的if else这样的判断逻辑，使用关联关系/对象引用。

## 里氏代换原则

(Liskov Substitution Principle, LSP)：所有引用基类（父类）的地方必须能透明地使用其子类的对象。

**里氏代换原则是实现开闭原则的重要方式之一**，由于使用基类对象的地方都可以使用子类对
象，因此在程序中尽量使用基类类型来对对象进行定义，而在运行时再确定其子类类型，用
子类对象来替换父类对象。

## 依赖倒转原则
(Dependency Inversion Principle, DIP)：抽象不应该依赖于细节，细节应当依赖于抽象。换言之，要针对接口编程，而不是针对实现编程。

依赖倒转原则要求我们在程序代码中传递参数时或在关联关系中，尽量引用层次高的抽象层类，即使用接口和抽象类进行变量类型声明、参数类型声明、方法返回类型声明，以及数据类型的转换等，而不要用具体类来做这些事情。

## 接口隔离原则

(Interface Segregation Principle, ISP)：使用多个专门的接口，而不使用单一的总接口，即客户端不应该依赖那些它不需要的接口。

在使用接口隔离原则时，我们需要注意控制接口的粒度，接口不能太小，如果太小会导致系统中接口泛滥，不利于维护；接口也不能太大，太大的接口将违背接口隔离原则，灵活性较差，使用起来很不方便。一般而言，接口中仅包含为某一类用户定制的方法即可，不应该强迫客户依赖于那些它们不用的方法。

## 合成复用原则
(Composite Reuse Principle, CRP)：尽量使用对象组合，而不是继承来达到复用的目的。

合成复用原则就是在一个新的对象里通过关联关系（包括组合关系和聚合关系）来使用一些
已有的对象，使之成为新对象的一部分；新对象通过委派调用已有对象的方法达到复用功能
的目的。简言之：复用时要尽量使用组合/聚合关系（关联关系），少用继承。

## 迪米特法则

(Law of Demeter, LoD)：一个软件实体应当尽可能少地与其他实体发生相互作用。

迪米特法则要求我们在设计系统时，应该尽量减少对象之间的交互，如果两个对象之间不必
彼此直接通信，那么这两个对象就不应当发生任何直接的相互作用，如果其中的一个对象需
要调用另一个对象的某一个方法的话，可以通过第三者转发这个调用。简言之，就是通过引
入一个合理的第三者来降低现有对象之间的耦合度。

# 创建型模式
    
## 工厂方法模式
   Factory Method Pattern：定义一个用于创建对象的接口，让子类决定将哪一个类实例化。工厂方法模式使一个类的实例化延迟到其子类。 
   ![](https://pic.downk.cc/item/5e06bb1576085c3289ecd736.jpg)
    
## 抽象工厂模式(Abstract Factory): 
提供一个创建一系列相关或相互依赖对象的接口，而无须指定它们具体的类。
![](https://pic.downk.cc/item/5e06bb4d76085c3289ecdee7.jpg)

## 单例模式(Singleton):
 保证一个类仅有一个实例，并提供一个访问它的全局访问点。
 
    ```java
    public class IoDHSingletonLoadBalancer {
    //可用服务器集合
    private List serverList = null;

    //私有构造方法
    private IoDHSingletonLoadBalancer() {
        serverList = new ArrayList();
    }

    //静态类，直到jvm确定其会被使用，才会初始化
    //也就是在执行getInstance的时候，才会初始化LazyHolder，并初始化静态变量instance
    private static class LazyHolder {
        static final IoDHSingletonLoadBalancer instance = new IoDHSingletonLoadBalancer();
    }

    public static IoDHSingletonLoadBalancer getInstance() {
        return LazyHolder.instance;
    }

    //增加服务器
    public void addServer(String server) {
        serverList.add(server);
    }

    //删除
    public void removeServer(String server) {
        serverList.remove(server);
    }

    //分配服务器（随机）
    public String getServer() {
        Random random = new Random();
        int i = random.nextInt(serverList.size());
        return (String) serverList.get(i);
    }

    public static void main(String[] args) {
        IoDHSingletonLoadBalancer LB1, LB2, LB3;
        LB1 = IoDHSingletonLoadBalancer.getInstance();
        LB2 = IoDHSingletonLoadBalancer.getInstance();
        LB3 = IoDHSingletonLoadBalancer.getInstance();

        if(LB1 == LB2 && LB2 == LB3 && LB1 == LB3) {
            System.out.println("same instance");
        }

        LB1.addServer("server 1");
        LB1.addServer("server 2");
        LB1.addServer("server 3");
        LB1.addServer("server 4");

        for (int i = 0; i < 10; i++) {
            String server = LB1.getServer();
            System.out.println("get server" + server);
        }
    }
    }
    ```
## 建造者模式(Builder):
 将一个**复杂对象**的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。
  ![](https://pic.downk.cc/item/5e06bc6e76085c3289ed0387.jpg)

## 原型模式(Prototype):
用原型实例指定创建对象的种类，并且通过**拷贝**这个原型来创建新的对象。

# 结构型模式

##适配器模式(Adapter):
 将一个类的接口转换成用户希望的另一个接口，使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。
    ![](https://pic.downk.cc/item/5e06bda576085c3289ed2da2.jpg)

## 桥接模式(Bridge):
 **（两个变化维度的组合）**将抽象部分与实现部分分离，使它们都可以独立地变化。
    ![](https://pic.downk.cc/item/5e06be0b76085c3289ed3cfa.jpg)

## 组合模式(Composite):
 将对象组合成树形结构以表示“部分-整体”的层次结构，它使得客户对单个对象和复合对象的使用具有一致性。
    ![](https://pic.downk.cc/item/5e06bee176085c3289ed5ca3.jpg)

## 装饰模式(Decorator): 
动态地给一个对象添加一些额外的职责，就扩展功能而言， 它比生成子类的方式更为灵活。
    ![](https://pic.downk.cc/item/5e06c04876085c3289ed8dc6.jpg)

## 外观模式(Facade):
 **子系统**中的一组接口提供一个一致的界面，定义一个高层接口，这个接口使得这一子系统更加容易使用。
    ![](https://pic.downk.cc/item/5e06c1be76085c3289edbee7.jpg)

## 享元模式(Flyweight)/轻量级模式: 
运用共享技术有效地支持大量细粒度的对象。
    ![](https://pic.downk.cc/item/5e06c42f76085c3289ee3e56.jpg)

## 代理模式(Proxy)：
为其他对象提供一个代理以控制对这个对象的访问。
    ![](https://pic.downk.cc/item/5e06c74d76085c3289ef11c0.jpg)
    
# 行为型模式

## 职责链模式(Chain of Responsibility):
 为解除请求的发送者和接收者之间耦合，而使多个对象都有机会处理这个请求；将这些对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理它。在客户端中设置链。
    ![](https://pic.downk.cc/item/5e06c81476085c3289ef403a.jpg)

## 命令模式(Command):
 将一个请求封装为一个对象，从而可用不同的请求对客户进行参数化；对请求排队或记录请求日志，以及支持可取消的操作。
    ![](https://pic.downk.cc/item/5e06c8fd76085c3289ef7617.jpg)

## 解释器模式(Interpreter):
 **定义语言的文法**，并且建立一个解释器来解释该语言中的句子。
    ![](https://pic.downk.cc/item/5e06ca1176085c3289efb3bf.jpg)

## 迭代器模式(Iterator):
 提供一种方法顺序访问一个聚合对象中各个元素，而又不需暴露该对象的内部表示。
    ![](https://pic.downk.cc/item/5e06cb1476085c3289efeb76.jpg)

## 中介者模式(Mediator):
 用一个中介对象来封装一系列的对象交互；中介者使各对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互。
    多对多关系转换成一对多关系。
    ![](https://pic.downk.cc/item/5e06cba576085c3289f00e73.jpg)

## 备忘录模式(Memento):
 在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态，这样以后就可将该对象恢复到先前保存的状态。
    撤销，游戏存档，ctrlZ
    ![](https://pic.downk.cc/item/5e06cc5876085c3289f036af.jpg)
## 观察者模式(Observer):
 定义对象间的一种一对多的依赖关系，以便当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并自动更新。
    ![](https://pic.downk.cc/item/5e06cee376085c3289f0ce6d.jpg)

## 状态模式(State):
 允许一个对象在其内部状态改变时改变它的行为，对象看起来似乎修改了它所属的类。
      。状态模式将一个对象的状态从该对象中分离出来，封装到专门的状态类
      中，使得对象状态可以灵活变化，对于客户端而言，无须关心对象状态的转换以及对象所处
      的当前状态，无论对于何种状态的对象，客户端都可以一致处理。
    ![](https://pic.downk.cc/item/5e06d05b76085c3289f12543.jpg)
## 策略模式(Strategy):
 定义一系列的算法,把它们一个个封装起来，并且使它们可相互替换，策略模式使得算法的变化可独立于使用它的客户。
    策略模式的主要目的是将算法的定义与使用分开，也就是将算法的行为和环境分开，将算法
    的定义放在专门的策略类中，每一个策略类封装了一种实现算法，使用算法的环境类针对抽
    象策略类进行编程，符合“依赖倒转原则”。
    ![](https://pic.downk.cc/item/5e06d0e276085c3289f144da.jpg)

## 模板方法模式(Template Method):
 定义一个操作中的算法的骨架，而将一些步骤延迟到子类中，使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。
    ![](https://pic.downk.cc/item/5e06d35876085c3289f1d63d.jpg)

## 访问者模式(Visitor):
 表示一个作用于某对象结构中的各元素的操作，可以在不改变各元素的类的前提下定义作用于这些元素的新操作。
    ![](https://pic.downk.cc/item/5e06d3b976085c3289f1ee66.jpg)
          