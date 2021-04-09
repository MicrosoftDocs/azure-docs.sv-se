---
title: Översikt över Apache Kafka i ett samspelande moln – Azure-partner lösningar
description: Lär dig mer om att använda Apache Kafka i Azure Marketplace.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 01/15/2021
ms.author: tomfitz
ms.openlocfilehash: b4aa00b7a2cc7ca6bc968be70957c127a8e00738
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98253611"
---
# <a name="what-is-apache-kafka-for-confluent-cloud"></a>Vad är Apache Kafka för ett samfluent-moln?

Apache Kafka för ett samfluent-moln är ett Azure Marketplace-erbjudande som tillhandahåller Apache Kafka som en tjänst. Den är fullständigt hanterad så att du kan fokusera på att skapa program i stället för att hantera klustren.

För att minska belastningen på plattforms oberoende hantering kan Microsoft samar beta med flytt molnet för att bygga ett integrerat etablerings lager från Azure till flytt molnet. Det ger en konsol IDE rad upplevelse för att använda ett samfluent-moln i Azure. Du kan enkelt integrera och hantera ett hanterings moln med dina Azure-program.

Tidigare var du tvungen att köpa det samspelande moln erbjudandet i marknads platsen och konfigurera kontot i molnet separat. För att hantera konfigurationer och resurser var du tvungen att navigera mellan portalerna för Azure och överförings molnet.

Nu kan du etablera moln resurserna via en resurs leverantör som heter **Microsoft. Fluent**. Du kan skapa och hantera organisations resurser i molnet med [Azure Portal](https://portal.azure.com/), [Azure CLI](/cli/azure/)eller [Azure SDK](/azure/#languages-and-tools): er. SaaS-program (program vara som en tjänst), inklusive miljöer, kluster, ämnen, API-nycklar och hanterade anslutningar.

## <a name="capabilities"></a>Funktioner

Den djupgående integreringen mellan samordnings molnet och Azure möjliggör följande funktioner:

- Etablera en ny miljö organisations resurs från Azure Portal med helt hanterad infrastruktur.
- Effektivisera enkel inloggning (SSO) från Azure till att Samannonsera molnet med Azure Active Directory (Azure AD). Ingen separat autentisering krävs från samarbets moln portalen.
- Få en enhetlig fakturering av samkunds moln förbrukning via prenumerations fakturering i Azure.
- Hantera moln resurser från Azure Portal och spåra dem på sidan **alla resurser** med dina andra Azure-resurser.

## <a name="confluent-organization"></a>Samordnings organisation

En samordnings organisation är en resurs som tillhandahåller mappningen mellan Azure-och moln resurser. Det är den överordnade resursen för andra moln resurser.

Varje Azure-prenumeration kan innehålla flera samflytande planer. Varje "samordnings plan" mappas till ett användar konto och en organisation i en samarbets Portal. I varje samordnings organisation kan du skapa flera miljöer, kluster, ämnen och anslutningar.

När du etablerar en moln resurs i Azure får du ett organisations-ID, standard miljö och användar konto. Mer information finns i [snabb start: kom igång med ett samfluent-moln på Azure](create.md).

För fakturering är varje erbjudande för samköps moln som köpts på Marketplace till en unik organisation.

## <a name="single-sign-on"></a>Enkel inloggning

När du loggar in på Azure Portal används även dina autentiseringsuppgifter för att logga in på SaaS-portalen för. Upplevelsen använder [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) och [Azure AD SSO](../../active-directory/manage-apps/what-is-single-sign-on.md) för att tillhandahålla ett säkert och bekvämt sätt att logga in.

Mer information finns i [enkel inloggning](manage.md#single-sign-on).

## <a name="billing"></a>Fakturering

Det finns två fakturerings alternativ: betala per användning – månads plan och åtagande plan.

- Med den **månads prenumeration som du betalar per** användning får du till gång till moln förbruknings avgifter på din Azure månads faktura.
- Med en **åtagande plan** registrerar du dig för en minimal spenderad mängd och får rabatt på din allokerade användning av ett fritt moln.

Du bestämmer vilket fakturerings alternativ som ska användas när du skapar tjänsten.

## <a name="confluent-links"></a>Sambands länkar

Mer hjälp om hur du använder Apache Kafka för att samsätta molnet finns i följande länkar till den här [webbplatsen](https://docs.confluent.io/home/overview.html).

Information om fakturerings alternativ finns i:

* [Azure Marketplace med betala per användning](https://docs.confluent.io/cloud/current/billing/ccloud-azure-payg.html)
* [Azure Marketplace med åtaganden](https://docs.confluent.io/cloud/current/billing/ccloud-azure-ubb.html)

Information om hur du hanterar lösningarna finns i:

* [Skapa ett kluster i ett samfluent-moln](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)
* [Sammiljöiska moln miljöer](https://docs.confluent.io/current/cloud/using/environments.html)
* [Grunderna om att du pratar om molnet](https://docs.confluent.io/current/cloud/using/cloud-basics.html)

Information om support och villkor finns i:

* [Samfluent-support](https://support.confluent.io)
* [Tjänst villkor](https://www.confluent.io/confluent-cloud-tos).

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar en instans av Apache Kafka för ett samfluentt moln finns i [snabb start: kom igång med ett samfluent-moln på Azure](create.md).
