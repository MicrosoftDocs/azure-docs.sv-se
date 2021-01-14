---
title: Använda Automation-verktyg för infrastruktur
description: Lär dig hur du använder infrastruktur automatiserings verktyg som Ansible, chef, Puppet, terraform och Packer för att skapa och hantera virtuella datorer i Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 07/17/2020
ms.author: cynthn
ms.openlocfilehash: 96c68850cfd8b03f7d1457eb0f1ca9ce5880c926
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98198007"
---
# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Använd infrastruktur automatiserings verktyg med virtuella datorer i Azure

För att skapa och hantera virtuella datorer i Azure (VM) på ett konsekvent sätt i skala, är det vanligt att någon form av automatisering är det önskade. Det finns många verktyg och lösningar som gör att du kan automatisera hela livs cykeln för distribution och hantering av Azure-infrastrukturen. I den här artikeln beskrivs några av infrastruktur automatiserings verktygen som du kan använda i Azure. De här verktygen passar vanligt vis till någon av följande metoder:

- Automatisera konfigurationen av virtuella datorer
    - Verktygen omfattar [Ansible](#ansible), [chef](#chef), [Puppet](#puppet)och [Azure Resource Manager mall](#azure-resource-manager-template).
    - Verktyg som är speciella för anpassning av virtuella datorer är [Cloud-Init](#cloud-init) för virtuella Linux-datorer, PowerShell-tjänsten för [önskad tillstånds konfiguration (DSC)](#powershell-dsc)och [tillägget för Azures anpassade skript](#azure-custom-script-extension) för alla virtuella Azure-datorer

- Automatisera infrastruktur hantering
    - Verktygen inkluderar [Packer](#packer) för att automatisera anpassade versioner av VM-avbildningar och [terraform](#terraform) för att automatisera skapande processen för infrastrukturen.
    - [Azure Automation](#azure-automation) kan utföra åtgärder i din Azure och lokala infrastruktur.

- Automatisera program distribution och-leverans
    - Exempel är [Azure DevOps Services](#azure-devops-services) och [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) är en automatiserings motor för konfigurations hantering, skapande av virtuella datorer eller program distribution. Ansible använder en agent-mindre modell, vanligt vis med SSH-nycklar, för att autentisera och hantera mål datorer. Konfigurations uppgifter definieras i spel böcker med ett antal Ansible-moduler som är tillgängliga för att utföra vissa uppgifter. Mer information finns i [så här fungerar Ansible](https://www.ansible.com/how-ansible-works).

Lär dig att:

- [Installera och konfigurera Ansible i Linux för användning med Azure](/azure/developer/ansible/install-on-linux-vm).
- [Skapa en virtuell Linux-dator](/azure/developer/ansible/vm-configure).
- [Hantera en virtuell Linux-dator](/azure/developer/ansible/vm-manage).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) är en automatiserings plattform som hjälper dig att definiera hur din infrastruktur konfigureras, distribueras och hanteras. Ytterligare komponenter som ingår chefs livs miljöer för program livs cykel automatisering snarare än infrastrukturen och chefs INSPEC som hjälper till att automatisera efterlevnaden av säkerhets-och princip krav. Chefs klienterna installeras på mål datorerna med en eller flera centrala chefs servrar som lagrar och hanterar konfigurationerna. Mer information finns i [Översikt över chef](https://docs.chef.io/chef_overview.html).

Lär dig att:

- [Distribuera chef automatiserat från Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Installera chef på Windows och skapa virtuella Azure-datorer](/azure/developer/chef/windows-vm-configure).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) är en företags färdig automatiserings plattform som hanterar programmets leverans-och distributions process. Agenter installeras på mål datorerna så att Puppet-hanteraren kan köra manifest som definierar önskad konfiguration av Azure-infrastrukturen och virtuella datorer. Puppet kan integreras med andra lösningar som Jenkins och GitHub för ett förbättrat DevOps-arbetsflöde. Mer information finns i [så här fungerar Puppet](https://puppet.com/products/how-puppet-works).

Lär dig att:

- [Distribuera Puppet](https://puppet.com/docs/puppet/5.5/install_windows.html).


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) är ett vanligt sätt att anpassa en virtuell Linux-dator när den startas för första gången. Du kan använda cloud-init till att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom Cloud-Init anropas under den första start processen finns det inga ytterligare steg eller nödvändiga agenter för att tillämpa konfigurationen.  Mer information om hur du formaterar filerna korrekt `#cloud-config` finns på webbplatsen för [Cloud-Init-dokumentation](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` filer är textfiler kodade i base64.

Cloud-init fungerar med olika distributioner. Du använder till exempel inte **apt-get install** eller **yum install** när du vill installera ett paket. I stället definierar du en lista med paket att installera. Cloud-init använder automatiskt rätt pakethanteringsverktyg för den distribution du valt.

Vi arbetar aktivt med våra godkända Linux distribution-partner för att få moln-init-aktiverade avbildningar tillgängliga på Azure Marketplace. De här avbildningarna gör att dina Cloud-Init-distributioner och-konfigurationer fungerar sömlöst med virtuella datorer och skalnings uppsättningar för virtuella datorer.
Läs mer om Cloud-Init på Azure:

- [Cloud-Init-stöd för virtuella Linux-datorer i Azure](./linux/using-cloud-init.md)
- [Prova en själv studie kurs om automatiserad VM-konfiguration med Cloud-Init](./linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) är en hanterings plattform för att definiera konfigurationen för mål datorer. DSC kan också användas i Linux via [Open Management Infrastructure (OMI)-servern](https://collaboration.opengroup.org/omi/).

DSC-konfigurationer definierar vad som ska installeras på en dator och hur du konfigurerar värden. En lokal Configuration Manager-motor (LCM) körs på varje målnod som bearbetar begärda åtgärder baserat på push-konfigurationer. En pull-server är en webb tjänst som körs på en central värd för att lagra DSC-konfigurationer och associerade resurser. Pull-servern kommunicerar med LCM-motorn på varje målvärd för att tillhandahålla nödvändiga konfigurationer och rapportera om kompatibilitet.

Lär dig att:

- [Skapa en grundläggande DSC-konfiguration](/powershell/scripting/dsc/quickstarts/website-quickstart).
- [Konfigurera en DSC-pull-server](/powershell/scripting/dsc/pull-server/pullserver).
- [Använd DSC för Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Anpassade Azure-skripttillägg
Det anpassade skript tillägget i Azure för [Linux](./extensions/custom-script-linux.md) eller [Windows](./extensions/custom-script-windows.md) laddar ned och kör skript på virtuella Azure-datorer. Du kan använda tillägget när du skapar en virtuell dator eller när som helst efter att den virtuella datorn används.

Skript kan laddas ned från Azure Storage eller någon offentlig plats, till exempel en GitHub-lagringsplats. Med tillägget för anpassat skript kan du skriva skript på alla språk som körs på den virtuella käll datorn. Dessa skript kan användas för att installera program eller konfigurera den virtuella datorn efter behov. För att skydda autentiseringsuppgifter kan känslig information, till exempel lösen ord, lagras i en skyddad konfiguration. Autentiseringsuppgifterna dekrypteras bara i den virtuella datorn.

Lär dig att:

- [Skapa en virtuell Linux-dator med Azure CLI och Använd det anpassade skript tillägget](./scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Skapa en virtuell Windows-dator med Azure PowerShell och Använd det anpassade skript tillägget](./scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md).


## <a name="packer"></a>Packer
[Packer](https://www.packer.io) automatiserar Bygg processen när du skapar en anpassad VM-avbildning i Azure. Du använder Packer för att definiera operativ systemet och köra skript för efter konfiguration som anpassar den virtuella datorn efter dina egna behov. När den har kon figurer ATS, fångas den virtuella datorn in som en hanterad disk avbildning. Packer automatiserar processen för att skapa den virtuella käll datorn, nätverks-och lagrings resurser, köra konfigurations skript och skapa sedan den virtuella dator avbildningen.

Lär dig att:

- [Använd Packer för att skapa en virtuell Linux-avbildning i Azure](./linux/build-image-with-packer.md).
- [Använd Packer för att skapa en Windows VM-avbildning i Azure](./windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) är ett automatiserings verktyg som gör att du kan definiera och skapa en hel Azure-infrastruktur med ett enda mall format språk – HashiCorp-konfigurations språket (HCL). Med terraform definierar du mallar som automatiserar processen för att skapa nätverks-, lagrings-och VM-resurser för en specifik program lösning. Du kan använda dina befintliga terraform-mallar för andra plattformar med Azure för att säkerställa konsekvens och förenkla infrastruktur distributionen utan att behöva konvertera till en Azure Resource Manager-mall.

Lär dig att:

- [Installera och konfigurera terraform med Azure](/azure/developer/terraform/getting-started-cloud-shell).
- [Skapa en Azure-infrastruktur med terraform](/azure/developer/terraform/create-linux-virtual-machine-with-infrastructure).


## <a name="azure-automation"></a>Azure Automatisering
[Azure Automation](https://azure.microsoft.com/services/automation/) använder Runbooks för att bearbeta en uppsättning aktiviteter på de virtuella datorer som du är mål för. Azure Automation används för att hantera befintliga virtuella datorer i stället för att skapa en infrastruktur. Azure Automation kan köras över både virtuella Linux-och Windows-datorer, samt lokala virtuella eller fysiska datorer med en hybrid Runbook Worker. Runbooks kan lagras i en lagrings plats för käll kontroll, till exempel GitHub. Dessa Runbooks kan sedan köras manuellt eller enligt ett definierat schema.

Azure Automation tillhandahåller också en tjänst för önskad tillstånds konfiguration (DSC) som gör att du kan skapa definitioner för hur en given uppsättning virtuella datorer ska konfigureras. DSC säkerställer sedan att konfigurationen som krävs används och att den virtuella datorn förblir konsekvent. Azure Automation DSC körs på både Windows-och Linux-datorer.

Lär dig att:

- [Skapa en PowerShell-Runbook](../automation/learn/automation-tutorial-runbook-textual-powershell.md).
- [Använd hybrid Runbook Worker för att hantera lokala resurser](../automation/automation-hybrid-runbook-worker.md).
- [Använd Azure Automation DSC](../automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Azure DevOps Services
[Azure DevOps Services](https://www.visualstudio.com/team-services/) är en uppsättning verktyg som hjälper dig att dela och spåra kod, använda automatiserade versioner och skapa en komplett pipeline för kontinuerlig integrering och utveckling (CI/CD). Azure DevOps Services integreras med Visual Studio och andra redigerare för att förenkla användningen. Azure DevOps Services kan också skapa och konfigurera virtuella Azure-datorer och sedan distribuera kod till dem.

Läs mer om:

- [Azure DevOps-tjänster](/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://jenkins.io) är en kontinuerlig integrations server som hjälper till att distribuera och testa program och skapa automatiserade pipeliner för kod leverans. Det finns hundratals plugin-program för att utöka kärnan Jenkins-plattformen och du kan också integrera med många andra produkter och lösningar via Webhooks. Du kan installera Jenkins manuellt på en virtuell Azure-dator, köra Jenkins inifrån en Docker-behållare eller använda en fördefinierad Azure Marketplace-avbildning.

Lär dig att:

- [Skapa en utvecklings infrastruktur på en virtuell Linux-dator i Azure med Jenkins, GitHub och Docker](/azure/developer/jenkins/pipeline-with-github-and-docker).


## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
[Azure Resource Manager](../azure-resource-manager/templates/overview.md) distributions-och hanterings tjänsten för Azure. Det tillhandahåller ett hanterings lager som gör att du kan skapa, uppdatera och ta bort resurser i din Azure-prenumeration. Du kan använda hanteringsfunktioner som åtkomstkontroll, lås och taggar till att skydda och organisera dina resurser efter distributionen.

Lär dig att:

- [Distribuera virtuella datorer med hjälp av en Resource Manager-mall](./linux/spot-template.md).
- [Skapa en virtuell Windows-dator från en Resource Manager-mall](./windows/ps-template.md).
- [Ladda ned mallen för en virtuell dator](./windows/download-template.md).
- [Skapa en Azure Image Builder-mall](./linux/image-builder-json.md).

## <a name="next-steps"></a>Nästa steg
Det finns många olika alternativ för att använda automatiserings verktyg för infrastruktur i Azure. Du har frihet att använda den lösning som bäst passar dina behov och din miljö. Om du vill komma igång och testa några av de verktyg som är inbyggda i Azure läser du så här automatiserar du anpassningen av en [Linux](./linux/tutorial-automate-vm-deployment.md) -eller [Windows](./windows/tutorial-automate-vm-deployment.md) -VM.