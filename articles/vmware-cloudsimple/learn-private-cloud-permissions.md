---
title: Azure VMware-lösning av CloudSimple-behörighets modell för privata moln
description: Beskriver behörighets modellen, grupper och kategorier för CloudSimple privata moln
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1c8cfeda008955006f2fbad1df58c8047bd36541
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898053"
---
# <a name="cloudsimple-private-cloud-permission-model-of-vmware-vcenter"></a>CloudSimple privata moln behörighets modell för VMware vCenter

CloudSimple behåller fullständig administrativ åtkomst till den privata moln miljön. Varje CloudSimple-kund beviljas tillräckligt med administratörs behörighet för att kunna distribuera och hantera de virtuella datorerna i sin miljö.  Om det behövs kan du tillfälligt eskalera dina privilegier för att utföra administrativa funktioner.

## <a name="cloud-owner"></a>Moln ägare

När du skapar ett privat moln skapas en **CloudOwner** -användare i vCenter Single Sign-On-domänen med åtkomst till **rollen som moln ägar roll** för att hantera objekt i det privata molnet. Den här användaren kan också konfigurera ytterligare [vCenter identitets källor](set-vcenter-identity.md)och andra användare till det privata molnet vCenter.

> [!NOTE]
> Standard användaren för ditt CloudSimple privata moln vCenter är cloudowner@cloudsimple.local när ett privat moln skapas.

## <a name="user-groups"></a>Användargrupper

En grupp som kallas **moln ägar grupp** skapas vid distributionen av ett privat moln. Användare i den här gruppen kan administrera olika delar av vSphere-miljön i det privata molnet. Den här gruppen tilldelas automatiskt  **moln-ägar-roll-** privilegier och  **CloudOwner** -användaren läggs till som medlem i den här gruppen.  CloudSimple skapar ytterligare grupper med begränsade behörigheter för enkel hantering.  Du kan lägga till vilken användare som helst i de grupper som skapats i förväg och de behörigheter som definieras nedan tilldelas användare i grupperna automatiskt.

### <a name="pre-created-groups"></a>I förväg skapade grupper

| Gruppnamn | Syfte | Roll |
| -------- | ------- | ------ |
| Moln-ägar grupp | Medlemmar i den här gruppen har administratörs behörighet för det privata molnet vCenter | [Moln-ägar-roll](#cloud-owner-role) |
| Moln-globalt-kluster-admin-grupp | Medlemmar i den här gruppen har administratörs behörighet för det privata molnet vCenter-kluster | [Moln-kluster-admin-roll](#cloud-cluster-admin-role) |
| Cloud – Global Storage – admin-Group | Medlemmar i den här gruppen kan hantera lagring i det privata molnet vCenter | [Cloud-Storage – admin-Role](#cloud-storage-admin-role) |
| Cloud – Global-Network-admin-Group | Medlemmar i den här gruppen kan hantera nätverks-och distribuerade Port grupper i det privata molnet vCenter | [Cloud-network-admin-Role](#cloud-network-admin-role) |
| Moln – global-VM-admin-Group | Medlemmar i den här gruppen kan hantera virtuella datorer i det privata molnet vCenter | [Cloud-VM-admin-Role](#cloud-vm-admin-role) |

Om du vill ge enskilda användare behörighet att hantera det privata molnet, skapar du användar konton Lägg till i lämpliga grupper.

> [!CAUTION]
> Nya användare får endast läggas till i *moln-ägar-grupp*, *Cloud-global-Cluster-admin-Group*, *Cloud-Global-Storage-admin-Group*, *Cloud-Global-Network-admin-Group* eller, *Cloud-global-VM-admin-Group*.  Användare som lagts till i gruppen *Administratörer* tas då bort automatiskt.  Endast tjänst konton måste läggas till i *Administratörs* gruppen och tjänst kontona får inte användas för att logga in på vSphere-webbgränssnittet.

## <a name="list-of-vcenter-privileges-for-default-roles"></a>Lista över vCenter-privilegier för standard roller

### <a name="cloud-owner-role"></a>Moln-ägar-roll

| **Kategori** | **Grupperna** |
|----------|-----------|
| **Larm** | Bekräfta larm <br> Skapa alarm <br> Inaktivera alarm åtgärd <br> Ändra alarm <br> Ta bort alarm <br> Ange alarm status |
| **Behörigheter** | Ändra behörighet |
| **Innehållsbibliotek** | Lägg till biblioteks objekt <br> Skapa lokalt bibliotek <br> Skapa prenumerations bibliotek <br> Ta bort biblioteks objekt <br> Ta bort lokalt bibliotek <br> Ta bort prenumerations bibliotek <br> Ladda ned filer <br> Avlägsna biblioteks objekt <br> Ta bort prenumerations bibliotek <br> Importera lagring <br> Avsöknings prenumerations information <br> Läs lagring <br> Synkronisera biblioteks objekt <br> Synkronisera prenumerations bibliotek <br> Skriv introspektionsfunktionerna <br> Uppdatera konfigurations inställningar <br> Uppdatera filer <br> Uppdatera bibliotek <br> Uppdatera biblioteks objekt <br> Uppdatera lokalt bibliotek <br> Uppdatera prenumerations bibliotek <br> Visa konfigurations inställningar |
| **Kryptografiska åtgärder** | Lägg till disk <br> Klona <br> Avkryptera <br> Direktåtkomst <br> Kryptera <br> Kryptera ny <br> Hantera KMS <br> Hantera krypterings principer <br> Hantera nycklar <br> Migrera <br> Dekryptera om <br> Registrera VM <br> Registrera värd |
| **dvPort-grupp** | Skapa <br> Ta bort <br> Ändra <br> Princip åtgärd <br> Omfattnings åtgärd |
| **Datalager** | Allokera utrymme <br> Bläddra i data lager <br> Konfigurera data lager <br> Fil åtgärder på låg nivå <br> Flytta data lager <br> Ta bort data lager <br> Ta bort fil <br> Byt namn på data lager <br> Uppdatera filer för virtuella datorer <br> Uppdatera metadata för virtuell dator |
| **ESX Agent Manager** | Konfigurera <br> Ändra <br> Visa |
| **Anknytning** | Registrera tillägg <br> Avregistrera tillägg <br> Uppdatera tillägg |
| **Extern stats Provider**| Registrera dig <br> Avregistrera <br> Uppdatera |
| **Mapp** | Skapa mapp <br> Ta bort mapp <br> Flytta mapp <br> Byt namn på mapp |
| **Global** | Avbryt uppgift <br> Kapacitetsplanering <br> Diagnostik <br> Inaktivera metoder <br> Aktivera metoder <br> Global tagg <br> Hälsa <br> Licenser <br> Logga händelse <br> Hantera anpassade attribut <br> Proxy <br> Skript åtgärd <br> Tjänst hanterare <br> Ange anpassat attribut <br> System tag |
| **Hälso uppdaterings leverantör** | Registrera dig <br> Avregistrera <br> Uppdatera |
| **Konfiguration av värd >** | Konfiguration av lagringspool |
| **Värd > inventering** | Ändra kluster |
| **vSphere taggning** | Tilldela eller ta bort tilldelning av vSphere-tagg <br> Skapa vSphere-tagg <br> Skapa vSphere tag-kategori <br> Ta bort vSphere-tagg <br> Ta bort vSphere tag-kategori <br> Redigera vSphere-tagg <br> Redigera vSphere tag-kategori <br> Ändra UsedBy-fält för kategori <br> Ändra fältet UsedBy för tagg |
| **Nätverk** | Tilldela nätverk <br> Konfigurera <br> Flytta nätverk <br> Ta bort |
| **Prestanda** | Ändra intervall |
| **Värd profil** | Visa |
| **Resurs** | Använd rekommendation <br> Tilldela vApp till resurspoolen <br> Tilldela en virtuell dator till en resurspool <br> Skapa resurspool <br> Migrera avstängd virtuell dator <br> Migrera på den virtuella datorn <br> Ändra resurspool <br> Flytta resurspool <br> Fråga vMotion <br> Ta bort resurspool <br> Byt namn på resurspool |
| **Schemalagd aktivitet** | Skapa uppgifter <br> Ändra uppgift <br> Ta bort aktivitet <br> Kör uppgift |
| **Sessioner** | Personifiera användare <br> Meddelande <br> Verifiera session <br> Visa och stoppa sessioner |
| **Data lager kluster** | Konfigurera ett data lager kluster |
| **Profil driven lagring** | Profil driven lagrings uppdatering <br> Profil driven Storage-vy |
| **Lagringspooler** | Konfigurera tjänst <br> Visa |
| **Aktiviteter** | Skapa uppgift <br> Uppdatera uppgift |
| **Överförings tjänst**| Hantera <br> Monitor |
| **vApp** | Lägg till virtuell dator <br> Tilldela resurspool <br> Tilldela vApp <br> Klona <br> Skapa <br> Ta bort <br> Exportera <br> Importera <br> Flytta <br> Avstängning <br> Slå på <br> Byt namn <br> Suspend <br> Avregistrera <br> Visa OVF-miljö <br> vApp program konfiguration <br> konfiguration av vApp-instans <br> vApp managedBy-konfiguration <br> resurs konfiguration för vApp |
| **VRMPolicy** | Fråga VRMPolicy <br> Uppdatera VRMPolicy |
| **Konfiguration av virtuell dator >** | Lägg till befintlig disk <br> Lägg till ny disk <br> Lägg till eller ta bort enhet <br> Avancerat <br> Ändra antal processorer <br> Ändra resurs <br> Konfigurera managedBy <br> Disk ändrings spårning <br> Disk lån <br> Visa anslutnings inställningar <br> Utöka virtuell disk <br> Värd USB-enhet <br> Minne <br> Ändra enhets inställningar <br> Kompatibilitet för fel tolerans för fråga <br> Fråga efter filer som inte ägs <br> RAW-enhet <br> Läs in på nytt från sökväg <br> Ta bort disk <br> Byt namn <br> Återställ gäst information <br> Ange anteckning <br> Inställningar <br> Swapfile placering <br> Växla överordnad förgrening <br> Lås upp virtuell dator <br> Uppgradera kompatibilitet för virtuell dator |
| **> gäst åtgärder för virtuell dator** | Ändra alias för gäst åtgärd <br> Fråga om gäst åtgärds Ali Aset <br> Ändringar av gäst åtgärd <br> Körning av gäst åtgärds program <br> Gäst åtgärds frågor |
| **> interaktion för virtuell dator** | Svars fråga <br> Säkerhets kopierings åtgärd på virtuell dator <br> Konfigurera CD-medier <br> Konfigurera diskett medium <br> Konsol interaktion <br> Skapa skärm bild <br> Defragmentera alla diskar <br> Enhets anslutning <br> Dra och släpp <br> Hantering av gäst operativ system med VIX-API <br> Mata in USB HID-sökkoder <br> Pausa eller pausa <br> Utför rensnings-eller krympnings åtgärder <br> Avstängning <br> Slå på <br> Spela in session på virtuell dator <br> Replay-session på virtuell dator <br> Återställ <br> Återuppta fel tolerans <br> Suspend <br> Pausa fel tolerans <br> Redundanstest <br> Testa att starta om sekundär virtuell dator <br> Stäng av fel tolerans <br> Aktivera fel tolerans <br> Installera VMware-verktyg |
| **> inventering av virtuell dator** | Skapa från befintlig <br> Skapa ny <br> Flytta <br> Registrera dig <br> Ta bort <br> Avregistrera |
| **Etablering av virtuell dator >** | Tillåt disk åtkomst <br> Tillåt fil åtkomst <br> Tillåt skrivskyddad disk åtkomst <br> Tillåt nedladdning av virtuell dator <br> Tillåt uppladdning av filer för virtuella datorer <br> Klona mall <br> Klona virtuell dator <br> Skapa mall från virtuell dator <br> Anpassa <br> Distribuera mallen <br> Markera som mall <br> Markera som virtuell dator <br> Ändra anpassnings specifikation <br> Marknadsför diskar <br> Läs anpassnings specifikationer |
| **Konfiguration av > tjänst för virtuell dator** | Tillåt meddelanden <br> Tillåt avsökning av globala händelse meddelanden <br> Hantera tjänst konfigurationer <br> Ändra tjänst konfiguration <br> Query service-konfigurationer <br> Läs tjänst konfiguration |
| **> ögonblicks bild hantering av virtuell dator** | Skapa en ögonblicksbild <br> Ta bort ögonblicks bild <br> Byt namn på ögonblicks bild <br> Återgå till ögonblicks bild |
| **Virtuell dator > vSphere-replikering** | Konfigurera replikering <br> Hantera replikering <br> Övervaka replikering |
| **vService** | Skapa beroende <br> Förstör beroende <br> Konfigurera om beroende konfiguration <br> Uppdatera beroende |

### <a name="cloud-cluster-admin-role"></a>Moln-kluster-admin-roll

| **Kategori** | **Grupperna** |
|----------|-----------|
| **Datalager** | Allokera utrymme <br> Bläddra i data lager <br> Konfigurera data lager <br> Fil åtgärder på låg nivå <br> Ta bort data lager <br> Byt namn på data lager <br> Uppdatera filer för virtuella datorer <br> Uppdatera metadata för virtuell dator |
| **Mapp** | Skapa mapp <br> Ta bort mapp <br> Flytta mapp <br> Byt namn på mapp |
| **Konfiguration av värd >**  | Konfiguration av lagringspool |
| **vSphere taggning** | Tilldela eller ta bort tilldelning av vSphere-tagg <br> Skapa vSphere-tagg <br> Skapa vSphere tag-kategori <br> Ta bort vSphere-tagg <br> Ta bort vSphere tag-kategori <br> Redigera vSphere-tagg <br> Redigera vSphere tag-kategori <br> Ändra UsedBy-fält för kategori <br> Ändra fältet UsedBy för tagg |
| **Nätverk** | Tilldela nätverk |
| **Resurs** | Använd rekommendation <br> Tilldela vApp till resurspoolen <br> Tilldela en virtuell dator till en resurspool <br> Skapa resurspool <br> Migrera avstängd virtuell dator <br> Migrera på den virtuella datorn <br> Ändra resurspool <br> Flytta resurspool <br> Fråga vMotion <br> Ta bort resurspool <br> Byt namn på resurspool |
| **vApp** | Lägg till virtuell dator <br> Tilldela resurspool <br> Tilldela vApp <br> Klona <br> Skapa <br> Ta bort <br> Exportera <br> Importera <br> Flytta <br> Avstängning <br> Slå på <br> Byt namn <br> Suspend <br> Avregistrera <br> Visa OVF-miljö <br> vApp program konfiguration <br> konfiguration av vApp-instans <br> vApp managedBy-konfiguration <br> resurs konfiguration för vApp |
| **VRMPolicy** | Fråga VRMPolicy <br> Uppdatera VRMPolicy |
| **Konfiguration av virtuell dator >** | Lägg till befintlig disk <br> Lägg till ny disk <br> Lägg till eller ta bort enhet <br> Avancerat <br> Ändra antal processorer <br> Ändra resurs <br> Konfigurera managedBy <br> Disk ändrings spårning <br> Disk lån <br> Visa anslutnings inställningar <br> Utöka virtuell disk <br> Värd USB-enhet <br> Minne <br> Ändra enhets inställningar <br> Kompatibilitet för fel tolerans för fråga <br> Fråga efter filer som inte ägs <br> RAW-enhet <br> Läs in på nytt från sökväg <br> Ta bort disk <br> Byt namn <br> Återställ gäst information <br> Ange anteckning <br> Inställningar <br> Swapfile placering <br> Växla överordnad förgrening <br> Lås upp virtuell dator <br> Uppgradera kompatibilitet för virtuell dator |
| **> gäst åtgärder för virtuell dator** | Ändra alias för gäst åtgärd <br> Fråga om gäst åtgärds Ali Aset <br> Ändringar av gäst åtgärd <br> Körning av gäst åtgärds program <br> Gäst åtgärds frågor |
| **> interaktion för virtuell dator** | Svars fråga <br> Säkerhets kopierings åtgärd på virtuell dator <br> Konfigurera CD-medier <br> Konfigurera diskett medium <br> Konsol interaktion <br> Skapa skärm bild <br> Defragmentera alla diskar <br> Enhets anslutning <br> Dra och släpp <br> Hantering av gäst operativ system med VIX-API <br> Mata in USB HID-sökkoder <br> Pausa eller pausa <br> Utför rensnings-eller krympnings åtgärder <br> Avstängning <br> Slå på <br> Spela in session på virtuell dator <br> Replay-session på virtuell dator <br> Återställ <br> Återuppta fel tolerans <br> Suspend <br> Pausa fel tolerans <br> Redundanstest <br> Testa att starta om sekundär virtuell dator <br> Stäng av fel tolerans <br> Aktivera fel tolerans <br> Installera VMware-verktyg
| **> inventering av virtuell dator** | Skapa från befintlig <br> Skapa ny <br> Flytta <br> Registrera dig <br> Ta bort <br> Avregistrera |
| **Etablering av virtuell dator >** | Tillåt disk åtkomst <br> Tillåt fil åtkomst <br> Tillåt skrivskyddad disk åtkomst <br> Tillåt nedladdning av virtuell dator <br> Tillåt uppladdning av filer för virtuella datorer <br> Klona mall <br> Klona virtuell dator <br> Skapa mall från virtuell dator <br> Anpassa <br> Distribuera mallen <br> Markera som mall <br> Markera som virtuell dator <br> Ändra anpassnings specifikation <br> Marknadsför diskar  <br> Läs anpassnings specifikationer |
| **Konfiguration av > tjänst för virtuell dator** | Tillåt meddelanden <br> Tillåt avsökning av globala händelse meddelanden <br> Hantera tjänst konfigurationer <br> Ändra tjänst konfiguration <br> Query service-konfigurationer <br> Läs tjänst konfiguration
| **> ögonblicks bild hantering av virtuell dator** | Skapa en ögonblicksbild <br> Ta bort ögonblicks bild <br> Byt namn på ögonblicks bild <br> Återgå till ögonblicks bild |
| **Virtuell dator > vSphere-replikering** | Konfigurera replikering <br> Hantera replikering <br> Övervaka replikering |
| **vService** | Skapa beroende <br> Förstör beroende <br> Konfigurera om beroende konfiguration <br> Uppdatera beroende |

### <a name="cloud-storage-admin-role"></a>Cloud-Storage – admin-Role

| **Kategori** | **Grupperna** |
|----------|-----------|
| **Datalager** | Allokera utrymme <br> Bläddra i data lager <br> Konfigurera data lager <br> Fil åtgärder på låg nivå <br> Ta bort data lager <br> Byt namn på data lager <br> Uppdatera filer för virtuella datorer <br> Uppdatera metadata för virtuell dator |
| **Konfiguration av värd >** | Konfiguration av lagringspool |
| **Data lager kluster** | Konfigurera ett data lager kluster |
| **Profil driven lagring** | Profil driven lagrings uppdatering <br> Profil driven Storage-vy |
| **Lagringspooler** | Konfigurera tjänst <br> Visa |

### <a name="cloud-network-admin-role"></a>Cloud-network-admin-Role

| **Kategori** | **Grupperna** |
|----------|-----------|
| **dvPort-grupp** | Skapa <br> Ta bort <br> Ändra <br> Princip åtgärd <br> Omfattnings åtgärd |
| **Nätverk** | Tilldela nätverk <br> Konfigurera <br> Flytta nätverk <br> Ta bort |
| **Konfiguration av virtuell dator >** | Ändra enhets inställningar |

### <a name="cloud-vm-admin-role"></a>Cloud-VM-admin-Role

| **Kategori** | **Grupperna** |
|----------|-----------|
| **Datalager** | Allokera utrymme <br> Bläddra i data lager |
| **Nätverk** | Tilldela nätverk |
| **Resurs** | Tilldela en virtuell dator till en resurspool <br> Migrera avstängd virtuell dator <br> Migrera på den virtuella datorn
| **vApp** | Exportera <br> Importera |
| **Konfiguration av virtuell dator >** | Lägg till befintlig disk <br> Lägg till ny disk <br> Lägg till eller ta bort enhet <br> Avancerat <br> Ändra antal processorer <br> Ändra resurs <br> Konfigurera managedBy <br> Disk ändrings spårning <br> Disk lån <br> Visa anslutnings inställningar <br> Utöka virtuell disk <br> Värd USB-enhet <br> Minne <br> Ändra enhets inställningar <br> Kompatibilitet för fel tolerans för fråga <br> Fråga efter filer som inte ägs <br> RAW-enhet <br> Läs in på nytt från sökväg <br> Ta bort disk <br> Byt namn <br> Återställ gäst information <br> Ange anteckning <br> Inställningar <br> Swapfile placering <br> Växla överordnad förgrening <br> Lås upp virtuell dator <br> Uppgradera kompatibilitet för virtuell dator |
| **>gäst åtgärder för virtuell dator** | Ändra alias för gäst åtgärd <br> Fråga om gäst åtgärds Ali Aset <br> Ändringar av gäst åtgärd <br> Körning av gäst åtgärds program <br> Gäst åtgärds frågor    |
| **>interaktion för virtuell dator** | Svars fråga <br> Säkerhets kopierings åtgärd på virtuell dator <br> Konfigurera CD-medier <br> Konfigurera diskett medium <br> Konsol interaktion <br> Skapa skärm bild <br> Defragmentera alla diskar <br> Enhets anslutning <br> Dra och släpp <br> Hantering av gäst operativ system med VIX-API <br> Mata in USB HID-sökkoder <br> Pausa eller pausa <br> Utför rensnings-eller krympnings åtgärder <br> Avstängning <br> Slå på <br> Spela in session på virtuell dator <br> Replay-session på virtuell dator <br> Återställ <br> Återuppta fel tolerans <br> Suspend <br> Pausa fel tolerans <br> Redundanstest <br> Testa att starta om sekundär virtuell dator <br> Stäng av fel tolerans <br> Aktivera fel tolerans <br> Installera VMware-verktyg |
| **>inventering av virtuell dator** | Skapa från befintlig <br> Skapa ny <br> Flytta <br> Registrera dig <br> Ta bort <br> Avregistrera |
| **Etablering av virtuell dator >** | Tillåt disk åtkomst <br> Tillåt fil åtkomst <br> Tillåt skrivskyddad disk åtkomst <br> Tillåt nedladdning av virtuell dator <br> Tillåt uppladdning av filer för virtuella datorer <br> Klona mall <br> Klona virtuell dator <br> Skapa mall från virtuell dator <br> Anpassa <br> Distribuera mallen <br> Markera som mall <br> Markera som virtuell dator <br> Ändra anpassnings specifikation <br> Marknadsför diskar <br> Läs anpassnings specifikationer |
| **Konfiguration av >tjänst för virtuell dator** | Tillåt meddelanden <br> Tillåt avsökning av globala händelse meddelanden <br> Hantera tjänst konfigurationer <br> Ändra tjänst konfiguration <br> Query service-konfigurationer <br> Läs tjänst konfiguration
| **>ögonblicks bild hantering av virtuell dator** | Skapa en ögonblicksbild <br> Ta bort ögonblicks bild <br> Byt namn på ögonblicks bild <br> Återgå till ögonblicks bild |
| **Virtuell dator >vSphere-replikering** | Konfigurera replikering <br> Hantera replikering <br> Övervaka replikering |
| **vService** | Skapa beroende <br> Förstör beroende <br> Konfigurera om beroende konfiguration <br> Uppdatera beroende |
