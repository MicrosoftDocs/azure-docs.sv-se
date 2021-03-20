---
title: Lägga till eller uppdatera användar profil information – Azure AD
description: Anvisningar om hur du lägger till information i en användares profil i Azure Active Directory, inklusive bild-och jobb information.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 04/11/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27896bac6f5436fb6717e4bc1ab0a1f9381904ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92370924"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Lägga till eller uppdatera en användares profilinformation med hjälp av Azure Active Directory
Lägg till användar profil information, inklusive en profil bild, datorspecifik information och vissa inställningar med hjälp av Azure Active Directory (Azure AD). Mer information om hur du lägger till nya användare finns i [så här lägger du till eller tar bort användare i Azure Active Directory](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Lägga till eller ändra profil information
Som du ser finns det mer information i en användares profil än vad du kan lägga till när användaren skapades. All den här ytterligare informationen är valfri och kan läggas till efter behov av din organisation.

## <a name="to-add-or-change-profile-information"></a>Lägga till eller ändra profil information
1. Logga in på [Azure Portal](https://portal.azure.com/) som användar administratör för organisationen.

2. Välj **Azure Active Directory**, Välj **användare** och välj sedan en användare. Till exempel _Alain Charon_.

    Sidan **Alain Charon-profil** visas.

    ![Användarens profil sida, inklusive redigerbar information](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Välj **Redigera** om du vill lägga till eller uppdatera informationen som ingår i de tillgängliga avsnitten.

    ![Användarens profil sida, som visar de redigerbara områdena](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Profil bild.** Välj en miniatyr bild för användarens konto. Den här bilden visas i Azure Active Directory och på användarens personliga sidor, t. ex. sidan myapps.microsoft.com.

    - **Autentiseringsidentitet.** Lägg till eller uppdatera ytterligare ett identitets värde för användaren, till exempel ett gift efter namn. Du kan ange det här namnet oberoende av värdena för förnamn och efter namn. Du kan till exempel använda den för att inkludera initialer, ett företags namn eller ändra ordningen på de namn som visas. I ett annat exempel kan du använda identitets strängen för två användare vars namn är "Christer grönt" och ange deras namn till "Christer B". grönt "Christer R. grönt (contoso)."

    - **Jobb information.** Lägg till eventuell projektrelaterad information, till exempel användarens befattning, avdelning eller chef.

    - **Autentiseringsinställningar.** Bestäm om användaren kan logga in till Azure Active Directory klient organisationen. Du kan också ange användarens globala plats.

    - **Kontakt information.** Lägg till all relevant kontakt information för användaren, förutom användarens telefonnummer eller mobil kontakt information (endast en global administratör kan uppdatera för användare i administratörs roller).

    - **Information om autentisering av kontakt.** Kontrol lera den här informationen för att se till att det finns ett aktivt telefonnummer och e-postadress för användaren. Den här informationen används av Azure Active Directory för att se till att användaren verkligen är användaren vid inloggning. Kontakt information för autentisering kan bara uppdateras av en global administratör.

4. Välj **Spara**.

    Alla ändringar sparas för användaren.

    >[!Note]
    >Du måste använda Windows Server Active Directory för att uppdatera identitet, kontakt information eller jobb information för användare vars auktoritets källa är Windows Server Active Directory. När du har slutfört uppdateringen måste du vänta tills nästa synkronisering har slutförts innan du kan se ändringarna.

## <a name="next-steps"></a>Nästa steg
När du har uppdaterat användarnas profiler kan du utföra följande grundläggande processer:

- [Lägga till eller ta bort användare](add-users-azure-active-directory.md)

- [Tilldela roller till användare](active-directory-users-assign-role-azure-portal.md)

- [Skapa en basgrupp och lägg till medlemmar](active-directory-groups-create-azure-portal.md)

Du kan också utföra andra användar hanterings uppgifter, till exempel tilldela ombud, använda principer och dela användar konton. Mer information om andra tillgängliga åtgärder finns i [Azure Active Directory User Management-dokumentation](../enterprise-users/index.yml).
