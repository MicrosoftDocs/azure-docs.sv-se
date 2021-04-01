---
title: Jämförelse av Azure Data Lake Storage Gen1 med Blob Storage
description: Lär dig mer om skillnaderna mellan Azure Data Lake Storage Gen1 och Azure Blob Storage om några viktiga aspekter av stor data bearbetning.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 77ac3c0809c08719d77457c59ef311ad43ef99cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92108345"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Jämföra Azure Data Lake Storage Gen1 och Azure Blob Storage

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

I tabellen i den här artikeln sammanfattas skillnaderna mellan Azure Data Lake Storage Gen1 och Azure Blob Storage tillsammans med några viktiga aspekter av stor data bearbetning. Azure Blob Storage är ett generellt, skalbart objekt lager som har utformats för en mängd olika lagrings scenarier. Azure Data Lake Storage Gen1 är en databas för storskalig lagring som är optimerad för Big data Analytics-arbetsbelastningar.

| Kategori | Azure Data Lake Storage Gen1 | Azure Blob Storage |
| -------- | ---------------------------- | ------------------ |
| Syfte |Optimerad lagring för Big data Analytics-arbetsbelastningar |Allmän objekt lagring för en mängd olika lagrings scenarier, inklusive big data Analytics |
| Användningsfall |Batch, interaktiv, strömnings analys och maskin inlärnings data, till exempel loggfiler, IoT-data, klicka på strömmar, stora data uppsättningar |Alla typer av text eller binära data, t. ex. Programserver del, säkerhets kopierings data, medie lagring för strömning och generella användnings data. Dessutom är fullständig support för analys arbets belastningar. batch, interaktiv, strömnings analys och maskin inlärnings data, till exempel loggfiler, IoT-data, klicka på strömmar, stora data uppsättningar |
| Viktiga begrepp |Data Lake Storage Gen1-kontot innehåller mappar, vilket i sin tur innehåller data som lagras som filer |Lagrings kontot har behållare, vilket i sin tur har data i form av blobbar |
| Struktur |Hierarkiskt fil system |Objekt Arkiv med platt namn område |
| API |REST API över HTTPS |REST API över HTTP/HTTPS |
| API för Server Sidan |[WebHDFS-kompatibel REST API](/rest/api/datalakestore/) |[Azure Blob Storage REST API](/rest/api/storageservices/Blob-Service-REST-API) |
| Hadoop-filsystem-klient |Ja |Ja |
| Data åtgärder-autentisering |Baserat på [Azure Active Directory identiteter](../active-directory/develop/authentication-vs-authorization.md) |Baserat på delade hemligheter – [konto åtkomst nycklar](../storage/common/storage-account-keys-manage.md) och [signaturer för delad åtkomst](../storage/common/storage-sas-overview.md). |
| Data åtgärder – autentiseringsprotokoll |[OpenID Connect](https://openid.net/connect/). Anrop måste innehålla en giltig JWT-(JSON Web-token) som utfärdas av Azure Active Directory.|Hash-baserad Message Authentication Code (HMAC). Anrop måste innehålla en Base64-kodad SHA-256-hash över en del av HTTP-begäran. |
| Data åtgärder-auktorisering |POSIX Access Control listor (ACL: er).  ACL: er baserade på Azure Active Directory identiteter kan ställas in på fil-och mappnivå. |För auktorisering på konto nivå – Använd [konto åtkomst nycklar](../storage/common/storage-account-keys-manage.md)<br>För konto-, container-eller BLOB-auktorisering – Använd [signatur nycklar för delad åtkomst](../storage/common/storage-sas-overview.md) |
| Data åtgärder – granskning |Få. Mer information finns [här](data-lake-store-diagnostic-logs.md) . |Tillgänglig |
| Vilande data kryptering |<ul><li>Transparent, Server sida</li> <ul><li>Med tjänst nycklar som hanteras</li><li>Med Kundhanterade nycklar i Azure-valv</li></ul></ul> |<ul><li>Transparent, Server sida</li> <ul><li>Med tjänst nycklar som hanteras</li><li>Med Kundhanterade nycklar i Azure-valv (för hands version)</li></ul><li>Kryptering av klientsidan</li></ul> |
| Hanterings åtgärder (till exempel konto skapa) |[Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md) för konto hantering |[Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md) för konto hantering |
| SDK: er för utvecklare |.NET, Java, python, Node.js |.NET, Java, python, Node.js, C++, ruby, PHP, go, Android, iOS |
| Analys arbets belastnings prestanda |Optimerade prestanda för arbets belastningar med parallell analys. Högt data flöde och IOPS. |Optimerade prestanda för arbets belastningar med parallell analys. |
| Storleks begränsningar |Inga begränsningar för konto storlekar, fil storlekar eller antal filer |För vissa gränser, se [skalbarhets mål för standard lagrings konton](../storage/common/scalability-targets-standard-account.md) och [skalbarhets-och prestanda mål för Blob Storage](../storage/blobs/scalability-targets.md). Större konto gränser tillgängliga genom att kontakta [Azure-supporten](https://azure.microsoft.com/support/faq/) |
| Geo-redundans |Lokalt redundant (flera kopior av data i en Azure-region) |Lokalt redundant (LRS), zon redundant (ZRS), globalt redundant (GRS), Läs åtkomst globalt redundant (RA-GRS). Mer information finns [här](../storage/common/storage-redundancy.md) |
| Tjänsttillstånd |Allmänt tillgänglig |Allmänt tillgänglig |
| Regional tillgänglighet |Se [här](https://azure.microsoft.com/regions/#services) |Tillgängligt i alla Azure-regioner |
| Pris |Se [prissättning](https://azure.microsoft.com/pricing/details/data-lake-store/) |Se [prissättning](https://azure.microsoft.com/pricing/details/storage/) |