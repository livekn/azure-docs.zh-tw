---
title: 包含檔案
description: 包含檔案
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/19/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4bec8c8ea29c10b8c0d0351a41ebc9183bb45d4f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38942901"
---
## <a name="deployment-considerations"></a>部署考量

* 如需了解 N 系列 VM 的可用性，請參閱[依區域提供的產品](https://azure.microsoft.com/regions/services/)。

* N 系列 VM 只能部署在 Resource Manager 部署模型。

* N 系列 VM 對其磁碟支援的 Azure 儲存體類型有所不同。 NC 和 NV VM 只支援標準磁碟儲存體 (HDD) 所支持的 VM 磁碟。 NCv2、ND 和 NCv3 VM 只支援進階磁碟儲存體 (SSD) 所支持的 VM 磁碟。

* 如果您想要部署不只一些 N 系列 VM ，請考慮隨用隨付訂用帳戶或其他購買選項。 如果您使用 [Azure 免費帳戶](https://azure.microsoft.com/free/)，您只能使用有限數目的 Azure 計算核心。

* 您可能需要增加 Azure 訂用帳戶的核心配額 (依地區)，以及增加 NC、NCv2、NCv3、ND 或 NV 核心的個別配額。 若要要求增加配額，可免費[開啟線上客戶支援要求](../articles/azure-supportability/how-to-create-azure-support-request.md)。 預設限制會視您的訂用帳戶類別而有所不同。




