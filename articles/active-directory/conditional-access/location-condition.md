---
title: Plats villkor i Azure Active Directory villkorlig åtkomst
description: Lär dig hur du använder plats villkoret för att kontrol lera åtkomsten till dina molnappar baserat på användarens nätverks plats.
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
ms.openlocfilehash: 07af586bac71ee9b33ef314756454cb3c52ec912
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305930"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>Använda plats villkoret i en princip för villkorlig åtkomst 

Enligt beskrivningen i [översikts artikeln](overview.md) principer för villkorlig åtkomst är de mest grundläggande en if-then-sats som kombinerar signaler, för att fatta beslut och tillämpa organisations principer. En av dessa signaler som kan införlivas i besluts fattandet är nätverks plats.

![Konceptuell villkorlig signal plus beslut för att få tillämpning](./media/location-condition/conditional-access-signal-decision-enforcement.png)

Organisationer kan använda den här nätverks platsen för vanliga uppgifter, t. ex.: 

- Kräver Multi-Factor Authentication för användare som har åtkomst till en tjänst när de är utanför företags nätverket.
- Blockera åtkomst för användare som har åtkomst till en tjänst från vissa länder eller regioner.

Nätverks platsen bestäms av den offentliga IP-adressen som en klient tillhandahåller för att Azure Active Directory. Principer för villkorlig åtkomst gäller som standard för alla IPv4-och IPv6-adresser. 

## <a name="named-locations"></a>Namngivna platser

Platser anges i Azure Portal under **Azure Active Directory**  >  **säkerhets**  >  **åtkomst till**  >  **namngivna platser**. Dessa namngivna nätverks platser kan innehålla platser som en organisations nätverks intervall, VPN-adressintervall eller intervall som du vill blockera. Namngivna platser kan definieras av IPv4/IPv6-adressintervall eller länder/regioner. 

![Namngivna platser i Azure Portal](./media/location-condition/new-named-location.png)

### <a name="ip-address-ranges"></a>IP-adressintervall

Om du vill definiera en namngiven plats med IPv4/IPv6-adressintervall måste du ange ett **namn** och ett IP-intervall. 

Namngivna platser som definieras av IPv4/IPv6-adress intervall omfattas av följande begränsningar: 
- Konfigurera upp till 195 namngivna platser
- Konfigurera upp till 2000 IP-intervall per namngiven plats
- Både IPv4-och IPv6-intervall stöds
- Connot för privata IP-intervall måste konfigureras
- Antalet IP-adresser i ett intervall är begränsat. Endast CIDR-masker som är större än/8 tillåts när du definierar ett IP-intervall. 

### <a name="trusted-locations"></a>Betrodda platser

Administratörer kan ange namngivna platser som definieras av IP-adressintervall som betrodda namngivna platser. 

![Betrodda platser i Azure Portal](./media/location-condition/new-trusted-location.png)

Inloggningar från betrodda namngivna platser förbättrar noggrannheten i Azure AD Identity Protections risk beräkning, vilket minskar risken för användares inloggning när de autentiseras från en plats som är markerad som betrodd. Dessutom kan betrodda namngivna platser riktas mot principer för villkorlig åtkomst. Du kan till exempel behöva begränsa registreringen av Multi-Factor Authentication till betrodda namngivna platser. 

### <a name="countries-and-regions"></a>Länder och regioner

Vissa organisationer kan välja att begränsa åtkomsten till vissa länder eller regioner med villkorlig åtkomst. Förutom att definiera namngivna platser med IP-intervall kan administratörer definiera namngivna platser efter land eller region. När en användare loggar in löser Azure AD användarens IPv4-adress till ett land eller en region, och mappningen uppdateras regelbundet. Organisationer kan använda namngivna platser som definieras av länder för att blockera trafik från länder där de inte gör affärer, till exempel Nord Korea. 

> [!NOTE]
> Inloggningar från IPv6-adresser kan inte mappas till länder eller regioner och betraktas som okända områden. Endast IPv4-adresser kan mappas till länder eller regioner.

![Skapa en ny lands-eller regions-baserad plats i Azure Portal](./media/location-condition/new-named-location-country-region.png)

#### <a name="include-unknown-areas"></a>Inkludera okända områden

Vissa IP-adresser mappas inte till ett visst land eller en viss region, inklusive alla IPv6-adresser. Om du vill avbilda dessa IP-platser markerar du kryss rutan **Inkludera okända områden** när du definierar en plats. Med det här alternativet kan du välja om de här IP-adresserna ska tas med på den namngivna platsen. Använd den här inställningen när principen som använder den namngivna platsen ska gälla för okända platser.

### <a name="configure-mfa-trusted-ips"></a>Konfigurera MFA-betrodda IP-adresser

Du kan också konfigurera IP-adressintervall som representerar organisationens lokala intranät i [inställningarna för Multi-Factor Authentication-tjänsten](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Med den här funktionen kan du konfigurera upp till 50 IP-adressintervall. IP-adressintervallet är i CIDR-format. Mer information finns i [betrodda IP-adresser](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Om du har konfigurerat betrodda IP-adresser visas de som **MFA-betrodda IP-adresser** i listan över platser för plats villkoret.

### <a name="skipping-multi-factor-authentication"></a>Hoppar över Multi-Factor Authentication

På sidan Inställningar för Multi-Factor Authentication Service kan du identifiera företags intranäts användare genom att välja  **hoppa över Multi-Factor Authentication för förfrågningar från federerade användare i mitt intranät**. Den här inställningen anger att det i företags nätverks anspråk, som utfärdas av AD FS, ska vara betrott och användas för att identifiera användaren som i företags nätverket. Mer information finns i [aktivera funktionen för betrodda IP-adresser med hjälp av villkorlig åtkomst](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

När du har markerat det här alternativet, inklusive den namngivna platsen **MFA-betrodda IP-adresser** kommer att gälla för alla principer med det här alternativet valt.

För mobil-och skriv bords program, som har länge livs längd för sessionens livs längd, utvärderas villkorlig åtkomst regelbundet om. Standardvärdet är en gång i timmen. Om det interna nätverks anspråk bara utfärdas vid tidpunkten för den inledande autentiseringen kanske inte Azure AD har en lista över betrodda IP-intervall. I det här fallet är det svårare att avgöra om användaren fortfarande befinner sig i företags nätverket:

1. Kontrol lera att användarens IP-adress finns i något av de betrodda IP-intervallen.
1. Kontrol lera om de tre första oktetterna i användarens IP-adress matchar de tre första oktetterna i IP-adressen för den inledande autentiseringen. IP-adressen jämförs med den första autentiseringen när den interna nätverks anspråket utfärdades ursprungligen och användar platsen verifierades.

Om båda stegen inte fungerar, anses en användare inte längre på en betrodd IP-adress.

## <a name="location-condition-in-policy"></a>Plats villkor i princip

När du konfigurerar plats villkoret har du möjlighet att skilja mellan:

- Valfri plats
- Alla betrodda platser
- Valda platser

### <a name="any-location"></a>Valfri plats

Som standard gör en princip att tillämpas på alla IP-adresser, vilket innebär vilken adress **som helst på** Internet. Den här inställningen är inte begränsad till IP-adresser som du har konfigurerat som namngiven plats. När du väljer **en plats** kan du fortfarande utesluta vissa platser från en princip. Du kan till exempel använda en princip för alla platser utom betrodda platser för att ange omfattningen till alla platser, förutom företags nätverket.

### <a name="all-trusted-locations"></a>Alla betrodda platser

Det här alternativet gäller för:

- Alla platser som har marker ATS som betrodda platser
- **MFA-betrodda IP-adresser** (om de kon figurer ATS)

### <a name="selected-locations"></a>Valda platser

Med det här alternativet kan du välja en eller flera namngivna platser. För att en princip med den här inställningen ska tillämpas måste användaren ansluta från någon av de valda platserna. När du klickar på **Välj** den namngivna nätverks val kontrollen som visar listan över namngivna nätverk som öppnas. I listan visas även om nätverks platsen har marker ATS som betrodd. Den namngivna platsen för **MFA-betrodda IP-adresser** används för att inkludera de IP-inställningar som kan konfigureras på inställnings sidan för Multi-Factor Authentication-tjänsten.

## <a name="ipv6-traffic"></a>IPv6-trafik

Som standard gäller principer för villkorlig åtkomst för all IPv6-trafik. Du kan undanta vissa IPv6-adressintervall från en princip för villkorlig åtkomst om du inte vill att principer ska tillämpas för vissa IPv6-intervall. Till exempel, om du inte vill genomdriva en princip för användning i företags nätverket och företagets nätverk finns på offentliga IPv6-intervall.  

### <a name="when-will-my-tenant-have-ipv6-traffic"></a>När kommer min klient att ha IPv6-trafik?

Azure Active Directory (Azure AD) stöder för närvarande inte direkta nätverks anslutningar som använder IPv6. Det finns dock vissa fall där autentiserings trafik är via proxy via en annan tjänst. I dessa fall används IPv6-adressen under princip utvärderingen.

Merparten av den IPv6-trafik som hämtar proxy till Azure AD kommer från Microsoft Exchange Online. När det är tillgängligt kommer Exchange att föredra IPv6-anslutningar. **Så om du har några principer för villkorlig åtkomst för Exchange, som har kon figurer ATS för vissa IPv4-intervall, vill du se till att du även har lagt till dina organisations IPv6-intervall.** Att inte inkludera IPv6-intervall orsakar oväntat beteende i följande två fall:

- När en e-postklient används för att ansluta till Exchange Online med äldre autentisering kan Azure AD ta emot en IPv6-adress. Begäran om inledande autentisering går till Exchange och skickas sedan till proxy till Azure AD.
- När Outlook Web Access (OWA) används i webbläsaren kontrollerar det regelbundet att alla principer för villkorlig åtkomst fortfarande är uppfyllda. Den här kontrollen används för att fånga upp fall där en användare kan ha flyttat från en tillåten IP-adress till en ny plats, t. ex. att kaféet är i gatan. I det här fallet, om en IPv6-adress används och IPv6-adressen inte är i ett konfigurerat intervall, kan användaren avbryta sessionen och dirigera tillbaka till Azure AD för att autentisera igen. 

Detta är de vanligaste orsakerna till att du kan behöva konfigurera IPv6-intervall på dina namngivna platser. Om du använder Azure-virtuella nätverk kommer du dessutom att ha trafik som kommer från en IPv6-adress. Om du har VNet-trafik blockerad av en princip för villkorlig åtkomst, kontrollerar du inloggnings loggen för Azure AD. När du har identifierat trafiken kan du hämta IPv6-adressen som används och utesluta den från principen. 

> [!NOTE]
> Om du vill ange ett IP-CIDR-intervall för en enskild adress använder du bit masken/128. Om du säger IPv6-adressen 2607: fb90: b27a: 6f69: f8d5: dea0: fb39:74A och vill utesluta den enkla adressen som ett intervall, använder du 2607: fb90: b27a: 6f69: f8d5: dea0: fb39:74A/128.

### <a name="identifying-ipv6-traffic-in-the-azure-ad-sign-in-activity-reports"></a>Identifiera IPv6-trafik i rapporter om inloggnings aktiviteter i Azure AD

Du kan identifiera IPv6-trafik i din klient genom att gå till [rapporterna för inloggnings aktiviteter i Azure AD](../reports-monitoring/concept-sign-ins.md). Lägg till kolumnen "IP-adress" när du har öppnat aktivitets rapporten. I den här kolumnen kan du identifiera IPv6-trafiken.

Du kan också hitta klientens IP-adress genom att klicka på en rad i rapporten och sedan gå till fliken "plats" i inloggnings aktivitetens information. 

## <a name="what-you-should-know"></a>Det här bör du veta

### <a name="when-is-a-location-evaluated"></a>När utvärderas platsen?

Principer för villkorlig åtkomst utvärderas när:

- En användare loggar in från början till en webbapp, ett mobilt eller Skriv bords program.
- Ett mobil-eller Skriv bords program som använder modern autentisering använder en uppdateringstoken för att hämta en ny åtkomsttoken. Som standard är den här kontrollen en gång i timmen.

Den här kontrollen avser mobila och Station ära program som använder modern autentisering, men en ändrings plats upptäcktes inom en timme efter att nätverks platsen ändrades. För mobila och Station ära program som inte använder modern autentisering tillämpas principen på varje Tokenbegäran. Frekvensen för begäran kan variera beroende på programmet. På samma sätt gäller för webb program principen vid inledande inloggning och är lämpligt för sessionens livs längd i webb programmet. På grund av skillnader i sessionernas livs längder mellan olika program, kommer tiden mellan princip utvärdering också att variera. Varje gången programmet begär en ny inloggnings-token tillämpas principen.

Som standard utfärdar Azure AD en token per timme. När du har flyttat företags nätverket inom en timme tillämpas principen för program som använder modern autentisering.

### <a name="user-ip-address"></a>Användarens IP-adress

IP-adressen som används i princip utvärderingen är användarens offentliga IP-adress. För enheter i ett privat nätverk är den här IP-adressen inte klientens IP-adress för användarens enhet i intranätet, den är den adress som används av nätverket för att ansluta till det offentliga Internet.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Mass överföring och hämtning av namngivna platser

När du skapar eller uppdaterar namngivna platser för Mass uppdateringar kan du ladda upp eller ladda ned en CSV-fil med IP-intervall. Vid en uppladdning ersätts IP-intervallen i listan med intervallen från filen. Varje rad i filen innehåller ett IP-adressintervall i CIDR-format.

### <a name="cloud-proxies-and-vpns"></a>Cloud-proxyservrar och VPN-nätverk

När du använder en molnbaserad proxy eller VPN-lösning för molnet används IP-adressen för Azure AD vid utvärdering av en princip är proxyserverns IP-adress. Det X-vidarebefordrade-for-(XFF)-huvud som innehåller användarens offentliga IP-adress används inte eftersom det inte finns någon validering av att den kommer från en betrodd källa, så att det finns en metod för att Faking en IP-adress.

När en molnbaserad proxy är på plats kan en princip som används för att kräva en hybrid Azure AD-ansluten enhet användas, eller på den inre Corpnet-anspråket från AD FS.

### <a name="api-support-and-powershell"></a>API-stöd och PowerShell

En för hands version av Graph API för namngivna platser är tillgänglig. mer information finns i [namedLocation-API: et](/graph/api/resources/namedlocation?view=graph-rest-beta).

> [!NOTE]
> Namngivna platser som du skapar med hjälp av PowerShell visas bara på namngivna platser (för hands version). Du kan inte se namngivna platser i den gamla vyn.  

## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst kan du läsa artikeln [skapa en princip för villkorlig åtkomst](concept-conditional-access-policies.md).
- Letar du efter en exempel princip med plats villkoret? Se artikeln [villkorlig åtkomst: blockera åtkomst efter plats](howto-conditional-access-policy-location.md)
