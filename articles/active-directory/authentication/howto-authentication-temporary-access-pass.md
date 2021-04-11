---
title: Konfigurera ett tillfälligt åtkomst pass i Azure AD för att registrera metoder för lösen ords lös autentisering
description: Lär dig hur du konfigurerar och gör det möjligt för användare att registrera metoder för lösen ords lös autentisering med hjälp av ett tillfälligt åtkomst pass
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarckms
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8774df6a2eee15f8b5a0c37362e5b20f14b07549
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167370"
---
# <a name="configure-temporary-access-pass-in-azure-ad-to-register-passwordless-authentication-methods-preview"></a>Konfigurera tillfälligt åtkomst pass i Azure AD för att registrera metoder för lösen ords lös autentisering (för hands version)

Metoder för lösen ords lös autentisering, till exempel FIDO2 och lösen ords lös telefonin loggning via Microsoft Authenticator-appen, gör det möjligt för användare att logga in på ett säkert sätt utan lösen ord. Användare kan starta metoder för att använda lösen ord på något av två sätt:

- Använda befintliga metoder för Azure AD-Multi-Factor Authentication 
- Använda ett tillfälligt åtkomst pass (KNACKa) 

Ett tillfälligt åtkomst pass är ett tidsbegränsat lösen ord som utfärdats av en administratör som uppfyller kraven på starka autentisering och som kan användas för att publicera andra autentiseringsmetoder, inklusive lösen ord. Ett tillfälligt åtkomst pass gör det lättare att återställa när en användare har förlorat eller glömt sin starka autentiserings faktor, till exempel en säkerhets nyckel för FIDO2 eller Microsoft Authenticator app, men du måste logga in för att registrera nya metoder för stark autentisering.


Den här artikeln visar hur du aktiverar och använder ett tillfälligt åtkomst pass i Azure AD med hjälp av Azure Portal. Du kan också utföra dessa åtgärder med hjälp av REST-API: er. 

>[!NOTE]
>Tillfälligt åtkomst pass är för närvarande en offentlig för hands version. Vissa funktioner kanske inte stöds eller har begränsade funktioner. 

## <a name="enable-the-temporary-access-pass-policy"></a>Aktivera principen för temporär åtkomst pass

En tillfällig princip för åtkomst pass definierar inställningar, till exempel livs längden för pass som skapas i klienten eller de användare och grupper som kan använda ett tillfälligt åtkomst pass för inloggning. Innan någon kan logga in med ett tillfälligt åtkomst pass måste du aktivera principen för autentiseringsmetod och välja vilka användare och grupper som kan logga in med hjälp av ett tillfälligt åtkomst pass.
Även om du kan skapa ett tillfälligt åtkomst pass för alla användare kan bara de som ingår i principen logga in med det.

Rollen global administratör och autentiseringsmetod för princip administratörer kan uppdatera principen för tillfälliga åtkomst pass-autentiseringsmetoden.
Så här konfigurerar du principen för temporär åtkomst pass-autentiseringsmetod:

1. Logga in på Azure Portal som global administratör och klicka på **Azure Active Directory**  >    >  **säkerhetsautentiseringsmetoder**  >  **temporär åtkomst pass**.
1. Klicka på **Ja** om du vill aktivera principen, välja vilka användare som principen ska tillämpas på och eventuella **allmänna** inställningar.

   ![Skärm bild av hur du aktiverar principen för temporär åtkomst pass-autentiseringsmetod](./media/how-to-authentication-temporary-access-pass/policy.png)

   Standardvärdet och intervallet för tillåtna värden beskrivs i följande tabell.


   | Inställning | Standardvärden | Tillåtna värden | Kommentarer |
   |---|---|---|---|
   | Lägsta livstid | 1 timme | 10 – 43200 minuter (30 dagar) | Minsta antal minuter som det tillfälliga åtkomst passet är giltigt. |
   | Högsta livstid | 24 timmar | 10 – 43200 minuter (30 dagar) | Maximalt antal minuter som det tillfälliga åtkomst passet är giltigt. |
   | Standard livs längd | 1 timme | 10 – 43200 minuter (30 dagar) | Standardvärden kan åsidosättas av de enskilda stegen, inom den lägsta och högsta livstid som kon figurer ATS av principen. |
   | Användning i taget | Falskt | True/false | När principen är inställd på false kan pass i klienten användas antingen en gång eller mer än en gång under giltighets tiden (högsta livstid). Genom att framtvinga användning av en gång i den tillfälliga åtkomst pass-principen skapas alla pass som skapats i klient organisationen som en engångs användning. |
   | Längd | 8 | 8-48 tecken | Definierar längden på lösen ordet. |

## <a name="create-a-temporary-access-pass"></a>Skapa ett tillfälligt åtkomst pass

När du har aktiverat en princip kan du skapa ett tillfälligt åtkomst pass för en användare i Azure AD. Dessa roller kan utföra följande åtgärder som rör ett tillfälligt åtkomst pass.

- Global administratör kan skapa, ta bort och visa ett tillfälligt åtkomst pass för alla användare (förutom själva)
- Administratörer för privilegie rad autentisering kan skapa, ta bort och visa ett tillfälligt åtkomst pass på administratörer och medlemmar (förutom själva)
- Autentiserings administratörer kan skapa, ta bort och visa ett tillfälligt åtkomst pass på medlemmar (förutom själva)
- Global administratör kan visa information om tillfälliga åtkomst pass för användaren (utan att läsa själva koden).

1. Logga in på Azure Portal som global administratör, privilegie rad autentisering administratör eller administratör för autentisering. 
1. Klicka på **Azure Active Directory**, bläddra till användare, Välj en användare, till exempel *Christer grönt*, och välj **autentiseringsmetoder**.
1. Om det behövs väljer du alternativet för att **testa den nya metoden** för användarautentisering.
1. Välj alternativet för att **lägga till autentiseringsmetoder**.
1. Under **Välj metod** klickar du på **tillfälligt åtkomst pass (för hands version)**.
1. Definiera en anpassad aktiverings tid eller varaktighet och klicka på **Lägg till**.

   ![Skärm bild av hur du skapar ett tillfälligt åtkomst pass](./media/how-to-authentication-temporary-access-pass/create.png)

1. När det har lagts till visas information om det tillfälliga åtkomst passet. Anteckna det faktiska värdet för tillfälligt åtkomst pass. Du anger det här värdet för användaren. Du kan inte Visa det här värdet när du har klickat på **OK**.
   
   ![Skärm bild av information om tillfälligt åtkomst pass](./media/how-to-authentication-temporary-access-pass/details.png)

Följande kommandon visar hur du skapar och får ett tillfälligt åtkomst pass med hjälp av PowerShell:

```powershell
# Create a Temporary Access Pass for a user
$properties = @{}
$properties.isUsableOnce = $True
$properties.startDateTime = '2021-03-11 06:00:00'
$propertiesJSON = $properties | ConvertTo-Json

New-MgUserAuthenticationTemporaryAccessPassMethod -UserId user2@contoso.com -BodyParameter $propertiesJSON

Id                                   CreatedDateTime       IsUsable IsUsableOnce LifetimeInMinutes MethodUsabilityReason StartDateTime         TemporaryAccessPass
--                                   ---------------       -------- ------------ ----------------- --------------------- -------------         -------------------
c5dbd20a-8b8f-4791-a23f-488fcbde3b38 9/03/2021 11:19:17 PM False    True         60                NotYetValid           11/03/2021 6:00:00 AM TAPRocks!

# Get a user's Temporary Access Pass
Get-MgUserAuthenticationTemporaryAccessPassMethod -UserId user3@contoso.com

Id                                   CreatedDateTime       IsUsable IsUsableOnce LifetimeInMinutes MethodUsabilityReason StartDateTime         TemporaryAccessPass
--                                   ---------------       -------- ------------ ----------------- --------------------- -------------         -------------------
c5dbd20a-8b8f-4791-a23f-488fcbde3b38 9/03/2021 11:19:17 PM False    True         60                NotYetValid           11/03/2021 6:00:00 AM

```

## <a name="use-a-temporary-access-pass"></a>Använd ett tillfälligt åtkomst pass

Det vanligaste användnings steget för ett tillfälligt åtkomst pass är att en användare kan registrera autentiseringsinformation under den första inloggningen, utan att behöva slutföra ytterligare säkerhets frågor. Autentiseringsmetoder registreras på [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Användare kan också uppdatera befintliga autentiseringsmetoder här.

1. Öppna en webbläsare för att [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Ange UPN för det konto som du skapade det tillfälliga åtkomst passet för, till exempel *tapuser@contoso.com* .
1. Om användaren ingår i den tillfälliga åtkomst pass principen visas en skärm för att ange det tillfälliga åtkomst passet.
1. Ange det tillfälliga åtkomst pass som visades i Azure Portal.

   ![Skärm bild av hur du anger ett tillfälligt åtkomst pass](./media/how-to-authentication-temporary-access-pass/enter.png)

>[!NOTE]
>För federerade domäner föredras ett tillfälligt åtkomst pass framför federationen. En användare med ett tillfälligt åtkomst pass kommer att slutföra autentiseringen i Azure AD och kommer inte att omdirigeras till den federerade identitets leverantören (IdP).

Användaren är nu inloggad och kan uppdatera eller registrera en metod som FIDO2 säkerhets nyckel. Användare som uppdaterar sina autentiseringsmetoder på grund av förlust av sina autentiseringsuppgifter eller enheter bör se till att de tar bort gamla autentiseringsmetoder.

Användare kan också använda sitt tillfälliga åtkomst pass för att registrera sig för inloggning via lösen ord direkt från Authenticator-appen. Mer information finns i [Lägg till ditt arbets-eller skol konto i Microsoft Authenticator-appen](../user-help/user-help-auth-app-add-work-school-account.md).

![Skärm bild av hur du anger ett tillfälligt åtkomst pass med arbets-eller skol konto](./media/how-to-authentication-temporary-access-pass/enter-work-school.png)

## <a name="delete-a-temporary-access-pass"></a>Ta bort ett tillfälligt åtkomst pass

Det går inte att använda ett tillfälligt slutat åtkomst pass. Under **autentiseringsmetoder** för en användare visar kolumnen **information** när det tillfälliga åtkomst passet har upphört att gälla. Du kan ta bort ett tillfälligt slutat åtkomst pass med följande steg:

1. Bläddra till **användare** på Azure AD-portalen, Välj en användare, t. ex. *Tryck på användare* och välj **autentiseringsmetoder**.
1. På den högra sidan av autentiseringsmetoden **temporär åtkomst pass (förhands granskning)** som visas i listan väljer du **ta bort**.

Du kan också använda PowerShell:

```powershell
# Remove a user's Temporary Access Pass
Remove-MgUserAuthenticationTemporaryAccessPassMethod -UserId user3@contoso.com -TemporaryAccessPassAuthenticationMethodId c5dbd20a-8b8f-4791-a23f-488fcbde3b38
```

## <a name="replace-a-temporary-access-pass"></a>Ersätta ett tillfälligt åtkomst pass 

- En användare kan bara ha ett tillfälligt åtkomst pass. Lösen ordet kan användas under start-och slut tiden för det tillfälliga åtkomst passet.
- Om användaren behöver ett nytt tillfälligt åtkomst pass:
  - Om det befintliga tillfälliga åtkomst passet är giltigt måste administratören ta bort det befintliga tillfälliga åtkomst passet och skapa ett nytt pass för användaren. Om du tar bort ett giltigt tillfälligt åtkomst pass återkallar du användarens sessioner. 
  - Om det befintliga tillfälliga åtkomst passet har upphört att gälla, åsidosätter ett nytt tillfälligt åtkomst pass det befintliga tillfälliga åtkomst passet och kommer inte att återkalla användarens sessioner.

Mer information om NIST-standarder för onboarding och återställning finns i [NIST Special Publication 800-63a](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4).

## <a name="limitations"></a>Begränsningar

Tänk på följande begränsningar:

- När du använder ett temporärt åtkomst pass för att registrera en lösen ords lös metod som t. ex. FIDO2 eller telefonin loggning måste användaren slutföra registreringen inom 10 minuters inloggning med ett temporärt åtkomst pass i taget. Den här begränsningen gäller inte för ett tillfälligt åtkomst pass som kan användas mer än en gång.
- Gäst användare kan inte logga in med ett tillfälligt åtkomst pass.
- Användare i omfång för registrerings principen för självbetjänings återställning (SSPR) *eller* [identitets skydd Multi-Factor Authentication](../identity-protection/howto-identity-protection-configure-mfa-policy.md) måste registrera autentiseringsmetoder när de har loggat in med ett tillfälligt åtkomst pass. Användare inom omfånget för dessa principer kommer att omdirigeras till [avbrotts läget i den kombinerade registreringen](concept-registration-mfa-sspr-combined.md#combined-registration-modes). Den här upplevelsen stöder för närvarande inte registrering av FIDO2 och telefonin loggning. 
- Det går inte att använda ett tillfälligt åtkomst pass med nätverks princip Server (NPS)-tillägget och Active Directory Federation Services (AD FS) (AD FS)-kortet, eller under Installationsprogrammet för Windows/out-of-Box-Experience (OOBE) och autopilot. 
- När sömlös SSO är aktiverat på klient organisationen uppmanas användarna att ange ett lösen ord. Länken **Använd ditt tillfälliga åtkomst pass** är tillgänglig för användaren att logga in med ett tillfälligt åtkomst pass.

  ![Skärm bild av Använd ett tillfälligt åtkomst pass i stället](./media/how-to-authentication-temporary-access-pass/alternative.png)

## <a name="troubleshooting"></a>Felsökning    

- Om ett tillfälligt åtkomst pass inte erbjuds till en användare under inloggningen, kontrollerar du följande:
  - Användaren är omfånget för principen för tillfällig åtkomst pass-autentiseringsmetod.
  - Användaren har ett giltigt tillfälligt åtkomst pass och om det används i taget används det inte ännu.
- Om **du har blockerat logga in för temporär åtkomst på grund av att användaren har** loggat in med ett tillfälligt åtkomst pass, kontrollerar du följande:
  - Användaren har ett tillfälligt åtkomst pass medan principen för autentisering kräver ett temporärt åtkomst pass vid ett tillfälle.
  - Ett temporärt åtkomst pass i taget har redan använts.

## <a name="next-steps"></a>Nästa steg

- [Planera distribution av lösenordsskyddad autentisering i Azure Active Directory](howto-authentication-passwordless-deployment.md)

