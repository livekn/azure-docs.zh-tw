---
title: 使用範本部署 Azure 防火牆
description: 使用範本部署 Azure 防火牆
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: 1a732e22d72c36afe11030e42bae529baa35df1a
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38992259"
---
# <a name="deploy-azure-firewall-using-a-template"></a>使用範本部署 Azure 防火牆

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Azure 防火牆文件中的範例假設您已啟用 Azure 防火牆公開預覽。 如需詳細資訊，請參閱[啟用 Azure 防火牆公用預覽](public-preview.md)。

此範本會建立防火牆和測試網路環境。 網路有一個 VNet，包含三個子網路：AzureFirewallSubnet、ServersSubnet 及 JumpboxSubnet。 ServersSubnet 和 JumpboxSubnet 中各有一個 2 核心 Windows Server。

防火牆位於 AzureFirewallSubnet 中，並使用應用程式規則集合進行設定，其中包含單一規則可允許存取 www.microsoft.com。

所建立的使用者定義路由會將 ServersSubnet 中的網路流量指向通過防火牆，該防火牆會套用防火牆規則。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="template-location"></a>範本位置

範本位於：

[https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox)

閱讀簡介，當準備好部署時，請按一下 [部署至 Azure]。

## <a name="clean-up-resources"></a>清除資源

首先探索使用防火牆建立的資源，之後若不再需要，您可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令來移除資源群組、防火牆和所有相關資源。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```
## <a name="next-steps"></a>後續步驟

接下來，您可以監視 Azure 防火牆記錄：

- [教學課程：監視 Azure 防火牆記錄](./tutorial-diagnostics.md)

