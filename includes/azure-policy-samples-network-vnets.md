---
title: 包含檔案
description: 包含檔案
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: b7bbf1fb8ffc1f57a53682d3baf288077b144572
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664428"
---
### <a name="virtual-networks"></a>虛擬網路

|  |  |
|---------|---------|
| [允許的應用程式閘道 SKU](../articles/azure-policy/scripts/allowed-app-gate-sku.md) | 要求應用程式閘道使用已核准的 SKU。 您需指定已核准的 SKU 陣列。 |
| [禁止對 ER 網路進行網路對等互連](../articles/azure-policy/scripts/no-peering-er-net.md) | 禁止將網路對等互連與指定資源群組中的網路建立關聯。 用來防止與中央受控網路基礎結構建立連線。 您需指定要防止建立關聯的資源群組名稱。 |
| [禁止使用者定義的路由表](../articles/azure-policy/scripts/no-user-def-route-table.md)  |禁止使用使用者定義的路由表來部署虛擬網路。 |
| [對每個子網路使用 NSG X](../articles/azure-policy/scripts/nsg-on-subnet.md) | 要求搭配每個虛擬子網路使用特定的網路安全性群組。 您需指定要使用之網路安全性群組的識別碼。 |
| [針對 VM 網路介面使用已核准的子網路](../articles/azure-policy/scripts/use-approved-subnet-vm-nics.md) | 要求網路介面使用已核准的子網路。 您需指定已核准之子網路的識別碼。 |
| [針對 VM 網路介面使用已核准的 vNet](../articles/azure-policy/scripts/use-approved-vnet-vm-nics.md) | 要求網路介面使用已核准的虛擬網路。 您需指定已核准之虛擬網路的識別碼。 |