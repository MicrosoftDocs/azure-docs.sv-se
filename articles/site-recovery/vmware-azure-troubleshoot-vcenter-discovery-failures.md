---
title: Felsök fel i VMware vCenter-identifiering i Azure Site Recovery
description: Den här artikeln beskriver hur du felsöker VMware vCenter Discovery-fel i Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: 1a8471305af93194ccae7b0928685e10d4d64726
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92366672"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>Felsöka vCenter Server identifierings fel

Den här artikeln hjälper dig att felsöka problem som inträffar på grund av fel i VMware vCenter-identifiering.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Icke-numeriska värden i egenskapen maxSnapShots

I tidigare versioner än 9,20 kopplas vCenter ned när det hämtar ett icke-numeriskt värde för egenskapen Property  `snapshot.maxSnapShots` på en virtuell dator.

Det här problemet identifieras av fel-ID 95126.

```output
ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
System.FormatException: Input string was not in a correct format.
    at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
    at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
    at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
```

Så här löser du problemet:

- Identifiera den virtuella datorn och ange värdet till ett numeriskt värde (Redigera inställningar för virtuell dator i vCenter).

Eller

- Uppgradera konfigurations servern till version 9,20 eller senare.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problem med proxykonfigurationen för vCenter-anslutning

vCenter-identifieringen följer systemets standardinställningar för proxy som kon figurer ATS av system användaren. Den DRA-tjänsten följer proxyinställningarna från användaren under installationen av konfigurations servern med hjälp av installations programmet för installations program eller-ägg. 

I allmänhet används proxyn för att kommunicera med offentliga nätverk. till exempel att kommunicera med Azure. Om proxyservern har kon figurer ATS och vCenter är i en lokal miljö kan den inte kommunicera med DRA.

Följande situationer inträffar när det här problemet uppstår:

- VCenter-servern \<vCenter> kan inte nås på grund av felet: fjärrservern returnerade ett fel: (503) servern är inte tillgänglig
- Det går inte att hitta vCenter-servern på \<vCenter> grund av felet: fjärrservern returnerade ett fel: det går inte att ansluta till fjärrservern.
- Det går inte att ansluta till vCenter/ESXi-servern.

Så här löser du problemet:

Ladda ned [PsExec-verktyget](/sysinternals/downloads/psexec). 

Använd PsExec-verktyget för att få åtkomst till system användar kontexten och ta reda på om proxyservern har kon figurer ATS. Du kan sedan lägga till vCenter i listan över undantag med följande procedurer.

För konfiguration av identifierings-proxy:

1. Öppna IE i system användar kontext med PsExec-verktyget.
    
    PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe"

2. Ändra proxyinställningarna i Internet Explorer för att kringgå vCenter-IP-adressen.
3. Starta om tmanssvc-tjänsten.

För att DRA en konfiguration:

1. Öppna en kommando tolk och öppna mappen Microsoft Azure Site Recovery Provider.
 
    **CD C:\Program Files\Microsoft Azure Site Recovery Provider**

3. Kör följande kommando i kommandotolken.
   
   **DRCONFIGURATOR.EXE/Configure/AddBypassUrls [IP-adress/FQDN för vCenter Server som anges vid tidpunkten för att lägga till vCenter]**

4. Starta om tjänsten för att DRA providern.

## <a name="next-steps"></a>Nästa steg

[Hantera konfigurations servern för haveri beredskap för virtuella VMware-datorer](./vmware-azure-manage-configuration-server.md#refresh-configuration-server)