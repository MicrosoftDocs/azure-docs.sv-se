---
title: Konfigurera en Azure Active Directory-hybridanslutning för hanterade domäner | Microsoft Docs
description: Konfigurera Azure Active Directory-hybridanslutning för hanterade domäner.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4545e92767a427b8cd89af07ed4d06053685977a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578014"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Självstudie: Konfigurera Azure Active Directory Join-hybrid för hanterade domäner

I den här självstudien får du lära dig hur du konfigurerar hybrid-Azure Active Directory (Azure AD)-anslutning för Active Directory domänanslutna enheter. Den här metoden har stöd för en hanterad miljö som innehåller både lokala Active Directory och Azure AD.

Som en användare i din organisation är en enhet en kärn identitet som du vill skydda. Du kan använda en enhets identitet för att skydda dina resurser när du vill och var som helst. Du kan uppnå det här målet genom att hantera enhets identiteter i Azure AD. Använd någon av följande metoder:

- Azure Active Directory-anslutning
- Hybrid Azure Active Directory-anslutning
- Azure Active Directory-registrering

Den här artikeln fokuserar på Hybrid Azure AD-anslutning.

Genom att överföra dina enheter till Azure AD kan du maximera användar produktiviteten genom enkel inloggning (SSO) i molnet och lokala resurser. Du kan skydda åtkomsten till molnet och lokala resurser med [villkorlig åtkomst](../conditional-access/howto-conditional-access-policy-compliant-device.md) på samma gång.

Du kan distribuera en hanterad miljö med hjälp av [PHS (Password hash Sync)](../hybrid/whatis-phs.md) eller [direktautentisering (PTA)](../hybrid/how-to-connect-pta.md) med [sömlös enkel inloggning](../hybrid/how-to-connect-sso.md). De här scenarierna kräver inte att du konfigurerar en Federations Server för autentisering.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera Hybrid Azure Active Directory-anslutning
> * Aktivera äldre Windows-enheter
> * Verifiera anslutna enheter
> * Felsöka

## <a name="prerequisites"></a>Förutsättningar

- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 eller senare)
- Autentiseringsuppgifterna för en global administratör för din Azure AD-klient
- Autentiseringsuppgifter för företags administratör för varje skog

Bekanta dig med de här artiklarna:

- [Vad är en enhetsidentitet?](overview.md)
- [Gör så här: planera din hybrid Azure Active Directory delta-implementering](hybrid-azuread-join-plan.md)
- [Kontrollerad verifiering av Azure AD-anslutningshybrid](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD stöder inte smartkort eller certifikat i hanterade domäner.

Kontrol lera att Azure AD Connect har synkroniserat dator objekt för de enheter som du vill ska vara hybrid Azure AD-anslutna till Azure AD. Om datorns objekt tillhör vissa organisationsenheter, konfigurerar du organisationsenheterna för synkronisering i Azure AD Connect. Om du vill veta mer om hur du synkroniserar dator objekt med hjälp av Azure AD Connect, se [organisatorisk enhet – baserad filtrering](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

> [!NOTE]
> Om du vill hämta synkroniseringen av enhets registrering för att lyckas, som en del av enhets registrerings konfigurationen, ska du inte utesluta standardattributen för enheten från Azure AD Connect Sync-konfigurationen. Om du vill veta mer om standardenhets-attribut som synkroniseras till AAD, se [attribut som synkroniseras med Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized#windows-10).

Från och med version 1.1.819.0 innehåller Azure AD Connect en guide för att konfigurera hybrid Azure AD-anslutning. Guiden fören klar konfigurations processen avsevärt. Guiden konfigurerar tjänst anslutnings punkter (SCP: r) för enhets registrering.

Konfigurations stegen i den här artikeln baseras på hur du använder guiden i Azure AD Connect.

Hybrid Azure AD-anslutning kräver att enheter har åtkomst till följande Microsoft-resurser inifrån din organisations nätverk:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (Om du använder eller planerar att använda sömlös enkel inloggning)

> [!WARNING]
> Om din organisation använder proxyservrar som fångar upp SSL-trafik för scenarier som förebyggande av data förlust eller begränsningar för Azure AD-innehavare, kontrollerar du att trafik till ( https://device.login.microsoftonline.com ) är exkluderad från TLS-och-undersök. Det går inte att utesluta " https://device.login.microsoftonline.com " kan orsaka störningar med autentisering av klient certifikat, vilket orsakar problem med enhets registrering och enhets-baserad villkorlig åtkomst.

Om din organisation kräver åtkomst till Internet via en utgående proxy kan du använda [implementera WPAD (Web Proxy Auto-Discovery)](/previous-versions/tn-archive/cc995261(v=technet.10)) för att aktivera Windows 10-datorer för enhets registrering med Azure AD. Information om hur du löser problem med att konfigurera och hantera WPAD finns i [Felsöka automatisk identifiering](/previous-versions/tn-archive/cc302643(v=technet.10)). I Windows 10-enheter före 1709-uppdateringen är WPAD det enda tillgängliga alternativet för att konfigurera en proxy så att den fungerar med hybrid Azure AD-anslutning. 

Om du inte använder WPAD kan du konfigurera inställningar för WinHTTP-proxy på datorn som börjar med Windows 10 1709. Mer information finns i [Inställningar för WinHTTP-proxy som distribueras av GPO](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo).

> [!NOTE]
> Om du konfigurerar proxyinställningar på datorn med hjälp av WinHTTP-inställningar kommer alla datorer som inte kan ansluta till den konfigurerade proxyn inte att kunna ansluta till Internet.

Om din organisation kräver åtkomst till Internet via en autentiserad utgående proxy kontrollerar du att dina Windows 10-datorer kan autentiseras mot den utgående proxyn. Eftersom Windows 10-datorer kör enhets registrering med hjälp av dator kontext konfigurerar du utgående proxy-autentisering med hjälp av dator kontext. Kontrollera konfigurationskraven med leverantören av den utgående proxyn.

Kontrol lera att enheten har åtkomst till ovanstående Microsoft-resurser under system kontot med hjälp av anslutnings skriptet för [test av enhets registrering](/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/) .

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurera Hybrid Azure Active Directory-anslutning

Konfigurera en hybrid Azure AD-anslutning med hjälp av Azure AD Connect:

1. Starta Azure AD Connect och välj sedan **Konfigurera**.

1. Välj **Konfigurera enhets alternativ** i **Ytterligare aktiviteter** och välj sedan **Nästa**.

   ![Ytterligare uppgifter](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. I **Översikt** väljer du **Nästa**.

1. I **Anslut till Azure AD** anger du autentiseringsuppgifterna för en global administratör för din Azure AD-klient.  

1. I **enhets alternativ** väljer du **Konfigurera hybrid Azure AD-anslutning** och väljer sedan **Nästa**.

   ![Enhetsalternativ](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. I **enhetens operativ system** väljer du de operativ system som enheterna i din Active Directory miljö använder och väljer sedan **Nästa**.

   ![Enhetsoperativsystem](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. I **SCP-konfigurationen**, för varje skog där du vill Azure AD Connect konfigurera SCP, slutför du följande steg och väljer sedan **Nästa**.

   1. Välj en **skog**.
   1. Välj en **autentiseringstjänst**.
   1. Välj **Lägg till** för att ange autentiseringsuppgifter för företags administratören.

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. I **redo att konfigurera** väljer du **Konfigurera**.

1. I **konfigurationen har slutförts** väljer du **Avsluta**.

## <a name="enable-windows-down-level-devices"></a>Aktivera äldre Windows-enheter

Om några av dina domänanslutna enheter är Windows-enheter med äldre versioner måste du:

- Konfigurera inställningarna för det lokala intranätet för enhetsregistrering
- Konfigurera sömlös SSO
- Installera Microsoft Workplace Join för Windows-datorer med äldre versioner

> [!NOTE]
> Support för Windows 7 upphörde 14 januari 2020. Mer information finns i [Support för Windows 7 avslutad](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurera inställningarna för det lokala intranätet för enhetsregistrering

För att slutföra hybrid Azure AD-anslutning av dina Windows-enheter med äldre versioner och för att undvika certifikat meddelanden när enheter autentiserar till Azure AD kan du skicka en princip till dina domänanslutna enheter för att lägga till följande URL: er till zonen Lokalt intranät i Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Du måste också aktivera **Tillåt uppdateringar av statusfältet via skript** i användarens lokala intranät zon.

### <a name="configure-seamless-sso"></a>Konfigurera sömlös SSO

För att slutföra hybrid Azure AD-anslutning av dina Windows-enheter med äldre versioner i en hanterad domän som använder [hash-synkronisering av lösen ord](../hybrid/whatis-phs.md) eller [direktautentisering](../hybrid/how-to-connect-pta.md) som din Azure AD Cloud-autentiseringsmetod, måste du också [Konfigurera sömlös SSO](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Installera Microsoft Workplace Join för Windows-datorer med äldre versioner

För att registrera Windows-enheter på låg nivå måste organisationerna installera [Microsoft Workplace Join för datorer som inte är Windows 10-datorer](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join för datorer som inte är Windows 10-datorer finns i Microsoft Download Center.

Du kan distribuera paketet med hjälp av ett program distributions system som [Microsoft Endpoint Configuration Manager](/configmgr/). Paketet stöder vanliga obevakade installations alternativ med `quiet` parametern. Den aktuella versionen av Configuration Manager erbjuder förmåner jämfört med tidigare versioner, t. ex. möjligheten att spåra slutförda registreringar.

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
3. Om den **registrerade** kolumnen säger **väntar**, slutförs inte hybrid Azure AD Join.
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

Om du får problem med att slutföra hybrid Azure AD Join för domänanslutna Windows-enheter, se:

- [Felsöka enheter med kommandot dsregcmd](./troubleshoot-device-dsregcmd.md)
- [Felsöka Azure Active Directory-hybridanslutna enheter](troubleshoot-hybrid-join-windows-current.md)
- [Felsöka hybrid Azure Active Directory anslutna enheter med äldre versioner](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du hanterar enhets identiteter med hjälp av Azure Portal.
> [!div class="nextstepaction"]
> [Hantera enhetsidentiteter](device-management-azure-portal.md)
