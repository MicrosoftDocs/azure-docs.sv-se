---
title: Begär värd kvot och aktivera Azure VMware-lösning
description: Lär dig hur du begär värd kvot/kapacitet och aktiverar resurs leverantören för Azure VMware-lösningen. Du kan också begära fler värdar i ett befintligt privat moln i Azure VMware-lösningen.
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 02/17/2021
ms.openlocfilehash: 5d154f5c63ffccdbf1729e641133b54be478d884
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100653179"
---
# <a name="request-host-quota-and-enable-azure-vmware-solution"></a>Begär värd kvot och aktivera Azure VMware-lösning

I den här instruktionen får du lära dig hur du begär värd kvot/kapacitet och aktiverar resurs leverantören för [Azure VMware-lösningen](introduction.md) , som aktiverar tjänsten. Innan du kan aktivera Azure VMware-lösningen måste du skicka in ett support ärende för att dina värdar ska tilldelas. Om du har ett befintligt privat moln i Azure VMware-lösningen och vill att fler värdar ska tilldelas, följer du samma process.

>[!IMPORTANT]
>Det kan ta några dagar att allokera värdarna beroende på det begärda antalet.  Så begär du vad som behövs för etableringen så att du inte behöver göra en kvot ökning ofta.


Den övergripande processen är enkel och innehåller två steg:
- Begär ytterligare värd kvot/kapacitet för antingen [EA-kunder](#request-host-quota-for-ea-customers) eller [CSP-kunder](#request-host-quota-for-csp-customers) 
- Aktivera resurs leverantören för Microsoft. AVS

## <a name="eligibility-criteria"></a>Behörighetskrav

Du behöver ett Azure-konto i en Azure-prenumeration. Azure-prenumerationen måste följa något av följande kriterier:

- En prenumeration under ett [Azure-Enterprise-avtal (EA)](../cost-management-billing/manage/ea-portal-agreements.md) med Microsoft.
- En prenumeration på moln lösnings leverantör (CSP) som har hanterats under ett befintligt Azure-erbjudande för Azure eller en Azure-plan.

## <a name="request-host-quota-for-ea-customers"></a>Begär värd kvot för EA-kunder

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


## <a name="request-host-quota-for-csp-customers"></a>Begär värd kvot för CSP-kunder 

Kryptografiproviders måste använda [Microsoft Partner Center](https://partner.microsoft.com) för att aktivera Azure VMware-lösningen för sina kunder. I den här artikeln används [CSP Azure-plan](/partner-center/azure-plan-lp) som ett exempel för att illustrera inköps proceduren för partner.

Få åtkomst till Azure Portal med hjälp av administrate-proceduren för **admin på uppdrag av** () från Partner Center.

>[!IMPORTANT] 
>Azure VMware Solution service ger inget krav på flera innehavare. Det finns inte stöd för värd partner som kräver det. 

1. Konfigurera CSP: n Azure-plan:

   1. I **partner Center** väljer du **CSP** för att få åtkomst till avsnittet **kunder** .
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Microsoft Partner Center-kunder, region" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::
   
   1. Välj din kund och välj sedan **Lägg till produkter**.
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft Partner Center" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::
   
   1. Välj **Azure-plan** och välj sedan **Lägg till i kundvagn**. 
   
   1. Granska och slutför de allmänna inställningarna för Azure plan-prenumerationen för din kund. Mer information finns i [dokumentationen till Microsoft Partner Center](/partner-center/azure-plan-manage).

1. När du har konfigurerat Azure-planen och du har nödvändiga [Azure RBAC-behörigheter](/partner-center/azure-plan-manage) för prenumerationen begär du kvoten för din Azure plan-prenumeration. 

   1. Få åtkomst Azure Portal från [Microsoft Partner Center](https://partner.microsoft.com) med hjälp av administrate-proceduren ( **admin på uppdrag av** ).
   
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

## <a name="register-the-microsoftavs-resource-provider"></a>Registrera **Microsoft. AVS** Resource Provider

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="next-steps"></a>Nästa steg

När du har aktiverat resursen och rätt nätverk på plats kan du [skapa ett privat moln](tutorial-create-private-cloud.md).
