---
title: Allokera Azure-kostnader
description: I den här artikeln beskrivs hur du skapar regler för kostnadsallokering för att distribuera kostnader för prenumerationer, resursgrupper eller taggar till andra.
author: bandersmsft
ms.author: banders
ms.date: 03/23/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: e7afef7e0a10bb4be3c30112fc207467167e4a17
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726528"
---
# <a name="create-and-manage-azure-cost-allocation-rules-preview"></a>Skapa och hantera regler för kostnadsallokering i Azure (för hands version)

Stora företag har ofta Azure-tjänster eller -resurser som hanteras centralt, men som används av olika interna avdelningar eller affärsenheter. Vanligtvis vill det centrala hanteringsteamet omallokera kostnaden för de delade tjänsterna tillbaka till de interna avdelningar eller organisationsenheter som aktivt använder tjänsterna. Den här artikeln hjälper dig att förstå och använda kostnadsallokering i Azure Cost Management.

Med kostnadsallokering kan du omallokera eller distribuera kostnaderna för delade tjänster från prenumerationer, resursgrupper eller taggar till andra prenumerationer, resursgrupper eller taggar i din organisation. Med kostnadsallokering flyttas kostnaderna för delade tjänster till en annan prenumeration, andra resursgrupper eller andra taggar som ägs av de interna avdelningar eller affärsenheter som använder tjänsterna. Med andra ord kan kostnadsallokering hantera och visa _kostnadsansvar_ från en plats till en annan.

Kostnadsallokering påverkar inte din faktura. Faktureringsansvaret ändras inte. Det främsta syftet med kostnadsallokering är att hjälpa dig att debitera kostnaderna tillbaka till andra. Alla debiteringsprocesser sker i din organisation utanför Azure. Med kostnadsallokering får du hjälp att debitera kostnaderna genom att visa dem när de har omallokerats eller omdistribuerats.

Allokerade kostnader visas i en kostnadsanalys. De visas som ytterligare objekt som är associerade med de målprenumerationer, resursgrupper eller taggar som du anger när du skapar en kostnadsallokeringsregel.

> [!NOTE]
> Kostnadsallokeringsfunktionen i Azure Cost Management är för närvarande i offentlig förhandsversion. Vissa funktioner i Cost Management kanske inte stöds eller har begränsad funktionalitet.

## <a name="prerequisites"></a>Förutsättningar

- För närvarande är det kunder med ett [Microsoft-kundavtal](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) eller ett [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) som kan använda kostnadsallokering.
- Om du vill skapa eller hantera en kostnadsallokeringsregel måste du använda ett Enterprise-administratörskonto för [Enterprise-avtal](../manage/understand-ea-roles.md). Du måste vara ägare till ett [faktureringskonto](../manage/understand-mca-roles.md) för ett Microsoft-kundavtal.

## <a name="create-a-cost-allocation-rule"></a>Skapa en kostnadsallokeringsregel

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com/).
2. Gå till **Cost Management + fakturering** > **Cost Management**.
3. Under **Inställningar** > **Konfiguration** väljer du **Kostnadsallokering (förhandsversion)** .
4. Kontrollera att rätt EA-registrering eller faktureringskonto har valts.
5. Välj **+Lägg till**.
6. Ange beskrivande text för kostnadsallokeringsregelns namn.

:::image type="content" source="./media/allocate-costs/rule-name.png" alt-text="Exempel som visar ett regelnamn som skapas" lightbox="./media/allocate-costs/rule-name.png" :::

Startdatum för regelns utvärdering används för att generera procentandelar för förifylld kostnadsallokering.

1. Välj **Lägg till källor** och välj sedan prenumerationer, resursgrupper eller taggar för att välja kostnader som ska distribueras.
2. Välj **Lägg till mål** och välj sedan prenumerationer, resursgrupper eller taggar som ska ta emot de allokerade kostnaderna.
3. Upprepa detta om du behöver skapa fler regler för kostnadsallokering.

## <a name="configure-the-allocation-percentage"></a>Konfigurera allokeringsprocent

Konfigurera allokeringsprocent för att definiera hur kostnaderna delas upp proportionellt mellan de angivna målen. Du kan definiera hela antalet procentandelar manuellt för att skapa en allokeringsregel. Du kan också fördela kostnaderna proportionellt baserat på den aktuella användningen av beräkning, lagring och nätverk över de angivna målen.

När du distribuerar kostnader per beräkningskostnad, lagringskostnad och nätverks kostnad härleds den proportionella procentandelen genom att det valda målets kostnader utvärderas. Kostnaderna är associerade till resurstypen för den aktuella faktureringsmånaden.

När du distribuerar kostnader proportionellt mot den totala kostnaden tilldelas den proportionella procentandelen av summan eller den totala kostnaden för de valda målen för den aktuella faktureringsmånaden.

:::image type="content" source="./media/allocate-costs/cost-distribution.png" alt-text="Exempel som visar allokeringsprocent" lightbox="./media/allocate-costs/cost-distribution.png" :::

När du har angett de fördefinierade procentandelarna är de fasta. De används för alla pågående allokeringar. Procentandelarna ändras endast när regeln uppdateras manuellt.

1. Välj något av följande alternativ i listan **Fyll i procentandelar till**.
    - **Distribuera jämnt** – varje mål får en lika stor procentandel av den totala kostnaden.
    - **Total kostnad** – skapar en kvot som är proportionell för målen baserat på den totala kostnaden. Kvoten används för att fördela kostnaderna från de valda källorna.
    - **Compute Cost** – skapar en kvot som är proportionell för målen baserat på deras Azure Compute Cost (resurstyper i [Microsoft.Compute](/azure/templates/microsoft.compute/allversions)-namnrymden. Kvoten används för att fördela kostnaderna från de valda källorna.
    - **Lagringskostnad** – skapar en kvot som är proportionell för målen baserat på deras Azure Storage-kostnad (resurstyper i [Microsoft.Storage](/azure/templates/microsoft.storage/allversions)-namnområdet). Kvoten används för att fördela kostnaderna från de valda källorna.
    - **Nätverkskostnad** – skapar en kvot som är proportionell för målen baserat på deras Azure-nätverks-kostnad (resurstyper i [Microsoft.Storage](/azure/templates/microsoft.network/allversions)-namnområdet). Kvoten används för att fördela kostnaderna från de valda källorna.
    - **Anpassad** – tillåter att en heltalsprocentandel anges manuellt. Den angivna summan måste vara lika med 100 %.
1. När regeln har konfigurerats väljer du **Skapa**.

Allokeringsregeln börjar bearbetas. När regeln är aktiv allokeras alla valda källkostnader till de angivna målen.

> [!NOTE] 
> Det kan ta upp till två timmar innan den nya regeln har bearbetats och aktiverats.

## <a name="verify-the-cost-allocation-rule"></a>Verifiera kostnadsallokeringsregeln

När kostnadsallokeringsregeln är aktiv distribueras kostnader från de valda källorna till de angivna allokeringsmålen. Använd följande information för att kontrollera att kostnaden är korrekt allokerad till målen.

### <a name="view-cost-allocation-for-a-subscription"></a>Visa kostnadsallokering för en prenumeration

Du visar effekten av allokeringsregeln i en kostnadsanalys. I Azure-portalen går du till [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Välj en prenumeration i listan som är riktad mot en aktiv kostnadstilldelningsregel. Välj sedan **Kostnadsanalys** på menyn. I Kostnadsanalys väljer du **Gruppera efter** och väljer sedan **Kostnadsallokering**. I vyn som visas ser du en snabb kostnadsuppdelning som genererats av prenumerationen. Kostnader som allokeras till prenumerationen visas också, som i följande bild.

:::image type="content" source="./media/allocate-costs/cost-breakdown.png" alt-text="Exempel som visar kostnadsuppdelning" lightbox="./media/allocate-costs/cost-breakdown.png" :::

### <a name="view-cost-allocation-for-a-resource-group"></a>Visa kostnadsallokering för en resursgrupp

Använd en liknande process för att se påverkan av en kostnadsallokering för en resursgrupp. I Azure Portal går du till [Resursgrupper](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups). Välj en resursgrupp i listan som är riktad mot en aktiv kostnadstilldelningsregel. Välj sedan **Kostnadsanalys** på menyn. I Kostnadsanalys väljer du **Gruppera efter** och väljer sedan **Kostnadsallokering**. I vyn som visas ser du en snabb kostnadsuppdelning som genererats av resursgruppen. Kostnader som allokerats till resursgruppen visas också.

### <a name="view-cost-allocation-for-tags"></a>Visa kostnadsallokering för taggar

I Azure Portal navigerar du till **Cost Management + fakturering** > **Cost Management** > **Kostnadsanalys**. I Kostnadsanalys väljer du **Lägg till filter**. Välj **Tagg**, välj taggnyckeln och tagga värden som har kostnader allokerade till dem.

:::image type="content" source="./media/allocate-costs/tagged-costs.png" alt-text="Exempel som visar kostnader för alla kunder" lightbox="./media/allocate-costs/tagged-costs.png" :::

Här är en video som visar hur du skapar en kostnadsallokeringsregel.

>[!VIDEO https://www.youtube.com/embed/nYzIIs2mx9Q]


## <a name="edit-an-existing-cost-allocation-rule"></a>Redigera en befintlig kostnadsallokeringsregel

Du kan redigera en kostnadsallokeringsregel för att ändra källan eller målet, eller för att uppdatera den förifyllda procentandelen för beräknings-, lagrings- eller nätverksalternativ. Redigera reglerna på samma sätt som du skapar dem. Det kan ta upp till två timmar att ändra befintliga regler.

## <a name="current-limitations"></a>Aktuella begränsningar

För närvarande kan kostnadsallokering användas i Cost Management av kostnadsanalyser, budgetar och prognosvyer. Allokerade kostnader visas också i listan med prenumerationer och på sidan Prenumerationsöversikt.

Följande objekt kan för närvarande inte användas i den offentliga förhandsversionen av kostnadsallokering:

- Schemalagda [exporter](tutorial-export-acm-data.md)
- Data som exponeras av API:et [Användningsinformation](/rest/api/consumption/usagedetails/list)
- Området Faktureringsprenumerationer
- [Cost Management Power BI-appen](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)
- [Power BI Desktop-anslutningsappen](/power-bi/connect-data/desktop-connect-azure-cost-management)


## <a name="next-steps"></a>Nästa steg

- Läs vanliga [Cost Management + Billing och svar](../cost-management-billing-faq.yml) om kostnadsallokering.
- Skapa eller uppdatera allokeringsregler med hjälp av [REST-API:et för kostnadsallokering](/rest/api/cost-management/costallocationrules)
- Läs mer om att [optimera din molninvestering med Azure Cost Management](cost-mgt-best-practices.md)