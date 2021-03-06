---
title: -optimize
ms.date: 07/20/2015
helpviewer_keywords:
- optimize compiler option [Visual Basic]
- /optimize compiler option [Visual Basic]
- optimization [Visual Basic], enabling
- -optimize compiler option [Visual Basic]
ms.assetid: fcba4a97-3622-4b87-a891-0f77deab4998
ms.openlocfilehash: e8daf4a49123623b6470bc3c6281869f1b9b3d0f
ms.sourcegitcommit: eff6adb61852369ab690f3f047818c90580e7eb1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2019
ms.locfileid: "72005367"
---
# <a name="-optimize"></a>-optimize
启用或禁用编译器优化。  
  
## <a name="syntax"></a>语法  
  
```console  
-optimize[ + | - ]  
```  
  
## <a name="arguments"></a>自变量  
  
|术语|定义|  
|---|---|  
|`+` &#124; `-`|可选。 `-optimize-` 选项启用或禁用编译器优化。 `-optimize+` 选项启用优化。 默认情况下，禁用优化。|  
  
## <a name="remarks"></a>备注  
 编译器优化会使输出文件更智能、更快并且更有效。 但是，由于优化会导致输出文件中的代码重排，因此 `-optimize+` 可能会增加调试的难度。  
  
 使用 `-target:module` 为程序集生成的所有模块都必须使用与程序集相同的 `-optimize` 设置。 有关详细信息，请参阅 [-target (Visual Basic)](../../../visual-basic/reference/command-line-compiler/target.md)。  
  
 可以组合 `-optimize` 和 `-debug` 选项。  
  
|在 Visual Studio 集成开发环境中设置 -optimize|  
|---|  
|1.在 **“解决方案资源管理器”** 中选择一个项目。 在“项目”菜单上，单击“属性”   。<br />     <br />2.单击“编译”  选项卡。<br />3.单击“高级”  按钮。<br />4.修改“启用优化”  复选框。|  
  
## <a name="example"></a>示例  
 下面的代码编译 `T2.vb`，并启用编译器优化。  
  
```console
vbc t2.vb -optimize  
```  
  
## <a name="see-also"></a>请参阅

- [Visual Basic 命令行编译器](../../../visual-basic/reference/command-line-compiler/index.md)
- [-debug (Visual Basic)](../../../visual-basic/reference/command-line-compiler/debug.md)
- [示例编译命令行](../../../visual-basic/reference/command-line-compiler/sample-compilation-command-lines.md)
- [-target (Visual Basic)](../../../visual-basic/reference/command-line-compiler/target.md)
