





------

### **创建型模式（Creational Patterns）**：

1. **单例模式（Singleton）**：确保类只有一个实例，并提供全局访问点。
2. **工厂方法模式（Factory Method）**：定义创建对象的接口，让子类决定实例化哪一个类。
3. **抽象工厂模式（Abstract Factory）**：提供一个接口，创建一系列相关或相互依赖的对象，而无需指定具体类。
4. **建造者模式（Builder）**：分步骤构建复杂对象，允许不同的构建方式。
5. **原型模式（Prototype）**：通过复制现有对象来创建新对象。

### **结构型模式（Structural Patterns）**：

1. **适配器模式（Adapter）**：将一个类的接口转换为客户端期望的另一种接口。
2. **桥接模式（Bridge）**：将抽象与实现分离，使它们可以独立变化。
3. **组合模式（Composite）**：将对象组合成树形结构，使客户端可以一致地处理单个对象和组合对象。
4. **装饰器模式（Decorator）**：动态地给对象添加职责，而不修改类定义。
5. **外观模式（Facade）**：为复杂系统提供一个简化的接口。
6. **享元模式（Flyweight）**：通过共享对象来减少内存使用，提高性能。
7. **代理模式（Proxy）**：为其他对象提供代理，以控制对这个对象的访问。

### **行为型模式（Behavioral Patterns）**：

1. **责任链模式（Chain of Responsibility）**：使多个对象都有机会处理请求，形成链式处理。
2. **命令模式（Command）**：将请求封装为对象，使得客户端可以用不同请求参数化操作。
3. **解释器模式（Interpreter）**：给定语言的文法，定义一个解释器用来解释该语言中的句子。
4. **迭代器模式（Iterator）**：提供一种方法顺序访问集合中的各个元素，而不暴露集合的内部表示。
5. **中介者模式（Mediator）**：通过一个中介对象来处理对象之间的通信，避免对象直接交互，减少耦合。
6. **备忘录模式（Memento）**：在不破坏封装的前提下，捕获并保存对象的内部状态，以便之后恢复。
7. **观察者模式（Observer）**：定义对象间一对多的依赖，当一个对象改变状态时通知依赖它的所有对象。
8. **状态模式（State）**：允许对象在内部状态变化时改变其行为，状态模式看起来像是改变了对象的类。
9. **策略模式（Strategy）**：定义一系列算法，并将每个算法封装起来，使它们可以相互替换。
10. **模板方法模式（Template Method）**：在基类中定义算法的骨架，将某些步骤延迟到子类中实现。
11. **访问者模式（Visitor）**：封装作用于某对象结构中的操作，可以在不改变各元素类的前提下定义作用于这些元素的新操作。

------

### 

### 1. **单例模式 (Singleton Pattern)**

#### **目的**：

单例模式的主要目标是确保某个类只有一个实例，并提供一个全局的访问点。例如，在一个大型系统中，通常有一些全局资源需要被共享，比如日志系统、数据库连接等，这些资源就可以用单例模式来实现。

#### **详细类比**：

假设你在一个公司里，公司里只有一台打印机。无论谁想打印文件，都需要通过这台唯一的打印机。如果你让每个员工都有一台自己的打印机，不但浪费资源，还会导致过度的管理问题。这台打印机就像单例对象，每个人都能访问它，但只有这一台。

#### **详细实现**：

假设我们要实现一个简单的日志系统，它记录程序的运行日志。整个程序里应该只有一个日志系统实例，否则多个日志文件会造成管理混乱。

```
cpp复制代码#include <iostream>
#include <fstream>
#include <string>

// 单例类
class Logger {
private:
    static Logger* instance;
    std::ofstream logFile;

    // 私有构造函数，确保外部无法直接创建对象
    Logger() {
        logFile.open("log.txt", std::ios::app); // 打开日志文件，追加模式
    }

public:
    // 静态方法，获取唯一实例
    static Logger* getInstance() {
        if (instance == nullptr) {
            instance = new Logger();
        }
        return instance;
    }

    // 记录日志
    void log(const std::string& message) {
        logFile << message << std::endl;
    }

    // 析构函数关闭文件
    ~Logger() {
        if (logFile.is_open()) {
            logFile.close();
        }
    }
};

// 初始化静态成员
Logger* Logger::instance = nullptr;

int main() {
    // 获取日志系统实例
    Logger* logger = Logger::getInstance();

    // 记录日志
    logger->log("Program started.");
    logger->log("Something happened.");
    logger->log("Program ended.");

    return 0;
}
```

**细节分析**：

- 这里我们实现了一个`Logger`类，确保所有日志记录都写入到同一个`log.txt`文件中。程序中，任何地方都可以通过`Logger::getInstance()`来获取这个唯一的`Logger`实例。
- 私有构造函数防止直接实例化，只有通过`getInstance()`方法才能获取实例。

------

### 2. **工厂模式 (Factory Pattern)**

#### **目的**：

工厂模式的目标是让创建对象的过程更灵活。通常情况下，你可能有很多类型的对象，每种类型的创建过程可能略有不同。工厂模式通过定义一个统一的接口，让子类决定要创建哪个具体的类对象。

#### **详细类比**：

想象你是一家汽车制造商，你有多种类型的汽车（比如SUV、轿车、跑车）。每种类型的汽车制造过程是不同的，你不希望工人们每次手动选择如何制造一辆车，而是通过一个统一的工厂接口，告诉工厂生产什么类型的车，工厂自己决定如何去制造。

#### **详细实现**：

现在我们扩展这个例子，假设我们有不同类型的汽车（`SUV`和`Sedan`），并且我们需要一个工厂来根据需求生产不同类型的汽车。

```
cpp复制代码#include <iostream>
#include <string>

// 汽车基类
class Car {
public:
    virtual void drive() = 0; // 纯虚函数，强制子类实现
    virtual ~Car() {} // 虚析构函数，确保子类可以正确析构
};

// 具体产品类 - SUV
class SUV : public Car {
public:
    void drive() override {
        std::cout << "Driving an SUV!" << std::endl;
    }
};

// 具体产品类 - 轿车 (Sedan)
class Sedan : public Car {
public:
    void drive() override {
        std::cout << "Driving a Sedan!" << std::endl;
    }
};

// 工厂类
class CarFactory {
public:
    // 根据类型返回不同的汽车对象
    Car* createCar(const std::string& type) {
        if (type == "SUV") {
            return new SUV();
        } else if (type == "Sedan") {
            return new Sedan();
        }
        return nullptr; // 如果类型不匹配，返回空指针
    }
};

int main() {
    CarFactory factory;

    // 生产一辆SUV
    Car* suv = factory.createCar("SUV");
    suv->drive(); // 输出 "Driving an SUV!"

    // 生产一辆Sedan
    Car* sedan = factory.createCar("Sedan");
    sedan->drive(); // 输出 "Driving a Sedan!"

    // 清理内存
    delete suv;
    delete sedan;

    return 0;
}
```

**细节分析**：

- `Car`是一个抽象基类，它定义了一个接口（`drive()`），不同类型的汽车（如`SUV`和`Sedan`）会根据自身实现来提供具体行为。
- `CarFactory`类根据传入的字符串类型（如"Sedan"或"SUV"）来创建具体的汽车对象，调用者无需知道如何创建这些对象的具体细节。

------

### 3. **抽象工厂模式 (Abstract Factory Pattern)**

#### **目的**：

抽象工厂模式提供了创建一组相关或相互依赖对象的接口，而无需指定具体的类。它将产品的具体类的创建延迟到子类中。

#### **详细类比**：

想象你有两家不同风格的家具工厂，一家专门生产现代风格的家具，另一家专门生产古典风格的家具。如果你想为你的客厅选购一套现代风格的家具，你只需要告诉现代家具工厂，而不用关心每件家具是如何制造的。这个工厂会给你提供一整套符合风格的家具。

#### **详细实现**：

我们继续家具的例子，使用抽象工厂模式来创建不同风格的家具（现代风格和古典风格）。

```
cpp复制代码#include <iostream>
#include <string>

// 抽象产品类 - 椅子
class Chair {
public:
    virtual void sitOn() = 0;
    virtual ~Chair() {}
};

// 抽象产品类 - 桌子
class Table {
public:
    virtual void placeItems() = 0;
    virtual ~Table() {}
};

// 具体产品类 - 现代风格椅子
class ModernChair : public Chair {
public:
    void sitOn() override {
        std::cout << "Sitting on a modern chair!" << std::endl;
    }
};

// 具体产品类 - 现代风格桌子
class ModernTable : public Table {
public:
    void placeItems() override {
        std::cout << "Placing items on a modern table!" << std::endl;
    }
};

// 具体产品类 - 古典风格椅子
class ClassicChair : public Chair {
public:
    void sitOn() override {
        std::cout << "Sitting on a classic chair!" << std::endl;
    }
};

// 具体产品类 - 古典风格桌子
class ClassicTable : public Table {
public:
    void placeItems() override {
        std::cout << "Placing items on a classic table!" << std::endl;
    }
};

// 抽象工厂类
class FurnitureFactory {
public:
    virtual Chair* createChair() = 0;
    virtual Table* createTable() = 0;
    virtual ~FurnitureFactory() {}
};

// 具体工厂类 - 现代风格家具工厂
class ModernFurnitureFactory : public FurnitureFactory {
public:
    Chair* createChair() override {
        return new ModernChair();
    }
    Table* createTable() override {
        return new ModernTable();
    }
};

// 具体工厂类 - 古典风格家具工厂
class ClassicFurnitureFactory : public FurnitureFactory {
public:
    Chair* createChair() override {
        return new ClassicChair();
    }
    Table* createTable() override {
        return new ClassicTable();
    }
};

int main() {
    // 创建一个现代风格的家具工厂
    FurnitureFactory* modernFactory = new ModernFurnitureFactory();

    // 生产现代风格的椅子和桌子
    Chair* modernChair = modernFactory->createChair();
    Table* modernTable = modernFactory->createTable();
    
    modernChair->sitOn(); // 输出 "Sitting on a modern chair!"
    modernTable->placeItems(); // 输出 "Placing items on a modern table!"

    // 清理内存
    delete modernChair;
    delete modernTable;
    delete modernFactory;

    // 创建一个古典风格的家具工厂
    FurnitureFactory* classicFactory = new ClassicFurnitureFactory();

    // 生产古典风格的椅子和桌子
    Chair* classicChair = classicFactory->createChair();
    Table* classicTable = classicFactory->createTable();

    classicChair->sitOn(); // 输出 "Sitting on a classic chair!"
    classicTable->placeItems(); // 输出 "Placing items on a classic table!"

    // 清理内存
    delete classicChair;
    delete classicTable;
    delete classicFactory;

    return 0;
}
```

**细节分析**：

- `FurnitureFactory`是抽象工厂，定义了`createChair()`和`createTable()`两个抽象方法。具体的工厂类（如`ModernFurnitureFactory`和`ClassicFurnitureFactory`）根据不同风格实现这些方法。
- 这样，我们可以创建一整套符合特定风格的家具，而不需要关心每件家具的具体创建细节。

------

### 4. **建造者模式 (Builder Pattern)**

#### **目的**：

建造者模式的目的是通过一步步地构造一个复杂的对象，从而允许使用同样的构造过程创建不同表示的对象。

#### **详细类比**：

假设你在一家快餐店订购汉堡套餐。你可以根据自己的口味选择不同的配料（比如是否加芝士、是否有生菜等），但最终的结果是一个完整的汉堡套餐。建造者模式就是这个过程的实现，它一步步地添加组件，最后组合出一个完整的对象。

#### **详细实现**：

我们扩展汉堡套餐的例子，用建造者模式实现自定义汉堡套餐。

```
cpp复制代码#include <iostream>
#include <string>

// 汉堡类，表示我们要构建的复杂对象
class Burger {
public:
    bool hasCheese = false;
    bool hasTomato = false;
    bool hasLettuce = false;

    void show() {
        std::cout << "Burger with:" 
                  << (hasCheese ? " Cheese" : "")
                  << (hasTomato ? " Tomato" : "")
                  << (hasLettuce ? " Lettuce" : "") 
                  << std::endl;
    }
};

// 建造者类
class BurgerBuilder {
private:
    Burger* burger;

public:
    // 构造函数初始化一个新的汉堡对象
    BurgerBuilder() {
        burger = new Burger();
    }

    // 加芝士
    BurgerBuilder* addCheese() {
        burger->hasCheese = true;
        return this;
    }

    // 加番茄
    BurgerBuilder* addTomato() {
        burger->hasTomato = true;
        return this;
    }

    // 加生菜
    BurgerBuilder* addLettuce() {
        burger->hasLettuce = true;
        return this;
    }

    // 返回最终的汉堡对象
    Burger* build() {
        return burger;
    }
};

int main() {
    // 创建一个汉堡建造者
    BurgerBuilder* builder = new BurgerBuilder();

    // 根据用户需求添加配料
    Burger* myBurger = builder->addCheese()->addTomato()->build();

    // 显示汉堡的最终配料
    myBurger->show(); // 输出 "Burger with: Cheese Tomato"

    // 清理内存
    delete myBurger;
    delete builder;

    return 0;
}
```

**细节分析**：

- `BurgerBuilder`允许用户根据需要逐步添加配料，每次添加后返回自身（实现链式调用）。最终通过`build()`方法返回构建好的汉堡对象。
- 这种模式适合用于构建步骤繁多且需要灵活配置的复杂对象。

------

这些更详细的例子希望能帮助你更好地理解每个设计模式的实用性和实现过程。请告诉我你对这些模式的理解是否清晰，或者是否需要我进一步解释C++的某些相关概念，比如类、继承、多态或内存管理等。





1. **创建型模式**（解决如何创建对象的问题）
2. **结构型模式**（解决如何组织类和对象的问题）
3. **行为型模式**（解决类和对象之间如何交互的问题）

你刚刚看到的四种模式——**单例模式**、**工厂模式**、**抽象工厂模式**和**建造者模式**——都属于**创建型模式**。接下来，我将继续为你介绍其他设计模式，包括**结构型模式**和**行为型模式**中的一些重要模式。

------

### 5. 原型模式 (Prototype Pattern)

**类型：创建型模式**

#### **目的**：

通过复制（克隆）已有对象来创建新对象，而不是通过实例化类来创建。

#### **类比**：

假设你要创建一栋相同结构的房子，而有现成的蓝图。每次你只需复制蓝图，而不需要重新设计。原型模式就是这种复制的过程。

#### **详细实现**：

在C++中，原型模式通常通过一个“原型”对象的复制功能（通常使用`clone()`方法）来创建新的实例。

```
cpp复制代码#include <iostream>
#include <string>

// 抽象原型类
class Prototype {
public:
    virtual Prototype* clone() = 0;
    virtual void show() = 0;
    virtual ~Prototype() {}
};

// 具体原型类1
class ConcretePrototypeA : public Prototype {
private:
    std::string type;
public:
    ConcretePrototypeA() : type("Type A") {}
    
    Prototype* clone() override {
        return new ConcretePrototypeA(*this);  // 复制当前对象
    }

    void show() override {
        std::cout << "Prototype: " << type << std::endl;
    }
};

// 具体原型类2
class ConcretePrototypeB : public Prototype {
private:
    std::string type;
public:
    ConcretePrototypeB() : type("Type B") {}

    Prototype* clone() override {
        return new ConcretePrototypeB(*this);  // 复制当前对象
    }

    void show() override {
        std::cout << "Prototype: " << type << std::endl;
    }
};

int main() {
    // 创建原型对象
    Prototype* prototypeA = new ConcretePrototypeA();
    Prototype* prototypeB = new ConcretePrototypeB();

    // 克隆原型对象
    Prototype* cloneA = prototypeA->clone();
    Prototype* cloneB = prototypeB->clone();

    // 显示克隆结果
    cloneA->show();  // 输出 "Prototype: Type A"
    cloneB->show();  // 输出 "Prototype: Type B"

    // 清理内存
    delete prototypeA;
    delete prototypeB;
    delete cloneA;
    delete cloneB;

    return 0;
}
```

**细节分析**：

- 通过`clone()`方法，原型模式让我们无需直接实例化类，而是通过克隆已有对象来生成新的对象。这适合用于需要频繁创建大量相似对象的场景。

------

### 6. 适配器模式 (Adapter Pattern)

**类型：结构型模式**

#### **目的**：

适配器模式将一个类的接口转换为客户端期望的另一种接口，使原本不兼容的类能够一起工作。

#### **类比**：

假设你有一个老式的电器，它只能插入圆形插座，而你当前只有方形插座。你需要一个“适配器”（转换插头）来将圆形插头适配到方形插座。适配器模式就是通过中间层实现这种接口转换的方式。

#### **详细实现**：

假设我们有一个旧的`OldPrinter`类，它只有`printOld()`方法，但我们需要使用一个新的`Printer`接口，且接口定义了`print()`方法。

```
cpp复制代码#include <iostream>
#include <string>

// 新接口
class Printer {
public:
    virtual void print(const std::string& content) = 0;
};

// 旧类，拥有不兼容的新接口的功能
class OldPrinter {
public:
    void printOld(const std::string& content) {
        std::cout << "Old Printer: " << content << std::endl;
    }
};

// 适配器类，将 OldPrinter 适配到 Printer 接口
class PrinterAdapter : public Printer {
private:
    OldPrinter* oldPrinter;  // 持有旧类的实例

public:
    PrinterAdapter(OldPrinter* printer) : oldPrinter(printer) {}

    void print(const std::string& content) override {
        oldPrinter->printOld(content);  // 调用旧类的功能
    }
};

int main() {
    OldPrinter* oldPrinter = new OldPrinter();
    Printer* adapter = new PrinterAdapter(oldPrinter);

    adapter->print("Hello, World!");  // 输出 "Old Printer: Hello, World!"

    delete oldPrinter;
    delete adapter;

    return 0;
}
```

**细节分析**：

- `PrinterAdapter`将`OldPrinter`的`printOld()`方法适配成`Printer`接口的`print()`方法，解决了新旧接口不兼容的问题。

------

### 7. 装饰器模式 (Decorator Pattern)

**类型：结构型模式**

#### **目的**：

动态地给对象添加额外的职责，而不改变对象的结构。装饰器模式提供了比继承更灵活的功能扩展方式。

#### **类比**：

想象你喝一杯咖啡，基础的咖啡是黑咖啡。如果你想加奶、加糖等，这些都是装饰。装饰器模式允许你在基础功能上动态添加这些附加功能，而不需要改变咖啡的原有制作过程。

#### **详细实现**：

假设我们有一个基本的`Coffee`类，它有一个`cost()`方法来计算价格。装饰器模式允许我们动态为`Coffee`添加不同的附加功能，如“加奶”和“加糖”。

```
cpp复制代码#include <iostream>

// 基础接口
class Coffee {
public:
    virtual double cost() = 0;
    virtual ~Coffee() {}
};

// 具体组件类 - 基础咖啡
class BasicCoffee : public Coffee {
public:
    double cost() override {
        return 5.0;  // 黑咖啡价格
    }
};

// 装饰器基类
class CoffeeDecorator : public Coffee {
protected:
    Coffee* baseCoffee;

public:
    CoffeeDecorator(Coffee* coffee) : baseCoffee(coffee) {}

    virtual ~CoffeeDecorator() {
        delete baseCoffee;
    }
};

// 具体装饰器类 - 加奶
class MilkDecorator : public CoffeeDecorator {
public:
    MilkDecorator(Coffee* coffee) : CoffeeDecorator(coffee) {}

    double cost() override {
        return baseCoffee->cost() + 1.5;  // 加奶的附加价格
    }
};

// 具体装饰器类 - 加糖
class SugarDecorator : public CoffeeDecorator {
public:
    SugarDecorator(Coffee* coffee) : CoffeeDecorator(coffee) {}

    double cost() override {
        return baseCoffee->cost() + 0.5;  // 加糖的附加价格
    }
};

int main() {
    // 创建基础咖啡
    Coffee* coffee = new BasicCoffee();
    std::cout << "Basic Coffee Cost: " << coffee->cost() << std::endl;  // 输出 5.0

    // 加奶装饰
    Coffee* milkCoffee = new MilkDecorator(coffee);
    std::cout << "Coffee with Milk Cost: " << milkCoffee->cost() << std::endl;  // 输出 6.5

    // 加奶加糖装饰
    Coffee* milkSugarCoffee = new SugarDecorator(milkCoffee);
    std::cout << "Coffee with Milk and Sugar Cost: " << milkSugarCoffee->cost() << std::endl;  // 输出 7.0

    // 清理内存
    delete milkSugarCoffee;

    return 0;
}
```

**细节分析**：

- 通过装饰器模式，你可以为一个基本对象（如`BasicCoffee`）动态地添加功能（如“加奶”、“加糖”），而不需要修改基础类。
- 装饰器模式允许功能组合的高度灵活性，适合用于需要扩展多个功能的场景。

------

### 8. 观察者模式 (Observer Pattern)

**类型：行为型模式**

#### **目的**：

定义对象间的一对多依赖关系，当一个对象状态发生改变时，所有依赖于它的对象都会自动收到通知并更新。

#### **类比**：

想象你是一个新闻发布平台，你有很多订阅者。一旦发布了新的新闻，你会通知所有订阅者，让他们都收到最新的消息。这种一对多的关系就是观察者模式的核心思想。

#### **详细实现**：

我们以一个简单的新闻发布系统为例，`NewsPublisher`是发布者，`Subscriber`是订阅者。

```
cpp复制代码#include <iostream>
#include <vector>
#include <string>

// 抽象观察者接口
class Subscriber {
public:
    virtual void update(const std::string& news) = 0;
    virtual ~Subscriber() {}
};

// 具体观察者类
class ConcreteSubscriber : public Subscriber {
private:
    std::string name;

public:
    ConcreteSubscriber(const std::string& subscriberName) : name(subscriberName) {}

    void update(const std::string& news) override {
        std::cout << name << " received news: " << news << std::endl;
    }
};

// 抽象发布者接口
class NewsPublisher {
public:
    virtual void addSubscriber(Subscriber* subscriber) = 0;
    virtual void removeSubscriber(Subscriber* subscriber) = 0;
    virtual void notifySubscribers() = 0;
    virtual ~NewsPublisher() {}
};

// 具体发布者类
class ConcreteNewsPublisher : public NewsPublisher {
private:
    std::vector<Subscriber*> subscribers;
    std::string latestNews;

public:
    void addSubscriber(Subscriber* subscriber) override {
        subscribers.push_back(subscriber);
    }

    void removeSubscriber(Subscriber* subscriber) override {
        subscribers.erase(std::remove(subscribers.begin(), subscribers.end(), subscriber), subscribers.end());
    }

    void publishNews(const std::string& news) {
        latestNews = news;
        notifySubscribers();
    }

    void notifySubscribers() override {
        for (Subscriber* subscriber : subscribers) {
            subscriber->update(latestNews);
        }
    }
};

int main() {
    // 创建发布者
    ConcreteNewsPublisher* publisher = new ConcreteNewsPublisher();

    // 创建订阅者
    Subscriber* subscriber1 = new ConcreteSubscriber("Alice");
    Subscriber* subscriber2 = new ConcreteSubscriber("Bob");

    // 添加订阅者
    publisher->addSubscriber(subscriber1);
    publisher->addSubscriber(subscriber2);

    // 发布新闻
    publisher->publishNews("Breaking News: Design Patterns are awesome!");

    // 移除订阅者
    publisher->removeSubscriber(subscriber1);

    // 发布另一条新闻
    publisher->publishNews("Latest News: C++ is a powerful language!");

    // 清理内存
    delete subscriber1;
    delete subscriber2;
    delete publisher;

    return 0;
}
```

**细节分析**：

- 观察者模式定义了`NewsPublisher`（发布者）和`Subscriber`（订阅者）之间的关系。发布者维护一组订阅者的列表，当发布者状态改变时，它会通知所有的订阅者。
- 这种模式适用于类似订阅机制、事件监听等场景。

### 9. 外观模式 (Facade Pattern)

**类型：结构型模式**

#### **目的**：

外观模式为复杂系统提供一个简化的接口。它隐藏了系统的内部复杂性，使客户端通过一个简单的接口与系统交互，而无需关心内部的细节。

#### **类比**：

想象你去一家高档餐厅吃饭。你不需要知道餐厅后厨的复杂流程，只需和服务员下单，服务员会与厨房和其他工作人员协调，最终你只需等待上菜即可。服务员就是这个“外观”，它简化了你和餐厅内部复杂系统的交互。

#### **详细实现**：

我们用一个家庭影院系统为例，它包括多个复杂的子系统（如DVD播放器、音响系统、投影仪等）。外观模式可以通过一个简单的接口来控制整个系统。

```
cpp复制代码#include <iostream>

// 子系统1 - DVD播放器
class DVDPlayer {
public:
    void on() { std::cout << "DVD Player is ON." << std::endl; }
    void play() { std::cout << "DVD is playing." << std::endl; }
    void off() { std::cout << "DVD Player is OFF." << std::endl; }
};

// 子系统2 - 音响系统
class SoundSystem {
public:
    void on() { std::cout << "Sound System is ON." << std::endl; }
    void setVolume(int level) { std::cout << "Volume set to " << level << std::endl; }
    void off() { std::cout << "Sound System is OFF." << std::endl; }
};

// 子系统3 - 投影仪
class Projector {
public:
    void on() { std::cout << "Projector is ON." << std::endl; }
    void setInput(const std::string& input) { std::cout << "Projector input set to " << input << std::endl; }
    void off() { std::cout << "Projector is OFF." << std::endl; }
};

// 外观类
class HomeTheaterFacade {
private:
    DVDPlayer* dvd;
    SoundSystem* sound;
    Projector* projector;

public:
    HomeTheaterFacade(DVDPlayer* dvd, SoundSystem* sound, Projector* projector)
        : dvd(dvd), sound(sound), projector(projector) {}

    void watchMovie() {
        std::cout << "Setting up home theater to watch a movie..." << std::endl;
        dvd->on();
        sound->on();
        sound->setVolume(10);
        projector->on();
        projector->setInput("DVD Player");
        dvd->play();
    }

    void endMovie() {
        std::cout << "Shutting down home theater..." << std::endl;
        dvd->off();
        sound->off();
        projector->off();
    }
};

int main() {
    // 创建子系统对象
    DVDPlayer* dvd = new DVDPlayer();
    SoundSystem* sound = new SoundSystem();
    Projector* projector = new Projector();

    // 创建外观对象
    HomeTheaterFacade* homeTheater = new HomeTheaterFacade(dvd, sound, projector);

    // 启动家庭影院
    homeTheater->watchMovie();

    // 结束电影
    homeTheater->endMovie();

    // 清理内存
    delete dvd;
    delete sound;
    delete projector;
    delete homeTheater;

    return 0;
}
```

**细节分析**：

- `HomeTheaterFacade`提供了一个简单的接口，来控制`DVDPlayer`、`SoundSystem`和`Projector`这些复杂的子系统。客户端只需调用外观类的`watchMovie()`和`endMovie()`方法，而不需要直接与各个子系统打交道。

------

### 10. 组合模式 (Composite Pattern)

**类型：结构型模式**

#### **目的**：

组合模式将对象组织成树形结构，使得客户端可以一致地对待单个对象和对象组合。这个模式用于表示部分-整体的层次结构。

#### **类比**：

想象你在操作一个文件系统，有文件夹和文件。文件夹可以包含文件或其他文件夹。当你对文件夹进行某些操作时（例如删除），这实际上对文件夹中的所有文件和子文件夹也执行了同样的操作。组合模式就是通过这种层次结构，让客户端可以一致地对待文件和文件夹。

#### **详细实现**：

我们用文件系统作为例子，文件夹和文件可以组合在一起，组成一个树形结构。

```
cpp复制代码#include <iostream>
#include <vector>
#include <string>

// 组件接口
class FileSystemComponent {
public:
    virtual void show() = 0;  // 展示组件信息
    virtual ~FileSystemComponent() {}
};

// 叶子节点 - 文件
class File : public FileSystemComponent {
private:
    std::string name;

public:
    File(const std::string& name) : name(name) {}

    void show() override {
        std::cout << "File: " << name << std::endl;
    }
};

// 组合节点 - 文件夹
class Folder : public FileSystemComponent {
private:
    std::string name;
    std::vector<FileSystemComponent*> components;

public:
    Folder(const std::string& name) : name(name) {}

    void addComponent(FileSystemComponent* component) {
        components.push_back(component);
    }

    void show() override {
        std::cout << "Folder: " << name << std::endl;
        for (FileSystemComponent* component : components) {
            component->show();
        }
    }

    ~Folder() {
        for (FileSystemComponent* component : components) {
            delete component;
        }
    }
};

int main() {
    // 创建文件和文件夹
    FileSystemComponent* file1 = new File("file1.txt");
    FileSystemComponent* file2 = new File("file2.txt");
    FileSystemComponent* file3 = new File("file3.txt");

    Folder* folder1 = new Folder("folder1");
    folder1->addComponent(file1);
    folder1->addComponent(file2);

    Folder* folder2 = new Folder("folder2");
    folder2->addComponent(folder1);  // 文件夹里还可以嵌套文件夹
    folder2->addComponent(file3);

    // 显示文件系统结构
    folder2->show();

    // 清理内存
    delete folder2;

    return 0;
}
```

**细节分析**：

- `File`和`Folder`类都实现了`FileSystemComponent`接口，这样客户端可以一致地处理它们。
- `Folder`是一个组合对象，它包含多个子组件（可以是文件或其他文件夹）。客户端不需要区分处理文件还是文件夹，都可以通过相同的接口调用。

------

### 11. 策略模式 (Strategy Pattern)

**类型：行为型模式**

#### **目的**：

策略模式定义了一组可互换的算法，并让这些算法独立于使用它的客户端而变化。通过策略模式，客户端可以选择不同的算法来完成某个任务。

#### **类比**：

想象你在打车应用中选择出行方式。你可以选择“快车”或“拼车”，不同的选择意味着不同的算法来计算行程费用。策略模式就是这种可以动态替换不同计算策略的模式。

#### **详细实现**：

我们用一个简单的支付系统为例，客户可以选择不同的支付方式（如信用卡支付、现金支付），不同的支付方式有不同的处理逻辑。

```
cpp复制代码#include <iostream>
#include <string>

// 策略接口
class PaymentStrategy {
public:
    virtual void pay(int amount) = 0;
    virtual ~PaymentStrategy() {}
};

// 具体策略类 - 信用卡支付
class CreditCardPayment : public PaymentStrategy {
public:
    void pay(int amount) override {
        std::cout << "Paid " << amount << " using Credit Card." << std::endl;
    }
};

// 具体策略类 - 现金支付
class CashPayment : public PaymentStrategy {
public:
    void pay(int amount) override {
        std::cout << "Paid " << amount << " using Cash." << std::endl;
    }
};

// 上下文类
class ShoppingCart {
private:
    PaymentStrategy* paymentMethod;

public:
    ShoppingCart(PaymentStrategy* paymentMethod) : paymentMethod(paymentMethod) {}

    void checkout(int amount) {
        paymentMethod->pay(amount);  // 使用策略对象进行支付
    }

    ~ShoppingCart() {
        delete paymentMethod;
    }
};

int main() {
    // 客户选择用信用卡支付
    PaymentStrategy* creditCard = new CreditCardPayment();
    ShoppingCart* cart1 = new ShoppingCart(creditCard);
    cart1->checkout(100);  // 输出 "Paid 100 using Credit Card."

    // 客户选择用现金支付
    PaymentStrategy* cash = new CashPayment();
    ShoppingCart* cart2 = new ShoppingCart(cash);
    cart2->checkout(50);  // 输出 "Paid 50 using Cash."

    // 清理内存
    delete cart1;
    delete cart2;

    return 0;
}
```

**细节分析**：

- `PaymentStrategy`接口定义了支付方法，具体的策略类（如`CreditCardPayment`和`CashPayment`）实现了不同的支付逻辑。
- `ShoppingCart`通过组合策略对象来实现不同的支付方式，而不需要修改自身的逻辑。

------

### 12. 责任链模式 (Chain of Responsibility Pattern)

**类型：行为型模式**

#### **目的**：

责任链模式让多个对象都有机会处理请求，从而避免了请求的发送者和接收者之间的耦合。请求沿着责任链传递，直到有对象处理它为止。

#### **类比**：

假设你在一个公司的客服系统提交了一个问题请求，这个请求首先会被初级客服处理，如果初级客服无法解决，它会把请求传给更高级的客服，直到问题被解决为止。这就是责任链模式的工作方式。

#### **详细实现**：

我们用一个简单的客户支持系统为例，不同级别的客服人员可以处理不同难度的问题。

```
cpp复制代码#include <iostream>
#include <string>

// 处理器基类
class SupportHandler {
protected:
    SupportHandler* nextHandler;

public:
    SupportHandler() : nextHandler(nullptr) {}

    void setNextHandler(SupportHandler* handler) {
        nextHandler = handler;
    }

    virtual void handleRequest(const std::string& request) = 0;
    virtual ~SupportHandler() {}
};

// 具体处理者类 - 初级客服
class BasicSupport : public SupportHandler {
public:
    void handleRequest(const std::string& request) override {
        if (request == "basic") {
            std::cout << "Basic Support handled the request." << std::endl;
        } else if (nextHandler != nullptr) {
            nextHandler->handleRequest(request);  // 传递请求到下一个处理者
        }
    }
};

// 具体处理者类 - 高级客服
class AdvancedSupport : public SupportHandler {
public:
    void handleRequest(const std::string& request) override {
        if (request == "advanced") {
            std::cout << "Advanced Support handled the request." << std::endl;
        } else if (nextHandler != nullptr) {
            nextHandler->handleRequest(request);  // 传递请求到下一个处理者
        }
    }
};

// 具体处理者类 - 主管
class SupervisorSupport : public SupportHandler {
public:
    void handleRequest(const std::string& request) override {
        if (request == "supervisor") {
            std::cout << "Supervisor Support handled the request." << std::endl;
        } else {
            std::cout << "Request could not be handled." << std::endl;
        }
    }
};

int main() {
    // 创建各级客服
    SupportHandler* basic = new BasicSupport();
    SupportHandler* advanced = new AdvancedSupport();
    SupportHandler* supervisor = new SupervisorSupport();

    // 设置责任链
    basic->setNextHandler(advanced);
    advanced->setNextHandler(supervisor);

    // 提交不同的请求
    basic->handleRequest("basic");  // 输出 "Basic Support handled the request."
    basic->handleRequest("advanced");  // 输出 "Advanced Support handled the request."
    basic->handleRequest("supervisor");  // 输出 "Supervisor Support handled the request."
    basic->handleRequest("unknown");  // 输出 "Request could not be handled."

    // 清理内存
    delete basic;
    delete advanced;
    delete supervisor;

    return 0;
}
```

------

### 

------

### **13. 享元模式 (Flyweight Pattern)**

**类型：结构型模式**

#### **目的**：

通过共享大量细粒度对象来减少内存占用。当应用中存在大量相似对象时，享元模式可以有效减少内存消耗。

#### **类比**：

想象你有一个很大的地图应用，其中显示了成千上万棵树。每棵树的种类可能有很多，但是每棵树的外观数据可以共享（比如颜色、树种）。享元模式让你可以复用这些数据，而不需要为每棵树都创建完全独立的对象。

#### **详细实现**：

享元模式通过共享相似对象，节省内存开销。假设我们有一个“树”对象，其中相同类型的树共享相同的树形数据。

```
cpp复制代码#include <iostream>
#include <unordered_map>

// 享元类，表示树的共享部分
class TreeType {
private:
    std::string name;
    std::string color;
    std::string texture;

public:
    TreeType(const std::string& name, const std::string& color, const std::string& texture)
        : name(name), color(color), texture(texture) {}

    void draw(int x, int y) {
        std::cout << "Drawing tree of type " << name << " at (" << x << ", " << y << ") with color " << color << " and texture " << texture << std::endl;
    }
};

// 享元工厂，确保共享对象的复用
class TreeFactory {
private:
    std::unordered_map<std::string, TreeType*> treeTypes;

public:
    TreeType* getTreeType(const std::string& name, const std::string& color, const std::string& texture) {
        std::string key = name + color + texture;
        if (treeTypes.find(key) == treeTypes.end()) {
            treeTypes[key] = new TreeType(name, color, texture);
        }
        return treeTypes[key];
    }

    ~TreeFactory() {
        for (auto pair : treeTypes) {
            delete pair.second;
        }
    }
};

class Tree {
private:
    int x, y;
    TreeType* type;

public:
    Tree(int x, int y, TreeType* type) : x(x), y(y), type(type) {}

    void draw() {
        type->draw(x, y);
    }
};

int main() {
    TreeFactory* factory = new TreeFactory();

    Tree* tree1 = new Tree(10, 20, factory->getTreeType("Oak", "Green", "Smooth"));
    Tree* tree2 = new Tree(30, 40, factory->getTreeType("Pine", "Dark Green", "Rough"));
    Tree* tree3 = new Tree(50, 60, factory->getTreeType("Oak", "Green", "Smooth"));  // 复用第一个树种

    tree1->draw();  // 输出类似于 "Drawing tree of type Oak at (10, 20)"
    tree2->draw();  // 输出类似于 "Drawing tree of type Pine at (30, 40)"
    tree3->draw();  // 输出类似于 "Drawing tree of type Oak at (50, 60)"

    delete tree1;
    delete tree2;
    delete tree3;
    delete factory;

    return 0;
}
```

**细节分析**：

- `TreeType`类表示树的共享部分，而`Tree`类则包含树的具体位置数据。
- `TreeFactory`通过复用`TreeType`对象减少了重复创建同类型对象的开销。

------

### **14. 代理模式 (Proxy Pattern)**

**类型：结构型模式**

#### **目的**：

代理模式为其他对象提供一个代理，以控制对该对象的访问。它可以用于控制、优化或监控某些资源的访问。

#### **类比**：

想象你需要通过一个VPN访问某个网站。VPN服务器就相当于代理，它帮助你间接地访问目标，而不直接与目标服务器交互。代理模式可以用来控制访问某些资源，或者在访问某些昂贵资源时进行延迟初始化等。

#### **详细实现**：

我们用一个图像加载的例子，假设加载一张图片的成本较高，因此我们使用代理类来延迟加载图片。

```
cpp复制代码#include <iostream>

// 抽象图片接口
class Image {
public:
    virtual void display() = 0;
    virtual ~Image() {}
};

// 真实图片类
class RealImage : public Image {
private:
    std::string fileName;

public:
    RealImage(const std::string& fileName) : fileName(fileName) {
        loadFromDisk();
    }

    void loadFromDisk() {
        std::cout << "Loading " << fileName << " from disk." << std::endl;
    }

    void display() override {
        std::cout << "Displaying " << fileName << std::endl;
    }
};

// 图片代理类
class ProxyImage : public Image {
private:
    RealImage* realImage;
    std::string fileName;

public:
    ProxyImage(const std::string& fileName) : realImage(nullptr), fileName(fileName) {}

    void display() override {
        if (realImage == nullptr) {
            realImage = new RealImage(fileName);  // 延迟加载
        }
        realImage->display();
    }

    ~ProxyImage() {
        delete realImage;
    }
};

int main() {
    Image* image = new ProxyImage("test_image.jpg");

    // 图片不会在此时加载，直到真正显示时才加载
    image->display();  // 输出 "Loading test_image.jpg from disk."
                       // 接着 "Displaying test_image.jpg"
    
    // 再次显示时不会重新加载图片
    image->display();  // 输出 "Displaying test_image.jpg"

    delete image;

    return 0;
}
```

**细节分析**：

- `ProxyImage`类控制了对`RealImage`的访问，并实现了延迟加载。当图片实际需要显示时才加载，而不是在对象创建时就加载。

------

### **15. 桥接模式 (Bridge Pattern)**

**类型：结构型模式**

#### **目的**：

桥接模式将抽象与实现分离，使它们可以独立变化。它通过分离对象的抽象部分和实现部分，解决了类的多维度扩展问题。

#### **类比**：

想象你有一个形状类（如圆形、矩形），你想要给这些形状添加不同的颜色。桥接模式允许你将“形状”和“颜色”独立出来，使得形状和颜色可以自由组合，而不需要为每种形状和颜色的组合创建单独的类。

#### **详细实现**：

我们用“形状”和“颜色”来展示桥接模式的实现。

```
cpp复制代码#include <iostream>

// 实现部分 - 颜色接口
class Color {
public:
    virtual void applyColor() = 0;
    virtual ~Color() {}
};

// 具体实现 - 红色
class Red : public Color {
public:
    void applyColor() override {
        std::cout << "Applying red color." << std::endl;
    }
};

// 具体实现 - 绿色
class Green : public Color {
public:
    void applyColor() override {
        std::cout << "Applying green color." << std::endl;
    }
};

// 抽象部分 - 形状
class Shape {
protected:
    Color* color;

public:
    Shape(Color* color) : color(color) {}

    virtual void draw() = 0;
    virtual ~Shape() {
        delete color;
    }
};

// 扩展抽象 - 圆形
class Circle : public Shape {
public:
    Circle(Color* color) : Shape(color) {}

    void draw() override {
        std::cout << "Drawing a Circle. ";
        color->applyColor();  // 使用颜色实现
    }
};

// 扩展抽象 - 方形
class Square : public Shape {
public:
    Square(Color* color) : Shape(color) {}

    void draw() override {
        std::cout << "Drawing a Square. ";
        color->applyColor();  // 使用颜色实现
    }
};

int main() {
    // 创建红色的圆形
    Shape* redCircle = new Circle(new Red());
    redCircle->draw();  // 输出 "Drawing a Circle. Applying red color."

    // 创建绿色的方形
    Shape* greenSquare = new Square(new Green());
    greenSquare->draw();  // 输出 "Drawing a Square. Applying green color."

    delete redCircle;
    delete greenSquare;

    return 0;
}
```

**细节分析**：

- 桥接模式允许你将“形状”和“颜色”这两个维度独立出来，使得你可以灵活组合不同的形状和颜色，而无需创建大量类。

------

### **16. 命令模式 (Command Pattern)**

**类型：行为型模式**

#### **目的**：

命令模式将请求封装为对象，使得客户端可以用不同的请求来参数化对象。它可以用于“撤销”、“重做”功能，或者记录日志等场景。

#### **类比**：

想象你在操作一个遥控器，每个按钮都代表一个命令，比如“开电视”、“关电视”、“调高音量”等。命令模式让你可以将这些操作封装成对象，并传递给遥控器来执行，甚至可以实现“撤销”或“重做”这些命令。

#### **详细实现**：

我们实现一个简单的遥控器，执行不同的命令，如“打开电视”和“关闭电视”。

```
cpp复制代码#include <iostream>

// 命令接口
class Command {
public:
    virtual void execute() = 0;
    virtual ~Command() {}
};

// 接收者类 - 电视
class Television {
public:
    void on() {
        std::cout << "Television is ON." << std::endl;
    }

    void off() {
        std::cout << "Television is OFF." << std::endl;
    }
};

// 具体命令类 - 打开电视
class TVOnCommand : public Command {
private:
    Television* tv;

public:
    TVOnCommand(Television* tv) : tv(tv) {}

    void execute() override {
        tv->on();
    }
};

// 具体命令类 - 关闭电视
class TVOffCommand : public Command {
private:
    Television* tv;

public:
    TVOffCommand(Television* tv) : tv(tv) {}

    void execute() override {
        tv->off();
    }
};

// 调用者类 - 遥控器
class RemoteControl {
private:
    Command* command;

public:
    void setCommand(Command* command) {
        this->command = command;
    }

    void pressButton() {
        command->execute();
    }
};

int main() {
    // 创建接收者
    Television* tv = new Television();

    // 创建具体命令
    Command* tvOn = new TVOnCommand(tv);
    Command* tvOff = new TVOffCommand(tv);

    // 创建遥控器
    RemoteControl* remote = new RemoteControl();

    // 设置打开电视命令并执行
    remote->setCommand(tvOn);
    remote->pressButton();  // 输出 "Television is ON."

    // 设置关闭电视命令并执行
    remote->setCommand(tvOff);
    remote->pressButton();  // 输出 "Television is OFF."

    delete tv;
    delete tvOn;
    delete tvOff;
    delete remote;

    return 0;
}
```

**细节分析**：

- `Command`对象将每个操作封装成独立的命令。`RemoteControl`可以通过设置不同的命令来执行操作，命令模式使得这些操作与执行者解耦。

------

### **17. 解释器模式 (Interpreter Pattern)**

**类型：行为型模式**

#### **目的**：

解释器模式用于定义一种语言的语法表示，并提供一个解释器来解释该语言中的句子。通常用于构建领域特定语言（DSL）或简单的编译器。

#### **类比**：

解释器模式类似于我们使用的计算器，用户输入一系列表达式，解释器按照特定的语法规则对输入进行解析，并返回结果。

#### **详细实现**：

我们实现一个简单的解释器，来处理加法和减法表达式。

```
cpp复制代码#include <iostream>
#include <string>
#include <sstream>
#include <vector>

// 抽象表达式接口
class Expression {
public:
    virtual int interpret() = 0;
    virtual ~Expression() {}
};

// 数字表达式
class NumberExpression : public Expression {
private:
    int number;

public:
    NumberExpression(int number) : number(number) {}

    int interpret() override {
        return number;
    }
};

// 加法表达式
class PlusExpression : public Expression {
private:
    Expression* left;
    Expression* right;

public:
    PlusExpression(Expression* left, Expression* right)
        : left(left), right(right) {}

    int interpret() override {
        return left->interpret() + right->interpret();
    }

    ~PlusExpression() {
        delete left;
        delete right;
    }
};

// 减法表达式
class MinusExpression : public Expression {
private:
    Expression* left;
    Expression* right;

public:
    MinusExpression(Expression* left, Expression* right)
        : left(left), right(right) {}

    int interpret() override {
        return left->interpret() - right->interpret();
    }

    ~MinusExpression() {
        delete left;
        delete right;
    }
};

int main() {
    // 解释表达式 "5 + 10 - 3"
    Expression* expression = new MinusExpression(
        new PlusExpression(new NumberExpression(5), new NumberExpression(10)),
        new NumberExpression(3)
    );

    std::cout << "Result: " << expression->interpret() << std::endl;  // 输出 "Result: 12"

    delete expression;

    return 0;
}
```

**细节分析**：

- 解释器模式将表达式的每个操作（如加法和减法）封装为类。`interpret()`方法用于递归解释表达式，并返回最终结果。尽管在实际应用中不常用，但它在构建DSL或编译器时非常有用。

------

### **18. 中介者模式 (Mediator Pattern)**

**类型：行为型模式**

#### **目的**：

中介者模式通过引入一个中介对象，来处理对象之间的复杂交互，减少对象之间的耦合。它将对象之间的通信逻辑集中到一个中介者对象中，而不是让对象彼此直接引用。

#### **类比**：

想象你在机场，机场调度员负责协调每架飞机的起飞和降落。如果没有调度员，每架飞机都需要自己与其他飞机进行通信，这将非常混乱。中介者模式就是调度员，它协调系统中各个对象之间的通信。

#### **详细实现**：

我们用一个聊天室的例子展示中介者模式，不同用户通过中介者（聊天室）发送消息。

```
cpp复制代码#include <iostream>
#include <string>
#include <vector>

// 前向声明
class User;

// 中介者接口
class ChatRoomMediator {
public:
    virtual void showMessage(User* user, const std::string& message) = 0;
    virtual ~ChatRoomMediator() {}
};

// 用户类
class User {
protected:
    std::string name;
    ChatRoomMediator* chatRoom;

public:
    User(const std::string& name, ChatRoomMediator* chatRoom)
        : name(name), chatRoom(chatRoom) {}

    virtual void sendMessage(const std::string& message) {
        chatRoom->showMessage(this, message);
    }

    std::string getName() const {
        return name;
    }
};

// 具体中介者类 - 聊天室
class ChatRoom : public ChatRoomMediator {
public:
    void showMessage(User* user, const std::string& message) override {
        std::cout << user->getName() << ": " << message << std::endl;
    }
};

int main() {
    // 创建聊天室
    ChatRoom* chatRoom = new ChatRoom();

    // 创建用户
    User* alice = new User("Alice", chatRoom);
    User* bob = new User("Bob", chatRoom);

    // 用户通过中介者发送消息
    alice->sendMessage("Hello, Bob!");
    bob->sendMessage("Hi, Alice!");

    // 清理内存
    delete alice;
    delete bob;
    delete chatRoom;

    return 0;
}
```

**细节分析**：

- 中介者类`ChatRoom`负责处理用户之间的消息传递。用户不需要直接互相通信，而是通过中介者进行消息交换，这大大降低了系统的耦合度。

------

### **19. 备忘录模式 (Memento Pattern)**

**类型：行为型模式**

#### **目的**：

备忘录模式用于在不破坏对象封装的前提下，保存并恢复对象的内部状态。它通常用于实现撤销/恢复功能。

#### **类比**：

备忘录模式类似于你在文档中使用的“撤销”功能，系统在后台保存了每次修改前的状态，你可以随时恢复到之前的某个状态。

#### **详细实现**：

我们用一个文本编辑器的例子，演示如何使用备忘录模式来保存和恢复文本状态。

```
cpp复制代码#include <iostream>
#include <string>

// 备忘录类，保存编辑器的状态
class EditorMemento {
private:
    std::string text;

public:
    EditorMemento(const std::string& text) : text(text) {}

    std::string getText() const {
        return text;
    }
};

// 编辑器类
class TextEditor {
private:
    std::string text;

public:
    void type(const std::string& words) {
        text += words;
    }

    std::string getText() const {
        return text;
    }

    EditorMemento* save() {
        return new EditorMemento(text);  // 创建当前状态的备忘录
    }

    void restore(EditorMemento* memento) {
        text = memento->getText();  // 恢复到备忘录保存的状态
    }
};

int main() {
    TextEditor editor;

    editor.type("Hello, ");
    editor.type("World!");

    // 保存当前状态
    EditorMemento* savedState = editor.save();

    editor.type(" Adding more text.");
    std::cout << "Current Text: " << editor.getText() << std::endl;  // 输出 "Hello, World! Adding more text."

    // 恢复到之前保存的状态
    editor.restore(savedState);
    std::cout << "Restored Text: " << editor.getText() << std::endl;  // 输出 "Hello, World!"

    delete savedState;

    return 0;
}
```

**细节分析**：

- 备忘录模式通过`EditorMemento`类保存对象的状态，并允许对象在需要时恢复到先前的状态。这非常适合撤销、恢复等功能。

------

### **20. 状态模式 (State Pattern)**

**类型：行为型模式**

#### **目的**：

状态模式允许对象在其内部状态发生改变时，改变其行为。它使得状态相关的行为局部化，并将不同状态的行为封装到独立的状态类中。

#### **类比**：

状态模式就像一个交通信号灯。交通信号灯的行为（红灯、绿灯、黄灯）是基于当前状态决定的。当状态改变时，信号灯的行为也会相应改变。

#### **详细实现**：

我们用一个简单的电梯状态机来演示状态模式。电梯在不同状态下（如移动、停止）表现不同。

```
cpp复制代码#include <iostream>

// 状态接口
class ElevatorState {
public:
    virtual void moveUp() = 0;
    virtual void stop() = 0;
    virtual ~ElevatorState() {}
};

// 电梯类，持有状态
class Elevator {
private:
    ElevatorState* currentState;

public:
    Elevator(ElevatorState* state) : currentState(state) {}

    void setState(ElevatorState* state) {
        currentState = state;
    }

    void moveUp() {
        currentState->moveUp();
    }

    void stop() {
        currentState->stop();
    }
};

// 具体状态类 - 移动状态
class MovingState : public ElevatorState {
public:
    void moveUp() override {
        std::cout << "Elevator is moving up..." << std::endl;
    }

    void stop() override {
        std::cout << "Elevator cannot stop while moving." << std::endl;
    }
};

// 具体状态类 - 停止状态
class StoppedState : public ElevatorState {
public:
    void moveUp() override {
        std::cout << "Elevator starts moving up." << std::endl;
    }

    void stop() override {
        std::cout << "Elevator is already stopped." << std::endl;
    }
};

int main() {
    // 创建电梯
    ElevatorState* stopped = new StoppedState();
    Elevator* elevator = new Elevator(stopped);

    elevator->moveUp();  // 输出 "Elevator starts moving up."
    elevator->stop();    // 输出 "Elevator is already stopped."

    // 电梯切换到移动状态
    ElevatorState* moving = new MovingState();
    elevator->setState(moving);

    elevator->moveUp();  // 输出 "Elevator is moving up..."
    elevator->stop();    // 输出 "Elevator cannot stop while moving."

    delete stopped;
    delete moving;
    delete elevator;

    return 0;
}
```

**细节分析**：

- `Elevator`对象根据其当前状态，改变其行为。当状态改变时，对象的行为也会随之变化，状态模式让状态逻辑更加清晰、独立。

------

### **21. 访问者模式 (Visitor Pattern)**

**类型：行为型模式**

#### **目的**：

访问者模式允许在不改变对象结构的情况下，定义作用于这些对象的新操作。它通过将操作封装在访问者对象中，使得操作和对象结构解耦。

#### **类比**：

想象一个博物馆里有很多展品，你可以带着不同的导游参观。同样的展品，不同的导游可以给你讲解不同的信息。访问者模式就是这种为同一对象结构定义不同操作的方式。

#### **详细实现**：

我们用一个简单的例子展示访问者模式，假设我们有不同类型的图形对象，每个访问者可以对这些图形对象执行不同的操作。

```
cpp复制代码#include <iostream>
#include <vector>

// 前向声明
class Circle;
class Square;

// 访问者接口
class Visitor {
public:
    virtual void visitCircle(Circle* circle) = 0;
    virtual void visitSquare(Square* square) = 0;
    virtual ~Visitor() {}
};

// 元素接口
class Shape {
public:
    virtual void accept(Visitor* visitor) = 0;
    virtual ~Shape() {}
};

// 具体元素 - 圆形
class Circle : public Shape {
public:
    void accept(Visitor* visitor) override {
        visitor->visitCircle(this);
    }
};

// 具体元素 - 方形
class Square : public Shape {
public:
    void accept(Visitor* visitor) override {
        visitor->visitSquare(this);
    }
};

// 具体访问者 - 计算面积
class AreaVisitor : public Visitor {
public:
    void visitCircle(Circle* circle) override {
        std::cout << "Calculating area of circle." << std::endl;
    }

    void visitSquare(Square* square) override {
        std::cout << "Calculating area of square." << std::endl;
    }
};

// 具体访问者 - 计算周长
class PerimeterVisitor : public Visitor {
public:
    void visitCircle(Circle* circle) override {
        std::cout << "Calculating perimeter of circle." << std::endl;
    }

    void visitSquare(Square* square) override {
        std::cout << "Calculating perimeter of square." << std::endl;
    }
};

int main() {
    // 创建图形对象
    std::vector<Shape*> shapes = {new Circle(), new Square()};

    // 创建访问者
    Visitor* areaVisitor = new AreaVisitor();
    Visitor* perimeterVisitor = new PerimeterVisitor();

    // 让访问者访问图形对象
    for (Shape* shape : shapes) {
        shape->accept(areaVisitor);      // 输出计算面积的操作
        shape->accept(perimeterVisitor); // 输出计算周长的操作
    }

    // 清理内存
    for (Shape* shape : shapes) {
        delete shape;
    }
    delete areaVisitor;
    delete perimeterVisitor;

    return 0;
}
```

**细节分析**：

- 访问者模式将操作封装在访问者类中，而不是图形对象类中。这样，即使增加新的操作（如面积、周长计算），也无需修改图形类的结构，符合“开放封闭原则”。

------

### **22. 模板方法模式 (Template Method Pattern)**

**类型：行为型模式**

#### **目的**：

模板方法模式定义了一个算法的骨架，并将某些步骤的实现延迟到子类。它使得子类可以在不改变算法结构的情况下，重新定义算法的某些步骤。

#### **类比**：

模板方法模式类似于你在编程时实现的函数骨架，不同细节可以通过传递参数或子类实现来变化。例如，一个餐馆的菜单固定了基本的做菜流程，但你可以选择具体的菜品材料（比如牛肉还是鸡肉），而不改变整体流程。

#### **详细实现**：

我们用一个例子展示模板方法模式，假设我们有一个抽象的“餐馆菜单”，不同的餐馆可以选择不同的配料，但做菜的整体流程是固定的。

```
cpp复制代码#include <iostream>

// 抽象类 - 菜单
class Recipe {
public:
    // 模板方法，定义做菜的骨架
    void prepareDish() {
        boilWater();
        addIngredients();
        cook();
        serve();
    }

protected:
    void boilWater() {
        std::cout << "Boiling water..." << std::endl;
    }

    virtual void addIngredients() = 0;  // 留给子类实现
    virtual void cook() = 0;  // 留给子类实现

    void serve() {
        std::cout << "Serving the dish." << std::endl;
    }
};

// 具体实现类 - 牛肉菜谱
class BeefRecipe : public Recipe {
protected:
    void addIngredients() override {
        std::cout << "Adding beef to the pot." << std::endl;
    }

    void cook() override {
        std::cout << "Cooking beef." << std::endl;
    }
};

// 具体实现类 - 鸡肉菜谱
class ChickenRecipe : public Recipe {
protected:
    void addIngredients() override {
        std::cout << "Adding chicken to the pot." << std::endl;
    }

    void cook() override {
        std::cout << "Cooking chicken." << std::endl;
    }
};

int main() {
    // 准备牛肉菜
    Recipe* beefDish = new BeefRecipe();
    beefDish->prepareDish();  // 固定流程，牛肉菜的具体实现

    std::cout << std::endl;

    // 准备鸡肉菜
    Recipe* chickenDish = new ChickenRecipe();
    chickenDish->prepareDish();  // 固定流程，鸡肉菜的具体实现

    // 清理内存
    delete beefDish;
    delete chickenDish;

    return 0;
}
```

**细节分析**：

- `Recipe`类定义了做菜的整体流程（算法骨架），但具体的食材和烹饪方式由子类`BeefRecipe`和`ChickenRecipe`来实现。模板方法模式让算法的结构保持一致，但允许不同的实现细节。

------

### **23. 迭代器模式 (Iterator Pattern)**

**类型：行为型模式**

#### **目的**：

迭代器模式提供了一种方法，顺序访问集合中的各个元素，而不暴露集合的内部表示。它使得你可以统一遍历不同类型的集合对象。

#### **类比**：

迭代器模式类似于你在使用游标（cursor）遍历数据库表或使用标准库中的`for`循环遍历容器。它提供了一种遍历集合对象的通用接口，而不需要了解集合的内部结构。

#### **详细实现**：

我们用一个简单的例子展示迭代器模式，创建一个可以遍历整数集合的迭代器。

```
cpp复制代码#include <iostream>
#include <vector>

// 迭代器接口
template <typename T>
class Iterator {
public:
    virtual bool hasNext() = 0;
    virtual T next() = 0;
    virtual ~Iterator() {}
};

// 容器类，存储一组整数
class IntegerCollection {
private:
    std::vector<int> items;

public:
    void addItem(int item) {
        items.push_back(item);
    }

    std::vector<int>& getItems() {
        return items;
    }

    // 创建迭代器
    class IntegerIterator : public Iterator<int> {
    private:
        IntegerCollection* collection;
        size_t index;

    public:
        IntegerIterator(IntegerCollection* collection) : collection(collection), index(0) {}

        bool hasNext() override {
            return index < collection->getItems().size();
        }

        int next() override {
            return collection->getItems()[index++];
        }
    };

    IntegerIterator* createIterator() {
        return new IntegerIterator(this);
    }
};

int main() {
    // 创建整数集合
    IntegerCollection* collection = new IntegerCollection();
    collection->addItem(1);
    collection->addItem(2);
    collection->addItem(3);

    // 创建迭代器
    Iterator<int>* iterator = collection->createIterator();

    // 遍历集合
    while (iterator->hasNext()) {
        std::cout << iterator->next() << std::endl;  // 输出 1, 2, 3
    }

    delete iterator;
    delete collection;

    return 0;
}
```

**细节分析**：

- `Iterator`接口定义了遍历集合的标准方法。`IntegerIterator`类实现了这个接口，允许我们遍历`IntegerCollection`中的元素，而不需要暴露集合的内部实现细节。
