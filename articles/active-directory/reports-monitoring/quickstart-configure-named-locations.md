---
title: Konfigurera namngivna platser i Azure Active Directory | Microsoft Docs
description: Lär dig att konfigurera namngivna platser.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.subservice: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94f4d17596936dd9d0ebbdae3c351cac9ed2a570
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89299872"
---
# <a name="quickstart-configure-named-locations-in-azure-active-directory"></a>Snabbstart: Konfigurera namngivna platser i Azure Active Directory

Du kan använda namngivna platser för att sätta etiketter på betrodda IP-adressintervall i din organisation. Azure AD använder namngivna platser för att:
- Identifiera falska positiva identifieringar i [risk identifieringar](../identity-protection/overview-identity-protection.md). Inloggning från en betrodd plats minskar en användares inloggningsrisk.   
- Konfigurera [plats baserad villkorlig åtkomst](../conditional-access/location-condition.md).

I den här snabbstarten får du lära dig att konfigurera namngivna platser i din miljö.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du:

* En Azure AD-klientorganisation. Registrera dig för en [kostnads fri utvärderings version](https://azure.microsoft.com/trial/get-started-active-directory/). 
* En användare som är en global administratör för klienten.
* Ett IP-adressintervall som är etablerat och betrott inom din organisation. IP-intervallet måste använda formatet **Classless Interdomain Routing (CIDR)**.

## <a name="configure-named-locations"></a>Konfigurera namngivna platser

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Azure Active Directory** i den vänstra rutan och välj sedan **villkorlig åtkomst** i avsnittet **säkerhet** .

    ![Fliken villkorlig åtkomst](./media/quickstart-configure-named-locations/entrypoint.png)

3. På sidan **Villkorlig åtkomst** väljer du **Namngivna platser** och **Ny plats**.

    ![Namngivna platser](./media/quickstart-configure-named-locations/namedlocation.png)

6. Fyll i formuläret på den nya sidan. 

   * I rutan **Namn** skriver du ett namn på din namngivna plats.
   * I rutan **IP-intervall** skriver du ett IP-adressintervall i CIDR-format.  
   * Klicka på **Skapa**.
    
     ![Det nya bladet](./media/quickstart-configure-named-locations/61.png)

## <a name="next-steps"></a>Nästa steg

Mer information finns i:

- [Plats som ett villkor i villkorlig åtkomst](../conditional-access/concept-conditional-access-conditions.md#locations).
- [Rapport över riskfyllda inloggningar](../identity-protection/overview-identity-protection.md).