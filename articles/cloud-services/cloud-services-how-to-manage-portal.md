---
title: Vanliga hanterings uppgifter för moln tjänster | Microsoft Docs
description: Lär dig hur du hanterar Cloud Services i Azure Portal. I de här exemplen används Azure Portal.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 306da8dae31c2bbdb487e7128e3a2e24424c239d
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743397"
---
# <a name="manage-cloud-services-classic-in-the-azure-portal"></a>Hantera Cloud Services (klassisk) i Azure Portal

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

I **Cloud Servicess** delen av Azure Portal kan du:

* Uppdatera en tjänst roll eller en distribution.
* Höj upp en mellanlagrad distribution till produktion.
* Länka resurser till moln tjänsten så att du kan se resurs beroenden och skala resurserna tillsammans.
* Ta bort en moln tjänst eller en distribution.

Mer information om hur du skalar moln tjänsten finns i [Konfigurera automatisk skalning för en moln tjänst i portalen](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Uppdatera en moln tjänst roll eller distribution
Om du behöver uppdatera program koden för moln tjänsten använder du **Uppdatera** på bladet moln tjänst. Du kan uppdatera en enskild roll eller alla roller. Om du vill uppdatera kan du ladda upp ett nytt tjänst paket eller en tjänst konfigurations fil.

1. I [Azure Portal][Azure portal]väljer du den moln tjänst som du vill uppdatera. Det här steget öppnar bladet moln tjänst instans.

2. På bladet väljer du **Uppdatera**.

    ![Knappen Uppdatera](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Uppdatera distributionen med en ny Service Pack-fil (. cspkg) och tjänst konfigurations fil (. cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Du kan också uppdatera lagrings kontot och distributions etiketten.

5. Om några roller bara har en roll instans markerar du kryss rutan **distribuera även om en eller flera roller innehåller en enda instans** för att aktivera uppgraderingen för att fortsätta.

    Azure kan endast garantera 99,95 procents tillgänglighet för tjänster under en moln tjänst uppdatering om varje roll har minst två roll instanser (virtuella datorer). Med två roll instanser bearbetar en virtuell dator klient begär Anden medan den andra uppdateras.

6. Markera kryss rutan **Starta distribution** om du vill tillämpa uppdateringen när paketet har laddats upp är klart.

7. Välj **OK** för att börja uppdatera tjänsten.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Växla distributioner för att befordra en mellanlagrad distribution till produktion
När du bestämmer dig för att distribuera en ny version av en moln tjänst kan du mellanlagra och testa din nya version i moln tjänstens utvecklings miljö. Använd **Växla** för att växla de URL: er som de två distributionerna riktas mot och befordra en ny version till produktion.

Du kan växla distributioner från **Cloud Services** sidan eller instrument panelen.

1. I [Azure Portal][Azure portal]väljer du den moln tjänst som du vill uppdatera. Det här steget öppnar bladet moln tjänst instans.

2. På bladet väljer du **Växla**.

    ![Knapp för Cloud Services växling](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Följande bekräftelse meddelande öppnas:

    ![Cloud Services växling](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. När du har kontrollerat distributions informationen väljer du **OK** för att växla distributioner.

    Distributions växlingen sker snabbt eftersom det enda som ändras är de virtuella IP-adresserna (VIP) för distributionerna.

    För att spara beräknings kostnader kan du ta bort mellanlagrings distributionen när du har kontrollerat att produktions distributionen fungerar som förväntat.

### <a name="common-questions-about-swapping-deployments"></a>Vanliga frågor om hur du byter distributioner

**Vilka är kraven för att byta distribution?**

Det finns två viktiga krav för en lyckad distributions växling:

- Om du vill använda en statisk IP-adress för din produktions plats måste du även reservera en för mellanlagringsplatsen. Annars Miss lyckas växlingen.

- Alla instanser av dina roller måste köras innan du kan utföra växlingen. Du kan kontrol lera status för dina instanser på bladet **Översikt** i Azure Portal. Du kan också använda kommandot [Get-AzureRole](/powershell/module/servicemanagement/azure.service/get-azurerole?view=azuresmps-3.7.0&preserve-view=true) i Windows PowerShell.

Observera att gäst operativ system uppdateringar och tjänst återställnings åtgärder också kan orsaka att distributions växlingar inte fungerar. Mer information finns i [Felsöka problem med distribution av moln tjänster](cloud-services-troubleshoot-deployment-problems.md).

**Uppstår avbrott för mitt program i byte? Hur ska jag hantera det?**

Som det beskrivs i föregående avsnitt är en distributions växling vanligt vis snabb eftersom det bara är en konfigurations ändring i Azure Load Balancer. I vissa fall kan det ta 10 sekunder och leda till tillfälliga anslutnings problem. Om du vill begränsa påverkan till dina kunder kan du överväga att implementera [logik för omprövning av klienter](/azure/architecture/best-practices/transient-faults).

## <a name="delete-deployments-and-a-cloud-service"></a>Ta bort distributioner och en moln tjänst
Innan du kan ta bort en moln tjänst måste du ta bort varje befintlig distribution.

För att spara beräknings kostnader kan du ta bort mellanlagrings distributionen när du har kontrollerat att produktions distributionen fungerar som förväntat. Du debiteras för beräknings kostnader för distribuerade roll instanser som har stoppats.

Använd följande procedur för att ta bort en distribution eller din moln tjänst.

1. I [Azure Portal][Azure portal]väljer du den moln tjänst som du vill ta bort. Det här steget öppnar bladet moln tjänst instans.

2. Välj **ta bort** på bladet.

    ![Knappen Cloud Services ta bort](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Om du vill ta bort hela moln tjänsten markerar du kryss rutan **moln tjänst och dess distributioner** . Eller så kan du välja antingen kryss rutan **produktions distribution** eller **mellanlagrings distribution** .

    ![Cloud Services ta bort](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Välj **ta bort** längst ned.

5. Om du vill ta bort moln tjänsten väljer du **ta bort moln tjänst**. Sedan väljer du **Ja** i bekräftelse meddelandet.

> [!NOTE]
> När en moln tjänst tas bort och utförlig övervakning har kon figurer ATS måste du ta bort data manuellt från ditt lagrings konto. Information om var du hittar mått tabeller finns i [Introduktion till moln tjänst övervakning](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>Hitta mer information om misslyckade distributioner
Bladet **Översikt** har ett statusfält överst. När du väljer stapeln öppnas ett nytt blad och visar eventuell fel information. Om distributionen inte innehåller några fel är informations bladet tomt.

![Översikt över Cloud Services](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Nästa steg
* [Allmän konfiguration av din moln tjänst](cloud-services-how-to-configure-portal.md).
* Lär dig hur du [distribuerar en moln tjänst](cloud-services-how-to-create-deploy-portal.md).
* Konfigurera ett [anpassat domän namn](cloud-services-custom-domain-name-portal.md).
* Konfigurera [TLS/SSL-certifikat](cloud-services-configure-ssl-certificate-portal.md).