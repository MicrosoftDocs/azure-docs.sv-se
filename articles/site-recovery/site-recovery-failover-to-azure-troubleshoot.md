---
title: Felsöka redundansväxling till Azure-fel | Microsoft Docs
description: I den här artikeln beskrivs olika sätt att felsöka vanliga fel vid växling till Azure
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2020
ms.author: mayg
ms.openlocfilehash: 8fe21ce5b92d672a2e025e0b45b8cbaea5951a8b
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043911"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>Felsöka fel vid redundansväxling av virtuell VMware-dator eller fysisk dator till Azure

Du kan få ett av följande fel när du gör en redundansväxling av en virtuell dator till Azure. Om du vill felsöka använder du de steg som beskrivs för varje fel tillstånd.

## <a name="failover-failed-with-error-id-28031"></a>Redundansväxlingen misslyckades med fel-ID 28031

Site Recovery kunde inte skapa en misslyckad virtuell dator i Azure. Det kan bero på någon av följande orsaker:

* Det finns inte tillräckligt med tillgänglig kvot för att skapa den virtuella datorn: du kan kontrol lera den tillgängliga kvoten genom att gå till prenumerations > användning och kvoter. Du kan öppna en [ny supportbegäran](https://aka.ms/getazuresupport) för att öka kvoten.

* Du försöker redundansväxla virtuella datorer med olika storleks familjer i samma tillgänglighets uppsättning. Se till att du väljer samma storleks familj för alla virtuella datorer i samma tillgänglighets uppsättning. Ändra storlek genom att gå till beräknings-och nätverks inställningarna för den virtuella datorn och försök sedan redundansväxla igen.

* Det finns en princip för prenumerationen som förhindrar att en virtuell dator skapas. Ändra principen så att den tillåter att en virtuell dator skapas och försök sedan att redundansväxla igen.

## <a name="failover-failed-with-error-id-28092"></a>Redundansväxlingen misslyckades med fel-ID 28092

Site Recovery kunde inte skapa ett nätverks gränssnitt för den misslyckade över virtuella datorn. Se till att du har tillräcklig tillgänglig kvot för att skapa nätverks gränssnitt i prenumerationen. Du kan kontrol lera den tillgängliga kvoten genom att gå till prenumerations > användning och kvoter. Du kan öppna en [ny supportbegäran](https://aka.ms/getazuresupport) för att öka kvoten. Om du har tillräcklig kvot kan detta vara ett tillfälligt problem. försök igen. Om problemet kvarstår även efter försöken lämnar du en kommentar i slutet av det här dokumentet.  

## <a name="failover-failed-with-error-id-70038"></a>Redundansväxlingen misslyckades med fel-ID 70038

Site Recovery kunde inte skapa en misslyckad över klassisk virtuell dator i Azure. Det kan bero på att:

* En av resurserna, till exempel ett virtuellt nätverk som krävs för att den virtuella datorn ska skapas, finns inte. Skapa det virtuella nätverket enligt inställningarna för beräkning och nätverk på den virtuella datorn eller ändra inställningen till ett virtuellt nätverk som redan finns och försök sedan redundansväxla igen.

## <a name="failover-failed-with-error-id-170010"></a>Redundansväxlingen misslyckades med fel-ID 170010

Site Recovery kunde inte skapa en misslyckad virtuell dator i Azure. Det kan bero på att en intern aktivitet i hydrering misslyckades för den lokala virtuella datorn.

För att kunna ta upp en dator i Azure kräver Azure-miljön att några av driv rutinerna är i Start tillstånd och tjänster som DHCP ska vara i Autostart-tillstånd. Hydrering-aktivitet, vid tidpunkten för redundansväxlingen, konverterar start typen för **ATAPI-, intelide-, storflt-, VMBus-och storvsc-drivrutiner** till Start. Den konverterar också start typen för några tjänster som DHCP till Autostart. Den här aktiviteten kan inte utföras på grund av specifika problem i miljön. 

Om du vill ändra start typen för driv rutiner manuellt för **Windows gäst operativ system** följer du stegen nedan:

1. [Hämta](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) skriptet No-hydrering och kör det på följande sätt. Det här skriptet kontrollerar om den virtuella datorn kräver hydrering.

    `.\Script-no-hydration.ps1`

    Det ger följande resultat om hydrering krävs:

    ```output
    REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

    This system doesn't meet no-hydration requirement.
    ```

    Om den virtuella datorn uppfyller hydrering-kravet ger skriptet resultatet "det här systemet uppfyller inga krav för hydrering". I det här fallet är alla driv rutiner och tjänster i det tillstånd som krävs av Azure och hydrering på den virtuella datorn inte obligatoriska.

2. Kör skriptet No-hydrering-set enligt följande om den virtuella datorn inte uppfyller kravet på hydrering.

    `.\Script-no-hydration.ps1 -set`
    
    Detta konverterar driv Rutinens starttyp och ger resultatet som nedan:

    ```output
    REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

    Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0

    This system is now no-hydration compatible.
    ```

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Det går inte att ansluta/RDP/SSH till den misslyckade över virtuella datorn på grund av en nedtonad anslutnings knapp på den virtuella datorn

Detaljerad information om hur du felsöker RDP-problem finns i vår dokumentation [här](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection).

Detaljerad information om hur du felsöker SSH-problem finns i vår dokumentation [här](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection).

Om knappen **Anslut** på den misslyckade virtuella datorn i Azure är nedtonad och du inte är ansluten till Azure via en Express Route eller plats-till-plats-VPN-anslutning, sedan

1. Gå till **virtuella dator**  >  **nätverk**, klicka på namnet på det nödvändiga nätverks gränssnittet.  ![Skärm bild som visar sidan nätverk för en virtuell dator med det angivna nätverks gränssnitts namnet.](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Navigera till **IP-konfigurationer** och klicka sedan på fältet namn för obligatorisk IP-konfiguration. ![Skärm bild som visar sidan I P-konfigurationer för nätverks gränssnittet med namnet I P-konfigurationen valt.](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Om du vill aktivera offentlig IP-adress klickar du på **Aktivera**. ![Aktivera IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Klicka på **Konfigurera nödvändiga inställningar**  >  **Skapa ny**. ![Skapa ny](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Ange namnet på den offentliga adressen, Välj standard alternativen för **SKU** och **tilldelning** och klicka sedan på **OK**.
6. Spara ändringarna genom att klicka på **Spara**.
7. Stäng panelerna och navigera till **översikts** avsnittet på den virtuella datorn för att ansluta/RDP.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Det går inte att ansluta/RDP/SSH-Connect-anslutning knappen tillgänglig

Om knappen **Anslut** på den misslyckade virtuella datorn i Azure är tillgänglig (inte nedtonad) kontrollerar du **startdiagnostik** på den virtuella datorn och söker efter fel som beskrivs i [den här artikeln](/troubleshoot/azure/virtual-machines/boot-diagnostics).

1. Om den virtuella datorn inte har startats kan du försöka redundansväxla till en äldre återställnings punkt.
2. Om programmet i den virtuella datorn inte är igång kan du försöka redundansväxla till en programkonsekvent återställnings punkt.
3. Om den virtuella datorn är domänansluten kontrollerar du att domänkontrollanten fungerar korrekt. Detta kan göras genom att följa nedanstående steg:

    a. Skapa en ny virtuell dator i samma nätverk.

    b.  Se till att det går att ansluta till samma domän som den misslyckade över virtuella datorn förväntas komma i.

    c. Om domänkontrollanten **inte** fungerar korrekt kan du försöka logga in på den virtuella datorn med ett lokalt administratörs konto.
4. Om du använder en anpassad DNS-server måste du kontrol lera att den kan kontaktas. Detta kan göras genom att följa nedanstående steg:

    a. Skapa en ny virtuell dator i samma nätverk och

    b. Kontrol lera om den virtuella datorn kan göra namn matchning med hjälp av den anpassade DNS-servern

>[!Note]
>Att aktivera andra inställningar än startdiagnostik kräver att Azure VM-agenten installeras på den virtuella datorn före redundansväxlingen

## <a name="unable-to-open-serial-console-after-failover-of-a-uefi-based-machine-into-azure"></a>Det går inte att öppna en seriell konsol efter redundansväxlingen av en UEFI-baserad dator till Azure

Om du kan ansluta till datorn med RDP men inte kan öppna en serie konsol följer du stegen nedan:

* Om datorns operativ system är Red Hat eller Oracle Linux 7. */8.0 kör du följande kommando på den virtuella Azure-datorn med rot behörigheter. Starta om den virtuella datorn efter kommandot.

  ```console
  grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
  ```

* Om datorns operativ system är CentOS 7. * kör du följande kommando på den virtuella Azure-datorn med rot behörigheter. Starta om den virtuella datorn efter kommandot.

  ```console
  grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg
  ```

## <a name="unexpected-shutdown-message-event-id-6008"></a>Oväntat avslutnings meddelande (händelse-ID 6008)

Om du får ett oväntat avstängnings meddelande på den återställda virtuella datorn när du startar en virtuell Windows-dator efter fel, indikerar det att ett stängnings tillstånd för virtuell dator inte har registrerats i återställnings punkten som används för redundans. Detta inträffar när du återställer till en punkt när den virtuella datorn inte har stängts helt.

Detta är vanligt vis inte en orsak till betänkligheter och kan vanligt vis ignoreras för oplanerad redundans. Om redundansväxlingen är planerat kontrollerar du att den virtuella datorn är korrekt avstängd innan redundansväxlingen sker och ger tillräckligt med tid för väntande replikeringsdata på plats att skickas till Azure. Använd sedan det **senaste** alternativet på sidan [redundans](site-recovery-failover.md#run-a-failover) så att alla väntande data på Azure bearbetas till en återställnings punkt som sedan används för redundans av virtuella datorer.

## <a name="unable-to-select-the-datastore"></a>Det går inte att välja data lagret

Det här problemet visas när du inte kan se data lagret i Azure-portalen när du försöker återaktivera skyddet av den virtuella dator som har en redundansväxling. Detta beror på att huvud målet inte identifieras som en virtuell dator under vCenter läggs till Azure Site Recovery.

Mer information om hur du återaktiverar en virtuell dator finns i [återskydda och återställa datorer till en lokal plats efter en redundansväxling till Azure](vmware-azure-reprotect.md).

Så här löser du problemet:

Skapa huvud målet i vCenter som hanterar käll datorn manuellt. Data lagret blir tillgängligt efter nästa vCenter-identifiering och uppdatering av infrastruktur resurser.

> [!Note]
> 
> Det kan ta upp till 30 minuter att slutföra identifiering och uppdatering av infrastruktur resurser. 

## <a name="linux-master-target-registration-with-cs-fails-with-a-tls-error-35"></a>Linux Master Target-registrering med CS Miss lyckas med ett TLS-fel 35 

Azure Site Recovery huvud mål registreringen med konfigurations servern Miss lyckas på grund av att den autentiserade proxyn har Aktiver ATS på huvud mål servern. 
 
Det här felet anges av följande strängar i installations loggen: 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

Så här löser du problemet:
 
1. Öppna en kommando tolk på den virtuella konfigurations servern och verifiera proxyinställningarna med följande kommandon:

    katt/etc/Environment ECHO $http _proxy ECHO $https _proxy 

2. Om utdata från föregående kommandon visar att antingen http_proxy-eller https_proxys inställningarna har definierats använder du någon av följande metoder för att avblockera kommunikationen mellan huvud målet med konfigurations servern:
   
   - Ladda ned [PsExec-verktyget](/sysinternals/downloads/psexec).
   - Använd verktyget för att få åtkomst till system användar kontexten och ta reda på om proxyservern har kon figurer ATS. 
   - Om proxyservern har kon figurer ATS öppnar du IE i en system användar kontext med hjälp av PsExec-verktyget.
  
     **PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe"**

   - För att säkerställa att huvud mål servern kan kommunicera med konfigurations servern:
  
     - Ändra proxyinställningarna i Internet Explorer för att kringgå IP-adressen för huvud mål servern via proxyn.   
     Eller
     - Inaktivera proxyn på huvud mål servern. 


## <a name="next-steps"></a>Nästa steg
- Felsöka [RDP-anslutning till virtuell Windows-dator](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection)
- Felsök [ssh-anslutning till virtuella Linux-datorer](/troubleshoot/azure/virtual-machines/detailed-troubleshoot-ssh-connection)

Om du behöver mer hjälp kan du skicka din fråga på [sidan Microsoft Q&en fråga för Site Recovery](/answers/topics/azure-site-recovery.html) eller lämna en kommentar i slutet av det här dokumentet. Vi har en aktiv community som kan hjälpa dig.