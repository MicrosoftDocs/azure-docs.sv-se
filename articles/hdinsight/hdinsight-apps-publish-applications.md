---
title: Publicera Azure HDInsight-program
description: Lär dig hur du skapar ett HDInsight-program och sedan publicerar det på Azure Marketplace.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 05/14/2018
ms.openlocfilehash: 73d291fd76d9adbe2a095406b56bd879d497d3b5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942665"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Publicera ett HDInsight-program på Azure Marketplace
Du kan installera ett Azure HDInsight-program i ett Linux-baserat HDInsight-kluster. I den här artikeln får du lära dig hur du publicerar ett HDInsight-program på Azure Marketplace. Allmän information om hur du publicerar på Azure Marketplace finns i [publicera ett erbjudande på Azure Marketplace](../marketplace/overview.md).

HDInsight-program använder *BYOL-modellen (ta med din egen licens)* . I ett BYOL-scenario ansvarar en programprovider för att licensiera programmet till App-användare. App-användare debiteras bara för de Azure-resurser som de skapar, till exempel HDInsight-klustret och klustrets virtuella datorer och noder. Faktureringen av själva programmet sker för närvarande inte i Azure.

Mer information finns i dessa artiklar om HDInsight-program:

* [Installera HDInsight-program](hdinsight-apps-install-applications.md). Lär dig hur du installerar ett HDInsight-program i klustren.
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md). Lär dig hur du installerar och testar anpassade HDInsight-program.

## <a name="prerequisites"></a>Förutsättningar
Om du vill skicka in ditt anpassade program på Marketplace måste du först [skapa och testa ditt anpassade program](hdinsight-apps-install-custom-applications.md).

Du måste också registrera ditt Developer-konto. Mer information finns i [publicera ett erbjudande på Azure Marketplace](../marketplace/overview.md) och [skapa ett Microsoft Developer-konto](../marketplace/overview.md).

## <a name="define-the-application"></a>Definiera programmet
Två steg är inblandade i att publicera program på Marketplace. Börja med att definiera en *createUiDef.jsi* filen. Filen createUiDef.jspå anger vilka kluster som programmet är kompatibelt med. Publicera sedan mallen från Azure Portal. Här är ett exempel på en createUiDef.jspå filen:

```json
{
    "handler": "Microsoft.HDInsight",
    "version": "0.0.1-preview",
    "clusterFilters": {
        "types": ["Hadoop", "HBase", "Storm", "Spark"],
        "versions": ["3.6"]
    }
}
```

| Fält | Beskrivning | Möjliga värden |
| --- | --- | --- |
| typer |De klustertyper programmet är kompatibelt med. |Hadoop, HBase, Storm, Spark (eller någon kombination av dessa) |
| versioner |De typer av HDInsight-kluster programmet är kompatibelt med. |3.4 |

## <a name="application-installation-script"></a>Installations skript för program
När ett program installeras i ett kluster (antingen på ett befintligt kluster eller på ett nytt) skapas en Edge-nod. Programmets installations skript körs på Edge-noden.

  > [!IMPORTANT]  
  > Namnet på programmets installations skript måste vara unikt för ett enskilt kluster. Skript namnet måste ha följande format:
  > 
  > "namn": "[concat (' nyans-install-v0 ', '-', uniquestring (' applicationName ')] '
  > 
  > Skript namnet består av tre delar:
  > 
  > * Ett skript namn prefix som måste innehålla antingen program namnet eller ett namn som är relevant för programmet.
  > * Ett bindestreck, för läsbarhet.
  > * En unik sträng funktion med program namnet som parameter.
  > 
  > I listan bestående skript åtgärd visas föregående exempel som **nyans-install-v0-4wkahss55hlas**. Se ett [exempel](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json)på en JSON-nyttolast.
  > 

Installations skriptet måste ha följande egenskaper:
* Skriptet är idempotenta. Flera anrop till skriptet ger samma resultat.
* Skriptet har rätt versions hantering. Använd en annan plats för skriptet när du uppgraderar eller testar ändringar. Detta säkerställer att kunder som installerar programmet inte påverkas av dina uppdateringar eller tester. 
* Skriptet har tillräckligt med loggning vid varje punkt. Skript loggar är vanligt vis det enda sättet att felsöka problem med program installationen.
* Anrop till externa tjänster eller resurser har lämpliga återförsök så att installationen inte påverkas av tillfälliga nätverks problem.
* Om skriptet startar tjänster på noderna övervakas och konfigureras tjänsterna så att de startar automatiskt om en nod startas om.

## <a name="package-the-application"></a>Paketera programmet
Skapa en zip-fil som innehåller alla filer som krävs för att installera HDInsight-programmet. Du kan använda zip-filen för att publicera programmet. Zip-filen innehåller följande filer:

* createUiDefinition.jspå
* mainTemplate.jspå (mer information finns i [installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).)
* Alla nödvändiga skript

> [!NOTE]  
> Du kan vara värd för programfilerna (inklusive alla webbappar-filer) på en offentligt tillgänglig slut punkt.

## <a name="publish-the-application"></a>Publicera programmet
Så här publicerar du ett HDInsight-program:

1. Logga in på [Azure Publishing](https://publish.windowsazure.com/).
2. Välj **Solution templates** i den vänstra menyn.
3. Ange en rubrik och välj sedan **skapa en ny lösnings mall**.
4. Om du inte redan har registrerat din organisation väljer du **skapa dev Center-konto och ansluter till Azure-programmet**.  Mer information finns i [skapa ett Microsoft Developer-konto](../marketplace/overview.md).
5. Välj **definiera vissa topologier för att komma igång**. En lösnings mal len är "överordnad" till alla dess topologier. Du kan definiera flera topologier i ett erbjudande eller en lösnings mall. När ett erbjudande skickas till mellanlagringen, skickas det med alla topologier. 
6. Ange ett Topology-namn och välj sedan **+** .
7. Ange en ny version och välj sedan **+** .
8. Ladda upp zip-filen som du skapade när du paketerade programmet.  
9. Välj **begär certifiering**. Microsofts certifierings team granskar filerna och certifierar topologin.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [installerar HDInsight-program](hdinsight-apps-install-applications.md) i klustren.
* Lär dig hur du [installerar anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md) och distribuerar ett opublicerat HDInsight-program till HDInsight.
* Lär dig hur du [använder skript åtgärder för att anpassa Linux-baserade HDInsight-kluster](hdinsight-hadoop-customize-cluster-linux.md) och lägga till fler program. 
* Lär dig hur du [skapar Linux-baserade Apache Hadoop kluster i HDInsight med Azure Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
* Lär dig hur du [använder en tom Edge-nod i HDInsight](hdinsight-apps-use-edge-node.md) för att komma åt HDInsight-kluster, testa HDInsight-program och vara värd för HDInsight-program.