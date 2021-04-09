---
title: Azure Virtual Machines-distribution för SAP NetWeaver | Microsoft Docs
description: Lär dig hur du distribuerar SAP-program på virtuella Linux-datorer i Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/16/2020
ms.author: sedusch
ms.openlocfilehash: 5d6ea75936383388a57a7822f054e0ea7297471e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101695523"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Azure Virtual Machines-distribution för SAP NetWeaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../../../cli-install-nodejs.md
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Azure Virtual Machines DBMS-distribution för SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Cachelagring för virtuella datorer och virtuella hård diskar)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Programvaru-RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Struktur för en RDBMS-distribution)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Hög tillgänglighet och haveri beredskap med virtuella Azure-datorer)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 och senare)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 och tidigare versioner)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Använda en SQL Server-avbildning från Azure Marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Översikt över allmänt SQL Server för SAP på Azure)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Information till SQL Server RDBMS)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Lagrings konfiguration)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Säkerhets kopiering och återställning)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Prestanda överväganden för säkerhets kopiering och återställning)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Andra)
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md (Azure Virtual Machines distribution för SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP-resurser)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Distribuera en virtuell dator med hjälp av en anpassad avbildning)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scenario 1: Distribuera en virtuell dator från Azure Marketplace för SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scenario 2: Distribuera en virtuell dator med en anpassad avbildning för SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scenario 3: flytta en virtuell dator från en lokal dator med en icke-generaliserad virtuell Azure-dator med SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Distributions scenarier för virtuella datorer för SAP på Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Distribuera Azure PowerShell-cmdletar)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Hämta och importera SAP-relevanta PowerShell-cmdletar)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Anslut en virtuell dator till en lokal domän – endast Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Ladda ned, installera och aktivera Azure VM-agenten)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Konfigurera Azure-tillägget för SAP)
[deployment-guide-configure-new-extension-ps]:deployment-guide.md#2ad55a0d-9937-4943-9dd2-69bc2b5d3de0 (Konfigurera det nya Azure-tillägget för SAP med Azure PowerShell)
[deployment-guide-configure-new-extension-cli]:deployment-guide.md#c8749c24-fada-42ad-b114-f9aae2dc37da (Konfigurera det nya Azure-tillägget för SAP med Azure CLI)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Beredskaps kontroll för Azure-tillägg för SAP)
[deployment-guide-5.1-new]:deployment-guide.md#7bf24f59-7347-4c7a-b094-4693e4687ee5 (Beredskaps kontroll för nya Azure-tillägg för SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Hälso kontroll för Azure-tillägget för SAP-konfiguration)
[deployment-guide-5.2-new]:deployment-guide.md#464ac96d-7d3c-435d-a5ae-3faf3bfef4b3 (Hälso kontroll för den nya Azure-tillägget för SAP-konfiguration)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Felsöka Azure-tillägget för SAP)
[deployment-guide-5.3-new]:deployment-guide.md#b7afb8ef-a64c-495d-bb37-2af96688c530 (Felsöka det nya Azure-tillägget för SAP)
[deployment-guide-contact-support]:deployment-guide.md#3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2 (Felsöka Azure-tillägget för SAP – kontakta supporten)
[deployment-guide-run-the-script]:deployment-guide.md#0d2847ad-865d-4a4c-a405-f9b7baaa00c7 (Felsöka Azure-tillägget för SAP – kör installations skriptet)
[deployment-guide-redeploy-after-sysprep]:deployment-guide.md#2cd61f22-187d-42ed-bb8c-def0c983d756 (Felsöka Azure-tillägget för SAP-omdistribution efter Sysprep)
[deployment-guide-fix-internet-connection]:deployment-guide.md#e92bc57d-80d9-4a2b-a2f4-16713a22ad89 ( Felsöka Azure-tillägget för SAP-Fix Internet anslutning)


[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Konfigurera VM-tillägg)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Konfigurera proxyn)
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Kontroller och fel sökning)

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-Azvmaemextension]:https://docs.microsoft.com/powershell/module/az.compute/set-azvmaemextension

[planning-guide]:planning-guide.md (Azure Virtual Machines planera och implementera SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Resurser)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Hög tillgänglighet och haveri beredskap för SAP-NetWeaver som körs på Azure Virtual Machines)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Hög tillgänglighet för SAP-programservrar)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Använda Autostart för SAP-instanser)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Enbart molnbaserad distribution av virtuella datorer i Azure utan beroenden på det lokala kund nätverket)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Lokal distribution av en eller flera virtuella SAP-datorer i Azure fullständigt integrerat med det lokala nätverket)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure-regioner)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Fel domäner)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Uppgradera domäner)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Tillgänglighets uppsättningar i Azure)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Koncept för Microsoft Azure virtuella datorer)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Flytta en virtuell dator från lokal plats till Azure med en icke-generaliserad disk)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Distribuera en virtuell dator med en kundspecifik avbildning)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Förberedelser för att flytta en virtuell dator från en lokal plats till Azure med en icke-generaliserad disk)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Förberedelser inför distribution av en virtuell dator med en kundspecifik avbildning för SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Förbereda virtuella datorer med SAP för Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Skillnad mellan en Azure-disk och en Azure-avbildning)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Ladda upp en virtuell hård disk från lokal plats till Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Kopiera diskar mellan Azure Storage-konton)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (VM/VHD-struktur för SAP-distributioner)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Ställer in automontering för anslutna diskar)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure Monitoring-lösning för SAP)
[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure nätverk)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Lagring: Microsoft Azure Storage och data diskar)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (Produkt tillgänglighets mat ris för SAP)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-os-disk-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk-md%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image-md%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image-md%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Distribuera och hantera virtuella datorer med hjälp av Azure Resource Manager mallar och Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Hantera virtuella datorer med hjälp av Azure Resource Manager och PowerShell)
[virtual-machines-windows-agent-user-guide]:../../extensions/agent-windows.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-linux.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../windows/quick-create-powershell.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../windows/upload-image.md
[virtual-machines-windows-tutorial]:../../windows/quick-create-portal.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[qs-configure-powershell-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md
[qs-configure-cli-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md
[howto-assign-access-powershell]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md
[howto-assign-access-cli]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Azure Virtual Machines är lösningen för organisationer som behöver beräknings-och lagrings resurser, i minimal tid och utan längd på att lösas. Du kan använda Azure Virtual Machines för att distribuera klassiska program, t. ex. SAP NetWeaver-baserade program, i Azure. Utöka programmets tillförlitlighet och tillgänglighet utan ytterligare lokala resurser. Azure Virtual Machines stöder anslutning mellan platser, så att du kan integrera Azure Virtual Machines i organisationens lokala domäner, privata moln och SAP-system liggande.

I den här artikeln beskriver vi stegen för att distribuera SAP-program på virtuella datorer (VM) i Azure, inklusive alternativa distributions alternativ och fel sökning. Den här artikeln bygger på informationen i [Azure Virtual Machines planering och implementering för SAP NetWeaver][planning-guide]. Det kompletterar även SAP-installations dokumentation och SAP-anteckningar, som är de viktigaste resurserna för att installera och distribuera SAP-program.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Att konfigurera en virtuell Azure-dator för SAP-programdistribution omfattar flera steg och resurser. Innan du börjar ska du kontrol lera att du uppfyller kraven för att installera SAP-program på virtuella datorer i Azure.

### <a name="local-computer"></a>Lokal dator

Om du vill hantera virtuella Windows-eller Linux-datorer kan du använda ett PowerShell-skript och Azure Portal. För båda verktygen behöver du en lokal dator som kör Windows 7 eller en senare version av Windows. Om du bara vill hantera virtuella Linux-datorer och du vill använda en Linux-dator för den här uppgiften kan du använda Azure CLI.

### <a name="internet-connection"></a>Internet anslutning

Du måste vara ansluten till Internet för att kunna hämta och köra de verktyg och skript som krävs för distribution av SAP-program. Den virtuella Azure-datorn som kör Azure-tillägget för SAP behöver också åtkomst till Internet. Om den virtuella Azure-datorn är en del av ett virtuellt Azure-nätverk eller en lokal domän kontrollerar du att de relevanta proxyinställningarna är inställda, enligt beskrivningen i [Konfigurera proxyservern][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Microsoft Azure-prenumeration

Du behöver ett aktivt Azure-konto.

### <a name="topology-and-networking"></a>Topologi och nätverk

Du måste definiera topologi och arkitektur för SAP-distributionen i Azure:

* Azure Storage-konton som ska användas
* Virtuellt nätverk där du vill distribuera SAP-systemet
* Resurs grupp som du vill distribuera SAP-systemet till
* Azure-region där du vill distribuera SAP-systemet
* SAP-konfiguration (två nivåer eller tre nivåer)
* VM-storlekar och antalet ytterligare data diskar som ska monteras på de virtuella datorerna
* Konfiguration av SAP-korrigering och transport system (CTS)

Skapa och konfigurera Azure Storage-konton (om det behövs) eller virtuella Azure-nätverk innan du påbörjar distributions processen för SAP-programvaran. Information om hur du skapar och konfigurerar dessa resurser finns i [Azure Virtual Machines planera och implementera för SAP NetWeaver][planning-guide].

### <a name="sap-sizing"></a>SAP-storlek

Läs följande information, för SAP-storlek:

* Projekterad SAP-arbets belastning, till exempel med hjälp av SAP-verktyget för snabb storlek och SAPS-numret (SAP Application Performance standard)
* Nödvändig processor resurs och minnes användning för SAP-systemet
* Nödvändiga åtgärder för in-/utdata (I/O) per sekund
* Nödvändig nätverks bandbredd för eventuell kommunikation mellan virtuella datorer i Azure
* Nödvändig nätverks bandbredd mellan lokala till gångar och det Azure-distribuerade SAP-systemet

### <a name="resource-groups"></a>Resursgrupper

I Azure Resource Manager kan du använda resurs grupper för att hantera alla program resurser i din Azure-prenumeration. Mer information finns i [Översikt över Azure Resource Manager][resource-group-overview].

## <a name="resources"></a>Resurser

### <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP-resurser

När du konfigurerar SAP-program distributionen behöver du följande SAP-resurser:

* SAP anmärkning [1928533], som har:
  * Lista över storlekar på virtuella Azure-datorer som stöds för distribution av SAP-program
  * Viktig kapacitets information för Azure VM-storlekar
  * Stöd för SAP-program och operativ system (OS) och databas kombinationer
  * Nödvändig SAP kernel-version för Windows och Linux på Microsoft Azure

* SAP NOTE [2015553] visar krav för SAP-program distributioner som stöds i Azure.
* SAP NOTE [2178632] innehåller detaljerad information om alla övervaknings mått som rapporter ATS för SAP i Azure.
* SAP NOTE [1409604] har den nödvändiga SAP-värd agent versionen för Windows i Azure.
* SAP NOTE [2191498] har den version av SAP host agent som krävs för Linux i Azure.
* SAP NOTE [2243692] innehåller information om SAP-licensiering på Linux i Azure.
* SAP NOTE [1984787] innehåller allmän information om SUSE Linux Enterprise Server 12.
* SAP NOTE [2002167] innehåller allmän information om Red Hat Enterprise Linux 7. x.
* SAP NOTE [2069760] innehåller allmän information om Oracle Linux 7. x.
* SAP NOTE [1999351] innehåller ytterligare felsöknings information för Azure-tillägget för SAP.
* SAP NOTE [1597355] har allmän information om växlings utrymme för Linux.
* [Sidan SAP på Azure SCN](https://wiki.scn.sap.com/wiki/x/Pia7Gg) innehåller nyheter och en samling användbara resurser.
* [SAP community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP-anteckningar för Linux.
* SAP-angivna PowerShell-cmdletar som är en del av [Azure PowerShell][azure-ps].
* SAP-vissa Azure CLI-kommandon som ingår i [Azure CLI][azure-cli].

### <a name="windows-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Windows-resurser

Dessa Microsoft-artiklar avser SAP-distributioner i Azure:

* [Azure Virtual Machines planera och implementera SAP-NetWeaver][planning-guide]
* [Azure Virtual Machines-distribution för SAP NetWeaver (den här artikeln)][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP NetWeaver][dbms-guide]

## <a name="deployment-scenarios-for-sap-software-on-azure-vms"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Distributions scenarier för SAP-program på virtuella Azure-datorer

Du har flera alternativ för att distribuera virtuella datorer och tillhör ande diskar i Azure. Det är viktigt att förstå skillnaderna mellan distributions alternativ, eftersom du kan vidta olika åtgärder för att förbereda dina virtuella datorer för distribution baserat på vilken distributions typ du väljer.

### <a name="scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Scenario 1: Distribuera en virtuell dator från Azure Marketplace för SAP

Du kan använda en avbildning från Microsoft eller en tredje part på Azure Marketplace för att distribuera den virtuella datorn. Marketplace erbjuder vissa standard operativ Systems avbildningar av Windows Server och olika Linux-distributioner. Du kan även distribuera en avbildning som inkluderar DBMS (Database Management System) SKU: er, till exempel Microsoft SQL Server. Mer information om hur du använder avbildningar med DBMS SKU: er finns i [Azure Virtual Machines DBMS-distribution för SAP NetWeaver][dbms-guide].

Följande flödes schema visar den SAP-särskilda sekvens av steg för att distribuera en virtuell dator från Azure Marketplace:

![Flödes schema för VM-distribution för SAP-system med hjälp av en VM-avbildning från Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Skapa en virtuell dator med hjälp av Azure Portal

Det enklaste sättet att skapa en ny virtuell dator med en avbildning från Azure Marketplace är att använda Azure Portal.

1.  Gå till <https://portal.azure.com/#create/hub>.  Eller Välj **+ nytt** på Azure Portal-menyn.
1.  Välj **Compute (beräkna**) och välj sedan den typ av operativ system som du vill distribuera. Till exempel Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7,2 (RHEL 7,2) eller Oracle Linux 7,2. Standard List visningen visar inte alla operativ system som stöds. Välj **Visa alla** för en fullständig lista. Mer information om operativ system som stöds för distribution av SAP-program finns i SAP anmärkning [1928533].
1.  På nästa sida granskar du allmänna villkor.
1.  I rutan **Välj en distributions modell** väljer du **Resource Manager**.
1.  Välj **Skapa**.

Guiden vägleder dig genom att ange de parametrar som krävs för att skapa den virtuella datorn, förutom alla nödvändiga resurser, t. ex. nätverks gränssnitt och lagrings konton. Några av dessa parametrar är:

1. **Grunder**:
   * **Namn**: namnet på resursen (namnet på den virtuella datorn).
   * **Typ av virtuell dator disk**: Välj disk typen för OS-disken. Om du vill använda Premium Storage för dina data diskar rekommenderar vi att du även använder Premium Storage för operativ system disken.
   * **Användar namn och lösen ord** eller **Offentlig SSH-nyckel**: Ange användar namn och lösen ord för den användare som skapas under etableringen. För en virtuell Linux-dator kan du ange den SSH-nyckel (Public Secure Shell) som du använder för att logga in på datorn.
   * **Prenumeration**: Välj den prenumeration som du vill använda för att etablera den nya virtuella datorn.
   * **Resurs grupp**: namnet på resurs gruppen för den virtuella datorn. Du kan antingen ange namnet på en ny resurs grupp eller namnet på en resurs grupp som redan finns.
   * **Plats**: var den nya virtuella datorn ska distribueras. Om du vill ansluta den virtuella datorn till ditt lokala nätverk, se till att du väljer platsen för det virtuella nätverk som ansluter Azure till ditt lokala nätverk. Mer information finns i [Microsoft Azure nätverk][planning-guide-microsoft-azure-networking] i [Azure Virtual Machines planera och implementera för SAP NetWeaver][planning-guide].
1. **Storlek**:

     En lista över VM-typer som stöds finns i SAP anmärkning [1928533]. Se till att du väljer rätt VM-typ om du vill använda Azure Premium Storage. Alla typer av virtuella datorer har inte stöd för Premium Storage. Mer information finns i [lagring: Microsoft Azure Storage och data diskar][planning-guide-storage-microsoft-azure-storage-and-data-disks] och [Azure Storage för SAP-arbetsbelastningar](./planning-guide-storage.md) i [Azure Virtual Machines planera och implementera för SAP NetWeaver][planning-guide].

1. **Inställningar**:
   * **Storage**
     * **Disktyp**: Välj disk typen för OS-disken. Om du vill använda Premium Storage för dina data diskar rekommenderar vi att du även använder Premium Storage för operativ system disken.
     * **Använd Managed disks**: om du vill använda Managed disks väljer du Ja. Mer information om Managed Disks finns i kapitel [Managed disks](./planning-guide-storage.md#microsoft-azure-storage-resiliency) i planerings guiden.
     * **Lagrings konto**: Välj ett befintligt lagrings konto eller skapa ett nytt. Alla lagrings typer fungerar inte för att köra SAP-program. Mer information om lagrings typer finns i [lagrings strukturen för en virtuell dator för RDBMS-distributioner](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64).
   * **Nätverk**
     * **Virtuellt nätverk** och **undernät**: om du vill integrera den virtuella datorn med intranätet väljer du det virtuella nätverk som är anslutet till ditt lokala nätverk.
     * **Offentlig IP-adress**: Välj den offentliga IP-adress som du vill använda, eller ange parametrar för att skapa en ny offentlig IP-adress. Du kan använda en offentlig IP-adress för att få åtkomst till den virtuella datorn via Internet. Se till att du även skapar en nätverks säkerhets grupp för att skydda åtkomsten till den virtuella datorn.
     * **Nätverks säkerhets grupp**: Mer information finns i [kontrol lera nätverks trafik flöde med nätverks säkerhets grupper][virtual-networks-nsg].
   * **Tillägg**: du kan installera tillägg för virtuella datorer genom att lägga till dem i distributionen. Du behöver inte lägga till tillägg i det här steget. De tillägg som krävs för SAP-supporten installeras senare. Se kapitlet [Konfigurera Azure-tillägget för SAP][deployment-guide-4.5] i den här hand boken.
   * **Hög tillgänglighet**: Välj en tillgänglighets uppsättning eller ange parametrarna för att skapa en ny tillgänglighets uppsättning. Mer information finns i [Azures tillgänglighets uppsättningar][planning-guide-3.2.3].
   * **Övervakning**
     * **Startdiagnostik**: du kan välja **inaktivera** för startdiagnostik.
     * **Diagnostik för gäst operativ system**: du kan välja **inaktivera** för övervakning av diagnostik.

1. **Sammanfattning**:

   Granska dina val och välj sedan **OK**.

Den virtuella datorn distribueras i den resurs grupp som du har valt.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Skapa en virtuell dator med hjälp av en mall

Du kan skapa en virtuell dator med hjälp av en av SAP-mallarna som publiceras i [GitHub-lagringsplatsen för Azure-snabb start-mallar][azure-quickstart-templates-github]. Du kan också skapa en virtuell dator manuellt med hjälp av [Azure Portal][virtual-machines-windows-tutorial], [POWERSHELL][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]eller [Azure CLI][virtual-machines-linux-tutorial].

* [**Konfiguration på två nivåer (endast en virtuell dator) mall** (SAP-2-Tier-Marketplace-image)][sap-templates-2-tier-marketplace-image]

  Använd den här mallen om du vill skapa ett system på två nivåer med bara en virtuell dator.
* [**Konfiguration på två nivåer (endast en virtuell dator) mall – Managed disks** (SAP-2-Tier-Marketplace-image-MD)][sap-templates-2-tier-marketplace-image-md]

  Använd den här mallen om du vill skapa ett system på två nivåer med bara en virtuell dator och Managed Disks.
* [**Konfiguration av tre nivåer (flera virtuella datorer) mall** (SAP-3-tier-Marketplace-image)][sap-templates-3-tier-marketplace-image]

  Använd den här mallen om du vill skapa ett system på tre nivåer med hjälp av flera virtuella datorer.
* [**Konfiguration av tre nivåer (flera virtuella datorer) mall – Managed disks** (SAP-3-tier-Marketplace-image-MD)][sap-templates-3-tier-marketplace-image-md]

  Använd den här mallen om du vill skapa ett system på tre nivåer genom att använda flera virtuella datorer och Managed Disks.

Ange följande parametrar för mallen i Azure Portal:

1. **Grunder**:
   * **Prenumeration**: den prenumeration som ska användas för att distribuera mallen.
   * **Resurs grupp**: resurs gruppen som används för att distribuera mallen. Du kan skapa en ny resurs grupp, eller så kan du välja en befintlig resurs grupp i prenumerationen.
   * **Plats**: var du vill distribuera mallen. Om du har valt en befintlig resurs grupp används platsen för resurs gruppen.

1. **Inställningar**:
   * **SAP-system-ID**: SAP-system-ID: t (sid).
   * **OS-typ**: det operativ system som du vill distribuera, till exempel Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7,2 (RHEL 7,2) eller Oracle Linux 7,2.

     List visningen visar inte alla operativ system som stöds. Mer information om operativ system som stöds för distribution av SAP-program finns i SAP anmärkning [1928533].
   * **SAP-system storlek**: storleken på SAP-systemet.

     Antalet SAPS som det nya systemet erbjuder. Om du inte är säker på hur många SAPS systemet kräver kan du fråga din SAP Technology-partner eller system integrerare.
   * **System tillgänglighet** (endast mall i tre skikt): systemets tillgänglighet.

     Välj **ha** för en konfiguration som passar för en installation med hög tillgänglighet. Två databas servrar och två servrar för ABAP SAP Central Services (ASCS) skapas.
   * **Lagrings typ** (endast mallar på två nivåer): den typ av lagring som ska användas.

     För större system rekommenderar vi starkt att du använder Azure Premium Storage. Mer information om lagrings typer finns i följande resurser:
      * [Användning av Azure Premium SSD Storage för SAP DBMS-instans][2367194]
      * [Lagrings struktur för en virtuell dator för RDBMS-distributioner](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: lagring med höga prestanda för arbets belastningar för virtuella Azure-datorer][storage-premium-storage-preview-portal]
      * [Introduktion till Microsoft Azure Storage][storage-introduction]
   * **Administratörens användar namn** och **Administratörs lösen ord**: användar namn och lösen ord.
     En ny användare skapas för att logga in på den virtuella datorn.
   * **Nytt eller befintligt undernät**: avgör om ett nytt virtuellt nätverk och undernät skapas eller om ett befintligt undernät används. Om du redan har ett virtuellt nätverk som är anslutet till ditt lokala nätverk väljer du **befintligt**.
   * **Undernäts-ID**: om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har ett undernät definierat måste den virtuella datorn vara tilldelad, namnge ID: t för det aktuella under nätet. ID: t ser vanligt vis ut så här:/Subscriptions/ &lt; prenumerations-id>/resourceGroups/ &lt; resurs grupp namn>/providers/Microsoft.Network/virtualNetworks/ &lt; virtuellt nätverks namn>/subnets/ &lt; under nät namn>

1. **Allmänna** villkor:  
    Granska och godkänn de juridiska villkoren.

1. Välj **Köp**.

Azure VM-agenten distribueras som standard när du använder en avbildning från Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Konfigurera proxyinställningar

Beroende på hur ditt lokala nätverk är konfigurerat kan du behöva konfigurera proxyn på den virtuella datorn. Om den virtuella datorn är ansluten till ditt lokala nätverk via VPN eller ExpressRoute kanske den virtuella datorn inte kan ansluta till Internet och kan inte ladda ned de nödvändiga VM-tilläggen eller samla in information om Azure-infrastrukturen för SAP-värdservern via SAP-tillägget för Azure. Mer information finns i [Konfigurera proxyservern][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Anslut till en domän (endast Windows)

Om din Azure-distribution är ansluten till en lokal Active Directory eller DNS-instans via en Azure plats-till-plats VPN-anslutning eller ExpressRoute (Detta kallas för *olika platser* i [Azure Virtual Machines planering och implementering för SAP NetWeaver][planning-guide]) förväntas vi att den virtuella datorn ansluter till en lokal domän. Mer information om överväganden för den här uppgiften finns i [ansluta en virtuell dator till en lokal domän (endast Windows)][deployment-guide-4.3].

#### <a name="configure-vm-extension"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Konfigurera VM-tillägg

Om du vill vara säker på att SAP stöder din miljö konfigurerar du Azure-tillägget för SAP enligt beskrivningen i [Konfigurera Azure-tillägget för SAP][deployment-guide-4.5]. Kontrol lera kraven för SAP och de lägsta versionen av SAP kernel och SAP host agent som krävs i resurserna som listas i [SAP-resurser][deployment-guide-2.2].

#### <a name="vm-extension-for-sap-check"></a>VM-tillägg för SAP-kontroll

Kontrol lera om VM-tillägget för SAP fungerar, enligt beskrivningen i [checkar och fel sökning][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Åtgärder efter distributionen

När du har skapat den virtuella datorn och den virtuella datorn har distribuerats måste du installera de program komponenter som krävs på den virtuella datorn. På grund av installations ordningen för distribution/program vara i den här typen av VM-distribution måste den program vara som ska installeras redan vara tillgänglig, antingen i Azure, på en annan virtuell dator eller som en disk som kan kopplas. Du kan också överväga att använda ett scenario mellan platser, där anslutningen till lokala till gångar (installations resurser) anges.

När du har distribuerat den virtuella datorn i Azure följer du samma rikt linjer och verktyg för att installera SAP-programvaran på din virtuella dator på samma sätt som i en lokal miljö. För att kunna installera SAP-program på en virtuell Azure-dator rekommenderar både SAP och Microsoft att du överför och lagrar SAP-installationsmedia på virtuella Azure-datorer eller Managed Disks, eller att du skapar en virtuell Azure-dator som fungerar som en fil server som har alla nödvändiga SAP-installationsmedia.

### <a name="scenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Scenario 2: Distribuera en virtuell dator med en anpassad avbildning för SAP

Eftersom olika versioner av ett operativ system eller DBMS har olika korrigerings krav kanske de avbildningar som du hittar på Azure Marketplace inte uppfyller dina behov. Du kanske vill skapa en virtuell dator med hjälp av din egen avbildning av operativ system/DBMS-VM, som du kan distribuera igen senare.
Du kan använda olika steg för att skapa en privat avbildning för Linux än att skapa en för Windows.

---
> ![Windows-logotyp.][Logo_Windows] Windows
>
> För att förbereda en Windows-avbildning som du kan använda för att distribuera flera virtuella datorer, måste Windows-inställningarna (t. ex. Windows SID och värdnamn) vara abstrakta eller generaliserade på den lokala virtuella datorn. Du kan använda [Sysprep](/previous-versions/windows/it-pro/windows-8.1-and-8/hh825084(v=win.10)) för att göra detta.
>
> ![Linux-logotyp.][Logo_Linux] Linux
>
> För att förbereda en Linux-avbildning som du kan använda för att distribuera flera virtuella datorer, måste vissa Linux-inställningar vara abstrakta eller generaliserade på den lokala virtuella datorn. Du kan använda `waagent -deprovision`  för att göra detta. Mer information finns i [avbilda en virtuell Linux-dator som körs på Azure][virtual-machines-linux-capture-image] och [användar handboken för Azure Linux-agenten][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

---
Du kan förbereda och skapa en anpassad avbildning och sedan använda den för att skapa flera nya virtuella datorer. Detta beskrivs i [Azure Virtual Machines planera och implementera för SAP NetWeaver][planning-guide]. Konfigurera ditt databas innehåll med hjälp av SAP Software Provisioning Manager för att installera ett nytt SAP-system (återställer en säkerhets kopia av databasen från en disk som är kopplad till den virtuella datorn) eller genom att direkt återställa en säkerhets kopia av databasen från Azure Storage, om ditt DBMS stöder det. Mer information finns i [Azure Virtual Machines DBMS-distribution för SAP NetWeaver][dbms-guide]. Om du redan har installerat ett SAP-system på den lokala virtuella datorn (särskilt för två nivåer) kan du anpassa inställningarna för SAP-systemet efter distributionen av den virtuella Azure-datorn med hjälp av metoden system Rename som stöds av SAP Software Provisioning Manager (SAP NOTE [1619720]). Annars kan du installera SAP-programvaran när du har distribuerat den virtuella Azure-datorn.

Följande flödes schema visar den SAP-anpassade sekvensen av steg för att distribuera en virtuell dator från en anpassad avbildning:

![Flödes schema för VM-distribution för SAP-system med hjälp av en VM-avbildning på en privat Marketplace][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Skapa en virtuell dator med hjälp av Azure Portal

Det enklaste sättet att skapa en ny virtuell dator från en hanterad disk avbildning är att använda Azure Portal. Mer information om hur du skapar en hanterad disk avbildning finns [i avbilda en hanterad avbildning av en generaliserad virtuell dator i Azure](../../windows/capture-image-resource.md)

1.  Gå till <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. Alternativt väljer du **bilder** på Azure Portal-menyn.
1.  Välj den hanterade disk avbildning som du vill distribuera och klicka på **Skapa virtuell dator**

Guiden vägleder dig genom att ange de parametrar som krävs för att skapa den virtuella datorn, förutom alla nödvändiga resurser, t. ex. nätverks gränssnitt och lagrings konton. Några av dessa parametrar är:

1. **Grunder**:
   * **Namn**: namnet på resursen (namnet på den virtuella datorn).
   * **Typ av virtuell dator disk**: Välj disk typen för OS-disken. Om du vill använda Premium Storage för dina data diskar rekommenderar vi att du även använder Premium Storage för operativ system disken.
   * **Användar namn och lösen ord** eller **Offentlig SSH-nyckel**: Ange användar namn och lösen ord för den användare som skapas under etableringen. För en virtuell Linux-dator kan du ange den SSH-nyckel (Public Secure Shell) som du använder för att logga in på datorn.
   * **Prenumeration**: Välj den prenumeration som du vill använda för att etablera den nya virtuella datorn.
   * **Resurs grupp**: namnet på resurs gruppen för den virtuella datorn. Du kan antingen ange namnet på en ny resurs grupp eller namnet på en resurs grupp som redan finns.
   * **Plats**: var den nya virtuella datorn ska distribueras. Om du vill ansluta den virtuella datorn till ditt lokala nätverk, se till att du väljer platsen för det virtuella nätverk som ansluter Azure till ditt lokala nätverk. Mer information finns i [Microsoft Azure nätverk][planning-guide-microsoft-azure-networking] i [Azure Virtual Machines planera och implementera för SAP NetWeaver][planning-guide].
1. **Storlek**:

     En lista över VM-typer som stöds finns i SAP anmärkning [1928533]. Se till att du väljer rätt VM-typ om du vill använda Azure Premium Storage. Alla typer av virtuella datorer har inte stöd för Premium Storage. Mer information finns i [lagring: Microsoft Azure Storage och data diskar][planning-guide-storage-microsoft-azure-storage-and-data-disks] och [Azure Storage för SAP-arbetsbelastningar](./planning-guide-storage.md) i [Azure Virtual Machines planera och implementera för SAP NetWeaver][planning-guide].

1. **Inställningar**:
   * **Storage**
     * **Disktyp**: Välj disk typen för OS-disken. Om du vill använda Premium Storage för dina data diskar rekommenderar vi att du även använder Premium Storage för operativ system disken.
     * **Använd Managed disks**: om du vill använda Managed disks väljer du Ja. Mer information om Managed Disks finns i kapitel [Managed disks](./planning-guide-storage.md#microsoft-azure-storage-resiliency) i planerings guiden.
   * **Nätverk**
     * **Virtuellt nätverk** och **undernät**: om du vill integrera den virtuella datorn med intranätet väljer du det virtuella nätverk som är anslutet till ditt lokala nätverk.
     * **Offentlig IP-adress**: Välj den offentliga IP-adress som du vill använda, eller ange parametrar för att skapa en ny offentlig IP-adress. Du kan använda en offentlig IP-adress för att få åtkomst till den virtuella datorn via Internet. Se till att du även skapar en nätverks säkerhets grupp för att skydda åtkomsten till den virtuella datorn.
     * **Nätverks säkerhets grupp**: Mer information finns i [kontrol lera nätverks trafik flöde med nätverks säkerhets grupper][virtual-networks-nsg].
   * **Tillägg**: du kan installera tillägg för virtuella datorer genom att lägga till dem i distributionen. Du behöver inte lägga till tillägg i det här steget. De tillägg som krävs för SAP-supporten installeras senare. Se kapitlet [Konfigurera Azure-tillägget för SAP][deployment-guide-4.5] i den här hand boken.
   * **Hög tillgänglighet**: Välj en tillgänglighets uppsättning eller ange parametrarna för att skapa en ny tillgänglighets uppsättning. Mer information finns i [Azures tillgänglighets uppsättningar][planning-guide-3.2.3].
   * **Övervakning**
     * **Startdiagnostik**: du kan välja **inaktivera** för startdiagnostik.
     * **Diagnostik för gäst operativ system**: du kan välja **inaktivera** för övervakning av diagnostik.

1. **Sammanfattning**:

   Granska dina val och välj sedan **OK**.

Den virtuella datorn distribueras i den resurs grupp som du har valt.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Skapa en virtuell dator med hjälp av en mall

Om du vill skapa en distribution med hjälp av en privat OS-avbildning från Azure Portal använder du någon av följande SAP-mallar. Dessa mallar publiceras i GitHub- [lagringsplatsen för Azure-snabb start-mallar][azure-quickstart-templates-github]. Du kan också skapa en virtuell dator manuellt med hjälp av [PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Konfiguration på två nivåer (endast en virtuell dator) mall** (SAP-2-Tier-User-image)][sap-templates-2-tier-user-image]

  Använd den här mallen om du vill skapa ett system på två nivåer med bara en virtuell dator.
* [**Konfiguration på två nivåer (endast en virtuell dator) mall för hanterad disk avbildning** (SAP-2-Tier-User-image-MD)][sap-templates-2-tier-user-image-md]

  Använd den här mallen om du vill skapa ett system med två nivåer genom att bara använda en virtuell dator och en hanterad disk avbildning.
* [**Konfiguration av tre nivåer (flera virtuella datorer) mall** (SAP-3-tier-User-image)][sap-templates-3-tier-user-image]

  Använd den här mallen om du vill skapa ett system på tre nivåer med hjälp av flera virtuella datorer eller din egen OS-avbildning.
* [**Konfiguration på tre nivåer (flera virtuella datorer) mall för hanterad disk avbildning** (SAP-3-tier-User-image-MD)][sap-templates-3-tier-user-image-md]

  Använd den här mallen om du vill skapa ett system på tre nivåer med hjälp av flera virtuella datorer eller din egen OS-avbildning och en hanterad disk avbildning.

Ange följande parametrar för mallen i Azure Portal:

1. **Grunder**:
   * **Prenumeration**: den prenumeration som ska användas för att distribuera mallen.
   * **Resurs grupp**: resurs gruppen som används för att distribuera mallen. Du kan skapa en ny resurs grupp eller välja en befintlig resurs grupp i prenumerationen.
   * **Plats**: var du vill distribuera mallen. Om du har valt en befintlig resurs grupp används platsen för resurs gruppen.
1. **Inställningar**:
   * **SAP-system-ID**: SAP-system-ID: t.
   * **OS-typ**: den typ av operativ system som du vill distribuera (Windows eller Linux).
   * **SAP-system storlek**: storleken på SAP-systemet.

     Antalet SAPS som det nya systemet erbjuder. Om du inte är säker på hur många SAPS systemet kräver kan du fråga din SAP Technology-partner eller system integrerare.
   * **System tillgänglighet** (endast mall i tre skikt): systemets tillgänglighet.

     Välj **ha** för en konfiguration som passar för en installation med hög tillgänglighet. Två databas servrar och två servrar för ASCS skapas.
   * **Lagrings typ** (endast mallar på två nivåer): den typ av lagring som ska användas.

     För större system rekommenderar vi starkt att du använder Azure Premium Storage. Mer information om lagrings typer finns i följande resurser:
      * [Användning av Azure Premium SSD Storage för SAP DBMS-instans][2367194]
      * [Lagrings struktur för en virtuell dator för RDBMS-distributioner](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: lagring med höga prestanda för arbets belastningar för virtuella Azure-datorer][storage-premium-storage-preview-portal]
      * [Introduktion till Microsoft Azure Storage][storage-introduction]
   * **VHD-URI för användar avbildning** (endast hanterad disk avbildnings mall): URI för den virtuella OS-avbildningens virtuella hård disk, till exempel https:// &lt; accountname>. blob.Core.Windows.net/VHDs/userimage.VHD.
   * **Användar avbildningens lagrings konto** (endast hanterade disk avbildnings mallar): namnet på det lagrings konto där den privata OS-avbildningen lagras, till exempel &lt; accountname> i https:// &lt; AccountName>. blob.Core.Windows.net/VHDs/userimage.VHD.
   * **userImageId** (endast hanterad disk avbildnings mal len): ID för den hanterade disk avbildning som du vill använda
   * **Administratörens användar namn** och **Administratörs lösen ord**: användar namn och lösen ord.

     En ny användare skapas för att logga in på den virtuella datorn.
   * **Nytt eller befintligt undernät**: avgör om ett nytt virtuellt nätverk och undernät skapas eller om ett befintligt undernät används. Om du redan har ett virtuellt nätverk som är anslutet till ditt lokala nätverk väljer du **befintligt**.
   * **Undernäts-ID**: om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har ett undernät definierat måste den virtuella datorn vara tilldelad, namnge ID: t för det aktuella under nätet. ID: t ser vanligt vis ut så här:/Subscriptions/ &lt; prenumerations-id>/resourceGroups/ &lt; resurs grupp namn>/providers/Microsoft.Network/virtualNetworks/ &lt; virtuellt nätverks namn>/subnets/ &lt; under nät namn>

1. **Allmänna** villkor:  
    Granska och godkänn de juridiska villkoren.

1. Välj **Köp**.

#### <a name="install-the-vm-agent-linux-only"></a>Installera VM-agenten (endast Linux)

Om du vill använda mallarna som beskrivs i föregående avsnitt, måste Linux-agenten redan vara installerad i användar avbildningen, annars kommer distributionen att Miss sen. Hämta och installera VM-agenten i användar avbildningen enligt beskrivningen i [Hämta, installera och aktivera Azure VM-agenten][deployment-guide-4.4]. Om du inte använder mallarna kan du också installera VM-agenten senare.

#### <a name="join-a-domain-windows-only"></a>Anslut till en domän (endast Windows)

Om din Azure-distribution är ansluten till en lokal Active Directory eller DNS-instans via en Azure plats-till-plats-VPN-anslutning eller Azure-ExpressRoute (Detta kallas för *olika platser* i [Azure Virtual Machines planering och implementering för SAP NetWeaver][planning-guide]) förväntas vi att den virtuella datorn ansluter till en lokal domän. Mer information om överväganden för det här steget finns i [ansluta en virtuell dator till en lokal domän (endast Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Konfigurera proxyinställningar

Beroende på hur ditt lokala nätverk är konfigurerat kan du behöva konfigurera proxyn på den virtuella datorn. Om den virtuella datorn är ansluten till ditt lokala nätverk via VPN eller ExpressRoute kanske den virtuella datorn inte har åtkomst till Internet, och det går inte att hämta de nödvändiga VM-tilläggen eller samla in information om Azure-infrastrukturen för SAP-värd agenten via SAP-tillägget för Azure. mer information finns i [Konfigurera proxyn][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Konfigurera Azure VM-tillägg för SAP

Om du vill vara säker på att SAP stöder din miljö konfigurerar du Azure-tillägget för SAP enligt beskrivningen i [Konfigurera Azure-tillägget för SAP][deployment-guide-4.5]. Kontrol lera kraven för SAP och de lägsta versionen av SAP kernel och SAP host agent som krävs i resurserna som listas i [SAP-resurser][deployment-guide-2.2].

#### <a name="sap-vm-extension-check"></a>Kontroll av SAP VM-tillägg

Kontrol lera om VM-tillägget för SAP fungerar, enligt beskrivningen i [checkar och fel sökning][deployment-guide-troubleshooting-chapter].


### <a name="scenario-3-moving-an-on-premises-vm-by-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Scenario 3: flytta en lokal virtuell dator med hjälp av en icke-generaliserad virtuell Azure-dator med SAP

I det här scenariot planerar du att flytta ett särskilt SAP-system från en lokal miljö till Azure. Det kan du göra genom att ladda upp den virtuella hård disk som har operativ systemet, SAP-binärfilerna och till sist de virtuella hård diskarna, samt de virtuella hård diskarna med data-och loggfilerna för DBMS, till Azure. Till skillnad från scenariot som beskrivs i [Scenario 2: Distribuera en virtuell dator med en anpassad avbildning för SAP][deployment-guide-3.3], i det här fallet, behåller du värd namnet, SAP sid och SAP-användarkonton i den virtuella Azure-datorn, eftersom de har kon figurer ATS i den lokala miljön. Du behöver inte generalisera operativ systemet. Det här scenariot gäller oftast scenarier med flera platser där en del av SAP-landskapet körs lokalt och en del av det körs på Azure.

I det här scenariot installeras **inte** VM-agenten automatiskt under distributionen. Eftersom VM-agenten och Azure-tillägget för SAP krävs för att köra SAP NetWeaver på Azure måste du ladda ned, installera och aktivera båda komponenterna manuellt när du har skapat den virtuella datorn.

Mer information om Azure VM-agenten finns i följande resurser.

---
> ![Windows-logotyp.][Logo_Windows] Windows
>
> [Översikt över Azure Virtual Machine agent][virtual-machines-windows-agent-user-guide]
>
> ![Linux-logotyp.][Logo_Linux] Linux
>
> [Användarguide för Azure Linux-agenten][virtual-machines-linux-agent-user-guide]
>
>

---

Följande flödes schema visar ordningen på stegen för att flytta en lokal virtuell dator med hjälp av en icke-generaliserad Azure-VHD:

![Flödes schema för VM-distribution för SAP-system med hjälp av en virtuell dator disk][deployment-guide-figure-400]

Om disken redan har laddats upp och definierats i Azure (se [azure Virtual Machines planering och implementering för SAP NetWeaver][planning-guide]) ska du utföra de uppgifter som beskrivs i följande avsnitt.

#### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Om du vill skapa en distribution med hjälp av en privat OS-disk via Azure Portal använder du SAP-mallen som publicerades i [Azure-snabb start-mallarna GitHub-lagringsplats][azure-quickstart-templates-github]. Du kan också skapa en virtuell dator manuellt med hjälp av PowerShell.

* [**Konfiguration på två nivåer (endast en virtuell dator) mall** (SAP-2-Tier-User-disk)][sap-templates-2-tier-os-disk]

  Använd den här mallen om du vill skapa ett system på två nivåer med bara en virtuell dator.
* [**Konfiguration på två nivåer (endast en virtuell dator) mall för hanterad disk** (SAP-2-Tier-User-disk-MD)][sap-templates-2-tier-os-disk-md]

  Använd den här mallen om du vill skapa ett system med två nivåer genom att bara använda en virtuell dator och en hanterad disk.

Ange följande parametrar för mallen i Azure Portal:

1. **Grunder**:
   * **Prenumeration**: den prenumeration som ska användas för att distribuera mallen.
   * **Resurs grupp**: resurs gruppen som används för att distribuera mallen. Du kan skapa en ny resurs grupp eller välja en befintlig resurs grupp i prenumerationen.
   * **Plats**: var du vill distribuera mallen. Om du har valt en befintlig resurs grupp används platsen för resurs gruppen.
1. **Inställningar**:
   * **SAP-system-ID**: SAP-system-ID: t.
   * **OS-typ**: den typ av operativ system som du vill distribuera (Windows eller Linux).
   * **SAP-system storlek**: storleken på SAP-systemet.

     Antalet SAPS som det nya systemet erbjuder. Om du inte är säker på hur många SAPS systemet kräver kan du fråga din SAP Technology-partner eller system integrerare.
   * **Lagrings typ** (endast mallar på två nivåer): den typ av lagring som ska användas.

     För större system rekommenderar vi starkt att du använder Azure Premium Storage. Mer information om lagrings typer finns i följande resurser:
      * [Användning av Azure Premium SSD Storage för SAP DBMS-instans][2367194]
      * [Lagrings struktur för en virtuell dator för RDBMS-distributioner](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: lagring med höga prestanda för arbets belastningar för virtuella Azure-datorer][storage-premium-storage-preview-portal]
      * [Introduktion till Microsoft Azure Storage][storage-introduction]
   * **VHD-URI för OS-disk** (endast hanterad disk mall): URI för den privata OS-disken, till exempel https:// &lt; accountname>. blob.Core.Windows.net/VHDs/OSDisk.VHD.
   * **Hanterat disk-ID för operativ system disk** (endast mall för hanterad disk): ID för den hanterade DISKens OS-disk,/Subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/Group/providers/Microsoft.Compute/disks/Win
   * **Nytt eller befintligt undernät**: avgör om ett nytt virtuellt nätverk och undernät skapas, eller om ett befintligt undernät används. Om du redan har ett virtuellt nätverk som är anslutet till ditt lokala nätverk väljer du **befintligt**.
   * **Undernäts-ID**: om du vill distribuera den virtuella datorn till ett befintligt virtuellt nätverk där du har ett undernät definierat måste den virtuella datorn vara tilldelad, namnge ID: t för det aktuella under nätet. ID: t ser vanligt vis ut så här:/Subscriptions/ &lt; prenumerations-id>/resourceGroups/ &lt; resurs grupp namn>/providers/Microsoft.Network/virtualNetworks/ &lt; virtuellt nätverks namn>/subnets/ &lt; under nät namn>

1. **Allmänna** villkor:  
    Granska och godkänn de juridiska villkoren.

1. Välj **Köp**.

#### <a name="install-the-vm-agent"></a>Installera VM-agenten

Om du vill använda mallarna som beskrivs i föregående avsnitt måste den virtuella dator agenten vara installerad på operativ system disken, annars kommer distributionen att Miss klar. Hämta och installera VM-agenten på den virtuella datorn, enligt beskrivningen i [Hämta, installera och aktivera Azure VM-agenten][deployment-guide-4.4].

Om du inte använder mallarna som beskrivs i föregående avsnitt kan du också installera VM-agenten efteråt.

#### <a name="join-a-domain-windows-only"></a>Anslut till en domän (endast Windows)

Om din Azure-distribution är ansluten till en lokal Active Directory eller DNS-instans via en Azure plats-till-plats VPN-anslutning eller ExpressRoute (Detta kallas för *olika platser* i [Azure Virtual Machines planering och implementering för SAP NetWeaver][planning-guide]) förväntas vi att den virtuella datorn ansluter till en lokal domän. Mer information om överväganden för den här uppgiften finns i [ansluta en virtuell dator till en lokal domän (endast Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Konfigurera proxyinställningar

Beroende på hur ditt lokala nätverk är konfigurerat kan du behöva konfigurera proxyn på den virtuella datorn. Om den virtuella datorn är ansluten till ditt lokala nätverk via VPN eller ExpressRoute kanske den virtuella datorn inte har åtkomst till Internet, och det går inte att hämta de nödvändiga VM-tilläggen eller samla in information om Azure-infrastrukturen för SAP-värd agenten via SAP-tillägget för Azure. mer information finns i [Konfigurera proxyn][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Konfigurera Azure VM-tillägg för SAP

Om du vill vara säker på att SAP stöder din miljö konfigurerar du Azure-tillägget för SAP enligt beskrivningen i [Konfigurera Azure-tillägget för SAP][deployment-guide-4.5]. Kontrol lera kraven för SAP och de lägsta versionen av SAP kernel och SAP host agent som krävs i resurserna som listas i [SAP-resurser][deployment-guide-2.2].

#### <a name="sap-vm-check"></a>Kontroll av SAP VM

Kontrol lera om VM-tillägget för SAP fungerar, enligt beskrivningen i [checkar och fel sökning][deployment-guide-troubleshooting-chapter].

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>Uppdatera konfigurationen av Azure-tillägget för SAP

Uppdatera konfigurationen av Azure-tillägget för SAP i något av följande scenarier:
* Det gemensamma Microsoft/SAP-teamet utökar funktionerna i VM-tillägget och begär fler eller färre räknare.
* Microsoft presenterar en ny version av den underliggande Azure-infrastrukturen som levererar data och Azure-tillägget för SAP måste anpassas till dessa ändringar.
* Du monterar ytterligare data diskar till din virtuella Azure-dator eller tar bort en datadisk. I det här scenariot uppdaterar du insamlingen av Storage-relaterade data. Att ändra konfigurationen genom att lägga till eller ta bort slut punkter eller genom att tilldela IP-adresser till en virtuell dator påverkar inte tilläggs konfigurationen.
* Du ändrar storleken på din virtuella Azure-dator, till exempel från storlek A5 till någon annan VM-storlek.
* Du lägger till nya nätverks gränssnitt till din virtuella Azure-dator.

Uppdatera inställningarna genom att uppdatera konfigurationen av Azure-tillägget för SAP genom att följa stegen i [Konfigurera Azure-tillägget för SAP][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>Detaljerade uppgifter för distribution av SAP-program

Det här avsnittet innehåller detaljerade anvisningar för hur du utför vissa uppgifter i konfigurations-och distributions processen.

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Distribuera Azure PowerShell-cmdletar

Följ stegen som beskrivs i artikeln [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps)

Sök ofta efter uppdateringar av PowerShell-cmdletarna, som vanligt vis uppdateras varje månad. Följ stegen som beskrivs i [den här](/powershell/azure/install-az-ps#update-the-azure-powershell-module) artikeln. Om inget annat anges i SAP NOTE [1928533] eller SAP NOTE [2015553], rekommenderar vi att du arbetar med den senaste versionen av Azure PowerShell-cmdletar.

Om du vill kontrol lera vilken version av Azure PowerShell-cmdletarna som är installerade på datorn kör du följande PowerShell-kommando:

```powershell
(Get-Module Az.Compute).Version
```

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Distribuera Azure CLI

Följ stegen som beskrivs i artikeln [Installera Azure CLI](/cli/azure/install-azure-cli)

Sök ofta efter uppdateringar till Azure CLI, som vanligt vis uppdateras varje månad.

Kör följande kommando för att kontrol lera vilken version av Azure CLI som är installerad på datorn:

```console
az --version
```

### <a name="join-a-vm-to-an-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Anslut en virtuell dator till en lokal domän (endast Windows)

Om du distribuerar virtuella SAP-datorer i ett scenario mellan platser, där lokala Active Directory och DNS utökas i Azure, förväntas det att de virtuella datorerna är kopplade till en lokal domän. Detaljerade anvisningar som du vidtar för att ansluta en virtuell dator till en lokal domän och den ytterligare program vara som krävs för att vara medlem i en lokal domän varierar beroende på kund. Om du vill ansluta en virtuell dator till en lokal domän måste du vanligt vis installera ytterligare program vara, till exempel program mot skadlig kod och program vara för säkerhets kopiering eller övervakning.

I det här scenariot måste du också se till att om Internet-proxyinställningarna framtvingas när en virtuell dator ansluter till en domän i din miljö, har Windows lokalt system konto (S-1-5-18) i den virtuella gäst datorn samma proxyinställningar. Det enklaste alternativet är att tvinga proxyn genom att använda en domän grupprincip, som gäller system i domänen.

### <a name="download-install-and-enable-the-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Ladda ned, installera och aktivera Azure VM-agenten

För virtuella datorer som distribueras från en OS-avbildning som inte är generaliserad (till exempel en avbildning som inte kommer från Windows system förberedelse eller Sysprep, verktyg) måste du manuellt hämta, installera och aktivera Azure VM-agenten.

Det här steget krävs inte om du distribuerar en virtuell dator från Azure Marketplace. Avbildningar från Azure Marketplace har redan Azure VM-agenten.

#### <a name="windows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Ladda ned Azure VM-agenten:
   1.  Hämta [installations paketet för Azure VM-agenten](https://go.microsoft.com/fwlink/?LinkId=394789).
   1.  Lagra VM-agentens MSI-paket lokalt på en personlig dator eller server.
1. Installera Azure VM-agenten:
   1.  Anslut till den distribuerade virtuella Azure-datorn med hjälp av Remote Desktop Protocol (RDP).
   1.  Öppna Windows Explorer-fönstret på den virtuella datorn och välj mål katalogen för MSI-filen för VM-agenten.
   1.  Dra MSI-filen för installations programmet för Azure VM-agenten från din lokala dator/server till mål katalogen för VM-agenten på den virtuella datorn.
   1.  Dubbelklicka på MSI-filen på den virtuella datorn.
1. För virtuella datorer som är anslutna till lokala domäner, se till att eventuella Internet proxyinställningar även gäller för det lokala system kontot (S-1-5-18) för Windows i den virtuella datorn, enligt beskrivningen i [Konfigurera proxyn][deployment-guide-configure-proxy]. VM-agenten körs i den här kontexten och måste kunna ansluta till Azure.

Ingen användar åtgärd krävs för att uppdatera Azure VM-agenten. VM-agenten uppdateras automatiskt och kräver ingen omstart av datorn.

#### <a name="linux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

Använd följande kommandon för att installera VM-agenten för Linux:

* **SUSE Linux Enterprise Server (SLES)**

  ```console
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) eller Oracle Linux**

  ```console
  sudo yum install WALinuxAgent
  ```

Om agenten redan är installerad, för att uppdatera Azure Linux-agenten, utför du stegen som beskrivs i [Uppdatera Azure Linux-agenten på en virtuell dator till den senaste versionen från GitHub][virtual-machines-linux-update-agent].

### <a name="configure-the-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Konfigurera proxyn

De steg som du vidtar för att konfigurera proxyservern i Windows skiljer sig från hur du konfigurerar proxyservern i Linux.

#### <a name="windows"></a>Windows

Proxyinställningarna måste konfigureras korrekt för det lokala system kontot för att få åtkomst till Internet. Om proxyinställningarna inte anges av grupprincip kan du konfigurera inställningarna för det lokala system kontot.

1. Gå till **Start**, ange **gpedit. msc** och välj sedan **RETUR**.
1. Välj **dator konfiguration**  >  **administrativa mallar**  >  **Windows-komponenter**  >  **Internet Explorer**. Kontrol lera att inställningen **gör proxyinställningar per dator (i stället för per användare)** inaktive rad eller inte konfigurerad.
1. I **kontroll panelen** går du till **nätverks-och delnings Center**  >  **Internet alternativ**.
1. På fliken **anslutningar** väljer du knappen **LAN-inställningar** .
1. Avmarkera kryssrutan **Automatisk identifiering av inställningar**.
1. Markera kryss rutan **Använd en proxyserver för ditt lokala nätverk** och ange sedan proxyservern och-porten.
1. Välj knappen **Avancerat** .
1. I rutan **undantag** anger du IP- **168.63.129.16**. Välj **OK**.

#### <a name="linux"></a>Linux

Konfigurera rätt proxy i konfigurations filen för Microsoft Azure gäst agenten, som finns i \\ etc \\ . waagent. conf.

Ange följande parametrar:

1. **Värd för http-proxy**. Ange till exempel den som **proxy. Corp. local**.

   ```console
   HttpProxy.Host=<proxy host>

   ```

1. **Http-proxyserver**. Ange till exempel till **80**.

   ```console
   HttpProxy.Port=<port of the proxy host>

   ```

1. Starta om agenten.

   ```console
   sudo service waagent restart
   ```

Proxyinställningarna i \\ osv \\ waagent. conf gäller även för de VM-tillägg som krävs. Om du vill använda Azure-lagringsplatserna kontrollerar du att trafiken till dessa databaser inte går via ditt lokala intranät. Om du har skapat användardefinierade vägar för att aktivera Tvingad tunnel trafik, se till att du lägger till en väg som dirigerar trafik till databaserna direkt till Internet och inte via VPN-anslutningen från plats till plats.

* **SLES**

  Du måste också lägga till vägar för IP-adresserna som anges i \\ osv \\ regionserverclnt. cfg. Följande bild visar ett exempel:

  ![Tvingad tunneltrafik][deployment-guide-figure-50]


* **RHEL**

  Du måste också lägga till vägar för IP-adresserna för de värdar som anges i \\ osv \\ yum. databaser. d \\ rhui-belastningsutjämnare. Ett exempel finns i föregående figur.

* **Oracle Linux**

  Det finns inga databaser för Oracle Linux på Azure. Du måste konfigurera dina egna databaser för Oracle Linux eller använda de offentliga lagrings platserna.

Mer information om användardefinierade vägar finns i [användardefinierade vägar och IP-vidarebefordring][virtual-networks-udr-overview].

### <a name="configure-the-azure-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Konfigurera Azure-tillägget för SAP

> [!NOTE]
> Allmän support instruktion:  
> Support för Azure-tillägget för SAP tillhandahålls via SAP-support kanaler. Om du behöver hjälp med Azure-tillägget för SAP måste du öppna ett support ärende med [SAP-support](https://support.sap.com/). 

När du har för berett den virtuella datorn enligt beskrivningen i [distributions scenarier för virtuella datorer för SAP på Azure][deployment-guide-3]installeras Azure VM-agenten på den virtuella datorn. Nästa steg är att distribuera Azure-tillägget för SAP, som är tillgängligt i Azure Extension-lagringsplatsen i de globala Azure-datacentren. Mer information finns i [Azure Virtual Machines planera och implementera för SAP NetWeaver][planning-guide-9.1].

Vi håller på att lansera en ny version av Azure-tillägget för SAP. Det nya tillägget använder den systemtilldelade identiteten för den virtuella datorn för att få information om de anslutna diskarna, nätverks gränssnitten och själva virtuella datorn. För att kunna få åtkomst till dessa resurser måste system identiteten för den virtuella datorn ha läsar behörighet för den virtuella datorn, OS-disken, data diskarna och nätverks gränssnitten. Vi rekommenderar för närvarande att bara installera det nya tillägget i följande scenarier:

1. Du vill installera tillägget med terraform, Azure Resource Manager mallar eller med andra metoder än Azure CLI eller Azure PowerShell
1. Du vill installera tillägget på SUSE SLES 15 eller senare.
1. Microsoft eller SAP-support uppmanar dig att installera det nya tillägget
1. Du vill använda Azure Ultra disk eller standard Managed Disks

I dessa scenarier följer du stegen i kapitlet [Konfigurera det nya Azure-tillägget för SAP med Azure PowerShell][deployment-guide-configure-new-extension-ps] för Azure PowerShell eller [Konfigurera det nya Azure-tillägget för SAP med Azure CLI][deployment-guide-configure-new-extension-cli] för Azure CLI.

Följ [Azure PowerShell][deployment-guide-4.5.1] eller [Azure CLI][deployment-guide-4.5.2] för att installera och konfigurera standard versionen av Azure-tillägget för SAP.

#### <a name="azure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell för virtuella Linux-och Windows-datorer

Så här installerar du Azure-tillägget för SAP med hjälp av PowerShell:

1. Kontrol lera att du har installerat den senaste versionen av Azure PowerShell-cmdleten. Mer information finns i [distribuera Azure PowerShell-cmdletar][deployment-guide-4.1].  
1. Kör följande PowerShell-cmdlet:
    För en lista över tillgängliga miljöer kör du cmdlet `Get-AzEnvironment` . Om du vill använda Global Azure är din miljö **AzureCloud**. För Azure Kina 21Vianet väljer du **AzureChinaCloud**.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

När du har angett dina konto data distribuerar skriptet de nödvändiga tilläggen och aktiverar de nödvändiga funktionerna. Det kan ta flera minuter.
Mer information `Set-AzVMAEMExtension` finns i [set-AzVMAEMExtension][msdn-set-Azvmaemextension].

![Lyckad körning av SAP-Specific Azure-cmdlet Set-AzVMAEMExtension][deployment-guide-figure-900]

`Set-AzVMAEMExtension`Konfigurationen gör alla steg för att konfigurera värd data insamling för SAP.

Utdata i skriptet innehåller följande information:

* Bekräfta att data insamlingen för OS-disken och alla ytterligare data diskar har kon figurer ATS.
* Följande två meddelanden bekräftar konfigurationen av lagrings mått för ett angivet lagrings konto.
* En rad utdata ger status för den faktiska uppdateringen av VM-tillägget för SAP-konfiguration.
* En annan rad utdata bekräftar att konfigurationen har distribuerats eller uppdaterats.
* Den sista raden med utdata är information. Den visar dina alternativ för att testa VM-tillägget för SAP-konfigurationen.
* För att kontrol lera att alla steg i Azure VM-tillägget för SAP-konfigurationen har körts och att Azure-infrastrukturen tillhandahåller nödvändiga data, fortsätter du med beredskaps kontrollen för Azure-tillägget för SAP, enligt beskrivningen i [beredskaps kontroll för Azure][deployment-guide-5.1]-tillägget för SAP.
* Vänta 15-30 minuter för Azure-diagnostik att samla in relevanta data.

#### <a name="azure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Azure CLI för virtuella Linux-datorer

Så här installerar du Azure-tillägget för SAP med hjälp av Azure CLI:

   1. Installera den klassiska Azure-CLI: en som beskrivs i [installera den klassiska Azure-CLI: en][azure-cli].
   1. Logga in med ditt Azure-konto:

      ```console
      azure login
      ```

   1. Växla till Azure Resource Manager läge:

      ```console
      azure config mode arm
      ```

   1. Aktivera Azure-tillägg för SAP:

      ```console
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Installera med Azure CLI 2,0

   1. Installera Azure CLI 2,0 enligt beskrivningen i [Installera Azure cli 2,0][azure-cli-2].
   1. Logga in med ditt Azure-konto:

      ```azurecli
      az login
      ```

   1. Installera Azure CLI AEM-tillägg
  
      ```azurecli
      az extension add --name aem
      ```
  
   1. Installera tillägget med
  
      ```azurecli
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Kontrol lera att Azure-tillägget för SAP är aktivt på den virtuella Azure Linux-datorn. Kontrol lera om filen \\ var \\ lib- \\ AzureEnhancedMonitor \\ PerfCounters finns. Om den finns går du till kommando tolken och kör det här kommandot för att visa information som samlats in av Azure-tillägget för SAP:

   ```console
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   Utdata ser ut så här:

   ```output
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

#### <a name="configure-the-new-azure-extension-for-sap-with-azure-powershell"></a><a name="2ad55a0d-9937-4943-9dd2-69bc2b5d3de0"></a>Konfigurera det nya Azure-tillägget för SAP med Azure PowerShell

Det nya VM-tillägget för SAP använder en hanterad identitet som tilldelats den virtuella datorn för att komma åt övervaknings-och konfigurations data för den virtuella datorn. Om du vill installera det nya Azure-tillägget för SAP med hjälp av PowerShell måste du först tilldela en sådan identitet till den virtuella datorn och bevilja identitets åtkomst till alla resurser som används av den virtuella datorn, t. ex. diskar och nätverks gränssnitt.

> [!NOTE]
> Följande steg kräver ägar privilegier över resurs gruppen eller enskilda resurser (virtuell dator, data diskar osv.)

1. Se till att använda SAP host agent 7,21 PL 47 eller högre.
1. Se till att avinstallera den aktuella versionen av VM-tillägget för SAP. Det finns inte stöd för att installera båda versionerna av VM-tillägget för SAP på samma virtuella dator.
1. Kontrol lera att du har installerat den senaste versionen av Azure PowerShell-cmdlet (minst 4.3.0). Mer information finns i [distribuera Azure PowerShell-cmdletar][deployment-guide-4.1].
1. Kör följande PowerShell-cmdlet:
    För en lista över tillgängliga miljöer kör du cmdlet `Get-AzEnvironment` . Om du vill använda Global Azure är din miljö **AzureCloud**. För Azure Kina 21Vianet väljer du **AzureChinaCloud**.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name> -InstallNewExtension
    ```

#### <a name="configure-the-new-azure-extension-for-sap-with-azure-cli"></a><a name="c8749c24-fada-42ad-b114-f9aae2dc37da"></a>Konfigurera det nya Azure-tillägget för SAP med Azure CLI

Det nya VM-tillägget för SAP använder en hanterad identitet som tilldelats den virtuella datorn för att komma åt övervaknings-och konfigurations data för den virtuella datorn. Om du vill installera det nya Azure-tillägget för SAP med hjälp av Azure CLI måste du först tilldela en sådan identitet till den virtuella datorn och bevilja identitets åtkomst till alla resurser som används av den virtuella datorn, till exempel diskar och nätverks gränssnitt.

> [!NOTE]
> Följande steg kräver ägar privilegier över resurs gruppen eller enskilda resurser (virtuell dator, data diskar osv.)

1. Se till att använda SAP host agent 7,21 PL 47 eller högre.
1. Se till att avinstallera den aktuella versionen av VM-tillägget för SAP. Det finns inte stöd för att installera båda versionerna av VM-tillägget för SAP på samma virtuella dator.
1. Installera Azure CLI 2,0 enligt beskrivningen i [Installera Azure cli 2,0][azure-cli-2].

1. Logga in med ditt Azure-konto:

   ```azurecli
   az login
   ```

1. Installera Azure CLI AEM-tillägget. Se till att du använder minst version 0.2.0 eller högre.
  
   ```azurecli
   az extension add --name aem
   ```
  
1. Installera det nya tillägget med
  
   ```azurecli
   az vm aem set -g <resource-group-name> -n <vm name> --install-new-extension
   ```

## <a name="checks-and-troubleshooting"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Kontroller och fel sökning

När du har distribuerat den virtuella Azure-datorn och konfigurerat Azure-tillägget för SAP kontrollerar du om alla komponenter i tillägget fungerar som förväntat.

Kör beredskaps kontrollen för Azure-tillägget för SAP enligt beskrivningen i [beredskaps kontroll för Azure-tillägget för SAP][deployment-guide-5.1]. Om alla beredskaps kontroll resultat är positiva och alla relevanta prestanda räknare visas OK, har Azure-tillägget för SAP kon figurer ATS korrekt. Du kan fortsätta med installationen av SAP host agent enligt beskrivningen i SAP-anteckningar i [SAP-resurser][deployment-guide-2.2]. Om beredskaps kontrollen indikerar att räknare saknas, kör du hälso kontrollen för Azure-tillägget för SAP, enligt beskrivningen i [hälso kontroll för Azure-tillägget för SAP-konfiguration][deployment-guide-5.2]. Mer fel söknings alternativ finns i [Felsöka Azure-tillägg för SAP][deployment-guide-5.3].

### <a name="readiness-check-for-the-azure-extension-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Beredskaps kontroll för Azure-tillägget för SAP

> [!NOTE]
> Det finns två versioner av VM-tillägget. Det här kapitlet beskriver standard tillägget för virtuell dator. Om du har installerat det nya tillägget för virtuella datorer kan du se kapitel [beredskap för det nya Azure-tillägget för SAP][deployment-guide-5.1-new]

Den här kontrollen säkerställer att alla prestanda mått som visas i ditt SAP-program tillhandahålls av det underliggande Azure-tillägget för SAP.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Kör beredskaps kontrollen på en virtuell Windows-dator

1. Logga in på den virtuella Azure-datorn (med ett administratörs konto behövs inte).
1. Öppna ett kommandotolksfönster.
1. I kommando tolken ändrar du katalogen till installationsmappen för Azure-tillägget för SAP: C: \\ packages plugin- \\ program \\ Microsoft. AzureCAT. AzureEnhancedMonitoring. AzureCATExtensionHandler \\ &lt; version>\\ drop

   *Versionen* i sökvägen till tillägget kan variera. Om du ser mappar för flera versioner av tillägget i installationsmappen kontrollerar du konfigurationen av Windows-tjänsten AzureEnhancedMonitoring och växlar sedan till mappen som anges som *sökväg till körbar fil*.

   ![Egenskaper för tjänsten som kör Azure-tillägget för SAP][deployment-guide-figure-1000]

1. Kör **azperflib.exe** utan parametrar i kommando tolken.

   > [!NOTE]
   > Azperflib.exe körs i en slinga och uppdaterar de insamlade räknarna var 60: e sekund. Stäng kommando tolks fönstret för att avsluta slingan.
   >
   >

Om Azure-tillägget för SAP inte är installerat, eller om AzureEnhancedMonitoring-tjänsten inte körs, har tillägget inte kon figurer ATS korrekt. Detaljerad information om hur du distribuerar tillägget finns i [Felsöka Azure-tillägget för SAP][deployment-guide-5.3].

> [!NOTE]
> Azperflib.exe är en komponent som inte kan användas för egna syfte. Det är en komponent som levererar Azures infrastruktur data som är relaterade till den virtuella datorn för SAP-värd agenten exklusivt.
> 

##### <a name="check-the-output-of-azperflibexe"></a>Kontrol lera utdata från azperflib.exe

Azperflib.exe utdata visar alla fyllda Azure-prestandaräknare för SAP. Längst ned i listan över insamlade räknare visar en sammanfattnings-och hälso indikator statusen för Azure-tillägget för SAP.

![Utdata från hälso kontroll genom att köra azperflib.exe, vilket tyder på att det inte finns några problem][deployment-guide-figure-1100]
<a name="figure-11"></a>

Kontrol lera resultatet som returnerades för **räknaren Totalt antal** utdata, som rapporteras som tomt och för **hälso status**, som visas i föregående bild.

Tolka de resulterande värdena enligt följande:

| Azperflib.exe resultat värden | Azure-tillägg för hälso status för SAP |
| --- | --- |
| **API-anrop – inte tillgängligt** | Räknare som inte är tillgängliga kan vara antingen inte tillämpliga för konfigurationen av den virtuella datorn eller är fel. Se **hälso status**. |
| **Totalt antal räknare – tomt** |Följande två räknare för Azure Storage kan vara tomma: <ul><li>Storage Read op latens-Server MSEK</li><li>Lagring Läs op latens E2E MSEK</li></ul>Alla andra räknare måste ha värden. |
| **Hälso status** |OK om retur status visas **OK**. |
| **Diagnostik** |Detaljerad information om hälso status. |

Om värdet för **hälso status** inte är **OK**, följer du anvisningarna i [hälso kontroll för Azure-tillägget för SAP-konfiguration][deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Kör beredskaps kontrollen på en virtuell Linux-dator

1. Anslut till den virtuella Azure-datorn med hjälp av SSH.

1. Kontrol lera utdata från Azure-tillägget för SAP.

   a.  Kör `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **Förväntat resultat**: returnerar listan över prestanda räknare. Filen får inte vara tom.

   b. Kör `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **Förväntat resultat**: returnerar en rad där felet är **ingen**, till exempel **3; config; Fel;; 0; 0; ingen; 0; 1456416792; TST-servercs;**

   c. Kör `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

   **Förväntat resultat**: returneras som tomt eller finns inte.

Om föregående kontroll inte lyckades kör du följande ytterligare kontroller:

1. Kontrol lera att waagent har installerats och Aktiver ATS.

   a.  Kör `sudo ls -al /var/lib/waagent/`

     **Förväntat resultat**: visar innehållet i waagent-katalogen.

   b.  Kör `ps -ax | grep waagent`

   **Förväntat resultat**: visar en post som liknar: `python /usr/sbin/waagent -daemon`

1. Kontrol lera att Azure-tillägget för SAP är installerat och körs.

   a.  Kör `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`

   **Förväntat resultat**: visar innehållet i Azure-tillägget för SAP-katalogen.

   b. Kör `ps -ax | grep AzureEnhanced`

   **Förväntat resultat**: visar en post som liknar: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Installera SAP host agent enligt beskrivningen i SAP anmärkning [1031096]och kontrol lera utdata från `saposcol` .

   a.  Kör `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Kör `dump ccm`

   c.  Kontrol lera om den **Virtualization_Configuration \Enhanced övervaknings åtkomst** måttet är **Sant**.

Om du redan har en SAP NetWeaver ABAP-Programserver installerad kan du öppna transaktion ST06 och kontrol lera om övervakning har Aktiver ATS.

Om någon av dessa kontroller inte fungerar och detaljerad information om hur du distribuerar om tillägget finns i [Felsöka Azure-tillägget för SAP][deployment-guide-5.3].

### <a name="readiness-check-for-the-new-azure-extension-for-sap"></a><a name="7bf24f59-7347-4c7a-b094-4693e4687ee5"></a>Beredskaps kontroll för det nya Azure-tillägget för SAP

> [!NOTE]
> Det finns två versioner av VM-tillägget. I det här kapitlet beskrivs det nya VM-tillägget. Om du har installerat standard tillägget för virtuell dator, se kapitel [beredskaps kontroll för Azure-tillägget för SAP][deployment-guide-5.1].

Den här kontrollen säkerställer att alla prestanda mått som visas i ditt SAP-program tillhandahålls av det underliggande Azure-tillägget för SAP.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Kör beredskaps kontrollen på en virtuell Windows-dator

1. Logga in på den virtuella Azure-datorn (med ett administratörs konto behövs inte).
1. Öppna en webbläsare och gå till http://127.0.0.1:11812/azure4sap/metrics
1. Webbläsaren bör visa eller hämta en XML-fil som innehåller övervaknings data för den virtuella datorn. Om så inte är fallet kontrollerar du att Azure-tillägget för SAP är installerat.

##### <a name="check-the-content-of-the-xml-file"></a>Kontrol lera innehållet i XML-filen

XML-filen som du har åtkomst till http://127.0.0.1:11812/azure4sap/metrics innehåller alla ifyllda Azure-prestandaräknare för SAP. Den innehåller också en sammanfattning och en hälso indikator för Azure-tillägget för SAP.

Kontrol lera värdet för **beskrivnings elementet för providerns hälso tillstånd** . Om värdet inte är **OK**, följer du anvisningarna i [hälso kontroll för nya Azure-tillägg för SAP-konfiguration][deployment-guide-5.2-new].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Kör beredskaps kontrollen på en virtuell Linux-dator

1. Anslut till den virtuella Azure-datorn med hjälp av SSH.

1. Kontrol lera utdata från följande kommando

    ```console
    curl http://127.0.0.1:11812/azure4sap/metrics
    ```
    
   **Förväntat resultat**: returnerar ett XML-dokument som innehåller övervaknings information för den virtuella datorn, dess diskar och nätverks gränssnitt.

Om föregående kontroll inte lyckades kör du följande ytterligare kontroller:

1. Kontrol lera att waagent har installerats och Aktiver ATS.

   a.  Kör `sudo ls -al /var/lib/waagent/`

     **Förväntat resultat**: visar innehållet i waagent-katalogen.

   b.  Kör `ps -ax | grep waagent`

   **Förväntat resultat**: visar en post som liknar: `python /usr/sbin/waagent -daemon`

1. Kontrol lera att Azure-tillägget för SAP är installerat och körs.

   a.  Kör `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-*/'`

   **Förväntat resultat**: visar innehållet i Azure-tillägget för SAP-katalogen.

   b. Kör `ps -ax | grep AzureEnhanced`

   **Förväntat resultat**: visar en post som liknar: `/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-1.0.0.82/AzureEnhancedMonitoring -monitor`

1. Installera SAP host agent enligt beskrivningen i SAP anmärkning [1031096]och kontrol lera utdata från `saposcol` .

   a.  Kör `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Kör `dump ccm`

   c.  Kontrol lera om den **Virtualization_Configuration \Enhanced övervaknings åtkomst** måttet är **Sant**.

Om du redan har en SAP NetWeaver ABAP-Programserver installerad kan du öppna transaktion ST06 och kontrol lera om övervakning har Aktiver ATS.

Om någon av dessa kontroller inte fungerar och detaljerad information om hur du distribuerar om tillägget finns i [Felsöka det nya Azure-tillägget för SAP][deployment-guide-5.3-new].

### <a name="health-check-for-the-azure-extension-for-sap-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Hälso kontroll för Azure-tillägget för SAP-konfiguration

> [!NOTE]
> Det finns två versioner av VM-tillägget. Det här kapitlet beskriver standard tillägget för virtuell dator. Om du har installerat det nya VM-tillägget kan du läsa mer i kapitel [hälso kontroll för den nya Azure-tillägget för SAP-konfiguration][deployment-guide-5.2-new].

Om några av infrastruktur data inte levereras korrekt enligt beskrivningen i [beredskaps kontrollen för Azure-tillägget för SAP][deployment-guide-5.1], kör du `Test-AzVMAEMExtension` cmdleten för att kontrol lera om Azure-infrastrukturen och Azure-tillägget för SAP är korrekt konfigurerade.

1. Kontrol lera att du har installerat den senaste versionen av Azure PowerShell-cmdlet, enligt beskrivningen i [distribuera Azure PowerShell-cmdletar][deployment-guide-4.1].
1. Kör följande PowerShell-cmdlet: Kör cmdleten om du vill ha en lista över tillgängliga miljöer `Get-AzEnvironment` . Om du vill använda Global Azure väljer du **AzureCloud** -miljön. För Azure Kina 21Vianet väljer du **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Skriptet testar konfigurationen av den virtuella dator som du väljer.

   ![Utdata för lyckad test av Azure-tillägget för SAP][deployment-guide-figure-1300]

Kontrol lera att alla hälso kontroll resultat är **OK**. Om vissa kontroller inte visar **OK** kör du uppdaterings-cmdleten enligt beskrivningen i [Konfigurera Azure-tillägget för SAP][deployment-guide-4.5]. Vänta i 15 minuter och upprepa kontrollerna som beskrivs i [beredskaps kontrollen för Azure-tillägg för SAP][deployment-guide-5.1] och [hälso kontroll för Azure-tillägget för SAP-konfiguration][deployment-guide-5.2]. Om kontrollerna fortfarande indikerar ett problem med vissa eller alla räknare, se [Felsöka Azure-tillägget för SAP][deployment-guide-5.3].

> [!Note]
> Du kan uppleva vissa varningar i fall där du använder hanterade standard Azure-diskar. Varningar visas i stället för testerna som returnerar "OK". Detta är normalt och avsett i händelse av disk typen. Se även [Felsöka Azure-tillägget för SAP][deployment-guide-5.3]
> 

### <a name="health-check-for-the-new-azure-extension-for-sap-configuration"></a><a name="464ac96d-7d3c-435d-a5ae-3faf3bfef4b3"></a>Hälso kontroll för den nya Azure-tillägget för SAP-konfiguration

> [!NOTE]
> Det finns två versioner av VM-tillägget. I det här kapitlet beskrivs det nya VM-tillägget. Om du har installerat standard tillägget för virtuell dator kan du läsa mer i kapitel [hälso kontroll för Azure-tillägget för SAP-konfiguration][deployment-guide-5.2].

Om några av infrastruktur data inte levereras korrekt enligt beskrivningen i [beredskaps kontrollen för Azure-tillägget för SAP][deployment-guide-5.1-new], kör du `Get-AzVMExtension` cmdleten för att kontrol lera om Azure-tillägget för SAP är installerat. `Test-AzVMAEMExtension`Har ännu inte stöd för det nya tillägget. När cmdleten har stöd för det nya tillägget kommer vi att uppdatera den här artikeln.

1. Kontrol lera att du har installerat den senaste versionen av Azure PowerShell-cmdlet, enligt beskrivningen i [distribuera Azure PowerShell-cmdletar][deployment-guide-4.1].
1. Kör följande PowerShell-cmdlet: Kör cmdleten om du vill ha en lista över tillgängliga miljöer `Get-AzEnvironment` . Om du vill använda Global Azure väljer du **AzureCloud** -miljön. För Azure Kina 21Vianet väljer du **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Cmdleten testar konfigurationen av VM-tillägget för SAP på den virtuella dator som du väljer.

### <a name="troubleshooting-azure-extension-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Felsöka Azure-tillägget för SAP

> [!NOTE]
> Det finns två versioner av VM-tillägget. Det här kapitlet beskriver standard tillägget för virtuell dator. Om du har installerat det nya VM-tillägget går du [till kapitlet felsöka det nya Azure-tillägget för SAP][deployment-guide-5.3-new].

#### <a name="windows-logologo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows-logotyp.][Logo_Windows] Prestanda räknare för Azure visas inte alls

Windows-tjänsten AzureEnhancedMonitoring samlar in prestanda mått i Azure. Om tjänsten inte har installerats på rätt sätt eller om den inte körs på den virtuella datorn kan inga prestanda mått samlas in.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Installations katalogen för Azure-tillägget för SAP är tom

###### <a name="issue"></a>Problem

Installations katalogen C: \\ packages \\ plugin-program \\ Microsoft. AzureCAT. AzureEnhancedMonitoring. AzureCATExtensionHandler \\ &lt; version>\\ Drop är tom.

###### <a name="solution"></a>Lösning

Tillägget är inte installerat. Ta reda på om detta är ett proxy-problem (som beskrivits tidigare). Du kan behöva starta om datorn eller köra `Set-AzVMAEMExtension` konfigurations skriptet igen.

##### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>Tjänsten för Azure-tillägg för SAP finns inte

###### <a name="issue"></a>Problem

AzureEnhancedMonitoring Windows-tjänsten finns inte.

Azperflib.exe utdata genererar ett fel:

![Körning av azperflib.exe anger att tjänsten för Azure-tillägget för SAP inte körs][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Lösning

Om tjänsten inte finns har Azure-tillägget för SAP inte installerats på rätt sätt. Distribuera tillägget igen med hjälp av stegen som beskrivs i distributions scenariot i [distributions scenarier för virtuella datorer för SAP i Azure][deployment-guide-3].

När du har distribuerat tillägget, efter en timme, kontrollerar du igen om Azures prestanda räknare finns i den virtuella Azure-datorn.

##### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>Det finns redan en tjänst för Azure-tillägget för SAP, men det går inte att starta

###### <a name="issue"></a>Problem

AzureEnhancedMonitoring Windows-tjänsten finns och är aktive rad, men det går inte att starta. Mer information finns i händelse loggen för programmet.

###### <a name="solution"></a>Lösning

Konfigurationen är felaktig. Starta om Azure-tillägget för SAP på den virtuella datorn enligt beskrivningen i [Konfigurera Azure-tillägget för SAP][deployment-guide-4.5].

#### <a name="windows-logologo_windows-some-azure-performance-counters-are-missing"></a>![Windows-logotyp.][Logo_Windows] Vissa prestanda räknare för Azure saknas

Windows-tjänsten AzureEnhancedMonitoring samlar in prestanda mått i Azure. Tjänsten hämtar data från flera källor. Vissa konfigurations data samlas in lokalt och vissa prestanda mått läses från Azure-diagnostik. Lagrings räknare används för att logga in på lagrings prenumerations nivån.

Om fel sökning med hjälp av SAP NOTE [1999351] inte löser problemet kör du `Set-AzVMAEMExtension` konfigurations skriptet igen. Du kan behöva vänta en timme eftersom Storage Analytics-eller Diagnostics-räknare kanske inte skapas direkt efter att de har Aktiver ATS. Om problemet kvarstår öppnar du ett SAP-kund support meddelande på komponenten BC-OP-NT-AZR för Windows eller BC-OP-LNX-AZR för en virtuell Linux-dator.

#### <a name="linux-logologo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux-logotyp.][Logo_Linux] Prestanda räknare för Azure visas inte alls

Prestanda mått i Azure samlas in av en daemon. Om daemon inte körs kan inga prestanda mått samlas in.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Installations katalogen för Azure-tillägget för SAP är tom

###### <a name="issue"></a>Problem

Katalogen \\ var \\ lib- \\ waagent saknar \\ under katalog för Azure-tillägget för SAP.

###### <a name="solution"></a>Lösning

Tillägget är inte installerat. Ta reda på om detta är ett proxy-problem (som beskrivits tidigare). Du kan behöva starta om datorn och/eller köra `Set-AzVMAEMExtension` konfigurations skriptet igen.

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>Körningen av Set-AzVMAEMExtension och Test-AzVMAEMExtension visar varnings meddelanden som talar om att standard Managed Disks inte stöds

###### <a name="issue"></a>Problem

När du kör Set-AzVMAEMExtension eller Test-AzVMAEMExtension meddelanden som visas:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Om du kör azperfli.exe som beskrivits tidigare kan du få ett resultat som anger ett icke-felfritt tillstånd. 

###### <a name="solution"></a>Lösning

Meddelandena orsakas av att standard Managed Disks inte levererar de API: er som används av SAP-tillägget för SAP för att kontrol lera statistik för standard Azure Storages kontona. Detta är inte en angelägenhets risk. Anledningen till att samla in data för standard Disklagring-konton gjorde begränsning av indata och utdata som har inträffat ofta. De hanterade diskarna förhindrar sådan begränsning genom att begränsa antalet diskar i ett lagrings konto. Därför är det inte viktigt att inte ha denna typ av data.


#### <a name="linux-logologo_linux-some-azure-performance-counters-are-missing"></a>![Linux-logotyp.][Logo_Linux] Vissa prestanda räknare för Azure saknas

Prestanda mått i Azure samlas in av en daemon, som hämtar data från flera olika källor. Vissa konfigurations data samlas in lokalt och vissa prestanda mått läses från Azure-diagnostik. Lagrings räknare kommer från loggarna i din lagrings prenumeration.

En fullständig och aktuell lista över kända problem finns i SAP anmärkning [1999351], som innehåller ytterligare felsöknings information för Azure-tillägget för SAP.

Om fel sökning med hjälp av SAP NOTE [1999351] inte löser problemet kör du `Set-AzVMAEMExtension` konfigurations skriptet på det sätt som beskrivs i [Konfigurera Azure-tillägget för SAP][deployment-guide-4.5]. Du kan behöva vänta en timme eftersom lagrings analys-eller diagnostik-räknare kanske inte skapas direkt efter att de har Aktiver ATS. Om problemet kvarstår öppnar du ett SAP-kund support meddelande på komponenten BC-OP-NT-AZR för Windows eller BC-OP-LNX-AZR för en virtuell Linux-dator.

### <a name="troubleshooting-the-new-azure-extension-for-sap"></a><a name="b7afb8ef-a64c-495d-bb37-2af96688c530"></a>Felsöka det nya Azure-tillägget för SAP

> [!NOTE]
> Det finns två versioner av VM-tillägget. I det här kapitlet beskrivs det nya VM-tillägget. Om du har installerat standard tillägget för virtuell dator kan du läsa mer i avsnittet [Felsöka Azure-tillägget för SAP][deployment-guide-5.3].

#### <a name="windows-logologo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows-logotyp.][Logo_Windows] Prestanda räknare för Azure visas inte alls

AzureEnhancedMonitoring-processen samlar in prestanda mått i Azure. Om processen inte körs på den virtuella datorn kan inga prestanda mått samlas in.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Installations katalogen för Azure-tillägget för SAP är tom

###### <a name="issue"></a>Problem

Installations katalogen C: \\ paket plugin-program \\ \\ Microsoft. AzureCAT. AzureEnhancedMonitoring. MonitorX64Windows \\ &lt; version> är tom.

###### <a name="solution"></a>Lösning

Tillägget är inte installerat. Ta reda på om detta är ett proxy-problem (som beskrivits tidigare). Du kan behöva starta om datorn eller installera VM-tillägget igen.

#### <a name="windows-logologo_windows-some-azure-performance-counters-are-missing"></a>![Windows-logotyp.][Logo_Windows] Vissa prestanda räknare för Azure saknas

AzureEnhancedMonitoring Windows-processen samlar in prestanda mått i Azure. Processen hämtar data från flera källor. Vissa konfigurations data samlas in lokalt och vissa prestanda mått läses från Azure Monitor.

Om fel sökning med hjälp av SAP NOTE [1999351] inte löser problemet öppnar du ett SAP kund support meddelande på komponenten BC-op-NT-AZR för Windows eller BC-op-LNX-AZR för en virtuell Linux-dator. Anslut logg filen C: \\ packages plugin-program \\ \\ Microsoft. AzureCAT. AzureEnhancedMonitoring. MonitorX64Windows \\ &lt; version>\\logapp.txt till incidenten.

#### <a name="linux-logologo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux-logotyp.][Logo_Linux] Prestanda räknare för Azure visas inte alls

Prestanda mått i Azure samlas in av en daemon. Om daemon inte körs kan inga prestanda mått samlas in.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Installations katalogen för Azure-tillägget för SAP är tom

###### <a name="issue"></a>Problem

Katalogen \\ var \\ lib- \\ waagent saknar \\ under katalog för Azure-tillägget för SAP.

###### <a name="solution"></a>Lösning

Tillägget är inte installerat. Ta reda på om detta är ett proxy-problem (som beskrivits tidigare). Du kan behöva starta om datorn och/eller installera VM-tillägget igen.

#### <a name="linux-logologo_linux-some-azure-performance-counters-are-missing"></a>![Linux-logotyp.][Logo_Linux] Vissa prestanda räknare för Azure saknas

Prestanda mått i Azure samlas in av en daemon, som hämtar data från flera olika källor. Vissa konfigurations data samlas in lokalt och vissa prestanda mått läses från Azure Monitor.

En fullständig och aktuell lista över kända problem finns i SAP anmärkning [1999351], som innehåller ytterligare felsöknings information för Azure-tillägget för SAP.

Om fel sökning med SAP NOTE [1999351] inte löser problemet, installerar du tillägget igen enligt beskrivningen i [Konfigurera Azure-tillägget för SAP][deployment-guide-4.5]. Om problemet kvarstår öppnar du ett SAP-kund support meddelande på komponenten BC-OP-NT-AZR för Windows eller BC-OP-LNX-AZR för en virtuell Linux-dator. Anslut logg filens/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux- &lt; version>/logapp.txt till incidenten.

## <a name="azure-extension-error-codes"></a>Fel koder för Azure-tillägg

| Fel-ID | Felbeskrivning | Lösning |
|---|---|---|
| <a name="cfg_018"></a>cfg/018 | App-konfigurationen saknas. | [Kör installations skript][deployment-guide-run-the-script] |
| <a name="cfg_019"></a>cfg/019 | Inget distributions-ID i appens konfiguration. | [kontakta supporten][deployment-guide-contact-support] |
| <a name="cfg_020"></a>cfg/020 | Ingen RoleInstanceId i appens konfiguration. | [kontakta supporten][deployment-guide-contact-support] |
| <a name="cfg_022"></a>cfg/022 | Ingen RoleInstanceId i appens konfiguration. | [kontakta supporten][deployment-guide-contact-support] |
| <a name="cfg_031"></a>cfg/031 | Det går inte att läsa Azure-konfigurationen. | [kontakta supporten][deployment-guide-contact-support] |
| <a name="cfg_021"></a>cfg/021 | Konfigurations filen för appen saknas. | [Kör installations skript][deployment-guide-run-the-script] |
| <a name="cfg_015"></a>cfg/015 | Ingen VM-storlek i appens konfiguration. | [Kör installations skript][deployment-guide-run-the-script] |
| <a name="cfg_016"></a>cfg/016 | GlobalMemoryStatusEx-räknaren misslyckades. | [kontakta supporten][deployment-guide-contact-support] |
| <a name="cfg_023"></a>cfg/023 | MaxHwFrequency-räknaren misslyckades. | [kontakta supporten][deployment-guide-contact-support] |
| <a name="cfg_024"></a>cfg/024 | NIC-räknare misslyckades. | [kontakta supporten][deployment-guide-contact-support] |
| <a name="cfg_025"></a>cfg/025 | Disk mappnings räknaren misslyckades. | [kontakta supporten][deployment-guide-contact-support] |
| <a name="cfg_026"></a>cfg/026 | Processor namns räknaren misslyckades. | [kontakta supporten][deployment-guide-contact-support] |
| <a name="cfg_027"></a>cfg/027 | Disk mappnings räknaren misslyckades. | [kontakta supporten][deployment-guide-contact-support] |
| <a name="cfg_038"></a>cfg/038 | Måttet disk Type saknas i tilläggets konfigurations fil config.xml. "Disk Type" tillsammans med några andra räknare infördes i v 2.2.0.68 12/16/2015. Om du har distribuerat tillägget före 12/16/2015 används den gamla konfigurations filen. Azure Extension Framework uppgraderar automatiskt tillägget till en nyare version, men config.xml förblir oförändrad. Uppdatera konfigurationen genom att ladda ned och köra det senaste installations skriptet för PowerShell. | [Kör installations skript][deployment-guide-run-the-script] |
| <a name="cfg_039"></a>cfg/039 | Ingen diskcachelagring. | [Kör installations skript][deployment-guide-run-the-script] |
| <a name="cfg_036"></a>cfg/036 | Inget SLA-genomflöde för disk. | [Kör installations skript][deployment-guide-run-the-script] |
| <a name="cfg_037"></a>cfg/037 | Ingen disk-SLA IOPS. | [Kör installations skript][deployment-guide-run-the-script] |
| <a name="cfg_028"></a>cfg/028 | Disk mappnings räknaren misslyckades. | [kontakta supporten][deployment-guide-contact-support] |
| <a name="cfg_029"></a>cfg/029 | Senaste ändrings räknaren för maskin vara misslyckades. | [kontakta supporten][deployment-guide-contact-support] |
| <a name="cfg_030"></a>cfg/030 | NIC-räknare misslyckades | [kontakta supporten][deployment-guide-contact-support] |
| <a name="cfg_017"></a>cfg/017 | På grund av Sysprep på den virtuella datorn har ditt Windows-SID ändrats. | [distribuera om efter Sysprep][deployment-guide-redeploy-after-sysprep] |
| <a name="str_007"></a>Str/007 | Det gick inte att komma åt lagrings analys. <br /><br />Eftersom populationen av lagrings analys data på en nyligen skapad virtuell dator kan behöva upp till en halvtimme, kan felet försvinna efter en stund. Kör installations skriptet igen om felet fortfarande visas. | [Kör installations skript][deployment-guide-run-the-script] |
| <a name="str_010"></a>Str/010 | Inga Lagringsanalys räknare. | [Kör installations skript][deployment-guide-run-the-script] |
| <a name="str_009"></a>Str/009 | Lagringsanalys misslyckades. | [Kör installations skript][deployment-guide-run-the-script] |
| <a name="wad_004"></a>wad/004 | Felaktig WAD-konfiguration. | [Kör installations skript][deployment-guide-run-the-script] |
| <a name="wad_002"></a>wad/002 | Oväntat WAD-format. | [kontakta supporten][deployment-guide-contact-support] |
| <a name="wad_001"></a>wad/001 | Inga WAD-räknare hittades. | [Kör installations skript][deployment-guide-run-the-script] |
| <a name="wad_040"></a>wad/040 | Inaktuella WAD-räknare hittades. | [kontakta supporten][deployment-guide-contact-support] |
| <a name="wad_003"></a>wad/003 | Det går inte att läsa WAD-tabellen. Det finns ingen anslutning till WAD-tabellen. Det kan finnas flera orsaker till detta:<br /><br /> 1) inaktuell konfiguration <br />2) ingen nätverks anslutning till Azure <br />3) problem med installationen av WAD | [Kör installations skript][deployment-guide-run-the-script]<br />[åtgärda Internet anslutning][deployment-guide-fix-internet-connection]<br />[kontakta supporten][deployment-guide-contact-support] |
| <a name="prf_011"></a>PRF/011 | Prestandan för perfmon NIC misslyckades. | [kontakta supporten][deployment-guide-contact-support] |
| <a name="prf_012"></a>PRF/012 | Det gick inte att utföra perfmon-disk mått. | [kontakta supporten][deployment-guide-contact-support] |
| <a name="prf_013"></a>PRF/013 | Vissa prefmon-mått misslyckades. | [kontakta supporten][deployment-guide-contact-support] |
| <a name="prf_014"></a>PRF/014 | Det gick inte att skapa en räknare i perfmon. | [kontakta supporten][deployment-guide-contact-support] |
| <a name="cfg_035"></a>cfg/035 | Inga mått leverantörer har kon figurer ATS. | [kontakta supporten][deployment-guide-contact-support] |
| <a name="str_006"></a>Str/006 | Felaktig Lagringsanalys konfiguration. | [Kör installations skript][deployment-guide-run-the-script] |
| <a name="str_032"></a>Str/032 | Lagringsanalys måtten misslyckades. | [Kör installations skript][deployment-guide-run-the-script] |
| <a name="cfg_033"></a>cfg/033 | En av mått leverantörerna misslyckades. | [Kör installations skript][deployment-guide-run-the-script] |
| <a name="str_034"></a>Str/034 | Det gick inte att koppla providern. | [kontakta supporten][deployment-guide-contact-support] |

### <a name="detailed-guidelines-on-solutions-provided"></a>Detaljerade rikt linjer för lösningar som tillhandahålls

#### <a name="run-the-setup-script"></a><a name="0d2847ad-865d-4a4c-a405-f9b7baaa00c7"></a>Kör installations skriptet

Följ stegen i kapitlet [Konfigurera Azure-tillägget för SAP][deployment-guide-4.5] i den här guiden för att installera tillägget igen. Observera att vissa räknare kan behöva upp till 30 minuter för etablering.

Om felen inte försvinner [kontaktar du supporten][deployment-guide-contact-support].

#### <a name="contact-support"></a><a name="3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2"></a>Kontakta supporten

Ett oväntat fel eller så finns det ingen känd lösning. Samla in filen AzureEnhancedMonitoring_service. log som finns i mappen C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \Drop (Windows) eller/var/log/Azure/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux (Linux) och kontakta SAP-supporten om du vill ha mer hjälp.

#### <a name="redeploy-after-sysprep"></a><a name="2cd61f22-187d-42ed-bb8c-def0c983d756"></a>Distribuera om efter Sysprep

Om du planerar att bygga en generaliserad Sysprep OS-avbildning (som kan innehålla SAP-program) rekommenderar vi att den här avbildningen inte inkluderar Azure-tillägget för SAP. Du bör installera Azure-tillägget för SAP när den nya instansen av den generaliserade OS-avbildningen har distribuerats.

Men om din generaliserade och Sysprep OS-avbildning redan innehåller Azure-tillägget för SAP kan du använda följande lösning för att konfigurera om tillägget på den nyligen distribuerade virtuella dator instansen:

* Ta bort innehållet i följande mappar på den nyligen distribuerade virtuella dator instansen:  
  C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \RuntimeSettings C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \Status

* Följ stegen i kapitlet [Konfigurera Azure-tillägget för SAP][deployment-guide-4.5] i den här guiden för att installera tillägget igen.

#### <a name="fix-internet-connection"></a><a name="e92bc57d-80d9-4a2b-a2f4-16713a22ad89"></a>Åtgärda Internet anslutning

Den Microsoft Azure virtuella datorn som kör Azure-tillägget för SAP kräver åtkomst till Internet. Om den virtuella Azure-datorn tillhör en Azure-Virtual Network eller en lokal domän kontrollerar du att de relevanta proxyinställningarna är inställda. Dessa inställningar måste också vara giltiga för att LocalSystem-kontot ska kunna få åtkomst till Internet. Följ kapitlet [Konfigurera proxyn][deployment-guide-configure-proxy] i den här hand boken.

Om du behöver ange en statisk IP-adress för din virtuella Azure-dator ska du dessutom inte ange den manuellt i den virtuella Azure-datorn, men ange den med [Azure PowerShell](../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md) [Azure CLI](../../../virtual-network/virtual-networks-static-private-ip-arm-cli.md) [Azure Portal](../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). Den statiska IP-adressen sprids via Azure DHCP-tjänsten.

Det finns inte stöd för att ange en statisk IP-adress manuellt i den virtuella Azure-datorn, och det kan leda till problem med Azure-tillägget för SAP.
