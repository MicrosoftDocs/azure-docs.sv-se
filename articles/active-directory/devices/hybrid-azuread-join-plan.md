---
title: Planera hybrid Azure Active Directory Join – Azure Active Directory
description: Lär dig att konfigurera Hybrid Azure Active Directory-anslutningsenheter.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: cadba181ea7d6a12ca64c78f3c7c58654d5f756f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500816"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Gör så här: planera din hybrid Azure Active Directory delta-implementering

På ett liknande sätt som en användare är en enhet en annan kärn identitet som du vill skydda och använda den för att skydda dina resurser när som helst och var som helst. Du kan uppnå det här målet genom att placera och hantera enhets identiteter i Azure AD med någon av följande metoder:

- Azure Active Directory-anslutning
- Hybrid Azure Active Directory-anslutning
- Azure Active Directory-registrering

När du börjar använda dina enheter med Azure Active Directory maximerar du användarnas produktivitet med enkel inloggning (SSO) mellan dina molnresurser och lokala resurser. På samma gång kan du skydda åtkomsten till molnet och lokala resurser med [villkorlig åtkomst](../conditional-access/overview.md).

Om du har en lokal Active Directory (AD)-miljö och vill ansluta till dina AD-domänanslutna datorer till Azure AD kan du göra detta genom att göra en hybrid Azure AD-anslutning. Den här artikeln innehåller relaterade steg för att implementera en hybrid Azure AD-anslutning i din miljö. 

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med [introduktionen till enhets identitets hantering i Azure Active Directory](./overview.md).

> [!NOTE]
> Den minsta nödvändiga domänkontrollanten för Windows 10 hybrid Azure AD Join är Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Planera implementeringen

För att planera din hybrid Azure AD-implementering bör du bekanta dig med:

> [!div class="checklist"]
> - Granska enheter som stöds
> - Granska saker du bör känna till
> - Granska kontrollerad validering av hybrid Azure AD-anslutning
> - Välj ditt scenario baserat på din identitets infrastruktur
> - Granska lokala AD UPN-stöd för Hybrid Azure AD-anslutning

## <a name="review-supported-devices"></a>Granska enheter som stöds

Hybrid Azure AD-anslutning har stöd för ett brett utbud av Windows-enheter. Eftersom konfigurationen för enheter som kör äldre versioner av Windows kräver ytterligare eller olika steg, grupperas de enheter som stöds i två kategorier:

### <a name="windows-current-devices"></a>Aktuella Windows-enheter

- Windows 10
- Windows Server 2016
  - **Obs!** Azure National Cloud-kunder kräver version 1803
- Windows Server 2019

För enheter som kör operativ systemet Windows-skrivbordet visas den version som stöds i den här artikeln [information om Windows 10-utgåvor](/windows/release-information/). Som bästa praxis rekommenderar Microsoft att du uppgraderar till den senaste versionen av Windows 10.

### <a name="windows-down-level-devices"></a>Windows-enheter på nivån

- Windows 8,1
- Support för Windows 7 upphörde 14 januari 2020. Mer information finns i [Support för Windows 7 har avslut ATS](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Information om support för Windows Server 2008 och 2008 R2 finns i [förbereda för Windows server 2008-slut för support](https://www.microsoft.com/cloud-platform/windows-server-2008).

Som första planerings steg bör du granska din miljö och avgöra om du behöver stöd för Windows-enheter på äldre nivå.

## <a name="review-things-you-should-know"></a>Granska saker du bör känna till

### <a name="unsupported-scenarios"></a>Scenarier som inte stöds
- Hybrid Azure AD Join stöds inte för närvarande om din miljö består av en enda AD-skog som synkroniserar identitets data till mer än en Azure AD-klient.

- Hybrid Azure AD Join stöds inte för Windows Server som kör rollen domänkontrollant (DC).

- Hybrid Azure AD-anslutning stöds inte på Windows-enheter med äldre versioner vid användning av centrala autentiseringsuppgifter eller centrala profiler för användar profiler.

- Server Core OS stöder inte någon typ av enhets registrering.

- User State Migration Tool (USMT) fungerar inte med enhets registrering.  

### <a name="os-imaging-considerations"></a>Överväganden för OS-avbildningar
- Om du förlitar dig på system förberedelse verktyget (Sysprep) och om du använder en avbildning med **tidigare versioner än Windows 10 1809** för installation kontrollerar du att avbildningen inte är från en enhet som redan har registrerats med Azure AD som en hybrid Azure AD-anslutning.

- Om du förlitar dig på en ögonblicks bild av en virtuell dator (VM) för att skapa ytterligare virtuella datorer, se till att ögonblicks bilden inte är från en virtuell dator som redan är registrerad i Azure AD som en hybrid Azure AD-anslutning.

- Om du använder [enhetligt Skriv filter](/windows-hardware/customize/enterprise/unified-write-filter) och liknande tekniker som rensar ändringar på disken vid omstart måste de tillämpas när enheten är hybrid Azure AD-ansluten. Om du aktiverar sådana tekniker innan du slutförde en hybrid Azure AD-anslutning kommer enheten att bli frånkopplad vid varje omstart

### <a name="handling-devices-with-azure-ad-registered-state"></a>Hantera enheter med registrerade Azure AD-tillstånd
Om dina Windows 10-domänanslutna enheter är [registrerade i Azure AD](overview.md#getting-devices-in-azure-ad) till din klient organisation, kan det leda till ett dubbelt tillstånd med hybrid Azure AD-anslutna och en registrerad Azure AD-enhet. Vi rekommenderar att du uppgraderar till Windows 10 1803 (med KB4489894 installerat) eller senare för att automatiskt hantera det här scenariot. I pre-1803-versioner måste du ta bort Azure AD-registrerat tillstånd manuellt innan du aktiverar hybrid Azure AD Join. I 1803 och senare versioner har följande ändringar gjorts för att undvika detta dubbla tillstånd:

- Alla befintliga Azure AD-registrerade tillstånd för en användare tas bort automatiskt <i>när enheten är hybrid-Azure AD-ansluten och samma användare loggar in</i>. Om användaren till exempel hade ett registrerat Azure AD-tillstånd på enheten rensas det dubbla läget för användare A endast när användaren loggar in på enheten. Om det finns flera användare på samma enhet rensas det dubbla läget individuellt när användarna loggar in. Förutom att ta bort det registrerade Azure AD-läget avregistrerar Windows 10 även enheten från Intune eller andra MDM, om registreringen skedde som en del av Azure AD-registreringen via automatisk registrering.
- Azure AD-registrerat tillstånd på lokala konton på enheten påverkas inte av den här ändringen. Den kan bara användas för domän konton. Därför tas inte Azure AD-registrerade tillstånd på lokala konton bort automatiskt, även efter användar inloggningen, eftersom användaren inte är en domän användare. 
- Du kan förhindra att din domänanslutna enhet är registrerad i Azure AD genom att lägga till följande register värde i HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin: "BlockAADWorkplaceJoin" = DWORD: 00000001.
- Om du har konfigurerat Windows Hello för företag i Windows 10 1803 måste användaren konfigurera Windows Hello för företag igen när dubbelt tillstånd rensas. Det här problemet har åtgärd ATS med KB4512509

> [!NOTE]
> Även om Windows 10 automatiskt tar bort Azure AD-registrerade tillstånd lokalt, raderas inte enhets objekt i Azure AD omedelbart om det hanteras av Intune. Du kan verifiera borttagning av Azure AD-registrerat tillstånd genom att köra dsregcmd/status och se till att enheten inte är registrerad som Azure AD baserat på den.

### <a name="additional-considerations"></a>Annat som är bra att tänka på
- Om din miljö använder VDI (Virtual Desktop Infrastructure), se [enhets identitet och skriv bords virtualisering](./howto-device-identity-virtual-desktop-infrastructure.md).

- Hybrid Azure AD-anslutning stöds för FIPS-kompatibla TPM 2,0 och stöds inte för TPM 1,2. Om dina enheter har FIPS-kompatibel TPM 1,2 måste du inaktivera dem innan du fortsätter med hybrid Azure AD-anslutning. Microsoft tillhandahåller inga verktyg för att inaktivera FIPS-läge för TPM eftersom det är beroende av TPM-tillverkaren. Kontakta maskin varans OEM om du vill ha hjälp. 

- Från och med Windows 10 1903-versionen används inte TPM 1,2 med hybrid Azure AD-anslutning och enheter med de här TPM: erna anses som om de inte har någon TPM.

- UPN-ändringar stöds bara vid start av Windows 10 2004 Update. För enheter före uppdatering av Windows 10 2004 skulle användare ha SSO-och villkorliga åtkomst problem på sina enheter. För att lösa det här problemet måste du koppla från enheten från Azure AD (kör "dsregcmd/Leave" med utökade privilegier) och återansluta (sker automatiskt). Användare som loggar in med Windows Hello för företag möter dock inte det här problemet.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Granska kontrollerad validering av hybrid Azure AD-anslutning

När alla krav är på plats registreras Windows-enheter automatiskt som enheter i din Azure AD-klient. Status för dessa enhets identiteter i Azure AD kallas för Hybrid Azure AD-anslutning. Mer information om de begrepp som beskrivs i den här artikeln finns i artikeln [Introduktion till enhets identitets hantering i Azure Active Directory](overview.md).

Organisationer kan vilja göra en kontrollerad validering av hybrid Azure AD-anslutning innan den aktive ras i hela organisationen samtidigt. Läs igenom artikeln [kontrollerad verifiering av hybrid Azure AD-anslutning](hybrid-azuread-join-control.md) för att förstå hur du utför den.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Välj ditt scenario baserat på din identitets infrastruktur

Hybrid Azure AD Join fungerar med både hanterade och federerade miljöer beroende på om UPN är dirigerbart eller inte. Se slutet på sidan för tabell i scenarier som stöds.  

### <a name="managed-environment"></a>Hanterad miljö

En hanterad miljö kan distribueras antingen via [PHS (Password hash Sync)](../hybrid/whatis-phs.md) eller [genom strömning (PTA)](../hybrid/how-to-connect-pta.md) med [sömlös enkel inloggning](../hybrid/how-to-connect-sso.md).

De här scenarierna kräver inte att du konfigurerar en Federations Server för autentisering.

> [!NOTE]
> [Molnbaserad autentisering med stegvis](../hybrid/how-to-connect-staged-rollout.md) distribution stöds endast vid start av Windows 10 1903 Update

### <a name="federated-environment"></a>Federerad miljö

En federerad miljö bör ha en identitetsprovider som uppfyller följande krav. Om du har en federerad miljö som använder Active Directory Federation Services (AD FS) (AD FS) stöds redan nedanstående krav.

- **WIAORMULTIAUTHN-anspråk:** Detta anspråk krävs för att göra en hybrid Azure AD-anslutning för Windows-enheter på hög nivå.
- **WS-Trust-protokoll:** Det här protokollet krävs för att autentisera Windows aktuella hybrid Azure AD-anslutna enheter med Azure AD. När du använder AD FS måste du aktivera följande WS-Trust slut punkter: `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Både **ADFS/tjänster/Trust/2005/windowstransport** eller **adfs/services/trust/13/windowstransport** ska aktive ras som enbart intranät riktade slut punkter och får inte visas som extra näts slut punkter via webbprogramproxy. Mer information om hur du inaktiverar WS-Trust slut punkter i Windows finns i [inaktivera WS-Trust Windows-slutpunkter på proxyn](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Du kan se vilka slut punkter som aktive ras via AD FS hanterings konsolen under **tjänst**  >  **slut punkter**.

> [!NOTE]
> Azure AD har inte stöd för smartkort eller certifikat i hanterade domäner.

Från och med version 1.1.819.0 tillhandahåller Azure AD Connect en guide för konfiguration av Hybrid Azure AD-anslutning. Med guiden kan du förenkla konfigurationsprocessen avsevärt. Om du installerar den nödvändiga versionen av Azure AD Connect inte är ett alternativ för dig, kan du läsa mer i [Konfigurera enhets registrering manuellt](hybrid-azuread-join-manual.md). 

Baserat på scenariot som matchar din identitets infrastruktur, se:

- [Konfigurera hybrid Azure Active Directory anslutning för federerad miljö](hybrid-azuread-join-federated-domains.md)
- [Konfigurera hybrid Azure Active Directorys anslutning för hanterad miljö](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>Granska lokala AD-användares UPN-stöd för Hybrid Azure AD-anslutning

Ibland kan dina lokala AD-användares UPN skilja sig från dina Azure AD-UPN. I sådana fall ger Windows 10 hybrid Azure AD Join begränsat stöd för lokala AD-UPN: er baserat på [autentiseringsmetoden](../hybrid/choose-ad-authn.md), domän typen och Windows 10-versionen. Det finns två typer av lokala AD-UPN: er som kan finnas i din miljö:

- Dirigera användarens UPN: ett dirigerbart UPN har en giltig verifierad domän som är registrerad hos en domän registrator. Om contoso.com till exempel är den primära domänen i Azure AD, är contoso.org den primära domänen i den lokala AD som ägs av Contoso och [verifierats i Azure AD](../fundamentals/add-custom-domain.md)
- UPN för icke-dirigerbart användare: ett icke-dirigerbart UPN har ingen verifierad domän. Den kan bara användas inom din organisations privata nätverk. Om contoso.com till exempel är den primära domänen i Azure AD, är contoso. local den primära domänen i den lokala AD-domänen, men är inte en verifierbar domän på Internet och används endast i Contosos nätverk.

> [!NOTE]
> Informationen i det här avsnittet gäller endast för lokala användares UPN. Den gäller inte för en lokal dators domänsuffix (exempel: DATOR1. contoso. local).

Tabellen nedan innehåller information om stöd för dessa lokala AD-UPN i Windows 10 hybrid Azure AD Join

| Typ av lokalt AD-UPN | Domäntyp | Windows 10-version | Beskrivning |
| ----- | ----- | ----- | ----- |
| Dirigera | Federerade | Från 1703-version | Allmänt tillgänglig |
| Ej dirigerbart | Federerade | Från 1803-version | Allmänt tillgänglig |
| Dirigera | Hanterad | Från 1803-version | Azure AD SSPR på Windows-låsskärm stöds inte allmänt tillgänglig. Det lokala UPN: en måste synkroniseras med     `onPremisesUserPrincipalName` attributet i Azure AD |
| Ej dirigerbart | Hanterad | Stöds inte | |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera hybrid Azure Active Directory anslutning för federerad miljö](hybrid-azuread-join-federated-domains.md) 
>  [Konfigurera hybrid Azure Active Directorys anslutning för hanterad miljö](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
