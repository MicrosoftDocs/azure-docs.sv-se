---
title: Ta bort en Azure AD-organisation (klient) – Azure Active Directory | Microsoft Docs
description: Förklarar hur du förbereder en Azure AD-organisation (klient) för borttagning, inklusive självbetjänings organisationer
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2edc6fb98359c5360836bc369e5ae1928464df92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861038"
---
# <a name="delete-a-tenant-in-azure-active-directory"></a>Ta bort en klient i Azure Active Directory

När en Azure AD-organisation (klient) tas bort, tas även alla resurser som ingår i organisationen bort. Förbered din organisation genom att minimera dess associerade resurser innan du tar bort den. Endast en global administratör för Azure Active Directory (Azure AD) kan ta bort en Azure AD-organisation från portalen.

## <a name="prepare-the-organization"></a>Förbered organisationen

Det går inte att ta bort en organisation i Azure AD förrän flera kontroller har passerats. Dessa kontroller minskar risken för att en Azure AD-organisation påverkar användarens åtkomst negativt, till exempel möjligheten att logga in på Microsoft 365 eller få åtkomst till resurser i Azure. Till exempel om organisationen som är associerad med en prenumeration oavsiktligt har tagits bort, kan användarna inte komma åt Azure-resurser för den prenumerationen. Följande villkor kontrolleras:

* Det får inte finnas några användare i Azure AD-organisationen (klient organisationen) förutom en global administratör som vill ta bort organisationen. Andra användare måste tas bort innan organisationen kan tas bort. Om användarna synkroniseras lokalt måste synkroniseringen först inaktive ras och användarna måste tas bort i moln organisationen med hjälp av Azure Portal-eller Azure PowerShell-cmdletar.
* Det kan inte finnas några program i organisationen. Alla program måste tas bort innan organisationen kan tas bort.
* Det kan inte finnas några Multi-Factor Authentication-providrar kopplade till organisationen.
* Det kan inte finnas några prenumerationer för Microsoft Online Services, till exempel Microsoft Azure, Microsoft 365 eller Azure AD Premium som är kopplade till organisationen. Om du till exempel har skapat en Azure AD-organisation som standard i Azure kan du inte ta bort den här organisationen om din Azure-prenumeration fortfarande är beroende av den här organisationen för autentisering. På samma sätt kan du inte ta bort en organisation om en annan användare har associerat en prenumeration med den.

## <a name="delete-the-organization"></a>Ta bort organisationen

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med ett konto som är den globala administratören för din organisation.

2. Välj **Azure Active Directory**.

3. Växla till den organisation som du vill ta bort.
  
   ![Bekräfta organisation innan borttagning](./media/directory-delete-howto/delete-directory-command.png)

4. Välj **ta bort klient organisation**.
  
   ![Välj kommandot för att ta bort organisationen](./media/directory-delete-howto/delete-directory-list.png)

5. Om din organisation inte klarar en eller flera kontroller får du en länk till mer information om hur du skickar. När du har godkänt alla kontroller väljer du **ta bort** för att slutföra processen.

## <a name="if-you-cant-delete-the-organization"></a>Om du inte kan ta bort organisationen

När du konfigurerade din Azure AD-organisation kan du också ha aktiverat licensbaserade prenumerationer för din organisation som Azure AD Premium P2, Microsoft 365 Business Standard eller Enterprise Mobility + Security E5. För att undvika oavsiktlig data förlust kan du inte ta bort en organisation förrän prenumerationerna har tagits bort helt. Prenumerationerna måste vara i **avetablerat** tillstånd för att organisationen ska kunna tas bort. En **förfallen** eller **avbruten** prenumeration flyttas till **inaktiverat** tillstånd och den slutliga fasen är tillståndet **deetablerat** .

Information om vad som ska förväntas när en utvärderings version Microsoft 365 prenumerationen upphör (inklusive betald partner/CSP, Enterprise-avtal eller volym licensiering) finns i följande tabell. Mer information om Microsoft 365 data kvarhållning och prenumerations livs cykel finns i [Vad händer med mina data och åtkomst när min Microsoft 365 for Business-prenumeration slutar?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Prenumerations tillstånd | Data | Åtkomst till data
----- | ----- | -----
Aktiv (30 dagar för utvärderings version) | Data tillgängliga för alla | Användare har normal åtkomst till Microsoft 365 filer eller appar<br>Administratörer har normal åtkomst till Microsoft 365 administrations Center och-resurser 
Upphört (30 dagar) | Data tillgängliga för alla| Användare har normal åtkomst till Microsoft 365 filer eller appar<br>Administratörer har normal åtkomst till Microsoft 365 administrations Center och-resurser
Inaktive rad (30 dagar) | Endast data tillgängliga för administratör | Användare har inte åtkomst till Microsoft 365 filer eller appar<br>Administratörer kan komma åt Microsoft 365 administrations Center men kan inte tilldela licenser till eller uppdatera användare
Avetablerat (30 dagar efter inaktive rad) | Data har tagits bort (tas bort automatiskt om inga andra tjänster används) | Användare har inte åtkomst till Microsoft 365 filer eller appar<br>Administratörer kan komma åt Microsoft 365 administrations Center för att köpa och hantera andra prenumerationer

## <a name="delete-a-subscription"></a>Ta bort en prenumeration

Du kan lägga till en prenumeration i **avetablerat** tillstånd för borttagning på tre dagar med hjälp av Microsoft 365 administrations Center.

1. Logga in på [Microsoft 365 administrations Center](https://admin.microsoft.com) med ett konto som är en global administratör i din organisation. Om du försöker ta bort organisationen "contoso" som har den inledande standard domänen contoso.onmicrosoft.com loggar du in med ett UPN, till exempel admin@contoso.onmicrosoft.com .

2. Förhandsgranska den nya Microsoft 365 administrations centret genom att se till att **prova den nya administrations Center** -växlingen är aktive rad.

   ![Förhandsgranska den nya M365 administrations Center-upplevelsen](./media/directory-delete-howto/preview-toggle.png)

3. När det nya administrations centret har Aktiver ATS måste du avbryta en prenumeration innan du kan ta bort den. Välj **fakturerings** -och välj **produkter & tjänster** och välj sedan **Avbryt prenumerationen** för den prenumeration som du vill avbryta. Du kommer till en feedback-sida.

   ![Välj en prenumeration för att avbryta](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Slutför feedback-formuläret och välj **Avbryt prenumeration** om du vill avbryta prenumerationen.

   ![Avbryt kommando i för hands versionen av prenumerationen](./media/directory-delete-howto/cancel-command.png)

5. Nu kan du ta bort prenumerationen. Välj **ta bort** för den prenumeration som du vill ta bort. Om du inte hittar prenumerationen på sidan **produkter & tjänster** kontrollerar du att **prenumerations status** är inställd på **alla**.

   ![Ta bort länk för borttagning av prenumeration](./media/directory-delete-howto/delete-command.png)

6. Välj **ta bort prenumeration** för att ta bort prenumerationen och godkänn villkoren. Alla data tas bort permanent inom tre dagar. Du kan [återaktivera prenumerationen](/office365/admin/subscriptions-and-billing/reactivate-your-subscription) under den tre dagars perioden om du ändrar dig.
  
   ![Läs noggrant allmänna villkor](./media/directory-delete-howto/delete-terms.png)

7. Nu har prenumerations statusen ändrats och prenumerationen har marker ATS för borttagning. Prenumerationen går in i **avetablerat** tillstånd 72 timmar senare.

8. När du har tagit bort en prenumeration i din organisation och 72 timmar har förflutit kan du logga in igen i Azure AD Admin Center igen och det bör inte finnas någon obligatorisk åtgärd och inga prenumerationer blockerar din organisations borttagning. Du bör kunna ta bort din Azure AD-organisation.
  
   ![Skicka prenumerations kontroll på borttagnings skärmen](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Jag har en utvärderings prenumeration som blockerar borttagningen

Det finns [självbetjänings registrerings produkter](/office365/admin/misc/self-service-sign-up) som Microsoft Power BI, Rights Management Services, Microsoft Power Apps eller Dynamics 365. enskilda användare kan registrera sig via Microsoft 365, vilket även skapar en gäst användare för autentisering i din Azure AD-organisation. Dessa självbetjänings produkter blockerar katalog borttagningar tills produkterna tas bort helt från organisationen för att undvika data förlust. De kan bara tas bort av Azure AD-administratören om användaren har registrerat sig individuellt eller tilldelat produkten.

Det finns två typer av självbetjänings registrerings produkter i hur de tilldelas: 

* Tilldelning på organisations nivå: en Azure AD-administratör tilldelar produkten till hela organisationen och en användare kan aktivt använda tjänsten med den här tilldelningen på organisations nivå, även om de inte licensieras separat.
* Tilldelning på användar nivå: en enskild användare under självbetjänings registreringen tilldelar produkten själva utan administratör. När organisationen har hanterats av en administratör (se [Administratörs övertag ande av en ohanterad organisation](domains-admin-takeover.md)kan administratören direkt tilldela produkten till användare utan självbetjänings registrering.  

När du påbörjar borttagningen av en självbetjänings registrerings produkt raderas data permanent och alla användare får åtkomst till tjänsten. Alla användare som har tilldelats erbjudandet individuellt eller på organisations nivå blockeras sedan från att logga in eller komma åt befintliga data. Om du vill förhindra data förlust med självbetjänings registrerings produkten som [Microsoft Power BI instrument paneler](/power-bi/service-export-to-pbix) eller [princip konfiguration för Rights Management-tjänster](/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), se till att data säkerhets kopie ras och sparas någon annan stans.

Mer information om för närvarande tillgängliga registrerings produkter och tjänster för självbetjäning finns i [tillgängliga självbetjänings program](/office365/admin/misc/self-service-sign-up#available-self-service-programs).

Information om vad som ska förväntas när en utvärderings version Microsoft 365 prenumerationen upphör (inklusive betald partner/CSP, Enterprise-avtal eller volym licensiering) finns i följande tabell. Mer information om Microsoft 365 data kvarhållning och prenumerations livs cykel finns i [Vad händer med mina data och åtkomst när min Microsoft 365 for Business-prenumeration slutar?](/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires).

Produkt tillstånd | Data | Åtkomst till data
------------- | ---- | --------------
Aktiv (30 dagar för utvärderings version) | Data tillgängliga för alla | Användare har normal åtkomst till självbetjänings registrering, filer eller appar<br>Administratörer har normal åtkomst till Microsoft 365 administrations Center och-resurser
Borttagen | Data har tagits bort | Användare kan inte komma åt självbetjänings registrering, filer eller appar<br>Administratörer kan komma åt Microsoft 365 administrations Center för att köpa och hantera andra prenumerationer

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Hur kan jag ta bort en självbetjänings registrerings produkt i Azure Portal?

Du kan publicera en självbetjänings registrerings produkt som Microsoft Power BI eller Azure Rights Management Services i ett **borttagnings** tillstånd som genast tas bort i Azure AD-portalen.

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med ett konto som är en global administratör i organisationen. Om du försöker ta bort organisationen "contoso" som har den inledande standard domänen contoso.onmicrosoft.com loggar du in med ett UPN, till exempel admin@contoso.onmicrosoft.com .

2. Välj **licenser** och välj sedan **självbetjänings registrerings produkter**. Du kan se alla självbetjänings registrerings produkter separat från de platsbaserade prenumerationerna. Välj den produkt som du vill ta bort permanent. Här är ett exempel i Microsoft Power BI:

    ![Skärm bild som visar sidan licenser – självbetjänings registrering.](./media/directory-delete-howto/licenses-page.png)

3. Välj **ta bort** för att ta bort produkten och godkänn villkoren som data tas bort omedelbart och oåterkalleligt. Den här borttagnings åtgärden tar bort alla användare och tar bort organisations åtkomst till produkten. Klicka på Ja om du vill fortsätta med borttagningen.  

    ![Skärm bild som visar sidan "licenser – självbetjänings registrering" med fönstret "ta bort självbetjänings registrering Product" öppen.](./media/directory-delete-howto/delete-product.png)

4. När du väljer **Ja** kommer borttagning av självbetjänings produkten att initieras. Det finns ett meddelande som talar om att borttagning pågår.  

    ![Skärm bild som visar sidan "licenser – självbetjänings registrering" där meddelandet "borttagning pågår" visas.](./media/directory-delete-howto/progress-message.png)

5. Nu har den självbetjänings registrerings produktens status ändrats till **borttagen**. När du uppdaterar sidan bör produkten tas bort från sidan för självbetjänings **registrering** .  

    ![Skärm bild som visar sidan "licenser – självbetjänings registrering" med rutan "självbetjänings registrering produkten borttagen" på den högra sidan.](./media/directory-delete-howto/product-deleted.png)

6. När du har tagit bort alla produkter kan du logga in igen i Azure AD Admin Center igen och det bör inte finnas någon obligatorisk åtgärd och inga produkter som blockerar din organisation tas bort. Du bör kunna ta bort din Azure AD-organisation.

    ![användar namnet är felaktigt eller har inte hittats](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Nästa steg

[Dokumentation om Azure Active Directory](../index.yml)
