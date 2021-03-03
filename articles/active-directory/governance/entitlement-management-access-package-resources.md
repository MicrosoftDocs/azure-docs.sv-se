---
title: Ändra resurs roller för ett Access-paket i Azure AD-hantering av rättigheter – Azure Active Directory
description: Lär dig hur du ändrar resurs roller för ett befintligt Access-paket i Azure Active Directory rättighets hantering.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/14/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65f69cf492ec3e28d7f4aa86971dc6c91b34bdf5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644188"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Ändra resurs roller för ett Access-paket i hantering av Azure AD-rättigheter

Som en Access Package Manager kan du när som helst ändra resurserna i ett Access-paket utan att behöva oroa dig för att du ska få åtkomst till de nya resurserna eller ta bort åtkomsten från de tidigare resurserna. I den här artikeln beskrivs hur du ändrar resurs roller för ett befintligt Access-paket.

Den här videon ger en översikt över hur du ändrar ett Access-paket.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>Kontrol lera katalogen för resurser

Om du behöver lägga till resurser i ett Access-paket bör du kontrol lera om resurserna som behövs är tillgängliga i katalogen. Om du är en Access Package Manager kan du inte lägga till resurser i en katalog, även om du äger dem. Du är begränsad till att använda de resurser som är tillgängliga i katalogen.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **katalog** och öppnar katalogen.

1. Klicka på **resurser** på den vänstra menyn för att visa listan över resurser i katalogen.

    ![Lista över resurser i en katalog](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. Om du är en Access Package Manager och behöver lägga till resurser i katalogen kan du be katalog ägaren att lägga till dem.

## <a name="add-resource-roles"></a>Lägg till resurs roller

En resurs roll är en samling behörigheter som är kopplade till en resurs. Hur du gör resurser som är tillgängliga för användare att begära är att lägga till resurs roller till ditt åtkomst paket. Du kan lägga till resurs roller för grupper, team, program och SharePoint-webbplatser.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **resurs roller** på den vänstra menyn.

1. Klicka på **Lägg till resurs roller** för att öppna sidan Lägg till resurs roller för åtkomst till paket.

    ![Åtkomst paket – Lägg till resurs roller](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. Beroende på om du vill lägga till en grupp, ett team, ett program eller en SharePoint-webbplats, utför du stegen i någon av följande resurs Rolls avsnitt.

## <a name="add-a-group-or-team-resource-role"></a>Lägg till en grupp-eller team resurs roll

Du kan låta rättighets hantering automatiskt lägga till användare i en grupp eller ett team i Microsoft Teams när de tilldelas ett åtkomst paket. 

- När en grupp eller ett team ingår i ett Access-paket och en användare tilldelas till det åtkomst paketet, läggs användaren till i gruppen eller teamet, om det inte redan finns.
- När en användares åtkomst paket tilldelning går ut tas de bort från gruppen eller teamet, om de inte har en tilldelning till ett annat Access-paket som innehåller samma grupp eller team.

Du kan välja valfri [Azure AD-säkerhetsgrupp eller Microsoft 365 grupp](../fundamentals/active-directory-groups-create-azure-portal.md). Administratörer kan lägga till valfri grupp i en katalog. Katalog ägare kan lägga till valfri grupp till katalogen om de är ägare till gruppen. Behåll följande Azure AD-begränsningar i åtanke när du väljer en grupp:

- När en användare, inklusive en gäst, läggs till som en medlem i en grupp eller ett team kan de Se alla andra medlemmar i gruppen eller teamet.
- Azure AD kan inte ändra medlemskap för en grupp som har synkroniserats från Windows Server Active Directory att använda Azure AD Connect eller som skapades i Exchange Online som en distributions grupp.  
- Det går inte att uppdatera medlemskapet i dynamiska grupper genom att lägga till eller ta bort en medlem, så dynamiska grupp medlemskap är inte lämpliga för användning med hantering av rättigheter.

Mer information finns i [Jämför grupper](/office365/admin/create-groups/compare-groups) och [Microsoft 365 grupper och Microsoft Teams](/microsoftteams/office-365-groups).

1. Öppna fönstret Välj grupper genom att klicka på **grupper och team** på sidan **Lägg till resurs roller för åtkomst till paket** .

1. Välj de grupper och team som du vill ta med i åtkomst paketet.

    ![Åtkomst paket – Lägg till resurs roller – Välj grupper](./media/entitlement-management-access-package-resources/group-select.png)

1. Klicka på **Välj**.

    När du har valt gruppen eller teamet visas en av följande under typer i kolumnen **under** typ:

    | Undertyp | Beskrivning |
    | --- | --- |
    | Säkerhet | Används för att bevilja åtkomst till resurser. |
    | Distribution | Används för att skicka aviseringar till en grupp med personer. |
    | Microsoft 365 | Microsoft 365 grupp som inte är team-aktiverad. Används för samarbete mellan användare, både inom och utanför företaget. |
    | Team | Microsoft 365 grupp som är team-aktiverade. Används för samarbete mellan användare, både inom och utanför företaget. |

1. Välj **ägare** eller **medlem** i listan **roll** .

    Vanligt vis väljer du medlems rollen. Om du väljer ägar rollen så att användarna kan lägga till eller ta bort andra medlemmar eller ägare.

    ![Åtkomst paket – Lägg till resurs roll för en grupp eller ett team](./media/entitlement-management-access-package-resources/group-role.png)

1. Klicka på **Lägg till**.

    Alla användare med befintliga tilldelningar till åtkomst paketet blir automatiskt medlemmar i den här gruppen eller teamet när de läggs till.

## <a name="add-an-application-resource-role"></a>Lägg till en program resurs roll

Du kan låta Azure AD automatiskt tilldela användare åtkomst till ett Azure AD Enterprise-program, inklusive både SaaS-program och din organisations program federerade till Azure AD, när en användare tilldelas ett Access-paket. För program som integreras med Azure AD via federerad enkel inloggning utfärdar Azure AD federationsmetadata för användare som är tilldelade till programmet.

Program kan ha flera roller. När du lägger till ett program i ett Access-paket, om det programmet har fler än en roll, måste du ange rätt roll för dessa användare. Om du utvecklar program kan du läsa mer om hur dessa roller läggs till i dina program i [How to: Configure the roll Claim som utfärdats i SAML-token för företags program](../develop/active-directory-enterprise-app-role-management.md).

När en program roll är en del av ett Access-paket:

- När en användare tilldelas ett åtkomst paket läggs användaren till i program rollen, om den inte redan finns.
- När en användares åtkomst paket tilldelning går ut tas deras åtkomst bort från programmet, om de inte har en tilldelning till ett annat Access-paket som innehåller den program rollen.

Här följer några saker att tänka på när du väljer ett program:

- Program kan också ha grupper som har tilldelats till deras roller också.  Du kan välja att lägga till en grupp i stället för en program roll i ett Access-paket, men programmet kommer inte att vara synligt för användaren som en del av Access-paketet i åtkomst portalen.

1. Öppna fönstret Välj program genom att klicka på **program** på sidan **Lägg till resurs roller för åtkomst till paket** .

1. Välj de program som du vill ta med i åtkomst paketet.

    ![Åtkomst paket – Lägg till resurs roller – Välj program](./media/entitlement-management-access-package-resources/application-select.png)

1. Klicka på **Välj**.

1. Välj en program roll i listan **roll** .

    ![Åtkomst paket – Lägg till resurs roll för ett program](./media/entitlement-management-access-package-resources/application-role.png)

1. Klicka på **Lägg till**.

    Alla användare med befintliga tilldelningar till åtkomst paketet får automatiskt åtkomst till det här programmet när det läggs till.

## <a name="add-a-sharepoint-site-resource-role"></a>Lägg till en resurs roll för SharePoint-webbplats

Azure AD kan automatiskt tilldela användare åtkomst till en SharePoint Online-webbplats eller SharePoint Online-webbplatssamling när de tilldelas ett åtkomst paket.

1. På sidan **Lägg till resurs roller att komma åt paket** klickar du på **SharePoint-webbplatser** för att öppna fönstret Välj SharePoint Online-webbplatser.

    :::image type="content" source="media/entitlement-management-access-package-resources/resource-sharepoint-add.png" alt-text="Åtkomst paket – Lägg till resurs roller – Välj SharePoint-webbplatser – Portal-vy":::

1. Välj de SharePoint Online-webbplatser som du vill ta med i åtkomst paketet.

    ![Åtkomst paket – Lägg till resurs roller – Välj SharePoint Online-webbplatser](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. Klicka på **Välj**.

1. I listan **roll** väljer du en SharePoint Online-webbplats roll.

    ![Åtkomst paket – Lägg till resurs roll för en SharePoint Online-webbplats](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. Klicka på **Lägg till**.

    Alla användare med befintliga tilldelningar till åtkomst paketet får automatiskt åtkomst till den här SharePoint Online-webbplatsen när den läggs till.

## <a name="remove-resource-roles"></a>Ta bort resurs roller

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **resurs roller** på den vänstra menyn.

1. Leta upp den resurs roll som du vill ta bort i listan över resurs roller.

1. Klicka på ellipsen (**...**) och sedan på **ta bort resurs roll**.

    Alla användare med befintliga tilldelningar till åtkomst paketet får automatiskt åtkomst till den här resurs rollen när den tas bort.

## <a name="when-changes-are-applied"></a>När ändringar tillämpas

I hantering av rättigheter bearbetar Azure AD Mass ändringar för tilldelning och resurser i dina åtkomst paket flera gånger per dag. Så om du gör en tilldelning eller ändrar resurs rollerna för ditt åtkomst paket, kan det ta upp till 24 timmar innan ändringen görs i Azure AD, plus den tid det tar att sprida ändringarna till andra Microsoft Online Services eller anslutna SaaS-program. Om din ändring bara påverkar några få objekt tar ändringen troligen bara några minuter att tillämpa i Azure AD, efter vilka andra Azure AD-komponenter kommer att identifiera ändringen och uppdatera SaaS-programmen. Om din ändring påverkar tusentals objekt tar ändringen längre tid. Om du till exempel har ett Access-paket med 2 program och 100 användar tilldelningar och du bestämmer dig för att lägga till en SharePoint-webbplats roll i åtkomst paketet kan det uppstå en fördröjning tills alla användare är en del av den SharePoint-webbplatsens roll. Du kan övervaka förloppet via Azure AD audit-loggen, Azure AD-etablerings loggen och gransknings loggarna för SharePoint-webbplatsen.

När du tar bort en medlem i ett team tas de även bort från gruppen Microsoft 365. Borttagningen från teamets chattfunktioner kan vara fördröjd. Mer information finns i [grupp medlemskap](/microsoftteams/office-365-groups#group-membership).

## <a name="next-steps"></a>Nästa steg

- [Skapa en basgrupp och lägga till medlemmar med hjälp av Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Gör så här: konfigurera det roll anspråk som utfärdats i SAML-token för företags program](../develop/active-directory-enterprise-app-role-management.md)
- [Introduktion till SharePoint Online](/sharepoint/introduction)