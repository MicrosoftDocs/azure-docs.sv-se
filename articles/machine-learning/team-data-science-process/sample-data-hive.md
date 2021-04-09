---
title: Exempel data i Azure HDInsight Hive-tabeller – team data science process
description: Exempel data som lagras i Azure HDInsight Hive-tabeller med Hive-frågor för att minska data till en storlek som kan hanteras mer för analys.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6a015da77cb7c0ba54be1dd5e729a9ee8a848c9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321892"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Exempeldata i Azure HDInsight Hive-tabeller
Den här artikeln beskriver hur du kan stänga av data som lagras i Azure HDInsight Hive-tabeller med Hive-frågor för att minska den till en storlek som är mer hanterbar för analys. Den täcker tre populära provtagnings metoder:

* Enhetlig Stick prov
* Slumpmässig sampling efter grupper
* Stratified-sampling

**Varför ska du testa dina data?**
Om data uppsättningen som du planerar att analysera är stor är det vanligt vis en bra idé att stänga av data för att minska den till en mindre men representativ och mer hanterbar storlek. Nedsampling underlättar data förståelse, utforskning och funktions teknik. Dess roll i team data science-processen är att möjliggöra snabb prototyper av data bearbetnings funktioner och maskin inlärnings modeller.

Den här samplings aktiviteten är ett steg i [TDSP (Team data science process)](./index.yml).

## <a name="how-to-submit-hive-queries"></a>Så här skickar du Hive-frågor
Hive-frågor kan skickas från Hadoop Command-Line-konsolen på Head-noden i Hadoop-klustret.  Logga in på noden Head i Hadoop-klustret, öppna Hadoop Command-Line-konsolen och skicka Hive-frågor därifrån. Anvisningar om hur du skickar Hive-frågor i Hadoop Command-Line-konsolen finns i [så här skickar du Hive-frågor](move-hive-tables.md#submit).

## <a name="uniform-random-sampling"></a><a name="uniform"></a> Enhetlig Stick prov
Enhetligt Stick prov innebär att varje rad i data uppsättningen har samma chans att bli samplad. Det kan implementeras genom att lägga till ett extra fält rand () i data uppsättningen i den inre "Select"-frågan och i den yttre "Välj" fråga som villkoret i det slumpmässiga fältet.

Här är en exempelfråga:

```python
SET sampleRate=<sample rate, 0-1>;
select
    field1, field2, …, fieldN
from
    (
    select
        field1, field2, …, fieldN, rand() as samplekey
    from <hive table name>
    )a
where samplekey<='${hiveconf:sampleRate}'
```

Här `<sample rate, 0-1>` anger den andel av de poster som användarna vill sampla.

## <a name="random-sampling-by-groups"></a><a name="group"></a> Slumpmässig sampling efter grupper
När du samplar kategoriska data kanske du vill antingen ta med eller undanta alla instanser av något värde för kategoriska-variabeln. Den här sortens sampling kallas "sampling by Group". Om du till exempel har en kategoriska variabel "*State*", som har värden som New, MA, ca, NJ och PA, vill du att poster från varje tillstånd ska vara tillsammans, oavsett om de samplas eller inte.

Här är ett exempel på en fråga som samplas efter grupp:

```python
SET sampleRate=<sample rate, 0-1>;
select
    b.field1, b.field2, …, b.catfield, …, b.fieldN
from
    (
    select
        field1, field2, …, catfield, …, fieldN
    from <table name>
    )b
join
    (
    select
        catfield
    from
        (
        select
            catfield, rand() as samplekey
        from <table name>
        group by catfield
        )a
    where samplekey<='${hiveconf:sampleRate}'
    )c
on b.catfield=c.catfield
```

## <a name="stratified-sampling"></a><a name="stratified"></a>Stratified-sampling
Slumpmässig provtagning är Stratified med avseende på en kategoriska-variabel när de hämtade exemplen har kategoriska värden som förekommer i samma förhållande som de var i den överordnade populationen. I samma exempel som ovan antar vi att dina data har följande observationer efter tillstånd: NJ har 100 observationer, NY har 60 observationer och WA har 300 observationer. Om du anger samplings frekvensen för Stratified till 0,5 bör det erhållna provet ha cirka 50, 30 och 150 observationer av NJ, Sverige och WA.

Här är en exempelfråga:

```hiveql
SET sampleRate=<sample rate, 0-1>;
select
    field1, field2, field3, ..., fieldN, state
from
    (
    select
        field1, field2, field3, ..., fieldN, state,
        count(*) over (partition by state) as state_cnt,
          rank() over (partition by state order by rand()) as state_rank
      from <table name>
    ) a
where state_rank <= state_cnt*'${hiveconf:sampleRate}'
```

Mer information om mer avancerade samplings metoder som är tillgängliga i Hive finns i [LanguageManual-sampling](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).