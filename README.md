# VertxApp

    VertxApp是Vertx的Kotlin包装，使用简单、扩展方便。
    这个框架是用于快速开发Web App，基于Kotlin编写，基于异步的Vertx，建立了一种应用开发模式，已将很多Vertx的细节隐藏起来，让使用者可以专注于自己的实际内容开发，而不用学习冗长的基本知识。是一个高度定制化的、适合思路清晰的开发者的一个框架。特点总结如下：
1.生成的最终文件是jar，包含客户应用与web server，java -xxx.jar 即可以全套运行，管理方便
2.使用Gradle作为包管理，使用时可以只导入一个jar包就行
3.99%的http请求统一封装为post方式，使用输入和输出都是json，即让客户开发集中于“处理一个输入的json，并输出一个处理过的json”
4.内部模块间数据交换和配置全部使用json
5.全异步处理
6.提供了关于json、validator、sql，redis，微信message等二次包装后的大量工具类

对使用者的要求：
1.理解异步概念
2.理解sql语句
3.理解json
