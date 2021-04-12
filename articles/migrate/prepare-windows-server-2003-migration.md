---
title: Förbered Windows Server 2003-servrar för migrering med Azure Migrate
description: Lär dig hur du förbereder Windows Server 2003-servrar för migrering med Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: f8d3dea970d07d951467a44661e12000ba413f72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96753763"
---
# <a name="prepare-windows-server-2003-machines-for-migration"></a>Förbereda Windows Server 2003-datorer för migrering

Den här artikeln beskriver hur du förbereder datorer som kör Windows Server 2003 för migrering till Azure. 


> [!NOTE]
> [Windows Server 2003 Extended support](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support) slutade den 14 juli 2015.  Support teamet för Azure fortsätter att hjälpa till med fel sökning av problem som rör körning av Windows Server 2003 på Azure. Detta stöd är dock begränsat till problem som inte kräver fel sökning eller uppdateringar på operativ system nivå. Att migrera dina program till Azure-instanser som kör en nyare version av Windows Server är den rekommenderade metoden för att säkerställa att du effektivt utnyttjar Azure-molnets flexibilitet och tillförlitlighet. Men om du fortfarande väljer att migrera Windows Server 2003 till Azure kan du använda verktyget Azure Migrate: Migreringsverktyg för server om Windows Server är en virtuell dator som körs på VMware eller Hyper-V.


- Du kan använda en agent lös migrering för att migrera virtuella [Hyper-V-datorer](tutorial-migrate-hyper-v.md) och [virtuella VMware-datorer](tutorial-migrate-vmware.md) till Azure.
- För att kunna ansluta till virtuella Azure-datorer efter migreringen, måste integrerings tjänsterna för Hyper-V vara installerade på den virtuella Azure-datorn. Windows Server 2003-datorer har inte installerat som standard.
- Det finns ingen direkt nedladdnings länk för att installera Hyper-V-integrerings tjänster, så du måste göra följande:
    - För virtuella Hyper-V-datorer som inte har det installerat extraherar du installationsfiler för integrerings tjänster på en dator som kör Windows Server 2012 R2/Windows Server 2012 med Hyper-V-rollen och kopierar sedan installations programmet till Windows Server 2003-datorn. Installationsfilerna är inte tillgängliga på datorer som kör Windows Server 2016.
    - För virtuella VMware-datorer skapar du en start åtgärd som installerar integrerings tjänster när den virtuella Azure-datorn startar efter migreringen.


## <a name="install-on-hyper-v-vms"></a>Installera på virtuella Hyper-V-datorer

Innan du migrerar kontrollerar du om integrerings tjänsterna för Hyper-V är installerade och installerar vid behov.

1. Följ [dessa anvisningar](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#turn-an-integration-service-on-or-off-using-hyper-v-manager) för att kontrol lera om den är installerad.
2. Om den inte är installerad loggar du in på en dator som kör Windows Server 2012 R2/Windows Server 2012 med Hyper-V-rollen.
3. Navigera till installations filen på **C:\Windows\System32\vmguest.ISO** och montera filen.
2. Kopiera installationsmappen till Windows Server 2003-datorn och installera Integration Services.
4. Efter installationen kan du lämna standardinställningarna i integrerings tjänsterna. 

## <a name="install-on-vmware-vms"></a>Installera på virtuella VMware-datorer

1. Logga in på en dator som kör Windows Server 2012 R2/Windows Server 2012 med Hyper-V-rollen.
2. Navigera till installations filen på **C:\Windows\System32\vmguest.ISO** och montera filen.
3. Kopiera installationsmappen till den virtuella VMware-datorn.
4. Kör på kommando raden på den virtuella datorn ```gpedit.msc``` .
5. Öppna **dator konfiguration**  >  **Windows-inställningar**  >  **skript (start/avstängning)**.
6. I **Start**  >  **Lägg till**  >  **skript namn** skriver du setup.exe adressen.
7. Efter migrering till Azure körs skriptet första gången den virtuella Azure-datorn startas.
8. Starta om den virtuella Azure-datorn manuellt. Det finns ett popup-fönster för startdiagnostik som indikerar att en omstart krävs.
9. När skriptet har körts och Hyper-V integrations tjänster har installerats på den virtuella Azure-datorn kan du ta bort skriptet från start.
10. Efter installationen kan du lämna standardinställningarna i integrerings tjänsterna. 

## <a name="next-steps"></a>Nästa steg

- Granska kraven för migrering för virtuella [VMware](migrate-support-matrix-vmware-migration.md) [-och Hyper-V-](migrate-support-matrix-hyper-v-migration.md) datorer.
- Migrera [](server-migrate-overview.md) virtuella VMware [-och Hyper-V-](tutorial-migrate-hyper-v.md) datorer.
