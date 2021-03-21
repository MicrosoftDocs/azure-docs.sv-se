---
title: Så här skapar du ett hanterat tjänst erbjudande på Microsofts kommersiella marknads platser
description: Lär dig hur du skapar ett nytt hanterat tjänst erbjudande för Azure Marketplace med hjälp av programmet för kommersiella marknads platser i Microsoft Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 46a9c9d953a2311d83b5fd18b83727d6765734fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97918414"
---
# <a name="how-to-create-a-managed-service-offer-for-the-commercial-marketplace"></a>Så här skapar du ett hanterat tjänst erbjudande för den kommersiella Marketplace

Den här artikeln beskriver hur du skapar ett hanterat tjänst erbjudande för Microsofts kommersiella Marketplace med hjälp av Partner Center.

Om du vill publicera ett hanterat tjänst erbjudande måste du ha fått Microsoft-kompetensen guld eller silver i moln plattformen. Om du inte redan har gjort det, Läs [planera ett hanterat tjänst erbjudande för den kommersiella Marketplace](./plan-managed-service-offer.md). Det hjälper dig att förbereda de till gångar du behöver när du skapar erbjudandet i Partner Center.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2. På den vänstra navigerings menyn väljer du **kommersiell Marketplace**-  >  **Översikt**.
3. På fliken Översikt väljer du **+ ny erbjud**  >  **hanterad tjänst**.

:::image type="content" source="./media/new-offer-managed-service.png" alt-text="Visar menyn till vänster-navigering.":::

4. I dialog rutan **nytt erbjudande** anger du ett **erbjudande-ID**. Detta är en unik identifierare för varje erbjudande i ditt konto. Detta ID visas i URL: en för den kommersiella Marketplace-listan och Azure Resource Manager mallar, om tillämpligt. Om du till exempel anger test-erbjudande-1 i den här rutan, är webb adressen för erbjudandet `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

    * Varje erbjudande i ditt konto måste ha ett unikt erbjudande-ID.
    * Använd bara gemena bokstäver och siffror. Det kan innehålla bindestreck och under streck, men inte blank steg, och är begränsat till 50 tecken.
    * Erbjudande-ID: t kan inte ändras när du har valt **skapa**.

5. Ange ett **erbjudande alias**. Detta är det namn som används för erbjudandet i Partner Center. Den visas inte i onlinebutiker och skiljer sig från namnet på erbjudandet som visas för kunderna.
6. Om du vill generera erbjudandet och fortsätta väljer du **skapa**.

## <a name="configure-lead-management"></a>Konfigurera hantering av leads

Anslut ditt CRM-system (Customer Relations hip Management) till ditt kommersiella Marketplace-erbjudande så att du kan ta emot kund kontakt information när en kund uttrycker intresse för din konsult tjänst. Du kan när som helst ändra den här anslutningen under eller efter att du har skapat erbjudandet. Mer detaljerad information finns i [kund leads från ditt kommersiella Marketplace-erbjudande](./partner-center-portal/commercial-marketplace-get-customer-leads.md).

Så här konfigurerar du lead-hantering i Partner Center:

1. Gå till fliken **erbjudande konfiguration** i Partner Center.
2. Under **kund ledare** väljer du länken **Anslut** .
3. I dialog rutan **anslutnings information** väljer du ett lead-mål i listan.
4. Fyll i fälten som visas. Detaljerade anvisningar finns i följande artiklar:

    * [Konfigurera ditt erbjudande för att skicka leads till Azure-tabellen](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Konfigurera ditt erbjudande för att skicka leads till Dynamics 365 kund engagemang](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (tidigare Dynamics CRM Online)
    * [Konfigurera ditt erbjudande för att skicka leads till HTTPS-slutpunkt](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Konfigurera ditt erbjudande för att skicka leads till Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Konfigurera ditt erbjudande för att skicka leads till Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5. Om du vill verifiera konfigurationen som du har angett väljer du **länken verifiera**.
6. När du har konfigurerat anslutnings informationen väljer du **Anslut**.
7. Välj **Spara utkast**.

När du har skickat in ditt erbjudande för publicering i Partner Center, validerar vi anslutningen och skickar ett test lead. Medan du för hands Grans kar erbjudandet innan det blir aktivt kan du testa din lead-anslutning genom att försöka köpa erbjudandet själv i för hands versionen.

> [!TIP]
> Se till att anslutningen till målet för målet förblir uppdaterad så att du inte förlorar några leads.

## <a name="configure-offer-properties"></a>Konfigurera erbjudande egenskaper

På sidan Egenskaper för ditt erbjudande i Partner Center definierar du de kategorier som är tillämpliga för ditt erbjudande och juridiska avtal. Den här informationen ser till att den hanterade tjänsten visas korrekt i onlinebutiken och erbjuds till rätt kund uppsättning.

### <a name="select-a-category"></a>Välj en kategori

Under **Kategorier** väljer du minst en och upp till fem kategorier för att gruppera ditt erbjudande i lämpliga sökområden för kommersiella Marketplace.

### <a name="provide-terms-and-conditions"></a>Ange villkor

Under **Legal** anger du dina villkor för det här erbjudandet. Kunderna måste godkänna dem innan de kan använda erbjudandet. Du kan också ange URL: en där du hittar dina villkor.

Välj **Spara utkast** innan du fortsätter.

## <a name="next-step"></a>Nästa steg

* [Konfigurera den hanterade tjänstens erbjudande lista](./create-managed-service-offer-listing.md)