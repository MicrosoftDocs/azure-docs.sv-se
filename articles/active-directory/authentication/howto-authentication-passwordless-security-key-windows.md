---
title: Lösen ords skydd med lösen ords skydd i Windows – Azure Active Directory
description: Lär dig hur du aktiverar inloggning med lösen ord med lösen ord för att Azure Active Directory med hjälp av FIDO2-säkerhetsnycklar
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 190e9c857f1ec9d19eb89493dc4b4a9fb68fac87
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653515"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory"></a>Aktivera inloggning med lösen ord utan lösen ord till Windows 10-enheter med Azure Active Directory 

Det här dokumentet fokuserar på att aktivera FIDO2-säkerhetsnyckel baserad lösenordsautentisering med Windows 10-enheter. I slutet av den här artikeln kan du logga in på både Azure AD-och hybrid Azure AD-anslutna Windows 10-enheter med ditt Azure AD-konto med hjälp av en FIDO2 säkerhets nyckel.

## <a name="requirements"></a>Krav

| Enhetstyp | Azure AD-ansluten | Hybrid Azure AD-ansluten |
| --- | --- | --- |
| [Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md) | X | X |
| [Kombinerad säkerhets informations registrering](concept-registration-mfa-sspr-combined.md) | X | X |
| Kompatibla [säkerhets nycklar för FIDO2](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| Webauthn kräver Windows 10 version 1903 eller senare | X | X |
| [Azure AD-anslutna enheter](../devices/concept-azure-ad-join.md) kräver Windows 10 version 1909 eller senare | X |   |
| [Hybrid Azure AD-anslutna enheter](../devices/concept-azure-ad-join-hybrid.md) kräver Windows 10 version 2004 eller senare |   | X |
| Fullständigt korrigerade Windows Server 2016/2019-domänkontrollanter. |   | X |
| [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) version 1.4.32.0 eller senare |   | X |
| [Microsoft Intune](/intune/fundamentals/what-is-intune) (valfritt) | X | X |
| Etablerings paket (valfritt) | X | X |
| Grupprincip (valfritt) |   | X |

### <a name="unsupported-scenarios"></a>Scenarier som inte stöds

Följande scenarier stöds inte:

- Windows Server Active Directory Domain Services (AD DS)-domänanslutna (endast lokala enheter) distribution.
- RDP-, VDI-och Citrix-scenarier med hjälp av en säkerhets nyckel.
- S/MIME med hjälp av en säkerhets nyckel.
- "Kör som" med hjälp av en säkerhets nyckel.
- Logga in på en server med hjälp av en säkerhets nyckel.
- Om du inte har använt din säkerhets nyckel för att logga in på enheten när du är online kan du inte använda den för att logga in eller låsa upp offline.
- Logga in eller låsa upp en Windows 10-enhet med en säkerhets nyckel som innehåller flera Azure AD-konton. Det här scenariot använder det senaste kontot som har lagts till i säkerhets nyckeln. Med webauthn kan användarna välja det konto de vill använda.
- Låsa upp en enhet som kör Windows 10 version 1809. Använd Windows 10 version 1903 eller senare för bästa möjliga upplevelse.

## <a name="prepare-devices"></a>Förbereda enheter

Azure AD-anslutna enheter måste köra Windows 10 version 1909 eller senare.

Hybrid Azure AD-anslutna enheter måste köra Windows 10 version 2004 eller senare.

## <a name="enable-security-keys-for-windows-sign-in"></a>Aktivera säkerhets nycklar för Windows-inloggning

Organisationer kan välja att använda en eller flera av följande metoder för att aktivera användning av säkerhets nycklar för Windows-inloggning baserat på deras organisations krav:

- [Aktivera med Intune](#enable-with-intune)
- [Riktad Intune-distribution](#targeted-intune-deployment)
- [Aktivera med ett konfigurations paket](#enable-with-a-provisioning-package)
- [Aktivera med grupprincip (endast hybrid Azure AD-anslutna enheter)](#enable-with-group-policy)

> [!IMPORTANT]
> Organisationer med **hybrid Azure AD-anslutna enheter** måste **också** följa stegen i artikeln, [Aktivera FIDO2-autentisering till lokala resurser](howto-authentication-passwordless-security-key-on-premises.md) innan autentisering med Windows 10 FIDO2-säkerhetsnyckeln fungerar.
>
> Organisationer med **Azure AD-anslutna enheter** måste göra detta innan deras enheter kan autentisera till lokala resurser med FIDO2 säkerhets nycklar.

### <a name="enable-with-intune"></a>Aktivera med Intune

Utför följande steg för att aktivera användning av säkerhets nycklar med Intune:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Bläddra till **Microsoft Intune**  >  **enhets registrering**  >  **Windows-registrering**  >  **Egenskaper för Windows Hello för företag**  >  .
1. Under **Inställningar** anger du **Använd säkerhets nycklar för inloggning** till **aktive rad**.

Konfiguration av säkerhets nycklar för inloggning är inte beroende av att konfigurera Windows Hello för företag.

### <a name="targeted-intune-deployment"></a>Riktad Intune-distribution

Om du vill använda specifika enhets grupper för att aktivera Credential-providern använder du följande anpassade inställningar via Intune:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Bläddra till **Microsoft Intune**  >  **enhets konfiguration**  >  **profiler**  >  **Skapa profil**.
1. Konfigurera den nya profilen med följande inställningar:
   - Namn: säkerhets nycklar för Windows Sign-In
   - Beskrivning: aktiverar FIDO-säkerhetsnycklar som ska användas vid inloggning i Windows
   - Plattform: Windows 10 och senare
   - Profil typ: anpassad
   - Anpassade OMA-URI-inställningar:
      - Namn: Aktivera FIDO säkerhets nycklar för Windows Sign-In
      - OMA-URI:./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      - Datatyp: heltal
      - Värde: 1
1. Den här principen kan tilldelas till vissa användare, enheter eller grupper. Mer information finns i [Tilldela användar-och enhets profiler i Microsoft Intune](/intune/device-profile-assign).

![Skapa anpassad enhets konfigurations princip för Intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Aktivera med ett konfigurations paket

För enheter som inte hanteras av Intune kan ett konfigurations paket installeras för att aktivera funktionen. Windows Configuration designer-appen kan installeras från [Microsoft Store](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22). Utför följande steg för att skapa ett konfigurations paket:

1. Starta Windows Configuration designer.
1. Välj **Arkiv**  >  **nytt projekt**.
1. Ge projektet ett namn och anteckna sökvägen där projektet skapas och välj sedan **Nästa**.
1. Lämna *etablerings paketet* valt som det **valda projekt arbets flödet** och välj **Nästa**.
1. Välj *alla versioner av Windows-skrivbordet* under **Välj vilka inställningar du vill visa och konfigurera** och välj sedan **Nästa**.
1. Välj **Slutför**.
1. I det nyskapade projektet bläddrar du till **körnings inställningar**  >  **WindowsHelloForBusiness**  >  **SecurityKeys**  >  **UseSecurityKeyForSignIn**.
1. Ange **UseSecurityKeyForSignIn** till *aktive rad*.
1. Välj **Exportera**  >  **etablerings paket**
1. Lämna standardvärdena i fönstret **build** under **Beskriv etablerings paketet** och välj sedan **Nästa**.
1. Lämna standardvärdena i fönstret **build** under **Välj säkerhets information för etablerings paketet** och välj **Nästa**.
1. Anteckna eller ändra sökvägen i **build** Windows under **Välj var du vill spara etablerings paketet** och välj **Nästa**.
1. Välj **build** på sidan **bygga etablerings paket** .
1. Spara de två filerna som skapats (*ppkg* och *Cat*) till en plats där du kan tillämpa dem på datorer senare.
1. Om du vill använda etablerings paketet som du har skapat läser du [Använd ett konfigurations paket](/windows/configuration/provisioning-packages/provisioning-apply-package).

> [!NOTE]
> Enheter som kör Windows 10 version 1903 måste också aktivera Shared PC Mode (*EnableSharedPCMode*). Mer information om hur du aktiverar den här funktionen finns i [Konfigurera en delad eller gäst dator med Windows 10](/windows/configuration/set-up-shared-or-guest-pc).

### <a name="enable-with-group-policy"></a>Aktivera med grupprincip

För **hybrid Azure AD-anslutna enheter** kan organisationer konfigurera följande grupprincip inställning för att aktivera Fido-inloggning för säkerhets nycklar. Inställningen finns under **dator konfiguration**  >  **administrativa mallar**  >  **system**  >  **inloggning**  >  **Aktivera inloggning med säkerhets nyckel**:

- När den här principen **är aktive rad** kan användarna logga in med säkerhets nycklar.
- Om du ställer in principen på **inaktive rad** eller **inte konfigurerad** stoppas användare från att logga in med säkerhets nycklar.

Den här grupprincip inställningen kräver en uppdaterad version av `CredentialProviders.admx` grupprincips mal len. Den här nya mallen är tillgänglig med nästa version av Windows Server och Windows 10 20H1. Den här inställningen kan hanteras med en enhet som kör någon av dessa nyare versioner av Windows eller centralt genom att följa anvisningarna i support avsnittet [så här skapar och hanterar du den centrala butiken för grupprincip administrativa mallar i Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="sign-in-with-fido2-security-key"></a>Logga in med säkerhets nyckeln FIDO2

I exemplet nedan har en användare med namnet Bala Sandhu redan etablerad sin FIDO2-säkerhetsnyckel med hjälp av stegen i föregående artikel, [Aktivera lösen ords skydds nyckel inloggning](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). För Hybrid Azure AD-anslutna enheter ser du till att du även har [aktiverat inloggning med lösen ords lös säkerhets nyckel till lokala resurser](howto-authentication-passwordless-security-key-on-premises.md). Bala kan välja providern för säkerhets nyckel autentiseringsuppgifter från Lås skärmen för Windows 10 och infoga säkerhets nyckeln för att logga in på Windows.

![Inloggning av säkerhets nyckel på Lås skärmen i Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Hantera säkerhets nyckel bio metrisk, PIN-kod eller återställning säkerhets nyckel

* Windows 10 version 1903 eller senare
   * Användare kan öppna **Windows-inställningar** på sina enheter >  >  **säkerhets nyckel** för konton
   * Användare kan ändra sin PIN-kod, uppdatera biometrik eller återställa sin säkerhets nyckel

## <a name="troubleshooting-and-feedback"></a>Felsökning och feedback

Om du vill dela feedback eller stöta på problem med den här funktionen kan du dela via Windows Feedback Hub-appen med hjälp av följande steg:

1. Starta **feedback Hub** och se till att du är inloggad.
1. Skicka feedback under följande kategorisering:
   - Kategori: säkerhet och sekretess
   - Under kategori: FIDO
1. Om du vill avbilda loggar använder du alternativet för att **återskapa problemet**.

## <a name="next-steps"></a>Nästa steg

[Aktivera åtkomst till lokala resurser för Azure AD och hybrid Azure AD-anslutna enheter](howto-authentication-passwordless-security-key-on-premises.md)

[Läs mer om enhets registrering](../devices/overview.md)

[Läs mer om Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)