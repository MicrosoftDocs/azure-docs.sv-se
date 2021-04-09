---
title: Planera ett erbjudande för virtuell dator – Microsoft Commercial Marketplace
description: I den här artikeln beskrivs kraven för att publicera ett virtuellt dator erbjudande på Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: 9169d691b58ad4a59796123c64f856128a0f8c83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879334"
---
# <a name="how-to-plan-a-virtual-machine-offer"></a>Planera ett erbjudande för virtuell dator

I den här artikeln beskrivs de olika alternativen och kraven för att publicera ett erbjudande för virtuell dator (VM) på den kommersiella marknads platsen. VM-erbjudanden är Transact-bara erbjudanden som distribueras och debiteras via Azure Marketplace.

Innan du börjar ska du [skapa ett konto för kommersiell marknads plats i Partner Center](./partner-center-portal/create-account.md) och se till att ditt konto registreras i programmet för kommersiella marknads platser.

### <a name="technical-fundamentals"></a>Tekniska grunderna

Processen för att utforma, skapa och testa erbjudanden tar tid och kräver expertis i både Azure-plattformen och teknikerna som används för att skapa ditt erbjudande. Ditt tekniska team bör ha en fungerande kunskap om [Azure-Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)och [Azure-nätverk](https://azure.microsoft.com/services/?filter=networking#networking), samt kunskaper i [design och arkitektur för Azure-program](https://azure.microsoft.com/solutions/architecture/). Se dessa ytterligare tekniska resurser: 

- Självstudier
  - [Virtuella Linux-datorer](../virtual-machines/linux/tutorial-manage-vm.md)
  - [Virtuella Windows-datorer](../virtual-machines/windows/tutorial-manage-vm.md)

- Exempel
  - [Azure CLI-exempel för virtuella Linux-datorer](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Azure PowerShell för virtuella Linux-datorer](https://github.com/Azure/azure-docs-powershell-samples/tree/master/virtual-machine)
  - [Azure CLI-exempel för virtuella Windows-datorer](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Azure PowerShell för virtuella Windows-datorer](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

## <a name="technical-requirements"></a>Tekniska krav

VM-erbjudanden har följande tekniska krav:

- Du måste förbereda en virtuell hård disk för operativ system (VHD). Datadisk-VHD: ar är valfria. Detta förklaras i detalj nedan.
- Kunden kan när som helst säga upp erbjudandet.
- Du måste skapa minst en plan för ditt erbjudande. Din plan priss ätts baserat på det [licensierings alternativ](#licensing-options) du väljer.
   > [!IMPORTANT]
   > Varje VM-avbildning i en plan måste ha samma antal data diskar.

En virtuell dator innehåller två komponenter:

- **Operativ hård disk** – innehåller det operativ system och den lösning som distribueras med ditt erbjudande. Processen för att förbereda den virtuella hård disken varierar beroende på om det är en Linux-, Windows-eller anpassad-baserad virtuell dator.
- **Datadisk-VHD: er** (tillval) – dedikerad och beständig lagring för en virtuell dator. Använd inte den virtuella hård disken (t. ex. C: Drive) för att lagra beständig information. 
    - Du kan ta med upp till 16 data diskar.
    - Använd en virtuell hård disk per data disk, även om disken är tom.

    > [!NOTE]
    > Oavsett vilket operativ system du använder lägger du endast till det minsta antalet data diskar som behövs för lösningen. Kunder kan inte ta bort diskar som ingår i en avbildning vid tidpunkten för distributionen, men de kan alltid lägga till diskar under eller efter distributionen.

Detaljerade anvisningar om hur du förbereder dina tekniska till gångar finns i [skapa en virtuell dator med en godkänd bas](azure-vm-create-using-approved-base.md) eller [skapa en virtuell dator med hjälp av en egen avbildning](azure-vm-create-using-own-image.md).

## <a name="preview-audience"></a>Förhandsgranska mål grupp

En förhands gransknings grupp kan komma åt ditt virtuella dator erbjudande innan du publicerar den Live på Azure Marketplace. På sidan för **hands versions mål** kan du definiera en begränsad förhands gransknings mål grupp. 

> [!NOTE]
> En förhands gransknings mål skiljer sig från en privat plan. En privat plan är en som du bara gör tillgänglig för en speciell mål grupp som du väljer. På så sätt kan du förhandla fram en anpassad plan med vissa kunder. Mer information finns i nästa avsnitt: planer.

Du kan skicka inbjudningar till Microsoft-konto (MSA) eller Azure Active Directory (Azure AD) e-postadresser. Lägg till upp till 10 e-postadresser manuellt eller importera upp till 20 med en. csv-fil. Om erbjudandet redan är Live kan du fortfarande definiera en förhands gransknings grupp för att testa ändringar eller uppdateringar av ditt erbjudande.

## <a name="plans-and-pricing"></a>Planer och priser

VM-erbjudanden kräver minst en plan. En plan definierar lösningens omfattning och begränsningar samt tillhör ande prissättning. Du kan skapa flera planer för ditt erbjudande för att ge dina kunder olika tekniska och licensierings alternativ, samt kostnads fria utvärderings versioner. Se [planer och priser för kommersiella Marketplace-erbjudanden](plans-pricing.md) för allmän vägledning om planer, inklusive pris modeller, kostnads fria utvärderingar och privata planer. 

Virtuella datorer är helt handelsaktiverade med hjälp av "betala per användning"-eller "BYOL-licensierings modeller". Microsoft är värd för handels transaktionen och fakturerar kunden för din räkning. Du får fördelen med att använda den prioriterade betalnings relationen mellan kunden och Microsoft, inklusive eventuella företags avtal. Mer information finns i [Transact-funktioner för kommersiella Marketplace](./marketplace-commercial-transaction-capabilities-and-considerations.md).

> [!NOTE]
> Den förskotts betalning i Azure (tidigare kallat betalnings åtagande) som är associerad med en Enterprise-avtal kan användas mot Azures användning av din virtuella dator, men inte mot licens avgifterna för din program vara.

### <a name="licensing-options"></a>Licensierings alternativ

När du förbereder för att publicera ett nytt virtuellt dator erbjudande måste du bestämma vilket licensierings alternativ som ska väljas. Detta avgör vilken ytterligare information du behöver för senare när du skapar erbjudandet i Partner Center.

Detta är de tillgängliga licens alternativen för VM-erbjudanden:

| Licensieringsalternativ | Transaktions process |
| --- | --- |
| Kostnadsfri utvärderingsversion | Erbjud dina kunder en en-, tre-eller sex månaders kostnads fri utvärderings version. |
| Test enhet | Med det här alternativet kan dina kunder utvärdera virtuella datorer utan extra kostnad. De behöver inte vara en befintlig Azure-kund för att kunna kommunicera med utvärderings versionen. Mer information finns i [Vad är en test-enhet?](./what-is-test-drive.md) |
| BYOL | Med alternativet för att ta med din egen licens kan kunderna hämta befintliga program licenser till Azure.\* |
| Användning-baserad | Med det här alternativet, även kallat betala per användning, kan kunderna betala per timme. |
| Interaktiv demo  | Ge kunderna en guidad upplevelse av din lösning med hjälp av en interaktiv demonstration. Fördelen är att du kan erbjuda en utvärderings upplevelse utan att behöva tillhandahålla en komplicerad konfiguration av din komplexa lösning. |
|

\* Som utgivare har du stöd för alla aspekter av program licens transaktionen, inklusive (men inte begränsat till) order, uppfyllelse, avläsning, fakturering, fakturering, betalning och insamling.

I följande exempel visas ett virtuellt dator erbjudande på Azure Marketplace som har användnings-baserade priser.

:::image type="content" source="media/vm/sample-offer-screen.png" alt-text="Exempel på virtuell erbjudande skärm.":::

### <a name="private-plans"></a>Privata planer

Du kan begränsa identifieringen och distributionen av den virtuella datorn till en speciell uppsättning kunder genom att publicera avbildningen och prissättningen som en privat plan. Privata planer gör att du kan skapa exklusiva erbjudanden för dina närmaste kunder och erbjuda anpassade program och villkor. Med de anpassade villkoren kan du markera olika scenarier, inklusive fält LED ande avtal med särskilda priser och villkor samt tidig åtkomst till begränsad version av program vara. Med privata planer kan du ge särskilda priser eller produkter till en begränsad uppsättning kunder.

Mer information finns i avsnittet [om prenumerationer och priser för erbjudanden för kommersiella Marketplace](plans-pricing.md) och [privata erbjudanden på Microsofts kommersiella marknads platser](private-offers.md).

## <a name="test-drives"></a>Provkörningar

Du kan välja att aktivera en testen het för den virtuella datorn. Test enheter ger kunderna till gång till en förkonfigurerad miljö för ett fast antal timmar. Du kan aktivera test enheter för alla publicerings alternativ, men den här funktionen har ytterligare krav. Mer information om test enheter finns i [Vad är en test enhet?](what-is-test-drive.md). Information om hur du konfigurerar olika typer av test enheter finns i [teknisk konfiguration för test enhet](test-drive-technical-configuration.md).

> [!TIP]
> En testen het skiljer sig från en [kostnads fri utvärderings version](plans-pricing.md#free-trials). Du kan erbjuda en testenhet, en kostnads fri utvärderings version eller både och. De förser båda kunderna med din lösning under en fast period. Men en testenhet innehåller även en praktisk guidad rundtur i produktens viktiga funktioner och fördelar som visas i ett verkligt implementerings scenario.

## <a name="customer-leads"></a>Kund ledare

Du måste ansluta ditt erbjudande till ditt CRM-system (Customer Relations hip Management) för att samla in kund information. Kunden uppmanas att ange behörighet för att dela sin information. Dessa kund uppgifter, tillsammans med erbjudande namn, ID och onlinebutik där de hittade ditt erbjudande, kommer att skickas till det CRM-system som du har konfigurerat. Den kommersiella Marketplace har stöd för en rad olika CRM-system, tillsammans med alternativet att använda en Azure-tabell eller konfigurera en HTTPS-slutpunkt med energi automatisering.

Du kan lägga till eller ändra en CRM-anslutning när som helst under eller efter att erbjudandet har skapats. Mer detaljerad information finns i [kund leads från ditt kommersiella Marketplace-erbjudande](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="legal-contracts"></a>Juridiska kontrakt

För att förenkla inköps processen för kunder och minska den juridiska komplexiteten för program varu leverantörer erbjuder Microsoft ett standard kontrakt som du kan använda för dina erbjudanden på den kommersiella marknaden. När du erbjuder program varan under standard kontraktet behöver kunderna bara läsa och godkänna den en gång, och du behöver inte skapa anpassade allmänna villkor.

Om du väljer att använda standard kontraktet har du möjlighet att lägga till generella ändrings villkor och upp till 10 anpassade ändringar i standard kontraktet. Du kan också använda egna villkor i stället för standard kontraktet. Du kommer att hantera informationen på sidan **Egenskaper** . Detaljerad information finns i [standard kontrakt för Microsoft Commercial Marketplace](standard-contract.md).

> [!NOTE]
> När du har publicerat ett erbjudande med hjälp av standard avtalet för den kommersiella marknads platsen kan du inte använda dina egna anpassade villkor. Det är ett "eller"-scenario. Du erbjuder antingen din lösning enligt standard avtalet eller dina egna villkor. Om du vill ändra villkoren i standard kontraktet kan du göra det via standard kontrakts ändringar.

## <a name="cloud-solution-providers"></a>Leverantörer av molnlösningar

När du skapar erbjudandet i Partner Center visas fliken åter försäljning **via kryptografiproviders** . Med det här alternativet kan partner som ingår i CSP-programmet (Microsoft Cloud solution providers) återförsälja den virtuella datorn som en del av ett sammanställt erbjudande. Alla BYOL-planer () är automatiskt inloggade i programmet. Du kan också välja att välja icke-BYOL planer. Mer information finns i [Cloud Solution Provider-programmet](cloud-solution-providers.md) . 

> [!NOTE]
> Du kan välja att välja partner kanal för CSP (Cloud Solution Provider) nu. Mer information om marknadsföring av ditt erbjudande via Microsoft CSP partner Channels finns i [**moln lösnings leverantörer**](./cloud-solution-providers.md).

## <a name="next-steps"></a>Nästa steg

- [Skapa ett erbjudande för virtuell dator på Azure Marketplace](azure-vm-create.md)
- [Skapa en virtuell dator med en godkänd bas](azure-vm-create-using-approved-base.md) eller [skapa en virtuell dator med hjälp av en egen avbildning](azure-vm-create-using-own-image.md).
- [Metodtips för erbjudandelistor](gtm-offer-listing-best-practices.md)