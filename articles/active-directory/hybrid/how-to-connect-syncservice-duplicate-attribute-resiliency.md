---
title: Återhämtning av Identitetssynkronisering och duplicerat attribut | Microsoft Docs
description: Nytt beteende för hantering av objekt med UPN-eller ProxyAddress-konflikter under katalog synkronisering med Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09dd6a127bd04ae698cb6cad2ffd7f35e3b51c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94413436"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Identitetssynkronisering och duplicerad attributåterhämtning
Återhämtning av duplicerat attribut är en funktion i Azure Active Directory som eliminerar friktion som orsakas av **userPrincipalName** och SMTP- **proxyAddress** konflikter vid körning av ett av Microsofts verktyg för synkronisering.

Dessa två attribut krävs vanligt vis för att vara unika för alla **användare**, **grupper** eller **kontakt** objekt i en specifik Azure Active Directory klient.

> [!NOTE]
> Endast användare kan ha UPN-användare.
> 
> 

Det nya beteende som den här funktionen möjliggör är i moln delen av den synkroniserade pipelinen, och därför är den klient oberoende och relevant för alla Microsoft-synkroniseringsanvändare, inklusive Azure AD Connect, DirSync och MIM + Connector. Den generiska termen "synkronisera klient" används i det här dokumentet för att representera någon av dessa produkter.

## <a name="current-behavior"></a>Aktuellt beteende
Om det finns ett försök att etablera ett nytt objekt med ett UPN-eller ProxyAddress-värde som strider mot detta unikhetsvillkor, Azure Active Directory blockerar det objektet från att skapas. På samma sätt Miss lyckas uppdateringen om ett objekt uppdateras med ett UPN-eller ProxyAddress som inte är unikt. Etablerings försöket eller uppdateringen görs om av synkroniseringsklienten vid varje export cykel och fortsätter att Miss lyckas tills konflikten har lösts. Ett e-postmeddelande med fel rapporter genereras vid varje försök och ett fel loggas av synkroniseringsklienten.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Beteende med duplicerat attribut återhämtning
I stället för att helt sluta att etablera eller uppdatera ett objekt med ett duplicerat attribut Azure Active Directory "karantäner" det duplicerade attributet som skulle bryta mot unikhetsvillkoret. Om det här attributet krävs för etablering, till exempel UserPrincipalName, tilldelar tjänsten ett plats hållare-värde. Formatet för dessa tillfälliga värden är  
_**\<OriginalPrefix> + \<4DigitNumber> \@ \<InitialTenantDomain> . onmicrosoft.com**_.

Återhämtnings processen för attribut hanterar endast UPN-och SMTP- **proxyAddress** -värden.

Om attributet inte krävs, t. ex. en  **proxyAddress**, kommer Azure Active Directory att helt enkelt placera det konfliktskapande attributet och fortsätter med att skapa eller uppdatera objektet.

Vid sätta av attributet skickas information om konflikten i samma fel rapport-e-postmeddelande som används i det gamla beteendet. Den här informationen visas dock bara i fel rapporten en gång när karantänen inträffar, men den fortsätter inte att loggas i framtida e-postmeddelanden. Eftersom exporten för det här objektet har slutförts, loggar inte synkroniseringsklienten ett fel och försöker inte att skapa/uppdatera igen vid efterföljande synkroniseringar.

För att stödja det här beteendet har ett nytt attribut lagts till i klasserna användare, grupp och kontakt objekt:  
**DirSyncProvisioningErrors**

Detta är ett flervärdesattribut som används för att lagra de motstridiga attribut som skulle bryta mot unikhetsvillkoret om de läggs till normalt. En timer för bakgrunden har Aktiver ATS i Azure Active Directory som körs varje timme för att leta efter dubbletter av konflikter som har lösts och automatiskt tar bort attributen i fråga från karantänen.

### <a name="enabling-duplicate-attribute-resiliency"></a>Aktivera duplicerat attribut återhämtning
Återhämtning av duplicerat attribut är det nya standard beteendet för alla Azure Active Directory klienter. Den är aktive rad som standard för alla klienter som aktiverade synkronisering för första gången den 22 augusti 2016 eller senare. Klienter som aktiverade synkronisering före detta datum kommer att ha funktionen aktive rad i batchar. Den här distributionen börjar i september 2016 och ett e-postmeddelande skickas till varje innehavares tekniska meddelande kontakt med det angivna datumet när funktionen ska aktive ras.

> [!NOTE]
> När duplicerat attribut har återställts kan det inte inaktive ras.

Du kan kontrol lera om funktionen är aktive rad för din klient genom att ladda ned den senaste versionen av Azure Active Directory PowerShell-modulen och köra:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Du kan inte längre använda Set-MsolDirSyncFeature cmdlet för att aktivera återhämtnings funktionen duplicerat attribut innan den är aktive rad för din klient. För att kunna testa funktionen måste du skapa en ny Azure Active Directory klient.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identifiera objekt med DirSyncProvisioningErrors
Det finns för närvarande två metoder för att identifiera objekt som har dessa fel på grund av duplicerade egenskaps konflikter, Azure Active Directory PowerShell och [Microsoft 365 administrations Center](https://admin.microsoft.com). Det finns planer på att utöka till ytterligare Portal baserad rapportering i framtiden.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
För PowerShell-cmdlets i det här avsnittet är följande sant:

* Alla följande cmdlet: ar är Skift läges känsliga.
* - **ErrorCategory-PropertyConflict** måste alltid inkluderas. Det finns för närvarande inga andra typer av **ErrorCategory**, men det kan utökas i framtiden.

Kom först igång genom att köra **Connect-MSOLService** och ange autentiseringsuppgifter för en klient administratör.

Använd sedan följande cmdlets och operatorer för att visa fel på olika sätt:

1. [Visa alla](#see-all)
2. [Efter egenskaps typ](#by-property-type)
3. [Efter motstridigt värde](#by-conflicting-value)
4. [Använda en strängs ökning](#using-a-string-search)
5. Sorted
6. [I en begränsad kvantitet eller alla](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Visa alla
När du är ansluten kan du se en allmän lista med konfigurations fel för attribut i klient körningen:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Detta resulterar i ett resultat som följande:  
 ![Get-MsolDirSyncProvisioningError](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Efter egenskaps typ
Om du vill visa fel efter egenskaps typ lägger du till flaggan **-PropertyName** med argumentet **userPrincipalName** eller **proxyAddresses** :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Eller

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Efter motstridigt värde
Om du vill se fel som rör en speciell egenskap lägger du till flaggan **-PropertyValue** (**-PropertyName** måste användas också när du lägger till den här flaggan):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Använda en strängs ökning
Om du vill göra en bred Strängs sökning använder du flaggan **-searchString** . Detta kan användas oberoende av alla flaggorna ovan, med undantag för **-ErrorCategory PropertyConflict**, vilket alltid krävs:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>I en begränsad kvantitet eller alla
1. **MaxResults \<Int>** kan användas för att begränsa frågan till ett angivet antal värden.
2. **Alla** kan användas för att se till att alla resultat hämtas i händelse av att det finns ett stort antal fel.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="microsoft-365-admin-center"></a>Administrationscenter för Microsoft 365
Du kan visa synkroniseringsfel i Microsoft 365 administrations centret. Rapporten i Microsoft 365 administrations Center visar bara **användar** objekt som innehåller dessa fel. Den visar inte information om konflikter mellan **grupper** och **kontakter**.

![Skärm bild som visar fel i katalogens synkroniseringsfel i Microsoft 365 administrations centret.](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1234.png "Aktiva användare")

Instruktioner för hur du visar synkroniseringsfel i Microsoft 365 administrations Center finns i [identifiera katalogs synkroniseringsfel i Microsoft 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Identitetssynkronisering fel rapport
När ett objekt med en duplicerad attribut-konflikt hanteras med den här nya funktionen inkluderas en avisering i e-postmeddelandet för standard-ID-synkronisering som skickas till den tekniska meddelande kontakten för klient organisationen. Det finns dock en viktig förändring i detta beteende. Tidigare skulle information om en konflikt i ett duplicerat attribut inkluderas i varje efterföljande fel rapport tills konflikten har lösts. Med det här nya beteendet visas fel meddelandet för en specifik konflikt bara en gång – vid tidpunkten då det motstridiga attributet är i karantän.

Här är ett exempel på hur e-postmeddelandet ser ut för en ProxyAddress-konflikt:  
    ![Skärm bild som visar ett exempel på ett e-postmeddelande om en ProxyAddress-konflikt.](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Aktiva användare")  

## <a name="resolving-conflicts"></a>Lösa konflikter
Fel sökning av strategi-och lösnings taktiker för dessa fel bör inte skilja sig från hur duplicerade attributändringar hanterades tidigare. Den enda skillnaden är att timer-aktiviteten sveper genom klienten på tjänst sidan för att automatiskt lägga till attributet i fråga till rätt objekt när konflikten har lösts.

Följande artikel beskriver olika fel söknings-och lösnings strategier: [dubbletter eller ogiltiga attribut förhindrar katalog synkronisering i Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Kända problem
Inga av dessa kända problem medför data förlust eller tjänst försämring. Flera av dem är attributnamn, andra orsakar standard "*för återhämtning*" av duplicerade attributändringar som ska utlösas i stället för att sätta i det orsakade attributet, och ett annat leder till att vissa fel kräver extra Manuell korrigering.

**Core-beteende:**

1. Objekt med Specific Attribute-konfigurationer fortsätter att ta emot export fel i stället för de duplicerade attributen i karantän.  
   Exempel:
   
    a. En ny användare skapas i AD med ett UPN för **Joe \@ contoso.com** och proxyAddress **SMTP: Johan \@ contoso.com**
   
    b. Egenskaperna för det här objektet är i konflikt med en befintlig grupp, där ProxyAddress är **SMTP: johan \@ contoso.com**.
   
    c. Vid export genereras ett **konflikt** fel i proxyAddress i stället för att de hamnar i konflikt i karantän. Åtgärden provas på nytt vid varje efterföljande synkronisering, eftersom den skulle ha varit innan återhämtnings funktionen var aktive rad.
2. Om två grupper skapas lokalt med samma SMTP-adress, går det inte att etablera vid det första försöket med ett duplicerat standard **proxyAddress** -fel. Det duplicerade värdet är dock i karantän vid nästa synkronisering.

**Office-Portal-rapport**:

1. Det detaljerade fel meddelandet för två objekt i en UPN-konflikt har angetts. Detta anger att de båda har ändrat UPN-värde/i karantän, om bara en av dem hade ändrats.
2. Det detaljerade fel meddelandet för en UPN-konflikt visar fel displayName för en användare som har ändrat UPN-namnet/i karantän. Exempel:
   
    a. **Användare A** synkroniseras först med **UPN = User \@ contoso.com**.
   
    b. **Användaren B** försökte synkroniseras härnäst med **UPN = User \@ contoso.com**.
   
    c. **Användare b:s** UPN har ändrats till **User1234 \@ contoso.onmicrosoft.com** och **användar \@ contoso.com** läggs till i **DirSyncProvisioningErrors**.
   
    d. Fel meddelandet för **användare B** bör ange att **användaren** redan har **\@ contoso.com** som ett UPN, men det visar **användarens** eget DisplayName.

**Identitetssynkronisering fel rapport**:

Länken för *steg om hur du löser det här problemet* är felaktigt:  
    ![Aktiva användare](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Aktiva användare")  

Den ska peka på [https://aka.ms/duplicateattributeresiliency]() .

## <a name="see-also"></a>Se även
* [Azure AD Connect synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
* [Identifiera fel i Active Directory-synkronisering i Microsoft 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)