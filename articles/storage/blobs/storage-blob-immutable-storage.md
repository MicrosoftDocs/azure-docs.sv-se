---
title: Oåterkalleligt blob-lagring – Azure Storage
description: Azure Storage erbjuder mask (Skriv en gång, Läs många) stöd för blob-lagring (objekt) som gör det möjligt för användare att lagra data i ett icke-redigerbart tillstånd för ett visst intervall.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: ad660ee69bb568e1a76d59344cf31fbf044aaae9
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581437"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Lagra affärskritiska blobdata med oföränderlig lagring

Oföränderlig lagring för Azure Blob Storage gör det möjligt för användare att lagra affärs kritiska data objekt i en mask (Skriv en gång, läsa många). Det här läget gör att data inte kan raderas och inte kan ändras för ett användardefinierat intervall. För varaktigheten för kvarhållningsintervallet kan blobbar skapas och läsas, men kan inte ändras eller tas bort. Oföränderlig lagring är tillgängligt för generell användning v1, General-Purpose v2, BlobStorage och BlockBlobStorage-konton i alla Azure-regioner.

Information om hur du ställer in och rensar juridiska undantag eller skapar en tidsbaserad bevarande princip med hjälp av Azure Portal, PowerShell eller Azure CLI finns i [Ange och hantera oföränderlighets-principer för Blob Storage](storage-blob-immutability-policies-manage.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="about-immutable-blob-storage"></a>Om oföränderligt Blob Storage

Med oföränderlig lagring får sjukvårds organisationer, finansiella institutioner och relaterade branscher &mdash; särskilt Broker-åter försäljare &mdash; att lagra data på ett säkert sätt. Oföränderlig lagring kan också utnyttjas i alla scenarier för att skydda viktiga data mot ändringar eller borttagningar.

Vanliga program innehåller:

- **Efterlevnad**: oföränderlig lagring för Azure Blob Storage hjälper organisationers adress SEC 17a-4 (f), CFTC 1.31 (d), FINRA och andra bestämmelser. Ett tekniskt informations dokument från Cohasset Associates beskriver hur oföränderligt lagrings utrymme som de här regleringsmyndigheterna kan hämtas via [Microsoft Service Trust Portal](https://aka.ms/AzureWormStorage). [Azure Säkerhetscenter](https://www.microsoft.com/trustcenter/compliance/compliance-overview) innehåller detaljerad information om våra certifieringar för efterlevnad.

- **Kvarhållning av säkra dokument**: oföränderligt lagrings utrymme för Azure Blob Storage garanterar att data inte kan ändras eller tas bort av någon användare, inklusive användare med administratörs behörighet för kontot.

- **Juridiskt bevarande**: oföränderlig lagring för Azure Blob Storage gör det möjligt för användare att lagra känslig information som är viktig för tvister eller företags användning i ett Manipulerings bevis för önskad varaktighet tills spärren tas bort. Den här funktionen är inte begränsad till juridiska användnings fall, men kan även betraktas som ett händelsebaserade undantag eller ett företags lås, där behovet av att skydda data baserat på händelse utlösare eller företags princip krävs.

Oföränderlig lagring stöder följande funktioner:

- **[Stöd för tidsbaserad bevarande princip](#time-based-retention-policies)**: användare kan ange principer för att lagra data för ett angivet intervall. När en tidsbaserad bevarande princip anges kan blobbar skapas och läsas, men inte ändras eller tas bort. När kvarhållningsperioden har upphört att gälla kan blobbar tas bort men inte skrivas över.

- **[Support för juridiskt undantag](#legal-holds)**: om kvarhållningsintervallet inte är känt kan användarna ange juridiska undantag för att lagra oföränderliga data tills det juridiska undantaget är avmarkerat.  När en princip för juridiskt undantag anges kan blobbar skapas och läsas, men inte ändras eller tas bort. Varje juridiskt undantag är associerat med en användardefinierad alfanumerisk tagg (t. ex. ett ärende-ID, händelse namn osv.) som används som en ID-sträng. 

- **Stöd för alla blobnivåer**: WORM-principer är oberoende av Azure Blob Storage-nivå och tillämpas på alla nivåer, frekvent, lågfrekvent och arkiv. Användarna kan flytta data till den mest kostnadsoptimerade nivån för arbetsbelastningen samtidigt som data förblir oförändrade.

- **Konfiguration av container nivå**: användare kan konfigurera tidsbaserade bevarande principer och juridiska undantags taggar på behållar nivån. Användare kan bland annat skapa och låsa tidsbaserade kvarhållningspolicyer, utöka kvarhållningsintervaller, ställa in och ta bort kvarhållning via enkla inställningar på containernivå. De här policyerna gälle för alla blobar i containern, både befintliga och nya.

- **Stöd för gransknings loggning**: varje behållare innehåller en princip Gransknings logg. Det visar upp till sju tidsbaserade kvarhållning-kommandon för låsta tidsbaserade bevarande principer och innehåller användar-ID, kommando typ, tidsstämplar och kvarhållningsintervall. För kvarhållning av juridiska skäl innehåller loggen användar-ID, typ av kommando, tidsstämplar och etiketter för kvarhållningen av juridiska skäl. Den här loggen behålls för principens livs längd i enlighet med rikt linjerna för s 17a-4 (f). [Azure aktivitets loggen](../../azure-monitor/essentials/platform-logs-overview.md) visar en mer omfattande logg över alla kontroll Plans aktiviteter. När du aktiverar [Azures resurs loggar](../../azure-monitor/essentials/platform-logs-overview.md) behålls och visas data Plans åtgärder. Det är användarens ansvar att lagra de här loggarna beständigt såsom krävs enligt regelverk eller andra ändamål.

## <a name="how-it-works"></a>Så här fungerar det

I den oföränderliga lagringen för Azure-blobar finns stöd för två olika typer av WORM- eller oföränderliga policyer: tidsbaserad kvarhållning och kvarhållning av juridiska skäl. När en tidsbaserad bevarande princip eller ett juridiskt undantag tillämpas på en behållare flyttas alla befintliga blobar till ett oföränderligt WORM-tillstånd på mindre än 30 sekunder. Alla nya blobbar som överförs till den princip skyddade behållaren kommer också att flyttas till ett oföränderligt tillstånd. När alla blobbar har ett oföränderligt tillstånd bekräftas den oföränderliga principen och eventuella Skriv-eller borttagnings åtgärder i den oföränderliga behållaren är inte tillåtna.

Borttagning av behållare och lagrings konto tillåts inte heller om det finns blobbar i en behållare som skyddas av ett juridiskt undantag eller en låst tidsbaserad princip. En juridisk undantags princip skyddar mot borttagning av BLOB, container och lagrings konto. Både olåsta och låsta tidsbaserade principer skyddar mot BLOB-borttagning under den angivna tiden. Både olåsta och låsta tidsbaserade principer skyddar mot borttagning av behållare endast om det finns minst en BLOB i behållaren. Endast en behållare med *låst* tidsbaserad princip skyddar mot lagrings konto borttagningar. behållare med upplåsta Time-based-principer erbjuder inte lagrings kontots borttagnings skydd eller efterlevnad.

Följande diagram visar hur tidsbaserade bevarande principer och juridiska undantag förhindrar Skriv-och borttagnings åtgärder när de tillämpas.

:::image type="content" source="media/storage-blob-immutable-storage/worm-diagram.png" alt-text="Diagram över hur bevarande principer och juridiska undantag förhindrar Skriv-och borttagnings åtgärder":::

Mer information om hur du ställer in och låser tidsbaserade bevarande principer finns i [Ange och hantera oföränderlighets-principer för Blob Storage](storage-blob-immutability-policies-manage.md).

## <a name="time-based-retention-policies"></a>Tidsbaserade bevarande principer

> [!IMPORTANT]
> En tidsbaserad bevarande princip måste vara *låst* för att blobben ska vara i ett inkompatibelt läge (Write and delete Protected) för SEC 17a-4 (f) och annan myndighets efterlevnad. Vi rekommenderar att du låser principen inom rimlig tid, vanligt vis mindre än 24 timmar. Det ursprungliga läget för en tillämpad tidsbaserad bevarande princip *låses upp*, så att du kan testa funktionen och göra ändringar i principen innan du låser den. Även om det *upplåsta* läget ger oföränderlighets skydd rekommenderar vi inte att du använder det *upplåsta* läget för något annat ändamål än kortsiktiga funktions försök. 

När en tidsbaserad bevarande princip tillämpas på en behållare kommer alla blobar i behållaren att stanna kvar i det oföränderliga läget under den *gällande* kvarhållningsperioden. Den effektiva kvarhållningsperioden för blobbar är lika med skillnaden mellan blobens **skapande tid** och det användardefinierade kvarhållningsintervallet. Eftersom användarna kan utöka kvarhållningsintervallet används det senaste värdet för det användardefinierade kvarhållningsintervallet när den effektiva kvarhållningsperioden ska beräknas.

Anta till exempel att en användare skapar en tidsbaserad bevarande princip med ett kvarhållningsintervall på fem år. En befintlig BLOB i behållaren, _testblob1_, har skapats för ett år sedan; Därför är den effektiva Retentions perioden för _testblob1_ fyra år. När en ny BLOB, _testblob2_, överförs till behållaren, är den effektiva kvarhållningsperioden för _testblob2_ fem år från tidpunkten då den skapades.

En upplåst tidsbaserad bevarande princip rekommenderas endast för funktions testning och en princip måste vara låst för att vara kompatibel med s 17a-4 (f) och annan myndighets efterlevnad. När en tidsbaserad bevarande princip är låst går det inte att ta bort principen och maximalt fem ökningar tillåts för den gällande kvarhållningsperioden.

Följande begränsningar gäller för bevarande principer:

- För ett lagrings konto är det maximala antalet behållare med låsta tidsbaserade, oföränderliga principer som är 10 000.
- Det minsta kvarhållningsintervallet är 1 dag. Det maximala värdet är 146 000 dagar (400 år).
- För en behållare är det maximala antalet redigeringar som utökar ett kvarhållningsintervall för låsta tidsbaserade, oföränderliga principer 5.
- För en behållare behålls högst sju tidsbaserade tidsbaserade logg gransknings loggar för en låst princip.

### <a name="allow-protected-append-blobs-writes"></a>Tillåt att skyddade bifogade blobbar skrivs

Tillägg av blobar består av data block och optimerade för data tilläggs åtgärder som krävs av gransknings-och loggnings scenarier. Genom att lägga till blobar tillåts du bara lägga till nya block till slutet av blobben. Oavsett oföränderlighets, är det grundläggande inte tillåtet att ändra eller ta bort befintliga block i en tilläggs-blob. Mer information om hur du lägger till blobar finns i avsnittet [om att lägga till blobbar](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs).

Endast tidsbaserade bevarande principer har en `allowProtectedAppendWrites` inställning som gör det möjligt att skriva nya block till en append-BLOB samtidigt som oföränderlighets skydd och efterlevnad upprätthålls. Om den här inställningen är aktive rad, kan du skapa en tilläggs-BLOB direkt i den skyddade behållaren och fortsätta att lägga till nya data block i slutet av befintliga tilläggs-blobar med hjälp av *AppendBlock* -API: et. Det går bara att lägga till nya block och befintliga block kan inte ändras eller tas bort. Oföränderlighets-skydd för tids kvarhållning gäller fortfarande, vilket förhindrar borttagning av tilläggs-bloben tills den gällande kvarhållningsperioden har förflutit. Aktivering av den här inställningen påverkar inte oföränderlighets beteendet för block-blobbar eller Page blobbar.

Eftersom den här inställningen är en del av en tidsbaserad bevarande princip stannar de bifogade Blobbarna kvar i det oföränderliga läget för varaktigheten för den *gällande* kvarhållningsperioden. Eftersom nya data kan läggas till utöver den inledande skapandet av tilläggs-bloben, finns det en liten skillnad i hur kvarhållningsperioden fastställs. Den effektiva kvarhållning är skillnaden mellan bifogad blobs **senaste ändrings tid** och det användardefinierade kvarhållningsintervall. På samma sätt som kvarhållningsintervallet är utökad använder oföränderlig lagring det senaste värdet för det användardefinierade kvarhållningsintervallet för att beräkna den effektiva kvarhållningsperioden.

Anta till exempel att en användare skapar en tidsbaserad bevarande princip med `allowProtectedAppendWrites` aktive rad och ett kvarhållningsintervall på 90 dagar. En tilläggs-BLOB, _logblob1_, skapas i behållaren idag, nya loggar fortsätter att läggas till i tilläggs-bloben under de närmaste 10 dagarna. den effektiva kvarhållningsperioden för _logblob1_ är alltså 100 dagar från idag (tidpunkten för senaste tillägg + 90 dagar).

Vid låsning av tidsbaserade tidsbaserade bevarande principer kan `allowProtectedAppendWrites` inställningen aktive ras och inaktive ras när som helst. När den tidsbaserade bevarande principen är låst `allowProtectedAppendWrites` går det inte att ändra inställningen.

Juridiska undantags principer kan inte aktive ras `allowProtectedAppendWrites` och alla juridiska undantag kommer att upphäver egenskapen "allowProtectedAppendWrites". Om ett juridiskt undantag tillämpas på en tidsbaserad bevarande princip som `allowProtectedAppendWrites` är aktive rad, Miss *AppendBlock* API: et tills det juridiska undantaget lyfts upp.

## <a name="legal-holds"></a>Kvarhållning av juridiska skäl

Juridiska undantag är temporära undantag som kan användas för juridisk undersökning eller allmänna skydds principer. Varje princip för juridiskt bevarande måste associeras med en eller flera taggar. Taggar används som en namngiven identifierare, t. ex. ett ärende-ID eller en händelse, för att kategorisera och beskriva syftet med undantagen.

En behållare kan ha både ett juridiskt undantag och en tidsbaserad bevarande princip på samma gång. Alla blobar i containern förblir då i det oföränderliga tillståndet tills alla juridiska skäl tas bort, även om den effektiva kvarhållningsperioden har upphört. På motsvarande sätt förblir blobarna i det oföränderliga tillståndet tills den effektiva kvarhållningsperioden upphör även om alla juridiska skäl tas bort.

Följande begränsningar gäller för juridiska undantag:

- För ett lagrings konto är det maximala antalet behållare med en juridisk undantags inställning 10 000.
- För en behållare är det maximala antalet juridiska undantags etiketter 10.
- Den minsta längden för en juridiskt Hold-tagg består av tre alfanumeriska tecken. Den maximala längden är 23 alfanumeriska tecken.
- För en behållare behålls det maximala antalet gransknings loggar för den här principen.

## <a name="scenarios"></a>Scenarier

I följande tabell visas de typer av Blob Storage-åtgärder som har inaktiverats för olika scenarier. Mer information finns i REST API dokumentationen för [Azure Blob service](/rest/api/storageservices/blob-service-rest-api) .

| Scenario | BLOB-tillstånd | Nekade BLOB-åtgärder | Skydd av behållare och konto |
|--|--|--|--|
| Effektivt kvarhållningsintervall för blobben har ännu inte gått ut och/eller bevarande av juridiska skäl har angetts | Oåterkallelig: både ta bort- och skrivskyddad | Lägg till BLOB<sup>1</sup>, sätt block<sup>1</sup>, list block lista<sup>1</sup>, ta bort behållare, ta bort BLOB, ange BLOB-metadata, sätt sida, ange BLOB-egenskaper, ögonblicks bilds-BLOB, stegvis Copy-BLOB, Lägg till block<sup>2</sup> | Borttagning av container nekad; Borttagning av lagrings konto nekades |
| Det gällande kvarhållningsintervallet i blobben har upphört att gälla och inget juridiskt undantag har angetts | Skrivskyddad endast (ta bort tillåts) | Lägg till BLOB<sup>1</sup>, sätt block<sup>1</sup>, list block lista<sup>1</sup>, ange BLOB-metadata, placerings sida, ange BLOB-egenskaper, ögonblicks bilds-BLOB, stegvis kopia-BLOB, tillägg block<sup>2</sup> | Borttagning av behållare nekas om det finns minst 1 BLOB i den skyddade behållaren. Borttagning av lagrings konto nekas endast för *låsta* tidsbaserade principer |
| Ingen WORM-princip har tillämpats (ingen tidsbaserad kvarhållning och inget juridiskt undantags märke) | Föränderlig | Inga | Inga |

<sup>1</sup> Blob-tjänsten tillåter dessa åtgärder att skapa en ny BLOB en gång. Alla efterföljande överskrivnings åtgärder på en befintlig BLOB-sökväg i en oföränderlig container är inte tillåtna.

<sup>2</sup> append-block tillåts endast för tidsbaserade bevarande principer med `allowProtectedAppendWrites` egenskapen aktive rad. Mer information finns i avsnittet [Tillåt att skyddade bifogade blobbar skrivs](#allow-protected-append-blobs-writes) .

> [!IMPORTANT]
> Vissa arbets belastningar, till exempel [SQL-säkerhetskopiering till URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url), skapar en blob och lägger sedan till den. Om behållaren har en aktiv tidsbaserad bevarande princip eller ett juridiskt undantag, kommer det här mönstret inte att fungera.

## <a name="pricing"></a>Prissättning

Det kostar inget extra att använda den här funktionen. Oföränderliga data priss ätts på samma sätt som föränderligt-data. Pris information om Azure Blob Storage finns på sidan med [Azure Storage priser](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="faq"></a>Vanliga frågor

**Kan du ge dokumentation om WORM-kompatibilitet?**

Ja. För att dokumentera efterlevnaden har Microsoft bevarat en ledande oberoende utvärderings verksamhet som specialiserar sig på hantering av registrering och informations styrning, Cohassete Associates, för att utvärdera oföränderligt Blob Storage och dess efterlevnad av krav som är särskilt kopplade till den finansiella tjänste industrin. Cohasset validerade att den oföränderliga blob-lagringen, när den används för att bevara tidsbaserade blobbar i ett WORM-tillstånd, uppfyller relevanta lagrings krav för CFTC Rule 1.31 (c)-(d), FINRA-regel 4511 och SEC-regel 17a-4. Microsoft riktade sig på den här uppsättningen regler eftersom de representerar de mest förenklade vägledningarna globalt för poster som kvarhålls för finansiella institutioner. Cohasset-rapporten finns i [Microsoft Service Trust Center](https://aka.ms/AzureWormStorage). Kontakta Azure-supporten om du vill begära en skrivelse från Microsoft avseende WORM oföränderlighets-kompatibilitet.

**Gäller funktionen bara block-blobbar och bifoga blobbar, eller till sid-blobar också?**

Oåterkalleligt lagrings utrymme kan användas med valfri Blob-typ som anges på behållar nivån, men vi rekommenderar att du använder mask för behållare som huvudsakligen lagrar block-blobbar och bifogade blobbar. Befintliga blobbar i en behållare skyddas av en Nydefinierad WORM-princip. Men eventuella nya sid-blobar måste skapas utanför mask behållaren och kopieras sedan i. När du har kopierat till en WORM-behållare tillåts inga ytterligare ändringar i en sid-blob. Att ställa in en mask princip på en behållare som lagrar virtuella hård diskar (Page blobbar) för aktiva virtuella datorer, rekommenderas inte eftersom den kommer att låsa den virtuella dator disken. Vi rekommenderar att du noga läser igenom dokumentationen och testar dina scenarier innan du låser eventuella tidsbaserade principer.

**Måste jag skapa ett nytt lagrings konto för att använda den här funktionen?**

Nej, du kan använda oföränderligt lagrings utrymme med befintliga eller nyligen skapade allmänna v1-, BlobStorage-eller BlockBlobStorage-konton. Generella v1-lagrings konton stöds, men vi rekommenderar att du uppgraderar till General-Purpose v2 så att du kan dra nytta av fler funktioner. Information om hur du uppgraderar ett befintligt allmänt v1-lagrings konto finns i [uppgradera ett lagrings konto](../common/storage-account-upgrade.md).

**Kan jag använda både en juridisk princip för kvarhållning och tidsbaserad lagring?**

Ja, en behållare kan ha både ett juridiskt undantag och en tidsbaserad bevarande princip på samma gång; inställningen "allowProtectedAppendWrites" kommer dock inte att gälla förrän det juridiska undantaget har rensats. Alla blobar i containern förblir då i det oföränderliga tillståndet tills alla juridiska skäl tas bort, även om den effektiva kvarhållningsperioden har upphört. På motsvarande sätt förblir blobarna i det oföränderliga tillståndet tills den effektiva kvarhållningsperioden upphör även om alla juridiska skäl tas bort. 

**Finns det bara juridiska undantags principer för rättsliga förfaranden eller andra användnings scenarier?**

Nej, juridiskt bevarande är bara den allmänna termen som används för en icke-baserad bevarande princip. Den behöver inte bara användas för tvister som rör tvister. Principer för juridiskt bevarande är användbara för att inaktivera Skriv över och ta bort för att skydda viktiga Enterprise WORM-data, där kvarhållningsperioden är okänt. Du kan använda den som en företags princip för att skydda dina verksamhets kritiska mask arbets belastningar eller använda den som en princip för en mellanlagrings princip innan en anpassad händelse utlösare kräver att en tidsbaserad bevarande princip används. 

**Kan jag ta bort en _låst_ tidsbaserad bevarande princip eller ett juridiskt undantag?**

Endast upplåsta tidsbaserade bevarande principer kan tas bort från en behållare. När en tidsbaserad bevarande princip är låst kan den inte tas bort. endast tillägg för gällande kvarhållningsperiod är tillåtna. Juridiska undantags taggar kan tas bort. När alla juridiska taggar tas bort tas det juridiska undantaget bort.

**Vad händer om jag försöker ta bort en behållare med en tidsbaserad bevarande princip eller ett juridiskt undantag?**

Det går inte att ta bort container om det finns minst en BLOB i behållaren med antingen en låst eller olåst tidsbaserad bevarande princip eller om behållaren har ett juridiskt undantag. Åtgärden för att ta bort behållare lyckas bara om det inte finns några blobbar i behållaren och det inte finns några juridiska undantag. 

**Vad händer om jag försöker ta bort ett lagrings konto med en behållare som har en tidsbaserad bevarande princip eller ett juridiskt undantag?**

Det går inte att ta bort lagrings kontot om det finns minst en behållare med en juridisk undantags uppsättning eller en **låst** tidsbaserad princip. En behållare med en upplåst tidsbaserad princip skyddar inte mot borttagning av lagrings kontot. Du måste ta bort alla juridiska undantag och ta bort alla **låsta** behållare innan du kan ta bort lagrings kontot. Information om borttagning av behållare finns i föregående fråga. Du kan också använda ytterligare borttagnings skydd för ditt lagrings konto med [Azure Resource Manager lås](../../azure-resource-manager/management/lock-resources.md).

**Kan jag flytta data mellan olika BLOB-nivåer (frekvent, låg frekvent, arkivera) när blobben är i läget oföränderligt?**

Ja, du kan använda kommandot Set BLOB Tier för att flytta data mellan BLOB-nivåerna samtidigt som data hålls i ett kompatibelt tillstånd. Oföränderligt lagrings utrymme stöds på BLOB-nivåer för frekvent, låg frekvent och arkiv.

**Vad händer om jag inte betalat och mitt kvarhållningsintervall inte har gått ut?**

Vid utebliven betalning tillämpas normala data bevarande principer enligt vad som anges i avtals villkoren i avtalet med Microsoft. Allmän information finns i [Data Management på Microsoft](https://www.microsoft.com/en-us/trust-center/privacy/data-management). 

**Finns det en utvärderingsversion eller en respitperiod för att bara testa funktionen?**

Ja. När en tidsbaserad bevarande princip först skapas är den i ett *låst* läge. I det här läget kan du göra önskade ändringar i kvarhållningsintervallet, till exempel ökning eller minskning och till och med ta bort principen. När principen är låst förblir den låst tills kvarhållningsintervallet upphör att gälla. Den här låsta principen förhindrar borttagning och ändring av kvarhållningsintervallet. Vi rekommenderar starkt att du bara använder det *upplåsta* läget för utvärderings syfte och låser principen inom en 24-timmarsperiod. Dessa metoder hjälper dig att följa s 17a-4 (f) och andra bestämmelser.

**Kan jag använda mjuk borttagning tillsammans med oföränderliga BLOB-principer?**

Ja, om dina krav för efterlevnad tillåter att mjuk borttagning aktive ras. [Mjuk borttagning för Azure Blob Storage](./soft-delete-blob-overview.md) gäller för alla behållare i ett lagrings konto, oavsett en juridisk undantags-eller tidsbaserad bevarande princip. Vi rekommenderar att du aktiverar mjuk borttagning för ytterligare skydd innan eventuella mask principer som inte kan användas och bekräftas.

## <a name="next-steps"></a>Nästa steg

- [Ange och hantera oföränderlighets-principer för Blob Storage](storage-blob-immutability-policies-manage.md)
- [Ange regler för att automatiskt nivå och ta bort BLOB-data med livs cykel hantering](storage-lifecycle-management-concepts.md)
- [Mjuk borttagning för Azure Storage-blobar](./soft-delete-blob-overview.md)
- [Skydda prenumerationer, resurs grupper och resurser med Azure Resource Manager lås](../../azure-resource-manager/management/lock-resources.md).