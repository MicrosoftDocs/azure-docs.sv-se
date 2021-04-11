---
title: Azure Data Catalog utveckla koncept
description: Introduktion till viktiga begrepp i Azure Data Catalog konceptuell modell, som visas i katalogen REST API.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: ddeab4838feb07d1101993cab4ebc86581b4d8b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674707"
---
# <a name="azure-data-catalog-developer-concepts"></a>Azure Data Catalog utveckla koncept

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Microsoft **Azure Data Catalog** är en helt hanterad moln tjänst som tillhandahåller funktioner för identifiering av data källor och gemensamt skapade metadata för data källan. Utvecklare kan använda tjänsten via dess REST-API: er. Att förstå begreppen som implementeras i tjänsten är viktigt för att utvecklare ska kunna integrera med **Azure Data Catalog**.

## <a name="key-concepts"></a>Viktiga begrepp 
Den **Azure Data Catalog** konceptuella modellen baseras på fyra viktiga begrepp: **katalog**, **användare**, **till gångar** och **anteckningar**.

![Bild av Azure Data Catalog konceptuella modell](./media/data-catalog-developer-concepts/concept2.png)

### <a name="catalog"></a>Katalog
En **katalog** är behållaren på den översta nivån för alla metadata som en organisation lagrar. En **katalog** tillåts per Azure-konto. Kataloger är kopplade till en Azure-prenumeration, men det går bara att skapa en **katalog** för ett angivet Azure-konto, även om ett konto kan ha flera prenumerationer.

En katalog innehåller **användare** och **till gångar**.

### <a name="users"></a>Användare
Användare är säkerhets objekt som har behörighet att utföra åtgärder (söka i katalogen, lägga till, redigera eller ta bort objekt osv.) i katalogen.

Det finns flera olika roller som en användare kan ha. Information om roller finns i avsnittet roller och auktorisering.

Enskilda användare och säkerhets grupper kan läggas till.

Azure Data Catalog använder Azure Active Directory för identitets-och åtkomst hantering. Varje katalog användare måste vara medlem i Active Directory för kontot.

### <a name="assets"></a>Tillgångar
En **katalog** innehåller data till gångar. **Till gångar** är den enhets kornig het som hanteras av katalogen.

En till gångs granularitet varierar beroende på data källan. För SQL Server eller Oracle Database kan en till gång vara en tabell eller en vy. För SQL Server Analysis Services kan en till gång vara ett mått, en dimension eller en KPI (Key Performance Indicator). För SQL Server Reporting Services är en till gång en rapport.

En **till gång** är att lägga till eller ta bort från en katalog. Det är den resultat enhet som du kommer tillbaka från **sökningen**.

En **till gång** skapas från dess namn, plats, typ och anteckningar som beskriver den ytterligare.

### <a name="annotations"></a>Anteckningar
Anteckningar är objekt som representerar metadata om till gångar.

Exempel på kommentarer är beskrivning, taggar, schema, dokumentation osv. Se [avsnittet till gångs objekt modell](#asset-object-model) för en fullständig lista över till gångs typer och antecknings typer.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Gemensamt skapade-kommentarer och användar perspektiv (uppfattning om yttrandet)
En viktig aspekt av Azure Data Catalog är hur den stöder gemensamt skapade för metadata i systemet. I stället för en wiki-metod – där det bara finns ett yttrande och den senaste skrivaren vinner – Azure Data Catalogs modellen gör att flera åsikter kan leva sida vid sida i systemet.

Den här metoden återspeglar den verkliga världen för företags data där olika användare kan ha olika perspektiv på en specifik till gång:

* En databas administratör kan ge information om service nivå avtal eller fönstret tillgänglig bearbetning för Mass ETL-åtgärder
* En data ledning kan ge information om de affärs processer som till gången gäller, eller de klassificeringar som företaget har tillämpat på den
* En finansiell analytiker kan ge information om hur data används under rapporteringen av rapportens slut punkt

För att stödja det här exemplet kan varje användare – DBA, data ledning och analytiker – lägga till en beskrivning i en enskild tabell som har registrerats i katalogen. Alla beskrivningar finns kvar i systemet och i Azure Data Catalog portalen visas alla beskrivningar.

Det här mönstret används för de flesta objekt i objekt modellen, så objekt typer i JSON-nyttolasten är ofta matriser för egenskaper där du kan förväntar sig en singleton.

Till exempel är till gångs roten en matris med beskrivnings objekt. Mat ris egenskapen heter "beskrivningar". Ett beskrivnings objekt har en egenskaps beskrivning. Mönstret är att varje användare som har typen Beskrivning hämtar ett beskrivnings objekt som skapats för det värde som anges av användaren.

UX-modulen kan sedan välja hur kombinationen ska visas. Det finns tre olika mönster att visa.

* Det enklaste mönstret är "Visa alla". I det här mönstret visas alla objekt i en listvy. I Azure Data Catalog portalens UX används det här mönstret för beskrivning.
* Ett annat mönster är "merge". I det här mönstret slås alla värden från de olika användarna samman, och dubbletter tas bort. Exempel på det här mönstret i Azure Data Catalog portalens UX är Taggar och experters egenskaper.
* Ett tredje mönster är "sista skrivaren vinner". I det här mönstret visas bara det senaste värdet som anges i. friendlyName är ett exempel på det här mönstret.

## <a name="asset-object-model"></a>Till gångs objekt modell
Som vi introducerat i avsnittet viktiga begrepp innehåller **Azure Data Catalog** objekt modellen objekt, som kan vara till gångar eller kommentarer. Objekt har egenskaper som kan vara valfria eller obligatoriska. Vissa egenskaper gäller för alla objekt. Vissa egenskaper gäller för alla till gångar. Vissa egenskaper gäller endast för vissa till gångs typer.

### <a name="system-properties"></a>Systemegenskaper
<table><tr><td><b>Egenskapens namn</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr><tr><td>timestamp</td><td>DateTime</td><td>Den senaste gången objektet ändrades. Det här fältet genereras av servern när ett objekt infogas och varje gång ett objekt uppdateras. Värdet för den här egenskapen ignoreras vid inläsning av publicerings åtgärder.</td></tr><tr><td>ID</td><td>URI</td><td>Absolut URL för objektet (skrivskyddat). Det är den unika adresser bara URI: n för objektet.  Värdet för den här egenskapen ignoreras vid inläsning av publicerings åtgärder.</td></tr><tr><td>typ</td><td>Sträng</td><td>Typ av till gång (skrivskyddad).</td></tr><tr><td>etag</td><td>Sträng</td><td>En sträng som motsvarar den version av objektet som kan användas för optimistisk concurrency-kontroll när du utför åtgärder som uppdaterar objekt i katalogen. "*" kan användas för att matcha vilket värde som helst.</td></tr></table>

### <a name="common-properties"></a>Gemensamma egenskaper
Dessa egenskaper gäller för alla typer av rot till gångar och alla antecknings typer.

<table>
<tr><td><b>Egenskapens namn</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr>
<tr><td>fromSourceSystem</td><td>Boolesk</td><td>Anger om objektets data härleds från ett käll system (t. ex. SQL Server Database, Oracle Database) eller har skapats av en användare.</td></tr>
</table>

### <a name="common-root-properties"></a>Gemensamma rot egenskaper
<p>
Dessa egenskaper gäller för alla typer av rot till gångar.

<table><tr><td><b>Egenskapens namn</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr><tr><td>name</td><td>Sträng</td><td>Ett namn som härletts från data källans plats information</td></tr><tr><td>via</td><td>DataSourceLocation</td><td>Beskriver unikt data källan och är en av identifierarna för till gången. (Se avsnittet dubbla identiteter).  DSL-strukturen varierar beroende på protokoll och typ av källa.</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>Mer information om typen av till gång.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Beskriver den användare som senast registrerade den här till gången.  Innehåller både det unika ID: t för användaren (UPN) och visnings namn (lastName och firstName).</td></tr><tr><td>Hålla</td><td>Sträng</td><td>ID för behållar till gången för data källan. Den här egenskapen stöds inte för behållar typen.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Vanliga egenskaper för icke-singleton-anteckning
Dessa egenskaper gäller för alla typer av icke-singleton-anteckningar (kommentarer som kan vara flera per till gång).

<table>
<tr><td><b>Egenskapens namn</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr>
<tr><td>key</td><td>Sträng</td><td>En användardefinierad nyckel som unikt identifierar anteckningen i den aktuella samlingen. Nyckel längden får inte överskrida 256 tecken.</td></tr>
</table>

### <a name="root-asset-types"></a>Rot till gångs typer
Rot till gångs typer är de typer som representerar de olika typerna av data till gångar som kan registreras i katalogen. Det finns en vy för varje rottyp, som beskriver till gångar och anteckningar som ingår i vyn. Vyns namn ska användas i motsvarande {view_name} URL-segment när du publicerar en till gång med REST API.

<table><tr><td><b>Till gångs typ (visnings namn)</b></td><td><b>Ytterligare egenskaper</b></td><td><b>Datatyp</b></td><td><b>Tillåtna anteckningar</b></td><td><b>Kommentarer</b></td></tr><tr><td>Tabell ("tabeller")</td><td></td><td></td><td>Beskrivning<p>FriendlyName<p>Tagg<p>Schema<p>ColumnDescription<p>ColumnTag<p> Expert<p>Förhandsgranskning<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>Dokumentation<p></td><td>En tabell representerar alla tabell data.  Exempel: SQL-tabell, SQL-vy, Analysis Services tabell tabell, Analysis Services flerdimensionell dimension, Oracle-tabell osv.   </td></tr><tr><td>Mått ("mått")</td><td></td><td></td><td>Beskrivning<p>FriendlyName<p>Tagg<p>Expert<p>AccessInstruction<p>Dokumentation<p></td><td>Den här typen representerar ett Analysis Services mått.</td></tr><tr><td></td><td>åtgärder</td><td>Kolumn</td><td></td><td>Metadata som beskriver måttet</td></tr><tr><td></td><td>isCalculated </td><td>Boolesk</td><td></td><td>Anger om måttet beräknas eller inte.</td></tr><tr><td></td><td>measureGroup</td><td>Sträng</td><td></td><td>Fysisk container för mått</td></tr><td>KPI ("KPI: er")</td><td></td><td></td><td>Beskrivning<p>FriendlyName<p>Tagg<p>Expert<p>AccessInstruction<p>Dokumentation</td><td></td></tr><tr><td></td><td>measureGroup</td><td>Sträng</td><td></td><td>Fysisk container för mått</td></tr><tr><td></td><td>goalExpression</td><td>Sträng</td><td></td><td>Ett numeriskt MDX-uttryck eller en beräkning som returnerar KPI-värdets målvärde.</td></tr><tr><td></td><td>valueExpression</td><td>Sträng</td><td></td><td>Ett numeriskt MDX-uttryck som returnerar KPI-indikatorns faktiska värde.</td></tr><tr><td></td><td>statusExpression</td><td>Sträng</td><td></td><td>Ett MDX-uttryck som representerar status för KPI vid en angiven tidpunkt.</td></tr><tr><td></td><td>trendExpression</td><td>Sträng</td><td></td><td>Ett MDX-uttryck som utvärderar KPI-värdet över tid. Trenden kan vara ett tidsbaserat kriterium som är användbart i en speciell affärs kontext.</td>
<tr><td>Rapport ("rapporter")</td><td></td><td></td><td>Beskrivning<p>FriendlyName<p>Tagg<p>Expert<p>AccessInstruction<p>Dokumentation<p></td><td>Den här typen representerar en SQL Server Reporting Services rapport </td></tr><tr><td></td><td>assetCreatedDate</td><td>Sträng</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>Sträng</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>Sträng</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>Sträng</td><td></td><td></td></tr><tr><td>Container ("behållare")</td><td></td><td></td><td>Beskrivning<p>FriendlyName<p>Tagg<p>Expert<p>AccessInstruction<p>Dokumentation<p></td><td>Den här typen representerar en behållare för andra till gångar, till exempel en SQL-databas, en Azure-blobs-behållare eller en Analysis Services modell.</td></tr></table>

### <a name="annotation-types"></a>Antecknings typer
Antecknings typer representerar typer av metadata som kan tilldelas andra typer i katalogen.

<table>
<tr><td><b>Antecknings typ (namn på kapslad vy)</b></td><td><b>Ytterligare egenskaper</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr>

<tr><td>Beskrivning ("beskrivningar")</td><td></td><td></td><td>Den här egenskapen innehåller en beskrivning av en till gång. Varje användare av systemet kan lägga till en egen beskrivning.  Endast den användaren kan redigera Description-objektet.  (Administratörer och till gångs ägare kan ta bort Description-objektet men inte redigera det). Systemet bibehåller användarnas beskrivningar separat.  Det finns därför en matris med beskrivningar för varje till gång (en för varje användare som har bidragit med sina kunskaper om till gången, förutom en som innehåller information som härletts från data källan).</td></tr>
<tr><td></td><td>beskrivning</td><td>sträng</td><td>En kort beskrivning (2-3 rader) av till gången</td></tr>

<tr><td>Tagg ("Taggar")</td><td></td><td></td><td>Den här egenskapen definierar en tagg för en till gång. Varje användare av systemet kan lägga till flera taggar för en till gång.  Endast den användare som skapade tagga objekt kan redigera dem.  (Administratörer och till gångs ägare kan ta bort taggnamnet men inte redigera det). Systemet bibehåller användarnas Taggar separat.  Det finns därför en matris med att tagga objekt på varje till gång.</td></tr>
<tr><td></td><td>tagg</td><td>sträng</td><td>En tagg som beskriver till gången.</td></tr>

<tr><td>FriendlyName ("friendlyName")</td><td></td><td></td><td>Den här egenskapen innehåller ett eget namn för en till gång. FriendlyName är en singleton-anteckning – det går bara att lägga till ett FriendlyName i en till gång.  Endast användaren som skapade FriendlyName-objektet kan redigera det. (Administratörer och till gångs ägare kan ta bort FriendlyName-objektet men inte redigera det). Systemet underhåller användarnas egna namn separat.</td></tr>
<tr><td></td><td>friendlyName</td><td>sträng</td><td>Ett eget namn på till gången.</td></tr>

<tr><td>Schema ("schema")</td><td></td><td></td><td>Schemat beskriver data strukturen.  Den listar attributet (kolumn-, attribut-, fält-osv)-namn, typer och andra metadata.  Den här informationen är allt härledd från data källan.  Schemat är en singleton-anteckning – det går bara att lägga till ett schema för en till gång.</td></tr>
<tr><td></td><td>kolumner</td><td>Kolumn []</td><td>En matris med kolumn objekt. De beskriver kolumnen med information som härletts från data källan.</td></tr>

<tr><td>ColumnDescription ("columnDescriptions")</td><td></td><td></td><td>Den här egenskapen innehåller en beskrivning av en kolumn.  Varje användare av systemet kan lägga till egna beskrivningar för flera kolumner (högst ett per kolumn). Endast den användare som skapade ColumnDescription-objekt kan redigera dem.  (Administratörer och till gångs ägare kan ta bort ColumnDescription-objektet men inte redigera det). Systemet underhåller dessa användares kolumn beskrivningar separat.  Därför finns det en matris med ColumnDescription-objekt på varje till gång (en per kolumn för varje användare som har bidragit till deras kännedom om kolumnen, förutom en som innehåller information som härletts från data källan).  ColumnDescription binds löst till schemat så att det kan bli osynkroniserade. ColumnDescription kan beskriva en kolumn som inte längre finns i schemat.  Det är upp till skrivaren att behålla beskrivningen och schemat synkroniserat.  Data källan kan också ha kolumn beskrivnings information och de är ytterligare ColumnDescription-objekt som skulle skapas när verktyget körs.</td></tr>
<tr><td></td><td>columnName</td><td>Sträng</td><td>Namnet på kolumnen som beskrivningen refererar till.</td></tr>
<tr><td></td><td>beskrivning</td><td>Sträng</td><td>en kort beskrivning (2-3 rader) i kolumnen.</td></tr>

<tr><td>ColumnTag ("columnTags")</td><td></td><td></td><td>Den här egenskapen innehåller en tagg för en kolumn. Varje användare av systemet kan lägga till flera taggar för en specifik kolumn och kan lägga till taggar för flera kolumner. Endast den användare som skapade ColumnTag-objekt kan redigera dem. (Administratörer och till gångs ägare kan ta bort ColumnTag-objektet men inte redigera det). Systemet underhåller dessa användares kolumn etiketter separat.  Därför finns det en matris med ColumnTag-objekt på varje till gång.  ColumnTag binds löst till schemat så att det kan bli osynkroniserade. ColumnTag kan beskriva en kolumn som inte längre finns i schemat.  Det är upp till skribenten att behålla kolumn tag gen och schemat synkroniserat.</td></tr>
<tr><td></td><td>columnName</td><td>Sträng</td><td>Namnet på kolumnen som den här taggen refererar till.</td></tr>
<tr><td></td><td>tagg</td><td>Sträng</td><td>En tagg som beskriver kolumnen.</td></tr>

<tr><td>Expert ("experter")</td><td></td><td></td><td>Den här egenskapen innehåller en användare som betraktas som en expert i data uppsättningen. Experternas åsikter (beskrivningar) bubblas längst upp i UXen när du registrerar beskrivningar. Varje användare kan ange sina egna experter. Endast den användaren kan redigera experternas objekt. (Administratörer och till gångs ägare kan ta bort expert objekt, men inte redigera det).</td></tr>
<tr><td></td><td>man</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>För hands version ("för hands versioner")</td><td></td><td></td><td>Förhands granskningen innehåller en ögonblicks bild av de 20 översta raderna med data för till gången. För hands versionen passar bara för vissa typer av till gångar (det är meningsfullt för tabellen men inte för mått).</td></tr>
<tr><td></td><td>preview</td><td>objekt []</td><td>Matris med objekt som representerar en kolumn.  Varje-objekt har en egenskaps mappning till en kolumn med ett värde för den kolumnen för raden.</td></tr>

<tr><td>AccessInstruction ("accessInstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>sträng</td><td>MIME-typen för innehållet.</td></tr>
<tr><td></td><td>innehåll</td><td>sträng</td><td>Instruktioner för hur du får åtkomst till den här data till gången. Innehållet kan vara en URL, en e-postadress eller en uppsättning instruktioner.</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>Antalet rader i data uppsättningen</td></tr>
<tr><td></td><td>ikoner</td><td>long</td><td>Data uppsättningens storlek i byte.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>sträng</td><td>När schemat senast ändrades</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>sträng</td><td>Den senaste gången data uppsättningen ändrades (data lades till, ändrades eller togs bort)</td></tr>

<tr><td>ColumnsDataProfile ("columnsDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>kolumner</td></td><td>ColumnDataProfile[]</td><td>En matris med kolumn data profiler.</td></tr>

<tr><td>ColumnDataClassification ("columnDataClassifications")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>Sträng</td><td>Namnet på kolumnen som denna klassificering refererar till.</td></tr>
<tr><td></td><td>klassificering</td><td>Sträng</td><td>Klassificeringen av data i den här kolumnen.</td></tr>

<tr><td>Dokumentation ("dokumentation")</td><td></td><td></td><td>En specifik till gång kan bara ha en tillhör ande dokumentation.</td></tr>
<tr><td></td><td>mimeType</td><td>sträng</td><td>MIME-typen för innehållet.</td></tr>
<tr><td></td><td>innehåll</td><td>sträng</td><td>Dokumentations innehållet.</td></tr>

</table>

### <a name="common-types"></a>Vanliga typer
Vanliga typer kan användas som typer för egenskaper, men är inte objekt.

<table>
<tr><td><b>Gemensam typ</b></td><td><b>Egenskaper</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>sourceType</td><td>sträng</td><td>Beskriver typ av data källa.  Exempel: SQL Server, Oracle Database osv.  </td></tr>
<tr><td></td><td>objectType</td><td>sträng</td><td>Beskriver typen av objekt i data källan. Exempel: tabell, vy för SQL Server.</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>protokollhanterare</td><td>sträng</td><td>Krävs. Beskriver ett protokoll som används för att kommunicera med data källan. Exempel: `tds` för SQL Server `oracle` för Oracle osv. Se [referens specifikationen för data källor – DSL-strukturen](data-catalog-dsr.md) för listan över protokoll som stöds för närvarande.</td></tr>
<tr><td></td><td>adress</td><td>Ord lista &lt; sträng, objekt&gt;</td><td>Krävs. Adress är en uppsättning data som är speciella för det protokoll som används för att identifiera data källan som refereras till. Adress data som omfattas av ett visst protokoll, vilket innebär att det är meningslöst utan att känna till protokollet.</td></tr>
<tr><td></td><td>autentisering</td><td>sträng</td><td>Valfritt. Autentiseringsschema som används för att kommunicera med data källan. Till exempel: Windows, OAuth, osv.</td></tr>
<tr><td></td><td>connectionProperties</td><td>Ord lista &lt; sträng, objekt&gt;</td><td>Valfritt. Ytterligare information om hur du ansluter till en data källa.</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>Server delen utför ingen validering av angivna egenskaper mot Azure Active Directory under publiceringen.</td></tr>
<tr><td></td><td>UPN</td><td>sträng</td><td>Unik e-postadress för användaren. Måste anges om objectId inte anges eller i kontexten för egenskapen lastRegisteredBy, annars valfritt.</td></tr>
<tr><td></td><td>objectId</td><td>GUID</td><td>Användare eller säkerhets grupp Azure Active Directory identitet. Valfritt. Måste anges om UPN inte anges, annars valfritt.</td></tr>
<tr><td></td><td>firstName</td><td>sträng</td><td>Användarens förnamn (för visnings syfte). Valfritt. Endast giltigt i kontexten för egenskapen "lastRegisteredBy". Kan inte anges vid tillhandahållande av säkerhets objekt för "roller", "behörigheter" och "experter".</td></tr>
<tr><td></td><td>lastName</td><td>sträng</td><td>Användarens efter namn (för visnings syfte). Valfritt. Endast giltigt i kontexten för egenskapen "lastRegisteredBy". Kan inte anges vid tillhandahållande av säkerhets objekt för "roller", "behörigheter" och "experter".</td></tr>

<tr><td>Kolumn</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>sträng</td><td>Namnet på kolumnen eller attributet.</td></tr>
<tr><td></td><td>typ</td><td>sträng</td><td>data typen för kolumnen eller attributet. De tillåtna typerna beror på data sourceType för till gången.  Endast en delmängd av typerna stöds.</td></tr>
<tr><td></td><td>Max</td><td>int</td><td>Den maximala tillåtna längden för kolumnen eller attributet. Härlett från data källa. Gäller endast för vissa typer av ursprung.</td></tr>
<tr><td></td><td>precision</td><td>stor</td><td>Precisionen för kolumnen eller attributet. Härlett från data källa. Gäller endast för vissa typer av ursprung.</td></tr>
<tr><td></td><td>isNullable</td><td>Boolesk</td><td>Anger om kolumnen får ha ett null-värde eller inte. Härlett från data källa. Gäller endast för vissa typer av ursprung.</td></tr>
<tr><td></td><td>uttryck</td><td>sträng</td><td>Om värdet är en beräknad kolumn innehåller det här fältet det uttryck som uttrycker värdet. Härlett från data källa. Gäller endast för vissa typer av ursprung.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>sträng</td><td>Kolumnens namn</td></tr>
<tr><td></td><td>typ </td><td>sträng</td><td>Kolumnens typ</td></tr>
<tr><td></td><td>min </td><td>sträng</td><td>Det minsta värdet i data uppsättningen</td></tr>
<tr><td></td><td>max </td><td>sträng</td><td>Det maximala värdet i data uppsättningen</td></tr>
<tr><td></td><td>Gmsn </td><td>double</td><td>Genomsnittligt värde i data uppsättningen</td></tr>
<tr><td></td><td>StDev </td><td>double</td><td>Standard avvikelsen för data uppsättningen</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>Antalet null-värden i data uppsättningen</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>Antalet distinkta värden i data uppsättningen</td></tr>
</table>

## <a name="asset-identity"></a>Till gångens identitet
Azure Data Catalog använder "protokoll" och identitets egenskaper från egenskaps uppsättningen "Address" för DataSourceLocation "DSL" för att generera identiteten för till gången, som används för att adressera till gången i katalogen.
Till exempel har tabell data ström (TDS)-protokollet identitets egenskaper "Server", "databas", "schema" och "Object". Kombinationerna av protokollet och identitets egenskaperna används för att generera identiteten för den SQL Server tabellen till gång.
Azure Data Catalog innehåller flera inbyggda data käll protokoll, som visas i [referens specifikationen för data källor – DSL-struktur](data-catalog-dsr.md).
De protokoll som stöds kan utökas program mässigt (se Data Catalog REST API referens). Administratörer av katalogen kan registrera protokoll för anpassade data källor. I följande tabell beskrivs de egenskaper som krävs för att registrera ett anpassat protokoll.

### <a name="custom-data-source-protocol-specification"></a>Specifikation av anpassad data källa protokoll
<table>
<tr><td><b>Typ</b></td><td><b>Egenskaper</b></td><td><b>Datatyp</b></td><td><b>Kommentarer</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namnområde</td><td>sträng</td><td>Protokollets namnrymd. Namn området måste vara mellan 1 och 255 tecken långt, innehåller en eller flera icke-tomma delar, avgränsade med punkter (.). Varje del måste vara mellan 1 och 255 tecken långt, börja med en bokstav och endast innehålla bokstäver och siffror.</td></tr>
<tr><td></td><td>name</td><td>sträng</td><td>Namnet på protokollet. Namnet måste vara mellan 1 och 255 tecken långt, börja med en bokstav och får bara innehålla bokstäver, siffror och bindestreck (-).</td></tr>
<tr><td></td><td>identityProperties</td><td>DataSourceProtocolIdentityProperty[]</td><td>Lista över identitets egenskaper måste innehålla minst en, men inte fler än 20 egenskaper. Till exempel: "Server", "databas", "schema", "Object" är identitets egenskaper för "TDS"-protokollet.</td></tr>
<tr><td></td><td>identitySets</td><td>DataSourceProtocolIdentitySet[]</td><td>Lista över identitets uppsättningar. Definierar uppsättningar med identitets egenskaper, som representerar en giltig till gångs identitet. Måste innehålla minst en, men inte fler än 20 uppsättningar. Exempel: {"Server", "Database", "schema" och "Object"} är en identitets uppsättning för TDS-protokollet, som definierar identiteten för SQL Server tabell till gång.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>sträng</td><td>Egenskapens namn. Namnet måste vara mellan 1 och 100 tecken långt, börja med en bokstav och får bara innehålla bokstäver och siffror.</td></tr>
<tr><td></td><td>typ</td><td>sträng</td><td>Typ av egenskap. Värden som stöds: "bool", Boolean "," byte "," GUID "," int "," Integer "," Long "," String "," URL "</td></tr>
<tr><td></td><td>ignoreCase</td><td>boolesk</td><td>Anger om Skift läget ska ignoreras när egenskapens värde används. Kan endast anges för egenskaper med typen String. Standardvärdet är false.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool []</td><td>Anger om Skift läge ska ignoreras för varje segment i URL-sökvägen. Kan endast anges för egenskaper med "URL"-typ. Standardvärdet är [FALSE].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>sträng</td><td>Namnet på identitets uppsättningen.</td></tr>
<tr><td></td><td>properties</td><td>sträng []</td><td>Listan över identitets egenskaper som ingår i den här identitets uppsättningen. Den får inte innehålla dubbletter. Varje egenskap som identitets uppsättningen refererar till måste definieras i listan över "identityProperties" för protokollet.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Roller och auktorisering
Microsoft Azure Data Catalog tillhandahåller auktoriseringsregler för CRUD-åtgärder på till gångar och anteckningar.

Azure Data Catalog använder två autentiseringsmetoder:

* Rollbaserad auktorisering
* Behörighets-baserad auktorisering

### <a name="roles"></a>Roller
Det finns tre roller: **administratör**, **ägare** och **deltagare**.  Varje roll har sitt omfång och rättigheter, som sammanfattas i följande tabell.

<table><tr><td><b>Role</b></td><td><b>Omfång</b></td><td><b>Behörigheter</b></td></tr><tr><td>Administratör</td><td>Katalog (alla till gångar/anteckningar i katalogen)</td><td>Läs ta bort ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Ägare</td><td>Varje till gång (rot objekt)</td><td>Läs ta bort ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Deltagare</td><td>Varje enskild till gång och anteckning</td><td>Läs uppdatering ta bort ViewRoles Obs! alla rättigheter återkallas om Läs behörigheten för objektet har återkallats från deltagaren</td></tr></table>

> [!NOTE]
> **Läsa**, **Uppdatera**, **ta bort**, **ViewRoles** rättigheter gäller för alla objekt (till gång eller anteckning) medan **TakeOwnerShip**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions** bara kan användas för rot till gången.
> 
> **Ta bort** rättighet gäller för ett objekt och eventuella under objekt eller enstaka objekt under det. Om du till exempel tar bort en till gång raderas även eventuella anteckningar för den till gången.
> 

### <a name="permissions"></a>Behörigheter
Behörighet är en lista över åtkomst kontroll poster. Varje åtkomst kontroll post tilldelar en uppsättning rättigheter till ett säkerhets objekt. Behörigheter kan bara anges för en till gång (d.v.s. ett rot objekt) och tillämpas på till gången och eventuella under objekt.

Under **Azure Data Catalog** för hands versionen stöds endast **Läs** behörighet i behörighets listan för att aktivera scenariot för att begränsa synligheten för en till gång.

Som standard har alla autentiserade användare **Läs** behörighet för alla objekt i katalogen om inte synlighet är begränsad till en uppsättning huvud konton i behörigheterna.

## <a name="rest-api"></a>REST-API
Begär Anden för att **lägga** till och **publicera** objekt kan användas för att kontrol lera roller och behörigheter: förutom objektets nytto last kan två system egenskaper anges **roller** och **behörigheter**.

> [!NOTE]
> **behörigheter** gäller endast för ett rot objekt.
> 
> **Ägar** rollen gäller endast för ett rot objekt.
> 
> Som standard när ett objekt skapas i katalogen har **deltagaren** angetts till den för tillfället autentiserade användaren. Om objektet ska kunna uppdateras av alla ska **deltagare** vara inställt på &lt; alla &gt; särskilda säkerhets objekt i egenskapen **roles** när objektet publiceras första gången (se följande exempel). **Deltagare** kan inte ändras och förblir på samma gång som ett objekts livs längd (till och med **administratören** eller **ägaren** har inte rätt att ändra **deltagare**). Det enda värde som stöds för den explicita inställningen för **deltagare** är &lt; alla &gt; : **deltagare** kan bara vara en användare som skapade ett objekt eller &lt; alla &gt; .
> 

### <a name="examples"></a>Exempel
**Ange deltagare till &lt; alla &gt; när du publicerar ett objekt.**
Särskilda säkerhets objekt &lt; alla &gt; har objectId "00000000-0000-0000-0000-000000000201".
  **Publicera** https: \/ /API.azuredatacatalog.com/Catalogs/default/views/tables/?API-version=2016-03-30

> [!NOTE]
> Vissa HTTP-serverimplementeringar kan automatiskt återutfärda begär Anden som svar på en 302 från servern, men vanligt vis stripe-svarshuvuden från begäran. Eftersom Authorization-huvudet krävs för att göra begär anden till Azure Data Catalog, måste du se till att Authorization-huvudet fortfarande anges när du skickar en begäran till en omdirigerings plats som anges av Azure Data Catalog. Följande exempel kod visar hur du använder .NET HttpWebRequest-objektet.
> 

**Brödtext**
```json
    {
        "roles": [
            {
                "role": "Contributor",
                "members": [
                    {
                        "objectId": "00000000-0000-0000-0000-000000000201"
                    }
                ]
            }
        ]
    }
```

  **Tilldela ägare och begränsa synligheten för ett befintligt rot objekt**: **Lägg** till https: \/ /API.azuredatacatalog.com/Catalogs/default/views/tables/042297b0...1be45ecd462a?API-version=2016-03-30

```json
    {
        "roles": [
            {
                "role": "Owner",
                "members": [
                    {
                        "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
                        "upn": "user1@contoso.com"
                    },
                    {
                        "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
                        "upn": "user2@contoso.com"
                    }
                ]
            }
        ],
        "permissions": [
            {
                "principal": {
                    "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
                    "upn": "user3@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            },
            {
                "principal": {
                    "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
                    "upn": "user4@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            }
        ]
    }
```

> [!NOTE]
> I princip krävs det inte att du anger en objekt nytto Last i texten: med kan du använda för att uppdatera bara roller och/eller behörigheter.
> 

## <a name="next-steps"></a>Nästa steg
[Azure Data Catalog REST API referens](/rest/api/datacatalog/)
