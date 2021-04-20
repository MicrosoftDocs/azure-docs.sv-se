---
title: ta med fil
description: ta med fil
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 02/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c0ca74b6cf63e3c81d830eca76eea6815548faa1
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732527"
---
### <a name="do-i-need-a-public-ip-on-my-virtual-machine-to-connect-via-azure-bastion"></a><a name="publicip"></a>Behöver jag en offentlig IP-adress på min virtuella dator för att ansluta via Azure Bastion?

Nej. När du ansluter till en virtuell dator Azure Bastion behöver du ingen offentlig IP-adress på den virtuella Azure-dator som du ansluter till. Bastion-tjänsten öppnar RDP/SSH-sessionen/anslutningen till den virtuella datorn via den virtuella datorns privata IP-adress i det virtuella nätverket.

### <a name="is-ipv6-supported"></a>Stöds IPv6?

För närvarande stöds inte IPv6. Azure Bastion stöder endast IPv4.

### <a name="can-i-use-azure-bastion-with-azure-private-dns-zones"></a>Kan jag använda Azure Bastion Med Azure Privat DNS Zones?

Användningen av Azure Bastion med Azure Privat DNS Zones stöds inte för närvarande. Innan du distribuerar Azure Bastion bör du kontrollera att det virtuella värdnätverket inte är länkat till en privat DNS-zon.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>Behöver jag en RDP- eller SSH-klient?

Nej. Du behöver ingen RDP- eller SSH-klient för att komma åt RDP/SSH till din virtuella Azure-dator i din Azure Portal. Använd [Azure Portal](https://portal.azure.com) för att få RDP/SSH-åtkomst till den virtuella datorn direkt i webbläsaren.

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Behöver jag en agent som körs på den virtuella Azure-datorn?

Nej. Du behöver inte installera en agent eller programvara i webbläsaren eller på din virtuella Azure-dator. Bastion-tjänsten är agentlös och kräver ingen ytterligare programvara för RDP/SSH.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Hur många samtidiga RDP- och SSH-sessioner har Azure Bastion stöd för?

Både RDP och SSH är ett användningsbaserat protokoll. Hög användning av sessioner gör att skyddsvärden stöder ett lägre totalt antal sessioner. Siffrorna nedan förutsätter normala dagliga arbetsflöden.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>Vilka funktioner stöds i en RDP-session?

För närvarande stöds endast kopiering och inklistring av text. Funktioner, till exempel filkopiering, stöds inte. Dela gärna din feedback om nya funktioner på sidan [Azure Bastion Feedback.](https://feedback.azure.com/forums/217313-networking?category_id=367303)

### <a name="does-bastion-hardening-work-with-aadj-vm-extension-joined-vms"></a><a name="aadj"></a>Fungerar Bastion-härdning med virtuella datorer som är tilläggs-ansluten till AADJ VM?

Den här funktionen fungerar inte med datorer som är ansluten till AADJ VM-tillägg med hjälp av Azure AD-användare. Mer information finns i Virtuella [Windows Azure-datorer och Azure AD.](../articles/active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#requirements)

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Vilka webbläsare stöds?

Webbläsaren måste ha stöd för HTML 5. Använd webbläsaren Microsoft Edge eller Google Chrome i Windows. För Apple Mac använder du webbläsaren Google Chrome. Microsoft Edge Chromium stöds även på både Windows och Mac.

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>Var lagrar Azure Bastion kunddata?

Azure Bastion flyttar eller lagrar inte kunddata från den region som de distribueras i.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>Krävs det några roller för att få åtkomst till en virtuell dator?

Följande roller krävs för att upprätta en anslutning:

* Läsarroll på den virtuella datorn
* Läsarroll på nätverkskortet med den virtuella datorns privata IP-adress
* Läsarroll på Azure Bastion-resursen

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Vad är prissättningen?

Mer information finns på sidan med [priser](https://aka.ms/BastionHostPricing).

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Kräver Azure Bastion en RDS CAL för administrativa ändamål på azure-värdbaserade virtuella datorer?

Nej, åtkomst till virtuella Windows Server-datorer Azure Bastion kräver inte en [RDS CAL](https://www.microsoft.com/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) när den endast används för administrativa ändamål.

### <a name="which-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Vilka tangentbordslayouter stöds under fjärrsessionen i Bastion?

Azure Bastion stöder för närvarande tangentbordslayouten en-us-qwerty på den virtuella datorn.  Stöd för andra språk för tangentbordslayout pågår.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Stöds användardefinierad routning (UDR) i ett Azure Bastion undernät?

Nej. UDR stöds inte i ett Azure Bastion undernät.

För scenarier som omfattar både Azure Bastion och Azure Firewall/Network Virtual Appliance (NVA) i samma virtuella nätverk behöver du inte tvinga trafik från ett Azure Bastion-undernät till Azure Firewall eftersom kommunikationen mellan Azure Bastion och dina virtuella datorer är privat. Mer information finns i [Åtkomst till virtuella datorer bakom Azure Firewall med Bastion](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Varför visas felmeddelandet "Sessionen har upphört att gälla" innan Bastion-sessionen startar?

En session bör endast initieras från Azure Portal. Logga in på Azure Portal och starta sessionen igen. Om du går till URL:en direkt från en annan webbläsarsession eller flik förväntas det här felet. Det hjälper till att säkerställa att sessionen är säkrare och att sessionen endast kan nås via Azure Portal.

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Hur gör jag för att hantera distributionsfel?

Granska eventuella felmeddelanden och [skapa en supportbegäran i Azure Portal](../articles/azure-portal/supportability/how-to-create-azure-support-request.md) efter behov. Distributionsfel kan uppstå på grund [av begränsningar, kvoter och begränsningar för Azure-prenumerationer.](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) Mer specifikt kan kunder stöta på en gräns för antalet tillåtna offentliga IP-adresser per prenumeration, vilket gör att Azure Bastion distributionen misslyckas.

### <a name="how-do-i-incorporate-azure-bastion-in-my-disaster-recovery-plan"></a><a name="dr"></a>Hur gör jag för att införliva Azure Bastion i min haveriberedskapsplan?

Azure Bastion distribueras i virtuella nätverk eller peer-kopplade virtuella nätverk och är associerad med en Azure-region. Du ansvarar för att distribuera Azure Bastion till ett haveriberedskapswebbplatsens virtuella nätverk. I händelse av ett haveri i Azure-regionen utför du en redundansåtgärd för dina virtuella datorer till DR-regionen. Använd sedan den Azure Bastion som har distribuerats i REGIONEN FÖR PROGRAM FÖR ATT ansluta till de virtuella datorer som nu distribueras där.
