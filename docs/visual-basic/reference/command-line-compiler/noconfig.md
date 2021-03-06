---
title: -noconfig
ms.date: 03/13/2018
helpviewer_keywords:
- noconfig compiler option [Visual Basic]
- -noconfig compiler option [Visual Basic]
- /noconfig compiler option [Visual Basic]
ms.assetid: a7405067-bd21-4171-adf4-a126fa3ad6c3
ms.openlocfilehash: c57ed1699d110959e9faf3dc3d43bcc200851c1c
ms.sourcegitcommit: eff6adb61852369ab690f3f047818c90580e7eb1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2019
ms.locfileid: "72005444"
---
# <a name="-noconfig"></a>-noconfig
指定编译器不应自动引用常用 .NET Framework 程序集，也不应导入 `System` 和 `Microsoft.VisualBasic` 命名空间。  
  
## <a name="syntax"></a>语法  
  
```console  
-noconfig  
```  
  
## <a name="remarks"></a>备注  
 `-noconfig` 选项告知编译器不要在 Vbc.rsp 文件中编译，该文件与 Vbc.exe 文件位于同一目录。 Vbc.rsp 文件引用常用的 .NET Framework 程序集，并导入 `System` 和 `Microsoft.VisualBasic` 命名空间。 除非指定 `-nostdlib` 选项，否则编译器将隐式引用 System.dll 程序集。 `-nostdlib` 选项指示编译器不要在 Vbc.rsp 中编译或自动引用 System.dll 程序集。  
  
> [!NOTE]
> 始终引用 Mscorlib.dll 和 Microsoft.VisualBasic.dll 程序集。  
  
 你可以修改 Vbc.rsp 文件以指定应包括在每个 Vbc.exe 编译中的其他编译器选项（指定 `-noconfig` 选项时除外）。 有关详细信息，请参阅 [@（指定响应文件）](../../../visual-basic/reference/command-line-compiler/specify-response-file.md)。  
  
 编译器最后处理传递给 `vbc` 命令的选项。 因此，命令行中的任何选项都将替代 Vbc.rsp 文件中相同选项的设置。  
  
> [!NOTE]
> `-noconfig` 选项在 Visual Studio 开发环境内无法使用；仅当从命令行编译时才可用。  
  
## <a name="see-also"></a>请参阅

- [-nostdlib (Visual Basic)](../../../visual-basic/reference/command-line-compiler/nostdlib.md)
- [Visual Basic 命令行编译器](../../../visual-basic/reference/command-line-compiler/index.md)
- [@（指定响应文件）](../../../visual-basic/reference/command-line-compiler/specify-response-file.md)
- [-reference (Visual Basic)](../../../visual-basic/reference/command-line-compiler/reference.md)
