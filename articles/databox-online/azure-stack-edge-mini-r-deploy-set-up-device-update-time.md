---
title: Självstudie för att konfigurera enhet, uppdatering, tid för Azure Stack Edge Mini R-enhet i Azure Portal
description: Självstudie för att distribuera Azure Stack Edge Mini R instruerar dig att konfigurera enhets-, uppdaterings-och tids inställningar för den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R  so I can use it to transfer data to Azure.
ms.openlocfilehash: e9f4d4f4ad27081bb105cdb1698438837fc2fe02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100546677"
---
# <a name="tutorial-configure-the-device-settings-for-azure-stack-edge-mini-r"></a>Självstudie: Konfigurera enhets inställningarna för Azure Stack Edge Mini R

I den här självstudien beskrivs hur du konfigurerar enhets inställningar för din Azure Stack Edge Mini R-enhet med en onboard-GPU. Du kan ställa in enhets namn, uppdaterings Server och tids server via det lokala webb gränssnittet.

Det kan ta cirka 5-7 minuter att slutföra enhets inställningarna.

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Förutsättningar
> * Konfigurera enhetsinställningar
> * Konfigurera uppdatering 
> * Konfigurera tid

## <a name="prerequisites"></a>Förutsättningar

Innan du konfigurerar enhets relaterade inställningar på din Azure Stack Edge Mini R-enhet med GPU måste du kontrol lera att:

* För den fysiska enheten:

    - Du har installerat den fysiska enheten enligt beskrivningen i [installera Azure Stack Edge Mini R ](azure-stack-edge-mini-r-deploy-install.md).
    - Du har konfigurerat nätverk och aktiverat och konfigurerat Compute Network på enheten som beskrivs i [själv studie kursen: Konfigurera nätverk för Azure Stack Edge Mini R med GPU](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md).


## <a name="configure-device-settings"></a>Konfigurera enhetsinställningar

Följ dessa steg om du vill konfigurera inställningar för enheten:

1. Utför följande steg på sidan **enhet** :

    1. Ange ett eget namn för din enhet. Det egna namnet måste innehålla mellan 1 och 13 tecken och får innehålla bokstäver, siffror och bindestreck.

    2. Ange en **DNS-domän** för din enhet. Den här domänen används för att konfigurera enheten som en fil server.

    3. Om du vill validera och tillämpa de konfigurerade enhets inställningarna väljer du **tillämpa**.

        ![Lokalt webb gränssnitt "enhet", sida 1](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/set-up-device-1.png)

        Om du har ändrat enhets namnet och DNS-domänen kommer de självsignerade certifikat som fanns på enheten inte att fungera. 

        ![Lokalt webb gränssnitt "enhet", sida 2](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/set-up-device-2.png)

        Du måste välja något av följande alternativ när du konfigurerar certifikat: 
        
        - Generera och hämta enhets certifikaten. 
        - Ta med dina egna certifikat för enheten, inklusive signerings kedjan.
    

    4. När enhets namnet och DNS-domänen ändras, skapas SMB-slutpunkten.  

        ![Lokalt webb gränssnitt "enhet", sida 3](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/set-up-device-3.png)

    5. När inställningarna har tillämpats väljer du **Nästa: uppdatera Server**.


## <a name="configure-update"></a>Konfigurera uppdatering

1. På sidan **Uppdatera** kan du nu konfigurera platsen från vilken du vill ladda ned uppdateringar för enheten.  

    - Du kan hämta uppdateringar direkt från Microsoft Update- **servern**.

        ![Sidan uppdatera Server på det lokala webb gränssnittet](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/update-server-1.png)

        Du kan också välja att distribuera uppdateringar från WSUS ( **Windows Server Update Services** ). Ange sökvägen till WSUS-servern.
        
        ![Lokalt webb gränssnitt "uppdaterings Server" på sidan 2](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/update-server-2.png)

        > [!NOTE] 
        > Om en separat Windows Update Server har kon figurer ATS och du väljer att ansluta över *https* (i stället för *http*) behövs signerings kedjan som krävs för att ansluta till uppdaterings servern. Information om hur du skapar och laddar upp certifikat finns i [Hantera certifikat](azure-stack-edge-gpu-manage-certificates.md). För att arbeta i frånkopplat läge, till exempel din Azure Stack gräns för enhets nivå till modulärt Data Center, aktiverar du alternativet WSUS. Vid aktiveringen söker enheten efter uppdateringar och om servern inte har kon figurer ATS kommer aktiveringen att Miss förväntas. 

2. Välj **Använd**.
3. När uppdaterings servern har kon figurer ATS väljer du **Nästa: tid**.
    

## <a name="configure-time"></a>Konfigurera tid

Följ de här stegen för att konfigurera tids inställningar på enheten. 

> [!IMPORTANT]
> Även om tids inställningarna är valfria rekommenderar vi starkt att du konfigurerar en primär NTP och en sekundär NTP-server i det lokala nätverket för din enhet. Om den lokala servern inte är tillgänglig kan offentliga NTP-servrar konfigureras.

NTP-servrar krävs eftersom din enhet måste synkronisera tid så att den kan autentiseras med dina moln tjänst leverantörer.

1. På sidan **tid** kan du välja tidszon och de primära och sekundära NTP-servrarna för enheten.  
    
    1. I list rutan **tids zon** väljer du den tidszon som motsvarar den geografiska plats där enheten distribueras.
        Standard tids zonen för enheten är PST. Enheten använder den här tidszonen för alla schemalagda åtgärder.

    2. I rutan **primär NTP-server** anger du den primära servern för enheten eller accepterar standardvärdet för Time.Windows.com.  
        Se till att ditt nätverk tillåter att NTP-trafik skickas från ditt data Center till Internet.

    3. Alternativt kan du ange en sekundär server för enheten i rutan **sekundär NTP-server** .

    4. Om du vill validera och tillämpa de konfigurerade tids inställningarna väljer du **tillämpa**.

        ![Sidan tid för lokalt webb gränssnitt](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/time-settings-1.png)

2. När inställningarna har tillämpats väljer du **Nästa: certifikat**.


## <a name="next-steps"></a>Nästa steg

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Förutsättningar
> * Konfigurera enhetsinställningar
> * Konfigurera uppdatering 
> * Konfigurera tid

Information om hur du konfigurerar certifikat för din Azure Stack Edge-Mini R-enhet finns i:

> [!div class="nextstepaction"]
> [Konfigurera certifikat](./azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)
