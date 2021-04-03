---
title: Begär ett åtkomst paket – hantering av Azure AD-berättigande
description: Lär dig hur du använder min åtkomst Portal för att begära åtkomst till ett Access-paket i Azure Active Directory rättighets hantering.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 726507fa6ea0651b23d46424bda669a2d8ad41e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996652"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Begär åtkomst till ett Access-paket i Azure AD-hantering av rättigheter

Med hantering av Azure AD-behörighet kan du använda ett Access-paket för att konfigurera resurser och principer som automatiskt administrerar åtkomst för åtkomst paketets livs längd. 

En Access Package Manager kan konfigurera principer för att kräva att användare får åtkomst till paket. En användare som behöver åtkomst till ett Access-paket kan skicka en begäran om att få åtkomst. Den här artikeln beskriver hur du skickar en åtkomstbegäran.

## <a name="sign-in-to-the-my-access-portal"></a>Logga in på portalen för åtkomst

Det första steget är att logga in på min åtkomst portal där du kan begära åtkomst till ett Access-paket.

**Nödvändig roll:** Begär Ande

1. Sök efter ett e-postmeddelande eller ett meddelande från projektet eller Business Manager som du arbetar med. E-postmeddelandet ska innehålla en länk till åtkomst paketet som du behöver åtkomst till. Länken börjar med `myaccess` , innehåller ett katalog tips och slutar med ett Access-paket-ID.  (För amerikanska myndigheter kan domänen vara `https://myaccess.microsoft.us` i stället.)
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Öppna länken.

1. Logga in på portalen för åtkomst.

    Se till att du använder ditt organisations konto (arbets-eller skol konto). Om du är osäker kan du kontakta projektet eller Business Manager.

## <a name="request-an-access-package"></a>Begär ett Access-paket

När du har hittat Access-paketet i My Access-portalen kan du skicka en begäran.

**Nödvändig roll:** Begär Ande

1. Hitta Access-paketet i listan.  Om det behövs kan du söka genom att skriva en Sök sträng och sedan välja filtret **namn**, **katalog** eller **resurser** .

    ![Min åtkomst Portal – resurs sökning](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Klicka på bock markeringen för att välja Access-paketet.

1. Klicka på **begär åtkomst** för att öppna fönstret begär åtkomst.

    ![Åtkomst till Portal – åtkomst paket](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. Om rutan **affärs justering** visas anger du en motivering för att ha åtkomst.

1. Välj **Ja** eller **Nej** om **begäran om angiven period?** är aktive rad.

1. Ange vid behov start datum och slutdatum.

    ![Min åtkomst Portal – begär åtkomst](./media/entitlement-management-shared/my-access-request-access.png)

1. När du är färdig klickar du på **Skicka** för att skicka in din begäran.

1. Klicka på **begär ande historik** om du vill se en lista över dina begär Anden och status.

    Om åtkomst paketet kräver godkännande är begäran nu i ett tillstånd som väntar på godkännande.

### <a name="select-a-policy"></a>Välj en princip

Om du begär åtkomst till ett Access-paket som har flera principer som gäller kan du bli ombedd att välja en princip. Till exempel kan en Access Package Manager Konfigurera ett Access-paket med två principer för två grupper av interna anställda. Den första principen kan ge åtkomst i 60 dagar och kräver godkännande. Den andra principen kan tillåta åtkomst i 2 dagar och kräver inte godkännande. Om du stöter på det här scenariot måste du välja den princip som du vill använda.

![Min åtkomst Portal – begär åtkomst – flera principer](./media/entitlement-management-request-access/my-access-multiple-policies.png)

### <a name="fill-out-requestor-information"></a>Fyll i information om beställare

Du kan begära åtkomst till ett Access-paket som kräver affärs justering och ytterligare information om begär Ande innan du beviljar åtkomst till Access-paketet. Fyll i all information om begär Ande som krävs för att få åtkomst till Access-paketet.

![Min åtkomst Portal – begär åtkomst-Fyll i information om beställare](./media/entitlement-management-request-access/my-access-requestor-information.png)

## <a name="resubmit-a-request"></a>Skicka en begäran igen

När du begär åtkomst till ett Access-paket kan din begäran nekas eller så kan din begäran gå ut om god kännare inte svarar i tid. Om du behöver åtkomst kan du försöka igen och skicka din begäran igen. Följande procedur beskriver hur du skickar en åtkomstbegäran igen:

**Nödvändig roll:** Begär Ande

1. Logga in på portalen för **åtkomst** .

1. Klicka på **begär historik** på navigerings menyn till vänster.

1. Hitta det åtkomst paket som du skickar en begäran till.

1. Klicka på kryss markeringen för att välja Access-paketet.

1. Klicka på länken för den blåa **vyn** till höger om det valda åtkomst paketet.
    
    ![Välj Access-paket och Visa länk](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    Ett fönster öppnas till höger med förfrågnings historiken för Access-paketet.
    
    ![Välj knappen Skicka igen](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. Klicka på knappen **Skicka igen** längst ned i fönstret.

## <a name="cancel-a-request"></a>Avbryt en begäran

Om du skickar en åtkomstbegäran och begäran fortfarande har statusen **väntar på godkännande** kan du avbryta begäran.

**Nödvändig roll:** Begär Ande

1. I portalen My Access klickar du på **begär ande historik** för att se en lista över dina begär Anden och status.

1. Klicka på länken **Visa** för den begäran som du vill avbryta.

1. Om begäran fortfarande har statusen väntar på **godkännande** kan du klicka på **Avbryt begäran** om du vill avbryta begäran.

    ![Min åtkomst Portal-Avbryt förfrågan](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Klicka på **begär ande historik** för att bekräfta att begäran avbröts.

## <a name="next-steps"></a>Nästa steg

- [Godkänn eller neka åtkomst begär Anden](entitlement-management-request-approve.md)
- [Begär process och e-postmeddelanden](entitlement-management-process.md)
