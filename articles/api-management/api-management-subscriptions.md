---
title: Prenumerationer i Azure API Management | Microsoft Docs
description: 'Lär dig mer om begreppet prenumerationer i Azure API Management. Konsumenter får åtkomst till API: er med hjälp av prenumerationer i Azure API Management.'
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: cdc7668b06308bd5532f4885d0e1365fc34ae989
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97511254"
---
# <a name="subscriptions-in-azure-api-management"></a>Prenumerationer i Azure API Management

Prenumerationer är ett viktigt begrepp i Azure API Management. De är det vanligaste sättet för API-konsumenter att få åtkomst till API: er som publiceras via en API Management instans. Den här artikeln innehåller en översikt över konceptet.

## <a name="what-are-subscriptions"></a>Vad är prenumerationer?

När du publicerar API: er via API Management är det enkelt och vanligt att skydda åtkomsten till dessa API: er med hjälp av prenumerations nycklar. Utvecklare som behöver använda de publicerade API: erna måste innehålla en giltig prenumerations nyckel i HTTP-begäranden när de ringer till dessa API: er. Annars avvisas anropen direkt av API Management Gateway. De vidarebefordras inte till backend-tjänsterna.

En prenumeration krävs för att få en prenumerations nyckel för åtkomst till API: er. En prenumeration är i stort sett en namngiven behållare för ett par prenumerations nycklar. Utvecklare som behöver använda de publicerade API: erna kan få prenumerationer. Och de behöver inte godkännas av API-utgivare. API-utgivare kan också skapa prenumerationer direkt för API-konsumenter.

> [!TIP]
> API Management stöder också andra metoder för att skydda åtkomsten till API: er, inklusive följande exempel:
> - [OAuth 2.0](api-management-howto-protect-backend-with-aad.md)
> - [Klient certifikat](api-management-howto-mutual-certificates-for-clients.md)
> - [Begränsa anropare IP-adresser](./api-management-access-restriction-policies.md#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Prenumerations omfång

Prenumerationer kan kopplas till olika scope: produkt, alla API: er eller en enskild API.

### <a name="subscriptions-for-a-product"></a>Prenumerationer för en produkt

Traditionellt var prenumerationer i API Management alltid kopplade till en enda [API-produkt](api-management-terminology.md) omfattning. Utvecklare hittade listan över produkter på Developer-portalen. Sedan skickar de prenumerations begär Anden för de produkter de ville använda. När en prenumerations förfrågan har godkänts, antingen automatiskt eller av API-utgivare, kan utvecklaren använda nycklarna i den för att få åtkomst till alla API: er i produkten. För närvarande visar Developer-portalen endast produkt omfattnings prenumerationer i avsnittet användar profil. 

![Produktprenumerationer](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> Under vissa scenarier kanske API-utgivare vill publicera en API-produkt till allmänheten utan krav på prenumerationer. De kan avmarkera alternativet **Kräv prenumeration** på sidan **Inställningar** för produkten i Azure Portal. Därför kan alla API: er under produkten nås utan API-nyckel.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Prenumerationer för alla API: er eller en enskild API

När vi introducerade [förbruknings](https://aka.ms/apimconsumptionblog) nivån för API Management gjorde vi några ändringar för att effektivisera nyckel hanteringen:
- Först lade vi till två fler prenumerations omfattningar: alla API: er och ett enda API. Prenumerations omfånget är inte längre begränsat till en API-produkt. Nu är det möjligt att skapa nycklar som ger åtkomst till ett API, eller alla API: er inom en API Management instans, utan att behöva skapa en produkt och lägga till API: erna till den först. Dessutom levereras varje API Management instans nu med en oföränderlig prenumeration med alla API: er. Den här prenumerationen gör det enklare och mer enkelt att testa och felsöka API: er i test konsolen.

- För det andra, tillåter API Management nu **fristående** prenumerationer. Prenumerationer behöver inte längre vara associerade med ett utvecklar-konto. Den här funktionen är användbar i scenarier som när flera utvecklare eller team delar en prenumeration.

- Slutligen kan API-utgivare nu [skapa prenumerationer](api-management-howto-create-subscriptions.md) direkt i Azure Portal:

    ![Flexibla prenumerationer](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Nästa steg
Få mer information om API Management:

+ Lär dig andra [koncept](api-management-terminology.md) i API Management.
+ Följ våra [självstudier](import-and-publish.md) för att lära dig mer om API Management.
+ På [sidan med vanliga frågor och svar](api-management-faq.md) hittar du vanliga frågor.
