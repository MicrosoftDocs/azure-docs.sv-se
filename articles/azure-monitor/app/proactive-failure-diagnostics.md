---
title: Smart identifiering – fel avvikelser, i Application Insights | Microsoft Docs
description: Varnar dig om ovanliga ändringar av antalet misslyckade förfrågningar till din webbapp och ger diagnostisk analys. Ingen konfiguration krävs.
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: 978b63d74e6be4104ff53eef66e9633c78b90eb8
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510642"
---
# <a name="smart-detection---failure-anomalies"></a>Smart identifiering – fel avvikelser
[Application Insights](./app-insights-overview.md) automatiskt varna dig i nära real tid om din webbapp upplever en onormal ökning av antalet misslyckade förfrågningar. Den identifierar en ovanlig ökning i frekvensen av HTTP-begäranden eller beroende anrop som rapporteras som misslyckade. För förfrågningar har misslyckade förfrågningar vanligt vis svars koder på 400 eller högre. För att hjälpa dig att prioritering och diagnostisera problemet finns en analys av egenskaperna för fel och relaterade program data i aviserings informationen. Det finns också länkar till Application Insights Portal för ytterligare diagnos. Funktionen behöver inte konfigureras eller konfigureras eftersom den använder Machine Learning-algoritmer för att förutsäga den normala felfrekvensen.

Den här funktionen fungerar för alla webb program, som finns i molnet eller på dina egna servrar, som genererar programbegäran eller beroende data. Om du till exempel har en arbets roll som anropar [TrackRequest ()](./api-custom-events-metrics.md#trackrequest) eller [TrackDependency ()](./api-custom-events-metrics.md#trackdependency).

När du har konfigurerat [Application Insights för projektet](./app-insights-overview.md), och om din app genererar en viss minsta mängd data, tar det en smart identifiering av fel avvikelser i 24 timmar att lära sig om appens normala beteende, innan den slås på och kan skicka aviseringar.

Här är ett exempel på en avisering:

:::image type="content" source="./media/proactive-failure-diagnostics/013.png" alt-text="Exempel på Smart identifierings avisering som visar kluster analys kring fel." lightbox="./media/proactive-failure-diagnostics/013.png":::

Aviserings informationen meddelar dig:

* Felfrekvensen jämfört med normalt appens beteende.
* Hur många användare påverkas – så att du vet hur mycket du behöver oroa dig.
* Ett egenskaps mönster som är kopplat till felen. I det här exemplet finns det en viss svarskod, ett namn på begäran (åtgärd) och program version. Det visar genast var du börjar titta på koden. Andra möjligheter kan vara en speciell webbläsare eller ett klient operativ system.
* Undantag, logg spårningar och beroende fel (databaser eller andra externa komponenter) som verkar vara associerade med de felbara felen.
* Länkar direkt till relevanta sökningar efter data i Application Insights.

## <a name="benefits-of-smart-detection"></a>Förmåner för smart identifiering
Vanliga [mått aviseringar](../platform/alerts-log.md) visar att det kan finnas ett problem. Men smart identifiering startar diagnostiskt arbete åt dig, så att du kan utföra mycket analys som du annars skulle behöva göra själv. Du får de resultat som du kan paketera, vilket hjälper dig att snabbt komma till orsaken till problemet.

## <a name="how-it-works"></a>Så här fungerar det
Smart identifiering övervakar de data som tas emot från din app och särskilt fel frekvensen. Den här regeln räknar antalet begär Anden som `Successful request` egenskapen är falsk till och antalet beroende anrop för vilka `Successful call` egenskapen är falsk. Som standard för förfrågningar `Successful request == (resultCode < 400)` (om du inte har skrivit anpassad kod för att [filtrera](./api-filtering-sampling.md#filtering) eller generera dina egna [TrackRequest](./api-custom-events-metrics.md#trackrequest) -anrop). 

Appens prestanda har ett typiskt beteende mönster. Vissa förfrågningar eller beroende anrop är svårare att Miss lyckas än andra. och den övergripande felfrekvensen kan gå upp när belastningen ökar. Smart identifiering använder Machine Learning för att hitta dessa avvikelser.

När data kommer till Application Insights från din webbapp jämför Smart identifiering det aktuella beteendet med de mönster som visas under de senaste dagarna. Om en onormal ökning av felfrekvensen observeras genom jämförelse med tidigare prestanda utlöses en analys.

När en analys utlöses utför tjänsten en kluster analys på den misslyckade begäran, för att försöka identifiera ett mönster med värden som karaktäriserar felen. 

I exemplet ovan har analysen identifierat att de flesta felen är en viss resultat kod, namn på begäran, server-URL-värd och roll instans. 

När din tjänst instrumenteras med dessa anrop, söker Analyzer efter ett undantag och ett beroende fel som är associerat med begär anden i klustret. det har identifierats tillsammans med ett exempel på alla spårnings loggar som är kopplade till dessa förfrågningar.

Den resulterande analysen skickas till dig som avisering, om du inte har konfigurerat den inte till.

Precis som [aviseringarna som du anger manuellt](../platform/alerts-log.md)kan du kontrol lera statusen för den utlöst aviseringen, som kan lösas om problemet är löst. Konfigurera varnings reglerna på sidan aviseringar i din Application Insights-resurs. Men till skillnad från andra aviseringar behöver du inte konfigurera eller konfigurera Smart identifiering. Om du vill kan du inaktivera den eller ändra dess mål-e-postadresser.

### <a name="alert-logic-details"></a>Information om aviserings logik

Aviseringarna utlöses av vår leverantörsspecifika Machine Learning-algoritm så att vi inte kan dela den exakta implementerings informationen. Med detta, förstår vi att du ibland behöver veta mer om hur den underliggande logiken fungerar. De primära faktorer som utvärderas för att avgöra om en avisering ska utlösas är: 

* Analys av misslyckad procent andel begär Anden/beroenden i en rullande tids period på 20 minuter.
* En jämförelse av antalet misslyckade procent av de senaste 20 minuterna i priset under de senaste 40 minuterna och de senaste sju dagarna och letar efter betydande avvikelser som överstiger X-gånger som standard avvikelsen.
* Använder en anpassad gräns för minsta antal misslyckade procent, vilket varierar beroende på appens volym för begär Anden/beroenden.
* Det finns logik som automatiskt kan lösa det utlösma aviserings övervaknings villkoret, om problemet inte längre upptäcks i 8-24 timmar.
  Obs: i den aktuella designen. ett meddelande eller en åtgärd skickas inte när en avisering om Smart identifiering har åtgärd ATS. Du kan kontrol lera om en avisering om Smart identifiering har lösts i Azure Portal.

## <a name="configure-alerts"></a>Konfigurera varningar

Du kan inaktivera aviserings regeln för smart identifiering från portalen eller använda Azure Resource Manager ([se mall exempel](./proactive-arm-config.md)).

Den här varnings regeln skapas med en associerad [Åtgärds grupp](../platform/action-groups.md) med namnet "Application Insights Smart identifiering" som innehåller e-post-och webhook-åtgärder och kan utökas för att utlösa ytterligare åtgärder när aviseringen utlöses.

> [!NOTE]
> E-postmeddelanden som skickas från den här varnings regeln skickas nu som standard till användare som är associerade med prenumerationens roll för övervakning och övervakning av deltagare. Mer [information finns här.](./proactive-email-notification.md)
> Meddelanden som skickas från den här aviserings regeln följer det [vanliga aviserings schemat](../platform/alerts-common-schema.md).
>

Öppna sidan aviseringar. Aviserings regler för fel avvikelser inkluderas tillsammans med eventuella aviseringar som du har angett manuellt, och du kan se om det är i aviserings tillstånd.

:::image type="content" source="./media/proactive-failure-diagnostics/021.png" alt-text="På sidan Application Insights resurs klickar du på panelen aviseringar och hanterar sedan aviserings regler." lightbox="./media/proactive-failure-diagnostics/021.png":::

Klicka på aviseringen för att konfigurera den.

:::image type="content" source="./media/proactive-failure-diagnostics/032.png" alt-text="Regel konfigurations skärmen." lightbox="./media/proactive-failure-diagnostics/032.png":::

Observera att du kan inaktivera eller ta bort en varnings regel för fel avvikelser, men du kan inte skapa en annan på samma Application Insights-resurs.

## <a name="example-of-failure-anomalies-alert-webhook-payload"></a>Exempel på fel avvikelser avisering webhook nytto Last

```json
{
    "properties": {
        "essentials": {
            "severity": "Sev3",
            "signalType": "Log",
            "alertState": "New",
            "monitorCondition": "Resolved",
            "monitorService": "Smart Detector",
            "targetResource": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule",
            "targetResourceName": "test-rule",
            "targetResourceGroup": "test-group",
            "targetResourceType": "microsoft.insights/components",
            "sourceCreatedId": "1a0a5b6436a9b2a13377f5c89a3477855276f8208982e0f167697a2b45fcbb3e",
            "alertRule": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.alertsmanagement/smartdetectoralertrules/failure anomalies - test-rule",
            "startDateTime": "2019-10-30T17:52:32.5802978Z",
            "lastModifiedDateTime": "2019-10-30T18:25:23.1072443Z",
            "monitorConditionResolvedDateTime": "2019-10-30T18:25:26.4440603Z",
            "lastModifiedUserName": "System",
            "actionStatus": {
                "isSuppressed": false
            },
            "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls."
        },
        "context": {
            "DetectionSummary": "An abnormal rise in failed request rate",
            "FormattedOccurenceTime": "2019-10-30T17:50:00Z",
            "DetectedFailureRate": "50.0% (200/400 requests)",
            "NormalFailureRate": "0.0% (over the last 30 minutes)",
            "FailureRateChart": [
                [
                    "2019-10-30T05:20:00Z",
                    0
                ],
                [
                    "2019-10-30T05:40:00Z",
                    100
                ],
                [
                    "2019-10-30T06:00:00Z",
                    0
                ],
                [
                    "2019-10-30T06:20:00Z",
                    0
                ],
                [
                    "2019-10-30T06:40:00Z",
                    100
                ],
                [
                    "2019-10-30T07:00:00Z",
                    0
                ],
                [
                    "2019-10-30T07:20:00Z",
                    0
                ],
                [
                    "2019-10-30T07:40:00Z",
                    100
                ],
                [
                    "2019-10-30T08:00:00Z",
                    0
                ],
                [
                    "2019-10-30T08:20:00Z",
                    0
                ],
                [
                    "2019-10-30T08:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:00:00Z",
                    0
                ],
                [
                    "2019-10-30T17:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:00:00Z",
                    0
                ],
                [
                    "2019-10-30T09:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:40:00Z",
                    100
                ],
                [
                    "2019-10-30T10:00:00Z",
                    0
                ],
                [
                    "2019-10-30T10:20:00Z",
                    0
                ],
                [
                    "2019-10-30T10:40:00Z",
                    100
                ],
                [
                    "2019-10-30T11:00:00Z",
                    0
                ],
                [
                    "2019-10-30T11:20:00Z",
                    0
                ],
                [
                    "2019-10-30T11:40:00Z",
                    100
                ],
                [
                    "2019-10-30T12:00:00Z",
                    0
                ],
                [
                    "2019-10-30T12:20:00Z",
                    0
                ],
                [
                    "2019-10-30T12:40:00Z",
                    100
                ],
                [
                    "2019-10-30T13:00:00Z",
                    0
                ],
                [
                    "2019-10-30T13:20:00Z",
                    0
                ],
                [
                    "2019-10-30T13:40:00Z",
                    100
                ],
                [
                    "2019-10-30T14:00:00Z",
                    0
                ],
                [
                    "2019-10-30T14:20:00Z",
                    0
                ],
                [
                    "2019-10-30T14:40:00Z",
                    100
                ],
                [
                    "2019-10-30T15:00:00Z",
                    0
                ],
                [
                    "2019-10-30T15:20:00Z",
                    0
                ],
                [
                    "2019-10-30T15:40:00Z",
                    100
                ],
                [
                    "2019-10-30T16:00:00Z",
                    0
                ],
                [
                    "2019-10-30T16:20:00Z",
                    0
                ],
                [
                    "2019-10-30T16:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:30:00Z",
                    50
                ]
            ],
            "ArmSystemEventsRequest": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourceGroups/test-group/providers/microsoft.insights/components/test-rule/query?query=%0d%0a++++++++++++++++systemEvents%0d%0a++++++++++++++++%7c+where+timestamp+%3e%3d+datetime(%272019-10-30T17%3a20%3a00.0000000Z%27)+%0d%0a++++++++++++++++%7c+where+itemType+%3d%3d+%27systemEvent%27+and+name+%3d%3d+%27ProactiveDetectionInsight%27+%0d%0a++++++++++++++++%7c+where+dimensions.InsightType+in+(%275%27%2c+%277%27)+%0d%0a++++++++++++++++%7c+where+dimensions.InsightDocumentId+%3d%3d+%27718fb0c3-425b-4185-be33-4311dfb4deeb%27+%0d%0a++++++++++++++++%7c+project+dimensions.InsightOneClassTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightExceptionCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightDependencyCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightRequestCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightTraceCorrelationTable%0d%0a++++++++++++&api-version=2018-04-20",
            "LinksTable": [
                {
                    "Link": "<a href=\"https://portal.azure.com/#blade/AppInsightsExtension/ProactiveDetectionFeedBlade/ComponentId/{\"SubscriptionId\":\"4f9b81be-fa32-4f96-aeb3-fc5c3f678df9\",\"ResourceGroup\":\"test-group\",\"Name\":\"test-rule\"}/SelectedItemGroup/718fb0c3-425b-4185-be33-4311dfb4deeb/SelectedItemTime/2019-10-30T17:50:00Z/InsightType/5\" target=\"_blank\">View full details in Application Insights</a>"
                }
            ],
            "SmartDetectorId": "FailureAnomaliesDetector",
            "SmartDetectorName": "Failure Anomalies",
            "AnalysisTimestamp": "2019-10-30T17:52:32.5802978Z"
        },
        "egressConfig": {
            "displayConfig": [
                {
                    "rootJsonNode": null,
                    "sectionName": null,
                    "displayControls": [
                        {
                            "property": "DetectionSummary",
                            "displayName": "What was detected?",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "FormattedOccurenceTime",
                            "displayName": "When did this occur?",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "DetectedFailureRate",
                            "displayName": "Detected failure rate",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "NormalFailureRate",
                            "displayName": "Normal failure rate",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "chartType": "Line",
                            "xAxisType": "Date",
                            "yAxisType": "Percentage",
                            "xAxisName": "",
                            "yAxisName": "",
                            "property": "FailureRateChart",
                            "displayName": "Failure rate over last 12 hours",
                            "type": "Chart",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "defaultLoad": true,
                            "displayConfig": [
                                {
                                    "rootJsonNode": null,
                                    "sectionName": null,
                                    "displayControls": [
                                        {
                                            "showHeader": false,
                                            "columns": [
                                                {
                                                    "property": "Name",
                                                    "displayName": "Name"
                                                },
                                                {
                                                    "property": "Value",
                                                    "displayName": "Value"
                                                }
                                            ],
                                            "property": "tables[0].rows[0][0]",
                                            "displayName": "All of the failed requests had these characteristics:",
                                            "type": "Table",
                                            "isOptional": false,
                                            "isPropertySerialized": true
                                        }
                                    ]
                                }
                            ],
                            "property": "ArmSystemEventsRequest",
                            "displayName": "",
                            "type": "ARMRequest",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "showHeader": false,
                            "columns": [
                                {
                                    "property": "Link",
                                    "displayName": "Link"
                                }
                            ],
                            "property": "LinksTable",
                            "displayName": "Links",
                            "type": "Table",
                            "isOptional": false,
                            "isPropertySerialized": false
                        }
                    ]
                }
            ]
        }
    },
    "id": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule/providers/Microsoft.AlertsManagement/alerts/7daf8739-ca8a-4562-b69a-ff28db4ba0a5",
    "type": "Microsoft.AlertsManagement/alerts",
    "name": "Failure Anomalies - test-rule"
}
```

## <a name="triage-and-diagnose-an-alert"></a>Prioritering och diagnostisera en avisering

En varning anger att en onormal ökning av frekvensen för misslyckade begär Anden upptäcktes. Det är troligt att det finns problem med din app eller dess miljö.

Om du vill undersöka ytterligare klickar du på Visa fullständiga uppgifter i Application Insights. länkarna på den här sidan kommer att ta dig direkt till en [Sök sida](./diagnostic-search.md) som filtrerats efter relevanta begär Anden, undantag, beroenden eller spårningar. 

Du kan också öppna [Azure Portal](https://portal.azure.com), navigera till Application Insights resurs för din app och öppna sidan med problem.

Om du klickar på diagnostisera fel får du mer information och kan lösa problemet.

:::image type="content" source="./media/proactive-failure-diagnostics/051.png" alt-text="Diagnostisk sökning." lightbox="./media/proactive-failure-diagnostics/051.png#lightbox":::

I procent av begär Anden och antalet användare som påverkas kan du bestämma hur brådskande problemet är. I exemplet ovan jämförs fel frekvensen på 78,5% med en normal taxa på 2,2%, vilket innebär att något är skadat. Å andra sidan påverkades bara 46 användare. Om det var din app kan du utvärdera hur allvarligt det är.

I många fall kommer du att kunna diagnostisera problemet snabbt från namn, undantag, beroende fel och spårnings data som tillhandahålls.

I det här exemplet uppstod ett undantag från SQL Database på grund av att gränsen för begäran har uppnåtts.

:::image type="content" source="./media/proactive-failure-diagnostics/052.png" alt-text="Information om misslyckade förfrågningar." lightbox="./media/proactive-failure-diagnostics/052.png":::

## <a name="review-recent-alerts"></a>Granska nya aviseringar

Klicka på **aviseringar** på sidan Application Insights resurs för att få de senaste utlösta aviseringarna:

:::image type="content" source="./media/proactive-failure-diagnostics/070.png" alt-text="Aviserings Sammanfattning." lightbox="./media/proactive-failure-diagnostics/070.png":::

## <a name="whats-the-difference-"></a>Vad är skillnaden...
Smart identifiering av fel avvikelser kompletterar andra liknande, men distinkta funktioner i Application Insights.

* [mått varningar](../platform/alerts-log.md) anges av dig och kan övervaka en mängd olika mått, till exempel processor användning, begär ande frekvens, sid inläsnings tider och så vidare. Du kan använda dem för att varna dig, till exempel om du behöver lägga till fler resurser. Som kontrast täcker Smart identifiering av fel avvikelser ett litet antal kritiska mått (för närvarande endast misslyckade begär Anden), utformade för att meddela dig i nära real tid när webbappens misslyckade begär ande frekvens ökar jämfört med webbappens normala beteende. Till skillnad från mått aviseringar ställer Smart identifiering automatiskt in och uppdaterar tröskelvärden vid svars ändringar i beteendet. Smart identifiering startar också det diagnostiska arbetet åt dig, vilket sparar tid för att lösa problem.

* [Smart identifiering av prestanda avvikelser](proactive-performance-diagnostics.md) använder också Machine Intelligence för att identifiera ovanliga mönster i dina mått och ingen konfiguration krävs av dig. Men till skillnad från Smart identifiering av fel avvikelser är syftet med Smart identifiering av prestanda avvikelser att hitta segment i användnings grenen som kan vara dåligt Betjänad, till exempel genom vissa sidor i en speciell typ av webbläsare. Analysen utförs dagligen och om ett resultat hittas är det troligt vis mycket mindre brådskande än en avisering. I motsats sker analysen av fel avvikelser kontinuerligt på inkommande program data och du får ett meddelande inom några minuter om Server fel hastigheterna är större än förväntat.

## <a name="if-you-receive-a-smart-detection-alert"></a>Om du får en avisering om Smart identifiering
*Varför har jag fått den här aviseringen?*

* Vi har identifierat en onormal ökning av antalet misslyckade begär Anden jämfört med den normala bas linjen för föregående period. Efter analysen av fel och associerade program data, tror vi att det finns ett problem som du bör titta på.

*Betyder meddelandet att jag definitivt har ett problem?*

* Vi försöker Varna vid program avbrott eller försämring, men bara du kan helt förstå semantiken och påverkan på appen eller användarna.

*Så tittar du på mina program data?*

* Nej. Tjänsten är helt automatisk. Endast du får meddelanden. Dina data är [privata](./data-retention-privacy.md).

*Måste jag prenumerera på den här aviseringen?*

* Nej. Alla program som skickar begär ande data har aviserings regeln för smart identifiering.

*Kan jag avbryta prenumerationen eller hämta meddelanden som skickas till mina kollegor i stället?*

* Ja, i varnings regler klickar du på regeln för smart identifiering för att konfigurera den. Du kan inaktivera aviseringen eller ändra mottagare för aviseringen.

*Jag förlorade e-postmeddelandet. Var hittar jag meddelanden i portalen?*

* I aktivitets loggarna. I Azure öppnar du Application Insights resursen för din app och väljer sedan aktivitets loggar.

*Några av aviseringarna är om kända problem och jag vill inte ta emot dem.*

* Du kan använda [varnings åtgärds funktionen regler](../platform/alerts-action-rules.md) för att ignorera.

## <a name="next-steps"></a>Nästa steg
De här diagnostikverktygen hjälper dig att inspektera data från din app:

* [Metric Explorer](../platform/metrics-charts.md)
* [Sök Utforskaren](./diagnostic-search.md)
* [Analys-kraftfullt frågespråk](../log-query/log-analytics-tutorial.md)

Smart identifiering är automatiskt. Men kanske vill du konfigurera ytterligare aviseringar?

* [Manuellt konfigurerade mått varningar](../platform/alerts-log.md)
* [Webbtester för tillgänglighet](./monitor-web-app-availability.md)