---
title: Viktig information om Azure Data Box Gateway för allmän tillgänglighet | Microsoft Docs
description: Beskriver kritiska öppna problem och lösningar för Azure Data Box Gateway som kör den allmänna tillgänglighets versionen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 2984f7990b9570c5ec57633de7f7e50162fb6f46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96583019"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Viktig information om Azure Data Box Edge/Azure Data Box Gateway för allmän tillgänglighet

## <a name="overview"></a>Översikt

I följande versions information identifieras kritiska öppnings problem och lösta problem för allmän tillgänglighet (GA) för Azure Data Box Edge och Azure Data Box Gateway. 

Viktig information uppdateras kontinuerligt och eftersom kritiska problem som kräver en lösning upptäcks, läggs de till. Innan du distribuerar Data Box Edge/Data Box Gateway bör du gå igenom informationen i viktig information.

GA-versionen motsvarar program varu versionerna:

- **Data Box Gateway 1903 (1.5.814.447)**
- **Data Box Edge 1903 (1.5.814.447)**


## <a name="whats-new"></a>Nyheter

- **Nya virtuella disk avbildningar** – nya VHDX och VMDK är nu tillgängliga i Azure Portal. Hämta de här avbildningarna för att etablera, konfigurera och distribuera nya Data Box Gateway GA-enheter. Data Box Gateway enheter som skapades i de tidigare för hands versionerna kan inte uppdateras till den här versionen. Mer information finns i [förbereda för distribution av Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- **NFS-support** – NFS-stöd är för närvarande en för hands version och tillgänglig för v 3.0-och v 4.1-klienter som har åtkomst till Data Box Edge och data Box gateway enheter.
- **Lagrings återhämtning** – din data Box Edge enhet kan motstå en datadisk med lagrings återhämtnings funktionen. Den här funktionen finns för närvarande som en förhandsversion. Du kan aktivera lagrings återhämtning genom att välja alternativet **elastisk** i **lagrings inställningarna** i det lokala webb gränssnittet.


## <a name="known-issues-in-ga-release"></a>Kända problem i GA-versionen

Följande tabell innehåller en sammanfattning av kända problem för den Data Box Gateway versionen som körs.

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
| **1.** |Filtyper | Följande filtyper stöds inte: Character-filer, blockera filer, Sockets, Pipes, symboliska länkar.  |Om de här filerna kopieras skapas 0-långa filer som skapas på NFS-resursen. Filerna är i ett fel tillstånd och rapporteras också i *error.xml*. <br> Symboliska länkar till kataloger resulterar i att kataloger aldrig markeras som offline. Det innebär att du kanske inte ser det grå korset i de kataloger som anger att katalogerna är offline och att allt tillhör ande innehåll har laddats upp fullständigt till Azure. |
| **2.** |Borttagning | På grund av ett fel i den här versionen, om en NFS-resurs tas bort, går det inte att ta bort resursen. Resurs statusen kommer att visas *när du tar bort*.  |Detta inträffar bara när resursen använder ett fil namn som inte stöds. |
| **3.** |Kopiera | Data kopieringen misslyckades med felet: den begärda åtgärden kunde inte slutföras på grund av en begränsning i fil systemet.  |Den alternativa data strömmen (ADS) som är associerad med fil storleken större än 128 KB stöds inte.   |


## <a name="next-steps"></a>Nästa steg

- [Förbered för att distribuera Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- [Förbered för att distribuera Azure Data Box Edge](../databox-online/azure-stack-edge-deploy-prep.md).
