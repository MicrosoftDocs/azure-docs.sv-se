---
title: Returnera din Azure Stack Edge Pro-enhet | Microsoft Docs
description: Lär dig hur du rensar data och returnerar Azure Stack Edge Pro-enheten och sedan tar bort resursen som är kopplad till enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.openlocfilehash: cb11d7d3b2da9ab793cb18814e4021ea7afeb806
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443598"
---
# <a name="return-your-azure-stack-edge-pro-device"></a>Returnera din Azure Stack Edge Pro-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

I den här artikeln beskrivs hur du rensar data och sedan returnerar din Azure Stack Edge Pro-enhet. När du har returnerat enheten kan du också ta bort resursen som är kopplad till enheten.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> * Rensa data från data diskarna på enheten
> * Initiera enhetsreturen i Azure-portalen
> * Packa upp enheten och Schemalägg en upphämtning
> * Ta bort resursen i Azure Portal

## <a name="erase-data-from-the-device"></a>Radera data från enheten

Om du vill rensa bort data från enhetens data diskar måste du återställa enheten.

Innan du återställer skapar du en kopia av lokala data på enheten om det behövs. Du kan kopiera data från enheten till en Azure Storage behållare. 

Du kan initiera enhets returen även innan enheten återställs.

Du kan återställa enheten i det lokala webb gränssnittet eller i PowerShell. PowerShell-instruktioner finns i [återställa din enhet](./azure-stack-edge-connect-powershell-interface.md#reset-your-device).

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

> [!NOTE]
> - Om du utbyter eller uppgraderar till en ny enhet rekommenderar vi att du återställer enheten först när du har fått den nya enheten.
> - Enhets återställningen tar bara bort alla lokala data från enheten. De data som finns i molnet tas inte bort och [kostnader](https://azure.microsoft.com/pricing/details/storage/)samlas in. Dessa data måste tas bort separat med ett hanterings verktyg för moln lagring som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="initiate-device-return"></a>Initiera enhets retur

Utför följande steg för att påbörja retur processen.

1. Gå till din Azure Stack Edge Pro/Data Box Gateway-resurs i Azure Portal. I **översikten** går du till kommando fältet i den högra rutan och väljer **RETUR enhet**. 

    ![Returnera enhet 1](media/azure-stack-edge-return-device/return-device-1.png)  

2. Under grundläggande information på bladet **RETUR enhet** , under **grundläggande information**:

    1. Ange enhetens serie nummer. Hämta enhetens serie nummer genom att gå till enhetens lokala webb gränssnitt och gå till **Översikt**.  
    
       ![Enhets serie nummer 1](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    2. Ange Service Tag-numret. Service Tag-numret är en identifierare med fem eller flera tecken, som är unik för din enhet. Service Tag-numret finns i det nedre högra hörnet på enheten (när du står inför enheten). Dra ut informations tag gen (den är en uppskjuten etikett panel). Den här panelen innehåller system information, till exempel service tag, NIC, MAC-adress och så vidare. 
    
       ![Service tag-nummer 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. Välj en orsak för returen i list rutan.

       ![Returnera enhet 2](media/azure-stack-edge-return-device/return-device-2.png) 

3. Under **leverans information**:

    1. Ange ditt namn, företagets namn och den fullständiga företags adressen. Ange en arbets telefon inklusive rikt nummer och ett e-post-ID för avisering.
    2. Om du behöver en retur frakt Box kan du begära den. Svara **Ja** på frågan **behöver en tom ruta för att returnera**.

    ![Returnera enhet 3](media/azure-stack-edge-return-device/return-device-3.png)

4. Läs igenom **Sekretess villkoren** och markera kryss rutan med den anteckning som du har granskat och godkänner sekretess villkoren.

5. Välj **Starta retur**.

    ![Returnera enhet 4](media/azure-stack-edge-return-device/return-device-4.png) 

6. När din enhets information har hämtats kan du meddela Azure Stack Edge Pro Operations-teamet via ett e-postmeddelande. Du kan använda ditt e-postprogram förutsatt att e-postprogrammet är installerat och konfigurerat. Du kan också kopiera data för att skapa och skicka ett e-postmeddelande.

    ![Returnera enhet 5](media/azure-stack-edge-return-device/return-device-5.png) 

7. När Azure Stack Edge Pro Operations-teamet får e-postmeddelandet, skickas en etikett för omvänd försändelse. När du får den här etiketten kan du schemalägga hämtningen av enheten med operatören. 

## <a name="schedule-a-pickup"></a>Schemalägg hämtning

Följ stegen nedan om du vill schemalägga en upphämtning.

1. Stäng av enheten. I det lokala webb gränssnittet går du till **underhåll > energi inställningar**.
2. Välj **Stäng av**. När du uppmanas att bekräfta klickar du på **Ja** för att fortsätta. Mer information finns i [hantera energi](../databox-gateway/data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Koppla bort ström kablar och ta bort alla nätverks kablar från enheten.
4. Förbered försändelse paketet genom att använda din egen ruta eller den tomma box som du fick från Azure. Placera enheten och de ström sladdar som levererades med enheten i rutan.
5. Fäst frakt sedeln som du fick från Azure på paketet.
6. Schemalägg en upphämtning med ditt regionala transportföretag. Om du returnerar enheten i USA, kan operatören vara UPS-eller FedEx. Så här schemalägger du en upphämtning med UPS:

    1. Ring din lokala UPS (lands-/regionspecifikt avgiftsfritt nummer).
    2. I ditt samtal offerterar du det omvända försändelse spårnings numret som det visas på den utskrivna etiketten.
    3. Om spårnings numret inte är citerat, kräver UPS att du betalar extra kostnad under upphämtningen.

    I stället för att schemalägga upphämtningen kan du också ta bort Azure Stack Edge Pro på närmsta plats.

## <a name="delete-the-resource"></a>Ta bort resursen

När enheten har tagits emot i Azure-datacentret, inspekteras enheten efter skada eller tecken på manipulation.

- Om enheten anländer och är i en lämplig form stoppas fakturerings mätaren för den resursen. Azure Stack Edge Pro Operations-teamet kommer att kontakta dig för att bekräfta att enheten returnerades. Du kan sedan ta bort resursen som är kopplad till enheten i Azure Portal.
- Om enheten kommer kraftigt skadas kan böter gälla. Mer information finns i [vanliga frågor och svar om förlorade eller skadade enhets](https://azure.microsoft.com/pricing/details/databox/edge/) [-och produkt villkor](https://www.microsoft.com/licensing/product-licensing/products).  


Du kan ta bort enheten i Azure Portal:

- När du har placerat en order och innan enheten förbereds av Microsoft.
- När du har returnerat en enhet till Microsoft och Azure Stack Edge Pro Operations-teamet har anropat för att bekräfta att enheten returnerades. Drift teamet anropas inte förrän den returnerade enheten skickar den fysiska kontrollen till Azure-datacentret.

Om du har aktiverat enheten mot en annan prenumeration eller plats flyttar Microsoft din beställning till den nya prenumerationen eller platsen inom en arbets dag. När ordningen har flyttats kan du ta bort den här resursen.


Utför följande steg för att ta bort enheten och resursen i Azure Portal.

1. I Azure Portal går du till din resurs och sedan till **Översikt**. I kommando fältet väljer du **ta bort**.

    ![Välj Ta bort](media/azure-stack-edge-return-device/delete-resource-1.png)

2. I bladet **ta bort enhet** skriver du namnet på den enhet som du vill ta bort och väljer **ta bort**.

    ![Bekräfta borttagning](media/azure-stack-edge-return-device/delete-resource-2.png)

Du får ett meddelande när enheten och den tillhör ande resursen har tagits bort.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [får en ersättnings Azure Stack Edge Pro-enhet](azure-stack-edge-replace-device.md).