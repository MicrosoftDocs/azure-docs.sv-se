---
title: Labb i Azure Lab Services – vanliga frågor och svar | Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor och svar om labb i Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 757af8f30e9a71a3889d9f625c87a002af2e1302
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96437192"
---
# <a name="labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Labb i Azure Lab Services – vanliga frågor och svar
Få svar på några av de vanligaste frågorna om labb i Azure Lab Services. 

## <a name="quotas"></a>Kvoter

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>Är kvoten per användare eller per vecka eller per hela tiden för labbet? 
Den kvot som du har angett för ett labb är för varje student under hela Labbets varaktighet. Och den [schemalagda körningen av virtuella datorer](how-to-create-schedules.md) räknas inte mot den kvot som tilldelats till en användare. Kvoten är för tiden utanför de schema timmar som en student tillbringar på virtuella datorer.  Mer information om kvoter finns i [Ange kvoter för användare](how-to-configure-student-usage.md#set-quotas-for-users).

### <a name="if-educator-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>Om lärare aktiverar en elev-VM, påverkar detta student kvoten? 
Nej. Det har den inte. När lärare slår på den virtuella student datorn påverkar den inte den kvot som tilldelats studenten. 

## <a name="schedules"></a>Scheman

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Startar alla virtuella datorer automatiskt i labbet när ett schema har angetts? 
Nej. Inte alla virtuella datorer. Endast de virtuella datorer som är tilldelade till användare enligt ett schema. De virtuella datorer som inte är tilldelade till en användare startas inte automatiskt. Det är avsiktligt. 

## <a name="lab-accounts"></a>Labbuppgiftskonton

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Varför kan jag inte skapa ett labb på grund av att adress intervallet inte är tillgängligt? 

Labb kan skapa virtuella labb datorer i ett IP-adressintervall som du anger när du skapar ditt labb konto i Azure Portal. När ett adress intervall anges, är varje labb som skapas efter att det har tilldelats 512 IP-adresser för virtuella labb datorer. Adress intervallet för labb kontot måste vara tillräckligt stort för att rymma alla labb som du planerar att skapa under labb kontot. 

Om du till exempel har ett block på/19-10.0.0.0/19 får detta adress intervall 8192 IP-adresser och 16 labb (8 192/512 = 16 labb). I det här fallet går det inte att skapa labbet vid skapande av 17 Lab.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Vilka port intervall ska jag öppna i min organisations brand Väggs inställning för att ansluta till virtuella labb datorer via RDP/SSH?

Portarna är: 49152 – 65535. Labben ligger bakom en belastningsutjämnare. Varje labb har en enda offentlig IP-adress och varje virtuell dator i labbet har en unik port. 

Du kan också se den privata IP-adressen för varje virtuell dator på fliken **virtuell dator pool** på Start sidan för labbet i Azure Portal. Om du återpublicerar ett labb kommer den offentliga IP-adressen för labbet inte att ändras, men den privata IP-adressen och port numret för varje virtuell dator i labbet kan ändras. Du kan läsa mer i artikeln: [brand Väggs inställningar för Azure Lab Services](how-to-configure-firewall-settings.md).

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Vilka offentliga IP-adressintervall ska jag öppna i min organisations brand Väggs inställningar för att ansluta till virtuella labb datorer via RDP/SSH?
Se [Azure IP-intervall och service märken – offentliga moln](https://www.microsoft.com/download/details.aspx?id=56519), som tillhandahåller det offentliga IP-adressintervallet för data Center i Azure. Du kan öppna IP-adresserna för de regioner där dina labb konton finns.

## <a name="virtual-machine-images"></a>Virtuella datoravbildningar

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>Varför kan jag inte aktivera ytterligare avbildnings alternativ i list rutan avbildning av virtuella datorer när jag skapar en ny labb-skapare?

När en administratör lägger till dig som en labb skapare till ett labb konto får du behörighet att skapa labb. Men du har inte behörighet att redigera några inställningar i labb kontot, inklusive listan över aktiverade avbildningar av virtuella datorer. Om du vill aktivera ytterligare avbildningar kontaktar du din labb konto administratör för att göra det åt dig, eller be administratören att lägga till dig som deltagar roll till labb kontot. Deltagar rollen ger dig behörighet att redigera avbildnings listan för virtuella datorer i labb kontot.

### <a name="can-i-attach-additional-disks-to-a-virtual-machine"></a>Kan jag koppla ytterligare diskar till en virtuell dator?
Nej. Det går inte att koppla ytterligare diskar till en virtuell dator i ett klass rums labb. 

## <a name="users"></a>Användare

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>Hur många användare kan vara i ett klass rums labb?
Du kan lägga till upp till 400 användare i ett klass rums labb. 

## <a name="blog-post"></a>Blogg inlägg
Prenumerera på [Azure Lab Services blogg](https://aka.ms/azlabs-blog).

## <a name="update-notifications"></a>Uppdatera meddelanden
Prenumerera på [labb tjänst uppdateringar](https://azure.microsoft.com/updates/?product=lab-services) för att hålla dig informerad om nya funktioner i labb tjänster.

## <a name="general"></a>Allmänt
### <a name="what-if-my-question-isnt-answered-here"></a>Vad händer om min fråga inte besvaras här?
Om din fråga inte visas här kan vi berätta för oss, så vi kan hjälpa dig att hitta ett svar.

- Publicera en fråga i slutet av dessa vanliga frågor och svar. 
- För att få en större mål grupp kan du publicera en fråga på [Azure Lab Services – Tech community-forumet](https://techcommunity.microsoft.com/t5/azure-lab-services/bd-p/AzureLabServices). 
- Skicka dina förfrågningar och idéer till [Azure Lab Services – användar rösten](https://feedback.azure.com/forums/320373-lab-services?category_id=352774)för funktions förfrågningar.

