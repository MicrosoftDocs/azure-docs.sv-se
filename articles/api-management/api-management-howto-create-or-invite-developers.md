---
title: Hantera användar konton i Azure API Management | Microsoft Docs
description: Lär dig hur du skapar eller bjuder in användare i Azure API Management. Visa ytterligare resurser som du kan använda när du har skapat ett Developer-konto.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 92e032eb104835788f515cc7800fe5dacfa8adaa
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566139"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Hantera användarkonton i Azure API Management

I API Management är utvecklare användare av de API: er som du exponerar med API Management. Den här guiden visar hur du skapar och bjuder in utvecklare att använda de API: er och produkter som du gör tillgängliga för dem med din API Management-instans. Information om hur du hanterar användar konton program mässigt finns i dokumentationen för [användar enheter](/rest/api/apimanagement/2019-12-01/user) i [API Management rest](/rest/api/apimanagement/) -referens.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Förutsättningar

Slutför uppgifter i den här artikeln: [skapa en Azure API Management-instans](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-new-developer"></a><a name="create-developer"> </a>Skapa en ny utvecklare

Följ stegen i det här avsnittet om du vill lägga till en ny användare:

1. Välj fliken **användare** till vänster på skärmen.
2. Tryck på **+ Lägg till**.
3. Ange lämplig information för användaren.
4. Tryck på **Lägg till**.

    ![Lägga till en ny användare](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Som standard är nya skapade utvecklarresurser **aktiva**och kopplade till gruppen **utvecklare** . De utvecklar konton som är i ett **aktivt** tillstånd kan användas för att få åtkomst till alla API: er som de har prenumerationer för. Information om hur du associerar nya utvecklare med ytterligare grupper finns i [så här associerar du grupper med utvecklare][How to associate groups with developers].

## <a name="invite-a-developer"></a><a name="invite-developer"> </a>Bjud in en utvecklare
Om du vill bjuda in en utvecklare följer du stegen i det här avsnittet:

1. Välj fliken **användare** till vänster på skärmen.
2. Tryck på **+ Bjud in**.

Ett bekräftelse meddelande visas, men den nyligen inbjudna utvecklaren visas inte i listan förrän du accepterar inbjudan. 

När en utvecklare bjuds in skickas ett e-postmeddelande till utvecklaren. Det här e-postmeddelandet genereras med en mall och kan anpassas. Mer information finns i [Konfigurera e-postmallar][Configure email templates].

När inbjudan godkänns blir kontot aktivt.

## <a name="deactivate-or-reactivate-a-developer-account"></a><a name="block-developer"> </a> Inaktivera eller återaktivera ett Developer-konto

Som standard är de nyligen skapade eller inbjudna utvecklarnas konton **aktiva**. Klicka på **blockera**om du vill inaktivera ett Developer-konto. Återaktivera ett blockerat utvecklarresurser genom att klicka på **Aktivera**. Ett blockerat konto för utvecklare kan inte komma åt Developer-portalen eller anropa API: er. Klicka på **ta bort**om du vill ta bort ett användar konto.

Följ stegen nedan om du vill blockera en användare.

1. Välj fliken **användare** till vänster på skärmen.
2. Klicka på den användare som du vill blockera.
3. Tryck på **block**.

## <a name="reset-a-user-password"></a>Återställa ett användarlösenord

Information om hur du program mässigt arbetar med användar konton finns i dokumentationen för användar enheter i [API Management REST API](/rest/api/apimanagement/) referens. Om du vill återställa ett lösen ord för användar kontot till ett visst värde, kan du använda åtgärden [Uppdatera en användare](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) och ange önskat lösen ord.

## <a name="next-steps"></a><a name="next-steps"> </a>Nästa steg
När ett utvecklar-konto har skapats kan du associera det med roller och prenumerera på produkter och API: er. Mer information finns i [så här skapar du och använder grupper][How to create and use groups].

[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png


[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
