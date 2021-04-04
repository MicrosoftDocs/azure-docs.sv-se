---
title: Så här konfigurerar du en moln tjänst (klassisk) – Portal | Microsoft Docs
description: Lär dig hur du konfigurerar moln tjänster i Azure. Lär dig att uppdatera moln tjänst konfigurationen och konfigurera fjärråtkomst till roll instanser. I de här exemplen används Azure Portal.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: f16fcfe227663958279281659b09929a4cd2d386
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743431"
---
# <a name="how-to-configure-and-azure-cloud-service-classic"></a>Konfigurera och Azure Cloud Service (klassisk)

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Du kan konfigurera de vanligaste inställningarna för en moln tjänst i Azure Portal. Eller, om du vill uppdatera konfigurationsfilerna direkt, kan du hämta en tjänst konfigurations fil som ska uppdateras och sedan ladda upp den uppdaterade filen och uppdatera moln tjänsten med konfigurations ändringarna. Oavsett hur du gör så skickas konfigurations uppdateringarna ut till alla roll instanser.

Du kan också hantera instanser av dina moln tjänst roller eller fjärr skrivbord i dem.

Azure kan bara se till att 99,95 procent är tillgänglig under konfigurations uppdateringarna om du har minst två roll instanser för varje roll. Detta gör att en virtuell dator kan bearbeta klient förfrågningar medan den andra uppdateras. Mer information finns i [service nivå avtal](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Ändra en moln tjänst

När du har öppnat [Azure Portal](https://portal.azure.com/)går du till moln tjänsten. Härifrån kan du hantera många delar av det.

![Sidan Inställningar](./media/cloud-services-how-to-configure-portal/cloud-service.png)

Länkarna **Inställningar** eller **alla inställningar** öppnar **Inställningar** där du kan ändra **egenskaperna**, ändra **konfigurationen**, hantera **certifikaten**, konfigurera **aviserings regler** och hantera de **användare** som har åtkomst till den här moln tjänsten.

![Azure Cloud Service-inställningar](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Hantera gäst operativ system version

Som standard uppdaterar Azure regelbundet ditt gäst operativ system till den senaste avbildning som stöds i OS-familjen som du har angett i tjänst konfigurationen (. cscfg), t. ex. Windows Server 2016.

Om du behöver ange en speciell operativ Systems version som mål kan du ange den i **konfigurationen**.

![Ange OS-version](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Om du väljer en annan operativ system version inaktive ras automatiska uppdateringar av operativ systemet och du får en korrigering av ditt ansvar. Du måste se till att roll instanserna tar emot uppdateringar eller att du kan exponera ditt program för säkerhets risker.

## <a name="monitoring"></a>Övervakning

Du kan lägga till aviseringar till moln tjänsten. Klicka på **Inställningar**  >  **varnings regler**  >  **Lägg till avisering**.

![Skärm bild av inställningarna panorera med alternativet varnings regler markerat och visas i rött och alternativet Lägg till avisering som anges i rött.](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Härifrån kan du konfigurera en avisering. Med list rutan **mått** kan du ställa in en avisering för följande typer av data.

* Disk läsning
* Disk skrivning
* Nätverk – inkommande
* Nätverk – utgående
* CPU-procent

![Skärm bild av fönstret Lägg till en varnings regel med alla konfigurations alternativ inställda.](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Konfigurera övervakning från en mått panel

I stället för att använda  >  **varnings regler** för inställningar kan du klicka på någon av mått panelerna i **övervaknings** avsnittet i moln tjänsten.

![Övervakning av moln tjänst](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Härifrån kan du anpassa diagrammet som används med panelen eller lägga till en varnings regel.

## <a name="reboot-reimage-or-remote-desktop"></a>Starta om, avbildning eller fjärr skrivbord

Du kan konfigurera fjärr skrivbord via [Azure Portal (Konfigurera fjärr skrivbord)](cloud-services-role-enable-remote-desktop-new-portal.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)eller via [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).

Om du vill starta om, återställa avbildningen eller fjärranslutna till en moln tjänst väljer du moln tjänst instansen.

![Moln tjänst instans](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Du kan sedan starta en anslutning till fjärr skrivbord, fjärrstarta instansen eller fjärravbildningen (börja med en ny avbildning) instansen.

![Knappar för moln tjänst instans](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Konfigurera om. cscfg

Du kan behöva konfigurera om moln tjänsten via [tjänst konfigurations filen (cscfg)](cloud-services-model-and-package.md#cscfg) . Först måste du ladda ned. cscfg-filen, ändra den och sedan ladda upp den.

1. Klicka på ikonen **Inställningar** eller länken **alla inställningar** för att öppna **Inställningar**.

    ![Sidan Inställningar](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Klicka på **konfigurationsobjektet** .

    ![Konfigurations blad](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Klicka på knappen **Hämta**.

    ![Ladda ned](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. När du har uppdaterat tjänst konfigurations filen laddar du upp och tillämpar konfigurations uppdateringarna:

    ![Ladda upp](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Välj. cscfg-filen och klicka på **OK**.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [distribuerar en moln tjänst](cloud-services-how-to-create-deploy-portal.md).
* Konfigurera ett [anpassat domän namn](cloud-services-custom-domain-name-portal.md).
* [Hantera din moln tjänst](cloud-services-how-to-manage-portal.md).
* Konfigurera [TLS/SSL-certifikat](cloud-services-configure-ssl-certificate-portal.md).



