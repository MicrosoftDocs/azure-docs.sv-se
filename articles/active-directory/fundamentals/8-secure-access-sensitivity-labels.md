---
title: Kontrol lera extern åtkomst till resurser i Azure Active Directory med känslighets etiketter.
description: Använd känslighets etiketter som en del av din övergripande säkerhets plan för extern åtkomst.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef987305fadb83f9ed8b3f080cfb5a8d52fc36df
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97744105"
---
# <a name="control-access-with-sensitivity-labels"></a>Kontrol lera åtkomst med känslighets etiketter 

[Känslighets etiketter](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels?view=o365-worldwide) hjälper dig att kontrol lera åtkomsten till ditt innehåll i Office 365-program och i behållare som Microsoft Teams, Microsoft 365 grupper och SharePoint-webbplatser. De kan skydda ditt innehåll utan att hindra användarnas samarbets-och produktions möjligheter. Med känslighets etiketter kan du skicka din organisations innehåll mellan enheter, appar och tjänster, samtidigt som du skyddar dina data och uppfyller dina efterlevnads-och säkerhets principer. 

Med känslighets etiketter kan du:

* **Klassificera innehåll utan att lägga till några skydds inställningar**. Du kan tilldela en klassificering till innehåll (som en etikett) som finns kvar och roamas med ditt innehåll när det används och delas. Du kan använda den här klassificeringen till att generera användningsrapporter och se aktivitetsdata för ditt känsliga innehåll.

* **Använd skydds inställningar som kryptering, vattenstämplar och åtkomst begränsningar**. Användare kan till exempel använda en konfidentiell etikett på ett dokument eller e-postmeddelande, och etiketten kan [kryptera innehållet](https://docs.microsoft.com/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide) och lägga till en "konfidentiell"-vattenstämpel. Dessutom kan du [använda en känslighets etikett för en behållare](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide) som en SharePoint-webbplats och se om externa användare kan komma åt innehållet den innehåller.

Känslighets etiketter för e-post och annat innehåll som överförs med innehållet. Känslighets etiketter på behållare kan begränsa åtkomsten till behållaren, men innehållet i behållaren ärver inte etiketten. En användare kan till exempel ta innehåll från en skyddad plats, ladda ned den och sedan dela den utan begränsningar om inte innehållet också har en känslighets etikett.

 >[!NOTE]
>Om du vill använda känslighets etiketter måste användarna vara inloggade på sitt Microsoft arbets-eller skol konto. 

 
## <a name="permissions-necessary-to-create-and-manage-sensitivity-levels"></a>Behörigheter som krävs för att skapa och hantera känslighets nivåer

Medlemmar i ditt Compliance team som kommer att skapa känslighets etiketter behöver behörighet till Microsoft 365 Compliance Center, Microsoft 365 Security Center eller säkerhets & Compliance Center.

Som standard har globala administratörer för din klient åtkomst till dessa administrations Center och kan ge efterlevnad och andra personer åtkomst, utan att ge dem alla behörigheter för en klient administratör. För den här delegerade begränsade administratörs åtkomsten lägger du till användare i roll gruppen kompatibilitets data administratör, regelefterlevnad eller säkerhets administratör.

 

## <a name="determine-your-sensitivity-label-strategy"></a>Ta reda på strategi för känslighets etiketter

När du funderar på att styra extern åtkomst till ditt innehåll måste du bestämma följande:

**För allt innehåll och behållare**

* Hur kan du definiera vad som är hög, medel eller låg inverkan på företaget (HBI, MBI, LBI)? Överväg att din organisation påverkas om vissa typer av innehåll delas på ett felaktigt sätt.

   * Innehåll med vissa typer av [innehålls känsligt innehåll](https://docs.microsoft.com/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide), till exempel kredit kort eller Passport-nummer

   * Innehåll som skapats av vissa grupper eller personer (till exempel tjänstemän, ekonomi chefer eller chefer)

   * Innehåll i vissa bibliotek eller webbplatser. Till exempel behållare som är värdar för organisations strategi eller privata finansiella data

   * Andra villkor

* Vilka kategorier av innehåll (t. ex. HBI innehåll) ska begränsas från åtkomst av externa användare?

   * Begränsningar kan omfatta åtgärder som att begränsa åtkomsten till behållare och Kryptera innehåll.

* Vilka standardinställningar ska användas för HBI-data, webbplatser eller Microsoft 365s grupper?

* Var kommer du att använda känslighets etiketter för att [märka och övervaka](https://docs.microsoft.com/microsoft-365/compliance/label-analytics?view=o365-worldwide), jämfört med [kryptering](https://docs.microsoft.com/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide) eller för att [genomdriva åtkomst begränsningar för behållare](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)?

**För e-post och innehåll**

* Vill du [använda känslighets etiketter automatiskt](https://docs.microsoft.com/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide) för innehåll eller göra det manuellt?

   * Vill du [rekommendera användarna att använda en etikett](https://docs.microsoft.com/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide)om du väljer att göra det manuellt?

**För behållare**

* Vilka kriterier avgör om M365 grupper, grupper eller SharePoint-webbplatser kräver åtkomst att begränsas med hjälp av känslighets etiketter?

* Vill du bara att etikett innehållet i dessa behållare flyttas framåt eller vill du [automatiskt märka](https://docs.microsoft.com/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide) befintliga filer i SharePoint och OneDrive?

Se dessa [vanliga scenarier för känslighets etiketter](https://docs.microsoft.com/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide) för andra idéer om hur du kan använda känslighets etiketter.

### <a name="sensitivity-labels-on-email-and-content"></a>Känslighets etiketter för e-post och innehåll

När du tilldelar en känslighets etikett till ett dokument eller e-postmeddelande, är det som en stämpel som används på innehåll som är anpassningsbart, tydligt text och beständigt. 

* **Anpassningsbar** innebär att du kan skapa etiketter som passar din organisation och bestämma vad som händer när de tillämpas.

* **Clear text** betyder att den är en del av objektets metadata och kan läsas av program och tjänster så att de kan tillämpa sina egna skydds åtgärder.

* **Persistent** innebär att etiketten och alla tillhör ande skydd i roaming med innehållet och blir grunden för att tillämpa och tillämpa principer.

 

> [!NOTE]
> Varje innehålls element kan ha en enskild känslighets etikett.


### <a name="sensitivity-labels-on-containers"></a>Känslighets etiketter på behållare

Du kan använda känslighets etiketter på behållare som [Microsoft 365 grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-assign-sensitivity-labels), [Microsoft Teams](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)och [SharePoint-webbplatser](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide). När du använder den här känslighets etiketten för en behållare som stöds, tillämpar etiketten automatiskt klassificerings-och skydds inställningarna på den anslutna webbplatsen eller gruppen. Känslighets etiketter för dessa behållare kan styra följande aspekter av behållare:

* **Sekretess**. Du kan välja vem som ska kunna se webbplatsen: vissa användare, alla interna användare eller någon.

* **Åtkomst till externa användare**. Anger om grupp ägaren kan lägga till gäster i gruppen.

* **Åtkomst från ohanterade enheter**. Anger om och hur ohanterade enheter kan komma åt innehåll.

 

![En skärm bild av etiketter för att redigera känslighet](media/secure-external-access/8-edit-label.png)

 

När du använder en känslighets etikett för en behållare, till exempel en SharePoint-webbplats, tillämpas den inte på innehåll där: känslighets etiketter på behållare styr åtkomsten till innehållet i behållaren. 

* Om du automatiskt vill tillämpa etiketter på innehållet i behållaren, se [Använd en känslighet för innehåll automatiskt](https://docs.microsoft.com/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide).

* Om du vill att användarna ska kunna tillämpa etiketter på det här innehållet manuellt, se till att du har [aktiverat känslighets etiketter för Office-filer i SharePoint och OneDrive](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files?view=o365-worldwide).

### <a name="plan-to-implement-sensitivity-labels"></a>Planera att implementera känslighets etiketter

När du har fastställt hur du vill använda känslighets etiketter och vilka innehåll och platser du vill använda, kan du läsa följande dokumentation som hjälper dig att utföra implementeringen.

1. [Kom igång med känslighets etiketter](https://docs.microsoft.com/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide)

2. [Skapa en distributions strategi](https://docs.microsoft.com/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide)

3. [Skapa och publicera känslighets etiketter](https://docs.microsoft.com/microsoft-365/compliance/create-sensitivity-labels?view=o365-worldwide)

4. [Begränsa åtkomsten till innehåll med känslighets etiketter för att tillämpa kryptering](https://docs.microsoft.com/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide)

5. [Använd känslighets etiketter med team, grupper och platser](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)

6. [Aktivera känslighets etiketter för Office-filer i SharePoint och OneDrive](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files?view=o365-worldwide)

### <a name="next-steps"></a>Nästa steg

Se följande artiklar om hur du skyddar extern åtkomst till resurser. Vi rekommenderar att du vidtar åtgärder i den angivna ordningen.

1. [Fastställ önskad säkerhets position för extern åtkomst](1-secure-access-posture.md)

2. [Identifiera ditt nuvarande tillstånd](2-secure-access-current-state.md)

3. [Skapa en styrnings plan](3-secure-access-plan.md)

4. [Använda grupper för säkerhet](4-secure-access-groups.md)

5. [Över gång till Azure AD B2B](5-secure-access-b2b.md)

6. [Säker åtkomst med hantering av rättigheter](6-secure-access-entitlement-managment.md)

7. [Säker åtkomst med principer för villkorlig åtkomst](7-secure-access-conditional-access.md)

8. [Säker åtkomst med känslighets etiketter](8-secure-access-sensitivity-labels.md) (du är här.)

9. [Säker åtkomst till Microsoft Teams, OneDrive och SharePoint](9-secure-access-teams-sharepoint.md)
