---
title: Konfigurera en säkerhets nyckel som verifierings metod – Azure AD
description: Hur du konfigurerar din säkerhets information (förhands gransknings sida) för att verifiera din identitet för att använda en snabb identitet Online (FIDO2)-säkerhets nyckel som din verifierings metod.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: c056e439deac71417ff14dcfc3f2c3c95db41946
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88797674"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>Konfigurera en säkerhets nyckel som verifierings metod

Du kan använda säkerhets nycklar som en lösen ords avskriven inloggnings metod i din organisation. En säkerhets nyckel är en fysisk enhet som används med en unik PIN-kod för att logga in på ditt arbets-eller skol konto. Eftersom säkerhets nycklar kräver att du har den fysiska enheten och något som du bara känner till, betraktas det som en starkare verifierings metod än användar namn och lösen ord.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> Om du inte ser alternativet säkerhets nyckel är det möjligt att din organisation inte tillåter att du använder det här alternativet för verifiering. I så fall måste du välja en annan metod eller kontakta din organisations supportavdelning om du behöver mer hjälp.

## <a name="security-verification-versus-password-reset-authentication"></a>Säkerhets verifiering jämfört med autentisering av lösen ords återställning

Metoder för säkerhets information används för både dubbelriktad säkerhets verifiering och för lösen ords återställning. Dock kan inte alla metoder användas för båda.

| Metod | Används för |
| ------ | -------- |
| Autentiseringsapp | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| SMS | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| Telefonsamtal | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| Säkerhets nyckel | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| E-postkonto | Endast autentisering med återställning av lösenord. Du måste välja en annan metod för tvåfaktorsverifiering. |
| Säkerhetsfrågor | Endast autentisering med återställning av lösenord. Du måste välja en annan metod för tvåfaktorsverifiering. |

## <a name="what-is-a-security-key"></a>Vad är en säkerhets nyckel?

Vi stöder för närvarande flera konstruktioner och providrar för säkerhets nycklar med hjälp av FIDO2-autentiseringsprotokoll ( [snabb identitet Online (Fido)](https://fidoalliance.org/fido2/) ). Med dessa nycklar kan du logga in på ditt arbets-eller skol konto för att få åtkomst till organisationens molnbaserade resurser när du har en enhet och webbläsare som stöds.

Din administratör eller din organisation får en säkerhets nyckel om de kräver det för ditt arbets-eller skol konto. Det finns olika typer av säkerhets nycklar som du kan använda, till exempel en USB-nyckel som du ansluter till din enhet eller en NFC-nyckel som du trycker på en NFC-läsare. Du kan få mer information om din säkerhets nyckel, inklusive vilken typ det är, från tillverkarens dokumentation.

> [!Note]
> Om du inte kan använda en säkerhets nyckel för FIDO2 kan du använda andra metoder för att verifiera lösen ord som du kan använda som Microsoft Authenticator app eller Windows Hello. Mer information om Microsoft Authenticator-appen finns i [Vad är Microsoft Authenticator-appen?](user-help-auth-app-overview.md). Mer information om Windows Hello finns i [Översikt över Windows Hello](https://www.microsoft.com/windows/windows-hello).

## <a name="before-you-begin"></a>Innan du börjar

Innan du kan registrera din säkerhets nyckel måste följande vara sant:

- Din administratör har aktiverat den här funktionen för användning i din organisation.

- Du befinner dig på en enhet som kör Windows 10 maj 2019 Update och använder en webbläsare som stöds.

- Du har en fysisk säkerhets nyckel som har godkänts av administratören eller din organisation. Din säkerhets nyckel måste vara både FIDO2 och Microsoft-kompatibel. Om du har frågor om din säkerhets nyckel och om den är kompatibel kontaktar du din organisations supportavdelning.

## <a name="register-your-security-key"></a>Registrera din säkerhets nyckel

Du måste skapa din säkerhets nyckel och ge den en unik PIN-kod innan du kan logga in på ditt arbets-eller skol konto med hjälp av nyckeln. Du kan ha upp till 10 nycklar registrerade med ditt konto. 

1. Gå till sidan **min profil** i https://myaccount.microsoft.com och logga in om du inte redan har gjort det.

2. Välj **säkerhets information**, Välj **Lägg till metod** och välj sedan **säkerhets nyckel** i list rutan **Lägg till en metod** .

    ![Rutan Lägg till metod med säkerhets nyckeln vald](media/security-info/security-info-security-key-add-method.png)

3. Välj **Lägg till** och välj sedan den typ av säkerhets nyckel du har, antingen **USB-enhet** eller **NFC-enhet**.

    ![Välj om du har en USB-eller NFC-typ för säkerhets nyckel](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Om du inte är säker på vilken typ av säkerhets nyckel du har kan du läsa tillverkarens dokumentation. Om du inte är säker på tillverkaren kan du kontakta supportavdelningen för att få hjälp.

4. Ha din säkerhets nyckel fysiskt tillgänglig och välj sedan **Nästa** i rutan **säkerhets nyckel** .

    ![Start registrerings ruta för säkerhets nyckel](media/security-info/security-info-security-key-start-setup.png)

    En ny ruta visas för att hjälpa dig att konfigurera din nya inloggnings metod.

5. I rutan konfigurera **din nya inloggnings metod väljer du** **Nästa** och sedan:

    - Om din säkerhets nyckel är en USB-enhet sätter du in din säkerhets nyckel i USB-porten på enheten.

    - Om din säkerhets nyckel är en NFC-enhet trycker du på din säkerhets nyckel i läsaren.

6. Skriv din unika säkerhets nyckel för PIN-kod i rutan **Windows-säkerhet** och välj sedan **OK**.

    Du kommer tillbaka till rutan **Konfigurera din nya inloggnings metod** .

7. Välj **Nästa**.

8. Gå tillbaka till sidan **säkerhets information** , ange ett namn som du känner igen senare för din nya säkerhets nyckel och välj sedan **Nästa**.

    ![Sidan säkerhets information, namnge din säkerhets nyckel](media/security-info/security-info-security-key-name.png)

    Din säkerhets nyckel är registrerad och redo att användas för att logga in på ditt arbets-eller skol konto.

9. Välj **slutfört** för att stänga rutan **säkerhets nyckel** .

    Sidan **säkerhets information** uppdateras med din säkerhets nyckel information.

    ![Sidan säkerhets information där alla registrerade metoder visas](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Ta bort en säkerhets nyckel från din säkerhets information

Om du felplacerar eller inte längre vill använda en säkerhets nyckel kan du ta bort nyckeln från din säkerhets information. Även om detta förhindrar att säkerhets nyckeln används med ditt arbets-eller skol konto, fortsätter säkerhets nyckeln att lagra information om dina data och autentiseringsuppgifter. Om du vill ta bort informationen om dina data och autentiseringsuppgifter från själva säkerhets nyckeln måste du följa anvisningarna i avsnittet [återställa en Microsoft-kompatibel säkerhets nyckel](#reset-your-security-key) i den här artikeln.

1. Välj länken **ta bort** från säkerhets nyckeln som ska tas bort.

2. Välj **OK** i rutan **ta bort säkerhets nyckel** .

    Din säkerhets nyckel tas bort och du kommer inte längre att kunna använda den för att logga in på ditt arbets-eller skol konto.

>[!Important]
>Om du tar bort en säkerhets nyckel av misstag kan du registrera den igen med hjälp av anvisningarna i avsnittet [så här registrerar du din säkerhets nyckel](#register-your-security-key) i den här artikeln.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Hantera dina säkerhets nyckel inställningar från Windows-inställningar

Du kan hantera säkerhets nyckel inställningarna från appen **Windows-inställningar** , inklusive återställa din säkerhets nyckel och skapa en ny PIN-kod för säkerhets nyckeln.

### <a name="reset-your-security-key"></a>Återställa din säkerhets nyckel

Om du vill ta bort all konto information som lagras i din fysiska säkerhets nyckel, måste du återställa nyckeln till fabriks inställningarna igen. Om du återställer din säkerhets nyckel tas allt från nyckeln bort, så att du kan börja om.

>[!IMPORTANT]
>Om du återställer säkerhets nyckeln raderas allt från nyckeln och återställs till fabriks inställningarna.
>
> **Alla data och autentiseringsuppgifter raderas.**

#### <a name="to-reset-your-security-key"></a>Återställa din säkerhets nyckel

1. Öppna appen Windows-inställningar, Välj **konton**, Välj **inloggnings alternativ**, Välj **säkerhets nyckel** och välj sedan **Hantera**.

2. Sätt i din säkerhets nyckel i USB-porten eller tryck på din NFC-läsare för att verifiera din identitet.

3. Följ anvisningarna på skärmen, baserat på din aktuella tillverkare av säkerhets nyckel. Om din nyckel tillverkare inte visas i anvisningarna på skärmen kan du läsa mer i tillverkarens webbplats.

4. Välj **Stäng** för att stänga skärmen **Hantera** .

### <a name="create-a-new-security-key-pin"></a>Skapa en ny PIN-kod för säkerhets nyckeln

Du kan skapa en ny PIN-kod för säkerhets nyckeln för din säkerhets nyckel.

#### <a name="to-create-a-new-security-key-pin"></a>Så här skapar du en ny PIN-kod för säkerhets nyckeln

1. Öppna appen Windows-inställningar, Välj **konton**, Välj **inloggnings alternativ**, Välj **säkerhets nyckel** och välj sedan **Hantera**.

2. Sätt i din säkerhets nyckel i USB-porten eller tryck på din NFC-läsare för att verifiera din identitet.
3. Välj **Lägg till** från rutan **PIN-kod för säkerhets nyckel** , skriv och bekräfta din nya PIN-kod för säkerhets nyckeln och välj sedan **OK**.

     Säkerhets nyckeln uppdateras med den nya säkerhets nyckeln PIN-kod för användning med ditt arbets-eller skol konto. Om du bestämmer dig för att ändra din PIN-kod igen, kan du välja knappen **ändra** .
4. Välj **Stäng** för att stänga skärmen **Hantera** .

## <a name="additional-security-info-methods"></a>Ytterligare metoder för säkerhets information

För att kunna registrera en säkerhets nyckel måste du ha minst en annan metod för säkerhets verifiering registrerad. Mer information finns i [översikts avsnittet](./security-info-setup-auth-app.md) . 

## <a name="next-steps"></a>Nästa steg

- Mer information om verifierings metoder för lösen ord finns i [Microsoft Azure AD påbörjar den offentliga för hands versionen av FIDO2-säkerhetsnycklar, aktiverar blogg inloggnings](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) blogg eller läser artikeln [Vad är Microsoft Authenticator app?](user-help-auth-app-overview.md) och [Översikt över Windows Hello](https://www.microsoft.com/windows/windows-hello) .

- Mer detaljerad information om [Microsoft-kompatibla säkerhets nycklar](/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Återställ lösenordet om du har tappat eller glömt det från [portalen för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följ stegen i artikeln [Återställa lösenordet för arbets- eller skolkonto](active-directory-passwords-update-your-own-password.md).

- Få felsökningstips och hjälp om inloggningsproblem i artikeln [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).