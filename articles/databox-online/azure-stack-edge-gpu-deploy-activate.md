---
title: Självstudie för att aktivera Azure Stack Edge Pro-enhet med GPU i Azure Portal | Microsoft Docs
description: Självstudie för att distribuera Azure Stack Edge Pro GPU instruerar dig att aktivera den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/07/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: c3989b3d4b148dcffccb03fb5b4279c89e5c686d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562400"
---
# <a name="tutorial-activate-azure-stack-edge-pro-with-gpu"></a>Självstudie: Aktivera Azure Stack Edge Pro med GPU

I den här självstudien beskrivs hur du kan aktivera din Azure Stack Edge Pro-enhet med en onboard-GPU med hjälp av det lokala webb gränssnittet.

Aktiverings processen kan ta cirka 5 minuter att slutföra.

I den här självstudien lärde du dig att:

> [!div class="checklist"]
> * Förutsättningar
> * Aktivera den fysiska enheten

## <a name="prerequisites"></a>Förutsättningar

Innan du konfigurerar och konfigurerar din Azure Stack Edge Pro-enhet med GPU måste du kontrol lera att:

* För den fysiska enheten: 
    
    - Du har installerat den fysiska enheten enligt beskrivningen i [installera Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md).
    - Du har konfigurerat nätverks-och beräknings nätverks inställningarna enligt beskrivningen i [Konfigurera nätverk, beräknings nätverk, webbproxy](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
    - Du har laddat upp dina egna eller genererat enhets certifikat på enheten om du har ändrat enhets namnet eller DNS-domänen via **enhets** sidan. Om du inte har gjort det här steget visas ett fel meddelande när enheten aktive ras och aktiveringen blockeras. Mer information finns i [Konfigurera certifikat](azure-stack-edge-gpu-deploy-configure-certificates.md).
    
* Du har aktiverings nyckeln från Azure Stack Edge-tjänsten som du skapade för att hantera Azure Stack Edge Pro-enheten. Mer information finns i [förbereda för att distribuera Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md).


## <a name="activate-the-device"></a>Aktivera enheten

1. Gå till sidan **Kom igång** i enhetens lokala webb gränssnitt.
2. På **aktiverings** panelen väljer du **Aktivera**. 

    ![Sidan Cloud information för det lokala webb gränssnittet](./media/azure-stack-edge-gpu-deploy-activate/activate-1.png)
    
3. I fönstret **Aktivera** anger du **aktiverings nyckeln** som du fick i [hämta aktiverings nyckeln för Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).

4. Välj **Använd**.

    ![Lokal webb gränssnitt "moln information" sida 2](./media/azure-stack-edge-gpu-deploy-activate/activate-2.png)


5. Först är enheten aktive rad. Du uppmanas sedan att ladda ned nyckel filen.
    
    ![Lokal webb gränssnitt "moln information" sida 3](./media/azure-stack-edge-gpu-deploy-activate/activate-3.png)
    
    Välj **Hämta och fortsätt** och spara filen *device-serial-no.jspå* en säker plats utanför enheten. **Den här nyckel filen innehåller återställnings nycklarna för operativ system disken och data diskarna på enheten**. Dessa nycklar kan behövas för att under lätta en framtida system återställning.

    Här följer innehållet i *JSON* -filen:

        
    ```json
    {
      "Id": "<Device ID>",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "<BitLocker key for data disk>",
        "hcsdata": "<BitLocker key for data disk>"
      },
      "SystemVolumeBitLockerRecoveryKey": "<BitLocker key for system volume>",
      "ServiceEncryptionKey": "<Azure service encryption key>"
    }
    ```
        
 
    I följande tabell förklaras de olika nycklarna:
    
    |Fält  |Beskrivning  |
    |---------|---------|
    |`Id`    | Detta är ID för enheten.        |
    |`DataVolumeBitLockerExternalKeys`|Detta är BitLocker-nycklar för data diskarna och används för att återställa lokala data på enheten.|
    |`SystemVolumeBitLockerRecoveryKey`| Det här är BitLocker-nyckeln för system volymen. Den här nyckeln hjälper till att återställa system konfigurationen och system data för enheten. |
    |`ServiceEncryptionKey`| Den här nyckeln skyddar de data som flödar genom Azure-tjänsten. Den här nyckeln garanterar att en kompromiss i Azure-tjänsten inte resulterar i att lagrad information komprometteras. |

6. Gå till **översikts** sidan. Enhetens tillstånd bör visas som **aktive rad**.

    ![Lokal webb gränssnitt "moln information" sida 4](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
Enhets aktiveringen har slutförts. Nu kan du lägga till resurser på enheten.

Om du stöter på problem under aktiveringen går du till [Felsöka aktivering och Azure Key Vault fel](azure-stack-edge-gpu-troubleshoot-activation.md#activation-errors).

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att:

> [!div class="checklist"]
> * Förutsättningar
> * Aktivera den fysiska enheten

Information om hur du överför data med din Azure Stack Edge Pro-enhet finns i:

> [!div class="nextstepaction"]
> [Överföra data med Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-add-shares.md)