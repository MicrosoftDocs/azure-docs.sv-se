---
title: Anslut Symantec ICDx-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder Symantec ICDx Connector för att enkelt ansluta alla dina Symantec-säkerhetslösnings loggar med Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 7b32baa23a31342095e542d60745a503b81e89a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100092714"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Anslut Symantec ICDx-enheten 



Med Symantec ICDx Connector kan du enkelt ansluta alla dina Symantec-säkerhetslösnings loggar med din Azure Sentinel, Visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Detta ger dig bättre insikt i din organisations nätverk och förbättrar dina säkerhets åtgärder. Integreringen mellan Symantec ICDx och Azure Sentinel använder REST API.


> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="configure-and-connect-symantec-icdx"></a>Konfigurera och ansluta Symantec-ICDx 

Symantec ICDx kan integrera och exportera loggar direkt till Azure Sentinel.

1. Öppna ICDx-hanteringskonsolen för att lägga till Microsoft Azure Sentinel-vidarebefordrare (Log Analytics).
2. I navigerings fältet ICDx klickar du på **konfiguration**. 
3. Klicka på **vidarebefordrare** längst upp på sidan **konfiguration** .
4. Under **vidarebefordrare**, bredvid Microsoft Azure Sentinel (Log Analytics), klickar du på **Lägg till**. 
4. I fönstret **Microsoft Azure Sentinel (Log Analytics)** klickar du på **Visa Avancerat**. 
5. Överst i fönstret utökat till Microsoft Azure Sentinel (Log Analytics) gör du följande:
    -   **Namn**: Ange ett namn för vidarebefordraren som innehåller högst 30 tecken. Välj ett unikt, beskrivande namn. Det här namnet visas i listan över vidarebefordrare på **konfigurations** skärmen och i instrument panelerna på **instrument panelens** skärm. Exempel: Microsoft Azure Log Analytics öst. Det här fältet är obligatoriskt.
    -   **Beskrivning**: Ange en beskrivning av vidarebefordraren. Den här beskrivningen visas också i listan över vidarebefordrare på **konfigurations** skärmen. Inkludera information, till exempel händelse typen som vidarebefordras och gruppen som måste granska data.
    -   **Starttyp**: Välj Start metod för konfigurationen av vidarebefordraren. Alternativen är manuella och automatiska.<br>Standardvärdet är automatisk. 
6. Under **händelser** gör du följande: 
    - **Källa**: Välj en eller flera arkiv som händelser ska vidarebefordras från. Du kan välja aktiva insamlade arkiv (inklusive det gemensamma arkivet), överblivna insamlade arkiv (dvs. Arkiv för de insamlare som du har tagit bort), ICDx mottagar arkiv eller system arkivet. <br>Standard är gemensamt arkiv.
      > [!NOTE]
      > ICDx mottagar Arkiv anges separat, efter namn. 
 
    - **Filter**: Lägg till ett filter som anger den delmängd av händelser som ska vidarebefordras. Gör något av följande:
        - Om du vill välja ett filter villkor klickar du på en typ, ett attribut, en operator och ett värde. 
        - Granska filter villkoret i fältet filter. Du kan redigera den direkt i fältet eller ta bort den vid behov.
        - Klicka och eller eller för att lägga till i filter villkoret.
        - Du kan också klicka på sparade frågor för att tillämpa en sparad fråga.
    - **Inkluderade attribut**: Ange en kommaavgränsad lista med attribut som ska ingå i vidarebefordrade data. De inkluderade attributen prioriteras över exkluderade attribut.
    - **Exkluderade attribut**: Ange en kommaavgränsad lista med attribut som ska undantas från vidarebefordrade data.
    - **Batchstorlek**: Välj antalet händelser som ska skickas per batch. Alternativen är 10, 50, 100, 500 och 1000.<br>Standardvärdet är 100. 
    - **Hastighets begränsning**: Välj den hastighet med vilken händelser vidarebefordras, uttryckt som händelser per sekund. Alternativen är obegränsade, 500, 1000, 5000, 10000. <br> Standardvärdet är 5000. 
7. Gör följande under **Azure-mål**: 
    - **Arbetsyte-ID**: klistra in arbetsyte-ID: t nedan. Det här fältet är obligatoriskt.
    - **Primär nyckel**: klistra in primär nyckeln från nedan. Det här fältet är obligatoriskt.
    - **Namn på anpassad logg**: Skriv namnet på den anpassade loggen i Microsoft Azure-portalen Log Analytics arbets ytan som du ska vidarebefordra händelser till. Standardvärdet är SymantecICDx. Det här fältet är obligatoriskt.
8. Klicka på *Spara* för att slutföra konfigurationen av vidarebefordraren. 
9. Starta vidarebefordraren under **alternativ** genom att klicka på **mer** och sedan på **Starta**.
10. Om du vill använda det relevanta schemat i Log Analytics för Symantec ICDx-händelser söker du efter **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics. 



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Symantec ICDx till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.


