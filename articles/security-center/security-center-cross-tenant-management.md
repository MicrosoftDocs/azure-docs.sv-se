---
title: Hantering av flera innehavare i Azure Security Center | Microsoft Docs
description: Lär dig hur du konfigurerar hantering av flera innehavare för att hantera säkerhets position för flera klienter i Security Center med Azure-delegerad resurs hantering.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: 493a06e85ad6c8260c342cf8167386394835b1c6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099496"
---
# <a name="cross-tenant-management-in-security-center"></a>Hantering av flera innehavare i Security Center

Med hantering av flera innehavare kan du Visa och hantera säkerhets position för flera klienter i Security Center genom att använda [Azure-delegerad resurs hantering](../lighthouse/concepts/azure-delegated-resource-management.md) . Hantera flera klienter effektivt, från en enda vy, utan att behöva logga in på varje klients katalog.

- Tjänste leverantörer kan hantera position av resurser, för flera kunder, inifrån sin egen klient organisation.

- Säkerhets team för organisationer med flera klienter kan visa och hantera sina säkerhets position från en enda plats.

## <a name="set-up-cross-tenant-management"></a>konfigurera hantering av flera klientorganisationer.

Azure-delegerad resurs hantering är en av de viktigaste komponenterna i Azure dataLighthouses. Konfigurera hantering av flera innehavare genom att delegera åtkomst till resurser för hanterade klienter till din egen klient med hjälp av de här anvisningarna från Azure Lighthouse-dokumentationen: [Azure delegerad resurs hantering](../lighthouse/concepts/azure-delegated-resource-management.md).


## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Hur fungerar hantering av flera innehavare i Security Center

Du kan granska och hantera prenumerationer över flera klienter på samma sätt som du hanterar flera prenumerationer i en enda klient.

Klicka på filter ikonen i det övre meny fältet och välj prenumerationer från varje klient katalog som du vill visa.

  ![Filtrera klient organisationer](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

Vyer och åtgärder är i princip samma. Här är några exempel:

- **Hantera säkerhets principer**: från en vy, hantera säkerhets position för många resurser med [principer](tutorial-security-policy.md), vidta åtgärder med säkerhets rekommendationer och samla in och hantera säkerhetsrelaterade data.
- **Förbättra position för säker Poäng och efterlevnad**: visning av flera klienter gör att du kan visa den övergripande säkerhets position för alla klienter och var och hur du bäst förbättrar den [säkra poängen](secure-score-security-controls.md) och [efterlevnaden position](security-center-compliance-dashboard.md) för var och en av dem.
- **Åtgärda rekommendationer**: övervaka och reparera en [rekommendation](security-center-recommendations.md) för många resurser från olika klienter på en gång. Du kan sedan omedelbart ta itu över de säkerhets risker som visar den högsta risken för alla klienter.
- **Hantera aviseringar**: identifiera [aviseringar](security-center-alerts-overview.md) i de olika klient organisationerna. Vidta åtgärder för resurser som inte är kompatibla med åtgärds bara [steg](security-center-managing-and-responding-alerts.md).

- **Hantera avancerade funktioner för moln skydd och mer**: hantera de olika skydds tjänsterna, till exempel [just-in-Time (JIT) VM-åtkomst](security-center-just-in-time.md), [anpassningsbar nätverks härdning](security-center-adaptive-network-hardening.md), [anpassningsbara program kontroller](security-center-adaptive-application.md)och mycket annat.
 
## <a name="next-steps"></a>Nästa steg
Den här artikeln förklarar hur hantering av flera innehavare fungerar i Security Center. För att upptäcka hur Azure Lighthouse kan förenkla hantering av flera innehavare i ett företag som använder flera Azure AD-klienter, se [Azure Lighthouse i företags scenarier](../lighthouse/concepts/enterprise.md).