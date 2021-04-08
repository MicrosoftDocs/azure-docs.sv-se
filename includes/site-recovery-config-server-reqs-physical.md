---
title: ta med fil
description: ta med fil
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/03/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 3a8a7be6f437687a4de31ce8e0ac62588f64e2eb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96016919"
---
**Krav på konfiguration/Processerver för fysisk server-replikering**

**Komponent** | **Krav** 
--- | ---
**MASKIN VARU INSTÄLLNINGAR** | 
Processorkärnor | 8 
RAM | 16 GB
Antal diskar | 3, inklusive OS-disken, cache-disk för processerver och lagrings enhet för återställning efter fel 
Ledigt disk utrymme (bearbetnings serverns cacheminne) | 600 GB
Ledigt disk utrymme (kvarhållning av disk) | 600 GB
 | 
**PROGRAM VARU INSTÄLLNINGAR** | 
Operativsystem | Windows Server 2012 R2 <br> Windows Server 2016
Nationella inställningar för operativsystem | Engelska (en-us)
Windows Server-roller | Aktivera inte följande roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Grup principer | Aktivera inte dessa grup principer: <br> -Förhindra åtkomst till kommando tolken. <br> -Förhindra åtkomst till verktyg för redigering av registret. <br> – Förtroende logik för bifogade filer. <br> – Aktivera skript körning. <br> [Läs mer](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | -Ingen befintlig standard webbplats <br> – Ingen befintlig webbplats/program lyssnar på port 443 <br>-Aktivera  [Anonym autentisering](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> – Aktivera [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) -inställningen.
IP-adresstyp | Statisk 
| 
**ÅTKOMST INSTÄLLNINGAR** | 
MYSQL | MySQL bör installeras på konfigurations servern. Du kan installera manuellt, eller så kan Site Recovery installera det under distributionen. Om du vill Site Recovery installera, kontrollerar du att datorn kan komma åt http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi .
Webbadresser | Konfigurations servern behöver åtkomst till dessa URL: er (direkt eller via proxy):<br/><br/> Azure AD: `login.microsoftonline.com` ; `login.microsoftonline.us` ; `*.accesscontrol.windows.net`<br/><br/> Överföring av replikeringsdata: `*.backup.windowsazure.com` ; `*.backup.windowsazure.us`<br/><br/> Hantering av replikering: `*.hypervrecoverymanager.windowsazure.com` ; `*.hypervrecoverymanager.windowsazure.us` ; `https://management.azure.com` ; `*.services.visualstudio.com`<br/><br/> Lagrings åtkomst: `*.blob.core.windows.net` ; `*.blob.core.usgovcloudapi.net`<br/><br/> Tidssynkronisering: `time.nist.gov` ; `time.windows.com`<br/><br/> Telemetri (valfritt): `dc.services.visualstudio.com`
Brandvägg | IP-adressbaserade brand Väggs regler ska tillåta kommunikation till Azure-URL: er. Vi rekommenderar att du använder URL-filtrering för att förenkla och begränsa IP-intervall.<br/><br/>**För kommersiella IP-adresser:**<br/><br/>-Tillåt [IP-intervall för Azure-datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653)och HTTPS-porten (443).<br/><br/> – Tillåt IP-adressintervall för USA, västra (som används för Access Control-och identitets hantering).<br/><br/> – Tillåt IP-adressintervall för Azure-regionen för din prenumeration, för att stödja de URL: er som behövs för Azure Active Directory, säkerhets kopiering, replikering och lagring.<br/><br/> **För myndighets-IP: er:**<br/><br/> -Tillåt IP-intervall för Azure Government-datacenter och HTTPS-porten (443).<br/><br/> – Tillåt IP-adressintervall för alla US Gov regioner (Virginia, Texas, Arizona och Iowa) för att stödja de URL: er som krävs för Azure Active Directory, säkerhets kopiering, replikering och lagring.
Portar | Tillåt 443 (kontroll av kanal dirigering)<br/><br/> Tillåt 9443 (data transport) 


**Krav på konfiguration/processervern storlek**

**Processor** | **Minne** | **Cachelagra disk** | **Data ändrings takt** | **Replikerade datorer**
--- | --- | --- | --- | ---
8 virtuella processorer<br/><br/> 2 Sockets * 4 kärnor \@ 2,5 GHz | GB | 300 GB | 500 GB eller mindre | < 100-datorer
12 virtuella processorer<br/><br/> 2 SOCKS * 6 kärnor \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 till 150-datorer
16 virtuella processorer<br/><br/> 2 SOCKS * 8 kärnor \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200-datorer