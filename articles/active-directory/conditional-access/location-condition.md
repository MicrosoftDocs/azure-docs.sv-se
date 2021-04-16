---
title: Platsvillkor i Azure Active Directory villkorlig åtkomst
description: Lär dig hur du använder platsvillkoret för att styra åtkomsten till dina molnappar baserat på en användares nätverksplats.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 8b46ca16fc32a7b96c071a745f49bf5d5557f34b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530223"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>Använda platsvillkoret i en princip för villkorsstyrd åtkomst 

Som förklaras i [översiktsartikeln](overview.md) är principer för villkorsstyrd åtkomst de mest grundläggande en if-then-instruktion som kombinerar signaler, för att fatta beslut och genomdriva organisationsprinciper. En av dessa signaler som kan införlivas i beslutsprocessen är nätverksplats.

![Konceptuell villkorlig signal samt beslut om att få framtvingande](./media/location-condition/conditional-access-signal-decision-enforcement.png)

Organisationer kan använda den här nätverksplatsen för vanliga uppgifter som: 

- Kräva multifaktorautentisering för användare som har åtkomst till en tjänst när de är utanför företagsnätverket.
- Blockera åtkomst för användare som har åtkomst till en tjänst från specifika länder eller regioner.

Nätverksplats bestäms av den offentliga IP-adress som en klient tillhandahåller för att Azure Active Directory. Principer för villkorsstyrd åtkomst gäller som standard för alla IPv4- och IPv6-adresser. 

## <a name="named-locations"></a>Namngivna platser

Platser anges i den här Azure Portal under **Azure Active Directory**  >  **Security Conditional**  >  **Access**  >  **Named locations**. Dessa namngivna nätverksplatser kan innehålla platser som en organisations huvudkontors nätverksintervall, VPN-nätverksintervall eller intervall som du vill blockera. Namngivna platser kan definieras av IPv4/IPv6-adressintervall eller av länder/regioner. 

![Namngivna platser i Azure Portal](./media/location-condition/new-named-location.png)

### <a name="ip-address-ranges"></a>IP-adressintervall

Om du vill definiera en namngiven plats efter IPv4/IPv6-adressintervall måste du ange ett **namn** och ett IP-intervall. 

Namngivna platser som definieras av IPv4-/IPv6-adressintervall omfattas av följande begränsningar: 
- Konfigurera upp till 195 namngivna platser
- Konfigurera upp till 2 000 IP-intervall per namngiven plats
- Både IPv4- och IPv6-intervall stöds
- Privata IP-intervall kan inte konfigureras
- Antalet IP-adresser som finns i ett intervall är begränsat. Endast CIDR-masker som är större än /8 tillåts när du definierar ett IP-intervall. 

### <a name="trusted-locations"></a>Betrodda platser

Administratörer kan ange namngivna platser som definieras av IP-adressintervall som betrodda namngivna platser. 

![Betrodda platser i Azure Portal](./media/location-condition/new-trusted-location.png)

Inloggningar från betrodda namngivna platser förbättrar noggrannheten för Azure AD Identity Protection riskberäkning, vilket minskar en användares inloggningsrisk när de autentiserar från en plats som markerats som betrodd. Dessutom kan betrodda namngivna platser vara mål för principer för villkorlig åtkomst. Du kan till exempel kräva att du endast begränsar registreringen av multifaktorautentisering till betrodda namngivna platser. 

### <a name="countries-and-regions"></a>Länder och regioner

Vissa organisationer kan välja att begränsa åtkomsten till vissa länder eller regioner med villkorlig åtkomst. Förutom att definiera namngivna platser efter IP-intervall kan administratörer definiera namngivna platser efter land eller region. När en användare loggar in matchar Azure AD användarens IPv4-adress till ett land eller en region och mappningen uppdateras regelbundet. Organisationer kan använda namngivna platser som definieras av länder för att blockera trafik från länder där de inte gör affärer, till exempel Sydkorea. 

> [!NOTE]
> Inloggningar från IPv6-adresser kan inte mappas till länder eller regioner och betraktas som okända områden. Endast IPv4-adresser kan mappas till länder eller regioner.

![Skapa ett nytt land eller en ny regionbaserad plats i Azure Portal](./media/location-condition/new-named-location-country-region.png)

#### <a name="include-unknown-areas"></a>Inkludera okända områden

Vissa IP-adresser mappas inte till ett visst land eller en viss region, inklusive alla IPv6-adresser. Om du vill avbilda dessa IP-platser markerar du kryssrutan **Inkludera okända områden när** du definierar en plats. Med det här alternativet kan du välja om dessa IP-adresser ska inkluderas på den namngivna platsen. Använd den här inställningen när principen som använder den namngivna platsen ska gälla för okända platser.

### <a name="configure-mfa-trusted-ips"></a>Konfigurera betrodda IP-adresser för MFA

Du kan också konfigurera IP-adressintervall som representerar din organisations lokala intranät i [inställningarna för multifaktorautentiseringstjänsten.](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx) Med den här funktionen kan du konfigurera upp till 50 IP-adressintervall. IP-adressintervallen är i CIDR-format. Mer information finns i Tillförlitliga [IP-adresser.](../authentication/howto-mfa-mfasettings.md#trusted-ips)  

Om du har konfigurerat betrodda IP-adresser visas de som **betrodda MFA-IPS** i listan över platser för platsvillkoret.

### <a name="skipping-multi-factor-authentication"></a>Hoppa över multifaktorautentisering

På sidan med inställningar för multifaktorautentiseringstjänsten kan du identifiera företagets intranätanvändare genom att välja Hoppa över multifaktorautentisering för begäranden från **federerade användare på mitt intranät.** Den här inställningen anger att det inuti företagets nätverksanspråk, som utfärdas av AD FS, ska vara betrott och användas för att identifiera användaren som att den finns i företagsnätverket. Mer information finns i Aktivera [funktionen för betrodda IP-adresser med villkorlig åtkomst.](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access)

När du har markerat det här alternativet gäller **MFA Trusted IPS för alla** principer med det här alternativet valt, inklusive den namngivna platsen.

För mobila och stationära program som har lång livslängd för sessioner omvärderas villkorlig åtkomst regelbundet. Standardvärdet är en gång i timmen. När anspråket i företagsnätverket endast utfärdas vid tidpunkten för den första autentiseringen kanske inte Azure AD har en lista över betrodda IP-intervall. I det här fallet är det svårare att avgöra om användaren fortfarande är i företagsnätverket:

1. Kontrollera om användarens IP-adress finns i ett av de betrodda IP-intervallen.
1. Kontrollera om de tre första oktetterna i användarens IP-adress matchar de första tre oktetterna i IP-adressen för den första autentiseringen. IP-adressen jämförs med den första autentiseringen när anspråket i företagsnätverket ursprungligen utfärdades och användarens plats verifierades.

Om båda stegen misslyckas anses en användare inte längre ha en betrodd IP-adress.

## <a name="location-condition-in-policy"></a>Platsvillkor i principen

När du konfigurerar platsvillkoret har du möjlighet att skilja mellan:

- Valfri plats
- Alla betrodda platser
- Valda platser

### <a name="any-location"></a>Valfri plats

Om du väljer **Valfri plats tillämpas** som standard en princip på alla IP-adresser, vilket innebär alla adresser på Internet. Den här inställningen är inte begränsad till IP-adresser som du har konfigurerat som namngiven plats. När du väljer **Valfri plats** kan du fortfarande undanta specifika platser från en princip. Du kan till exempel tillämpa en princip på alla platser utom betrodda platser för att ange omfånget för alla platser, utom företagsnätverket.

### <a name="all-trusted-locations"></a>Alla betrodda platser

Det här alternativet gäller för:

- Alla platser som har markerats som betrodda platser
- **MFA-betrodd IPS** (om konfigurerat)

### <a name="selected-locations"></a>Valda platser

Med det här alternativet kan du välja en eller flera namngivna platser. För att en princip med den här inställningen ska gälla måste en användare ansluta från någon av de valda platserna. När du klickar **på Välj** den namngivna kontrollen för val av nätverk som visar att listan över namngivna nätverk öppnas. Listan visar också om nätverksplatsen har markerats som betrodd. Den namngivna platsen **med namnet Betrodda IP-adresser** för MFA används för att inkludera DE IP-inställningar som kan konfigureras på inställningssidan för multifaktorautentiseringstjänsten.

## <a name="ipv6-traffic"></a>IPv6-trafik

Som standard gäller principer för villkorlig åtkomst för all IPv6-trafik. Du kan undanta specifika IPv6-adressintervall från en princip för villkorsstyrd åtkomst om du inte vill att principer ska tillämpas för specifika IPv6-intervall. Om du till exempel inte vill framtvinga en princip för användning i företagsnätverket och företagsnätverket finns i offentliga IPv6-intervall.  

### <a name="when-will-my-tenant-have-ipv6-traffic"></a>När kommer min klient ha IPv6-trafik?

Azure Active Directory (Azure AD) stöder för närvarande inte direkta nätverksanslutningar som använder IPv6. Det finns dock vissa fall där autentiseringstrafiken proxieras via en annan tjänst. I dessa fall används IPv6-adressen under principutvärderingen.

Merparten av den IPv6-trafik som kommer via proxy till Azure AD kommer från Microsoft Exchange Online. När det är tillgängligt föredrar Exchange IPv6-anslutningar. **Så om du har några principer för villkorlig åtkomst för Exchange, som har konfigurerats för specifika IPv4-intervall, bör du se till att du även har lagt till organisationens IPv6-intervall.** Om du inte inkluderar IPv6-intervall kommer det att leda till oväntat beteende i följande två fall:

- När en e-postklient används för att ansluta till Exchange Online med äldre autentisering kan Azure AD få en IPv6-adress. Den första autentiseringsbegäran går till Exchange och kommer sedan att skicka den till Azure AD.
- När Outlook Web Access (OWA) används i webbläsaren verifierar den regelbundet att alla principer för villkorlig åtkomst fortsätter att vara uppfyllda. Den här kontrollen används för att fånga fall där en användare kan ha flyttat från en tillåten IP-adress till en ny plats, till exempel ett kafé på gatan. I det här fallet, om en IPv6-adress används och om IPv6-adressen inte är inom ett konfigurerat intervall, kan användaren få sessionen avbruten och dirigeras tillbaka till Azure AD för att återauktorera. 

Det här är de vanligaste orsakerna till att du kan behöva konfigurera IPv6-intervall på dina namngivna platser. Om du använder virtuella Azure-nätverk har du dessutom trafik som kommer från en IPv6-adress. Om du har blockerad VNet-trafik av en princip för villkorsstyrd åtkomst kontrollerar du inloggningsloggen för Azure AD. När du har identifierat trafiken kan du hämta IPv6-adressen som används och exkludera den från principen. 

> [!NOTE]
> Om du vill ange ett IP CIDR-intervall för en enskild adress använder du /128-bitarsmasken. Om du säger IPv6-adressen 2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a och vill undanta den enskilda adressen som ett intervall använder du 2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a/128.

### <a name="identifying-ipv6-traffic-in-the-azure-ad-sign-in-activity-reports"></a>Identifiera IPv6-trafik i rapporter om Azure AD-inloggningsaktiviteter

Du kan identifiera IPv6-trafik i din klientorganisation genom att gå till [azure AD-inloggningsaktivitetsrapporterna](../reports-monitoring/concept-sign-ins.md). När du har öppnat aktivitetsrapporten lägger du till kolumnen "IP-adress". I den här kolumnen får du information om IPv6-trafiken.

Du kan också hitta klientens IP-adress genom att klicka på en rad i rapporten och sedan gå till fliken "Plats" i informationen om inloggningsaktiviteten. 

## <a name="what-you-should-know"></a>Det här bör du veta

### <a name="when-is-a-location-evaluated"></a>När utvärderas en plats?

Principer för villkorlig åtkomst utvärderas när:

- En användare loggar in på en webbapp, mobil- eller skrivbordsapp.
- Ett mobil- eller skrivbordsprogram som använder modern autentisering använder en uppdateringstoken för att hämta en ny åtkomsttoken. Som standard är den här kontrollen en gång i timmen.

Den här kontrollen innebär att en platsändring identifieras inom en timme efter att nätverksplatsen har ändrats för mobilappar och skrivbordsprogram som använder modern autentisering. För mobil- och datorprogram som inte använder modern autentisering tillämpas principen på varje tokenbegäran. Frekvensen för begäran kan variera beroende på programmet. På samma sätt tillämpas principen vid den första inloggningen för webbprogram och är bra för sessionens hela livslängd i webbprogrammet. På grund av skillnader i sessionslivslängd mellan program varierar tiden mellan principutvärderingen. Varje gång programmet begär en ny inloggningstoken tillämpas principen.

Som standard utfärdar Azure AD en token per timme. När du har flyttat från företagsnätverket tillämpas principen inom en timme för program som använder modern autentisering.

### <a name="user-ip-address"></a>Användarens IP-adress

IP-adressen som används i principutvärderingen är användarens offentliga IP-adress. För enheter i ett privat nätverk är den här IP-adressen inte klient-IP-adressen för användarens enhet på intranätet, det är den adress som används av nätverket för att ansluta till det offentliga Internet.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Massuppladdning och nedladdning av namngivna platser

När du skapar eller uppdaterar namngivna platser för massuppdateringar kan du ladda upp eller ladda ned en CSV-fil med IP-intervallen. En uppladdning ersätter IP-intervallen i listan med de intervallen från filen. Varje rad i filen innehåller ett IP-adressintervall i CIDR-format.

### <a name="cloud-proxies-and-vpns"></a>Molnproxies och VPN

När du använder en molnbaserad proxy eller VPN-lösning använder DEN IP-adress som Azure AD använder vid utvärdering av en princip IP-adressen för proxyn. Huvudet X-Forwarded-For (XFF) som innehåller användarens offentliga IP-adress används inte eftersom det inte finns någon validering av att den kommer från en betrodd källa, så skulle vara en metod för att faking av en IP-adress.

När en molnproxy är på plats kan en princip som används för att kräva en Azure AD-ansluten hybridenhet användas, eller det inifrån corpnet-anspråket från AD FS.

### <a name="api-support-and-powershell"></a>API-stöd och PowerShell

Det finns en förhandsversion av Graph API för namngivna platser. Mer information finns i [namedLocation API](/graph/api/resources/namedlocation).

> [!NOTE]
> Namngivna platser som du skapar med hjälp av PowerShell visas endast i Namngivna platser (förhandsversion). Du kan inte se namngivna platser i den gamla vyn.  

## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst kan du läsa artikeln [Skapa en princip för villkorlig åtkomst.](concept-conditional-access-policies.md)
- Letar du efter en exempelprincip som använder platsvillkoret? Mer information finns i [artikeln Villkorlig åtkomst: Blockera åtkomst efter plats](howto-conditional-access-policy-location.md)
