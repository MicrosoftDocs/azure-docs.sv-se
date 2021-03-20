---
title: Hantera hot mot resurser och data
titleSuffix: Azure AD B2C
description: Lär dig mer om identifierings-och minsknings metoder för DOS-attacker och lösen ords attacker i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e5184698cab1874f327173fb30cf527feee48cad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85384982"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Hantera hot mot resurser och data i Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) har inbyggda funktioner som kan hjälpa dig att skydda mot hot mot dina resurser och data. Dessa hot omfattar denial-of-Service-attacker och lösen ords attacker. Denial-of-Service-attacker kan göra att resurser inte är tillgängliga för avsedda användare. Lösen ords attacker leder till obehörig åtkomst till resurser.

## <a name="denial-of-service-attacks"></a>DOS-attacker (Denial-of-Service)

Azure AD B2C skyddar mot SYN översvämnings attacker med en SYN-cookie. Azure AD B2C skyddar också mot denial-of-Service-attacker genom att använda gränser för priser och anslutningar.

## <a name="password-attacks"></a>Lösen ords attacker

Lösen ord som anges av användarna måste vara ganska komplexa. Azure AD B2C har metoder för att undvika lösen ords attacker på plats. Minskning omfattar identifiering av lösen ords attacker med brute forcehet och ord listor. Genom att använda olika signaler analyserar Azure AD B2C integriteten för begär Anden. Azure AD B2C är utformad för att intelligent skilja ut avsedda användare från hackare och botnät.

Azure AD B2C använder en sofistikerad strategi för att låsa konton. Kontona är låsta baserat på IP-adressen för begäran och lösen orden som anges. Låsningens varaktighet ökar också beroende på sannolikheten för att det är ett angrepp. När ett lösen ord provas 10 gånger (standard tröskelvärdet för försök) sker en en minuters utelåsning. Nästa gång en inloggning Miss lyckas efter det att kontot har låsts upp (det vill säga efter att kontot har låsts automatiskt av tjänsten när utelåsnings perioden har gått ut), sker en annan-minuters utelåsning och fortsätter för varje misslyckad inloggning. Att ange samma lösen ord upprepade gånger räknas inte som flera misslyckade inloggningar.

De första 10 utelåsnings perioderna är en minut lång. De följande 10 utelåsnings perioderna är lite längre och ökar varaktigheten efter var 10 utelåsnings perioder. Utelåsnings räknaren återställs till noll efter en lyckad inloggning när kontot inte är låst. Utelåsnings perioder kan vara upp till fem timmar.

## <a name="manage-password-protection-settings"></a>Hantera inställningar för lösen ords skydd

För att hantera inställningar för lösen ords skydd, inklusive utelåsnings tröskeln:

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Använd filtret för **katalog + prenumeration** på den översta menyn för att välja den katalog som innehåller Azure AD B2C klient.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Under **säkerhet** väljer du **autentiseringsmetoder (förhands granskning)** och väljer sedan **lösen ords skydd**.
1. Ange önskade inställningar för lösen ords skydd och välj sedan **Spara**.

    ![Sidan Azure Portal lösen ords skydd i Azure AD-inställningar](./media/threat-management/portal-02-password-protection.png)
    <br />*Anger utelåsnings tröskeln till 5 i inställningar för **lösen ords skydd***.

## <a name="view-locked-out-accounts"></a>Visa låsta konton

För att få information om låsta konton kan du kontrol lera [rapporten Active Directory inloggnings aktivitet](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md). Under **status** väljer du **Miss lyckas**. Misslyckade inloggnings försök med en **inloggnings fel kod** som `50053` anger ett låst konto:

![Avsnitt i Azure AD-inloggnings rapport som visar utelåst konto](./media/threat-management/portal-01-locked-account.png)

Information om hur du visar rapporten över inloggnings aktiviteter i Azure Active Directory finns i [rapport fel koder för inloggnings aktiviteter](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
