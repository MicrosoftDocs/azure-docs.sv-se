---
title: Vanliga frågor och svar om Internet Analyzer | Microsoft Docs
description: Vanliga frågor och svar om Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "74184256"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Vanliga frågor och svar om Azure Internet Analyzer (för hands version)

Detta är vanliga frågor och svar om Azure Internet Analyzer – om du har fler frågor går du till [feedback-forumet](https://aka.ms/internetAnalyzerFeedbackForum) och publicerar din fråga. När en fråga ofta blir tillfrågas, så Lägg till den i den här artikeln så att den snabbt och enkelt kan hittas.

## <a name="how-do-i-participate-in-the-preview"></a>Hur gör jag för att delta i förhands granskningen?

För hands versionen är tillgänglig för utvalda kunder. Om du är intresse rad av att delta i förhands granskningen gör du följande:

1. Logga in på [Azure-portalen](https://ms.portal.azure.com).
2. Gå till **prenumerations** sidan.
3. Klicka på den Azure-prenumeration som du planerar att använda Internet Analyzer med.
4. Gå till **resurs leverantörs** inställningarna för prenumerationen.
5. Sök efter **Microsoft. Network** och klicka på knappen **Registrera** (eller **Registrera** om).
![åtkomstbegäran](./media/ia-faq/request-preview-access.png)

6. [Begär godkännande](https://aka.ms/internetAnalyzerContact) genom att ange din e-postadress och det ID för Azure-prenumeration som användes för att göra åtkomstbegäran.
7. När din begäran har godkänts får du en bekräftelse via e-post och du kan skapa/uppdatera/ändra Internet Analyzer-resurser från den nya tillåtna Azure-prenumerationen.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>Måste jag bädda in klienten för att köra ett test?

Ja, Internet Analyzer-klienten måste vara installerad i ditt program för att samla in mått som är specifika för dina användare. [Lär dig hur du installerar-klienten.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>Faktureras jag för deltagande i för hands versionen?
Nej, Azure Internet Analyzer är kostnads fri att användas i för hands versionen. Det finns inget service nivå avtal under för hands versionen.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Vilka scenarier har Internet Analyzer utformats för att adressera?

Internet Analyzer är utformat för att ge dig insikter om nätverks prestanda baserat på din användar population. För att hjälpa användarna att fatta bästa prestanda för dina användare, jämför Internet analys prestandan för två Internet-slutpunkter med hjälp av din specifika användar ifyllning. Även om Internet Analyzer kan svara på flera olika frågor är några av de vanligaste:

* Vilken prestanda påverkas när du migrerar till molnet? 
    * *Föreslaget test: anpassad (din aktuella lokala infrastruktur) vs. Azure (en förkonfigurerad slut punkt)*
* Vad är värdet för att placera mina data i kanten jämfört med i ett Data Center? 
    *  *Föreslaget test: Azure-eller Azure-frontend, Azure vs. Azure CDN från Microsoft*
* Vad är prestanda fördelarna med Azures front dörr?
    *  *Föreslaget test: anpassad/Azure/CDN jämfört med Azures frontend-dörr*
* Vad är prestanda fördelarna med Azure CDN från Microsoft? 
    *  *Föreslaget test: Custom/Azure/AFD vs. Azure CDN från Microsoft*
* Hur Azure CDN från Microsoft stack? 
    *  *Föreslaget test: anpassad (annan CDN-slutpunkt) jämfört med Azure CDN från Microsoft*
* Vilket är det bästa molnet för din slut användar population i varje region? 
    *  *Föreslaget test: anpassad (annan moln tjänst) jämfört med Azure (en förkonfigurerad slut punkt)*

## <a name="which-tests-can-i-run-in-preview"></a>Vilka tester kan jag köra i för hands versionen?

Varje test som du skapar i Internet Analyzer består av två slut punkter – slut punkt A och slut punkt B. Någon av följande kombinationer kan köras som tester:  
* Två förkonfigurerade slut punkter,
* En anpassad och en förkonfigurerad slut punkt, eller
* Två [anpassade slut punkter](internet-analyzer-custom-endpoint.md) (en anpassad slut punkt måste finnas i Azure).

Följande förkonfigurerade slut punkter är tillgängliga under för hands versionen:
* **Azure-regioner**
    * Brasilien, södra
    * Indien, centrala
    * Central US
    * Asien, östra
    * East US
    * Japan, västra
    * Europa, norra
    * Sydafrika, norra
    * Sydostasien
    * Förenade Arabemiraten, norra
    * Storbritannien, västra  
    * Europa, västra
    * USA, västra
    * USA, västra 2
* **Flera kombinationer av Azure-regioner**
    * Östra USA, södra Brasilien
    * USA, östra Asien, östra
    * Västeuropa, södra Brasilien
    * Europa, västra Sydostasien
    * Västeuropa, Västeuropa, Nord Förenade Arabemiraten
    * Västra USA, östra USA
    * Västra USA, Västeuropa
    * Västra USA, Förenade Arabemiraten Nord
    * Västeuropa, Västeuropa, Förenade Arabemiraten, norra, Sydostasien
    * Västra USA, Västeuropa, Asien, östra
    * Västra USA, norra Europa, Sydostasien, Nord, sydöstra Afrika, Nord Förenade Arabemiraten 
* **Azure + Azure-front dörr** – distribuerad på en eller flera kombinationer av Azure-regioner som anges ovan
* **Azure + Azure CDN från Microsoft** – distribuerat på en enskild kombination av Azure-regioner som anges ovan
* **Azure** -och azure-Traffic Manager – distribuerat på en kombination av flera Azure-regioner som anges ovan

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Hur skiljer sig Internet Analyzer från andra övervaknings tjänster som tillhandahålls av Azure?

Internet Analyzer hjälper dig att förstå dina slutanvändares prestanda och hjälper till att förbättra prestandan. Även om andra Azure Monitoring-verktyg ger insikter om dina Azure-tjänster fokuserar Internet Analyzer på att mäta slut punkt till slut punkt på Internet för dina användare.

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>Hur hanteras mätnings data av Internet Analyzer?

Azure har [starka säkerhets processer och uppfyller en rad olika krav för efterlevnad](https://azure.microsoft.com/support/trust-center/). Endast du och ditt utsedda team har åtkomst till dina data. Microsoft-personal kan ha begränsad åtkomst till den endast under särskilda begränsade omständigheter med din vetskap. Den är krypterad under överföring och i vila.

## <a name="next-steps"></a>Nästa steg

Läs mer i [Översikt över Internet Analyzer](internet-analyzer-overview.md).
