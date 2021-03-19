---
title: ta med fil
description: ta med fil
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ccb5e40738680181e7339b8652d029597c7d0bd4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "70935123"
---
1. Logga in på [Firebase-konsolen](https://firebase.google.com/console/). Skapa ett nytt Firebase-projekt om du inte redan har ett.
2. När du har skapat ett projekt väljer du **Lägg till Firebase till din Android-app**. 

    ![Lägg till Firebase till din Android-app](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Utför följande steg på sidan **Lägg till Firebase på din Android-app** : 
    1. För **namn på Android-paket** kopierar du värdet för din **applicationId** i programmets build. gradle-fil. I det här exemplet är det `com.fabrikam.fcmtutorial1app` . 

        ![Ange paket namnet](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Välj **Registrera app**. 
4. Välj **hämta google-services.jspå**, spara filen i **app** -mappen i projektet och välj sedan **Nästa**. 

    ![Ladda ned google-services.jspå](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Gör följande **konfigurations ändringar** i projektet i Android Studio. 
    1.  I filen build. gradle för projekt nivå ( &lt; Project &gt; /build.gradle) lägger du till följande-instruktion i avsnittet **beroenden** . 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. Lägg till följande-instruktioner i avsnittet beroenden i filen build. gradle för app-nivå ( &lt; Project &gt; / &lt; app-module &gt; /build.gradle).  

        ```
        implementation 'com.google.firebase:firebase-core:16.0.8'
        implementation 'com.google.firebase:firebase-messaging:17.3.4'
        ```

    3. Lägg till följande rad i slutet av filen build. gradle för app-nivå efter avsnittet beroenden. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Välj **Synkronisera nu** i verktygsfältet. 
 
        ![konfigurations ändringar för build. gradle](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Välj **Nästa**. 
7. Välj **hoppa över det här steget**. 

    ![Hoppa över det sista steget](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Välj kugghjulet för ditt projekt i Firebase-konsolen. Välj sedan **Projektinställningar**.

    ![Välj Projektinställningar](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Om du inte har hämtat google-services.jsfilen till mappen **app** i Android Studio-projektet kan du göra det på den här sidan. 
5. Växla till fliken **Cloud Messaging** överst. 
6. Kopiera och spara **Server nyckeln** för senare användning. Du använder det här värdet för att konfigurera hubben.
