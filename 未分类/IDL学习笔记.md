# IDL学习笔记

[TOC]

&emsp;&emsp;**IDL(Interface Definition Language)——接口描述语言**，是thrift用来定义通用接口的语言，定义好接口后，利用不同语言的工具转换为各自语言的定义，达到跨语言、平台的功能

&emsp;&emsp;IDL只用来定义接口，所以语法只与各种类型、错误相关，不含流程控制等内容

&emsp;&emsp;IDL的文法类似C语言

&emsp;&emsp;最新版本语法在http://thrift.apache.org/docs/idl

### 基本类型

```
BaseType	::=  'bool' | 'byte' | 'i8' | 'i16' | 'i32' | 'i64' | 'double' | 'string' | 'binary' | 'slist'
```

### 容器类型

&emsp;&emsp;IDL支持三种容器、对应线性表、哈希表、集合

&emsp;&emsp;集合内的元素可以是自定义类型、基本类型和容器类型

```
FieldType	::=  Identifier | BaseType | ContainerType


ContainerType		::=  MapType | SetType | ListType

MapType		::=  'map' CppType? '<' FieldType ',' FieldType '>'

SetType		::=  'set' CppType? '<' FieldType '>'

ListType	::=  'list' '<' FieldType '>' CppType?

CppType  	::=  'cpp_type' Literal
```

### 自定义类型

&emsp;&emsp;包括别名、枚举、结构体、联合、异常、服务（实际上就是接口的接口，因为idl本身是描述接口的）

```
Typedef			::=  'typedef' DefinitionType Identifier

Enum				::=  'enum' Identifier '{' (Identifier ('=' IntConstant)? ListSeparator?)* '}'

Struct			::=  'struct' Identifier '{' Field* '}'

Union				::=  'union' Identifier '{' Field* '}'

Exception		::=  'exception' Identifier '{' Field* '}'

Service    	::=  'service' Identifier ( 'extends' Identifier )? '{' Function* '}'
```

### 常量

```
Const						::=  'const' FieldType Identifier '=' ConstValue ListSeparator?

ConstValue   	  ::=  IntConstant | DoubleConstant | Literal | Identifier | ConstList | ConstMap

IntConstant     ::=  ('+' | '-')? Digit+

DoubleConstant  ::=  ('+' | '-')? Digit* ('.' Digit+)? ( ('E' | 'e') IntConstant )?

ConstList       ::=  '[' (ConstValue ListSeparator?)* ']'

ConstMap				::=  '{' (ConstValue ':' ConstValue ListSeparator?)* '}'
```

### 字段

&emsp;&emsp;字段包括字段ID、字段要求、字段类型、标识符、值、分隔符

```
Field			::=  FieldID? FieldReq? FieldType Identifier ('=' ConstValue)? ListSeparator?

FieldID		::=  IntConstant ':'
```

### 函数

```
Function        ::=  'oneway'? FunctionType Identifier '(' Field* ')' Throws? ListSeparator?

FunctionType    ::=  FieldType | 'void'

Throws          ::=  'throws' '(' Field* ')'
```

### include和命名空间

&emsp;&emsp;include运行加入别的文件中的定义，命名空间声明了这是哪个命名空间/模块/程序包等，范围指示名称空间适用的语言

```
Include		::=  'include' Literal

Literal		::=  ('"' [^"]* '"') | ("'" [^']* "'")

Namespace       ::=  ( 'namespace' ( NamespaceScope Identifier ) )

NamespaceScope  ::=  '*' | 'c_glib' | 'cpp' | 'csharp' | 'delphi' | 'go' | 'java' | 'js' | 'lua' | 'netcore' | 'perl' | 'php' | 'py' | 'py.twisted' | 'rb' | 'st' | 'xsd'
```

### 举例

```idl
namespace go example

struct req {
    1: required string a,
    2: optional i64 b,
}
struct resp {
    1: required string a,
  	2: required double b,
}
service ExampleService {
    resp ExampleMethod(1: req r, 2: bool b),
}
```