---
title: Så här lägger du till teknisk information för ditt SaaS-erbjudande i Microsoft Partner Center
description: Lär dig mer om att tillhandahålla teknisk information för ditt SaaS-erbjudande (Software as a Service) för Microsoft Commercial Marketplace.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 28c0be40387f411286230f94c19fa23a80e650af
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96746411"
---
# <a name="how-to-add-technical-details-for-your-saas-offer"></a>Så här lägger du till teknisk information för ditt SaaS-erbjudande

Den här artikeln beskriver hur du anger teknisk information som hjälper Microsofts kommersiella marknads plats att ansluta till din lösning. Med den här anslutningen kan vi tillhandahålla ditt erbjudande för kunden om de väljer att förvärva och hantera dem. Mer information om de här inställningarna finns i [teknisk information](plan-saas-offer.md#technical-information).

> [!NOTE]
> Om du väljer att bearbeta transaktioner oberoende av visas inte det här alternativet. Gå istället till [hur du säljer ditt SaaS-erbjudande](create-new-saas-offer-marketing.md).

## <a name="technical-configuration"></a>Teknisk konfiguration

På fliken **teknisk konfiguration** definierar du de tekniska uppgifter som den kommersiella Marketplace använder för att kommunicera med ditt SaaS-program eller-lösning. 

- **Landnings sidans URL** (krävs) – definiera webbplatsens URL för SaaS (till exempel: `https://contoso.com/signup` ) som kunderna ska landa efter att ha skaffat ditt erbjudande från den kommersiella marknads platsen och utlösa konfigurations processen från den nyligen skapade SaaS-prenumerationen.

  > [!IMPORTANT]
  > Din landnings sida bör vara igång 24/7. Detta är det enda sättet du får information om nya köp av dina SaaS-erbjudanden som görs i den kommersiella marknads platsen, eller konfigurations förfrågningar för en aktiv prenumeration på ett erbjudande.

- **Anslutning-webhook** (krävs) – för alla asynkrona händelser som Microsoft behöver skicka till dig (till exempel SaaS-prenumerationen har avbrutits) måste du ange en anslutning-webhook-URL. Vi kommer att anropa denna URL för att meddela dig om evenemanget.

  > [!IMPORTANT]
  > Webhooken bör vara igång 24/7 eftersom det är det enda sättet som du kommer att få information om uppdateringar om dina kunders SaaS prenumerationer som köpts via Comercial Marketplace.

- **Azure Active Directory klient-ID** (krävs) – du hittar klient-ID: t för din Azure Active Directory-app (Azure AD) genom att gå till bladet [Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) i Azure Active Directory. I kolumnen **visnings namn** väljer du appen. Leta sedan efter den **katalog (klient) ID-** nummer som anges (till exempel `50c464d3-4930-494c-963c-1e951d15360e` ).

- **Azure Active Directory program-ID** (obligatoriskt) – om du vill hitta ditt [program-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)går du till bladet [Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) i Azure Active Directory. I kolumnen **visnings namn** väljer du appen. Leta sedan reda på det program-ID (klient) som visas (till exempel `50c464d3-4930-494c-963c-1e951d15360e` ).

Välj **Spara utkast** innan du fortsätter till nästa flik: plan översikt.

## <a name="next-steps"></a>Nästa steg

- [Skapa planer för ditt SaaS-erbjudande](create-new-saas-offer-plans.md).