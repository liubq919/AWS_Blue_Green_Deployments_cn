## 介绍

在传统的应用部署方法中，通常通过重新部署较旧的，稳定版本的应用来修复失败的部署。由于配置额外资源的成本和工作量，传统数据中心的重新部署通常在同一组资源上完成。虽然这种方法有效，但它有许多缺点。回滚并不容易，因为它是通过从头开始重新部署旧版本来实现的。此过程需要时间，使应用程序可能长时间不可用。甚至应用只有受损的情况下，覆盖错误版本的回滚才被需要。结果，您没有机会调试错误的应用程序。

践行AWS平台的敏捷性，可伸缩性，公共设施消耗和自动化功能的原则可以改变应用程序部署的范例。这样可以实现更好的部署方法，即蓝/绿部署。

### 蓝绿部署方法论

蓝/绿部署提供接近零停机时间的发布和回滚功能。蓝/绿部署背后的基本思想是在运行不同版本应用的两个相同环境之间转移流量。蓝色环境代表服务于生产流量的当前应用程序版本。与此同时，绿色环境是即将服务的环境，运行另一个版本的应用。绿色环境准备就绪并经过测试后，生产流量将从蓝色重定向到绿色。如果发现任何问题，您可以通过将流量恢复到蓝色环境以实现回滚。

![1](Figure1.jpg)
图1: 基本的蓝色/绿色示例

虽然蓝/绿部署不是一个新概念，由于配置其他资源所需的成本和工作量，您通常不会在传统的本地托管环境中看到它。云计算的出现极大地改变了采用蓝/绿方法部署软件的简单性和成本效益。

### 蓝/绿的优点
传统上，通过就地升级，很难在生产部署中验证新的应用版本的同时还能继续运行旧版本的应用。蓝/绿部署在您的蓝和绿应用环境之间提供了一定程度的隔离。它保证创建的平行绿色环境不会影响支撑蓝色环境的资源。这种隔离降低了部署的风险。

在部署完绿色环境之后，你有机会对其进行验证。在将生产流量发送到绿色环境之前，可以通过测试流量或者使用一小部分的生产流量来验证绿色环境，以更好的反映实际用户的流量。这种方法成为金丝雀分析或者金丝雀测试。即使你发现绿色环境没有达到预期，这一切也不会对蓝色环境有什么影响。你可以将流量重新返回蓝色环境，最小化受损操作或停机时间，限制影响的冲击半径。

这种简单地将流量回滚到仍然运行的蓝色环境的能力是蓝/绿部署的主要优势。您可以在部署过程中随时回滚到蓝色环境。由于影响仅限于绿色环境问题检测和流量转移回蓝色环境之间的时间窗口，因此受损操作或停机时间能做到最小化。此外，影响仅限于流向绿色环境的流量，而不是所有流量。如果部署错误的冲击半径降低了，则整体部署风险也会降低。

蓝/绿部署也适用于持续集成和持续部署（CI / CD）工作流，在许多情况下限制了它们的复杂性。您的部署自动化必须考虑对现有环境，状态或配置的较少依赖性。您的新绿色环境将部署到一组全新的资源上。

在AWS中，蓝色/绿色部署还提供成本优化优势。你不需要依赖于相同的底层资源。因此，如果应用程序的性能范围从一个版本更改为另一个版本，则只需启动有优化资源的新环境，无论是更少的资源还是仅仅是不同的计算资源。您也不必长时间运行过度配置的体系结构。在部署过程中，随着更多流量被发送到绿色环境，您可以扩展绿色环境，同时，蓝色环境会收到较少的流量，你可以缩减蓝色环境。一旦部署陈工，你可以退役蓝色环境，不再需要为蓝色环境的资源付费。

### 定义环境边界
在规划蓝/绿部署时，必须考虑环境边界 - 哪里有变更，哪些需要部署以实现这些变更。环境边界受许多因素的影响，如下表所述。
|    因素         | 准则 |
| ----------- | ----------- |
| 应用架构    | 依赖性，松/紧耦合   |
| 组织结构    | 迭代的速度和次数 |
| 风险和复杂性 | 冲击半径和部署失败的影响   |
| 人员       | 团队的专长 |
| 流程       | 测试/QA，回滚能力   |
| 成本      | 运营预算，额外资源 |

表格1: 影响环境边界的因素

例如，运行基于微服务架构模式的应用程序的组织可能具有较小的环境边界，因为各个服务之间存在松散耦合和明确定义的接口。运行传统的巨石应用的组织仍然可以利用蓝/绿部署，但环境范围可能更宽泛，测试更广泛。无论环境边界如何，都应该尽可能利用自动化来简化流程，减少人为错误并控制成本。

## AWS中保障蓝/绿部署的工具与服务
AWS提供了许多工具和服务，可帮助你通过AWS API自动化和简化部署和基础架构，你可以使用Web控制台，CLI工具，SDK和IDE来利用这些API。由于AWS生态系统中提供了许多服务，因此以下内容并非完整列表。相反，此列表仅提供我们在本文中将讨论的服务的概览。你可以在AWS之外找到软件解决方案来帮助自动化和监控基础架构和部署，但本文主要关注AWS服务。

### Amazon Route 53
Amazon Route 53是一种高可用且可扩展的权威DNS服务，可将用户对基于Internet资源的请求路由到适当的目标。Amazon Route 53运行在一个由DNS服务器组成的全球网络上，为客户提供了附加的功能，比如基于健康检查、地理位置和延迟的路由。DNS是蓝/绿部署的经典方法，允许管理员通过简单地更新托管区域中的DNS记录来引导流量。此外，存活时间（TTL）可以调整资源记录; 这对于有效的DNS模式很重要，因为较短的TTL允许更改的记录更快地传播到客户端。

### Elastic Load Balancing
为蓝/绿部署路由流量的另一种常见方法是使用负载均衡技术。Elastic Load Balancing在指定的Amazon Elastic Compute Cloud（Amazon EC2）实例之间分配进入的应用流量。Elastic Load Balancing基于传入的请求进行伸缩，针对Amazon EC2资源执行健康检查，并自然地与其他AWS工具集成，例如Auto Scaling。对于希望提高应用容错性的客户来说，这是一个很好的选择。

### Auto Scaling
Auto Scaling有助于维护应用程序可用性，并允许客户根据定义的条件自动伸缩EC2容量。在Auto Scaling组中用于启动EC2实例的模板被称为*启动配置*。可以将不同版本的启动配置附加到Auto Scaling组以启用蓝/绿部署。还可以配置Auto Scaling以与Elastic Load Balancing负载均衡器配合使用。在此配置中，Elastic Load Balancing会平衡Auto Scaling组中运行的EC2实例的流量。可以在Auto Scaling组中定义终止策略，以确定在伸缩操作期间要删除的EC2实例。如“Auto Scaling开发人员指南”中所述，Auto Scaling还允许将实例置于待机状态，而不是终止，这有助于在需要时快速回滚。Auto Scaling的终止策略和待机状态都支持蓝/绿部署。

### AWS Elastic Beanstalk
AWS Elastic Beanstalk是一种在AWS上启动和运行应用程序的快速而简单的方法。对于希望部署代码而不必担心管理底层基础设施的开发人员来说，它是完美的。Elastic Beanstalk支持Auto Scaling和Elastic Load Balancing，两者都支持蓝/绿部署。Elastic Beanstalk可以轻松运行多个版本的应用，并提供交换环境URL的功能，从而促进蓝/绿部署。

### AWS OpsWorks
AWS OpsWorks是一种基于Chef的配置管理服务，允许用户在AWS上部署和管理应用堆。用户可以指定资源和应用程序配置，以及部署和监视运行资源。在准备蓝/绿环境时，OpsWorks简化了克隆整个堆栈的过程。

### AWS CloudFormation
AWS CloudFormation使客户能够通过JSON格式的模板描述他们所需的AWS资源。此服务提供非常强大的自动化功能，用于配置蓝/绿环境并促进交换流量的更新，无论是通过Route 53 DNS，还是Elastic Load Balancing等。服务可以作为更庞大的基础架构中的一部分作为代码策略使用，该基础架构使用代码和软件开发技术(如版本控制和持续集成)，类似于处理应用代码的方式以分配和管理。

### Amazon CloudWatch
Amazon CloudWatch是AWS云资源和运行在AWS上的应用的监控服务。CloudWatch可以收集和跟踪指标，收集和监控日志文件以及设置警报。它提供了系统范围内对资源利用率，应用程序性能和运行状况的可视性，这对于蓝/绿部署中的应用运行状况的早期检测至关重要。

## 方法

以下方法是如何在AWS上实现蓝/绿的示例。虽然我们强调每种方法中的特定服务，但你可能还有其他服务或工具来实现相同的模式。根据现有架构，应用程序的性质以及组织中软件部署的目标，选择合适的模式。尽可能多地进行尝试，为你的环境获得更多的经验，并了解不同的部署风险因素如何与特定工作负载相互作用。

### 使用Amazon Route 53更新DNS路由
通过记录更新进行DNS路由是蓝/绿部署的常用方法。DNS是将流量从蓝色环境切换到绿色环境的机制，如果需要回滚，则反之亦然。此方法适用于各种环境配置，只要你可以将endpoint作为DNS名称或IP地址表示到环境中。

在AWS中，此方法适用于以下环境：
- 单实例，具有公共或弹性IP地址
- Elastic Load Balancing负载均衡器或第三方负载均衡器下游的实例组
- Auto Scaling组中的实例，以Elastic Load Balancing负载均衡器作为前端
- 在Elastic Load Balancing负载均衡器下游的Amazon EC2容器服务（Amazon ECS）群集上运行的服务
- Elastic Beanstalk环境Web层
- 暴露IP或DNS端点的其他配置
![2](Figure2.jpg)
图2 经典DNS模式
图2显示了Amazon Route 53如何管理DNS托管域。通过更新别名记录，可以将流量从蓝色环境路由到绿色环境。

您可以一次性转移所有流量，也可以进行加权分配。使用Amazon Route 53，可以定义进入绿色环境的流量百分比以并逐渐更新权重，直到绿色环境承载完整的生产流量。加权分布通过将一小部分生产流量引入新环境以提供金丝雀分析的能力。可以测试新代码并监控错误，如果遇到任何问题，则限制冲击半径。如果使用了Elastic Load Balancing，还允许绿色环境向外扩展以支持完整的生产负载，比如，Elastic Load Balancing会自动调整其请求处理容量，以满足入站应用程序流量；伸缩过程不是瞬间的，因此我们建议你测试，观察并了解你的流量模式。负载均衡器也可以通过请求支持以预先加热（配置为最佳容量）。

![3](Figure3.jpg)
图3 经典的DNS权重分配

如果在部署期间出现问题，可以通过更新DNS记录来实现回滚，以将流量转回到蓝色环境。虽然DNS路由很容易实现蓝/绿，但问题是可以多快完成回滚。DNS TTL确定客户端缓存查询结果的时间。但是，对于更陈旧的和潜在行为不规范的客户端，某些会话仍可能与之前的环境相关联。尽管回滚可能具有挑战性，但这种模式的确有利于按照自己的进度进行颗粒度转换，以便对伸缩活动进行更实质的测试。为帮助管理成本，请考虑使用Auto Scaling为EC2实例根据实际需求扩展资源。这适用于使用Amazon Route 53加权分配的逐步转换。要进行完全转换，请务必调整Auto Scaling策略以按预期进行扩展，并记住新的Elastic Load Balancing endpoint可能也需要时间进行扩展。

### 在弹性负载均衡器下游交换Auto Scaling组
如果DNS复杂性过高，请考虑将负载均衡用于蓝/绿环境的流量管理。此方法使用Auto Scaling管理蓝/绿环境的EC2资源，根据实际需求进行扩展或缩小。你还可以通过更新特定组的最大所需实例的数量来控制Auto Scaling组大小。

Auto Scaling还与Elastic Load Balancing集成，因此如果任何新实例通过负载均衡器管理的健康检查，则会自动添加到负载均衡池。Elastic Load Balancing使用简单的ping或更复杂的连接尝试或请求来测试已注册的EC2实例的运行状况。健康检查以可配置的时间间隔进行，并具有定义的阈值来确定实例是健康的还是不健康。例如，可以使用Elastic Load Balancing运行状况检查策略，每隔20秒ping一次80端口，并在通过10次成功ping的阈值后，将实例报告为InService。如果足够多的ping请求超时，则报告该实例为OutofService。与Auto Scaling配合使用，如果Auto Scaling策略发出指示，则可以替换OutofService实例。相反，对于收缩活动，负载均衡器会从池中删除EC2实例，并在终止之前消耗完当前连接。