---
title: Referens för Azure CDNs regel motor | Microsoft Docs
description: Referens dokumentation för Azure CDN regel motor matchar villkor och funktioner.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: 295bc0a20a547bf944f48af6711b18af34571b02
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91362588"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Azure CDN från Verizon Premium-regel motor referens

Den här artikeln innehåller detaljerade beskrivningar av de tillgängliga matchnings villkoren och funktionerna för [regel motorn](cdn-verizon-premium-rules-engine.md)för Azure Content Delivery Network (CDN).

Regel motorn är utformad för att vara den slutliga behörigheten för hur vissa typer av begär Anden bearbetas av CDN.

**Vanliga användnings områden**:

- Åsidosätt eller definiera en anpassad princip för cachelagring.
- Skydda eller neka begär Anden om känsligt innehåll.
- Omdirigera begär Anden.
- Lagra anpassade logg data.
## <a name="key-concepts"></a>Viktiga begrepp
Viktiga begrepp för att ställa in regel motor beskrivs nedan.
### <a name="draft"></a>Draft
Ett utkast till en princip består av en eller flera regler som är avsedda att identifiera begär Anden och den uppsättning åtgärder som ska tillämpas på dem. Ett utkast är ett pågående arbete som tillåter frekventa konfigurations uppdateringar utan att påverka plats trafiken. När ett utkast är klart att slutföras, ska det konverteras till en skrivskyddad princip.

### <a name="rule"></a>Regel
En regel identifierar en eller flera typer av begär Anden och den uppsättning åtgärder som ska tillämpas på dem.

Det består av: 

- En uppsättning villkors uttryck som definierar den logik genom vilken begär Anden identifieras.
- En uppsättning matchnings villkor som definierar de kriterier som används för att identifiera begär Anden.
- En uppsättning funktioner som definierar hur CDN ska hantera ovanstående begär Anden.
Dessa element identifieras i följande bild.

![Skärm bild med etiketter visar villkors uttrycket, matchningen och funktionerna i en regel.](./media/cdn-verizon-premium-rules-engine-reference/verizon-rules-engine-reference.png)

### <a name="policy"></a>Policy
En princip som består av en uppsättning skrivskyddade regler innebär att:

- Skapa, lagra och hantera flera varianter av reglerna.
- Återställa till en tidigare distribuerad version.
- Förbered Event-specifika regler i förväg (t. ex. en regel som omdirigerar trafik till följd av ett kund ursprung underhåll.)

> [!NOTE]
> Även om bara en enda princip per miljö tillåts kan principer distribueras vid behov.

### <a name="deploy-request"></a>Distribuera begäran
En distributions förfrågan ger en enkel och strömlinjeformad procedur genom vilken en princip snabbt kan tillämpas på mellanlagrings-eller produktions miljön. En historik över distributions begär Anden tillhandahålls för att under lätta spårningen av ändringar som tillämpas på dessa miljöer.

> [!NOTE]
> Endast begär Anden som inte klarar våra automatiska verifierings-och fel identifierings system kräver manuell granskning och godkännande.

### <a name="rule-precedence"></a>Regel prioritet
Reglerna som finns i en princip bearbetas vanligt vis i den ordning som de visas i listan (dvs. längst ned). Om begäran matchar motstridiga regler, prioriteras den senaste regeln som ska bearbetas.

### <a name="policy-deployment-workflow"></a>Arbets flöde för princip distribution
Det arbets flöde genom vilket en princip kan tillämpas på antingen produktions-eller mellanlagringsdatabasen illustreras nedan.

![Arbets flöde för princip distribution](./media/cdn-verizon-premium-rules-engine-reference/policy-deployment-workflow.png)

|Steg |Beskrivning |
|---------|---------|
|[Skapa utkast](https://docs.vdms.com/cdn/index.html#HRE/AdministeringDraftsandRules.htm#Create)    |    Ett utkast består av en uppsättning regler som definierar hur begär Anden för ditt innehåll ska hanteras av CDN.     |
|Lås utkast   |     När ett utkast har slutförts bör det låsas och konverteras till en skrivskyddad princip.    |
|[Skicka distributions förfrågan](https://docs.vdms.com/cdn/index.html#HRE/DeployRequest.htm)   |   <br> En distributions förfrågan gör det möjligt att tillämpa en princip för antingen test-eller produktions trafik.</br> <br>Skicka en distributions förfrågan till antingen mellanlagrings-eller produktions miljön.</br>     |
|Distribuera granskning av begäran   |    <br>En distributions förfrågan görs med automatiserad verifiering och fel identifiering.</br><br>Även om majoriteten av distributions förfrågningarna godkänns automatiskt, krävs manuell granskning för mer komplexa principer.</br>   |
|Princip distribution ([mellanlagring](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Staging))   |  <br> Vid godkännande av en distributions förfrågan till mellanlagrings miljön kommer en princip att tillämpas på mellanlagrings miljön. Den här miljön gör att en princip kan testas mot en modell plats trafik.</br><br>När principen är redo att tillämpas på den aktiva plats trafiken bör en ny distributions förfrågan för produktions miljön skickas.</br>      |
|Princip distribution ([produktion](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Producti))   |  Vid godkännande av en distributions förfrågan till produktions miljön kommer en princip att tillämpas på produktions miljön. I den här miljön kan en princip fungera som slutgiltig myndighet för att fastställa hur CDN ska hantera direktsänd trafik.     |
## <a name="syntax"></a>Syntax

Det sätt på vilket specialtecken behandlas varierar beroende på hur ett matchnings villkor eller en funktion hanterar text värden. Ett matchnings villkor eller en funktion kan tolka text på något av följande sätt:

- [**Litterala värden**](#literal-values)
- [**Jokertecken**](#wildcard-values)
- [**Reguljära uttryck**](#regular-expressions)

### <a name="literal-values"></a>Litterala värden

Text som tolkas som ett litteralt värde behandlar alla specialtecken, med undantag för symbolen%, som en del av värdet som måste matchas. Ett exakt matchnings villkor `\'*'\` är dock bara uppfyllt när det exakta värdet (det vill säga `\'*'\` ) hittas.

En procent symbol används för att indikera URL-kodning (till exempel `%20` ).

### <a name="wildcard-values"></a>Jokertecken

Text som tolkas som ett jokertecken tilldelar ytterligare en mening till specialtecken. I följande tabell beskrivs hur följande tecken uppsättning tolkas:

Tecken | Beskrivning
----------|------------
\ | Ett omvänt snedstreck används för att undanta de tecken som anges i den här tabellen. Ett omvänt snedstreck måste anges direkt före det specialtecken som ska undantas.<br/>Följande syntax kan till exempel undanta en asterisk: `\*`
% | En procent symbol används för att indikera URL-kodning (till exempel `%20` ).
\* | En asterisk är ett jokertecken som representerar ett eller flera tecken.
Space | Ett blank steg anger att ett matchnings villkor kan uppfyllas av något av de angivna värdena eller mönstren.
värde | Ett enkelt citat har ingen särskild betydelse. En uppsättning enkla citat tecken används dock för att ange att ett värde ska behandlas som ett litteralt värde. Den kan användas på följande sätt:<br><br/>– Det gör att ett matchnings villkor uppfylls när det angivna värdet matchar någon del av jämförelse värdet.  `'ma'`Skulle exempelvis matcha någon av följande strängar: <br/><br/>/Business/**ma** rathon/asset.htm<br/>**ma** p.gif<br/>/business/template. **ma** p<br /><br />– Det gör att ett specialtecken kan anges som ett litteralt tecken. Du kan till exempel ange ett tecken för tecken avstånd genom att omsluta ett blank steg i en uppsättning enkla citat tecken (det vill säga `' '` eller `'sample value'` ).<br/>– Det gör att du kan ange ett tomt värde. Ange ett tomt värde genom att ange en uppsättning enkla citat tecken (det vill säga).<br /><br/>**Viktigt!**<br/>-Om det angivna värdet inte innehåller ett jokertecken betraktas det automatiskt som ett tecken värde, vilket innebär att det inte är nödvändigt att ange en uppsättning enkla citat tecken.<br/>– Om ett omvänt snedstreck inte översätter ett annat tecken i tabellen, ignoreras det när det anges i en uppsättning enkla citat tecken.<br/>Ett annat sätt att ange ett specialtecken som ett litteralt tecken är att kringgå det med ett omvänt snedstreck (det vill säga `\` ).

### <a name="regular-expressions"></a>Reguljära uttryck

Reguljära uttryck definierar ett mönster som genomsöks i ett text värde. Reguljär uttrycks notation definierar olika betydelser för olika symboler. I följande tabell visas hur specialtecken behandlas av matchnings villkor och funktioner som stöder reguljära uttryck.

Specialtecken | Beskrivning
------------------|------------
\ | Ett omvänt snedstreck utvärderar det tecken som följer, vilket gör att tecken behandlas som ett litteralt värde i stället för dess reguljära uttryck. Följande syntax kan till exempel undanta en asterisk: `\*`
% | Innebörden av en procent symbol beror på dess användning.<br/><br/> `%{HTTPVariable}`: Den här syntaxen identifierar en HTTP-variabel.<br/>`%{HTTPVariable%Pattern}`: Den här syntaxen använder en procent symbol för att identifiera en HTTP-variabel och som avgränsare.<br />`\%`: Med en procents symbol kan det användas som ett litteralt värde eller för att indikera URL-kodning (till exempel `\%20` ).
\* | En asterisk tillåter att föregående tecken matchas noll eller flera gånger.
Space | Ett blank stegs tecken behandlas vanligt vis som ett tecken.
värde | Enkla citat tecken behandlas som litterala tecken. En uppsättning enkla citat tecken har ingen särskild betydelse.

Matcha villkor och funktioner som stöder reguljära uttryck accepterar mönster som definieras av perl-kompatibla reguljära uttryck (PCRE).



## <a name="next-steps"></a>Nästa steg

- [Regelmotor – matchningsvillkor](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Regelmotor – villkorliga uttryck](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regelmotor – funktioner](cdn-verizon-premium-rules-engine-reference-features.md)
- [Åsidosätt HTTP-beteende med regel motorn](cdn-verizon-premium-rules-engine.md)
- [Översikt över Azure CDN](cdn-overview.md)
