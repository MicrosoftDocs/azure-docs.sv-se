---
title: Ge begränsad åtkomst till data med signaturer för delad åtkomst (SAS)
titleSuffix: Azure Storage
description: Lär dig mer om att använda signaturer för delad åtkomst (SAS) för att delegera åtkomst till Azure Storage resurser, inklusive blobbar, köer, tabeller och filer.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/28/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 51e73222233602491b0c8ed3835d032610c68e0d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722794"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Bevilja begränsad åtkomst till Azure Storage resurser med signaturer för delad åtkomst (SAS)

En signatur för delad åtkomst (SAS) ger säker delegerad åtkomst till resurser i ditt lagrings konto. Med en SAS har du detaljerad kontroll över hur en klient kan komma åt dina data. Exempel:
 
- Vilka resurser som klienten kan komma åt.

- Vilka behörigheter de har till resurserna.

- Hur länge SAS är giltig.

## <a name="types-of-shared-access-signatures"></a>Typer av signaturer för delad åtkomst

Azure Storage stöder tre typer av signaturer för delad åtkomst:

- SAS för användar delegering

- Tjänstens SAS

- Kontots SAS

### <a name="user-delegation-sas"></a>SAS för användar delegering

En användar Delegerings-sa skyddas med Azure Active Directory (Azure AD)-autentiseringsuppgifter och även av de behörigheter som angetts för SAS. En användar Delegerings-SAS gäller endast för Blob Storage.

Mer information om sa för användar delegering finns i [skapa en användar delegering SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas).

### <a name="service-sas"></a>Tjänstens SAS

En tjänst-SAS skyddas med lagrings konto nyckeln. En tjänst-SAS delegerar åtkomst till en resurs i endast en av de Azure Storage tjänsterna: Blob Storage, Queue Storage, Table Storage eller Azure Files.

Mer information om tjänste-SAS finns i [skapa en tjänst SAS (REST API)](/rest/api/storageservices/create-service-sas).

### <a name="account-sas"></a>Kontots SAS

En konto säkerhets Association skyddas med lagrings konto nyckeln. En konto-SAS delegerar åtkomst till resurser i en eller flera av lagringstjänsterna. Alla åtgärder som är tillgängliga via en tjänst eller en användar Delegerings-SAS är också tillgängliga via en konto säkerhets Association. 

Du kan också delegera åtkomst till följande:

- Åtgärder på tjänst nivå (till exempel **egenskaperna get/set service** och **Get service stats** ). 

- Läs-, skriv-och borttagnings åtgärder som inte är tillåtna med en tjänst-SAS. 

Om du vill ha mer information om kontots SAS [skapar du ett konto SAS (REST API)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> Microsoft rekommenderar att du använder Azure AD-autentiseringsuppgifter när det är möjligt som en säkerhets åtgärd, i stället för att använda konto nyckeln, vilket kan vara enklare att avslöja. När program designen kräver delade åtkomst-signaturer för att få åtkomst till Blob Storage använder du autentiseringsuppgifter för Azure AD för att skapa en användar Delegerings-sa när det är möjligt för överlägsen säkerhet. Mer information finns i [bevilja åtkomst till blobbar och köer med hjälp av Azure Active Directory](storage-auth-aad.md).

En signatur för delad åtkomst kan ha något av följande två former:

- **Ad hoc SAS**. När du skapar en ad hoc SAS, anges start tiden, förfallo tiden och behörigheter i SAS-URI: n. Alla typer av SAS kan vara ad hoc SAS.

- **Tjänstens SAS med lagrad åtkomst princip**. En lagrad åtkomst princip definieras på en resurs behållare, som kan vara en BLOB-behållare, tabell, kö eller fil resurs. Den lagrade åtkomst principen kan användas för att hantera begränsningar för en eller flera signaturer för delad åtkomst för en tjänst. När du associerar en tjänst-SAS med en lagrad åtkomst princip ärver SAS begränsningarna &mdash; Start tiden, förfallo tiden och de behörigheter som &mdash; definierats för den lagrade åtkomst principen.

> [!NOTE]
> En användar Delegerings-SAS eller en konto säkerhets Association måste vara ad hoc SAS. Lagrade åtkomst principer stöds inte för användar Delegerings-SAS eller kontots SAS.

## <a name="how-a-shared-access-signature-works"></a>Så här fungerar en signatur för delad åtkomst

En signatur för delad åtkomst är en signerad URI som pekar på en eller flera lagrings resurser. URI: n innehåller en token som innehåller en speciell uppsättning frågeparametrar. Token anger hur resurserna kan nås av klienten. En av frågeparametrar, signaturen, är konstruerad från SAS-parametrarna och är signerad med den nyckel som användes för att skapa SAS. Den här signaturen används av Azure Storage för att ge åtkomst till lagrings resursen.

> [!NOTE]
> Det går inte att granska genereringen av SAS-token. Alla användare som har behörighet att skapa en SAS-token, antingen genom att använda konto nyckeln eller via en Azure RBAC-roll tilldelning, kan göra det utan att ha kunskap om lagrings kontots ägare. Var noga med att begränsa behörigheter som tillåter användare att generera SAS-token. För att hindra användare från att skapa en SAS som är signerad med konto nyckeln för blob-och kö-arbetsbelastningar, kan du förhindra åtkomst till lagrings kontot för delad nyckel. Mer information finns i [förhindra auktorisering med delad nyckel](shared-key-authorization-prevent.md).

### <a name="sas-signature-and-authorization"></a>SAS-signatur och-auktorisering

Du kan signera en SAS-token med en användar Delegerings nyckel eller med en lagrings konto nyckel (delad nyckel). 

#### <a name="signing-a-sas-token-with-a-user-delegation-key"></a>Signera en SAS-token med en användar Delegerings nyckel

Du kan signera en SAS-token med hjälp av en *användar Delegerings nyckel* som skapades med Azure Active Directory (autentiseringsuppgifter för Azure AD). En användar Delegerings-SAS är signerad med användar Delegerings nyckeln.

För att hämta nyckeln och sedan skapa SAS måste ett Azure AD-säkerhetsobjekt tilldelas en Azure-roll som innehåller `Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey` åtgärden. Mer information finns i [skapa en användar delegering SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas).

#### <a name="signing-a-sas-token-with-an-account-key"></a>Signera en SAS-token med en konto nyckel

Både en tjänst-SAS och ett konto säkerhets associationer signeras med lagrings konto nyckeln. För att skapa en SAS som är signerad med konto nyckeln måste ett program ha åtkomst till konto nyckeln.

När en begäran innehåller en SAS-token, auktoriseras begäran baserat på hur SAS-token signeras. Den åtkomst nyckel eller de autentiseringsuppgifter som du använder för att skapa en SAS-token används också av Azure Storage för att ge åtkomst till en klient som innehar SAS.

I följande tabell sammanfattas hur varje typ av SAS-token är auktoriserad.

| Typ av SAS | Typ av auktorisering |
|-|-|
| Användar delegering SAS (endast blob-lagring) | Azure AD |
| Tjänstens SAS | Delad nyckel |
| Kontots SAS | Delad nyckel |

Microsoft rekommenderar att du använder en användar Delegerings-SAS när det är möjligt för överlägsen säkerhet.

### <a name="sas-token"></a>SAS-token

SAS-token är en sträng som du skapar på klient sidan, till exempel genom att använda en av klient biblioteken för Azure Storage. SAS-token spåras inte av Azure Storage på något sätt. Du kan skapa ett obegränsat antal SAS-token på klient sidan. När du har skapat en SAS kan du distribuera den till klient program som kräver åtkomst till resurser i ditt lagrings konto.

Klient program ger SAS-URI: n till Azure Storage som en del av en begäran. Sedan kontrollerar tjänsten SAS-parametrarna och signaturen för att kontrol lera att den är giltig. Om tjänsten verifierar att signaturen är giltig är begäran auktoriserad. Annars nekas begäran med felkoden 403 (tillåts inte).

Här är ett exempel på en SAS-URI för tjänsten som visar resurs-URI och SAS-token. Eftersom SAS-token omfattar URI-frågesträngen måste resurs-URI: n följas först av ett frågetecken och sedan av SAS-token:

![Komponenter i en SAS-URI för tjänsten](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>När du ska använda en signatur för delad åtkomst

Använd en SAS för att ge säker åtkomst till resurser i ditt lagrings konto till alla klienter som inte på annat sätt har behörighet till dessa resurser.

Ett vanligt scenario där en SAS är användbar är en tjänst där användare kan läsa och skriva egna data till ditt lagrings konto. I ett scenario där ett lagringskonto lagrar användardata finns det två typiska designmönster:

1. Klienter laddar upp och laddar ned en via en klientproxytjänst, som utför autentisering. Den här frontend-proxyservern tillåter validering av affärs regler. Men för stora mängder data eller hög volym transaktioner kan det vara dyrt eller svårt att skapa en tjänst som kan skalas för att matcha efter frågan.

   ![Scenario diagram: klient delens proxy-tjänst](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

2. En förenklad tjänst autentiserar klienten efter behov och genererar sedan en SAS. När klient programmet tar emot SAS, kan det komma åt lagrings konto resurser direkt. Åtkomst behörigheter definieras av SAS och för det intervall som tillåts av SAS. SAS minskar behovet att dirigera alla data genom klientproxytjänsten.

   ![Scenario diagram: SAS-tjänstleverantör](./media/storage-sas-overview/sas-storage-provider-service.png)

Många verkliga tjänster kan använda en hybrid av dessa två metoder. Till exempel kan vissa data bearbetas och verifieras via frontend-proxyservern. Andra data sparas och/eller läses direkt med hjälp av SAS.

Dessutom krävs en SAS för att ge åtkomst till källobjektet i en kopierings åtgärd i vissa situationer:

- När du kopierar en blob till en annan blob som finns i ett annat lagrings konto. 
  
  Du kan också använda en SAS för att ge åtkomst till mål-bloben.

- När du kopierar en fil till en annan fil som finns i ett annat lagrings konto. 

  Du kan också använda en SAS för att auktorisera åtkomst till målfilen.

- När du kopierar en blob till en fil eller en fil till en blob. 

  Du måste använda en SAS även om käll-och mål objekt finns i samma lagrings konto.

## <a name="best-practices-when-using-sas"></a>Metodtips när du använder SAS

När du använder signaturer för delad åtkomst i dina program måste du vara medveten om två potentiella risker:

- Om en SAS läcker ut kan den användas av alla som erhåller den, vilket kan skada ditt lagrings konto.

- Om en SAS som tillhandahålls för ett klient program upphör att gälla och programmet inte kan hämta en ny SAS från tjänsten, kan programmets funktioner hindras.

Följande rekommendationer för att använda signaturer för delad åtkomst kan minimera riskerna:

- **Använd alltid https** för att skapa eller distribuera en SAS. Om en SAS skickas över HTTP och fångas, kan en angripare som utför en person-i-ett-mellan-angrepp läsa SAS. Sedan kan de använda den SAS-datorn precis som den avsedda användaren kan ha. Detta kan potentiellt kompromettera känsliga data eller tillåta data skada av den skadliga användaren.

- **Använd en användar Delegerings-SAS när det är möjligt.** En användar Delegerings-SAS ger överlägsen säkerhet för en tjänst-SAS eller en konto säkerhets Association. En användar Delegerings-SAS skyddas av autentiseringsuppgifter för Azure AD, så att du inte behöver lagra din konto nyckel med din kod.

- **Ha en återkallnings plan för SAS.** Se till att du är för beredd att svara om en SAS komprometteras.

- **Definiera en lagrad åtkomst princip för en tjänst-SAS.** Lagrade åtkomst principer ger dig möjlighet att återkalla behörigheter för en tjänst-SAS utan att behöva återskapa lagrings konto nycklarna. Ange förfallo datum för dessa hittills i framtiden (eller oändligt) och kontrol lera att de uppdateras regelbundet för att flytta den längre fram i framtiden.

- **Använd tider med nära giltighets tider för en ad hoc SAS-tjänst SAS eller konto säkerhets associationer.** På det här sättet, även om en SAS har komprometterats, är den bara giltig under en kort tid. Den här metoden är särskilt viktig om du inte kan referera till en lagrad åtkomst princip. Snart kan förfallo tiden begränsa den mängd data som kan skrivas till en BLOB genom att begränsa den tillgängliga tiden att laddas upp till den.

- **Låt klienterna automatiskt förnya SAS vid behov.** Klienterna bör förnya SAS-vältiden innan det går ut, för att tillåta tid för nya försök om tjänsten som tillhandahåller SAS inte är tillgänglig. Detta kan vara onödigt i vissa fall. Till exempel kanske du planerar att SAS ska användas för ett litet antal omedelbara, kortsiktiga åtgärder. De här åtgärderna förväntas vara slutförda inom utgångs perioden. Det innebär att du inte förväntar dig att säkerhets associationen ska förnyas. Men om du har en klient som rutinmässigt utför begär Anden via SAS, kommer möjligheten att upphöra att gälla bli i spel. 

- **Var försiktig med start tiden för SAS.** Om du anger start tiden för en SAS till den aktuella tiden kan fel inträffa tillfälligt under de första minuterna. Detta beror på att olika datorer har något olika aktuella tider (kallas klock skevning). I allmänhet anger du Start tiden till minst 15 minuter tidigare. Eller, ange inte det alls, vilket gör det giltigt omedelbart i samtliga fall. Samma gäller vanligt vis för förfallo tid, och kom ihåg att du kan se upp till 15 minuters klock skevning i båda riktningarna. För klienter som använder en REST-version före 2012-02-12 är den maximala tiden för en SAS som inte refererar till en lagrad åtkomst princip 1 timme. Alla principer som anger en längre tid än 1 timme kommer att Miss missrar.

- **Var försiktig med SAS-formatet för SAS.** För vissa verktyg (t. ex. AzCopy) behöver du datetime-format som "+% Y-% m-% dT% H:%M:% SZ". Det här formatet omfattar särskilt sekunderna. 

- **Var unik för den resurs som ska nås.** En säkerhets metod är att tillhandahålla en användare med den lägsta behörighet som krävs. Om en användare bara behöver Läs behörighet till en enskild entitet kan du ge dem Läs behörighet till den enskilda entiteten och inte läsa/skriva/ta bort åtkomst till alla entiteter. Detta hjälper också till att minska skadan om en SAS komprometteras eftersom SAS har mindre kraft i händerna på en angripare.

- **Förstå att ditt konto debiteras för all användning, inklusive via en SAS.** Om du ger skriv åtkomst till en BLOB kan en användare välja att ladda upp en 200 GB-blob. Om du har gett dem Läs behörighet kan de välja att ladda ned det 10 gånger, vilket ger 2 TB i utgående kostnader för dig. På nytt ger du begränsade behörigheter för att minska risken för att skadliga användare kan utföra åtgärder. Använd SAS med kort livs längd för att minska det här hotet (men var mindful klock skevning vid slut tiden).

- **Verifiera data som skrivits med en SAS.** När ett klient program skriver data till ditt lagrings konto bör du tänka på att det kan finnas problem med dessa data. Om du planerar att validera data utför du den verifieringen när data har skrivits och innan de används av ditt program. Den här övningen skyddar också mot skadade eller skadliga data som skrivs till ditt konto, antingen av en användare som har förvärvat SAS eller av en användare som utnyttjar en läcka SAS.

- **Vet när du inte ska använda en SAS.** Ibland uppväger riskerna som är kopplade till en viss åtgärd mot ditt lagrings konto fördelarna med att använda en SAS. För sådana åtgärder skapar du en tjänst på mellan nivå som skriver till ditt lagrings konto när du har utfört verifiering, autentisering och granskning av affärs regler. Ibland är det också enklare att hantera åtkomst på andra sätt. Om du till exempel vill att alla blobbar i en behållare ska kunna läsas offentligt kan du göra behållaren offentlig i stället för att tillhandahålla en SAS för varje klient för åtkomst.

- **Använd Azure Monitor och Azure Storage loggar för att övervaka ditt program.** Auktoriseringsfel kan uppstå på grund av ett avbrott i SAS-providern. De kan också utföras från en oavsiktlig borttagning av en lagrad åtkomst princip. Du kan använda Azure Monitor-och Storage Analytics-loggning för att se insamling i dessa typer av auktoriseringsfel. Mer information finns i [Azure Storage mått i Azure Monitor](../blobs/monitor-blob-storage.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json) och [Azure-lagringsanalys loggning](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Lagringen spårar inte antalet signaturer för delad åtkomst som har genererats för ett lagrings konto och inget API kan tillhandahålla denna information. Om du behöver veta antalet signaturer för delad åtkomst som har genererats för ett lagrings konto måste du spåra numret manuellt.

## <a name="get-started-with-sas"></a>Kom igång med SAS

Information om hur du kommer igång med signaturer för delad åtkomst finns i följande artiklar för varje SAS-typ.

### <a name="user-delegation-sas"></a>SAS för användar delegering

- [Skapa en användar Delegerings-SAS för en behållare eller BLOB med PowerShell](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Skapa en användar Delegerings-SAS för en behållare eller BLOB med Azure CLI](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Skapa en användar Delegerings-SAS för en behållare eller BLOB med .NET](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>Tjänstens SAS

- [Skapa en tjänst-SAS för en behållare eller BLOB med .NET](../blobs/sas-service-create.md)

### <a name="account-sas"></a>Kontots SAS

- [Skapa ett konto säkerhets associationer med .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Nästa steg

- [Delegera åtkomst med en signatur för delad åtkomst (REST API)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Skapa en användar Delegerings-SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Skapa en tjänst-SAS (REST API)](/rest/api/storageservices/create-service-sas)
- [Skapa ett konto SAS (REST API)](/rest/api/storageservices/create-account-sas)
