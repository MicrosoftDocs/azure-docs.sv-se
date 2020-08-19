---
title: Exempel på Jupyter Notebooks med NOAA-data
titleSuffix: Azure Open Datasets
description: Använd exempel Jupyter Notebooks för Azure Open data uppsättningar för att lära dig hur du läser in öppna data uppsättningar och använder dem för att utöka demonstrations data. Tekniker inkluderar användning av Spark-och Pandas för att bearbeta data.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 05/06/2020
ms.openlocfilehash: c24363caac1db8dd8ce21b690ef989b2beb97f2d
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88506064"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Exempel på Jupyter Notebooks visar hur du kan utöka data med öppna data uppsättningar 
Exemplet Jupyter Notebooks för Azure Open data uppsättningar visar hur du läser in öppna data uppsättningar och använder dem för att utöka demonstrations data. Tekniker inkluderar användning av Apache Spark-och Pandas för att bearbeta data.

>[!IMPORTANT]
>När du arbetar i en miljö som inte är Spark-miljö kan öppna data uppsättningar bara hämta en månads data i taget med vissa klasser för att undvika MemoryError med stora data mängder.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>Läsa in NOAA ISD-data (Integrated Surface Database) 
|Notebook-fil        | Beskrivning                                    |
|----------------|------------------------------------------------|
|[Läs in en senaste månad med väder data till en Pandas-dataframe](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Lär dig hur du läser in historiska väder data i dina favorit Pandas-dataframe. |
|[Läs in en senaste månad med väder data till en spark-dataframe](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Lär dig hur du läser in historiska väder data i din favorit Spark-dataframe.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Koppla demonstrations data med NOAA ISD-data 
|Notebook-fil        | Beskrivning                                    |
|----------------|------------------------------------------------|
|[Delta i demonstrations data med väder data – Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Delta i en demonstrations data uppsättning på en månad med sensor platser med väder läsningar i en Pandas dataframe.  |
|[Delta i demonstrations data med väder data – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Delta i en demo uppsättning med sensor platser med väder läsningar i en spark-dataframe. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Delta i NYC taxi-data med NOAA ISD-data 
|Notebook-fil        | Beskrivning                                    |
|----------------|------------------------------------------------|
|[Taxi rese data som berikas med väder data – Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Läs in NYCs gröna taxi data (över 1 månad) och utöka den med väder data i en Pandas-dataframe. Det här exemplet åsidosätter metoden `get_pandas_limit` och balanserar data inläsnings prestanda med data mängden.|
|[Taxi rese data som är omfattande med väder data – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Läs in NYCs gröna taxi data och utöka dem med väder data i Spark dataframe.  |

## <a name="next-steps"></a>Nästa steg

* [Självstudie: Regressions modellering med automatiserad maskin inlärning och en öppen data uppsättning](/azure/machine-learning/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [Python SDK för öppna data uppsättningar](/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)
* [Azure Open Datasets-katalog](https://azure.microsoft.com/services/open-datasets/catalog/)
* [Skapa Azure Machine Learning data uppsättning från öppen data uppsättning](how-to-create-azure-machine-learning-dataset-from-open-dataset.md)