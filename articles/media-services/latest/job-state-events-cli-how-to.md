---
title: 將 Azure 媒體服務事件路由至自訂 Web 端點 | Microsoft Docs
description: 使用 Azure 事件格線訂閱媒體服務工作狀態變更事件。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: e9df0cd24ef890765b78c25a073d671889be10a7
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723740"
---
# <a name="route-azure-media-services-events-to-a-custom-web-endpoint-using-cli"></a>使用 CLI，將 Azure 媒體服務事件路由至自訂 Web 端點

Azure Event Grid 是一項雲端事件服務。 在本文中，您可使用 Azure CLI 訂閱 Azure 媒體服務工作狀態變更事件，以及觸發事件來檢視結果。 

一般而言，您可將事件傳送至可回應事件的端點，例如 Webhook 或 Azure Function。 本教學課程說明如何建立並設定 Webhook。

當您完成本文所述的步驟時，您會看到事件資料已傳送至端點。

## <a name="log-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](http://portal.azure.com)，然後啟動 **CloudShell** 來執行 CLI 命令，如後續步驟所示。

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

如果您選擇在本機安裝和使用 CLI，本文需要 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找您擁有的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

請務必記住您用於媒體服務帳戶名稱、儲存體名稱和資源名稱的值。

## <a name="enable-event-grid-resource-provider"></a>啟用事件格線資源提供者

您必須進行的第一件事是確定自己的訂用帳戶已啟用事件格線資源提供者。 

在 **Azure** 入口網站中，請執行下列動作：

1. 前往訂用帳戶。
2. 選取您的訂用帳戶。
3. 在 [設定] 下，選取 [資源提供者]。
4. 搜尋 "EventGrid"。
5. 請確定事件格線已註冊。 如果沒有，請按 [註冊] 按鈕。  

## <a name="create-a-generic-azure-function-webhook"></a>建立泛型 Azure Function Webhook 

### <a name="create-a-message-endpoint"></a>建立訊息端點

訂閱事件格線文章之前，建立一個可收集訊息的端點，以方便您檢視。

按照[泛型 Webhook](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function) 文章所述，建立一個由泛型 Webhook 觸發的函式。 本教學課程使用 **C#** 程式碼。

Webhook 建立之後，按一下 **Azure** 入口網站視窗上方的「取得函式 URL」連結來複製 URL。 您不需要 URL 的最後一個部分 (*&clientID=default*)。

![建立 Webhook](./media/job-state-events-cli-how-to/generic_webhook_files.png)

### <a name="validate-the-webhook"></a>驗證 Webhook

當您使用事件格線註冊自己的 Webhook 端點時，事件格線會將帶有簡單驗證碼的 POST 要求傳送給您，以證明端點的所有權。 您的應用程式需要回傳驗證碼作為回應。 事件格線不會將未經驗證的事件傳遞至 Webhook 端點。 如需詳細資訊，請參閱 [Event Grid 安全性和驗證](https://docs.microsoft.com/azure/event-grid/security-authentication)。 這個部分會定義兩個組件，如此才能通過驗證。

#### <a name="update-the-source-code"></a>更新原始程式碼

建立 Webhook 之後，**run.csx** 檔案會顯示在瀏覽器中。 使用以下程式碼取代預設程式碼。 

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"Webhook was triggered!");

    string jsonContent = await req.Content.ReadAsStringAsync();
    string eventGridValidation = 
        req.Headers.FirstOrDefault( x => x.Key == "Aeg-Event-Type" ).Value?.FirstOrDefault();

    dynamic eventData = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"event: {eventData}");

    if (eventGridValidation != String.Empty)
    {
        if (eventData[0].data.validationCode !=String.Empty && eventData[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent")
        {
            return req.CreateResponse(HttpStatusCode.OK, new 
            {
                validationResponse = eventData[0].data.validationCode
            });
        }
    }
    
    log.Info(jsonContent);

    return req.CreateResponse(HttpStatusCode.OK);
}
```

#### <a name="update-test-request-body"></a>更新測試要求本文

在 **Azure** 入口網站視窗右側，您會看到兩個索引標籤：[檢視檔案] 和 [測試]。 選取 [測試] 索引標籤。在 [要求本文] 中，貼上下列 json。 您可以照原樣貼上，不需要變更任何值。

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2017-08-06T22:09:30.740323Z"
}
]
```

按視窗頂端的 [儲存並執行]。

![Request body](./media/job-state-events-cli-how-to/generic_webhook_test.png)

## <a name="register-for-the-event-grid-subscription"></a>註冊事件格線訂用帳戶 

您可訂閱文章，告知事件格線您想追蹤的事件。下列範例會訂閱您建立的媒體服務帳戶，並傳遞您所建立 Azure Function Webhook 的 URL，以作為事件通知的端點。 

將 `<event_subscription_name>` 換成事件訂用帳戶的唯一名稱。 對於 `<resource_group_name>` 和 `<ams_account_name>`，使用您稍早建立的值。  針對 `<endpoint_URL>`，請貼上端點 URL。 移除 URL 中的 *&clientID=default*。 藉由在訂閱時指定端點，以便 Event Grid 將事件路由傳送至該端點。 

```cli
amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $amsResourceId \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

媒體服務帳戶資源識別碼值應該類似如下：

/subscriptions/81212121-2f4f-4b5d-a3dc-ba0015515f7b/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amstestaccount

## <a name="test-the-events"></a>測試事件

建立編碼工作。 例如，如[串流處理視訊檔案](stream-files-dotnet-quickstart.md)快速入門所述。

您已觸發此事件，而 Event Grid 會將訊息傳送至您在訂閱時設定的端點。 瀏覽至您稍早建立的 Webhook。 依序按一下 [監視] 和 [重新整理]。 您會看到工作的狀態變更事件：已排入佇列、已排程、處理中、已完成、錯誤、已取消、取消中。  如需詳細資訊，請參閱[媒體服務事件結構描述](media-services-event-schemas.md)。

例如︰

```json
[{
  "topic": "/subscriptions/<subscription id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job id>",
  "eventType": "Microsoft.Media.JobStateChange",
  "eventTime": "2018-04-20T21:17:26.2534881",
  "id": "<id>",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

![測試事件](./media/job-state-events-cli-how-to/test_events.png)

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續使用此儲存體帳戶和事件訂用帳戶，請勿清除在本文中建立的資源。 如果您不打算繼續，請使用下列命令來刪除您在本文建立的資源。

以您在上面建立的資源群組取代 `<resource_group_name>`。

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>後續步驟

[對事件做出反應](reacting-to-media-services-events.md)

## <a name="see-also"></a>另請參閱

[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
