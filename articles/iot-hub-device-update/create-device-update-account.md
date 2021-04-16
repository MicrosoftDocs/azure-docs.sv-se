---
title: Skapa ett enhetsuppdateringskonto i Enhetsuppdatering för Azure IoT Hub | Microsoft Docs
description: Skapa ett enhetsuppdateringskonto i Enhetsuppdatering för Azure IoT Hub.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: d3f7f4e1cdd56675d6084448abc810c9a41992f9
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520147"
---
# <a name="device-update-for-iot-hub-resource-management"></a>Enhetsuppdatering för IoT Hub-resurshantering

För att komma igång med Enhetsuppdatering måste du skapa ett konto för enhetsuppdatering, en instans och ange åtkomstkontrollroller. 

## <a name="prerequisites"></a>Förutsättningar

* Åtkomst till en IoT Hub. Vi rekommenderar att du använder en S1-nivå (Standard) eller högre. 
* Webbläsare som stöds:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>Skapa ett konto för enhetsuppdatering

1. Gå till [Azure Portal](https://portal.azure.com)

2. Klicka på Skapa en resurs och sök efter "Enhetsuppdatering för IoT Hub"

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="Skärmbild av Enhetsuppdatering för IoT Hub resurs." lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. Klicka på Skapa -> Enhetsuppdatering för IoT Hub

4. Ange den Azure-prenumeration som ska associeras med ditt enhetsuppdateringskonto och resursgrupp

5. Ange ett namn och en plats för ditt enhetsuppdateringskonto

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="Skärmbild av kontoinformation." lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > Du kan gå till [sidan Azure-produkter per region för](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) att identifiera de regioner där enhetsuppdatering för IoT Hub är tillgänglig. Om Enhetsuppdatering för IoT Hub inte är tillgänglig i din region kan du välja att skapa ett konto i en tillgänglig region som är närmast dig. 

6. Klicka på "Nästa: Granska + skapa>"

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="Skärmbild av granskning av kontoinformation." lightbox="media/create-device-update-account/account-review.png":::

7. Granska informationen och välj sedan "Skapa". Du ser att distributionen pågår. 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="Skärmbild av kontodistribution pågår." lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. Distributionsstatusen ändras till "klar" om några minuter. Klicka på Gå till resurs

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="Skärmbild av att kontodistributionen är klar." lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>Skapa en instans för enhetsuppdatering 

En instans av Enhetsuppdatering är associerad med en enda IoT-hubb. Välj den IoT-hubb som ska användas med Enhetsuppdatering. Vi skapar en ny princip för delad åtkomst under det här steget för att säkerställa att enhetsuppdateringen endast använder de behörigheter som krävs för att arbeta med IoT Hub (registerskrivning och tjänståtkomst). Den här principen säkerställer att åtkomsten endast begränsas till Enhetsuppdatering.

Så här skapar du en instans av enhetsuppdatering efter att ett konto har skapats.

1. När du är i din nyligen skapade kontoresurs går du till bladet Instanshantering för instanser

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="Skärmbild av instanshantering i kontot." lightbox="media/create-device-update-account/instance-blade.png":::

2. Klicka på Skapa och ange ett instansnamn och välj IoT Hub

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="Skärmbild av instansinformation." lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > Den IoT Hub du länkar till din enhetsuppdateringsresurs behöver inte finnas i samma region som ditt enhetsuppdateringskonto. För bättre prestanda rekommenderar vi dock att IoT Hub i en region som är samma som eller nära regionen för ditt enhetsuppdateringskonto. 

3. Klicka på Skapa. Instansen visas i tillståndet "Skapar". 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="Skärmbild av instansskapande." lightbox="media/create-device-update-account/instance-creating.png":::

4. Tillåt 5–10 minuter för att instansdistributionen ska slutföras. Uppdatera statusen tills du ser "Etableringsstatus" och "Lyckades".

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="Skärmbild av att instansen har skapats." lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>Konfigurera IoT Hub 

För att enhetsuppdateringen ska kunna ta emot ändringsmeddelanden från IoT Hub integreras enhetsuppdateringen med den inbyggda händelsehubben. När du klickar på knappen IoT Hub konfigureras de meddelandevägar och åtkomstprinciper som krävs för att kommunicera med IoT-enheter. 

Så här konfigurerar du IoT Hub

1. När instansen "Etableringstillstånd" blir "Lyckades" väljer du instansen på bladet Instanshantering. Klicka på "Konfigurera IoT Hub"

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="Skärmbild av konfiguration IoT Hub för en instans." lightbox="media/create-device-update-account/instance-configure.png":::

2. Välj "Jag godkänner att göra dessa ändringar"

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="Skärmbild av att godkänna att konfigurera IoT Hub för en instans." lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. Klicka på "Uppdatera"

   > [!NOTE] 
   > Om du använder en kostnadsfri nivå på Azure IoT Hub är det tillåtna antalet meddelandevägar begränsat till 5. Enhetsuppdatering för IoT Hub måste konfigurera 4 meddelandevägar för att fungera som förväntat. 

[Lär dig mer om de meddelandevägar som har konfigurerats.](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>Konfigurera åtkomstkontrollroller

För att andra användare ska ha åtkomst till Enhetsuppdatering måste användare beviljas åtkomst till den här resursen. 

1. Gå till Åtkomstkontroll (IAM) i kontot för enhetsuppdatering

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="Skärmbild av åtkomstkontroll i enhetsuppdateringskontot." lightbox="media/create-device-update-account/account-access-control.png":::

2. Klicka på Lägg till rolltilldelningar

3. Under "Välj en roll" väljer du en enhetsuppdateringsroll bland de angivna alternativen
     - Administratör för enhetsuppdatering
     - Läsare för enhetsuppdatering
     - Innehållsadministratör för enhetsuppdatering
     - Innehållsläsare för enhetsuppdatering
     - Administratör för enhetsuppdateringsdistributioner
     - Läsare för enhetsuppdateringsdistributioner
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="Skärmbild av rolltilldelningar för åtkomstkontroll i enhetsuppdateringskontot." lightbox="media/create-device-update-account/role-assignment.png":::
    
    [Lär dig mer om rollbaserad åtkomstkontroll i Enhetsuppdatering för IoT Hub](device-update-control-access.md) 
    
4. Tilldela åtkomst till en användare eller En Azure AD-grupp
5. Klicka på Spara
6. Nu är du redo att använda enhetsuppdateringsupplevelsen från ditt IoT Hub

## <a name="next-steps"></a>Nästa steg

Prova att uppdatera en enhet med någon av följande snabbsjälvstudier:

 - [Enhetsuppdatering i en simulator](device-update-simulator.md)
 - [Enhetsuppdatering på Raspberry Pi](device-update-raspberry-pi.md)
 - [Enhetsuppdatering på Ubuntu Server 18.04 x64-paketagenten](device-update-ubuntu-agent.md)

[Läs mer om kontot och instansen för enhetsuppdatering.](device-update-resources.md) 

[Läs mer om roller för åtkomstkontroll för enhetsuppdatering. ](device-update-control-access.md) 

