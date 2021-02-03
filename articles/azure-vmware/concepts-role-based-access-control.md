---
title: Koncept – vSphere-rollbaserad åtkomst kontroll (vSphere RBAC)
description: Lär dig mer om de viktigaste funktionerna i vSphere-rollbaserad åtkomst kontroll för Azure VMware-lösning
ms.topic: conceptual
ms.date: 10/23/2020
ms.openlocfilehash: 6f5c30b070b010f312d038d8286be994039c56f8
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509256"
---
# <a name="vsphere-role-based-access-control-vsphere-rbac-for-azure-vmware-solution"></a>vSphere-rollbaserad åtkomst kontroll (vSphere RBAC) för Azure VMware-lösning

I Azure VMware-lösningen har vCenter en inbyggd lokal användare som kallas cloudadmin och tilldelad den inbyggda CloudAdmin-rollen. Den lokala cloudadmin-användaren används för att konfigurera användare i AD. I allmänhet skapar och hanterar CloudAdmin-rollen arbets belastningar i ditt privata moln. I Azure VMware-lösningen har CloudAdmin-rollen vCenter-behörigheter som skiljer sig från andra VMware Cloud-lösningar.     

> [!NOTE]
> Azure VMware-lösningen erbjuder för närvarande inte anpassade roller på vCenter eller Azure VMware Solution Portal. 

I en vCenter-och ESXi lokal distribution har administratören åtkomst till vCenter- administrator@vsphere.local kontot. De kan också ha ytterligare Active Directory (AD) användare/grupper tilldelade. 

I en distribution av Azure VMware-lösningar har administratören inte åtkomst till administratörs användar kontot. Men de kan tilldela AD-användare och-grupper till CloudAdmin-rollen på vCenter.  

Det privata molnets användare har inte åtkomst till och kan inte konfigurera vissa hanterings komponenter som stöds och hanteras av Microsoft. Till exempel kluster, värdar, data lager och distribuerade virtuella växlar.

## <a name="azure-vmware-solution-cloudadmin-role-on-vcenter"></a>CloudAdmin-rollen för Azure VMware-lösningen på vCenter

Du kan visa de behörigheter som har tilldelats Azure VMware-lösningen CloudAdmin-rollen i Azure VMware-lösningen privat moln vCenter.

1. Logga in på SDDC vSphere-klienten och gå till **meny**  >  **Administration**.
1. Under **Access Control** väljer du **roller**.
1. Välj **CloudAdmin** i listan över roller och välj sedan **behörigheter**. 

   :::image type="content" source="media/role-based-access-control-cloudadmin-privileges.png" alt-text="Så här visar du CloudAdmin-rollen behörigheter i vSphere-klienten":::

CloudAdmin-rollen i Azure VMware-lösningen har följande behörigheter på vCenter. I [produkt dokumentationen för VMware](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html) hittar du en detaljerad förklaring av varje behörighet.

| Privilege | Description |
| --------- | ----------- |
| **Larm** | Bekräfta larm<br />Skapa alarm<br />Inaktivera alarm åtgärd<br />Ändra alarm<br />Ta bort alarm<br />Ange alarm status |
| **Behörigheter** | Ändra behörigheter |
| **Innehållsbibliotek** | Lägg till biblioteks objekt<br />Skapa en prenumeration för ett publicerat bibliotek<br />Skapa lokalt bibliotek<br />Skapa prenumerations bibliotek<br />Ta bort biblioteks objekt<br />Ta bort lokalt bibliotek<br />Ta bort prenumerations bibliotek<br />Ta bort prenumerationen på ett publicerat bibliotek<br />Ladda ned filer<br />Avlägsna biblioteks objekt<br />Ta bort prenumerations bibliotek<br />Importera lagring<br />Avsöknings prenumerations information<br />Publicera ett biblioteks objekt till sina prenumeranter<br />Publicera ett bibliotek till dess prenumeranter<br />Läs lagring<br />Synkronisera biblioteks objekt<br />Synkronisera prenumerations bibliotek<br />Skriv introspektionsfunktionerna<br />Uppdatera konfigurations inställningar<br />Uppdatera filer<br />Uppdatera bibliotek<br />Uppdatera biblioteks objekt<br />Uppdatera lokalt bibliotek<br />Uppdatera prenumerations bibliotek<br />Uppdatera prenumerationen på ett publicerat bibliotek<br />Visa konfigurations inställningar |
| **Kryptografiska åtgärder** | Direkt åtkomst |
| **Datalager** | Allokera utrymme<br />Bläddra i data lager<br />Konfigurera data lager<br />Fil åtgärder på låg nivå<br />Ta bort filer<br />Uppdatera metadata för virtuell dator |
| **Mapp** | Skapa mapp<br />Ta bort mapp<br />Flytta mapp<br />Byt namn på mapp |
| **Global** | Avbryt uppgift<br />Global tagg<br />Hälsa<br />Logga händelse<br />Hantera anpassade attribut<br />Tjänst hanterare<br />Ange anpassat attribut<br />System tag |
| **Värd** | vSphere-replikering<br />&#160;&#160;&#160;&#160;hantera replikering |
| **vSphere taggning** | Tilldela och ta bort tilldelning av vSphere-tagg<br />Skapa vSphere-tagg<br />Skapa vSphere tag-kategori<br />Ta bort vSphere-tagg<br />Ta bort vSphere tag-kategori<br />Redigera vSphere-tagg<br />Redigera vSphere tag-kategori<br />Ändra UsedBy-fält för kategori<br />Ändra fältet UsedBy för tagg |
| **Nätverk** | Tilldela nätverk |
| **Resurs** | Använd rekommendation<br />Tilldela vApp till resurspoolen<br />Tilldela en virtuell dator till en resurspool<br />Skapa resurspool<br />Migrera avstängd virtuell dator<br />Migrera på den virtuella datorn<br />Ändra resurspool<br />Flytta resurspool<br />Fråga vMotion<br />Ta bort resurspool<br />Byt namn på resurspool |
| **Schemalagd aktivitet** | Skapa uppgift<br />Ändra uppgift<br />Ta bort aktivitet<br />Kör uppgift |
| **Sessioner** | Meddelande<br />Verifiera session |
| **Profil** | Profil driven Storage-vy |
| **Storage-vy** | Visa |
| **vApp** | Lägg till virtuell dator<br />Tilldela resurspool<br />Tilldela vApp<br />Klona<br />Skapa<br />Ta bort<br />Exportera<br />Importera<br />Flytta<br />Avstängning<br />Slå på<br />Byt namn<br />Suspend<br />Avregistrera<br />Visa OVF-miljö<br />vApp program konfiguration<br />konfiguration av vApp-instans<br />vApp managedBy-konfiguration<br />resurs konfiguration för vApp |
| **Virtuell dator** | Ändra konfiguration<br />&#160;&#160;&#160;&#160;erhålla disk lån<br />&#160;&#160;&#160;&#160;lägga till befintlig disk<br />&#160;&#160;&#160;&#160;lägga till ny disk<br />&#160;&#160;&#160;&#160;lägga till eller ta bort enhet<br />&#160;&#160;&#160;&#160;Avancerad konfiguration<br />&#160;&#160;&#160;&#160;ändra antalet processorer<br />&#160;&#160;&#160;&#160;ändra minne<br />&#160;&#160;&#160;&#160;ändra inställningar<br />&#160;&#160;&#160;&#160;ändra swapfile-placering<br />&#160;&#160;&#160;&#160;ändra resurs<br />&#160;&#160;&#160;&#160;konfigurera värd USB-enhet<br />&#160;&#160;&#160;&#160;konfigurera RAW-enhet<br />&#160;&#160;&#160;&#160;konfigurera managedBy<br />&#160;&#160;&#160;&#160;Visa anslutnings inställningar<br />&#160;&#160;&#160;&#160;utöka virtuell disk<br />&#160;&#160;&#160;&#160;ändra enhets inställningar<br />&#160;&#160;&#160;&#160;kompatibilitet för fel tolerans för fråga<br />&#160;&#160;&#160;&#160;fråga efter filer som inte ägs<br />&#160;&#160;&#160;&#160;läsa in på nytt från sökvägar<br />&#160;&#160;&#160;&#160;ta bort disk<br />Byt namn på &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;återställa gäst information<br />&#160;&#160;&#160;&#160;ange anteckning<br />&#160;&#160;&#160;&#160;växla spårning av disk ändring<br />&#160;&#160;&#160;&#160;växla överordnad förgrening<br />&#160;&#160;&#160;&#160;Uppgradera kompatibilitet för virtuell dator<br />Redigera inventering<br />&#160;&#160;&#160;&#160;skapa från befintlig<br />&#160;&#160;&#160;&#160;skapa nya<br />&#160;&#160;&#160;&#160;flytta<br />&#160;&#160;&#160;&#160;registrera dig<br />&#160;&#160;&#160;&#160;ta bort<br />&#160;&#160;&#160;&#160;avregistrera<br />Gäst åtgärder<br />Ändra alias för &#160;&#160;&#160;&#160;gäst åtgärd<br />&#160;&#160;&#160;&#160;fråga om gäst åtgärds Ali Aset<br />&#160;&#160;&#160;&#160;ändringar i gäst åtgärden<br />&#160;&#160;&#160;&#160;körning av gäst åtgärds program<br />&#160;&#160;&#160;&#160;gäst åtgärds frågor<br />Interaktion<br />&#160;&#160;&#160;&#160;svars fråga<br />&#160;&#160;&#160;&#160;säkerhetskopiera på den virtuella datorn<br />&#160;&#160;&#160;&#160;konfigurera CD-medier<br />&#160;&#160;&#160;&#160;konfigurera diskett medium<br />&#160;&#160;&#160;&#160;ansluta enheter<br />&#160;&#160;&#160;&#160;-konsol-interaktion<br />&#160;&#160;&#160;&#160;skapa skärm bild<br />&#160;&#160;&#160;&#160;defragmentera alla diskar<br />&#160;&#160;&#160;&#160;dra och släpp<br />&#160;&#160;&#160;&#160;hantering av gäst operativ system med API för VIX<br />&#160;&#160;&#160;&#160;mata in USB HID-sökkoder<br />&#160;&#160;&#160;&#160;Installera VMware-verktyg<br />&#160;&#160;&#160;&#160;pausa eller pausa<br />&#160;&#160;&#160;&#160;utför rensnings-eller krympnings åtgärder<br />&#160;&#160;&#160;&#160;avstängning<br />&#160;&#160;&#160;&#160;strömförsörjning<br />&#160;&#160;&#160;&#160;Record-sessionen på den virtuella datorn<br />&#160;&#160;&#160;&#160;Replay-sessionen på den virtuella datorn<br />&#160;&#160;&#160;&#160;pausa<br />&#160;&#160;&#160;&#160;inaktivera fel tolerans<br />&#160;&#160;&#160;&#160;redundanstest<br />&#160;&#160;&#160;&#160;testa att starta om den sekundära virtuella datorn<br />&#160;&#160;&#160;&#160;inaktivera fel tolerans<br />&#160;&#160;&#160;&#160;aktivera fel tolerans<br />Etablering<br />&#160;&#160;&#160;&#160;Tillåt disk åtkomst<br />&#160;&#160;&#160;&#160;Tillåt fil åtkomst<br />&#160;&#160;&#160;&#160;Tillåt skrivskyddad disk åtkomst<br />&#160;&#160;&#160;&#160;Tillåt nedladdning av virtuell dator<br />Klona mall för &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;klona virtuell dator<br />&#160;&#160;&#160;&#160;Skapa mall från virtuell dator<br />&#160;&#160;&#160;&#160;anpassa gäst<br />&#160;&#160;&#160;&#160;distribuera mall<br />&#160;&#160;&#160;&#160;Markera som mall<br />&#160;&#160;&#160;&#160;ändra anpassnings specifikation<br />&#160;&#160;&#160;&#160;befordra diskar<br />&#160;&#160;&#160;&#160;läsa anpassnings specifikationer<br />Tjänstkonfiguration<br />&#160;&#160;&#160;&#160;Tillåt meddelanden<br />&#160;&#160;&#160;&#160;tillåta avsökning av globala händelse meddelanden<br />&#160;&#160;&#160;&#160;hantera tjänst konfiguration<br />&#160;&#160;&#160;&#160;ändra tjänst konfigurationen<br />&#160;&#160;&#160;&#160;query service-konfigurationer<br />&#160;&#160;&#160;&#160;läsa tjänst konfiguration<br />Ögonblicks bilds hantering<br />&#160;&#160;&#160;&#160;skapa ögonblicks bild<br />&#160;&#160;&#160;&#160;ta bort ögonblicks bild<br />&#160;&#160;&#160;&#160;Byt namn på ögonblicks bild<br />&#160;&#160;&#160;&#160;Återställ ögonblicks bild<br />vSphere-replikering<br />&#160;&#160;&#160;&#160;Konfigurera replikering<br />&#160;&#160;&#160;&#160;hantera replikering<br />Övervaka replikering av &#160;&#160;&#160;&#160; |
| **vService** | Skapa beroende<br />Förstör beroende<br />Konfigurera om beroende konfiguration<br />Uppdatera beroende |



## <a name="next-steps"></a>Nästa steg

Nu när du har täckt grunderna i vSphere-rollbaserad åtkomst kontroll för Azure VMware-lösningen kanske du vill lära dig mer om:

- Information om varje behörighet finns i [produkt dokumentationen för VMware](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html).
- [Hur Azure VMware-lösningen övervakar och reparerar privata moln](concepts-monitor-repair-private-cloud.md).
- [Så här aktiverar du Azure VMware-lösnings resurser](enable-azure-vmware-solution.md).

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VMware product documentation]: https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html

