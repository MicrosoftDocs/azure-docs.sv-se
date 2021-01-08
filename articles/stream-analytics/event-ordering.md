---
title: Konfigurera principer för händelse ordning för Azure Stream Analytics
description: Den här artikeln beskriver hur du konfigurerar inställningar för till och med att beställa i Stream Analytics
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: f7ec6f32b48a93a29210311c7ba6747eb2e2d066
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014303"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Konfigurera principer för händelse ordning för Azure Stream Analytics

I den här artikeln beskrivs hur du konfigurerar och använder sent mottagna och inaktuella händelse principer i Azure Stream Analytics. Dessa principer används endast när du använder [timestamp by](/stream-analytics-query/timestamp-by-azure-stream-analytics) -satsen i frågan och de tillämpas bara för moln ingångs källor.

## <a name="event-time-and-arrival-time"></a>Tid och tid för händelsen

Ditt Stream Analytics-jobb kan bearbeta händelser baserat på *händelse tid* eller *tid för införsel*. **Händelse/tillämpnings tid** är tidsstämpeln som finns i händelse nytto lasten (när händelsen genererades). **Införsel tiden** är tidsstämpeln när händelsen togs emot i Indatakällan (Event Hubs/IoT Hub/blob-lagring). 

Som standard bearbetar Stream Analytics händelser efter *ankomst tid*, men du kan välja att bearbeta händelser efter *tidpunkt* genom att använda [timestamp by](/stream-analytics-query/timestamp-by-azure-stream-analytics) -satsen i frågan. Sena ingångs-och utgångs principer gäller endast om du bearbetar händelser efter händelse tid. Överväg krav på svarstid och korrekthet för ditt scenario när du konfigurerar de här inställningarna. 

## <a name="what-is-late-arrival-policy"></a>Vad är principen för händelser som kommer sent?

Ibland tar händelser emot sent på grund av olika orsaker. Till exempel kommer en händelse som tar 40 sekunder sent att ha händelse tid = 00:10:00 och ankomst tid = 00:10:40. Om du ställer in principen för sent införsel på 15 sekunder, kommer alla händelser som tar längre tid än 15 sekunder att släppas (inte bearbetas av Stream Analytics) eller att händelse tiden justeras. I exemplet ovan, eftersom händelsen anlänt 40 sekunder (mer än princip uppsättning), justeras händelse tiden till det högsta värdet för sent införsel princip 00:10:25 (ankomst tid – sent införsel princip värde). Standard principen för sent införsel är 5 sekunder.

## <a name="what-is-out-of-order-policy"></a>Vad är en felaktig princip? 

Händelsen kan också komma att tas ur ordning. När händelse tiden har justerats baserat på principen för sen införsel, kan du också välja att automatiskt släppa eller justera händelser som är utanför ordningen. Om du ställer in den här principen på 8 sekunder sorteras alla händelser som anländer till en annan ordning men inom det 8-andra fönstret sorteras om efter händelse tid. Händelser som kommer senare kommer att antingen släppas eller justeras till det maximala värdet för principens värde. Standard principen är 0 sekunder. 

## <a name="adjust-or-drop-events"></a>Justera eller ta bort händelser

Om händelserna inkommer sent eller utanför ordningen baserat på de principer som du har konfigurerat kan du antingen ta bort sådana händelser (som inte bearbetas av Stream Analytics) eller ha händelse tiden justerad.

Låt oss se ett exempel på dessa principer i praktiken.
<br> **Sen införsel princip:** 15 sekunder
<br> **Princip som inte är i ordning:** 8 sekunder

| Händelse nr. | Händelsetid | Införsel tid | System.Timestamp | Förklaring |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | Händelsen har anlänt sent och utanför tolerans nivån. Händelse tiden justeras så att den maximala sena införsel toleransen ändras.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | Händelsen anlände sent men inom tolerans nivån. Händelse tiden får inte justeras.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | Händelsen har anlänt i tid. Ingen justering krävs.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | Händelsen har anlänt i rätt ordning men inom toleransen på 8 sekunder. Händelse tiden justeras därför inte. I analys syfte kommer den här händelsen att anses som föregående händelse nummer 4.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Händelsen har anlänt utanför ordningen och utanför toleransen på 8 sekunder. Händelse tiden justeras så att den maximala toleransen ligger utanför ordern. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Kan de här inställningarna fördröja utdata från mitt jobb? 

Ja. Som standard är inaktive rad princip inställd på noll (00 minuter och 00 sekunder). Om du ändrar standardvärdet fördröjs ditt jobbs första utdata av detta värde (eller större). 

Om någon av partitionerna i dina indata inte tar emot händelser, bör du förvänta dig att dina utdata försenas av värdet för sent införsel princip. Läs avsnittet InputPartition-fel nedan om du vill veta varför. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>Jag ser LateInputEvents-meddelanden i min aktivitets logg

Dessa meddelanden visas för att informera dig om att händelser har anlänt och antingen släpps eller justeras enligt konfigurationen. Du kan ignorera dessa meddelanden om du har konfigurerat principen för sen införsel på lämpligt sätt. 

Exempel på detta meddelande är: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>Jag ser InputPartitionNotProgressing i min aktivitets logg

Din indatakälla (Händelsehubben/IoT Hub) har troligen flera partitioner. Azure Stream Analytics producerar utdata för tidsstämpeln T1 endast efter att alla partitioner som kombineras är minst vid tiden T1. Anta till exempel att frågan läser från en händelsehubben partition som har två partitioner. En av partitionerna P1, innehåller händelser tills tiden T1. Den andra partitionen, P2, har händelser fram till tid T1 + x. Utdata skapas sedan tills tiden T1. Men om det finns en explicit partition by PartitionId-satsen, kommer båda partitionerna att bearbetas oberoende av varandra. 

När flera partitioner från samma inkommande data ström kombineras, är den sena införsel toleransen den maximala tid som varje partition väntar på nya data. Om det finns en partition i händelsehubben, eller om IoT Hub inte tar emot indata, så bearbetas inte tids linjen för den partitionen förrän den når tolerans tröskeln för sent införsel. Detta försenar resultatet med tröskelvärdet för sent införsel tolerans. I sådana fall kan följande meddelande visas:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Det här meddelandet för att meddela att minst en partition i dina indata är tom och kommer att fördröja utdata med tröskelvärdet för sent införsel. För att undvika detta rekommenderar vi att du antingen:  
1. Se till att alla partitioner för Event Hub/IoT Hub ta emot ininformation. 
2. Använd partition by PartitionID-sats i frågan. 

## <a name="why-do-i-see-a-delay-of-5-seconds-even-when-my-late-arrival-policy-is-set-to-0"></a>Varför ser jag en fördröjning på 5 sekunder även om min sent införsel princip är inställd på 0?
Detta inträffar när det finns en inpartition som aldrig har tagit emot några inaktuella ingångar. Du kan verifiera ingångs måtten efter partition för att validera det här beteendet. 

När en partition inte har några data för mer än det konfigurerade tröskelvärdet för sent införsel, förflyttar sig Stream Analytics-programmet för program tids stämpling enligt beskrivningen i avsnittet om sortering av evenemang. Detta kräver uppskattad införsel tid. Om partitionen aldrig hade några data beräknar Stream Analytics ankomst tiden som *lokal tid – 5 sekunder*. På grund av dessa partitioner som aldrig hade några data kan det Visa en fördröjning på vattenstämpeln på 5 sekunder.  

## <a name="next-steps"></a>Nästa steg
* [Överväganden för tidshantering](stream-analytics-time-handling.md)
* [Mått som är tillgängliga i Stream Analytics](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics)