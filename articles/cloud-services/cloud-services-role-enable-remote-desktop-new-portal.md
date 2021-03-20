---
title: Använd portalen för att aktivera fjärr skrivbord för en roll
description: Så här konfigurerar du Azure Cloud Service-programmet för att tillåta fjärr skrivbords anslutningar
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 8fa0d3c0e29c53e6fe9cb32ddf02168686be1efe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743261"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic"></a>Aktivera Anslutning till fjärrskrivbord för en roll i Azure Cloud Services (klassisk)

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

> [!div class="op_single_selector"]
> * [Azure-portalen](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Med fjärr skrivbord kan du komma åt Skriv bordet för en roll som körs i Azure. Du kan använda en anslutning till fjärr skrivbord för att felsöka och diagnostisera problem med programmet medan det körs.

Du kan aktivera en anslutning till fjärr skrivbord i din roll under utvecklingen genom att inkludera fjärr skrivbords-moduler i din tjänst definition eller så kan du välja att aktivera fjärr skrivbord via fjärr skrivbords tillägget. Den bästa metoden är att använda fjärr skrivbords tillägget som du kan aktivera fjärr skrivbord även efter att programmet har distribuerats utan att du behöver distribuera om ditt program.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Konfigurera fjärr skrivbord från Azure Portal

I Azure Portal används metoden för fjärr skrivbords tillägg så att du kan aktivera fjärr skrivbord även efter att programmet har distribuerats. Med **fjärr skrivbords** inställningarna för moln tjänsten kan du aktivera fjärr skrivbord, ändra det lokala administratörs kontot som används för att ansluta till de virtuella datorerna, certifikatet som används i autentisering och ange förfallo datum.

1. Klicka på **Cloud Services**, Välj namnet på moln tjänsten och välj sedan **fjärr skrivbord**.

    ![bild som visar moln tjänster, fjärr skrivbord](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Välj om du vill aktivera fjärr skrivbord för en enskild roll eller för alla roller och ändra värdet för växlaren till **aktive rad**.

3. Fyll i de obligatoriska fälten för användar namn, lösen ord, förfallo datum och certifikat.

    ![Moln tjänster, fjärr skrivbord](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Alla roll instanser kommer att startas om när du först aktiverar fjärr skrivbord och väljer **OK** (bock). För att förhindra en omstart måste certifikatet som används för att kryptera lösen ordet vara installerat på rollen. Du kan förhindra en omstart genom att [överföra ett certifikat för moln tjänsten](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) och sedan gå tillbaka till den här dialog rutan.

4. I **roller** väljer du den roll som du vill uppdatera eller väljer **alla** för alla roller.

5. När du är klar med konfigurations uppdateringarna väljer du **Spara**. Det tar en stund innan roll instanserna är redo att ta emot anslutningar.

## <a name="remote-into-role-instances"></a>Fjärran slut till roll instanser

När fjärr skrivbord är aktiverat på rollerna kan du starta en anslutning direkt från Azure Portal:

1. Öppna **instans** inställningarna genom att klicka på **instanser** .
2. Välj en roll instans som har fjärr skrivbord konfigurerat.
3. Klicka på **Anslut** för att ladda ned en RDP-fil för roll instansen.

    ![Bild av fjärr skrivbord i Cloud Services](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Klicka på **Öppna** och **Anslut** för att starta anslutningen till fjärr skrivbordet.

>[!NOTE]
> Om din moln tjänst är placerad bakom en NSG kan du behöva skapa regler som tillåter trafik på portarna **3389** och **20000**.  Fjärr skrivbord använder port **3389**.  Moln tjänst instanser är belastningsutjämnade, så du kan inte styra vilken instans som ska anslutas direkt.  *RemoteForwarder* -och *RemoteAccess* -agenterna hanterar RDP-trafik och gör att klienten kan skicka en RDP-cookie och ange en enskild instans att ansluta till.  *RemoteForwarder* -och *RemoteAccess* -agenterna kräver att port **20000*** är öppen, vilket kan blockeras om du har en NSG.

## <a name="additional-resources"></a>Ytterligare resurser

[Så här konfigurerar du Cloud Services](cloud-services-how-to-configure-portal.md)
