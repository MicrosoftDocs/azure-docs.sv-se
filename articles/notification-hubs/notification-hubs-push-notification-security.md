---
title: Notification Hubs säkerhets modell
description: Lär dig mer om säkerhets modellen för Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: 07600b1fe0cb7420989fbbfbe55c2f1a4197d2fc
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548258"
---
# <a name="notification-hubs-security"></a>Notification Hubs säkerhet

## <a name="overview"></a>Översikt

I det här avsnittet beskrivs säkerhets modellen för Azure Notification Hubs.

## <a name="shared-access-signature-security"></a>Säkerhet för signatur för delad åtkomst

Notification Hubs implementerar ett säkerhets schema på enhets nivå som kallas en *signatur för delad åtkomst* (SAS). Varje regel innehåller ett namn, ett nyckel värde (delad hemlighet) och en uppsättning rättigheter som beskrivs senare i [säkerhets anspråk](#security-claims). 

När du skapar en hubb skapas två regler automatiskt: en med **avlyssnings** rättigheter (som klient programmet använder) och en med **alla** rättigheter (som används av appens Server del):

- **DefaultListenSharedAccessSignature**: beviljar endast **lyssnings** behörighet.
- **DefaultFullSharedAccessSignature**: ger behörighet att **Lyssna**, **Hantera** och **Skicka** . Den här principen ska endast användas i din app-server del. Använd den inte i klient program. Använd en princip med enbart **avlyssnings** åtkomst. Om du vill skapa en ny anpassad åtkomst princip med en ny SAS-token, se [SAS-token för åtkomst principer](#sas-tokens-for-access-policies) senare i den här artikeln.

När du utför registrerings hantering från-klient program, om informationen som skickas via meddelanden inte är känslig (t. ex. väder uppdateringar), är ett vanligt sätt att komma åt en Notification Hub att ge nyckelvärdet för regeln lyssnings åtkomst till klient programmet och att ge nyckel värdet för regeln fullständig åtkomst till appens Server del.

Appar ska inte bädda in nyckelvärdet i Windows Store-klientprogram; Låt i stället klient appen Hämta den från appens Server del vid start.

Med den här nyckeln med **avlyssnings** åtkomst kan en klient app registrera sig för alla Taggar. Om din app måste begränsa registreringen till vissa taggar till vissa klienter (till exempel när taggar representerar användar-ID), måste appens Server del utföra registreringarna. Mer information finns i [registrerings hantering](notification-hubs-push-notification-registration-management.md). Observera att på det här sättet kommer klient programmet inte att ha direkt åtkomst till Notification Hubs.

## <a name="security-claims"></a>Säkerhets anspråk

På samma sätt som andra entiteter tillåts Notification Hub-åtgärder för tre säkerhets anspråk: **Lyssna**, **Skicka** och **Hantera**.

| Begär   | Description                                          | Tillåtna åtgärder |
| ------- | ---------------------------------------------------- | ------------------ |
| Lyssna  | Skapa/uppdatera, läsa och ta bort enstaka registreringar | Skapa/uppdatera registrering<br><br>Läs registrering<br><br>Läs alla registreringar för en referens<br><br>Ta bort registrering |
| Skicka    | Skicka meddelanden till Notification Hub                | Skicka meddelande |
| Hantera  | CRUDs på Notification Hubs (inklusive uppdatering av PNS-autentiseringsuppgifter och säkerhets nycklar) och Läs registreringar baserat på Taggar |Skapa/uppdatera/läsa/ta bort hubbar<br><br>Läs registreringar efter tagg |

Notification Hubs accepterar SAS-token som genererats med delade nycklar som kon figurer ATS direkt i hubben.

Det går inte att skicka ett meddelande till fler än ett namn område. Namn områden är logiska behållare för Notification Hubs och ingår inte i sändningen av meddelanden.

Använd åtkomst principer (autentiseringsuppgifter) på namn områdes nivå för åtgärder på namn områdes nivå. exempel: lista hubbar, skapa eller ta bort hubbar osv. Du kan bara skicka meddelanden med åtkomst principerna på NAV-nivå.

### <a name="sas-tokens-for-access-policies"></a>SAS-token för åtkomst principer

Om du vill skapa ett nytt säkerhets anspråk eller Visa befintliga SAS-nycklar gör du följande:

1. Logga in på Azure-portalen.
2. Välj **Alla resurser**.
3. Välj namnet på den aviserings hubb som du vill skapa anspråket för eller se SAS-nyckeln.
4. I den vänstra menyn väljer du **åtkomst principer**.
5. Välj **ny princip** om du vill skapa ett nytt säkerhets anspråk. Ge principen ett namn och välj de behörigheter som du vill bevilja. Välj sedan **OK**.
6. Den fullständiga anslutnings strängen (inklusive den nya SAS-nyckeln) visas i fönstret åtkomst principer. Du kan kopiera den här strängen till Urklipp för senare användning.

Om du vill extrahera SAS-nyckeln från en speciell princip väljer du **kopierings** knappen bredvid principen som innehåller den SAS-nyckel som du vill använda. Klistra in det här värdet på en tillfällig plats och kopiera sedan SAS-nyckel delen av anslutnings strängen. I det här exemplet används ett Notification Hubs-namnområde som kallas **mytestnamespace1** och en princip med namnet **policy2**. SAS-nyckeln är värdet nära slutet av strängen som anges av **SharedAccessKey**:

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![Hämta SAS-nycklar](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>Nästa steg

- [Översikt över Notification Hubs](notification-hubs-push-notification-overview.md)
