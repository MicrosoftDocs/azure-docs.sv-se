---
title: Faktisk slutanvändarmätning med Visual Studio Mobile Center – Azure Traffic Manager
description: Konfigurera ditt mobil program som utvecklats med Visual Studio Mobile Center för att skicka Faktisk slutanvändarmätning till Traffic Manager
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: devx-track-js
ms.openlocfilehash: f9e8cdd3eb5c9f441444683fb5efaccc880b2757
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98184619"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Så här skickar du Faktisk slutanvändarmätning till Traffic Manager med Visual Studio Mobile Center

Du kan konfigurera ditt mobil program som utvecklats med Visual Studio Mobile Center för att skicka Faktisk slutanvändarmätning till Traffic Manager genom att följa stegen:

>[!NOTE]
> För närvarande stöds endast sändning av Faktisk slutanvändarmätning till Traffic Manager för Android.

Om du vill konfigurera Faktisk slutanvändarmätning måste du skaffa en nyckel och instrumentera appen med Rom-paketet.

## <a name="step-1-obtain-a-key"></a>Steg 1: Hämta en nyckel
    
De mätningar du tar och skickar till Traffic Manager från ditt klient program identifieras av tjänsten med hjälp av en unik sträng, som kallas Faktisk slutanvändarmätning (Rom) nyckel. Du kan hämta en RUM nyckel med hjälp av Azure Portal, en REST API eller med hjälp av PowerShell/CLI-gränssnitt.

Hämta RUM-nyckeln med Azure Portal hjälp av följande procedur:
1. Logga in på Azure Portal från en webbläsare. Om du inte redan har ett konto kan du registrera dig för en kostnadsfri utvärderingsmånad.
2. Leta efter namnet på Traffic Manager-profilen som du vill modifiera och klicka sedan på Traffic Manager-profilen i resultaten som visas.
3. På sidan Traffic Manager profil klickar du på **faktisk slutanvändarmätning** under **Inställningar**.
4. Klicka på **generera nyckel** för att skapa en ny rum nyckel.
        
   ![Generera Faktisk slutanvändarmätning nyckel](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Bild 1: Faktisk slutanvändarmätning nyckel skapande**

5. Sidan visar den RUM nyckel som genereras och ett kods tycke för JavaScript-kod som måste bäddas in på HTML-sidan.
 
   ![JavaScript-kod för Faktisk slutanvändarmätning nyckel](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Bild 2: skript för att Faktisk slutanvändarmätning nyckel och mätning**
 
6. Klicka på **kopierings** knappen för att kopiera rum nyckeln. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Steg 2: instrumenterar din app med Rom-paketet för Mobile Center SDK

Om du inte har arbetat med Visual Studio Mobile Center kan du besöka [webbplatsen](https://mobile.azure.com). Detaljerade anvisningar om SDK-integrering finns [komma igång med Android SDK](/mobile-center/sdk/getting-started/Android).

Slutför följande procedur om du vill använda Faktisk slutanvändarmätning:

1.  Lägg till SDK i projektet

    Under för hands versionen av ATM Rom SDK måste du uttryckligen referera till paketets lagrings plats.

    Lägg till följande rader i **appen/build. gradle-** filen:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    Lägg till följande rader i **appen/build. gradle-** filen:

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Starta SDK: n

    Öppna appens huvud aktivitets klass och Lägg till följande import uttryck:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Sök efter `onCreate` återanropet i samma fil och Lägg till följande kod:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [faktisk slutanvändarmätning](traffic-manager-rum-overview.md)
- Lär dig [hur Traffic Manager fungerar](traffic-manager-overview.md)
- Lär dig mer om [Mobile Center](/mobile-center/)
- [Registrera dig](https://mobile.azure.com) för Mobile Center
- Läs mer om [metoderna för trafik-routning](traffic-manager-routing-methods.md) som stöds av Traffic Manager
- Lär dig hur du [skapar en Traffic Manager-profil](./quickstart-create-traffic-manager-profile.md)