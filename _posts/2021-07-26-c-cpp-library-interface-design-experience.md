---
title: "C/C++库设计经验（持续总结）"
categories: 编码经验
tags: 编码经验
---

近几年写了一些C/C++库, 对于库接口的设计也有了一些自己的经验和心得，还有参考自github社区和google的一些C++项目的经验, 记录一下. 每条都比较独立, 暂时想不起来太多, 想到了会持续补充.

## 接口相关

函数出参使用指针, 即便是C++接口也使用指针而不是引用。这也是google的C++ code style的要求。

* 使用更灵活, 用户也许只是想使用返回值, 或者只是尝试调用, 而不是真的想拿到数据, 这时候只需要传入nullptr即可.
* 函数调用处可以很明显的看到使用取地址符“&", 明确表示这是一个会被修改的参数，增加代码可读性。

C接口不想暴露内部数据结构时一般会使用void \*指针作为数据交互接口, 但还可以使用未定义的结构体指针代替。

* 直接使用 `struct SomeObject*`即可，但当此指针作为函数参数时，编译器无法理解这个指针会报错，解决方法是使头文件中的第一个使用此指针的函数的返回值为`struct SomeObject*`，比如`struct SomeObject* CreateSomeObject();`。

给函数添加多个参数更好, 还是给库添加更多接口更好？（给C++接口添加默认参数不是好方法，除非迫不得已）

* 添加多个接口更好，你很可能以后还想添加更多的参数，那就会导致接口不兼容，而添加接口则不会。这也意味着没有被用户调用的配置项需要有一个默认值。

## 工程相关

使用现代cmake构建代码库，使用户仅需要一条`target_link_libraries(user_project PRIVATE your_lib_name)`cmake指令就可以使用。

* 利用现代cmake指令`target_link_libraries`，`target_include_directories`等描述工程。
* 利用`PRIVATE` `INTERFACE` `PUBLIC`这些属性把需要暴露和不需要暴露的接口描述好。

添加一个CHANGELOG.md文件，发布版本时及时更新变更历史。

* 来源：社区发起的倡议[Keep a Changelog](https://keepachangelog.com/en/1.0.0/)

语义化版本控制，通过查看版本号让用户明确知道新版本的兼容性。（我认为这也意味着，除非用户量很大很稳定了，不然不要轻易的发布第一个release版本。不然需要重构的时候就惨了）

* 来源：社区发起的倡议[Semantic Versioning](https://semver.org/spec/v2.0.0.html)
