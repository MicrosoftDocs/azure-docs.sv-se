---
title: Introduktion till Table storage – Objektlagring i Azure | Microsoft Docs
description: Lagra strukturerade data i molnet med hjälp av Azure Table Storage, en NoSQL-databas.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.devlang: dotnet
ms.topic: overview
ms.date: 01/07/2021
ms.subservice: tables
ms.openlocfilehash: 292104651c6bb9b63e9d8cbe26fea2bf8c6acbf1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98027748"
---
# <a name="what-is-azure-table-storage-"></a>Vad är Azure Table Storage? 

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Azure Table Storage är en tjänst som lagrar icke-relationella strukturerade data (även kallade strukturerade NoSQL-data) i molnet, vilket ger ett Arkiv för nyckel/attribut med en schema lös design. Eftersom Table Storage är schemalös är det enkelt att anpassa dina data i takt med att programmets behov förändras. Åtkomsten till data i Table Storage är snabb och kostnadseffektiv för många typer av program, och medför normalt lägre kostnad än traditionell SQL för liknande datavolymer.

Du kan använda Table Storage för att lagra flexibla datauppsättningar som användardata för webbprogram, adressböcker, enhetsinformation eller andra typer av metadata som din tjänst kräver. Du kan lagra valfritt antal enheter i en tabell, och ett lagringskonto kan innehålla valfritt antal tabeller, upp till lagringskontots kapacitetsgräns.

[!INCLUDE [storage-table-concepts-include](../../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Nästa steg

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.

* [Kom igång med Azure Table Storage i .NET](../../cosmos-db/tutorial-develop-table-dotnet.md)

* Fullständig information om tillgängliga API:er finns i referensdokumentationen för tabelltjänsten:

    * [Storage-klientbibliotek för .NET-referens](/dotnet/api/overview/azure/storage)

    * [Referens för REST-API](/rest/api/storageservices/)