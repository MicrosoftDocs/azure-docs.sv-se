---
title: Leverera ett koncept bevis – Azure DevTest Labs | Microsoft Docs
description: Lär dig att leverera ett koncept bevis så Azure DevTest Labs kan integreras i en företags miljö.
ms.topic: article
ms.date: 06/2/2020
ms.openlocfilehash: 9c28cf9eebd8a39a2edce48e4fb8b96dc7608d80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87288027"
---
# <a name="deliver-a-proof-of-concept"></a>Leverera ett koncept bevis 

Ett av de viktigaste scenarierna för Azure DevTest Labs är att aktivera utvecklings-och testnings miljöer i molnet. Exempel:

* Skapa skriv bord för utvecklare i molnet.
* Konfigurera miljöer för testning.
* Aktivera åtkomst till virtuella datorer och andra Azure-resurser.
* Konfigurera ett begränsat områden för utvecklare att lära sig och experimentera.

DevTest Labs innehåller också principen guardrails och kostnads kontroller som gör det möjligt för företaget att tillhandahålla "självbetjänings Azure" till utvecklare som följer företagets principer för säkerhet, regler och efterlevnad. 

Alla företag har olika krav för hur Azure DevTest Labs kan införlivas i deras miljö. I den här artikeln beskrivs de vanligaste stegen som företag måste utföra för att få ett välklarat koncept bevis. Ett koncept bevis är det första steget mot en lyckad distribution från slut punkt till slut punkt. 

## <a name="getting-started"></a>Komma igång 

För att komma igång med att leverera ett koncept bevis. Det är viktigt att ägna lite tid åt att lära dig mer om Azure och DevTest Labs.  Här är några start resurser: 

* [Förstå Azure Portal](https://azure.microsoft.com/features/azure-portal/)
* [Grunderna för DevTest Labs](devtest-lab-overview.md)
* [Scenarier som stöds av DevTest Labs](devtest-lab-guidance-get-started.md)
* [Dokumentation om DevTest Labs Enterprise](devtest-lab-guidance-prescriptive-adoption.md)
* [Introduktion till Azure-nätverk](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Förutsättningar 

För att kunna slutföra en pilot eller ett koncept bevis med DevTest Labs finns det några krav: 

* **Azure-prenumeration**: företag har ofta en befintlig [Enterprise-avtal](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) på plats som ger åtkomst till Azure, och de kan använda en befintlig eller ny prenumeration för DevTest Labs. Företag kan också använda en [Visual Studio-prenumeration](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) under piloten (med fördel av de kostnads fria Azure-krediterna). Om inget av dessa alternativ är tillgängligt kan företaget skapa och använda ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ). Om det finns en Enterprise-avtal på plats är det ett bra alternativ att använda en [Enterprise dev/test-prenumeration](https://azure.microsoft.com/offers/ms-azr-0148p/) för att få åtkomst till klient operativ systemen Windows 10/Windows 8,1 och rabatterat pris för utveckling och testning av arbets belastningar. 
* **Azure Active Directory klient**: om du vill aktivera hantering av användare (till exempel lägga till användare eller lägga till labb ägare) måste användarna vara en del av den [Azure Active Directory klient organisationen](https://azure.microsoft.com/services/active-directory/) som används i Azure-prenumerationen för piloten. Företag kommer ofta att ställa in [Hybrid identiteter](../active-directory/hybrid/whatis-hybrid-identity.md) så att användarna kan använda sina lokala identiteter i molnet, men detta krävs inte för DevTest Labs-piloten. 

## <a name="scoping-of-the-pilot"></a>Omfånget för piloten 

Det är viktigt att planera en pilot innan du påbörjar implementeringen. I förväg vet du att resurserna inte hålls kvar runt de oändliga uppsättningarna med lämpliga förväntningar för användare av piloten. 

> [!IMPORTANT]
> Vi kan inte fokusera tillräckligt mycket av att klara piloten och ställa in förväntningarna direkt.

Besvara dessa viktiga frågor innan du startar piloten: 

* Vad vill du lära dig och hur ser det ut som för piloten? 
* Vilka arbets belastningar eller scenarier kommer att omfattas av piloten? Det är viktigt att endast definiera en liten uppsättning för att se till att piloten kan komma att omfångs och slutföras snabbt. 
* Vilka resurser måste vara tillgängliga i labbet? Exempel: anpassade avbildningar, Marketplace-avbildningar, principer, nätverkstopologi. 
* Vilka är de användare och team som kommer att ingå i piloten för att verifiera erfarenheten?  
* Vad är pilotens varaktighet? Välj en tidsram som justeras bra till planerat omfång, till exempel två veckor eller en månad. 
* Vad händer när piloten är slutförd, vad händer med de tilldelade resurserna som användes under piloten? Planerar du att ta bort pilot resurserna? Du kan tänka dig:
   
   "Om vi kan planera för att ta bort de virtuella datorerna och labben i slutet av piloten kan vi konfigurera en enda prenumeration för piloten och göra allt det där, medan vi löser problemet med att skala distributionen parallellt." 

Det finns en allmän tendens att göra piloten "perfekt" så att den motsvarar det slutliga tillstånd som kommer att ske efter att tjänsten har distribuerats till företaget. Detta är ett falskt antagande. Desto närmare blir du "perfekt", desto mer måste du slutföra *innan* du börjar använda piloten. Syftet med piloten är att fatta rätt beslut om att skala upp och ut ur den slutliga tjänsten. 

Pilotens fokus bör vara att välja minst de arbets belastningar och beroenden som krävs för att besvara frågan om Azure DevTest Labs är rätt tjänst för ditt företag. Vi rekommenderar att du väljer den enklaste arbets belastningen med minst beroenden för att säkerställa en snabb och ren framgång. Om det inte är möjligt väljer du en mest representativ arbets belastning som visar potentiella komplicerade risker så att framgång i pilot fasen kan replikeras i den skalbara fasen. 

I följande exempel visas ett väldefinierat koncept bevis. 

## <a name="example-proof-of-concept-plan"></a>Exempel: proof-of-Concept plan 

Det här avsnittet innehåller ett exempel som du kan använda för att omfånget för ett koncept bevis för piloten för DevTest Labs. 

> [!TIP]
> Vi rekommenderar starkt att du inte hoppar över exemplet som beskrivs i det här avsnittet för att minimera risken för att skapa ett projekt för haveri. 

### <a name="overview"></a>Översikt 

Vi planerar att utveckla en ny miljö i Azure baserat på DevTest Labs för leverantörer som ska användas som isolerad miljö från företags nätverket. För att avgöra om lösningen uppfyller kraven kommer vi att utveckla ett koncept bevis för att validera lösningen från slut punkt till slut punkt. Vi har inkluderat flera leverantörer för att testa och verifiera upplevelsen. 

### <a name="outcomes"></a>Resultat 

När vi skapar ett POC-koncept fokuserar vi först på resultatet (Vad försöker vi uppnå). I slutet av koncept beviset förväntas vi: 

* En fungerande heltäckande lösning för leverantörer att använda gäst konton i Azure Active Directory (Azure AD) för att få åtkomst till en isolerad miljö i Azure. Miljön har de resurser som krävs för att de ska vara produktiva. 
* Eventuella problem som kan blockeras och som påverkar den bredare skalnings användningen och användningen räknas upp och förstås.
* De personer som sysslar med att utveckla POC-beviset har en god förståelse för all kod. De kan också förstå vad som är viktigt och är tryggare införande.

### <a name="open-questions-and-prerequisites"></a>Öppna frågor och krav 

* Har vi en prenumeration som vi kan använda för det här projektet? 
* Har vi en Azure AD-klient och en global Azure AD-administratör identifierad som kan ge hjälp och vägledning för Azure AD-relaterade frågor? 
* Har vi en plats för att samar beta för de personer som arbetar med projektet? 

   * Källkod och skript (som Azure databaser) 
   * Dokument (till exempel Microsoft Teams eller SharePoint)  
   * Konversationer (t. ex. Microsoft-team) 
   * Arbets objekt (till exempel Azure-kort) 
* Vilka är de resurser som krävs för leverantörer? Detta inkluderar program som är tillgängliga i nätverket, både lokalt på de virtuella datorerna och på andra nödvändiga servrar. 
* Kommer de virtuella datorerna att vara anslutna till en domän i Azure? I så fall, kommer detta att Azure Active Directory Domain Services (Azure AD DS) eller något annat? 
* Har vi identifierat de team eller leverantörer som är målet för koncept beviset? Vilka är kunderna för miljön?
* Vilken Azure-region kommer vi att använda för koncept beviset? 
* Har vi en lista över tjänster som leverantörerna får använda via DevTest Labs förutom IaaS (VM)? 
* Hur planerar vi att träna leverantörer/användare om att använda labbet? 

### <a name="components-of-the-proof-of-concept-solution"></a>Komponenter i koncept bevis lösningen 

Vi förväntar oss att lösningen ska ha följande komponenter: 

* Olika leverantörs team kommer att använda en uppsättning labb i Azure.
* Labben är anslutna till en nätverks infrastruktur som uppfyller kraven.
* Leverantörerna har åtkomst till labben via Azure AD och roll tilldelningar.
* Leverantörer har möjlighet att ansluta till sina resurser. Mer specifikt möjliggör en plats-till-plats-VPN åtkomst till virtuella datorer direkt utan offentliga IP-adresser.
* En uppsättning artefakter omfattar nödvändig program vara som leverantörer behöver på de virtuella datorerna.

## <a name="additional-planning-and-design-decisions"></a>Ytterligare planerings-och design beslut 

Innan du släpper en fullständig DevTest Labs-lösning måste du fatta några viktiga planerings-och design beslut. Erfarenheten av att arbeta med koncept bevis kan hjälpa dig att fatta besluten. Ytterligare överväganden omfattar: 

* **Prenumerations sto pol Ogin**: kraven på företags nivå för resurser i Azure kan utökas utöver de [tillgängliga kvoterna i en enda prenumeration](../azure-resource-manager/management/azure-subscription-service-limits.md). Detta kräver flera Azure-prenumerationer och/eller tjänst begär Anden för att öka den inledande prenumerations gränsen. Det är viktigt att du bestämmer dig för att distribuera resurser mellan prenumerationer. En värdefull resurs är [prenumerations besluts hand boken](/azure/architecture/cloud-adoption/decision-guides/subscriptions/) eftersom det är svårt att flytta resurser till en annan prenumeration senare. Ett labb kan till exempel inte flyttas till en annan prenumeration när den har skapats.  
* **Nätverkstopologi**: den [standard nätverks infrastruktur](../app-service/networking-features.md) som DevTest Labs skapar kanske inte räcker för att uppfylla kraven och begränsningarna för företags användare. Det är vanligt att se [Azure ExpressRoute-anslutna virtuella nätverk](/azure/architecture/reference-architectures/hybrid-networking/), [nav-och-eker](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) för anslutning mellan prenumerationer och till och med [Tvingad routning](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) för att säkerställa endast lokal anslutning. Med DevTest Labs kan befintliga virtuella nätverk anslutas till labbet så att de kan användas när du skapar nya virtuella datorer i labbet. 
* **Fjärråtkomst till virtuella datorer**: det finns många alternativ för att fjärrans luta till de virtuella datorer som finns i DevTest Labs. Det enklaste sättet är att använda offentliga IP-adresser eller delade offentliga IP-adresser. Dessa [inställningar är tillgängliga i labbet](devtest-lab-shared-ip.md). Om dessa alternativ inte räcker, är det också ett alternativ att använda en gateway för fjärråtkomst. Det här alternativet visas i [DevTest Labs Enterprise-referens arkitektur](devtest-lab-reference-architecture.md) och beskrivs ytterligare i [dokumentationen för DevTest Labs Remote Desktop Gateway](configure-lab-remote-desktop-gateway.md). Företag kan också använda ExpressRoute eller en plats-till-plats-VPN för att ansluta sina labb till sitt lokala nätverk. Med det här alternativet aktive ras direkta fjärr skrivbords-eller SSH-anslutningar till de virtuella datorerna baserat på deras privata IP-adress utan exponering för Internet. 
* **Hanterings behörigheter**: de två nyckel behörigheter som ofta används i DevTest Labs är [ägare och labb användare](devtest-lab-add-devtest-user.md). Det är viktigt att bestämma innan du utvärderar DevTest Labs i stort sett vem som ska anförtros varje nivå av åtkomst i labbet. En gemensam modell är budget ägaren (till exempel grupp ledare) som labb ägare och grupp medlemmar som labb användare. Den här modellen gör det möjligt för den person (grupp ledare) som ansvarar för budgeten att justera princip inställningarna och hålla teamet inom budgeten.  

## <a name="completing-the-proof-of-concept"></a>Slutför koncept beviset 

När förväntad information har täckts är det dags att slutföra piloten. Det är dags att samla in feedback från användarna, avgöra om piloten lyckades och bestämma om organisationen ska fortsätta att distribuera DevTest Labs i företaget. Det är också en bra stund att överväga att automatisera distributionen av DevTest Labs och tillhör ande resurser för att säkerställa konsekvens i hela distributionen. 

## <a name="next-steps"></a>Nästa steg 

* [Dokumentation om DevTest Labs Enterprise](devtest-lab-guidance-prescriptive-adoption.md)
* [Referens arkitektur för ett företag](devtest-lab-reference-architecture.md)
* [Skala upp din DevTest Labs-distribution](devtest-lab-guidance-orchestrate-implementation.md)
* [Dirigera implementeringen av Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
