---
title: Ange autentiseringsuppgifter för servern för att identifiera program varu inventering, beroenden och SQL Server instanser och databaser
description: Lär dig hur du anger autentiseringsuppgifter för server i Configuration Manager
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: e72d509f71704bbf8608543df5e819a9b8783935
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562077"
---
# <a name="provide-server-credentials-to-discover-software-inventory-dependencies-and-sql-server-instances-and-databases"></a>Ange autentiseringsuppgifter för servern för att identifiera program varu inventering, beroenden och SQL Server instanser och databaser

Följ den här artikeln för att lära dig hur du lägger till flera autentiseringsuppgifter för server i Konfigurations hanteraren för att utföra program varu inventering (identifiera installerade program), en agent lös beroende analys och identifiera SQL Server instanser och databaser.

> [!Note]
> Identifiering och utvärdering av SQL Server instanser och databaser som körs i din VMware-miljö är nu i för hands version. Om du vill testa funktionen använder du [**den här länken**](https://aka.ms/AzureMigrate/SQL) till att skapa ett projekt i regionen **Australien, östra**. Om du redan har ett projekt i Australien, östra och vill prova den här funktionen måste du se till att du har slutfört dessa [**krav**](how-to-discover-sql-existing-project.md) på portalen.

[Azure Migrate](migrate-appliance.md) -installationen är en förenklad utrustning som används av Azure Migrate: identifiering och utvärdering för att identifiera lokala servrar som körs i VMware-miljön och som skickar Server konfiguration och prestanda-metadata till Azure. Installationen kan också användas för att utföra program varu inventering, en agent lös beroende analys och upptäcka SQL Server instanser och databaser.

Om du vill använda dessa funktioner kan du ange autentiseringsuppgifter för servern genom att följa stegen nedan. Installations programmet försöker automatiskt mappa autentiseringsuppgifterna till servrarna för att utföra identifierings funktionerna.

## <a name="add-credentials-for-servers-running-in-vmware-environment"></a>Lägg till autentiseringsuppgifter för servrar som körs i VMware-miljön

### <a name="types-of-server-credentials-supported"></a>Typer av autentiseringsuppgifter som stöds av servern

Du kan lägga till flera autentiseringsuppgifter för servern i Configuration Manager, som kan vara domän, icke-domän (Windows eller Linux) eller SQL Server autentiseringsuppgifter för autentisering.

De typer av autentiseringsuppgifter som stöds anges i tabellen nedan:

Typ av autentiseringsuppgifter | Description
--- | ---
**Domänautentiseringsuppgifter** | Du **kan lägga till domänautentiseringsuppgifter genom** att välja alternativet i list rutan i **Lägg till spärrade autentiseringsuppgifter** . <br/><br/> Om du vill ange domänautentiseringsuppgifter måste du ange **domän namnet** som måste anges i FQDN-formatet (till exempel Prod.Corp.contoso.com). <br/><br/> Du måste också ange ett eget namn för autentiseringsuppgifter, användar namn och lösen ord. <br/><br/> De domän referenser som läggs till kommer automatiskt att verifieras för äkthet mot domänens Active Directory. Detta är för att förhindra att konto utelåsning när installationen försöker mappa domänautentiseringsuppgifter mot identifierade servrar. <br/><br/> Installations programmet kommer inte att försöka mappa domänautentiseringsuppgifter som inte kunde verifieras. Du måste ha minst en verifierad domän autentiseringsuppgift eller minst en icke-domänansluten-autentiseringsuppgift för att kunna fortsätta med program varu inventeringen.<br/><br/>Domänautentiseringsuppgifter som mappas automatiskt mot Windows-servrar kommer att användas för att utföra program varu inventering och kan även användas för att identifiera SQL Server instanser och databaser _(om du har konfigurerat Windows-autentiseringsläge på SQL-servrarna)_.<br/> [Läs mer](/dotnet/framework/data/adonet/sql/authentication-in-sql-server) om de typer av autentiseringsläge som stöds på SQL-servrar.
**Autentiseringsuppgifter för icke-domän (Windows/Linux)** | Du kan lägga till **Windows (icke-domän)** eller **Linux (icke-domän)** genom att välja alternativet som krävs i list rutan i **Lägg till spärrade autentiseringsuppgifter** . <br/><br/> Du måste ange ett eget namn för autentiseringsuppgifter, användar namn och lösen ord.
**SQL Server autentiseringsuppgifter för autentisering** | Du kan lägga till SQL Server autentiseringsuppgifter för **autentisering** genom att välja alternativet i list rutan i **Lägg till spärrade autentiseringsuppgifter** . <br/><br/> Du måste ange ett eget namn för autentiseringsuppgifter, användar namn och lösen ord. <br/><br/> Du kan lägga till den här typen av autentiseringsuppgifter för att identifiera SQL Server instanser och databaser som körs i din VMware-miljö, om du har konfigurerat SQL Server autentiseringsläge på SQL-servrarna.<br/> [Läs mer](/dotnet/framework/data/adonet/sql/authentication-in-sql-server) om de typer av autentiseringsläge som stöds på SQL-servrar.<br/><br/> Du måste ange minst en verifierad domän autentiseringsuppgift eller minst en Windows-autentiseringsuppgift (inte domän) så att installationen kan slutföra program varu inventeringen för att identifiera SQL installerat på servrarna innan du använder SQL Server autentiseringsuppgifter för att identifiera SQL Server-instanser och-databaser.

Kontrol lera behörigheterna som krävs för Windows/Linux-autentiseringsuppgifterna för att utföra program varu inventeringen, agent lös beroende analys och identifiera SQL Server instanser och databaser.

### <a name="required-permissions"></a>Behörigheter som krävs

I tabellen nedan visas de behörigheter som krävs för de autentiseringsuppgifter som har angetts för servern för att utföra respektive funktion:

Funktion | Windows-autentiseringsuppgifter | Linux-autentiseringsuppgifter
---| ---| ---
**Programvaruinventering** | Gäst användar konto | Reguljärt/normalt användar konto (icke-sudo åtkomst behörigheter)
**Identifiering av SQL Server instanser och databaser** | Användar konto som är medlem i Server rollen sysadmin. | _Stöds inte för närvarande_
**Agentlös beroendeanalys** | Domän konto eller icke-domännätverk (lokalt) med administratörs behörighet | Rot användar konto eller <br/> ett konto med dessa behörigheter för/bin/netstat-och/bin/ls-filer: CAP_DAC_READ_SEARCH och CAP_SYS_PTRACE.<br/><br/> Ange dessa funktioner med följande kommandon: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP-/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP-/bin/netstat

### <a name="recommended-practices-to-provide-credentials"></a>Rekommenderade metoder för att ange autentiseringsuppgifter

- Vi rekommenderar att du skapar ett dedikerat domän användar konto med de [behörigheter som krävs](add-server-credentials.md#required-permissions), vilket är begränsat till att utföra program varu inventering, agent lös beroende analys och identifiering av SQL Server instanser och databaser på önskade servrar.
- Vi rekommenderar att du anger minst en korrekt verifierad domän autentiseringsuppgift eller minst en icke-domänansluten för att initiera program varu inventeringen.
- För att identifiera SQL Server instanser och databaser kan du ange domänautentiseringsuppgifter, om du har konfigurerat Windows-autentiseringsläge på SQL-servrarna.
- Du kan också ange SQL Server autentiseringsuppgifter för autentisering om du har konfigurerat SQL Server autentiseringsläge på SQL-servrarna, men vi rekommenderar att du anger minst en verifierad domän autentiseringsuppgift eller minst en Windows-autentiseringsuppgift (inte domän) så att installationen kan slutföra program varu inventeringen först.

## <a name="credentials-handling-on-appliance"></a>Hantering av autentiseringsuppgifter på enheten

- Alla autentiseringsuppgifter som anges i Konfigurations hanteraren för enheten lagras lokalt på installations servern och skickas inte till Azure.
- Autentiseringsuppgifterna som lagras på installations servern krypteras med hjälp av Data Protection API (DPAPI).
- När du har lagt till autentiseringsuppgifter försöker installations programmet automatiskt mappa autentiseringsuppgifterna för att utföra identifiering på respektive servrar.
- Enheten använder de autentiseringsuppgifter som automatiskt mappas på en server för alla efterföljande identifierings cykler till autentiseringsuppgifterna för att hämta nödvändiga identifierings data. Om autentiseringsuppgifterna slutar fungera försöker enheten igen att mappa från listan över tillagda autentiseringsuppgifter och fortsätta med den pågående identifieringen på servern.
- De domän referenser som läggs till kommer automatiskt att verifieras för äkthet mot domänens Active Directory. Detta är för att förhindra att konto utelåsning när installationen försöker mappa domänautentiseringsuppgifter mot identifierade servrar. Installations programmet kommer inte att försöka mappa domänautentiseringsuppgifter som inte kunde verifieras.
- Om installationen inte kan mappa några domän-eller icke-domänautentiseringsuppgifter mot en server visas statusen "autentiseringsuppgifterna är inte tillgänglig" mot servern i projektet.

## <a name="next-steps"></a>Nästa steg

Gå igenom självstudierna för [identifiering av servrar som körs i VMware-miljön](tutorial-discover-vmware.md)