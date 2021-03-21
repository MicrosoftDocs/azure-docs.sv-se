---
title: Rekommendationer för Endpoint Protection i Azure Security Center
description: Hur lösningar för slut punkts skydd identifieras och identifieras som felfria.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: 1ce20deed8b26dc5f5bebf4656dd3f1c370d766f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561236"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Endpoint Protection-utvärdering och rekommendationer i Azure Security Center

Azure Security Center tillhandahåller hälso utvärdering av versioner av Endpoint Protection-lösningar som [stöds](security-center-services.md#endpoint-supported) . I den här artikeln beskrivs scenarier som leder Security Center att generera följande två rekommendationer:

* **Installera Endpoint Protection-lösningar på den virtuella datorn**
* **Lös problem med hälso tillstånd för slut punkts skydd på dina datorer**

## <a name="windows-defender"></a>Windows Defender

* Security Center rekommenderar att du **installerar Endpoint Protection-lösningar på den virtuella datorn** när [Get-MpComputerStatus](/powershell/module/defender/get-mpcomputerstatus) körs och resultatet är **AMServiceEnabled: falskt**

* Security Center rekommenderar att du **löser problem med hälso tillstånd för slut punkts skydd på dina datorer** när [Get-MpComputerStatus](/powershell/module/defender/get-mpcomputerstatus) körs och något av följande inträffar:

  * Någon av följande egenskaper är falskt:

    - **AMServiceEnabled**
    - **AntispywareEnabled**
    - **RealTimeProtectionEnabled**
    - **BehaviorMonitorEnabled**
    - **IoavProtectionEnabled**
    - **OnAccessProtectionEnabled**

  * Om en eller båda av följande egenskaper är 7 eller mer:

    - **AntispywareSignatureAge**
    - **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center Endpoint Protection

* Security Center rekommenderar att du **installerar Endpoint Protection-lösningar på den virtuella datorn** när du importerar **SCEPMpModule ("$env:P rogramfiles\microsoft Security Client\MpProvider\MpProvider.psd1")** och kör **Get-MProtComputerStatus** -resultat i **AMServiceEnabled = false**.

* Security Center rekommenderar att du **löser problem med hälso tillstånd för slut punkts skydd på dina datorer** när **Get-MprotComputerStatus** körs och något av följande inträffar:

  * Minst en av följande egenskaper är falsk:

    - **AMServiceEnabled**
    - **AntispywareEnabled**
    - **RealTimeProtectionEnabled**
    - **BehaviorMonitorEnabled**
    - **IoavProtectionEnabled**
    - **OnAccessProtectionEnabled**

  * Om en eller båda av följande uppdateringar av signaturen är större än eller lika med 7:

    * **AntispywareSignatureAge**
    * **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* Security Center rekommenderar att du **installerar Endpoint Protection-lösningar på den virtuella datorn** när någon av följande kontroller inte är uppfyllda:
    - **HKLM: \ SOFTWARE\TrendMicro\Deep säkerhets agent** finns
    - **HKLM: \ SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** finns
    - Filen **dsa_query. cmd** finns i installationsmappen
    - Kör **dsa_query. cmd** -resultat med **komponent. am. mode: analys av Micro-djup säkerhets agent har identifierats**

## <a name="symantec-endpoint-protection"></a>Symantec Endpoint Protection
Security Center rekommenderar att du **installerar Endpoint Protection-lösningar på den virtuella datorn** när någon av följande kontroller inte är uppfyllda:

- **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**
- **HKLM: \ Software\Symantec\Symantec-slutpunkt Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Eller

- **HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**
- **HKLM: \ Software\Wow6432Node\Symantec\Symantec-slutpunkt Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Security Center rekommenderar att du **löser problem med hälso tillstånd för slut punkts skydd på dina datorer** när någon av följande kontroller inte är uppfyllda:

- Kontrol lera Symantec-versionen >= 12: register plats: **HKLM: \ Software\Symantec\Symantec slut punkt Protection\CurrentVersion "-value" PRODUCTVERSION "**
- Kontrol lera Real-Time skydds status: **HKLM: \ Software\Wow6432Node\Symantec\Symantec-slutpunkt Protection\AV\Storages\Filesystem\RealTimeScan\OnOff = = 1**
- Kontrol lera status för uppdatering av signatur: **HKLM\Software\Symantec\Symantec-slutpunkt Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 dagar**
- Kontrol lera fullständig skannings status: **HKLM: \ Software\Symantec\Symantec-slutpunkt Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 dagar**
- Sök efter signatur version nummer sökväg till signatur version för Symantec 12: **register Sök vägar + "CurrentVersion\SharedDefs"-värde "SRTSP"** 
- Sökväg till Signature version för Symantec 14: **register Sök vägar + "CurrentVersion\SharedDefs\SDSDefs"-värde "SRTSP"**

Register Sök vägar:
- **"HKLM: \ Software\Symantec\Symantec Endpoint Protection" + $Path;**
- **"HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee Endpoint Protection för Windows

Security Center rekommenderar att du **installerar Endpoint Protection-lösningar på den virtuella datorn** när någon av följande kontroller inte är uppfyllda:

- **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion** finns
- **HKLM: \ SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

Security Center rekommenderar att du **löser problem med hälso tillstånd för slut punkts skydd på dina datorer** när någon av följande kontroller inte är uppfyllda:

- McAfee-version: **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**
- Hitta Signature-version: **HKLM: \ Software\McAfee\AVSolution\DS\DS-Value "dwContentMajorVersion"**
- Sök efter signatur datum: **HKLM: \ Software\McAfee\AVSolution\DS\DS-Value "szContentCreationDate" >= 7 dagar**
- Sök efter genomsöknings datum: **HKLM: \ Software\McAfee\Endpoint\AV\ODS-Value "LastFullScanOdsRunTime" >= 7 dagar**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>Säkerhet för McAfee-slutpunkten för Linux hot förebyggande 

Security Center rekommenderar att du **installerar Endpoint Protection-lösningar på den virtuella datorn** när någon av följande kontroller inte är uppfyllda:

- Filen **/opt/ISEC/ens/threatprevention/bin/isecav** finns
- **"/opt/ISEC/ens/threatprevention/bin/isecav--version"-** utdata är: **McAfee Name = McAfee-slutpunktens säkerhet för Linux-skydd och McAfee-version >= 10**

Security Center rekommenderar att du **löser problem med hälso tillstånd för slut punkts skydd på dina datorer** när någon av följande kontroller inte är uppfyllda:

- **"/opt/ISEC/ens/threatprevention/bin/isecav--listtask"** returnerar **snabb sökning, fullständig genomsökning** och båda genomsökningarna <= 7 dagar
- **"/opt/ISEC/ens/threatprevention/bin/isecav--listtask"** returnerar **dat-och motor uppdaterings tid** och båda <= 7 dagar
- **"/opt/ISEC/ens/threatprevention/bin/isecav--getoasconfig--Summary"** returnerar status **för åtkomst skanning**

## <a name="sophos-antivirus-for-linux"></a>Sophos Antivirus för Linux 

Security Center rekommenderar att du **installerar Endpoint Protection-lösningar på den virtuella datorn** när någon av följande kontroller inte är uppfyllda:
- Filen **/opt/Sophos-av/bin/savdstatus** avslutas eller söker efter den anpassade platsen **"Readlink $ (som savscan)"**
- **"/opt/Sophos-av/bin/savdstatus--version"** returnerar Sophos-namn = **Sophos Anti-virus och sophos-version >= 9**

Security Center rekommenderar att du **löser problem med hälso tillstånd för slut punkts skydd på dina datorer** när någon av följande kontroller inte är uppfyllda:
- **"/opt/Sophos-av/bin/savlog--MaxAge = 7 | grep-i "schemalagd sökning. \* slutfört | pilslut-1 "**, returnerar ett värde
- **"/opt/Sophos-av/bin/savlog--MaxAge = 7 | grep "skanningen är färdig"** | pilslut-1 ", returnerar ett värde
- **"/opt/Sophos-av/bin/savdstatus--lastupdate"** returnerar lastupdate, vilket ska vara <= 7 dagar 
- **"/opt/Sophos-av/bin/savdstatus-v"** är lika med **"Genomsökning vid åtkomst körs"** 
- **"/opt/Sophos-av/bin/savconfig get LiveProtection"** returnerar aktiverat

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Microsoft-tilläggs loggar för program mot skadlig kod finns på: **%systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware (eller PaaSAntimalware) \1.5.5.x (version #) \CommandExecution.log**

### <a name="support"></a>Support

Om du behöver mer hjälp kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Eller fil en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj få support. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).