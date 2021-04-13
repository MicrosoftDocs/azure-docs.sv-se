---
title: Så här redigerar du en publicerad Azure-certifierad enhet
description: En guide för att redigera enhets informationen när du har certifierat och publicerat enheten via programmet Azure Certified Device.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 14a29d438103f07dd35b3a3380b7cc094f215824
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304451"
---
# <a name="edit-your-published-device"></a>Redigera din publicerade enhet

När enheten har certifierats och publicerats till Azure-certifierad enhets katalog kan du behöva uppdatera enhets informationen. Detta kan bero på en uppdatering av distributions listan, ändringar av URL: er för inköps sidan eller uppdateringar av maskin varu specifikationer (till exempel operativ systemets version eller en ny komponent tillägg). Med hjälp av Azure-certifierad enhets Portal gör vi det enkelt att uppdatera enhets informationen utan att ta bort produkten från vår katalog.

## <a name="prerequisites"></a>Förutsättningar

- Du måste vara inloggad och ha ett **godkänt** projekt för din enhet på [Azure-certifierad enhets Portal](https://certify.azure.com). Om du inte har någon certifierad enhet kan du se den här [självstudien](tutorial-01-creating-your-project.md) för att komma igång.

## <a name="editing-your-published-project"></a>Redigera ditt publicerade projekt

I projekt sammanfattningen bör du se att ditt projekt är i skrivskyddat läge eftersom det redan har granskats och godkänts. Om du vill göra ändringar måste du begära en redigering av ditt projekt och få uppdateringen godkänd av Azure-certifierings teamet.

1. Klicka på `Request Metadata Edit` knappen överst på sidan  

    ![Begär metadata-uppdatering](./media/images/request-metadata-edit.png)

1. Bekräfta meddelandet på sidan som du behöver för att skicka in produkten för granskning efter redigering.
    > [!NOTE]
    > Genom att bekräfta den här redigeringen tar du **inte** bort enheten från Azure-certifierad enhets katalog om den redan har publicerats. Din tidigare version av produkten finns kvar i katalogen tills du har publicerat om enheten.

1. När du har bekräftat den här varningen kan du redigera din enhets information. Se till att lämna en anteckning i `Comments for Reviewer` avsnittet av `Device Details` vad som har ändrats.

    ![Obs! redigera metadata](./media/images/edit-notes.png)

1. På sidan projekt Sammanfattning klickar du på `Submit for review` för att ändringarna ska godkännas av Azure-certifierings teamet.
1. När ändringarna har granskats och godkänts kan du publicera ändringarna till katalogen via portalen (se vår [självstudie](./tutorial-04-publishing-your-device.md)).

## <a name="next-steps"></a>Nästa steg

Du har nu redigerat enheten i Azure Certified Device-katalogen. Du kan checka ut dina ändringar i katalogen eller certifiera en annan enhet!
- [Azure-certifierad enhets katalog](https://devicecatalog.azure.com/)
- [Kom igång med att certifiera en enhet](./tutorial-01-creating-your-project.md)
