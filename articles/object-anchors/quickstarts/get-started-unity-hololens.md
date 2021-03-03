---
title: 'Snabb start: skapa en HoloLens-app med Unity'
description: I den här snabb starten får du lära dig hur du skapar en HoloLens units-app med hjälp av objekt ankare.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: caf64883635d7fa1746d12caf24333a22ba2fa98
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748842"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity"></a>Snabb start: skapa en HoloLens-app med Azure Object-ankare, i Unity

I den här snabb starten skapar du en enhet för enhets-HoloLens som använder [Azures objekt ankare](../overview.md). Objekt ankare i Azure är en hanterad moln tjänst som konverterar 3D-tillgångar till AI-modeller som möjliggör objekt medveten, mixad verklighets upplevelser för HoloLens. När du är klar har du en HoloLens-app byggd med Unity som kan identifiera objekt i den fysiska världen.

Du lär dig följande:

> [!div class="checklist"]
> * Förbered Unity build-inställningar.
> * Exportera HoloLens Visual Studio-projektet.
> * Distribuera appen och kör den på en HoloLens 2-enhet.

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

## <a name="open-the-sample-project"></a>Öppna exempelprojektet

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

Öppna projektet i Unity `quickstarts/apps/unity/basic` .

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build and run](../../../includes/object-anchors-quickstart-unity-build-run.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### <a name="run-the-sample-app"></a>Kör exempelappen

Sätt på enheten, Välj **alla appar** och leta upp och starta appen. Efter skärmens välkomst skärm visas ett meddelande som anger att objekt observatören har initierats. Du måste dock lägga till din modell i appen.

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

Appen söker efter objekt i det aktuella fältet i vyn och spårar dem sedan när de har identifierats. En instans tas bort när den är 6 meter bort från användarens plats. Fel söknings texten visar information om en instans, till exempel ID, uppdaterad kvot för tids stämpling och omfattning.

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Uniting HoloLens med MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [Begrepp: SDK-översikt](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Vanliga frågor och svar](../faq.md)
