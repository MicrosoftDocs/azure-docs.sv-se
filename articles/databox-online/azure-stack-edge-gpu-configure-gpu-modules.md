---
title: Köra en GPU-modul på Microsoft Azure Stack Edge Pro GPU-enhet | Microsoft Docs
description: Beskriver hur du konfigurerar och kör en modul på GPU på en Azure Stack Edge Pro-enhet via Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: 348ddff56ed61cd608d6b9f28417e7cd4c4e6b13
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563971"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-pro-device"></a>Konfigurera och köra en modul på GPU på Azure Stack Edge Pro-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Din Azure Stack Edge Pro-enhet innehåller en eller flera GPU: er (Graphics Processing Unit). GPU:er är ett populärt alternativ för AI-beräkningar som erbjuder parallell bearbetning och snabbare bildåtergivning än processorer (CPU:er). Om du vill ha mer information om GPU: n som finns i din Azure Stack Edge Pro-enhet går du till [Azure Stack tekniska specifikationer för Edge Pro-enhet](azure-stack-edge-gpu-technical-specifications-compliance.md).

Den här artikeln beskriver hur du konfigurerar och kör en modul på GPU: n på din Azure Stack Edge Pro-enhet. I den här artikeln ska du använda en offentligt tillgänglig **siffra** för container som skrivits för NVIDIA T4-GPU: er. Den här proceduren kan användas för att konfigurera andra moduler som publiceras av NVIDIA för dessa GPU: er.


## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har åtkomst till en GPU-aktiverad 1-Node Azure Stack Edge Pro-enhet. Enheten aktiveras med en resurs i Azure.  

## <a name="configure-module-to-use-gpu"></a>Konfigurera en modul för att använda GPU

Konfigurera en modul så att den använder GPU: n på din Azure Stack Edge Pro-enhet för att köra en modul<!--Can it be simplified? "To configure a module to be run by the GPU on your Azure Stack Edge Pro device,"?--> Följ de här stegen.

1. I Azure Portal går du till den resurs som är kopplad till din enhet.

2. I **Översikt** väljer du **IoT Edge**.

    ![Konfigurera modulen för att använda GPU 1](media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-1.png)

3. I **aktivera IoT Edge tjänst** väljer du **Lägg till**.

   ![Konfigurera modulen för att använda GPU 2](media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-2.png)

4. I **skapa IoT Edge tjänst** anger du inställningarna för din IoT Hub-resurs:

   |Fält   |Värde    |
   |--------|---------|
   |Prenumeration      | Prenumeration som används av Azure Stack Edge-resursen. |
   |Resursgrupp    | Resurs grupp som används av Azure Stack Edge-resursen. |
   |IoT Hub           | Välj från **Skapa ny** eller **Använd befintlig**. <br> Som standard används nivån Standard (S1) till att skapa en IoT-resurs. Om du vill använda en IoT-resurs på kostnadsfri nivå skapar du en sådan och väljer sedan den befintliga resursen. <br> I båda fallen använder IoT Hub-resursen samma prenumeration och resursgrupp som Azure Stack Edge-resursen använder.     |
   |Name              | Om du inte vill använda det standard namn som angetts för en ny IoT Hub resurs anger du ett annat namn. |

   När du är klar med inställningarna väljer du **Granska + skapa**. Granska inställningarna för din IoT Hub resurs och välj **skapa**.

   ![Kom igång med Compute 2](./media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-3.png)

   Det tar flera minuter att skapa en resurs för en IoT Hub resurs. När resursen har skapats visar **översikten** IoT Edges tjänsten körs nu.

   ![Kom igång med Compute 3](./media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-4.png)

5. För att bekräfta att Edge Compute-rollen har kon figurer ATS, väljer du **Egenskaper**.

   ![Kom igång med Compute 4](./media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-5.png)

6. I **Egenskaper** väljer du länken för **IoT Edge enhet**.

   ![Konfigurera modulen för att använda GPU 6](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-2.png)

   I den högra rutan ser du den IoT Edge enhet som är kopplad till din Azure Stack Edge Pro-enhet. Enheten motsvarar IoT Edge enhet som du skapade när du skapade IoT Hub resursen.
 
7. Välj den här IoT Edge enheten.

   ![Konfigurera modulen för att använda GPU 7](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-3.png)

8. Välj **Ange moduler**.

   ![Konfigurera modulen för att använda GPU 8](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-4.png)

9. Välj **+ Lägg till** och välj sedan **+ IoT Edge modul**. 

    ![Konfigurera modulen för att använda GPU 9](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-5.png)

10. På fliken **Lägg till IoT Edge modul** :

    1. Ange **avbildnings-URI: n**. Du kommer att använda de allmänt tillgängliga NVIDIA-modulernas **siffror** här. 
    
    2. Ange att **principen för omstart** är **alltid**.
    
    3. Ange **önskat tillstånd** för att **köra**.
    
    ![Konfigurera modulen för att använda GPU 10](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-6.png)

11. På fliken **miljövariabler** anger du namnet på variabeln och motsvarande värde. 

    1. Använd NVIDIA_VISIBLE_DEVICES om du vill att den aktuella modulen ska använda en GPU på den här enheten. 

    2. Ange värdet till 0 eller 1. Värdet 0 eller 1 garanterar att minst en GPU används av enheten för den här modulen. När du ställer in värdet på 0, 1, betyder det att både GPU på enheten används av den här modulen.

       ![Konfigurera modulen för att använda GPU 11](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-7.png)

       Mer information om miljövariabler som du kan använda med nVidia-GPU: n finns i [NVIDIA container runtime](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).

    > [!NOTE]
    > En modul kan använda en, båda eller inga GPU: er.

12. Ange ett namn för modulen. Nu kan du välja att tillhandahålla alternativet för att skapa behållare och ändra modulens dubbla inställningar eller om det är färdigt väljer du **Lägg till**. 

    ![Konfigurera modulen för att använda GPU 12](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-8.png)

13. Kontrol lera att modulen körs och välj **Granska + skapa**.

    ![Konfigurera modulen för att använda GPU 13](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-9.png)

14. På fliken **Granska + skapa** visas de distributions alternativ som du har valt. Granska alternativen och välj **skapa**.
    
    ![Konfigurera modulen för att använda GPU 14](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-10.png)

15. Anteckna **körnings status** för modulen.
    
    ![Konfigurera modulen för att använda GPU 15](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-11.png)

    Det tar några minuter för modulen att distribueras. Välj **Uppdatera** och se till att **körnings status** uppdateringen **körs**.

    ![Konfigurera modulen för att använda GPU 16](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [miljövariabler som du kan använda med nVidia GPU](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).
