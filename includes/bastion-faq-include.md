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
ms.openlocfilehash: 9ba0e7173c41e26a698596fa18bf1fc1453f3fb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99628946"
---
### <a name="do-i-need-a-public-ip-on-my-virtual-machine-to-connect-via-azure-bastion"></a><a name="publicip"></a>Behöver jag en offentlig IP-adress på min virtuella dator för att ansluta via Azure skydds?

Nej. När du ansluter till en virtuell dator med hjälp av Azure skydds behöver du inte någon offentlig IP-adress på den virtuella Azure-dator som du ansluter till. Skydds-tjänsten öppnar RDP/SSH-sessionen/-anslutningen till den virtuella datorn via den privata IP-adressen för den virtuella datorn i det virtuella nätverket.

### <a name="is-ipv6-supported"></a>Stöds IPv6?

För tillfället stöds inte IPv6. Azure skydds stöder endast IPv4.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>Behöver jag en RDP-eller SSH-klient?

Nej. Du behöver inte en RDP-eller SSH-klient för att få åtkomst till RDP/SSH till din virtuella Azure-dator i din Azure Portal. Använd [Azure Portal](https://portal.azure.com) så att du kan få RDP/SSH-åtkomst till den virtuella datorn direkt i webbläsaren.

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Behöver jag en agent som körs på den virtuella Azure-datorn?

Nej. Du behöver inte installera en agent eller någon program vara i din webbläsare eller din virtuella Azure-dator. Skydds-tjänsten är utan agent och kräver ingen ytterligare program vara för RDP/SSH.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Hur många samtidiga RDP-och SSH-sessioner gör varje Azure skydds-support?

Både RDP och SSH är ett användnings-baserat protokoll. Med en hög användning av sessioner kan skydds-värden stödja ett lägre antal sessioner. Siffrorna nedan förutsätter normala dagliga arbets flöden.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>Vilka funktioner stöds i en RDP-session?

För närvarande stöds endast kopiering och inklistring av text. Funktioner, till exempel fil kopiering, stöds inte. Det är kostnads fritt att dela din feedback om nya funktioner på [feedback-sidan för Azure skydds](https://feedback.azure.com/forums/217313-networking?category_id=367303).

### <a name="does-bastion-hardening-work-with-aadj-vm-extension-joined-vms"></a><a name="aadj"></a>Fungerar skydds härdning med AADJ VM Extensions-anslutna virtuella datorer?

Den här funktionen fungerar inte med AADJ VM-tillägg som är anslutna till datorer med Azure AD-användare. Mer information finns i [virtuella Windows Azure-datorer och Azure AD](../articles/active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#requirements).

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Vilka webbläsare stöds?

Använd Microsoft Edge-webbläsaren eller Google Chrome i Windows. För Apple Mac använder du Google Chrome-webbläsaren. Microsoft Edge-krom stöds också både för Windows och Mac.

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>Var lagrar Azure skydds kund information?

Azure-skydds flyttar eller lagrar inte kund information från den region som den distribueras i.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>Krävs det några roller för att få åtkomst till en virtuell dator?

Följande roller krävs för att upprätta en anslutning:

* Läsarroll på den virtuella datorn
* Läsarroll på nätverkskortet med den virtuella datorns privata IP-adress
* Läsarroll på Azure Bastion-resursen

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Vad är prissättningen?

Mer information finns på sidan med [priser](https://aka.ms/BastionHostPricing).

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Kräver Azure-skydds en klient åtkomst licens för fjärr skrivbords tjänster för administrativa orsaker på virtuella datorer i Azure?

Nej, åtkomst till virtuella Windows Server-datorer med Azure skydds kräver inte en [klient åtkomst licens för fjärr skrivbords tjänster](https://www.microsoft.com/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) när den används enbart för administrativa syfte.

### <a name="which-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Vilka tangentbordslayouter stöds under skydds-fjärrsessionen?

Azure skydds stöder för närvarande en-US-QWERTY-tangentbordslayout inuti den virtuella datorn.  Stöd för andra språk för tangentbordslayout är pågående.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Stöds UDR (User-Defined routing) i ett Azure skydds-undernät?

Nej. UDR stöds inte i ett Azure skydds-undernät.

För scenarier som omfattar både Azure-skydds och Azure Firewall/Network Virtual Machine (NVA) i samma virtuella nätverk behöver du inte tvinga trafik från ett Azure skydds-undernät till Azure-brandväggen eftersom kommunikationen mellan Azure skydds och dina virtuella datorer är privat. Mer information finns i [komma åt virtuella datorer bakom Azure Firewall med skydds](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Varför visas ett fel meddelande om att sessionen har upphört att gälla innan skydds-sessionen startar?

En session ska endast initieras från Azure Portal. Logga in på Azure Portal och påbörja sessionen igen. Om du går till URL: en direkt från en annan webbläsarsession eller TABB förväntas det här felet. Det hjälper till att säkerställa att sessionen är säkrare och att sessionen bara kan nås via Azure Portal.

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Hur gör jag för att hantera distributions problem?

Granska eventuella fel meddelanden och [få en supportbegäran i Azure Portal](../articles/azure-portal/supportability/how-to-create-azure-support-request.md) efter behov. Distributions felen kan bero på [begränsningar, kvoter och begränsningar för Azure-prenumerationen](../articles/azure-resource-manager/management/azure-subscription-service-limits.md). Mer specifikt kan kunderna stöta på en gräns för antalet offentliga IP-adresser som tillåts per prenumeration och som gör att Azure skydds-distributionen Miss lyckas.

### <a name="how-do-i-incorporate-azure-bastion-in-my-disaster-recovery-plan"></a><a name="dr"></a>Hur gör jag för att införliva Azure-skydds i min haveri beredskaps plan?

Azure-skydds distribueras i virtuella nätverk eller peered virtuella nätverk och är kopplad till en Azure-region. Du ansvarar för att distribuera Azure-skydds till en haveri beredskaps webbplats VNet. Om ett fel uppstår i Azure-regionen utför du en redundansväxling för dina virtuella datorer till DR-regionen. Använd sedan den Azure skydds-värd som distribueras i DR-regionen för att ansluta till de virtuella datorer som nu har distribuerats där.
