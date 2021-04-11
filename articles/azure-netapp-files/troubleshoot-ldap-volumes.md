---
title: Felsöka problem med LDAP-volymer för Azure NetApp Files | Microsoft Docs
description: Beskriver lösningar på fel villkor som du kan ha när du konfigurerar LDAP-volymer för Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: eea3f691bc6d91948dc73b4a02c89abfac12d384
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106499029"
---
# <a name="troubleshoot-ldap-volume-issues"></a>Felsöka problem med LDAP-volymer

I den här artikeln beskrivs lösningar på fel villkor som du kan ha när du konfigurerar LDAP-volymer.

## <a name="errors-and-resolutions-for-ldap-volumes"></a>Fel och lösningar för LDAP-volymer

|     Fel tillstånd    |     Lösningar    |
|-|-|
| Fel vid skapande av en SMB-volym med ldapEnabled som sant: <br> `Error Message: ldapEnabled option is only supported with NFS protocol volume. ` | Du kan inte skapa en SMB-volym med LDAP aktiverat. <br> Skapa SMB-volymer med LDAP inaktiverat. |
| Fel vid uppdatering av värdet för parametern ldapEnabled för en befintlig volym: <br> `Error Message: ldapEnabled parameter is not allowed to update` |  Du kan inte ändra inställningen för LDAP-alternativ när du har skapat en volym. <br> Uppdatera inte alternativ inställningen för LDAP på en volym som skapats. Mer information finns i [Konfigurera tillägg för LDAP med utökade grupper för NFS-volymer](configure-ldap-extended-groups.md) . |
| Fel vid skapande av en LDAP-aktiverad NFS-volym: <br> `Could not query DNS server` <br> `Sample error message:` <br> `"log": time="2020-10-21 05:04:04.300" level=info msg=Res method=GET url=/v2/Volumes/070d0d72-d82c-c893-8ce3-17894e56cea3  x-correlation-id=9bb9e9fe-abb6-4eb5-a1e4-9e5fbb838813 x-request-id=c8032cb4-2453-05a9-6d61-31ca4a922d85 xresp="200:  {\"created\":\"2020-10-21T05:02:55.000Z\",\"lifeCycleState\":\"error\",\"lifeCycleStateDetails\":\"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available.\",\"name\":\"smb1\",\"ownerId\ \":\"8c925a51-b913-11e9-b0de-9af5941b8ed0\",\"region\":\"westus2stage\",\"volumeId\":\"070d0d72-d82c-c893-8ce3-` |  Felet beror på att DNS inte kan kontaktas. <br> <ul><li> Kontrol lera att du har konfigurerat rätt plats (plats definitions område) för Azure NetApp Files. </li><li> Orsaken till att DNS inte kan kontaktas kan vara felaktig DNS IP-adress eller nätverks problem. Kontrol lera DNS-IP-adressen som anges i AD-anslutningen för att kontrol lera att den är korrekt. </li><li> Kontrol lera att AD och volymen finns i samma region och samma VNet. Om de finns i olika virtuella nätverk kontrollerar du att VNet-peering har upprättats mellan de två virtuella nätverk.</li></ul> |
| Fel vid skapande av volym från en ögonblicks bild: <br> `Aggregate does not exist` | Azure NetApp Files har inte stöd för etablering av en ny, LDAP-aktiverad volym från en ögonblicks bild som tillhör en LDAP-inaktive rad volym. <br> Försök att skapa en ny LDAP-inaktive rad volym från den aktuella ögonblicks bilden. |

## <a name="next-steps"></a>Nästa steg  

* [Konfigurera lägger till LDAP med utökade grupper för NFS-volym åtkomst](configure-ldap-extended-groups.md)
* [Skapa en NFS-volym för Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Skapa en NFSv3-och SMB-volym (Dual-Protocol) för Azure NetApp Files](create-volumes-dual-protocol.md)