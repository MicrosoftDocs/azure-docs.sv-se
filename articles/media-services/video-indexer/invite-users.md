---
title: Bjud in användare till Video Indexer – Azure
titleSuffix: Azure Media Services
description: Den här artikeln visar hur du bjuder in användare till Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 02/03/2021
ms.author: juliako
ms.openlocfilehash: 7d3415ffad4820d8651841398ec6d47352869ce2
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100534787"
---
# <a name="quickstart-invite-users-to-video-indexer"></a>Snabb start: Bjud in användare till Video Indexer

Om du vill samar beta med dina kollegor kan du bjuda in dem till ditt Video Indexer-konto. 

> [!NOTE]
> Endast kontots administratör kan lägga till eller ta bort användare.

## <a name="invite-new-users"></a>Bjud in nya användare

1. Logga in på [Video Indexer](https://www.videoindexer.ai/)-webbplatsen. Se till att du är ansluten till ett administratörs konto.
1. Om du är administratör visas knappen **dela konto** i det övre högra hörnet. Klicka på knappen och du kan bjuda in användare. 

    :::image type="content" source="./media/invite-users/share-account.png" alt-text="Dela ditt konto":::
1. Ange e-postadresser för en person som du vill bjuda in till ditt Video Indexer konto i dialog rutan **dela det här kontot med andra** .

    :::image type="content" source="./media/invite-users/share-account-others.png" alt-text="Bjud in användare till det här kontot":::  
1. När du har klickat på **Bjud in** kommer personen att läggas till i listan över väntande inbjudningar. <br/>Du kan välja mellan två alternativ för varje inbjudan som ännu inte har anslutit till kontot: **ta bort inbjudan** eller **Kopiera inbjudans webb adress**.

    :::image type="content" source="./media/invite-users/invites-pending.png" alt-text="Väntande inbjudningar":::  
1. När inbjudan har kopplats till kontot visas tre alternativ att välja mellan. Två alternativ för roller: **deltagare** (standard) eller **ägare**. Av kan du välja att ta bort inbjudan genom att trycka på **ta bort**.

    :::image type="content" source="./media/invite-users/joined-invitee-options.png" alt-text="Ansluten till inbjudan":::  

    Användare får inget meddelande vid borttagning. När de har tagits bort är användarna inte behöriga att logga in.

## <a name="manage-roles-invite-more-users"></a>Hantera roller, Bjud in fler användare

Förutom att ta fram dialog rutan **dela det här kontot med andra** genom att klicka på **dela konto** (enligt beskrivningen ovan) kan du göra det från **Inställningar**.

1. Tryck på knappen **Inställningar** i det öppna kontot. 

    :::image type="content" source="./media/invite-users/settings.png" alt-text="Kontoinställningar":::  
1. Klicka på knappen **hantera roller** .
1. Om du vill bjuda in en annan användare klickar du på **Bjud in fler personer till kontot**.

    :::image type="content" source="./media/invite-users/invite-more-people.png" alt-text="Bjud in fler användare":::  
1. När du har klickat på **Bjud in fler personer till det här kontot** visas inbjudan dialog ruta
 
    :::image type="content" source="./media/invite-users/share-account-others.png" alt-text="Bjud in användare till det här kontot":::  

## <a name="next-steps"></a>Nästa steg

Du kan nu använda [video Indexer webbplats](video-indexer-view-edit.md) eller [video Indexer Developer-portalen](video-indexer-use-apis.md) för att se insikterna för videon.

## <a name="see-also"></a>Se även

- [Översikt över Video Indexer](video-indexer-overview.md)
- [Så registrerar du dig och laddar upp din första video](video-indexer-get-started.md)
- [Börja använda API: er](video-indexer-use-apis.md)
