---
title: Vad är en provkörning? Microsofts kommersiella marknadsplats
description: Förklaring av marketplace-funktionen för provkörning
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 06/19/2020
ms.openlocfilehash: e44d5d94a8dc172962a26f3e0dae9ccbb7f8a865
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818898"
---
# <a name="what-is-a-test-drive"></a>Vad är en provkörning?

En testkörning är ett bra sätt att visa upp ditt erbjudande för potentiella kunder genom att ge dem möjlighet att prova innan du köper, generera högt kvalificerade leads och resultera i ökad konvertering. En testkörning ger liv i produkten i ett verkligt implementeringsscenario. Kunder som provar din produkt visar en tydlig avsikt att köpa en liknande lösning. Använd detta till din fördel genom att följa upp med mer avancerade leads.

Dina kunder har också nytta av en provkörning. Genom att låta dem prova produkten först minskar du friktionen i inköpsprocessen. Dessutom är testenheten föreetabled, dvs. kunderna behöver inte ladda ned, konfigurera eller konfigurera produkten.

## <a name="how-does-it-work"></a>Hur fungerar det?

Testenheter är hanterade instanser som startar din lösning eller ditt program på begäran för kunder som begär det. När en provkörningsinstans har tilldelats är den tillgänglig för användning av kunden under en viss period. När perioden har avslutats tas den bort för att skapa utrymme för en annan kund.

Som utgivare hanterar och konfigurerar du inställningarna för testenheten i Partnercenter. Informationen om teknisk konfiguration varierar beroende på typen av erbjudande. Detaljerad information finns i Teknisk [konfiguration för provkörning.](./test-drive-technical-configuration.md)

Potentiella kunder upptäcker din provkörning som CTA i ditt erbjudande på [AppSource.](https://appsource.microsoft.com/en-US/) De anger sin kontaktinformation och godkänner erbjudandets villkor och sekretesspolicy och får sedan åtkomst till din förkonfigurerade miljö för att prova den under en fast period. Kunder får en praktisk, självledd utvärderingsversion av produktens viktiga funktioner och fördelar, och du får ett värdefullt lead.

## <a name="types-of-test-drives"></a>Typer av testenheter

Det finns olika testenheter på den kommersiella marknadsplatsen för utvalda erbjudanden beroende på vilken typ av produkt, scenario och marknadsplats du befinner dig på:

- Azure Resource Manager
    - Azure-program
    - SaaS
    - Virtual Machines
- Värdindelade provkörningar
    - Dynamics 365 for Business Central (stöds inte för närvarande)
    - Dynamics 365 för Customer Engagement
    - Dynamics 365 for Operations
- Logikapp (endast i supportläge)
- Power BI

Mer information om hur du konfigurerar en av dessa testenheter finns i [Teknisk konfiguration av testenhet.](./test-drive-technical-configuration.md) 

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager provkörning

Den här distributionsmallen innehåller alla Azure-resurser som utgör din lösning. Produkter som passar det här scenariot använder endast Azure-resurser. Den Azure Resource Manager provkörningen är tillgänglig för dessa erbjudandetyper: 

- Azure-program
- SaaS
- Virtuella datorer

>[!NOTE]
>Det här är det enda provkörningsalternativet för virtuella datorer och Azure-programerbjudanden.

### <a name="hosted-test-drive-recommended"></a>Värdindelade provkörningar (rekommenderas)

En värdbaserade testenhet tar bort installationens komplexitet genom att låta Microsoft vara värd för och underhålla den tjänst som utför användareetablering och avetablering av testenheten. Om du har ett erbjudande på Microsoft AppSource skapar du testkörningen för att ansluta till en Dynamics AX/CRM-instans. Du kan använda följande AppSource-erbjudanden:

- Använd [Dynamics 365 for Customer Engagement och Power Apps](dynamics-365-customer-engage-offer-setup.md) för ett Customer Engagement-system som försäljning, tjänst, projekttjänst och fälttjänst.
- Använd [Dynamics 365 for Operations](partner-center-portal/create-new-operations-offer.md) för ett resursplaneringssystem för Finance and Operations enterprise, till exempel ekonomi, drift och tillverkning, leveranskedja.

### <a name="logic-app-test-drive"></a>Testkörning för logikapp

Den här typen av testenhet finns inte hos Microsoft och använder arm Azure Resource Manager mallar (arm) för erbjudandetyper för Dynamics AX/CRM. Du måste köra ARM-mallen för att skapa nödvändiga resurser i din Azure-prenumeration. Logic App Test Drive är för närvarande endast i supportläge och rekommenderas inte av Microsoft. Mer information om hur du konfigurerar en testenhet för logikapp finns i Teknisk konfiguration [av testenhet.](./test-drive-technical-configuration.md)

### <a name="power-bi-test-drive"></a>Power BI testkörning

Det här är helt enkelt en inbäddad länk till en anpassad instrumentpanel. Alla produkter som endast visar ett interaktivt Power BI visuellt objekt bör använda den här typen av testkörning.

## <a name="transforming-examples"></a>Transformeringsexempel

Processen att omvandla en arkitektur med resurser till en testenhet kan vara svår. Kolla in de här exemplen på hur du bäst omvandlar aktuella arkitekturer.

[Omvandla en webbplatsmall till en provkörning](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[Omvandla en mall för virtuella datorer till en provkörning](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[Omvandla en befintlig Resource Manager-mall till en provkörning](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>Generera leads från testkörningen

En testkörning på den kommersiella marknadsplatsen är ett bra verktyg för marknadsförare. Vi rekommenderar att du införlivar det i ditt go-to-market-arbete när du startar för att generera fler leads för din verksamhet. Detaljerad information finns i [Kund-leads från ditt erbjudande på den kommersiella marknadsplatsen.](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md)

Om du stänger ett avtal med en provkörar-lead måste du registrera den på [Microsoft Partner Sales Connect.](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect) Vi vill också gärna höra om kunden vinner där en provkörning spelar en roll.

## <a name="other-resources"></a>Andra resurser

Ytterligare testkörningsresurser:

- [Metodtips för Test Drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Översikt](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; kontrollera att blockeringen av popup-fönster är inaktiverad)

## <a name="next-step"></a>Nästa steg

- [Teknisk konfiguration för provkörning](test-drive-technical-configuration.md)