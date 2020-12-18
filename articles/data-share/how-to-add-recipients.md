---
title: Lägg till mottagare i Azure Data Share
description: Lär dig hur du lägger till mottagare till en befintlig data resurs i Azure Data Share.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: a8e3dac620873ab11ae24395310066037f6d2df4
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680631"
---
# <a name="how-to-add-a-recipient-to-your-share"></a>Så här lägger du till en mottagare i din resurs

Du kan lägga till mottagare när du skapar en ny resurs eller till en befintlig resurs. Du kan lägga till mottagare med hjälp av Azures inloggnings-e-post för användaren från användar gränssnittet för Azure Data delning.  Från API kan du använda en kombination av användare/tjänstens huvud namn och klient-ID. När ett klient-ID anges kan inbjudan bara accepteras till den här klienten. Även från API kan du skapa en inbjudan utan att skicka ett e-postmeddelande till mottagaren. 

## <a name="add-recipient-to-an-existing-share"></a>Lägg till mottagare i en befintlig resurs

Gå till den skickade resursen i Azure Data Share och välj fliken **inbjudningar** . Här visas alla mottagare av inbjudningar till den här data resursen. Klicka på **Lägg till mottagare** om du vill lägga till en ny.

![Skärm bild som visar Lägg till mottagare valt.](./media/how-to/how-to-add-recipients/add-recipient.png)

En panel dyker upp till höger på sidan. Klicka på **Lägg till mottagare** och fyll sedan i e-postmeddelandets nya mottagare på den tomma raden. Se till att använda mottagarens Azure login-e-post (med deras e-postalias fungerar inte). 

![Skärm bild som visar fönstret Lägg till mottagare där du kan lägga till och skicka inbjudan.](./media/how-to/how-to-add-recipients/add-recipient-side.png)

Klicka på **Lägg till och skicka inbjudan**. De nya mottagarna kommer att skicka Inbjudnings meddelanden till den här resursen.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om hur du [tar bort en inbjudan till en resurs](how-to-delete-invitation.md).
