---
title: Azure Storage jämförelse av migreringsverktyg – ostrukturerade data
description: Grundläggande funktioner och jämförelse mellan verktyg som används för migrering av ostrukturerade data
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 862feace6aab4f49ad3482c4ccd6510669c876a1
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576541"
---
# <a name="comparison-matrix"></a>Jämförelsematris

I följande jämförelsematris visas grundläggande funktioner i olika verktyg som kan användas för migrering av ostrukturerade data. 

## <a name="supported-azure-services"></a>Azure-tjänster som stöds

|    | [Microsoft](https://www.microsoft.com/) | [Datadbis](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Lösningsnamn**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview)              | [Datamobilitet och migrering](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligenta Datahantering](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Azure Files support (alla nivåer)** | Ja                          | Ja                      | Ja            | Ja                            |
| **Azure NetApp Files support**      | Inga                           | Ja                      | Ja            | Ja                            |
| **Stöd för Azure Blob Hot/Cool**   | No                           | Ja (via NFS-förhandsgranskning)    | Ja            | Ja                            |
| **Stöd för Azure Blob Archive-nivån** | Inga                           | Inga                       | Inga             | Ja (som migreringsmål) |
| **Azure Data Lake Storage support** | Inga                           | Inga                       | Inga             | Inga                             |
| **Källor som stöds**      | Windows Server 2012 R2 och upp | NAS & molnfilsystem | Alla NAS och S3 | NAS, Blob, S3                  |

## <a name="supported-protocols-source--destination"></a>Protokoll som stöds (källa/mål)

|    | [Microsoft](https://www.microsoft.com/) | [Datadbis](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Lösningsnamn**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Datamobilitet och migrering](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligent Datahantering](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **SMB 2.1**       | Ja | Ja | Ja | Ja |
| **SMB 3.0**       | Ja | Ja | Ja | Ja |
| **SMB 3.1**       | Ja | Ja | Ja | Ja |
| **NFS v3**        | Inga  | Ja | Ja | Ja |
| **NFS v4.1**      | Inga  | Ja | Inga  | Ja |
| **Blob-REST API** | Inga  | Inga  | Ja | Ja |
| **S3**            | Inga  | Ja | Ja | Ja |

## <a name="extended-features"></a>Utökade funktioner

|    | [Microsoft](https://www.microsoft.com/) | [Datadbis](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Lösningsnamn**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Datamobilitet och migrering](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligenta Datahantering](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **UID-/SID-ommappning**                   | Inga  | Ja                        | Ja | Inga                             |
| **Ommappning av protokoll-ACL**                | Inga  | Inga                         | Inga  | Inga                             |
| **DFS-stöd**                           | Ja | Ja                        | Ja | Ja                            |
| **Begränsningsstöd**                    | Ja | Ja                        | Ja | Ja                            |
| **Undantag för filmönster**               | Inga  | Ja                        | Ja | Ja (med kopieringsfunktioner) |
| **Stöd för selektiva filattribut** | Ja | Ja                        | Ja | Ja (för utökade attribut)  |
| **Ta bort spridningar**                   | Ja | Ja                        | Ja | Ja                            |
| **Följ NTFS- knutpunkter**                 | Inga  | Ja                        | Inga  | Ja                            |
| **Åsidosätt SMB-ägare och gruppägare**    | Ja | Ja                        | Ja | Inga                             |
| **Rapportering om leveranskedjan**            | Inga  | Ja                        | Inga  | Ja                            |
| **Stöd för alternativa dataströmmar**    | Inga  | Ja                        | Ja | Inga                             |
| **Schemaläggning för migrering**              | Inga  | Ja                        | Ja | Ja                            |
| **Bevara ACL**                        | Inga  | Ja                        | Ja | Ja                            |
| **DACL-stöd**                          | Ja | Ja                        | Ja | Ja                            |
| **SACL-stöd**                          | Ja | Ja                        | Ja | Inga                             |
| **Bevara åtkomsttid**                | Ja | Ja                        | Ja | Ja                            |
| **Bevara ändrad tid**              | Ja | Ja                        | Ja | Ja                            |
| **Bevara skapandetid**              | Inga  | Ja                        | Ja | Ja                            |
| **Azure Data Box support**       | Ja | Ja                        | Inga  | Inga                             |
| **Migrering av ögonblicksbilder**                | No  | Manuell                     | Ja | Inga                             |
| **Symboliskt länkstöd**                 | Inga  | Ja                        | Inga  | Ja                            |
| **Stöd för hårda länkar**                     | No  | Migreras som separata filer | Ja | Ja                            |
| **Stöd för öppna/låsta filer**       | Ja | Ja                        | Ja | Ja                            |
| **Inkrementell migrering**                 | Ja | Ja                        | Ja | Ja                            |
| **Stöd för växling**                    | Inga  | Ja                        | Ja | Nej (endast manuellt)               |
| **[Andra funktioner](#other-features)**         | [Länk](#azure-file-sync)| [Länk](#dobimigrate) | [Länk](#data-mobility-and-migration) | [Länk](#intelligent-data-management)                |

## <a name="assessment-and-reporting"></a>Utvärdering och rapportering

|    | [Microsoft](https://www.microsoft.com/) | [Datadbis](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Lösningsnamn**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Datamobilitet och migrering](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligenta Datahantering](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Kapacitet**                        | Inga      | Ja | Ja | Ja            |
| **Antal filer/mappar**            | Inga      | Ja | Ja | Ja            |
| **Åldersfördelning över tid**      | Inga      | Ja | Ja | Ja            |
| **Åtkomsttid**                     | Inga      | Ja | Ja | Ja            |
| **Ändrad tid**                   | Inga      | Ja | Ja | Ja            |
| **Skapandetid**                   | Inga      | Ja | Ja | Ja            |
| **Rapportstatus per fil/objekt** | Delvis | Ja | Ja | Ja            |

## <a name="licensing"></a>Licensiering

|    | [Microsoft](https://www.microsoft.com/) | [Datadbis](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Lösningsnamn**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Datamobilitet och migrering](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligenta Datahantering](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **BYOL**             | N/A | Ja | Ja | Ja |
| **Azure-åtagande** | Ja   | Ja | Ja | Ja |

## <a name="other-features"></a>Andra funktioner

### <a name="azure-file-sync"></a>Azure File Sync

- Intern hash-validering

### <a name="dobimigrate"></a>DobiMigrate

- Förhandskontroller av migrering
- Migreringsplanering
- Testkörning för cut over-testning
- Identifiera och avisering på målsidans användaraktivitet innan du klipper över
- Principdrivna migreringar
- Schemalagda iterationer av kopiering
- Konfigurerbara alternativ för hantering av rotkatalogsäkerhet
- Verifieringskörningar på begäran
- Verifiering av dataläsning på källa och mål
- Grafiskt, interaktivt arbetsflöde för felhantering
- Möjlighet att begränsa vissa åtgärder från att spridas som borttagningar och uppdateringar
- Möjlighet att bevara åtkomsttiden på källan (förutom målet)
- Möjlighet att köra återställning till källan under migreringsväxeln
- Möjlighet att migrera valda SMB-filattribut
- Möjlighet att rensa NTFS-säkerhetsbeskrivningar
- Möjlighet att åsidosätta NFSv3-behörigheter och skriva nya läges bitar till målet
- Möjlighet att konvertera NFSv3 POSIX-utkast-ACLS till NFSv4 ACLS
- SMB 1 (CIFS)

### <a name="data-mobility-and-migration"></a>Datamobilitet och migrering

- Hash-validering

### <a name="intelligent-data-management"></a>Intelligenta Datahantering

- Projekt-/katalogbaserade migreringar
- Automatiskt återförsök av fel
- Utvärdering/rapportering: Filtyper, filstorlek, projektbaserad
- Utvärdering/rapportering: Anpassade metadatabaserade sökningar
- Fullständig lösning för livscykelhantering av data för arkivering, replikering, analys
- Få åtkomst till tidsbaserad analys av Blob, S3-data
- Taggning
- Stöd 24 x 7 x 365
- Hash-validering

*Listan verifierades senast den 31 mars 2021.*

## <a name="see-also"></a>Se även

- [Översikt över lagringsmigrering](../../../common/storage-migration-overview.md)
- [Välja en Azure-lösning för dataöverföring](/azure/storage/common/storage-choose-data-transfer-solution?toc=/azure/storage/blobs/toc.json)
- [Migrera till Azure-filresurser](/azure/storage/files/storage-files-migration-overview)
- [Migrera till Data Lake Storage med WANdisco LiveData Platform för Azure](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [Kopiera eller flytta data till Azure Storage med AzCopy](https://aka.ms/azcopy)
- [Migrera stora datauppsättningar till Azure Blob Storage med AzReplicate (exempelprogram)](https://github.com/Azure/AzReplicate/tree/master/)
