---
title: Samar beta med andra – LUIS
titleSuffix: Azure Cognitive Services
description: En app-ägare kan lägga till deltagare till redigerings resursen. Dessa deltagare kan ändra modellen, träna och publicera appen.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 5ca13784fe2f9a6a5b448bc838bf508f01b0a9fe
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095184"
---
# <a name="add-contributors-to-your-app"></a>Lägg till deltagare i din app

En app-ägare kan lägga till deltagare i appar. Dessa deltagare kan ändra modellen, träna och publicera appen. När du har [migrerat](luis-migration-authoring.md) ditt konto hanteras _deltagare_ i Azure Portal för redigerings resursen med hjälp av **åtkomst kontrolls sidan (IAM)** . Lägg till en användare med hjälp av medarbetarens e-postadress och _deltagar_ rollen.

## <a name="add-contributor-to-azure-authoring-resource"></a>Lägg till deltagare i Azure Authoring-resursen

Du har migrerat om din LUIS redigerings miljö är knuten till en redigerings resurs på sidan för att **hantera > Azure-resurser** på Luis-portalen.

1. I Azure Portal letar du reda på den Language Understanding (LUIS) Authoring-resursen. Den har typen `LUIS.Authoring` .
1. På den här resursens **Access Control (IAM)** väljer du **+ Lägg till** och väljer sedan **Lägg till roll tilldelning**.

    ![I Azure Portal lägger du till roll tilldelning på redigerings resurs.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. I fönstret **Lägg till roll tilldelning** väljer du **rollen** deltagare. I alternativet **tilldela åtkomst till väljer du** **Azure AD-användare, grupp eller tjänstens huvud namn**. I alternativet **Välj** anger du användarens e-postadress. Om användaren är känd av fler än en e-postadress för samma domän kontrollerar du att ange det _primära_ e-postkontot.

    ![Lägg till användarens e-post till deltagar rollen för Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    När användarens e-postadress hittas väljer du kontot och väljer **Spara**.

    Om du har problem med den här roll tilldelningen kan du läsa mer i [tilldela Azure-roller](../../role-based-access-control/role-assignments-portal.md) och [fel sökning av Azure-åtkomst kontroll](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments).

## <a name="view-the-app-as-a-contributor"></a>Visa appen som en deltagare

När du har lagts till som deltagare loggar du [in på Luis-portalen](sign-in-luis-portal.md).

[!INCLUDE [switch azure directories](includes/switch-azure-directories.md)]

### <a name="users-with-multiple-emails"></a>Användare med flera e-postmeddelanden

Om du lägger till deltagare i en LUIS-app, anger du den exakta e-postadressen. Medan Azure Active Directory (Azure AD) tillåter att en enskild användare har mer än ett e-postkonto som används interutbytbart, kräver LUIS att användaren loggar in med den e-postadress som angavs när deltagaren lades till.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Azure Active Directory resurser

Om du använder [Azure Active Directory](../../active-directory/index.yml) (Azure AD) i din organisation behöver language Understanding (Luis) behörighet till informationen om användarnas åtkomst när de vill använda Luis. De resurser som LUIS kräver är minimala.

Den detaljerade beskrivningen visas när du försöker registrera dig för ett konto som har administratörs medgivande eller som inte kräver administratörs medgivande, till exempel administratörs medgivande:

* Gör att du kan logga in på appen med ditt organisations konto och låta appen läsa din profil. Appen kan också läsa grundläggande företags information. Detta ger LUIS behörighet att läsa grundläggande profil data, t. ex. användar-ID, e-post, namn
* Tillåter appen att se och uppdatera dina data, även när du inte använder appen för närvarande. Behörigheten krävs för att uppdatera åtkomsttoken för användaren.


### <a name="azure-active-directory-tenant-user"></a>Azure Active Directory klient organisations användare

LUIS använder standard Azure Active Directory (Azure AD) för godkännande flöden.

Klient organisationens administratör bör arbeta direkt med den användare som behöver åtkomst beviljad att använda LUIS i Azure AD.

* Först loggar användaren in på LUIS och ser popup-dialogrutan som kräver administratörs godkännande. Användaren kontaktar klient administratören innan du fortsätter.
* Sedan loggar klient organisations administratören in på LUIS och ser en dialog ruta för godkännande flöde. Det här är den dialog som administratören behöver för att ge användaren behörighet. När administratören godkänner behörigheten kan användaren fortsätta med LUIS. Om klient organisationens administratör inte kan logga in på LUIS kan administratören komma åt [medgivande](https://account.activedirectory.windowsazure.com/r#/applications) för Luis. På den här sidan kan du filtrera listan till objekt som innehåller namnet `LUIS` .

Om klient administratören bara vill att vissa användare ska använda LUIS, finns det ett par möjliga lösningar:
* Ge "administrativt medgivande" (medgivande till alla användare av Azure AD), men Ställ sedan in på "Ja" "användar tilldelning krävs" under egenskaper för företags program och tilldela/Lägg slutligen till de önskade användarna i programmet. Med den här metoden ger administratören fortfarande "administrativt medgivande" till appen, men det är möjligt att kontrol lera vilka användare som har åtkomst till den.
* En andra lösning är att använda [API: et för identitets-och åtkomst hantering i Azure AD i Microsoft Graph](/graph/azuread-identity-access-management-concept-overview) för att ge medgivande till varje enskild användare.

Lär dig mer om Azure Active Directory-användare och medgivande:
* [Begränsa din app](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) till en uppsättning användare

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du använder versioner](luis-how-to-manage-versions.md) för att kontrol lera appens livs cykel.
* Förstå begreppen, inklusive [redigering av resurser](luis-how-to-azure-subscription.md#authoring-key) och [deltagare](luis-how-to-azure-subscription.md#contributions-from-other-authors) på den resursen.
* Lär dig [hur du skapar](luis-how-to-azure-subscription.md) redigerings-och körnings resurser
* Migrera till den nya [redigerings resursen](luis-migration-authoring.md)