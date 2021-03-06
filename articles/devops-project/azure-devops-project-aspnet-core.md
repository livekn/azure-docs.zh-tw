---
title: 使用 Azure DevOps Project 建立適用於 .NET 的 CI/CD 管線 | 快速入門
description: DevOps Project 可讓您輕鬆地開始使用 Azure。 它可協助您透過幾個簡單的步驟，在您選擇的 Azure 服務上啟動 .NET 應用程式。
ms.prod: devops
ms.technology: devops-cicd
services: azure-devops-project
documentationcenter: vs-devops-build
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: c858288768e9a3d93015881bf8ebbb4e9d4dc008
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37966916"
---
# <a name="create-a-cicd-pipeline-for-net-with-the-azure-devops-project"></a>使用 Azure DevOps Project 建立適用於 .NET 的 CI/CD 管線

使用 **Azure DevOps Project** 為您的 .NET Core 或 ASP.NET 應用程式設定持續整合 (CI) 與持續傳遞 (CD)。  Azure DevOps 專案可簡化初始設定 VSTS 組建與發行管線。

如果您沒有 Azure 訂用帳戶，可以透過 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 取得一個免費的訂用帳戶。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

Azure DevOps Project 會在 VSTS 中建立 CI/CD 管線。  您可以建立**新的 VSTS** 帳戶，或使用**現有的帳戶**。  Azure DevOps Project 也會在您選擇的 **Azure 訂用帳戶**中建立 **Azure 資源**。

1. 登入 [Microsoft Azure 入口網站](https://portal.azure.com)。

1. 在左側導覽列中選擇 [建立資源] 圖示，然後搜尋 [DevOps 專案]。  選擇 [建立] 。

    ![啟動持續傳遞](_img/azure-devops-project-aspnet-core/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>選取應用程式範例和 Azure 服務

1. 選取 **.NET** 範例應用程式。  .NET 範例包含開放原始碼 ASP.NET Framework 或跨平台.NET Core Framework 的選項。

    ![.NET Framework](_img/azure-devops-project-aspnet-core/chooselanguagedotnet.png)

1. 選取 **.NET Core** 應用程式架構。  此範例為 ASP.NET Core MVC 應用程式。 完成時，選擇 [下一步]。

1. **Windows 上的 Web App** 是預設的部署目標。  您可以選擇性地選擇 Linux 上的 Web 應用程式或用於容器的 Web 應用程式。  您在上一個步驟中選擇的應用程式架構，會指出這裡可用的 Azure 服務部署目標類型。  保留預設的服務，然後選擇 [下一步]。

## <a name="configure-vsts-and-an-azure-subscription"></a>設定 VSTS 和 Azure 訂用帳戶 

1. 免費建立**新的** VSTS 帳戶，或選擇**現有的**帳戶。  為 VSTS 專案選擇 [名稱]。  選取您應用程式的 [Azure 訂用帳戶]、[位置]，然後選擇 [名稱]。  完成時，選擇 [完成]。

    ![輸入 VSTS 資訊](_img/azure-devops-project-aspnet-core/vstsazureinfo.png)

1. 在幾分鐘內，系統會在 Azure 入口網站中載入 [專案儀表板]。  系統會在您 VSTS 帳戶中的存放庫中設定範例應用程式、執行組建，然後將您的應用程式部署至 Azure。  此儀表板可顯示您的**程式碼存放庫**、**VSTS CI/CD 管線**，和您**在 Azure 中的應用程式**。  若要檢視執行中的應用程式，請在儀表板右側選取 [瀏覽]。

    ![儀表板檢視](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>認可程式碼變更並執行 CI/CD

Azure DevOps 專案已在您的 VSTS 或 GitHub 帳戶中建立 Git 存放庫。  請遵循下列步驟來檢視存放庫，並變更您應用程式的程式碼。

1. 在 DevOps 專案儀表板的左側，選取您 [主要] 分支的連結。  此連結會開啟新建立 Git 存放庫的檢視。

1. 若要檢視存放庫複製 URL，請從瀏覽器右上方選取 [複製]。 您可以在最愛的 IDE 中複製 Git 存放庫。  在接下來的幾個步驟中，您可以使用網頁瀏覽器，直接進行和認可主要分支的程式碼變更。

1. 在瀏覽器的左側，瀏覽至 **Views/Home/index.cshtml** 檔案。

1. 選取 [編輯]，並進行 h2 標題的變更。  例如，輸入**立即開始使用 Azure DevOps Project** 或進行其他變更。

    ![程式碼編輯](_img/azure-devops-project-aspnet-core/codechange.png)

1. 選擇 [認可]，然後儲存您的變更。

1. 在瀏覽器中，瀏覽至 [Azure DevOps 專案儀表板]。  您現在應該會看到正在進行中的組建。  您所做的變更會透過 VSTS CI/CD 管線自動建置及部署。

## <a name="examine-the-vsts-cicd-pipeline"></a>檢查 VSTS CI/CD 管線

Azure DevOps 專案會在您的 VSTS 帳戶中自動設定完整的 VSTS CI/CD 管線。  瀏覽管線，並視需要進行自訂。  請遵循下列步驟以熟悉 VSTS 組建和發行定義。

1. 從 Azure DevOps 專案儀表板**頂端**選取 [建置管線]。  此連結會開啟瀏覽器索引標籤，並開啟新專案的 VSTS 組建定義。

1. 選取 [省略符號]。  這個動作會開啟功能表，您可以用它來啟動數個活動，例如將新的組建排入佇列、暫停組建，以及編輯組建定義。

1. 選取 [編輯]。

    ![組建定義](_img/azure-devops-project-aspnet-core/builddef.png)

1. 在這個檢視中，**檢查組建定義的各種工作**。  組建會執行各種工作，例如從 Git 存放庫擷取來源、還原相依性，以及發佈用來進行部署的輸出。

1. 在組建定義的頂端，選取**組建定義名稱**。

1. 將您組建定義的**名稱**變得更具有描述性。  選取 [儲存並排入佇列]，然後選取 [儲存]。

1. 在您的組建定義名稱下，選取 [歷程記錄]。  您會看到組建最近變更的稽核線索。  VSTS 會追蹤對組建定義進行的任何變更，並可讓您比較版本。

1. 選取 [觸發程序]。  Azure DevOps 專案已自動建立 CI 觸發程序，且每次對存放庫的認可都會建立新的組建。  您可以選擇性地選擇要在 CI 程序中包含還是排除分支。

1. 選取 [保留期]。  根據您的案例，您可以指定原則來保留或移除特定數目的組建。

1. 選取 [建置及發行]，然後選擇 [版本]。  Azure DevOps 專案已建立 VSTS 發行定義，來管理 Azure 的部署。

1. 在瀏覽器的左側，選取您發行定義旁的 [省略符號]，然後選擇 [編輯]。

1. 發行定義中包含 [管線]，會定義發行程序。  在 [成品] 下，選取 [置放]。  您在先前步驟中檢查的組建定義會產生用於成品的輸出。 

1. 在 [置放] 圖示右側，選取 [持續部署觸發程序]。  這個發行定義已啟用 CD 觸發程序，每次有新的組建成品可用時，它就會執行部署。  您可以選擇性地停用觸發程序，因此您的部署需要手動執行。 

1. 在瀏覽器左側，選取 [工作]。  工作是您部署程序所執行的活動。  在此範例中，會建立一個工作來部署到 **Azure App 服務**。

1. 在瀏覽器右側，選取 [檢視版本]。  此檢視會顯示版本的歷程記錄。

1. 選取您其中一個版本旁的 [省略符號]，然後選擇 [開啟]。  您可從這個檢視中瀏覽數個功能表，例如版本摘要、相關聯的工作項目及測試。

1. 選取 [認可]。  此檢視會顯示與特定部署相關聯的程式碼認可。 

1. 選取 [記錄]。  記錄包含關於部署程序的實用資訊。  您可以在部署期間和部署之後加以檢視。

## <a name="clean-up-resources"></a>清除資源

當您不再需要時，可以使用 Azure DevOps 專案儀表板中的 [刪除] 功能，將 Azure App 服務和在本快速入門中建立的相關資源刪除。

## <a name="next-steps"></a>後續步驟

若要深入了解如何修改組建和發行定義以符合小組的需求，請參閱本教學課程：

> [!div class="nextstepaction"]
> [自訂 CD 程序](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>影片

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]
