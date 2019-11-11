---
title: 服务网格通信基础结构
description: 了解服务网格技术如何简化云本地微服务通信
author: robvet
ms.date: 09/10/2019
ms.openlocfilehash: a9192bf9f5827d05b2453c796c72e11782f9f911
ms.sourcegitcommit: 559259da2738a7b33a46c0130e51d336091c2097
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "73841281"
---
# <a name="service-mesh-communication-infrastructure"></a><span data-ttu-id="d8ae5-103">服务网格通信基础结构</span><span class="sxs-lookup"><span data-stu-id="d8ae5-103">Service Mesh communication infrastructure</span></span>

[!INCLUDE [book-preview](../../../includes/book-preview.md)]

<span data-ttu-id="d8ae5-104">在本章中，我们探讨了微服务通信的难题。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-104">Throughout this chapter, we've explored the challenges of microservice communication.</span></span> <span data-ttu-id="d8ae5-105">我们说，开发团队需要对后端服务之间的通信方式保密。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-105">We said that development teams need to be sensitive to how back-end services communicate with each other.</span></span> <span data-ttu-id="d8ae5-106">理想情况下，服务间通信越少，性能越好。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-106">Ideally, the less inter-service communication, the better.</span></span> <span data-ttu-id="d8ae5-107">不过，由于后端服务经常依赖于另一种方式来完成操作，因此不一定能避免这种情况。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-107">However, avoidance isn't always possible as back-end services often rely on one another to complete operations.</span></span>

<span data-ttu-id="d8ae5-108">我们探讨了不同的方法来实现同步 HTTP 通信和异步消息传递。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-108">We explored different approaches for implementing synchronous HTTP communication and asynchronous messaging.</span></span> <span data-ttu-id="d8ae5-109">在每种情况下，开发人员都能实现通信代码。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-109">In each of the cases, the developer is burdened with implementing communication code.</span></span> <span data-ttu-id="d8ae5-110">通信代码非常复杂且耗时。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-110">Communication code is complex and time intensive.</span></span> <span data-ttu-id="d8ae5-111">决策不当会导致严重的性能问题。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-111">Incorrect decisions can lead to significant performance issues.</span></span>

<span data-ttu-id="d8ae5-112">一种更新式的微服务通信中心方法，围绕一种新的、快速发展的技术，以*服务网格*为依据。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-112">A more modern approach to microservice communication centers around a new and rapidly evolving technology entitled *Service Mesh*.</span></span> <span data-ttu-id="d8ae5-113">[服务网格](https://www.nginx.com/blog/what-is-a-service-mesh/)是一种可配置的基础结构层，它具有内置功能，可用于处理服务到服务通信、复原和许多跨切削问题。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-113">A [service mesh](https://www.nginx.com/blog/what-is-a-service-mesh/) is a configurable infrastructure layer with built-in capabilities to handle service-to-service communication, resiliency, and many cross-cutting concerns.</span></span> <span data-ttu-id="d8ae5-114">它会将这些问题的责任转移到微服务和服务网格层。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-114">It moves the responsibility for these concerns out of the microservices and into service mesh layer.</span></span> <span data-ttu-id="d8ae5-115">从微服务抽象出通信。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-115">Communication is abstracted away from your microservices.</span></span>

<span data-ttu-id="d8ae5-116">服务网格的一个关键组件是代理。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-116">A key component of a service mesh is a proxy.</span></span> <span data-ttu-id="d8ae5-117">在云本机应用程序中，代理的实例通常与每个微服务在一起。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-117">In a cloud-native application, an instance of a proxy is typically colocated with each microservice.</span></span> <span data-ttu-id="d8ae5-118">虽然它们在单独的进程中执行，但两者密切相关并共享相同的生命周期。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-118">While they execute in separate processes, the two are closely linked and share the same lifecycle.</span></span> <span data-ttu-id="d8ae5-119">此模式称为[挎斗模式](https://docs.microsoft.com/azure/architecture/patterns/sidecar)，如图4-23 所示。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-119">This pattern, known as the [Sidecar pattern](https://docs.microsoft.com/azure/architecture/patterns/sidecar), and is shown in Figure 4-23.</span></span>

![使用侧面汽车的服务网格](./media/service-mesh-with-side-car.png)

<span data-ttu-id="d8ae5-121">图 4-23。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-121">**Figure 4-23**.</span></span> <span data-ttu-id="d8ae5-122">使用侧面汽车的服务网格</span><span class="sxs-lookup"><span data-stu-id="d8ae5-122">Service mesh with a side car</span></span>

<span data-ttu-id="d8ae5-123">请注意，上图中的消息是由每个微服务一起运行的代理截取的。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-123">Note in the previous figure how messages are intercepted by a proxy that runs alongside each microservice.</span></span> <span data-ttu-id="d8ae5-124">每个代理都可以配置特定于微服务的流量规则。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-124">Each proxy can be configured with traffic rules specific to the microservice.</span></span> <span data-ttu-id="d8ae5-125">它了解消息，并可以将消息路由到您的服务和外界。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-125">It understands messages and can route them across your services and the outside world.</span></span>

<span data-ttu-id="d8ae5-126">除了管理服务到服务通信以外，服务网格还提供对服务发现和负载平衡的支持。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-126">Along with managing service-to-service communication, the Service Mesh provides support for service discovery and load balancing.</span></span>

<span data-ttu-id="d8ae5-127">一旦配置后，服务网格就会非常有效。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-127">Once configured, a service mesh is highly functional.</span></span> <span data-ttu-id="d8ae5-128">网格从服务发现终结点中检索相应的实例池。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-128">The mesh retrieves a corresponding pool of instances from a service discovery endpoint.</span></span> <span data-ttu-id="d8ae5-129">它向特定服务实例发送请求，记录结果的延迟和响应类型。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-129">It sends a request to a specific service instance, recording the latency and response type of the result.</span></span> <span data-ttu-id="d8ae5-130">它根据不同因素选择最有可能返回快速响应的实例，包括观察到的最近请求的延迟。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-130">It chooses the instance most likely to return a fast response based on different factors, including the observed latency for recent requests.</span></span>

<span data-ttu-id="d8ae5-131">服务网格在应用程序级别管理流量、通信和网络问题。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-131">A service mesh manages traffic, communication, and networking concerns at the application level.</span></span> <span data-ttu-id="d8ae5-132">它了解消息和请求。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-132">It understands messages and requests.</span></span> <span data-ttu-id="d8ae5-133">服务网格通常与容器 orchestrator 集成。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-133">A service mesh typically integrates with a container orchestrator.</span></span> <span data-ttu-id="d8ae5-134">Kubernetes 支持可以添加服务网格的可扩展体系结构。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-134">Kubernetes supports an extensible architecture in which a service mesh can be added.</span></span>

<span data-ttu-id="d8ae5-135">第6章深入探讨了服务网格技术，其中包括对其体系结构和可用开源实现的讨论。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-135">In chapter 6, we deep-dive into Service Mesh technologies including a discussion on its architecture and available open-source implementations.</span></span>

## <a name="summary"></a><span data-ttu-id="d8ae5-136">总结</span><span class="sxs-lookup"><span data-stu-id="d8ae5-136">Summary</span></span>

<span data-ttu-id="d8ae5-137">在本章中，我们讨论了云本机通信模式。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-137">In this chapter, we discussed cloud-native communication patterns.</span></span> <span data-ttu-id="d8ae5-138">我们首先检查前端客户端如何与后端微服务通信。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-138">We started by examining how front-end clients communicate with back-end microservices.</span></span> <span data-ttu-id="d8ae5-139">在此过程中，我们讨论了 API 网关平台和实时通信。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-139">Along the way, we talked about API Gateway platforms and real-time communication.</span></span> <span data-ttu-id="d8ae5-140">然后介绍了微服务如何与其他后端服务进行通信。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-140">We then looked at how microservices communicate with other back-end services.</span></span> <span data-ttu-id="d8ae5-141">我们同时探讨了跨服务的同步 HTTP 通信和异步消息传递。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-141">We looked at both synchronous HTTP communication and asynchronous messaging across services.</span></span> <span data-ttu-id="d8ae5-142">我们介绍了 gRPC，这是云中的一项技术。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-142">We covered gRPC, an upcoming technology in the cloud-native world.</span></span> <span data-ttu-id="d8ae5-143">最后，我们引入了一种新的、快速发展的技术，该技术可简化微服务通信。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-143">Finally, we introduced a new and rapidly evolving technology entitled Service Mesh that can streamline microservice communication.</span></span>

<span data-ttu-id="d8ae5-144">特别强调的是托管 Azure 服务，可帮助实现云本机系统中的通信：</span><span class="sxs-lookup"><span data-stu-id="d8ae5-144">Special emphasis was on managed Azure services that can help implement communication in cloud-native systems:</span></span>

- [<span data-ttu-id="d8ae5-145">Azure 应用程序网关</span><span class="sxs-lookup"><span data-stu-id="d8ae5-145">Azure Application Gateway</span></span>](https://docs.microsoft.com/azure/application-gateway/overview)
- [<span data-ttu-id="d8ae5-146">Azure API 管理</span><span class="sxs-lookup"><span data-stu-id="d8ae5-146">Azure API Management</span></span>](https://azure.microsoft.com/services/api-management/)
- [<span data-ttu-id="d8ae5-147">Azure SignalR 服务</span><span class="sxs-lookup"><span data-stu-id="d8ae5-147">Azure SignalR Service</span></span>](https://azure.microsoft.com/services/signalr-service/)
- [<span data-ttu-id="d8ae5-148">Azure 存储队列</span><span class="sxs-lookup"><span data-stu-id="d8ae5-148">Azure Storage Queues</span></span>](https://docs.microsoft.com/azure/storage/queues/storage-queues-introduction)
- [<span data-ttu-id="d8ae5-149">Azure 服务总线</span><span class="sxs-lookup"><span data-stu-id="d8ae5-149">Azure Service Bus</span></span>](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview)
- [<span data-ttu-id="d8ae5-150">Azure 事件网格</span><span class="sxs-lookup"><span data-stu-id="d8ae5-150">Azure Event Grid</span></span>](https://docs.microsoft.com/azure/event-grid/overview)
- [<span data-ttu-id="d8ae5-151">Azure 事件中心</span><span class="sxs-lookup"><span data-stu-id="d8ae5-151">Azure Event Hub</span></span>](https://azure.microsoft.com/services/event-hubs/)

<span data-ttu-id="d8ae5-152">接下来，我们将迁移到云本机系统中的分布式数据，以及它所呈现的优点和挑战。</span><span class="sxs-lookup"><span data-stu-id="d8ae5-152">We next move to distributed data in cloud-native systems and the benefits and challenges that it presents.</span></span>

### <a name="references"></a><span data-ttu-id="d8ae5-153">reference</span><span class="sxs-lookup"><span data-stu-id="d8ae5-153">References</span></span>

- [<span data-ttu-id="d8ae5-154">.NET 微服务：容器化 .NET 应用程序的体系结构</span><span class="sxs-lookup"><span data-stu-id="d8ae5-154">.NET Microservices: Architecture for Containerized .NET applications</span></span>](https://dotnet.microsoft.com/download/thank-you/microservices-architecture-ebook)

- [<span data-ttu-id="d8ae5-155">为微服务设计服务间通信</span><span class="sxs-lookup"><span data-stu-id="d8ae5-155">Designing Interservice Communication for Microservices</span></span>](https://docs.microsoft.com/azure/architecture/microservices/design/interservice-communication)

- [<span data-ttu-id="d8ae5-156">Azure SignalR 服务是一项完全托管的服务，可用于添加实时功能</span><span class="sxs-lookup"><span data-stu-id="d8ae5-156">Azure SignalR Service, a fully managed service to add real-time functionality</span></span>](https://azure.microsoft.com/blog/azure-signalr-service-a-fully-managed-service-to-add-real-time-functionality/)

- [<span data-ttu-id="d8ae5-157">Azure API 网关入口控制器</span><span class="sxs-lookup"><span data-stu-id="d8ae5-157">Azure API Gateway Ingress Controller</span></span>](https://azure.github.io/application-gateway-kubernetes-ingress/)

- [<span data-ttu-id="d8ae5-158">关于 Azure Kubernetes Service （AKS）中的入口</span><span class="sxs-lookup"><span data-stu-id="d8ae5-158">About Ingress in Azure Kubernetes Service (AKS)</span></span>](https://vincentlauzon.com/2018/10/10/about-ingress-in-azure-kubernetes-service-aks/)

- [<span data-ttu-id="d8ae5-159">实际 gRPC</span><span class="sxs-lookup"><span data-stu-id="d8ae5-159">Practical gRPC</span></span>](https://www.worldcat.org/title/practical-grpc/oclc/1042342319)

- [<span data-ttu-id="d8ae5-160">gRPC 文档</span><span class="sxs-lookup"><span data-stu-id="d8ae5-160">gRPC Documentation</span></span>](https://grpc.io/docs/guides/)

- <span data-ttu-id="d8ae5-161">[适用于 WCF 开发人员的 gRPC](https://bing.com) [Mark 的 gRPC book]</span><span class="sxs-lookup"><span data-stu-id="d8ae5-161">[gRPC for WCF Developers](https://bing.com) [Mark's gRPC book]</span></span>

- [<span data-ttu-id="d8ae5-162">将 gRPC Services 与 HTTP Api 进行比较</span><span class="sxs-lookup"><span data-stu-id="d8ae5-162">Comparing gRPC Services with HTTP APIs</span></span>](https://docs.microsoft.com/aspnet/core/grpc/comparison?view=aspnetcore-3.0)

>[!div class="step-by-step"]
><span data-ttu-id="d8ae5-163">[上一页](rest-grpc.md)
>[下一页](distributed-data.md)</span><span class="sxs-lookup"><span data-stu-id="d8ae5-163">[Previous](rest-grpc.md)
[Next](distributed-data.md)</span></span>