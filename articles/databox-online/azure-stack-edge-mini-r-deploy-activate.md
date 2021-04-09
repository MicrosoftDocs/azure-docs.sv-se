---
title: Självstudie för att kryptera och aktivera Azure Stack Edge-Mini R-enheten i Azure Portal | Microsoft Docs
description: Självstudie för att distribuera Azure Stack Edge Mini R instruerar dig att kryptera och aktivera den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: 483bf27b68e50c751c8dea1bf9375a95ed2a051f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558813"
---
# <a name="tutorial-activate-azure-stack-edge-mini-r"></a>Självstudie: Aktivera Azure Stack Edge Mini R

I den här självstudien beskrivs hur du kan aktivera din Azure Stack Edge Mini R-enhet med hjälp av det lokala webb gränssnittet.

Aktiverings processen kan ta cirka 15 minuter att slutföra.

I den här självstudien lärde du dig att:

> [!div class="checklist"]
> * Förutsättningar
> * Aktivera den fysiska enheten

## <a name="prerequisites"></a>Förutsättningar

Innan du konfigurerar och konfigurerar din Azure Stack Edge Mini R-enhet ser du till att:

* För den fysiska enheten: 
    
    - Du har installerat den fysiska enheten enligt beskrivningen i [installera Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-install.md).
    - Du har konfigurerat nätverks-och beräknings nätverks inställningarna enligt beskrivningen i [Konfigurera nätverk, beräknings nätverk, webbproxy](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)
    - Du har laddat upp dina egna certifikat på enheten om du har ändrat enhets namnet eller DNS-domänen via **enhets** sidan. De här stegen beskrivs i [Konfigurera certifikat, VPN och kryptering vid vila](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md). Om du inte har gjort det här steget kommer aktiveringen att blockeras.
    - Du har konfigurerat kryptering vid vila för enheten. Om du inte har gjort det här steget visas ett fel meddelande när enheten aktive ras och aktiveringen blockeras. Mer information finns i [Konfigurera certifikat, VPN och kryptering vid vila](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md).
    
* Du har aktiverings nyckeln från Azure Stack Edge-tjänsten som du skapade för att hantera Azure Stack Edge-Mini R-enheten. Mer information finns i [förbereda för distribution Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-prep.md).


## <a name="activate-the-device"></a>Aktivera enheten

1. Gå till sidan **Kom igång** i enhetens lokala webb gränssnitt.
2. På **aktiverings** panelen väljer du **Aktivera**. 

    ![Lokal webb gränssnitt "moln information" sida 1](./media/azure-stack-edge-mini-r-deploy-activate/activation-1.png)
    
3. I fönstret **Aktivera** :
    1. Ange aktiverings **nyckeln** som du fick i [Hämta aktiverings nyckeln för Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

    1. Du kan aktivera proaktiv logg insamling för att låta Microsoft samla in loggar baserat på enhetens hälso status. Loggarna som samlas in på det här sättet överförs till ett Azure Storage-konto.
    
    1. Välj **Använd**.

    ![Lokal webb gränssnitt "moln information" sida 2](./media/azure-stack-edge-mini-r-deploy-activate/activation-2.png)


5. Först är enheten aktive rad. Du uppmanas sedan att ladda ned nyckel filen.
    
    ![Lokal webb gränssnitt "moln information" sida 3](./media/azure-stack-edge-mini-r-deploy-activate/activation-3.png)
    
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
      "SEDEncryptionExternalKey": "xZM/vC7GxjqHZ3VMo7xSs/wH9rRsF/PNM+mOsZ+GaL0=",
      "ServiceEncryptionKey": "<Azure service encryption key>"
    }
    ```        
 
    I följande tabell förklaras de olika nycklarna:
    
    |Fält  |Beskrivning  |
    |---------|---------|
    |`Id`    | Detta är ID för enheten.        |
    |`DataVolumeBitLockerExternalKeys`|Det här är BitLocker-nycklarna för data diskarna och används för att återställa lokala data på enheten.|
    |`SystemVolumeBitLockerRecoveryKey`| Det här är BitLocker-nyckeln för system volymen. Den här nyckeln hjälper till att återställa system konfigurationen och system data för enheten. |
    |`SEDEncryptionExternalKey`| Den här användaren eller den genererade nyckeln används för att skydda Självkrypterande data enheter som har inbyggd kryptering. |
    |`ServiceEncryptionKey`| Den här nyckeln skyddar de data som flödar genom Azure-tjänsten. Den här nyckeln garanterar att en kompromiss i Azure-tjänsten inte resulterar i att lagrad information komprometteras. |

6. Gå till **översikts** sidan. Enhetens tillstånd bör visas som **aktive rad**.

    ![Lokal webb gränssnitt "moln information" sida 4](./media/azure-stack-edge-mini-r-deploy-activate/activation-4.png)
 
Enhets aktiveringen har slutförts. Nu kan du lägga till resurser på enheten.

Om du stöter på problem under aktiveringen går du till [Felsöka aktivering och Azure Key Vault fel](azure-stack-edge-gpu-troubleshoot-activation.md#activation-errors).

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att:

> [!div class="checklist"]
> * Förutsättningar
> * Aktivera den fysiska enheten

Information om hur du överför data med din Azure Stack Edge Mini R-enhet finns i:

> [!div class="nextstepaction"]
> [Överföra data med Azure Stack Edge Mini R](./azure-stack-edge-gpu-deploy-add-shares.md)