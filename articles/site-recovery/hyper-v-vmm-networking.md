---
title: Konfigurera IP-adresser efter redundansväxling till en sekundär plats med Azure Site Recovery
description: Beskriver hur du konfigurerar IP-adresser för att ansluta till virtuella datorer på en sekundär lokal plats efter haveri beredskap och redundans med Azure Site Recovery.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 1169748d7dae9990728dff91782a20d42e3c860b
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580259"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Konfigurera IP-adressering för att ansluta till en sekundär lokal plats efter redundans

När du har växlat över virtuella Hyper-V-datorer i System Center Virtual Machine Manager-moln (VMM) till en sekundär plats måste du kunna ansluta till de virtuella replik datorerna. Den här artikeln hjälper dig att göra detta. 

## <a name="connection-options"></a>Anslutnings alternativ

Efter redundansväxlingen finns det ett par olika sätt att hantera IP-adresser för virtuella replik datorer: 

- **Behåll samma IP-adress efter redundansväxlingen**: i det här scenariot har den REPLIKERADE virtuella datorn samma IP-adress som den primära virtuella datorn. Detta fören klar nätverksrelaterade problem efter redundansväxlingen, men kräver en del infrastruktur arbete.
- **Använd en annan IP-adress efter redundans**: i det här scenariot hämtar den virtuella datorn en ny IP-adress efter redundansväxlingen. 
 

## <a name="retain-the-ip-address"></a>Behåll IP-adressen

Om du vill behålla IP-adresserna från den primära platsen efter redundansväxlingen till den sekundära platsen kan du:

- Distribuera ett utsträckt undernät mellan primära och sekundära platser.
- Utför en fullständig undernäts växling från den primära till den sekundära platsen. Du måste uppdatera vägar för att ange den nya platsen för IP-adresserna.


### <a name="deploy-a-stretched-subnet"></a>Distribuera ett utsträckt undernät

I en utsträckt konfiguration är under nätet tillgängligt samtidigt på både den primära och den sekundära platsen. När du flyttar en dator och dess IP-adress (Layer 3) till den sekundära platsen i ett utsträckt undernät dirigerar nätverket trafiken automatiskt till den nya platsen. 

- Från ett lager 2-perspektiv (data länk lager) behöver du nätverks utrustning som kan hantera ett utsträckt VLAN.
- Genom att sträcka ut VLAN utökar den potentiella fel domänen till båda platserna. Det blir en enskild felpunkt. I ett sådant scenario kanske du inte kan isolera en incident, till exempel en sändning storm. 


### <a name="fail-over-a-subnet"></a>Redundansväxla ett undernät

Du kan växla över hela under nätet för att få fördelarna med det utsträckta under nätet, utan att faktiskt vidga det. I den här lösningen är ett undernät tillgängligt på käll-eller mål platsen, men inte i båda samtidigt.

- Om du vill underhålla IP-adressutrymmet i händelse av en redundansväxling kan du program mässigt ordna för router-infrastrukturen för att flytta undernät från en plats till en annan.
- När en redundansväxling inträffar flyttas undernät med tillhör ande virtuella datorer.
- Den främsta nack delen med den här metoden är att om ett problem uppstår måste du flytta hela under nätet.

#### <a name="example"></a>Exempel

Här är ett exempel på en komplett undernäts växling vid fel. 

- Före redundansväxlingen har den primära platsen program som körs i under nätet 192.168.1.0/24.
- Under redundansväxlingen växlar alla virtuella datorer i det här under nätet till den sekundära platsen och behåller sina IP-adresser. 
- Vägar mellan alla platser måste ändras för att återspegla att alla virtuella datorer i under nätet 192.168.1.0/24 nu har flyttats till den sekundära platsen.

Följande grafik illustrerar under näten före och efter redundansväxlingen.


**Före redundans**

![Diagram som visar under näten före redundansväxlingen.](./media/hyper-v-vmm-networking/network-design2.png)

**Efter redundans**

![Diagram som visar under näten efter redundansväxlingen.](./media/hyper-v-vmm-networking/network-design3.png)

Efter redundansväxlingen allokerar Site Recovery en IP-adress för varje nätverks gränssnitt på den virtuella datorn. Adressen tilldelas från den statiska IP-adresspoolen i det aktuella nätverket, för varje VM-instans.

- Om IP-adresspoolen på den sekundära platsen är samma som på käll platsen Site Recovery allokerar samma IP-adress (från den virtuella käll datorn) till den virtuella replik datorn. IP-adressen är reserverad i VMM, men den har inte angetts som IP-adress för redundans på Hyper-V-värden. IP-adressen för redundans på en Hyper-v-värd anges precis innan redundansväxlingen.
- Om samma IP-adress inte är tillgänglig allokerar Site Recovery en annan tillgänglig IP-adress från poolen.
- Om virtuella datorer använder DHCP hanterar Site Recovery inte IP-adresserna. Du måste kontrol lera att DHCP-servern på den sekundära platsen kan allokera adresser från samma intervall som käll platsen.

### <a name="validate-the-ip-address"></a>Verifiera IP-adressen

När du har aktiverat skydd för en virtuell dator kan du använda följande exempel skript för att verifiera den adress som tilldelats den virtuella datorn. Den här IP-adressen har angetts som IP-adress för redundans och tilldelas den virtuella datorn vid tidpunkten för redundans:

```powershell
$vm = Get-SCVirtualMachine -Name <VM_NAME>
$na = $vm[0].VirtualNetworkAdapters>
$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
$ip.address
```

## <a name="use-a-different-ip-address"></a>Använd en annan IP-adress

I det här scenariot ändras IP-adresserna för de virtuella datorer som växlar över. Nack delarna med den här lösningen är underhållet som krävs.  DNS-och cache-poster kan behöva uppdateras. Detta kan resultera i stillestånds tid, vilket kan minskas på följande sätt:

- Använd låga TTL-värden för intranät program.
- Använd följande skript i en Site Recovery återställnings plan för en tids uppdatering av DNS-servern. Du behöver inte skriptet om du använder dynamisk DNS-registrering.

    ```powershell
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>Exempel 

I det här exemplet har vi olika IP-adresser för primära och sekundära platser och det finns en tredje plats från vilken program som finns på den primära platsen eller återställnings platsen kan nås.

- Före redundans är apparna värdbaserade undernät 192.168.1.0/24 på den primära platsen.
- Efter redundansväxlingen konfigureras appar i under nätet 172.16.1.0/24 på den sekundära platsen.
- Alla tre platserna har åtkomst till varandra.
- Efter redundansväxlingen kommer appar att återställas i återställnings under nätet.
- I det här scenariot behöver du inte redundansväxla hela under nätet och inga ändringar krävs för att konfigurera om VPN-eller nätverks vägar. Redundansväxlingen och vissa DNS-uppdateringar kontrollerar att programmen är tillgängliga.
- Om DNS är konfigurerat för att tillåta dynamiska uppdateringar registrerar de virtuella datorerna sig själva med den nya IP-adressen när de startar efter redundansväxlingen.

**Före redundans**

![Diagram som visar olika IP-adresser före redundans.](./media/hyper-v-vmm-networking/network-design10.png)

**Efter redundans**

![Diagram som visar olika IP-adresser efter redundansväxlingen.](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Nästa steg

[Köra en redundansväxling](hyper-v-vmm-failover-failback.md)

