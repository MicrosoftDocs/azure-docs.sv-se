---
title: Konfigurera en Azure Active Directory-hybridanslutning för federerade domäner | Microsoft Docs
description: Lär dig hur du konfigurerar Azure Active Directory-hybridanslutningar för federerade domäner.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221b7bdbb8ab5d0121e9c8032be8f18d8ae60d1e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578064"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Självstudier: Konfigurera Hybrid Azure Active Directory-anslutningar för federerade domäner

Som en användare i din organisation är en enhet en kärn identitet som du vill skydda. Du kan använda en enhets identitet för att skydda dina resurser när du vill och var som helst. Du kan uppnå det här målet genom att ta med enhets identiteter och hantera dem i Azure Active Directory (Azure AD) med hjälp av någon av följande metoder:

- Azure Active Directory-anslutning
- Hybrid Azure Active Directory-anslutning
- Azure Active Directory-registrering

Genom att överföra dina enheter till Azure AD kan du maximera användar produktiviteten genom enkel inloggning (SSO) i molnet och lokala resurser. Du kan skydda åtkomsten till molnet och lokala resurser med [villkorlig åtkomst](../conditional-access/howto-conditional-access-policy-compliant-device.md) på samma gång.

En federerad miljö bör ha en identitetsprovider som uppfyller följande krav. Om du har en federerad miljö som använder Active Directory Federation Services (AD FS) (AD FS) stöds redan nedanstående krav.

- **WIAORMULTIAUTHN-anspråk:** Detta anspråk krävs för att göra en hybrid Azure AD-anslutning för Windows-enheter på hög nivå.
- **WS-Trust-protokoll:** Det här protokollet krävs för att autentisera Windows aktuella hybrid Azure AD-anslutna enheter med Azure AD.
  När du använder AD FS måste du aktivera följande WS-Trust slut punkter: `/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Både **ADFS/tjänster/Trust/2005/windowstransport** och **adfs/services/trust/13/windowstransport** ska aktive ras som enbart intranät riktade slut punkter och får inte visas som extra näts slut punkter via webbprogramproxy. Mer information om hur du inaktiverar WS-Trust slut punkter i Windows finns i [inaktivera WS-Trust Windows-slutpunkter på proxyn](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Du kan se vilka slut punkter som aktive ras via AD FS hanterings konsolen under **tjänst**  >  **slut punkter**.

I den här självstudien får du lära dig hur du konfigurerar hybrid Azure AD-anslutning för Active Directory domänanslutna datorer enheter i en federerad miljö med hjälp av AD FS.

Lär dig att:

> [!div class="checklist"]
> * Konfigurera Hybrid Azure Active Directory-anslutning
> * Aktivera äldre Windows-enheter
> * Verifiera registreringen
> * Felsöka

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien förutsätter att du är bekant med de här artiklarna:

- [Vad är en enhetsidentitet?](overview.md)
- [Planera din hybrid Azure AD Join-implementering](hybrid-azuread-join-plan.md)
- [Så här gör du en kontrollerad verifiering av hybrid Azure AD-anslutning](hybrid-azuread-join-control.md)

För att kunna konfigurera scenariot i den här självstudien behöver du:

- Windows Server 2012 R2 med AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) version 1.1.819.0 eller senare

Från och med version 1.1.819.0 innehåller Azure AD Connect en guide som du kan använda för att konfigurera hybrid Azure AD-anslutning. Guiden fören klar konfigurations processen avsevärt. Relaterad guide:

- Konfigurerar tjänst anslutnings punkter (SCP: r) för enhets registrering
- Säkerhetskopierar ditt befintliga förlitande part-förtroende för Azure AD
- Uppdaterar anspråksreglerna i ditt Azure AD-förtroende

Konfigurations stegen i den här artikeln baseras på hur du använder guiden Azure AD Connect. Om du har en tidigare version av Azure AD Connect installerad måste du uppgradera den till 1.1.819 eller senare för att kunna använda guiden. Om du installerar den senaste versionen av Azure AD Connect inte är ett alternativ för dig, kan du läsa mer i [Konfigurera hybrid Azure AD-anslutning manuellt](hybrid-azuread-join-manual.md).

Hybrid Azure AD-anslutning kräver att enheter har åtkomst till följande Microsoft-resurser inifrån din organisations nätverk:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Din organisations säkerhetstokentjänst (STS) (för federerade domäner)
- `https://autologon.microsoftazuread-sso.com` (Om du använder eller planerar att använda sömlös enkel inloggning)

> [!WARNING]
> Om din organisation använder proxyservrar som fångar upp SSL-trafik för scenarier som förebyggande av data förlust eller begränsningar för Azure AD-innehavare, kontrollerar du att trafik till ( https://device.login.microsoftonline.com ) är exkluderad från TLS-och-undersök. Det går inte att utesluta " https://device.login.microsoftonline.com " kan orsaka störningar med autentisering av klient certifikat, vilket orsakar problem med enhets registrering och enhets-baserad villkorlig åtkomst.

Från och med Windows AD FS 10 1803 förlitar vi sig på Azure AD Connect för att synkronisera datorobjektet i Azure AD som sedan används för att slutföra enhets registreringen för Hybrid Azure AD Join. Kontrol lera att Azure AD Connect har synkroniserat dator objekt för de enheter som du vill ska vara hybrid Azure AD-anslutna till Azure AD. Om datorns objekt tillhör vissa organisationsenheter (OU) måste du även konfigurera organisationsenheterna för synkronisering i Azure AD Connect. Mer information om hur du synkroniserar dator objekt med hjälp av Azure AD Connect finns i [Konfigurera filtrering med hjälp av Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

> [!NOTE]
> Om du vill hämta synkroniseringen av enhets registrering för att lyckas, som en del av enhets registrerings konfigurationen, ska du inte utesluta standardattributen för enheten från Azure AD Connect Sync-konfigurationen. Om du vill veta mer om standardenhets-attribut som synkroniseras till AAD, se [attribut som synkroniseras med Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized#windows-10).

Om din organisation kräver åtkomst till Internet via en utgående proxy rekommenderar Microsoft att [implementera WPAD (Web Proxy Auto-Discovery)](/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) för att aktivera Windows 10-datorer för enhets registrering med Azure AD. Om du stöter på problem med att konfigurera och hantera WPAD, se [Felsöka automatisk identifiering](/previous-versions/tn-archive/cc302643(v=technet.10)). 

Om du inte använder WPAD och vill konfigurera proxyinställningar på datorn kan du göra det från och med Windows 10 1709. Mer information finns i [Konfigurera WinHTTP-inställningar med hjälp av ett grup princip objekt (GPO)](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo).

> [!NOTE]
> Om du konfigurerar proxyinställningar på datorn med hjälp av WinHTTP-inställningar kommer alla datorer som inte kan ansluta till den konfigurerade proxyn inte att kunna ansluta till Internet.

Om din organisation kräver åtkomst till Internet via en autentiserad utgående proxy, måste du se till att dina Windows 10-datorer kan autentiseras mot den utgående proxyn. Eftersom Windows 10-datorer kör enhets registrering med hjälp av dator kontext måste du konfigurera utgående proxyautentisering med hjälp av dator kontext. Kontrollera konfigurationskraven med leverantören av den utgående proxyn.

För att kontrol lera om enheten har åtkomst till ovanstående Microsoft-resurser under system kontot kan du använda anslutnings skriptet för [test av enhets registrering](/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/) .

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurera Hybrid Azure Active Directory-anslutning

Om du vill konfigurera en hybrid Azure AD-anslutning med hjälp av Azure AD Connect behöver du:

- Autentiseringsuppgifterna för en global administratör för din Azure AD-klient  
- Autentiseringsuppgifter för företags administratör för varje skog
- Autentiseringsuppgifterna för AD FS administratören

**Konfigurera en hybrid Azure AD-anslutning med hjälp av Azure AD Connect**:

1. Starta Azure AD Connect och välj sedan **Konfigurera**.

   ![Välkommen](./media/hybrid-azuread-join-federated-domains/11.png)

1. På sidan **Ytterligare aktiviteter** väljer du **Konfigurera enhets alternativ** och väljer sedan **Nästa**.

   ![Ytterligare uppgifter](./media/hybrid-azuread-join-federated-domains/12.png)

1. På sidan **Översikt** väljer du **Nästa**.

   ![Översikt](./media/hybrid-azuread-join-federated-domains/13.png)

1. På sidan **Anslut till Azure AD** anger du autentiseringsuppgifterna för en global administratör för din Azure AD-klient och väljer sedan **Nästa**.

   ![Anslut till Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. På sidan **enhets alternativ** väljer du **Konfigurera hybrid Azure AD-anslutning** och väljer sedan **Nästa**.

   ![Enhetsalternativ](./media/hybrid-azuread-join-federated-domains/15.png)

1. Utför följande steg på sidan **SCP** och välj sedan **Nästa**:

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Välj skogen.
   1. Välj autentiseringstjänst. Du måste välja **AD FS server** om inte din organisation har enbart Windows 10-klienter och du har konfigurerat synkronisering av dator/enhet eller om din organisation använder sömlös SSO.
   1. Välj **Lägg till** för att ange autentiseringsuppgifter för företags administratören.

1. På sidan **enhets operativ system** väljer du de operativ system som enheterna i din Active Directory miljö använder och väljer sedan **Nästa**.

   ![Enhetsoperativsystem](./media/hybrid-azuread-join-federated-domains/17.png)

1. På sidan **Federations konfiguration** anger du autentiseringsuppgifterna för AD FS administratör och väljer sedan **Nästa**.

   ![Federationskonfiguration](./media/hybrid-azuread-join-federated-domains/18.png)

1. På sidan **klar att konfigurera** väljer du **Konfigurera**.

   ![Klart att konfigurera](./media/hybrid-azuread-join-federated-domains/19.png)

1. På sidan **konfigurationen har slutförts** väljer du **Avsluta**.

   ![Konfigurationen är klar](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Aktivera äldre Windows-enheter

Om några av dina domänanslutna enheter är Windows-enheter med äldre enheter måste du:

- Konfigurera inställningarna för det lokala intranätet för enhetsregistrering
- Installera Microsoft Workplace Join för äldre Windows-datorer

> [!NOTE]
> Support för Windows 7 upphörde 14 januari 2020. För mer information [har support för Windows 7 upphört](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurera inställningarna för det lokala intranätet för enhetsregistrering

För att slutföra en fullständig Azure AD-anslutning av dina Windows-enheter med äldre versioner och för att undvika certifikat meddelanden när enheter autentiserar till Azure AD kan du skicka en princip till dina domänanslutna enheter för att lägga till följande URL: er till zonen Lokalt intranät i Internet Explorer:

- `https://device.login.microsoftonline.com`
- Din organisations STS (för federerade domäner)
- `https://autologon.microsoftazuread-sso.com` (För sömlös enkel inloggning)

Du måste också aktivera **Tillåt uppdateringar av statusfältet via skript** i användarens lokala intranät zon.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Installera Microsoft Workplace Join för äldre Windows-datorer

För att registrera Windows-enheter som är äldre måste organisationer installera [Microsoft Workplace Join för icke-Windows 10-datorer](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join för datorer som inte är Windows 10-datorer finns i Microsoft Download Center.

Du kan distribuera paketet med hjälp av ett program distributions system som [Microsoft Endpoint Configuration Manager](/configmgr/). Paketet stöder vanliga obevakade installations alternativ med `quiet` parametern. Den aktuella grenen av Configuration Manager erbjuder förmåner jämfört med tidigare versioner, t. ex. möjligheten att spåra slutförda registreringar.

Installations programmet skapar en schemalagd aktivitet på det system som körs i användar kontexten. Aktiviteten utlöses när användaren loggar in i Windows. Uppgiften ansluter tyst till enheten med Azure AD med hjälp av användarautentiseringsuppgifterna när den har autentiserats med Azure AD.

## <a name="verify-the-registration"></a>Verifiera registreringen

Här är tre sätt att hitta och kontrol lera enhets status:

### <a name="locally-on-the-device"></a>Lokalt på enheten

1. Öppna Windows PowerShell.
2. Ange `dsregcmd /status`.
3. Kontrol lera att både **AzureAdJoined** och **DomainJoined** har angetts till **Ja**.
4. Du kan använda **DeviceID** och jämföra statusen för tjänsten med hjälp av antingen Azure Portal eller PowerShell.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

1. Gå till sidan enheter med en [direkt länk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices).
2. Information om hur du hittar en enhet hittar [du i hantera enhets identiteter med hjälp av Azure Portal](./device-management-azure-portal.md).
3. Om den **registrerade** kolumnen säger **väntar**, slutförs inte hybrid Azure AD Join. I federerade miljöer kan detta bara inträffa om det inte kunde registreras och AAD Connect har kon figurer ATS för att synkronisera enheterna.
4. Om den **registrerade** kolumnen innehåller ett **datum/tid** har hybrid Azure AD Join slutförts.

### <a name="using-powershell"></a>Använda PowerShell

Verifiera enhetens registrerings tillstånd i din Azure-klient med hjälp av **[Get-MsolDevice](/powershell/module/msonline/get-msoldevice)**. Denna cmdlet finns i [Azure Active Directory PowerShell-modulen](/powershell/azure/active-directory/install-msonlinev1).

När du använder cmdleten **Get-MSolDevice** för att kontrol lera tjänst informationen:

- Det måste finnas ett objekt med det **enhets-ID** som matchar ID: t för Windows-klienten.
- Värdet för **DeviceTrustType** **är domänanslutna**. Den här inställningen motsvarar **hybrid Azure AD-anslutna** tillstånd på sidan **enheter** i Azure AD-portalen.
- För enheter som används i villkorlig åtkomst är värdet för **Enabled** **True** och **DeviceTrustLevel** **hanteras**.

1. Öppna Windows PowerShell som administratör.
2. Ange `Connect-MsolService` för att ansluta till din Azure-klient.

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>Räkna alla hybrid Azure AD-anslutna enheter (exklusive **väntande** tillstånd)

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Räkna alla hybrid Azure AD-anslutna enheter med **väntande** tillstånd

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>Lista alla hybrid Azure AD-anslutna enheter

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Lista alla hybrid Azure AD-anslutna enheter med **väntande** tillstånd

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>Visa information om en enskild enhet:

1. Ange `get-msoldevice -deviceId <deviceId>` (detta är den **DeviceID** som hämtades lokalt på enheten).
2. Kontrollera att **Aktiverad** är inställd på **SANT**.

## <a name="troubleshoot-your-implementation"></a>Felsöka din implementering

Om du får problem med att slutföra hybrid Azure AD-anslutning för domänanslutna Windows-enheter, se:

- [Felsöka enheter med kommandot dsregcmd](./troubleshoot-device-dsregcmd.md)
- [Felsöka hybrid Azure AD-anslutning för aktuella Windows-enheter](troubleshoot-hybrid-join-windows-current.md)
- [Felsöka hybrid Azure AD-anslutning för tidigare Windows-enheter](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [hanterar enhets identiteter med hjälp av Azure Portal](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
