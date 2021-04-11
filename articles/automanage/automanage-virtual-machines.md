---
title: Azure automanage för virtuella datorer
description: Lär dig mer om Azure automanage för virtuella datorer.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 973bd1ac121513a297574bbb37d1663b5a18c345
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276917"
---
# <a name="azure-automanage-for-virtual-machines"></a>Azure automanage för virtuella datorer

Den här artikeln beskriver hur du hanterar Azure automanage för virtuella datorer, vilket har följande fördelar:

- Virtuella datorer med intelligenta funktioner för att välja bästa praxis för Azure-tjänster
- Konfigurerar automatiskt varje tjänst enligt Azure Best Practices
- Övervakare för drift och korrigeras efter identifiering
- Ger en enkel upplevelse (peka, klicka, ange, glöm)


## <a name="overview"></a>Översikt

Automatisk hantering av Azure för virtuella datorer är en tjänst som eliminerar behovet av att upptäcka, veta hur du ska publicera och hur du konfigurerar vissa tjänster i Azure som kan dra nytta av den virtuella datorn. De här tjänsterna anses vara Azure Best Practices-tjänster och hjälper till att förbättra tillförlitligheten, säkerheten och hanteringen för virtuella datorer. Exempel tjänster inkluderar [Azure uppdateringshantering](../automation/update-management/overview.md) och [Azure Backup](../backup/backup-overview.md).

När du har registrerat dina virtuella datorer i Azure automanage konfigureras varje tjänst för bästa praxis till de rekommenderade inställningarna. Bästa praxis är olika för var och en av tjänsterna. Ett exempel kan vara Azure Backup, där bästa praxis kan vara att säkerhetskopiera den virtuella datorn en gång om dagen och ha en kvarhållningsperiod på sex månader.

Automatisk hantering i Azure övervakar också driften och korrigeras automatiskt när den upptäcks. Vad det innebär är om den virtuella datorn har registrerats för Azure automanage konfigurerar vi inte bara det per Azures bästa praxis, men vi övervakar datorn för att se till att den fortsätter att följa de bästa metoderna i hela livs cykeln. Om den virtuella datorn stöter på eller avviker från dessa metoder (till exempel om en tjänst är offboarded), korrigerar vi den och tar tillbaka datorn i önskat tillstånd.

## <a name="prerequisites"></a>Förutsättningar

Det finns flera förutsättningar att tänka på innan du försöker aktivera Azure automanage på dina virtuella datorer.

- [Windows Server-versioner](automanage-windows-server.md#supported-windows-server-versions) och [Linux-distributioner](automanage-linux.md#supported-linux-distributions-and-versions) som stöds
- Virtuella datorer måste finnas i en region som stöds (se nedan)
- Användaren måste ha rätt behörigheter (se nedan)
- Automanage stöder inte sandbox-prenumerationer just nu

### <a name="supported-regions"></a>Regioner som stöds
Den automatiska hanteringen stöder bara virtuella datorer som finns i följande regioner:
* Europa, västra
* Europa, norra
* Central US
* East US
* USA, östra 2
* USA, västra
* USA, västra 2
* Kanada, centrala
* USA, västra centrala
* USA, södra centrala
* Japan, östra
* Storbritannien, södra
* Australien, östra
* Australien, sydöstra

### <a name="required-rbac-permissions"></a>Nödvändiga RBAC-behörigheter
Ditt konto kräver något annorlunda RBAC-roller beroende på om du aktiverar automanage med ett nytt automatiskt hanterings konto.

Om du aktiverar automanage med ett nytt konto för autohantering:
* **Ägar** rollen för den eller de prenumerationer som innehåller dina virtuella datorer, _**eller**_
* Administratörs roller för **deltagare** och **användar åtkomst** i de prenumerationer som innehåller dina virtuella datorer

Om du aktiverar automanage med ett befintligt konto för autohantering:
* **Deltagar** rollen i resurs gruppen som innehåller dina virtuella datorer

Kontot för att hantera automatiskt beviljas **bidrags** deltagare och behörigheter för **resurs principer** för att utföra åtgärder på autohanterade datorer.

> [!NOTE]
> Om du vill använda automanage på en virtuell dator som är ansluten till en arbets yta i en annan prenumeration måste du ha de behörigheter som beskrivs ovan för varje prenumeration.

## <a name="participating-services"></a>Deltagande tjänster

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services.png" alt-text="Intelligenta onboard-tjänster.":::

En fullständig lista över deltagande Azure-tjänster och deras miljö som stöds finns i följande avsnitt:
- [Autohantera för Linux](automanage-linux.md)
- [Autohantera för Windows Server](automanage-windows-server.md)

 Vi kommer automatiskt att publicera dig till dessa deltagande tjänster. De är viktiga för våra bästa praxis white paper, som du hittar i vårt [ramverk för moln införande](/azure/cloud-adoption-framework/manage/azure-server-management).

För alla dessa tjänster kommer vi att automatiskt registrera, konfigurera automatiskt, övervaka för drift och tjänstassisterad om driften upptäcks.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Aktivera autohantering för virtuella datorer i Azure Portal

I Azure Portal kan du aktivera automanage på en befintlig virtuell dator eller när du skapar en ny virtuell dator. Om du vill ha kortare steg i den här processen kan du gå till [snabb starten för virtuella datorer](quick-create-virtual-machines-portal.md).

Om det är första gången du aktiverar automatisk hantering för din virtuella dator kan du söka i den Azure Portal för automatisk **hantering – metod tips för Azure virtuella datorer**. Klicka på **Aktivera på befintlig virtuell dator**, Välj de virtuella datorer som du vill publicera, klicka på **Välj**, klicka på **Aktivera** och du är klar.

Den enda tid som du kan behöva interagera med den här virtuella datorn för att hantera dessa tjänster är i händelse av att vi försökte åtgärda den virtuella datorn, men det gick inte att göra det. Om vi har åtgärdat den virtuella datorn kommer vi att se till att de är kompatibla igen utan att du varnar dig. Mer information finns i [status för virtuella datorer](#status-of-vms).

## <a name="enabling-automanage-for-vms-using-azure-policy"></a>Aktivera automanage för virtuella datorer med hjälp av Azure Policy
Du kan också aktivera autohantering på virtuella datorer i skala med hjälp av den inbyggda Azure Policy. Principen har en DeployIfNotExists-påverkan, vilket innebär att alla kvalificerade virtuella datorer som finns inom omfånget för principen registreras automatiskt för att hantera bästa metoder för virtuella datorer.

En direkt länk till principen finns [här](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3).

### <a name="how-to-apply-the-policy"></a>Tillämpa principen
1. Klicka på knappen **tilldela** när du visar princip definitionen
1. Välj den omfattning som du vill tillämpa principen på (kan vara hanterings grupp, prenumeration eller resurs grupp)
1. Under **parametrar** anger du parametrar för kontot för automatisk hantering av konton, konfigurations profil och effekter (resultatet bör vanligt vis vara DeployIfNotExists)
    1. Om du inte har ett konto för automatiskt hantering måste du [skapa ett](#create-an-automanage-account).
1. Under **reparation** markerar du kryss rutan "Klicka på en reparations uppgift". Detta utför onboarding för att hantera automatiskt.
1. Klicka på **Granska + skapa** och kontrol lera att alla inställningar ser lämpliga ut.
1. Klicka på **Skapa**.

## <a name="environment-configuration"></a>Miljökonfiguration

När du aktiverar autohantering för den virtuella datorn krävs en miljö. Miljöer är grunden för den här tjänsten. De definierar vilka tjänster som vi kan använda för att publicera dina datorer i en viss utsträckning vad konfigurationen av dessa tjänster skulle vara.

### <a name="default-environments"></a>Standard miljöer

Det finns två miljöer som är tillgängliga för närvarande.

- **Utvecklings-och test** miljö är utformad för dev/test-datorer.
- **Produktions** miljön är för produktion.

Orsaken till den här differentieringen är att vissa tjänster rekommenderas, baserat på den arbets belastning som körs. I en produktions dator kommer vi till exempel automatiskt att du Azure Backup. För en dev/test-dator skulle dock en säkerhets kopierings tjänst vara en onödig kostnad eftersom dev/test-datorer vanligt vis sänker sin påverkan på verksamheten.

### <a name="customizing-an-environment-using-preferences"></a>Anpassa en miljö med hjälp av inställningar

Förutom de standard tjänster som vi har publicerat, kan du konfigurera en viss del av inställningarna. De här inställningarna tillåts inom en uppsättning konfigurations alternativ. Om du till exempel använder Azure Backup kan du definiera frekvensen för säkerhets kopieringen och vilken veckodag den infaller på.

> [!NOTE]
> I utvecklings-och test miljön säkerhets kopie ras inte den virtuella datorn alls.

Du kan justera inställningarna för en standard miljö via inställningar. Lär dig hur du skapar en inställning [här](virtual-machines-custom-preferences.md).

> [!NOTE]
> Du kan inte ändra enivonrment-konfigurationen på den virtuella datorn när den automatiska hanteringen är aktive rad. Du måste inaktivera autohantering för den virtuella datorn och sedan återaktivera autohantering med önskad miljö och preferenser.

En fullständig lista över deltagande Azure-tjänster och om de stöder inställningar finns här:
- [Autohantera för Linux](automanage-windows-server.md)
- [Autohantera för Windows Server](automanage-windows-server.md)


## <a name="automanage-account"></a>Hantera konto

Kontot för automatisk hantering är säkerhets kontexten eller den identitet under vilken de automatiserade åtgärderna utförs. Vanligt vis är alternativet för att hantera konton onödigt att du väljer, men om det var ett Delegerings scenario där du ville dela upp den automatiserade hanteringen av dina resurser (kanske mellan två system administratörer) kan du med det här alternativet definiera en Azure-identitet för var och en av dessa administratörer.

När du aktiverar autohantering på de virtuella datorerna i Azure Portals upplevelsen finns en avancerad listruta på bladet **Aktivera Azure VM Best Practice** som gör att du kan tilldela eller skapa det automatiska hanterings kontot manuellt.

Kontot för att hantera automatiskt kommer att beviljas roller för både **deltagare** och **resurs princip deltagare** till de prenumerationer som innehåller de datorer som du har registrerat i autohantering. Du kan använda samma konto för autohantering på datorer över flera prenumerationer, vilket ger dig behörighet att automatiskt hantera konto **deltagare** och behörigheter för **resurs principer** för alla prenumerationer.

Om den virtuella datorn är ansluten till en Log Analytics arbets yta i en annan prenumeration beviljas det automatiska hanterings kontot både **deltagar** -och **resurs princip deltagare** i den andra prenumerationen.

Om du aktiverar automanage med ett nytt konto för autohantering behöver du följande behörigheter för din prenumeration: **ägar** roll eller **deltagare** tillsammans med administratörs roller för **användar åtkomst** .

Om du aktiverar automanage med ett befintligt konto för autohantering måste du ha **deltagar** rollen i resurs gruppen som innehåller dina virtuella datorer.

> [!NOTE]
> När du inaktiverar metoder för autohantering av den här funktionen, bevaras automatiskt hantera kontots behörigheter för alla associerade prenumerationer. Ta bort behörigheterna manuellt genom att gå till prenumerationens IAM-sida eller ta bort det automatiska hanterings kontot. Det går inte att ta bort det automatiska hanterings kontot om det fortfarande hanterar datorer.

### <a name="create-an-automanage-account"></a>Skapa ett konto för autohantering
Du kan skapa ett konto för autohantering med hjälp av portalen eller med en ARM-mall.

#### <a name="portal"></a>Portal
1. Navigera till bladet **Hantera automatiska inställningar** i portalen
1. Klicka på **Aktivera på befintlig dator**
1. Under **Avancerat** klickar du på "skapa ett nytt konto"
1. Fyll i de obligatoriska fälten och klicka på **skapa**

#### <a name="arm-template"></a>ARM-mall
Spara följande ARM-mall som `azuredeploy.json` och kör följande kommando: `az deployment group create --resource-group <resource group name> --template-file azuredeploy.json`

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automanageAccountName": {
            "type": "String"
        },
        "location": {
            "type": "String"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "type": "Microsoft.Automanage/accounts",
            "name": "[parameters('automanageAccountName')]",
            "location": "[parameters('location')]",
            "identity": {
                "type": "SystemAssigned"
            }
        }
    ]
}
```

## <a name="status-of-vms"></a>Status för virtuella datorer

I Azure Portal går du till sidan för att **Hantera virtuella datorer med bästa praxis** för att visa en lista över alla dina automatiskt hanterade virtuella datorer. Här visas övergripande status för varje virtuell dator.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Lista över konfigurerade virtuella datorer.":::

Följande information visas för varje virtuell dator i listan: namn, miljö, konfigurations inställningar, status, operativ system, konto, prenumeration och resurs grupp.

**Status** kolumnen kan visa följande tillstånd:
- *Pågående* – den virtuella datorn har precis Aktiver ATS och konfigureras
- *Konfigurerad* – den virtuella datorn har kon figurer ATS och ingen avvikelse har upptäckts
- *Misslyckades* – den virtuella datorn har inträffat och vi kunde inte åtgärda det
- *Väntar* – den virtuella datorn körs inte för tillfället och den automatiska hanteringen försöker att publicera eller reparera den virtuella datorn när den körs nästa gång

Om du ser **status** som *misslyckad* kan du felsöka distributionen via resurs gruppen som den virtuella datorn finns i. Gå till **resurs grupper**, välj din resurs grupp, klicka på **distributioner** och se statusen *misslyckades* där, med fel information.


## <a name="disabling-automanage-for-vms"></a>Inaktiverar autohantering för virtuella datorer

Du kan välja en dag för att inaktivera autohantering på vissa virtuella datorer. Din dator kör till exempel viss superkänslig säker arbets belastning och du behöver låsa den ännu mer än Azure, så du måste konfigurera datorn utanför Azures bästa praxis.

Om du vill göra det i Azure Portal går du till sidan för att **Hantera virtuella Azure-datorer med de bästa metoderna** för att visa en lista över alla dina automatiskt hanterade virtuella datorer. Markera kryss rutan bredvid den virtuella dator som du vill inaktivera från autohantering och klicka sedan på knappen **inaktivera autohantering** .

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Inaktiverar autohantering på en virtuell dator.":::

Läs noggrant igenom meddelande tjänsten i det resulterande popup-meddelandet innan du godkänner **inaktive ring**.

> [!NOTE]
> Genom att inaktivera automanagement i en virtuell dator får du följande beteende:
>
> - Konfigurationen av den virtuella datorn och de tjänster som den har publicerats till ändras inte.
> - Avgifter som debiteras av dessa tjänster förblir fakturerbara och fortsätter att uppkomma.
> - Alla beteenden för autohantering stoppas omedelbart.


Först och främst kommer vi inte att stänga av den virtuella datorn från någon av de tjänster som vi har publicerat till och konfigurerat. Avgifterna för dessa tjänster kommer att fortsätta vara fakturerbara. Om det behövs måste du stänga av kortet. Alla beteenden för automatiskt hantering stoppas omedelbart. Till exempel kommer vi inte längre att övervaka den virtuella datorn för drift.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig att den automatiska hanteringen för virtuella datorer ger dig ett sätt för vilket du kan eliminera behovet av, publicera på och konfigurera bästa praxis för Azure-tjänster. Dessutom, om en dator som du har registrerat för automatisk hantering för virtuella datorer från miljön, så kommer vi automatiskt att se till att kompatibiliteten fungerar igen.

Försök att aktivera autohantering för virtuella datorer i Azure Portal.

> [!div class="nextstepaction"]
> [Aktivera automanage för virtuella datorer i Azure Portal](quick-create-virtual-machines-portal.md)