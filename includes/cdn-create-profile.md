---
title: ta med fil
description: ta med fil
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 04/30/2020
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: c6352ee9d29e4e45aa4be449046a0715fee06047
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165917"
---
## <a name="create-a-new-cdn-profile"></a>Skapa en ny CDN-profil

En CDN-profil är en container för CDN-slutpunkter och den anger en prisnivå.

1. I Azure Portal väljer du **skapa en resurs** (längst upp till vänster). Fönstret **Ny** visas.
   
1. Sök efter och välj **CDN** och välj sedan **skapa**:
   
    ![Välj CDN-resurs](./media/cdn-create-profile/cdn-new-resource.png)

    Fönstret **CDN-profil** visas.

1. Ange följande värden:
   
    | Inställning  | Värde |
    | -------- | ----- |
    | **Namn** | Ange *CDN-Profile-123* för ditt profil namn. Det här namnet måste vara globalt unikt. Ange ett annat namn om det redan används. |
    | **Prenumeration** | Välj en Azure-prenumeration i listrutan. |
    | **Resursgrupp** | Välj **Skapa ny** och ange *CDNQuickstart-RG* som resurs grupp namn eller Välj **Använd befintlig** och välj *CDNQuickstart-RG* om du redan har gruppen. | 
    | **Resursgruppsplats** | Välj en plats nära dig i list rutan. |
    | **Prisnivå** | Välj ett **standard** alternativ för Akamai i list rutan. (Distributions tiden för Akamai-nivån är ungefär en minut. Microsoft-nivån tar cirka 10 minuter och Verizon-nivåerna tar cirka 30 minuter.) |
    | **Skapa en ny CDN-slutpunkt nu** | Lämna det avmarkerat. |  
   
    ![Ny CDN-profil](./media/cdn-create-profile/cdn-new-profile.png)

1. Välj **Skapa** för att skapa profilen.

