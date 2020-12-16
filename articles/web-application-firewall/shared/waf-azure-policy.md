---
title: Brand vägg för Azure-webbprogram och Azure Policy
description: Azure Web Application Firewall (WAF) kombinerat med Azure Policy kan hjälpa organisations standarder och utvärdera kompatibilitet för WAF-resurser
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: 7798d7e960286d4f8aa971eb2eb0b03d24bd6360
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589465"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Brand vägg för Azure-webbprogram och Azure Policy

Azure Web Application-brandväggen (WAF) kombinerat med Azure Policy kan hjälpa organisations standarder och utvärdera kompatibiliteten för WAF-resurser. Azure policy är ett styrnings verktyg som ger en sammanställd vy för att utvärdera miljöns övergripande tillstånd, med möjlighet att öka detalj nivån för att öka detalj nivån per resurs, per princip. Med Azure policy kan du också få till gång till dina resurser genom att använda Mass reparationer för befintliga resurser och automatisk reparation av nya resurser.

## <a name="azure-policy-for-web-application-firewall"></a>Azure Policy för brand vägg för webbaserade program

Det finns flera inbyggda Azure Policys definitioner för att hantera WAF-resurser. En uppdelning av princip definitionerna och deras funktioner är följande:

1. **Brand vägg för webbaserade program (WAF) måste vara aktive rad för Azures frontend-tjänst**: Azure-frontend-tjänster utvärderas på om det finns en WAF för att skapa resurser. Principen har tre effekter: Audit, Deny och Disable. Gransknings spår när en Azure-frontend-tjänst inte har en WAF och låter användarna se vad Azure-tjänsten för front dörr inte följer. Neka förhindrar att ingen Azure-frontend-tjänst skapas om en WAF inte är ansluten. Inaktive rad inaktiverar den här principen.

2. **Brand vägg för webbaserade program (WAF) måste vara aktive rad för Application Gateway**: programgatewayer utvärderas på om det finns en WAF för att skapa en resurs. Principen har tre effekter: Audit, Deny och Disable. Gransknings spår när en Application Gateway inte har en WAF och låter användarna se vad Application Gateway inte uppfyller. Neka förhindrar att Application Gateway skapas om en WAF inte är ansluten. Inaktive rad inaktiverar den här principen.

3. **Brand vägg för webbaserade program (WAF) ska använda det angivna läget för Azures frontend-tjänst**: bestämmer användningen av "identifiering" eller "förebyggande" läge så att de aktive ras i alla brand Väggs principer för webb program för Azure-tjänsten för front dörr. Principen har tre effekter: Audit, Deny och Disable. Gransknings spår när en WAF inte passar det angivna läget. Neka förhindrar att ingen WAF skapas om den inte är i rätt läge. Inaktive rad inaktiverar den här principen.

4. **Brand vägg för webbaserade program (WAF) ska använda det angivna läget för Application Gateway**: bestämmer användningen av "identifiering" eller "förebyggande" läge som aktiv på alla brand Väggs principer för webb program för Application Gateway. Principen har tre effekter: Audit, Deny och Disable. Gransknings spår när en WAF inte passar det angivna läget. Neka förhindrar att ingen WAF skapas om den inte är i rätt läge. Inaktive rad inaktiverar den här principen.

## <a name="launch-an-azure-policy"></a>Starta en Azure Policy

1.  På sidan Azure-start skriver du princip i Sök fältet och klickar på ikonen Azure Policy

2.  Välj **tilldelningar** under **redigering** i Azure policys tjänsten.

:::image type="content" source="../media/waf-azure-policy/policy-home.png" alt-text="Fliken tilldelningar i Azure Policy":::

3.  På sidan tilldelningar väljer du ikonen **tilldela princip** överst.

:::image type="content" source="../media/waf-azure-policy/assign-policy.png" alt-text="Fliken grundläggande på sidan tilldela princip":::

4.  Uppdatera följande fält på fliken för att tilldela princip sidor:
    1.  **Omfång**: Välj vilka Azure-prenumerationer och resurs grupper som ska påverkas av princip definitionen.
    2.  **Undantag**: Välj eventuella resurser från omfånget som ska undantas från princip tilldelningen.
    3.  **Princip definition**: Välj den princip definition som ska användas för omfånget med undantag. Skriv "brand vägg för webbaserade program" i Sök fältet för att välja relevant brand vägg för webbaserade program Azure Policy.

:::image type="content" source="../media/waf-azure-policy/policy-listing.png" alt-text="Skärm bild som visar fliken &quot;princip definitioner&quot; på sidan tillgängliga definitioner.":::

5.  Välj fliken **parametrar** och uppdatera princip tilldelnings parametrarna. För att ytterligare klargöra vad parametern gör, Hovra över informations ikonen bredvid parameter namnet för ytterligare klargörande.

6.  Välj **Granska + skapa** för att slutföra princip tilldelningen. Princip tilldelningen tar cirka 15 minuter tills den är aktiv för nya resurser.
