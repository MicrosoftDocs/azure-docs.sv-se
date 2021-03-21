---
title: Självstudie för att ansluta till, konfigurera, aktivera Azure Stack Edge Pro R-enhet i Azure Portal | Microsoft Docs
description: Lär dig hur du kan ansluta till din Azure Stack Edge Pro R-enhet med hjälp av det lokala webb gränssnittet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: ce97c22cf4bfbe5cca01183574597706a8c239e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96468730"
---
# <a name="tutorial-connect-to-azure-stack-edge-pro-r"></a>Självstudie: Anslut till Azure Stack Edge Pro R

I den här självstudien beskrivs hur du kan ansluta till din Azure Stack Edge Pro R-enhet med hjälp av det lokala webb gränssnittet.

Den här anslutningsprocessen kan ta omkring 5 minuter att slutföra.

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Förutsättningar
> * Ansluta till en fysisk enhet


## <a name="prerequisites"></a>Förutsättningar

Innan du konfigurerar och konfigurerar din Azure Stack Edge Pro R-enhet ser du till att:

* Du har installerat den fysiska enheten enligt beskrivningen i [installera Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Anslut till konfiguration av lokalt webb gränssnitt

1. Konfigurera Ethernet-nätverkskortet i datorn för anslutning till Azure Stack Edge Pro R-enheten med den statiska IP-adressen 192.168.100.5 och undernätet 255.255.255.0.

2. Anslut datorn till PORT 1 på din enhet. Om du ansluter datorn till enheten direkt (utan någon switch) använder du en korskopplad kabel eller ett USB Ethernet-nätverkskort. Använd följande bild för att identifiera PORT 1 på enheten.

    ![Baksidan på en kabelansluten enhet](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)


3. Öppna en webbläsare och anslut till enhetens lokala webbgränssnitt på `https://192.168.100.10`.  
    Den här åtgärden kan ta några minuter efter att du har aktiverat enheten.

    Du ser ett fel eller en varning om ett problem med webbplatsens säkerhetscertifikat. 
   
    ![Webbplats säkerhets certifikat fel meddelande](media/azure-stack-edge-pro-r-deploy-connect/connect-web-ui-1.png)


4. Välj **Fortsätt till den här webb sidan**.  
    De här stegen kan variera beroende på vilken webbläsare du använder.

5. Logga in i enhetens webbgränssnitt. Standard lösen ordet är *Password1*. 
   
    ![Inloggnings sida för Azure Stack Edge-enhet](media/azure-stack-edge-pro-r-deploy-connect/connect-web-ui-3.png)

6. Ändra enhetens administratörs lösen ord vid prompten.  
    Det nya lösenordet måste innehålla mellan 8 och 16 tecken. Det måste innehålla tre av följande tecken: versaler, gemener, siffror och specialtecken.

Du är nu på sidan **Översikt** på enheten. Nästa steg är att konfigurera nätverks inställningarna för enheten.


## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att:

> [!div class="checklist"]
> * Förutsättningar
> * Ansluta till en fysisk enhet


Information om hur du konfigurerar nätverks inställningar på din Azure Stack Edge Pro R-enhet finns i:

> [!div class="nextstepaction"]
> [Konfigurera nätverk](./azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)
