---
title: Android Unit Test
---
摘要：本文介绍了Unit Test相关知识，包括：什么是Unit Test、它的作用、如何配置、如何生成、如何编码等。其中Unit Test的作用是个人理解，是我个人使用Unit Test的方法论。我希望能够对你有所帮助。
<!--more-->

## Unit Test相关知识
### 什么是Unit Test
作为开发人员，大多数情况下都是在写需求代码。我们尽可能的去完善我们的代码，处理各种场景，使程序在用户使用时不出现问题或者有个更好的交互体验。但是用户在使用时，往往会出现各种奇怪或者我们没有想到的问题。  
为了防止这种情况发生，我们经常通过各种测试去保证程序的健壮性：单元测试、集成测试等等。

单元测试是指对最小的可测试单元进行检查和验证。单元测试是在软件开发过程中要进行的最低级别的测试活动，软件的独立单元将在与程序的其他部分相隔离的情况下进行测试。

单元测试通常是开发者编写一小段代码，用于检验被测代码的一个很小的、很明确的功能是否正确。通常而言，一个单元测试是用于判断某个特定条件（或者场景）下某个特定函数的行为。

### Unit Test有什么用
学习单元测试的时候，在国外一个blog上看到一句很有意思的话：“是什么让代码成为企业级代码？是代码量么？是用户量么？是该项目产生的收益么？都不是，是单元测试！”。这句话将单元测试拔高到一个很高的地位，它也表述了单元测试重要的作用。以下是我个人理解的作用：  
* 对开发者来说，可以写出高质量代码，提高编码水平   
我常常好奇一些事情：框架代码是怎么写出来的？某些框架代码里面，明明是一件很简单的事情，为什么代码绕过来绕过去的？ 直到我学习了单元测试并对它有一定的了解后，我才在一定程度上理解这些问题。   
对于大多数的开发者来说，基本都多多少少知道一些设计模式，其中耳熟能详的就包括MVP和MVVM这种设计模式。在项目中，这两个设计模式也随处可见，但是它们使用的的合理么？拿Rxjava的使用来举例，我们常常在ViewModel或者Presenter中使用Rxjava来生成一个网络请求的被观察者，然后在Activity或者Fragment中去订阅它们，加入网络返回判断、视图操作判断和视图操作等代码。这种写法其实在一定程度上违背了MVP这种框架的分层原则，即逻辑代码和视图代码分离的原则，在上述例子中网络返回判断、解析、视图操作判断都应该放在逻辑层，而不是写在View层。虽然我们明白视图层、逻辑层的职责，我们也主动分出了这2层，但是代码一写起来就会将两层的职责模糊化了，并且耦合越来越紧。  
如果我们有写单元测试的习惯，那么就不会出现这种情况，因为将逻辑写在View层将很难被单元测试测到，那么在写代码或者接口设计的时候，下意识的就会想：这个逻辑单元测试应该怎么测？在这样潜意识的影响下，我们会主动将逻辑代码放在逻辑层的。  

* 为代码重构提供安全保障
如果某个代码实现的太糟糕了，或者效率有问题，我们需要将代码进行重构，怎么保证重构的正确性？将原来的单元测试用例全部再次运行一遍即可，如果单元测试用例设计的合理并且用例都通过了，那么我们可以认为这次重构是没有问题的。需要注意的是，我们的重构没有改动单元测试用例涉及的接口。如果改动了接口，那么需要对单元测试用例进行维护和更新。

* 对测试来说，如果减少了需求bug，即减少了测试同事反复验证bug的时间  
测试中有个测试金字塔的概念，示意图如下：
{% asset_img test_pyramid.jpg %}
图中三层分别表示：单元测试、集成测试、端到端测试。其中70%的测试工作量是由单元测试承担的，并且越靠近底层测试成本越低，测试速度越快。
  
* 对产品来说，单元测试可以减少黑盒测试无法构建的场景出错的概率，提高程序的健壮性  

### JUnit
JUnit是java的单元测试框架。它属于白盒测试，通过注解的方式来执行测试代码段。

## Android Unit Test
只在本地机器JVM上运行，以最小化执行时间，这种单元测试不依赖于Android框架，或者即使有依赖，也很方便使用模拟框架来模拟依赖，以达到隔离Android依赖的目的，模拟框架如google推荐的[Mockito](https://github.com/mockito/mockito)；

### 配置
module的gradle文件中添加配置：
```
// 设置test文件夹
sourceSets {
    main {
        ...
    }
    test {
        // 如果是AS自动生成的module那么就不需要指定文件夹；
        // 并且AS会自动生成src/test和src/androidTest
        res.srcDirs=['test']
        ...
    }
}

dependencies {
    ...
    testImplementation 'junit:junit:4.12'
}
```

### 使用
如下代码创建单元测试
{% asset_img sample_code.png %}

1. 在需要创建unit test类上右键->Go To->Test  
{% asset_img create1.png %}
2. 在弹出框中选择`Create New Test...`  
{% asset_img create2.png %}
3. 弹出`Create Test`弹框，该弹框允许设置单元测试的文件名称，一般会自动填充  类名+Test  选择`OK`按钮
{% asset_img create3.png %}
4. 点击`OK`按钮后，会弹出`Choose Destination Directory`弹框让用户选择测试类放在哪个文件夹下。一般 `InstrumentedTest`放在`xxx/androidTest/xxxx`中，单元测试放在`xxx/test/xxx/`中，如下图选择test文件夹，点击确定。
{% asset_img create4.png %}

5. 点击确定后，`test`目录下会生成需要创建的类
{% asset_img created_folder.png %}

6. 创建test方法，一般方法命名为test_xxx_xxx,并加上`@Test`注解，这样的一个方法称为一个用例。一般一个方法需要多个用例来检验不同条件下功能正确性。
{% asset_img test_code.png %}
7. 在Android Studio中可以点击行号旁边的三角形执行图标来允许单元测试。类名坐标的图标表示执行该类所有的单元测试用例，方法名左边的图标表示只执行对应的一个用例
{% asset_img run1.png %}
8. 点击类名旁边的执行按钮，Android Studio会首先去编译相关module的代码，然后在Run窗口输出结果，左边是用例执行结果，右边显示具体信息，如果用例没有通过，报错信息也会出现在右边。
{% asset_img run2.png %}

### 常用注解
`@Test`  
* 表示该方法是一个测试用例，可以执行  

`@BeforeClass`  
* 在测试类里所有用例运行之前运行一次这个方法，并且只会运行一次  
  
`@AfterClass`
* 与`@BeforeClass`对应，在测试类里的所有用例运行之后运行一次，并且只会运行一次  

`@Before`
* 在每个用例运行之前运行一次

`@After`
* 与`@Before`对应，在每个用例运行之后运行一次  

`@Ignore`
* 忽略某个用例，被忽略的用例将不会被执行

## 常用框架  
### robolectric
官网：[robolectric](http://robolectric.org/)  
它实现了一套JVM能运行的Android SDK，从而能够脱离Android环境进行测试，将原来运行一次测试的时间从几分钟缩短到几秒钟  
配置
{% asset_img robolectric_config.png %}
使用
{% asset_img rebolectric_use.png %}
  

### mockito
官网：[mockito](https://github.com/mockito/mockito)  
在写单元测试的过程中，我们往往会遇到要测试的类有很多依赖，这些依赖的类/对象/资源又有别的依赖，从而形成一个大的依赖树，要在单元测试的环境中完整地构建这样的依赖，是一件很困难的事情,并且我们也不关注这些依赖到底功能正不正确。    
Mockito框架用一个虚拟的对象（Mock对象）来创建这种不易构建的类以便测试。  
配置
{% asset_img mockito_config.png %}  
使用
{% asset_img mockito_use1.png %}
{% asset_img mockito_use2.png %}
  

### google truth
官网：[google truth](https://github.com/google/truth)   
一个用于测试的断言库。简单易用，更具可读性。  
配置
{% asset_img truth_config.png %}  
使用
{% asset_img truth_use.png %}

## Demo
[Android Test Demo](https://github.com/leimenghao1991/AndroidUnitTestDemo)


