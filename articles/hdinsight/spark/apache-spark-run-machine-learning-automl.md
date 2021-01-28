---
title: Kör Azure Machine Learning arbets belastningar på Apache Spark i HDInsight
description: Lär dig hur du kör Azure Machine Learning arbets belastningar med automatiserad Machine Learning (AutoML) på Apache Spark i Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/13/2019
ms.openlocfilehash: 4087341a9a96ae56c00972f886ce3cc8891750a5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929733"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>Kör Azure Machine Learning arbets belastningar med automatiserad maskin inlärning på Apache Spark i HDInsight

Azure Machine Learning fören klar och påskyndar byggnaden, utbildningen och distributionen av Machine Learning-modeller. I Automated Machine Learning (AutoML) börjar du med tränings data som har en definierad mål funktion och går sedan igenom kombinationer av algoritmer och funktions val för att automatiskt välja den bästa modellen för dina data baserat på utbildnings poängen. Med HDInsight kan kunderna etablera kluster med hundratals noder. AutoML som körs i spark i ett HDInsight-kluster gör att användarna kan använda beräknings kapacitet över dessa noder för att köra utbildnings jobb på ett skalbart sätt och för att köra flera utbildnings jobb parallellt. Detta gör att användarna kan köra AutoML experiment samtidigt som de delar beräkningen med sina andra Big data-arbetsbelastningar.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Installera Azure Machine Learning i ett HDInsight-kluster

Allmän själv studie kurs om automatisk maskin inlärning finns i [Självstudier: använda Automatisk maskin inlärning för att bygga Regressions modellen](../../machine-learning/tutorial-auto-train-models.md).
Alla nya HDInsight-Spark-kluster levereras förinstallerade med AzureML-AutoML SDK.

> [!Note]
> Azure Machine Learning-paket installeras i python3 Conda-miljö. Den installerade Jupyter Notebook ska köras med PySpark3-kärnan.

Du kan även använda Zeppelin-anteckningsböcker för att använda AutoML.

> [!Note]
> Zeppelin har ett [känt problem](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) där PySpark3 inte väljer rätt version av python. Använd det dokumenterade arbetet runt.

## <a name="authentication-for-workspace"></a>Autentisering för arbets yta

Skapandet av arbets ytan och experimentet kräver en autentiseringstoken. Denna token kan genereras med hjälp av ett [Azure AD-program](../../active-directory/develop/app-objects-and-service-principals.md). En [Azure AD-användare](/azure/python/python-sdk-azure-authenticate) kan också användas för att generera en obligatorisk autentiseringstoken om Multi-Factor Authentication inte har Aktiver ATS för kontot.  

Följande kodfragment skapar en autentiseringstoken med hjälp av ett **Azure AD-program**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```

Följande kodfragment skapar en autentiseringstoken med en **Azure AD-användare**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>Läser in data uppsättning

Automatisk maskin inlärning på Spark använder **data flöden**, som är Lazy-utvärderade, oföränderliga åtgärder på data.  Ett data flöde kan läsa in en data uppsättning från en blob med offentlig Läs behörighet eller från en BLOB-URL med en SAS-token.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Du kan också registrera data lagret med arbets ytan med en engångs registrering.

## <a name="experiment-submission"></a>Experiment överföring

I den [automatiserade konfigurationen av Machine Learning](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) `spark_context` ska egenskapen anges för det paket som ska köras i distribuerat läge. Egenskapen `concurrent_iterations` , som är det högsta antalet iterationer som körs parallellt, ska anges till ett tal som är mindre än utförar-kärnan för Spark-appen.

## <a name="next-steps"></a>Nästa steg

* Mer information om motiveringen bakom Automatisk maskin inlärning finns i [versions modeller i takt med Microsofts automatiserade maskin inlärning!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Mer information om hur du använder Azure ML automatiserade ML-funktioner finns i [nya funktioner för automatisk maskin inlärning i Azure Machine Learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [AutoML-projekt från Microsoft Research](https://www.microsoft.com/research/project/automl/)
