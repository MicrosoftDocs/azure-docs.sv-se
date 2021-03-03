---
title: Felsöka Apache Kafka för samspels moln – Azure-partner lösningar
description: Den här artikeln innehåller information om fel sökning och vanliga frågor och svar om att samkänna molnet med Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/18/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: b1e4b06fcbecf11d7d5f58a583fe3bd6643d99ec
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709408"
---
# <a name="troubleshooting-apache-kafka-for-confluent-cloud-solutions"></a>Felsöka Apache Kafka för samsiga moln lösningar

Det här dokumentet innehåller information om hur du felsöker dina lösningar som använder Apache Kafka för att lösa molnet.

Om du inte hittar något svar eller inte kan lösa ett problem, kan du [skapa en begäran via Azure Portal](manage.md#get-support) eller kontakta sitt [support](https://support.confluent.io).

## <a name="cant-find-offer-in-the-marketplace"></a>Det går inte att hitta erbjudandet i Marketplace

Använd följande steg för att hitta erbjudandet på Azure Marketplace:

1. I [Azure Portal](https://portal.azure.com)väljer du **skapa en resurs**.
1. Sök efter _Apache Kafka i ett samfluent-moln_.
1. Välj panelen program.

Om erbjudandet inte visas kontaktar du [supporten](https://support.confluent.io)för att få hjälp. Ditt Azure Active Directory klient-ID måste finnas i listan över tillåtna klienter. Information om hur du hittar klient-ID finns i [så här hittar du Azure Active Directory klient-ID](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md).

## <a name="purchase-errors"></a>Köp fel

* Köpet Miss lyckas eftersom ett giltigt kredit kort inte är anslutet till Azure-prenumerationen eller så är ingen betalnings metod kopplad till prenumerationen.

  Använd en annan Azure-prenumeration. Eller Lägg till eller uppdatera kredit kortet eller betalnings metoden för prenumerationen. Mer information finns i [Uppdatera kredit-och betalnings metoden](../../cost-management-billing/manage/change-credit-card.md).

* EA-prenumerationen tillåter inte Marketplace-inköp.

  Använd en annan prenumeration. Eller kontrol lera om din EA-prenumeration är aktive rad för Marketplace-köp. Mer information finns i [Aktivera inköp på Marketplace](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases). Om dessa alternativ inte löser problemet kan du kontakta [supporten](https://support.confluent.io).

## <a name="conflict-error"></a>Konflikt fel

Om du tidigare har registrerat dig för ett samspelande moln måste du använda en ny e-postadress för att skapa en annan moln organisations resurs. När du använder en tidigare registrerad e-postadress får du ett **konflikt** fel. Registrera igen men den här gången med en ny e-postadress.

## <a name="deploymentfailed-error"></a>DeploymentFailed-fel

Om du får ett **DeploymentFailed** -fel kan du kontrol lera statusen för din Azure-prenumeration. Se till att den inte är inaktive rad och inte har några fakturerings problem.

## <a name="resource-isnt-displayed"></a>Resursen visas inte

Om en **Översikt** eller **ta bort** blad för att samsätta molnet inte visas i portalen kan du prova att uppdatera sidan. Det här felet kan vara ett tillfälligt problem med portalen. Kontakta [supporten](https://support.confluent.io)om det inte fungerar.

Om det inte går att hitta en moln resurs i listan med **alla resurser** i Azure kontaktar du [supporten](https://support.confluent.io).

## <a name="resource-creation-takes-long-time"></a>Det tar lång tid att skapa en resurs

Om distributions processen tar mer än tre timmar att slutföra, så kontakta supporten.

Om distributionen Miss lyckas och moln resursen har statusen `Failed` ta bort resursen. Försök att skapa resursen efter borttagningen.

## <a name="offer-plan-doesnt-load"></a>Erbjudande planen läses inte in

Det här felet kan vara ett tillfälligt problem med Azure Portal. Försök att distribuera erbjudandet igen.

## <a name="unable-to-delete"></a>Det går inte att ta bort

Om du inte kan ta bort samarbets resurser måste du kontrol lera att du har behörighet att ta bort resursen. Du måste ha behörighet att göra Microsoft. Fluent/*/Delete-åtgärder. Information om hur du visar behörigheter finns i [lista Azure Role-tilldelningar med hjälp av Azure Portal](../../role-based-access-control/role-assignments-list-portal.md).

Om du har rätt behörigheter men ändå inte kan ta bort resursen kan du kontakta [supporten](https://support.confluent.io). Det här villkoret kan vara relaterat till en princip för att införa en bevarande princip. Med hjälp av funktionen kan du ta bort organisationen och e-postadressen åt dig.

## <a name="unable-to-use-single-sign-on"></a>Det går inte att använda enkel inloggning

Om SSO inte fungerar för SaaS-portalen för hanterings moln kontrollerar du att du använder rätt Azure Active Directory e-post. Du måste också ha samtyckt till att tillåta åtkomst för SaaS-portalen (program vara som en tjänst). Mer information finns i [vägledningen för enkel inloggning](manage.md#single-sign-on).

Kontakta [supporten](https://support.confluent.io)om problemet kvarstår.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [att hantera din instans](manage.md) av Apache Kafka för samsiga moln.
