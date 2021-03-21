---
title: Hantering av Azure Stack Edge Pro GPU Compute | Microsoft Docs
description: Beskriver hur du hanterar kant beräknings inställningarna, t. ex. utlösare, moduler, Visa beräknings konfiguration, ta bort konfiguration via Azure Portal på Azure Stack Edge Pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 057f80049b13a265925f2d6f24a008d1e1e04c96
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102639015"
---
# <a name="manage-compute-on-your-azure-stack-edge-pro-gpu"></a>Hantera beräkning på Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

I den här artikeln beskrivs hur du hanterar Compute via IoT Edge-tjänsten på din Azure Stack Edge Pro GPU-enhet. Du kan hantera beräkningen via Azure Portal eller via det lokala webb gränssnittet. Använd Azure Portal för att hantera moduler, utlösare och IoT Edge konfiguration och det lokala webb gränssnittet för att hantera beräknings nätverks inställningar.



## <a name="manage-triggers"></a>Hantera utlösare

Händelser är saker som sker i din molnmiljö eller på din enhet som du kanske vill vidta någon åtgärd för. När en fil skapas i en resurs är det till exempel en händelse. Utlösare utlöser de här händelserna. Utlösare för Azure Stack Edge Pro kan vara svar på fil händelser eller ett schema.

- **Fil**: dessa utlösare är svar på fil händelser som att skapa en fil, ändra en fil.
- **Schemalagd**: dessa utlösare är som svar på ett schema som du kan definiera med start datum, start tid och upprepnings intervallet.


### <a name="add-a-trigger"></a>Lägga till en utlösare

Utför följande steg i Azure Portal för att skapa en utlösare.

1. I Azure Portal går du till Azure Stack Edge-resursen och går sedan till **IoT Edge**. Gå till **utlösare** och välj **+ Lägg till utlösare** i kommando fältet.

    ![Välj Lägg till utlösare](media/azure-stack-edge-gpu-manage-compute/add-trigger-1-m.png)

2. Ange ett unikt namn för utlösaren i bladet **Lägg till utlösare** .
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Välj en **typ** för utlösaren. Välj **fil** när utlösaren är svar på en fil händelse. Välj **schemalagd** när du vill att utlösaren ska starta vid en angiven tidpunkt och köras vid ett angivet upprepnings intervall. Beroende på ditt val visas en annan uppsättning alternativ.

    - **Fil utlösare** – Välj i list rutan en monterad resurs. När en fil händelse utlöses i den här resursen anropar utlösaren en Azure-funktion.

        ![Lägga till SMB-resurs](media/azure-stack-edge-gpu-manage-compute/add-file-trigger.png)

    - **Schemalagd utlösare** – ange start datum/-tid och upprepnings intervall i timmar, minuter eller sekunder. Ange också namnet på ett ämne. I ett avsnitt får du flexibiliteten att dirigera utlösaren till en modul som distribuerats på enheten.

        Ett exempel på en väg sträng är: `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"` .

        ![Lägga till NFS-resurs](media/azure-stack-edge-gpu-manage-compute/add-scheduled-trigger.png)

4. Välj **Lägg till** för att skapa utlösaren. Ett meddelande visar att utlösaren håller på att skapas. När utlösaren har skapats, uppdateras bladet för att återspegla den nya utlösaren.
 
    ![Uppdaterad utlösare](media/azure-stack-edge-gpu-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Ta bort en utlösare

Utför följande steg i Azure Portal för att ta bort en utlösare.

1. Välj den utlösare som du vill ta bort i listan över utlösare.

    ![Välj utlösare](media/azure-stack-edge-gpu-manage-compute/delete-trigger-1.png)

2. Högerklicka på och välj sedan **ta bort**.

    ![Välj Ta bort](media/azure-stack-edge-gpu-manage-compute/delete-trigger-2.png)

3. Klicka på **Ja** när du uppmanas att bekräfta åtgärden.

    ![Bekräfta borttagning](media/azure-stack-edge-gpu-manage-compute/delete-trigger-3.png)

Listan över utlösare uppdateras för att återspegla borttagningen.

## <a name="manage-iot-edge-configuration"></a>Hantera IoT Edge konfiguration

Använd Azure Portal för att Visa beräknings konfigurationen, ta bort en befintlig beräknings konfiguration eller uppdatera beräknings konfigurationen för att synkronisera åtkomst nycklar för IoT-enheten och IoT Edge enheten för din Azure Stack Edge Pro.

### <a name="view-iot-edge-configuration"></a>Visa IoT Edge konfiguration

Utför följande steg i Azure Portal för att Visa IoT Edge konfigurationen för enheten.

1. I Azure Portal går du till Azure Stack Edge-resursen och går sedan till **IoT Edge**. När IoT Edge tjänsten är aktive rad på din enhet anger sidan Översikt att IoT Edges tjänsten fungerar bra.

    ![Välj Visa beräkning](media/azure-stack-edge-gpu-manage-compute/view-compute-1.png)

2. Gå till **Egenskaper** för att Visa IoT Edge-konfigurationen på enheten. När du har konfigurerat Compute skapade du en IoT Hub-resurs. Under den IoT Hub resursen konfigureras en IoT-enhet och en IoT Edge enhet. Endast Linux-moduler stöds för att köras på den IoT Edge enheten.

    ![Visa konfiguration](media/azure-stack-edge-gpu-manage-compute/view-compute-2.png)


### <a name="remove-iot-edge-service"></a>Ta bort IoT Edge tjänst

Utför följande steg i Azure Portal för att ta bort den befintliga IoT Edges konfigurationen för enheten.

1. I Azure Portal går du till Azure Stack Edge-resursen och går sedan till **IoT Edge**. Gå till **Översikt** och välj **ta bort** i kommando fältet.

    ![Välj Ta bort beräkning](media/azure-stack-edge-gpu-manage-compute/remove-compute-1.png)

2. Om du tar bort tjänsten IoT Edge går det inte att ångra åtgärden. De moduler och utlösare som du har skapat tas också bort. Du måste konfigurera om enheten om du behöver använda IoT Edge igen. När du uppmanas att bekräfta, väljer du **OK**.

    ![Välj Ta bort beräkning 2](media/azure-stack-edge-gpu-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Synkronisera IoT-enheten och IoT Edge enhets åtkomst nycklar

När du konfigurerar Compute på Azure Stack Edge Pro skapas en IoT-enhet och en IoT Edge enhet. De här enheterna tilldelas automatiskt symmetriska åtkomst nycklar. Som en säkerhets åtgärd roteras dessa nycklar regelbundet via IoT Hub tjänsten.

Om du vill rotera dessa nycklar går du till IoT Hub tjänst som du har skapat och väljer IoT-enheten eller IoT Edge enheten. Varje enhet har en primär åtkomst nyckel och sekundära åtkomst nycklar. Tilldela den primära åtkomst nyckeln till den sekundära åtkomst nyckeln och återskapa den primära åtkomst nyckeln.

Om din IoT-enhet och IoT Edge enhets nycklar har roterats måste du uppdatera konfigurationen på din Azure Stack Edge Pro för att få de senaste åtkomst nycklarna. Synkroniseringen hjälper enheten att hämta de senaste nycklarna för din IoT-enhet och IoT Edge enhet. Azure Stack Edge Pro använder bara de primära åtkomst nycklarna.

Utför följande steg i Azure Portal för att synkronisera åtkomst nycklarna för enheten.

1. I Azure Portal går du till Azure Stack Edge-resursen och går sedan till **IoT Edge Compute**. Gå till **Översikt** och välj **Uppdatera konfiguration** i kommando fältet.

    ![Välj Uppdatera konfiguration](media/azure-stack-edge-gpu-manage-compute/refresh-configuration-1.png)

2. Välj **Ja** när du uppmanas att bekräfta.

    ![Välj Ja när du tillfrågas](media/azure-stack-edge-gpu-manage-compute/refresh-configuration-2.png)

3. Avsluta dialogrutan när synkroniseringen är klar.

## <a name="change-external-service-ips-for-containers"></a>Ändra IP-adresser för externa tjänster för behållare

Kubernetes external service IP-adresser används för att kontakta tjänster som exponeras utanför Kubernetes-klustret. När enheten har Aktiver ATS kan du ange eller ändra de externa IP-adresserna för arbets belastningar för behållare för din enhet genom att använda det lokala användar gränssnittet.


1. I enhetens lokala användar gränssnitt går du till **Compute**.
1. Välj den port vars nätverk har kon figurer ATS för beräkning. I bladet som öppnas, anger du (ny) eller ändrar (om det finns befintliga) Kubernetes external service IP-adresser. Dessa IP-adresser används för alla tjänster som behöver exponeras utanför Kubernetes-klustret. 
    - Du behöver minst 1 tjänst-IP för `edgehub` tjänsten som körs på enheten och används av IoT Edge moduler. 
    - Du behöver en IP-adress för varje ytterligare IoT Edge modul eller behållare som du tänker distribuera. 
    - Dessa är statiska och sammanhängande IP-adresser.

    ![Ändra Kubernetes-tjänstens IP-adresser](media/azure-stack-edge-gpu-manage-compute/change-service-ips-1.png)

1. Välj **Använd**. När IP-adresserna har tillämpats behöver enheten inte startas om eller startas om. Nya IP-adresser börjar gälla omedelbart.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [felsöker din Azure Stack Edge Pro](azure-stack-edge-gpu-troubleshoot.md).
