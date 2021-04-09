---
title: Azure Data Lake Storage Gen1 migrering mellan regioner | Microsoft Docs
description: Lär dig vad du ska tänka på när du planerar och slutför en migrering till Azure Data Lake Storage Gen1 när den blir tillgänglig i nya regioner.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: twooley
ms.openlocfilehash: c6520036f3ddb8799025129391330268b0604886
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97723814"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Migrera Azure Data Lake Storage Gen1 över regioner

När Azure Data Lake Storage Gen1 blir tillgängligt i nya regioner kan du välja att göra en migrering vid ett tillfälle, för att dra nytta av den nya regionen. Lär dig vad du ska tänka på när du planerar och Slutför migreringen.

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Mer information finns i [skapa ett kostnads fritt Azure-konto idag](https://azure.microsoft.com/pricing/free-trial/).
* **Ett data Lake Storage gen1 konto i två olika regioner**. Mer information finns i [Kom igång med Azure Data Lake Storage gen1](data-lake-store-get-started-portal.md).
* **Azure Data Factory**. Mer information finns i [Introduktion till Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Överväganden vid migrering

Börja med att identifiera den migrations strategi som passar bäst för ditt program som skriver, läser eller bearbetar data i Data Lake Storage Gen1. När du väljer en strategi bör du tänka på programmets tillgänglighets krav och avbrotts tiden som inträffar under en migrering. Den enklaste metoden kan till exempel vara att använda moln migrations modellen "lyft och-Shift". I den här metoden ska du pausa programmet i din befintliga region medan alla dina data kopieras till den nya regionen. När kopieringen är färdig, återupptar du ditt program i den nya regionen och tar sedan bort det gamla Data Lake Storage Gen1 kontot. Stillestånds tid under migreringen krävs.

För att minska stillestånds tiden kan du omedelbart börja mata in nya data i den nya regionen. När du har den minsta mängd data som krävs kör du ditt program i den nya regionen. I bakgrunden fortsätter du att kopiera äldre data från det befintliga Data Lake Storage Gen1-kontot till det nya Data Lake Storage Gen1-kontot i den nya regionen. Genom att använda den här metoden kan du göra växeln till den nya regionen med lite stillestånds tid. Ta bort det gamla Data Lake Storage Gen1-kontot när alla äldre data har kopierats.

Andra viktiga uppgifter som du bör tänka på när du planerar migreringen är:

* **Data volym**. Mängden data (i gigabyte, antalet filer och mappar osv.) påverkar den tid och de resurser som du behöver för migreringen.

* **Data Lake Storage gen1 konto namn**. Det nya konto namnet i den nya regionen måste vara globalt unikt. Till exempel kan namnet på ditt gamla Data Lake Storage Gen1-konto i USA, östra 2, vara contosoeastus2.azuredatalakestore.net. Du kan namnge ditt nya Data Lake Storage Gen1-konto i Nord EU-contosonortheu.azuredatalakestore.net.

* **Verktyg**. Vi rekommenderar att du använder [Azure Data Factory kopierings aktivitet](../data-factory/connector-azure-data-lake-store.md) för att kopiera data Lake Storage gen1-filer. Data Factory stöder data förflyttning med hög prestanda och tillförlitlighet. Tänk på att Data Factory kopierar endast mapphierarkin och innehållet i filerna. Du måste manuellt tillämpa alla åtkomst kontrol listor (ACL: er) som du använder i det gamla kontot för det nya kontot. Mer information, inklusive prestanda mål för bästa tänkbara scenarier, finns i [prestanda-och justerings guiden för kopierings aktivitet](../data-factory/copy-activity-performance.md). Om du vill att data ska kopieras snabbare kan du behöva använda ytterligare enheter för moln data flytt. Vissa andra verktyg, t. ex. AdlCopy, stöder inte kopiering av data mellan regioner.  

* **Bandbredds avgifter**. [Bandbredds avgifterna](https://azure.microsoft.com/pricing/details/bandwidth/) gäller eftersom data överförs från en Azure-region.

* **ACL: er för dina data**. Skydda dina data i den nya regionen genom att använda ACL: er för filer och mappar. Mer information finns i [skydda data som lagras i Azure Data Lake Storage gen1](data-lake-store-secure-data.md). Vi rekommenderar att du använder migreringen för att uppdatera och justera dina ACL: er. Du kanske vill använda inställningar som liknar dina aktuella inställningar. Du kan visa de ACL: er som tillämpas på en fil med hjälp av Azure Portal, [PowerShell-cmdletar](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission)eller SDK: er.  

* **Plats för Analytics-tjänster**. För bästa prestanda bör analys tjänsterna, som Azure Data Lake Analytics eller Azure HDInsight, finnas i samma region som dina data.  

## <a name="next-steps"></a>Nästa steg
* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
