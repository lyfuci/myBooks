# Spring Web Service Reference Document

在当前的面向服务的体系结构时代，越来越多的人正在使用WS来连接以前未连接的系统。最初，Web服务被认为是进行远程过程调用（RPC）的另一种方式。 但是随着时间的推移，人们发现RPC和Web服务之间存在很大的差异。尤其是当与其他平台的互操作性很重要时，发送封装的XML文档通常会更好，其中包含处理请求所需的所有数据。 从概念上讲，基于XML的Web服务比消息队列而不是远程解决方案更好。 总的来说，XML应该被认为是数据的平台中立表示，这是SOA的**国际语言**。 在开发或使用Web服务时，重点应放在这个XML上，而不是Java上。

Spring Web Services着重于创建这些文档驱动的Web服务。 Spring Web Services促进了契约优先的SOAP服务开发，允许使用多种方式来操作XML有效载荷，从而创建灵活的Web服务。 Spring-WS提供了强大的消息契约[分派框架](https://www.gitbook.com/book/lyfuci/spring-web-services/edit#)，与现有应用程序安全解决方案集成的[WS-Security](https://www.gitbook.com/book/lyfuci/spring-web-services/edit#)解决方案，以及遵循熟悉的Spring模板模式的[客户端API](https://www.gitbook.com/book/lyfuci/spring-web-services/edit#)。

### 一、简介

参考文档的第一部分是Spring Web服务及其基本概念的[概述](https://docs.spring.io/spring-ws/docs/3.0.0.RELEASE/reference/#what-is-spring-ws)。 然后介绍了并解释了契约优先Web服务开发背后的概念。

#### 1. 什么是Spring web Service？

##### 1.1 简介

Spring Web服务（Spring-WS）是Spring社区致力于创建文档驱动的Web服务的产物。Spring Web Services旨在促进契约优先（contract-first）的SOAP服务开发，允许使用多种方法之一来创建灵活的Web服务来操作XML有效载荷。 该产品基于Spring本身，这意味着可以将Spring概念（如依赖注入）用作Web服务的组成部分。

人们使用Spring-WS的原因有很多，但是大多数人在找到替代的SOAP堆栈之后，大多数都是在遵循Web服务最佳实践的时候才使用它。 Spring-WS使最佳实践变得简单实践。 这包括WS-I的基本概况，Contract-First开发以及契约和实施之间的松散耦合等实践。 Spring Web服务的其他主要特性是：

###### 1.1.1 强大的映射能力

您可以将传入的XML请求分发给任何对象，具体取决于消息有效负载，SOAP Action标头或XPath表达式。

###### 1.1.2 XML API 支持

传入的XML消息不仅可以使用标准JAXP API（如DOM，SAX和StAX），还可以使用JDOM，dom4j，XOM甚至编组技术进行处理。

###### 1.1.3 灵活的XML编组（Marshalling）

Spring Web Services建立在Spring Framework的Object / XML Mapping模块上，它支持JAXB 1和2，Castor，XMLBeans，JiBX和XStream。

###### 1.1.4 Spring专业知识重用

Spring-WS使用Spring应用程序上下文进行所有配置，这有助于Spring开发人员快速正确的上手。并且，Spring-WS的体系结构类似于Spring-MVC的体系结构。

###### 1.1.5 支持 Supports WS-Security

WS-Security允许您发布（sign）SOAP消息，对其进行加密和解密，或者对其进行身份验证。

###### 1.1.6 和Spring Security进行集成

Spring Web服务的WS-Security实现提供了与Spring Security的集成。 这意味着你也可以使用你现有的Spring Security配置。

###### 1.1.7 Apache license

You can confidently use Spring-WS in your project.

##### 1.2 运行环境

Spring Web Services需要一个标准的Java 7或者Java 8运行时环境。 Spring-WS基于Spring Framework 4.0.9，也支持更高版本。

Spring-WS由多个模块组成，在本节的其余部分中将对其进行介绍。

* XML模块（spring-xml.jar）包含Spring Web Services的各种XML支持类。 这个模块主要侧重于Spring-WS框架本身，而不是Web服务开发。
* 核心模块（spring-ws-core.jar）是Spring Web Service功能的核心部分。 它提供了服务器端框架最中心的WebServiceMessage和SoapMessage接口，这两个接口拥有强大的消息分派功能，并且提供了用于实现Web服务端点（endpoints）的各种支持类; 在客户端方面，中心接口则是WebServiceTemplate。
* 支持模块（spring-ws-support.jar），包含额外的传输功能（JMS，电子邮件等）。
* 安全包（spring-ws-security.jar）提供了与核心Web服务包集成的WS-Security实现。 它允许您添加主体令牌（principal token），签名（sign），解密和加密SOAP消息。 此外，它还允许您利用现有Spring Security的安全性实现进行身份验证和授权。

下图说明了Spring-WS模块以及它们之间的依赖关系。 箭头表示依赖关系，即Spring-WS Core依赖于Spring-XML和Spring 3及更高版本中的OXM模块。

![spring-deps](image/spring-deps.png)

##### 1.3 支持的标准

Spring Web Services支持以下标准：

- SOAP 1.1 and 1.2
- WSDL 1.1 and 2.0 (XSD-based generation only supported for WSDL 1.1)
- WS-I Basic Profile 1.0, 1.1, 1.2 and 2.0
- WS-Addressing 1.0 and the August 2004 draft
- SOAP Message Security 1.1, Username Token Profile 1.1, X.509 Certificate Token Profile 1.1, SAML Token Profile 1.1, Kerberos Token Profile 1.1, Basic Security Profile 1.1

####  2. 为什么契约（contract）优先

##### 2.1 简介

创建Web服务时，有两种开发风格：契约优先和契约最后。 在使用契约最后的方法时，首先从Java代码开始，然后让Web Service合约（WSDL）从java代码中生成。 当使用契约优先的风格时，您从WSDL开始，并使用Java来执行实现所述契约。

> WSDL代表Web Service描述语言。 WSDL文件是描述Web服务的XML文档。 它指定了服务的位置以及服务公开的操作（或方法）。 有关WSDL的更多信息，请参阅 [WSDL specification](https://www.w3.org/TR/wsdl)。

Spring-WS只支持契约优先的开发风格，本节解释其原因。

##### 2.2 Object/XML 阻抗不匹配（Impedance Mismatch）

与ORM的字段相似，我们有一个[对象/关系阻抗不匹配](https://en.wikipedia.org/wiki/Object-Relational_impedance_mismatch)，在将Java对象转换为XML时存在类似的问题。 乍一看，O / X映射问题看起来很简单：为每个Java对象创建一个XML元素，将所有Java属性和字段转换为子元素或属性。 然而事情并不像看起来那么简单：XML（尤其是XSD）等分层语言与Java的图形模型之间存在根本的区别。

###### 2.2.1 XSD extensions

在Java中，更改类行为的唯一方法是继承该类，并将新行为添加到其子类中。 在XSD中，可以通过限制数据类型来扩展数据类型：即限制元素和属性的有效值。 例如，考虑下面的例子：

```xml
<simpleType name="AirportCode">
	<restriction base="string">
		<pattern value="[A-Z][A-Z][A-Z]"/>
	</restriction>
</simpleType>
```
这里限制了一个XSD的String类型，只允许三个大写字母。如果这个类型转换为Java，我们将以普通的 *java.lang.String* 结束; 正则表达式在转换过程中丢失了，因为Java不允许这种扩展。

###### 2.2.2 类型不可移植

Web服务最重要的目标之一是可以互操作的：支持Java，.NET，Python等多种平台。因为所有这些语言都有不同的类库，所以你必须使用一些通用的语言间格式来进行通信 它们之间。 这种格式是XML，所有这些语言都支持这种格式。

由于这种转换，您必须确保在服务实现中使用便携式类型。 例如，考虑一个返回 java.util.TreeMap 的服务，如下所示：
```
public Map getFlights() {  // use a tree map, to make sure it's sorted  	TreeMap map = new TreeMap(); 
	map.put("KL1117", "Stockholm"); 
	return map;
}
```

毫无疑问，这个映射的内容可以转换成某种类型的XML，但是由于在XML中没有**标准**的方式来描述映射，所以它是专有的。 另外，即使可以将其转换为XML，许多平台也没有类似于TreeMap的数据结构。 所以当一个.NET客户端访问你的Web服务时，它可能最终会解析为一个System.Collections.Hashtable，这让它具有不同的语义。

在客户端工作时也存在此问题。 考虑下面的XSD片段，它描述了一个服务合约：

```xml
<element name="GetFlightsRequest">
  <complexType>
    <all>
      <element name="departureDate" type="date"/>
      <element name="from" type="string"/>
      <element name="to" type="string"/>
    </all>
  </complexType>
</element>
```

该合同定义了一个请求，该请求采用日期，该日期是表示年，月和日的XSD数据类型。 如果我们从Java调用这个服务，我们可能会使用`java.util.Date`或`java.util.Calendar`。 然而，这两个类实际上是描述时间，而不是日期。 因此，我们实际上最终会发送代表2007年4月4日午夜（`2007-04-04T00：00：00`）的数据，这与`2007-04-04`不一样。

###### 2.2.3 循环图（Cyclic graphs）

假设我们有以下的类结构：

```java
public class Flight {
  private String number;
  private List<Passenger> passengers;

  // getters and setters omitted
}

public class Passenger {
  private String name;
  private Flight flight;

  // getters and setters omitted
}
```

这是一个循环图：`Flight`指向`Passenger`，`Passenger`引用`Flight`。 像Java这样的循环图在Java中很常见。 如果我们采取一种天真的方式将其转换为XML，我们最终会得到如下结果：

```xml
<flight number="KL1117">
  <passengers>
    <passenger>
      <name>Arjen Poutsma</name>
      <flight number="KL1117">
        <passengers>
          <passenger>
            <name>Arjen Poutsma</name>
            <flight number="KL1117">
              <passengers>
                <passenger>
                   <name>Arjen Poutsma</name>
                   ...
```

这将需要相当长的时间才能完成，因为这个循环没有停止条件。

解决这个问题的一个方法是使用已经编组的对象的引用，如下所示：

```xml
<flight number="KL1117">
  <passengers>
    <passenger>
      <name>Arjen Poutsma</name>
      <flight href="KL1117" />
    </passenger>
    ...
  </passengers>
</flight>
```

这解决了递归问题，但引入了新的问题。 首先，你不能使用XML验证器来验证这个结构。 另一个问题是，在SOAP（RPC /encoded）中使用这些引用的标准方式已被弃用，以支持document/literal （请参阅WS-I基本[配置文件](http://www.ws-i.org/Profiles/BasicProfile-1.1.html#SOAP_encodingStyle_Attribute)）。

这些只是处理O / X映射时的几个问题。 编写Web服务时要尊重这些问题。 尊重它们的最好方式是将Java作为一种实现语言，而完全专注于XML。 这才是契约第一。

##### 2.3 契约优先VS契约最后

除了上一节提到的对象/ XML映射问题外，还有一些优先采用契约优先开发风格的原因。

###### 2.3.1 脆弱

如前所述，契约最后的开发风格导致您的Web服务契约（WSDL和您的XSD）是从您的Java（通常是一个接口）生成的。 如果你使用这种方法，你将不能保证约定保持不变。 每次更改Java并重新部署时，可能会对Web服务契约进行更改。

另外，并不是所有的SOAP堆栈都会从Java合约生成相同的Web服务合约。 这意味着改变你的当前SOAP堆栈（不管是什么原因），也可能会改变你的Web服务契约。

当一个Web服务契约发生变化时，契约用户必须被指示获得新的契约，并且可能改变他们的代码以适应契约的任何变化。

为了使契约有用，它必须尽可能保持不变。 如果契约发生变化，您必须联系您的服务的所有用户，并指示他们获得新版本的契约。

###### 2.3.2 性能

当Java自动转换为XML时，无法确定通过线路发送的内容。 一个对象可能引用另一个对象，引用另一个对象等。最后，虚拟机堆中的一半对象可能会转换为XML，这会导致响应时间变慢。

当使用契约优先，你明确地描述了什么地方发送的XML，从而确保它正是你想要的。

###### 2.3.3. 可重用性

在一个单独的文件中定义模式允许您在不同的场景中重用该文件。 如果您在名为`airline.xsd`的文件中定义一个`AirportCode`，如下所示：

```xml
<simpleType name="AirportCode">
    <restriction base="string">
        <pattern value="[A-Z][A-Z][A-Z]"/>
    </restriction>
</simpleType>
```

您可以使用import语句在其他模式，甚至WSDL文件中重复使用此定义。

###### 2.3.4 版本

尽管契约必须尽可能保持不变，但有时候也**确实**需要改变。 在Java中，这通常会导致一个新的Java接口，如`AirlineService2`，以及该接口的一个（新）实现。 当然，旧服务必须保留，因为可能还有客户还没有迁移。

如果使用契约优先，我们可以在契约和执行之间有一个宽松的耦合。 这种宽松的耦合使我们能够在一个类中实现两个版本的合同。 例如，我们可以使用XSLT样式表将任何“老”消息转换为“新”消息。

#### 3 编写契约优先的Web Service

##### 3.1 简介 

本教程将向您展示如何编写[契约优先的Web服务](https://docs.spring.io/spring-ws/docs/3.0.0.RELEASE/reference/#why-contract-first)，也就是开发以XML Schema / WSDL契约开始，随后是Java代码开始的Web服务。 Spring-WS侧重于这种开发风格，本教程将帮助您开始。 请注意，本教程的第一部分几乎没有包含Spring-WS特定信息：它主要是关于XML，XSD和WSDL。 [第二部分](https://docs.spring.io/spring-ws/docs/3.0.0.RELEASE/reference/#tutorial-creating-project)着重于使用Spring-WS实现这个契约。

做契约优先的Web服务开发时最重要的事情就是尝试用XML来思考。 这意味着Java语言的概念不太重要。 您应该专注于通过电线发送的XML。 实现细节仍然是Java被用来实现Web服务的事实。

##### 3.2 消息

在本节中，我们将重点讨论发送到Web服务和从Web服务发送的实际XML消息。 我们将首先确定这些消息是什么样子的。

###### 3.2.1 Holiday

在这种情况下，我们必须处理`Holiday`请求，因此确定XML在假期中的样子是有意义的：

```xml
<Holiday xmlns="http://mycompany.com/hr/schemas">
    <StartDate>2006-07-03</StartDate>
    <EndDate>2006-07-07</EndDate>
</Holiday>
```

A holiday 由开始日期和结束日期组成。 我们还决定在日期中使用标准的[ISO 8601](https://www.cl.cam.ac.uk/~mgk25/iso-time.html)日期格式，因为这样可以节省很多分析的麻烦。 我们还为元素添加了一个名称空间，以确保我们的元素可以在其他XML文档中使用。

###### 3.2.2 Employee

在这种情况下，也有员工的概念。 这里是它在XML中的样子：

```xml
<Employee xmlns="http://mycompany.com/hr/schemas">
    <Number>42</Number>
    <FirstName>Arjen</FirstName>
    <LastName>Poutsma</LastName>
</Employee>
```

我们已经使用了和以前一样的命名空间。 如果`<Employee />`元素可以在其他场景中使用，则可以使用不同的名称空间，比如http://mycompany.com/employees/schemas。

###### 3.2.3 HolidayRequest

`holiday`和`employee`元素都可以放在`<HolidayRequest />`中：

```xml
<HolidayRequest xmlns="http://mycompany.com/hr/schemas">
    <Holiday>
        <StartDate>2006-07-03</StartDate>
        <EndDate>2006-07-07</EndDate>
    </Holiday>
    <Employee>
        <Number>42</Number>
        <FirstName>Arjen</FirstName>
        <LastName>Poutsma</LastName>
    </Employee>
</HolidayRequest>
```

这两个元素的顺序并不重要：<Employee />也可以是第一个元素。 重要的是所有的数据都在那里。 事实上，数据是唯一重要的东西：我们正在采取**数据驱动**的方法。

##### 3.3 Data Contract

现在我们已经看到了一些我们将要使用的XML数据的例子，把它形式化为一个模式是有意义的。 这个数据契约定义了我们接受的消息格式。 定义这种XML合同有四种不同的方式：

* DTDs
* [XML Schema (XSD)](https://www.w3.org/XML/Schema)
* [RELAX NG](http://www.relaxng.org/)
* [Schematron](http://www.schematron.com/)


DTD具有有限的名称空间支持，所以它们不适合Web服务。 RELAX NG和Schematron肯定比XML Schema容易。 不幸的是，它们并没有得到如此广泛的平台支持。 我们将使用XML Schema。

到目前为止，创建XSD最简单的方法是从示例文档中推断出来。 任何好的XML编辑器或Java IDE都可以提供这种功能。 基本上，这些工具使用一些示例XML文档，并从中生成一个模式来验证它们。 最终的结果肯定需要打磨，但这是一个很好的起点。

使用上面描述的示例，我们得到了以下生成的schema：

```xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
		elementFormDefault="qualified"
      targetNamespace="http://mycompany.com/hr/schemas"
        xmlns:hr="http://mycompany.com/hr/schemas">
    <xs:element name="HolidayRequest">
        <xs:complexType>
            <xs:sequence>
                <xs:element ref="hr:Holiday"/>
                <xs:element ref="hr:Employee"/>
            </xs:sequence>
        </xs:complexType>
    </xs:element>
    <xs:element name="Holiday">
        <xs:complexType>
            <xs:sequence>
                <xs:element ref="hr:StartDate"/>
                <xs:element ref="hr:EndDate"/>
            </xs:sequence>
        </xs:complexType>
    </xs:element>
    <xs:element name="StartDate" type="xs:NMTOKEN"/>
    <xs:element name="EndDate" type="xs:NMTOKEN"/>
    <xs:element name="Employee">
        <xs:complexType>
            <xs:sequence>
                <xs:element ref="hr:Number"/>
                <xs:element ref="hr:FirstName"/>
                <xs:element ref="hr:LastName"/>
            </xs:sequence>
        </xs:complexType>
    </xs:element>
    <xs:element name="Number" type="xs:integer"/>
    <xs:element name="FirstName" type="xs:NCName"/>
    <xs:element name="LastName" type="xs:NCName"/>
</xs:schema>
```
这个生成的schema显然可以改进。 首先要注意的是每种类型都有一个根级元素声明。 这意味着Web服务应该能够接受所有这些元素作为数据。 这是不可取的：我们只想接受`<HolidayRequest />`。 通过去除包裹元素标签（从而保持类型），并将结果内联，我们可以做到这一点。

```xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
        xmlns:hr="http://mycompany.com/hr/schemas"
        elementFormDefault="qualified"
        targetNamespace="http://mycompany.com/hr/schemas">
    <xs:element name="HolidayRequest">
        <xs:complexType>
            <xs:sequence>
                <xs:element name="Holiday" type="hr:HolidayType"/>
                <xs:element name="Employee" type="hr:EmployeeType"/>
            </xs:sequence>
        </xs:complexType>
    </xs:element>
    <xs:complexType name="HolidayType">
        <xs:sequence>
            <xs:element name="StartDate" type="xs:NMTOKEN"/>
            <xs:element name="EndDate" type="xs:NMTOKEN"/>
        </xs:sequence>
    </xs:complexType>
    <xs:complexType name="EmployeeType">
        <xs:sequence>
            <xs:element name="Number" type="xs:integer"/>
            <xs:element name="FirstName" type="xs:NCName"/>
            <xs:element name="LastName" type="xs:NCName"/>
        </xs:sequence>
    </xs:complexType>
</xs:schema>
```

该模式仍然有一个问题：像这样的模式，您可以期望以下消息进行验证：

```xml
<HolidayRequest xmlns="http://mycompany.com/hr/schemas">
    <Holiday>
        <StartDate>this is not a date</StartDate>
        <EndDate>neither is this</EndDate>
    </Holiday>
    PlainText Section qName:lineannotation level:4, chunks:[<, !-- ... --, >] attrs:[:]
</HolidayRequest>
```

显然，我们必须确保开始日期和结束日期真的是日期。 XML Schema有一个很好的内置日期类型供我们使用。 我们也把NCName改成字符串s。 最后，我们将`<HolidayRequest />`中的序列更改为all。 这告诉XML解析器，`<Holiday />`和`<Employee />`的顺序不重要。 我们的最终XSD现在看起来像这样：

```xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
        xmlns:hr="http://mycompany.com/hr/schemas"
        elementFormDefault="qualified"
        targetNamespace="http://mycompany.com/hr/schemas">
    <xs:element name="HolidayRequest">
        <xs:complexType>
            <xs:all>
                <xs:element name="Holiday" type="hr:HolidayType"/> 
                <xs:element name="Employee" type="hr:EmployeeType"/> 
            </xs:all>
        </xs:complexType>
    </xs:element>
    <xs:complexType name="HolidayType">
        <xs:sequence>
            <xs:element name="StartDate" type="xs:date"/> 
            <xs:element name="EndDate" type="xs:date"/> 
        </xs:sequence>
    </xs:complexType>
    <xs:complexType name="EmployeeType">
        <xs:sequence>
            <xs:element name="Number" type="xs:integer"/>
            <xs:element name="FirstName" type="xs:string"/> 
            <xs:element name="LastName" type="xs:string"/> 
        </xs:sequence>
    </xs:complexType>
</xs:schema>
```

* `all`告诉XML解析器，`<Holiday />`和`<Employee />`的顺序不重要。
* 对于`<StartDate />`和`<EndDate />`，我们使用`xs：date`数据类型，包括年，月和日。
* `xs：string`用于LastName和FirstName。

我们将上面这个文件存储到hr.xsd.

##### 3.4 Service contract

服务契约通常表示为[WSDL](https://www.w3.org/TR/wsdl)文件。 请注意，在Spring-WS中，手动编写WSDL不是必需的。 基于XSD和一些约定，Spring-WS可以为您创建WSDL，如“[实现mj端点](https://docs.spring.io/spring-ws/docs/3.0.0.RELEASE/reference/#tutorial.implementing.endpoint)”一节中所述。 如果你愿意，你可以跳到[下一节](https://docs.spring.io/spring-ws/docs/3.0.0.RELEASE/reference/#tutorial-creating-project)。 本节的其余部分将向您展示如何手动编写自己的WSDL。

我们用标准序言开始我们的WSDL，并导入我们现有的XSD。 为了从定义中分离模式，我们将为WSDL定义使用单独的名称空间：http://mycompany.com/hr/definitions。
```xml
<wsdl:definitions xmlns:wsdl="http://schemas.xmlsoap.org/wsdl/"
              xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/"
              xmlns:schema="http://mycompany.com/hr/schemas"
              xmlns:tns="http://mycompany.com/hr/definitions"
              targetNamespace="http://mycompany.com/hr/definitions">
<wsdl:types>
    <xsd:schema xmlns:xsd="http://www.w3.org/2001/XMLSchema">
        <xsd:import namespace="http://mycompany.com/hr/schemas" schemaLocation="hr.xsd"/>
    </xsd:schema>
</wsdl:types>
```
接下来，我们根据写入的模式类型添加我们的消息。 我们只有一个消息：一个与`<HolidayRequest />`我们放在架构：

```xml
   <wsdl:message name="HolidayRequest">
        <wsdl:part element="schema:HolidayRequest" name="HolidayRequest"/>
    </wsdl:message>
```

我们将消息添加到端口类型作为操作：

```xml
  <wsdl:portType name="HumanResource">
        <wsdl:operation name="Holiday">
            <wsdl:input message="tns:HolidayRequest" name="HolidayRequest"/>
        </wsdl:operation>
    </wsdl:portType>
```

这完成了WSDL的抽象部分（接口，原来是这样），并离开了具体部分。 具体部分由一个`binding`组成，它告诉客户**如何**调用你刚刚定义的操作; 和一个`service`，告诉它在哪里调用它。

添加一个具体的部分是非常标准的：只要引用您之前定义的抽象部分，确保您使用`soap：binding`元素（`rpc / encoded`已弃用）的**document / literal**，为操作选择一个`soapAction`（在这种情况下为http://mycompany.com/RequestHoliday，但任何URI都可以），并确定你想要请求的`location`URL（在这个例子中是http://mycompany.com/humanresources）：

```xml
<wsdl:definitions xmlns:wsdl="http://schemas.xmlsoap.org/wsdl/"
                  xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/"
                  xmlns:schema="http://mycompany.com/hr/schemas"
                  xmlns:tns="http://mycompany.com/hr/definitions"
                  targetNamespace="http://mycompany.com/hr/definitions">
    <wsdl:types>
        <xsd:schema xmlns:xsd="http://www.w3.org/2001/XMLSchema">
            <xsd:import namespace="http://mycompany.com/hr/schemas"              
                schemaLocation="hr.xsd"/>
        </xsd:schema>
    </wsdl:types>
    <wsdl:message name="HolidayRequest">                                         
        <wsdl:part element="schema:HolidayRequest" name="HolidayRequest"/>       
    </wsdl:message>
    <wsdl:portType name="HumanResource">                                         
        <wsdl:operation name="Holiday">
            <wsdl:input message="tns:HolidayRequest" name="HolidayRequest"/>     
        </wsdl:operation>
    </wsdl:portType>
    <wsdl:binding name="HumanResourceBinding" type="tns:HumanResource">          
        <soap:binding style="document"                                           
            transport="http://schemas.xmlsoap.org/soap/http"/>                   
        <wsdl:operation name="Holiday">
            <soap:operation soapAction="http://mycompany.com/RequestHoliday"/>   
            <wsdl:input name="HolidayRequest">
                <soap:body use="literal"/>                                       
            </wsdl:input>
        </wsdl:operation>
    </wsdl:binding>
    <wsdl:service name="HumanResourceService">
        <wsdl:port binding="tns:HumanResourceBinding" name="HumanResourcePort">  
            <soap:address location="http://localhost:8080/holidayService/"/>     
        </wsdl:port>
    </wsdl:service>
</wsdl:definitions>
```

1. 我们导入[Data Contract](https://docs.spring.io/spring-ws/docs/3.0.0.RELEASE/reference/#tutorial.xsd)中定义的模式。
2. 我们定义了在`PortType`中使用的`HolidayRequest`消息。
3. `HolidayRequest`类型在`schema`中定义了。
4. 我们定义`HumanResource`端口类型，它在`binding`中使用。
5. 我们定义了在端口中使用的`HumanResourceBinding`绑定。
6. 我们使用了 document/literal 风格.
7. literal 的http://schemas.xmlsoap.org/soap/http表示一个HTTP传输。
8. `soapAction`属性表示将随每个请求一起发送的`SOAPAction` HTTP标头。
9. http:// localhost:8080/holidayService/address是可以调用Web服务的URL。

这是最终的WSDL。 我们将在下一节描述如何实现结果模式和WSDL。

##### 3.5 创建项目

在本节中，我们将使用Maven3为我们创建初始项目结构。 这样做并不是必需的，但是大大减少了我们编写的用于设置我们的HolidayService的代码量。

下面的命令为我们创建一个Maven3 Web应用程序项目，使用Spring-WS原型（即项目模板）

```shell
mvn archetype:create -DarchetypeGroupId=org.springframework.ws \
  -DarchetypeArtifactId=spring-ws-archetype \
  -DarchetypeVersion= \
  -DgroupId=com.mycompany.hr \
  -DartifactId=holidayService
```

该命令将创建一个名为holidayService的新目录。 在这个目录中，有一个“src / main / webapp”目录，它将包含WAR文件的根目录。 您将在这里找到标准Web应用程序部署描述符“WEB-INF / web.xml”，它定义了一个Spring-WS MessageDispatcherServlet并将所有传入请求映射到此servlet。

```xml
<web-app xmlns="http://java.sun.com/xml/ns/j2ee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee
             http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd"
         version="2.4">

    <display-name>MyCompany HR Holiday Service</display-name>

    <!-- take special notice of the name of this servlet -->
    <servlet>
        <servlet-name>spring-ws</servlet-name>
        <servlet-class>org.springframework.ws.transport.http.MessageDispatcherServlet</servlet-class>
    </servlet>

    <servlet-mapping>
        <servlet-name>spring-ws</servlet-name>
        <url-pattern>/*</url-pattern>
    </servlet-mapping>

</web-app>
```

除了上面的`'WEB-INF/web.xml'`文件之外，还需要另外一个名为`WEB-INF/spring-ws-servlet.xml`的特定于Spring-WS的配置文件。 这个文件包含所有Spring-WS特定的bean，比如`EndPoints`，`WebServiceMessageReceivers`等等，并且用来创建一个新的Spring容器。 这个文件的名字来源于附加了`-servlet.xml`的servlet的名称（在本例中是`spring-ws`）。 所以，如果你定义了一个名为`dynamite`的`MessageDispatcherServlet`，那么Spring-WS特定的配置文件的名字就是`WEB-INF/dynamite-servlet.xml`。

（您可以在[[tutorial.example.sws-conf-file](https://docs.spring.io/spring-ws/docs/3.0.0.RELEASE/reference/#tutorial.example.sws-conf-file)]中看到本示例的`WEB-INF / spring-ws-servlet.xml`文件的内容。

一旦创建了项目结构，就可以将上一节中的schema和wsdl放入`WEB-INF /`文件夹中。

##### 3.6 Implementing the Endpoint

在Spring-WS中，您将实现Endpoints来处理传入的XML消息。 一个endpoint 通常是通过用@Endpoint注解注释一个类来创建的。 在此endpoint 类中，您将创建一个或多个处理传入请求的方法。 方法签名可以非常灵活：您可以包含与传入的XML消息相关的任何类型的参数类型，并在之后对其进行解释。

###### 3.6.1 处理XML Message

在这个示例应用程序中，我们将使用[JDom 2](http://www.jdom.org/)来处理XML消息。 我们也使用[XPath](https://www.w3.org/TR/xpath20/)，因为它允许我们选择XML JDOM树的特定部分，而不需要严格的模式一致性。

```java
package com.mycompany.hr.ws;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Arrays;
import java.util.Date;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.ws.server.endpoint.annotation.Endpoint;
import org.springframework.ws.server.endpoint.annotation.PayloadRoot;
import org.springframework.ws.server.endpoint.annotation.RequestPayload;

import com.mycompany.hr.service.HumanResourceService;
import org.jdom2.Element;
import org.jdom2.JDOMException;
import org.jdom2.Namespace;
import org.jdom2.filter.Filters;
import org.jdom2.xpath.XPathExpression;
import org.jdom2.xpath.XPathFactory;

@Endpoint                                             
public class HolidayEndpoint {
    private static final String NAMESPACE_URI = "http://mycompany.com/hr/schemas";

    private XPathExpression<Element> startDateExpression;

    private XPathExpression<Element> endDateExpression;

    private XPathExpression<Element> firstNameExpression;

    private XPathExpression<Element> lastNameExpression;

    private HumanResourceService humanResourceService;

    @Autowired                                                                                
    public HolidayEndpoint(HumanResourceService humanResourceService) throws JDOMException {
        this.humanResourceService = humanResourceService;

        Namespace namespace = Namespace.getNamespace("hr", NAMESPACE_URI);
        XPathFactory xPathFactory = XPathFactory.instance();
        startDateExpression = xPathFactory.compile("//hr:StartDate", Filters.element(), null, namespace);
        endDateExpression = xPathFactory.compile("//hr:EndDate", Filters.element(), null, namespace);
        firstNameExpression = xPathFactory.compile("//hr:FirstName", Filters.element(), null, namespace);
        lastNameExpression = xPathFactory.compile("//hr:LastName", Filters.element(), null, namespace);
    }

    @PayloadRoot(namespace = NAMESPACE_URI, localPart = "HolidayRequest")                      
    public void handleHolidayRequest(@RequestPayload Element holidayRequest) throws Exception {
        Date startDate = parseDate(startDateExpression, holidayRequest);
        Date endDate = parseDate(endDateExpression, holidayRequest);
        String name = firstNameExpression.evaluateFirst(holidayRequest).getText() + " " + lastNameExpression.evaluateFirst(holidayRequest).getText();

        humanResourceService.bookHoliday(startDate, endDate, name);
    }

    private Date parseDate(XPathExpression<Element> expression, Element element) throws ParseException {
        Element result = expression.evaluateFirst(element);
        if (result != null) {
            SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");
            return dateFormat.parse(result.getText());
        } else {
            throw new IllegalArgumentException("Could not evaluate [" + expression + "] on [" + element + "]");
        }
    }

}

```

1. `HolidayEndpoint`用`@Endpoint`注释。 这将该类标记为一个@Component，适合在Spring-WS中处理XML消息，并使其适用于组件扫描。
2. `HolidayEndpoint`需要`HumanResourceService`业务服务进行操作，所以我们通过构造函数注入依赖关系，并使用`@Autowired`对其进行注释。 接下来，我们使用JDOM2 API设置XPath表达式。 有四个表达式：`// hr：StartDate`用于提取`<StartDate>`文本值，`// hr：EndDate`用于提取结束日期，另外两个用于提取员工的姓名。
3. `@PayloadRoot`注解告诉Spring-WS，`handleHolidayRequest`方法适合处理XML消息。 此方法可以处理的消息类型由注释值指示，在这种情况下，它可以处理具有`HolidayRequest`本地部分和`http://mycompany.com/hr/schemas`命名空间的XML元素。 有关将消息映射到端点的更多信息在下一节中提供。
4. `handleHolidayRequest（..）`方法是主要的处理方法方法，它通过传入XML消息中的`<HolidayRequest />`元素传递。`@RequestPayload`注解指示应该将`holidayRequest`参数映射到请求消息的payload 。 我们使用XPath表达式从XML消息中提取字符串值，并使用`SimpleDateFormat`（`parseData`方法）将这些值转换为Date对象。 有了这些值，我们就可以调用业务服务的方法了。 通常，这将导致数据库事务正在启动，并且在数据库中某些记录被更改。最后，我们定义一个void返回类型，它向Spring-WS指出我们不想发送响应消息。 如果我们想要一个响应消息，我们可以返回一个代表响应消息payload的JDOM元素。

使用JDOM只是处理XML的一个选项：其他选项包括DOM，dom4j，XOM，SAX和StAX，还有像JAXB，Castor，XMLBeans，JiBX和XStream这样的编组技术，下一章将对其进行介绍。 我们选择JDOM是因为它使我们能够访问原始XML，并且因为它基于类（不像W3C DOM和dom4j那样是基于接口和工厂方法），这样使得代码更加精简。 我们使用XPath是因为它不像编组技术那样脆弱：我们不关心严格的模式一致性，只要我们可以找到日期和名称即可。

因为我们使用JDOM，我们必须添加一些依赖到Maven pom.xml，这是在我们的项目目录的根目录。 这是POM的相关部分：

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.ws</groupId>
        <artifactId>spring-ws-core</artifactId>
        <version></version>
    </dependency>
    <dependency>
        <groupId>jdom</groupId>
        <artifactId>jdom</artifactId>
        <version>2.0.1</version>
    </dependency>
    <dependency>
        <groupId>jaxen</groupId>
        <artifactId>jaxen</artifactId>
        <version>1.1</version>
    </dependency>
</dependencies>
```

以下是我们将如何使用组件扫描在我们的spring-ws-servlet.xml Spring XML配置文件中配置这些类。 我们还通过<sws：annotation-driven>元素指示Spring-WS使用注释驱动的端点。

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:context="http://www.springframework.org/schema/context"
  xmlns:sws="http://www.springframework.org/schema/web-services"
  xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
  http://www.springframework.org/schema/web-services http://www.springframework.org/schema/web-services/web-services-2.0.xsd
  http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd">

  <context:component-scan base-package="com.mycompany.hr"/>

  <sws:annotation-driven/>

</beans>
```

###### 3.6.2 路由信息到 Endpoint

As part of writing the endpoint, we also used the `@PayloadRoot` annotation to indicate which sort of messages can be handled by the `handleHolidayRequest` method. In Spring-WS, this process is the responsibility of an `EndpointMapping`. Here we route messages based on their content, by using a `PayloadRootAnnotationMethodEndpointMapping`. The annotation used above:

```java
@PayloadRoot(namespace = "http://mycompany.com/hr/schemas", localPart = "HolidayRequest")
```

基本上意味着只要接收到命名空间为http://mycompany.com/hr/schemas并且`HolidayRequest`为本地名称的XML消息，它就会被路由到`handleHolidayRequest`方法。 通过在我们的配置中使用`<sws：annotation-driven>`元素，我们启用对`@PayloadRoot`注释的检测。 在endpoint中有多个相关的处理方法是可以的（也是相当普遍的），每个方法处理不同的XML消息。

还有其他方法可以将端点映射到XML消息，这将在下一章中介绍。

###### 3.6.3 提供服务和存根实施

现在我们有了**Endpoint**，我们需要`HumanResourceService`及其实现供`HolidayEndpoint`使用。

```java
package com.mycompany.hr.service;

import java.util.Date;

public interface HumanResourceService {
    void bookHoliday(Date startDate, Date endDate, String name);
}
```

为了教程的目的，我们将使用`HumanResourceService`的一个简单的存根实现。

```java
package com.mycompany.hr.service;

import java.util.Date;

import org.springframework.stereotype.Service;

@Service                                                                 
public class StubHumanResourceService implements HumanResourceService {
    public void bookHoliday(Date startDate, Date endDate, String name) {
        System.out.println("Booking holiday for [" + startDate + "-" + endDate + "] for [" + name + "] ");
    }
}
```

1. StubHumanResourceService用@Service注释。 这标志着该类作为一个访问的门面，这使得这个类可以通过@Autowired注入HolidayEndpoint。

##### 3.7 公开WSDL

最后，我们需要发布WSDL。 如服务合同中所述，我们不需要自己编写WSDL; Spring-WS可以根据一些约定为我们生成一个。 下面是我们如何定义一代：

```xml
<sws:dynamic-wsdl id="holiday"
    portTypeName="HumanResource"
    locationUri="/holidayService/" targetNamespace="http://mycompany.com/hr/definitions">    
  <sws:xsd location="/WEB-INF/hr.xsd"/>
</sws:dynamic-wsdl>
```

1. 该id确定可以检索WSDL的URL。 在这种情况下，id是holiday，这意味着WSDL可以在servlet上下文中作为`holiday.wsdl`检索。 完整的URL通常是http://localhost:8080/holidayService/holiday.wsdl。
2. 接下来，我们将WSDL端口类型设置为`HumanResource`。
3. 我们设置服务可以到达的位置`:/holidayService/`。 我们使用相对URI，并指示框架将其动态转换为绝对URI。 因此，如果服务部署到不同的上下文中，我们不必手动更改URI。 有关更多信息，请参阅“[WSDL自动暴露](https://docs.spring.io/spring-ws/docs/3.0.0.RELEASE/reference/#server-automatic-wsdl-exposure)”一节。 为了实现位置转换，我们需要在`web.xml`中添加一个init参数给`spring-ws `servlet（如下所示）。
4. 我们为WSDL定义本身定义目标名称空间。 设置此属性不是必需的。 如果没有设置，WSDL将具有与XSD架构相同的名称空间。
5. `xsd`元素是指我们在[Data Contract](https://docs.spring.io/spring-ws/docs/3.0.0.RELEASE/reference/#tutorial.xsd)中定义的人力资源模式。 我们只是将模式放在应用程序的`WEB-INF`目录中。

```xml
<init-param>
  <param-name>transformWsdlLocations</param-name>
  <param-value>true</param-value>
</init-param>
```

您可以使用mvn install来创建一个WAR文件。 如果您部署应用程序（到Tomcat，Jetty等），并将浏览器指向此位置，您将看到生成的WSDL。 这个WSDL已经准备好被客户端使用，比如soapUI或者其他的SOAP框架。

本教程结束。 教程代码可以在Spring-WS的完整发行版中找到。 下一步就是查看作为发行版一部分的回声示例应用程序。 之后，看航空公司的样本，这有点复杂，因为它使用JAXB，WS-Security，Hibernate和事务服务层。 最后，您可以阅读参考文档的其余部分。



<<<<<<< HEAD
### 二、参考文档

参考文档的这一部分详细介绍了构成Spring Web Services的各个组件。 包括了[一章](https://docs.spring.io/spring-ws/docs/2.4.2.RELEASE/reference/#common)讨论客户端和服务器端的通用部分，一章讨论[编写服务器端WS](https://docs.spring.io/spring-ws/docs/2.4.2.RELEASE/reference/#server)，一章讨论如何在[客户端使用Web服务](https://docs.spring.io/spring-ws/docs/2.4.2.RELEASE/reference/#client)，以及一章讨论如何使用[WS-Security](https://docs.spring.io/spring-ws/docs/2.4.2.RELEASE/reference/#security)。

#### 4 共用组件

在本章中，我们将探讨在客户端和服务器端Spring-WS开发之间共享的组件。 这些被共享的接口和类代表了Spring-WS的构建块(building blocks)，因此即使不直接使用它们，理解它们的作用也是很重要的。

##### 4.1 Web service messages

###### 4.1.1 `WebServiceMessage`

Spring Web Services的核心接口之一是`WebServiceMessage`。 这个接口表示一个协议不可知的XML消息。 该接口以`javax.xml.transform.Source`或`javax.xml.transform.Result`的形式提供对消息负载的访问。`Source`和`Result`是代表XML输入和输出的一种抽象抽象的标记接口。 具体实现的各种XML包装类，如下表所示。

| **Source/Result implementation**         | **Wraps XML representation**             |
| ---------------------------------------- | ---------------------------------------- |
| `javax.xml.transform.dom.DOMSource`      | `org.w3c.dom.Node`                       |
| `javax.xml.transform.dom.DOMResult`      | `org.w3c.dom.Node`                       |
| `javax.xml.transform.sax.SAXSource`      | `org.xml.sax.InputSource` and `org.xml.sax.XMLReader` |
| `javax.xml.transform.sax.SAXResult`      | org.xml.sax.ContentHandler               |
| `javax.xml.transform.stream.StreamSource` | `java.io.File`, `java.io.InputStream`, or `java.io.Reader` |
| `javax.xml.transform.stream.StreamResult` | `java.io.File`, `java.io.OutputStream`, or `java.io.Writer` |

除了从负载读写之外，Web服务消息还可以将自己写入输出流。

###### 4.1.2 SoapMessage

SoapMessage是WebServiceMessage的一个子类。 它包含特定于SOAP的方法，例如获取SOAP Header，SOAP Faults等。通常，您的代码不应该依赖于SoapMessage，因为SOAP Body的内容（消息的有效内容）可以通过getPayloadSource（） 和WebServiceMessage中的getPayloadResult（）。 只有在需要执行特定于SOAP的操作（如添加标头(head)，获取附件等）时，才需要将WebServiceMessage强制转换为SoapMessage。

###### 4.1.3 Message Factories

具体的消息实现由WebServiceMessageFactory创建。 这个工厂可以创建一个空的消息，或者根据输入流读取消息。 WebServiceMessageFactory有两个具体的实现; 一个基于SAAJ(the SOAP with Attachments API for Java),另一个是基于Axis 2的AXIOM的AXis对象模型(AXis Object Model)。

*SaajSoapMessageFactory*

SaajSoapMessageFactory使用带有附件API的SOAP来创建SoapMessage实现。 SAAJ是J2EE 1.4的一部分，所以它应该在大多数现代应用程序服务器下得到支持。 以下是常用应用程序服务器提供的SAAJ版本的概述：

| Application Server                       | **SAAJ** Version |
| ---------------------------------------- | ---------------- |
| BEA WebLogic 8                           | 1.1              |
| BEA WebLogic 9                           | 1.1/1.21         |
| IBM WebSphere 6                          | 1.2              |
| SUN Glassfish 1                          | 1.3              |
| 1Weblogic 9 在 **SAAJ** 1.2 实现中有一个一直的bug: 它实现了所有1.2的借口, 但是当调用接口的时候会抛出 `UnsupportedOperationException` . Spring Web Services 有一个工作区: 这个工作区在操作WebLogic 9 时使用**SAAJ** 1.1. |                  |

另外，Java SE 6包含了SAAJ 1.3。 你像下面这样声明一个SaajSoapMessageFactory：

```xml
<bean id="messageFactory" class="org.springframework.ws.soap.saaj.SaajSoapMessageFactory" />
```

> **SAAJ**基于 DOM, the Document Object Model. 这表示所有SOAP信息都存储与**内存**中. 对于很大的SOAP信息, 这可能不是非常高性能的. 这种情况下, `AxiomSoapMessageFactory`会更实用.

*AxiomSoapMessageFactory*

`AxiomSoapMessageFactory` 使用AXis 2 Object Model 来实现`SoapMessage`. **AXIOM**基于 **StAX**, the Streaming API for XML（XML的流式API）. StAX为读取XML消息提供了一个基于拉（pull）机制，这种机制对于处理大的信息具有更高的效率。

要提高`AxiomSoapMessageFactory`的读取性能，可以将`payloadCaching`属性设置为false（默认值为true）。 这将直接从套接字流中读取SOAP主体的内容。 启用此设置时，只能读取一次有效负载（payload）。 这意味着你必须确保消息的任何预处理（记录等）不会消耗(consume)它。

你可以想下面这样使用`AxiomSoapMessageFactory`:

```xml
<bean id="messageFactory" class="org.springframework.ws.soap.axiom.AxiomSoapMessageFactory">
    <property name="payloadCaching" value="true"/>
</bean>
```

除了有效载荷缓存外，**AXIOM**还支持`StreamingWebServiceMessage`中定义的完整的流消息。 这意味着负载（payload）可以直接设置在响应消息上，而不是写入DOM树或缓冲区。

**AXIOM**的完整流处理是当一个处理器(handler)返回支持JAXB2的对象时才会使用的。 它会自动设置这个编组对象(marshalled object)到响应消息中，并在响应发送时写出到外发套接字流中。

有关完整流式传输的更多信息，请参阅StreamingWebServiceMessage和StreamingPayload的类级Javadoc。

<font color='red'>SOAP 1.1 or 1.2</font>

SaajSoapMessageFactory和AxiomSoapMessageFactory都有一个soapVersion属性，您可以在其中注入一个SoapVersion常量。 默认情况下，版本是1.1，但是可以像这样设置为1.2：

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:util="http://www.springframework.org/schema/util"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-2.0.xsd
       http://www.springframework.org/schema/util
       http://www.springframework.org/schema/util/spring-util-2.0.xsd">

    <bean id="messageFactory" class="org.springframework.ws.soap.saaj.SaajSoapMessageFactory">
        <property name="soapVersion">
            <util:constant static-field="org.springframework.ws.soap.SoapVersion.SOAP_12"/>
        </property>
    </bean>

</beans>
```

上面的示例中, 我们定义了一个 只接受**SOAP** 1.2 messages的`SaajSoapMessageFactory`  .

> 尽管两种版本的**SOAP**在格式上都非常相似，但1.2版本并不向下兼容1.1版本，因为它使用了不同的XML命名空间。 **SOAP** 1.1和1.2之间的其他主要差异包括Fault的不同结构，以及`SOAPAction` HTTP头被因为效率问题而被弃用，但他们实际上还是可以使用。
>
> 使用SOAP版本号或WS- *规范版本号通常需要注意的一个重要事项是，最新版本的规范通常不是最流行的版本。 对于SOAP，这意味着目前最好的版本是1.1。 1.2版可能会在未来更受欢迎，但是目前1.1是最安全的选择。

###### 4.1.4 `MessageContext`

通常情况下，消息是成对的：一个请求对应一个响应。 在客户端创建一个请求，通过一些传输将请求发送到服务器端，在该服务器端生成响应。 这个响应被发送回客户端，在那里被读取。

在Spring Web Services中，这样的对话包含在`MessageContext`中，它具有获取请求和响应消息的属性。 在客户端，MessageContext由`WebServiceTemplate`创建。 在服务器端，MessageContext是从传输特定的输入流中读取的。 例如，在HTTP中，它是从`HttpServletRequest`中读取的，并将响应写回到`HttpServletResponse`中。

##### 4.2 `TransportContext`

SOAP协议的一个关键属性是它试图与传输无关。 这就是为什么Spring-WS不支持通过HTTP请求URL将消息映射到端点（endpoints），而是通过消息内容映射。

但是，有时需要在客户端或服务器端访问底层传输(underlying transport)。 为此，Spring Web Services提供了`TransportContext`。 传输上下文（transport context）允许访问底层的`WebServiceConnection`，它通常是服务器端的`HttpServletConnection`; 或客户端的`HttpUrlConnection`或`CommonsHttpConnection`。 例如，您可以在服务器端点（server-side endpoint）或拦截器中(interceptor)获取当前请求的IP地址，如下所示：

```java
TransportContext context = TransportContextHolder.getTransportContext();
HttpServletConnection connection = (HttpServletConnection )context.getConnection();
HttpServletRequest request = connection.getHttpServletRequest();
String ipAddress = request.getRemoteAddr();
```

##### 4.3 Handling XML With XPath

XPath是处理XML文档最好的方法之一 . 引自[[effective-xml\]](https://docs.spring.io/spring-ws/docs/2.4.2.RELEASE/reference/#effective-xml), 第35条:

> XPath is a fourth generation declarative language that allows you to specify which nodes you want to process without specifying exactly how the processor is supposed to navigate to those nodes. XPath’s data model is very well designed to support exactly what almost all developers want from XML. For instance, it merges all adjacent text including that in CDATA sections, allows values to be calculated that skip over comments and processing instructions` and include text from child and descendant elements, and requires all external entity references to be resolved. In practice, XPath expressions tend to be much more robust against unexpected but perhaps insignificant changes in the input document.
>
> ​								— Elliotte Rusty Harold

Spring Web Services有两种在应用程序中使用XPath的方法：其中，XpathExpression更快速，而XPathTemplate更灵活。

###### 4.3.1 `XPathExpression`

XPathExpression是对编译的XPath表达式（如Java 5 中的javax.xml.xpath.XPathExpression或Jaxen的XPath类）的抽象。 要在应用程序上下文中构建表达式，需要使用XPathExpressionFactoryBean。下面有一个使用这个工厂bean的例子：

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans-2.0.xsd">

    <bean id="nameExpression" class="org.springframework.xml.xpath.XPathExpressionFactoryBean">
        <property name="expression" value="/Contacts/Contact/Name"/>
    </bean>

    <bean id="myEndpoint" class="sample.MyXPathClass">
        <constructor-arg ref="nameExpression"/>
    </bean>

</beans>
```

上面的表达式不使用名称空间，但是我们可以使用factory bean的名称空间属性来设置它们。 表达式可以在代码中使用如下：

```java
package sample;

public class MyXPathClass {

    private final XPathExpression nameExpression;

    public MyXPathClass(XPathExpression nameExpression) {
        this.nameExpression = nameExpression;
    }

    public void doXPath(Document document) {
        String name = nameExpression.evaluateAsString(document.getDocumentElement());
        System.out.println("Name: " + name);
    }

}
```

为了更灵活的方法，你可以使用一个`NodeMapper`，它类似于Spring的JDBC中的`RowMapper`。 以下示例显示了我们如何使用它：

```java
package sample;

public class MyXPathClass  {

   private final XPathExpression contactExpression;

   public MyXPathClass(XPathExpression contactExpression) 	{
      this.contactExpression = contactExpression;
   }

   public void doXPath(Document document) {
      List contacts = contactExpression.evaluate(document,
        new NodeMapper() {
           public Object mapNode(Node node, int nodeNum) throws DOMException {
              Element contactElement = (Element) node;
              Element nameElement = (Element) contactElement.getElementsByTagName("Name").item(0);
              Element phoneElement = (Element) contactElement.getElementsByTagName("Phone").item(0);
              return new Contact(nameElement.getTextContent(), phoneElement.getTextContent());
           }
        });
      PlainText Section qName:lineannotation level:5, chunks:[// do something with list of Contact objects] attrs:[:]
   }
}
```

与Spring JDBC中的`RowMapper`映射类似，每个结果节点(result node)都使用匿名内部类进行映射。 在这种情况下，我们创建一个`Contact`对象，稍后我们会使用它.

4.3.2 `XPathTemplate`

**XPathExpression**仅允许您评估一个预编译的表达式。 **XpathTemplate**更灵活，但速度更慢。 这个类遵循Spring中通用的模板模式（JdbcTemplate，JmsTemplate等）。 下面是一个例子：

```java
package sample;

public class MyXPathClass {

    private XPathOperations template = new Jaxp13XPathTemplate();

    public void doXPath(Source source) {
        String name = template.evaluateAsString("/Contacts/Contact/Name", request);
        // do something with name
    }

}
```

##### 4.4. 信息日志和追溯(Message Logging and Tracing)

在开发或调试Web服务时，在（SOAP）消息到达时或者在发送之前查看（SOAP）消息的内容是非常有用的。 Spring Web Services通过标准的Commons Logging接口提供了这个功能

> 确保使用Commons Logging version 1.1或更高版本。 早期版本有类加载问题，不能与Log4J TRACE级别集成。

要记录所有服务器端消息，只需将`org.springframework.ws.server.MessageTracing`记录器设置为DEBUG或TRACE级别即可。 在调试级别上，只记录净荷根元素; 在TRACE级别，整个消息内容。 如果您只想记录发送的消息，请使用`org.springframework.ws.server.MessageTracing.sent`记录器; 或`org.springframework.ws.server.MessageTracing.received`记录收到的消息。

在客户端，存在类似的记录器：`org.springframework.ws.client.MessageTracing.sent`和`org.springframework.ws.client.MessageTracing.received`。

下面是log4j.properties配置示例，记录客户端发送的消息的完整内容，以及客户端收到的消息的唯一有效负载根元素。 在服务器端，记录有效负载根用于发送和接收的消息：

```properties
log4j.rootCategory=INFO, stdout
log4j.logger.org.springframework.ws.client.MessageTracing.sent=TRACE
log4j.logger.org.springframework.ws.client.MessageTracing.received=DEBUG

log4j.logger.org.springframework.ws.server.MessageTracing=DEBUG

log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%p [%c{3}] %m%n
```

下面是这个配置的一个典型输出：

```
TRACE [client.MessageTracing.sent] Sent request [<SOAP-ENV:Envelope xmlns:SOAP-ENV="...
DEBUG [server.MessageTracing.received] Received request [SaajSoapMessage {http://example.com}request] ...
DEBUG [server.MessageTracing.sent] Sent response [SaajSoapMessage {http://example.com}response] ...
DEBUG [client.MessageTracing.received] Received response [SaajSoapMessage {http://example.com}response] ...
```



#### 5 使用Spring-WS创建 Web service 

##### 5.1简介

Spring-WS的服务器侧的支持是围绕着一个`MessageDispatcher`设计的，这个`MessageDispather`将进入的消息分发到端点，具有可配置的端点映射（end mappings)，响应生成(response generation)和端点拦截(endpoint interception)。 端点通常使用`@Endpoint`注释进行注释，并具有一个或多个处理方法(handling methods)。 这些方法通过检查部分消息（通常是负载(payload)）来处理传入的XML请求消息，并创建某种响应。 您可以使用另一个注释（通常是@PayloadRoot）来注释请求的方法，以指明它可以处理的消息类型。

Spring-WS的XML处理非常灵活。 端点可以从Spring-WS支持的大量XML处理库中进行选择，包括DOM系列（W3C DOM，JDOM，dom4j和XOM），如果想要更高的性能可以使用SAX或StAX，想从消息中提取信息可以使用XPath，或者 编组（marshalling）技术（JAXB，Castor，XMLBeans，JiBX或XStream）将XML转换为对象，或者将对象转换为XML。

##### 5.2 `MessageDispatcher`

Spring-WS的服务器端是围绕着将传入的XML消息分发到端点的中心类（`MessageDispather`）来设计的。 Spring-WS的`MessageDispatcher`非常灵活，只要可以在Spring IoC容器中配置，就可以使用任何类作为端点。 从某种意义上说，message dispatcher 类似于Spring的`DispatcherServlet`，即Spring Web MVC中使用的“前端控制器(Front Controller)”。

`MessageDispatcher`的处理和调度流程如下图所示。

![序列图](image/sequence.png)

当`MessageDispatcher` 开始使用，一个请求进入该Dispather，然后`MessageDispatcher`开始处理该请求。 下面的列表描述了由`MessageDispatcher`处理时请求所经历的完整过程：

1. 使用配置过的EndpointMapping（s）搜索适当的端点。 如果找到端点，则将执行与端点(预处理器(pre-processors)，后处理器(post-processors)和端点(endpoints))相关联的调用链以创建响应。
2. 为端点(`endPoint`)寻找一个合适的适配器(`adpter`).  `MessageDispatcher` 将dialing这个适配器以调用端点(endpoint)的方法。
3. 如果返回响应，则发送它。 如果没有响应被返回（这可能是由于预处理器或后置处理器拦截了请求，比如出于安全原因），则不发送响应。

在处理请求期间抛出的异常被在应用程序上下文中声明的任何端点异常解析器(resolvers)所取代。 通过使用这些异常解析器，您可以定义自定义行为（例如返回SOAP错误），以防引发这些异常。

`MessageDispatcher`有几个属性，分别用于设置端点适配器(endpoint adapters)，映射(mapping)，异常解析器(exception resolvers)。 但是，设置这些属性不是必须的，因为dispather将自动检测所有这些在应用程序上下文中注册了的类型。 只有当需要被覆盖时，才应该设置这些属性。

 message dispatcher在 [message context](https://docs.spring.io/spring-ws/docs/2.4.2.RELEASE/reference/#message-context)上运行，而不是在特定的输入流和输出流上传输。 因此，特定传输方式的请求需要读入一个`MessageContext` 中。 对于HTTP，可以通过一个`WebServiceMessageReceiverHandlerAdapter`（它是一个Spring Web `HandlerInterceptor`）完成，因此MessageDispatcher可以被加载到一个标准的DispatcherServlet中。 有一个更方便的方法来做到这一点，请参见[`MessageDispatcherServlet`](https://docs.spring.io/spring-ws/docs/2.4.2.RELEASE/reference/#message-dispatcher-servlet)。

##### 5.3 Transports

Spring Web Services 支持多种传输协议. 其中最常见的是 HTTP 传输, SWS为其定义了一种特殊类型的servlet, 它也可以通过JMS和Email进行传输。

###### 5.3.1 `MessageDispatcherServlet`

`MessageDispatcherServlet`是一个标准的`Servlet`，他是标准的Spring Web `DispatcherServlet` 的一个扩展，他包装了一个`MessageDispatcher`。 因此，它将以下接口合而为一：`MessageDispatcher`，它遵循与前一节中所述相同的请求处理流程； `Servlet`，`MessageDispatcherServlet`必须在你web应用中的web.xml中进行配置。你想让 `MessageDispatcherServlet` 处理的请求也必须使用同一web.xml文件中的URL映射。 这是标准的Java EE servlet配置; 下面是一个`MessageDispatcherServlet`声明和映射的例子。

```xml
<web-app>

    <servlet>
        <servlet-name>spring-ws</servlet-name>
        <servlet-class>org.springframework.ws.transport.http.MessageDispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>spring-ws</servlet-name>
        <url-pattern>/*</url-pattern>
    </servlet-mapping>

</web-app>
```

在上面的例子中，所有请求都将由'spring-ws' `MessageDispatcherServlet`处理。 这只是设置Spring Web服务的第一步，因为Spring-WS框架使用的各种组件bean也需要配置; 这个配置由标准的Spring XML `<bean />`定义组成。 由于`MessageDispatcherServlet`是一个标准的Spring `DispatcherServlet`，因此它将在您的Web应用程序的`WEB-INF`目录中查找名为 *[servlet-name] -servlet.xml* 的文件，并在Spring容器中创建定义的bean。 在上面的例子中，这意味着它查找'/WEB-INF/spring-ws-servlet.xml'。 这个文件将包含所有的Spring Web Services bean，如端点(endpoints)，marshallers等。

作为`web.xml`的替代方案，如果您正在Servlet 3+环境中运行，则可以以编程方式配置Spring-WS。 为此，Spring-WS提供了许多抽象基类，它们扩展了Spring框架中的`WebApplicationInitializer`接口。 如果您还在为您的bean定义使用`@Configuration`类，那么最好是扩展`AbstractAnnotationConfigMessageDispatcherServletInitializer`，如下所示：

```java
public class MyServletInitializer
    extends AbstractAnnotationConfigMessageDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{MyRootConfig.class};
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{MyEndpointConfig.class};
    }

}
```

在上面的例子中，我们告诉Spring可以在`MyEndpointConfig`类（它是一个@Configuration类）中找到端点(endpoint)bean的定义。 其他的bean定义（通常是服务，存储库等）可以在`MyRootConfig`类中找到。 默认情况下，`AbstractAnnotationConfigMessageDispatcherServletInitializer`将servlet映射到两个模式：`/ services`和`* .wsdl`，尽管这可以通过覆盖`getServletMappings（）`方法来改变。 有关`MessageDispatcherServlet`的编程配置的更多详细信息，请参阅`AbstractMessageDispatcherServletInitializer`和`AbstractAnnotationConfigMessageDispatcherServletInitializer`的Javadoc。

<font color="red" >Automatic WSDL exposure</font>

`MessageDispatcherServlet`将自动检测在Spring容器中定义的任何`WsdlDefinition` bean。 所有检测到的`WsdlDefinition` bean也将通过`WsdlDefinitionHandlerAdapter`公开; 这是一个非常方便的方法，只需定义一些bean就可以将WSDL公开给客户端。

举个栗子，考虑以下在Spring-WS配置文件（/ WEB-INF / [servlet-name] -servlet.xml）中定义的`<static-wsdl>` bean。 注意'id'属性的值，因为这将在暴露WSDL时使用。

```xml
<sws:static-wsdl id="orders" location="orders.wsdl"/>
```

或者在@Configuration中有@Bean注解的方法:

```java
@Bean
public SimpleWsdl11Definition orders() {
	return new SimpleWsdl11Definition(new ClassPathResource("orders.wsdl"));
}
```

然后，可以通过`GET`请求访问在classpath的“orders.wsdl”文件中定义的WSDL，以便对以下表单的URL（根据需要替换主机，端口和servlet上下文路径）进行访问。

```url
http://localhost:8080/spring-ws/orders.wsdl
```

> 所有的`WsdlDefinition` bean定义都由`MessageDispatcherServlet`以它们的bean名称加上后缀`.wsdl`公开。 因此，如果bean名称是`echo`，主机名是“server”，并且Servlet上下文（war name）是“spring-ws”，则WSDL可以通过[http://server/spring-ws/echo.wsdl](http://server/spring-ws/echo.wsdl)获得。

`MessageDispatcherServlet`(或者更确切的说是`WsdlDefinitionHandlerAdapter`)的另一个很好的功能是它能够转换它公开的所有WSDL的"location"的值，以映射传入请求的URL。

请注意，这个“位置”转换功能默认是关闭的。要开启此功能，只需要为MessageDispatcherServlet指定一个初始化参数，如下所示：

```xml
<web-app>

  <servlet>
    <servlet-name>spring-ws</servlet-name>
    <servlet-class>org.springframework.ws.transport.http.MessageDispatcherServlet</servlet-class>
    <init-param>
      <param-name>transformWsdlLocations</param-name>
      <param-value>true</param-value>
    </init-param>
  </servlet>

  <servlet-mapping>
    <servlet-name>spring-ws</servlet-name>
    <url-pattern>/*</url-pattern>
  </servlet-mapping>

</web-app>
```

如果使用`AbstractAnnotationConfigMessageDispatcherServletInitializer`，则启用转换与重写`isTransformWsdlLocations（）`方法以返回true一样简单。

请参阅`WsdlDefinitionHandlerAdapter`的类级别Javadoc，以了解有关整个转换过程的更多信息。

作为手动编写WSDL，并且使用`<static-wsdl>`公开它的替代方法，Spring Web Services也可以从XSD模式生成一个WSDL。 这是 [Publishing the WSDL](https://docs.spring.io/spring-ws/docs/2.4.2.RELEASE/reference/#tutorial-publishing-wsdl)中显示的方法。 下面的应用程序上下文片段展示了如何创建这样一个动态的WSDL文件：

```xml
<sws:dynamic-wsdl id="orders"
    portTypeName="Orders"
    locationUri="http://localhost:8080/ordersService/">
  <sws:xsd location="Orders.xsd"/>
</sws:dynamic-wsdl>
```

或者,声明为 `@Bean` 的方法:

```java
@Bean
public DefaultWsdl11Definition orders() {
    DefaultWsdl11Definition definition = new DefaultWsdl11Definition();
    definition.setPortTypeName("Orders");
    definition.setLocationUri("http://localhost:8080/ordersService/");
    definition.setSchema(new SimpleXsdSchema(new ClassPathResource("echo.xsd")));

    return definition;
}
```

<dynamic-wsdl>元素依赖于`DefaultWsdl11Definition`类。 此定义类使用`org.springframework.ws.wsdl.wsdl11.provider`包中的WSDL providers ，并在第一次请求时使用`ProviderBasedWsdl4jDefinition`生成WSDL。 如果有必要扩展其实现机制，请参考这些类的Javadoc。

`DefaultWsdl11Definition`（`<dynamic-wsdl>`标记）按照习惯从XSD schema构建 WSDL。 它迭代schema中找到的所有`element`元素，并为所有`element`创建消息。然后，它为所有以定义的请求或响应后缀结束的消息创建WSDL `operation`。 默认的请求后缀是`Request`; 默认响应后缀是`Response`，这些也可以分别通过在<dynamic-wsdl />上设置`requestSuffix`和`responseSuffix`属性来更改。 它还可以基于操作构建`portType`, `binding`, and `service`。

例如，如果我们的`Orders.xsd` schema定义了`GetOrdersRequest`和`GetOrdersResponse`元素，那么`<dynamic-wsdl>`将创建一个`GetOrdersRequest`和`GetOrdersResponse`消息，以及一个`GetOrders`操作，该操作被放入`Orders`端口类型(port type)中。

如果你想使用多个schema，可以使用include或import，你需要把Commons XMLSchema放在classpath上。 如果Commons XMLSchema位于classpath上，那么之前定义的`<dynamic-wsdl>`元素将找到所有的XSD include和import，并将它们作为单个XSD内联在WSDL中。 这极大地简化了模式的部署，这仍然可以单独编辑它们。

> 尽管从XSD在运行时创建WSDL非常方便，但这种方法仍然存在一些缺点。 首先，尽管我们试图在版本之间保持WSDL生成过程的一致性，但仍然有可能发生变化（轻微）。 其次，其生成有点慢，尽管一旦生成，WSDL被缓存供以后参考。

因此，建议在项目开发阶段只使用`<dynamic-wsdl>`。 然后，我们建议使用浏览器下载生成的WSDL，将其存储在项目中，并使用`<static-wsdl>`公开它。 这是确保WSDL不会随着时间而改变的唯一方法。

###### 5.3.2 Wiring up Spring-WS in a `DispatcherServlet`

作为`MessageDispatcherServlet`的替代方案，您可以在标准的Spring-Web MVC `DispatcherServlet`中加载一个`MessageDispatcher`。 默认情况下，`DispatcherServlet`只能委托给`Controller`处理，但我们可以通过向Servlet的Web application context添加`WebServiceMessageReceiverHandlerAdapter`来告诉它委托给`MessageDispatcher`去处理：

```xml
<beans>

    <bean class="org.springframework.ws.transport.http.WebServiceMessageReceiverHandlerAdapter"/>

    <bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
        <property name="defaultHandler" ref="messageDispatcher"/>
    </bean

    <bean id="messageDispatcher" class="org.springframework.ws.soap.server.SoapMessageDispatcher"/>

    ...

    <bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter"/>

</beans>
```
请注意，通过显式添加`WebServiceMessageReceiverHandlerAdapter`，dispathcer servlet不会加载默认的adapters，并且无法处理标准的Spring-MVC `@Controllers`。 因此，我们在最后添加l了`RequestMappingHandlerAdapter`。

类似的方式，您也可以加载一个`WsdlDefinitionHandlerAdapter`以确保`DispatcherServlet`可以处理`WsdlDefinition`接口的实现：

```xml
<beans>

    <bean class="org.springframework.ws.transport.http.WebServiceMessageReceiverHandlerAdapter"/>

    <bean class="org.springframework.ws.transport.http.WsdlDefinitionHandlerAdapter"/>

    <bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
        <property name="mappings">
           <props>
             <prop key="*.wsdl">myServiceDefinition</prop>
           </props>
        </property>
        <property name="defaultHandler" ref="messageDispatcher"/>
    </bean>

    <bean id="messageDispatcher" class="org.springframework.ws.soap.server.SoapMessageDispatcher"/>

    <bean id="myServiceDefinition" class="org.springframework.ws.wsdl.wsdl11.SimpleWsdl11Definition">
       <prop name="wsdl" value="/WEB-INF/myServiceDefintion.wsdl"/>
    </bean>

    ...

</beans>
```

###### 5.3.3 JMS transport

Spring Web Services通过 Spring 框架提供的 JMS 功能支持服务器端的JMS处理。 Spring Web Services提供了`WebServiceMessageListener`作为插件插入`MessageListenerContainer`。 这个消息监听器需要一个`WebServiceMessageFactory` 和 `MessageDispatcher`来操作。 以下一段配置展示了如何具体操作：

```xml
<beans>

    <bean id="connectionFactory" class="org.apache.activemq.ActiveMQConnectionFactory">
        <property name="brokerURL" value="vm://localhost?broker.persistent=false"/>
    </bean>

    <bean id="messageFactory" class="org.springframework.ws.soap.saaj.SaajSoapMessageFactory"/>

    <bean class="org.springframework.jms.listener.DefaultMessageListenerContainer">
        <property name="connectionFactory" ref="connectionFactory"/>
        <property name="destinationName" value="RequestQueue"/>
        <property name="messageListener">
            <bean class="org.springframework.ws.transport.jms.WebServiceMessageListener">
                <property name="messageFactory" ref="messageFactory"/>
                <property name="messageReceiver" ref="messageDispatcher"/>
            </bean>
        </property>
    </bean>

    <bean id="messageDispatcher" class="org.springframework.ws.soap.server.SoapMessageDispatcher">
        <property name="endpointMappings">
            <bean
              class="org.springframework.ws.server.endpoint.mapping.PayloadRootAnnotationMethodEndpointMapping">
                <property name="defaultEndpoint">
                    <bean class="com.example.MyEndpoint"/>
                </property>
            </bean>
        </property>
    </bean>
</beans>
```

###### 5.3.4 Email transport

除了HTTP和JMS之外，Spring Web Services还提供了服务器端的电子邮件处理功能。 这个功能是通过`MailMessageReceiver`类提供的。 该类监视POP3或IMAP文件夹，将电子邮件转换为`WebServiceMessage`，使用SMTP发送任何响应。 主机名称可以通过`storeUri`进行配置，它指示要监听请求代表的的邮件文件夹（典型的是POP3和IMAP文件夹）；而指示发送请求的服务器可以使用`transportUri`进行配置。

`MailMessageReceiver`监视传入消息的方式如何使用插件式的策略进行配置呢？其使用的是`MonitoringStrategy`，默认情况下使用轮询策略(polling stategy)，即每5分钟轮询传入文件夹中的新邮件。 这个5分钟的间隔可以通过在Strategy上设置`pollingInterval`属性来更改。 默认情况下，所有的`MonitoringStrategy`的实现都会删除处理过的消息; 这可以通过设置`deleteMessages`属性来改变。

轮询是一种非常低效的策略，作为轮询方法的替代方案，有一种使用IMAP **IDLE**的监控策略。 **IDLE**命令是IMAP电子邮件协议的可选扩展，它允许邮件服务器异步发送新的邮件更新到`MailMessageReceiver`。 如果使用支持IDLE命令的IMAP服务器，则可以将`ImapIdleMonitoringStrategy`设置到`monitoringStrategy`属性中。 除了支持服务器之外，还需要使用JavaMail 1.4.1或更高版本才能使用。

以下一段配置显示了如何使用服务器端电子邮件设置，将默认轮询间隔覆盖为每30秒（30000毫秒）检查一次的值：

```xml
<beans>

    <bean id="messageFactory" class="org.springframework.ws.soap.saaj.SaajSoapMessageFactory"/>

    <bean id="messagingReceiver" class="org.springframework.ws.transport.mail.MailMessageReceiver">
        <property name="messageFactory" ref="messageFactory"/>
        <property name="from" value="Spring-WS SOAP Server &lt;server@example.com&gt;"/>
        <property name="storeUri" value="imap://server:s04p@imap.example.com/INBOX"/>
        <property name="transportUri" value="smtp://smtp.example.com"/>
        <property name="messageReceiver" ref="messageDispatcher"/>
        <property name="monitoringStrategy">
            <bean class="org.springframework.ws.transport.mail.monitor.PollingMonitoringStrategy">
                <property name="pollingInterval" value="30000"/>
            </bean>
        </property>
    </bean>

    <bean id="messageDispatcher" class="org.springframework.ws.soap.server.SoapMessageDispatcher">
        <property name="endpointMappings">
            <bean
              class="org.springframework.ws.server.endpoint.mapping.PayloadRootAnnotationMethodEndpointMapping">
                <property name="defaultEndpoint">
                    <bean class="com.example.MyEndpoint"/>
                </property>
            </bean>
        </property>
    </bean>
</beans>
```

###### 5.3.5 Embedded HTTP Server transport

Spring Web Services提供基于Sun JRE 1.6 [HTTP server](http://java.sun.com/javase/6/docs/jre/api/net/httpserver/spec/index.html)的传输服务。 嵌入式HTTP服务器是一个易于配置的独立服务器。 它适用于传统servlet容器的更轻的替代方案。

使用嵌入式HTTP服务器时，不需要外部部署描述文件（`web.xml`）。 您只需要定义一个服务器的实例并将其配置为处理传入的请求。 Core Spring Framework中的远程处理模块包含一个用于HTTP服务器的方便的factory bean ：`SimpleHttpServerFactoryBean`。 最重要的属性是contexts，它将context路径映射到相应的“ HttpHandler ”之上。

Spring Web Services提供了2个`HttpHandler`的实现接口：`WsdlDefinitionHttpHandler`和`WebServiceMessageReceiverHttpHandler`。 前者将传入的GET请求映射到`WsdlDefinition`。 后者负责处理对Web服务消息的POST请求，因此需要`WebServiceMessageFactory`（通常是`SaajSoapMessageFactory`）和`WebServiceMessageReceiver`（通常是`SoapMessageDispatcher`）来完成其任务。

为了和servlet尽量相似，`contexts` property在`web.xml`中起着servlet mapping的作用，而`WebServiceMessageReceiverHttpHandler`则相当于`MessageDispatcherServlet`。

以下片段显示了HTTP服务器传输服务的简单配置示例：

```xml
<beans>

    <bean id="messageFactory" class="org.springframework.ws.soap.saaj.SaajSoapMessageFactory"/>

    <bean id="messageReceiver" class="org.springframework.ws.soap.server.SoapMessageDispatcher">
        <property name="endpointMappings" ref="endpointMapping"/>
    </bean>

    <bean id="endpointMapping" class="org.springframework.ws.server.endpoint.mapping.PayloadRootAnnotationMethodEndpointMapping">
        <property name="defaultEndpoint" ref="stockEndpoint"/>
    </bean>

    <bean id="httpServer" class="org.springframework.remoting.support.SimpleHttpServerFactoryBean">
        <property name="contexts">
            <map>
                <entry key="/StockService.wsdl" value-ref="wsdlHandler"/>
                <entry key="/StockService" value-ref="soapHandler"/>
            </map>
        </property>
    </bean>

    <bean id="soapHandler" class="org.springframework.ws.transport.http.WebServiceMessageReceiverHttpHandler">
        <property name="messageFactory" ref="messageFactory"/>
        <property name="messageReceiver" ref="messageReceiver"/>
    </bean>

    <bean id="wsdlHandler" class="org.springframework.ws.transport.http.WsdlDefinitionHttpHandler">
        <property name="definition" ref="wsdlDefinition"/>
    </bean>
</beans>
```

###### 5.3.6 XMPP transport

Spring Web Services 2.0 引入了对XMPP的支持, 也称之为 Jabber.这种支持基于 [Smack](https://www.igniterealtime.org/projects/smack/index.jsp) 库.

Spring Web Services对XMPP的支持与其他传输服务非常相似：WebServiceTemplate对应XmppMessageSender，MessageDispatcher对应XmppMessageReceiver。

以下示例显示如何设置服务器端XMPP组件：

```xml
<beans>

    <bean id="messageFactory" class="org.springframework.ws.soap.saaj.SaajSoapMessageFactory"/>

    <bean id="connection" class="org.springframework.ws.transport.xmpp.support.XmppConnectionFactoryBean">
        <property name="host" value="jabber.org"/>
        <property name="username" value="username"/>
        <property name="password" value="password"/>
    </bean>

    <bean id="messagingReceiver" class="org.springframework.ws.transport.xmpp.XmppMessageReceiver">
        <property name="messageFactory" ref="messageFactory"/>
        <property name="connection" ref="connection"/>
        <property name="messageReceiver" ref="messageDispatcher"/>
    </bean>

    <bean id="messageDispatcher" class="org.springframework.ws.soap.server.SoapMessageDispatcher">
        <property name="endpointMappings">
            <bean
              class="org.springframework.ws.server.endpoint.mapping.PayloadRootAnnotationMethodEndpointMapping">
                <property name="defaultEndpoint">
                    <bean class="com.example.MyEndpoint"/>
                </property>
            </bean>
        </property>
    </bean>

</beans>
```

##### 5.3.7. MTOM

MTOM是向Web服务发送二进制数据的机制。 你可以看看[MTOM示例](https://github.com/spring-projects/spring-ws-samples/tree/master/mtom) 来尝试实现Spring WS。

#### 5.4. Endpoints

Endpoints是Spring-WS服务器端支持的核心概念。 Endpoints 提供了应用程序行为的访问方法，一般来说访问方法通常由业务服务接口定义。 endpoint 解释XML请求消息并使用该输入的信息来调用业务服务上的方法。 该服务调用的结果被表示为响应消息。 Spring-WS有各种各样的endpoint，这些endpoint使用各种各样的方式处理XML 信息来创建响应消息。

您可以通过@Endpoint来注释一个类，表明他是一个endpoint。 在此类中，通过使用各种各样的参数类型（如DOM元素，JAXB2对象等）来定义处理传入XML请求的一个或多个方法。 您可以通过使用另一个注释（通常是@PayloadRoot）来指示某种方法可以处理的消息类型。

我们看一下下面的一个示例endpoint：

```java
package samples;

import org.w3c.dom.Element;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.ws.server.endpoint.annotation.Endpoint;
import org.springframework.ws.server.endpoint.annotation.PayloadRoot;
import org.springframework.ws.soap.SoapHeader;

@Endpoint
public class AnnotationOrderEndpoint {

  private final OrderService orderService;

  @Autowired                                         
  public AnnotationOrderEndpoint(OrderService orderService) {
      this.orderService = orderService;
  }

  @PayloadRoot(localPart = "order", namespace = "http://samples")
  public void order(@RequestPayload Element orderElement) {                                    
    Order order = createOrder(orderElement);
    orderService.createOrder(order);
  }

  @PayloadRoot(localPart = "orderRequest", namespace = "http://samples")                       
  @ResponsePayload
  public Order getOrder(@RequestPayload OrderRequest orderRequest, SoapHeader header) {        
    checkSoapHeaderForSomething(header);
    return orderService.getOrder(orderRequest.getId());
  }

  ...

}
```

1. 该类用@Endpoint注释标记为Spring-WS端点。

2. 构造函数被 `@Autowired` 标注, 这样 `OrderService` 业务service就被注入到了个这个endpoint中.

3. Order方法将Element作为参数，并使用`@RequestPayload`对其进行注释。 这意味着消息的负载将作为一个DOM元素传递到这个方法上。 该方法返回类型为void，表示没有响应消息返回。 有关endpoint的方法的更多信息，请参阅`@Endpoint`[处理方法](https://docs.spring.io/spring-ws/docs/2.4.2.RELEASE/reference/#server-atEndpoint-methods)。

4. `getOrder`方法接受一个用`@RequestPayload`注解的`OrderRequest` 类型作为参数。 该参数是JAXB2支持的对象（因为使用了`@XmlRootElement`注解）。 这意味着消息的负载作为解组对象( unmarshalled object)）传递给了此方法。 这里还有一个参数是`SoapHeader`类型。 在调用时，该参数将包含请求消息的SOAP头。 本方法使用`@ResponsePayload` 进行了标注，表示返回值（Order类型）被用作响应消息的载荷。 有关端点方法的更多信息，请参阅`@Endpoint`[处理方法](https://docs.spring.io/spring-ws/docs/2.4.2.RELEASE/reference/#server-atEndpoint-methods)。

5. endpoints的两个处理方法用`@PayloadRoot`进行了标记，表明该方法分别可以处理什么类型的请求消息：具有`orderRequest`  local name和http://samples 名称空间URI的请求，将调用`getOrder`方法; 具有`order` local name的请求的order方法。 有关`@PayloadRoot`的更多信息，请参阅[Endpoint映射](https://docs.spring.io/spring-ws/docs/2.4.2.RELEASE/reference/#server-endpoint-mapping)。

   ​

为了支持`@Endpoint`和相关的Spring-WS注解，您需要将以下内容添加到Spring应用程序上下文中：

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:sws="http://www.springframework.org/schema/web-services"
  xsi:schemaLocation="http://www.springframework.org/schema/beans
      http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/web-services
      http://www.springframework.org/schema/web-services/web-services.xsd">

  <sws:annotation-driven />

</beans>
```

或者, 假如你使用 `@Configuration` 而不是Spring的XML配置, you can annotate your configuration class with `@EnableWs`, like so:

```java
@EnableWs//this line is the key.
@Configuration
public class EchoConfig {

    // @Bean definitions go here

}
```

要自定义`@EnableWs`配置，您可以重写实现`WsConfigurer`接口，而扩展`WsConfigurerAdapter`类将会是一个更好的选择。 例如：

```java
@Configuration
@EnableWs
@ComponentScan(basePackageClasses = { MyConfiguration.class })
public class MyConfiguration extends WsConfigurerAdapter {

  @Override
  public void addInterceptors(List<EndpointInterceptor> interceptors) {
    interceptors.add(new MyInterceptor());
  }

  @Override
  public void addArgumentResolvers(List<MethodArgumentResolver> argumentResolvers) {
    argumentResolvers.add(new MyArgumentResolver());
  }

  // More overridden methods ...
}
```

在接下来的几节中，给出了`@Endpoint`编程模型的更详细的描述。

> 与任何其他Spring Bean一样，Endpoints的默认作用域是单例(single)模式的，即每个容器创建一个bean定义的实例。 单例模式意味着多个线程可以同时使用它，所以endpoint必须是线程安全的。 如果你想使用一个不同的作用域，比如prototype，请参考[Spring Reference文档](http://static.springframework.org/spring/docs/2.5.x/reference/beans.html#beans-factory-scopes)。

请注意，Spring-WS中提供的所有抽象基类都是线程安全的，如果不是在类级别的Javadoc中将另有说明。

###### 5.4.1. `@Endpoint` handling methods

为了使endpoint确实处理到传入的XML消息，它需要有一个或多个处理方法。 处理方法可以使用多种多样的参数和返回类型，但通常它们有一个参数将包含消息载荷(message payload)，并且返回响应消息的载荷（如果有的话）。 在本节中您将了解哪些参数和返回类型是受支持的。

为了申明方法可以处理的消息类型，通常使用@PayloadRoot或@SoapAction注释来标注方法。 您将在端点[endpoint mappings](https://docs.spring.io/spring-ws/docs/2.4.2.RELEASE/reference/#server-endpoint-mapping)中了解有关这些注释的更多信息。

下面是一个处理方法的示例：

```java
@PayloadRoot(localPart = "order", namespace = "http://samples")
public void order(@RequestPayload Element orderElement) {
  Order order = createOrder(orderElement);
  orderService.createOrder(order);
}
```

`Order`方法将`Element`类型作为参数，使用`@RequestPayload`进行注释。 这意味着消息的有效载荷作为一个DOM元素在这个方法上传递。 该方法有一个`void`返回类型，表示没有响应消息发送。

<font color='red'>Handling method parameters</font>

处理方法通常具有一个或多个参数表示XML消息的各个部分。 通常，处理方法将有一个映射到负载的参数，当然，也可以映射到请求消息的其他部分，如SOAP头。 本部分将描述您可以在处理方法签名中使用的参数。

要将参数映射到请求消息的负载，您需要使用@RequestPayload注释来注释此参数。 这个注解告诉Spring-WS需要被绑定到请求载荷中的参数。