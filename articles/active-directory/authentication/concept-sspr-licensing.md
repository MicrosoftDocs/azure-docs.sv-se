---
title: Återställning av lösen ord för självbetjäning – Azure Active Directory
description: Lär dig mer om skillnaden Azure Active Directory licens krav för lösen ords återställning via självbetjäning
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d332c831cc764c61a4672ea5ad1db231b68e106
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952379"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Licensierings krav för Azure Active Directory återställning av lösen ord för självbetjäning

För att minska antalet support samtal och produktivitets förlust när en användare inte kan logga in på sin enhet eller ett program, kan användar konton i Azure Active Directory (Azure AD) aktive ras för lösen ords återställning via självbetjäning (SSPR). Funktioner som utgör SSPR inkluderar lösen ords ändring, återställning, upplåsning och tillbakaskrivning av en lokal katalog. Grundläggande SSPR-funktioner finns i Microsoft 365 Business Standard eller högre och alla Azure AD Premium SKU: er utan kostnad.

Den här artikeln beskriver de olika sätt som lösen ords återställning via självbetjäning kan licensieras och användas. Detaljerad information om priser och fakturering finns på [pris sidan för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Jämför versioner och funktioner

SSPR kräver bara en licens för klient organisationen. 

I följande tabell beskrivs olika SSPR-scenarier för ändring av lösen ord, återställning eller lokala tillbakaskrivning och vilka SKU: er som tillhandahåller funktionen.

| Funktion | Azure AD Kostnadsfri | Microsoft 365 Business Standard | Microsoft 365 Business Premium | Azure AD Premium P1 eller P2 |
| --- |:---:|:---:|:---:|:---:|
| **Endast moln ändring av användar lösen ord**<br />När en användare i Azure AD känner till sitt lösen ord och vill ändra det till något nytt. | ● | ● | ● | ● |
| **Enbart molnbaserad återställning av användar lösen ord**<br />När en användare i Azure AD har glömt sitt lösen ord och behöver återställa det. | | ● | ● | ● |
| **Ändra lösen ord för Hybrid användare eller Återställ med lokal tillbakaskrivning**<br />När en användare i Azure AD som är synkroniserad från en lokal katalog som använder Azure AD Connect vill ändra eller återställa sitt lösen ord och även skriva det nya lösen ordet tillbaka till lokal. | | | ● | ● |

> [!WARNING]
> Fristående Microsoft 365 Basic-och standard licens avtal stöder inte SSPR med lokal tillbakaskrivning. Den lokala tillbakaskrivning-funktionen kräver Azure AD Premium P1, Premium P2 eller Microsoft 365 Business Premium.

För ytterligare licens information, inklusive kostnader, se följande sidor:

* [Azure Active Directory priser](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory funktioner och funktioner](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Företag](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="next-steps"></a>Nästa steg

Kom igång med SSPR genom att följa de här självstudierna:

> [!div class="nextstepaction"]
> [Självstudie: Aktivera självbetjäning för återställning av lösen ord (SSPR)](tutorial-enable-sspr.md)