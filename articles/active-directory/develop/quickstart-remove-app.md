---
title: 'Gör så här: ta bort en registrerad app från Microsoft Identity Platform | Azure'
titleSuffix: Microsoft identity platform
description: I den här instruktionen får du lära dig hur du tar bort ett program som är registrerat hos Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: a0543905f41abc9c21327159d63f7adaa5b025dc
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645899"
---
# <a name="how-to-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Ta bort ett program som är registrerat med Microsoft Identity Platform

Enterprise-utvecklare och SaaS-leverantörer (Software-as-a-Service) som har registrerade program med Microsoft Identity Platform kan behöva ta bort en program registrering.

I följande avsnitt får du lära dig att:

* Ta bort ett program som skapats av dig eller din organisation
* Ta bort ett program som skapats av en annan organisation

## <a name="prerequisites"></a>Förutsättningar

* Ett [program som är registrerat i din Azure AD-klient](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Ta bort ett program som skapats av dig eller din organisation

Program som du eller din organisation har registrerat representeras av både ett programobjekt och ett tjänsthuvudnamnsobjekt i din klientorganisation. Mer information finns i [Programobjekt och tjänsthuvudnamnsobjekt](./app-objects-and-service-principals.md).

> [!NOTE]
> Om du tar bort ett program raderas även dess tjänst huvud objekt i programmets Hem Katalog. För program med flera klienter tas inte tjänstens huvud objekt i andra kataloger bort.

Om du vill ta bort ett program ska du listas som ägare till programmet eller ha administratörs behörighet.

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>.
1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: på den översta menyn för att välja den klient som appen är registrerad i.
1. Sök och välj **Azure Active Directory**. 
1. Under **Hantera** väljer du **Appregistreringar**  och väljer det program som du vill konfigurera. När du har valt appen ser du programmets **översiktssida**.
1. På sidan **Översikt** väljer du **Ta bort**.
1. Välj **Ja** för att bekräfta att du vill ta bort appen.

## <a name="remove-an-application-authored-by-another-organization"></a>Ta bort ett program som skapats av en annan organisation

Om du visar **Appregistreringar** i kontexten för en klientorganisation kommer en delmängd av de program som visas under fliken **Alla appar** från en annan klientorganisation och registrerades i din klientorganisation under medgivandeprocessen. Mer specifikt representeras de endast av en tjänsthuvudnamnsobjekt i din klientorganisation utan motsvarande programobjekt. Mer information om skillnaderna mellan program- och tjänsthuvudnamnsobjekt finns i [Programobjekt och tjänsthuvudnamnsobjekt i Azure AD](./app-objects-and-service-principals.md).

För att kunna ta bort åtkomsten för ett program till din katalog (efter att medgivande har givits) måste företagets administratör ta bort dess tjänsthuvudnamn. Administratören måste ha global nytt-åtkomst och kan ta bort programmet via Azure Portal eller använda [Azure AD PowerShell-cmdletar](/previous-versions/azure/jj151815(v=azure.100)) för att ta bort åtkomst.

## <a name="next-steps"></a>Nästa steg

Läs mer om [program-och tjänst huvud objekt](app-objects-and-service-principals.md) i Microsoft Identity Platform.
