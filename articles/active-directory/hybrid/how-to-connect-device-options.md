---
title: 'Azure AD Connect: enhets alternativ | Microsoft Docs'
description: Det här dokumentet innehåller information om enhets alternativ som är tillgängliga i Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90d8cc39174f6392b0c532948305fa713535f0c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279235"
---
# <a name="azure-ad-connect-device-options"></a>Azure AD Connect: enhets alternativ

Följande dokumentation innehåller information om de olika enhets alternativ som är tillgängliga i Azure AD Connect. Du kan använda Azure AD Connect för att konfigurera följande två åtgärder: 
* **Hybrid Azure AD-anslutning**: om din miljö har ett lokalt AD-utrymme och du vill ha fördelarna med Azure AD kan du implementera hybrid Azure AD-anslutna enheter. De här enheterna är anslutna till både din lokala Active Directory och din Azure Active Directory.
* **Tillbakaskrivning av enhet**: enhetens tillbakaskrivning används för att aktivera villkorlig åtkomst baserat på enheter till AD FS (2012 R2 eller högre) skyddade enheter

## <a name="configure-device-options-in-azure-ad-connect"></a>Konfigurera enhets alternativ i Azure AD Connect

1.  Kör Azure AD Connect. På sidan **Ytterligare aktiviteter** väljer du **Konfigurera enhets alternativ**.  Klicka på **Nästa**.
    ![Konfigurera enhets alternativ](./media/how-to-connect-device-options/deviceoptions.png) 

    På sidan **Översikt** visas information.
    ![Översikt](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > De nya alternativen för att konfigurera enheten är bara tillgängliga i version 1.1.819.0 och senare.

2.  När du har angett autentiseringsuppgifterna för Azure AD kan du välja vilken åtgärd som ska utföras på sidan enhets alternativ.
    ![Enhets åtgärder](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Nästa steg

* [Konfigurera hybrid Azure AD-anslutning](../devices/hybrid-azuread-join-plan.md)
* [Konfigurera/inaktivera tillbakaskrivning av enhet](how-to-connect-device-writeback.md)