---
title: Azure IoT Edge Java 教學課程 | Microsoft Docs
description: 本教學課程說明如何使用 Java 程式碼建立 IoT Edge 模組，並部署至邊緣裝置。
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 08/30/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 370fe646ca23ad5f364536ef9d9e05d042d2ed15
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337358"
---
# <a name="tutorial-develop-a-java-iot-edge-module-and-deploy-to-your-simulated-device"></a>教學課程：開發 Java IoT Edge 模組並部署至模擬裝置

您可以使用 Azure IoT Edge 模組來部署程式碼，直接在 IoT Edge 裝置上實作您的商務邏輯。 本教學課程會逐步引導您建立並部署能篩選感應器資料的 IoT Edge 模組。 您將會使用模擬的 IoT Edge 裝置，其建立方法詳述於[在 Windows 中的模擬裝置上部署 Azure IoT Edge][lnk-tutorial1-win]，以及[在 Linux 中的模擬裝置上部署 Azure IoT Edge][lnk-tutorial1-lin] 這兩個快速入門中。 在本教學課程中，您了解如何：    

> [!div class="checklist"]
> * 使用 Visual Studio Code 來根據 Azure IoT Edge maven 範本套件和 Azure IoT Java 裝置 SDK，建立 IoT Edge Java 模組。
> * 使用 Visual Studio Code 和 Docker 建立 Docker 映像，並將其發佈至您的登錄中。
> * 將模組部署到您的 IoT Edge 裝置。
> * 檢視產生的資料。


您於此教學課程中建立的 IoT Edge 模組，能夠篩選由您裝置所產生的溫度資料。 它只有在溫度超過指定的閾值時，才會將訊息往上游傳送。 這類於邊緣所進行的分析，對於減少針對雲端所傳輸及儲存的資料量相當有幫助。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>必要條件

Azure IoT Edge 裝置：

* 您可以遵循 [Linux](quickstart-linux.md) 或 [Windows 裝置](quickstart.md)快速入門中的步驟，使用您的開發電腦或虛擬機器作為邊緣裝置。

雲端資源：

* Azure 中的標準層 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。 

開發資源：

* [Visual Studio Code](https://code.visualstudio.com/)。 
* 適用於 Visual Studio Code 的 [Java 擴充套件](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) (英文)。
* 適用於 Visual Studio Code 的 [Azure IoT Edge 擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
* [Java SE 開發套件 10](http://www.oracle.com/technetwork/java/javase/downloads/index.html) (英文)，並[設定 `JAVA_HOME` 環境變數](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) (英文) 指向 JDK 安裝。
* [Maven](https://maven.apache.org/)
* [Docker CE](https://docs.docker.com/install/)
   * 如果您在 Windows 裝置上開發，請確定 Docker 已[設定為使用 Linux 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) (英文)。 


## <a name="create-a-container-registry"></a>建立容器登錄庫
在本教學課程中，您會使用適用於 VS Code 的 Azure IoT Edge 擴充功能來建置模組，並從檔案建立**容器映像**。 接著，您會將此映像推送至儲存並管理映像的**登錄**。 最後，您會從登錄部署該映像，以在 IoT Edge 裝置上執行。  

您可以此教學課程中使用任何與 Docker 相容的登錄。 雲端中提供使用的兩個熱門 Docker 登錄服務為 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 和 [Docker 中樞](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) (英文)。 本教學課程使用的是 Azure Container Registry。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立資源] > [容器] > [Azure Container Registry]。
2. 為登錄提供名稱，選擇訂用帳戶，選擇資源群組，然後將 SKU 設定為 [基本]。 
3. 選取 [建立] 。
4. 建立容器登錄之後，請加以瀏覽，並選取 [存取金鑰]。 
5. 將 [管理使用者] 切換為 [啟用]。
6. 複製 [登入伺服器]、[使用者名稱] 及 [密碼] 的值。 稍後您在本教學課程中，將 Docker 映像發佈到您的登錄，或將登錄認證新增至 Azure IoT Edge 執行階段時，將會用到這些值。 

## <a name="create-an-iot-edge-module-project"></a>建立 IoT Edge 模組專案
下列步驟會使用 Visual Studio Code 和 Azure IoT Edge 擴充功能，建立以 Azure IoT Edge maven 範本套件和 Azure IoT Java 裝置 SDK 為基礎的 IoT Edge 模組專案。

### <a name="create-a-new-solution"></a>建立新解決方案

建立可讓您使用自己的程式碼自訂的 Java 解決方案範本。 

1. 在 Visual Studio Code 中，選取 [檢視] > [命令選擇區]，以開啟 VS Code 命令選擇區。 

2. 請在命令選擇區中，輸入並執行命令 **Azure: Sign in**，然後依照指示登入您的 Azure 帳戶。 如果您已登入，則可以略過此步驟。

3. 在 [命令選擇區] 中，輸入並執行命令 **Azure IoT Edge: New IoT Edge solution**。 在命令選擇區中提供下列資訊，以建立解決方案： 

   1. 選取要用來建立解決方案的資料夾。 
   2. 為解決方案提供名稱，或是接受預設值 **EdgeSolution**。
   3. 選擇 [Java 模組] 作為模組範本。 
   4. 提供 groupId 的值，或接受預設值 **com.edgemodule**。
   5. 將預設模組名稱改為 **JavaModule**。 
   6. 將您在上一節所建立的 Azure 容器登錄，指定為第一個模組的映像存放庫。 將 **localhost:5000** 取代為您所複製的登入伺服器值。 最終字串看起來會像 \<登錄名稱\>.azurecr.io/javamodule。


如果是第一次建立 Java 模組，可能需要幾分鐘的時間下載 maven 套件。 之後，VS Code 視窗會載入您的 IoT Edge 解決方案工作區。 解決方案工作區包含五個最上層元件。 您在本教學課程中將不會編輯 **\.vscode** 資料夾或 **\.gitignore** 檔案。 **modules** 資料夾包含您的模組所需的 Java 程式碼，以及用來將模組建置成容器映像的 Dockerfile。 **\.env** 檔案會儲存您的容器登錄認證。 **Deployment.template.json** 檔案包含 IoT Edge 執行階段用來在裝置上部署模組的資訊。 

如果您在建立解決方案時未指定容器登錄，但接受了預設的 localhost:5000 值，則不會有 \.env 檔案。 

### <a name="add-your-registry-credentials"></a>新增登錄認證

環境檔案會儲存容器登錄的認證，並與 IoT Edge 執行階段共用這些認證。 執行階段需要有這些認證才能將私人映像提取到 IoT Edge 裝置。 

1. 在 VS Code 總管中，開啟 .env 檔案。 
2. 使用從 Azure Container Registry 複製過來的 [使用者名稱] 和 [密碼] 值來更新欄位。 
3. 儲存這個檔案。 

### <a name="update-the-module-with-custom-code"></a>使用自訂程式碼來更新模組

1. 在 VS Code 總管中，開啟 [modules] > [JavaModule] > [src] > [main] > [java] > [com] > [edgemodule] > [App.java]。

5. 在檔案頂端新增下列程式碼，以匯入新的參考類別。

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;
    
    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;
    
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

5. 將下列定義新增至 **App** 類別。 此變數會設定在將資料傳送至 IoT 中樞之前，測量的溫度必須超過的值。 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

7. 將 **MessageCallbackMqtt** 的執行方法換成下列程式碼。 每當模組從 IoT Edge 中樞收到 MQTT 訊息時，就會呼叫此方法。 它會篩選所報告溫度低於 (透過模組對應項所設定) 之溫度閾值的訊息。

    ```java
        private int counter = 0;
       @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;
 
            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    ```

8. 將下列兩個靜態內部類別新增至 **App** 類別。 這些類別會從模組對應項接收所需屬性的更新，並將  **tempThreshold** 變數更新為一致。 所有模組都具有自己的模組對應項，這可讓您直接從雲端設定於模組內執行的程式碼。

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
 
    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

9. 在 **main** 方法的 **client.open()** 後面新增下列幾行，以訂閱模組對應項的更新。

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

11. 儲存這個檔案。

## <a name="build-your-iot-edge-solution"></a>建置 IoT Edge 解決方案

在上一節中，您已建立 IoT Edge 解決方案，並將程式碼新增至 **JavaModule**，以篩選掉報告的機器溫度低於可接受閾值的訊息。 現在，您需要建置容器映像形式的解決方案，並將它推送到容器登錄。 

1. 透過在 Visual Studio Code 整合式終端機中輸入下列命令，來登入 Docker。 接著，您可以將模組映像推送到您的 Azure 容器登錄。
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   使用您在第一節中從 Azure 容器登錄複製而來的使用者名稱、密碼及登入伺服器。 您也可以在 Azure 入口網站中，從登錄的 [存取金鑰] 區段擷取這些資料。

2. 在 VS Code 總管中，於 IoT Edge 解決方案工作區開啟 deployment.template.json 檔案。 這個檔案會指示 **$edgeAgent** 部署兩個模組：**tempSensor** 和 **JavaModule**。 **JavaModule.image** 值會設定為映像的 Linux amd64 版本。 如果 **arm32v7** 是您 IoT Edge 裝置的架構，請將映像版本變更為此值。 

   確認範本具有正確的模組名稱，而不是您在建立 IoT Edge 解決方案時變更的預設 **SampleModule** 名稱。

   若要深入了解部署資訊清單，請參閱[了解如何使用、設定以及重複使用 IoT Edge 模組](module-composition.md)。

3. 在 deployment.template.json 檔案中，**registryCredentials** 區段會儲存您的 Docker 登錄認證。 實際的使用者名稱和密碼組，會儲存在 git 所忽略的 .env 檔案中。  

4. 將 **JavaModule** 模組對應項新增至部署資訊清單。 在 **moduleContent** 區段底部，於 **$edgeHub** 模組對應項後面插入下列 JSON 內容： 
    ```json
        "JavaModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. 儲存這個檔案。

5. 在 VS Code 總管中，以滑鼠右鍵按一下 deployment.template.json 檔案，然後選取 [建置並推送 IoT Edge 解決方案]。 

當您指示 Visual Studio Code 建置解決方案時，它會先擷取部署範本中的資訊，再於名為 **config** 的新資料夾中，產生 deployment.json 檔案。然後，它會在整合式終端機中執行兩個命令：`docker build` 和 `docker push`。 這兩個命令會組建程式碼、將 Java 應用程式容器化，然後將程式碼推送至您在初始化解決方案時所指定的容器登錄。 

您可以在 VS Code 整合式終端機中檢視完整容器映像位址。 系統會根據 module.json 檔案中的資訊建置映像位址，其格式如下：\<存放庫\>：\<版本\>-\<平台\>。 在本教學課程中，看起來應該像 registryname.azurecr.io/javamodule:0.0.1-amd64。

## <a name="deploy-and-run-the-solution"></a>部署並執行解決方案

在您用來設定 IoT Edge 裝置的快速入門文章中，您使用 Azure 入口網站部署了模組。 您可以使用 Visual Studio Code 的 Azure IoT 工具組擴充功能來部署模組。 您已備妥您的案例所需的部署資訊清單，即 **deployment.json** 檔案。 現在您只需選取要接收部署的裝置即可。

1. 在 VS Code 命令選擇區中，執行 [Azure IoT 中樞：選取 IoT 中樞]。 

2. 選擇您要設定的 IoT Edge 裝置所屬的訂用帳戶和 IoT 中樞。 

3. 在 VS Code 總管中，展開 [Azure IoT 中樞裝置] 區段。 

4. 以滑鼠右鍵按一下 IoT Edge 裝置的名稱，然後選取 [建立單一裝置的部署]。 

   ![建立單一裝置的部署](./media/tutorial-java-module/create-deployment.png)

5. 選取 **config** 資料夾中的 **deployment.json** 檔案，然後按一下 [選取 Edge 部署資訊清單]。 請勿使用 deployment.template.json 檔案。 

6. 按一下 [重新整理] 按鈕。 您應該會看到新的 **JavaModule** 正在與 **TempSensor** 模組以及 **$edgeAgent** 和 **$edgeHub** 一起執行。  

## <a name="view-generated-data"></a>檢視產生的資料

1. 若要監視抵達 IoT 中樞的資料，請選取省略符號 (**...**)，然後選取 [開始監視 D2C 訊息]。
2. 若要監視特定裝置的 D2C 訊息，請以滑鼠右鍵按一下清單中的裝置，然後選取 [開始監視 D2C 訊息]。
3. 若要停止監視資料，請在命令選擇區中執行命令 **Azure IoT Hub: Stop monitoring D2C message**。 
4. 若要檢視或更新模組對應項，請以滑鼠右鍵按一下清單中的模組，然後選取 [編輯模組對應項]。 若要更新模組對應項，請儲存對應項 JSON 檔案，並以滑鼠右鍵按一下編輯器區域，然後選取 [更新模組對應項]。
5. 若要檢視 Docker 記錄，請安裝適用於 VS Code 的 [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)。 您可以在 Docker 總管中，本機找到您執行中的模組。 在快顯功能表中，選取 [顯示記錄]，以在整合式終端機中進行檢視。
 
## <a name="clean-up-resources"></a>清除資源 

如果您打算繼續閱讀下一篇建議的文章，則可以保留您所建立的資源和組態，並加以重複使用。 您可以也繼續使用相同的 IoT Edge 裝置作為測試裝置。 

否則，可以刪除您在本文中建立的本機組態和 Azure 資源，以避免產生費用。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立包含程式碼的 IoT Edge 模組，可篩選您 IoT Edge 裝置所產生的原始資料。 您可以繼續進行後續教學課程，以了解 Azure IoT Edge 有什麼其他方法，可協助您將此資料轉換成 Edge 上的商業見解。

> [!div class="nextstepaction"]
> [使用 SQL Server 資料庫在Edge 上儲存資料](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
