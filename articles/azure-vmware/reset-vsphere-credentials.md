---
title: Återställ vSphere-autentiseringsuppgifter för Azure VMware-lösning
description: Lär dig hur du återställer vSphere-autentiseringsuppgifter för ditt privata moln i Azure VMware-lösningen och se till att HCX-anslutningen har de senaste vSphere-autentiseringsuppgifterna.
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: e5a15caa98a46b0ae75b68ee7b568dabdbf1956c
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103603273"
---
# <a name="reset-vsphere-credentials-for-azure-vmware-solution"></a>Återställ vSphere-autentiseringsuppgifter för Azure VMware-lösning

I den här artikeln ska vi gå igenom stegen för att återställa vSphere-autentiseringsuppgifterna för ditt privata moln i Azure VMware-lösningen. Detta gör att du kan se till att HCX-anslutningen har de senaste vSphere-autentiseringsuppgifterna.

## <a name="reset-your-vsphere-credentials"></a>Återställ dina vSphere-autentiseringsuppgifter

 Först ska vi återställa dina vSphere-autentiseringsuppgifter. Dina vCenter-CloudAdmin och NSX-T-admin-autentiseringsuppgifter upphör inte; Du kan dock följa de här stegen för att generera nya lösen ord för dessa konton.

> [!NOTE]
> Om du använder dina CloudAdmin-autentiseringsuppgifter för anslutna tjänster som HCX, vCenter Orchestrator, vCloud Director eller vRealize slutar dina anslutningar att fungera när du har uppdaterat ditt lösen ord.  De här tjänsterna bör stoppas innan du initierar lösen ords rotationen.  Om du inte gör det kan det leda till tillfälliga lås på dina vCenter-CloudAdmin och NSX-T-administratörskonton, eftersom dessa tjänster kontinuerligt kommer att anropas med dina gamla autentiseringsuppgifter.  Mer information om hur du konfigurerar separata konton för anslutna tjänster finns i [åtkomst-och identitets koncept](https://docs.microsoft.com/azure/azure-vmware/concepts-identity).

1. Öppna en kommando rad i Azure VMware Solutions-portalen.

2. Kör följande kommando för att uppdatera ditt vCenter CloudAdmin-lösenord.  Du måste ersätta {SubscriptionID}, {ResourceGroup} och {PrivateCloudName} med de faktiska värdena för det privata moln som CloudAdmin-kontot tillhör.

```
az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```
          
3. Kör följande kommando för att uppdatera ditt NSX-T-administratörs lösen ord. Du måste ersätta {SubscriptionID}, {ResourceGroup} och {PrivateCloudName} med de faktiska värdena för det privata moln som NSX-T admin-kontot tillhör.

```
az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```

## <a name="ensure-the-hcx-connector-has-your-latest-vsphere-credentials"></a>Se till att HCX-anslutningen har dina senaste vSphere-autentiseringsuppgifter

Nu när du har återställt dina autentiseringsuppgifter följer du de här stegen för att se till att HCX-anslutningen har dina uppdaterade autentiseringsuppgifter.

1. När ditt lösen ord har ändrats går du till det lokala HCX Connector-webbgränssnittet med hjälp av https://{IP för HCX Connector-apparaten}: 443. Se till att använda port 443. Logga in med dina nya autentiseringsuppgifter.

2. Välj **webbplats länkning** på VMware HCX-instrumentpanelen.
    
    :::image type="content" source="media/reset-vsphere-credentials/hcx-site-pairing.png" alt-text="Skärm bild av VMware HCX-instrumentpanelen med plats länkning markerat.":::
 
3. Välj korrekt anslutning till AVS (om det finns mer än ett) och välj **Redigera anslutning**.
 
4. Ange de nya vSphere-autentiseringsuppgifterna och välj **Redigera**, vilket sparar autentiseringsuppgifterna. Spara ska Visa lyckades.

## <a name="next-steps"></a>Nästa steg

Nu när du har täckt att återställa vSphere-autentiseringsuppgifter för Azure VMware-lösningen kanske du vill lära dig mer om:

- [Konfigurera NSX Network-komponenter i Azure VMware-lösningen](configure-nsx-network-components-azure-portal.md).
- [Livs cykel hantering av virtuella datorer i Azure VMware-lösningen](lifecycle-management-of-azure-vmware-solution-vms.md).
- [Distribuera haveri beredskap för virtuella datorer med hjälp av Azure VMware-lösningen](disaster-recovery-for-virtual-machines.md).
