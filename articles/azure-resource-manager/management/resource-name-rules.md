---
title: Namngivningsbegränsningar för resurser
description: Visar regler och begränsningar för namngivning av Azure-resurser.
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: e260c9055b26d82f2fd2f8458d287a35a838f40f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477799"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Namngivningsregler och begränsningar för Azure-resurser

I den här artikeln sammanfattas namngivningsregler och begränsningar för Azure-resurser. Rekommendationer om hur du namnger resurser finns [i Rekommenderade namngivnings- och taggningskonventioner.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

Den här artikeln innehåller resurser efter resursproviderns namnområde. En lista över hur resursproviders matchar Azure-tjänster finns i [Resursproviders för Azure-tjänster.](azure-services-resource-providers.md)

Resursnamn är okänsliga om de inte anges i kolumnen med giltiga tecken.

I följande tabeller refererar termen alfanumeriskt till:

* **a** till **och med z** (gemener)
* **A** till **och med Z** (versaler)
* **0** till **9** (siffror)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Servrar | Resursgrupp | 3–63 | Gemener och siffror.<br><br>Börja med gemener. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | tjänst | Globala | 1-50 | Alfanumeriska värden och bindestreck.<br><br>Börja med bokstaven och avsluta med alfanumeriskt. |
> | tjänst/API:er | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst/API:er/problem | api | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst/apis/problem/bifogade filer | issue | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst/apis/problem/kommentarer | issue | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst/api:er/åtgärder | api | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst/apis/åtgärder/taggar | operation | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst/apis/versioner | api | 1–80 | Alfanumeriska värden, understreck och bindestreck.<br><br>Börja och avsluta med alfanumeriskt eller understreck. |
> | tjänst/API:er/scheman | api | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | service/apis/tagDescriptions | api | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/apis/taggar | api | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/api-version-sets | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/authorizationServers | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/backends | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/certifikat | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst/diagnostik | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst/grupper | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst/grupper/användare | group | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst/identityProviders | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst/loggare | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst/meddelanden | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst/meddelanden/mottagareE-post | avisering | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/openidConnectProviders | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/principer | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/produkter | tjänst | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/produkter/API:er | produkt | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/produkter/grupper | produkt | 1-256 | Kan inte använda:<br> `*#&+:<>?` |
> | tjänst/produkter/taggar | produkt | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst/egenskaper | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst/prenumerationer | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst/taggar | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst/mallar | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |
> | tjänst/användare | tjänst | 1-256 | Det går inte att använda:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | configurationStores | Resursgrupp | 5-50 | Alfanumeriska värden, understreck och bindestreck. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Lås | tilldelningsomfång | 1–90 | Alfanumeriska värden, punkter, understreck, bindestreck och parenteser.<br><br>Det går inte att avsluta i perioden. |
> | policyAssignments | tilldelningsomfång | 1–128 visningsnamn<br><br>1–64 resursnamn<br><br>1–24 resursnamn i hanteringsgruppomfånget | Visningsnamn kan innehålla alla tecken.<br><br>Resursnamnet får inte innehålla `%` och kan inte sluta med punkt eller blanksteg. |
> | policyDefinitions | definitionsområde | 1–128 visningsnamn<br><br>1–64 resursnamn | Visningsnamn kan innehålla alla tecken.<br><br>Resursnamnet får inte innehålla `%` och kan inte sluta med punkt eller blanksteg. |
> | policySetDefinitions | definitionsområde | 1–128 visningsnamn<br><br>1–64 resursnamn<br><br>1–24 resursnamn i hanteringsgruppomfånget | Visningsnamnet kan innehålla alla tecken.<br><br>Resursnamnet får inte innehålla `%` och kan inte sluta med punkt eller blanksteg.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | automationAccounts | resursgrupp & region <br>(Se anteckningen nedan) | 6-50 | Alfanumeriska tecken och bindestreck.<br><br>Börja med bokstaven och avsluta med alfanumeriskt. |
> | automationAccounts/certifikat | automation-konto | 1-128 | Det går inte att använda:<br> `<>*%&:\?.+/` <br><br>Det går inte att sluta med utrymme.  |
> | automationAccounts/connections | automation-konto | 1-128 | Det går inte att använda:<br> `<>*%&:\?.+/` <br><br>Det går inte att sluta med utrymme. |
> | automationAccounts/credentials | automation-konto | 1-128 | Kan inte använda:<br> `<>*%&:\?.+/` <br><br>Det går inte att sluta med utrymme. |
> | automationAccounts/runbooks | automation-konto | 1–63 | Alfanumeriska värden, understreck och bindestreck.<br><br>Börja med bokstaven .  |
> | automationAccounts/scheman | automation-konto | 1-128 | Kan inte använda:<br> `<>*%&:\?.+/` <br><br>Det går inte att sluta med utrymme. |
> | automationAccounts/variables | automation-konto | 1-128 | Kan inte använda:<br> `<>*%&:\?.+/` <br><br>Det går inte att sluta med utrymme. |
> | automationKonto/bevakare | automation-konto | 1–63 |  Alfanumeriska värden, understreck och bindestreck.<br><br>Börja med bokstaven . |
> | automationAccounts/webhooks | automation-konto | 1-128 | Det går inte att använda:<br> `<>*%&:\?.+/` <br><br>Det går inte att sluta med utrymme. |

> [!NOTE]
> Automation-kontonamn är unika per region och resursgrupp. Namn på borttagna Automation-konton kanske inte är omedelbart tillgängliga.

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | batchAccounts | Region | 3–24 | Gemener och siffror. |
> | batchAccounts/applications | batch-konto | 1-64 | Alfanumeriska värden, understreck och bindestreck. |
> | batchAccounts/certifikat | batch-konto | 5-45 | Alfanumeriska värden, understreck och bindestreck. |
> | batchAccounts/pools | batch-konto | 1-64 | Alfanumeriska värden, understreck och bindestreck. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | blockchainMembers | Globala | 2-20 | Gemener och siffror.<br><br>Börja med gemener. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | botServices | Globala | 2–64 |  Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. |
> | botServices/channels | robottjänst | 2–64 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. |
> | botServices/Anslutningar | robottjänst | 2–64 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. |
> | enterpriseChannels | Resursgrupp | 2–64 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Redis | Globala | 1–63 | Alfanumeriska tecken och bindestreck.<br><br>Börja och avsluta med alfanumeriskt. Efterföljande bindestreck tillåts inte. |
> | Redis/firewallRules | Redis | 1-256 | Alfanumeriska |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Profiler | Resursgrupp | 1-260 | Alfanumeriska tecken och bindestreck.<br><br>Börja och avsluta med alfanumeriskt. |
> | profiler/slutpunkter | Globala | 1-50 | Alfanumeriska värden och bindestreck.<br><br>Börja och avsluta med alfanumeriskt. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | certificateOrders | Resursgrupp | 3-30 | Alfanumeriska. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | konton | Resursgrupp | 2–64 | Alfanumeriska värden och bindestreck.<br><br>Börja och avsluta med alfanumeriskt. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | availabilitySets | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta med alfanumeriskt eller understreck. |
> | diskEncryptionSets | Resursgrupp | 1–80 | Alfanumeriska värden och understreck. |
> | Diskar | Resursgrupp | 1–80 | Alfanumeriska värden, understreck och bindestreck. |
> | Gallerier | Resursgrupp | 1–80 | Alfanumeriska värden och punkter.<br><br>Börja och avsluta med alfanumeriskt. |
> | gallerier/program | galleri | 1–80 | Alfanumeriska tecken, bindestreck och punkter.<br><br>Börja och avsluta med alfanumeriskt. |
> | gallerier/program/versioner | program | 32-bitars heltal | Tal och punkter. |
> | gallerier/bilder | galleri | 1–80 | Alfanumeriska värden, understreck, bindestreck och punkter.<br><br>Börja och avsluta med alfanumeriskt. |
> | gallerier/bilder/versioner | image | 32-bitars heltal | Tal och punkter. |
> | images | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta med alfanumeriskt eller understreck. |
> | snapshots | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta med alfanumeriskt eller understreck. |
> | virtualMachines | Resursgrupp | 1-15 (Windows)<br>1-64 (Linux)<br><br>Se anteckningen nedan. | Det går inte att använda blanksteg eller dessa tecken:<br> `~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \ | ; : . ' " , < > / ?`<br><br>Virtuella Windows-datorer får inte innehålla punkt eller slut med bindestreck.<br><br>Virtuella Linux-datorer kan inte sluta med punkt eller bindestreck. |
> | virtualMachineScaleSets | Resursgrupp | 1-15 (Windows)<br>1-64 (Linux)<br><br>Se anteckningen nedan. | Det går inte att använda blanksteg eller dessa tecken:<br> `~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \ | ; : . ' " , < > / ?`<br><br>Det går inte att börja med understreck. Det går inte att sluta med punkt eller bindestreck. |

> [!NOTE]
> Virtuella Azure-datorer har två distinkta namn: resursnamn och värdnamn. När du skapar en virtuell dator i portalen används samma värde för båda namnen. Begränsningarna i föregående tabell gäller för värdnamnet. Det faktiska resursnamnet kan innehålla upp till 64 tecken.

## <a name="microsoftcommunication"></a>Microsoft.Communication

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | communicationServices | Globala | 1–63 | Alfanumeriska tecken, bindestreck och understreck. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | containerGroups | Resursgrupp | 1–63 | Gemener, siffror och bindestreck.<br><br>Det går inte att starta eller avsluta med bindestreck. Flera bindestreck efter varandra tillåts inte. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Register | Globala | 5-50 | Alfanumeriska. |
> | register/buildTasks | registry | 5-50 | Alfanumeriska. |
> | register/buildTasks/steps | build-uppgift | 5-50 | Alfanumeriska. |
> | register/replikeringar | registry | 5-50 | Alfanumeriska. |
> | register/scopeMaps | registry | 5-50 | Alfanumeriska värden, bindestreck och understreck. |
> | register/uppgifter | registry | 5-50 | Alfanumeriska värden, bindestreck och understreck. |
> | register/token | registry | 5-50 | Alfanumeriska värden, bindestreck och understreck. |
> | register/webhooks | registry | 5-50 | Alfanumeriska. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | managedClusters | Resursgrupp | 1–63 | Alfanumeriska värden, understreck och bindestreck.<br><br>Börja och avsluta med alfanumeriskt. |
> | openShiftManagedClusters | Resursgrupp | 1-30 | Alfanumeriska. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | nav | Resursgrupp | 1-64 | Alfanumeriska.<br><br>Börja med bokstaven .  |
> | hubs/authorizationPolicies | hubben | 1-50 | Alfanumeriska värden, understreck och punkter.<br><br>Börja och avsluta med alfanumeriskt. |
> | hubbar/anslutningsappar | hubben | 1-128 | Alfanumeriska värden och understreck.<br><br>Börja med bokstaven . |
> | hubs/connectors/mappings | anslutningsapp | 1-128 | Alfanumeriska värden och understreck.<br><br>Börja med bokstaven . |
> | hubbar/interaktioner | hubben | 1-128 | Alfanumeriska värden och understreck.<br><br>Börja med bokstaven . |
> | hubbar/kpi | hubben | 1-512 | Alfanumeriska värden och understreck.<br><br>Börja med bokstaven . |
> | hubbar/länkar | hubben | 1-512 | Alfanumeriska värden och understreck.<br><br>Börja med bokstaven . |
> | hubbar/förutsägelser | hubben | 1-512 | Alfanumeriska värden och understreck.<br><br>Börja med bokstaven . |
> | hubbar/profiler | hubben | 1-128 | Alfanumeriska värden och understreck.<br><br>Börja med bokstaven . |
> | hubs/relationshipLinks | hubben | 1-512 | Alfanumeriska värden och understreck.<br><br>Börja med bokstaven . |
> | hubbar/relationer | hubben | 1-512 | Alfanumeriska värden och understreck.<br><br>Börja med bokstaven . |
> | hubs/roleAssignments | hubben | 1-128 | Alfanumeriska värden och understreck.<br><br>Börja med bokstaven . |
> | hubbar/vyer | hubben | 1-512 | Alfanumeriska värden och understreck.<br><br>Börja med bokstaven . |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Sammanslutningar | Resursgrupp | 1-180 | Det går inte att använda:<br>`%&\\?/`<br><br>Det går inte att avsluta med punkt eller blanksteg. |
> | resourceProviders | Resursgrupp | 3-64 | Det går inte att använda:<br>`%&\\?/`<br><br>Det går inte att sluta med punkt eller blanksteg. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Jobb | Resursgrupp | 3–24 | Alfanumeriska tecken, bindestreck, understreck och punkter. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | arbetsytor | Resursgrupp | 3-30 | Alfanumeriska värden, understreck och bindestreck |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Fabriker | Globala | 3–63 | Alfanumeriska värden och bindestreck.<br><br>Börja och avsluta med alfanumeriskt. |
> | fabriker/dataflöden | Fabriken | 1-260 | Kan inte använda:<br>`<>*#.%&:\\+?/`<br><br>Börja med alfanumeriskt. |
> | fabriker/datauppsättningar | Fabriken | 1-260 | Det går inte att använda:<br>`<>*#.%&:\\+?/`<br><br>Börja med alfanumeriskt. |
> | fabriker/integrationRuntimes | Fabriken | 3–63 | Alfanumeriska tecken och bindestreck.<br><br>Börja och avsluta med alfanumeriskt. |
> | fabriker/länkade tjänster | Fabriken | 1-260 | Det går inte att använda:<br>`<>*#.%&:\\+?/`<br><br>Börja med alfanumeriskt. |
> | fabriker/pipelines | Fabriken | 1-260 | Det går inte att använda:<br>`<>*#.%&:\\+?/`<br><br>Börja med alfanumeriskt. |
> | fabriker/utlösare | Fabriken | 1-260 | Det går inte att använda:<br>`<>*#.%&:\\+?/`<br><br>Börja med alfanumeriskt. |
> | fabriker/utlösare/rerunTriggers | Utlösa | 1-260 | Kan inte använda:<br>`<>*#.%&:\\+?/`<br><br>Börja med alfanumeriskt. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | konton | Globala | 3–24 | Gemener och siffror. |
> | konton/computePolicies | konto | 3-60 | Alfanumeriska värden, bindestreck och understreck. |
> | accounts/dataLakeStoreAccounts | konto | 3–24 | Gemener och siffror. |
> | konton/firewallRules | konto | 3-50 | Alfanumeriska värden, bindestreck och understreck. |
> | konton/storageAccounts | konto | 3-60 | Alfanumeriska värden, bindestreck och understreck. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | konton | Globala | 3–24 | Gemener och siffror. |
> | konton/firewallRules | konto | 3-50 | Alfanumeriska tecken, bindestreck och understreck. |
> | konton/virtualNetworkRules | konto | 3-50 | Alfanumeriska tecken, bindestreck och understreck. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | services | Resursgrupp | 2-62 | Alfanumeriska tecken, bindestreck, punkter och understreck.<br><br>Börja med alfanumeriskt. |
> | tjänster/projekt | tjänst | 2-57 | Alfanumeriska tecken, bindestreck, punkter och understreck.<br><br>Börja med alfanumeriskt. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMarinaDB

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Servrar | Globala | 3–63 | Gemener, bindestreck och siffror.<br><br>Det går inte att börja eller sluta med bindestreck. |
> | servrar/databaser | Servrar | 1–63 | Alfanumeriska värden och bindestreck. |
> | servrar/firewallRules | Servrar | 1-128 | Alfanumeriska värden, bindestreck och understreck. |
> | servrar/virtualNetworkRules | Servrar | 1-128 | Alfanumeriska värden och bindestreck. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Servrar | Globala | 3–63 | Gemener, bindestreck och siffror.<br><br>Det går inte att börja eller sluta med bindestreck. |
> | servrar/databaser | Servrar | 1–63 | Alfanumeriska tecken och bindestreck. |
> | servrar/firewallRules | Servrar | 1-128 | Alfanumeriska tecken, bindestreck och understreck. |
> | servrar/virtualNetworkRules | Servrar | 1-128 | Alfanumeriska tecken och bindestreck. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Servrar | Globala | 3–63 | Gemener, bindestreck och siffror.<br><br>Det går inte att starta eller avsluta med bindestreck. |
> | servrar/databaser | Servrar | 1–63 | Alfanumeriska tecken och bindestreck. |
> | servrar/firewallRules | Servrar | 1-128 | Alfanumeriska värden, bindestreck och understreck. |
> | servrar/virtualNetworkRules | Servrar | 1-128 | Alfanumeriska värden och bindestreck. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | IotHubs | Globala | 3-50 | Alfanumeriska värden och bindestreck.<br><br>Det går inte att avsluta med bindestreck. |
> | IotHubs/certifikat | IoT Hub | 1-64 | Alfanumeriska värden, bindestreck, punkter och understreck. |
> | IotHubs/eventHubEndpoints/ConsumerGroups | eventHubEndpoints | 1-50 | Alfanumeriska värden, bindestreck, punkter och understreck. |
> | provisioningServices | Resursgrupp | 3-64 | Alfanumeriska tecken och bindestreck.<br><br>Avsluta med alfanumeriskt. |
> | provisioningServices/certificates | provisioningServices | 1-64 | Alfanumeriska tecken, bindestreck, punkter och understreck. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Labs | Resursgrupp | 1-50 | Alfanumeriska värden, understreck och bindestreck. |
> | labb/customimages | Lab | 1–80 | Alfanumeriska värden, understreck, bindestreck och parenteser. |
> | labb/formler | Lab | 1–80 | Alfanumeriska värden, understreck, bindestreck och parenteser. |
> | labb/virtualmachines | Lab | 1–15 (Windows)<br>1-64 (Linux) | Alfanumeriska värden och bindestreck.<br><br>Börja och avsluta med alfanumeriskt. Kan inte vara alla tal. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | databaseAccounts | Globala | 3-44 | Gemener, siffror och bindestreck.<br><br>Börja med gemener eller siffra. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Domäner | Resursgrupp | 3-50 | Alfanumeriska värden och bindestreck. |
> | domäner/ämnen | domän | 3-50 | Alfanumeriska värden och bindestreck. |
> | eventSubscriptions | Resursgrupp | 3-64 | Alfanumeriska värden och bindestreck. |
> | Ämnen | Resursgrupp | 3-50 | Alfanumeriska tecken och bindestreck. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Kluster | Resursgrupp | 6-50 | Alfanumeriska tecken och bindestreck.<br><br>Börja med bokstaven . Avsluta med bokstav eller siffra. |
> | Namnområden | Globala | 6-50 | Alfanumeriska tecken och bindestreck.<br><br>Börja med bokstaven . Avsluta med bokstav eller siffra. |
> | namespaces/AuthorizationRules | namnområde | 1-50 | Alfanumeriska värden, punkter, bindestreck och understreck.<br><br>Börja och avsluta med bokstav eller siffra. |
> | namespaces/disasterRecoveryConfigs | namnområde | 1-50 | Alfanumeriska värden, punkter, bindestreck och understreck.<br><br>Börja och avsluta med bokstav eller siffra. |
> | namnrymder/eventhubs | namnområde | 1-50 | Alfanumeriska värden, punkter, bindestreck och understreck.<br><br>Börja och avsluta med bokstav eller siffra. |
> | namespaces/eventhubs/authorizationRules | händelsehubb | 1-50 | Alfanumeriska värden, punkter, bindestreck och understreck.<br><br>Börja och avsluta med bokstav eller siffra. |
> | namnområden/eventhubs/consumergroups | händelsehubb | 1-50 | Alfanumeriska värden, punkter, bindestreck och understreck.<br><br>Börja och avsluta med bokstav eller siffra. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Kluster | Globala | 3-59 | Alfanumeriska tecken och bindestreck<br><br>Börja och avsluta med bokstav eller siffra. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Jobb | Resursgrupp | 2–64 | Alfanumeriska tecken och bindestreck.<br><br>Börja med bokstaven . |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | actionGroups | Resursgrupp | 1-260 | Det går inte att använda:<br>`/&%\?` <br><br>Det går inte att sluta med blanksteg eller punkt.  |
> | Komponenter | Resursgrupp | 1-260 | Det går inte att använda:<br>`%&\?/` <br><br>Det går inte att sluta med blanksteg eller punkt.  |
> | scheduledQueryRules | Resursgrupp | 1-260 | Det går inte att använda:<br>`*<>%{}&:\\?/#` <br><br>Det går inte att sluta med blanksteg eller punkt.  |
> | metricAlerts | Resursgrupp | 1-260 | Kan inte använda:<br>`*#&+:<>?@%{}\/` <br><br>Det går inte att sluta med blanksteg eller punkt.  |
> | activityLogAlerts | Resursgrupp | 1-260 | Kan inte använda:<br>`<>*%{}&:\\?+/#` <br><br>Det går inte att sluta med blanksteg eller punkt.  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | IoTApps | Globala | 2-63 | Gemener, siffror och bindestreck.<br><br>Börja med gemener eller siffra. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Valv | Globala | 3–24 | Alfanumeriska värden och bindestreck.<br><br>Börja med bokstaven . Avsluta med bokstav eller siffra. Får inte innehålla efterföljande bindestreck. |
> | valv/hemligheter | Valv | 1-127 | Alfanumeriska tecken och bindestreck. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Kluster | Globala | 4-22 | Gemener och siffror.<br><br>Börja med bokstaven . |
> | /clusters/databases | cluster | 1-260 | Alfanumeriska tecken, bindestreck, blanksteg och punkter. |
> | /clusters/databases/dataConnections | databas | 1-40 | Alfanumeriska tecken, bindestreck, blanksteg och punkter. |
> | /clusters/databases/eventhubconnections | databas | 1-40 | Alfanumeriska tecken, bindestreck, blanksteg och punkter. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | integrationAccounts | Resursgrupp | 1–80 | Alfanumeriska värden, bindestreck, understreck, punkter och parenteser. |
> | integrationAccounts/assemblies | integrationskonto | 1–80 | Alfanumeriska värden, bindestreck, understreck, punkter och parenteser. |
> | integrationAccounts/batchConfigurations | integrationskonto | 1-20 | Alfanumeriska. |
> | integrationAccounts/certificates | integrationskonto | 1–80 | Alfanumeriska värden, bindestreck, understreck, punkter och parenteser. |
> | integrationAccounts/maps | integrationskonto | 1–80 | Alfanumeriska värden, bindestreck, understreck, punkter och parenteser. |
> | integrationAccounts/partners | integrationskonto | 1–80 | Alfanumeriska värden, bindestreck, understreck, punkter och parenteser. |
> | integrationAccounts/integrationanetprocessconfigurations | integrationskonto | 1–80 | Alfanumeriska värden, bindestreck, understreck, punkter och parenteser. |
> | integrationAccounts/scheman | integrationskonto | 1–80 | Alfanumeriska värden, bindestreck, understreck, punkter och parenteser. |
> | integrationAccounts/sessions | integrationskonto | 1–80 | Alfanumeriska värden, bindestreck, understreck, punkter och parenteser. |
> | integrationServiceEnvironments | Resursgrupp | 1–80 | Alfanumeriska värden, bindestreck, punkter och understreck. |
> | integrationServiceEnvironments/managedApis | integreringstjänstmiljö | 1–80 | Alfanumeriska tecken, bindestreck, punkter och understreck. |
> | Arbetsflöden | Resursgrupp | 1–80 | Alfanumeriska värden, bindestreck, understreck, punkter och parenteser. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | commitmentPlans | Resursgrupp | 1-260 | Det går inte att använda:<br>`<>*%&:?+/\\`<br><br>Det går inte att avsluta med ett blanksteg. |
> | Webservices | Resursgrupp | 1-260 | Det går inte att använda:<br>`<>*%&:?+/\\`<br><br>Det går inte att avsluta med ett blanksteg. |
> | arbetsytor | Resursgrupp | 1-260 | Det går inte att använda:<br>`<>*%&:?+/\\`<br><br>Det går inte att avsluta med ett blanksteg. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | arbetsytor | Resursgrupp | 3-33 | Alfanumeriska tecken och bindestreck. |
> | arbetsytor/beräkningar | arbetsyta | 2–16 | Alfanumeriska tecken och bindestreck. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | userAssignedIdentities | Resursgrupp | 3-128 | Alfanumeriska tecken, bindestreck och understreck<br><br>Börja med bokstav eller siffra. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | konton | Resursgrupp | 1–98 (för resursgruppens namn och kontonamn) | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | mediaservices | Resursgrupp | 3–24 | Gemener och siffror. |
> | mediaservices/liveEvents | Medietjänst | 1-32 | Alfanumeriska värden och bindestreck.<br><br>Börja med alfanumeriskt. |
> | mediaservices/liveEvents/liveOutputs | Livehändelse | 1-256 | Alfanumeriska värden och bindestreck.<br><br>Börja med alfanumeriskt. |
> | mediaservices/streamingEndpoints | Medietjänst | 1-24 | Alfanumeriska tecken och bindestreck.<br><br>Börja med alfanumeriskt. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | applicationGateways | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | applicationSecurityGroups | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | azureFirewalls | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta med alfanumeriskt eller understreck. |
> | bastionHosts | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | Anslutningar | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | dnsZones | Resursgrupp | 1–63 tecken<br><br>2 till 34 etiketter<br><br>Varje etikett är en uppsättning tecken avgränsade med en punkt. Till exempel har **contoso.com** 2 etiketter. | Varje etikett kan innehålla alfanumeriska tecken, understreck och bindestreck.<br><br>Varje etikett avgränsas med en punkt. |
> | expressRouteCircuits | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | firewallPolicies | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | firewallPolicies/ruleGroups | brandväggsprincip | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | frontDoors | Globala | 5-64 | Alfanumeriska tecken och bindestreck.<br><br>Börja och avsluta med alfanumeriskt. |
> | frontdoorWebApplicationFirewallPolicies | Resursgrupp | 1-128 | Alfanumeriska.<br><br>Börja med bokstaven . |
> | loadBalancers | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | loadBalancers/inboundNatRules | lastbalanserare | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | localNetworkGateways | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | networkInterfaces | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | networkSecurityGroups | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | networkSecurityGroups/securityRules | nätverkssäkerhetsgrupp | 1–80 |  Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | networkWatchers | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | privateDnsZones | Resursgrupp | 1–63 tecken<br><br>2 till 34 etiketter<br><br>Varje etikett är en uppsättning tecken avgränsade med en punkt. Till exempel **contoso.com** två etiketter. | Varje etikett kan innehålla alfanumeriska tecken, understreck och bindestreck.<br><br>Varje etikett avgränsas med en punkt. |
> | privateDnsZones/virtualNetworkLinks | privat DNS-zon | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | publicIPAddresses | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | publicIPPrefixes | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | routeFilters | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | routeFilters/routeFilterRules | flödesfilter | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | routeTables | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | routeTables/routes | route table | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | serviceEndpointPolicies | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | trafficmanagerprofiles | Globala | 1–63 | Alfanumeriska tecken, bindestreck och punkter.<br><br>Börja och avsluta med alfanumeriskt. |
> | virtualNetworkGateways | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | virtualNetworks | Resursgrupp | 2–64 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | virtualnetworks/undernät | virtuellt nätverk | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | virtualNetworks/virtualNetworkPeerings | virtuellt nätverk | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | virtualWans | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | vpnGateways | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | vpnGateways/vpnConnections | VPN gateway | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |
> | vpnSites | Resursgrupp | 1–80 | Alfanumeriska värden, understreck, punkter och bindestreck.<br><br>Börja med alfanumeriskt. Avsluta alfanumeriskt eller understreck. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Namnområden | Globala | 6-50 | Alfanumeriska tecken och bindestreck<br><br>Börja med bokstaven . Avsluta med alfanumeriskt. |
> | namespaces/AuthorizationRules | namnområde | 1-256 | Alfanumeriska värden, punkter, bindestreck och understreck.<br><br>Starta alfanumeriskt. |
> | namespaces/notificationHubs | namnområde | 1-260 | Alfanumeriska värden, punkter, bindestreck och understreck.<br><br>Starta alfanumeriskt. |
> | namespaces/notificationHubs/AuthorizationRules | notification hub | 1-256 | Alfanumeriska värden, punkter, bindestreck och understreck.<br><br>Starta alfanumeriskt. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Kluster | Resursgrupp | 4-63 | Alfanumeriska värden och bindestreck.<br><br>Börja och avsluta med alfanumeriskt. |
> | arbetsytor | Globala | 4-63 | Alfanumeriska värden och bindestreck.<br><br>Börja och avsluta med alfanumeriskt. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | lösningar | arbetsyta | Ej tillämpligt | För lösningar som har skrivits av Microsoft måste namnet finnas i mönstret:<br>`SolutionType(WorkspaceName)`<br><br>För lösningar som har skrivits av tredje part måste namnet finnas i mönstret:<br>`SolutionType[WorkspaceName]`<br><br>Ett giltigt namn är till exempel:<br>`AntiMalware(contoso-IT)`<br><br>Lösningstypen är fallkänslig. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | instrumentpaneler | Resursgrupp | 3-160 | Alfanumeriska tecken och bindestreck.<br><br>Om du vill använda begränsade tecken lägger du till en tagg **med namnet hidden-title** med det instrumentpanelsnamn som du vill använda. Portalen visar det namnet när du visar instrumentpanelen. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3–63 | Alfanumeriska tecken och bindestreck.<br><br>Det går inte att börja med bindestreck. Det går inte att använda efterföljande bindestreck. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Kapacitet | region | 3–63 | Gemener eller siffror<br><br>Börja med gemener. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Valv | Resursgrupp | 2-50 | Alfanumeriska tecken och bindestreck.<br><br>Börja med bokstaven . |
> | valv/backupPolicies | valv | 3-150 | Alfanumeriska tecken och bindestreck.<br><br>Börja med bokstaven . Det går inte att avsluta med bindestreck. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Namnområden | Globala | 6-50 | Alfanumeriska värden och bindestreck.<br><br>Börja med en bokstav. Avsluta med en bokstav eller siffra. |
> | namnområden/AuthorizationRules | namnområde | 1-50 |  Alfanumeriska värden, punkter, bindestreck och understreck.<br><br>Börja och avsluta med alfanumeriskt. |
> | namespaces/HybridConnections | namnområde | 1-260 | Alfanumeriska värden, punkter, bindestreck, understreck och snedstreck.<br><br>Börja och avsluta med alfanumeriskt. |
> | namespaces/HybridConnections/authorizationRules | hybridanslutning | 1-50 | Alfanumeriska värden, punkter, bindestreck och understreck.<br><br>Börja och avsluta med alfanumeriskt. |
> | namespaces/WcfRelays | namnområde | 1-260 | Alfanumeriska värden, punkter, bindestreck, understreck och snedstreck.<br><br>Börja och avsluta med alfanumeriskt. |
> | namespaces/WcfRelays/authorizationRules | Wcf Relay | 1-50 | Alfanumeriska värden, punkter, bindestreck och understreck.<br><br>Börja och avsluta med alfanumeriskt. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Distributioner | Resursgrupp | 1-64 | Alfanumeriska värden, understreck, parenteser, bindestreck och punkter. |
> | resourcegroups | prenumeration | 1–90 | Alfanumeriska tecken, understreck, parenteser, bindestreck, punkter och Unicode-tecken som matchar [regex-dokumentationen](/rest/api/resources/resourcegroups/createorupdate).<br><br>Det går inte att avsluta med punkt. |
> | tagNames | resource | 1-512 | Det går inte att använda:<br>`<>%&\?/` |
> | tagNames/tagValues | taggnamn | 1-256 | Alla tecken. |
> | templateSpecs | Resursgrupp | 1–90 | Alfanumeriska värden, understreck, parenteser, bindestreck och punkter. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Namnområden | Globala | 6-50 | Alfanumeriska värden och bindestreck.<br><br>Börja med en bokstav. Avsluta med en bokstav eller siffra.<br><br>Mer information finns i [Skapa namnområde](/rest/api/servicebus/create-namespace). |
> | namnområden/AuthorizationRules | namnområde | 1-50 | Alfanumeriska värden, punkter, bindestreck och understreck.<br><br>Börja och avsluta med alphnumeric. |
> | namespaces/disasterRecoveryConfigs | Globala | 6-50 | Alfanumeriska värden och bindestreck.<br><br>Börja med bokstaven . Avsluta med alfanumeriskt. |
> | namespaces/migrationConfigurations | namnområde |  | Bör alltid vara **$default**. |
> | namnrymder/köer | namnområde | 1-260 | Alfanumeriska värden, punkter, bindestreck, understreck och snedstreck.<br><br>Börja och avsluta med alfanumeriskt. |
> | namespaces/queues/authorizationRules | kö | 1-50 | Alfanumeriska värden, punkter, bindestreck och understreck.<br><br>Starta och avsluta med alphnumeric. |
> | namnrymder/ämnen | namnområde | 1-260 | Alfanumeriska värden, punkter, bindestreck, understreck och snedstreck.<br><br>Börja och avsluta med alfanumeriskt. |
> | namespaces/topics/authorizationRules | ämne | 1-50 | Alfanumeriska värden, punkter, bindestreck och understreck.<br><br>Starta och avsluta med alphnumeriskt. |
> | namnområden/ämnen/prenumerationer | ämne | 1-50 | Alfanumeriska värden, punkter, bindestreck och understreck.<br><br>Börja och avsluta med alphnumeric. |
> | namnområden/ämnen/prenumerationer/regler | prenumeration | 1-50 | Alfanumeriska värden, punkter, bindestreck och understreck.<br><br>Börja och avsluta med alphnumeric. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Kluster | region | 4-23 | Gemener, siffror och bindestreck.<br><br>Börja med gemener. Avsluta med en gemen eller en siffra. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | signalR | Globala | 3–63 | Alfanumeriska värden och bindestreck.<br><br>Börja med bokstaven . Avsluta med bokstav eller siffra.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | managedInstances | Globala | 1–63 | Gemener, siffror och bindestreck.<br><br>Det går inte att starta eller avsluta med bindestreck. <br><br> Kan inte ha några specialtecken, till exempel `@` . |
> | Servrar | Globala | 1–63 | Gemener, siffror och bindestreck.<br><br>Det går inte att starta eller avsluta med bindestreck. |
> | servrar/administratörer | server |  | Måste vara `ActiveDirectory` . |
> | servrar/databaser | server | 1-128 | Det går inte att använda:<br>`<>*%&:\/?`<br><br>Det går inte att sluta med punkt eller blanksteg. |
> | servrar/databaser/syncGroups | databas | 1-150 | Alfanumeriska tecken, bindestreck och understreck. |
> | servrar/elasticPools | server | 1-128 | Kan inte använda:<br>`<>*%&:\/?`<br><br>Det går inte att sluta med punkt eller blanksteg. |
> | servrar/failoverGroups | Globala | 1–63 | Gemener, siffror och bindestreck.<br><br>Det går inte att börja eller sluta med bindestreck. |
> | servrar/firewallRules | server | 1-128 | Kan inte använda:<br>`<>*%&:;\/?`<br><br>Det går inte att sluta med punkt. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | storageAccounts | Globala | 3–24 | Gemener och siffror. |
> | storageAccounts/blobServices | storage account |  | Måste vara `default` . |
> | storageAccounts/blobServices/containers | storage account | 3–63 | Gemener, siffror och bindestreck.<br><br>Börja med gemener eller siffra. Det går inte att använda på varandra följande bindestreck. |
> | storageAccounts/fileServices | storage account |  | Måste vara `default` . |
> | storageAccounts/fileServices/shares | storage account | 3–63 | Gemener, siffror och bindestreck.<br><br>Det går inte att starta eller avsluta med bindestreck. Det går inte att använda efterföljande bindestreck. |
> | storageAccounts/managementPolicies | storage account |  | Måste vara `default` . |
> | blob | container | 1–1024 | Alla URL-tecken, fallkänsliga |
> | kö | storage account | 3–63 | Gemener, siffror och bindestreck.<br><br>Det går inte att starta eller avsluta med bindestreck. Det går inte att använda efterföljande bindestreck. |
> | tabell | storage account | 3–63 | Alfanumeriska.<br><br>Börja med bokstaven . |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | storageSyncServices | Resursgrupp | 1-260 | Alfanumeriska värden, blanksteg, punkter, bindestreck och understreck.<br><br>Det går inte att avsluta med punkt eller blanksteg. |
> | storageSyncServices/syncGroups | lagringssynkroniseringstjänst | 1-260 | Alfanumeriska värden, blanksteg, punkter, bindestreck och understreck.<br><br>Det går inte att sluta med punkt eller blanksteg. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Chefer | Resursgrupp | 2-50 | Alfanumeriska värden och bindestreck.<br><br>Börja med bokstaven . Avsluta med alfanumeriskt. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | streamingjobs | Resursgrupp | 3–63 | Alfanumeriska värden, bindestreck och understreck. |
> | streamingjobs/functions | strömmande jobb | 3–63 | Alfanumeriska värden, bindestreck och understreck. |
> | streamingjobs/indata | strömmande jobb | 3–63 | Alfanumeriska värden, bindestreck och understreck. |
> | streamingjobs/outputs | strömmande jobb | 3–63 | Alfanumeriska tecken, bindestreck och understreck. |
> | streamingjobs/transformationer | strömmande jobb | 3–63 | Alfanumeriska tecken, bindestreck och understreck. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | Miljöer | Resursgrupp | 1–90 | Det går inte att använda:<br>`'<>%&:\?/#` |
> | miljöer/accessPolicies | miljö | 1–90 | Det går inte att använda:<br> `'<>%&:\?/#` |
> | miljöer/eventSources | miljö | 1–90 | Det går inte att använda:<br>`'<>%&:\?/#` |
> | miljöer/referenceDataSets | miljö | 3–63 | Alfanumeriska |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Entitet | Omfång | Längd | Giltiga tecken |
> | --- | --- | --- | --- |
> | certifikat | Resursgrupp | 1-260 | Det går inte att använda:<br>`/` <br><br>Det går inte att sluta med blanksteg eller punkt.  | 
> | serverfarms | Resursgrupp | 1-40 | Alfanumeriska tecken och bindestreck. |
> | webbplatser | global eller per domän. Se anteckningen nedan. | 2-60 | Innehåller alfanumeriska tecken och bindestreck.<br><br>Det går inte att börja eller sluta med bindestreck. |
> | platser/platser | webbplats | 2-59 | Alfanumeriska värden och bindestreck. |

> [!NOTE]
> En webbplats måste ha en globalt unik URL. När du skapar en webbplats som använder en värdplan är URL:en `http://<app-name>.azurewebsites.net` . Appnamnet måste vara globalt unikt. När du skapar en webbplats som använder App Service-miljön måste appnamnet vara unikt inom domänen [för App Service-miljön](../../app-service/environment/using-an-ase.md#app-access). I båda fallen är webbplatsens webbadress globalt unik.
>
> Azure Functions har samma namngivningsregler och begränsningar som Microsoft.Web/sites.

## <a name="next-steps"></a>Nästa steg

Rekommendationer om hur du namnger resurser finns [i Ready: Recommended naming and tagging conventions (Redo: Rekommenderade namngivnings- och taggningskonventioner).](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)
