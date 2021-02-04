---
title: Azure automanage för virtuella datorer
description: Lär dig mer om Azure automanage för virtuella datorer.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 7772d57937393da1c48fa2658818d8a1a2b28a1f
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550792"
---
# <a name="azure-automanage-for-virtual-machines"></a>Azure automanage för virtuella datorer

Den här artikeln beskriver hur du hanterar Azure automanage för virtuella datorer, vilket har följande fördelar:

- Virtuella datorer med intelligenta funktioner för att välja bästa praxis för Azure-tjänster
- Konfigurerar automatiskt varje tjänst enligt Azure Best Practices
- Övervakare för drift och korrigeras efter identifiering
- Ger en enkel upplevelse (peka, klicka, ange, glöm)


## <a name="overview"></a>Översikt

Automatisk hantering av Azure för virtuella datorer är en tjänst som eliminerar behovet av att upptäcka, veta hur du ska publicera och hur du konfigurerar vissa tjänster i Azure som kan dra nytta av den virtuella datorn. Dessa tjänster bidrar till att öka tillförlitligheten, säkerheten och hanteringen för virtuella datorer och betraktas som Azure-tjänster för bästa praxis, till exempel [azure uppdateringshantering](../automation/update-management/overview.md) och [Azure Backup](../backup/backup-overview.md) – bara för att ge några.

När du har registrerat dina virtuella datorer i Azure automanage konfigurerar den automatiskt varje tjänst för bästa praxis till de rekommenderade inställningarna. Bästa praxis är olika för var och en av tjänsterna. Ett exempel kan vara Azure Backup, där bästa praxis kan vara att säkerhetskopiera den virtuella datorn en gång om dagen och ha en kvarhållningsperiod på sex månader.

Automatisk hantering i Azure övervakar också driften och korrigeras automatiskt när den upptäcks. Vad det innebär är om den virtuella datorn har registrerats för Azure automanage konfigurerar vi inte bara det per Azures bästa praxis, men vi övervakar datorn för att se till att den fortsätter att följa de bästa metoderna i hela livs cykeln. Om den virtuella datorn stöter på eller avviker från dessa metoder, korrigerar vi den och tar tillbaka datorn i önskat tillstånd.

Slutligen är upplevelsen otroligt enkel.


## <a name="prerequisites"></a>Förutsättningar

Det finns flera förutsättningar att tänka på innan du försöker aktivera Azure automanage på dina virtuella datorer.

- Endast Windows Server-VM
- Virtuella datorer måste finnas i en region som stöds (se punkt nedan)
- Användaren måste ha rätt behörigheter (se stycket nedan)
- Automanage stöder inte sandbox-prenumerationer just nu

Det är också viktigt att Observera att den automatiska hanteringen endast stöder virtuella Windows-datorer som finns i följande regioner: Västeuropa, östra USA, västra USA 2, centrala Kanada, västra centrala USA, Östra Japan.

Du måste ha **deltagar** rollen i resurs gruppen som innehåller dina virtuella datorer för att aktivera autohantering på virtuella datorer med ett befintligt konto för autohantering. Om du aktiverar automanage med ett nytt konto för autohantering behöver du följande behörigheter för din prenumeration: **ägar** roll eller **deltagare** tillsammans med administratörs roller för **användar åtkomst** .

> [!NOTE]
> Om du vill använda automanage på en virtuell dator som är ansluten till en arbets yta i en annan prenumeration måste du ha de behörigheter som beskrivs ovan för varje prenumeration.

## <a name="participating-services"></a>Deltagande tjänster

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services.png" alt-text="Intelligenta onboard-tjänster.":::

Se [Azure automanage för Virtual Machines bästa praxis](virtual-machines-best-practices.md) för den fullständiga listan med deltagande Azure-tjänster, samt de konfigurations profiler som stöds.

 Vi kommer automatiskt att publicera dig till dessa deltagande tjänster. De är viktiga för våra bästa praxis white paper, som du hittar i vårt [ramverk för moln införande](/azure/cloud-adoption-framework/manage/azure-server-management).

För alla dessa tjänster kommer vi automatiskt att registreras, konfigureras automatiskt, övervakas för drift och tjänstassisterad om driften upptäcks.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Aktivera autohantering för virtuella datorer i Azure Portal

I Azure Portal kan du aktivera automanage på en befintlig virtuell dator eller när du skapar en ny virtuell dator. Om du vill ha kortare steg i den här processen kan du gå till [snabb starten för virtuella datorer](quick-create-virtual-machines-portal.md).

Om det är första gången du aktiverar automatisk hantering för din virtuella dator kan du söka i den Azure Portal för automatisk **hantering – metod tips för Azure virtuella datorer**. Klicka på **Aktivera på befintlig virtuell dator**, Välj de virtuella datorer som du vill publicera, klicka på **Välj**, klicka på **Aktivera** och du är klar.

Den enda tid som du kan behöva interagera med den här virtuella datorn för att hantera dessa tjänster är i händelse av att vi försökte åtgärda den virtuella datorn, men det gick inte att göra det. Om vi har åtgärdat den virtuella datorn kommer vi att se till att de är kompatibla igen utan att du varnar dig.


## <a name="configuration-profiles"></a>Konfigurationsprofiler

När du aktiverar automatisk hantering för den virtuella datorn krävs en konfigurations profil. Konfigurations profiler är grunden för den här tjänsten. De definierar exakt vilka tjänster vi kan publicera dina datorer i och i viss utsträckning vad konfigurationen av dessa tjänster skulle vara.

### <a name="default-configuration-profiles"></a>Standard konfigurations profiler

Det finns två konfigurations profiler som är tillgängliga för närvarande.

- **Bästa praxis för Azure virtuella datorer –** konfigurations profilen för utveckling/testning är utformad för dev/test-datorer.
- **Metod tips för virtuella Azure-datorer – produktions** konfigurations profilen är för produktion.

Orsaken till den här differentieringen är att vissa tjänster rekommenderas, baserat på den arbets belastning som körs. I en produktions dator kommer vi till exempel automatiskt att du Azure Backup. För en dev/test-dator skulle dock en säkerhets kopierings tjänst vara en onödig kostnad eftersom dev/test-datorer vanligt vis sänker sin påverkan på verksamheten.

### <a name="customizing-a-configuration-profile-using-preferences"></a>Anpassa en konfigurations profil med hjälp av inställningar

Förutom de standard tjänster som vi har publicerat, kan du konfigurera en viss del av inställningarna. Dessa inställningar är tillåtna inom en uppsättning konfigurations alternativ som inte strider mot våra bästa praxis. Om du till exempel använder Azure Backup kan du definiera frekvensen för säkerhets kopieringen och vilken veckodag den infaller på. Vi kan dock *inte* inaktivera Azure Backup helt.

> [!NOTE]
> I konfigurations profilen för utveckling/testning kommer vi inte att säkerhetskopiera den virtuella datorn.

Du kan justera inställningarna för en standard konfigurations profil via inställningar. Lär dig hur du skapar en inställning [här](virtual-machines-custom-preferences.md).

> [!NOTE]
> Du kan inte ändra konfigurations profilen på den virtuella datorn när den automatiska hanteringen är aktive rad. Du måste inaktivera automatisk hantering för den virtuella datorn och sedan återaktivera automatisk hantering med önskad konfigurations profil och inställningar.


## <a name="automanage-account"></a>Hantera konto

Kontot för automatisk hantering är säkerhets kontexten eller den identitet under vilken de automatiserade åtgärderna utförs. Vanligt vis är alternativet för att hantera konton onödigt att du väljer, men om det var ett Delegerings scenario där du ville dela upp den automatiserade hanteringen av dina resurser (kanske mellan två system administratörer) kan du med det här alternativet definiera en Azure-identitet för var och en av dessa administratörer.

När du aktiverar autohantering på de virtuella datorerna i Azure Portals upplevelsen finns en avancerad listruta på bladet **Aktivera Azure VM Best Practice** som gör att du kan tilldela eller skapa det automatiska hanterings kontot manuellt.

Kontot för att hantera automatiskt kommer att beviljas roller för både **deltagare** och **resurs princip deltagare** till de prenumerationer som innehåller de datorer som du har registrerat i autohantering. Du kan använda samma konto för autohantering på datorer över flera prenumerationer, vilket ger dig behörighet att automatiskt hantera konto **deltagare** och behörigheter för **resurs principer** för alla prenumerationer.

Om den virtuella datorn är ansluten till en Log Analytics arbets yta i en annan prenumeration beviljas det automatiska hanterings kontot både **deltagar** -och **resurs princip deltagare** i den andra prenumerationen.

Om du aktiverar automanage med ett nytt konto för autohantering behöver du följande behörigheter för din prenumeration: **ägar** roll eller **deltagare** tillsammans med administratörs roller för **användar åtkomst** .

Om du aktiverar automanage med ett befintligt konto för autohantering måste du ha **deltagar** rollen i resurs gruppen som innehåller dina virtuella datorer.

> [!NOTE]
> När du inaktiverar metoder för autohantering av den här funktionen, bevaras automatiskt hantera kontots behörigheter för alla associerade prenumerationer. Ta bort behörigheterna manuellt genom att gå till prenumerationens IAM-sida eller ta bort det automatiska hanterings kontot. Det går inte att ta bort det automatiska hanterings kontot om det fortfarande hanterar datorer.


## <a name="status-of-vms"></a>Status för virtuella datorer

I Azure Portal går du till sidan för att **Hantera virtuella datorer med bästa praxis** för att visa en lista över alla dina automatiskt hanterade virtuella datorer. Här visas övergripande status för varje virtuell dator.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Lista över konfigurerade virtuella datorer.":::

Följande information visas för varje virtuell dator i listan: namn, konfigurations profil, konfigurations inställning, status, konto, prenumeration och resurs grupp.

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

I den här artikeln har du lärt dig att den automatiska hanteringen för virtuella datorer ger dig ett sätt för vilket du kan eliminera behovet av, publicera på och konfigurera bästa praxis för Azure-tjänster. Om en dator som du har registrerat för automatisk hantering av virtuella datorer från konfigurations profilerna har kon figurer ATS, så kommer vi automatiskt att sätta igång igen.

Försök att aktivera autohantering för virtuella datorer i Azure Portal.

> [!div class="nextstepaction"]
> [Aktivera automanage för virtuella datorer i Azure Portal](quick-create-virtual-machines-portal.md)