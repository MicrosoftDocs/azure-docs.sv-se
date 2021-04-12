---
title: Vanliga FabricClient-undantag utlöst
description: 'Beskriver vanliga undantag och fel som kan uppstå i FabricClient-API: erna när du utför åtgärder för program-och kluster hantering.'
ms.topic: conceptual
ms.date: 06/20/2018
ms.openlocfilehash: c2b5418729855ce366512d9718e22124e5cd837a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627718"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Vanliga undantag och fel när du arbetar med FabricClient-API:er
[FabricClient](/dotnet/api/system.fabric.fabricclient) -API: er gör det möjligt för kluster-och program administratörer att utföra administrativa uppgifter på ett Service Fabric program, en tjänst eller ett kluster. Till exempel program distribution, uppgradering och borttagning, kontroll av hälso tillståndet för ett kluster eller testning av en tjänst. Programutvecklare och kluster administratörer kan använda FabricClient-API: er för att utveckla verktyg för att hantera Service Fabric kluster och program.

Det finns många olika typer av åtgärder som kan utföras med hjälp av FabricClient.  Varje metod kan orsaka undantag för fel på grund av felaktiga inkörningar, körnings fel eller problem med övergående infrastruktur.  Se API Reference-dokumentationen för att ta reda på vilka undantag som genereras av en speciell metod. Det finns vissa undantag, som kan genereras av många olika [FabricClient](/dotnet/api/system.fabric.fabricclient) -API: er. I följande tabell visas de undantag som är gemensamma för FabricClient-API: erna.

| Undantag | Utlöst när |
| --- |:--- |
| [System. Fabric. FabricObjectClosedException](/dotnet/api/system.fabric.fabricobjectclosedexception) |[FabricClient](/dotnet/api/system.fabric.fabricclient) -objektet är i ett stängt tillstånd. Ta bort det [FabricClient](/dotnet/api/system.fabric.fabricclient) -objekt som du använder och instansiera ett nytt [FabricClient](/dotnet/api/system.fabric.fabricclient) -objekt. |
| [System. TimeoutException](/dotnet/core/api/system.timeoutexception) |Tids gränsen för åtgärden uppnåddes. [OperationTimedOut](/dotnet/api/system.fabric.fabricerrorcode) returneras när åtgärden tar fler än MaxOperationTimeout att slutföra. |
| [System. UnauthorizedAccessException](/dotnet/core/api/system.unauthorizedaccessexception) |Åtkomst kontrollen för åtgärden misslyckades. E_ACCESSDENIED returneras. |
| [System. Fabric. FabricException](/dotnet/api/system.fabric.fabricexception) |Ett körnings fel uppstod när åtgärden utfördes. Vilken som helst av FabricClient-metoderna kan resultera i [FabricException](/dotnet/api/system.fabric.fabricexception), egenskapen [ErrorCode](/dotnet/api/system.fabric.fabricexception.errorcode) anger den exakta orsaken till undantaget. Felkoder definieras i [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) -uppräkningen. |
| [System. Fabric. FabricTransientException](/dotnet/api/system.fabric.fabrictransientexception) |Åtgärden misslyckades på grund av ett tillfälligt fel tillstånd av någon typ. En åtgärd kan till exempel Miss lyckas eftersom det inte går att komma åt ett kvorum med repliker. Tillfälliga undantag motsvarar misslyckade åtgärder som kan göras om. |

Några vanliga [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) -fel som kan returneras i en [FabricException](/dotnet/api/system.fabric.fabricexception):

| Fel | Villkor |
| --- |:--- |
| CommunicationError |Det gick inte att utföra åtgärden på grund av ett kommunikations fel. försök igen. |
| InvalidCredentialType |Autentiseringstypen är ogiltig. |
| InvalidX509FindType |X509FindType är ogiltig. |
| InvalidX509StoreLocation |X509-lagrings platsen är ogiltig. |
| InvalidX509StoreName |X509-arkivets namn är ogiltigt. |
| InvalidX509Thumbprint |Den X509-certifikatets tumavtryck-sträng är ogiltig. |
| InvalidProtectionLevel |Skydds nivån är ogiltig. |
| InvalidX509Store |Det går inte att öppna X509-certifikat arkivet. |
| InvalidSubjectName |Ämnes namnet är ogiltigt. |
| InvalidAllowedCommonNameList |Formatet på den gemensamma namn List strängen är ogiltig. Det ska vara en kommaavgränsad lista. |
