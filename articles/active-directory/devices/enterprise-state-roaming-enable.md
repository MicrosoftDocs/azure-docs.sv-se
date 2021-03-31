---
title: Aktivera enterprise tillståndsväxling i Azure Active Directory
description: Vanliga frågor och svar om Enterprise State Roaming inställningar i Windows-enheter.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.custom: references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34b554fbef63f23b3540fe49e5c45976122add25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89268612"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Aktivera enterprise tillståndsväxling i Azure Active Directory

Enterprise State Roaming är tillgänglig för alla organisationer med en Azure AD Premium-eller Enterprise Mobility + Security-licens (EMS). Mer information om hur du skaffar en Azure AD-prenumeration finns på [produkt sidan för Azure AD](https://azure.microsoft.com/services/active-directory).

När du aktiverar Enterprise State Roaming beviljas din organisation automatiskt en kostnads fri, begränsad användnings licens för Azure Rights Management-skydd från Azure Information Protection. Den här kostnads fria prenumerationen är begränsad till kryptering och dekryptering av företags inställningar och programdata som synkroniseras med Enterprise State Roaming. Du måste ha [en betald prenumeration](https://azure.microsoft.com/pricing/details/information-protection/) för att kunna använda alla funktioner i Azure Rights Management-tjänsten.

> [!NOTE]
> Den här artikeln gäller Microsoft Edge äldre HTML-baserad webbläsare lanserad med Windows 10 i juli 2015. Artikeln gäller inte den nya Microsoft Edge krom-baserade webbläsaren som lanserades den 15 januari 2020. Mer information om hur synkronisering fungerar för den nya Microsoft Edge finns i artikeln [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="to-enable-enterprise-state-roaming"></a>Aktivera Enterprise State Roaming

1. Logga in på [Azure AD administrations Center](https://aad.portal.azure.com/).
1. Välj **Azure Active Directory**  >  **enheter**  >  **Enterprise State roaming**.
1. Välj **användare kan synkronisera inställningar och AppData mellan enheter**. Mer information finns i [så här konfigurerar du enhets inställningar](./device-management-azure-portal.md).
  
   ![bild av enhets inställningen märkta användare kan synkronisera inställningar och AppData mellan enheter](./media/enterprise-state-roaming-enable/device-settings.png)
  
För att en Windows 10-enhet ska kunna använda tjänsten Enterprise State Roaming måste enheten autentisera med en Azure AD-identitet. För enheter som är anslutna till Azure AD är användarens primära inloggnings identitet sin Azure AD-identitet, så ingen ytterligare konfiguration krävs. För enheter som använder lokala Active Directory måste IT-administratören [Konfigurera hybrid Azure Active Directory anslutna enheter](./hybrid-azuread-join-plan.md). 

## <a name="data-storage"></a>Datalagring

Enterprise State Roaming data finns i en eller flera [Azure-regioner](https://azure.microsoft.com/regions/) som bäst överensstämmer med värdet för land/region i Azure Active Directory-instansen. Enterprise State Roaming data partitioneras baserat på tre viktiga geografiska områden: Nordamerika, EMEA och APAC. Enterprise State Roaming data för klienten är lokalt placerade i den geografiska regionen och replikeras inte över flera regioner.  Exempel:

| Lands-/region värde | har data som finns i |
| -------------------- | ------------------------ |
| Ett EMEA-land/-region som Frankrike eller Zambia | En eller flera Azure-regioner i Europa |
| Ett Nord amerikanskt land/region som USA eller Kanada | En eller flera av Azure-regionerna i USA |
| Ett APAC land/region, till exempel Australien eller nya Zeeland | En eller flera av Azure-regionerna i Asien |
| Södra amerikanska och Antarktis regioner | En eller flera Azure-regioner i USA |

Värdet land/region anges som en del av processen för att skapa Azure AD-katalogen och kan sedan ändras. Om du behöver mer information om din data lagrings plats kan du [använda Azure-supporten](https://azure.microsoft.com/support/options/)som en biljett.

## <a name="view-per-user-device-sync-status"></a>Visa synkroniseringsstatus för enhet per användare

Följ de här stegen om du vill visa en status rapport för synkronisering av enheter per användare.

1. Logga in på [Azure AD administrations Center](https://aad.portal.azure.com/).
1. Välj **Azure Active Directory**  >  **användare**  >  **alla användare**.
1. Välj användaren och välj sedan **enheter**.
1. Under **Visa** väljer du **enheter synkroniserar inställningar och AppData** för att Visa synkroniseringsstatus.
  
   ![bild av inställningen synkronisering av enhets data](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. Om det finns enheter som synkroniseras för den här användaren ser du de enheter som visas här.
  
   ![bild av kolumn data för enhets synkronisering](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Datakvarhållning

Data som synkroniseras med Microsoft-molnet med hjälp av Enterprise State Roaming bevaras tills de tas bort manuellt eller tills data i fråga är inaktuella. 

### <a name="explicit-deletion"></a>Explicit borttagning

Explicit borttagning är när en Azure-administratör tar bort en användare eller en katalog eller på annat sätt begär att data ska tas bort.

* **Borttagning av användare**: när en användare tas bort i Azure AD raderas centrala data för användar kontot efter 90 till 180 dagar. 
* **Katalog borttagning**: det är en omedelbar åtgärd att ta bort en hel katalog i Azure AD. Alla inställnings data som är associerade med den katalogen tas bort efter 90 till 180 dagar. 
* **Vid borttagning av begäran**: om Azure AD-administratören vill ta bort en särskild användares data eller inställnings data manuellt, kan administratören skicka en biljett till [Azure-supporten](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Borttagning av inaktuella data

Data som inte har öppnats i ett år ("kvarhållningsperiod") kommer att behandlas som inaktuella och kan tas bort från Microsoft-molnet. Kvarhållningsperioden kan ändras men kommer inte att vara mindre än 90 dagar. Inaktuella data kan vara en specifik uppsättning Windows/program-inställningar eller alla inställningar för en användare. Exempel:

* Om inga enheter har åtkomst till en viss inställnings samling (till exempel ett program tas bort från enheten, eller en inställnings grupp som "tema" är inaktive rad för alla användares enheter), blir samlingen inaktuell efter kvarhållningsperioden och kan tas bort. 
* Om en användare har inaktiverat synkronisering av inställningar på alla sina enheter, kommer ingen av inställnings data att nås, och alla inställnings data för den användaren blir inaktuella och kan tas bort efter kvarhållningsperioden. 
* Om Azure AD Directory-administratören stänger av Enterprise State Roaming för hela katalogen kommer alla användare i den katalogen att sluta synkronisera inställningar och alla inställningar för alla användare blir inaktuella och kan tas bort efter kvarhållningsperioden. 

### <a name="deleted-data-recovery"></a>Återställning av borttagna data

Det går inte att konfigurera data bevarande principen. När data tas bort permanent går det inte att återställa. Inställnings data tas dock bara bort från Microsoft-molnet, inte från slutanvändarens enhet. Om en enhet senare återansluter till den Enterprise State Roaming tjänsten, synkroniseras inställningarna igen och lagras i Microsoft-molnet.

## <a name="next-steps"></a>Nästa steg

* [Översikt över enterprise tillståndsväxling](enterprise-state-roaming-overview.md)
* [Vanliga frågor och svar om inställningar och dataväxling](enterprise-state-roaming-faqs.md)
* [grupprincip-och MDM-inställningar för synkronisering av inställningar](enterprise-state-roaming-group-policy-settings.md)
* [Referens för Windows 10-växlingsinställningar](enterprise-state-roaming-windows-settings-reference.md)
* [Felsökning](enterprise-state-roaming-troubleshooting.md)