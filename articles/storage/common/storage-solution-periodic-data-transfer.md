---
title: Välj en Azure-lösning för regelbunden data överföring | Microsoft Docs
description: Lär dig hur du väljer en Azure-lösning för data överföring när du överför data regelbundet.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: a15ebd43861e2116ddbb2d9055b289645962e203
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96573926"
---
# <a name="solutions-for-periodic-data-transfer"></a>Lösningar för periodisk dataöverföring
 
Den här artikeln ger en översikt över data överförings lösningarna när du överför data regelbundet. Regelbunden data överföring över nätverket kan kategoriseras som återkommande med jämna mellanrum eller kontinuerlig data förflyttning. Artikeln beskriver också rekommenderade alternativ för data överföring och respektive nyckel funktions mat ris för det här scenariot.

Om du vill veta en översikt över alla tillgängliga alternativ för data överföring går du till [Välj en Azure Data Transfer-lösning](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Rekommenderade alternativ

De rekommenderade alternativen för periodisk dataöverföring är indelade i två kategorier beroende på om överföringen är återkommande eller löpande.

- **Skriptbaserade/programbaserade verktyg** – för data överföring som sker med jämna mellanrum använder du skript-och programmerings verktyg som AzCopy och Azure Storage REST-API: er. De här verktygen är avsedda för IT-proffs och utvecklare.

    - **AzCopy** – Använd det här kommando rads verktyget för att enkelt kopiera data till och från Azure-blobbar, filer och tabell lagring med optimala prestanda. AzCopy har stöd för samtidighet och parallellism, och du kan återuppta avbrutna kopieringsåtgärder.
    - **Azure Storage REST-API: er/SDK:** er – när du skapar ett program kan du utveckla programmet mot Azure Storage REST API: er och använda de Azure-SDK: er som erbjuds på flera språk. REST-API: er kan också utnyttja Azure Storage data flytt bibliotek som är särskilt utformade för att kopiera data till och från Azure.

- **Verktyg för kontinuerlig data inmatning** – för kontinuerlig, kontinuerlig data inmatning kan du välja en av data Box-enhet enhet för överföring online eller Azure Data Factory. De här verktygen konfigureras av IT-proffs och kan automatisera dataöverföringen med god insyn.

    - **Azure Data Factory** – Data Factory ska användas för att skala ut en överförings åtgärd och om det behövs för att dirigera och övervaka funktioner i företags klass. Använd Data Factory till att konfigurera en molnbaserad pipeline för att regelbundet överföra filer mellan olika Azure-tjänster, lokala miljöer eller en kombination. Med Data Factory kan du orkestrera datadrivna arbetsflöden som matar in data från olika datalager och automatisera både flytt och dataomvandling.
    - **Azure Data Box-serien för online-överföringar** – Data Box Edge och data Box Gateway är online nätverks enheter som kan flytta data till och från Azure. I Data Box Edge används AI-aktiverade Edge-beräkningar till att förbearbeta data innan uppladdningen. Data Box Gateway är en virtuell version av enheten med samma funktioner för dataöverföring.


## <a name="comparison-of-key-capabilities"></a>Jämförelse av viktiga funktioner

I den här tabellen sammanfattas skillnaderna mellan de viktigaste funktionerna.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Skriptad/programmatisk nätverks data överföring

| Funktion                  | AzCopy                                 | REST-API:er för Azure Storage       |
|-----------------------------|----------------------------------------|-------------------------------|
| Form faktor                 | Kommando rads verktyg från Microsoft       | Kunderna utvecklar mot lagring <br> REST API: er med Azures klient bibliotek |
| Inledande eng ång slöin ställning     | Minimal                                | Måttlig, varierande utvecklings ansträngning    |
| Data format                 | Azure-blobbar, Azure Files, Azure-tabeller | Azure-blobbar, Azure Files, Azure-tabeller   |
| Prestanda                 | Redan optimerad                      | Optimera när du utvecklar                  |
| Priser                     | Kostnads fritt gäller avgifterna för utgående data      | Kostnads fritt gäller avgifterna för utgående data        |

### <a name="continuous-data-ingestion-over-network"></a>Kontinuerlig data inmatning över nätverk

| Funktion                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Form faktor                                   | Virtuell enhet             | Fysisk enhet          | Tjänst i Azure Portal, agent lokalt                                                            |
| Maskinvara                                      | Din hypervisor            | Tillhandahålls av Microsoft    | NA                                                            |
| Första installations ansträngning                          | Låg (<30 minuter.)            | Måttlig (~ paret timmar) | Stor (~ dagar)                                                 |
| Data format                                   | Azure-blobbar, Azure Files   | Azure-blobbar, Azure Files | [Stöd för 70 + data anslutningar för data lager och format](../../data-factory/copy-activity-overview.md#supported-data-stores-and-formats)|
| För bearbetning av data                           | Inga                         | Ja, via Edge Compute    | Ja                                                           |
| Lokal cache<br>(för att lagra lokala data)    | Ja                        | Ja                      | Inga                                                            |
| Överföring från andra moln                    | Inga                         | Inga                       | Ja                                                           |
| Priser                                       | [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Prissättning](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Nästa steg

- [Överför data med AZCopy](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [Mer information om data överföring med Storage REST-API: er](/dotnet/api/overview/azure/storage).
- Förstå hur du:
    - [Överför data med data Box Gateway](../../databox-gateway/data-box-gateway-deploy-add-shares.md).
    - [Transformera data med data Box Edge innan du skickar dem till Azure](../../databox-online/azure-stack-edge-deploy-configure-compute.md).
- [Lär dig hur du överför data med Azure Data Factory](../../data-factory/tutorial-bulk-copy-portal.md).