---
title: Skapa Apache Kafka för samspels moln – Azure-partner lösningar
description: I den här artikeln beskrivs hur du använder Azure Portal för att skapa en instans av Apache Kafka för ett moln.
ms.service: partner-services
ms.topic: quickstart
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f4c6dacf63b1be44e826fe6841c87ccec4bf9b1a
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253798"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud"></a>Snabb start: kom igång med Apache Kafka för samsiga moln

I den här snabb starten använder du Azure Portal för att skapa en instans av Apache Kafka för ett pågående moln.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto. Om du inte har en aktiv Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/).
- Du måste ha rollen _ägare_ eller _deltagare_ för din Azure-prenumeration. Integrationen mellan Azure och samordningen kan bara ställas in av användare med _ägar_ -eller _deltagar_ åtkomst. Innan du börjar ska [du kontrol lera att du har rätt åtkomst](../../role-based-access-control/check-access.md).

## <a name="find-offer"></a>Sök erbjudande

Använd Azure Portal för att hitta Apache Kafka för att samtycker till moln programmet.

1. I en webbläsare går du till [Azure Portal](https://portal.azure.com/) och loggar in.

1. Om du har besökt **Marketplace** i en senaste session väljer du ikonen från de tillgängliga alternativen. Annars söker du efter _Marketplace_.

    :::image type="content" source="media/marketplace.png" alt-text="Marketplace-ikon.":::

1. På **Marketplace** -sidan har du två alternativ beroende på vilken typ av plan du vill ha. Du kan registrera dig för en plan som du betalar per användning eller en åtagande plan. Betala per användning är offentligt tillgänglig. Åtagande planen är tillgänglig för kunder som har godkänts för ett privat erbjudande.

   - För kunder **som betalar per** användning kan du söka efter _Apache Kafka i ett samfluent-moln_. Välj erbjudandet för Apache Kafka i ett samfluent-moln.

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="Sök på Azure Marketplace-erbjudandet.":::

   - För **åtagande** kunder väljer du länken för att **Visa privata erbjudanden**. Åtagandet kräver att du registrerar dig för en minimal spenderad mängd. Använd bara det här alternativet när du vet att du behöver tjänsten under en längre tid.

     :::image type="content" source="media/view-private-offers.png" alt-text="Visa privata erbjudanden.":::

     Leta efter _Apache Kafka i ett samfluent-moln_.

     :::image type="content" source="media/select-from-private-offers.png" alt-text="Välj privat erbjudande.":::

## <a name="create-resource"></a>Skapa resurs

När du har valt erbjudandet för Apache Kafka i ett pågående moln, är du redo att konfigurera programmet.

1. Om du har valt privata erbjudanden i föregående avsnitt har du två alternativ för plan typer:

    - Sätt Fluent-moln – betala per användning
    - Åtagande – för genomförande plan

   Om du inte har valt privata erbjudanden har du bara alternativet betala per användning.

   Välj den plan som du vill använda och välj Konfigurera **+ Prenumerera**.

    :::image type="content" source="media/setup-subscribe.png" alt-text="Konfigurera och prenumerera.":::

1. Ange följande värden på sidan **skapa grundläggande moln resurser** . När du är klar väljer du **Nästa: Taggar**.

    :::image type="content" source="media/setup-basics.png" alt-text="Formulär för att konfigurera en samordnad moln resurs.":::

    | Egenskap | Beskrivning |
    | ---- | ---- |
    | **Prenumeration** | Välj den Azure-prenumeration som du vill distribuera till på den nedrullningsbara menyn. Du måste ha _ägar_ -eller _deltagar_ åtkomst. |
    | **Resursgrupp** | Ange om du vill skapa en ny resurs grupp eller använda en befintlig resurs grupp. En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Mer information finns i [översikten över Azure-resursgrupper](../../azure-resource-manager/management/overview.md). |
    | **Organisations namn för omtalar** | Namnge SaaS-resursen (Software as a Service). |
    | **Region** | I den nedrullningsbara menyn väljer du något av följande regioner: <br/><br/> Östra Australien, centrala Kanada, centrala USA, östra USA, östra USA 2, centrala Frankrike, norra Europa, Sydostasien, Storbritannien, södra, västra centrala USA, Västeuropa, västra USA 2 |
    | **Planera** | Välj **betala per** användning eller **åtagande**. |
    | **Fakturerings period** | Förifyllt baserat på den valda fakturerings planen. |
    | **Pris** | Förifyllt baserat på den valda samarbets planen. |

1. Ange **namn** **-och värdepar** för de taggar som du vill använda för resursen i **taggar**. När du har angett taggarna väljer du **Granska + skapa**.

    :::image type="content" source="media/setup-tags.png" alt-text="Lägg till projekt taggar.":::

1. Granska de inställningar du har angett. När du är klar väljer du **skapa**.

1. Det tar några minuter att skapa resursen. Du kan visa distributions status i **meddelanden**. När distributionen är färdig väljer du resursen för att visa **översikts** sidan.

    :::image type="content" source="media/deployment-status.png" alt-text="Distributions status.":::

   Om du får ett fel meddelande kan du läsa [mer i fel sökning Apache Kafka för](troubleshoot.md)samsiga moln lösningar.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera moln resursen](manage.md)
