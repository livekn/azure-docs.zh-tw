---
title: 使用 Azure Container Registry 組建 (ACR 組建) 自動進行作業系統和架構修補
description: 簡介 ACR Build，這是 Azure Container Registry 中的一套功能，可在雲端提供安全、自動化的容器映像建置和修補。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 08/01/2018
ms.author: marsma
ms.openlocfilehash: 63bbd9b5711330207c34ac4aa05aac3a71304653
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413574"
---
# <a name="automate-os-and-framework-patching-with-acr-build"></a>使用 ACR Build 自動進行作業系統和架構修補

容器提供新的虛擬化層級，並且隔離應用程式和開發人員相依性與基礎結構和作業需求。 不過，還必須解決此應用程式虛擬化修補的方式。

**ACR Build** 為 Azure Container Registry 內的一套功能。 它能提供適用於 Linux、Windows 及 ARM 的雲端式容器映像建置，並可以自動化針對 Docker 容器的 [OS 和架構修補](#automate-os-and-framework-patching)。

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

## <a name="what-is-acr-build"></a>ACR Build 是什麼？

Azure Container Registry Build 是 Azure 原生的容器映像建置服務。 ACR Build 能夠透過隨選容器映像建置，以及原始程式碼認可和基底映像更新上的自動化建置，在雲端進行內部迴圈開發。

當程式碼認可至 Git 存放庫時，或是當容器的基底映像更新時，就會自動建置觸發程序容器映像。 您可以使用基底映像更新觸發程序，自動進行作業系統和應用程式架構修補工作流程，維護安全的環境，同時還能依循不可變容器的主體。

## <a name="quick-build-inner-loop-extended-to-the-cloud"></a>快速建置：內部迴圈擴充至雲端

在開發人員認可其第一行程式碼之前，生命週期管理便已開始。 ACR Build 的[快速建置](container-registry-tutorial-quick-build.md)功能可提供整合式本機內部迴圈開發經驗，並將組建卸載至 Azure。 使用快速建置時，您可以在認可程式碼之前，先確認您的自動化建置定義。

Azure CLI 中的 [az acr build][az-acr-build] 命令會使用熟悉的 `docker build` 格式來取得**內容** (要進行建置的檔案集合)，將它傳送至 ACR Build 服務，並依預設在完成時將建置的映像推送至其登錄。

下表顯示 ACR Build 所支援之內容位置的範例：

| 內容位置 | 說明 | 範例 |
| ---------------- | ----------- | ------- |
| 本機檔案系統 | 本機檔案系統上目錄內的檔案。 | `/home/user/projects/myapp` |
| GitHub 主要分支 | GitHub 存放庫之主要 (或其他預設) 分支內的檔案。  | `https://github.com/gituser/myapp-repo.git` |
| GitHub 分支 | GitHub 存放庫的特定分支。| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub PR | GitHub 存放庫中的提取要求。 | `https://github.com/gituser/myapp-repo.git#pull/23/head` |
| GitHub 子資料夾 | GitHub 存放庫中子資料夾內的檔案。 範例顯示的是指定 PR 和子資料夾的組合。 | `https://github.com/gituser/myapp-repo.git#pull/24/head:myfolder` |
| 遠端 Tarball | 遠端 Web 伺服器上壓縮封存中的檔案。 | `http://remoteserver/myapp.tar.gz` |

ACR Build 也會追蹤您的異地複寫登錄，讓分散的開發團隊能運用最接近的複寫登錄。

ACR Build 已設計為容器生命週期基元。 例如，將 ACR Build 整合到 CI/CD 解決方案中。 透過[服務主體][az-login-service-principal]執行 [az login][az-login]，您的 CI/CD 解決方案可接著發出 [az acr build][az-acr-build] 命令來開始進行映像建置。

若要了解如何使用快速建置，請參閱第一個 ACR Build 教學課程：[使用 Azure Container Registry Build 在雲端建置容器映像](container-registry-tutorial-quick-build.md)。

## <a name="automatic-build-on-source-code-commit"></a>在來源程式碼認可時自動建置

當程式碼認可至 Git 存放庫時，使用 ACR Build 自動觸發容器映像建置。 可使用 Azure CLI 命令 [az acr build-task][az-acr-build-task] 設定的建置工作，可讓您指定 Git 存放庫並選擇性地指定分支和 Dockerfile。 當您的團隊將程式碼認可至存放庫時，ACR Build 建立的 Webhook 會觸發在存放庫中定義的容器映像。

若要了解如何在原始程式碼認可時觸發建置，請參閱第二個 ACR Build 教學課程：[使用 Azure Container Registry Build 自動進行容器映像建置](container-registry-tutorial-build-task.md)。

## <a name="automate-os-and-framework-patching"></a>自動進行作業系統和架構修補

真正增強容器建置管線的 ACR Build 能力來自於其偵測基底映像更新的功能。 當已更新的基底映像推送至您的登錄時，ACR Build 可根據它自動建立任何應用程式映像。

容器映像可概括地分類為「基底」映像和「應用程式」映像。 您的基底映像通常包含您的應用程式建置所在的作業系統和應用程式架構，以及其他自訂項目。 這些基底映像本身通常是以公用上游映像為基礎，例如：[Alpine Linux][base-alpine]、[Windows][base-windows]、[.NET][base-dotnet] 或 [Node.js][base-node]。 您有數個應用程式映像可能會共用一個通用基底映像。

當上游維護程式 (例如重要 OS 安全性修補程式) 更新作業系統或應用程式架構映像時，您也必須更新您的基底映像以包含重要修正。 接著，還必須重建每個應用程式映像，以包含現在包含在基底映像中的這些上游修正。

因為 ACR Build 會在建立容器映像時動態地探索基底映像相依性，所以它可偵測應用程式映像的基底映像何時更新。 ACR Build 可接著使用其中一個預先設定的[建置工作](container-registry-tutorial-base-image-update.md#create-build-task)，為您**自動重建每個應用程式映像**。 ACR Build 可利用此自動偵測和重建功能，為您節省手動追蹤及更新每個參考已更新基底映像的應用程式映像所需的時間與精力。

若要了解作業系統和架構修補，請參閱第三個 ACR Build 教學課程：[使用 Azure Container Registry Build 在基底映像更新時自動進行映像建置](container-registry-tutorial-base-image-update.md)。

> [!NOTE]
> 針對初始預覽版，基底映像更新只會在基底與應用程式映像位於相同的 Azure 容器登錄，或是可公開存取的 Docker Hub 存放庫時觸發建置。

## <a name="next-steps"></a>後續步驟

當您藉由在雲端建置容器映像，而準備好自動進行作業系統和架構修補時，請查看三部分的 ACR Build 教學課程系列。

> [!div class="nextstepaction"]
> [使用 Azure Container Registry Build 在雲端建置容器映像](container-registry-tutorial-quick-build.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli#log-in-with-a-service-principal

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
