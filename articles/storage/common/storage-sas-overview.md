---
title: Ge begränsad åtkomst till data med signaturer för delad åtkomst (SAS)
titleSuffix: Azure Storage
description: Lär dig mer om att använda signaturer för delad åtkomst (SAS) för att delegera åtkomst till Azure Storage resurser, inklusive blobbar, köer, tabeller och filer.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: b9882168cd063cb4448269cc6a4949778fe93fb1
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509866"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Bevilja begränsad åtkomst till Azure Storage resurser med signaturer för delad åtkomst (SAS)

En signatur för delad åtkomst (SAS) ger säker delegerad åtkomst till resurser i ditt lagrings konto utan att kompromissa med säkerheten för dina data. Med en SAS har du detaljerad kontroll över hur en klient kan komma åt dina data. Du kan kontrol lera vilka resurser som klienten kan komma åt, vilka behörigheter de har på dessa resurser och hur länge SAS är giltig, bland andra parametrar.

## <a name="types-of-shared-access-signatures"></a>Typer av signaturer för delad åtkomst

Azure Storage stöder tre typer av signaturer för delad åtkomst:

- **SAS för användar delegering.** En användar Delegerings-sa skyddas med Azure Active Directory (Azure AD)-autentiseringsuppgifter och även av de behörigheter som angetts för SAS. En användar Delegerings-SAS gäller endast för Blob Storage.

    Mer information om sa för användar delegering finns i [skapa en användar delegering SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas).

- **Tjänstens SAS.** En tjänst-SAS skyddas med lagrings konto nyckeln. En tjänst-SAS delegerar åtkomst till en resurs i endast en av de Azure Storage tjänsterna: Blob Storage, Queue Storage, Table Storage eller Azure Files.

    Mer information om tjänste-SAS finns i [skapa en tjänst SAS (REST API)](/rest/api/storageservices/create-service-sas).

- **Kontots SAS.** En konto säkerhets Association skyddas med lagrings konto nyckeln. En konto-SAS delegerar åtkomst till resurser i en eller flera av lagringstjänsterna. Alla åtgärder som är tillgängliga via en tjänst eller en användar Delegerings-SAS är också tillgängliga via en konto säkerhets Association. Med kontots SAS kan du dessutom Delegera åtkomst till åtgärder som tillämpas på tjänst nivån, till exempel **Hämta/ange tjänst egenskaper** och **Hämta Service statistik** åtgärder. Du kan också delegera åtkomst till läs-, skriv- och borttagningsåtgärder i blobcontainrar, tabeller, köer och filresurser som inte tillåts med en tjänst-SAS. 

    Om du vill ha mer information om kontots SAS [skapar du ett konto SAS (REST API)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> Microsoft rekommenderar att du använder Azure AD-autentiseringsuppgifter när det är möjligt som en säkerhets åtgärd, i stället för att använda konto nyckeln, vilket kan vara enklare att avslöja. När program designen kräver delade åtkomst-signaturer för att få åtkomst till Blob Storage använder du autentiseringsuppgifter för Azure AD för att skapa en användar Delegerings-sa när det är möjligt för överlägsen säkerhet. Mer information finns i [bevilja åtkomst till blobbar och köer med hjälp av Azure Active Directory](storage-auth-aad.md).

En signatur för delad åtkomst kan ha ett av två formulär:

- **Ad hoc SAS:** När du skapar en ad hoc SAS, anges start tiden, förfallo tiden och behörigheterna för SAS-URI: n (eller underförstådd, om start tiden utelämnas). Alla typer av SAS kan vara ad hoc SAS.
- **Tjänstens SAS med lagrad åtkomst princip:** En lagrad åtkomst princip definieras på en resurs behållare, som kan vara en BLOB-behållare, tabell, kö eller fil resurs. Den lagrade åtkomst principen kan användas för att hantera begränsningar för en eller flera signaturer för delad åtkomst för en tjänst. När du associerar en tjänst-SAS med en lagrad åtkomst princip ärver SAS begränsningarna &mdash; Start tiden, förfallo tiden och de behörigheter som &mdash; definierats för den lagrade åtkomst principen.

> [!NOTE]
> En användar Delegerings-SAS eller en konto säkerhets Association måste vara ad hoc SAS. Lagrade åtkomst principer stöds inte för användar Delegerings-SAS eller kontots SAS.

## <a name="how-a-shared-access-signature-works"></a>Så här fungerar en signatur för delad åtkomst

En signatur för delad åtkomst är en signerad URI som pekar på en eller flera lagrings resurser och som innehåller en token som innehåller en särskild uppsättning frågeparametrar. Token anger hur resurserna kan nås av klienten. En av frågeparametrar, signaturen, är konstruerad från SAS-parametrarna och är signerad med den nyckel som användes för att skapa SAS. Den här signaturen används av Azure Storage för att ge åtkomst till lagrings resursen.

### <a name="sas-signature-and-authorization"></a>SAS-signatur och-auktorisering

Du kan signera en SAS-token på något av två sätt:

- Med en *användar Delegerings nyckel* som skapades med Azure Active Directory (autentiseringsuppgifter för Azure AD). En användar Delegerings-SAS är signerad med användar Delegerings nyckeln.

    För att hämta användar Delegerings nyckeln och skapa SAS måste ett Azure AD-säkerhetsobjekt tilldelas en Azure-roll som innehåller åtgärden **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** . För detaljerad information om Azure-roller med behörighet att hämta användar Delegerings nyckeln, se [skapa en användar delegering SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas).

- Med lagrings konto nyckeln (delad nyckel). Både en tjänst-SAS och ett konto säkerhets associationer signeras med lagrings konto nyckeln. För att skapa en SAS som är signerad med konto nyckeln måste ett program ha åtkomst till konto nyckeln.

När en begäran innehåller en SAS-token, auktoriseras begäran baserat på hur SAS-token signeras. Den åtkomst nyckel eller de autentiseringsuppgifter som du använder för att skapa en SAS-token används också av Azure Storage för att ge åtkomst till en klient som innehar SAS.

I följande tabell sammanfattas hur varje typ av SAS-token auktoriseras när den tas med på en begäran till Azure Storage:

| Typ av SAS | Typ av auktorisering |
|-|-|
| Användar delegering SAS (endast blob-lagring) | Azure AD |
| Tjänstens SAS | Delad nyckel |
| Kontots SAS | Delad nyckel |

Microsoft rekommenderar att du använder en användar Delegerings-SAS när det är möjligt för överlägsen säkerhet.

### <a name="sas-token"></a>SAS-token

SAS-token är en sträng som du skapar på klient sidan, till exempel genom att använda en av klient biblioteken för Azure Storage. SAS-token spåras inte av Azure Storage på något sätt. Du kan skapa ett obegränsat antal SAS-token på klient sidan. När du har skapat en SAS kan du distribuera den till klient program som kräver åtkomst till resurser i ditt lagrings konto.

När ett klient program tillhandahåller en SAS-URI som Azure Storage som en del av en begäran, kontrollerar tjänsten SAS-parametrarna och signaturen för att kontrol lera att det är giltigt för auktorisering av begäran. Om tjänsten verifierar att signaturen är giltig är begäran auktoriserad. Annars nekas begäran med felkoden 403 (tillåts inte).

Här är ett exempel på en SAS-URI för tjänsten som visar resurs-URI och SAS-token:

![Komponenter i en SAS-URI för tjänsten](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>När du ska använda en signatur för delad åtkomst

Använd en SAS när du vill ge säker åtkomst till resurser i ditt lagrings konto till alla klienter som inte på annat sätt har behörighet till dessa resurser.

Ett vanligt scenario där en SAS är användbar är en tjänst där användare kan läsa och skriva egna data till ditt lagrings konto. I ett scenario där ett lagringskonto lagrar användardata finns det två typiska designmönster:

1. Klienter laddar upp och laddar ned en via en klientproxytjänst, som utför autentisering. Den här frontend-proxyservern har fördelen att tillåta validering av affärs regler, men för stora mängder data eller hög volym transaktioner kan du skapa en tjänst som kan skalas för att matcha efter frågan kan vara kostsam eller svår.

   ![Scenario diagram: klient delens proxy-tjänst](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. En förenklad tjänst autentiserar klienten efter behov och genererar sedan en SAS. När klient programmet tar emot SAS, kan de komma åt lagrings konto resurser direkt med behörigheterna som definieras av SAS och för det intervall som tillåts av SAS. SAS minskar behovet att dirigera alla data genom klientproxytjänsten.

   ![Scenario diagram: SAS-tjänstleverantör](./media/storage-sas-overview/sas-storage-provider-service.png)

Många verkliga tjänster kan använda en hybrid av dessa två metoder. Till exempel kan vissa data bearbetas och verifieras via frontend-proxyn, medan andra data sparas och/eller läses direkt med hjälp av SAS.

Dessutom krävs en SAS för att ge åtkomst till källobjektet i en kopierings åtgärd i vissa situationer:

- När du kopierar en blob till en annan blob som finns på ett annat lagrings konto måste du använda en SAS för att ge åtkomst till käll-bloben. Du kan också använda en SAS för att ge åtkomst till mål-bloben.
- När du kopierar en fil till en annan fil som finns i ett annat lagrings konto måste du använda en SAS för att ge åtkomst till käll filen. Du kan också använda en SAS för att auktorisera åtkomst till målfilen.
- När du kopierar en blob till en fil eller en fil till en BLOB måste du använda en SAS för att ge åtkomst till källobjektet, även om käll-och mål objekt finns på samma lagrings konto.

## <a name="best-practices-when-using-sas"></a>Metodtips när du använder SAS

När du använder signaturer för delad åtkomst i dina program måste du vara medveten om två potentiella risker:

- Om en SAS läcker ut kan den användas av alla som erhåller den, vilket kan skada ditt lagrings konto.
- Om en SAS som tillhandahålls för ett klient program upphör att gälla och programmet inte kan hämta en ny SAS från tjänsten, kan programmets funktioner hindras.

Följande rekommendationer för att använda signaturer för delad åtkomst kan minimera riskerna:

- **Använd alltid https** för att skapa eller distribuera en SAS. Om en säkerhets Association skickas över HTTP och fångas, kan en angripare som utför en man-in-the-Middle-attack läsa SAS och sedan använda den precis som den avsedda användaren kan ha, potentiellt kompromissa med känsliga data eller tillåta data skada av den skadliga användaren.
- **Använd en användar Delegerings-SAS när det är möjligt.** En användar Delegerings-SAS ger överlägsen säkerhet för en tjänst-SAS eller en konto säkerhets Association. En användar Delegerings-SAS skyddas av autentiseringsuppgifter för Azure AD, så att du inte behöver lagra din konto nyckel med din kod.
- **Ha en återkallnings plan för SAS.** Se till att du är för beredd att svara om en SAS komprometteras.
- **Definiera en lagrad åtkomst princip för en tjänst-SAS.** Lagrade åtkomst principer ger dig möjlighet att återkalla behörigheter för en tjänst-SAS utan att behöva återskapa lagrings konto nycklarna. Ange förfallo datum för dessa hittills i framtiden (eller oändligt) och kontrol lera att de uppdateras regelbundet för att flytta den längre fram i framtiden.
- **Använd tider med nära giltighets tider för en ad hoc SAS-tjänst SAS eller konto säkerhets associationer.** På det här sättet, även om en SAS har komprometterats, är den bara giltig under en kort tid. Den här metoden är särskilt viktig om du inte kan referera till en lagrad åtkomst princip. Snart kan förfallo tiden begränsa den mängd data som kan skrivas till en BLOB genom att begränsa den tillgängliga tiden att laddas upp till den.
- **Låt klienterna automatiskt förnya SAS vid behov.** Klienterna bör förnya SAS-vältiden innan det går ut, för att tillåta tid för nya försök om tjänsten som tillhandahåller SAS inte är tillgänglig. Om din SAS är avsedd att användas för ett litet antal omedelbara, kortsiktiga åtgärder som förväntas bli slutförda inom förfallo perioden, kan detta vara onödigt eftersom sa-värdet inte förväntas förnyas. Men om du har en klient som rutinmässigt begär förfrågningar via SAS, kommer möjligheten att förfalla att bli i spel. Viktiga överväganden är att balansera behovet av att SAS ska vara kort livs längd (som tidigare anges) med behovet av att säkerställa att klienten begär förnyelse tillräckligt tidigt (för att undvika avbrott på grund av att SAS förfaller före en lyckad förnyelse).
- **Var försiktig med start tiden för SAS.** Om du anger start tiden för en SAS till den aktuella tiden kan det hända att felen inträffar tillfälligt under de första minuterna på grund av olika datorer med små variationer för den aktuella tiden (kallas klock skevning). I allmänhet anger du Start tiden till minst 15 minuter tidigare. Eller, ange inte det alls, vilket gör det giltigt omedelbart i samtliga fall. Samma gäller vanligt vis för förfallo tid, och kom ihåg att du kan se upp till 15 minuters klock skevning i båda riktningarna. För klienter som använder en REST-version före 2012-02-12 är den maximala tiden för en SAS som inte refererar till en lagrad åtkomst princip 1 timme och alla principer som anger längre villkors period än vad som kommer att Miss förväntas.
- **Var försiktig med SAS-formatet för SAS.** Om du ställer in start tid och/eller förfallo datum för en SAS måste du ha datetime-formatet "+% Y-% m-% dT% H:%M:% SZ", särskilt inklusive sekunder för att det ska fungera med SAS-token för vissa verktyg (till exempel för kommando rads verktyget AzCopy).  
- **Var unik för den resurs som ska nås.** En säkerhets metod är att tillhandahålla en användare med den lägsta behörighet som krävs. Om en användare bara behöver Läs behörighet till en enskild entitet kan du ge dem Läs behörighet till den enskilda entiteten och inte läsa/skriva/ta bort åtkomst till alla entiteter. Detta hjälper också till att minska skadan om en SAS komprometteras eftersom SAS har mindre kraft i händerna på en angripare.
- **Förstå att ditt konto debiteras för all användning, inklusive via en SAS.** Om du ger skriv åtkomst till en BLOB kan en användare välja att ladda upp en 200 GB-blob. Om du har gett dem Läs behörighet kan de välja att ladda ned det 10 gånger, vilket ger 2 TB i utgående kostnader för dig. På nytt ger du begränsade behörigheter för att minska risken för att skadliga användare kan utföra åtgärder. Använd SAS med kort livs längd för att minska det här hotet (men var mindful klock skevning vid slut tiden).
- **Verifiera data som skrivits med en SAS.** När ett klient program skriver data till ditt lagrings konto bör du tänka på att det kan finnas problem med dessa data. Om ditt program kräver att data verifieras eller auktoriseras innan de kan användas, bör du utföra den här verifieringen när data har skrivits och innan de används av ditt program. Den här övningen skyddar också mot skadade eller skadliga data som skrivs till ditt konto, antingen av en användare som har förvärvat SAS eller av en användare som utnyttjar en läcka SAS.
- **Vet när du inte ska använda en SAS.** Ibland uppväger riskerna som är kopplade till en viss åtgärd mot ditt lagrings konto fördelarna med att använda en SAS. För sådana åtgärder skapar du en tjänst på mellan nivå som skriver till ditt lagrings konto när du har utfört verifiering, autentisering och granskning av affärs regler. Ibland är det också enklare att hantera åtkomst på andra sätt. Om du till exempel vill att alla blobbar i en behållare ska kunna läsas offentligt kan du göra behållaren offentlig i stället för att tillhandahålla en SAS för varje klient för åtkomst.
- **Använd Azure Monitor och Azure Storage loggar för att övervaka ditt program.** Du kan använda Azure Monitor-och Storage Analytics-loggning för att se all insamling vid auktoriseringsfel på grund av ett avbrott i SAS-providerns tjänst eller oavsiktlig borttagning av en lagrad åtkomst princip. Mer information finns i [Azure Storage mått i Azure Monitor](monitor-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) och [Azure-lagringsanalys loggning](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="get-started-with-sas"></a>Kom igång med SAS

Information om hur du kommer igång med signaturer för delad åtkomst finns i följande artiklar för varje SAS-typ.

### <a name="user-delegation-sas"></a>SAS för användar delegering

- [Skapa en användar Delegerings-SAS för en behållare eller BLOB med PowerShell](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Skapa en användar Delegerings-SAS för en behållare eller BLOB med Azure CLI](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Skapa en användar Delegerings-SAS för en behållare eller BLOB med .NET](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>Tjänstens SAS

- [Skapa en tjänst-SAS för en behållare eller BLOB med .NET](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>Kontots SAS

- [Skapa ett konto säkerhets associationer med .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Nästa steg

- [Delegera åtkomst med en signatur för delad åtkomst (REST API)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Skapa en användar Delegerings-SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Skapa en tjänst-SAS (REST API)](/rest/api/storageservices/create-service-sas)
- [Skapa ett konto SAS (REST API)](/rest/api/storageservices/create-account-sas)
