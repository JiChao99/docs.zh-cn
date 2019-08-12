---
title: 教程：使用 ONNX 和 ML.NET 进行深度学习来检测对象
description: 本教程演示如何在 ML.NET 中使用预训练的 ONNX 深度学习模型来检测图像中的对象。
author: luisquintanilla
ms.author: luquinta
ms.date: 08/01/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 3e5b6b482dfbd1ff06347883a93a561944200a9f
ms.sourcegitcommit: 8c6426a3d2adff5fbcbe1fed0f28eda718c15351
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2019
ms.locfileid: "68733402"
---
# <a name="tutorial-detect-objects-using-onnx-in-mlnet"></a><span data-ttu-id="289e7-103">教程：在 ML.NET 中使用 ONNX 检测对象</span><span class="sxs-lookup"><span data-stu-id="289e7-103">Tutorial: Detect objects using ONNX in ML.NET</span></span>

<span data-ttu-id="289e7-104">了解如何在 ML.NET 中使用预训练的 ONNX 模型来检测图像中的对象。</span><span class="sxs-lookup"><span data-stu-id="289e7-104">Learn how to use a pre-trained ONNX model in ML.NET to detect objects in images.</span></span>

<span data-ttu-id="289e7-105">从头开始训练对象检测模型需要设置数百万个参数、大量已标记的训练数据和海量计算资源（数百个 GPU 小时）。</span><span class="sxs-lookup"><span data-stu-id="289e7-105">Training an object detection model from scratch requires setting millions of parameters, a large amount of labeled training data and a vast amount of compute resources (hundreds of GPU hours).</span></span> <span data-ttu-id="289e7-106">使用预训练的模型可让你快速完成训练过程。</span><span class="sxs-lookup"><span data-stu-id="289e7-106">Using a pre-trained model allows you to shortcut the training process.</span></span>

<span data-ttu-id="289e7-107">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="289e7-107">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="289e7-108">了解问题</span><span class="sxs-lookup"><span data-stu-id="289e7-108">Understand the problem</span></span>
> * <span data-ttu-id="289e7-109">了解什么是 ONNX 以及它如何与 ML.NET 配合使用</span><span class="sxs-lookup"><span data-stu-id="289e7-109">Learn what ONNX is and how it works with ML.NET</span></span>
> * <span data-ttu-id="289e7-110">了解模型</span><span class="sxs-lookup"><span data-stu-id="289e7-110">Understand the model</span></span>
> * <span data-ttu-id="289e7-111">重用预训练的模型</span><span class="sxs-lookup"><span data-stu-id="289e7-111">Reuse the pre-trained model</span></span>
> * <span data-ttu-id="289e7-112">使用已加载模型检测对象</span><span class="sxs-lookup"><span data-stu-id="289e7-112">Detect objects with a loaded model</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="289e7-113">先决条件</span><span class="sxs-lookup"><span data-stu-id="289e7-113">Pre-requisites</span></span>

- <span data-ttu-id="289e7-114">安装了“.NET Core 跨平台开发”工作负载的 [Visual Studio 2017 15.6 或更高版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2017)。</span><span class="sxs-lookup"><span data-stu-id="289e7-114">[Visual Studio 2017 15.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2017) with the ".NET Core cross-platform development" workload installed.</span></span>
- [<span data-ttu-id="289e7-115">Microsoft.ML Nuget 包</span><span class="sxs-lookup"><span data-stu-id="289e7-115">Microsoft.ML NuGet Package</span></span>](https://www.nuget.org/packages/Microsoft.ML/)
- [<span data-ttu-id="289e7-116">Microsoft.ML.ImageAnalytics NuGet 包</span><span class="sxs-lookup"><span data-stu-id="289e7-116">Microsoft.ML.ImageAnalytics NuGet Package</span></span>](https://www.nuget.org/packages/Microsoft.ML.ImageAnalytics/)
- [<span data-ttu-id="289e7-117">Microsoft.ML.OnnxTransformer NuGet 包</span><span class="sxs-lookup"><span data-stu-id="289e7-117">Microsoft.ML.OnnxTransformer NuGet Package</span></span>](https://www.nuget.org/packages/Microsoft.ML.OnnxTransformer/)
- [<span data-ttu-id="289e7-118">Tiny YOLOv2 预训练的模型</span><span class="sxs-lookup"><span data-stu-id="289e7-118">Tiny YOLOv2 pre-trained model</span></span>](https://github.com/onnx/models/tree/master/tiny_yolov2)
- <span data-ttu-id="289e7-119">[Netron](https://github.com/lutzroeder/netron)（可选）</span><span class="sxs-lookup"><span data-stu-id="289e7-119">[Netron](https://github.com/lutzroeder/netron) (optional)</span></span>

## <a name="onnx-object-detection-sample-overview"></a><span data-ttu-id="289e7-120">ONNX 对象检测示例概述</span><span class="sxs-lookup"><span data-stu-id="289e7-120">ONNX object detection sample overview</span></span>

<span data-ttu-id="289e7-121">此示例创建一个 .NET 核心控制台应用程序，该应用程序使用预训练的深度学习 ONNX 模型检测图像中的对象。</span><span class="sxs-lookup"><span data-stu-id="289e7-121">This sample creates a .NET core console application that detects objects within an image using a pre-trained deep learning ONNX model.</span></span> <span data-ttu-id="289e7-122">此示例的代码可以在 GitHub 上的 [dotnet/machinelearning-samples 存储库](https://github.com/dotnet/machinelearning-samples/tree/master/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx)找到。</span><span class="sxs-lookup"><span data-stu-id="289e7-122">The code for this sample can be found on the [dotnet/machinelearning-samples repository](https://github.com/dotnet/machinelearning-samples/tree/master/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx) on GitHub.</span></span>

## <a name="what-is-object-detection"></a><span data-ttu-id="289e7-123">什么是对象检测？</span><span class="sxs-lookup"><span data-stu-id="289e7-123">What is object detection?</span></span>

<span data-ttu-id="289e7-124">对象检测是计算机视觉问题。</span><span class="sxs-lookup"><span data-stu-id="289e7-124">Object detection is a computer vision problem.</span></span> <span data-ttu-id="289e7-125">虽然与图像分类密切相关，但是对象检测以更精细的比例执行图像分类。</span><span class="sxs-lookup"><span data-stu-id="289e7-125">While closely related to image classification, object detection performs image classification at a more granular scale.</span></span> <span data-ttu-id="289e7-126">对象检测用于定位  图像中的实体并对其进行分类。</span><span class="sxs-lookup"><span data-stu-id="289e7-126">Object detection both locates _and_ categorizes entities within images.</span></span> <span data-ttu-id="289e7-127">如果图像包含多个不同类型的对象，请使用对象检测。</span><span class="sxs-lookup"><span data-stu-id="289e7-127">Use object detection when images contain multiple objects of different types.</span></span>

![](./media/object-detection-onnx/img-classification-obj-detection.PNG)

<span data-ttu-id="289e7-128">对象检测的一些用例包括：</span><span class="sxs-lookup"><span data-stu-id="289e7-128">Some use cases for object detection include:</span></span>

- <span data-ttu-id="289e7-129">自动驾驶汽车</span><span class="sxs-lookup"><span data-stu-id="289e7-129">Self-Driving Cars</span></span>
- <span data-ttu-id="289e7-130">机器人</span><span class="sxs-lookup"><span data-stu-id="289e7-130">Robotics</span></span>
- <span data-ttu-id="289e7-131">人脸检测</span><span class="sxs-lookup"><span data-stu-id="289e7-131">Face Detection</span></span>
- <span data-ttu-id="289e7-132">工作区安全性</span><span class="sxs-lookup"><span data-stu-id="289e7-132">Workplace Safety</span></span>
- <span data-ttu-id="289e7-133">对象计数</span><span class="sxs-lookup"><span data-stu-id="289e7-133">Object Counting</span></span>
- <span data-ttu-id="289e7-134">活动识别</span><span class="sxs-lookup"><span data-stu-id="289e7-134">Activity Recognition</span></span>

## <a name="select-a-deep-learning-model"></a><span data-ttu-id="289e7-135">选择深度学习模型</span><span class="sxs-lookup"><span data-stu-id="289e7-135">Select a deep learning model</span></span>

<span data-ttu-id="289e7-136">深度学习是机器学习的一部分。</span><span class="sxs-lookup"><span data-stu-id="289e7-136">Deep learning is a subset of machine learning.</span></span> <span data-ttu-id="289e7-137">若要训练深度学习模型，则需要大量的数据。</span><span class="sxs-lookup"><span data-stu-id="289e7-137">To train deep learning models, large quantities of data are required.</span></span> <span data-ttu-id="289e7-138">数据中的模式用一系列层表示。</span><span class="sxs-lookup"><span data-stu-id="289e7-138">Patterns in the data are represented by a series of layers.</span></span> <span data-ttu-id="289e7-139">数据中的关系被编码为包含权重的层之间的连接。</span><span class="sxs-lookup"><span data-stu-id="289e7-139">The relationships in the data are encoded as connections between the layers containing weights.</span></span> <span data-ttu-id="289e7-140">权重越大，关系越强。</span><span class="sxs-lookup"><span data-stu-id="289e7-140">The higher the weight, the stronger the relationship.</span></span> <span data-ttu-id="289e7-141">总的来说，这一系列的层和连接被称为人工神经网络。</span><span class="sxs-lookup"><span data-stu-id="289e7-141">Collectively, this series of layers and connections are known as artificial neural networks.</span></span> <span data-ttu-id="289e7-142">网络中的层越多，它就越“深”，使其成为一个深层的神经网络。</span><span class="sxs-lookup"><span data-stu-id="289e7-142">The more layers in a network, the "deeper" it is, making it a deep neural network.</span></span> 

<span data-ttu-id="289e7-143">神经网络有多种类型，最常见的是多层感知器 (MLP)、卷积神经网络 (CNN) 和循环神经网络 (RNN)。</span><span class="sxs-lookup"><span data-stu-id="289e7-143">There are different types of neural networks, the most common being Multi-Layered Perceptron (MLP), Convolutional Neural Network (CNN) and Recurrent Neural Network (RNN).</span></span> <span data-ttu-id="289e7-144">最基本的是 MLP，可将一组输入映射到一组输出。</span><span class="sxs-lookup"><span data-stu-id="289e7-144">The most basic is the MLP, which maps a set of inputs to a set of outputs.</span></span> <span data-ttu-id="289e7-145">如果数据没有空间或时间组件，建议使用这种神经网络。</span><span class="sxs-lookup"><span data-stu-id="289e7-145">This neural network is good when the data does not have a spatial or time component.</span></span> <span data-ttu-id="289e7-146">CNN 利用卷积层来处理数据中包含的空间信息。</span><span class="sxs-lookup"><span data-stu-id="289e7-146">The CNN makes use of convolutional layers to process spatial information contained in the data.</span></span> <span data-ttu-id="289e7-147">图像处理就是 CNN 的一个很好的用例，它检测图像区域中是否存在特征（例如，图像中心是否有鼻子？）</span><span class="sxs-lookup"><span data-stu-id="289e7-147">A good use case for CNNs is image processing to detect the presence of a feature in a region of an image (for example, is there a nose in the center of an image?).</span></span> <span data-ttu-id="289e7-148">最后，RNN 允许将状态或内存的持久性用作输入。</span><span class="sxs-lookup"><span data-stu-id="289e7-148">Finally, RNNs allow for the persistence of state or memory to be used as input.</span></span> <span data-ttu-id="289e7-149">RNN 用于时间序列分析，其中事件的顺序排序和上下文很重要。</span><span class="sxs-lookup"><span data-stu-id="289e7-149">RNNs are used for time-series analysis, where the sequential ordering and context of events is important.</span></span> 

### <a name="understand-the-model"></a><span data-ttu-id="289e7-150">了解模型</span><span class="sxs-lookup"><span data-stu-id="289e7-150">Understand the model</span></span>

<span data-ttu-id="289e7-151">对象检测是图像处理任务。</span><span class="sxs-lookup"><span data-stu-id="289e7-151">Object detection is an image processing task.</span></span> <span data-ttu-id="289e7-152">因此，训练解决该问题的大多数深度学习模型都是 CNN。</span><span class="sxs-lookup"><span data-stu-id="289e7-152">Therefore, most deep learning models trained to solve this problem are CNNs.</span></span> <span data-ttu-id="289e7-153">本教程中使用的模型是 Tiny YOLOv2 模型，这是该文件中描述的 YOLOv2 模型的一个更紧凑版本：[“YOLO9000：更好、更快、更强”，作者：Redmon 和 Fadhari](https://arxiv.org/pdf/1612.08242.pdf)。</span><span class="sxs-lookup"><span data-stu-id="289e7-153">The model used in this tutorial is the Tiny YOLOv2 model, a more compact version of the YOLOv2 model described in the paper: ["YOLO9000: Better, Faster, Stronger" by Redmon and Fadhari](https://arxiv.org/pdf/1612.08242.pdf).</span></span> <span data-ttu-id="289e7-154">Tiny YOLOv2 在 Pascal VOC 数据集上进行训练，共包含 15 层，可预测 20 种不同类别的对象。</span><span class="sxs-lookup"><span data-stu-id="289e7-154">Tiny YOLOv2 is trained on the Pascal VOC dataset and is made up of 15 layers that can predict 20 different classes of objects.</span></span> <span data-ttu-id="289e7-155">由于 Tiny YOLOv2 是原始 YOLOv2 模型的精简版本，因此需要在速度和精度之间进行权衡。</span><span class="sxs-lookup"><span data-stu-id="289e7-155">Because Tiny YOLOv2 is a condensed version of the original YOLOv2 model, a tradeoff is made between speed and accuracy.</span></span> <span data-ttu-id="289e7-156">构成模型的不同层可以使用 Neutron 等工具进行可视化。</span><span class="sxs-lookup"><span data-stu-id="289e7-156">The different layers that make up the model can be visualized using tools like Netron.</span></span> <span data-ttu-id="289e7-157">检查模型将在构成神经网络的所有层之间生成连接映射，其中每个层都将包含层名称以及各自输入/输出的维度。</span><span class="sxs-lookup"><span data-stu-id="289e7-157">Inspecting the model would yield a mapping of the connections between all the layers that make up the neural network, where each layer would contain the name of the layer along with the dimensions of the respective input / output.</span></span> <span data-ttu-id="289e7-158">用于描述模型输入和输出的数据结构称为张量。</span><span class="sxs-lookup"><span data-stu-id="289e7-158">The data structures used to describe the inputs and outputs of the model are known as tensors.</span></span> <span data-ttu-id="289e7-159">可以将张量视为以 N 维存储数据的容器。</span><span class="sxs-lookup"><span data-stu-id="289e7-159">Tensors can be thought of as containers that store data in N-dimensions.</span></span> <span data-ttu-id="289e7-160">对于 Tiny YOLOv2，输入层名称为 `image`，它需要一个维度为 `3 x 416 x 416` 的张量。</span><span class="sxs-lookup"><span data-stu-id="289e7-160">In the case of Tiny YOLOv2, the name of the input layer is `image` and it expects a tensor of dimensions `3 x 416 x 416`.</span></span> <span data-ttu-id="289e7-161">输出层名称为 `grid`，且生成维度为 `125 x 13 x 13` 的输出张量。</span><span class="sxs-lookup"><span data-stu-id="289e7-161">The name of the output layer is `grid` and generates an output tensor of dimensions `125 x 13 x 13`.</span></span>  

![](./media/object-detection-onnx/netron-model-map.png)

<span data-ttu-id="289e7-162">YOLO 模型采用图像 `3(RGB) x 416px x 416px`。</span><span class="sxs-lookup"><span data-stu-id="289e7-162">The YOLO model takes an image `3(RGB) x 416px x 416px`.</span></span> <span data-ttu-id="289e7-163">模型接受此输入，并将其传递到不同的层以生成输出。</span><span class="sxs-lookup"><span data-stu-id="289e7-163">The model takes this input and passes it through the different layers to produce an output.</span></span> <span data-ttu-id="289e7-164">输出将输入图像划分为一个 `13 x 13` 网格，网格中的每个单元格由 `125` 值组成。</span><span class="sxs-lookup"><span data-stu-id="289e7-164">The output divides the input image into a `13 x 13` grid, with each cell in the grid consisting of `125` values.</span></span> 

### <a name="what-is-an-onnx-model"></a><span data-ttu-id="289e7-165">什么是 ONNX 模型？</span><span class="sxs-lookup"><span data-stu-id="289e7-165">What is an ONNX model?</span></span>

<span data-ttu-id="289e7-166">开放神经网络交换 (ONNX) 是 AI 模型的开放源代码格式。</span><span class="sxs-lookup"><span data-stu-id="289e7-166">The Open Neural Network Exchange (ONNX) is an open source format for AI models.</span></span> <span data-ttu-id="289e7-167">ONNX 支持框架之间的互操作性。</span><span class="sxs-lookup"><span data-stu-id="289e7-167">ONNX supports interoperability between frameworks.</span></span> <span data-ttu-id="289e7-168">这意味着，你可以在许多常见的机器学习框架（如 pytorch）中训练模型，将其转换为 ONNX 格式，并在其他框架（如 ML.NET）中使用 ONNX 模型。</span><span class="sxs-lookup"><span data-stu-id="289e7-168">This means you can train a model in one of the many popular machine learning frameworks like PyTorch, convert it into ONNX format and consume the ONNX model in a different framework like ML.NET.</span></span> <span data-ttu-id="289e7-169">有关详细信息，请参阅 [ONNX 网站](https://onnx.ai/)。</span><span class="sxs-lookup"><span data-stu-id="289e7-169">To learn more, visit the [ONNX website](https://onnx.ai/).</span></span> 

![](./media/object-detection-onnx/onnx-frameworks.png)

<span data-ttu-id="289e7-170">预训练的 Tiny YOLOv2 模型以 ONNX 格式存储，这是层的序列化表示形式，也是这些层的已知模式。</span><span class="sxs-lookup"><span data-stu-id="289e7-170">The pre-trained Tiny YOLOv2 model is stored in ONNX format, a serialized representation of the layers and learned patterns of those layers.</span></span> <span data-ttu-id="289e7-171">在 ML.NET 中，使用 [`ImageAnalytics`](xref:Microsoft.ML.Transforms.Image) 和 [`OnnxTransformer`](xref:Microsoft.ML.Transforms.Onnx.OnnxTransformer) NuGet 包实现了与 ONNX 的互操作性。</span><span class="sxs-lookup"><span data-stu-id="289e7-171">In ML.NET, interoperability with ONNX is achieved with the [`ImageAnalytics`](xref:Microsoft.ML.Transforms.Image) and [`OnnxTransformer`](xref:Microsoft.ML.Transforms.Onnx.OnnxTransformer) NuGet packages.</span></span> <span data-ttu-id="289e7-172">[ `ImageAnalytics` ](xref:Microsoft.ML.Transforms.Image) 包包含一系列转换，这些转换采用图像并将其编码为可用作预测或训练管道输入的数值。</span><span class="sxs-lookup"><span data-stu-id="289e7-172">The [`ImageAnalytics`](xref:Microsoft.ML.Transforms.Image) package contains a series of transforms that take an image and encode it into numerical values that can be used as input into a prediction or training pipeline.</span></span> <span data-ttu-id="289e7-173">[`OnnxTransformer`](xref:Microsoft.ML.Transforms.Onnx.OnnxTransformer) 包利用 ONNX 运行时加载 ONNX 模型并使用它根据提供的输入进行预测。</span><span class="sxs-lookup"><span data-stu-id="289e7-173">The [`OnnxTransformer`](xref:Microsoft.ML.Transforms.Onnx.OnnxTransformer) package leverages the ONNX Runtime to load an ONNX model and use it to make predictions based on input provided.</span></span> 

![](./media/object-detection-onnx/onnx-ml-net-integration.png)

## <a name="set-up-the-net-core-project"></a><span data-ttu-id="289e7-174">设置 .NET Core 项目</span><span class="sxs-lookup"><span data-stu-id="289e7-174">Set up the .NET Core project</span></span>

<span data-ttu-id="289e7-175">对 ONNX 的涵义以及 Tiny YOLOv2 的工作原理有了大致了解之后，接下来了解如何生成应用程序。</span><span class="sxs-lookup"><span data-stu-id="289e7-175">Now that you have a general understanding of what ONNX is and how Tiny YOLOv2 works, it's time to build the application.</span></span>

### <a name="create-a-console-application"></a><span data-ttu-id="289e7-176">创建控制台应用程序</span><span class="sxs-lookup"><span data-stu-id="289e7-176">Create a console application</span></span>

1. <span data-ttu-id="289e7-177">创建名为 ObjectDetection 的 .NET Core 控制台应用程序  。</span><span class="sxs-lookup"><span data-stu-id="289e7-177">Create a **.NET Core Console Application** called "ObjectDetection".</span></span>

1. <span data-ttu-id="289e7-178">安装“Microsoft.ML NuGet 包”  ：</span><span class="sxs-lookup"><span data-stu-id="289e7-178">Install the **Microsoft.ML NuGet Package**:</span></span>

    - <span data-ttu-id="289e7-179">在“解决方案资源管理器”中，右键单击项目，然后选择“管理 NuGet 包”  。</span><span class="sxs-lookup"><span data-stu-id="289e7-179">In Solution Explorer, right-click on your project and select **Manage NuGet Packages**.</span></span> 
    - <span data-ttu-id="289e7-180">选择“nuget.org”作为“包源”，选择“浏览”选项卡，再搜索“Microsoft.ML”  。</span><span class="sxs-lookup"><span data-stu-id="289e7-180">Choose "nuget.org" as the Package source, select the Browse tab, search for **Microsoft.ML**.</span></span> 
    - <span data-ttu-id="289e7-181">选择“安装”按钮  。</span><span class="sxs-lookup"><span data-stu-id="289e7-181">Select the **Install** button.</span></span> 
    - <span data-ttu-id="289e7-182">选择“预览更改”  对话框上的“确定”  按钮，如果你同意所列包的许可条款，则选择“接受许可”  对话框上的“我接受”  按钮。</span><span class="sxs-lookup"><span data-stu-id="289e7-182">Select the **OK** button on the **Preview Changes** dialog and then select the **I Accept** button on the **License Acceptance** dialog if you agree with the license terms for the packages listed.</span></span> 
    - <span data-ttu-id="289e7-183">对 Microsoft.ML.ImageAnalytics 和 Microsoft.ML.OnnxTransformer 重复这些步骤   。</span><span class="sxs-lookup"><span data-stu-id="289e7-183">Repeat these steps for **Microsoft.ML.ImageAnalytics** and **Microsoft.ML.OnnxTransformer**.</span></span>

### <a name="prepare-your-data-and-pre-trained-model"></a><span data-ttu-id="289e7-184">准备你的数据和预训练的模型</span><span class="sxs-lookup"><span data-stu-id="289e7-184">Prepare your data and pre-trained model</span></span>

1. <span data-ttu-id="289e7-185">下载并解压缩[项目资产目录 zip 文件](https://github.com/dotnet/machinelearning-samples/raw/master/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/assets.zip)。</span><span class="sxs-lookup"><span data-stu-id="289e7-185">Download [The project assets directory zip file](https://github.com/dotnet/machinelearning-samples/raw/master/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/assets.zip) and unzip.</span></span>

1. <span data-ttu-id="289e7-186">将 `assets` 目录复制到 ObjectDetection 项目目录中  。</span><span class="sxs-lookup"><span data-stu-id="289e7-186">Copy the `assets` directory into your *ObjectDetection* project directory.</span></span> <span data-ttu-id="289e7-187">此目录及其子目录包含本教程所需的图像文件（Tiny YOLOv2 模型除外，将在下一步中下载并添加此模型）。</span><span class="sxs-lookup"><span data-stu-id="289e7-187">This directory and its subdirectories contain the image files (except for the Tiny YOLOv2 model, which you'll download and add in the next step) needed for this tutorial.</span></span>

1. <span data-ttu-id="289e7-188">从 [ONNX Model Zoo](https://github.com/onnx/models/tree/master/vision/object_detection_segmentation/tiny_yolov2) 下载并解压缩 [Tiny YOLOv2 模型](https://onnxzoo.blob.core.windows.net/models/opset_8/tiny_yolov2/tiny_yolov2.tar.gz)。</span><span class="sxs-lookup"><span data-stu-id="289e7-188">Download the [Tiny YOLOv2 model](https://onnxzoo.blob.core.windows.net/models/opset_8/tiny_yolov2/tiny_yolov2.tar.gz) from the [ONNX Model Zoo](https://github.com/onnx/models/tree/master/vision/object_detection_segmentation/tiny_yolov2), and unzip.</span></span>

    <span data-ttu-id="289e7-189">打开命令提示符并输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="289e7-189">Open the command prompt and enter the following command:</span></span>

    ```shell
    tar -xvzf tiny_yolov2.tar.gz 
    ```

1. <span data-ttu-id="289e7-190">将提取的 `model.onnx` 文件从刚刚解压缩的目录复制到 ObjectDetection 项目的 `assets\Model` 目录中，并将其重命名为 `TinyYolo2_model.onnx`  。</span><span class="sxs-lookup"><span data-stu-id="289e7-190">Copy the extracted `model.onnx` file from the directory just unzipped into your *ObjectDetection* project `assets\Model` directory and rename it to `TinyYolo2_model.onnx`.</span></span> <span data-ttu-id="289e7-191">此目录包含本教程所需的模型。</span><span class="sxs-lookup"><span data-stu-id="289e7-191">This directory contains the model needed for this tutorial.</span></span>

1. <span data-ttu-id="289e7-192">在“解决方案资源管理器”中，右键单击资产目录和子目录中的每个文件，再选择“属性”  。</span><span class="sxs-lookup"><span data-stu-id="289e7-192">In Solution Explorer, right-click each of the files in the asset directory and subdirectories and select **Properties**.</span></span> <span data-ttu-id="289e7-193">在“高级”下，将“复制到输出目录”的值更改为“如果较新则复制”    。</span><span class="sxs-lookup"><span data-stu-id="289e7-193">Under **Advanced**, change the value of **Copy to Output Directory** to **Copy if newer**.</span></span>

### <a name="create-classes-and-define-paths"></a><span data-ttu-id="289e7-194">创建类和定义路径</span><span class="sxs-lookup"><span data-stu-id="289e7-194">Create classes and define paths</span></span>

<span data-ttu-id="289e7-195">打开 Program.cs 文件，并将以下附加的 `using` 语句添加到该文件顶部  ：</span><span class="sxs-lookup"><span data-stu-id="289e7-195">Open the *Program.cs* file and add the following additional `using` statements to the top of the file:</span></span>

[!code-csharp [ProgramUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L1-L9)]

<span data-ttu-id="289e7-196">接下来，定义各种资产的路径。</span><span class="sxs-lookup"><span data-stu-id="289e7-196">Next, define the paths of the various assets.</span></span> 

1. <span data-ttu-id="289e7-197">首先，在 `Program` 类的 `Main` 方法下面添加 `GetAbsolutePath` 方法。</span><span class="sxs-lookup"><span data-stu-id="289e7-197">First, add the `GetAbsolutePath` method below the `Main` method in the `Program` class.</span></span> 

    [!code-csharp [GetAbsolutePath](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L66-L74)]

1. <span data-ttu-id="289e7-198">然后，在 `Main` 方法内，创建字段以存储资产的位置：</span><span class="sxs-lookup"><span data-stu-id="289e7-198">Then, inside the `Main` method, create fields to store the location of your assets:</span></span>

    [!code-csharp [AssetDefinition](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L17-L21)]

<span data-ttu-id="289e7-199">向项目添加新目录以存储输入数据和预测类。</span><span class="sxs-lookup"><span data-stu-id="289e7-199">Add a new directory to your project to store your input data and prediction classes.</span></span>

<span data-ttu-id="289e7-200">在“解决方案资源管理器”中，右键单击项目，然后选择“添加” > “新文件夹”    。</span><span class="sxs-lookup"><span data-stu-id="289e7-200">In **Solution Explorer**, right-click the project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="289e7-201">当新文件夹出现在解决方案资源管理器中时，将其命名为“DataStructures”。</span><span class="sxs-lookup"><span data-stu-id="289e7-201">When the new folder appears in the Solution Explorer, name it "DataStructures".</span></span>

<span data-ttu-id="289e7-202">在新创建的“DataStructures”目录中创建输入数据类  。</span><span class="sxs-lookup"><span data-stu-id="289e7-202">Create your input data class in the newly created *DataStructures* directory.</span></span>

1. <span data-ttu-id="289e7-203">在“解决方案资源管理器”中，右键单击“DataStructures”目录，然后选择“添加” > “新项”     。</span><span class="sxs-lookup"><span data-stu-id="289e7-203">In **Solution Explorer**, right-click the *DataStructures* directory, and then select **Add** > **New Item**.</span></span>
1. <span data-ttu-id="289e7-204">在“添加新项”对话框中，选择“类”，并将“名称”字段更改为“ImageNetData.cs”     。</span><span class="sxs-lookup"><span data-stu-id="289e7-204">In the **Add New Item** dialog box, select **Class** and change the **Name** field to *ImageNetData.cs*.</span></span> <span data-ttu-id="289e7-205">然后，选择“添加”  按钮。</span><span class="sxs-lookup"><span data-stu-id="289e7-205">Then, select the **Add** button.</span></span>
     
    <span data-ttu-id="289e7-206">此时，将在代码编辑器中打开 ImageNetData.cs 文件  。</span><span class="sxs-lookup"><span data-stu-id="289e7-206">The *ImageNetData.cs* file opens in the code editor.</span></span> <span data-ttu-id="289e7-207">将下面的 `using` 语句添加到 ImageNetData.cs 顶部  ：</span><span class="sxs-lookup"><span data-stu-id="289e7-207">Add the following `using` statement to the top of *ImageNetData.cs*:</span></span>

    [!code-csharp [ImageNetDataUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/DataStructures/ImageNetData.cs#L1-L4)]

    <span data-ttu-id="289e7-208">删除现有类定义，并将 `ImageNetData` 类的以下代码添加到 ImageNetData.cs 文件中  ：</span><span class="sxs-lookup"><span data-stu-id="289e7-208">Remove the existing class definition and add the following code for the `ImageNetData` class to the *ImageNetData.cs* file:</span></span>
    
    [!code-csharp [ImageNetDataClass](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/DataStructures/ImageNetData.cs#L8-L23)]

    <span data-ttu-id="289e7-209">`ImageNetData` 是输入图像数据类，包含以下 <xref:System.String> 字段：</span><span class="sxs-lookup"><span data-stu-id="289e7-209">`ImageNetData` is the input image data class and has the following <xref:System.String> fields:</span></span>

    - <span data-ttu-id="289e7-210">`ImagePath` 包含存储图像的路径。</span><span class="sxs-lookup"><span data-stu-id="289e7-210">`ImagePath` contains the path where the image is stored.</span></span>
    - <span data-ttu-id="289e7-211">`Label` 包含文件的名称。</span><span class="sxs-lookup"><span data-stu-id="289e7-211">`Label` contains the name of the file.</span></span>

    <span data-ttu-id="289e7-212">此外，`ImageNetData` 包含方法 `ReadFromFile`，该方法加载存储在指定的 `imageFolder` 路径中的多个图像文件，并将它们作为 `ImageNetData` 对象的集合返回。</span><span class="sxs-lookup"><span data-stu-id="289e7-212">Additionally, `ImageNetData` contains a method `ReadFromFile` which loads multiple image files stored in the `imageFolder` path specified and returns them as a collection of `ImageNetData` objects.</span></span>

<span data-ttu-id="289e7-213">在“DataStructures”目录中创建预测类  。</span><span class="sxs-lookup"><span data-stu-id="289e7-213">Create your prediction class in the *DataStructures* directory.</span></span>

1. <span data-ttu-id="289e7-214">在“解决方案资源管理器”中，右键单击“DataStructures”目录，然后选择“添加” > “新项”     。</span><span class="sxs-lookup"><span data-stu-id="289e7-214">In **Solution Explorer**, right-click the *DataStructures* directory, and then select **Add** > **New Item**.</span></span>
1. <span data-ttu-id="289e7-215">在“添加新项”对话框中，选择“类”，并将“名称”字段更改为“ImageNetPrediction.cs”     。</span><span class="sxs-lookup"><span data-stu-id="289e7-215">In the **Add New Item** dialog box, select **Class** and change the **Name** field to *ImageNetPrediction.cs*.</span></span> <span data-ttu-id="289e7-216">然后，选择“添加”  按钮。</span><span class="sxs-lookup"><span data-stu-id="289e7-216">Then, select the **Add** button.</span></span>

    <span data-ttu-id="289e7-217">此时，将在代码编辑器中打开 ImageNetPrediction.cs 文件  。</span><span class="sxs-lookup"><span data-stu-id="289e7-217">The *ImageNetPrediction.cs* file opens in the code editor.</span></span> <span data-ttu-id="289e7-218">将下面的 `using` 语句添加到 ImageNetPrediction.cs 顶部  ：</span><span class="sxs-lookup"><span data-stu-id="289e7-218">Add the following `using` statement to the top of *ImageNetPrediction.cs*:</span></span>

    [!code-csharp [ImageNetPredictionUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/DataStructures/ImageNetPrediction.cs#L1)]

    <span data-ttu-id="289e7-219">删除现有类定义，并将 `ImageNetPrediction` 类的以下代码添加到 ImageNetPrediction.cs 文件中  ：</span><span class="sxs-lookup"><span data-stu-id="289e7-219">Remove the existing class definition and add the following code for the `ImageNetPrediction` class to the *ImageNetPrediction.cs* file:</span></span>

    [!code-csharp [ImageNetPredictionClass](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/DataStructures/ImageNetPrediction.cs#L5-L9)]

    <span data-ttu-id="289e7-220">`ImageNetPrediction` 是预测数据类，包含以下 `float[]` 字段：</span><span class="sxs-lookup"><span data-stu-id="289e7-220">`ImageNetPrediction` is the prediction data class and has the following `float[]` field:</span></span>

    - <span data-ttu-id="289e7-221">`PredictedLabel` 包含图像中检测到的每个边界框的尺寸、对象分数和类概率。</span><span class="sxs-lookup"><span data-stu-id="289e7-221">`PredictedLabel` contains the dimensions, objectness score and class probabilities for each of the bounding boxes detected in an image.</span></span>

### <a name="initialize-variables-in-main"></a><span data-ttu-id="289e7-222">在 Main 中初始化变量</span><span class="sxs-lookup"><span data-stu-id="289e7-222">Initialize variables in Main</span></span>

<span data-ttu-id="289e7-223">执行所有 ML.NET 操作都是从 [MLContext 类](xref:Microsoft.ML.MLContext)开始，初始化 `mlContext` 可创建一个新的 ML.NET 环境，可在模型创建工作流对象之间共享该环境。</span><span class="sxs-lookup"><span data-stu-id="289e7-223">The [MLContext class](xref:Microsoft.ML.MLContext) is a starting point for all ML.NET operations, and initializing `mlContext` creates a new ML.NET environment that can be shared across the model creation workflow objects.</span></span> <span data-ttu-id="289e7-224">从概念上讲，它与实体框架中的 `DBContext` 类似。</span><span class="sxs-lookup"><span data-stu-id="289e7-224">It's similar, conceptually, to `DBContext` in Entity Framework.</span></span>

<span data-ttu-id="289e7-225">通过将以下行添加到 `outputFolder` 字段下 Program.cs 的 `Main` 方法，使用新的 `MLContext` 实例初始化 `mlContext` 变量  。</span><span class="sxs-lookup"><span data-stu-id="289e7-225">Initialize the `mlContext` variable with a new instance of `MLContext` by adding the following line to the `Main` method of *Program.cs* below the `outputFolder` field.</span></span>

[!code-csharp [InitMLContext](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L24)]

### <a name="add-helper-methods"></a><span data-ttu-id="289e7-226">添加帮助程序方法</span><span class="sxs-lookup"><span data-stu-id="289e7-226">Add Helper Methods</span></span>

<span data-ttu-id="289e7-227">在模型进行预测（通常称为评分）并对输出进行处理后，必须在图像上绘制边界框。</span><span class="sxs-lookup"><span data-stu-id="289e7-227">After the model has made a prediction, commonly referred to as scoring, and the outputs have been processed, the bounding boxes have to be drawn on the image.</span></span> <span data-ttu-id="289e7-228">为此，请在 Program.cs 内的 `GetAbsolutePath` 方法下添加名为 `DrawBoundingBox` 的方法  。</span><span class="sxs-lookup"><span data-stu-id="289e7-228">To do so, add a method called `DrawBoundingBox` below the `GetAbsolutePath` method insode of *Program.cs*.</span></span>

```csharp
private static void DrawBoundingBox(string inputImageLocation, string outputImageLocation, string imageName, IList<YoloBoundingBox> filteredBoundingBoxes)
{

}
```

<span data-ttu-id="289e7-229">首先，加载图像并使用 `DrawBoundingBox` 方法获取高度和宽度尺寸。</span><span class="sxs-lookup"><span data-stu-id="289e7-229">First, load the image and get the height and width dimensions in the `DrawBoundingBox` method.</span></span>

[!code-csharp [LoadImage](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L78-L81)]

<span data-ttu-id="289e7-230">然后，创建 for-each 循环以遍历模型检测到的每个边界框。</span><span class="sxs-lookup"><span data-stu-id="289e7-230">Then, create a for-each loop to iterate over each of the bounding boxes detected by the model.</span></span>

```csharp
foreach (var box in filteredBoundingBoxes)
{

}
```

<span data-ttu-id="289e7-231">在 for each 循环的内部，获取边界框的维度。</span><span class="sxs-lookup"><span data-stu-id="289e7-231">Inside of the for-each loop, get the dimensions of the bounding box.</span></span>

[!code-csharp [GetBBoxDimensions](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L86-L89)]

<span data-ttu-id="289e7-232">由于边界框的维度对应于 `416 x 416` 的模型输入，因此缩放边界框维度以匹配图像的实际尺寸。</span><span class="sxs-lookup"><span data-stu-id="289e7-232">Because the dimensions of the bounding box correspond to the model input of `416 x 416`, scale the bounding box dimensions to match the actual size of the image.</span></span>

[!code-csharp [ScaleImage](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L92-L95)]

<span data-ttu-id="289e7-233">然后，为将出现在每个边界框上方的文本定义模板。</span><span class="sxs-lookup"><span data-stu-id="289e7-233">Then, define a template for text that will apear above each bounding box.</span></span> <span data-ttu-id="289e7-234">文本将包含相应边界框内的对象类以及置信度。</span><span class="sxs-lookup"><span data-stu-id="289e7-234">The text will contain the class of the object inside of the respective bounding box as well as the confidence.</span></span>

[!code-csharp [DefineBBoxText](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L98)]

<span data-ttu-id="289e7-235">若要在图像上绘制，请将其转换为 [`Graphics`](xref:System.Drawing.Graphics) 对象。</span><span class="sxs-lookup"><span data-stu-id="289e7-235">In order to draw on the image, convert it to a [`Graphics`](xref:System.Drawing.Graphics) object.</span></span>

```csharp
using (Graphics thumbnailGraphic = Graphics.FromImage(image))
{
    
}
```

<span data-ttu-id="289e7-236">在 `using` 代码块内，调整图形的 [`Graphics`](xref:System.Drawing.Graphics) 对象设置。</span><span class="sxs-lookup"><span data-stu-id="289e7-236">Inside the `using` code block, tune the graphic's [`Graphics`](xref:System.Drawing.Graphics) object settings.</span></span>

[!code-csharp [TuneGraphicSettings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L102-L104)]

<span data-ttu-id="289e7-237">在下面，设置文本和边界框的字体和颜色选项。</span><span class="sxs-lookup"><span data-stu-id="289e7-237">Below that, set the font and color options for the text and bounding box.</span></span>

[!code-csharp [SetColorOptions](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L106-L114)]

<span data-ttu-id="289e7-238">使用 [`FillRectangle`](xref:System.Drawing.Graphics.FillRectangle*) 方法创建并填充边界框上方的矩形以包含文本。</span><span class="sxs-lookup"><span data-stu-id="289e7-238">Create and fill a rectangle above the bounding box to contain the text using the [`FillRectangle`](xref:System.Drawing.Graphics.FillRectangle*) method.</span></span> <span data-ttu-id="289e7-239">这将有助于对比文本，提高可读性。</span><span class="sxs-lookup"><span data-stu-id="289e7-239">This will help contrast the text and improve readability.</span></span>

[!code-csharp [DrawTextBackground](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L117)]

<span data-ttu-id="289e7-240">然后，使用 [`DrawString`](xref:System.Drawing.Graphics.DrawString*) 和 [`DrawRectangle`](xref:System.Drawing.Graphics.DrawRectangle*) 方法在图像上绘制文本和边界框。</span><span class="sxs-lookup"><span data-stu-id="289e7-240">Then, Draw the text and bounding box on the image using the [`DrawString`](xref:System.Drawing.Graphics.DrawString*) and [`DrawRectangle`](xref:System.Drawing.Graphics.DrawRectangle*) methods.</span></span>

[!code-csharp [DrawClassAndBBox](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L118-L121)]

<span data-ttu-id="289e7-241">在 for-each 循环之外，添加代码以保存 `outputDirectory` 中的图像。</span><span class="sxs-lookup"><span data-stu-id="289e7-241">Outside of the for-each loop, add code to save the images in the `outputDirectory`.</span></span>

[!code-csharp [SaveImage](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L125-L130)]

<span data-ttu-id="289e7-242">若要获得应用程序在运行时按预期进行预测的其他反馈，请在 Program.cs 文件中的 `DrawBoundingBox` 方法下添加一个名为 `LogDetectedObjects` 的方法，以将检测到的对象输出到控制台  。</span><span class="sxs-lookup"><span data-stu-id="289e7-242">To get additional feedback that the application is making predictions as expected at runtime, add a method called `LogDetectedObjects` below the `DrawBoundingBox` method in the *Program.cs* file to output the detected objects to the console.</span></span>

[!code-csharp [LogOuptuts](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L133-L143)]

<span data-ttu-id="289e7-243">当模型生成输出并且已经处理完输出时，这两种方法都将非常有用。</span><span class="sxs-lookup"><span data-stu-id="289e7-243">Both of these methods will be useful when the model has produced outputs and those have been processed.</span></span> <span data-ttu-id="289e7-244">首先，创建处理模型输出的功能。</span><span class="sxs-lookup"><span data-stu-id="289e7-244">First though, create the functionality to process the model outputs.</span></span>

## <a name="create-a-parser-to-post-process-model-outputs"></a><span data-ttu-id="289e7-245">创建分析器来处理模型输出</span><span class="sxs-lookup"><span data-stu-id="289e7-245">Create a parser to post-process model outputs</span></span>

<span data-ttu-id="289e7-246">该模型将图像分割为 `13 x 13` 网格，其中每个网格单元格为 `32px x 32px`。</span><span class="sxs-lookup"><span data-stu-id="289e7-246">The model segments an image into a `13 x 13` grid, where each grid cell is `32px x 32px`.</span></span> <span data-ttu-id="289e7-247">每个网格单元格包含 5 个潜在的对象边界框。</span><span class="sxs-lookup"><span data-stu-id="289e7-247">Each grid cell contains 5 potential object bounding boxes.</span></span> <span data-ttu-id="289e7-248">边界框有 25 个元素：</span><span class="sxs-lookup"><span data-stu-id="289e7-248">A bounding box has  25 elements:</span></span>

![](./media/object-detection-onnx/model-output-description.png)

- <span data-ttu-id="289e7-249">`x`：边界框中心相对于与其关联的网格单元格的 x 位置。</span><span class="sxs-lookup"><span data-stu-id="289e7-249">`x` the x position of the bounding box center relative to the grid cell it's associated with.</span></span>
- <span data-ttu-id="289e7-250">`y`：边界框中心相对于与其关联的网格单元格格的 y 位置。</span><span class="sxs-lookup"><span data-stu-id="289e7-250">`y` the y position of the bounding box center relative to the grid cell it's associated with.</span></span>
- <span data-ttu-id="289e7-251">`w`：边界框的宽度。</span><span class="sxs-lookup"><span data-stu-id="289e7-251">`w` the width of the bounding box.</span></span>
- <span data-ttu-id="289e7-252">`h`：边界框的高度。</span><span class="sxs-lookup"><span data-stu-id="289e7-252">`h` the height of the bounding box.</span></span> 
- <span data-ttu-id="289e7-253">`o`：对象存在于边界框内的置信度值，也称为对象得分。</span><span class="sxs-lookup"><span data-stu-id="289e7-253">`o` the confidence value that an object exists within the bounding box, also known as objectness score.</span></span>
- <span data-ttu-id="289e7-254">模型预测的 20 个类中每个类的 `p1-p20` 类概率。</span><span class="sxs-lookup"><span data-stu-id="289e7-254">`p1-p20` class probabilities for each of the 20 classes predicted by the model.</span></span>

<span data-ttu-id="289e7-255">总之，描述 5 个边界框中每个框的 25 个元素构成了每个网格单元格中包含的 125 个元素。</span><span class="sxs-lookup"><span data-stu-id="289e7-255">In total, the 25 elements describing each of the 5 bounding boxes make up the 125 elements contained in each grid cell.</span></span>

<span data-ttu-id="289e7-256">预训练的 ONNX 模型生成的输出是长度为 `21125` 的浮点数组，表示维度为 `125 x 13 x 13` 的张量元素。</span><span class="sxs-lookup"><span data-stu-id="289e7-256">The output generated by the pre-trained ONNX model is a float array of length `21125`, representing the elements of a tensor with dimensions `125 x 13 x 13`.</span></span> <span data-ttu-id="289e7-257">为了将模型生成的预测转换为张量，需要进行一些后处理工作。</span><span class="sxs-lookup"><span data-stu-id="289e7-257">In order to transform the predictions generated by the model into a tensor, some post-processing work is required.</span></span> <span data-ttu-id="289e7-258">为此，请创建一组类以帮助分析输出。</span><span class="sxs-lookup"><span data-stu-id="289e7-258">To do so, create a set of classes to help parse the output.</span></span>

<span data-ttu-id="289e7-259">向项目中添加一个新目录以组织分析器类集。</span><span class="sxs-lookup"><span data-stu-id="289e7-259">Add a new directory to your project to organize the set of parser classes.</span></span>

1. <span data-ttu-id="289e7-260">在“解决方案资源管理器”中，右键单击项目，然后选择“添加” > “新文件夹”    。</span><span class="sxs-lookup"><span data-stu-id="289e7-260">In **Solution Explorer**, right-click the project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="289e7-261">当新文件夹出现在解决方案资源管理器中时，将其命名为“YoloParser”。</span><span class="sxs-lookup"><span data-stu-id="289e7-261">When the new folder appears in the Solution Explorer, name it "YoloParser".</span></span>

### <a name="create-bounding-boxes-and-dimensions"></a><span data-ttu-id="289e7-262">创建边界框和维度</span><span class="sxs-lookup"><span data-stu-id="289e7-262">Create bounding boxes and dimensions</span></span>

<span data-ttu-id="289e7-263">模型输出的数据包含图像中对象边界框的坐标和维度。</span><span class="sxs-lookup"><span data-stu-id="289e7-263">The data output by the model contains coordinates and dimensions of the bounding boxes of objects within the image.</span></span> <span data-ttu-id="289e7-264">创建维度的基类。</span><span class="sxs-lookup"><span data-stu-id="289e7-264">Create a base class for dimensions.</span></span>

1. <span data-ttu-id="289e7-265">在“解决方案资源管理器”中，右键单击“YoloParser”目录，然后选择“添加” > “新项”     。</span><span class="sxs-lookup"><span data-stu-id="289e7-265">In **Solution Explorer**, right-click the *YoloParser* directory, and then select **Add** > **New Item**.</span></span>
1. <span data-ttu-id="289e7-266">在“添加新项”对话框中，选择“类”并将“名称”字段更改为“DimensionsBase.cs”     。</span><span class="sxs-lookup"><span data-stu-id="289e7-266">In the **Add New Item** dialog box, select **Class** and change the **Name** field to *DimensionsBase.cs*.</span></span> <span data-ttu-id="289e7-267">然后，选择“添加”  按钮。</span><span class="sxs-lookup"><span data-stu-id="289e7-267">Then, select the **Add** button.</span></span>

    <span data-ttu-id="289e7-268">此时，将在代码编辑器中打开 DimensionsBase.cs 文件  。</span><span class="sxs-lookup"><span data-stu-id="289e7-268">The *DimensionsBase.cs* file opens in the code editor.</span></span> <span data-ttu-id="289e7-269">删除所有 `using` 语句和现有类定义。</span><span class="sxs-lookup"><span data-stu-id="289e7-269">Remove all `using` statements and existing class definition.</span></span> 

    <span data-ttu-id="289e7-270">将 `DimensionsBase` 类的以下代码添加到 DimensionsBase.cs 文件中  ：</span><span class="sxs-lookup"><span data-stu-id="289e7-270">Add the following code for the `DimensionsBase` class to the *DimensionsBase.cs* file:</span></span>

    [!code-csharp [DimensionsBaseClass](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/DimensionsBase.cs#L3-L9)]

    <span data-ttu-id="289e7-271">`DimensionsBase` 具有以下 `float` 字段：</span><span class="sxs-lookup"><span data-stu-id="289e7-271">`DimensionsBase` has the following `float` fields:</span></span>

    - <span data-ttu-id="289e7-272">`X`：包含对象沿 x 轴的位置。</span><span class="sxs-lookup"><span data-stu-id="289e7-272">`X` contains the position of the object along the x-axis.</span></span>
    - <span data-ttu-id="289e7-273">`Y`：包含对象沿 y 轴的位置。</span><span class="sxs-lookup"><span data-stu-id="289e7-273">`Y` contains the position of the object along the y-axis.</span></span>
    - <span data-ttu-id="289e7-274">`Height`：包含对象的高度。</span><span class="sxs-lookup"><span data-stu-id="289e7-274">`Height` contains the height of the object.</span></span>
    - <span data-ttu-id="289e7-275">`Width`：包含对象的宽度。</span><span class="sxs-lookup"><span data-stu-id="289e7-275">`Width` contains the width of the object.</span></span>

<span data-ttu-id="289e7-276">接下来，为边界框创建一个类。</span><span class="sxs-lookup"><span data-stu-id="289e7-276">Next, create a class for your bounding boxes.</span></span>

1. <span data-ttu-id="289e7-277">在“解决方案资源管理器”中，右键单击“YoloParser”目录，然后选择“添加” > “新项”     。</span><span class="sxs-lookup"><span data-stu-id="289e7-277">In **Solution Explorer**, right-click the *YoloParser* directory, and then select **Add** > **New Item**.</span></span>
1. <span data-ttu-id="289e7-278">在“添加新项”对话框中，选择“类”并将“名称”字段更改为“YoloBoundingBox.cs”     。</span><span class="sxs-lookup"><span data-stu-id="289e7-278">In the **Add New Item** dialog box, select **Class** and change the **Name** field to *YoloBoundingBox.cs*.</span></span> <span data-ttu-id="289e7-279">然后，选择“添加”  按钮。</span><span class="sxs-lookup"><span data-stu-id="289e7-279">Then, select the **Add** button.</span></span>

    <span data-ttu-id="289e7-280">此时，将在代码编辑器中打开 YoloBoundingBox.cs 文件  。</span><span class="sxs-lookup"><span data-stu-id="289e7-280">The *YoloBoundingBox.cs* file opens in the code editor.</span></span> <span data-ttu-id="289e7-281">将下面的 `using` 语句添加到 YoloBoundingBox.cs 顶部  ：</span><span class="sxs-lookup"><span data-stu-id="289e7-281">Add the following `using` statement to the top of *YoloBoundingBox.cs*:</span></span>

    [!code-csharp [YoloBoundingBoxUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloBoundingBox.cs#L1)]

    <span data-ttu-id="289e7-282">在现有类定义的正上方，添加一个名为 `BoundingBoxDimensions` 的新类定义，该定义继承自 `DimensionsBase` 类以包含相应边界框的维度。</span><span class="sxs-lookup"><span data-stu-id="289e7-282">Just above the existing class definition, add a new class definition called `BoundingBoxDimensions` which inherits from the `DimensionsBase` class to contain the dimensions of the respective bounding box.</span></span>

    [!code-csharp [BoundingBoxDimClass](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloBoundingBox.cs#L5)]

    <span data-ttu-id="289e7-283">删除现有 `YoloBoundingBox` 类定义，并将 `YoloBoundingBox` 类的以下代码添加到 YoloBoundingBox.cs 文件中  ：</span><span class="sxs-lookup"><span data-stu-id="289e7-283">Remove the existing `YoloBoundingBox` class definition and add the following code for the `YoloBoundingBox` class to the *YoloBoundingBox.cs* file:</span></span>

    [!code-csharp [YoloBoundingBoxClass](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloBoundingBox.cs#L7-L21)]
    
    <span data-ttu-id="289e7-284">`YoloBoundingBox` 具有以下字段：</span><span class="sxs-lookup"><span data-stu-id="289e7-284">`YoloBoundingBox` has the following fields:</span></span>

    - <span data-ttu-id="289e7-285">`Dimensions`：包含边界框的维度。</span><span class="sxs-lookup"><span data-stu-id="289e7-285">`Dimensions` contains dimensions of the bounding box.</span></span>
    - <span data-ttu-id="289e7-286">`Label`：包含在边界框内检测到的对象类。</span><span class="sxs-lookup"><span data-stu-id="289e7-286">`Label` contains the class of object detected within the bounding box.</span></span>
    - <span data-ttu-id="289e7-287">`Confidence`：包含类的置信度。</span><span class="sxs-lookup"><span data-stu-id="289e7-287">`Confidence` contains the confidence of the class.</span></span>
    - <span data-ttu-id="289e7-288">`Rect`：包含边界框维度的矩形表示形式。</span><span class="sxs-lookup"><span data-stu-id="289e7-288">`Rect` contains the rectangle representation of the bounding box's dimensions.</span></span>
    - <span data-ttu-id="289e7-289">`BoxColor`：包含与用于在图像上绘制的相应类关联的颜色。</span><span class="sxs-lookup"><span data-stu-id="289e7-289">`BoxColor` contains the color associated with the respective class used to draw on the image.</span></span>

### <a name="create-the-parser"></a><span data-ttu-id="289e7-290">创建分析器</span><span class="sxs-lookup"><span data-stu-id="289e7-290">Create the parser</span></span>

<span data-ttu-id="289e7-291">创建维度和边界框的类之后，接下来创建分析器。</span><span class="sxs-lookup"><span data-stu-id="289e7-291">Now that the classes for dimensions and bounding boxes are created, it's time to create the parser.</span></span>

1. <span data-ttu-id="289e7-292">在“解决方案资源管理器”中，右键单击“YoloParser”目录，然后选择“添加” > “新项”     。</span><span class="sxs-lookup"><span data-stu-id="289e7-292">In **Solution Explorer**, right-click the *YoloParser* directory, and then select **Add** > **New Item**.</span></span>
1. <span data-ttu-id="289e7-293">在“添加新项”对话框中，选择“类”并将“名称”字段更改为“YoloOutputParser.cs”     。</span><span class="sxs-lookup"><span data-stu-id="289e7-293">In the **Add New Item** dialog box, select **Class** and change the **Name** field to *YoloOutputParser.cs*.</span></span> <span data-ttu-id="289e7-294">然后，选择“添加”  按钮。</span><span class="sxs-lookup"><span data-stu-id="289e7-294">Then, select the **Add** button.</span></span>

    <span data-ttu-id="289e7-295">此时，将在代码编辑器中打开 YoloOutputParser.cs 文件  。</span><span class="sxs-lookup"><span data-stu-id="289e7-295">The *YoloOutputParser.cs* file opens in the code editor.</span></span> <span data-ttu-id="289e7-296">将下面的 `using` 语句添加到 YoloOutputParser.cs 顶部  ：</span><span class="sxs-lookup"><span data-stu-id="289e7-296">Add the following `using` statement to the top of *YoloOutputParser.cs*:</span></span>

    [!code-csharp [YoloParserUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L1-L4)]

    <span data-ttu-id="289e7-297">在现有 `YoloOutputParser` 类定义中，添加一个嵌套类，其中包含图像中每个单元格的尺寸。</span><span class="sxs-lookup"><span data-stu-id="289e7-297">Inside the existing `YoloOutputParser` class definition, add a nested class that contains the dimensions of each of the cells in the image.</span></span> <span data-ttu-id="289e7-298">为 `CellDimensions` 类添加以下代码，该类继承自 `YoloOutputParser` 类定义顶部的 `DimensionsBase` 类。</span><span class="sxs-lookup"><span data-stu-id="289e7-298">Add the following code for the `CellDimensions` class which inherits from the `DimensionsBase` class at the top of the `YoloOutputParser` class definition.</span></span>

    [!code-csharp [YoloParserUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L10)]

1. <span data-ttu-id="289e7-299">在 `YoloOutputParser` 类定义中，添加以下常量和字段。</span><span class="sxs-lookup"><span data-stu-id="289e7-299">Inside the `YoloOutputParser` class definition, add the following constant and fields.</span></span>

    [!code-csharp [ParserVarDefinitions](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L12-L21)]    

    - <span data-ttu-id="289e7-300">`ROW_COUNT` 是分割图像的网格中的行数。</span><span class="sxs-lookup"><span data-stu-id="289e7-300">`ROW_COUNT` is the number of rows in the grid the image is divided into.</span></span>
    - <span data-ttu-id="289e7-301">`COL_COUNT` 是分割图像的网格中的列数。</span><span class="sxs-lookup"><span data-stu-id="289e7-301">`COL_COUNT` is the number of columns in the grid the image is divided into.</span></span>
    - <span data-ttu-id="289e7-302">`CHANNEL_COUNT` 是其中一个网格单元格中包含的值的总数。</span><span class="sxs-lookup"><span data-stu-id="289e7-302">`CHANNEL_COUNT` is the total number of values contained in one cell of the grid.</span></span>
    - <span data-ttu-id="289e7-303">`BOXES_PER_CELL` 是单元格中边界框的数量，</span><span class="sxs-lookup"><span data-stu-id="289e7-303">`BOXES_PER_CELL` is the number of bounding boxes in a cell,</span></span>
    - <span data-ttu-id="289e7-304">`BOX_INFO_FEATURE_COUNT` 是框中包含的特征数（x、y、高度、宽度、置信度）。</span><span class="sxs-lookup"><span data-stu-id="289e7-304">`BOX_INFO_FEATURE_COUNT` is the number of features contained within a box (x,y,height,width,confidence).</span></span>
    - <span data-ttu-id="289e7-305">`CLASS_COUNT` 是每个边界框中包含的类预测数。</span><span class="sxs-lookup"><span data-stu-id="289e7-305">`CLASS_COUNT` is the number of class predictions contained in each bounding box.</span></span>
    - <span data-ttu-id="289e7-306">`CELL_WIDTH` 是图像网格中一个单元格的宽度。</span><span class="sxs-lookup"><span data-stu-id="289e7-306">`CELL_WIDTH` is the width of one cell in the image grid.</span></span>
    - <span data-ttu-id="289e7-307">`CELL_HEIGHT` 是图像网格中一个单元格的高度。</span><span class="sxs-lookup"><span data-stu-id="289e7-307">`CELL_HEIGHT` is the height of one cell in the image grid.</span></span>
    - <span data-ttu-id="289e7-308">`channelStride` 是网格中当前单元格的起始位置。</span><span class="sxs-lookup"><span data-stu-id="289e7-308">`channelStride` is the starting position of the current cell in the grid.</span></span>


    <span data-ttu-id="289e7-309">当模型对图像进行评分时，它会将 `416px x 416px` 输入划分为大小为 `13 x 13` 的单元格网格。</span><span class="sxs-lookup"><span data-stu-id="289e7-309">When the model scores an image, it divides the `416px x 416px`input into a grid of cells the size of `13 x 13`.</span></span> <span data-ttu-id="289e7-310">每个单元格都包含 `32px x 32px`。</span><span class="sxs-lookup"><span data-stu-id="289e7-310">Each cell contains is `32px x 32px`.</span></span> <span data-ttu-id="289e7-311">在每个单元格内，有 5 个边界框，每个边框包含 5 个特征（x、y、宽度、高度、置信度）。</span><span class="sxs-lookup"><span data-stu-id="289e7-311">Within each cell, there are 5 bounding boxes each containing 5 features (x, y, width, height, confidence).</span></span> <span data-ttu-id="289e7-312">此外，每个边界框包含每个类的概率，在这种情况下为 20。</span><span class="sxs-lookup"><span data-stu-id="289e7-312">In addition, each bounding box contains the probability of each of the classes which in this case is 20.</span></span> <span data-ttu-id="289e7-313">因此，每个单元包含 125 条信息（5 个特征 + 20 个类概率）。</span><span class="sxs-lookup"><span data-stu-id="289e7-313">Therefore, each cell contains 125 pieces of information (5 features + 20 class probabilities).</span></span> 

<span data-ttu-id="289e7-314">为所有 5 个边界框在 `channelStride` 下创建的定位点列表：</span><span class="sxs-lookup"><span data-stu-id="289e7-314">Create a list of anchors below `channelStride` for all 5 bounding boxes:</span></span>

[!code-csharp [ParserAnchors](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L23-L26)]   

<span data-ttu-id="289e7-315">定位点是边界框的预定义的高度和宽度比例。</span><span class="sxs-lookup"><span data-stu-id="289e7-315">Anchors are pre-defined height and width ratios of bounding boxes.</span></span> <span data-ttu-id="289e7-316">模型检测到的大多数对象或类都具有相似的比例。</span><span class="sxs-lookup"><span data-stu-id="289e7-316">Most object or classes detected by a model have similar ratios.</span></span> <span data-ttu-id="289e7-317">这在创建边界框时非常有用。</span><span class="sxs-lookup"><span data-stu-id="289e7-317">This is valuable when it comes to creating bounding boxes.</span></span> <span data-ttu-id="289e7-318">不是预测边界框，而是计算预定义维度的偏移量，因此减少了预测边界框所需的计算量。</span><span class="sxs-lookup"><span data-stu-id="289e7-318">Instead of predicting the bounding boxes, the offset from the pre-defined dimensions is calculated therefore reducing the computation required to predict the bounding box.</span></span> <span data-ttu-id="289e7-319">通常，这些定位点比例是基于所使用的数据集计算的。</span><span class="sxs-lookup"><span data-stu-id="289e7-319">Typically these anchor ratios are calculated based on the dataset used.</span></span> <span data-ttu-id="289e7-320">在这种情况下，由于数据集是已知的且值已预先计算，因此可以硬编码定位点。</span><span class="sxs-lookup"><span data-stu-id="289e7-320">In this case because the dataset is known and the values have been pre-computed, the anchors can be hard-coded.</span></span>

<span data-ttu-id="289e7-321">接下来，定义模型将预测的标签或类。</span><span class="sxs-lookup"><span data-stu-id="289e7-321">Next, define the labels or classes that the model will predict.</span></span> <span data-ttu-id="289e7-322">该模型预测了 20 个类，它们是原始 YOLOv2 模型预测的类总数的子集。</span><span class="sxs-lookup"><span data-stu-id="289e7-322">This model predicts 20 classes which is a subset of the total number of classes predicted by the original YOLOv2 model.</span></span>

<span data-ttu-id="289e7-323">在 `anchors` 下面添加标签列表。</span><span class="sxs-lookup"><span data-stu-id="289e7-323">Add your list of labels below the `anchors`.</span></span> 

[!code-csharp [ParserLabels](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L28-L34)]

<span data-ttu-id="289e7-324">每个类都有相关联的颜色。</span><span class="sxs-lookup"><span data-stu-id="289e7-324">There are colors associated with each of the classes.</span></span> <span data-ttu-id="289e7-325">在 `labels` 下分配类的颜色：</span><span class="sxs-lookup"><span data-stu-id="289e7-325">Assign your class colors below your `labels`:</span></span>

[!code-csharp [ParserColors](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L36-L59)]

### <a name="create-helper-functions"></a><span data-ttu-id="289e7-326">创建帮助程序函数</span><span class="sxs-lookup"><span data-stu-id="289e7-326">Create helper functions</span></span>

<span data-ttu-id="289e7-327">后处理阶段涉及一系列步骤。</span><span class="sxs-lookup"><span data-stu-id="289e7-327">There are a series of steps involved in the post-processing phase.</span></span> <span data-ttu-id="289e7-328">为此，可以使用几种帮助程序方法。</span><span class="sxs-lookup"><span data-stu-id="289e7-328">To help with that, several helper methods can be employed.</span></span> 

<span data-ttu-id="289e7-329">分析器使用的帮助程序方法是：</span><span class="sxs-lookup"><span data-stu-id="289e7-329">The helper methods used in by the parser are:</span></span>

- <span data-ttu-id="289e7-330">`Sigmoid` 应用 sigmoid 函数，该函数输出介于 0 和 1 之间的数字。</span><span class="sxs-lookup"><span data-stu-id="289e7-330">`Sigmoid` applies the sigmoid function that outputs a number between 0 and 1.</span></span>
- <span data-ttu-id="289e7-331">`Softmax` 将输入向量规范化为概率分布。</span><span class="sxs-lookup"><span data-stu-id="289e7-331">`Softmax` normalizes an input vector into a probability distribution.</span></span>
- <span data-ttu-id="289e7-332">`GetOffset` 将一维模型输出中的元素映射到 `125 x 13 x 13` 张量中的对应位置。</span><span class="sxs-lookup"><span data-stu-id="289e7-332">`GetOffset` maps elements in the one-dimensional model output to the corresponding position in a `125 x 13 x 13` tensor.</span></span>
- <span data-ttu-id="289e7-333">`ExtractBoundingBoxes` 使用模型输出中的 `GetOffset` 方法提取边界框维度。</span><span class="sxs-lookup"><span data-stu-id="289e7-333">`ExtractBoundingBoxes` extracts the bounding box dimensions using the `GetOffset` method from the model output.</span></span>
- <span data-ttu-id="289e7-334">`GetConfidence` 提取置信度值，该值表示模型是否检测到对象，并使用 `Sigmoid` 函数将其转换为百分比。</span><span class="sxs-lookup"><span data-stu-id="289e7-334">`GetConfidence` extracts the confidence value which states how sure the model is that it has detected an object and uses the `Sigmoid` function to turn it into a percentage.</span></span>
- <span data-ttu-id="289e7-335">`MapBoundingBoxToCell` 使用边界框维度并将它们映射到图像中的相应单元格。</span><span class="sxs-lookup"><span data-stu-id="289e7-335">`MapBoundingBoxToCell` uses the bounding box dimensions and maps them onto its respective cell within the image.</span></span>
- <span data-ttu-id="289e7-336">`ExtractClasses` 使用 `GetOffset` 方法从模型输出中提取边界框的类预测，并使用 `Softmax` 方法将它们转换为概率分布。</span><span class="sxs-lookup"><span data-stu-id="289e7-336">`ExtractClasses` extracts the class predictions for the bounding box from the model output using the `GetOffset` method and turns them into a probability distribution using the `Softmax` method.</span></span>
- <span data-ttu-id="289e7-337">`GetTopResult` 从具有最高概率的预测类列表中选择类。</span><span class="sxs-lookup"><span data-stu-id="289e7-337">`GetTopResult` selects the class from the list of predicted classes with the highest probability.</span></span>
- <span data-ttu-id="289e7-338">`IntersectionOverUnion` 筛选具有较低概率的重叠边界框。</span><span class="sxs-lookup"><span data-stu-id="289e7-338">`IntersectionOverUnion` filters overlapping bounding boxes with lower probabilities.</span></span>

<span data-ttu-id="289e7-339">在 `classColors` 列表下面添加所有帮助程序方法的代码。</span><span class="sxs-lookup"><span data-stu-id="289e7-339">Add the code for all the helper methods below your list of `classColors`.</span></span>

[!code-csharp [ParserHelperMethods](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L61-L151)]

<span data-ttu-id="289e7-340">定义完所有帮助程序方法之后，即可使用它们来处理模型输出。</span><span class="sxs-lookup"><span data-stu-id="289e7-340">Once you have defined all of the helper methods, it's time to use them to process the model output.</span></span>

<span data-ttu-id="289e7-341">在 `IntersectionOverUnion` 方法下面，创建 `ParseOutputs` 方法以处理模型生成的输出。</span><span class="sxs-lookup"><span data-stu-id="289e7-341">Below the `IntersectionOverUnion` method, create the `ParseOutputs` method to process the output generated by the model.</span></span>

```csharp
public IList<YoloBoundingBox> ParseOutputs(float[] yoloModelOutputs, float threshold = .3F)
{

}
```
    
<span data-ttu-id="289e7-342">创建一个列表来存储边界框并在 `ParseOutputs` 方法中定义变量。</span><span class="sxs-lookup"><span data-stu-id="289e7-342">Create a list to store your bounding boxes and define variables inside the `ParseOutputs` method.</span></span>

[!code-csharp [BBoxList](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L155)]

<span data-ttu-id="289e7-343">每个图像都被分成一个 `13 x 13` 单元格的网格。</span><span class="sxs-lookup"><span data-stu-id="289e7-343">Each image is divided into a grid of `13 x 13` cells.</span></span> <span data-ttu-id="289e7-344">每个单元格包含五个边界框。</span><span class="sxs-lookup"><span data-stu-id="289e7-344">Each cell contains five bounding boxes.</span></span> <span data-ttu-id="289e7-345">在 `boxes` 变量下方，添加代码以处理每个单元格中的所有框。</span><span class="sxs-lookup"><span data-stu-id="289e7-345">Below the `boxes` variable, add code to process all of the boxes in each of the cells.</span></span>

```csharp
for (int row = 0; row < ROW_COUNT; row++)
{
    for (int column = 0; column < COL_COUNT; column++)
    {
        for (int box = 0; box < BOXES_PER_CELL; box++)
        {

        }
    }
}
```

<span data-ttu-id="289e7-346">在最内层循环内，计算一维模型输出中当前框的起始位置。</span><span class="sxs-lookup"><span data-stu-id="289e7-346">Inside the inner-most loop, calculate the starting position of the current box within the one-dimensional model output.</span></span>

[!code-csharp [ChannelDef](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L163)]

<span data-ttu-id="289e7-347">在其下方，使用 `ExtractBoundingBoxDimensions` 方法获取当前边界框的维度。</span><span class="sxs-lookup"><span data-stu-id="289e7-347">Directly below that, use the `ExtractBoundingBoxDimensions` method to get the dimensions of the current bounding box.</span></span>

[!code-csharp [GetBBoxDims](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L165)]

<span data-ttu-id="289e7-348">然后，使用 `GetConfidence` 方法获取当前边界框的置信度。</span><span class="sxs-lookup"><span data-stu-id="289e7-348">Then, use the `GetConfidence` method to get the confidence for the current bounding box.</span></span>

[!code-csharp [GetConfidence](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L167)]

<span data-ttu-id="289e7-349">之后，使用 `MapBoundingBoxToCell` 方法将当前边界框映射到正在处理的当前单元格。</span><span class="sxs-lookup"><span data-stu-id="289e7-349">After that, use the `MapBoundingBoxToCell` method to map the current bounding box to the current cell being processed.</span></span>

[!code-csharp [MapBoundingBoxes](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L169)]

<span data-ttu-id="289e7-350">在进行任何进一步的处理之前，请检查置信值是否大于提供的阈值。</span><span class="sxs-lookup"><span data-stu-id="289e7-350">Before doing any further processing, check whether your confidence value is greater than the threshold provided.</span></span> <span data-ttu-id="289e7-351">如果没有，请处理下一个边界框。</span><span class="sxs-lookup"><span data-stu-id="289e7-351">If not, process the next bounding box.</span></span>

[!code-csharp [CheckThreshold1](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L171-L172)]

<span data-ttu-id="289e7-352">否则，继续处理输出。</span><span class="sxs-lookup"><span data-stu-id="289e7-352">Otherwise, continue processing the output.</span></span> <span data-ttu-id="289e7-353">下一步是使用 `ExtractClasses` 方法获取当前边界框的预测类的概率分布。</span><span class="sxs-lookup"><span data-stu-id="289e7-353">The next step is to get the probability distribution of the predicted classes for the current bounding box using the `ExtractClasses` method.</span></span>

[!code-csharp [ExtractClasses](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L174)]

<span data-ttu-id="289e7-354">然后，使用 `GetTopResult` 方法获取当前框概率最高的类的值和索引，并计算其得分。</span><span class="sxs-lookup"><span data-stu-id="289e7-354">Then, use the `GetTopResult` method to get the value and index of the class with the highest probability for the current box and compute its score.</span></span>

[!code-csharp [GetTopResult](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L176-L177)]

<span data-ttu-id="289e7-355">再次使用 `topScore` 只保留那些超过指定阈值的边界框。</span><span class="sxs-lookup"><span data-stu-id="289e7-355">Use the `topScore` to once again keep only those bounding boxes that are above the specified threshold.</span></span>

[!code-csharp [CheckThreshold2](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L179-L180)]

<span data-ttu-id="289e7-356">最后，如果当前边界框超过阈值，请创建一个新的 `BoundingBox` 对象并将其添加到 `boxes` 列表中。</span><span class="sxs-lookup"><span data-stu-id="289e7-356">Finally, if the current bounding box exceeds the threshold, create a new `BoundingBox` object and add it to the `boxes` list.</span></span>

[!code-csharp [AddBBoxToList](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L182-L194)]

<span data-ttu-id="289e7-357">处理完图像中的所有单元格后，返回 `boxes` 列表。</span><span class="sxs-lookup"><span data-stu-id="289e7-357">Once all cells in the image have been processed, return the `boxes` list.</span></span> <span data-ttu-id="289e7-358">在 `ParseOutputs` 方法的最外层 for 循环下面添加以下 return 语句。</span><span class="sxs-lookup"><span data-stu-id="289e7-358">Add the following return statement below the outer-most for-loop in the `ParseOutputs` method.</span></span>

[!code-csharp [ReturnBoxes](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L198)]

### <a name="filter-overlapping-boxes"></a><span data-ttu-id="289e7-359">筛选重叠框</span><span class="sxs-lookup"><span data-stu-id="289e7-359">Filter overlapping boxes</span></span>

<span data-ttu-id="289e7-360">从模型输出中提取所有高置信度的边界框之后，接下来需要进行额外的筛选以移除重叠的图像。</span><span class="sxs-lookup"><span data-stu-id="289e7-360">Now that all of the highly confident bounding boxes have been extracted from the model output, additional filtering needs to be done to remove overlapping images.</span></span> <span data-ttu-id="289e7-361">在 `ParseOutputs` 方法下添加一个名为 `FilterBoundingBoxes` 的方法：</span><span class="sxs-lookup"><span data-stu-id="289e7-361">Add a method called `FilterBoundingBoxes` below the `ParseOutputs` method:</span></span>

```csharp
public IList<YoloBoundingBox> FilterBoundingBoxes(IList<YoloBoundingBox> boxes, int limit, float threshold)
{

}
```

<span data-ttu-id="289e7-362">在 `FilterBoundingBoxes` 方法中，首先创建一个等于检测到的框大小的数组，并将所有插槽标记为“活动”或“待处理”。</span><span class="sxs-lookup"><span data-stu-id="289e7-362">Inside the `FilterBoundingBoxes` method, start off by creating an array equal to the size of detected boxes and marking all slots as active or ready for processing.</span></span>

[!code-csharp [InitActiveSlots](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L203-L207)]

<span data-ttu-id="289e7-363">然后，根据置信度按降序对包含边界框的列表进行排序。</span><span class="sxs-lookup"><span data-stu-id="289e7-363">Then, sort the list containing your bounding boxes in descending order based on confidence.</span></span>

[!code-csharp [SortBoxes](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L209-L211)]

<span data-ttu-id="289e7-364">之后，创建一个列表来保存筛选的结果。</span><span class="sxs-lookup"><span data-stu-id="289e7-364">After that, create a list to hold the filtered results.</span></span>

[!code-csharp [InitFilterResult](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L213)]

<span data-ttu-id="289e7-365">通过遍历每个边界框，开始处理每个边界框。</span><span class="sxs-lookup"><span data-stu-id="289e7-365">Begin processing each bounding box by iterating over each of the bounding boxes.</span></span>

```csharp
for (int i = 0; i < boxes.Count; i++)
{

}
```

<span data-ttu-id="289e7-366">在此 for 循环内部，检查是否可以处理当前边界框。</span><span class="sxs-lookup"><span data-stu-id="289e7-366">Inside of this for-loop, check whether the current bounding box can be processed.</span></span>

```csharp
if (isActiveBoxes[i])
{
    
}
```

<span data-ttu-id="289e7-367">如果可以，请将边界框添加到结果列表中。</span><span class="sxs-lookup"><span data-stu-id="289e7-367">If so, add the bounding box to the list of results.</span></span> <span data-ttu-id="289e7-368">如果结果超出了要提取的框的指定限制，则中断循环。</span><span class="sxs-lookup"><span data-stu-id="289e7-368">If the results exceeds the specified limit of boxes to be extracted, break out of the loop.</span></span> <span data-ttu-id="289e7-369">在 if 语句中添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="289e7-369">Add the following code inside the if-statement.</span></span>

[!code-csharp [AddFirstBBoxToResults](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L219-L223)]

<span data-ttu-id="289e7-370">否则，请查看相邻的边界框。</span><span class="sxs-lookup"><span data-stu-id="289e7-370">Otherwise, look at the adjacent bounding boxes.</span></span> <span data-ttu-id="289e7-371">在框限制检查下面添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="289e7-371">Add the following code below the box limit check.</span></span>

```csharp
for (var j = i + 1; j < boxes.Count; j++)
{

}
```

<span data-ttu-id="289e7-372">与第一个框一样，如果相邻框处于活动或待处理状态，请使用 `IntersectionOverUnion` 方法检查第一个框和第二个框是否超出指定的阈值。</span><span class="sxs-lookup"><span data-stu-id="289e7-372">Like the first box, if the adjacent box is active or ready to be processed, use the `IntersectionOverUnion` method to check whether the first box and the second box exceed the specified threshold.</span></span> <span data-ttu-id="289e7-373">将以下代码添加到最内层的 for 循环中。</span><span class="sxs-lookup"><span data-stu-id="289e7-373">Add the following code to your inner-most for-loop.</span></span>

[!code-csharp [IOUBBox](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L227-L239)]

<span data-ttu-id="289e7-374">在检查相邻边界框的最内部 for 循环之外，查看是否有任何剩余的边界框要处理。</span><span class="sxs-lookup"><span data-stu-id="289e7-374">Outside of the inner-most for-loop that checks adjacent bounding boxes, see whether there are any remaining bounding boxes to be processed.</span></span> <span data-ttu-id="289e7-375">如果没有，则中断外部 for 循环。</span><span class="sxs-lookup"><span data-stu-id="289e7-375">If not, break out of the outer for-loop.</span></span>

[!code-csharp [CheckActiveSlots](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L242-L243)]

<span data-ttu-id="289e7-376">最后，在 `FilterBoundingBoxes` 方法的初始 for 循环之外，返回结果：</span><span class="sxs-lookup"><span data-stu-id="289e7-376">Finally, outside of the initial for-loop of the `FilterBoundingBoxes` method, return the results:</span></span>

[!code-csharp [ReturnFilteredBBox](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/YoloParser/YoloOutputParser.cs#L246)]

<span data-ttu-id="289e7-377">很好！</span><span class="sxs-lookup"><span data-stu-id="289e7-377">Great!</span></span> <span data-ttu-id="289e7-378">现在可以将此代码与模型配合使用，以进行评分。</span><span class="sxs-lookup"><span data-stu-id="289e7-378">Now it's time to use this code along with the model for scoring.</span></span>

## <a name="use-the-model-for-scoring"></a><span data-ttu-id="289e7-379">使用该模型进行评分</span><span class="sxs-lookup"><span data-stu-id="289e7-379">Use the model for scoring</span></span>

<span data-ttu-id="289e7-380">就像后处理一样，评分步骤也有几个步骤。</span><span class="sxs-lookup"><span data-stu-id="289e7-380">Just like with post-processing, there are a few steps in the scoring steps.</span></span> <span data-ttu-id="289e7-381">为此，请向项目添加包含评分逻辑的类。</span><span class="sxs-lookup"><span data-stu-id="289e7-381">To help with this, add a class that will contain the scoring logic to your project.</span></span>

1. <span data-ttu-id="289e7-382">在“解决方案资源管理器”  中，右键单击项目，然后选择“添加”   > “新项”  。</span><span class="sxs-lookup"><span data-stu-id="289e7-382">In **Solution Explorer**, right-click the project, and then select **Add** > **New Item**.</span></span>
1. <span data-ttu-id="289e7-383">在“添加新项”对话框中，选择“类”并将“名称”字段更改为“OnnxModelScorer.cs”     。</span><span class="sxs-lookup"><span data-stu-id="289e7-383">In the **Add New Item** dialog box, select **Class** and change the **Name** field to *OnnxModelScorer.cs*.</span></span> <span data-ttu-id="289e7-384">然后，选择“添加”  按钮。</span><span class="sxs-lookup"><span data-stu-id="289e7-384">Then, select the **Add** button.</span></span>

    <span data-ttu-id="289e7-385">此时，将在代码编辑器中打开 OnnxModelScorer.cs 文件  。</span><span class="sxs-lookup"><span data-stu-id="289e7-385">The *OnnxModelScorer.cs* file opens in the code editor.</span></span> <span data-ttu-id="289e7-386">将下面的 `using` 语句添加到 OnnxModelScorer.cs 顶部  ：</span><span class="sxs-lookup"><span data-stu-id="289e7-386">Add the following `using` statement to the top of *OnnxModelScorer.cs*:</span></span>

    [!code-csharp [ScorerUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L1-L7)]

    <span data-ttu-id="289e7-387">在 `OnnxModelScorer` 类定义中，添加以下变量。</span><span class="sxs-lookup"><span data-stu-id="289e7-387">Inside the `OnnxModelScorer` class definition, add the following variables.</span></span>

    [!code-csharp [InitScorerVars](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L13-L17)]

    <span data-ttu-id="289e7-388">在其下方，为 `OnnxModelScorer` 类创建一个构造函数，用于初始化先前定义的变量。</span><span class="sxs-lookup"><span data-stu-id="289e7-388">Directly below that, create a constructor for the `OnnxModelScorer` class that will initialize the previously defined variables.</span></span>

    [!code-csharp [ScorerCtor](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L19-L24)]

    <span data-ttu-id="289e7-389">创建构造函数之后，定义两个结构，其中包含与图像和模型设置相关的变量。</span><span class="sxs-lookup"><span data-stu-id="289e7-389">Once you have created the constructor, define a couple of structs that contain variables related to the image and model settings.</span></span> <span data-ttu-id="289e7-390">创建一个名为 `ImageNetSettings` 的结构，以包含预期作为模型输入的高度和宽度。</span><span class="sxs-lookup"><span data-stu-id="289e7-390">Create a struct called `ImageNetSettings` to contain the height and width expected as input for the model.</span></span>

    [!code-csharp [ImageNetSettingStruct](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L26-L30)]

    <span data-ttu-id="289e7-391">然后，创建另一个名为 `TinyYoloModelSettings` 的结构，其中包含模型的输入层和输出层名称。</span><span class="sxs-lookup"><span data-stu-id="289e7-391">After that, create another struct called `TinyYoloModelSettings` which contains the names of the input and output layers of the model.</span></span> <span data-ttu-id="289e7-392">若要可视化模型的输入层和输出层名称，可以使用 [Netron](https://github.com/lutzroeder/netron) 之类的工具。</span><span class="sxs-lookup"><span data-stu-id="289e7-392">To visualize the name of the input and output layers of the model, you can use a tool like [Netron](https://github.com/lutzroeder/netron).</span></span>

    [!code-csharp [YoloSettingsStruct](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L32-L43)]

    <span data-ttu-id="289e7-393">接下来，创建用于评分的第一组方法。</span><span class="sxs-lookup"><span data-stu-id="289e7-393">Next, create the first set of methods use for scoring.</span></span> <span data-ttu-id="289e7-394">在 `OnnxModelScorer` 类中创建 `LoadModel` 方法。</span><span class="sxs-lookup"><span data-stu-id="289e7-394">Create the `LoadModel` method inside of your `OnnxModelScorer` class.</span></span>

    ```csharp
    private ITransformer LoadModel(string modelLocation)
    {

    }
    ```

    <span data-ttu-id="289e7-395">在 `LoadModel` 方法中，添加以下代码以进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="289e7-395">Inside the `LoadModel` method, add the following code for logging.</span></span>

    [!code-csharp [LoadModelLog](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L47-L49)]

    <span data-ttu-id="289e7-396">ML.NET 管道通常希望在调用 [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit*) 方法时对数据进行操作。</span><span class="sxs-lookup"><span data-stu-id="289e7-396">ML.NET pipelines typically expect data to operate on when the [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit*) method is called.</span></span> <span data-ttu-id="289e7-397">在这种情况下，将使用类似于训练的过程。</span><span class="sxs-lookup"><span data-stu-id="289e7-397">In this case, a process similar to training will be used.</span></span> <span data-ttu-id="289e7-398">但是，由于没有进行实际训练，因此可以使用空的 [`IDataView`](xref:Microsoft.ML.IDataView)。</span><span class="sxs-lookup"><span data-stu-id="289e7-398">However, because no actual training is happening, it is acceptable to use an empty [`IDataView`](xref:Microsoft.ML.IDataView).</span></span> <span data-ttu-id="289e7-399">使用空列表为管道创建新的 [`IDataView`](xref:Microsoft.ML.IDataView)。</span><span class="sxs-lookup"><span data-stu-id="289e7-399">Create a new [`IDataView`](xref:Microsoft.ML.IDataView) for the pipeline from an empty list.</span></span>

    [!code-csharp [LoadEmptyIDV](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L52)]    

    <span data-ttu-id="289e7-400">在此之后，定义管道。</span><span class="sxs-lookup"><span data-stu-id="289e7-400">Below that, define the pipeline.</span></span> <span data-ttu-id="289e7-401">管道将包含四个转换。</span><span class="sxs-lookup"><span data-stu-id="289e7-401">The pipeline will consist of four transforms.</span></span>

    - <span data-ttu-id="289e7-402">[`LoadImages`](xref:Microsoft.ML.ImageEstimatorsCatalog.LoadImages*) 将图片作为位图加载。</span><span class="sxs-lookup"><span data-stu-id="289e7-402">[`LoadImages`](xref:Microsoft.ML.ImageEstimatorsCatalog.LoadImages*) loads the image as a Bitmap.</span></span>
    - <span data-ttu-id="289e7-403">[`ResizeImages`](xref:Microsoft.ML.ImageEstimatorsCatalog.ResizeImages*) 将图片重新调整为指定的大小（在本例中为 `416 x 416`）。</span><span class="sxs-lookup"><span data-stu-id="289e7-403">[`ResizeImages`](xref:Microsoft.ML.ImageEstimatorsCatalog.ResizeImages*) rescales the image to the size specified (in this case, `416 x 416`).</span></span>
    - <span data-ttu-id="289e7-404">[`ExtractPixels`](xref:Microsoft.ML.ImageEstimatorsCatalog.ExtractPixels*) 将图像的像素表示形式从位图更改为数字向量。</span><span class="sxs-lookup"><span data-stu-id="289e7-404">[`ExtractPixels`](xref:Microsoft.ML.ImageEstimatorsCatalog.ExtractPixels*) changes the pixel representation of the image from a Bitmap to a numerical vector.</span></span>
    - <span data-ttu-id="289e7-405">[`ApplyOnnxModel`](xref:Microsoft.ML.OnnxCatalog.ApplyOnnxModel*) 加载 ONNX 模型并使用它对提供的数据进行评分。</span><span class="sxs-lookup"><span data-stu-id="289e7-405">[`ApplyOnnxModel`](xref:Microsoft.ML.OnnxCatalog.ApplyOnnxModel*) loads the ONNX model and uses it to score on the data provided.</span></span>

    <span data-ttu-id="289e7-406">在 `data` 变量下面的 `LoadModel` 方法中定义管道。</span><span class="sxs-lookup"><span data-stu-id="289e7-406">Define your pipeline in the `LoadModel` method below the `data` variable.</span></span>

    [!code-csharp [ScoringPipeline](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L55-L58)]

    <span data-ttu-id="289e7-407">现在，可以实例化模型以进行评分。</span><span class="sxs-lookup"><span data-stu-id="289e7-407">Now it's time to instantiate the model for scoring.</span></span> <span data-ttu-id="289e7-408">调用管道上的 [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit*) 方法并将其返回以进行进一步处理。</span><span class="sxs-lookup"><span data-stu-id="289e7-408">Call the [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit*) method on the pipeline and return it for further processing.</span></span>

    [!code-csharp [FitReturnModel](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L61-L63)]

<span data-ttu-id="289e7-409">加载模型后，可将其用来进行预测。</span><span class="sxs-lookup"><span data-stu-id="289e7-409">Once the model is loaded, it can then be used to make predictions.</span></span> <span data-ttu-id="289e7-410">若要简化该过程，请在 `LoadModel` 方法下创建一个名为 `PredictDataUsingModel` 的方法。</span><span class="sxs-lookup"><span data-stu-id="289e7-410">To facilitate that process, create a method called `PredictDataUsingModel` below the `LoadModel` method.</span></span>

```csharp
private IEnumerable<float[]> PredictDataUsingModel(IDataView testData, ITransformer model)
{

}
```

<span data-ttu-id="289e7-411">在 `PredictDataUsingModel` 中，添加以下代码以进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="289e7-411">Inside the `PredictDataUsingModel`, add the following code for logging.</span></span>

[!code-csharp [PredictDataLog](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L68-L71)]

<span data-ttu-id="289e7-412">然后，使用 [`Transform`](xref:Microsoft.ML.ITransformer.Transform*) 方法对数据进行评分。</span><span class="sxs-lookup"><span data-stu-id="289e7-412">Then, use the [`Transform`](xref:Microsoft.ML.ITransformer.Transform*) method to score the data.</span></span>

[!code-csharp [ScoreImages](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L73)]

<span data-ttu-id="289e7-413">提取预测的概率并将其返回以进行其他处理。</span><span class="sxs-lookup"><span data-stu-id="289e7-413">Extract the predicted probabilities and return them for additional processing.</span></span>

[!code-csharp [ReturnModelOutput](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L75-L77)]

<span data-ttu-id="289e7-414">现在已经设置了两个步骤，将它们合并为一个方法。</span><span class="sxs-lookup"><span data-stu-id="289e7-414">Now that both steps are set up, combine them into a single method.</span></span> <span data-ttu-id="289e7-415">在 `PredictDataUsingModel` 方法下面，添加一个名为 `Score` 的新方法。</span><span class="sxs-lookup"><span data-stu-id="289e7-415">Below the `PredictDataUsingModel` method, add a new method called `Score`.</span></span>

[!code-csharp [ScoreMethod](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/OnnxModelScorer.cs#L80-L85)]

<span data-ttu-id="289e7-416">马上就大功告成了！</span><span class="sxs-lookup"><span data-stu-id="289e7-416">Almost there!</span></span> <span data-ttu-id="289e7-417">现在可以将其全部投入使用。</span><span class="sxs-lookup"><span data-stu-id="289e7-417">Now it's time to put it all to use.</span></span>

## <a name="detect-objects"></a><span data-ttu-id="289e7-418">检测对象</span><span class="sxs-lookup"><span data-stu-id="289e7-418">Detect objects</span></span>

<span data-ttu-id="289e7-419">现在所有设置都已完成，可以检测一些对象。</span><span class="sxs-lookup"><span data-stu-id="289e7-419">Now that all of the setup is complete, it's time to detect some objects.</span></span> <span data-ttu-id="289e7-420">在 Program.cs 类的 `Main` 方法内，添加 try-catch 语句  。</span><span class="sxs-lookup"><span data-stu-id="289e7-420">Inside the `Main` method of your *Program.cs* class, add a try-catch statement.</span></span>

```csharp
try
{

}
catch (Exception ex)
{
    Console.WriteLine(ex.ToString());
}
```

<span data-ttu-id="289e7-421">在 `try` 块内部，开始实现对象检测逻辑。</span><span class="sxs-lookup"><span data-stu-id="289e7-421">Inside of the `try` block, start implementing the object detection logic.</span></span> <span data-ttu-id="289e7-422">首先，将数据加载到 [`IDataView`](xref:Microsoft.ML.IDataView) 中。</span><span class="sxs-lookup"><span data-stu-id="289e7-422">First, load the data into an [`IDataView`](xref:Microsoft.ML.IDataView).</span></span>

[!code-csharp [LoadData](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L29-L30)]

<span data-ttu-id="289e7-423">然后，创建 `OnnxModelScorer` 的实例，并使用它对加载的数据进行评分。</span><span class="sxs-lookup"><span data-stu-id="289e7-423">Then, create an instance of `OnnxModelScorer` and use it to score the loaded data.</span></span>

[!code-csharp [ScoreData](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L33-L36)]

<span data-ttu-id="289e7-424">现在执行后处理步骤。</span><span class="sxs-lookup"><span data-stu-id="289e7-424">Now it's time for the post-processing step.</span></span> <span data-ttu-id="289e7-425">创建 `YoloOutputParser` 的实例并使用它来处理模型输出。</span><span class="sxs-lookup"><span data-stu-id="289e7-425">Create an instance of `YoloOutputParser` and use it to process the model output.</span></span>

[!code-csharp [ParsePredictions](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L39-L44)]

<span data-ttu-id="289e7-426">处理完模型输出后，便可以在图像上绘制边界框。</span><span class="sxs-lookup"><span data-stu-id="289e7-426">Once the model output has been processed, it's time to draw the bounding boxes on the images.</span></span> <span data-ttu-id="289e7-427">创建 for 循环来遍历每个评分图像。</span><span class="sxs-lookup"><span data-stu-id="289e7-427">Create a for-loop to iterate over each of the scored images.</span></span>

```csharp
for (var i = 0; i < images.Count(); i++)
{

}
```

<span data-ttu-id="289e7-428">在 for 循环内部，获取图像文件的名称以及与之关联的边界框。</span><span class="sxs-lookup"><span data-stu-id="289e7-428">Inside of the for-loop, get the name of the image file and the bounding boxes associated with it.</span></span>

[!code-csharp [GetImageFileName](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L49-L50)]

<span data-ttu-id="289e7-429">在此之后，使用 `DrawBoundingBox` 方法在图像上绘制边界框。</span><span class="sxs-lookup"><span data-stu-id="289e7-429">Below that, use the `DrawBoundingBox` method to draw the bounding boxes on the image.</span></span>

[!code-csharp [DrawBBoxes](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L52)]

<span data-ttu-id="289e7-430">最后，使用 `LogDetectedObjects` 方法添加一些日志记录逻辑。</span><span class="sxs-lookup"><span data-stu-id="289e7-430">Lastly, add some logging logic with the `LogDetectedObjects` method.</span></span>

[!code-csharp [LogPredictionsOutput](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L54)]


<span data-ttu-id="289e7-431">在 try-catch 语句之后，添加其他逻辑以指示进程已完成运行。</span><span class="sxs-lookup"><span data-stu-id="289e7-431">After the try-catch statement, add additional logic to indicate the process is done running.</span></span>

[!code-csharp [EndProcessLog](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx/ObjectDetectionConsoleApp/Program.cs#L62-L63)]

<span data-ttu-id="289e7-432">就这么简单！</span><span class="sxs-lookup"><span data-stu-id="289e7-432">That's it!</span></span> 

## <a name="results"></a><span data-ttu-id="289e7-433">结果</span><span class="sxs-lookup"><span data-stu-id="289e7-433">Results</span></span> 

<span data-ttu-id="289e7-434">按照上述步骤操作后，运行控制台应用 (Ctrl + F5)。</span><span class="sxs-lookup"><span data-stu-id="289e7-434">After following the previous steps, run your console app (Ctrl + F5).</span></span> <span data-ttu-id="289e7-435">结果应如以下输出所示。</span><span class="sxs-lookup"><span data-stu-id="289e7-435">Your results should be similar to the following output.</span></span> <span data-ttu-id="289e7-436">你可能会看到警告或处理消息，为清楚起见，这些消息已从以下结果中删除。</span><span class="sxs-lookup"><span data-stu-id="289e7-436">You may see warnings or processing messages, but these messages have been removed from the following results for clarity.</span></span>

```console
=====Identify the objects in the images=====

.....The objects in the image image1.jpg are detected as below....
car and its Confidence score: 0.9697262
car and its Confidence score: 0.6674225
person and its Confidence score: 0.5226039
car and its Confidence score: 0.5224892
car and its Confidence score: 0.4675332

.....The objects in the image image2.jpg are detected as below....
cat and its Confidence score: 0.6461141
cat and its Confidence score: 0.6400049

.....The objects in the image image3.jpg are detected as below....
chair and its Confidence score: 0.840578
chair and its Confidence score: 0.796363
diningtable and its Confidence score: 0.6056048
diningtable and its Confidence score: 0.3737402

.....The objects in the image image4.jpg are detected as below....
dog and its Confidence score: 0.7608147
person and its Confidence score: 0.6321323
dog and its Confidence score: 0.5967442
person and its Confidence score: 0.5730394
person and its Confidence score: 0.5551759

========= End of Process..Hit any Key ========
```

<span data-ttu-id="289e7-437">若要查看带有边界框的图像，请导航到 `assets/images/output/` 目录。</span><span class="sxs-lookup"><span data-stu-id="289e7-437">To see the images with bounding boxes, navigate to the `assets/images/output/` directory.</span></span> <span data-ttu-id="289e7-438">以下是其中一个已处理的图像示例。</span><span class="sxs-lookup"><span data-stu-id="289e7-438">Below is a sample from one of the processed images.</span></span> 

![](./media/object-detection-onnx/image3.jpg)

<span data-ttu-id="289e7-439">祝贺你！</span><span class="sxs-lookup"><span data-stu-id="289e7-439">Congratulations!</span></span> <span data-ttu-id="289e7-440">现已通过重用 ML.NET 中的预训练 `ONNX` 模型，成功生成了对象检测机器学习模型。</span><span class="sxs-lookup"><span data-stu-id="289e7-440">You've now successfully built a machine learning model for object detection by reusing a pre-trained `ONNX` model in ML.NET.</span></span>

<span data-ttu-id="289e7-441">可以在 [dotnet/samples](https://github.com/dotnet/machinelearning-samples/tree/master/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx) 存储库中找到本教程的源代码。</span><span class="sxs-lookup"><span data-stu-id="289e7-441">You can find the source code for this tutorial at the [dotnet/samples](https://github.com/dotnet/machinelearning-samples/tree/master/samples/csharp/getting-started/DeepLearning_ObjectDetection_Onnx) repository.</span></span>

<span data-ttu-id="289e7-442">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="289e7-442">In this tutorial, you learned how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="289e7-443">了解问题</span><span class="sxs-lookup"><span data-stu-id="289e7-443">Understand the problem</span></span>
> * <span data-ttu-id="289e7-444">了解什么是 ONNX 以及它如何与 ML.NET 配合使用</span><span class="sxs-lookup"><span data-stu-id="289e7-444">Learn what ONNX is and how it works with ML.NET</span></span>
> * <span data-ttu-id="289e7-445">了解模型</span><span class="sxs-lookup"><span data-stu-id="289e7-445">Understand the model</span></span>
> * <span data-ttu-id="289e7-446">重用预训练的模型</span><span class="sxs-lookup"><span data-stu-id="289e7-446">Reuse the pre-trained model</span></span>
> * <span data-ttu-id="289e7-447">使用已加载模型检测对象</span><span class="sxs-lookup"><span data-stu-id="289e7-447">Detect objects with a loaded model</span></span>

<span data-ttu-id="289e7-448">请查看机器学习示例 GitHub 存储库，以探索扩展的对象检测示例。</span><span class="sxs-lookup"><span data-stu-id="289e7-448">Check out the Machine Learning samples GitHub repository to explore an expanded object detection sample.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="289e7-449">dotnet/machinelearning-samples GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="289e7-449">dotnet/machinelearning-samples GitHub repository</span></span>](https://github.com/dotnet/machinelearning-samples/tree/master/samples/csharp/end-to-end-apps/DeepLearning_ObjectDetection_Onnx)