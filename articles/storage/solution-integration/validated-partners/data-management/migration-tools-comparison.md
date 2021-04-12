---
title: Jämförelse av Azure Storage migrations verktyg – ostrukturerade data
description: Grundläggande funktioner och jämförelser mellan verktyg som används för migrering av ostrukturerade data
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 15daeb0e6bf320a0727d8e6ea502063a30e67ad0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231651"
---
# <a name="comparison-matrix"></a>Jämförelse mat ris

Följande jämförelse mat ris visar grundläggande funktioner för olika verktyg som kan användas för migrering av ostrukturerade data. 

## <a name="supported-azure-services"></a>Azure-tjänster som stöds

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Lösnings namn**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview)              | [Data mobilitet och migrering](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligent Datahantering](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Azure Files-support (alla nivåer)** | Ja                          | Ja                      | Ja            | Ja                            |
| **Azure NetApp Files support**      | Inga                           | Ja                      | Ja            | Ja                            |
| **Stöd för Azure Blob-hett/låg frekvent**   | Inga                           | Ja (via NFS-förhandsgranskning)    | Ja            | Ja                            |
| **Stöd för Azure Blob Archive-nivåer** | Inga                           | Inga                       | Inga             | Ja (som migreringsmålet) |
| **Azure Data Lake Storage support** | Inga                           | Inga                       | Inga             | Inga                             |
| **Källor som stöds**      | Windows Server 2012 R2 och uppåt | NAS & moln fil system | Alla NAS och S3 | NAS, BLOB, S3                  |

## <a name="supported-protocols-source--destination"></a>Protokoll som stöds (källa/mål)

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Lösnings namn**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Data mobilitet och migrering](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligent Datahantering](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **SMB 2.1**       | Ja | Ja | Ja | Ja |
| **SMB 3.0**       | Ja | Ja | Ja | Ja |
| **SMB 3,1**       | Ja | Ja | Ja | Ja |
| **NFS v3**        | Inga  | Ja | Ja | Ja |
| **NFS v 4.1**      | Inga  | Ja | Inga  | Ja |
| **BLOB-REST API** | Inga  | Inga  | Ja | Ja |
| **S3**            | Inga  | Ja | Ja | Ja |

## <a name="extended-features"></a>Utökade funktioner

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Lösnings namn**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Data mobilitet och migrering](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligent Datahantering](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Ommappning av UID/SID**                   | Inga  | Ja                        | Ja | Inga                             |
| **Ommappning av protokoll-ACL**                | Inga  | Inga                         | Inga  | Inga                             |
| **DFS-stöd**                           | Ja | Ja                        | Ja | Ja                            |
| **Stöd för begränsning**                    | Ja | Ja                        | Ja | Ja                            |
| **Fil mönster undantag**               | Inga  | Ja                        | Ja | Ja (med hjälp av kopierings funktionen) |
| **Stöd för selektiva filattribut** | Ja | Ja                        | Ja | Ja (för utökade attribut)  |
| **Ta bort spridningar**                   | Ja | Ja                        | Ja | Ja                            |
| **Följ NTFS-Knut punkter**                 | Inga  | Ja                        | Inga  | Ja                            |
| **Åsidosätt SMB-ägare och grupp ägare**    | Ja | Ja                        | Ja | Inga                             |
| **Kedja av vårdnads rapportering**            | Inga  | Ja                        | Inga  | Ja                            |
| **Stöd för alternativa data strömmar**    | Inga  | Ja                        | Ja | Inga                             |
| **Schemaläggning för migrering**              | Inga  | Ja                        | Ja | Ja                            |
| **Bevara ACL**                        | Inga  | Ja                        | Ja | Ja                            |
| **Stöd för DACL**                          | Ja | Ja                        | Ja | Ja                            |
| **Stöd för SACL**                          | Ja | Ja                        | Ja | Inga                             |
| **Bevara åtkomst tid**                | Ja | Ja                        | Ja | Ja                            |
| **Bevara ändrad tid**              | Ja | Ja                        | Ja | Ja                            |
| **Skapande tid för konservering**              | Inga  | Ja                        | Ja | Ja                            |
| **Azure Data Box support**       | Ja | Ja                        | Inga  | Inga                             |
| **Migrering av ögonblicks bilder**                | Inga  | Manuell                     | Ja | Inga                             |
| **Stöd för symboliska länkar**                 | Inga  | Ja                        | Inga  | Ja                            |
| **Stöd för hårda länkar**                     | Inga  | Migrerad som separata filer | Ja | Ja                            |
| **Stöd för öppna/låsta filer**       | Ja | Ja                        | Ja | Ja                            |
| **Inkrementell migrering**                 | Ja | Ja                        | Ja | Ja                            |
| **Stöd för växling**                    | Inga  | Ja                        | Ja | Nej (endast manuell)               |
| **[Andra funktioner](#other-features)**         | [Länk](#azure-file-sync)| [Länk](#dobimigrate) | [Länk](#data-mobility-and-migration) | [Länk](#intelligent-data-management)                |

## <a name="assessment-and-reporting"></a>Utvärdering och rapportering

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Lösnings namn**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Data mobilitet och migrering](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligent Datahantering](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Kapacitet**                        | Inga      | Ja | Ja | Ja            |
| **antal filer/mappar**            | Inga      | Ja | Ja | Ja            |
| **Ålders fördelning över tid**      | Inga      | Ja | Ja | Ja            |
| **Åtkomst tid**                     | Inga      | Ja | Ja | Ja            |
| **Ändrings tid**                   | Inga      | Ja | Ja | Ja            |
| **Skapande tid**                   | Inga      | Ja | Ja | Ja (endast SMB) |
| **Status för per fil/objekt rapport** | Delvis | Ja | Ja | Ja            |

## <a name="licensing"></a>Licensiering

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Lösnings namn**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Data mobilitet och migrering](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligent Datahantering](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **BYOL**             | EJ TILLÄMPLIGT | Ja | Ja | Ja |
| **Azure-åtagande** | Ja   | Ja | Ja | Ja |

## <a name="other-features"></a>Andra funktioner

### <a name="azure-file-sync"></a>Azure File Sync

- Intern hash-verifiering

### <a name="dobimigrate"></a>DobiMigrate

- Kontroller för migrering
- Planera migrering
- Torkat kör för avskurna i testning
- Identifiera och varna på mål sidans användar aktivitet innan den klipps över
- Princip driven migreringar
- Schemalagd kopiering av iterationer
- Konfigurerbara alternativ för att hantera rot Katalog säkerhet
- Verifierings körningar på begäran
- Läs verifiering av data på källa och mål
- Grafiskt, interaktivt arbets flöde för fel hantering
- Möjlighet att begränsa vissa åtgärder från att spridas som borttagning och uppdateringar
- Möjlighet att bevara åtkomst tiden för källan (utöver målet)
- Möjlighet att köra återställning till källan under migreringen
- Möjlighet att migrera valda SMB-filattribut
- Möjlighet att rensa NTFS säkerhets beskrivningar
- Möjlighet att åsidosätta NFSv3-behörigheter och att skriva nya läges bitar till mål
- Möjlighet att konvertera NFSv3 POSIX-utkast till NFSv4 ACL: er
- SMB 1 (CIFS)

### <a name="data-mobility-and-migration"></a>Data mobilitet och migrering

- Hash-validering

### <a name="intelligent-data-management"></a>Intelligent Datahantering

- Projekt/katalogbaserade migreringar
- Automatiskt återförsök för fel
- Utvärdering/rapportering: filtyper, fil storlek, projekt som baseras
- Utvärdering/rapportering: anpassade metadata-baserade sökningar
- Fullständig data livs cykel hanterings lösning för arkivering, replikering, analys
- Åtkomst till tidsbaserad analys på BLOB, S3-data
- Taggning
- Stöd för 24 x 7 x 365
- Hash-validering

*Listan kontrollerades senast den 31 mars 2021.*

## <a name="see-also"></a>Se även

- [Översikt över lagringsmigrering](../../../common/storage-migration-overview.md)
- [Välja en Azure-lösning för dataöverföring](/azure/storage/common/storage-choose-data-transfer-solution?toc=/azure/storage/blobs/toc.json)
- [Migrera till Azure-filresurser](/azure/storage/files/storage-files-migration-overview)
- [Migrera till Data Lake Storage med WANdisco LiveData-plattformen för Azure](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [Kopiera eller flytta data till Azure Storage med AzCopy](https://aka.ms/azcopy)
- [Migrera stora data uppsättningar till Azure Blob Storage med AzReplicate (exempel program)](https://github.com/Azure/AzReplicate/tree/master/)
