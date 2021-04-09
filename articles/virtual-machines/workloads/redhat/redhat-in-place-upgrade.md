---
title: Uppgradering på plats av Red Hat Enterprise Linux avbildningar på Azure
description: Lär dig hur du utför en uppgradering på plats från Red Hat Enterprise 7. x-bilder till den senaste 8. x-versionen.
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 1be0904cc640eff5af7a77bba3abd6aa062991a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676062"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux uppgraderingar på plats

Den här artikeln innehåller anvisningar om hur du gör en uppgradering på plats från Red Hat Enterprise Linux (RHEL) 7 till Red Hat Enterprise Linux 8. Anvisningarna använder `leapp` verktyget i Azure. Under uppgraderingen på plats ersätts det befintliga RHEL 7-operativsystemet av RHEL 8-versionen.

>[!Note] 
> Erbjudanden för SQL Server på Red Hat Enterprise Linux stöder inte uppgraderingar på plats på Azure.

## <a name="what-to-expect-during-the-upgrade"></a>Vad som ska förväntas under uppgraderingen
Under uppgraderingen startas systemet om några gånger. Den slutliga omstarten uppgraderar den virtuella datorn till den senaste mindre versionen av RHEL 8. 

Uppgraderings processen kan ta var som helst från 20 minuter till 2 timmar. Den totala tiden beror på flera faktorer, till exempel storleken på den virtuella datorn och antalet paket som har installerats i systemet.

## <a name="preparations"></a>Form
Red Hat och Azure rekommenderar att du använder en uppgradering på plats för att överföra ett system till nästa högre version. 

Tänk på följande innan du påbörjar uppgraderingen. 

>[!Important] 
> Ta en ögonblicks bild av avbildningen innan du påbörjar uppgraderingen.

* Kontrol lera att du använder den senaste versionen av RHEL 7. För närvarande är den senaste versionen RHEL 7,9. Om du använder en låst version och inte kan uppgradera till RHEL 7,9 följer [du de här stegen för att växla till en icke-EUs (utökad uppdaterings support) lagrings plats](./redhat-rhui.md#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Kör följande kommando för att kontrol lera uppgraderingen och se om den kommer att slutföras. Kommandot ska generera */var/log/leapp/leapp-report.txt* -filen. Den här filen förklarar processen, vad som händer och om uppgraderingen är möjlig.

    >[!NOTE]
    > Använd rot kontot för att köra kommandona i den här artikeln. 

    ```bash
    leapp preupgrade --no-rhsm
    ```
* Se till att serie konsolen fungerar. Du använder den här konsolen för övervakning under uppgraderings processen.

* Aktivera SSH-rot åtkomst i */etc/ssh/sshd_config*:
    1. Öppna filen */etc/ssh/sshd_config*.
    1. Sök efter `#PermitRootLogin yes`.
    1. Ta bort nummer tecknet ( `#` ) för att ta bort kommentaren till strängen.

## <a name="upgrade-steps"></a>Uppgraderings steg

Följ dessa steg noggrant. Vi rekommenderar att du testar uppgraderingen på en test dator innan du provar den på produktions instanser.

1. Gör en `yum` uppdatering för att hämta de senaste klient paketen.
    ```bash
    yum update -y
    ```

1. Installera `leapp-client-package` .
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. I [Red Hat-portalen](https://access.redhat.com/articles/3664871)hämtar du filen *leapp-data. tar. gz* som innehåller *repomap.csv* och *pes-events.jspå*. Extrahera filen *leapp-data. tar. gz* .
    1. Hämta filen *leapp-data. tar. gz* .
    1. Extrahera innehållet och ta bort filen. Ange följande kommando:
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. Lägg till en `answers` fil för `leapp` .
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. Starta uppgraderingen.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  När `leapp upgrade` kommandot har slutförts startar du om systemet manuellt för att slutföra processen. Systemet är inte tillgängligt eftersom det startar om ett par gånger. Övervaka processen med hjälp av serie konsolen.

1.  Verifiera att uppgraderingen har slutförts.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. När uppgraderingen är klar tar du bort rot-SSH-åtkomst:
    1. Öppna filen */etc/ssh/sshd_config*.
    1. Sök efter `#PermitRootLogin yes`.
    1. Kommentera strängen genom att lägga till ett nummer tecken ( `#` ).

1. Starta om SSHD-tjänsten för att tillämpa ändringarna.
    ```bash
    systemctl restart sshd
    ```
## <a name="common-problems"></a>Vanliga problem

Följande fel inträffar vanligt vis när `leapp preupgrade` processen Miss lyckas eller om `leapp upgrade` processen Miss lyckas:

* **Fel**: inga matchningar hittades för följande inaktiverade plugin-mönster.

    ```plaintext
    STDERR:
    No matches found for the following disabled plugin patterns: subscription-manager
    Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
    ```

    **Lösning**: inaktivera plugin-programmet för prenumerations hanteraren. Inaktivera det genom att redigera filen */etc/yum/pluginconf.d/Subscription-Manager.conf* och ändra `enabled` till `enabled=0` .

    Felet uppstår när `yum` plugin-programmet för prenumerations hanteraren som har Aktiver ATS inte används för `PAYG` virtuella datorer.

* **Fel**: möjliga problem med fjärrinloggning med roten.

    Du kanske ser det här felet när det `leapp preupgrade` Miss lyckas:

    ```structured-text
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    
    Upgrade has been inhibited due to the following problems:
        1. Inhibitor: Possible problems with remote login using root account
    Consult the pre-upgrade report for details and possible remediation.
    
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    ```
    **Lösning**: Aktivera rot åtkomst i */etc/sshd_config*.

    Det här felet inträffar när grundläggande SSH-åtkomst inte är aktive rad i */etc/sshd_config*. Mer information finns i avsnittet om [förberedelser](#preparations) i den här artikeln. 


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Red Hat-bilder i Azure](./redhat-images.md).
* Läs mer om [Red Hats uppdaterings infrastruktur](./redhat-rhui.md).
* Läs mer om [RHEL BYOS-erbjudandet](./byos.md).
* Mer information om uppgraderings processerna för Red Hat på plats finns i [Uppgradera från RHEL 7 till RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index) i Red Hat-dokumentationen.
* Mer information om stöd principer för Red Hat för alla versioner av RHEL finns i [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata) i Red Hat-dokumentationen.