---
title: Hantera autentiseringsmetoder för Azure AD Multi-Factor Authentication-Azure Active Directory
description: Lär dig hur du kan konfigurera Azure Active Directory användar inställningar för Azure AD Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/04/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc0be7e50c5cce511fafd7d8b407626bd57659bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98183140"
---
# <a name="manage-user-authentication-methods-for-azure-ad-multi-factor-authentication"></a>Hantera metoder för användarautentisering för Azure AD Multi-Factor Authentication

Användare i Azure AD har två distinkta uppsättningar kontakt uppgifter:  

- Kontakt information för offentliga profiler, som hanteras i användar profilen och är synlig för medlemmar i din organisation. För användare som har synkroniserats från lokala Active Directory hanteras den här informationen i lokala Windows Server-Active Directory Domain Services.
- Autentiseringsmetoder, som alltid hålls privata och som endast används för autentisering, inklusive Multi-Factor Authentication (MFA). Administratörer kan hantera dessa metoder på bladet autentiseringsmetod i en användares metod och användarna kan hantera sina metoder på sidan säkerhets information i ditt konto.

När du hanterar Azure AD-Multi-Factor Authentication metoder för dina användare kan autentiserings administratörer: 

1. Lägg till autentiseringsmetoder för en speciell användare, inklusive telefonnummer som används för MFA.
1. Återställa en användares lösen ord.
1. Kräv att en användare registrerar sig för MFA igen.
1. Återkalla befintliga MFA-sessioner.
1. Ta bort en användares befintliga applösenord  

## <a name="add-authentication-methods-for-a-user"></a>Lägga till autentiseringsmetoder för en användare 

Du kan lägga till autentiseringsmetoder för en användare via Azure Portal eller Microsoft Graph.  

> [!NOTE]
> Av säkerhets skäl ska fält för kontakt information från offentlig användare inte användas för att utföra MFA. I stället ska användarna fylla i sina autentiseringsmetoder som ska användas för MFA.  

:::image type="content" source="media/howto-mfa-userdevicesettings/add-authentication-method-detail.png" alt-text="Lägg till autentiseringsmetoder från Azure Portal":::

Så här lägger du till autentiseringsmetoder för en användare via Azure Portal:  

1. Logga in på **Azure-portalen**. 
1. Bläddra till **Azure Active Directory**  >  **användare**  >  **alla användare**. 
1. Välj den användare som du vill lägga till en autentiseringsmetod för och välj **autentiseringsmetoder**.  
1. Längst upp i fönstret väljer du **+ Lägg till autentiseringsmetod**.
   1. Välj en metod (telefonnummer eller e-postadress). E-post kan användas för lösen ords återställning, men inte autentisering. När du lägger till ett telefonnummer väljer du en telefon typ och anger telefonnummer med giltigt format (t. ex. + 1 4255551234).
   1. Välj **Lägg till**.

> [!NOTE]
> I för hands versionen kan administratörer lägga till alla tillgängliga autentiseringsmetoder för användare, medan den ursprungliga upplevelsen endast tillåter uppdatering av telefon-och alternativa telefon metoder.

### <a name="manage-methods-using-powershell"></a>Hantera metoder med PowerShell:  

Installera PowerShell-modulen Microsoft. Graph. Identity. inloggningar med hjälp av följande kommandon. 

```powershell
Install-module Microsoft.Graph.Identity.Signins
Connect-MgGraph -Scopes UserAuthenticationMethod.ReadWrite.All
Select-MgProfile -Name beta
```

Lista telefonbaserade autentiseringsmetoder för en speciell användare.

```powershell
Get-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com
```

Skapa en autentiseringsmetod för mobil telefon för en speciell användare.

```powershell
New-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -phoneType "mobile" -phoneNumber "+1 7748933135"
```

Ta bort en enskild telefon metod för en användare

```powershell
Remove-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -PhoneAuthenticationMethodId 3179e48a-750b-4051-897c-87b9720928f7
```

Autentiseringsmetoder kan också hanteras med hjälp av Microsoft Graph-API: er, mer information finns i [Översikt över Azure AD Authentication Methods API](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)

## <a name="manage-user-authentication-options"></a>Hantera alternativ för användarautentisering

Om du har tilldelats rollen som *administratör för autentisering* kan du kräva att användarna återställer sina lösen ord, omregistrerar sig för MFA eller återkalla befintliga MFA-sessioner från sina användar objekt. Utför följande steg för att hantera användar inställningar:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Till vänster väljer du **Azure Active Directory** > **Användare** > **Alla användare**.
1. Välj den användare som du vill utföra en åtgärd på och välj **autentiseringsmetoder**. Överst i fönstret väljer du något av följande alternativ för användaren:
   - **Återställ lösen ord** återställer användarens lösen ord och tilldelar ett tillfälligt lösen ord som måste ändras vid nästa inloggning.
   - **Kräv omregistrering av MFA** gör det så att när användaren loggar in nästa gång uppmanas de att ställa in en ny MFA-autentiseringsmetod.
   
      > [!NOTE]
      > Användarens aktuella registrerade autentiseringsmetoder tas inte bort när en administratör kräver omregistrering för MFA. När en användare har registrerats för MFA rekommenderar vi att de granskar sin säkerhets information och tar bort alla tidigare registrerade autentiseringsmetoder som inte längre kan användas.
   
   - **Återkalla MFA-sessioner** rensar användarens sparade MFA-sessioner och kräver att de utför MFA nästa gång den krävs av principen på enheten.
   
    :::image type="content" source="media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png" alt-text="Hantera autentiseringsmetoder från Azure Portal":::

## <a name="delete-users-existing-app-passwords"></a>Ta bort användarnas befintliga applösenord

För användare som har definierat applösenord kan administratörer också välja att ta bort dessa lösen ord, vilket gör att äldre autentisering inte fungerar i dessa program. De här åtgärderna kan vara nödvändiga om du behöver ge hjälp till en användare eller behöver återställa sina autentiseringsmetoder. Icke-webbläsarbaserade appar som kopplats till dessa applösenord slutar fungera tills ett nytt applösenord har skapats. 

För att ta bort en användares applösenord, utför följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. På den vänstra sidan väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.
1. Välj **Multi-Factor Authentication**. Du kan behöva bläddra till höger för att se det här meny alternativet. Välj skärm bilden nedan om du vill se hela Azure Portals fönster och meny plats: [ ![ Välj Multi-Factor Authentication från fönstret användare i Azure AD.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Markera kryss rutan bredvid den eller de användare som du vill hantera. En lista med snabb stegs alternativ visas till höger.
1. Välj **hantera användar inställningar** och markera sedan kryss rutan för **ta bort alla befintliga applösenord som har genererats av de valda användarna**, som du ser i följande exempel: ![ ta bort alla befintliga applösenord](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Välj **Spara** och sedan **Stäng**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln visar vi hur du konfigurerar enskilda användar inställningar. Information om hur du konfigurerar övergripande inställningar för Azure AD Multi-Factor Authentication-tjänsten finns i [Konfigurera inställningar för Azure AD-Multi-Factor Authentication](howto-mfa-mfasettings.md).

Om användarna behöver hjälp kan du läsa [användar handboken för Azure AD Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user-first-time.md).
