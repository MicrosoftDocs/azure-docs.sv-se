---
title: Så här skapar du ett konsult tjänst erbjudande på Microsofts kommersiella marknads plats
description: Lär dig hur du skapar ett nytt konsult tjänst erbjudande för Microsoft AppSource eller Azure Marketplace med programmet för kommersiella marknads platser i Microsoft Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: e9a0b2fe883fa46010fda74c58908128d05919e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92754573"
---
# <a name="how-to-create-a-consulting-service-offer-in-the-commercial-marketplace"></a>Så här skapar du ett konsult tjänst erbjudande i den kommersiella Marketplace

Den här artikeln beskriver hur du skapar ett konsult tjänst erbjudande för Microsofts kommersiella marknads plats med hjälp av Partner Center. 

## <a name="before-you-begin"></a>Innan du börjar

Om du vill publicera ett konsult tjänst erbjudande måste du uppfylla vissa krav på behörighet för att demonstrera expert kunskaper i ditt område. Om du inte redan har gjort det, Läs [planera ett konsult tjänst erbjudande för den kommersiella marknads platsen](./plan-consulting-service-offer.md). Den beskriver kraven för en konsult tjänst och de till gångar du behöver när du skapar erbjudandet med partner Center.

## <a name="create-a-new-consulting-service-offer"></a>Skapa ett nytt konsult tjänst erbjudande

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2.  På den vänstra navigerings menyn väljer du **kommersiell Marketplace**-  >  **Översikt**.
3.  På fliken Översikt väljer du **+ ny erbjudande**  >  **konsult tjänst**.

    ![Visar menyn till vänster-navigering.](./media/new-offer-consulting-service.png)

4. I dialog rutan **nytt erbjudande** anger du ett **erbjudande-ID**. Detta ID visas i URL: en för den kommersiella Marketplace-listan. Om du till exempel anger test-erbjudande-1 i den här rutan, är webb adressen för erbjudandet `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

    * Varje erbjudande i ditt konto måste ha ett unikt erbjudande-ID.
    * Använd bara gemena bokstäver och siffror. Erbjudande-ID: t kan innehålla bindestreck och under streck, men inte blank steg, och är begränsat till 50 tecken.
    * Erbjudande-ID: t kan inte ändras när du har valt **skapa**.

5. Ange ett **erbjudande alias**. Detta är det namn som används för erbjudandet i Partner Center. Den visas inte i onlinebutiker och skiljer sig från namnet på erbjudandet som visas för kunderna.
6. Om du vill generera erbjudandet och fortsätta väljer du **skapa**.

## <a name="configure-lead-management"></a>Konfigurera hantering av leads

Anslut ditt CRM-system (Customer Relations hip Management) till ditt kommersiella Marketplace-erbjudande så att du kan ta emot kund kontakt information när en kund uttrycker intresse för din konsult tjänst. Du kan när som helst ändra den här anslutningen under eller efter att du har skapat erbjudandet. Mer detaljerad information finns i [kund leads från ditt kommersiella Marketplace-erbjudande](./partner-center-portal/commercial-marketplace-get-customer-leads.md).

Så här konfigurerar du lead-hantering i Partner Center:

1.  Gå till fliken **erbjudande konfiguration** i Partner Center.
2.  Under **kund ledare** väljer du länken **Anslut** .
3.  I dialog rutan **anslutnings information** väljer du ett lead-mål i listan.
4.  Fyll i fälten som visas. Detaljerade anvisningar finns i följande artiklar:

    * [Konfigurera ditt erbjudande för att skicka leads till Azure-tabellen](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Konfigurera ditt erbjudande för att skicka leads till Dynamics 365 kund engagemang](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (tidigare Dynamics CRM Online)
    * [Konfigurera ditt erbjudande för att skicka leads till HTTPS-slutpunkt](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Konfigurera ditt erbjudande för att skicka leads till Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Konfigurera ditt erbjudande för att skicka leads till Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5.  Om du vill verifiera konfigurationen som du har angett väljer du **länken verifiera**.
6.  När du har konfigurerat anslutnings informationen väljer du **Anslut**.
7.  Välj **Spara utkast**.

När du har skickat in ditt erbjudande för publicering i Partner Center, validerar vi anslutningen och skickar ett test lead. Medan du för hands Grans kar erbjudandet innan det blir aktivt kan du testa din lead-anslutning genom att försöka köpa erbjudandet själv i för hands versionen.

> [!TIP]
> Se till att anslutningen till målet för målet förblir uppdaterad så att du inte förlorar några leads.

## <a name="next-steps"></a>Nästa steg

* [Så här konfigurerar du egenskaper för konsult tjänst erbjudande](./create-consulting-service-offer-properties.md)