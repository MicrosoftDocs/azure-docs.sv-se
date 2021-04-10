---
title: Lägg till och verifiera anpassade domän namn – Azure Active Directory | Microsoft Docs
description: Hanterings begrepp och instruktioner för att hantera ett domän namn i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 03/12/2021
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a201452a9c708d898ee1762385955b63684876c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577979"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Hantera anpassade domän namn i Azure Active Directory

Ett domän namn är en viktig del av identifieraren för många Azure Active Directory (Azure AD)-resurser: den är en del av ett användar namn eller en e-postadress för en användare, en del av en grupps adress och är ibland en del av app-ID-URI: n för ett program. En resurs i Azure AD kan innehålla ett domän namn som ägs av den organisation som innehåller resursen. Endast en global administratör kan hantera domäner i Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-organization"></a>Ange det primära domän namnet för din Azure AD-organisation

När din organisation skapas, är det första domän namnet, till exempel "contoso.onmicrosoft.com", även det primära domän namnet. Den primära domänen är standard domän namnet för en ny användare när du skapar en ny användare. Om du anger ett primärt domän namn effektiviseras processen för en administratör att skapa nya användare i portalen. Ändra namnet på den primära domänen:

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är en global administratör för organisationen.
2. Välj **Azure Active Directory**.
3. Välj **Egna domännamn**.
  
   ![Öppna sidan användar hantering](./media/domains-manage/add-custom-domain.png)
4. Välj namnet på den domän som du vill ska vara den primära domänen.
5. Välj kommandot **gör primär** . Bekräfta valet när du uppmanas till det.
  
   ![Gör ett domän namn till primärt](./media/domains-manage/make-primary-domain.png)

Du kan ändra det primära domän namnet för din organisation till valfri verifierad anpassad domän som inte är federerad. Om du ändrar den primära domänen för din organisation ändras inte användar namnet för befintliga användare.

## <a name="add-custom-domain-names-to-your-azure-ad-organization"></a>Lägg till anpassade domän namn i din Azure AD-organisation

Du kan lägga till upp till 5000 hanterade domän namn. Om du konfigurerar alla domäner för federation med lokala Active Directory kan du lägga till upp till 2500 domän namn i varje organisation.

## <a name="add-subdomains-of-a-custom-domain"></a>Lägg till under domäner i en anpassad domän

Om du vill lägga till ett under domän namn, till exempel "europe.contoso.com" i din organisation, bör du först lägga till och verifiera rot domänen, till exempel contoso.com. Under domänen verifieras automatiskt av Azure AD. Om du vill se att den under domän som du har lagt till har verifierats uppdaterar du domän listan i webbläsaren.

Om du redan har lagt till en contoso.com-domän i en Azure AD-organisation kan du också kontrol lera europe.contoso.com för under domänen i en annan Azure AD-organisation. När du lägger till under domänen uppmanas du att lägga till en TXT-post i DNS-hosting-providern.



## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Vad du gör om du ändrar DNS-registratorn för ditt anpassade domän namn

Om du ändrar DNS-registratorn finns det inga ytterligare konfigurations åtgärder i Azure AD. Du kan fortsätta att använda domän namnet med Azure AD utan avbrott. Om du använder det anpassade domän namnet med Microsoft 365, Intune eller andra tjänster som är beroende av anpassade domän namn i Azure AD, kan du läsa dokumentationen för dessa tjänster.

## <a name="delete-a-custom-domain-name"></a>Ta bort ett anpassat domän namn

Du kan ta bort ett anpassat domän namn från din Azure AD om din organisation inte längre använder det domän namnet, eller om du behöver använda det domän namnet med en annan Azure AD.

Om du vill ta bort ett anpassat domän namn måste du först se till att inga resurser i organisationen förlitar sig på domän namnet. Du kan inte ta bort ett domän namn från din organisation om:

* Alla användare har ett användar namn, en e-postadress eller en proxyadress som innehåller domän namnet.
* Alla grupper har en e-postadress eller proxyadress som innehåller domän namnet.
* Alla program i din Azure AD har en app-ID-URI som innehåller domän namnet.

Du måste ändra eller ta bort den här resursen i din Azure AD-organisation innan du kan ta bort det anpassade domän namnet.

### <a name="forcedelete-option"></a>Alternativet ForceDelete

Du kan **ForceDelete** ett domän namn i [Azure AD Admin Center](https://aad.portal.azure.com) eller använda [Microsoft Graph API](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true). Dessa alternativ använder en asynkron åtgärd och uppdaterar alla referenser från det anpassade domän namnet som " user@contoso.com " till det initiala standard domän namnet som " user@contoso.onmicrosoft.com ."

Om du vill anropa **ForceDelete** i Azure Portal måste du se till att det finns färre än 1000 referenser till domän namnet och alla referenser där Exchange är etablerings tjänsten måste uppdateras eller tas bort i [administrations centret för Exchange](https://outlook.office365.com/ecp/). Detta omfattar Exchange Mail-Enabled säkerhets grupper och distribuerade listor. Mer information finns i [ta bort e-postaktiverade säkerhets grupper](/Exchange/recipients/mail-enabled-security-groups#Remove%20mail-enabled%20security%20groups&preserve-view=true). **ForceDelete** -åtgärden lyckas inte heller om något av följande stämmer:

* Du har köpt en domän via Microsoft 365 domän prenumerations tjänster
* Du är en partner som administreras åt en annan kund organisation

Följande åtgärder utförs som en del av **ForceDelete** -åtgärden:

* Byter namn på UPN, EmailAddress och ProxyAddress för användare med referenser till det anpassade domän namnet till det inledande standard domän namnet.
* Byter namn på EmailAddress för grupper med referenser till det anpassade domän namnet till det inledande standard domän namnet.
* Byter namn på identifierUris för program med referenser till det anpassade domän namnet till det ursprungliga standard domän namnet.

Ett fel returneras när:

* Antalet objekt som ska byta namn är större än 1000
* Ett av de program som ska byta namn är en app för flera klienter

### <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F: Varför går det inte att ta bort domänen med ett fel som säger att jag har Exchange-hanterade grupper på det här domän namnet?** <br>
**A:** I dag är vissa grupper som Mail-Enabled säkerhets grupper och distribuerade listor etablerade av Exchange och måste rensas manuellt i [administrations Center för Exchange (UK)](https://outlook.office365.com/ecp/). Det kan finnas en kvarvarande ProxyAddresses som förlitar sig på det anpassade domän namnet och måste uppdateras manuellt till ett annat domän namn. 

**F: Jag är inloggad som administratör \@ contoso.com men jag kan inte ta bort domän namnet "contoso.com"?**<br>
**A:** Du kan inte referera till det anpassade domän namn som du försöker ta bort i ditt användar konto namn. Se till att det globala administratörs kontot använder det initiala standard domän namnet (. onmicrosoft.com), till exempel admin@contoso.onmicrosoft.com . Logga in med ett annat globalt administratörs konto som till exempel admin@contoso.onmicrosoft.com eller ett annat anpassat domän namn, till exempel "fabrikam.com" där kontot finns admin@fabrikam.com .

**F: Jag klickade på knappen Ta bort domän och se `In Progress` status för borttagnings åtgärden. Hur lång tid tar det? Vad händer om det Miss lyckas?**<br>
**A:** Åtgärden ta bort domän är en asynkron bakgrunds aktivitet som byter namn på alla referenser till domän namnet. Den bör slutföras inom en minut eller två. Om det inte går att ta bort domänen kontrollerar du att du inte har:

* Appar som kon figurer ATS på domän namnet med appIdentifierURI
* Alla e-postaktiverade grupper som refererar till det anpassade domän namnet
* Fler än 1000 referenser till domän namnet

Om du upptäcker att något av villkoren inte har uppfyllts kan du rensa referenserna manuellt och försöka ta bort domänen igen.

## <a name="use-powershell-or-the-microsoft-graph-api-to-manage-domain-names"></a>Använd PowerShell eller Microsoft Graph API för att hantera domän namn

De flesta hanterings aktiviteter för domän namn i Azure Active Directory kan också slutföras med hjälp av Microsoft PowerShell eller program mässigt med hjälp av Microsoft Graph API.

* [Använda PowerShell för att hantera domän namn i Azure AD](/powershell/module/azuread/#domains&preserve-view=true)
* [Domän resurs typ](/graph/api/resources/domain)

## <a name="next-steps"></a>Nästa steg

* [Lägga till anpassade domännamn](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
* [Ta bort Exchange mail-aktiverade säkerhets grupper i Exchange administrations Center på ett anpassat domän namn i Azure AD](/Exchange/recipients/mail-enabled-security-groups#Remove%20mail-enabled%20security%20groups&preserve-view=true)
* [ForceDelete ett anpassat domän namn med Microsoft Graph-API](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)
