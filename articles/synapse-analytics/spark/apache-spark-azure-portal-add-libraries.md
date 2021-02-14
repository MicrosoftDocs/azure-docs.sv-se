---
title: Hantera bibliotek för Apache Spark
description: Lär dig hur du lägger till och hanterar bibliotek som används av Apache Spark i Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 0458fb8b140166b7bdf0fc0df41dbb207fdce3c9
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518529"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Hantera bibliotek för Apache Spark i Azure Synapse Analytics

Bibliotek ger återanvändbar kod som du kanske vill inkludera i dina program eller projekt. Om du vill göra tredje part eller lokalt skapad kod tillgänglig för dina program, kan du installera ett bibliotek på någon av dina Server lös Apache Spark pooler. När ett bibliotek har installerats för en spark-pool är det tillgängligt för alla sessioner som använder samma pool. 

## <a name="before-you-begin"></a>Innan du börjar
- Om du vill installera och uppdatera bibliotek måste du ha behörighet för **Storage BLOB-data deltagare** eller **lagrings-BLOB-data** på det primära Gen2 lagrings konto som är länkat till Azure Synapse Analytics-arbetsytan.
  
## <a name="default-installation"></a>Standard installation
Apache Spark i Azure Synapse Analytics har en fullständig Anacondas-installation plus extra bibliotek. Du hittar den fullständiga biblioteks listan på [Apache Spark versions stöd](apache-spark-version-support.md). 

När en spark-instans startar, tas dessa bibliotek automatiskt med. Extra python och anpassade-skapade paket kan läggas till på nivån för Spark-poolen.


## <a name="manage-python-packages"></a>Hantera python-paket
När du har identifierat de bibliotek som du vill använda för Spark-programmet kan du installera dem i en spark-pool. 

 En *requirements.txt* -fil (utdata från `pip freeze` kommandot) kan användas för att uppgradera den virtuella miljön. Paketen som anges i den här filen för installation eller uppgradering laddas ned från PyPI vid tidpunkten för poolen startades. Den här krav filen används varje gång en spark-instans skapas från den Spark-poolen.

> [!IMPORTANT]
> - Om paketet som du installerar är stort eller tar lång tid att installera, påverkar detta start tiden för Spark-instansen.
> - Paket som kräver kompilatorstöd vid installationstillfället, till exempel GCC, stöds inte.
> - Paket kan inte nedgraderas, bara läggas till eller uppgraderas.
> - Det finns inte stöd för att ändra PySpark-, python-, Scala-/Java-, .NET-eller Spark-versionen.
> - Det finns inte stöd för att installera paket från PyPI i DEP-aktiverade arbets ytor.


### <a name="requirements-format"></a>Krav format

I följande kodfragment visas formatet för krav filen. PyPi-paketets namn visas tillsammans med en exakt version. Den här filen följer formatet som beskrivs i referens dokumentationen för [pip Freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/) . Det här exemplet fäster en speciell version. 

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="install-python-packages"></a>Installera python-paket
När du utvecklar Spark-programmet kanske du upptäcker att du behöver uppdatera befintliga eller installera nya bibliotek. Bibliotek kan uppdateras under eller efter att poolen har skapats.

> [!IMPORTANT]
> Om du vill installera bibliotek måste du ha behörigheter för Storage BLOB-data deltagare eller lagrings-BLOB-data på det primära Gen2-lagrings kontot som är kopplat till Synapse-arbetsytan.

#### <a name="install-packages-during-pool-creation"></a>Installera paket när du skapar en pool
Så här installerar du bibliotek till en spark-pool när du skapar en pool:
   
1. Navigera till din Azure Synapse Analytics-arbetsyta från Azure Portal.
   
2. Välj **skapa Apache Spark pool** och välj sedan fliken **ytterligare inställningar** . 
   
3. Ladda upp miljö konfigurations filen med fil väljaren i avsnittet **paket** på sidan. 
   
    ![Lägg till Python-bibliotek när du skapar en pool](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-add-library-python.png "Lägg till Python-bibliotek")
 

#### <a name="install-packages-from-the-synapse-workspace"></a>Installera paket från arbets ytan Synapse
Uppdatera eller lägga till fler bibliotek i en spark-pool från Azure Synapse Analytics-portalen:

1.  Navigera till din Azure Synapse Analytics-arbetsyta från Azure Portal.
   
2.  Starta din Azure Synapse Analytics-arbetsyta från Azure Portal.

3.  Välj **Hantera** från huvud navigerings panelen och välj sedan **Apache Spark pooler**.
   
4. Välj en enskild Spark-pool och överför miljö konfigurations filen med fil väljaren i avsnittet  **paket** på sidan.

    ![Lägga till Python-bibliotek i Synapse](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-update.png)
   
#### <a name="install-packages-from-the-azure-portal"></a>Installera paket från Azure Portal
Så här installerar du ett bibliotek på en spark-pool direkt från Azure Portal:
   
 1. Navigera till din Azure Synapse Analytics-arbetsyta från Azure Portal.
   
 2. Under avsnittet **Synapse-resurser** väljer du fliken **Apache Spark pooler** och väljer en spark-pool i listan.
   
 3. Välj **paket** i avsnittet **Inställningar** i Spark-poolen. 

 4. Ladda upp miljö konfigurations filen med fil väljaren.

    ![Skärm bild som visar knappen Ladda upp miljö konfigurations fil.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Lägg till Python-bibliotek")

### <a name="verify-installed-libraries"></a>Verifiera installerade bibliotek

Kontrol lera att rätt versioner av rätt bibliotek är installerade genom att köra följande kod

```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
### <a name="update-python-packages"></a>Uppdatera python-paket
Paket kan läggas till eller ändras när som helst mellan sessioner. En ny paket konfigurations fil kommer skriva över befintliga paket och versioner.  

Så här uppdaterar eller avinstallerar du ett bibliotek:
1. Navigera till din Azure Synapse Analytics-arbetsyta. 

2. Använd Azure Portal eller Azure dataSynapses-arbetsytan och välj den **Apache Spark-pool** som du vill uppdatera.

3. Navigera till avsnittet **paket** och ladda upp en ny miljö konfigurations fil
   
4. När du har sparat ändringarna måste du avsluta aktiva sessioner och låta poolen starta om. Du kan också tvinga aktiva sessioner att sluta genom att markera kryss rutan för att **tvinga nya inställningar**.

    ![Lägg till Python-bibliotek](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Lägg till Python-bibliotek")
   

> [!IMPORTANT]
> Genom att välja alternativet för att **tvinga nya inställningar** kommer du att avsluta alla aktuella sessioner för den valda Spark-poolen. När sessionerna har slutförts måste du vänta tills poolen startats om. 
>
> Om den här inställningen är omarkerad måste du vänta tills den aktuella Spark-sessionen avslutas eller stoppa den manuellt. När sessionen har upphört måste du låta poolen starta om. 


## <a name="manage-a-python-wheel"></a>Hantera ett python-hjul

### <a name="install-a-custom-wheel-file"></a>Installera en anpassad Wheel-fil
Anpassade hjul paket kan installeras på den Apache Spark poolen genom att ladda upp alla hjul till Azure Data Lake Storage-kontot (Gen2) som är länkat till arbets ytan Synapse. 

Filerna ska överföras till följande sökväg i lagrings kontots standard behållare: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

Du kan behöva lägga till ```python``` mappen i ```libraries``` mappen om den inte redan finns.

>[!IMPORTANT]
>Anpassade paket kan läggas till eller ändras mellan sessioner. Du måste dock vänta tills poolen och sessionen startats om för att se det uppdaterade paketet.

## <a name="next-steps"></a>Nästa steg
- Visa standard biblioteken: [Apache Spark versions stöd](apache-spark-version-support.md)
