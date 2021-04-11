---
title: 'Snabb start: skapa en Unity Android-app'
description: I den här snabbstarten lär du dig att skapa en Android-app med Unity med hjälp av Spatial Anchors.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/18/2021
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4f4bfdb5d09da6a9967b53ca56bb2491976592a1
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105892"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Snabb start: skapa en enhets-Android-app med avstånds ankare för Azure

Den här snabb starten beskriver hur du skapar en Unity Android-app med hjälp av [Azure spatiala ankare](../overview.md). Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en ARCore Android-app som skapats med Unity och som kan spara och återkalla en spatial fästpunkt.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Förbereda Unity-bygginställningar
> * Konfigurera konto-ID och kontonyckel för Spatial Anchors
> * Exportera Android Studio-projektet
> * Distribuera och köra på en Android-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har följande så att du kan utföra den här snabbstarten:

- En Windows-eller macOS-dator med <a href="https://unity3d.com/get-unity/download" target="_blank">Unity (LTS)</a>, inklusive **stöd för Android-build** med **Android SDK & NDK-verktyg** och **openjdk** -moduler. Använd **unity 2020 LTS** med ASA sdk version 2,9 eller senare (som använder [Unity XR plugin-ramverket](https://docs.unity3d.com/Manual/XRPluginArchitecture.html)) eller **Unity 2019 LTS** med ASA SDK version 2,8 eller tidigare.
  - Om du kör på Windows, behöver du även <a href="https://git-scm.com/download/win" target="_blank">git för Windows</a> och <a href="https://git-lfs.github.com/">git LF</a>.
  - Om du kör på macOS ska du Hämta git installerat via HomeBrew. Ange följande kommando i en enskild rad i terminalen: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` . Kör sedan `brew install git` och `brew install git-lfs` .
- En <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">utvecklaraktiverad</a> och <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-kapabel</a> Android-enhet.
  - Ytterligare enhets driv rutiner kan krävas för att datorn ska kunna kommunicera med din Android-enhet. Mer ytterligare information och instruktioner finns [här](https://developer.android.com/studio/run/device.html).

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Ladda ned och öppna exempel projektet Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Följ anvisningarna [här](../how-tos/setup-unity-project.md#download-asa-packages) för att hämta och importera de ASA SDK-paket som krävs för Android-plattformen.

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-android-studio-project"></a>Exportera Android Studio-projektet

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Välj enheten i **Kör enhet** och välj sedan **skapa och kör**. Du uppmanas att spara en `.apk` fil som du kan välja namn för.

I appen väljer du **BasicDemo** med pilarna och trycker sedan på **Go!** knappen för att köra demonstrationen. Följ anvisningarna för att placera och återkalla en fäst punkt.

![Skärm bild 1 ](./media/get-started-unity-android/screenshot-1.jpg)
 ![ skärmdump 2, ](./media/get-started-unity-android/screenshot-2.jpg)
 ![ skärm bild 3](./media/get-started-unity-android/screenshot-3.jpg)

Följ instruktionerna i appen för att placera och återkalla en fästpunkt.

## <a name="troubleshooting"></a>Felsökning

### <a name="rendering-issues"></a>Åter givnings problem

Om du kör appen, om du inte ser kameran som bakgrund (t. ex. en tom, blå eller annan struktur), måste du förmodligen importera till gångar i enhets gruppen. Stoppa appen. Från den översta menyn i Unity väljer du **Assets -> Reimport all** (Tillgångar -> Återimportera alla). Kör sedan appen igen.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudie: dela spatiala ankare mellan enheter](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Gör så här: Konfigurera Azure spatiala ankare i ett Unity-projekt](../how-tos/setup-unity-project.md)
