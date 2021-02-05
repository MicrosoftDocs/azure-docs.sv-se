---
title: Installera Azure spatiala ankare för enhets enhet
description: Konfigurera ett Unity-projekt för att använda Azures ankare för spatialdata
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 2/3/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 56532c17c91e6c703a6acd7990bbae47cd248165
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576573"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Konfigurera Azure spatiala ankare i ett Unity-projekt

Den här guiden visar hur du kommer igång med Azures spatiala ankare SDK i ditt Unity-projekt.

## <a name="project-requirements"></a>Projekt krav

[!INCLUDE [Unity Project Requirements](../../../includes/spatial-anchors-unity-project-requirements.md)]

## <a name="configuring-a-project"></a>Konfigurera ett projekt

Innan du inkluderar SDK: er för Azure spatial ankare i ditt Unity-projekt, måste du installera de [nödvändiga](#project-requirements) paketen via paket hanteraren.

### <a name="download-asa-packages"></a>Ladda ned ASA-paket
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-asa-packages"></a>Importera ASA-paket
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="android-only-configure-the-maintemplategradle-file"></a>Endast Android: Konfigurera filen mainTemplate. gradle

1. Gå till **Redigera**  >  **projekt inställnings**  >  **spelare**.
2. I **panelen kontrollant** för **Player-inställningar** väljer du **Android** -ikonen.
3. Under avsnittet **skapa** markerar du kryss rutan **anpassad mall för huvud Gradle** för att generera en anpassad Gradle-mall på `Assets\Plugins\Android\mainTemplate.gradle` .
4. Öppna `mainTemplate.gradle` filen i en text redigerare.
5. I `dependencies` avsnittet klistrar du in följande beroenden:

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

När allt är färdigt `dependencies` bör ditt avsnitt se ut ungefär så här:

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gör så här: skapa och hitta ankare i Unity](./create-locate-anchors-unity.md)
