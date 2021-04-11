---
title: Så här skapar du ett SaaS-erbjudande på Microsofts kommersiella marknads plats
description: Lär dig hur du skapar ett nytt SaaS-erbjudande (Software as a Service) för att lista eller sälja i Microsoft AppSource, Azure Marketplace eller via program varan för Cloud Solution Provider (CSP) med hjälp av den kommersiella Marketplace-portalen i Microsoft Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 74d30b7c42002c8f134520e0198774eba1519bcd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553846"
---
# <a name="how-to-create-a-saas-offer-in-the-commercial-marketplace"></a>Så här skapar du ett SaaS-erbjudande på den kommersiella Marketplace

Som en kommersiell Marketplace-utgivare kan du skapa ett SaaS-erbjudande (Software as a Service) så att potentiella kunder kan köpa din SaaS-baserade tekniska lösning. I den här artikeln beskrivs processen för att skapa ett SaaS-erbjudande för Microsofts kommersiella Marketplace.

## <a name="before-you-begin"></a>Innan du börjar

Om du inte redan har gjort det, Läs [planera ett SaaS-erbjudande för den kommersiella marknads platsen](plan-saas-offer.md). Den förklarar de tekniska kraven för din SaaS-app och den information och de till gångar du behöver när du skapar ditt erbjudande. Om du inte planerar att publicera en enkel lista (alternativ för att **kontakta mig** ) på den kommersiella Marketplace måste ditt SaaS-program uppfylla tekniska krav kring autentisering.

> [!IMPORTANT]
> Vi rekommenderar att du skapar ett separat utvecklings-och testnings erbjudande (DEV) och ett separat produktions erbjudande (PROD). Den här artikeln beskriver hur du skapar ett erbjudande för en produkt. Mer information om hur du skapar ett DEV-erbjudande finns i [skapa ett utvecklings-och test erbjudande](create-saas-dev-test-offer.md).

## <a name="create-a-new-saas-offer"></a>Skapa ett nytt SaaS-erbjudande

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
1. På den vänstra navigerings menyn väljer du **kommersiell Marketplace**-  >  **Översikt**.
1. På fliken **Översikt** väljer du **+ ny erbjudande**  >  **program vara som en tjänst**.

   :::image type="content" source="media/new-offer-saas.png" alt-text="Visar den vänstra navigerings menyn och den nya erbjudande listan.":::

1. I dialog rutan **nytt erbjudande** anger du ett **erbjudande-ID**. Detta ID visas i URL: en för den kommersiella Marketplace-listan och Azure Resource Manager mallar, om tillämpligt. Om du till exempel anger **test-erbjudande-1** i den här rutan, är webb adressen för erbjudandet `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
   + Varje erbjudande i ditt konto måste ha ett unikt erbjudande-ID.
   + Använd bara gemena bokstäver och siffror. Det kan innehålla bindestreck och under streck, men inte blank steg, och är begränsat till 50 tecken.
   + Erbjudande-ID: t kan inte ändras när du har valt **skapa**.

1. Ange ett **erbjudande alias**. Detta är det namn som används för erbjudandet i Partner Center.

   + Det här namnet visas inte i den kommersiella marknads platsen och det skiljer sig från namnet på erbjudandet och andra värden som visas för kunderna.
   + Det går inte att ändra namnet på erbjudandet när du har valt **skapa**.
1. Om du vill generera erbjudandet och fortsätta väljer du **skapa**.

## <a name="configure-your-saas-offer-setup-details"></a>Konfigurera konfigurations information för SaaS-erbjudandet

På fliken **erbjudande konfiguration** , under **installations information**, väljer du om du vill sälja ditt erbjudande via Microsoft eller hantera dina transaktioner oberoende av varandra. Erbjudanden som säljs via Microsoft kallas för _transacte erbjudanden_, vilket innebär att Microsoft underlättar utbytet av pengar för en program varu licens för utgivarens räkning. Mer information om de här alternativen finns i [list alternativ](plan-saas-offer.md#listing-options) och [avgör publicerings alternativet](determine-your-listing-type.md).

1. Om du vill sälja via Microsoft och få oss att förenkla transaktionerna väljer du **Ja**. Fortsätt att [Aktivera en testen het](#enable-a-test-drive-optional).

1. Om du vill visa ditt erbjudande via de kommersiella marknads marknads platser och bearbeta transaktioner oberoende av varandra väljer du **Nej** och gör sedan något av följande:
   + Om du vill tillhandahålla en kostnads fri prenumeration för ditt erbjudande väljer du **Hämta nu (kostnads fri)**. I rutan **erbjudande-URL** som visas anger du URL: en (från och med *http* eller *https*) där kunderna kan få en utvärderings version genom att [använda Azure Active Directory (Azure AD)](azure-ad-saas.md). Till exempel `https://contoso.com/saas-app`.
   + Om du vill tillhandahålla en 30-dagars kostnads fri utvärderings version väljer du den **kostnads fria utvärderings** versionen. i rutan **utvärderings-URL** som visas anger du URL (från och med *http* eller *https*) där kunder kan komma åt din kostnads fria utvärderings version genom att [använda Azure Active Directory (Azure AD)](azure-ad-saas.md). Till exempel `https://contoso.com/trial/saas-app`.
   + Om du vill få potentiella kunder att kontakta dig för att köpa ditt erbjudande väljer du **kontakta mig**.

## <a name="enable-a-test-drive-optional"></a>Aktivera en testen het (valfritt)

En testen het är ett bra sätt att presentera ditt erbjudande för potentiella kunder genom att ge dem till gång till en förkonfigurerad miljö för ett fast antal timmar. Att erbjuda en testen het resulterar i en ökad konverterings takt och genererar hög kvalificerade leads. Mer information om test enheter finns i [Vad är en test enhet?](./what-is-test-drive.md).

> [!TIP]
> En testen het skiljer sig från en kostnads fri utvärderings version. Du kan erbjuda antingen en testad enhet, en kostnads fri utvärderings version eller både och. De förser båda kunderna med din lösning för en fast tids period. Men en testenhet innehåller även en praktisk guidad rundtur i produktens viktiga funktioner och fördelar som visas i ett verkligt implementerings scenario.

**Så här aktiverar du en testen het**
1.  Under **test enhet** markerar du kryss rutan **Aktivera en testenhet** .
1.  Välj typen av test enhet i listan som visas.

## <a name="configure-lead-management"></a>Konfigurera hantering av leads

Anslut ditt CRM-system (Customer Relations hip Management) till ditt kommersiella Marketplace-erbjudande så att du kan ta emot kund kontakt information när en kund uttrycker intresse eller distribuerar din produkt. Du kan när som helst ändra den här anslutningen under eller efter att du har skapat erbjudandet.

> [!NOTE]
> Du måste konfigurera hantering av lead om du säljer ditt erbjudande via Microsoft eller har valt alternativet **kontakta mig** List. Mer detaljerad information finns i [kund leads från ditt kommersiella Marketplace-erbjudande](partner-center-portal/commercial-marketplace-get-customer-leads.md).

### <a name="configure-the-connection-details-in-partner-center"></a>Konfigurera anslutnings information i Partner Center

1.  Under **kund ledare** väljer du länken **Anslut** .
1. I dialog rutan **anslutnings information** väljer du ett lead-mål i listan.
1. Fyll i fälten som visas. Detaljerade anvisningar finns i följande artiklar:

   - [Konfigurera ditt erbjudande för att skicka leads till Azure-tabellen](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Konfigurera ditt erbjudande för att skicka leads till Dynamics 365 kund engagemang](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (tidigare Dynamics CRM Online)
   - [Konfigurera ditt erbjudande för att skicka leads till HTTPS-slutpunkt](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Konfigurera ditt erbjudande för att skicka leads till Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Konfigurera ditt erbjudande för att skicka leads till Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Om du vill verifiera konfigurationen som du har angett väljer du länken **Verifiera** .
1. Stäng dialog rutan genom att välja **OK**.

## <a name="configure-microsoft-365-app-integration"></a>Konfigurera Microsoft 365 App-integrering

Du kan lätt få fram [enhetlig identifiering och leverans](./plan-SaaS-offer.md) av ditt SaaS-erbjudande och relaterade Microsoft 365-användning av appar genom att länka dem.

### <a name="integrate-with-microsoft-api"></a>Integrera med Microsoft API

1. Om ditt SaaS-erbjudande inte integreras med Microsoft Graph API väljer du **Nej**. Fortsätt att länka publicerade Microsoft 365-klienter för program användning.  

1. Om ditt SaaS-erbjudande integreras med Microsoft Graph API väljer du **Ja** och anger sedan Azure Active Directory-app-ID som du har skapat och registrerat för att integrera med Microsoft Graph API. 

### <a name="link-published-microsoft-365-app-consumption-clients"></a>Länka publicerade Microsoft 365 program förbruknings klienter

1. Om du inte har publicerat Office-tillägg, team-appar eller SharePoint Framework-lösningar som fungerar med ditt SaaS-erbjudande väljer du **Nej**.

1. Om du har publicerat Office-tillägg, team-appar eller SharePoint Framework-lösningar som fungerar med ditt SaaS-erbjudande väljer du **Ja** och väljer sedan **+ Lägg till ytterligare en AppSource-länk** för att lägga till nya länkar.  

1. Ange en giltig AppSource-länk.

1. Fortsätt att lägga till alla länkar genom att välja **+ Lägg till ytterligare en AppSource-länk** och ange giltiga AppSource-länkar.  

1. Ordningen som de länkade produkterna visas på registrerings sidan för SaaS-erbjudandet anges med värdet rang, du kan ändra det genom att välja, hålla och flytta =-ikonen uppåt och nedåt i listan. 

1. Du kan ta bort en länkad produkt genom att välja **ta bort** på raden produkt.  


> [!IMPORTANT]
> Om du avslutar-säljer en länkad produkt tas den inte bort automatiskt i SaaS-erbjudandet. du måste ta bort det från listan över länkade produkter och skicka om erbjudandet för SaaS.  

 

## <a name="next-steps"></a>Nästa steg

- [Så här konfigurerar du egenskaperna för SaaS-erbjudandet](create-new-saas-offer-properties.md)