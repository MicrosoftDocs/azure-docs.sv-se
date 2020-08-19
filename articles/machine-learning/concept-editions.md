---
title: Azure Machine Learning Enterprise-och Basic-versioner
description: Lär dig mer om skillnaderna mellan utgåvorna av Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: j-martens
ms.author: jmartens
ms.date: 06/11/2020
ms.openlocfilehash: fbe3f0109ca3af2cf9ff44061c7882e6bd72c8a6
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508610"
---
# <a name="enterprise-preview-and-basic-editions-of-azure-machine-learning"></a>Enterprise (för hands version) och Basic-versioner av Azure Machine Learning 

Azure Machine Learning erbjuder två versioner som är skräddarsydda för dina maskin inlärnings behov. Dessa versioner avgör vilka Machine Learning-verktyg som är tillgängliga för utvecklare och data experter från deras arbets yta.

## <a name="choose-an-edition"></a>Välj en utgåva

Du tilldelar versionen varje gång du skapar en arbets yta. Kunderna är ansvariga för kostnader som uppstår för beräkning och andra Azure-resurser under den här tiden. Lär dig hur du [hanterar kostnader för Azure Machine Learning](concept-plan-manage-cost.md).

Lär dig hur du [uppgraderar en grundläggande arbets yta till Enterprise (för hands version)](how-to-manage-workspace.md#upgrade). 

## <a name="whats-in-each-edition"></a>Vad är i varje utgåva

### <a name="data-for-machine-learning-capabilities"></a>Data för Machine Learning funktioner  

| Funktioner                     | Utgåva                 |
|------------------------------------------------------------------------------------|:-----------:|
| Märkning: [skapa och hantera etikettbaserade projekt](tutorial-labeling.md) i Studio (webb)                                                | Alla                     |
| Märkning: Labeler i Studio (webb)                                    | Alla                     |
| Märkning: Använd privat arbets styrka                               | Alla                     |
| Märkning: [ml stöd för bild klassificering och objekt identifiering](how-to-label-images.md)                  | Endast Enterprise-utgåva |
| Data uppsättningar + data lager: skapa och hantera i python                       | Alla                     |
| Data uppsättningar + data lager: skapa och hantera i Studio (webb)                         | Alla                     |
| Avvikelse: Visa och hantera data uppsättnings övervakare i python                           | Alla                     |
| Avvikelse: Visa och hantera data uppsättnings övervakare i Studio (webb)                            | Endast Enterprise-utgåva |


<br/>
<br/>

### <a name="automated-training-capabilities-automl"></a>Funktioner för automatisk utbildning (AutoML)

| Funktioner    | Utgåva                 |
|------------------------------------------------------------------------------------|:-----------:|
| Skapa och kör [AutoML-experiment i antecknings böcker](how-to-configure-auto-train.md)               | Alla                     |
| Skapa och köra  [AutoML-experiment i Studio (webb)](how-to-use-automated-ml-for-ml-models.md)   | Endast Enterprise-utgåva |
| Branschledande AutoML prognos funktioner             | Endast Enterprise-utgåva |
| Stöd för djup inlärning och andra avancerade lärare | Endast Enterprise-utgåva |
| Klassificering och Regressions aktiviteter för stor data support (upp till 100 GB)                     | Endast Enterprise-utgåva |


<br/>
<br/>

### <a name="responsible-machine-learning"></a>Ansvarig Machine Learning

| Funktioner    | Utgåva                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Förklarad modell](how-to-machine-learning-interpretability-automl.md)                                              | Alla                     |
| [Differentiell sekretess](how-to-differential-privacy.md)                          | Alla                     |
| Anpassade taggar för att implementera datablad    | Alla                     |
| Skälighet AzureML-integrering                                      | Alla                     |

<br/>
<br/>


### <a name="build-and-train-capabilities"></a>Skapa och utbilda funktioner

| Funktioner    | Utgåva                 |
|------------------------------------------------------------------------------------|:-----------:|
| Visual Studio-kod integrering                                                     | Alla                     |
| Förstärka inlärning                                                             | Alla                     |
| Experimentering gränssnitt                                                                 | Alla                     |
| Jupyter, JupyterLab-integrering                                                    | Alla                     |
| Stöd för python SDK                                                                 | Alla                     |
| Stöd för R SDK                                                                      | Alla                     |
| ML pipelines: skapa, köra och publicera i python                           | Alla                     |
| ML pipelines: skapa, redigera och ta bort schemalagda körningar av pipelines i python| Alla                     |
| ML pipelines: skapa pipeline-slutpunkter i python SDK                                   | Alla                     |
| ML pipelines: Visa körnings information i Studio (webb)                                              | Alla                     |
| ML pipelines: skapa, köra, visualisera och publicera i designern                  | Endast Enterprise-utgåva |
| ML pipelines: skapa pipeline-slutpunkter i designern | Endast Enterprise-utgåva |
| Hanterade beräknings instanser för integrerade antecknings böcker                                 | Alla                     |


<br/>
<br/>

### <a name="deployment-and-model-management-capabilities"></a>Funktioner för distribution och modell hantering

| Funktioner                            | Utgåva                 |
|------------------------------------------------------------------------------------|:-----------:|
| Azure DevOps-tillägget för Machine Learning och Azure ML CLI                 | Alla                     |
| [Event Grid-integrering](how-to-use-event-grid.md)                                                             | Alla                     |
| Integrera Azure Stream Analytics med Azure Machine Learning                       | Alla                     |
| Skapa ML-pipelines i SDK                                                         | Alla                     |
| Batch-inferencing                                                                  | Alla                     |
| FPGA-baserade Maskinvaruaccelererade modeller                                             | Alla                     |
| Modell profilering                                                                    | Alla                     |

<br/>
<br/>

### <a name="security-governance-and-control-capabilities"></a>Säkerhet, styrning och kontroll funktioner

| Funktioner     | Utgåva                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Azure-rollbaserad åtkomst kontroll (Azure RBAC)](how-to-assign-roles.md) support                                           | Alla                     |
| Stöd för [Virtual Network (VNet)](how-to-enable-virtual-network.md) för beräkning                                         | Alla                     |
| Beräknings slut punktens autentisering                                                    | Alla                     |
| [Privat arbets ytans länk](how-to-configure-private-link.md)                                                            | Alla                     |
| [Kvot hantering](how-to-manage-quotas.md) över arbets ytor                                                 | Endast Enterprise-utgåva |

## <a name="next-steps"></a>Nästa steg

Läs mer om vad som är tillgängligt på [sidan med översikt över Azure Machine Learning Edition och prissättning](https://azure.microsoft.com/pricing/details/machine-learning/). 

Lär dig hur du [uppgraderar en grundläggande arbets yta till Enterprise Edition](how-to-manage-workspace.md#upgrade). 
