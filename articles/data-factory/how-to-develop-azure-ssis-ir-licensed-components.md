---
title: Installera licensierade komponenter för Azure-SSIS integration runtime
description: Lär dig hur en ISV kan utveckla och installera betalda eller licensierade anpassade komponenter för integrerings körningen för Azure-SSIS
ms.service: data-factory
author: swinarko
ms.author: sawinark
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/09/2020
ms.openlocfilehash: fd62822e111346ee9a81a5d1bcce55191b19da02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386296"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Installera betalda eller licensierade anpassade komponenter för Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln beskriver hur en ISV kan utveckla och installera betalda eller licensierade anpassade komponenter för SQL Server Integration Services-paket (SSIS) som körs i Azure i integrerings körningen för Azure-SSIS.

## <a name="the-problem"></a>Problemet

Typen av Azure-SSIS integration runtime visar flera utmaningar, vilket gör de typiska licens metoderna som används för den lokala installationen av anpassade komponenter olämpliga. Därför kräver Azure-SSIS IR en annan metod.

-   Noderna i Azure-SSIS IR är temporära och kan tilldelas eller släppas när som helst. Du kan till exempel starta eller stoppa noder för att hantera kostnaden eller skala upp och ned genom olika noder. Därför är det inte längre att binda en komponent licens från tredje part till en viss nod med hjälp av datorspecifik information, till exempel MAC-adress eller CPU-ID.

-   Du kan också skala Azure-SSIS IR in eller ut, så att antalet noder kan krympa eller expandera när som helst.

## <a name="the-solution"></a>Lösningen

Som ett resultat av begränsningarna för traditionella licens metoder som beskrivs i föregående avsnitt, tillhandahåller Azure-SSIS IR en ny lösning. I den här lösningen används Windows-miljövariabler och SSIS-systemvariabler för licens bindning och validering av komponenter från tredje part. ISV: er kan använda dessa variabler för att hämta unik och beständig information för en Azure-SSIS IR, till exempel kluster-ID och antal klusternoder. Med den här informationen kan ISV: er sedan binda licensen för sin komponent till ett Azure-SSIS IR *som ett kluster*. Den här bindningen använder ett ID som inte ändras när kunder startar eller stoppar, skalar upp eller ned, skalar in eller ut eller omkonfigurerar Azure-SSIS IR på något sätt.

I följande diagram visas typiska installations-, aktiverings-och licens bindningar och validerings flöden för komponenter från tredje part som använder dessa nya variabler:

![Installation av licensierade komponenter](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Instruktioner
1. ISV: er kan erbjuda sina licensierade komponenter på olika SKU: er eller nivåer (till exempel en nod, upp till 5 noder, upp till 10 noder och så vidare). ISV: en tillhandahåller motsvarande produkt nyckel när kunderna köper en produkt. ISV: en kan också tillhandahålla en Azure Storage BLOB-behållare som innehåller ett installations skript för ISV och tillhör ande filer. Kunder kan kopiera dessa filer till sina egna lagrings behållare och ändra dem med sin egen produkt nyckel (till exempel genom att köra `IsvSetup.exe -pid xxxx-xxxx-xxxx` ). Kunderna kan sedan etablera eller konfigurera om Azure-SSIS IR med SAS-URI: n för deras behållare som parameter. Mer information finns i [Anpassad konfiguration för Azure-SSIS integreringskörning](how-to-configure-azure-ssis-ir-custom-setup.md).

2. När Azure-SSIS IR är etablerad eller omkonfigureras körs ISV-installationen på varje nod för att fråga Windows-miljövariablerna `SSIS_CLUSTERID` och `SSIS_CLUSTERNODECOUNT` . Sedan skickar Azure-SSIS IR sitt kluster-ID och produkt nyckeln för den licensierade produkten till ISV Activation-servern för att generera en aktiverings nyckel.

3. När du har tagit emot aktiverings nyckeln kan ISV-installationen lagra nyckeln lokalt på varje nod (till exempel i registret).

4. När kunder kör ett paket som använder ISV: s licensierade komponent på en nod i Azure-SSIS IR, läser paketet den lokalt lagrade aktiverings nyckeln och verifierar den mot nodens kluster-ID. Paketet kan också rapportera antalet klusternoder till ISV-aktiverings servern.

    Här är ett exempel på kod som verifierar aktiverings nyckeln och rapporterar antalet klusternoder:

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="isv-partners"></a>ISV-partner

Du hittar en lista över ISV-partner som har anpassat sina komponenter och tillägg för Azure-SSIS IR i slutet av det här blogg inlägget- [Enterprise-utgåvan, anpassad installation och tredje parts utöknings barhet för SSIS i ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Enterprise-Edition-Custom-Setup-and-3rd-Party-Extensibility-for/ba-p/388360).

## <a name="next-steps"></a>Nästa steg

-   [Anpassad installation för Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Enterprise-versionen av Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
