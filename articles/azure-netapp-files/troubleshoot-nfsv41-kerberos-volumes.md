---
title: Felsöka problem med NFSv 4.1 Kerberos-volymer för Azure NetApp Files | Microsoft Docs
description: Beskriver fel meddelanden och lösningar som kan hjälpa dig att felsöka NFSv 4.1 Kerberos Volume-problem för Azure NetApp Files.
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
ms.date: 01/12/2021
ms.author: b-juche
ms.openlocfilehash: 638607da02b1db4842cdc04f86a4fed1860c243f
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134321"
---
# <a name="troubleshoot-nfsv41-kerberos-volume-issues"></a>Felsök problem med NFSv 4.1 Kerberos-volymer 

Den här artikeln beskriver lösningar på fel villkor som du kan ha när du skapar eller hanterar NFSv 4.1 Kerberos-volymer. 

## <a name="error-conditions-and-resolutions"></a>Fel tillstånd och lösningar

|     Fel tillstånd    |     Lösningar    |
|-|-|
|`Error allocating volume - Export policy rules does not match kerberosEnabled flag` | Azure NetApp Files stöder inte Kerberos för NFSv3-volymer. Kerberos stöds endast för NFSv 4.1-protokollet.  |
|`This NetApp account has no configured Active Directory   connections`  |  Konfigurera Active Directory för NetApp-kontot med fält **KDC IP** och **AD server namn**. Mer information finns i [konfigurera Azure Portal](configure-kerberos-encryption.md#configure-the-azure-portal) . |
|`Mismatch between KerberosEnabled flag value and ExportPolicyRule's access type parameter values.`  | Azure NetApp Files stöder inte konvertering av en enkel NFSv 4.1-volym till Kerberos NFSv 4.1-volym och vice versa. |
|`mount.nfs: access denied by server when mounting volume <SMB_SERVER_NAME-XXX.DOMAIN_NAME>/<VOLUME_NAME>` <br>  Exempel: `smb-test-64d9.contoso.com:/nfs41-vol101` | <ol><li> Se till att A/PTR-posterna är korrekt konfigurerade och finns i Active Directory för Server namnet `smb-test-64d9.contoso.com` . <br> I NFS-klienten, om `nslookup` av `smb-test-64d9.contoso.com` matchar IP-IP1 (det vill säga `10.1.1.68` ), `nslookup` måste IP1 matcha till endast en post (dvs. `smb-test-64d9.contoso.com` ). `nslookup` av IP1 *får* inte matchas till flera namn. </li>  <li>Ange AES-256 för NFS-dator kontot av typen `NFS-<Smb NETBIOS NAME>-<few random characters>` på AD med antingen PowerShell eller användar gränssnittet. <br> Exempel på kommandon: <ul><li>`Set-ADComputer <NFS_MACHINE_ACCOUNT_NAME> -KerberosEncryptionType AES256` </li><li>`Set-ADComputer NFS-SMB-TEST-64 -KerberosEncryptionType AES256` </li></ul> </li> <li>Se till att tiden för NFS-klienten, AD och Azure NetApp Files Storage-programvaran är synkroniserad med varandra och är inom ett intervall om fem minuter. </li>  <li>Hämta Kerberos-biljetten på NFS-klienten med hjälp av kommandot `kinit <administrator>` .</li> <li>Minska NFS-klientens värdnamn till färre än 15 tecken och utför sfär kopplingen igen. </li><li>Starta om NFS-klienten och `rpcgssd` tjänsten på följande sätt. Kommandot kan variera beroende på vilket operativ system du har.<br> RHEL 7: <br> `service nfs restart` <br> `service rpcgssd restart` <br> CentOS 8: <br> `systemctl enable nfs-client.target && systemctl start nfs-client.target` <br> Ubuntu <br> (Starta om `rpc-gssd` tjänsten.) <br> `sudo systemctl start rpc-gssd.service` </ul>| 
|`mount.nfs: an incorrect mount option was specified`   | Problemet kan vara relaterat till NFS-klientens problem. Starta om NFS-klienten.    |
|`Hostname lookup failed`   | Du måste skapa en zon för omvänd sökning på DNS-servern och sedan lägga till en PTR-post för AD host-datorn i den zonen för omvänd sökning. <br> Anta till exempel att du använder AD-datorns IP-adress `10.1.1.4` , värd namnet för AD-datorn (som hittas med hjälp av hostname-kommandot) `AD1` och domän namnet är `contoso.com` . PTR-posten som läggs till i zonen för omvänd sökning ska vara `10.1.1.4 -> AD1.contoso.com` . |
|`Volume creation fails due to unreachable DNS server`  | Det finns två möjliga lösningar: <br> <ul><li> Det här felet anger att DNS inte kan kontaktas. Orsaken kan vara en felaktig DNS IP-adress eller ett nätverks problem. Kontrol lera DNS-IP-adressen som angetts i AD-anslutning och kontrol lera att IP-adressen är korrekt. </li> <li> Kontrol lera att AD och volymen finns i samma region och i samma VNet. Om de finns i olika virtuella nätverk kontrollerar du att VNet-peering har upprättats mellan de två virtuella nätverk. </li></ul> |
|NFSv 4.1 det går inte att skapa Kerberos-volymen med ett fel som liknar följande exempel: <br> `Failed to enable NFS Kerberos on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". Failed to bind service principal name on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". SecD Error: server create fail join user auth.` |KDC-IP: en är fel och Kerberos-volymen har skapats. Uppdatera KDC-IP: en med rätt adress. <br> När du har uppdaterat KDC-IP: en så försvinner inte felet. Du måste återskapa volymen. |

## <a name="next-steps"></a>Nästa steg  

* [Konfigurera NFSv 4.1 Kerberos-kryptering för Azure NetApp Files](configure-kerberos-encryption.md)
