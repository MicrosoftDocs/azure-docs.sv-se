---
title: Hantering av Azure Stack Edge Pro Compute | Microsoft Docs
description: Beskriver hur du hanterar kant beräknings inställningarna, t. ex. utlösare, moduler, Visa beräknings konfiguration, ta bort konfiguration via Azure Portal på Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/06/2021
ms.author: alkohli
ms.openlocfilehash: ebf967fe02ab6424b9952bb0315b70fadb547fe3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97967919"
---
# <a name="manage-compute-on-your-azure-stack-edge-pro"></a>Hantera beräkning av Azure Stack Edge Pro

Den här artikeln beskriver hur du hanterar beräkningar på Azure Stack Edge Pro. Du kan hantera beräkningen via Azure Portal eller via det lokala webb gränssnittet. Använd Azure Portal för att hantera moduler, utlösare och beräknings konfiguration och det lokala webb gränssnittet för att hantera beräknings inställningar.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Hantera utlösare
> * Hantera beräknings konfiguration


## <a name="manage-triggers"></a>Hantera utlösare

Händelser är saker som sker i din molnmiljö eller på din enhet som du kanske vill vidta någon åtgärd för. När en fil skapas i en resurs är det till exempel en händelse. Utlösare utlöser de här händelserna. Utlösare för Azure Stack Edge Pro kan vara svar på fil händelser eller ett schema.

- **Fil**: dessa utlösare är svar på fil händelser som att skapa en fil, ändra en fil.
- **Schemalagd**: dessa utlösare är som svar på ett schema som du kan definiera med start datum, start tid och upprepnings intervallet.


### <a name="add-a-trigger"></a>Lägga till en utlösare

Utför följande steg i Azure Portal för att skapa en utlösare.

1. I Azure Portal går du till Azure Stack Edge-resursen och går sedan till **IoT Edge > utlösare**. Välj **+ Lägg till utlösare** i kommando fältet.

    ![Välj Lägg till utlösare](media/azure-stack-edge-manage-compute/add-trigger-1.png)

2. Ange ett unikt namn för utlösaren i bladet **Lägg till utlösare** .
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Välj en **typ** för utlösaren. Välj **fil** när utlösaren är svar på en fil händelse. Välj **schemalagd** när du vill att utlösaren ska starta vid en angiven tidpunkt och köras vid ett angivet upprepnings intervall. Beroende på ditt val visas en annan uppsättning alternativ.

    - **Fil utlösare** – Välj i list rutan en monterad resurs. När en fil händelse utlöses i den här resursen anropar utlösaren en Azure-funktion.

        ![Lägga till SMB-resurs](media/azure-stack-edge-manage-compute/add-file-trigger.png)

    - **Schemalagd utlösare** – ange start datum/-tid och upprepnings intervall i timmar, minuter eller sekunder. Ange också namnet på ett ämne. I ett avsnitt får du flexibiliteten att dirigera utlösaren till en modul som distribuerats på enheten.

        Ett exempel på en väg sträng är: `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"` .

        ![Lägga till NFS-resurs](media/azure-stack-edge-manage-compute/add-scheduled-trigger.png)

4. Välj **Lägg till** för att skapa utlösaren. Ett meddelande visar att utlösaren håller på att skapas. När utlösaren har skapats, uppdateras bladet för att återspegla den nya utlösaren.
 
    ![Uppdaterad utlösare](media/azure-stack-edge-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Ta bort en utlösare

Utför följande steg i Azure Portal för att ta bort en utlösare.

1. Välj den utlösare som du vill ta bort i listan över utlösare.

    ![Välj utlösare](media/azure-stack-edge-manage-compute/delete-trigger-1.png)

2. Högerklicka på och välj sedan **ta bort**.

    ![Välj Ta bort](media/azure-stack-edge-manage-compute/delete-trigger-2.png)

3. Klicka på **Ja** när du uppmanas att bekräfta åtgärden.

    ![Bekräfta borttagning](media/azure-stack-edge-manage-compute/delete-trigger-3.png)

Listan över utlösare uppdateras för att återspegla borttagningen.

## <a name="manage-compute-configuration"></a>Hantera beräknings konfiguration

Använd Azure Portal för att Visa beräknings konfigurationen, ta bort en befintlig beräknings konfiguration eller uppdatera beräknings konfigurationen för att synkronisera åtkomst nycklar för IoT-enheten och IoT Edge enheten för din Azure Stack Edge Pro.

### <a name="view-compute-configuration"></a>Visa beräknings konfiguration

Utför följande steg i Azure Portal för att Visa beräknings konfigurationen för enheten.

1. I Azure Portal går du till din Azure Stack Edge-resurs och går sedan till **IoT Edge > översikt**. 

    ![Välj Visa beräkning](media/azure-stack-edge-manage-compute/view-compute-1.png)

2. Gå till sidan **Egenskaper** . Anteckna beräknings konfigurationen på enheten. När du har konfigurerat Compute skapade du en IoT Hub-resurs. Under den IoT Hub resursen konfigureras en IoT-enhet och en IoT Edge enhet. Endast Linux-moduler stöds för att köras på den IoT Edge enheten.

    ![Visa konfiguration](media/azure-stack-edge-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>Ta bort beräknings konfiguration

Utför följande steg i Azure Portal för att ta bort den befintliga Edge Compute-konfigurationen för enheten.

1. I Azure Portal går du till din Azure Stack Edge-resurs och går sedan till **IoT Edge > översikt**. Välj **ta bort** i kommando fältet.

    ![Välj Ta bort beräkning](media/azure-stack-edge-manage-compute/remove-compute-1.png)

2. Om du tar bort beräknings konfigurationen måste du konfigurera om enheten om du behöver använda Compute igen. Välj **Ja** när du uppmanas att bekräfta.

    ![Välj Ta bort beräkning 2](media/azure-stack-edge-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Synkronisera IoT-enheten och IoT Edge enhets åtkomst nycklar

När du konfigurerar Compute på Azure Stack Edge Pro skapas en IoT-enhet och en IoT Edge enhet. De här enheterna tilldelas automatiskt symmetriska åtkomst nycklar. Som en säkerhets åtgärd roteras dessa nycklar regelbundet via IoT Hub tjänsten.

Om du vill rotera dessa nycklar går du till IoT Hub tjänst som du har skapat och väljer IoT-enheten eller IoT Edge enheten. Varje enhet har en primär åtkomst nyckel och sekundära åtkomst nycklar. Tilldela den primära åtkomst nyckeln till den sekundära åtkomst nyckeln och återskapa den primära åtkomst nyckeln.

Om din IoT-enhet och IoT Edge enhets nycklar har roterats måste du uppdatera konfigurationen på din Azure Stack Edge Pro för att få de senaste åtkomst nycklarna. Synkroniseringen hjälper enheten att hämta de senaste nycklarna för din IoT-enhet och IoT Edge enhet. Azure Stack Edge Pro använder bara de primära åtkomst nycklarna.

Utför följande steg i Azure Portal för att synkronisera åtkomst nycklarna för enheten.

1. I Azure Portal går du till din Azure Stack Edge-resurs och går sedan till **IoT Edge > översikt**. Välj **Uppdatera konfiguration** i kommando fältet.

    ![Välj Uppdatera konfiguration](media/azure-stack-edge-manage-compute/refresh-configuration-1.png)

2. Välj **Ja** när du uppmanas att bekräfta.

     ![Välj Ja när du tillfrågas](media/azure-stack-edge-manage-compute/refresh-configuration-2.png)

3. Avsluta dialogrutan när synkroniseringen är klar.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hanterar Edge Compute Network via Azure Portal](azure-stack-edge-extend-compute-access-modules.md).
