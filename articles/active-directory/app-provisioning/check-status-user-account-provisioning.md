---
title: Rapportera automatisk användar konto etablering till SaaS-program
description: Lär dig hur du kontrollerar status för automatiska etablerings jobb för användar konton och hur du felsöker etablering av enskilda användare.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/09/2018
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 5d93cec201feeb43700fe849f2fddc8eaf22488a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579492"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Självstudie: rapportering om automatisk etablering av användar konton

Azure Active Directory (Azure AD) innehåller en [tjänst för användar konto etablering](user-provisioning.md) som hjälper till att automatisera etableringen av inetablering av användar konton i SaaS-appar och andra system, i syfte att hantera livs cykel hantering från slut punkt till slut punkt. Azure AD har stöd för för hands integrerade användar etablerings anslutningar för alla program och system med användar etablerings självstudier [här](../saas-apps/tutorial-list.md).

Den här artikeln beskriver hur du kontrollerar status för etablerings jobb när de har kon figurer ATS och hur du felsöker etablering av enskilda användare och grupper.

## <a name="overview"></a>Översikt

Etablerings anslutningar konfigureras och konfigureras med hjälp av [Azure Portal](https://portal.azure.com), genom att följa den [angivna dokumentationen](../saas-apps/tutorial-list.md) för programmet som stöds. När du har konfigurerat och kört kan etablerings jobb rapporteras på ett av två sätt:

* **Azure Portal** – den här artikeln beskriver främst hämtning av rapportinformation från [Azure Portal](https://portal.azure.com), vilket ger både en sammanfattnings rapport för etablering och detaljerade etablerings loggar för ett angivet program.
* **Gransknings-API** – Azure Active Directory innehåller också ett gransknings-API som möjliggör program mässig hämtning av detaljerade gransknings loggar för etablering. Se [Azure Active Directory audit API-referens](/graph/api/resources/directoryaudit) för dokumentation som är särskilt för att använda detta API. Även om den här artikeln inte särskilt beskriver hur du använder API: et, innehåller den information om vilka typer av etablerings händelser som registreras i gransknings loggen.

### <a name="definitions"></a>Definitioner

I den här artikeln används följande villkor, som definieras nedan:

* **Käll system** – databasen med användare som Azure AD Provisioning-tjänsten synkroniserar från. Azure Active Directory är käll systemet för merparten av förintegrerade etablerings anslutningar, men det finns vissa undantag (exempel: inkommande synkronisering av arbets dagar).
* **Mål system** – databasen med användare som Azure AD Provisioning-tjänsten synkroniserar till. Detta är vanligt vis ett SaaS-program (exempel: Salesforce, ServiceNow, G Suite, Dropbox for Business), men i vissa fall kan det finnas ett lokalt system, till exempel Active Directory (exempel: inkommande synkronisering av arbets dagar till Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Hämtar etablerings rapporter från Azure Portal

Starta genom att starta [Azure Portal](https://portal.azure.com) och **Azure Active Directory** &gt;  &gt; **etablerings loggar för företags program (för hands version)** i avsnittet **aktivitet** för att få information om etablerings rapporter för ett angivet program. Du kan också bläddra till företags programmet för vilken etablering har kon figurer ATS. Om du till exempel konfigurerar användare till LinkedIn-höjning är navigerings Sök vägen till program informationen:

**Azure Active Directory > företags program > alla program > LinkedIn-höjning**

Härifrån kan du komma åt både etablerings förlopps fältet och etablerings loggarna, som beskrivs nedan.

## <a name="provisioning-progress-bar"></a>Förlopps indikator för etablering

[Etablerings förlopps indikatorn](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) visas på fliken **etablering** för ett angivet program. Den finns i avsnittet **aktuell status** och visar statusen för den aktuella inledande eller stegvisa cykeln. I det här avsnittet visas även:

* Det totala antalet användare och/grupper som har synkroniserats och som för närvarande ingår i omfånget för etablering mellan käll systemet och mål systemet.
* Den senaste gången som synkroniseringen kördes. Synkroniseringar sker vanligt vis var 20-40 minut, efter att en [första cykel](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) har slutförts.
* Huruvida en [första cykel](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) har slutförts eller inte.
* Huruvida etablerings processen har placerats i karantän och vad orsaken till karantän status är (till exempel fel vid kommunikation med mål systemet på grund av ogiltiga autentiseringsuppgifter för admin).

Den **aktuella statusen** bör vara den första plats som administratörer ser för att kontrol lera etablerings jobbets operativa hälsa.

 ![Sammanfattnings rapport](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Etablerings loggar (för hands version)

Alla aktiviteter som utförs av etablerings tjänsten registreras i Azure AD- [etablerings loggarna](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Du kan komma åt etablerings loggarna i Azure Portal genom att välja **Azure Active Directory** &gt; etablerings loggar för **företags appar** &gt; **(för hands version)** i avsnittet **aktivitet** . Du kan söka i etablerings data baserat på användarens namn eller identifieraren i antingen käll systemet eller mål systemet. Mer information finns i [etablerings loggar (för hands version)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Händelse typer för loggade aktiviteter är:

## <a name="troubleshooting"></a>Felsökning

Sammanfattnings rapporten för etablering och etablerings loggar spelar en nyckel roll som hjälper administratörer att felsöka olika problem med etablering av användar konto.

För scenariobaserade vägledning om hur du felsöker automatisk användar etablering, se problem med [att konfigurera och konfigurera användare i ett program](../app-provisioning/application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)