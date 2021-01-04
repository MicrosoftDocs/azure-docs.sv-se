---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d3ba7baf79ee972ed7289a1aab93484108da70cf
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724244"
---
Azure Files erbjuder fyra olika nivåer av lagring, Premium, transaktion optimerad, frekvent och låg frekvent så att du kan skräddarsy dina resurser efter prestanda-och pris krav för ditt scenario:

- **Premium**: Premium-filresurser backas upp av solid state-hårddiskar (SSD) och ger konsekvent höga prestanda och låg latens, inom ensiffriga millisekunder för de flesta IO-åtgärder, för i/o-intensiva arbets belastningar. Premium-filresurser lämpar sig för en mängd olika arbets belastningar, t. ex. databaser, webbplatser som är värdar för webbplatser och utvecklings miljöer. Premium fil resurser kan användas med både SMB-(Server Message Block) och NFS-protokoll (Network File System).
- **Transaktion optimerad**: transaktions optimerade fil resurser möjliggör transaktions intensiva arbets belastningar som inte behöver svars tiden som erbjuds av Premium-filresurser. Transaktions optimerade fil resurser erbjuds på standard lagrings maskin varan som backas upp av hård diskar (HDD). Transaktions optimeringen har historiskt sett "standard", men detta refererar till lagrings medie typen i stället för själva nivån (frekvent och låg frekvent är också "standard"-nivåer, eftersom de är på standard lagrings maskin vara).
- Frekvent **: Hot** fil resurser erbjuder lagring som är optimerad för generella fil delnings scenarier, till exempel team resurser. Hot fil resurser erbjuds på den standard lagrings maskin vara som backas upp av hård diskar.
- Låg **frekvent: låg** frekventa fil resurser erbjuder kostnads effektiv lagring som är optimerad för online-scenarier för Arkiv lag ring. Häftiga fil resurser erbjuds på standard lagrings maskin varan som backas upp av hård diskar.

Premium-filresurser distribueras i **FileStorage-lagrings kontots** typ och är bara tillgängliga i en etablerad fakturerings modell. Mer information om den etablerade fakturerings modellen för Premium-filresurser finns i [förstå etablering för Premium-filresurser](../articles/storage/files/understanding-billing.md#provisioned-billing). Standard fil resurser, inklusive transaktions optimering, frekventa och häftiga fil resurser, distribueras i **GPv2-lagring (General Purpose version 2)** och är tillgängliga via betala per användning. Frekventa och häftiga fil resurser är tillgängliga i alla offentliga Azure-och Azure Government regioner. Transaktions optimerade fil resurser är tillgängliga i alla Azure-regioner, inklusive Azure Kina och Azure Germany-regioner.

När du väljer en lagrings nivå för arbets belastningen bör du tänka på kraven på prestanda och användning. Om din arbets belastning kräver en ensiffrig svars tid, eller om du använder SSD-lagrings medier lokalt, är Premium-nivån antagligen den bästa anpassningen. Om låg latens inte är lika mycket av ett problem, till exempel med team resurser som är monterade lokalt från Azure eller cachelagras lokalt med hjälp av Azure File Sync, kan standard lagring vara en bättre anpassning från ett kostnads perspektiv.

När du har skapat en fil resurs i ett lagrings konto kan du inte flytta den till nivåer som är exklusiva till olika lagrings konto typer. Om du till exempel vill flytta en transaktions optimerad fil resurs till Premium-nivån måste du skapa en ny fil resurs i ett FileStorage lagrings konto och kopiera data från den ursprungliga resursen till en ny fil resurs i FileStorage-kontot. Vi rekommenderar att du använder AzCopy för att kopiera data mellan Azure-filresurser, men du kan också använda verktyg som `robocopy` i Windows eller `rsync` MacOS och Linux. 

Fil resurser som distribueras i GPv2 lagrings konton kan flyttas mellan standard-nivåerna (transaktion optimerad, frekvent och låg frekvent) utan att skapa ett nytt lagrings konto och migrera data, men du debiteras transaktionskostnader när du ändrar nivån. När du flyttar en resurs från en Hotter-nivå till en kylare-nivå kommer du att tillföra den kylare-nivåns Skriv transaktions avgift för varje fil i resursen. Att flytta en fil resurs från en låg frekvent nivå till en Hotter-nivå kommer att ådra sig den låg frekventa transaktions avgiften för varje fil i resursen.

Mer information finns i [förstå Azure Files fakturering](../articles/storage/files/understanding-billing.md) .