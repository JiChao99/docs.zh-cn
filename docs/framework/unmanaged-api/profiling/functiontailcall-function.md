---
title: FunctionTailcall 函数
ms.date: 03/30/2017
api_name:
- FunctionTailcall
api_location:
- mscorwks.dll
api_type:
- COM
f1_keywords:
- FunctionTailcall
helpviewer_keywords:
- FunctionTailcall function [.NET Framework profiling]
ms.assetid: 66347e03-9a97-41e8-8f9d-89b80803f7b5
topic_type:
- apiref
ms.openlocfilehash: bd03eccc923049c4a49062d18bd11659f3316e8a
ms.sourcegitcommit: b11efd71c3d5ce3d9449c8d4345481b9f21392c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76866818"
---
# <a name="functiontailcall-function"></a>FunctionTailcall 函数
通知探查器，当前正在执行的函数即将对另一个函数执行尾调用。  
  
> [!NOTE]
> `FunctionTailcall` 函数在 .NET Framework 版本2.0 中已弃用。 它将继续运行，但会导致性能下降。 改为使用[FunctionTailcall2](functiontailcall2-function.md)函数。  
  
## <a name="syntax"></a>语法  
  
```cpp
void __stdcall FunctionTailcall (  
    [in] FunctionID funcID  
);  
```  
  
## <a name="parameters"></a>参数

- `funcID`

  \[中的]：要进行尾调用的当前正在执行的函数的标识符。

## <a name="remarks"></a>备注  
 尾调用的目标函数将使用当前堆栈帧，并将直接返回到进行尾调用的函数的调用方。 这意味着将不会为作为尾调用目标的函数发出[FunctionLeave](functionleave-function.md)回调。  
  
 `FunctionTailcall` 函数是回调;必须实现此方法。 实现必须使用 `__declspec`（`naked`）存储类特性。  
  
 在调用此函数之前，执行引擎不会保存任何注册。  
  
- 进入时，必须保存使用的所有寄存器，包括浮点单元（FPU）中的所有寄存器。  
  
- 退出时，必须通过弹出由其调用方推送的所有参数来还原堆栈。  
  
 `FunctionTailcall` 的实现不应被阻止，因为它将延迟垃圾回收。 实现不应尝试垃圾回收，因为堆栈可能不处于垃圾回收友好状态。 如果尝试垃圾回收，则运行时将被阻止，直到 `FunctionTailcall` 返回。  
  
 此外，`FunctionTailcall` 函数不得调入托管代码或以任何方式导致托管的内存分配。  
  
## <a name="requirements"></a>需求  
 **平台：** 请参阅[系统要求](../../../../docs/framework/get-started/system-requirements.md)。  
  
 **标头：** Corprof.idl .idl  
  
 **库：** CorGuids.lib  
  
 **.NET Framework 版本：** 1.1、1.0  
  
## <a name="see-also"></a>另请参阅

- [FunctionEnter2 函数](functionenter2-function.md)
- [FunctionLeave2 函数](functionleave2-function.md)
- [SetEnterLeaveFunctionHooks2 方法](icorprofilerinfo2-setenterleavefunctionhooks2-method.md)
- [分析全局静态函数](profiling-global-static-functions.md)
