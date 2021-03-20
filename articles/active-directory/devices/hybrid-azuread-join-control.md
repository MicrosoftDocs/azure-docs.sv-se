---
title: Kontrollerad verifiering av hybrid Azure AD-anslutning – Azure AD
description: Lär dig hur du gör en kontrollerad validering av hybrid Azure AD Join innan du aktiverar den i hela organisationen samtidigt
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38754b9e349e27afcff58dac27a616e3e4fb5319
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96860940"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Kontrollerad verifiering av Azure AD-anslutningshybrid

När alla krav är på plats registreras Windows-enheter automatiskt som enheter i din Azure AD-klient. Status för dessa enhets identiteter i Azure AD kallas för Hybrid Azure AD-anslutning. Mer information om de begrepp som beskrivs i den här artikeln finns i artiklarna [Introduktion till enhets hantering i Azure Active Directory](overview.md) och [Planera hybrid Azure Active Directory Join-implementering](hybrid-azuread-join-plan.md).

Organisationer kan vilja göra en kontrollerad validering av hybrid Azure AD-anslutning innan den aktive ras i hela organisationen samtidigt. Den här artikeln förklarar hur du utför en kontrollerad validering av hybrid Azure AD-anslutning.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Kontrollerad verifiering av hybrid Azure AD-anslutning på aktuella Windows-enheter

För enheter som kör Windows Skriv bords operativ system är den version som stöds den Windows 10-jubileums uppdatering (version 1607) eller senare. Vi rekommenderar att du uppgraderar till den senaste versionen av Windows 10.

Om du vill göra en kontrollerad verifiering av hybrid Azure AD-anslutning på Windows-enheter måste du:

1. Rensa posten för tjänst anslutnings punkten (SCP) från Active Directory (AD) om den finns
1. Konfigurera register inställningen på klient sidan för SCP på domänanslutna datorer med hjälp av ett grupprincip objekt (GPO)
1. Om du använder AD FS måste du också konfigurera register inställningen på klient sidan för SCP på din AD FS-server med hjälp av ett grup princip objekt  
1. Du kan också behöva [Anpassa synkroniseringsalternativ](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect) i Azure AD Connect för att aktivera synkronisering av enheten. 


### <a name="clear-the-scp-from-ad"></a>Rensa SCP från AD

Använd redigeraren för Active Directory Services-gränssnitt (ADSI Edit) för att ändra SCP-objekt i AD.

1. Starta **ADSI Edit** Desktop-appen från och den administrativa arbets stationen eller en domänkontrollant som företags administratör.
1. Anslut till domänens **konfigurations namngivnings kontext** .
1. Bläddra till **CN = Configuration, DC = contoso, DC = com**  >  **CN = Services**  >  **CN = enhets registrerings konfiguration**
1. Högerklicka på objektet lövnod **CN = 62a0ff2e-97B9-4513-943F-0d221bd30080** och välj **Egenskaper**
   1. Välj **nyckelord** i fönstret **Redigera attribut** och klicka på **Redigera**
   1. Välj värdena för **azureADId** och **azureADName** (en i taget) och klicka på **ta bort**
1. Stäng **ADSI Edit**


### <a name="configure-client-side-registry-setting-for-scp"></a>Konfigurera register inställningen på klient sidan för SCP

Använd följande exempel för att skapa ett grupprincip objekt (GPO) för att distribuera en register inställning som konfigurerar en SCP-post i registret för dina enheter.

1. Öppna en grupprincip hanterings konsol och skapa ett nytt grupprincip-objekt i din domän.
   1. Ange ett namn för ditt nyligen skapade grup princip objekt (till exempel ClientSideSCP).
1. Redigera grupprincipobjektet och leta upp följande sökväg: **dator konfigurations**  >  **Inställningar**  >  **Windows-inställningar**  >  **register**
1. Högerklicka på registret och välj **nytt**  >  **register objekt**
   1. På fliken **Allmänt** konfigurerar du följande
      1. Åtgärd: **Uppdatera**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Nyckel Sök väg: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Värde namn: **TenantId**
      1. Värdetyp: **REG_SZ**
      1. Värde data: GUID eller **katalog-ID** för din Azure AD-instans (detta värde finns i **Azure Portal**  >  **Azure Active Directory**  >  **Egenskaper**  >  **katalog-ID**)
   1. Klicka på **OK**
1. Högerklicka på registret och välj **nytt**  >  **register objekt**
   1. På fliken **Allmänt** konfigurerar du följande
      1. Åtgärd: **Uppdatera**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Nyckel Sök väg: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Värde namn: **TenantName**
      1. Värdetyp: **REG_SZ**
      1. Värde data: ditt verifierade **domän namn** om du använder federerade miljöer som AD FS. Ditt verifierade **domän namn** eller ditt onmicrosoft.com domän namn till exempel `contoso.onmicrosoft.com` om du använder en hanterad miljö
   1. Klicka på **OK**
1. Stäng Redigeraren för det nyligen skapade GRUPPRINCIPOBJEKTet
1. Länka det nyligen skapade GRUPPRINCIPOBJEKTet till den önskade ORGANISATIONSENHETen som innehåller domänanslutna datorer som tillhör din kontrollerade installations population

### <a name="configure-ad-fs-settings"></a>Konfigurera AD FS inställningar

Om du använder AD FS måste du först konfigurera klient sidans SCP med hjälp av anvisningarna ovan genom att länka GRUPPRINCIPOBJEKTet till dina AD FS-servrar. SCP-objektet definierar källan för auktoritet för enhets objekt. Det kan vara lokalt eller i Azure AD. När klient sidans SCP har kon figurer ATS för AD FS, upprättas källan för enhets objekt som Azure AD.

> [!NOTE]
> Om du inte kunde konfigurera klient sidans SCP på dina AD FS-servrar anses källan för enhets identiteter vara lokalt. AD FS börjar sedan ta bort enhets objekt från den lokala katalogen efter den angivna perioden som definieras i AD FS-enhetens registrerings attribut "MaximumInactiveDays". ADFS Device Registration-objekt kan hittas med [cmdleten Get-AdfsDeviceRegistration](/powershell/module/adfs/get-adfsdeviceregistration).

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Kontrollerad verifiering av hybrid Azure AD-anslutning på Windows-enheter på nivån

För att registrera Windows-enheter på låg nivå måste organisationerna installera [Microsoft Workplace Join för icke-Windows 10-datorer som](https://www.microsoft.com/download/details.aspx?id=53554) är tillgängliga på Microsoft Download Center.

Du kan distribuera paketet med hjälp av ett program distributions system som [Microsoft Endpoint Configuration Manager](/configmgr/). Paketet stöder vanliga obevakade installations alternativ med parametern quiet. Den aktuella grenen av Configuration Manager erbjuder förmåner jämfört med tidigare versioner, t. ex. möjligheten att spåra slutförda registreringar.

Installations programmet skapar en schemalagd aktivitet på det system som körs i användar kontexten. Aktiviteten utlöses när användaren loggar in i Windows. Uppgiften ansluter tyst till enheten med Azure AD med användarautentiseringsuppgifterna efter autentisering med Azure AD.

Om du vill kontrol lera enhets registreringen bör du distribuera Windows Installer-paketet till den valda gruppen av Windows-enheter på nivån.

> [!NOTE]
> Om en SCP inte har kon figurer ATS i AD bör du följa samma metod som beskrivs i [Konfigurera register inställningen på klient sidan för SCP](#configure-client-side-registry-setting-for-scp)på domänanslutna datorer med hjälp av ett Grupprincip objekt (GPO).


När du har kontrollerat att allt fungerar som förväntat kan du automatiskt registrera resten av dina Windows-aktuella och äldre enheter med Azure AD genom att [Konfigurera SCP med hjälp av Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Nästa steg

[Planera implementeringen av Azure Active Directory-hybridanslutning](hybrid-azuread-join-plan.md)
