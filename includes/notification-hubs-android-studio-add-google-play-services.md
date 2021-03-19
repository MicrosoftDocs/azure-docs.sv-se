---
title: ta med fil
description: ta med fil
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd202c98ed605209f5600965ecdb6c0b4c03c17e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67509090"
---
1. I Android Studio väljer du **verktyg** på menyn och sedan **SDK-hanteraren**. 
2. Välj mål versionen av Android SDK som används i projektet. Välj sedan **Visa paket information**. 

    ![Android SDK Manager – välj målversion](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Välj **Google API: er** om det inte redan är installerat.

    ![Android SDK Manager – Google APIs valt](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Växla till fliken **SDK-verktyg** . Om du inte redan har installerat Google Play-tjänster väljer du **Google Play Services** som visas i följande bild. Välj sedan **Använd** för att installera. Anteckna SDK-sökvägen för användning i ett senare steg.

    ![Android SDK Manager – Google Play Services valt](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Om du ser dialogrutan **Bekräfta ändringen** väljer du **OK**. De begärda komponenterna installeras. Välj **Slutför** när komponenterna har installerats.
4. Välj **OK** för att stänga dialogrutan **Inställningar för nya projekt**.  
5. Öppna filen build. gradle i **app** -katalogen och Lägg sedan till följande rad under `dependencies` . 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Välj ikonen för att **Synkronisera nu** i verktygsfältet.

    ![Synkronisering med Gradle](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Öppna AndroidManifest.xml-filen och Lägg sedan till följande tagg i *program* tag gen.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
