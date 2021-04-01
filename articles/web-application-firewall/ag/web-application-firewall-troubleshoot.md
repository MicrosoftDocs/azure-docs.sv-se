---
title: Felsök – brand väggen för Azure-webbprogram
description: Den här artikeln innehåller felsöknings information för brand vägg för webbaserade program (WAF) för Azure Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 483d261a8cc107d01cfb7a405eac43667d7efcc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92131844"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Felsöka brand vägg för webbaserade program (WAF) för Azure Application Gateway

Det finns några saker du kan göra om förfrågningar som ska passera genom brand väggen för webbaserade program (WAF) är blockerade.

Se först till att du har läst [WAF-översikten](ag-overview.md) och [WAF-konfigurations](application-gateway-waf-configuration.md) dokumenten. Kontrol lera också att du har aktiverat [WAF övervakning](../../application-gateway/application-gateway-diagnostics.md) av de här artiklarna förklarar hur WAF-funktionerna fungerar, hur WAF regel uppsättningar fungerar och hur du kommer åt WAF-loggar.

OWASP-rulesets är utformade för att vara mycket strikt och kunna anpassas efter de specifika behoven hos programmet eller organisationen som använder WAF. Den är helt normal och förväntas i många fall, för att skapa undantag, anpassade regler och till och med inaktivera regler som kan orsaka problem eller falska positiva identifieringar. Principer per plats och per URI tillåter att dessa ändringar endast påverkar specifika platser/URI: er, så att ändringar inte behöver påverka andra platser som kanske inte körs på samma problem. 

## <a name="understanding-waf-logs"></a>Förstå WAF-loggar

Syftet med WAF-loggar är att visa varje begäran som matchas eller blockeras av WAF. Det är en redovisning av alla utvärderade begär Anden som matchas eller blockeras. Om du märker att WAF blockerar en begäran om att den inte ska (ett falskt positivt) kan du göra några saker. Först, begränsa och hitta en specifik begäran. Leta igenom loggarna för att hitta URI, tidsstämpel eller transaktions-ID för begäran. När du hittar de associerade logg posterna kan du börja arbeta med falska positiva identifieringar.

Anta till exempel att du har en legitim trafik som innehåller strängen *1 = 1* som du vill skicka genom din WAF. Om du testar begäran blockerar WAF trafik som innehåller din *1 = 1* -sträng i valfri parameter eller fält. Detta är en sträng som ofta är kopplad till en SQL-attack. Du kan titta igenom loggarna och se tidsstämpeln för begäran och reglerna som har blockerats/matchats.

I följande exempel kan du se att fyra regler utlöses under samma begäran (med hjälp av fältet TransactionId). Den första avstår att den matchade eftersom användaren använde en numerisk/IP-adress för begäran, vilket ökar avvikelse poängen med tre eftersom det är en varning. Nästa regel som matchade är 942130, vilket är den som du letar efter. Du kan se *1 = 1* i `details.data` fältet. Detta ökar avvikelse poängen genom tre igen, eftersom det också är en varning. Normalt ökar varje regel som har den åtgärd som **stämmer** överens med den avvikande poängen, och vid denna tidpunkt skulle avvikelse poängen vara sex. Mer information finns i fel [bedömnings läge](ag-overview.md#anomaly-scoring-mode).

De sista två logg posterna visar att begäran blockerades eftersom avvikelse poängen var tillräckligt hög. Dessa poster har en annan åtgärd än de andra två. De visar att de faktiskt *blockerade* begäran. Dessa regler är obligatoriska och kan inte inaktive ras. De bör inte betraktas som regler, men mer som kärn infrastruktur för WAF-interna.

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>Korrigera falska positiva identifieringar

Med den här informationen och kunskapen om regel 942130 är den som matchade strängen *1 = 1* , kan du göra några saker för att stoppa detta från att blockera din trafik:

- Använd en undantags lista

   Mer information om undantags listor finns i [WAF-konfiguration](application-gateway-waf-configuration.md#waf-exclusion-lists) .
- Inaktivera regeln.

### <a name="using-an-exclusion-list"></a>Använda en undantags lista

För att fatta ett informerat beslut om att hantera ett falskt positivt är det viktigt att bekanta dig med de tekniker som används i programmet. Anta till exempel att det inte finns någon SQL-Server i din tekniks tack och att du får falska positiva identifieringar som är relaterade till dessa regler. Att inaktivera dessa regler innebär inte nödvändigt vis att säkerheten minskas.

En fördel med att använda en undantags lista är att bara en speciell del av en begäran inaktive ras. Det innebär dock att en speciell undantag gäller för all trafik som passerar genom din WAF eftersom det är en global inställning. Detta kan till exempel leda till ett problem om *1 = 1* är en giltig begäran i bröd texten för en viss app, men inte för andra. En annan fördel är att du kan välja mellan brödtext, huvuden och cookies som ska uteslutas om ett visst villkor uppfylls, i stället för att utesluta hela begäran.

Ibland finns det fall där vissa parametrar får skickas till WAF på ett sätt som inte är intuitivt. Det finns till exempel en token som skickas när du autentiserar med hjälp av Azure Active Directory. Denna token, *__RequestVerificationToken*, vanligt vis skickas som en cookie för begäran. Men i vissa fall där cookies är inaktiverat, skickas denna token också som ett attribut för begäran eller "arg". I så fall måste du se till att *__RequestVerificationToken* läggs till i undantags listan som attribut för **begäran** .

![Undantag](../media/web-application-firewall-troubleshoot/exclusion-list.png)

I det här exemplet vill du undanta det **begärda attributnamnet** som är lika med *Text1*. Detta är uppenbart eftersom du kan se attributnamnet i brand Väggs loggarna: **data: matchade data: 1 = 1 som finns i argumenten: Text1:1 = 1**. Attributet är **Text1**. Du kan också hitta det här attributnamnet på några andra sätt, se [hitta attribut namn för begäran](#finding-request-attribute-names).

![Undantags listor för WAF](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>Inaktivera regler

Ett annat sätt att komma runt ett falskt positivt är att inaktivera regeln som matchade den angivna invärdet. WAF trodde var skadlig. Eftersom du har analyserat WAF-loggarna och har begränsat regeln till 942130 kan du inaktivera den i Azure Portal. Se [Anpassa brand Väggs regler för webb program via Azure Portal](application-gateway-customize-waf-rules-portal.md).

En fördel med att inaktivera en regel är att om du vet att all trafik som innehåller ett visst villkor som normalt kommer att blockeras är giltig trafik, kan du inaktivera regeln för hela WAF. Men om det bara är en giltig trafik i ett särskilt användnings fall öppnar du ett säkerhets problem genom att inaktivera regeln för hela WAF eftersom det är en global inställning.

Om du vill använda Azure PowerShell, se [Anpassa brand Väggs regler för webb program via PowerShell](application-gateway-customize-waf-rules-powershell.md). Om du vill använda Azure CLI går du till [Anpassa brand Väggs regler för webb program via Azure CLI](application-gateway-customize-waf-rules-cli.md).

![WAF-regler](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Söker efter attribut för begäran

Med hjälp av [Fiddler](https://www.telerik.com/fiddler)kan du kontrol lera enskilda förfrågningar och bestämma vilka specifika fält på en webb sida som anropas. Detta kan hjälpa till att utesluta vissa fält från granskning med undantags listor.

I det här exemplet kan du se att fältet där strängen *1 = 1* har angetts kallas **Text1**.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-1.png" alt-text="Skärm bild av Telerik-Fiddler för webb fel sökning. På fliken RAW är 1 = 1 synlig efter namnet Text1." border="false":::

Det här är ett fält som du kan undanta. Mer information om undantags listor finns i [begäran om storleks gränser och undantags listor för WebApplication-brandvägg](application-gateway-waf-configuration.md#waf-exclusion-lists). Du kan undanta utvärderingen i det här fallet genom att konfigurera följande undantag:

![WAF-undantag](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

Du kan också granska brand Väggs loggarna för att hämta information för att se vad du behöver lägga till i undantags listan. Om du vill aktivera loggning, se [backend-hälsa, resurs loggar och mått för Application Gateway](../../application-gateway/application-gateway-diagnostics.md).

Granska brand Väggs loggen och Visa PT1H.jspå filen under den timme som den begäran du vill kontrol lera inträffar.

I det här exemplet kan du se att du har fyra regler med samma TransactionID och att alla har inträffat på exakt samma tid:

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

Med dina kunskaper om hur du använder regel uppsättningarna för datoriserade system och att ruleset 3,0 fungerar med ett avvikande Poäng system (se [brand vägg för webbaserade program för Azure Application Gateway](ag-overview.md)) vet du att de två nedre reglerna med **åtgärden: blockerad** egenskap blockerar baserat på den totala avvikelse poängen. De regler som ska fokuseras på är de två översta.

Den första posten loggas eftersom användaren använde en numerisk IP-adress för att navigera till Application Gateway, som kan ignoreras i det här fallet.

Den andra (regel 942130) är den intressanta en. Du kan se information om att den matchade ett mönster (1 = 1) och att fältet heter **Text1**. Följ samma föregående steg för att undanta det **begärda attributnamnet** som **är lika med** **1 = 1**.

## <a name="finding-request-header-names"></a>Söker efter begärans huvud namn

Fiddler är ett användbart verktyg en gång till för att hitta rubrik namn för begäran. I följande skärm bild kan du se huvudena för GET-begäran, som innehåller *innehålls typ*, *användar agent* och så vidare.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-2.png" alt-text="Skärm bild av Telerik-Fiddler för webb fel sökning. På fliken RAW visas information om begär ande rubrik som anslutning, innehålls typ och användar agent." border="false":::

Ett annat sätt att Visa begäran och svarshuvuden är att titta närmare på utvecklarverktyg i Chrome. Du kan trycka på F12 eller högerklicka – > **inspektera**  ->  **utvecklarverktyg** och välj fliken **nätverk** . Läs in en webb sida och klicka på den begäran som du vill granska.

![Chrome F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Söker efter cookie-namn för begäran

Om begäran innehåller cookies kan fliken **cookies** väljas för att visa dem i Fiddler.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Begränsa globala parametrar för att eliminera falska positiva identifieringar

- Inaktivera kontroll av begär ande brödtext

   Genom att ställa in **kontrol lera att begär ande texten** är inaktive rad utvärderas inte WAF. Detta kan vara användbart om du vet att begär ande organ inte är skadliga för ditt program.

   Genom att inaktivera det här alternativet kontrol leras bara begär ande texten. Rubrikerna och cookies förblir kontrollerade, om inte enskilda inte är uteslutna med hjälp av undantags listans funktioner.

- Fil storleks begränsningar

   Genom att begränsa fil storleken för din WAF begränsar du risken för att en attack går till dina webb servrar. Genom att tillåta att stora filer överförs ökar risken för att din server del blir mer utsatt. Att begränsa fil storleken till ett normalt användnings fall för ditt program är bara ett annat sätt att förhindra attacker.

   > [!NOTE]
   > Om du vet att din app aldrig behöver ha några fil överföringar över en bestämd storlek, kan du begränsa detta genom att ange en gräns.

## <a name="firewall-metrics-waf_v1-only"></a>Brand Väggs mått (endast WAF_v1)

För v1 webb program brand väggar är följande mått nu tillgängliga i portalen: 

1. Blockerad begäran om brand vägg för webbaserade program antalet begär Anden som blockerades
2. Blockerad brand vägg för webb program brand vägg antal regler som har matchats **och** begäran blockerades
3. Brand vägg för brand vägg total regel distribution alla regler som matchades under utvärderingen
     
Om du vill aktivera mått väljer du fliken **mått** i portalen och väljer ett av de tre måtten.

## <a name="next-steps"></a>Nästa steg

Se [Konfigurera brand vägg för webbaserade program på Application Gateway](tutorial-restrict-web-traffic-powershell.md).
