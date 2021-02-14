---
title: Använd funktionen diagnostisera anslutning i SSIS integration runtime
description: Felsök anslutnings problem i SSIS-integrerings körningen med hjälp av funktionen diagnostisera anslutning.
ms.service: data-factory
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
ms.date: 06/07/2020
ms.openlocfilehash: 1fb9a0c77a7cdc286c7c206d6eb33e43917ee719
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361867"
---
# <a name="use-the-diagnose-connectivity-feature-in-the-ssis-integration-runtime"></a>Använd funktionen diagnostisera anslutning i SSIS integration runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Det kan finnas anslutnings problem när du kör SQL Server Integration Services-paket (SSIS) i SSIS integration Runtime. De här problemen uppstår särskilt om din SSIS-integration runtime ansluter till det virtuella Azure-nätverket.

Felsök anslutnings problem med hjälp av funktionen *diagnostisera anslutning* för att testa anslutningar. Funktionen finns på sidan övervakning av SSIS integration runtime i Azure Data Factory-portalen.

 ![Övervaka sidan – diagnostisera anslutning](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png)

 ![Övervaka Page-test-anslutning](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)

Använd följande avsnitt om du vill veta mer om de vanligaste felen som inträffar när du testar anslutningar. I varje avsnitt beskrivs:

- Felkod
- Felmeddelande
- Möjliga orsaker till felet
- Rekommenderad (a) lösning (er)

## <a name="error-code-invalidinput"></a>Felkod: InvalidInput

- **Fel meddelande**: "kontrol lera att du har angett rätt information."
- **Möjlig orsak**: inaktuella inaktuella inaktuella inaktuella.
- **Rekommendation**: kontrol lera din inaktuella information.

## <a name="error-code-firewallornetworkissue"></a>Felkod: FirewallOrNetworkIssue

- **Fel meddelande**: "kontrol lera att den här porten är öppen i brand väggen/servern/NSG och att nätverket är stabilt."
- **Möjliga orsaker:**
  - Servern öppnar inte porten.
  - Nätverks säkerhets gruppen nekar utgående trafik på porten.
  - Din NVA/Azure Firewall/lokala brand vägg öppnar inte porten.
- **Rekommenderade**
  - Öppna porten på servern.
  - Uppdatera nätverks säkerhets gruppen för att tillåta utgående trafik på porten.
  - Öppna porten i NVA/Azure brand vägg/lokal brand vägg.

## <a name="error-code-misconfigureddnssettings"></a>Felkod: MisconfiguredDnsSettings

- **Fel meddelande**: "om du använder din egen DNS-server i det virtuella nätverket som du Azure-SSIS IR, kontrollerar du att den kan matcha värd namnet."
- **Möjliga orsaker:**
  -  Det är problem med din anpassade DNS.
  -  Du använder inte ett fullständigt kvalificerat domän namn (FQDN) för det privata värd namnet.
- **Rekommenderade**
  -  Åtgärda ditt anpassade DNS-problem för att kontrol lera att det kan matcha värd namnet.
  -  Använd FQDN. Azure-SSIS IR lägger inte automatiskt till ditt eget DNS-suffix. Använd till exempel **<your_private_server>. contoso.com** i stället för **<your_private_server>**.

## <a name="error-code-servernotallowremoteconnection"></a>Felkod: ServerNotAllowRemoteConnection

- **Fel meddelande**: "kontrol lera att servern tillåter fjärr-TCP-anslutningar via den här porten."
- **Möjliga orsaker:**
  -  Server brand väggen tillåter inte fjärr-TCP-anslutningar.
  -  Servern är inte online.
- **Rekommenderade**
  -  Tillåt fjärr-TCP-anslutningar i Server brand väggen.
  -  Starta servern.
   
## <a name="error-code-misconfigurednsgsettings"></a>Felkod: MisconfiguredNsgSettings

- **Fel meddelande**: "kontrol lera att NSG för ditt VNet tillåter utgående trafik via den här porten. Om du använder Azure ExpressRoute och eller en UDR måste du kontrol lera att den här porten är öppen på brand väggen/servern. "
- **Möjliga orsaker:**
  -  Nätverks säkerhets gruppen nekar utgående trafik på porten.
  -  Din NVA/Azure Firewall/lokala brand vägg öppnar inte porten.
- **Rekommenderade**
  -  Uppdatera nätverks säkerhets gruppen för att tillåta utgående trafik på porten.
  -  Öppna porten i NVA/Azure brand vägg/lokal brand vägg.

## <a name="error-code-genericissues"></a>Felkod: GenericIssues

- **Fel meddelande**: "test anslutningen misslyckades på grund av allmänna problem."
- **Möjlig orsak**: test anslutningen påträffade ett allmänt tillfälligt problem.
- **Rekommendation**: försök att testa anslutningen senare. Kontakta Azure Data Factory support-teamet om du försöker igen.

## <a name="error-code-pspingexecutiontimeout"></a>Felkod: PSPingExecutionTimeout

- **Fel meddelande**: "Testa anslutningens tids gräns, försök igen senare."
- **Möjlig orsak**: anslutnings försöket nådde tids gränsen.
- **Rekommendation**: försök att testa anslutningen senare. Kontakta Azure Data Factory support-teamet om du försöker igen.

## <a name="error-code-networkinstable"></a>Felkod: NetworkInstable

- **Fel meddelande**: "Det gick oregelbundet att testa anslutningen på grund av instabilitet i nätverket".
- **Möjlig orsak**: tillfälligt nätverks problem.
- **Rekommendation**: kontrol lera om Server-eller brand Väggs nätverket är stabilt.

## <a name="next-steps"></a>Nästa steg

- [Distribuera ett SSIS-projekt till Azure med SSMS](/sql/integration-services/ssis-quickstart-deploy-ssms)
- [Köra SSIS-paket i Azure med SSMS](/sql/integration-services/ssis-quickstart-run-ssms)
- [Schemalägg SSIS-paket i Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)