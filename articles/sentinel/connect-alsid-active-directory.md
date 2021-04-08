---
title: Anslut din Alsid för Active Directory till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder Alsid för Active Directory Connector för att hämta Alsid-loggar till Azure Sentinel. Visa Alsid-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 654ecb65068e4321b85594d96e8ca7a7f73cde7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99566799"
---
# <a name="connect-your-alsid-for-active-directory-ad-to-azure-sentinel"></a>Anslut din Alsid för Active Directory (AD) till Azure Sentinel

> [!IMPORTANT]
> Alsid för Active Directory Connector är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Den här artikeln förklarar hur du ansluter din Alsid för AD-lösning till Azure Sentinel. Med Alsid för Active Directory data koppling kan du enkelt ansluta dina Alsid för AD-loggar med Azure Sentinel, så att du kan visa data i arbets böcker, fråga den för att skapa anpassade aviseringar och införliva den för att förbättra undersökningen. Integreringen mellan Alsid för AD och Azure Sentinel använder en Syslog-server med den Log Analytics-agenten installerad. Den använder också en anpassad, inbyggd logg parser som baseras på en Kusto-funktion.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha Skriv behörighet på Azure Sentinel-arbetsytan.

- Din Alsid för AD-lösning måste konfigureras för att exportera loggar via syslog.

## <a name="send-alsid-for-ad-logs-to-azure-sentinel-via-the-syslog-agent"></a>Skicka Alsid för AD-loggar till Azure Sentinel via syslog-agenten

Konfigurera Alsid för AD för att vidarebefordra syslog-meddelanden till din Azure Sentinel-arbetsyta via syslog-agenten.

1. I navigerings menyn i Azure Sentinel väljer du **data kopplingar**.

1. I galleriet **data anslutningar** väljer du **Alsid för Active Directory (för hands version)** och öppnar sedan **kopplings sidan**.

1. Följ anvisningarna på sidan **Alsid för Active Directory** koppling:

    1. Konfigurera en Syslog-server

        1. Om du inte redan har en, kan du skapa en Linux Syslog-server för Alsid för AD för att skicka loggar till. Azure Sentinel stöder **rsyslog** -och **syslog-ng-** daemon. 

        1. Konfigurera syslog-servern så att den returnerar Alsid för AD-loggar i en separat fil.

    1. Konfigurera Alsid för AD för att skicka loggar till syslog-servern

        1. I **Alsid för AD** portal går du till *system*, *konfiguration* och sedan *syslog*. Därifrån kan du skapa en ny syslog-avisering mot syslog-servern. Använd IP-adressen för den Linux-dator som du installerade Linux-agenten på på fjärrservern.

        1. Kontrol lera att loggarna är korrekt insamlade på servern i en separat fil (om du vill göra det kan du använda knappen **testa konfigurations** knappen i *syslog* -aviserings konfigurationen i Alsid för AD).

    1. Installera och publicera Log Analytics agent för Linux

        - Välj en virtuell Azure Linux-dator eller en icke-Azure Linux-dator (fysisk eller virtuell). Följ länkarna och anvisningarna på skärmen. Mer information finns i [Konfigurera din Linux-dator eller-apparat](connect-syslog.md#configure-your-linux-machine-or-appliance) .

    1. Konfigurera loggarna som ska samlas in av Log Analytics agent

        - Välj funktionerna och allvarlighets graderna i konfigurationen för avancerade inställningar för arbets ytan.

            1. Klicka på länken **Öppna konfiguration av avancerade inställningar >** på anslutnings ytan på kopplings sidan.

            1. På skärmen **Avancerade inställningar** väljer du **data** och sedan **anpassade loggar**.

            1. Markera kryss rutan **Använd konfigurationen nedan för mina Linux-datorer** och klicka på **Lägg till**.

            1. Klicka på **Välj fil** för att ladda upp en exempel-ALSID för AD syslog-filen från Linux-datorn som kör syslog-servern och klicka på **Nästa**.

            1. Kontrol lera att **Ange post avgränsare** har angetts till **ny rad** och klicka på **Nästa**.

            1. Välj **Linux** och ange fil Sök vägen till syslog-filen, klicka på **+** och sedan på **Nästa**.

            1. I fältet Namn skriver du *AlsidForADLog* före _CL suffixet och klickar sedan på **Slutför**.
    
Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under **anpassade loggar** i *AlsidForADLog_CL* tabellen.

Den här data kopplingen är beroende av en parser som baseras på en Kusto-funktion som fungerar som förväntat. Använd följande steg för att ställa in den **afad_parser** Kusto-funktionen som ska användas i frågor och arbets böcker.

1. På navigerings menyn i Azure Sentinel väljer du **loggar**.

1. Kopiera följande fråga och klistra in den i frågefönstret.
    ```kusto
    let CodenameTable=datatable(Codename: string, Explanation: string) [
    "test-checker-codename", "This is a test checker",
    "", "Not an alert",
    "C-ADM-ACC-USAGE", "Recent use of the default administrator account",
    "C-UNCONST-DELEG", "Dangerous delegation",
    "C-PASSWORD-DONT-EXPIRE", "Accounts with never expiring passwords",
    "C-USERS-CAN-JOIN-COMPUTERS", "Users allowed to join computers to the domain",
    "C-CLEARTEXT-PASSWORD", "Potential clear-text password",
    "C-PROTECTED-USERS-GROUP-UNUSED", "Protected Users group not used",
    "C-PASSWORD-POLICY", "Weak password policies are applied on users",
    "C-GPO-HARDENING", "Domain without computer-hardening GPOs",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-AAD-CONNECT", "Verify permissions related to AAD Connect accounts",
    "C-AAD-SSO-PASSWORD", "Verify AAD SSO account password last change",
    "C-GPO-SD-CONSISTENCY", "Verify sensitive GPO objects and files permissions",
    "C-DSHEURISTICS", "Domain using a dangerous backward-compatibility configuration",
    "C-DOMAIN-FUNCTIONAL-LEVEL", "Domains have an outdated functional level",
    "C-DISABLED-ACCOUNTS-PRIV-GROUPS", "Disabled accounts in privileged groups",
    "C-DCSHADOW", "Rogue domain controllers",
    "C-DC-ACCESS-CONSISTENCY", "Domain controllers managed by illegitimate users",
    "C-DANGEROUS-TRUST-RELATIONSHIP", "Dangerous trust relationship",
    "C-DANGEROUS-SENSITIVE-PRIVILEGES", "Dangerous sensitive privileges",
    "C-DANG-PRIMGROUPID", "User Primary Group ID",
    "C-BAD-PASSWORD-COUNT", "Brute-force attack detection",
    "C-ADMINCOUNT-ACCOUNT-PROPS", "AdminCount attribute set on standard users",
    "C-ACCOUNTS-DANG-SID-HISTORY", "Accounts having a dangerous SID History attribute",
    "C-ABNORMAL-ENTRIES-IN-SCHEMA", "Dangerous rights in AD's schema",
    "C-GPOLICY-DISABLED-UNLINKED", "Unlinked, disabled or orphan GPO",
    "C-KERBEROS-CONFIG-ACCOUNT", "Kerberos configuration on user account",
    "C-KRBTGT-PASSWORD", "KDC password last change",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-NATIVE-ADM-GROUP-MEMBERS", "Native administrative group members",
    "C-NETLOGON-SECURITY", "Unsecured configuration of Netlogon protocol",
    "C-OBSOLETE-SYSTEMS", "Computers running an obsolete OS",
    "C-PASSWORD-NOT-REQUIRED", "Account that might have an empty password",
    "C-PKI-WEAK-CRYPTO", "Use of weak cryptography algorithms into Active Directory PKI",
    "C-PRE-WIN2000-ACCESS-MEMBERS", "Accounts using a pre-Windows 2000 compatible access control",
    "C-PRIV-ACCOUNTS-SPN", "Privileged accounts running Kerberos services",
    "C-REVER-PWD-GPO", "Reversible passwords in GPO",
    "C-ROOTOBJECTS-SD-CONSISTENCY", "Root objects permissions allowing DCSync-like attacks",
    "C-SDPROP-CONSISTENCY", "Ensure SDProp consistency",
    "C-SENSITIVE-CERTIFICATES-ON-USER", "Ensure SDProp consistency",
    "C-SLEEPING-ACCOUNTS", "Sleeping accounts",
    "C-USER-PASSWORD", "User account using old password",
    "C-USERS-REVER-PWDS", "Reversible passwords"
    ];
    let Common = AlsidForADLog_CL
    | parse RawData with
                         Time:datetime  " "
                         Host:string  " "
                         Product:string "["
                         PID:int "]: \""
                         MessageType:int "\" \""
                         AlertID:int "\" \""
                         Forest:string "\" \""
                         Domain:string "\" "
                         DistinctPart:string;
    let Deviances = Common
    | where MessageType == 0 | parse DistinctPart with "\""
                         Codename:string "\" \""
                         Severity:string "\" \""
                         ADObject:string "\" \""
                         DevianceID:string "\" \""
                         ProfileID:string "\" \""
                         ReasonCodename:string "\" \""
                         EventID:string "\""
                         Attributes:string "\r\n";
    let Changes = Common
    | where MessageType == 1
    | parse kind=regex DistinctPart with "\""
                         ADObject:string "\" \""
                         EventID:string "\" \""
                         EventType:string "\" "
                         Attributes:string "\r?\n";
    union Changes, Deviances
    | project-away DistinctPart, Product, _ResourceId, _SubscriptionId
    | lookup kind=leftouter CodenameTable on Codename;
    ```

1. Klicka på list rutan **Spara** och sedan på **Spara**. I panelen **Spara**

    1. Under **namn** anger du **afad_parser**.

    1. Välj **funktion** under **Spara som**.

    1. Under **funktions Ali Aset** anger du **afad_parser**.

    1. Under **kategori**, anger du **Functions**.

    1. Klicka på **Spara**.

    Funktions appar tar vanligt vis mellan 10 och 15 minuter att aktivera.

Nu är du redo att fråga Alsid för AD-data genom att ange `afad_parser` i den översta raden i frågefönstret.

Se **Nästa steg** -fliken på kopplings sidan för fler fråge exempel.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Alsid för AD till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
