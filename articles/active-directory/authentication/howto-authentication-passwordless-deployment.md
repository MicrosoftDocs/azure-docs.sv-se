---
title: Planera distribution av lösenordsskyddad autentisering med Azure AD
description: Lär dig att planera och distribuera en Azure Active Directory implementering av lösen ords lös autentisering
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: baselden
author: justinha
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24d04c148e8a96e3b1d72c5301f6226df6d9a569
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530356"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Planera distribution av lösenordsskyddad autentisering i Azure Active Directory

> [!NOTE]
> Om du vill skapa en offline-version av den här distributions planen använder du webbläsarens utskrifts-till-PDF-funktion.

De flesta cyberhot-attacker börjar med ett komprometterat användar namn och lösen ord. Organisationer försöker räkna med hotet genom att kräva att användarna använder någon av följande metoder:

- Långa lösen ord
- Komplexa lösen ord
- Frekventa lösen ords ändringar
- Multi-Factor Authentication (MFA)

Microsofts [forskning visar](https://aka.ms/passwordguidance) att dessa ansträngningar Annoy användare och driver support kostnader. Mer information finns i [din pa $ $Word spelar ingen roll](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

### <a name="benefits-of-passwordless-authentication"></a>Fördelar med lösen ords verifiering

- **Ökad säkerhet**. Minska risken för nätfiske och attacker genom att ta bort lösen ord som en attack yta.
-  **Bättre användar upplevelse**. Ge användarna ett bekvämt sätt att komma åt data överallt. Ger enkel åtkomst till program och tjänster som Outlook, OneDrive eller Office medan mobilt.
-  **Robusta insikter**. Få insikter om hur användarna får en lösen ords åtgärd med robust loggning och granskning.

Lösen ordet för lösen ord ersätts med något som du har plus något som du är eller något du känner till. Windows Hello för företag kan till exempel använda Bio metrisk gest som ett ansikte eller finger avtryck, eller en enhetsspecifika PIN-kod som inte överförs över ett nätverk.

## <a name="passwordless-authentication-methods"></a>Metoder för lösen ords kryptering
Microsoft erbjuder tre lösen ords skydds alternativ som omfattar många olika scenarier. Dessa metoder kan användas i tandem:

- [Windows Hello för företag](./concept-authentication-passwordless.md) är bäst för användare på sina dedikerade Windows-datorer.
- Inloggning med säkerhets [nycklar med FIDO2-säkerhetsnycklar](./concept-authentication-passwordless.md) är särskilt användbart för användare som loggar in på delade datorer som kiosker, i situationer där användningen av telefoner är begränsad och för hög privilegierade identiteter.
- Logga in med Microsoft Authenticator- [appen](./concept-authentication-passwordless.md) är användbar för att ange ett lösen ords lös alternativ för användare med mobila enheter. Authenticator-appen förvandlar en iOS-eller Android-telefon till en stark, lösen ords lös autentiseringsuppgift genom att tillåta att användare loggar in på valfri plattform eller webbläsare. Användarna loggar in genom att få ett meddelande till telefonen, matcha ett tal som visas på skärmen till det som finns på telefonen och sedan använda sina bio metriska data eller PIN-kod för att bekräfta.

### <a name="passwordless-authentication-scenarios"></a>Scenarier med lösenordsskyddad autentisering

Microsofts autentiseringsmetoder för lösen ords kryptering möjliggör olika scenarier. Överväg organisationens behov, krav och funktionerna i varje autentiseringsmetod för att välja din strategi för lösen ords lös autentisering. Vi rekommenderar att alla organisationer som använder Windows 10-enheter använder Windows Hello för företag. Lägg sedan till antingen telefonin loggning (med Microsoft Authenticator-appen) eller säkerhets nycklar för ytterligare scenarier.

| Scenario | Telefon-autentisering | Säkerhets nycklar | Windows Hello för företag |
| --- | --- | --- | --- |
| **Dator inloggning**: <br> Från tilldelad Windows 10-enhet | **Nej** | **Ja** <br> Med bio metrisk PIN-kod | **Ja**<br>med bio metrisk igenkänning och eller PIN-kod |
| **Dator inloggning**: <br> Från delad Windows 10-enhet | **Nej** | **Ja** <br> Med bio metrisk PIN-kod  | **Nej** |
| **Web App-inloggning**: <br>från en användardefinierad dator | **Ja** | **Ja** <br> Tillhandahållen enkel inloggning till appar har Aktiver ATS genom dator inloggning | **Ja**<br> Tillhandahållen enkel inloggning till appar har Aktiver ATS genom dator inloggning |
| **Web App-inloggning**: <br> från en mobil eller icke-Windows-enhet | **Ja** | **Nej** | **Nej** |
| **Dator inloggning**: <br> Icke-Windows-dator | **Nej** | **Nej** | **Nej** |

Information om hur du väljer den bästa metoden för din organisation finns i [bestämma en metod för lösen ords](./concept-authentication-passwordless.md#choose-a-passwordless-method)avhjälpning.

## <a name="prerequisites"></a>Förutsättningar

Organisationer måste uppfylla följande krav innan en lösenordsskyddad distribution påbörjas:

| Förutsättning | Autentiseringsapp | FIDO2 säkerhets nycklar |
| --- | --- | --- |
| [Kombinerad registrering för Azure AD Multi-Factor Authentication och återställning av lösen ord för självbetjäning (SSPR)](howto-registration-mfa-sspr-combined.md) är aktiverat | √ | √ |
| [Användare kan utföra Azure AD-Multi-Factor Authentication](howto-mfa-getstarted.md) | √ | √ |
| [Användare har registrerat sig för Azure AD Multi-Factor Authentication och SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Användare har registrerat sina mobila enheter på Azure Active Directory](../devices/overview.md) | √ |   |
| Windows 10 version 1809 eller senare med en webbläsare som stöds, t. ex. Microsoft Edge eller Mozilla Firefox <br> (version 67 eller senare). <br> *Microsoft rekommenderar version 1903 eller senare för intern support*. |   | √ |
| Kompatibla säkerhets nycklar för FIDO2. Se till att du använder en [Microsoft-testad och verifierad](./concept-authentication-passwordless.md) FIDO2-säkerhetsenhet eller annan kompatibel FIDO2-säkerhetsenhet. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Krav för Windows Hello för företag

Kraven för Windows Hello är mycket beroende av om du distribuerar i en lokal, hybrid eller molnbaserad konfiguration. Mer information finns i [fullständig lista över krav för Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-ad-multi-factor-authentication"></a>Azure AD-multifaktorautentisering

Användarna registrerar sin metod för lösen ords begränsning som en del av registrerings flödet för Azure AD-Multi-Factor Authentication. Multi-Factor Authentication med ett användar namn och lösen ord tillsammans med en annan registrerad metod kan användas som en reserv om de inte kan använda sin telefon-eller säkerhets nyckel i vissa situationer.

### <a name="licensing"></a>Licensiering 
Det finns ingen ytterligare kostnad för lösen ords lösa autentisering, även om vissa krav kan kräva en Premium-prenumeration. För detaljerad information om funktioner och licensiering på [sidan Azure Active Directory licensiering](https://azure.microsoft.com/pricing/details/active-directory/). 

## <a name="develop-a-plan"></a>Utveckla en plan

Överväg dina affärs behov och användnings fall för varje autentiseringsmetod. Välj sedan den metod som passar dina behov bäst.

### <a name="use-cases"></a>Användningsfall

I följande tabell beskrivs de användnings fall som implementeras under det här projektet.

| Område | Beskrivning |
| --- | --- |
| **Åtkomst** | Lösen ords återställnings inloggning är tillgängligt från en företags enhet eller en personlig enhet inom eller utanför företags nätverket. |
| **Granskning** | Användnings data är tillgängliga för administratörer att granska i nära real tid. <br> Användnings data hämtas till företags system minst var 29: e dag, eller så används SIEM-verktyget. |
| **Styrning** | Livs cykeln för användar tilldelningar till lämplig autentiseringsmetod och associerade grupper definieras och övervakas. |
| **Säkerhet** | Åtkomst till rätt autentiseringsmetod styrs via användar-och grupp tilldelningar. <br> Endast auktoriserade användare kan använda lösen ords skydd. |
| **Prestanda** | Tids linjer för distribution av åtkomst tilldelningar dokumenteras och övervakas. <br> Inloggnings tider mäts för enkel användning. |
| **Användar upplevelse** | Användare är medvetna om mobil kompatibilitet. <br> Användare kan konfigurera autentiseraren för lösenordsskyddade appar. |
| **Support** | Användare är medvetna om hur man hittar stöd för problem med lösen ords lös inloggning. |

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknik projekt kraschar är det vanligt vis på grund av en felaktig matchning av konsekvenserna, resultat och ansvars områden. För att undvika dessa fall GRO par bör [du se till att du är engagerande rätt intressenter](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) och att från intressenter-rollerna i projektet är väl förstå.

### <a name="plan-communications"></a>Planera kommunikation

Kommunikationen är nödvändig för att en ny tjänst ska lyckas. Kommunicera proaktivt hur användarnas upplevelse kommer att ändras, när de kommer att ändras och hur de får support om de drabbas av problem.

Din kommunikation för slutanvändare bör innehålla följande information:

- [Aktivera den kombinerade säkerhets registrerings upplevelsen](howto-authentication-passwordless-phone.md)
- [Laddar ned Microsoft Authenticator-appen](../user-help/user-help-auth-app-download-install.md)
- [Registrera i Microsoft Authenticator-appen](howto-authentication-passwordless-phone.md)
- [Logga in med din telefon](../user-help/user-help-auth-app-sign-in.md)

Microsoft tillhandahåller Multi-Factor Authentication- [mallar](https://aka.ms/mfatemplates)för kommunikation, Self-Service-SSPR (Password [Reset) och](https://www.microsoft.com/download/details.aspx?id=56768) [dokumentation om slutanvändare](../user-help/security-info-setup-signin.md) för att hjälpa till att skriva till kommunikation. Du kan skicka användare till [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) att registrera direkt genom att välja länkarna för **säkerhets information** på sidan.

### <a name="plan-to-pilot"></a>Planera för pilot

När du distribuerar lösenordsbaserad autentisering bör du först aktivera en eller flera pilot grupper. Du kan [skapa grupper](../fundamentals/active-directory-groups-create-azure-portal.md) särskilt för detta ändamål. Lägg till de användare som ska ingå i piloten till grupperna. Aktivera sedan nya metoder för lösen ords kryptering för de valda grupperna.

Grupper kan synkroniseras från en lokal katalog eller från Azure AD. När du är nöjd med resultatet från piloten kan du växla till lösen ords avinstallationen för alla användare.

Se [metod tips för en pilot](../fundamentals/active-directory-deployment-plans.md) på sidan distributions planer.

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>Planera lösenordsbaserad autentisering med Microsoft Authenticator-appen

Microsoft Authenticator-appen är en kostnads fri nedladdning från Google Play eller Apple App Store. [Läs mer om att ladda ned Microsoft Authenticator-appen](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Låt användarna ladda ned Microsoft Authenticator-appen. och följ anvisningarna för att aktivera telefon inloggning. 

Det sätter en iOS-eller Android-telefon till en stark, lösen ords rik autentiseringsuppgift. Användarna loggar in på valfri plattform eller webbläsare genom att få ett meddelande till sin telefon, matcha ett nummer som visas på skärmen till det som visas på telefonen och sedan använda biometrik eller en PIN-kod för att bekräfta. [Se information om hur Microsoft Authenticator-appen fungerar](./concept-authentication-passwordless.md#microsoft-authenticator-app).

![Logga in med Authenticator-appen](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Tekniska överväganden för Microsoft Authenticator-appen

**AD FS-integration** – när en användare aktiverar Microsoft Authenticator lösen ords lös autentiseringsuppgifter, kommer autentiseringen för den användaren att skicka ett meddelande för godkännande. Användare i en hybrid klient hindras från att dirigeras till ADFS för inloggning om de inte väljer Använd lösen ordet i stället. Den här processen kringgår också alla lokala principer för villkorlig åtkomst och genom strömnings flöden. Men om en *login_hint* anges vidarebefordras användaren till ADFS och alternativet att använda lösen ords lös autentiseringsuppgifter kringgås.

**Azure AD Multi-Factor Authentication Server** -slutanvändare som har Aktiver ATS för Multi-Factor Authentication genom en organisations lokala Azure MFA-Server kan skapa och använda en enda inloggnings inloggning för lösen ord. Om användaren försöker uppgradera flera installationer (minst 5) av Microsoft Authenticator med autentiseringsuppgifterna, kan den här ändringen resultera i ett fel.

**Enhets registrering** – om du vill använda Authenticator-appen för lösen ords lös autentisering måste enheten vara registrerad i Azure AD-klienten och kan inte vara en delad enhet. En enhet kan bara registreras i en enda klient. Den här gränsen innebär att endast ett arbets-eller skol konto stöds för telefonin loggning med hjälp av Authenticator-appen.

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>Planera lösenordsbaserad autentisering med säkerhets nycklar för FIDO2
Det finns tre typer av lösen ords skydds bara inloggnings distributioner som är tillgängliga med säkerhets nycklar:

-    Azure Active Directory Web Apps i en webbläsare som stöds
-    Azure Active Directory anslutna Windows 10-enheter
-    Hybrid Azure Active Directory anslutna Windows 10-enheter (för hands version)
     -    Ger åtkomst till både molnbaserade och lokala resurser. Mer information om åtkomst till lokala resurser finns i [SSO till lokala resurser med hjälp av FIDO2-nycklar](./howto-authentication-passwordless-security-key-on-premises.md)

Du måste aktivera **kompatibla FIDO2 säkerhets nycklar**. Microsoft har lanserat [viktiga partnerskap med FIDO2 Key-leverantörer](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493).

**För Azure AD-webbappar och Windows-anslutna Azure AD-enheter**:

-    Windows 10 version 1809 eller senare med en webbläsare som stöds, t. ex. Microsoft Edge eller Mozilla Firefox (version 67 eller senare). 
-    Windows 10 version 1809 har stöd för FIDO2-inloggning och kan kräva att program vara från FIDO2 Key-tillverkaren distribueras. Vi rekommenderar att du använder version 1903 eller senare. 

**För anslutna Hybrid Azure Active Directory domänanslutna enheter**: 
-    Windows 10 version 2004 eller senare
-    Fullständigt korrigerade domän servrar som kör Windows Server 2016 eller 2019.
-    Senaste versionen av Azure AD Connect

En fullständig lista över kraven finns i [Aktivera lösen ords lös säkerhets nyckel inloggning till Windows 10-enheter med Azure Active Directory](./howto-authentication-passwordless-security-key-windows.md#requirements).


### <a name="security-key-life-cycle"></a>Livs cykel för säkerhets nyckel

Säkerhets nycklar ger åtkomst till dina resurser och du bör planera hanteringen av dessa fysiska enheter.

1. **Nyckel distribution**: planera hur du ska etablera nycklar till din organisation. Du kan ha en centraliserad etablerings process eller tillåta slutanvändare att köpa FIDO 2,0-kompatibla nycklar.
1. **Nyckel aktivering**: slutanvändare måste själv aktivera säkerhets nyckeln. Slutanvändare registrerar sina säkerhets nycklar på [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) och aktiverar den andra faktorn (PIN-kod eller bio metrisk) vid första användningen.
1. **Inaktivera en nyckel**: När funktionen för säkerhets nycklar är i förhands gransknings fasen finns det inget sätt för en administratör att ta bort en nyckel från ett användar konto. Användaren måste ta bort den. Om en nyckel tappas bort eller blir stulen:
   1. Ta bort användaren från en grupp som är aktive rad för autentisering utan lösen ord.
   1. Kontrol lera att de har tagit bort nyckeln som en autentiseringsmetod.
   1. Utfärda en ny nyckel. **Nyckel ersättning**: användare kan aktivera två säkerhets nycklar samtidigt. När du ersätter en säkerhets nyckel bör du se till att användaren även har tagit bort nyckeln som byts ut.

### <a name="enable-windows-10-support"></a>Aktivera stöd för Windows 10

Aktivering av Windows 10-inloggning med hjälp av säkerhets nycklar för FIDO2 kräver aktivering av funktionen för Credential-Provider i Windows 10. Välj något av följande:

- [Aktivera autentiseringsprovider för autentiseringsuppgifter med Intune](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - Intune-distributionen är det rekommenderade alternativet.
- [Aktivera autentiseringsprovider med ett konfigurations paket](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Om Intune-distributionen inte är möjlig måste administratörerna distribuera ett paket på varje dator för att aktivera funktionen för Credential-Provider. Paket installationen kan utföras med ett av följande alternativ:
      - grupprincip eller Configuration Manager
      - Lokal installation på en Windows 10-dator
- [Aktivera autentiseringsprovider för autentiseringsuppgifter med grupprincip](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - Stöds endast för Hybrid Azure AD-anslutna enheter.

#### <a name="enable-on-premises-integration"></a>Aktivera lokal integrering

Om du vill aktivera åtkomst till lokala resurser följer du stegen för att [Aktivera lösen ords lös säkerhets nyckel logga in till lokala resurser (för hands version)](howto-authentication-passwordless-security-key-on-premises.md).

> [!IMPORTANT]
> De här stegen måste också utföras för alla hybrid Azure AD-anslutna enheter för att använda FIDO2-säkerhetsnycklar för Windows 10-inloggning.

### <a name="register-security-keys"></a>Registrera säkerhets nycklar

Användarna måste registrera sina säkerhets nycklar på var och en av sina Azure Active Directory anslutna Windows 10-datorer.

Mer information finns i [användar registrering och hantering av säkerhets nycklar för FIDO2](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys).


## <a name="plan-auditing-security-and-testing"></a>Planera granskning, säkerhet och testning
Planering för granskning som uppfyller dina ramverk för organisation och efterlevnad är en viktig del av distributionen.

### <a name="auditing-passwordless"></a>Granska lösen ords kontroll

Azure AD har rapporter som ger teknisk och affärs insikter. Be dina företags-och teknik program ägare att ha ägande rätt till och använda rapporterna utifrån organisationens krav.

Avsnittet **autentiseringsmetoder** i Azure Active Directory Portal är där administratörer kan aktivera och hantera inställningar för lösen ords kryptering.

Azure AD lägger till poster i gransknings loggarna när:

- En administratör gör ändringar i avsnittet autentiseringsmetoder.
- En användare gör alla typer av ändringar i sina autentiseringsuppgifter i Azure Active Directory.

Följande tabell innehåller några exempel på typiska rapporterings scenarier:

|   | Hantera risk | Öka produktiviteten | Styrning och efterlevnad |
| --- | --- | --- | --- |
| **Rapporttyper** | Autentiseringsmetoder – användare som registrerats för kombinerad säkerhets registrering | Autentiseringsmetoder – användare registrerade för appens avisering | Inloggningar: granska vem som ansluter till klienten och hur |
| **Möjliga åtgärder** | Mål användare har ännu inte registrerats | Enhets införande av Microsoft Authenticator-app eller säkerhets nycklar | Återkalla åtkomst eller Använd ytterligare säkerhets principer för administratörer |

**Azure AD behåller de flesta gransknings data i 30 dagar** och gör data tillgängliga via Azure Admin Portal eller API som du kan ladda ned till dina analys system. Om du vill ha längre kvarhållning kan du exportera och använda loggar i ett SIEM-verktyg som [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk eller Sumo Logic. [Lär dig mer om att visa åtkomst-och användnings rapporter](../reports-monitoring/overview-reports.md).

Användare kan registrera och hantera sina autentiseringsuppgifter genom att gå till [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Den här länken dirigerar användarna till den hantering av autentiseringsuppgifter för slutanvändare som har Aktiver ATS via den kombinerade SSPR/Multi-Factor Authentication-registreringen. Azure AD loggar registrering av FIDO2-säkerhetsenheter och byter till autentiseringsmetoder av en användare.

### <a name="plan-security"></a>Planera säkerhet
Som en del av den här distributions planen rekommenderar Microsoft att lösen ords lös autentisering aktive ras för alla privilegierade administratörs konton.

När användarna aktiverar eller inaktiverar kontot för en säkerhets nyckel eller återställer den andra faktorn för säkerhets nyckeln på sina Windows 10-datorer, läggs en post till i säkerhets loggen och anges under följande händelse-ID: *4670* och *5382*.

### <a name="plan-testing"></a>Planera testning

Se till att resultaten är som förväntat i varje steg i distributionen när du testar scenarier och antar dem.

#### <a name="testing-the-microsoft-authenticator-app"></a>Testa Microsoft Authenticator-appen

Följande är exempel test fall för autentisering utan lösen ord med Microsoft Authenticator-appen:

| Scenario | Förväntat resultat |
| --- | --- |
| Användaren kan registrera Microsoft Authenticator app | Användaren kan registrera appen från aka.ms/mysecurityinfo |
| Användaren kan aktivera telefonin loggning | Telefonin loggning har kon figurer ATS för arbets konto |
| Användaren har åtkomst till en app med telefonin loggning | Användaren går igenom telefonens inloggnings flöde och når programmet. |
| Testa återställning av telefonin loggning genom att inaktivera Microsoft Authenticator lösen ords återställning på skärmen autentiseringsmetoder i Azure Active Directory portalen | Tidigare aktiverade användare kan inte använda lösen ords lös inloggning från Microsoft Authenticator. |
| Ta bort telefonin loggning från Microsoft Authenticator app | Arbets kontot är inte längre tillgängligt på Microsoft Authenticator |

#### <a name="testing-security-keys"></a>Testa säkerhets nycklar

Följande är exempel test fall för lösen ords utan autentisering med säkerhets nycklar.

**Lösen ords lös FIDO-inloggning till Azure Active Directory anslutna Windows 10-enheter**

| Scenario | Förväntat resultat |
| --- | --- |
| Användaren kan registrera FIDO2-enheten (1809) | Användaren kan registrera FIDO2-enheten med inställningarna > konton > inloggnings alternativ > säkerhets nyckel |
| Användaren kan återställa FIDO2-enheten (1809) | Användaren kan återställa FIDO2-enheten med hjälp av program vara för tillverkare |
| Användaren kan logga in med FIDO2-enheten (1809) | Användaren kan välja säkerhets nyckel från inloggnings fönstret och logga in. |
| Användaren kan registrera FIDO2-enheten (1903) | Användaren kan registrera FIDO2-enheten på Inställningar > konton > inloggning alternativ > säkerhets nyckel |
| Användaren kan återställa FIDO2-enheten (1903) | Användaren kan återställa FIDO2-enheten på Inställningar > konton > inloggning alternativ > säkerhets nyckel |
| Användaren kan logga in med FIDO2-enheten (1903) | Användaren kan välja säkerhets nyckel från inloggnings fönstret och logga in. |

**Lösen ords lös FIDO-inloggning till Azure AD-webbappar**

| Scenario | Förväntat resultat |
| --- | --- |
| Användaren kan registrera FIDO2-enheten på aka.ms/mysecurityinfo med Microsoft Edge | Registreringen ska lyckas |
| Användaren kan registrera FIDO2-enheten på aka.ms/mysecurityinfo med hjälp av Firefox | Registreringen ska lyckas |
| Användaren kan logga in på OneDrive online med FIDO2-enhet med Microsoft Edge | Inloggningen ska lyckas |
| Användaren kan logga in på OneDrive online med FIDO2-enhet med hjälp av Firefox | Inloggningen ska lyckas |
| Testa att återställa FIDO2 enhets registrering genom att inaktivera FIDO2-säkerhetsnycklar i fönstret autentiseringsmetod i Azure Active Directory-portalen | Användarna uppmanas att logga in med sina säkerhets nycklar. Användarna kommer att logga in och ett fel visas: "företags principen kräver att du använder en annan metod för att logga in". Användarna bör sedan kunna välja en annan metod och logga in. Stäng fönstret och logga in igen för att kontrol lera att de inte ser samma fel meddelande. |

### <a name="plan-for-rollback"></a>Planera för återställning

Även om lösenordsbaserad autentisering är en förenklad funktion med minimal påverkan på slutanvändare kan det vara nödvändigt att återställa igen.

Om du återställer måste administratören logga in på Azure Active Directory-portalen, välja önskade metoder för stark autentisering och ändra alternativet Aktivera till **Nej**. Den här processen stänger av lösen ords lös funktioner för alla användare.

Användare som redan har registrerat FIDO2 säkerhets enheter uppmanas att använda säkerhetsenheten vid nästa inloggning och sedan se följande fel:

![Välj ett annat sätt att logga in på](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>Distribuera och felsöka lösenordsbaserad autentisering

Följ stegen som är anpassade till den valda metoden nedan.

### <a name="required-administrative-roles"></a>Administrativa roller som krävs

| Azure AD-roll | Beskrivning |
| --- | --- |
| Global administratör|Minst privilegie rad roll kan implementera kombinerad registrerings upplevelse. |
| Administratör för autentisering | Minst privilegie rad roll kan implementera och hantera autentiseringsmetoder. |
| User | Minst privilegie rad roll för att konfigurera Authenticator-appen på enheten eller för att registrera säkerhets nyckel enhet för webb-eller Windows 10-inloggning. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Distribuera telefonin loggning med Microsoft Authenticator-appen

Följ anvisningarna i artikeln [Aktivera lösen ords lös inloggning med Microsoft Authenticator-appen](howto-authentication-passwordless-phone.md) för att aktivera Microsoft Authenticator-appen som en metod för lösen ords lös autentisering i din organisation.

### <a name="deploy-fido2-security-key-sign-in"></a>Distribuera FIDO2-inloggning för säkerhets nyckel

Följ stegen i artikeln, [Aktivera lösen ords lös säkerhets nyckel logga in för Azure AD](howto-authentication-passwordless-security-key.md) för att aktivera FIDO2-säkerhetsnycklar som lösen ords skydds metoder.

### <a name="troubleshoot-phone-sign-in"></a>Felsöka telefonin loggning

| Scenario | Lösning |
| --- | --- |
| Användaren kan inte utföra en kombinerad registrering. | Se till att [kombinerad registrering](concept-registration-mfa-sspr-combined.md) är aktive rad. |
| Användaren kan inte aktivera appen för telefon inloggnings verifiering. | Se till att användaren är inom omfånget för distribution. |
| Användaren är inte inom omfånget för lösen ords full autentisering, men visas med alternativet för lösen ords full inloggning, vilket inte kan slutföras. | Det här scenariot inträffar när användaren har aktiverat telefonin loggning i programmet innan principen skapas. <br> *Aktivera inloggning*: Lägg till användaren i omfånget användare som Aktiver ATS för lösen ords lös inloggning. <br> *För att blockera inloggning*: be användaren ta bort sina autentiseringsuppgifter från det programmet. |

### <a name="troubleshoot-security-key-sign-in"></a>Felsöka inloggning av säkerhets nycklar

| Scenario | Lösning |
| --- | --- |
| Användaren kan inte utföra en kombinerad registrering. | Se till att [kombinerad registrering](concept-registration-mfa-sspr-combined.md) är aktive rad. |
| Användaren kan inte lägga till en säkerhets nyckel i sina [säkerhets inställningar](https://aka.ms/mysecurityinfo). | Se till att [säkerhets nycklar](howto-authentication-passwordless-security-key.md) är aktiverade. |
| Användaren kan inte lägga till säkerhets nyckel i inloggnings alternativ för Windows 10. | [Kontrol lera att säkerhets nycklar för Windows-inloggning](./concept-authentication-passwordless.md) |
| **Fel meddelande**: vi har upptäckt att den här webbläsaren eller operativ systemet inte stöder FIDO2 säkerhets nycklar. | FIDO2-säkerhetsenheter kan bara registreras i webbläsare som stöds (Microsoft Edge, Firefox version 67) på Windows 10 version 1809 eller senare. |
| **Fel meddelande**: företags principen kräver att du använder en annan metod för att logga in. | Se till att säkerhets nycklar är aktiverade i klient organisationen. |
| Användaren kan inte hantera min säkerhets nyckel i Windows 10 version 1809 | Version 1809 kräver att du använder program varan för hantering av säkerhets nycklar som tillhandahålls av FIDO2 Key-leverantören. Kontakta leverantören för support. |
| Jag tror att min FIDO2-säkerhetsnyckel kan vara defekt, hur kan jag testa den. | Gå till [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/) , ange autentiseringsuppgifter för ett test konto, Anslut den misstänkta säkerhets nyckeln, Välj **+** knappen längst upp till höger på skärmen, klicka på Skapa och gå igenom processen. Om det här scenariot Miss lyckas kan det bero på att enheten är skadad. |

## <a name="next-steps"></a>Nästa steg

- [Aktivera lösen ords skydds nycklar för inloggning för Azure AD](howto-authentication-passwordless-security-key.md)
- [Aktivera inloggning med lösen ord med Microsoft Authenticator-appen](howto-authentication-passwordless-phone.md)
- [Lär dig mer om autentiseringsmetoder användning & insikter](howto-authentication-methods-usage-insights.md)
