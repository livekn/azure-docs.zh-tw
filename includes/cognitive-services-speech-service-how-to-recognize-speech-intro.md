---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 1103e5a217ca4972cc1c983a7ad0d07b0797e9e9
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2018
ms.locfileid: "43129307"
---
<!-- N.B. no header, no intents here, language-agnostic -->

認知服務[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 提供最簡單的方式，在您的應用程式中使用完整功能的 [語音轉換文字]。

1. 建立語音處理站，並提供語音服務訂用帳戶金鑰或 (授權權杖) 和[區域](~/articles/cognitive-services/speech-service/regions.md)作為參數。 您也可以提供自訂端點，以指定非標準的服務端點。

1. 從語音處理站取得語音辨識器。 您可以設定輸入語言和輸出格式。 辨識器可以使用裝置的預設麥克風、音訊串流或檔案中的音訊。

1. 繫結事件以便執行非同步作業 (如有需要)。 當辨識器具有過渡期和最終結果時，它會接著呼叫事件處理常式。 否則，您的應用程式只會收到最終轉譯結果。

1. 開始辨識。 針對一次性辨識 (例如命令或查詢辨識)，請使用 `RecognizeAsync()` 方法。 此方法會傳回第一個辨識出來的語句。 針對長時間執行的辨識 (例如轉譯)，請使用 `StartContinuousRecognitionAsync()` 方法。 請繫結事件，以產生非同步辨識結果。

請參閱使用語音 SDK 的語音辨識案例所適用的下列程式碼片段。

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
