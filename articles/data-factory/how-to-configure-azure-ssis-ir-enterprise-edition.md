---
title: Etablera Enterprise Edition för Azure-SSIS Integration Runtime
description: I den här artikeln beskrivs funktionerna i Enterprise Edition för Azure-SSIS Integration Runtime och hur det kan etableras
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/09/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: f700729c2d059648b1c3a7e451526aefcb436818
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387554"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Etablera Enterprise Edition för Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Med Enterprise-utgåvan av Azure-SSIS Integration Runtime kan du använda följande avancerade och Premium funktioner:
-   Komponenter för registrering av ändrings data (CDC)
-   Oracle-, Teradata-och SAP BW-anslutningar
-   SQL Server Analysis Services (SSAS) och Azure Analysis Services (AAS) och transformeringar
-   Nedtransformeringar av fuzzy-gruppering och fuzzy-uppslag
-   Transformeringar av villkors extrahering och terms ökning

Vissa av dessa funktioner kräver att du installerar ytterligare komponenter för att anpassa Azure-SSIS IR. Mer information om hur du installerar ytterligare komponenter finns i [anpassad installation för Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Enterprise-funktioner

| **Enterprise-funktioner** | **Förklaringar** |
|---|---|
| CDC-komponenter | Omvandlingen av CDC-källa, kontroll aktivitet och delar av delar förinstalleras på Azure-SSIS IR Enterprise Edition. Om du vill ansluta till Oracle måste du också installera CDC-designer och-tjänsten på en annan dator. |
| Oracle-kopplingar | Anslutnings hanteraren, källan och målet i Oracle förinstalleras på Azure-SSIS IR Enterprise Edition. Du måste också installera OCI-drivrutinen (Oracle Call Interface). om det behövs konfigurerar du TNS (Oracle Transport Network substrat) på Azure-SSIS IR. Mer information finns i [Anpassad konfiguration för Azure-SSIS integreringskörning](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Teradata-kopplingar | Du måste installera Teradata-anslutnings hanteraren,-källan och-målet, samt TPT-API: et (Teradata Parallel deporter) och Teradata ODBC-drivrutinen, på Azure-SSIS IR Enterprise Edition. Mer information finns i [Anpassad konfiguration för Azure-SSIS integreringskörning](how-to-configure-azure-ssis-ir-custom-setup.md). |
| SAP BW anslutningar | SAP BW anslutnings hanteraren, källa och mål har förinstallerats på Azure-SSIS IR Enterprise Edition. Du måste också installera SAP BW driv rutinen på Azure-SSIS IR. Dessa anslutningar stöder SAP BW 7,0 eller tidigare versioner. Om du vill ansluta till senare versioner av SAP BW eller andra SAP-produkter kan du köpa och installera SAP-anslutningar från tredjeparts-ISV: er på Azure-SSIS IR. Mer information om hur du installerar ytterligare komponenter finns i [anpassad installation för Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Analysis Services-komponenter               | Målet för data utvinnings modellens utbildnings mål, dimensions bearbetnings målet och partitionens bearbetnings mål, samt transformeringen för data utvinnings frågor, förinstalleras på Azure-SSIS IR Enterprise Edition. Alla dessa komponenter stöder SQL Server Analysis Services (SSAS), men det är bara partitionen som bearbetar målet som stöder Azure Analysis Services (AAS). För att ansluta till SSAS måste du också [Konfigurera autentiseringsuppgifter för Windows-autentisering i SSISDB](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Förutom dessa komponenter är Analysis Services köra DDL-aktivitet, Analysis Services bearbetnings aktiviteten och uppgiften data utvinnings fråga också förinstallerad på Azure-SSIS IR standard/Enterprise Edition. |
| Nedtransformeringar av fuzzy-gruppering och fuzzy-uppslag  | Transformeringarna för fuzzy-gruppering och fuzzy-uppslag förinstalleras på Azure-SSIS IR Enterprise Edition. Dessa komponenter stöder både SQL Server och Azure SQL Database för lagring av referens data. |
| Transformeringar av villkors extrahering och terms ökning | Transformeringarna av termen extrahering och terms ökning är förinstallerade på Azure-SSIS IR Enterprise Edition. Dessa komponenter stöder både SQL Server och Azure SQL Database för lagring av referens data. |

## <a name="instructions"></a>Instruktioner

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  Hämta och installera [Azure PowerShell](/powershell/azure/install-az-ps).

2.  När du etablerar eller omkonfigurerar Azure-SSIS IR med PowerShell kan du köra `Set-AzDataFactoryV2IntegrationRuntime` med **Enterprise** som värdet för parametern **version** innan du startar Azure-SSIS IR. Här är ett exempel skript:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Nästa steg

-   [Anpassad installation för Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Utveckla betalda eller licensierade anpassade komponenter för integrerings körningen av Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)