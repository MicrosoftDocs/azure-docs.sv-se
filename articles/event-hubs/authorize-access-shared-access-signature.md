---
title: Ge åtkomst till en signatur för delad åtkomst i Azure Event Hubs
description: Den här artikeln innehåller information om hur du auktoriserar åtkomst till Azure Event Hubs-resurser med hjälp av signaturer för delad åtkomst (SAS).
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 6a2d7385f82864e8d378055333377fb9c3f73c19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85323122"
---
# <a name="authorizing-access-to-event-hubs-resources-using-shared-access-signatures"></a>Auktorisera åtkomst till Event Hubs resurser med signaturer för delad åtkomst
En signatur för delad åtkomst (SAS) ger dig ett sätt att bevilja begränsad åtkomst till resurser i din Event Hubs-namnrymd. SAS skyddar åtkomsten till Event Hubs resurser baserat på auktoriseringsregler. Dessa regler konfigureras antingen i ett namn område eller i en entitet (händelsehubben eller ett ämne). Den här artikeln innehåller en översikt över SAS-modellen och granskar bästa praxis för SAS.

## <a name="what-are-shared-access-signatures"></a>Vad är signaturer för delad åtkomst?
En signatur för delad åtkomst (SAS) ger delegerad åtkomst till Event Hubs resurser baserat på auktoriseringsregler. En auktoriseringsregel har ett namn, är kopplat till vissa rättigheter och har ett par kryptografiska nycklar. Du använder regelns namn och nyckel via Event Hubs klienter eller i din egen kod för att generera SAS-token. En klient kan sedan skicka token till Event Hubs för att bevisa auktoriseringen för den begärda åtgärden.

SAS är en anspråksbaserad mekanism för auktorisering med hjälp av enkla tokens. Med SAS skickas aldrig nycklar i kabeln. Nycklar används för att signera information kryptografiskt som senare kan verifieras av tjänsten. SAS kan användas på samma sätt som användar namn och lösen ord, där klienten har omedelbar till gång till ett namn för auktoriseringsregeln och en matchande nyckel. SAS kan användas på samma sätt som en federerad säkerhets modell, där klienten får en tidsbegränsad och signerad åtkomsttoken från en säkerhetstokentjänst utan att någonsin ha fått till gång till signerings nyckeln.

> [!NOTE]
> Azure Event Hubs stöder auktorisering till Event Hubs resurser med hjälp av Azure Active Directory (Azure AD). Auktorisering av användare eller program med OAuth 2,0-token som returnerades av Azure AD ger överlägsen säkerhet och enkel användning över signaturer för delad åtkomst (SAS). Med Azure AD behöver du inte lagra tokens i din kod och potentiella säkerhets risker.
>
> Microsoft rekommenderar att du använder Azure AD med dina Azure Event Hubs-program när det är möjligt. Mer information finns i [ge åtkomst till Azure Event Hubs-resurser med hjälp av Azure Active Directory](authorize-access-azure-active-directory.md).

> [!IMPORTANT]
> SAS-token (Shared Access Signatures) är viktiga för att skydda dina resurser. När du tillhandahåller granularitet ger SAS klienterna åtkomst till dina Event Hubs-resurser. De bör inte delas offentligt. När du delar, om det behövs för fel söknings skäl, bör du överväga att använda en lägre version av loggfiler eller ta bort SAS-token (om de finns) från loggfilerna, och se till att skärm bilderna inte innehåller SAS-informationen.

## <a name="shared-access-authorization-policies"></a>Auktoriseringsprinciper för delad åtkomst
Varje Event Hubs-namnrymd och varje Event Hubs entitet (en Event Hub-instans eller ett Kafka-ämne) har en princip för delad åtkomst som består av regler. Principen på namn områdes nivån gäller för alla entiteter i namn området, oavsett deras enskilda princip konfiguration.
För varje auktoriseringsregel bestämmer du om tre delar av information: namn, omfattning och rättigheter. Namnet är ett unikt namn i det omfånget. Omfånget är URI: n för den aktuella resursen. För ett Event Hubs-namnområde är omfånget det fullständigt kvalificerade domän namnet (FQDN), till exempel `https://<yournamespace>.servicebus.windows.net/` .

Rättigheterna som anges i princip regeln kan vara en kombination av:
- **Skicka** – ger behörighet att skicka meddelanden till entiteten
- **Lyssna** – ger behörighet att lyssna eller ta emot till entiteten
- **Hantera** – ger behörighet att hantera topologin för namn området, inklusive att skapa och ta bort entiteter

> [!NOTE]
> Rättigheten **Hantera** innehåller behörigheterna **Skicka** och **Lyssna** .

En namnrymd eller en enhets princip kan innehålla upp till 12 regler för delad åtkomst, vilket ger plats för de tre regel uppsättningarna, var och en täcker de grundläggande rättigheterna och kombinationen av skicka och lyssna. Den här gränsen visar att SAS-principinställningar inte är avsedd att vara ett användar-eller tjänst konto arkiv. Om ditt program behöver bevilja åtkomst till Event Hubs resurser baserat på användar-eller tjänst identiteter bör det implementera en säkerhetstokentjänst som utfärdar SAS-token efter en autentisering och åtkomst kontroll.

En auktoriseringsregel tilldelas en **primär nyckel** och en **sekundär nyckel**. Nycklarna är kryptografiskt starka nycklar. Tappa inte bort dem eller läcker dem. De är alltid tillgängliga i Azure Portal. Du kan använda någon av de genererade nycklarna och du kan återskapa dem när du vill. Om du återskapar eller ändrar en nyckel i principen blir alla tidigare utfärdade token baserade på den nyckeln omedelbart ogiltiga. Pågående anslutningar som skapas baserat på sådana tokens fortsätter dock att fungera tills token upphör att gälla.

När du skapar ett Event Hubs-namnområde skapas automatiskt en princip regel med namnet **RootManageSharedAccessKey** för namn området. Den här principen har behörighet att **Hantera** hela namn området. Vi rekommenderar att du behandlar den här regeln som ett administratörs rot konto och inte använder den i ditt program. Du kan skapa ytterligare princip regler på fliken **Konfigurera** för namn området i portalen via PowerShell eller Azure CLI.

## <a name="best-practices-when-using-sas"></a>Metodtips när du använder SAS
När du använder signaturer för delad åtkomst i dina program måste du vara medveten om två potentiella risker:

- Om en SAS läcker ut kan den användas av alla som erhåller den, vilket kan äventyra Event Hubs-resurser.
- Om en SAS som tillhandahålls för ett klient program upphör att gälla och programmet inte kan hämta en ny SAS från tjänsten, kan programmets funktioner hindras.

Följande rekommendationer för att använda signaturer för delad åtkomst kan minimera riskerna:

- **Låt klienterna automatiskt förnya SAS vid behov**: klienterna bör förnya SAS-välbefinnande innan det går ut, för att tillåta tid för nya försök om tjänsten som tillhandahåller SAS inte är tillgänglig. Om din SAS är avsedd att användas för ett litet antal omedelbara, kortsiktiga åtgärder som förväntas bli slutförda inom förfallo perioden, kan det vara onödigt eftersom SAS inte förväntas förnyas. Men om du har en klient som rutinmässigt begär förfrågningar via SAS, kommer möjligheten att förfalla att bli i spel. Viktiga överväganden är att balansera behovet av att SAS ska vara kort livs längd (som tidigare anges) med behovet av att säkerställa att klienten begär förnyelse tillräckligt tidigt (för att undvika avbrott på grund av att SAS förfaller före en lyckad förnyelse).
- **Var försiktig med start tiden för SAS**: om du ställer in start tiden för SAS **nu**, sedan på grund av klock skevning (skillnader i aktuell tid beroende på olika datorer), kan det hända att felen observeras oregelbundet under de första minuterna. I allmänhet anger du Start tiden till minst 15 minuter tidigare. Eller, ange inte det alls, vilket gör det giltigt omedelbart i samtliga fall. Samma gäller vanligt vis förfallo tiden. Kom ihåg att du kan titta upp till 15 minuters klock skevning i båda riktningarna. 
- **Var speciell för den resurs som ska användas**: en säkerhets metod är att ge användaren den lägsta behörighet som krävs. Om en användare bara behöver Läs behörighet till en enskild entitet kan du ge dem Läs behörighet till den enskilda entiteten och inte läsa/skriva/ta bort åtkomst till alla entiteter. Det hjälper också till att minska skadan om en SAS komprometteras eftersom SAS har mindre kraft i händerna på en angripare.
- **Använd inte alltid SAS**: ibland uppväger riskerna som är kopplade till en viss åtgärd mot ditt Event Hubs fördelarna med SAS. För sådana åtgärder skapar du en tjänst på mellan nivå som skriver till din Event Hubs efter verifiering, autentisering och granskning av affärs regler.
- **Använd alltid https**: Använd alltid https för att skapa eller distribuera en SAS. Om en säkerhets Association skickas över HTTP och fångas, kan en angripare som utför en anslutning som gör att du kan läsa SAS och sedan använda den precis som den avsedda användaren kan ha, eventuellt kompromissa med känsliga data eller tillåta data skada av den skadliga användaren.

## <a name="conclusion"></a>Slutsats
Dela Access-signaturer är användbara för att ge begränsade behörigheter för att Event Hubs resurser till dina klienter. De är viktiga delar av säkerhets modellen för alla program som använder Azure Event Hubs. Om du följer de metod tips som anges i den här artikeln kan du använda SAS för att ge större flexibilitet vid åtkomst till dina resurser, utan att kompromissa med säkerheten för ditt program.

## <a name="next-steps"></a>Nästa steg
Se följande relaterade artiklar: 

- [Autentisera begär anden till Azure Event Hubs från ett program med Azure Active Directory](authenticate-application.md)
- [Autentisera en hanterad identitet med Azure Active Directory för att få åtkomst till Event Hubs resurser](authenticate-managed-identity.md)
- [Autentisera begär anden till Azure Event Hubs med signaturer för delad åtkomst](authenticate-shared-access-signature.md)
- [Ge åtkomst till Event Hubs resurser med Azure Active Directory](authorize-access-azure-active-directory.md)


