---
title: Modellerings relationer i Azure Table Storage design | Microsoft Docs
description: Förstå modell processen när du utformar din lösning för Azure Table Storage. Läs om en-till-många, en-till-en-och arvs relationer.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 3023b478ef7a4aaf6d9239e997bdf63282b56210
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271200"
---
# <a name="modeling-relationships"></a>Modellera relationer
I den här artikeln beskrivs modell processen som hjälper dig att utforma dina lagrings lösningar för Azure-tabeller.

Att skapa domän modeller är ett viktigt steg i utformningen av komplexa system. Normalt använder du modellerings processen för att identifiera entiteter och relationerna mellan dem som ett sätt att förstå företags domänen och informera utformningen av ditt system. Det här avsnittet fokuserar på hur du kan översätta några av de vanliga Relations typerna i domän modeller till design för Table service. Processen för att mappa från en logisk data modell till en fysisk NoSQL-baserad data modell skiljer sig från den som användes när du skapar en Relations databas. Design av Relations databaser förutsätter vanligt vis en data normaliserings process som är optimerad för att minimera redundans – och en deklarativ fråge funktion som sammanfattar hur databasen fungerar.  

## <a name="one-to-many-relationships"></a>En-till-många-relationer
En-till-många-relationer mellan företags domän objekt inträffar ofta: till exempel är en avdelning många anställda. Det finns flera sätt att implementera en-till-många-relationer i Table service var och en med-och nack delar som kan vara relevanta för det specifika scenariot.  

Överväg exemplet på ett stort företag med flera nationella organisationer med tusentals avdelningar och personal enheter där varje avdelning har många anställda och varje medarbetare som är kopplad till en speciell avdelning. En metod är att lagra separata avdelnings-och anställdas enheter, till exempel följande:  


![Lagra separata avdelnings-och personal enheter](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

Det här exemplet visar en implicit en-till-många-relation mellan typerna baserat på **PartitionKey** -värdet. Varje avdelning kan ha många anställda.  

I det här exemplet visas även en avdelnings enhet och dess relaterade anställdas entiteter i samma partition. Du kan välja att använda olika partitioner, tabeller eller till och med lagrings konton för de olika enhets typerna.  

En annan metod är att avnormalisera dina data och lagra endast anställdas enheter med avnormaliserade avdelnings data som visas i följande exempel. I det här scenariot kanske det här avnormaliserade tillvägagångs sättet inte är det bästa om du har ett krav för att kunna ändra informationen om en avdelnings hanterare, eftersom du måste uppdatera varje anställd på avdelningen för att göra detta.  

![Anställd entitet](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

Mer information finns i avsnittet om [avnormaliserings mönster](table-storage-design-patterns.md#denormalization-pattern) senare i den här hand boken.  

I följande tabell sammanfattas de olika metoderna och nack delar med var och en av de metoder som beskrivs ovan för att lagra personal-och avdelnings enheter som har en en-till-många-relation. Du bör också fundera över hur ofta du förväntar dig att utföra olika åtgärder: det kan vara acceptabelt att ha en design som innehåller en dyr åtgärd om åtgärden bara sker sällan.  

<table>
<tr>
<th>Metod</th>
<th>Fördelar</th>
<th>Nackdelar</th>
</tr>
<tr>
<td>Separata entitetstyper, samma partition, samma tabell</td>
<td>
<ul>
<li>Du kan uppdatera en avdelnings enhet med en enda åtgärd.</li>
<li>Du kan använda en enhets grupp transaktion * (EGT) för att upprätthålla konsekvens om du har ett krav för att ändra en avdelnings enhet när du uppdaterar/infogar/tar bort en anställds entitet. Om du till exempel underhåller ett antal anställda för varje avdelning.</li>
</ul>
</td>
<td>
<ul>
<li>Du kan behöva hämta både en anställd och en avdelnings enhet för vissa klient aktiviteter.</li>
<li>Lagrings åtgärder sker i samma partition. På hög transaktions volymer kan detta resultera i ett hotspot-område.</li>
<li>Du kan inte flytta en medarbetare till en ny avdelning med hjälp av en EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Separata entitetstyper, olika partitioner eller tabeller eller lagrings konton</td>
<td>
<ul>
<li>Du kan uppdatera en avdelnings enhet eller en anställds entitet med en enda åtgärd.</li>
<li>På hög transaktions volymer kan detta hjälpa till att sprida belastningen över flera partitioner.</li>
</ul>
</td>
<td>
<ul>
<li>Du kan behöva hämta både en anställd och en avdelnings enhet för vissa klient aktiviteter.</li>
<li>Du kan inte använda EGTs för att upprätthålla konsekvens när du uppdaterar/infogar/tar bort en medarbetare och uppdaterar en avdelning. Du kan till exempel uppdatera ett antal anställda i en avdelnings enhet.</li>
<li>Du kan inte flytta en medarbetare till en ny avdelning med hjälp av en EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Avnormalisera till en enhets typ</td>
<td>
<ul>
<li>Du kan hämta all information som du behöver med en enskild begäran.</li>
</ul>
</td>
<td>
<ul>
<li>Det kan vara dyrt att upprätthålla konsekvens om du behöver uppdatera avdelnings information (Detta kräver att du uppdaterar alla anställda på en avdelning).</li>
</ul>
</td>
</tr>
</table>

* Mer information finns i [enhets grupp transaktioner](table-storage-design.md#entity-group-transactions)  


Hur du väljer mellan dessa alternativ och vilka av-och nack delar som är mest signifikanta beror på dina specifika program scenarier. Till exempel hur ofta ändrar du avdelnings enheter. behöver alla dina anställda-frågor ytterligare avdelnings information. hur nära är du på skalbarhets gränserna för dina partitioner eller ditt lagrings konto?  

## <a name="one-to-one-relationships"></a>En-till-en-relationer
Domän modeller kan innehålla en-till-en-relationer mellan entiteter. Om du behöver implementera en en-till-en-relation i Table service måste du också välja hur du länkar de två relaterade entiteterna när du behöver hämta dem. Den här länken kan vara antingen implicit, baserat på en konvention i nyckel värden eller explicit genom att en länk lagras i form av **PartitionKey** -och **RowKey** -värden i varje entitet till dess relaterade entitet. En beskrivning av huruvida du bör lagra relaterade entiteter i samma partition finns i avsnittet [en-till-många-relationer](#one-to-many-relationships).  

Det finns också implementerings överväganden som kan leda till att du implementerar en-till-en-relationer i Table service:  

* Hantering av stora entiteter (mer information finns i [mönster för stora entiteter](table-storage-design-patterns.md#large-entities-pattern)).  
* Implementera åtkomst kontroller (mer information finns i kontrol lera åtkomst med signaturer för delad åtkomst).  

## <a name="join-in-the-client"></a>Anslut till klienten
Även om det finns olika sätt att modellera relationer i Table service bör du inte glömma att de två huvudsakliga orsakerna till att använda Table service är skalbarhet och prestanda. Om du hittar flera relationer som äventyrar prestanda och skalbarhet för din lösning, bör du fråga dig själv om det är nödvändigt att bygga alla data relationer i tabell designen. Du kanske kan förenkla designen och förbättra skalbarheten och prestandan för din lösning om du låter klient programmet utföra alla nödvändiga kopplingar.  

Om du till exempel har små tabeller som innehåller data som inte ändras ofta kan du hämta dessa data en gång och cachelagra den på klienten. Detta kan undvika upprepade turer för att hämta samma data. I exemplen som vi har tittat på i den här hand boken är en uppsättning avdelningar i en liten organisation troligt vis liten och ändra sällan så att det blir en bra kandidat för data som klient programmet kan ladda ned en gång och cachelagra som att slå upp data.  

## <a name="inheritance-relationships"></a>Arvs relationer
Om klient programmet använder en uppsättning klasser som utgör en del av en arvs relation som representerar affär senheter, kan du enkelt spara dessa entiteter i Table service. Du kan till exempel ha följande uppsättning klasser definierade i klient programmet där **personen** är en abstrakt klass.

![Abstrakt person klass](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

Du kan spara instanser av de två konkreta klasserna i Table service med hjälp av en enda person tabell med hjälp av entiteter i som ser ut så här:  

![Person tabell](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Mer information om hur du arbetar med flera olika entitetstyper i samma tabell i klient koden finns i avsnittet arbeta med heterogena entitetstyper senare i den här hand boken. Detta ger exempel på hur du kan identifiera enhets typen i klient koden.  


## <a name="next-steps"></a>Nästa steg

- [Mönster för tabelldesign](table-storage-design-patterns.md)
- [Utforma för frågor](table-storage-design-for-query.md)
- [Kryptera tabell data](table-storage-design-encrypt-data.md)
- [Utforma för dataändring](table-storage-design-for-modification.md)
