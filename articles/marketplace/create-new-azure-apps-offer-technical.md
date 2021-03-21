---
title: Så här lägger du till teknisk information för Azure Application erbjudandet
description: Lär dig hur du lägger till teknisk information för ditt Azure Application-erbjudande i Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a8658242bca48ee490e7fd44582f46e7c9c78e5b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94370455"
---
# <a name="how-to-add-technical-details-for-your-azure-application-offer"></a>Så här lägger du till teknisk information för Azure Application erbjudandet

Den här artikeln beskriver hur du anger teknisk information som hjälper Microsofts kommersiella marknads plats att ansluta till din lösning. Med den här anslutningen kan vi tillhandahålla ditt erbjudande för kunden om de väljer att förvärva och hantera dem.

Fyll bara i det här avsnittet om erbjudandet innehåller ett hanterat program som genererar mätnings händelser med hjälp av [Marketplace-avgiftsbelagda fakturerings-API: er](partner-center-portal/marketplace-metering-service-apis.md) och har en tjänst som ska autentiseras med en Azure AD-säkerhetstoken. Mer information finns i avsnittet om [autentisering av Marketplace för avläsning av tjänst](partner-center-portal/marketplace-metering-service-authentication.md) för olika autentiseringsalternativ.

## <a name="technical-configuration-offer-level"></a>Teknisk konfiguration (erbjudande nivå)

Fliken **teknisk konfiguration** gäller bara för dig om du ska skapa ett hanterat program som skapar mätnings händelser med hjälp av [API: er för avgiftsbelagda API: er](partner-center-portal/marketplace-metering-service-apis.md). I så fall, utför du följande steg. Annars går du till [Nästa steg](#next-steps). 

Mer information om de här fälten finns i [planera ett Azure Application erbjudande för den kommersiella marknads platsen](plan-azure-application-offer.md#technical-configuration).

1. På fliken **teknisk konfiguration** anger du **Azure Active Directory klient-ID** och **Azure Active Directory program-ID** som används för att validera anslutningen mellan våra två tjänster bakom en autentiserad kommunikation.

1. Välj **Spara utkast** innan du fortsätter till nästa flik: plan översikt.

## <a name="next-steps"></a>Nästa steg

- [Så här skapar du planer för Azure Application erbjudandet](create-new-azure-apps-offer-plans.md)
