---
title: Vad är en provkörning? Microsoft Commercial Marketplace
description: Förklaring av funktionen för Marketplace-testenhet
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 06/19/2020
ms.openlocfilehash: 193e7bd78046a1d73cb55171c15c9cb6a7278297
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96490048"
---
# <a name="what-is-a-test-drive"></a>Vad är en provkörning?

En testen het är ett bra sätt att presentera ditt erbjudande för potentiella kunder genom att ge dem möjlighet att prova innan du köper, genererar hög kvalificerade leads och resulterar i ökad konvertering. En testen het ger din produkt livs längd i ett verkligt implementerings scenario. Kunder som provar produkten är en tydlig avsikt att köpa en liknande lösning. Använd den här för din fördel genom att följa upp med mer avancerade leads.

Dina kunder får även nytta av en test-enhet. Genom att göra det möjligt för dem att testa produkten först, minskar du friktionen i inköps processen. Dessutom är test enheten företablerad, d.v.s. kunder behöver inte hämta, konfigurera eller konfigurera produkten.

## <a name="how-does-it-work"></a>Hur fungerar det?

Test enheter är hanterade instanser som startar din lösning eller program på begäran för kunder som begär det. När en Test Drive-instans har tilldelats är den tillgänglig för användning av den kunden under en angiven period. När perioden har upphört tas den bort för att skapa en plats för en annan kund.

Som utgivare hanterar och konfigurerar du inställningarna för test enheten i Partner Center. Teknisk konfigurations information varierar beroende på typen av erbjudande. Detaljerad vägledning finns i [teknisk konfiguration för test enheten](./test-drive-technical-configuration.md).

Potentiella kunder identifierar din testenhet som ett centrum för ditt erbjudande på [AppSource](https://appsource.microsoft.com/en-US/). De ger sina kontakt uppgifter och samtycker till ditt erbjudandes villkor och sekretess policy och får sedan till gång till förkonfigurerad miljö för att testa den under en viss period. Kunderna får en praktisk, själv guidad utvärderings version av produktens viktiga funktioner och fördelar och du får en värdefull lead.

## <a name="types-of-test-drives"></a>Typer av test enheter

Det finns olika test enheter tillgängliga på den kommersiella marknads platsen för utvalda erbjudanden beroende på vilken typ av produkt, scenario och Marketplace du är på:

- Azure Resource Manager
    - Azure-program
    - SaaS
    - Virtual Machines
- Värdbaserad testen het
    - Dynamics 365 for Business Central (stöds inte för närvarande)
    - Dynamics 365 för Customer Engagement
    - Dynamics 365 for Operations
- Logic app (endast i support läge)
- Power BI

Mer information om hur du konfigurerar en av dessa test enheter finns i [teknisk konfiguration för test enhet](./test-drive-technical-configuration.md). 

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager testen het

Den här distributions mal len innehåller alla Azure-resurser som utgör din lösning. Produkter som passar det här scenariot använder bara Azure-resurser. Den Azure Resource Manager test enheten är tillgänglig för dessa erbjudande typer: 

- Azure-program
- SaaS
- Virtuella datorer

>[!NOTE]
>Detta är det enda alternativet test enhet för erbjudanden för virtuella datorer och Azure-program.

### <a name="hosted-test-drive-recommended"></a>Värdbaserad testen het (rekommenderas)

En värdbaserad testen het tar bort komplexiteten i installationen genom att låta Microsoft Host och underhålla tjänsten som utför test enhetens användar etablering och avetablering. Om du har ett erbjudande på Microsoft AppSource skapar du test enheten för att ansluta till en Dynamics AX/CRM-instans. Du kan använda följande typer av AppSource-erbjudanden:

- Använd [Dynamics 365 för kund engagemang](partner-center-portal/create-new-customer-engagement-offer.md) för ett kund engagemang system, till exempel Sales, service, Project service och Field service.
- Använd [Dynamics 365 för åtgärder](partner-center-portal/create-new-operations-offer.md) för ekonomi-och drift företags resurs planerings system, till exempel ekonomi, drift och tillverkning, leverans kedja.

### <a name="logic-app-test-drive"></a>Test enhet för Logic app

Den här typen av test enhet är inte värd för Microsoft och använder Azure Resource Manager ARM-mallar för Dynamics AX/CRM-erbjudande typer. Du måste köra ARM-mallen för att skapa nödvändiga resurser i din Azure-prenumeration. Logic app-testenheten är för närvarande endast i support läge och rekommenderas inte av Microsoft för information om hur du konfigurerar en Logic app-testenhet, se [teknisk konfiguration av test enhet](./test-drive-technical-configuration.md).

### <a name="power-bi-test-drive"></a>Power BI testen het

Detta är bara en inbäddad länk till en anpassad instrument panel. En produkt som endast visar ett interaktivt Power BI visuellt objekt bör använda den här typen av test enhet.

## <a name="transforming-examples"></a>Transformera exempel

Processen att aktivera en arkitektur av resurser till en test enhet kan vara avskräckande. Kolla i följande exempel på hur du bäst omformar aktuella arkitekturer.

[Transformera en webbplatsmall till en test enhet](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[Transformera en mall för virtuella datorer till en test enhet](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[Omvandla en befintlig Resource Manager-mall till en test enhet](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>Generera leads från test enheten

En extern Marketplace-testenhet är ett utmärkt verktyg för marknads förare. Vi rekommenderar att du införlivar det i din marknads verksamhet när du startar för att generera fler leads för ditt företag. Mer detaljerad information finns i [kund leads från ditt kommersiella Marketplace-erbjudande](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md).

Om du stänger ett avtal med en testen het måste du registrera det på [Microsoft partner Sales Connect](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect). Vi skulle också älska att höra om din kund vinner där en testenhet spelade en roll.

## <a name="other-resources"></a>Andra resurser

Ytterligare resurser för test enheter:

- [Metod tips för test enhet](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Översikt](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF, se till att blockering av popup-fönster är inaktiverat)

## <a name="next-step"></a>Nästa steg

- [Teknisk konfiguration för provkörning](test-drive-technical-configuration.md)