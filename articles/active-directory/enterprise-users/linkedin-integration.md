---
title: Administrativt medgivande för LinkedIn-konto anslutningar – Azure AD | Microsoft Docs
description: Förklarar hur du aktiverar eller inaktiverar LinkedIn-integrerings konto anslutningar i Microsoft-appar i Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ffa937f91af7078b28331a655f99397d3821def
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96545901"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Integrera LinkedIn-konto anslutningar i Azure Active Directory

Du kan ge användare i organisationen åtkomst till sina LinkedIn-anslutningar i vissa Microsoft-appar. Inga data delas förrän användare beviljats att ansluta sina konton. Du kan integrera din organisation i [administrations centret](https://aad.portal.azure.com)för Azure Active Directory (Azure AD).

> [!IMPORTANT]
> Inställningen för LinkedIn-konto anslutningar är för närvarande distribuerad till Azure AD-organisationer. När den distribueras till din organisation är den aktive rad som standard.
>
> Undantag
>
> * Inställningen är inte tillgänglig för kunder som använder Microsoft Cloud för amerikanska myndigheter, Microsoft Cloud Tyskland eller Azure och Microsoft 365 som drivs av 21Vianet i Kina.
> * Inställningen är inaktive rad som standard för Azure AD-organisationer etablerade i Tyskland. Observera att inställningen inte är tillgänglig för kunder som använder Microsoft Cloud Tyskland.
> * Inställningen är inaktive rad som standard för organisationer etablerade i Frankrike.
>
> När LinkedIn-konton har Aktiver ATS för din organisation fungerar konto anslutningarna efter att de har gett appar till gång till företags data för deras räkning. Information om inställningen för användar medgivande finns i [så här tar du bort en användares åtkomst till ett program](../manage-apps/methods-for-removing-user-access.md).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Aktivera LinkedIn-konto anslutningar i Azure Portal

Du kan aktivera LinkedIn-konto anslutningar för enbart de användare som du vill ha åtkomst till, från hela organisationen till endast valda användare i din organisation.

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com/) med ett konto som är en global administratör för Azure AD-organisationen.
1. Välj **Användare**.
1. Välj **användar inställningar** på sidan **användare** .
1. Under **LinkedIn-konto anslutningar** kan användare ansluta sina konton för att komma åt sina LinkedIn-anslutningar i vissa Microsoft-appar. Inga data delas förrän användare beviljats att ansluta sina konton.

    * Välj **Ja** om du vill aktivera tjänsten för alla användare i din organisation
    * Välj **markerad grupp** om du vill aktivera tjänsten för endast en grupp med valda användare i din organisation
    * Välj **Nej** om du vill återkalla medgivande från alla användare i din organisation

    ![Integrera LinkedIn-konto anslutningar i organisationen](./media/linkedin-integration/linkedin-integration.png)

1. När du är klar väljer du **Spara** för att spara inställningarna.

> [!Important]
> LinkedIn-integrering är inte helt aktive rad för dina användare förrän de godkänner att de ansluter sina konton. Inga data delas när du aktiverar konto anslutningar för dina användare.

### <a name="assign-selected-users-with-a-group"></a>Tilldela markerade användare till en grupp

Vi har ersatt alternativet "valt" som anger en lista med användare med alternativet att välja en grupp användare så att du kan ansluta LinkedIn-och Microsoft-konton för en enskild grupp i stället för många enskilda användare. Om du inte har LinkedIn-konto anslutningar aktiverade för utvalda enskilda användare behöver du inte göra något. Om du tidigare har aktiverat LinkedIn-konto anslutningar för valda enskilda användare bör du:

1. Hämta den aktuella listan över enskilda användare
1. Flytta de för närvarande aktiverade enskilda användarna till en grupp
1. Använd gruppen från föregående som den valda gruppen i inställningen för LinkedIn-konto anslutningar i administrations centret för Azure AD.

> [!NOTE]
> Även om du inte flyttar dina för tillfället markerade enskilda användare till en grupp kan de fortfarande se LinkedIn-information i Microsoft-appar.

### <a name="move-currently-selected-users-to-a-group"></a>Flytta markerade användare till en grupp

1. Skapa en CSV-fil för de användare som har marker ATS för LinkedIn-konto anslutningar.
1. Logga in på Microsoft 365 med ditt administratörs konto.
1. Starta PowerShell.
1. Installera Azure AD-modulen genom att köra `Install-Module AzureAD`
1. Kör följande skript:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Om du vill använda gruppen från steg två som den valda gruppen i inställningen för inställningar för LinkedIn-konto i Azure AD Admin Center, se [Aktivera LinkedIn-konton anslutningar i Azure Portal](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Använd grupprincip för att aktivera LinkedIn-konto anslutningar

1. Hämta [Office 2016-filerna för administrativa mallar (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Extrahera **ADMX** -filerna och kopiera dem till den centrala butiken.
1. Öppna Grupprinciphantering.
1. Skapa ett Grupprincip-objekt med följande inställning: **användar konfiguration**  >  **administrativa mallar**  >  **Microsoft Office 2016**  >  **Diverse**  >  **Visa LinkedIn-funktioner i Office-program**.
1. Välj **aktive rad** eller **inaktive** rad.
  
   Tillstånd | Effekt
   ------ | ------
   **Aktiverad** | Inställningen **Visa LinkedIn-funktioner i Office-program** i alternativ för Office 2016 är aktive rad. Användare i din organisation kan använda LinkedIn-funktioner i sina Office 2016-program.
   **Disabled** (Inaktiverat) | Inställningen **Visa LinkedIn-funktioner i Office-program** i alternativ för Office 2016 är inaktive rad och slutanvändare kan inte ändra den här inställningen. Användare i din organisation kan inte använda LinkedIn-funktioner i sina Office 2016-program.

Den här grup principen påverkar endast Office 2016-appar för en lokal dator. Om användarna inaktiverar LinkedIn i sina Office 2016-appar kan de fortfarande se LinkedIn-funktioner i Microsoft 365.

## <a name="next-steps"></a>Nästa steg

* [Användar medgivande och data delning för LinkedIn](linkedin-user-consent.md)

* [LinkedIn-information och-funktioner i dina Microsoft-appar](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn Help Center](https://www.linkedin.com/help/linkedin)

* [Visa den aktuella LinkedIn-integrerings inställningen i Azure Portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)