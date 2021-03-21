---
title: Självstudie för att ansluta, konfigurera, aktivera Azure Stack Edge Pro-enhet med GPU i Azure Portal | Microsoft Docs
description: Självstudie för att distribuera Azure Stack Edge Pro GPU instruerar dig att ansluta, konfigurera och aktivera den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: ceaa16ee2a54886cde45f37ea90ed617abafffc1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100546915"
---
# <a name="tutorial-configure-the-device-settings-for-azure-stack-edge-pro-with-gpu"></a>Självstudie: Konfigurera enhets inställningarna för Azure Stack Edge Pro med GPU

I den här självstudien beskrivs hur du konfigurerar enhets relaterade inställningar för din Azure Stack Edge Pro-enhet med en inbyggd GPU. Du kan ställa in enhets namn, uppdaterings Server och tids server via det lokala webb gränssnittet.

Det kan ta cirka 5-7 minuter att slutföra enhets inställningarna.

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Förutsättningar
> * Konfigurera enhetsinställningar
> * Konfigurera uppdatering 
> * Konfigurera tid

## <a name="prerequisites"></a>Förutsättningar

Innan du konfigurerar enhets relaterade inställningar på din Azure Stack Edge Pro-enhet med GPU måste du kontrol lera att:

* För den fysiska enheten:

    - Du har installerat den fysiska enheten enligt beskrivningen i [installera Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md).
    - Du har konfigurerat nätverk och aktiverat och konfigurerat Compute Network på enheten som beskrivs i [Självstudier: Konfigurera nätverk för Azure Stack Edge Pro med GPU](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).


## <a name="configure-device-settings"></a>Konfigurera enhetsinställningar

Följ dessa steg om du vill konfigurera inställningar för enheten:

1. Utför följande steg på sidan **enhet** :

    1. Ange ett eget namn för din enhet. Det egna namnet måste innehålla mellan 1 och 13 tecken och får innehålla bokstäver, siffror och bindestreck.

    2. Ange en **DNS-domän** för din enhet. Den här domänen används för att konfigurera enheten som en fil server.

    3. Om du vill validera och tillämpa de konfigurerade enhets inställningarna väljer du **tillämpa**.

        ![Lokalt webb gränssnitt "enhet", sida 1](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

        Om du har ändrat enhets namnet och DNS-domänen kommer de automatiskt genererade självsignerade certifikaten på enheten inte att fungera. Du måste välja något av följande alternativ när du konfigurerar certifikat.: 
        
        - Generera och hämta enhets certifikaten. 
        - Ta med dina egna certifikat för enheten, inklusive signerings kedjan.
    

        ![Lokalt webb gränssnitt "enhet", sida 2](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-3.png)

    4. När enhets namnet och DNS-domänen ändras, skapas SMB-slutpunkten.  

    5. När inställningarna har tillämpats väljer du **Nästa: uppdatera Server**.

        ![Lokalt webb gränssnitt "enhet", sida 3](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-4.png)

## <a name="configure-update"></a>Konfigurera uppdatering

1. På sidan **Uppdatera** kan du nu konfigurera platsen från vilken du vill ladda ned uppdateringar för enheten.  

    - Du kan hämta uppdateringar direkt från Microsoft Update- **servern**.

        ![Sidan uppdatera Server på det lokala webb gränssnittet](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-2.png)

        Du kan också välja att distribuera uppdateringar från WSUS ( **Windows Server Update Services** ). Ange sökvägen till WSUS-servern.
        
        ![Lokalt webb gränssnitt "uppdaterings Server" på sidan 2](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-3.png)

        > [!NOTE] 
        > Om en separat Windows Update Server har kon figurer ATS och du väljer att ansluta över *https* (i stället för *http*) behövs signerings kedjan som krävs för att ansluta till uppdaterings servern. Information om hur du skapar och laddar upp certifikat finns i [Hantera certifikat](azure-stack-edge-gpu-manage-certificates.md). 

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

        ![Sidan tid för lokalt webb gränssnitt](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/time-2.png)

2. När inställningarna har tillämpats väljer du **Nästa: certifikat**.


## <a name="next-steps"></a>Nästa steg

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Förutsättningar
> * Konfigurera enhetsinställningar
> * Konfigurera uppdatering 
> * Konfigurera tid

Information om hur du konfigurerar certifikat för din Azure Stack Edge Pro-enhet finns i:

> [!div class="nextstepaction"]
> [Konfigurera certifikat](./azure-stack-edge-gpu-deploy-configure-certificates.md)
