---
title: Lösen ords återställning via självbetjäning för Windows-enheter – Azure Active Directory
description: Lär dig hur du aktiverar Azure Active Directory lösen ords återställning via självbetjäning på Windows inloggnings skärm.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa2d910c017d3cc626f737bdab50315aef8d1e77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99491393"
---
# <a name="enable-azure-active-directory-self-service-password-reset-at-the-windows-sign-in-screen"></a>Aktivera Azure Active Directory lösen ords återställning via självbetjäning på Windows inloggnings skärm

Lösen ords återställning via självbetjäning (SSPR) ger användare i Azure Active Directory (Azure AD) möjlighet att ändra eller återställa sitt lösen ord, utan administratörs-eller Supportens medverkan. Användare öppnar vanligt vis en webbläsare på en annan enhet för att få åtkomst till [SSPR-portalen](https://aka.ms/sspr). För att förbättra upplevelsen på datorer som kör Windows 7, 8, 8,1 och 10 kan du göra det möjligt för användare att återställa sina lösen ord på inloggnings skärmen för Windows.

![Exempel på Windows 7-och 10 inloggnings skärmar med SSPR-länk visas](./media/howto-sspr-windows/windows-reset-password.png)

> [!IMPORTANT]
> I den här självstudien visas en administratör för att aktivera SSPR för Windows-enheter i ett företag.
>
> Om ditt IT-team inte har aktiverat möjligheten att använda SSPR från din Windows-enhet eller om du har problem under inloggningen kan du kontakta supportavdelningen för ytterligare hjälp.

## <a name="general-limitations"></a>Allmänna begränsningar

Följande begränsningar gäller för att använda SSPR från Windows-inloggnings skärmen:

- Lösen ords återställning stöds inte för närvarande från ett fjärr skrivbord eller från utökade Hyper-V-sessioner.
- Vissa leverantörer av autentiseringsuppgifter för tredje part är kända för att orsaka problem med den här funktionen.
- Att inaktivera UAC via ändring av [register nyckeln EnableLUA](/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) är känt för att orsaka problem.
- Den här funktionen fungerar inte för nätverk med 802.1 x-nätverksautentisering distribuerad och alternativet "utför omedelbart före användar inloggning". För nätverk med 802.1 x-nätverksautentisering bör du använda datorautentisering för att aktivera den här funktionen.
- Hybrid Azure AD-anslutna datorer måste ha en nätverks anslutning till en domänkontrollant för att kunna använda det nya lösen ordet och uppdatera cachelagrade autentiseringsuppgifter. Det innebär att enheterna måste antingen vara i organisationens interna nätverk eller på ett VPN med nätverks åtkomst till en lokal domänkontrollant.
- Om du använder en avbildning innan du kör Sysprep kontrollerar du att webbcachen har rensats för den inbyggda administratören innan du utför CopyProfile-steget. Mer information om det här steget finns i Support artikeln [dåliga prestanda när du använder anpassad standard användar profil](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- Följande inställningar är kända för att störa möjligheten att använda och återställa lösen ord på Windows 10-enheter:
    - Om Ctrl + Alt + del krävs av en princip i Windows 10 fungerar inte **återställa lösen ord** .
    - Om meddelanden från Lås skärmen stängs av fungerar inte **Återställ lösen ord** .
    - *HideFastUserSwitching* har angetts till Enabled eller 1
    - *DontDisplayLastUserName* har angetts till Enabled eller 1
    - *NoLockScreen* har angetts till Enabled eller 1
    - *EnableLostMode* har angetts på enheten
    - Explorer.exe har ersatts med ett anpassat gränssnitt
- Kombinationen av följande tre inställningar kan orsaka att funktionen inte fungerar.
    - Interaktiv inloggning: Kräv inte CTRL + ALT + DEL = inaktive rad
    - *DisableLockScreenAppNotifications* = 1 eller aktive rad
    - Windows SKU är inte Home eller Professional Edition

> [!NOTE]
> Dessa begränsningar gäller även för PIN-återställning för Windows Hello för företag från enhets Lås skärmen.
>

## <a name="windows-10-password-reset"></a>Lösen ords återställning för Windows 10

Om du vill konfigurera en Windows 10-enhet för SSPR på inloggnings skärmen granskar du följande krav och konfigurations steg.

### <a name="windows-10-prerequisites"></a>Krav för Windows 10

- En administratör [måste aktivera lösen ords återställning via självbetjäning i Azure AD från Azure Portal](tutorial-enable-sspr.md).
- Användarna måste registrera sig för SSPR innan de kan använda den här funktionen på [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - Inte unikt för att använda SSPR från inloggnings skärmen i Windows. alla användare måste ange kontakt information för autentisering innan de kan återställa sina lösen ord.
- Krav för nätverks proxy:
    - Port 443 till `passwordreset.microsoftonline.com` och `ajax.aspnetcdn.com`
    - Windows 10-enheter stöder endast konfiguration av proxyserver på dator nivå.
- Kör minst Windows 10, version april 2018 Update (v1803) och enheterna måste vara antingen:
    - Azure AD-ansluten
    - Hybrid Azure AD-ansluten

### <a name="enable-for-windows-10-using-intune"></a>Aktivera för Windows 10 med Intune

Att distribuera konfigurations ändringen för att aktivera SSPR från inloggnings skärmen med Intune är den mest flexibla metoden. Med Intune kan du distribuera konfigurationsändringen till en särskild grupp av datorer som du definierar. Den här metoden kräver Intune-registrering av enheten.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Skapa en princip för enhetskonfiguration i Intune

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **Intune**.
1. Skapa en ny enhets konfigurations profil genom att gå till **enhetens konfigurations**  >  **profiler** och välj sedan **+ Skapa profil**
   - För **plattform** väljer du *Windows 10 och senare*
   - För **profil typ** väljer du *anpassad*
1. Välj **skapa** och ange sedan ett beskrivande namn för profilen, t. ex. *Windows 10-INLOGGNINGs skärmen SSPR*

    Du kan också ange en meningsfull beskrivning av profilen och sedan välja **Nästa**.
1. Under *konfigurations inställningar* väljer du **Lägg till** och anger följande OMA-URI-inställning för att aktivera länken Återställ lösen ord:
      - Ange ett beskrivande namn som förklarar vad inställningen gör, till exempel *Lägg till SSPR länk*.
      - Du kan också ange en meningsfull beskrivning av inställningen.
      - **OMA-URI** inställt på `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - **Datatyp** inställt på **Integer**
      - **Värde** inställt på **1**

    Välj **Lägg till** och sedan **Nästa**.
1. Principen kan tilldelas till vissa användare, enheter eller grupper. Tilldela profilen som du vill för din miljö, helst till en test grupp enheter först och välj sedan **Nästa**.

    Mer information finns i [Tilldela användar-och enhets profiler i Microsoft Intune](/mem/intune/configuration/device-profile-assign).

1. Konfigurera tillämplighets regler som önskade för din miljö, t. ex. för att *Tilldela profil om OS-versionen är Windows 10 Enterprise* och välj sedan **Nästa**.
1. Granska din profil och välj sedan **skapa**.

### <a name="enable-for-windows-10-using-the-registry"></a>Aktivera för Windows 10 med hjälp av registret

Utför följande steg för att aktivera SSPR på inloggnings skärmen med hjälp av en register nyckel:

1. Logga in på Windows-datorn med administratörs behörighet.
1. Tryck på **Windows**  +  **R** för att öppna dialog rutan *Kör* och kör sedan **regedit** som administratör
1. Ange följande registernyckel:

    ```cmd
    HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount
       "AllowPasswordReset"=dword:00000001
    ```

#### <a name="troubleshooting-windows-10-password-reset"></a>Fel sökning av lösen ords återställning i Windows 10

Om du har problem med att använda SSPR från Windows-inloggnings skärmen, innehåller Azure AD audit-loggen information om IP-adressen och *ClientType* där lösen ords återställningen inträffade, som du ser i följande exempel utdata:

![Exempel på lösen ords återställning i Windows 7 i Azure AD-gransknings loggen](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

När användarna återställer sina lösen ord från inloggnings skärmen för en Windows 10-enhet skapas ett tillfälligt konto med låg behörighet `defaultuser1` . Det här kontot används för att skydda processen för lösenordsåterställning.

Själva kontot har ett slumpmässigt genererat lösen ord, visas inte för enhets inloggning och tas automatiskt bort när användaren återställer lösen ordet. `defaultuser`Det kan finnas flera profiler men de kan ignoreras på ett säkert sätt.

## <a name="windows-7-8-and-81-password-reset"></a>Lösen ords återställning för Windows 7, 8 och 8,1

Om du vill konfigurera en Windows 7-, 8-eller 8,1-enhet för SSPR på inloggnings skärmen granskar du följande krav och konfigurations steg.

### <a name="windows-7-8-and-81-prerequisites"></a>Krav för Windows 7, 8 och 8,1

- En administratör [måste aktivera lösen ords återställning via självbetjäning i Azure AD från Azure Portal](tutorial-enable-sspr.md).
- Användarna måste registrera sig för SSPR innan de kan använda den här funktionen på [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - Inte unikt för att använda SSPR från inloggnings skärmen i Windows. alla användare måste ange kontakt information för autentisering innan de kan återställa sina lösen ord.
- Krav för nätverks proxy:
    - Port 443 till `passwordreset.microsoftonline.com`
- Operativ systemet Windows 7 eller Windows 8,1 har korrigerats.
- TLS 1,2 aktiverat med hjälp av rikt linjer som finns i [register inställningarna för Transport Layer Security (TLS)](/windows-server/security/tls/tls-registry-settings#tls-12).
- Om fler än en leverantör av autentiseringsuppgifter för tredje part är aktive rad på datorn visas fler än en användar profil på inloggnings skärmen.

> [!WARNING]
> TLS 1,2 måste vara aktiverat, inte bara inställt på automatisk förhandling.

### <a name="install"></a>Installera

För Windows 7, 8 och 8,1 måste en liten komponent installeras på datorn för att SSPR ska kunna aktive ras på inloggnings skärmen. Utför följande steg för att installera den här SSPR-komponenten:

1. Hämta lämpligt installations program för den Windows-version som du vill aktivera.

    Program varu installations programmet finns på Microsoft Download Center på [https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Logga in på den dator där du vill installera och kör installations programmet.
1. Efter installationen rekommenderas du starkt att starta om.
1. Efter omstarten väljer du en användare på inloggnings skärmen och väljer "glömt lösen ord?" för att initiera arbets flödet för lösen ords återställning.
1. Slutför arbets flödet genom att följa anvisningarna på skärmen för att återställa lösen ordet.

![Exempel Windows 7 klickade på "glömt lösen ord?" SSPR-flöde](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Tyst installation

SSPR-komponenten kan installeras eller avinstalleras utan prompter med hjälp av följande kommandon:

- För tyst installation använder du kommandot "msiexec/i SsprWindowsLogon.PROD.msi/Qn"
- Vid tyst avinstallation använder du kommandot "msiexec/x SsprWindowsLogon.PROD.msi/Qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Fel sökning av lösen ords återställning för Windows 7, 8 och 8,1

Om du har problem med att använda SSPR från inloggnings skärmen i Windows loggas händelser både på datorn och i Azure AD. Azure AD-händelser innehåller information om IP-adressen och ClientType där lösen ords återställning uppstod, som du ser i följande exempel utdata:

![Exempel på lösen ords återställning i Windows 7 i Azure AD-gransknings loggen](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Om ytterligare loggning krävs kan en register nyckel på datorn ändras för att aktivera utförlig loggning. Aktivera utförlig loggning för fel söknings syfte endast med följande register nyckel värde:

```cmd
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

- Om du vill aktivera utförlig loggning skapar du en `REG_DWORD: "EnableLogging"` och anger den till 1.
- Om du vill inaktivera utförlig loggning ändrar `REG_DWORD: "EnableLogging"` du till 0.

## <a name="what-do-users-see"></a>Vad ser användarna

Vilka ändringar av användaren har kon figurer ATS för dina Windows-enheter. SSPR Hur vet de att de kan återställa sitt lösenord på inloggningsskärmen? Följande exempel skärm bilder visar de ytterligare alternativen för en användare att återställa sina lösen ord med SSPR:

![Exempel på Windows 7-och 10 inloggnings skärmar med SSPR-länk visas](./media/howto-sspr-windows/windows-reset-password.png)

När användarna försöker logga in, ser de ett **lösen ord** eller en länk för att återställa **lösen ord** som öppnar självbetjäningen för återställning av lösen ord på inloggnings skärmen. Via den här funktionen kan användarna återställa sina lösenord utan att de behöver använda en annan enhet för att få åtkomst till webbläsaren.

Mer information för användare med att använda den här funktionen finns i [återställa ditt arbets-eller skol lösen ord](../user-help/active-directory-passwords-update-your-own-password.md)

## <a name="next-steps"></a>Nästa steg

För att förenkla användar registreringen kan du [förkonfigurera kontakt information för användarautentisering för SSPR](howto-sspr-authenticationdata.md).
