---
title: Azure Defender för Key Vault – fördelar och funktioner
description: Lär dig mer om fördelarna och funktionerna i Azure Defender för Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: f127a24bec5567a3868ae77cb8a52f0af2a19603
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100686"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>Introduktion till Azure Defender för Key Vault

Azure Key Vault är en moln tjänst som skyddar krypterings nycklar och hemligheter som certifikat, anslutnings strängar och lösen ord. 

Aktivera **Azure Defender för Key Vault** för Azure-internt Avancerat skydd mot skydd för Azure Key Vault, vilket ger ytterligare ett lager av säkerhets information. 

## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmän tillgänglighet (GA)|
|Priset|**Azure Defender för Key Vault** faktureras enligt [Security Center prissättning](https://azure.microsoft.com/pricing/details/security-center/)|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Inga](./media/icons/no-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>Vilka är fördelarna med Azure Defender för Key Vault?

Azure Defender identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Key Vault-konton. Det här skydds lagret gör att du kan åtgärda hot utan att vara säkerhets expert och utan att behöva hantera säkerhets övervaknings system från tredje part.  

När avvikande aktiviteter inträffar visar Azure Defender varningar och skickar dem via e-post till relevanta medlemmar i din organisation. Dessa aviseringar innehåller information om den misstänkta aktiviteten och rekommendationer om hur du undersöker och åtgärdar hot. 

## <a name="azure-defender-for-key-vault-alerts"></a>Aviseringar för Azure Defender för Key Vault
När du får en avisering från Azure Defender för Key Vault rekommenderar vi att du undersöker och svarar på aviseringen enligt beskrivningen i [svara på Azure Defender för Key Vault](defender-for-key-vault-usage.md). Azure Defender för Key Vault skyddar program och autentiseringsuppgifter, så även om du är van vid det program eller den användare som utlöste aviseringen, är det viktigt att kontrol lera situationen kring varje avisering.

Aviseringarna visas i Key Vault **säkerhets** sida, på Azure Defender-instrumentpanelen och på aviserings sidan för Security Center.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Azure Key Vault s säkerhets sida":::


> [!TIP]
> Du kan simulera Azure Defender för Key Vault aviseringar genom att följa instruktionerna i [verifiera Azure Key Vault hot identifiering i Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336).


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Azure Defender för Key Vault.

Information om relaterade material finns i följande artiklar: 

- [Key Vault säkerhets aviseringar](alerts-reference.md#alerts-azurekv)– avsnittet Key Vault i referens tabellen för alla Azure Security Center aviseringar
- [Exportera Security Center data kontinuerligt](continuous-export.md)
- [Ignorera aviseringar från Azure Defender](alerts-suppression-rules.md)