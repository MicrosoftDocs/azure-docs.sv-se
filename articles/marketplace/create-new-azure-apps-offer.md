---
title: Så här skapar du ett erbjudande för Azure-program på den kommersiella Marketplace
description: Lär dig hur du skapar ett nytt Azure-program för att lista eller sälja på Azure Marketplace eller via program varan för Cloud Solution Provider (CSP) med hjälp av den kommersiella Marketplace-portalen i Microsoft Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 3cc5e5114b435965eee4aa096e5898538b0a56e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370451"
---
# <a name="how-to-create-an-azure-application-offer-in-the-commercial-marketplace"></a>Så här skapar du ett erbjudande för Azure-program på den kommersiella Marketplace

Som en kommersiell Marketplace-utgivare kan du skapa ett Azure-program som ger potentiella kunder möjlighet att köpa din lösning. I den här artikeln beskrivs processen för att skapa ett Azure-program för Microsoft Commercial Marketplace.

Om du inte redan gjort det kan du läsa [planera ett Azure-program för den kommersiella Marketplace](plan-azure-application-offer.md). Den ger resurserna och hjälper dig att samla in den information och de till gångar du behöver när du skapar ditt erbjudande.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).

1. På den vänstra navigerings menyn väljer du **kommersiell Marketplace**-  >  **Översikt**.

1. På sidan Översikt väljer du **+ nytt erbjudande**  >  **Azure Application**.

    ![Visar menyn till vänster-navigering.](./media/create-new-azure-app-offer/new-offer-azure-app.png)

1. I dialog rutan **nytt erbjudande** anger du ett **erbjudande-ID**. Detta är en unik identifierare för varje erbjudande i ditt konto. Detta ID visas i URL: en för den kommersiella Marketplace-listan och Azure Resource Manager mallar, om tillämpligt. Om du till exempel anger test-erbjudande-1 i den här rutan, är webb adressen för erbjudandet `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

     * Varje erbjudande i ditt konto måste ha ett unikt erbjudande-ID.
     * Använd bara gemena bokstäver och siffror. Det kan innehålla bindestreck och under streck, men inte blank steg, och är begränsat till 50 tecken.
     * Erbjudande-ID: t kan inte ändras när du har valt **skapa**.

1. Ange ett **erbjudande alias**. Detta är det namn som används för erbjudandet i Partner Center.

     * Det här namnet visas bara i Partner Center och det skiljer sig från namnet på erbjudandet och andra värden som visas för kunderna.
     * Det går inte att ändra namnet på erbjudandet när du har valt **skapa**.

1. Om du vill generera erbjudandet och fortsätta väljer du  **skapa**.

## <a name="configure-your-azure-application-offer-setup-details"></a>Konfigurera konfigurations information för Azure Application-erbjudandet

På fliken **erbjudande konfiguration** , under **installations information**, väljer du om du vill konfigurera en testen het. Du kommer också att ansluta ditt CRM-system (Customer Relations hip Management) till ditt kommersiella Marketplace-erbjudande.

### <a name="enable-a-test-drive-optional"></a>Aktivera en testen het (valfritt)

En testen het är ett bra sätt att presentera ditt erbjudande för potentiella kunder genom att ge dem till gång till en förkonfigurerad miljö för ett fast antal timmar. Att erbjuda en testen het resulterar i en ökad konverterings takt och genererar hög kvalificerade leads. Mer information om test enheter finns i [Test Drive](plan-azure-application-offer.md#test-drive).

#### <a name="to-enable-a-test-drive"></a>Så här aktiverar du en testen het

- Under **test enhet** markerar du kryss rutan **Aktivera en testenhet** .

### <a name="customer-lead-management"></a>Kund ledar hantering

Anslut ditt CRM-system (Customer Relations hip Management) till ditt kommersiella Marketplace-erbjudande så att du kan ta emot kund kontakt information när en kund uttrycker intresse eller distribuerar din produkt.

#### <a name="to-configure-the-connection-details-in-partner-center"></a>Så här konfigurerar du anslutnings informationen i Partner Center

1. Under **kund ledare** väljer du länken **Anslut** .
1. I dialog rutan **anslutnings information** väljer du ett lead-mål i listan.
1. Fyll i fälten som visas. Detaljerade anvisningar finns i följande artiklar:

   - [Konfigurera ditt erbjudande för att skicka leads till Azure-tabellen](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Konfigurera ditt erbjudande för att skicka leads till Dynamics 365 kund engagemang](partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (tidigare Dynamics CRM Online)
   - [Konfigurera ditt erbjudande för att skicka leads till HTTPS-slutpunkt](partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Konfigurera ditt erbjudande för att skicka leads till Marketo](partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Konfigurera ditt erbjudande för att skicka leads till Salesforce](partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Om du vill verifiera konfigurationen som du har angett väljer du länken **Verifiera** , om tillämpligt.
1. Stäng dialog rutan genom att välja **Anslut**.
1. Välj **Spara utkast** innan du fortsätter till nästa flik: egenskaper.

> [!NOTE]
> Se till att anslutningen till mål platsen hålls uppdaterad så att du inte förlorar några leads. Se till att du uppdaterar anslutningarna när något har ändrats.

## <a name="next-steps"></a>Nästa steg

- [Så här konfigurerar du egenskaper för Azure Applications erbjudande](create-new-azure-apps-offer-properties.md)
