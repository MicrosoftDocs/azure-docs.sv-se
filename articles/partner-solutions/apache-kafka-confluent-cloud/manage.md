---
title: Hantera ett flytande moln – Azure-partner lösningar
description: Den här artikeln beskriver hanteringen av ett samarbets-moln på Azure Portal. Så här konfigurerar du enkel inloggning, tar bort en gemensam organisation och får support.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/08/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f8a54096ecda4729f7070120a02be3055f933cea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99989145"
---
# <a name="manage-the-confluent-cloud-resource"></a>Hantera moln resursen

Den här artikeln beskriver hur du hanterar din instansen av Apache Kafka för ett hanterings moln i Azure. Det visar hur du konfigurerar enkel inloggning (SSO), tar bort en samordnings organisation och skapar en support förfrågan.

## <a name="single-sign-on"></a>Enkel inloggning

För att implementera SSO för din organisation, kan klient administratören importera Galleri programmet. Det här är valfritt. Information om hur du importerar ett program finns i [snabb start: lägga till ett program till din Azure Active Directory (Azure AD)-klient](../../active-directory/manage-apps/add-application-portal.md). När klient organisations administratören importerar programmet behöver användarna inte uttryckligen bevilja åtkomst till samtyckerens moln Portal.

Följ dessa steg om du vill aktivera SSO:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till **översikten** över din instansen av moln resursen.
1. Välj den länk som ska **hanteras i** ett sambands moln.

   :::image type="content" source="media/sso-link.png" alt-text="Enkel inloggning i Fluent Portal.":::

1. Om klient organisations administratören inte har importerat Galleri programmet för SSO-medgivande, bevilja behörigheter och medgivande. Det här steget behövs bara första gången du öppnar länken för att **Hantera i** ett sambands moln.
1. Välj ett Azure AD-konto för enkel inloggning till samarbets-Cloud-portalen.
1. När ditt medgivande har tillhandahållits omdirigeras du till den samtycker-moln portalen.

## <a name="set-up-cluster"></a>Konfigurera kluster

Information om hur du konfigurerar klustret finns i [skapa ett kluster i den](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)här artikeln.

## <a name="delete-confluent-organization"></a>Ta bort samordnings organisation

När du inte längre behöver din moln resurs, tar du bort resursen i Azure och samkänna molnet.

Ta bort resurserna i Azure:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **alla resurser** och **Filtrera efter namnet** på den samordnings moln resurs eller **resurs typens** _organisation_. Eller Sök efter resurs namnet i Azure Portal.
1. I resurs **översikten** väljer du **ta bort**.

    :::image type="content" source="media/delete-resources-icon.png" alt-text="Ikon för resurs borttagning.":::

1. Bekräfta borttagningen genom att skriva resurs namnet och välja **ta bort**.

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="Fråga om att bekräfta borttagning av resurs.":::

Information om hur du tar bort resursen i ett samarbetst moln finns i dokumentationen för att avsäga dokumentation om [moln miljöer](https://docs.confluent.io/current/cloud/using/environments.html) och för att se om det finns några [grundläggande](https://docs.confluent.io/current/cloud/using/cloud-basics.html)dokumentation om den.

Klustret och alla data i klustret tas bort permanent. Om ditt kontrakt innehåller en data lagrings sats, kan du hålla dina data under den tids period som anges i [villkoren för service-innehålls förteckning](https://www.confluent.io/confluent-cloud-tos).

Du debiteras för beräknad användning upp till tiden då klustret tas bort. När klustret har tagits bort permanent skickar ett bekräftelse meddelande via e-post.

## <a name="get-support"></a>Få support

Om du vill skicka in en supportbegäran till dig, kan du antingen kontakta [supporten](https://support.confluent.io) eller skicka en begäran via portalen, som du ser nedan.

> [!NOTE]
> För första gången återställer du ditt lösen ord innan du loggar in på webbplatsen för att avhjälpa support. Om du inte har ett konto med ett överförings moln kan du skicka ett e-postmeddelande till `cloud-support@confluent.io` för att få hjälp.

I portalen kan du antingen skicka en förfrågan via Azure hjälp och support, eller direkt från din instans av Apache Kafka för att överföra molnet i Azure.

Skicka en begäran via Azure hjälp och support:

1. Välj **Hjälp + support**.
1. Välj **skapa en supportbegäran**.
1. I formuläret väljer du **teknisk** för **ärende typ**. Välj din prenumeration. I listan över tjänster väljer du samsig **till Azure**.

    :::image type="content" source="media/support-request-help.png" alt-text="Skapa en support förfrågan från hjälpen.":::

Följ dessa steg om du vill skicka en begäran från resursen:

1. I Azure Portal väljer du din organisation.
1. På menyn på skärmens vänstra sida väljer du **ny supportbegäran**.
1. Om du vill skapa en supportbegäran väljer du länken till sambands **portalen**.

    :::image type="content" source="media/support-request.png" alt-text="Skapa en supportbegäran från en instans.":::

## <a name="next-steps"></a>Nästa steg

Hjälp med fel sökning finns i [felsöka Apache Kafka för](troubleshoot.md)samsiga moln lösningar.
