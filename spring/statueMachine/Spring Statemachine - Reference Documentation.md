# Spring Statemachine - 参考文档

## Preface

状态机的概念很可能比这个参考文档的任何读者都老，而且比Java语言本身更老。 有限自动机的描述可以追溯到1943年，当时的沃伦·麦卡洛克（Warren McCulloch）和沃尔特·皮茨（Walter Pitts）写了一篇有关它的文章。 后来George H. Mealy在1955年提出了一个状态机的概念，这被称为Mealy机器。 一年后的1956年，爱德华·F·摩尔（Edward F. Moore）提出了另一篇被称为摩尔机器（Moore Machine）的论文。 如果你曾经读过任何关于状态机的信息，那么应该十分熟悉Mealy和Moore这两个名字。

这个参考文档包含以下这些部分。

[第一部分，“简介”](https://docs.spring.io/spring-statemachine/docs/2.0.0.M2/reference/htmlsingle/#introduction) 这部分是整个参考文档的简介

[第四部分，“使用Spring 状态机”](https://docs.spring.io/spring-statemachine/docs/2.0.0.M2/reference/htmlsingle/#statemachine) 描述了Spring状态机(ssm)的使用

[第六部分， “状态机示例”](https://docs.spring.io/spring-statemachine/docs/2.0.0.M2/reference/htmlsingle/#statemachine-examples) 更详细的状态机示例

[第七部分， “FAQ”](https://docs.spring.io/spring-statemachine/docs/2.0.0.M2/reference/htmlsingle/#statemachine-faq) 常见问题

[第八部分， “附录”](https://docs.spring.io/spring-statemachine/docs/2.0.0.M2/reference/htmlsingle/#appendices) 关于使用的材料和状态机的通用信息



## 第一部分 简介

Spring Statemachine（SSM）是应用程序开发人员在Spring应用程序中使用传统状态机概念的框架。 SSM旨在提供以下功能：

* 易于使用只有一级的状态机构建简单用例。
* 分层状态机结构(Hierarchical state machine)可以简化复杂的状态配置。
* 状态机领域(regions)提供更复杂的状态配置。
* 触发器(trigger)，转换(transitions)，警卫(guards)和动作(actions)的使用。
* 类型安全的可配置适配器。
* 状态机事件监听器。
* Spring IOC集成将bean与状态机关联起来。

在继续之前，值得通过[附录B.2节“术语表”](https://docs.spring.io/spring-statemachine/docs/2.0.0.M2/reference/htmlsingle/#glossary)和[B.3节“状态机崩溃教程”](https://docs.spring.io/spring-statemachine/docs/2.0.0.M2/reference/htmlsingle/#crashcourse)来了解状态机的一般概念，主要是因为其他文档期望 读者对状态机概念相当熟悉。

#### 1. 背景

状态机是非常强大的，因为它能保证行为的一致性，并且依赖操作规则在状态机开启时如何被写入相对容易debug。你的程序可能存在一个有限数量的状态，并且一些发生的动作可能让你的程序从一个状态会跳转到另一个状态。trigger会驱动一个状态机,它既可以派生自event也可以派生自timer。



