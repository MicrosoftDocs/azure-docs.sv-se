---
title: Felsöka Dual-Protocol-volymer för Azure NetApp Files | Microsoft Docs
description: Beskriver fel meddelanden och lösningar som kan hjälpa dig att felsöka problem med dubbla protokoll för Azure NetApp Files.
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
ms.openlocfilehash: 0ae7e8f745a91e080d12a47271057ed90f9bc835
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134338"
---
# <a name="troubleshoot-dual-protocol-volumes"></a>Felsöka volymer med dubbla protokoll

Den här artikeln beskriver lösningar på fel villkor som du kan ha när du skapar eller hanterar dubbla protokoll volymer.

## <a name="error-conditions-and-resolutions"></a>Fel tillstånd och lösningar

|     Fel tillstånd    |     Lösning    |
|-|-|
| Det gick inte att skapa dubbla protokoll med felet `This Active Directory has no Server root CA Certificate` .    |     Om det här felet uppstår när du skapar en dual-Protocol-volym kontrollerar du att rot certifikat utfärdarens certifikat har laddats upp i ditt NetApp-konto.    |
| Det gick inte att skapa dubbla protokoll med felet `Failed to validate LDAP configuration, try again after correcting LDAP configuration` .    |  Det kan hända att ingen pekare (PTR) av AD host-datorn saknas på DNS-servern. Du måste skapa en zon för omvänd sökning på DNS-servern och sedan lägga till en PTR-post för AD host-datorn i den zonen för omvänd sökning. <br> Anta till exempel att du använder AD-datorns IP-adress `1.1.1.1` , värd namnet för AD-datorn (som hittas med hjälp av `hostname` kommandot) `AD1` och domän namnet är `contoso.com` .  PTR-posten som läggs till i zonen för omvänd sökning ska vara `1.1.1.1`  ->  `contoso.com` .   |
| Det gick inte att skapa dubbla protokoll med felet `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 1.1.1.1, port 88 using TCP\\n**[ 950] FAILURE` . |  Det här felet anger att AD-lösenordet är fel när Active Directory är ansluten till NetApp-kontot. Uppdatera AD-anslutningen med rätt lösen ord och försök igen. |
| Det gick inte att skapa dubbla protokoll med felet `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` . |   Det här felet anger att DNS inte kan kontaktas. Orsaken kan vara att DNS-IP är felaktigt eller att det finns ett nätverks problem. Kontrol lera DNS-IP-adressen som angetts i AD-anslutning och kontrol lera att IP-adressen är korrekt. <br> Kontrol lera också att AD och volymen finns i samma region och i samma VNet. Om de finns i olika virtuella nätverk kontrollerar du att VNet-peering har upprättats mellan de två virtuella nätverk.|
| Behörigheten nekas vid montering av en dual-Protocol-volym. | En dual-Protocol-volym stöder både NFS-och SMB-protokollen.  När du försöker komma åt den monterade volymen på UNIX-systemet försöker systemet mappa den UNIX-användare som du använder till en Windows-användare. Om ingen mappning hittas inträffar felet "behörighet nekad". <br> Den här situationen gäller även när du använder rot användaren för åtkomst. <br> För att undvika problemet "behörighet nekad" kontrollerar du att Windows Active Directory innehåller `pcuser` innan du ansluter till monterings punkten. Om du lägger till `pcuser` efter att du har påträffat problemet "behörighet nekad" väntar du 24 timmar på att cacheposten rensas innan du försöker igen. |

## <a name="next-steps"></a>Nästa steg  

* [Skapa en volym med dubbla protokoll](create-volumes-dual-protocol.md)
* [Konfigurera en NFS-klient för Azure NetApp Files](configure-nfs-clients.md)
