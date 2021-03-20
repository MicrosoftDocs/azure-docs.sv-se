---
title: VM-SKU:er som stöds för Azure Policy
description: 'En artikel som beskriver de VM-SKU: er (som stöds av Publisher, avbildnings erbjudande och avbildnings-SKU) som stöds för de inbyggda Azure-principer som tillhandahålls av säkerhets kopiering'
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 4658d1beffe707682e173491edea1eac0db9c811
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "82183627"
---
# <a name="supported-vm-skus-for-azure-policy"></a>VM-SKU:er som stöds för Azure Policy

Azure Backup tillhandahåller en inbyggd princip (med Azure Policy) som kan tilldelas till **alla virtuella Azure-datorer på en angiven plats i en prenumeration eller resurs grupp**. När den här principen tilldelas ett angivet omfång konfigureras alla nya virtuella datorer i det omfånget automatiskt för säkerhets kopiering till ett **befintligt valv på samma plats och i en prenumeration**. I tabellen nedan visas alla VM-SKU: er som stöds av den här principen.

## <a name="supported-vms"></a>Virtuella datorer som stöds *

**Princip namn:** Konfigurera säkerhets kopiering på virtuella datorer för en plats till ett befintligt centralt valv på samma plats

Avbildnings utgivare | Bild erbjudande | Bild-SKU
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2008 R2 SP (2008-R2-SP1-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 Data Center (2012-Data Center)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 Data Center (2012-datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 R2 Data Center (2012-R2-Data Center)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 R2 Data Center (2012-R2-Data Center-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Data Center (2016-Data Center)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Data Center-Server Core (2016-Data Center-Server-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Data Center-Server Core (2016-Datacenter-Server-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Data Center (2016-datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Data Center Server Core med behållare (2016-Data Center-med-containers)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Remote Desktop Session Host 2016 (2016-Data Center-with-RDSH)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Data Center (2019-Data Center)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Data Center Server Core (2019-Data Center-kärna)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Data Center Server Core (2019-Data Center-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Data Center Server Core med behållare (2019-Data Center Core-with-containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Data Center Server Core med behållare (2019-Data Center-Core-with-containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Data Center (2019-datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Data Center med behållare (2019-Data Center-med-containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Data Center med behållare (2019-datacenter-with-containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Data Center (zh-cn) (2019-datacenter-zhcn)
MicrosoftWindowsServer | WindowsServerSemiAnnual | Data Center Core – 1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Data Center Core-1709-med-containers-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Data Center Core-1803-med-containers-smalldisk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | Alla avbildnings-SKU: er
MicrosoftSQLServer | SQL2016SP1 – WS2016 | Alla avbildnings-SKU: er
MicrosoftSQLServer | SQL2016 – WS2016 | Alla avbildnings-SKU: er
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | Alla avbildnings-SKU: er
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | Alla avbildnings-SKU: er
MicrosoftSQLServer | SQL2016-WS2012R2 | Alla avbildnings-SKU: er
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Alla avbildnings-SKU: er
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | Alla avbildnings-SKU: er
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | Alla avbildnings-SKU: er
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | Alla avbildnings-SKU: er
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | Alla avbildnings-SKU: er
MicrosoftRServer | MLServer – WS2016 | Alla avbildnings-SKU: er
MicrosoftVisualStudio | VisualStudio | Alla avbildnings-SKU: er
MicrosoftVisualStudio | Windows | Alla avbildnings-SKU: er
MicrosoftDynamicsAX | Dynamics | För-REQ-AX7-Onebox behållaravbildningen-U8
Microsoft-Ads | Windows-data-science-VM | Alla avbildnings-SKU: er
MicrosoftWindowsDesktop | Windows-10 | Alla avbildnings-SKU: er
Redhat | RHEL | 6,7, 6,8, 6,9, 6,10, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7
Redhat | RHEL – SAP-HANA | 6,7, 7,2, 7,3
SUSE | SLES | 12. X
SUSE | SLES – HPC | 12. X
SUSE | SLES-HPC-Priority | 12. X
SUSE | SLES – SAP | 12. X
SUSE | SLES – SAP-BYOS | 12. X
SUSE | SLES-Priority | 12. X
SUSE | SLES – BYOS | 12. X
SUSE | SLES – SAPCAL | 12. X
SUSE | SLES-Standard | 12. X
Canonical | UbuntuServer | 14.04.0 – LTS
Canonical | UbuntuServer | 14.04.1 – LTS
Canonical | UbuntuServer | 14.04.2-LTS
Canonical | UbuntuServer | 14.04.3 – LTS
Canonical | UbuntuServer | 14.04.4-LTS
Canonical | UbuntuServer | 14.04.5 – DAGLIGA LTS
Canonical | UbuntuServer | 14.04.5-LTS
Canonical | UbuntuServer | 16,04 – DAGLIGEN – LTS
Canonical | UbuntuServer | 16.04-LTS
Canonical | UbuntuServer | 16.04.0 – LTS
Canonical | UbuntuServer | 18,04 – DAGLIGEN – LTS
Canonical | UbuntuServer | 18,04 – LTS
Oracle | Oracle-Linux | 6,8, 6,9, 6,10, 7,3, 7,4, 7,5, 7,6
OpenLogic | CentOS | 6. X, 7. X
OpenLogic | CentOS – LVM | 6. X, 7. X
OpenLogic | CentOS – IOV | 6. X, 7. X
Cloudera | Cloudera-CentOS-OS | 6,9
