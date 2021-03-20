---
title: Azure Key Vault versioner
description: Olika versioner av Azure Key Vault
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: mbaldwin
ms.openlocfilehash: 2396aac67a17352987d9d6e3b49535c19ffe8917
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792386"
---
# <a name="key-vault-versions"></a>Key Vault-versioner

Här är what's nya med Azure Key Vault. Nya funktioner och förbättringar visas också på [Azure updates Key Vault-kanalen](https://azure.microsoft.com/updates/?category=security&query=Key%20vault).

## <a name="june-2020"></a>Juni 2020

Azure Monitor för Key Vault finns nu som för hands version.  Azure Monitor ger omfattande övervakning av dina nyckel valv genom att leverera en enhetlig vy över dina Key Vaults begär Anden, prestanda, problem och svars tider. Mer information finns i [Azure Monitor för Key Vault (för hands version).](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="may-2020"></a>Maj 2020

Key Vault "ta med din egen nyckel" (BYOK) är nu allmänt tillgänglig. Se [Azure Key Vault BYOK-specifikationen](../keys/byok-specification.md)och lär dig hur du [importerar HSM-skyddade nycklar till Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>Mars 2020

Privata slut punkter finns nu i för hands versionen. Med tjänsten Azure Private Link kan du komma åt Azure Key Vault och Azure-värdbaserade kund-/partner tjänster via en privat slut punkt i det virtuella nätverket.  Lär dig hur du [integrerar Key Vault med en privat Azure-länk](private-link-service.md).

## <a name="2019"></a>2019

- Version av nästa generations Azure Key Vault SDK: er. Exempel på hur de används finns i Azure Key Vault hemliga snabb starter för [python](../secrets/quick-create-python.md), [.net](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md)och [Node.js](../secrets/quick-create-node.md)
- Nya Azure-principer för att hantera nyckel valvs certifikat. Se [Azure policy inbyggda definitioner för Key Vault](../policy-reference.md).
- Azure Key Vault tillägget för virtuell dator är nu allmänt tillgängligt.  Se [Key Vault tillägg för virtuell dator för Linux](../../virtual-machines/extensions/key-vault-linux.md) och [Key Vault tillägg för virtuell dator för Windows](../../virtual-machines/extensions/key-vault-windows.md).
- Händelse driven hemligheter hanteras för Azure Key Vault nu tillgängligt i Azure Event Grid. Mer information finns i [Event Grid schema för händelser i Azure Key Vault] (.. /.. /Event-Grid/Event-schema-Key-Vault.MD] och lär dig hur du [tar emot och svarar på nyckel valvs meddelanden med Azure Event Grid](event-grid-tutorial.md).

## <a name="2018"></a>2018

Nya funktioner och integreringar som lanseras i år:

- Integrering med Azure Functions. Ett exempel scenario som utnyttjar [Azure Functions](../../azure-functions/index.yml) för nyckel valvs åtgärder finns i [Automatisera rotationen av en hemlighet](../secrets/tutorial-rotation.md). 
- [Integrering med Azure Databricks](/azure/databricks/scenarios/store-secrets-azure-key-vault). Med det här alternativet stöder Azure Databricks nu två typer av hemliga omfång: Azure Key Vault-och Databricks. Mer information finns i [skapa en Azure Key Vault hemligt hemligt omfång](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Tjänst slut punkter för virtuella nätverk för Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Nya funktioner som lanseras i år:

- Hanterade lagrings konto nycklar. Funktionen lagrings konto nycklar lades till enklare integrering med Azure Storage. I avsnittet Översikt finns mer information, [Översikt över hanterade lagrings konto nycklar](../secrets/overview-storage-keys.md).
- Mjuk borttagning. Funktionen mjuk borttagning förbättrar data skyddet för nyckel valven och nyckel valvs objekt. Se översikts avsnittet för mer information, [Översikt över mjuk borttagning](./soft-delete-overview.md).

## <a name="2015"></a>2015

Nya funktioner som lanseras i år:
- Certifikat hantering. Lades till som en funktion i GA version 2015-06-01 den 26 september 2016.

Allmän tillgänglighet (version 2015-06-01) presenterades den 24 juni 2015. Följande ändringar har gjorts i den här versionen: 
- Ta bort ett fält för nyckel användning borttaget.
- Hämta information om ett nyckel-"use"-fält som har tagits bort.
- Importera en nyckel till ett valv-"use"-fält borttaget.
- Återställa en nyckel-"use"-fält som har tagits bort.     
- Ändrade "RSA_OAEP" till "RSA-OAEP" för RSA-algoritmer. Se [om nycklar, hemligheter och certifikat](about-keys-secrets-certificates.md).    
 
Den andra för hands versionen (version 2015-02-01-för hands version) presenterades 20 april 2015. Mer information finns i blogg inlägget [REST API Update](/archive/blogs/kv/rest-api-update) . Följande uppgifter har uppdaterats:
 
- Visa en lista med nycklarna i ett valv som har lagt till stöd för sid brytning.
- Visar en lista över versioner av en åtgärd som har lagts till för att visa en lista över versioner av en nyckel.  
- Lista hemligheter i ett valv – stöd för sid brytning.
- Visar en lista över versioner av en hemlighet – Lägg till-åtgärd för att visa en lista över versioner av en hemlighet.  
- Alla åtgärder som har lagt till skapade/uppdaterade tidsstämplar i attribut.  
- Skapa en hemlighet-tillagd innehålls typ för hemligheter.
- Skapa en nyckel tillagd tagg som valfri information.
- Skapa en hemligt tillagda taggar som valfri information.
- Uppdatera en nyckel – tillagda taggar som valfri information.
- Uppdatera en hemligt tillagda taggar som valfri information.
- Ändrade Max storlek för hemligheter från 10 K till 25 kB byte. Se, [om nycklar, hemligheter och certifikat](about-keys-secrets-certificates.md).    
 
## <a name="2014"></a>2014
 
Första för hands versionen (version 2014-12-08 – för hands version) presenterades den 8 januari 2015.  
 
## <a name="next-steps"></a>Nästa steg

- [Om nycklar, hemligheter och certifikat](about-keys-secrets-certificates.md)
- [Nycklar](../keys/index.yml)
- [Hemligheter](../secrets/index.yml)
- [Certifikat](../certificates/index.yml)