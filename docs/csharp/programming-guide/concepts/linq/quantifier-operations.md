---
title: 限定符运算 (C#)
ms.date: 07/20/2015
ms.assetid: 84ac2ac2-7a63-4581-bc4c-14e34be1493b
ms.openlocfilehash: 5c931e0971a2ae7970415905be8772a64a82ee39
ms.sourcegitcommit: 7588136e355e10cbc2582f389c90c127363c02a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "75635478"
---
# <a name="quantifier-operations-c"></a>限定符运算 (C#)
限定符运算返回一个 <xref:System.Boolean> 值，该值指示序列中是否有一些元素满足条件或是否所有元素都满足条件。  
  
 下图描述了两个不同源序列上的两个不同限定符运算。 第一个运算询问是否有一个或多个元素为字符“A”，结果为 `true`。 第二个运算询问是否所有元素都为字符“A”，结果为 `true`。  
  
 ![LINQ 限定符操作](./media/quantifier-operations/linq-quantifier-operations.png)  
  
 下节列出了执行限定符运算的标准查询运算符方法。  
  
## <a name="methods"></a>方法  
  
|方法名|说明|C# 查询表达式语法|详细信息|  
|-----------------|-----------------|---------------------------------|----------------------|  
|All|确定是否序列中的所有元素都满足条件。|不适用。|<xref:System.Linq.Enumerable.All%2A?displayProperty=nameWithType><br /><br /> <xref:System.Linq.Queryable.All%2A?displayProperty=nameWithType>|  
|Any|确定序列中是否有元素满足条件。|不适用。|<xref:System.Linq.Enumerable.Any%2A?displayProperty=nameWithType><br /><br /> <xref:System.Linq.Queryable.Any%2A?displayProperty=nameWithType>|  
|Contains|确定序列是否包含指定的元素。|不适用。|<xref:System.Linq.Enumerable.Contains%2A?displayProperty=nameWithType><br /><br /> <xref:System.Linq.Queryable.Contains%2A?displayProperty=nameWithType>|  

## <a name="query-expression-syntax-examples"></a>查询表达式语法示例  
  
### <a name="all"></a>All  
以下示例使用 `All` 检查所有字符串是否为特定长度。
  
[!code-csharp[All](~/samples/snippets/csharp/VS_Snippets_VBCSharp/CsLINQQuantifier/CS/Quantifier.cs#All)]  
  
### <a name="any"></a>Any  
以下示例使用 `Any` 检查所有字符串是否以“o”开头。  
  
[!code-csharp[Any](~/samples/snippets/csharp/VS_Snippets_VBCSharp/CsLINQQuantifier/CS/Quantifier.cs#Any)]  
  
### <a name="contains"></a>Contains  
以下示例使用 `Contains` 检查所有数组是否具有特定元素。  
  
[!code-csharp[Contains](~/samples/snippets/csharp/VS_Snippets_VBCSharp/CsLINQQuantifier/CS/Quantifier.cs#Contains)]  
  
## <a name="see-also"></a>另请参阅

- <xref:System.Linq>
- [标准查询运算符概述 (C#)](./standard-query-operators-overview.md)
- [在运行时动态指定谓词筛选器](../../../linq/dynamically-specify-predicate-filters-at-runtime.md)
- [如何查询包含一组指定词语的句子 (LINQ) (C#)](./how-to-query-for-sentences-that-contain-a-specified-set-of-words-linq.md)
