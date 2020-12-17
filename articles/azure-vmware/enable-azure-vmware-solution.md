---
title: Aktivera din Azure VMware-lösnings resurs
description: Lär dig hur du skickar en support förfrågan för att aktivera din Azure VMware-lösnings resurs. Du kan också begära fler värdar i ditt befintliga Azure VMware-lösningar privata moln.
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: 6d614dffc4ab3127e1e6740b1a8773e5fd7c23ff
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630895"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Så här aktiverar du Azure VMware-lösnings resurser
Lär dig hur du skickar en support förfrågan för att aktivera din [Azure VMware-lösnings](introduction.md) resurs. Du kan också begära fler värdar i ditt befintliga Azure VMware-lösningar privata moln.

## <a name="eligibility-criteria"></a>Behörighetskrav

Du behöver ett Azure-konto i en Azure-prenumeration. Azure-prenumerationen måste vara kompatibel med något av följande kriterier:

* En prenumeration under ett [Azure-Enterprise-avtal (EA)](../cost-management-billing/manage/ea-portal-agreements.md) med Microsoft.
* En prenumeration på moln lösnings leverantör (CSP) som har hanterats under ett befintligt Azure-erbjudande för Azure eller en Azure-plan.


## <a name="enable-azure-vmware-solution-for-ea-customers"></a>Aktivera Azure VMware-lösningen för EA-kunder
Innan du skapar din Azure VMware-lösning måste du skicka in ett support ärende för att dina värdar ska tilldelas. När support teamet får din begäran tar det upp till fem arbets dagar för att bekräfta din begäran och allokera dina värdar. Om du har ett befintligt privat moln i Azure VMware-lösningen och vill att fler värdar ska tilldelas, går du igenom samma process.


1. I Azure Portal, under **Hjälp + Support**, skapa en **[ny supportbegäran](https://rc.portal.azure.com/#create/Microsoft.Support)** och ange följande information för biljetten:
   - **Typ av problem:** Produkt
   - **Prenumeration:** Välj din prenumeration
   - **Tjänst:** Alla tjänster > Azure VMware-lösning
   - **Resurs:** Allmän fråga 
   - **Sammanfattning:** Behöver kapacitet
   - **Problem typ:** Problem med kapacitets hantering
   - **Problem under typ:** Kund förfrågan om ytterligare värd kvot/-kapacitet

1. I **beskrivningen** av support ärendet på fliken **information** anger du:

   - POC eller produktion 
   - Regionsnamn
   - Antal värdar
   - Annan information

   >[!NOTE]
   >Azure VMware-lösningen rekommenderar minst tre värdar för att kunna sätta upp ditt privata moln och för redundanta N + 1-värdar. 

1. Välj **Granska + skapa** för att skicka begäran.

   Det tar upp till fem arbets dagar för en support representant att bekräfta din begäran.

   >[!IMPORTANT] 
   >Om du redan har en befintlig Azure VMware-lösning och du begär ytterligare värdar måste vi tänka på att vi behöver fem arbets dagar för att allokera värdarna. 

1. Innan du kan etablera dina värdar bör du kontrol lera att du registrerar resurs leverantören för **Microsoft. AVS** i Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Ytterligare sätt att registrera resurs leverantören finns i [Azure Resource providers och-typer](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="enable-azure-vmware-solution-for-csp-customers"></a>Aktivera Azure VMware-lösningen för CSP-kunder 

Kryptografiproviders måste använda [Microsoft Partner Center](https://partner.microsoft.com) för att aktivera Azure VMware-lösningen för sina kunder. I den här artikeln används [CSP Azure-plan](/partner-center/azure-plan-lp) som ett exempel för att illustrera inköps proceduren för partner.

   >[!IMPORTANT] 
   >Azure VMware Solution service ger inget krav på flera innehavare. Det finns inte stöd för värd partner som kräver det. 

1. I **partner Center** väljer du **CSP** för att få åtkomst till avsnittet **kunder** .

   :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Microsoft Partner Center-kunder, region" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::

1. Välj din kund och välj sedan **Lägg till produkter**.

   :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft Partner Center" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::

1. Välj **Azure-plan** och välj sedan **Lägg till i kundvagn**. 

1. Granska och slutför de allmänna inställningarna för Azure plan-prenumerationen för din kund. Mer information finns i [dokumentationen till Microsoft Partner Center](/partner-center/azure-plan-manage).

När du har konfigurerat Azure-planen och de nödvändiga [Azure RBAC-behörigheterna](/partner-center/azure-plan-manage) finns för prenumerationen ska du delta i Microsoft för att aktivera kvoten för en Azure plan-prenumeration. Åtkomst Azure Portal från [Microsoft Partner Center](https://partner.microsoft.com) med hjälp av **admin på uppdrag av** (administrate) procedur.

1. Logga in på [partner Center](https://partner.microsoft.com).

1. Välj **CSP** för att få åtkomst till avsnittet **kunder** .

1. Expandera kund information och välj **Microsoft Azure-hanteringsportal**.

1. I Azure Portal, under **Hjälp + Support**, skapa en **[ny supportbegäran](https://rc.portal.azure.com/#create/Microsoft.Support)** och ange följande information för biljetten:
   - **Typ av problem:** Produkt
   - **Prenumeration:** Välj din prenumeration
   - **Tjänst:** Alla tjänster > Azure VMware-lösning
   - **Resurs:** Allmän fråga 
   - **Sammanfattning:** Behöver kapacitet
   - **Problem typ:** Problem med kapacitets hantering
   - **Problem under typ:** Kund förfrågan om ytterligare värd kvot/-kapacitet

1. I **beskrivningen** av support ärendet på fliken **information** anger du:

   - POC eller produktion 
   - Regionsnamn
   - Antal värdar
   - Annan information
   - Är avsikten att vara värd för flera kunder?

   >[!NOTE]
   >Azure VMware-lösningen rekommenderar minst tre värdar för att kunna sätta upp ditt privata moln och för redundanta N + 1-värdar. 

1. Välj **Granska + skapa** för att skicka begäran.

   Det tar upp till fem arbets dagar för en support representant att bekräfta din begäran.

   >[!IMPORTANT] 
   >Om du redan har en befintlig Azure VMware-lösning och du begär ytterligare värdar måste vi tänka på att vi behöver fem arbets dagar för att allokera värdarna. 

1. Om prenumerationen hanteras av tjänste leverantören måste deras administrations team komma åt Azure Portal att använda **Administratörs processen på uppdrag av** (administrate) från Partner Center. En Azure Portal starta en [Cloud Shell](../cloud-shell/overview.md) instans och registrera **Microsoft. AVS** -Resurshanteraren och fortsätt med distributionen av det privata molnet Azure VMware-lösning.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Ytterligare sätt att registrera resurs leverantören finns i [Azure Resource providers och-typer](../azure-resource-manager/management/resource-providers-and-types.md).

1. Om prenumerationen hanteras direkt av kunden måste registreringen av **Microsoft. AVS** -resurshanteraren göras av en användare med tillräcklig behörighet i prenumerationen, se [Azure-providers och typer](../azure-resource-manager/management/resource-providers-and-types.md) för mer information och sätt att registrera resurs leverantören. 


## <a name="next-steps"></a>Nästa steg

När du har aktiverat resursen för Azure VMware-lösningen och du har rätt nätverk på plats kan du [skapa ett privat moln](tutorial-create-private-cloud.md).
