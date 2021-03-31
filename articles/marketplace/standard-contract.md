---
title: Standard kontrakt för Microsoft Commercial Marketplace
description: Standard kontrakt för Azure Marketplace och AppSource i Partner Center
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 05/20/2020
ms.openlocfilehash: 20a257bde6022249fd7b2ab875b94f356234b490
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94488884"
---
# <a name="standard-contract-for-microsoft-commercial-marketplace"></a>Standard kontrakt för Microsoft Commercial Marketplace

Microsoft erbjuder ett standard kontrakt för Microsofts kommersiella marknads platser. Detta bidrar till att förenkla inköps processen för kunder, minska den juridiska komplexiteten för program varu leverantörer och under lätta transaktioner i Marketplace. I stället för att utforma anpassade villkor, som en kommersiell Marketplace-utgivare, kan du välja att erbjuda program varan under [standard kontraktet](https://go.microsoft.com/fwlink/?linkid=2041178)som kunderna bara behöver Undersök och godkänna en gång.

Villkoret för ett erbjudande definieras när du skapar erbjudandet i Partner Center. Du kan välja att använda standard kontraktet för Microsofts kommersiella marknads plats i stället för att tillhandahålla egna allmänna villkor.

>[!Note]
>När du har publicerat ett erbjudande med hjälp av standard avtalet för Microsofts kommersiella marknads plats kan du inte använda dina egna anpassade villkor. Du erbjuder antingen din lösning enligt standard avtalet *eller* dina egna villkor. Anpassade villkor definieras på erbjudande nivån och tillämpas på alla planer. Skriv anpassade villkor på sidan **Egenskaper** för ditt erbjudande i Partner Center. Om du vill ändra villkoren i standard kontraktet kan du göra det via standard kontrakts ändringar.

## <a name="standard-contract-amendments"></a>Standard kontrakts ändringar

Med standard kontrakts ändringar kan utgivare välja standard kontrakt för enkelhetens skull och med anpassade villkor för produkten eller verksamheten. Kunderna behöver bara granska ändringarna i kontraktet, om de redan har granskat och godkänt Microsofts standard kontrakt.

Det finns två typer av ändringar som är tillgängliga för kommersiella Marketplace-utgivare:

* Universella ändringar: dessa ändringar tillämpas universellt på standard kontraktet för alla kunder. Universella ändringar visas för varje kund av erbjudandet i inköps flödet. Kunderna måste godkänna villkoren i standard kontraktet och ändringen innan de kan använda ditt erbjudande.

* Anpassade ändringar: dessa ändringar är särskilda ändringar i standard kontraktet som endast är riktade till specifika kunder via klient-ID: n för Azure. Utgivare kan välja den klient som de vill rikta in sig på. Endast kunder från klient organisationen visas med de anpassade ändrings villkoren i erbjudandets inköps flöde.  Kunderna måste godkänna villkoren i standard kontraktet och ändringarna innan de kan använda ditt erbjudande.

>[!Note]
>Dessa två typer av ändringar staplas ovanpå varandra. Kunder som är riktade mot anpassade ändringar får också den universella ändringen av standard kontraktet under köpet. Ändringar är begränsade till 4000 tecken, inklusive blank steg.

Du kan utnyttja standard kontraktet för Microsofts kommersiella marknads plats för följande erbjudande typer: Azure-program (lösningsfiler och hanterade program), Virtual Machines och SaaS.

## <a name="customer-experience"></a>Kund upplevelse

Under identifierings upplevelsen i Azure Marketplace eller AppSource kommer kunderna att kunna se de villkor som är associerade med erbjudandet som standard kontrakt för Microsofts kommersiella marknads plats och eventuella universella ändringar.

![Azure Portal kund identifierings upplevelsen.](media/marketplace-publishers-guide/azure-discovery-process.png)

Under inköps processen i Azure Portal kan kunderna se villkoren som är associerade med erbjudandet som standard kontrakt för Microsofts kommersiella marknads platser och alla universella och/eller klientbaserade ändringar.

![Azure Portal kundens köp upplevelse.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

Kunder kan använda Get-AzureRmMarketplaceTerms för att hämta villkoren för ett erbjudande och godkänna det. Standard kontraktet och tillhör ande ändringar kommer att returneras i utdata från cmdleten.
