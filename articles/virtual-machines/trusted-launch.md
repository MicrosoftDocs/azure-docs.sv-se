---
title: 'För hands version: betrodd start för virtuella Azure-datorer'
description: Lär dig mer om betrodd start för virtuella Azure-datorer.
author: khyewei
ms.author: khwei
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: conceptual
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: template-concept; references_regions
ms.openlocfilehash: 01c5d4aaa3896e05bc743be309df050471ece5ae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582059"
---
# <a name="trusted-launch-for-azure-virtual-machines-preview"></a>Betrodd start för Azure Virtual Machines (för hands version)

Azure erbjuder betrodd lansering som ett sömlöst sätt att förbättra säkerheten hos virtuella datorer i [generation 2](generation-2.md) . Betrodd lansering skyddar mot avancerade och permanenta angrepps tekniker. Betrodd lansering består av flera koordinerade infrastruktur tekniker som kan aktive ras oberoende av varandra. Varje teknik ger ytterligare ett skydd mot avancerade hot.

> [!IMPORTANT]
> Den betrodda starten kräver att nya virtuella datorer skapas. Du kan inte aktivera betrodd start på befintliga virtuella datorer som ursprungligen skapades utan den.
>
> Den betrodda starten är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. 
>
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="benefits"></a>Fördelar 

- Distribuera virtuella datorer på ett säkert sätt med verifierade start inläsare, OS-kerneler och driv rutiner.
- Skydda nycklar, certifikat och hemligheter på ett säkert sätt i de virtuella datorerna.
- Få insikter och förtroende för hela start kedjans integritet.
- Se till att arbets belastningarna är betrodda och verifierbara.

## <a name="public-preview-limitations"></a>Offentliga begränsningar i förhandsversionen

**Storleks stöd**: alla VM-storlekar i [generation 2](generation-2.md) , förutom:

- Lsv2-serien 
- M-serien 
- Mv2-serien 
- NDv4-serien 
- NVv4-serien

**OS-support**:
- RedHat Enterprise Linux 8,3
- SUSE 15 SP2
- Ubuntu 20,04 LTS
- Ubuntu 18.04 LTS
- Windows Server 2019
- Windows Server 2016
- Windows 10 Pro
- Windows 10 Enterprise

**Regioner**: 
- USA, södra centrala
- Europa, norra

**Prissättning**: ingen ytterligare kostnad för befintliga priser för virtuella datorer.

**Följande funktioner stöds inte i den här för hands versionen**:
- Backup
- Azure Site Recovery
- Delat bildgalleri
- Tillfällig OS-disk
- Delad disk
- Hanterad avbildning
- Azure Dedicated Host 

## <a name="secure-boot"></a>Säker start

Vid roten av den betrodda starten är säker start för den virtuella datorn. Det här läget, som implementeras i inbyggd plattform, skyddar mot installation av skadlig rootkits och start paket. Säker start fungerar för att säkerställa att endast signerade operativ system och driv rutiner kan starta. Den upprättar en "förtroende rot" för program varu stacken på den virtuella datorn. När säker start är aktiverat måste alla start komponenter för operativ systemet (start inläsare, kernel, kernel-drivrutiner) signeras av betrodda utgivare. Både Windows och Select Linux-distributioner stöder säker start. Om den säkra starten inte kan autentisera att avbildningen har signerats av en betrodd utgivare, kommer den virtuella datorn inte att kunna starta. Mer information finns i [Säker start](/windows-hardware/design/device-experiences/oem-secure-boot).

## <a name="vtpm"></a>vTPM

Betrodd start introducerar också vTPM för virtuella Azure-datorer. Det här är en virtualiserad version av en maskin varu [Trusted Platform Module](/windows/security/information-protection/tpm/trusted-platform-module-overview)som är kompatibel med TPM 2.0-specifikationen. Den fungerar som ett dedikerat säkert valv för nycklar och mätningar. Betrodd start tillhandahåller den virtuella datorn med en egen dedikerad TPM-instans som körs i en säker miljö utanför räckvidden för en virtuell dator. VTPM aktiverar [attestering](/windows/security/information-protection/tpm/tpm-fundamentals#measured-boot-with-support-for-attestation) genom att mäta hela den virtuella datorns start kedja (UEFI, OS, system och driv rutiner). 

Betrodd start använder vTPM för att utföra fjärrattestering av molnet. Detta används för hälso kontroller för plattformen och för att fatta förtroendebaserade beslut. Som hälso kontroll kan den betrodda starten kryptografiskt certifiera att den virtuella datorn har startats på rätt sätt. Om processen Miss lyckas, förmodligen på grund av att den virtuella datorn kör en oauktoriserad komponent, kommer Azure Security Center att utfärda integritets aviseringar. Aviseringarna innehåller information om vilka komponenter som inte kunde skicka integritets kontroller.

## <a name="virtualization-based-security"></a>Virtualiseringsbaserad säkerhet

[Virtualiseringsbaserad säkerhet](/windows-hardware/design/device-experiences/oem-vbs) (VBS) använder hypervisorn för att skapa en säker och isolerad region i minnet. Windows använder dessa regioner för att köra olika säkerhetslösningar med ökat skydd mot sårbarheter och skadliga sårbarheter. Med betrodd start kan du aktivera hypervisor-kod integritet (begärda HVCI) och Windows Defender Credential Guard.

BEGÄRDA HVCI är en kraftfull system minskning som skyddar Windows kernel-läges processer mot insprutning och körning av skadlig eller overifierad kod. Det kontrollerar kernel mode-drivrutiner och binärfiler innan de körs, vilket hindrar osignerade filer från att läsas in i minnet. Detta säkerställer att sådan körbar kod inte kan ändras när den har tillåtelse att läsa in. Mer information om VBS och begärda HVCI finns i [virtualiseringsbaserad säkerhet (VBS) och hypervisor-framtvingad kod integritet (begärda hvci)](https://techcommunity.microsoft.com/t5/windows-insider-program/virtualization-based-security-vbs-and-hypervisor-enforced-code/m-p/240571).

Med betrodd start och VBS kan du aktivera Windows Defender Credential Guard. Den här funktionen isolerar och skyddar hemligheter så att endast privilegie rad system program vara kan komma åt dem. Det hjälper till att förhindra obehörig åtkomst till hemligheter och stöld attacker, t. ex. pass-The-hash (PtH)-attacker. Mer information finns i [Credential Guard](/windows/security/identity-protection/credential-guard/credential-guard).


## <a name="security-center-integration"></a>Security Center-integrering

Betrodd lansering är integrerat med Azure Security Center för att se till att de virtuella datorerna är korrekt konfigurerade. Azure Security Center kommer kontinuerligt att utvärdera kompatibla virtuella datorer och utfärda relevanta rekommendationer.

- **Rekommendation för att aktivera säker start** – den här rekommendationen gäller endast för virtuella datorer som stöder betrodd start. Azure Security Center kommer att identifiera virtuella datorer som kan aktivera säker start, men som har inaktiverats. Den utfärdar en rekommendation om låg allvarlighets grad för att aktivera den.
- **Rekommendation för att aktivera vTPM** – om den virtuella datorn har vTPM aktive rad kan Azure Security Center använda den för att utföra gästautentisering och identifiera avancerade hot mönster. Om Azure Security Center identifierar virtuella datorer som stöder betrodd start och har vTPM inaktiverat, kommer den att utfärda en låg allvarlighets grad för att aktivera den. 
- **Hälso bedömning för attestering** – om din virtuella dator har vTPM aktiverat kan en utökning av Azure Security Center fjärrverifiera att din virtuella dator har startats på ett felfritt sätt. Detta kallas för fjärrattestering. Azure Security Center utvärderar en utvärdering som visar statusen för fjärrattestering.

## <a name="azure-defender-integration"></a>Azure Defender-integrering

Om dina virtuella datorer är korrekt konfigurerade med betrodd start kan Azure Defender identifiera och varna dig om problem med VM-hälsa.

- **Avisering om VM-attestering Miss lyckas** -Azure Defender genomför regelbundet attestering på dina virtuella datorer. Detta inträffar även när den virtuella datorn startas. Om attesteringen Miss lyckas utlöses en avisering om medelhög allvarlighets grad.
    VM-attesteringen kan inte utföras av följande orsaker:
    - Den bestyrkade informationen, som innehåller en start logg, avviker från en betrodd bas linje. Detta kan tyda på att ej betrodda moduler har lästs in och att operativ systemet kan komprometteras.
    - Det gick inte att verifiera attesterings offerten som härstammar från vTPM för den verifierade virtuella datorn. Detta kan tyda på att skadlig kod finns och kan fånga upp trafik till vTPM.
    - Attesterings tillägget på den virtuella datorn svarar inte. Detta kan tyda på en denial-of-service-attack av skadlig kod eller en OS-administratör.

    > [!NOTE]
    >  Den här aviseringen är tillgänglig för virtuella datorer med vTPM aktiverat och attesterings tillägget installerat. Säker start måste vara aktiverat för att attestering ska kunna skickas. Attesteringen Miss fungerar om säker start är inaktiverat. Om du måste inaktivera säker start kan du ignorera den här aviseringen för att undvika falska positiva identifieringar.

- **Avisering för obetrodd Linux-kernel-modul** – för betrodd start med säker start aktiverat, är det möjligt att en virtuell dator startar även om en kernel-drivrutin Miss lyckas med verifieringen och inte kan läsas in. Om detta inträffar utfärdar Azure Defender en avisering om låg allvarlighets grad. Även om det inte finns något omedelbart hot, eftersom den ej betrodda driv rutinen inte har lästs in, bör dessa händelser undersökas. Tänk också på följande:
    - Vilken kernel-drivrutin misslyckades? Känner jag till den här driv rutinen och väntar på att den ska läsas in?
    - Är den exakta versionen av driv rutinen jag förväntar mig? Är driv rutinen binärfiler intakt? Om det är en tredjeparts driv rutin, överför leverantören test av OS-kompatibilitet för att få det signerat?


## <a name="faq"></a>Vanliga frågor

Vanliga frågor och svar om betrodd start.

### <a name="why-should-i-use-trusted-launch-what-does-trusted-launch-guard-against"></a>Varför ska jag använda betrodd start? Vad är Trusted Launch Guard mot?

Betrodda lanserings skydd mot start paket, rootkits och skadlig kod på kernel-nivå. De här sofistikerade typerna av skadlig kod körs i kernelläge och förblir dolda för användarna. Exempel:
- Rootkits för inbyggd program vara: de här paketen skriver över den inbyggda program varan för den virtuella datorns BIOS, så att spökprogram kan starta innan operativ systemet. 
- Start paket: de här paketen ersätter OS-startprogrammet så att den virtuella datorn läser in start paketet före operativ systemet.
- Kernel-rootkits: dessa paket ersätter en del av OS-kerneln så att spökprogram kan starta automatiskt när operativ systemet laddas.
- Driv rutins rootkits: dessa paket är till för att vara en av de betrodda driv rutiner som används av operativ systemet för att kommunicera med den virtuella datorns komponenter.

### <a name="what-are-the-differences-between-secure-boot-and-measured-boot"></a>Vad är skillnaderna mellan säker start och uppmätt start?

I en säker start kedja kontrollerar varje steg i Start processen en kryptografisk signatur av de efterföljande stegen. Till exempel kontrollerar BIOS en signatur i inläsaren och inläsaren kontrollerar signaturer för alla kernel-objekt som den läser in och så vidare. Om något av objekten komprometteras stämmer inte signaturen och den virtuella datorn startas inte. Mer information finns i [Säker start](/windows-hardware/design/device-experiences/oem-secure-boot). Den uppmätta starten stoppar inte start processen, den mäter eller beräknar hashen för nästa objekt i kedjan och lagrar hasharna i plattformens konfigurations register (PCRs) på vTPM. Uppmätta start poster används för övervakning av start integritet.

### <a name="what-happens-when-an-integrity-fault-is-detected"></a>Vad händer när ett integritets fel upptäcks?

Betrodd lansering för virtuella Azure-datorer övervakas för avancerade hot. Om sådana hot identifieras aktive ras en avisering. Aviseringar är bara tillgängliga på [Standard-nivån](../security-center/security-center-pricing.md) för Azure Security Center.
Azure Security Center regelbundet utför attestering. Om attesteringen Miss lyckas aktive ras en avisering om medelhög allvarlighets grad. Attestering av betrodd start kan inte utföras av följande orsaker: 
- Den bestyrkade informationen, som innehåller en logg över den betrodda dator basen (TCB), avviker från en betrodd bas linje (t. ex. När säker start är aktiverat). Detta kan tyda på att ej betrodda moduler har lästs in och att operativ systemet kan komprometteras.
- Det gick inte att verifiera attesterings offerten som härstammar från vTPM för den verifierade virtuella datorn. Detta kan tyda på att skadlig kod finns och kan fånga upp trafik till TPM. 
- Attesterings tillägget på den virtuella datorn svarar inte. Detta kan tyda på en denial-of-service-attack av skadlig kod eller en OS-administratör.

  
### <a name="how-does-trusted-launch-compared-to-hyper-v-shielded-vm"></a>Hur jämförs betrodd start med Hyper-V-avskärmad virtuell dator?
Hyper-V-avskärmad virtuell dator är för närvarande endast tillgänglig i Hyper-V. [Den avskärmade virtuella datorn i Hyper-V](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms) distribueras vanligt vis tillsammans med skyddad infrastruktur resurs. En skyddad infrastruktur resurs består av en värd skydds tjänst (HGS), en eller flera skyddade värdar och en uppsättning avskärmade virtuella datorer. Hyper-V-avskärmade virtuella datorer är avsedda att användas i infrastruktur resurser där data och tillstånd för den virtuella datorn måste skyddas från både infrastruktur administratörer och ej betrodda program som körs på Hyper-V-värdarna. Betrodd lansering på den andra kan distribueras som en fristående virtuell dator eller skalnings uppsättningar för virtuella datorer i Azure utan ytterligare distribution och hantering av HGS. Alla betrodda start funktioner kan aktive ras med en enkel ändring i distributions koden eller en kryss ruta på Azure Portal.  

### <a name="how-can-i-convert-existing-vms-to-trusted-launch"></a>Hur kan jag konvertera befintliga virtuella datorer till en betrodd start?
För virtuella datorer av andra generationen riktar sig migreringen till den betrodda starten mot allmän tillgänglighet (GA).

## <a name="next-steps"></a>Nästa steg

Distribuera en [betrodd Launch virtuell dator med hjälp av portalen](trusted-launch-portal.md).