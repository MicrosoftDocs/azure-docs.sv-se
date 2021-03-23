---
title: Analysera & process-JSON med Apache Hive Azure HDInsight
description: Lär dig hur du använder JSON-dokument och analyserar dem med hjälp av Apache Hive i Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: d3c5077450dd4ec59f5ea7bc8f37879f9aa775bf
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868944"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Bearbeta och analysera JSON-dokument med hjälp av Apache Hive i Azure HDInsight

Lär dig att bearbeta och analysera JavaScript Object Notation-filer (JSON) med Apache Hive i Azure HDInsight. I den här artikeln används följande JSON-dokument:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

Filen finns på `wasb://processjson@hditutorialdata.blob.core.windows.net/` . Mer information om hur du använder Azure Blob Storage med HDInsight finns i [använda HDFS-kompatibla Azure Blob Storage med Apache Hadoop i HDInsight](../hdinsight-hadoop-use-blob-storage.md). Du kan kopiera filen till standard behållaren för klustret.

I den här artikeln använder du Apache Hive-konsolen. Instruktioner för hur du öppnar Hive-konsolen finns i [använda Apache Ambari Hive-vy med Apache Hadoop i HDInsight](apache-hadoop-use-hive-ambari-view.md).

> [!NOTE]  
> Hive-vyn är inte längre tillgänglig i HDInsight 4,0.

## <a name="flatten-json-documents"></a>Förenkla JSON-dokument

Metoderna som anges i nästa avsnitt kräver att JSON-dokumentet består av en enda rad. Därför måste du förenkla JSON-dokumentet till en sträng. Om JSON-dokumentet redan är utplattat kan du hoppa över det här steget och gå direkt till nästa avsnitt om hur du analyserar JSON-data. Om du vill förenkla JSON-dokumentet kör du följande skript:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

Rå JSON-filen finns på `wasb://processjson@hditutorialdata.blob.core.windows.net/` . Hive-tabellen i **StudentsRaw** pekar på det obehandlade JSON-dokument som inte är förenklat.

Hive-tabellen för **StudentsOneLine** lagrar data i HDInsight-standardfilsystemet under **/JSON/Students/** -sökvägen.

Instruktionen **insert** fyller i **StudentOneLine** -tabellen med de sammanslagna JSON-data.

**Select** -instruktionen returnerar bara en rad.

Här är resultatet av **Select** -instruktionen:

:::image type="content" source="./media/using-json-in-hive/hdinsight-flatten-json.png" alt-text="HDInsight förenkla JSON-dokumentet" border="true":::

## <a name="analyze-json-documents-in-hive"></a>Analysera JSON-dokument i Hive

Hive innehåller tre olika mekanismer för att köra frågor på JSON-dokument, eller så kan du skriva egna:

* Använd den get_json_object användardefinierade funktionen (UDF).
* Använd json_tuple UDF.
* Använd anpassad serialisering/deserialiserare (SerDe).
* Skriv din egen UDF genom att använda python eller andra språk. Mer information om hur du kör din egen python-kod med Hive finns i [python UDF med Apache Hive och Apache gris](./python-udf-hdinsight.md).

### <a name="use-the-get_json_object-udf"></a>Använd get_json_object UDF

Hive innehåller en inbyggd UDF som kallas [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) som frågar JSON under körning. Den här metoden tar två argument: tabellens namn och metod namn. Metod namnet har det sammanslagna JSON-dokumentet och JSON-fältet som behöver parsas. Nu ska vi titta på ett exempel för att se hur UDF fungerar.

Följande fråga returnerar förnamn och efter namn för varje elev:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Här är utdata när du kör den här frågan i konsol fönstret:

:::image type="content" source="./media/using-json-in-hive/hdinsight-get-json-object.png" alt-text="Apache Hive får UDF-objekt UDF" border="true":::

Det finns begränsningar för get_json_object UDF:

* Eftersom varje fält i frågan kräver omparsning av frågan, påverkas prestandan.
* **Hämta \_ JSON_OBJECT ()** returnerar sträng representationen för en matris. Om du vill konvertera matrisen till en Hive-matris måste du använda reguljära uttryck för att ersätta hakparenteserna "[" och "]" och sedan måste du också anropa Split för att hämta matrisen.

Den här konverteringen är varför Hive-wikin rekommenderar att du använder **json_tuple**.  

### <a name="use-the-json_tuple-udf"></a>Använd json_tuple UDF

En annan UDF som tillhandahålls av Hive kallas [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), vilket är bättre än [get_ JSON-_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Den här metoden använder en uppsättning nycklar och en JSON-sträng. Returnerar sedan en tupel med värden. Följande fråga returnerar Student-ID och klass från JSON-dokumentet:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Utdata från det här skriptet i Hive-konsolen:

:::image type="content" source="./media/using-json-in-hive/hdinsight-json-tuple.png" alt-text="Apache Hive JSON-frågeresultat" border="true":::

`json_tuple`UDF använder syntaxen för [sido visning](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) i Hive, som gör att JSON- \_ tupel kan skapa en virtuell tabell genom att använda UDT-funktionen på varje rad i den ursprungliga tabellen. Komplexa JSON blir för svårhanterligt på grund av den upprepade användningen av **sido visning**. Dessutom går det inte att hantera kapslade JSON- **JSON_TUPLE** .

### <a name="use-a-custom-serde"></a>Använd en anpassad SerDe

SerDe är det bästa valet för att parsa kapslade JSON-dokument. Du kan definiera JSON-schemat och sedan använda schemat för att parsa dokumenten. Instruktioner finns i [så här använder du en anpassad JSON-SerDe med Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Sammanfattning

Vilken typ av JSON-operator i Hive som du väljer beror på ditt scenario. Med ett enkelt JSON-dokument och ett fält att leta upp väljer du registrerings data filen UDF **get_json_object**. Om du har fler än en nyckel att leta efter kan du använda **json_tuple**. För kapslade dokument använder du **JSON-SerDe**.

## <a name="next-steps"></a>Nästa steg

Relaterade artiklar finns i:

* [Använda Apache Hive och HiveQL med Apache Hadoop i HDInsight för att analysera ett exempel på Apache log4j-filen](./hdinsight-use-hive.md)
* [Analysera flyg fördröjnings data med hjälp av interaktiv fråga i HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)