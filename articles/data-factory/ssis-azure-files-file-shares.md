---
title: Öppna och spara filer med SSIS-paket som distribuerats i Azure
description: Lär dig hur du öppnar och sparar filer lokalt och i Azure när du lyfter och byter SSIS-paket som använder lokala fil system i SSIS i Azure
ms.date: 06/27/2018
ms.topic: conceptual
ms.prod: sql
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 97ed9b6f3f21b6d9e220c1f4e17d4ba6b0d8b485
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555516"
---
# <a name="open-and-save-files-on-premises-and-in-azure-with-ssis-packages-deployed-in-azure"></a>Öppna och spara filer lokalt och i Azure med SSIS-paket som distribueras i Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln beskriver hur du öppnar och sparar filer lokalt och i Azure när du lyfter och Skift SSIS-paket som använder lokala fil system i SSIS i Azure.

## <a name="save-temporary-files"></a>Spara temporära filer

Om du behöver lagra och bearbeta temporära filer under en enskild paket körning kan paket använda den aktuella arbets katalogen ( `.` ) eller tillfälliga mappen ( `%TEMP%` ) för dina Azure-SSIS integration runtime noder.

## <a name="use-on-premises-file-shares"></a>Använd lokala fil resurser

Om du vill fortsätta att använda lokala **fil resurser** när du lyfter och flyttar paket som använder lokala fil system till SSIS i Azure gör du följande:

1. Överföra filer från lokala fil system till lokala fil resurser.

2. Anslut de lokala fil resurserna till ett virtuellt Azure-nätverk.

3. Anslut Azure-SSIS IR till samma virtuella nätverk. Mer information finns i [ansluta en Azure-SSIS integration runtime till ett virtuellt nätverk](./join-azure-ssis-integration-runtime-virtual-network.md).

4. Anslut Azure-SSIS IR till lokala fil resurser i samma virtuella nätverk genom att ställa in autentiseringsuppgifter som använder Windows-autentisering. Mer information finns i [ansluta till data och fil resurser med Windows-autentisering](ssis-azure-connect-with-windows-auth.md).

5. Uppdatera lokala fil Sök vägar i dina paket till UNC-sökvägar som pekar på lokala fil resurser. Till exempel uppdatera `C:\abc.txt` till `\\<on-prem-server-name>\<share-name>\abc.txt` .

## <a name="use-azure-file-shares"></a>Använd Azure-filresurser

Om du vill använda **Azure Files** när du lyfter och flyttar paket som använder lokala fil system till SSIS i Azure gör du följande:

1. Överför filer från lokala fil system till Azure Files. Mer information finns i [Azure Files](https://azure.microsoft.com/services/storage/files/).

2. Anslut Azure-SSIS IR till Azure Files genom att konfigurera autentiseringsuppgifter som använder Windows-autentisering. Mer information finns i [ansluta till data och fil resurser med Windows-autentisering](ssis-azure-connect-with-windows-auth.md).

3. Uppdatera lokala fil Sök vägar i dina paket till UNC-sökvägar som pekar på Azure Files. Till exempel uppdatera `C:\abc.txt` till `\\<storage-account-name>.file.core.windows.net\<share-name>\abc.txt` .

## <a name="next-steps"></a>Nästa steg

- Distribuera dina paket. Mer information finns i [distribuera ett SSIS-projekt till Azure med SSMS](/sql/integration-services/ssis-quickstart-deploy-ssms).
- Kör dina paket. Mer information finns i [köra SSIS-paket i Azure med SSMS](/sql/integration-services/ssis-quickstart-run-ssms).
- Schemalägg dina paket. Mer information finns i [Schemalägga SSIS-paket i Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).