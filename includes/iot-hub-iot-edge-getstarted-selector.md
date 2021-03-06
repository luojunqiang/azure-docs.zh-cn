> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-get-started.md)
> 
> 

本文详细演练 [Hello World 示例代码][lnk-helloworld-sample]，演示 [Azure IoT Edge][lnk-iot-edge] 体系结构的基本组件。 该示例使用 Azure IoT Edge 生成一个简单的网关，每隔 5 秒将“hello world”消息记录到文件中。

本文介绍的内容包括：

* **概念**：通过概念来大致了解相关组件，这些组件组成用户使用 IoT Edge 创建的网关。
* **Hello World 示例体系结构**：说明如何将概念应用到 Hello World 示例，以及如何将这些组件组合到一起。
* **如何生成示例**：生成示例所需的步骤。
* **如何运行示例**：运行示例所需的步骤。 
* **典型输出**：运行示例时预期获得的输出的示例。
* **代码段**：代码段的集合，显示 Hello World 示例如何实现重要的 IoT Edge 网关组件。

## <a name="azure-iot-edge-concepts"></a>Azure IoT Edge 概念
在查看示例代码或使用 IoT Edge 创建自己的现场网关之前，应该了解支撑 IoT Edge 体系结构的重要概念。

### <a name="iot-edge-modules"></a>IoT Edge 模块
使用 Azure IoT Edge 生成网关时，需创建 *IoT Edge 块*并对其进行组合。 模块通过 *消息* 来相互交换数据。 一个模块收到一条消息，对其执行某些操作，选择性地将其转换为新的消息，然后将其发布，供其他模块处理。 某些模块可能只生成新消息，不处理传入消息。 一系列的模块会生成一个数据处理管道，每个模块在该管道的某个点对数据进行转换。

![使用 Azure IoT Edge 构建的网关中的模块链][1]

IoT Edge 包含：

* 预先编写的模块，执行常见的网关功能。
* 供开发人员编写自定义模块的接口。
* 部署和运行一组模块所需的基础结构。

该 SDK 提供一个抽象层，你可以利用该抽象层生成在各种操作系统和平台上运行的网关。

![Azure IoT Edge 抽象层][2]

### <a name="messages"></a>消息
虽然可以通过思考模块互相传递消息的方式来了解网关运行过程中的概念，但这种思考过程并不能准确地反映实际发生的情况。 IoT Edge 模块使用中转站来互相通信，它们将消息发布到中转站（总线、pubsub 或任何其他消息传送模式），然后让中转站将消息路由到与其连接的模块。

模块使用 **Broker_Publish** 函数将消息发布到中转站。 中转站通过调用回调函数将消息传递给模块。 一条消息由一组键/值属性以及作为内存块传递的内容组成。

![Azure IoT Edge 中的中转站角色][3]

### <a name="message-routing-and-filtering"></a>消息路由和筛选
有两种方法可以将消息定向到正确的 IoT Edge 模块。 可以将一组链接传递到中转站，这样一来，中转站就能够知道每个模块的源和接收器；也可以让模块根据消息的属性进行筛选。 模块仅应在消息是其适用对象的情况下对消息执行操作。 链接和消息筛选可以有效创建消息管道。

## <a name="hello-world-sample-architecture"></a>Hello World 示例体系结构
Hello World 示例体现了上一部分所述概念。 Hello World 示例所实现的 IoT Edge 网关具有一个管道，该管道包含两个 IoT Edge 模块：

* *hello world* 模块每 5 秒创建一条消息，并将该消息传递给记录器模块。
* *记录器* 模块将接收的消息写入文件。

![使用 Azure IoT Edge 构建的 Hello World 示例的体系结构][4]

如前一部分所述，Hello World 模块并不是每隔 5 秒就直接将消息传递给记录器模块， 而是每隔 5 秒将消息发布到中转站。

Logger 模块从中转站接收消息并对消息执行操作，将消息内容写入文件。

Logger 模块只使用来自中转站的消息，而不会将新消息发布到中转站。

![中转站如何在 Azure IoT Edge 中的模块之间路由消息][5]

上图显示了 Hello World 示例的体系结构，同时显示了在[存储库][lnk-iot-edge]中对示例不同部分进行实现的源文件的相对路径。 请自行浏览代码，或使用下面的代码段作为指导。

<!-- Images -->
[1]: media/iot-hub-iot-edge-getstarted-selector/modules.png
[2]: media/iot-hub-iot-edge-getstarted-selector/modules_2.png
[3]: media/iot-hub-iot-edge-getstarted-selector/messages_1.png
[4]: media/iot-hub-iot-edge-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-iot-edge-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/iot-edge/tree/master/samples/hello_world
[lnk-iot-edge]: https://github.com/Azure/iot-edge