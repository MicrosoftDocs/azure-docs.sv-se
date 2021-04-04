---
title: Planera implementeringen av Azure Active Directory Join
description: Förklarar de steg som krävs för att implementera Azure AD-anslutna enheter i din miljö.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3acaf4929158b24ff50655aa18c05b41aeec4b53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96435458"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Gör så här: planera din Azure AD Join-implementering

Med Azure AD Join kan du ansluta enheter direkt till Azure AD utan att behöva ansluta till lokala Active Directory samtidigt som användarna är produktiva och säkra. Azure AD Join är företags klart för både storskaliga och omfångs distributioner.   

Den här artikeln innehåller den information du behöver för att planera din Azure AD Join-implementering.
 
## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med [introduktionen till enhets hantering i Azure Active Directory](./overview.md).

## <a name="plan-your-implementation"></a>Planera implementeringen

För att planera implementeringen av Azure AD Join bör du bekanta dig med:

> [!div class="checklist"]
> - Granska dina scenarier
> - Granska din identitets infrastruktur
> - Utvärdera din enhets hantering
> - Förstå överväganden för program och resurser
> - Förstå dina etablerings alternativ
> - Konfigurera roaming för företags tillstånd
> - Konfigurera villkorlig åtkomst

## <a name="review-your-scenarios"></a>Granska dina scenarier 

Även om hybrid Azure AD Join kan föredras för vissa scenarier kan du med Azure AD Join gå över till en molnbaserad modell med Windows. Om du planerar att modernisera din enhets hantering och minska enhets relaterade IT-kostnader ger Azure AD Join en bra grund för att uppnå dessa mål.  
 
Du bör överväga att använda Azure AD Join om dina mål överensstämmer med följande kriterier:

- Du antar Microsoft 365 som produktivitets Svit för dina användare.
- Du vill hantera enheter med en hanterings lösning för moln enheter.
- Du vill förenkla enhets etableringen för geografiskt distribuerade användare.
- Du planerar att modernisera din program infrastruktur.

## <a name="review-your-identity-infrastructure"></a>Granska din identitets infrastruktur  

Azure AD Join fungerar med både hanterade och federerade miljöer.  

### <a name="managed-environment"></a>Hanterad miljö

En hanterad miljö kan distribueras antingen via [hash-synkronisering av lösen ord](../hybrid/how-to-connect-password-hash-synchronization.md) eller [genom att passera](../hybrid/how-to-connect-pta-quick-start.md) med sömlös enkel inloggning.

De här scenarierna kräver inte att du konfigurerar en Federations Server för autentisering.

### <a name="federated-environment"></a>Federerad miljö

En federerad miljö bör ha en identitets leverantör som stöder både WS-Trust-och WS-Fed-protokoll:

- **WS-utfodras:** Det här protokollet krävs för att ansluta en enhet till Azure AD.
- **WS-förtroende:** Det här protokollet krävs för att logga in på en Azure AD-ansluten enhet.

När du använder AD FS måste du aktivera följande WS-Trust slut punkter: `/adfs/services/trust/2005/usernamemixed`
 `/adfs/services/trust/13/usernamemixed`
 `/adfs/services/trust/2005/certificatemixed`
 `/adfs/services/trust/13/certificatemixed`

Om din identitetsprovider inte stöder dessa protokoll fungerar inte Azure AD Join. 

>[!NOTE]
> Azure AD Join fungerar för närvarande inte med [AD FS 2019 som kon figurer ATS med externa autentiseringsproviders som primär autentiseringsmetod](/windows-server/identity/ad-fs/operations/additional-authentication-methods-ad-fs#enable-external-authentication-methods-as-primary). Azure AD Join-standardvärden för lösenordsautentisering som den primära metoden, vilket resulterar i autentiseringsfel i det här scenariot


### <a name="smartcards-and-certificate-based-authentication"></a>Smartkort och certifikatbaserad autentisering

Du kan inte använda smartkort eller certifikatbaserad autentisering för att ansluta enheter till Azure AD. Smartkort kan dock användas för att logga in på Azure AD-anslutna enheter om du har AD FS konfigurerat.

**Rekommendation:** Implementera Windows Hello för företag för stark, lösen ords lös autentisering för Windows 10-enheter.

### <a name="user-configuration"></a>Användar konfiguration

Om du skapar användare i din:

- **Lokala Active Directory** måste du synkronisera dem till Azure AD med hjälp av [Azure AD Connect](../hybrid/how-to-connect-sync-whatis.md). 
- **Azure AD** krävs ingen ytterligare konfiguration.

Lokala UPN: er som skiljer sig från Azure AD-UPN: er stöds inte på Azure AD-anslutna enheter. Om användarna använder ett lokalt UPN bör du planera att växla till att använda deras primära UPN i Azure AD.

UPN-ändringar stöds bara vid start av Windows 10 2004 Update. Användare på enheter med den här uppdateringen får inga problem efter att ha ändrat sina UPN. För enheter före uppdatering av Windows 10 2004 skulle användare ha SSO-och villkorliga åtkomst problem på sina enheter. De behöver logga in på Windows via panelen "annan användare" med hjälp av sitt nya UPN för att lösa det här problemet. 

## <a name="assess-your-device-management"></a>Utvärdera din enhets hantering

### <a name="supported-devices"></a>Enheter som stöds

Azure AD-anslutning:

- Gäller endast för Windows 10-enheter. 
- Gäller inte tidigare versioner av Windows eller andra operativ system. Om du har Windows 7/8.1-enheter måste du uppgradera till Windows 10 för att distribuera Azure AD Join.
- Stöds för FIPS-kompatibla TPM 2,0 men stöds inte för TPM 1,2. Om dina enheter har FIPS-kompatibel TPM 1,2 måste du inaktivera dem innan du fortsätter med Azure AD Join. Microsoft tillhandahåller inga verktyg för att inaktivera FIPS-läge för TPM eftersom det är beroende av TPM-tillverkaren. Kontakta maskin varans OEM om du vill ha hjälp.
 
**Rekommendation:** Använd alltid den senaste versionen av Windows 10 för att dra nytta av uppdaterade funktioner.

### <a name="management-platform"></a>Hanterings plattform

Enhets hantering för Azure AD-anslutna enheter baseras på en MDM-plattform som Intune och MDM-CSP: er. Windows 10 har en inbyggd MDM-agent som fungerar med alla kompatibla MDM-lösningar.

> [!NOTE]
> Grup principer stöds inte i Azure AD-anslutna enheter eftersom de inte är anslutna till lokala Active Directory. Hantering av Azure AD-anslutna enheter är bara möjlig via MDM

Det finns två metoder för att hantera Azure AD-anslutna enheter:

- **Endast MDM** – en enhet hanteras exklusivt av en MDM-provider som Intune. Alla principer levereras som en del av processen för MDM-registrering. För Azure AD Premium-eller EMS-kunder är MDM-registrering ett automatiserat steg som ingår i en Azure AD-anslutning.
- **Co-Management** – en enhet hanteras av en MDM-Provider och SCCM. I den här metoden installeras SCCM-agenten på en MDM-hanterad enhet för att administrera vissa aspekter.

Om du använder grup principer utvärderar du GRUPPRINCIPOBJEKTet och MDM-principens paritet genom att använda [Grupprincip Analytics](/mem/intune/configuration/group-policy-analytics) i Microsoft Endpoint Manager. 

Granska principer som stöds och som inte stöds för att avgöra om du kan använda en MDM-lösning i stället för grup principer. Tänk på följande för principer som inte stöds:

- Krävs de principer som inte stöds för Azure AD-anslutna enheter eller användare?
- Är de principer som inte stöds tillämpliga i en molnbaserad distribution?

Om MDM-lösningen inte är tillgänglig via Azure AD App-galleriet kan du lägga till den enligt den process som beskrivs i [Azure Active Directory integration med MDM](/windows/client-management/mdm/azure-active-directory-integration-with-mdm). 

Genom samhantering kan du använda SCCM för att hantera vissa aspekter av dina enheter medan principer levereras via din MDM-plattform. Microsoft Intune möjliggör samhantering med SCCM. Mer information om samhantering för Windows 10-enheter finns i [Vad är samtidig hantering?](/configmgr/core/clients/manage/co-management-overview). Om du använder en annan MDM-produkt än Intune bör du kontrol lera med MDM-providern om tillämpliga samhanterings scenarier.

**Rekommendation:** Överväg endast MDM-hantering för Azure AD-anslutna enheter.

## <a name="understand-considerations-for-applications-and-resources"></a>Förstå överväganden för program och resurser

Vi rekommenderar att du migrerar program från en lokal plats till molnet för en bättre användar upplevelse och åtkomst kontroll. Azure AD-anslutna enheter kan dock sömlöst ge åtkomst till både lokala program och moln program. Mer information finns i [hur SSO till lokala resurser fungerar på Azure AD-anslutna enheter](azuread-join-sso.md).

I följande avsnitt listas överväganden för olika typer av program och resurser.

### <a name="cloud-based-applications"></a>Molnbaserade program

Om ett program läggs till i Azure AD App-galleriet får användarna enkel inloggning via Azure AD-anslutna enheter. Ingen ytterligare konfiguration krävs. Användarna får SSO i både Microsoft Edge-och Chrome-webbläsarna. För Chrome måste du distribuera [tillägget Windows 10-konton](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). 

Alla Win32-program som:

- Förlita dig på webb konto hanteraren (WAM) för token-förfrågningar får även SSO på Azure AD-anslutna enheter. 
- Förlita dig inte på WAM kan uppmana användarna att autentisera sig. 

### <a name="on-premises-web-applications"></a>Lokala webb program

Om dina appar är anpassade och/eller värdbaserade lokalt, måste du lägga till dem i webbläsarens betrodda platser för att:

- Aktivera Windows-integrerad autentisering för arbete 
- Ge användarna en enkel SSO-upplevelse. 

Om du använder AD FS, se [Verifiera och hantera enkel inloggning med AD FS](/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Rekommendation:** Överväg att vara värd i molnet (till exempel Azure) och integrera med Azure AD för att få en bättre upplevelse.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Lokala program som förlitar sig på äldre protokoll

Användare får SSO från Azure AD-anslutna enheter om enheten har åtkomst till en domänkontrollant. 

**Rekommendation:** Distribuera [Azure AD App proxy](../manage-apps/application-proxy.md) för att aktivera säker åtkomst för dessa program.

### <a name="on-premises-network-shares"></a>Lokala nätverks resurser

Användarna har SSO från Azure AD-anslutna enheter när en enhet har åtkomst till en lokal domänkontrollant.

### <a name="printers"></a>Skrivare

För skrivare måste du distribuera [hybrid moln utskrift](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) för att identifiera skrivare på Azure AD-anslutna enheter. 

Även om skrivare inte kan identifieras automatiskt i en moln miljö kan användarna också använda skrivarens UNC-sökväg för att lägga till dem direkt. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>Lokala program som förlitar sig på datorautentisering

Azure AD-anslutna enheter stöder inte lokala program som förlitar sig på datorautentisering. 

**Rekommendation:** Överväg att dra tillbaka de här programmen och flytta till de moderna alternativen.

### <a name="remote-desktop-services"></a>Fjärrskrivbordstjänster

Anslutning till fjärr skrivbord till en Azure AD-ansluten enhet kräver att värddatorn antingen är Azure AD-ansluten eller hybrid Azure AD-ansluten. Fjärr skrivbord från en frånkopplad eller icke-Windows-enhet stöds inte. Mer information finns i [ansluta till en fjärran sluten Azure AD-ansluten dator](/windows/client-management/connect-to-remote-aadj-pc)

Genom att starta Windows 10 2004-uppdateringen kan användare även använda fjärr skrivbord från en Azure AD-registrerad Windows 10-enhet till en Azure AD-ansluten enhet. 

## <a name="understand-your-provisioning-options"></a>Förstå dina etablerings alternativ
**Obs!** Azure AD-anslutna enheter kan inte distribueras med system förberedelse verktyget (Sysprep) eller liknande avbildnings verktyg

Du kan etablera Azure AD Join med hjälp av följande metoder:

- **Självbetjäning i OOBE/Settings** – i självbetjänings läget går användarna igenom Azure AD Join-processen antingen under Windows OOBE (out of Box Experience) eller från Windows-inställningar. Mer information finns i [ansluta din arbets enhet till din organisations nätverk](../user-help/user-help-join-device-on-network.md). 
- **Windows autopilot** – Windows autopilot möjliggör för konfigurering av enheter för en smidig upplevelse i OOBE för att utföra en Azure AD-anslutning. Mer information finns i [Översikt över Windows autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot). 
- **Mass** registrering – Mass registrering gör det möjligt för en administratör att använda Azure AD-anslutning med hjälp av ett Mass etablerings verktyg för att konfigurera enheter. Mer information finns i [Mass registrering för Windows-enheter](/intune/windows-bulk-enroll).
 
Här är en jämförelse av dessa tre metoder 
 
| Element | Installation av självbetjäning | Windows Autopilot | Massregistrering |
| --- | --- | --- | --- |
| Kräv användar interaktion för att konfigurera | Ja | Ja | Inga |
| Kräv IT-ansträngning | Inga | Ja | Ja |
| Tillämpliga flöden | Inställningar för OOBE-& | Endast OOBE | Endast OOBE |
| Lokal administratörsbehörighet till primär användare | Ja, som standard | Konfigurerbar | Nej |
| Kräv enhets-OEM-support | Inga | Ja | Inga |
| Versioner som stöds | 1511 + | 1709 + | 1703 + |
 
Välj distributions metod eller-metoder genom att granska tabellen ovan och granska följande överväganden för att införa någon av metoderna:  

- Är dina användare Tech-smarta att gå igenom själva installationen? 
   - Självbetjäning kan fungera bäst för dessa användare. Överväg Windows autopilot för att förbättra användar upplevelsen.  
- Är dina användare fjärranslutna eller inom företagets lokaler? 
   - Självbetjänings-eller autopilot fungerar bäst för fjärran vändare för en smidig och kostnads fri installation. 
- Föredrar du en användare som är driven eller en administrativ hanterad konfiguration? 
   - Mass registrering fungerar bättre för administratörs driven distribution för att konfigurera enheter innan de överlämnas till användarna.     
- Köper du enheter från 1-2 OEM-tillverkare eller har du en bred distribution av OEM-enheter?  
   - Om du köper från begränsade OEM-tillverkare som också stöder autopilot kan du dra nytta av tätare integrering med autopilot. 

## <a name="configure-your-device-settings"></a>Konfigurera enhets inställningar

Med Azure Portal kan du styra distributionen av Azure AD-anslutna enheter i din organisation. Om du vill konfigurera de relaterade inställningarna väljer du på **sidan Azure Active Directory** `Devices > Device settings` .

### <a name="users-may-join-devices-to-azure-ad"></a>Användare kan ansluta enheter till Azure AD

Ange det här alternativet till **alla** eller **valt** baserat på distributionens omfattning och vem du vill tillåta att en Azure AD-ansluten enhet konfigureras. 

![Användare kan ansluta enheter till Azure AD](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Ytterligare lokala administratörer på Azure AD-anslutna enheter

Välj **markerade** och välj de användare som du vill lägga till i gruppen lokala administratörer på alla Azure AD-anslutna enheter. 

![Ytterligare lokala administratörer på Azure AD-anslutna enheter](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>Kräv Multi-factor auth för att ansluta enheter

Välj **Ja** om du vill att användarna ska kunna utföra MFA när de ansluter till enheter till Azure AD. För att användarna ska kunna ansluta enheter till Azure AD med MFA blir själva enheten en andra faktor.

![Kräv Multi-factor auth för att ansluta enheter](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>Konfigurera dina mobilitets inställningar

Innan du kan konfigurera dina mobilitets inställningar kan du behöva lägga till en MDM-Provider först.

**Så här lägger du till en MDM-Provider**:

1. På **sidan Azure Active Directory** går du till avsnittet **Hantera** och klickar på `Mobility (MDM and MAM)` . 
1. Klicka på **Lägg till program**.
1. Välj MDM-providern i listan.

   :::image type="content" source="./media/azureadjoin-plan/04.png" alt-text="Skärm bild av Azure Active Directory lägga till en program sida. Flera M D M-leverantörer visas." border="false":::

Välj MDM-providern för att konfigurera de relaterade inställningarna. 

### <a name="mdm-user-scope"></a>MDM-användaromfång

Välj **en** eller **flera** baserat på distributionens omfattning. 

![MDM-användaromfång](./media/azureadjoin-plan/05.png)

Beroende på ditt omfång händer något av följande: 

- **Användaren är i MDM-omfattning**: om du har en Azure AD Premium prenumeration automatiseras MDM-registreringen tillsammans med Azure AD Join. Alla omfångs användare måste ha en lämplig licens för din MDM. Om MDM-registreringen Miss lyckas i det här scenariot kommer Azure AD Join också att återställas.
- **Användaren är inte i MDM-omfattning**: om användarna inte är i MDM-omfattningen slutförs Azure AD Join utan någon MDM-registrering. Detta resulterar i en ohanterad enhet.

### <a name="mdm-urls"></a>MDM-URL:er

Det finns tre URL: er som är relaterade till din MDM-konfiguration:

- Webbadress till MDM-användarvillkor
- Webbadress till MDM-identifiering 
- Webbadress till MDM-kompatibilitet

:::image type="content" source="./media/azureadjoin-plan/06.png" alt-text="Skärm bild av en del av konfigurations avsnittet Azure Active Directory M D M, med U R L-fält för M D M användnings villkor, identifiering och efterlevnad." border="false":::

Varje URL har ett fördefinierat standardvärde. Om dessa fält är tomma kontaktar du MDM-providern för mer information.

### <a name="mam-settings"></a>MAM-inställningar

MAM gäller inte för Azure AD Join. 

## <a name="configure-enterprise-state-roaming"></a>Konfigurera roaming för företags tillstånd

Om du vill aktivera tillstånds växling till Azure AD så att användarna kan synkronisera sina inställningar på olika enheter, se [aktivera Enterprise State roaming i Azure Active Directory](enterprise-state-roaming-enable.md). 

**Rekommendation**: Aktivera den här inställningen även för Hybrid Azure AD-anslutna enheter.

## <a name="configure-conditional-access"></a>Konfigurera villkorlig åtkomst

Om du har en MDM-provider som kon figurer ATS för Azure AD-anslutna enheter, flaggar providern som kompatibel så snart enheten hanteras. 

![Kompatibel enhet](./media/azureadjoin-plan/46.png)

Du kan använda den här implementeringen för att [kräva hanterade enheter för Cloud app-åtkomst med villkorlig åtkomst](../conditional-access/require-managed-devices.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Anslut en ny Windows 10-enhet med Azure AD under en första körning](azuread-joined-devices-frx.md) 
>  [Anslut din arbets enhet till din organisations nätverk](../user-help/user-help-join-device-on-network.md)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
