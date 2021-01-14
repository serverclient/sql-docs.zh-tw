---
title: 建立延伸模組
description: 您可以使用延伸模組將功能新增到 Azure Data Studio。 了解如何建立延伸模組，以及將其發佈到延伸模組資源庫。
ms.prod: azure-data-studio
ms.technology: azure-data-studio
ms.topic: conceptual
author: markingmyname
ms.author: maghan
ms.reviewer: alayu
ms.custom: ''
ms.date: 08/26/2020
ms.openlocfilehash: d23e37065eb18a7a22d5717a95379161c10870a0
ms.sourcegitcommit: e40e75055c1435c5e3f9b6e3246be55526807b4c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98151234"
---
# <a name="extend-functionality-by-creating-azure-data-studio-extensions"></a>藉由建立 Azure Data Studio 延伸模組來擴充功能

Azure Data Studio 中的延伸模組提供一種簡單方式，其可將更多功能新增至基底 Azure Data Studio 安裝。

延伸模組是由 Azure Data Studio 小組 (Microsoft) 及協力廠商社群 (您！) 提供。

## <a name="author-an-extension"></a>撰寫延伸模組

如果您有興趣擴充 Azure Data Studio，您可以建立自己的延伸模組，並將其發佈至延伸模組資源庫。

### <a name="write-an-extension"></a>撰寫延伸模組

#### <a name="prerequisites"></a>Prerequisites

若要開發延伸模組，則需要安裝 [Node.js](https://nodejs.org/)，並使其可在 `$PATH` 中使用。 Node.js 包含 Node.js 套件管理員 npm，可用來安裝延伸模組產生器。

若要建立新的延伸模組，您可以使用 Azure Data Studio 延伸模組產生器。 Yeoman [延伸模組產生器](https://www.npmjs.com/package/generator-azuredatastudio)對於延伸模組專案而言是一個很好的起點。 若要啟動產生器，請在命令提示字元中輸入下列命令：

```console
npm install -g yo generator-azuredatastudio
yo azuredatastudio
```

如需如何開始使用延伸模組範本的詳細指引，請參閱[鍵盤對應延伸模組](keymap-extension.md)，文中會按部就班介紹延伸模組的建立方式。

### <a name="extensibility-references"></a>擴充性參考

若要了解 Azure Data Studio 擴充性，請參閱[擴充性概觀](../extensibility.md)。 您也可以在現有[範例](https://github.com/Microsoft/azuredatastudio/tree/main/samples)中查看如何使用 API 的範例。

## <a name="debug-an-extension"></a>對延伸模組進行偵錯

您可以使用 Visual Studio Code 延伸模組 [Azure Data Studio 偵錯](https://github.com/kevcunnane/sqlops-debug)，對新的延伸模組進行偵錯。

若要對您的延伸模組偵錯：

1. 使用 [Visual Studio Code](https://code.visualstudio.com/) 開啟延伸模組。
2. 安裝 Azure Data Studio 偵錯延伸模組。
3. 選取 **F5** 鍵或選取 [偵錯] 圖示，然後選取 [開始]。
4. Azure Data Studio 的新執行個體會以特殊模式 (延伸模組開發主機) 啟動。 這個新的執行個體現在可以識別您的延伸模組。

## <a name="create-an-extension-package"></a>建立延伸模組套件

撰寫延伸模組之後，需要建立會安裝在 Azure Data Studio 中的 VSIX 套件。 您可使用 [vscode-vsce](https://github.com/Microsoft/vscode-vsce) (Visual Studio Code 延伸模組) 來建立 VSIX 套件。

```console
npm install -g vsce
cd myExtensionName
vsce package
# The myExtensionName.vsix file has now been generated
```

透過 VSIX 套件，您可共用 .vsix 檔案，並從命令選擇區使用 [延伸模組：從 VSIX 檔案進行安裝] 命令，將延伸模組安裝至 Azure Data Studio，即可在本機和私人位置共用延伸模組。

## <a name="publish-an-extension"></a>發佈延伸模組

若要將您的新延伸模組發佈至 Azure Data Studio：

1. 將延伸模組新增至[延伸模組資源庫](https://github.com/Microsoft/azuredatastudio/blob/release/extensions/extensionsGallery.json)。
2. 我們目前不支援裝載第三方延伸模組。 Azure Data Studio 會提供瀏覽至下載頁面的選項，而不是下載延伸模組。 若要設定延伸模組的下載頁面，請設定 **Microsoft.AzureDataStudio.DownloadPage** 資產的值。
3. 依據版本/延伸模組分支建立 PR。
4. 將檢閱要求傳送給小組。

我們會檢閱您的延伸模組，並新增至延伸模組資源庫。

### <a name="publish-extension-updates"></a>發佈延伸模組更新

發佈更新與發佈延伸模組的程序類似。 請確定已更新 package.json 中的版本。

## <a name="next-steps"></a>後續步驟

請參閱下列其中一個延伸模組撰寫教學課程，以取得如何開始進行的逐步說明：

- [鍵盤對應延伸模組教學課程](keymap-extension.md)
- [儀表板延伸模組教學課程](dashboard-extension.md)
- [筆記本延伸模組教學課程](notebook-extension.md)
- [Jupyter Book 延伸模組教學課程](jupyter-book-extension.md)
- [精靈延伸模組教學課程](wizard-extension.md)
