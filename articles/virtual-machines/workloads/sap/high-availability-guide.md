---
title: Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer
description: Guide med hög tillgänglighet för SAP NetWeaver på Azure Virtual Machines
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/24/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6aedaf829df941a225cd0684318f28bb06ba89d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674607"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms"></a>Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer

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
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:https://docs.microsoft.com/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:dbms_guide_general.md
[dbms-guide-2.1]:dbms_guide_general.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:dbms_guide_general.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:dbms_guide_general.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:dbms_guide_general.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:dbms_guide_general.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:dbms_guide_general.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms_guide_general.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:dbms_guide_general.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:dbms_guide_general.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:dbms_guide_general.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:dbms_guide_general.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:dbms_guide_general.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms_guide_general.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:dbms_guide_general.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../../../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../../../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:../../virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:../../virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:../../virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:../../virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:high-availability-guide.md
[sap-ha-guide-1]:high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c
[sap-ha-guide-2]:high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-3]:high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1
[sap-ha-guide-3.1]:high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686
[sap-ha-guide-3.2]:high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860
[sap-ha-guide-4]:high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-4.1]:high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2
[sap-ha-guide-5]:high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275
[sap-ha-guide-5.1]:high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592
[sap-ha-guide-5.2]:high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd
[sap-ha-guide-6]:high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a
[sap-ha-guide-6.1]:high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341
[sap-ha-guide-6.2]:high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb
[sap-ha-guide-7]:high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa
[sap-ha-guide-7.1]:high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e
[sap-ha-guide-7.2]:high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db
[sap-ha-guide-7.2.1]:high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51
[sap-ha-guide-7.3]:high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027
[sap-ha-guide-7.4]:high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816
[sap-ha-guide-8]:high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.2]:high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310
[sap-ha-guide-8.3]:high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a
[sap-ha-guide-8.4]:high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e
[sap-ha-guide-8.5]:high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f
[sap-ha-guide-8.6]:high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30
[sap-ha-guide-8.7]:high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e
[sap-ha-guide-8.8]:high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.10]:high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.2]:high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca
[sap-ha-guide-8.12.2.1]:high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855
[sap-ha-guide-8.12.2.2]:high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d
[sap-ha-guide-8.12.3]:high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-ha-guide-9.1.2]:high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0
[sap-ha-guide-9.1.3]:high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-ha-guide-9.1.4]:high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-ha-guide-9.1.5]:high-availability-guide.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-ha-guide-9.2]:high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7
[sap-ha-guide-9.3]:high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748
[sap-ha-guide-9.4]:high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-ha-guide-9.5]:high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5
[sap-ha-guide-9.6]:high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772
[sap-ha-guide-10]:high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9
[sap-ha-guide-10.1]:high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445
[sap-ha-guide-10.2]:high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916
[sap-ha-guide-10.3]:high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5

[sap-ha-multi-sid-guide]:high-availability-multi-sid.md (SAP multi-SID-konfiguration med hög tillgänglighet)


[sap-ha-guide-figure-1000]:media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam (Produkt tillgänglighets mat ris för SAP)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
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
[virtual-machines-windows-attach-disk-portal]:../../virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../azure-resource-manager/management/overview.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-capture]:../../linux/capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:../../virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../virtual-machines-windows-sizes.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
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
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md


Azure Virtual Machines är lösningen för organisationer som behöver beräknings-, lagrings-och nätverks resurser, i minimal tid och utan längd för att lösa längder. Du kan använda Azure Virtual Machines för att distribuera klassiska program som SAP NetWeaver-baserade ABAP, Java och en ABAP + Java-stack. Utöka tillförlitligheten och tillgängligheten utan ytterligare lokala resurser. Azure Virtual Machines stöder anslutning mellan platser, så att du kan integrera Azure Virtual Machines i organisationens lokala domäner, privata moln och SAP-system liggande.

I den här artikeln beskriver vi de steg som du kan vidta för att distribuera SAP-system med hög tillgänglighet i Azure med hjälp av Azure Resource Manager distributions modell. Vi vägleder dig genom dessa större uppgifter:

* Hitta rätt SAP-anteckningar och installations guider som anges i avsnittet [resurser][sap-ha-guide-2] . Den här artikeln kompletterar SAP-installations dokumentation och SAP-anteckningar, som är de viktigaste resurserna som kan hjälpa dig att installera och distribuera SAP-program på vissa plattformar.
* Lär dig skillnaderna mellan Azure Resource Manager distributions modell och den klassiska Azure-distributions modellen.
* Lär dig mer om kompatibilitetsläge för Windows Server-redundanskluster, så du kan välja den modell som passar din Azure-distribution.
* Lär dig mer om den delade lagringen av Windows Server-redundanskluster i Azure-tjänster.
* Lär dig hur du skyddar en enskild fel hanterings komponent som Advanced Business Application Programming (ABAP) SAP Central Services (ASCS)/SAP Central Services (SCS) och databas hanterings system (DBMS) och redundanta komponenter som SAP Application Server i Azure.
* Följ ett steg-för-steg-exempel på en installation och konfiguration av ett SAP-system med hög tillgänglighet i ett kluster med Windows Server-redundanskluster i Azure med hjälp av Azure Resource Manager.
* Lär dig mer om ytterligare steg som krävs för att använda Windows Server-redundanskluster i Azure, men som inte behövs i en lokal distribution.

För att förenkla distribution och konfiguration i den här artikeln använder vi SAP-mallarna i tre nivåer med hög tillgänglighet. Mallarna automatiserar distributionen av hela den infrastruktur du behöver för ett SAP-system med hög tillgänglighet. Infrastrukturen stöder också SAPS-storlek (SAP Application Performance standard) i SAP-systemet.

## <a name="prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Krav
Innan du börjar ska du kontrol lera att du uppfyller de krav som beskrivs i följande avsnitt. Se också till att kontrol lera alla resurser som anges i avsnittet [resurser][sap-ha-guide-2] .

I den här artikeln använder vi Azure Resource Manager mallar för [SAP-NetWeaver på tre nivåer](https://github.com/Azure/azure-quickstart-templates/). En användbar översikt över mallar finns i [SAP Azure Resource Manager-mallar](/archive/blogs/saponsqlserver/azure-quickstart-templates-for-sap).

## <a name="resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Resurser
De här artiklarna avser SAP-distributioner i Azure:

* [Azure Virtual Machines planera och implementera SAP-NetWeaver][planning-guide]
* [Azure Virtual Machines-distribution för SAP NetWeaver][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP NetWeaver][dbms-guide]
* [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver (den här guiden)][sap-ha-guide]

> [!NOTE]
> När det är möjligt ger vi dig en länk till den refererande SAP-installationsprogrammet (se installations guiderna för [SAP][sap-installation-guides]). För krav och information om installations processen är det en bra idé att läsa igenom installations guiderna för SAP-NetWeaver noggrant. Den här artikeln beskriver endast vissa uppgifter för SAP NetWeaver-baserade system som du kan använda med Azure Virtual Machines.
>
>

Dessa SAP-anteckningar är relaterade till ämnet i SAP i Azure:

| Antecknings nummer | Rubrik |
| --- | --- |
| [1928533] |SAP-program på Azure: produkter och storlek som stöds |
| [2015553] |SAP på Microsoft Azure: stöd för krav |
| [1999351] |Förbättrad Azure-övervakning för SAP |
| [2178632] |Nyckel övervaknings mått för SAP på Microsoft Azure |
| [1999351] |Virtualisering i Windows: förbättrad övervakning |
| [2243692] |Användning av Azure Premium SSD Storage för SAP DBMS-instans |

Lär dig mer om [begränsningarna för Azure-prenumerationer][azure-resource-manager/management/azure-subscription-service-limits-subscription], inklusive allmänna standard begränsningar och högsta begränsningar.

## <a name="high-availability-sap-with-azure-resource-manager-vs-the-azure-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Hög tillgänglighet SAP med Azure Resource Manager jämfört med den klassiska Azure-distributions modellen
De klassiska distributions modellerna för Azure Resource Manager och Azure är olika i följande områden:

- Resursgrupper
- Azures interna belastnings Utjämnings beroende på Azure-resurs gruppen
- Stöd för SAP multi-SID-scenarier

### <a name="resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Resurs grupper
I Azure Resource Manager kan du använda resurs grupper för att hantera alla program resurser i din Azure-prenumeration. Alla resurser har samma livs cykel som en integrerad metod i en resurs grupp. Alla resurser skapas till exempel samtidigt och tas sedan bort samtidigt. Läs mer om [resursgrupper](../../../azure-resource-manager/management/overview.md#resource-groups).

### <a name="azure-internal-load-balancer-dependency-on-the-azure-resource-group"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Azures interna belastnings Utjämnings beroende på Azure-resurs gruppen

I den klassiska Azure-distributions modellen finns det ett beroende mellan Azures interna belastningsutjämnare (Azure Load Balancer tjänsten) och moln tjänst gruppen. Varje intern belastningsutjämnare behöver en moln tjänst grupp.

I Azure Resource Manager behöver du inte någon Azure-resurs grupp för att använda Azure Load Balancer. Miljön är enklare och mer flexibel.

### <a name="support-for-sap-multi-sid-scenarios"></a>Stöd för SAP multi-SID-scenarier

I Azure Resource Manager kan du installera flera SAP-system identifierare (SID) ASCS/SCS-instanser i ett kluster. Flera SID-instanser är möjliga på grund av stöd för flera IP-adresser för varje intern Azure-belastningsutjämnare.

Om du vill använda den klassiska Azure-distributions modellen följer du procedurerna som beskrivs i [SAP NetWeaver i Azure: klustring SAP ASCS/SCS-instanser med hjälp av Windows Server-redundanskluster i Azure med SIOS DataKeeper](https://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> Vi rekommenderar starkt att du använder Azure Resource Manager distributions modell för dina SAP-installationer. Den erbjuder många fördelar som inte är tillgängliga i den klassiska distributions modellen. Lär dig mer om [distributions modeller][virtual-machines-azure-resource-manager-architecture-benefits-arm]i Azure.   
>
>

## <a name="windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Kluster för växling vid fel i Windows Server
Windows Server-redundanskluster är grunden för en hög tillgänglig SAP-ASCS/SCS-installation och DBMS i Windows.

Ett redundanskluster är en grupp med 1 + n oberoende servrar (noder) som arbetar tillsammans för att öka tillgängligheten för program och tjänster. Om ett nodfel inträffar beräknar Windows Server-redundanskluster antalet fel som kan uppstå under underhållet av ett felfritt kluster för att tillhandahålla program och tjänster. Du kan välja mellan olika kvorumresurser för att nå redundanskluster.

### <a name="quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Lägen för kvorum
Du kan välja mellan fyra olika lägen när du använder Windows Server-redundanskluster:

* **Node majoritet**. Varje nod i klustret kan rösta. Klustret fungerar bara med majoritets röster, det vill säga med fler än hälften av rösterna. Vi rekommenderar det här alternativet för kluster som har ett ojämnt antal noder. Tre noder i ett kluster med sju noder kan till exempel inte fungera, och klustret kommer fortfarande att uppnå en majoritet och fortsätter att köras.  
* **Node och disk majoritet**. Varje nod och en angiven disk (ett disk vittne) i kluster lagringen kan rösta när de är tillgängliga och under kommunikation. Klustret fungerar bara med en majoritet av rösterna, det vill säga med fler än hälften av rösterna. Det här läget är meningsfullt i en kluster miljö med ett jämnt antal noder. Om hälften av noderna och disken är online förblir klustret i felfritt tillstånd.
* **Node-och fil resurs majoritet**. Varje nod plus en angiven fil resurs (ett fil resurs vittne) som administratören skapar kan rösta, oavsett om noderna och fil resursen är tillgängliga och vid kommunikation. Klustret fungerar bara med en majoritet av rösterna, det vill säga med fler än hälften av rösterna. Det här läget är meningsfullt i en kluster miljö med ett jämnt antal noder. Det liknar läget Node och disk majoritet, men använder en vittnes fil resurs i stället för en vittnes disk. Det här läget är enkelt att implementera, men om själva fil resursen inte är hög tillgänglig kan det bli en enskild felpunkt.
* **Ingen majoritet: endast disk**. Klustret har ett kvorum om en nod är tillgänglig och kommunicerar med en speciell disk i kluster lagringen. Endast noder som också är i kommunikation med den disken kan ansluta till klustret. Vi rekommenderar att du inte använder det här läget.

## <a name="windows-server-failover-clustering-on-premises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Windows Server-redundanskluster lokalt
Bild 1 visar ett kluster med två noder. Om nätverks anslutningen mellan noderna Miss lyckas och båda noderna är igång, avgör en kvorumdisk eller fil resursen vilken nod som fortsätter att tillhandahålla klustrets program och tjänster. Noden som har åtkomst till kvorumdisken eller fil resursen är den nod som säkerställer att tjänsterna fortsätter.

Eftersom det här exemplet använder ett kluster med två noder använder vi kvorumkonfigurationen nod-och fil resurs majoritet. Node och disk majoritet är också ett giltigt alternativ. I en produktions miljö rekommenderar vi att du använder en kvorumdisk. Du kan använda Nätverks-och lagrings system teknik för att göra den hög tillgänglig.

![Bild 1: exempel på en konfiguration av Windows Server-redundanskluster för SAP ASCS/SCS i Azure][sap-ha-guide-figure-1000]

_**Bild 1:** Exempel på en Windows Server-konfiguration för redundanskluster för SAP ASCS/SCS i Azure_

### <a name="shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Delad lagring
Bild 1 visar också ett delat lagrings kluster med två noder. I ett lokalt delat lagrings kluster identifierar alla noder i klustret delad lagring. En låsnings funktion skyddar data från skador. Alla noder kan identifiera om en annan nod Miss lyckas. Om en nod kraschar tar den återstående noden över ägandet av lagrings resurserna och ser till att tjänsterna är tillgängliga.

> [!NOTE]
> Du behöver inte delade diskar för hög tillgänglighet med vissa DBMS-program, till exempel med SQL Server. SQL Server Always on replikerar DBMS-data och loggfiler från den lokala disken på en klusternod till den lokala disken på en annan klusternod. I så fall behöver konfigurationen för Windows-klustret ingen delad disk.
>
>

### <a name="networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Nätverk och namn matchning
Klient datorerna når klustret över en virtuell IP-adress och ett virtuellt värdnamn som DNS-servern tillhandahåller. Lokala noder och DNS-servern kan hantera flera IP-adresser.

I en typisk konfiguration använder du två eller flera nätverks anslutningar:

* En dedikerad anslutning till lagrings platsen
* En kluster intern nätverks anslutning för pulsslaget
* Ett offentligt nätverk som klienter använder för att ansluta till klustret

## <a name="windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Kluster för växling vid fel i Windows Server i Azure
Jämfört med distributioner av Bare Metal eller privata moln kräver Azure Virtual Machines ytterligare steg för att konfigurera Windows Server-redundanskluster. När du skapar en delad kluster disk måste du ange flera IP-adresser och virtuella värdnamn för SAP ASCS/SCS-instansen.

I den här artikeln diskuterar vi viktiga begrepp och de ytterligare steg som krävs för att bygga ett SAP-kluster med hög tillgänglighet i Azure. Vi visar dig hur du konfigurerar SIOS-DataKeeper för verktyget, och hur du konfigurerar den interna Azure-belastningsutjämnaren. Du kan använda dessa verktyg för att skapa ett Windows-redundanskluster med ett fil resurs vittne i Azure.

![Bild 2: konfiguration av redundanskluster för Windows Server i Azure utan en delad disk][sap-ha-guide-figure-1001]

_**Bild 2:** Konfiguration av redundanskluster för Windows Server i Azure utan en delad disk_

### <a name="shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Delad disk i Azure med SIOS DataKeeper
Du behöver klusterdelad lagring för en hög tillgänglig SAP ASCS/SCS-instans. Från och med september 2016 erbjuder Azure inte delad lagring som du kan använda för att skapa ett delat lagrings kluster. Du kan använda program vara från tredje part SIOS DataKeeper Cluster Edition för att skapa en speglad lagring som simulerar delat kluster lagring. SIOS-lösningen tillhandahåller synkron data replikering i real tid. Så här kan du skapa en delad disk resurs för ett kluster:

1. Anslut ytterligare en virtuell Azure-hårddisk (VHD) till var och en av de virtuella datorerna (VM) i en Windows-kluster konfiguration.
2. Kör SIOS DataKeeper Cluster Edition på båda noderna för virtuella datorer.
3. Konfigurera SIOS DataKeeper Cluster Edition så att den speglar innehållet i den ytterligare VHD-anslutna volymen från den virtuella käll datorn till den ytterligare VHD-anslutna volymen på den virtuella mål datorn. SIOS DataKeeper sammanfattar käll-och mål volymerna och visar dem sedan Windows Server-redundanskluster som en delad disk.

Få mer information om [SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/).

![Bild 3: konfiguration av Windows Server-redundanskluster i Azure med SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Bild 3:** Konfiguration av redundanskluster för Windows Server i Azure med SIOS DataKeeper_

> [!NOTE]
> Du behöver inte delade diskar för hög tillgänglighet med vissa DBMS-produkter, t. ex. SQL Server. SQL Server Always on replikerar DBMS-data och loggfiler från den lokala disken på en klusternod till den lokala disken på en annan klusternod. I det här fallet behöver konfigurationen för Windows-klustret ingen delad disk.
>
>

### <a name="name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Namn matchning i Azure
Azure Cloud Platform erbjuder inte alternativet att konfigurera virtuella IP-adresser, till exempel flytande IP-adresser. Du behöver en alternativ lösning för att skapa en virtuell IP-adress för att komma åt kluster resursen i molnet.
Azure har en intern belastningsutjämnare i Azure Load Balancers tjänsten. Med den interna belastningsutjämnaren når klienterna klustret via klustrets virtuella IP-adress.
Du måste distribuera den interna belastningsutjämnaren i resurs gruppen som innehåller klusternoderna. Konfigurera sedan alla nödvändiga regler för port vidarebefordran med avsöknings portarna för den interna belastningsutjämnaren.
Klienterna kan ansluta via det virtuella värd namnet. DNS-servern matchar klustrets IP-adress och den interna belastningsutjämnaren hanterar port vidarebefordran till klustrets aktiva nod.

## <a name="sap-netweaver-high-availability-in-azure-infrastructure-as-a-service-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> SAP NetWeaver hög tillgänglighet i Azure Infrastructure-as-a-Service (IaaS)
För att uppnå hög tillgänglighet för SAP-program, till exempel för SAP-programkomponenter, måste du skydda följande komponenter:

* SAP Application Server-instans
* SAP ASCS/SCS-instans
* DBMS-Server

Mer information om hur du skyddar SAP-komponenter i scenarier med hög tillgänglighet finns i [Azure Virtual Machines planera och implementera för SAP NetWeaver](planning-guide.md).

### <a name="high-availability-sap-application-server"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> SAP-Programserver med hög tillgänglighet
Du behöver vanligt vis ingen specifik lösning för hög tillgänglighet för SAP-programservern och dialog instanser. Du uppnår hög tillgänglighet genom redundans och du konfigurerar flera dialog instanser i olika instanser av Azure Virtual Machines. Du bör ha minst två SAP-programinstanser installerade i två instanser av Azure Virtual Machines.

![Bild 4: SAP-Programserver med hög tillgänglighet][sap-ha-guide-figure-2000]

_**Bild 4:** SAP-Programserver med hög tillgänglighet_

Du måste placera alla virtuella datorer som är värdar för SAP Application Server-instanser i samma Azure-tillgänglighets uppsättning. En Azure-tillgänglighets uppsättning garanterar att:

* Alla virtuella datorer ingår i samma uppgraderings domän. En uppgraderings domän ser till exempel till att de virtuella datorerna inte uppdateras samtidigt under ett planerat underhålls avbrott.
* Alla virtuella datorer ingår i samma feldomän. En feldomän ser till exempel till att virtuella datorer distribueras så att ingen enskild felpunkt påverkar tillgängligheten för alla virtuella datorer.

Läs mer om hur du [hanterar tillgängligheten för virtuella datorer][virtual-machines-manage-availability].

Eftersom Azure Storage-kontot är en enskild felpunkt är det viktigt att ha minst två Azure Storage-konton, där minst två virtuella datorer distribueras. I en idealisk installation skulle diskarna för varje virtuell dator som kör en instans av en SAP-dialogruta distribueras i ett annat lagrings konto.

### <a name="high-availability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Hög tillgänglighet SAP ASCS/SCS-instans
Bild 5 är ett exempel på en SAP ASCS/SCS-instans med hög tillgänglighet.

![Bild 5: SAP ASCS/SCS-instans med hög tillgänglighet][sap-ha-guide-figure-2001]

_**Figur 5:** Hög tillgänglighet SAP ASCS/SCS-instans_

#### <a name="sap-ascsscs-instance-high-availability-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> SAP ASCS/SCS-instans med hög tillgänglighet med Windows Server-redundanskluster i Azure
Jämfört med distributioner av Bare Metal eller privata moln kräver Azure Virtual Machines ytterligare steg för att konfigurera Windows Server-redundanskluster. Om du vill bygga ett Windows-redundanskluster behöver du en delad kluster disk, flera IP-adresser, flera virtuella värdnamn och en intern Azure-belastningsutjämnare för att klustra en SAP ASCS/SCS-instans. Vi diskuterar detta mer detaljerat längre fram i artikeln.

![Bild 6: Windows Server-redundanskluster för en SAP ASCS/SCS-konfiguration i Azure med hjälp av SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Bild 6:** Windows Server-redundanskluster för en SAP ASCS/SCS-konfiguration i Azure med SIOS DataKeeper_

### <a name="high-availability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>DBMS-instans med hög tillgänglighet
DBMS är också en enda kontakt punkt i ett SAP-system. Du måste skydda den med en lösning med hög tillgänglighet. Bild 7 visar en SQL Server Always on-lösning med hög tillgänglighet i Azure med Windows Server-redundanskluster och den interna Azure-belastningsutjämnaren. SQL Server Always on replikerar DBMS-data och loggfiler med hjälp av sin egen DBMS-replikering. I det här fallet behöver du inte delade klusterdelade diskar, vilket fören klar hela installationen.

![Figur 7: exempel på en SAP-DBMS med hög tillgänglighet, med SQL Server Always on][sap-ha-guide-figure-2003]

_**Figur 7:** Exempel på en SAP-DBMS med hög tillgänglighet, med SQL Server Always on_

Mer information om kluster SQL Server i Azure med hjälp av Azure Resource Manager distributions modell finns i följande artiklar:

* [Konfigurera tillgänglighets gruppen Always on i Azure Virtual Machines manuellt med hjälp av Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Konfigurera en intern Azure-belastningsutjämnare för en tillgänglighets grupp som alltid är tillgänglig i Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="end-to-end-high-availability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Distributions scenarier från slut punkt till slut punkt

### <a name="deployment-scenario-using-architectural-template-1"></a>Distributions scenario med arkitektur mal len 1

Bild 8 visar ett exempel på en arkitektur med hög tillgänglighet för SAP NetWeaver i Azure för **ett** SAP-system. Det här scenariot konfigureras enligt följande:

- Ett dedikerat kluster används för SAP ASCS/SCS-instansen.
- Ett dedikerat kluster används för DBMS-instansen.
- SAP Application Server-instanser distribueras på egna dedikerade virtuella datorer.

![Figur 8: arkitektur mal len SAP med hög tillgänglighet 1, med dedikerat kluster för ASCS/SCS och DBMS][sap-ha-guide-figure-2004]

_**Figur 8:** SAP-mall för hög tillgänglighet med hög tillgänglighet 1, dedikerade kluster för ASCS/SCS och DBMS_

### <a name="deployment-scenario-using-architectural-template-2"></a>Distributions scenario med arkitektur mal len 2

Bild 9 visar ett exempel på en arkitektur med hög tillgänglighet för SAP NetWeaver i Azure för **ett** SAP-system. Det här scenariot konfigureras enligt följande:

- Ett dedikerat kluster används för **både** SAP ASCS/SCS-instansen och DBMS.
- SAP Application Server-instanser distribueras i egna dedikerade virtuella datorer.

![Bild 9: arkitektur mal len SAP med hög tillgänglighet 2, med ett dedikerat kluster för ASCS/SCS och ett dedikerat kluster för DBMS][sap-ha-guide-figure-2005]

_**Bild 9:** SAP-mall för hög tillgänglighet 2, med ett dedikerat kluster för ASCS/SCS och ett dedikerat kluster för DBMS_

### <a name="deployment-scenario-using-architectural-template-3"></a>Distributions scenario med arkitektur mal len 3

Bild 10 visar ett exempel på en arkitektur med hög tillgänglighet för SAP NetWeaver i Azure för **två** SAP-system, med &lt; SID1 &gt; och &lt; SID2 &gt; . Det här scenariot konfigureras enligt följande:

- Ett dedikerat kluster används för **både** SAP ASCS/SCS SID1-instansen *och* SAP ASCS/SCS SID2-instansen (ett kluster).
- Ett dedikerat kluster används för DBMS-SID1 och ett annat dedikerat kluster används för DBMS-SID2 (två kluster).
- SAP Application Server-instanser för SID1 för SAP-systemet har sina egna dedikerade virtuella datorer.
- SAP Application Server-instanser för SID2 för SAP-systemet har sina egna dedikerade virtuella datorer.

![Bild 10: avancerad arkitektur mall för hög tillgänglighet 3, med ett dedikerat kluster för olika ASCS/SCS-instanser][sap-ha-guide-figure-6003]

_**Bild 10:** Avancerad arkitektur mall för hög tillgänglighet 3, med ett dedikerat kluster för olika ASCS/SCS-instanser_

## <a name="prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Förbered infrastrukturen

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Förbered infrastrukturen för arkitektur mal len 1
Azure Resource Manager mallar för SAP bidrar till att förenkla distributionen av nödvändiga resurser.

Mallarna på tre nivåer i Azure Resource Manager också stöd för scenarier med hög tillgänglighet, till exempel i arkitektur mal len 1, som har två kluster. Varje kluster är en enkel fel punkt i SAP för SAP ASCS/SCS och DBMS.

Här kan du få Azure Resource Manager mallar för exempel scenariot som vi beskriver i den här artikeln:

* [Azure Marketplace-avbildning](https://github.com/Azure/azure-quickstart-templates/)  
* [Anpassad avbildning](https://github.com/Azure/azure-quickstart-templates/)

Förbereda infrastrukturen för arkitektur mal len 1:

- I Azure Portal på bladet **parametrar** väljer du **ha** i rutan **SYSTEMAVAILABILITY** .

  ![Bild 11: Ange Azure Resource Manager parametrar för hög tillgänglighet för SAP][sap-ha-guide-figure-3000]

_**Bild 11:** Ange Azure Resource Manager parametrar för hög tillgänglighet för SAP_


  Mallarna skapa:

  * **Virtuella datorer**:
    * Virtuella SAP-program Server-datorer: <*SAPSystemSID*>-di-<*Number*>
    * ASCS/SCS-kluster virtuella datorer: <*SAPSystemSID*>-ASCS-<*Number*>
    * DBMS-kluster: <*SAPSystemSID*>-db-<*Number*>

  * **Nätverkskort för alla virtuella datorer, med tillhör ande IP-adresser**:
    * <*SAPSystemSID*>-NIC-di-<*Number*>
    * <*SAPSystemSID*>-NIC-ascs-<*Number*>
    * <*SAPSystemSID*>-NIC-db-<*Number*>

  * **Azure-lagringskonton**

  * **Tillgänglighets grupper** för:
    * Virtuella SAP-program Server-datorer: <*SAPSystemSID*>-avset-di
    * Virtuella SAP ASCS/SCS-kluster datorer: <*SAPSystemSID*>-avset-ASCS
    * Virtuella DBMS-kluster datorer: <*SAPSystemSID*>-avset-DB

  * **Intern Azure-belastningsutjämnare**:
    * Med alla portar för ASCS/SCS-instansen och IP-adressen <*SAPSystemSID*>-lb-ASCS
    * Med alla portar för SQL Server-DBMS och IP-<*SAPSystemSID*> – lb-DB

  * **Nätverks säkerhets grupp**: <*SAPSystemSID*>-NSG-ASCs-0  
    * Med en öppen extern Remote Desktop Protocol-port (RDP) till <*SAPSystemSID*>-ASCs-0 virtuell dator

> [!NOTE]
> Alla IP-adresser för nätverkskorten och de interna Azure-belastningsutjämnaren är **dynamiska** som standard. Ändra dem till **statiska** IP-adresser. Vi beskriver hur du gör detta senare i artikeln.
>
>

### <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Distribuera virtuella datorer med företags nätverks anslutning (mellan platser) som ska användas i produktion
För produktion av SAP-system distribuerar du virtuella Azure-datorer med företags nätverks anslutning genom att använda Azure plats-till-plats-VPN eller Azure ExpressRoute.

> [!NOTE]
> Du kan använda Azure Virtual Network-instansen. Det virtuella nätverket och under nätet har redan skapats och för beretts.
>
>

1. I Azure Portal väljer du **befintlig** på bladet **parametrar** i rutan **NEWOREXISTINGSUBNET** .
2. I rutan **SUBNETID** lägger du till den fullständiga strängen för din för beredda Azure Network-SUBNETID där du planerar att distribuera dina virtuella Azure-datorer.
3. Om du vill hämta en lista över alla Azure Network-undernät, kör du följande PowerShell-kommando:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   I fältet **ID** visas **SUBNETID**.
4. Om du vill hämta en lista över alla **SUBNETID** -värden kör du följande PowerShell-kommando:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   **SUBNETID** ser ut så här:

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Distribuera endast molnbaserade SAP-instanser för test och demo
Du kan distribuera SAP-systemet med hög tillgänglighet i en distributions modell som endast är molnbaserad. Den här typen av distribution är främst användbar för demo-och test användnings fall. Det passar inte för produktions användnings fall.

- I Azure Portal väljer du **ny** på bladet **parametrar** i rutan **NEWOREXISTINGSUBNET** . Lämna fältet **SUBNETID** tomt.

  SAP Azure Resource Manager-mallen skapar automatiskt det virtuella Azure-nätverket och under nätet.

> [!NOTE]
> Du måste också distribuera minst en dedikerad virtuell dator för Active Directory och DNS i samma Azure Virtual Network-instans. Mallen skapar inte dessa virtuella datorer.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Förbered infrastrukturen för arkitektur mal len 2

Du kan använda den här Azure Resource Manager mallen för SAP för att förenkla distributionen av de nödvändiga infrastruktur resurserna för SAP-arkitektur mal len 2.

Här kan du hämta Azure Resource Manager mallar för det här distributions scenariot:

* [Azure Marketplace-avbildning](https://github.com/Azure/azure-quickstart-templates/)  
* [Anpassad avbildning](https://github.com/Azure/azure-quickstart-templates/)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Förbered infrastrukturen för arkitektur mal len 3

Du kan förbereda infrastrukturen och konfigurera SAP för **multi-sid**. Du kan till exempel lägga till ytterligare en SAP ASCS/SCS-instans i en *befintlig* kluster konfiguration. Mer information finns i [Konfigurera ytterligare en SAP ASCS/SCS-instans till en befintlig kluster konfiguration för att skapa en SAP multi-sid-konfiguration i Azure Resource Manager][sap-ha-multi-sid-guide].

Om du vill skapa ett nytt kluster med flera säkerhets-ID kan du använda [snabb starts mallar för](https://github.com/Azure/azure-quickstart-templates)flera-sid på GitHub.
Om du vill skapa ett nytt kluster med flera säkerhets identifierare måste du distribuera följande tre mallar:

* [ASCS/SCS-mall](#ASCS-SCS-template)
* [Mall för databas](#database-template)
* [Mall för program servrar](#application-servers-template)

I följande avsnitt finns mer information om mallarna och de parametrar som du måste ange i mallarna.

#### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a> ASCS/SCS-mall

ASCS/SCS-mallen distribuerar två virtuella datorer som du kan använda för att skapa ett Windows Server-redundanskluster som är värd för flera ASCS/SCS-instanser.

Konfigurera ASCS/SCS multi-SID-mallen genom att ange värden för följande parametrar i [mallen ASCS/SCS multi-sid][sap-templates-3-tier-multisid-xscs-marketplace-image]:

  - **Resource prefix**.  Ange ett resurs prefix, som används för att ge prefix till alla resurser som skapas under distributionen. Eftersom resurserna inte tillhör bara ett SAP-system, är prefixet för resursen inte SID för ett SAP-system.  Prefixet måste vara mellan **tre och sex tecken**.
  - **Typ av stack**. Välj källtyp för SAP-systemet. Beroende på vilken typ av stack det handlar om Azure Load Balancer har en (endast ABAP eller Java) eller två privata IP-adresser (ABAP + Java) per SAP-system.
  -  **OS-typ**. Välj operativ system för de virtuella datorerna.
  -  **Antal SAP-system**. Välj det antal SAP-system som du vill installera i det här klustret.
  -  **System tillgänglighet**. Välj **ha**.
  -  **Administratörens användar namn och administratörs lösen ord**. Skapa en ny användare som kan användas för att logga in på datorn.
  -  **Nytt eller befintligt undernät**. Ange om ett nytt virtuellt nätverk och undernät ska skapas, eller om ett befintligt undernät ska användas. Om du redan har ett virtuellt nätverk som är anslutet till ditt lokala nätverk väljer du **befintligt**.
  -  **Undernät-ID**. Om du vill distribuera den virtuella datorn till ett befintligt VNet där du har angett ett undernät som har definierats för den virtuella datorn ska du namnge ID: t för det aktuella under nätet. ID: t ser vanligt vis ut så här:/Subscriptions/<*prenumerations-id*>/resourceGroups/<*resurs grupp namn*>/providers/Microsoft.Network/virtualNetworks/<*virtuellt nätverks namn*>/subnets/<*namn på undernät*>

Mallen distribuerar en Azure Load Balancer instans, som stöder flera SAP-system.

- ASCS-instanserna har kon figurer ATS för instans nummer 00, 10, 20...
- SCS-instanserna har kon figurer ATS för instans nummer 01, 11, 21...
- ASCS ERS (endast Linux)-instanser har kon figurer ATS för instans nummer 02, 12, 22...
- SCS-instanserna för ERS (endast Linux) har kon figurer ATS för instans nummer 03, 13, 23...

Belastningsutjämnaren innehåller 1 (2 för Linux) VIP (s), 1x VIP för ASCS/SCS och 1x VIP för ERS (endast Linux).

Följande lista innehåller alla belastnings Utjämnings regler (där x är numret på SAP-systemet, till exempel 1, 2, 3...):
- Windows-/regionsspecifika portar för varje SAP-system: 445, 5985
- ASCS-portar (instans nummer x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS-portar (instans nummer x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- ASCS ERS-portar på Linux (instans nummer x2): 33x2, 5x213, 5x214, 5x216
- SCS ERS-portar på Linux (instans nummer x3): 33x3, 5x313, 5x314, 5x316

Belastningsutjämnaren är konfigurerad att använda följande avsöknings portar (där x är numret på SAP-systemet, till exempel 1, 2, 3...):
- ASCS/SCS-avsöknings port för den interna belastningsutjämnaren: 620x0
- ERS intern belastnings Utjämnings port (endast Linux): 621x2

#### <a name="database-template"></a><a name="database-template"></a> Mall för databas

Databas mal len distribuerar en eller två virtuella datorer som du kan använda för att installera RDBMS (Relations databas hanterings system) för ett SAP-system. Om du till exempel distribuerar en ASCS/SCS-mall för fem SAP-system måste du distribuera den här mallen fem gånger.

Om du vill ställa in databasens multi-SID-mall anger du värden för följande parametrar i [mallen databas flera sid][sap-templates-3-tier-multisid-db-marketplace-image]:

- **SAP-system-ID**. Ange SAP-system-ID: t för det SAP-system som du vill installera. ID: t kommer att användas som prefix för de resurser som distribueras.
- **OS-typ**. Välj operativ system för de virtuella datorerna.
- **DbType**. Välj den typ av databas som du vill installera i klustret. Välj **SQL** om du vill installera Microsoft SQL Server. Välj **Hana** om du planerar att installera SAP HANA på de virtuella datorerna. Se till att välja rätt operativ system typ: Välj **Windows** för SQL och välj en Linux-distribution för Hana. Azure Load Balancer som är anslutna till de virtuella datorerna kommer att konfigureras för att stödja den valda databas typen:
  * **SQL**. Belastningsutjämnaren belastnings Utjämnings port 1433. Se till att använda den här porten för din SQL Server alltid vid installationen.
  * **Hana**. Belastningsutjämnaren ska belastningsutjämna portarna 35015 och 35017. Se till att installera SAP HANA med instans nummer **50**.
  Belastningsutjämnaren kommer att använda avsöknings port 62550.
- **SAP-systemets storlek**. Ange antalet SAPS som det nya systemet kommer att tillhandahålla. Om du inte är säker på hur många SAPS systemet kommer att kräva, kan du fråga din SAP Technology-partner eller system integrerare.
- **System tillgänglighet**. Välj **ha**.
- **Administratörens användar namn och administratörs lösen ord**. Skapa en ny användare som kan användas för att logga in på datorn.
- **Undernät-ID**. Ange ID: t för det undernät som du använde vid distributionen av ASCS/SCS-mallen eller ID: t för det undernät som skapades som en del av distributionen av ASCS/SCS-mallen.

#### <a name="application-servers-template"></a><a name="application-servers-template"></a> Mall för program servrar

Mallen program servrar distribuerar två eller flera virtuella datorer som kan användas som SAP Application Server-instanser för ett SAP-system. Om du till exempel distribuerar en ASCS/SCS-mall för fem SAP-system måste du distribuera den här mallen fem gånger.

Ange värden för följande parametrar i [mallen program servrar][sap-templates-3-tier-multisid-apps-marketplace-image]för flera sid för att ställa in en mall för program servrar med flera sid:

  -  **SAP-system-ID**. Ange SAP-system-ID: t för det SAP-system som du vill installera. ID: t kommer att användas som prefix för de resurser som distribueras.
  -  **OS-typ**. Välj operativ system för de virtuella datorerna.
  -  **SAP-systemets storlek**. Antalet SAPS som det nya systemet kommer att tillhandahålla. Om du inte är säker på hur många SAPS systemet kommer att kräva, kan du fråga din SAP Technology-partner eller system integrerare.
  -  **System tillgänglighet**. Välj **ha**.
  -  **Administratörens användar namn och administratörs lösen ord**. Skapa en ny användare som kan användas för att logga in på datorn.
  -  **Undernät-ID**. Ange ID: t för det undernät som du använde vid distributionen av ASCS/SCS-mallen eller ID: t för det undernät som skapades som en del av distributionen av ASCS/SCS-mallen.


### <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Virtuellt Azure-nätverk
I vårt exempel är adress utrymmet för det virtuella Azure-nätverket 10.0.0.0/16. Det finns ett undernät som heter **undernät**, med adress intervallet 10.0.0.0/24. Alla virtuella datorer och interna belastningsutjämnare distribueras i det här virtuella nätverket.

> [!IMPORTANT]
> Gör inga ändringar i nätverks inställningarna i gäst operativ systemet. Detta inkluderar IP-adresser, DNS-servrar och undernät. Konfigurera alla nätverks inställningar i Azure. Tjänsten Dynamic Host Configuration Protocol (DHCP) sprider dina inställningar.
>
>

### <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS IP-adresser

Utför följande steg för att ange de DNS-IP-adresser som krävs.

1. I Azure Portal på bladet **DNS-servrar** kontrollerar du att alternativet **DNS-servrar** för virtuella nätverk är inställt på **anpassad DNS**.
2. Välj inställningar baserat på vilken typ av nätverk du har. Mer information finns i följande resurser:
   * Lägg till IP-adresserna för lokala DNS-servrar.  
   Du kan utöka lokala DNS-servrar till de virtuella datorer som körs i Azure. I det scenariot kan du lägga till IP-adresserna för de virtuella Azure-datorer som du kör DNS-tjänsten på.
   * För distributioner isolerade i Azure: Distribuera en ytterligare virtuell dator i samma Virtual Network-instans som fungerar som en DNS-server. Lägg till IP-adresserna för de virtuella Azure-datorer som du har konfigurerat för att köra DNS-tjänsten.

   ![Bild 12: Konfigurera DNS-servrar för Azure Virtual Network][sap-ha-guide-figure-3001]

   _**Figur 12:** Konfigurera DNS-servrar för Azure Virtual Network_

   > [!NOTE]
   > Om du ändrar IP-adresserna för DNS-servrarna måste du starta om de virtuella Azure-datorerna för att tillämpa ändringen och sprida nya DNS-servrar.
   >
   >

I vårt exempel installeras och konfigureras DNS-tjänsten på de virtuella Windows-datorerna:

| Rollen virtuell dator | Värddator namn för virtuell dator | Nätverks korts namn | Statisk IP-adress |
| --- | --- | --- | --- |
| Första DNS-servern |domcontr-0 |PR1-NIC-domcontr-0 |10.0.0.10 |
| Andra DNS-Server |domcontr-1 |PR1-NIC-domcontr-1 |10.0.0.11 |

### <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Värdnamn och statiska IP-adresser för SAP ASCS/SCS-klustrad instans och DBMS-klustrad instans

För lokal distribution behöver du dessa reserverade värdnamn och IP-adresser:

| Roll för virtuellt värd namn | Namn på virtuell värd | Virtuell statisk IP-adress |
| --- | --- | --- |
| SAP ASCS/SCS första kluster namn för virtuell värd (för kluster hantering) |PR1-ASCs-vir |10.0.0.42 |
| Namn på virtuell SAP-ASCS/SCS-instans |PR1-ASCs-SAP |10.0.0.43 |
| SAP DBMS andra kluster namn för virtuell värd (kluster hantering) |PR1-DBMS-vir |10.0.0.32 |

När du skapar klustret skapar du de virtuella värd namnen **PR1-ASCs-vir** och **PR1-DBMS-vir** och de associerade IP-adresserna som hanterar själva klustret. Information om hur du gör detta finns i [samla in klusternoder i en kluster konfiguration][sap-ha-guide-8.12.1].

Du kan skapa de andra två virtuella värd namnen manuellt, **PR1-ASCs-SAP** och **PR1-DBMS-SAP** och tillhör ande IP-adresser på DNS-servern. Den klustrade SAP-ASCS/SCS-instansen och den klustrade DBMS-instansen använder dessa resurser. Information om hur du gör detta finns i [skapa ett virtuellt värdnamn för en klustrad SAP ASCS/SCS-instans][sap-ha-guide-9.1.1].

### <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Ange statiska IP-adresser för virtuella SAP-datorer
När du har distribuerat de virtuella datorerna som ska användas i klustret måste du ange statiska IP-adresser för alla virtuella datorer. Gör detta i Azure Virtual Network-konfigurationen och inte i gäst operativ systemet.

1. I Azure Portal väljer du IP-adress för **resurs gruppens**  >  **nätverks kort**  >  **Inställningar**  >  .
2. Välj **statisk** i bladet **IP-adresser** under **tilldelning**. I rutan **IP-adress** anger du den IP-adress som du vill använda.

   > [!NOTE]
   > Om du ändrar IP-adressen för nätverkskortet måste du starta om de virtuella Azure-datorerna för att tillämpa ändringen.  
   >
   >

   ![Bild 13: ange statiska IP-adresser för nätverkskortet för varje virtuell dator][sap-ha-guide-figure-3002]

   _**Figur 13:** Ange statiska IP-adresser för nätverkskortet för varje virtuell dator_

   Upprepa det här steget för alla nätverks gränssnitt, det vill säga för alla virtuella datorer, inklusive virtuella datorer som du vill använda för din Active Directory/DNS-tjänst.

I vårt exempel har vi dessa virtuella datorer och statiska IP-adresser:

| Rollen virtuell dator | Värddator namn för virtuell dator | Nätverks korts namn | Statisk IP-adress |
| --- | --- | --- | --- |
| Första SAP Application Server-instansen |PR1-di-0 |PR1-NIC-di-0 |10.0.0.50 |
| Andra SAP Application Server-instans |PR1-di-1 |PR1-NIC-di-1 |10.0.0.51 |
| ... |... |... |... |
| Senaste SAP Application Server-instans |PR1-di-5 |PR1-NIC-di-5 |10.0.0.55 |
| Första klusternoden för ASCS/SCS-instans |PR1-ASCs-0 |PR1-NIC-ASCs-0 |10.0.0.40 |
| Andra klusternoden för ASCS/SCS-instans |PR1-ASCs-1 |PR1-NIC-ASCs-1 |10.0.0.41 |
| Första klusternoden för DBMS-instans |PR1-dB-0 |PR1-NIC-dB-0 |10.0.0.30 |
| Andra klusternoden för DBMS-instans |PR1-DB-1 |PR1-NIC-DB-1 |10.0.0.31 |

### <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Ange en statisk IP-adress för den interna Azure-belastningsutjämnaren

SAP Azure Resource Manager-mallen skapar en intern Azure-belastningsutjämnare som används för instans klustret SAP ASCS/SCS och DBMS-klustret.

> [!IMPORTANT]
> IP-adressen för det virtuella värd namnet för SAP ASCS/SCS är samma som IP-adressen för den interna belastningsutjämnaren för SAP ASCS/SCS: **PR1-lb-ASCS**.
> IP-adressen för det virtuella namnet på DBMS är samma som IP-adressen för den interna DBMS-belastningsutjämnaren: **PR1 – lb-DBMS**.
>
>

Ange en statisk IP-adress för den interna Azure-belastningsutjämnaren:

1. Den inledande distributionen anger den interna IP-adressen för belastningsutjämnaren till **dynamisk**. I Azure Portal väljer du **statisk** i bladet **IP-adresser** under **tilldelning**.
2. Ange IP-adressen för den interna belastningsutjämnaren **PR1-lb-ASCs** till IP-adressen för det virtuella värd namnet för SAP ASCS/SCS-instansen.
3. Ange IP-adressen för den interna belastningsutjämnaren **PR1 – lb-DBMS** till IP-adressen för den virtuella värd namnet för DBMS-instansen.

   ![Bild 14: ange statiska IP-adresser för den interna belastningsutjämnaren för SAP ASCS/SCS-instansen][sap-ha-guide-figure-3003]

   _**Bild 14:** Ange statiska IP-adresser för den interna belastningsutjämnaren för SAP ASCS/SCS-instansen_

I vårt exempel har vi två interna Azure-belastningsutjämnare som har följande statiska IP-adresser:

| Rollen Azures interna belastnings utjämning | Namn på Azure-intern belastningsutjämnare | Statisk IP-adress |
| --- | --- | --- |
| Intern belastningsutjämnare för SAP ASCS/SCS-instans |PR1 – lb-ASCs |10.0.0.43 |
| Intern belastningsutjämnare för SAP-DBMS |PR1 – lb-DBMS |10.0.0.33 |


### <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Standard ASCS/SCS-belastnings Utjämnings regler för den interna Azure-belastningsutjämnaren

SAP Azure Resource Manager-mallen skapar de portar som du behöver:
* En ABAP ASCS-instans med standard instans talet **00**
* En Java SCS-instans med standard instans numret **01**

När du installerar din SAP ASCS/SCS-instans måste du använda standard instansen **00** för din ABAP ASCS-instans och standard instans numret **01** för din Java SCS-instans.

Därefter skapar du nödvändiga interna belastnings Utjämnings slut punkter för SAP NetWeaver-portarna.

För att skapa nödvändiga interna belastnings Utjämnings slut punkter skapar du först dessa slut punkter för belastnings utjämning för SAP NetWeaver ABAP ASCS-portarna:

| Namn på tjänst/belastnings Utjämnings regel | Standard port nummer | Konkreta portar för (ASCS-instans med instans nummer 00) (ERS med 10) |
| --- | --- | --- |
| Köa Server/ *lbrule3200* |32<*InstanceNumber*> |3200 |
| ABAP meddelande Server/ *lbrule3600* |36<*InstanceNumber*> |3600 |
| Internt ABAP-meddelande/ *lbrule3900* |39<*InstanceNumber*> |3900 |
| Meddelande Server HTTP/ *Lbrule8100* |81<*InstanceNumber*> |8100 |
| SAP Start Service ASCS HTTP/ *Lbrule50013* |5<*InstanceNumber*>13 |50013 |
| SAP Start Service ASCS HTTPS/ *Lbrule50014* |5<*InstanceNumber*>14 |50014 |
| Köa replikering/ *Lbrule50016* |5<*InstanceNumber*>16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5<*InstanceNumber*>13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5<*InstanceNumber*>14 |51014 |
| Win RM- *Lbrule5985* | |5985 |
| *Lbrule445* för fil resurs | |445 |

_**Tabell 1:** Port nummer för SAP NetWeaver ABAP ASCS-instanser_

Skapa sedan de här slut punkterna för belastnings utjämning för SAP NetWeaver Java SCS-portarna:

| Namn på tjänst/belastnings Utjämnings regel | Standard port nummer | Konkreta portar för (SCS-instans med instans nummer 01) (ERS med 11) |
| --- | --- | --- |
| Köa Server/ *lbrule3201* |32<*InstanceNumber*> |3201 |
| Gateway-server/ *lbrule3301* |33<*InstanceNumber*> |3301 |
| Java-meddelande Server/ *lbrule3900* |39<*InstanceNumber*> |3901 |
| Meddelande Server HTTP/ *Lbrule8101* |81<*InstanceNumber*> |8101 |
| SAP Start Service SCS HTTP/ *Lbrule50113* |5<*InstanceNumber*>13 |50113 |
| SAP Start Service SCS HTTPS/ *Lbrule50114* |5<*InstanceNumber*>14 |50114 |
| Köa replikering/ *Lbrule50116* |5<*InstanceNumber*>16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5<*InstanceNumber*>13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5<*InstanceNumber*>14 |51114 |
| Win RM- *Lbrule5985* | |5985 |
| *Lbrule445* för fil resurs | |445 |

_**Tabell 2:** Port nummer för SAP NetWeaver Java SCS-instanserna_

![Bild 15: standard regler för ASCS/SCS-belastning för den interna Azure-belastningsutjämnaren][sap-ha-guide-figure-3004]

_**Bild 15:** Standard ASCS/SCS-belastnings Utjämnings regler för den interna Azure-belastningsutjämnaren_

Ange IP-adressen för belastningsutjämnaren **PR1 – lb-DBMS** till IP-adressen för det virtuella värd namnet för DBMS-instansen.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Ändra standard reglerna för belastnings utjämning för ASCS/SCS för den interna Azure-belastningsutjämnaren

Om du vill använda olika nummer för SAP ASCS-eller SCS-instanserna måste du ändra namn och värden för deras portar från standardvärdena.

1. I Azure Portal väljer du **< *sid*>-lb-ASCs** belastnings  >  **Utjämnings regler**.
2. Ändra följande värden för alla belastnings Utjämnings regler som tillhör SAP ASCS-eller SCS-instansen:

   * Namn
   * Port
   * Server dels port

   Om du till exempel vill ändra standard instans numret för ASCS från 00 till 31 måste du göra ändringarna för alla portar som anges i tabell 1.

   Här är ett exempel på en uppdatering för port *lbrule3200*.

   ![Bild 16: ändra standard reglerna för belastnings utjämning för ASCS/SCS för den interna Azure-belastningsutjämnaren][sap-ha-guide-figure-3005]

   _**Bild 16:** Ändra standard reglerna för belastnings utjämning för ASCS/SCS för den interna Azure-belastningsutjämnaren_

### <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Lägg till virtuella Windows-datorer i domänen

När du har tilldelat en statisk IP-adress till de virtuella datorerna lägger du till de virtuella datorerna i domänen.

![Bild 17: lägga till en virtuell dator i en domän][sap-ha-guide-figure-3006]

_**Bild 17:** Lägga till en virtuell dator i en domän_

### <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Lägg till register poster på båda klusternoderna för SAP ASCS/SCS-instansen

Azure Load Balancer har en intern belastningsutjämnare som stänger anslutningar när anslutningarna är inaktiva under en angiven tids period (tids gräns för inaktivitet). SAP-arbetsprocesser i dialog instanser öppnar anslutningar till SAP-kön så snart den första kön/avqueue-begäran måste skickas. Dessa anslutningar förblir vanligt vis etablerade tills arbets processen eller omstarten av kön startas om. Om anslutningen däremot är inaktiv under en angiven tids period stängs anslutningarna av Azure Internal Load Balancer. Detta är inte ett problem eftersom SAP-arbetsprocessen återupprättar anslutningen till kön om den inte längre finns. Dessa aktiviteter dokumenteras i utvecklarnas spår för SAP-processer, men de skapar en stor mängd extra innehåll i dessa spår. Det är en bra idé att ändra TCP/IP `KeepAliveTime` och `KeepAliveInterval` båda klusternoderna. Kombinera dessa ändringar i TCP/IP-parametrarna med SAP Profile-parametrar, som beskrivs senare i artikeln.

Om du vill lägga till register poster på båda klusternoderna för SAP ASCS/SCS-instansen lägger du först till dessa Windows-registerposter på båda Windows-klusternoderna för SAP ASCS/SCS:

| Sökväg | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Variabelnamn |`KeepAliveTime` |
| Variabel typ |REG_DWORD (decimal) |
| Värde |120000 |
| Länk till dokumentation |[https://technet.microsoft.com/library/cc957549.aspx](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) |

_**Tabell 3:** Ändra den första TCP/IP-parametern_

Lägg sedan till dessa Windows register poster på båda Windows-klusternoderna för SAP ASCS/SCS:

| Sökväg | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Variabelnamn |`KeepAliveInterval` |
| Variabel typ |REG_DWORD (decimal) |
| Värde |120000 |
| Länk till dokumentation |[https://technet.microsoft.com/library/cc957548.aspx](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) |

_**Tabell 4:** Ändra den andra TCP/IP-parametern_

**Om du vill tillämpa ändringarna startar du om båda klusternoderna**.

### <a name="set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Konfigurera ett kluster för redundanskluster i Windows Server för en SAP ASCS/SCS-instans

Att ställa in ett kluster för redundanskluster i Windows Server för en SAP ASCS/SCS-instans omfattar följande uppgifter:

- Samla in klusternoderna i en kluster konfiguration
- Konfigurera ett kluster fil resurs vittne

#### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Samla in klusternoderna i en kluster konfiguration

1. I guiden Lägg till roller och funktioner lägger du till kluster för växling vid fel i båda klusternoderna.
2. Konfigurera redundansklustret med hjälp av Klusterhanteraren för växling vid fel. I Klusterhanteraren för växling vid fel väljer du **skapa kluster** och lägger sedan till endast namnet på det första klustret, nod A. Lägg inte till den andra noden ännu; du kommer att lägga till den andra noden i ett senare steg.

   ![Bild 18: Lägg till servern eller namnet på den virtuella datorn för den första klusternoden][sap-ha-guide-figure-3007]

   _**Bild 18:** Lägg till servern eller namnet på den virtuella datorn för den första klusternoden_

3. Ange Nätverks namnet (det virtuella värd namnet) för klustret.

   ![Bild 19: Ange kluster namnet][sap-ha-guide-figure-3008]

   _**Bild 19:** Ange kluster namnet_

4. När du har skapat klustret kör du ett kluster validerings test.

   ![Bild 20: kör kluster verifierings kontrollen][sap-ha-guide-figure-3009]

   _**Bild 20:** Kör kluster verifierings kontrollen_

   Du kan ignorera eventuella varningar om diskarna i processen. Du kommer att lägga till ett fil resurs vittne och de SIOS delade diskarna senare. I det här skedet behöver du inte bekymra dig om att ha ett kvorum.

   ![Bild 21: ingen kvorumdisk hittades][sap-ha-guide-figure-3010]

   _**Bild 21:** Ingen kvorumdisk hittades_

   ![Bild 22: kärn kluster resursen behöver en ny IP-adress][sap-ha-guide-figure-3011]

   _**Bild 22:** Kärn kluster resursen behöver en ny IP-adress_

5. Ändra IP-adressen för kärn kluster tjänsten. Klustret kan inte starta förrän du ändrar IP-adressen för kärn kluster tjänsten, eftersom IP-adressen för-servern pekar på någon av noderna för virtuella datorer. Gör detta på **egenskaps** sidan för kärn kluster tjänstens IP-resurs.

   Vi måste t. ex. tilldela en IP-adress (i vårt exempel **10.0.0.42**) för det virtuella kluster värd namnet **PR1-ASCs-vir**.

   ![Bild 23: ändra IP-adressen i dialog rutan Egenskaper][sap-ha-guide-figure-3012]

   _**Figur 23:** Ändra IP-adressen i dialog rutan **Egenskaper**_

   ![Bild 24: tilldela den IP-adress som är reserverad för klustret][sap-ha-guide-figure-3013]

   _**Bild 24:** Tilldela den IP-adress som är reserverad för klustret_

6. Ta det virtuella kluster värd namnet online.

   ![Bild 25: kluster kärn tjänsten är igång och har rätt IP-adress][sap-ha-guide-figure-3014]

   _**Bild 25:** Kluster kärn tjänsten är igång och har en korrekt IP-adress_

7. Lägg till den andra klusternoden.

   Nu när kärn kluster tjänsten är igång kan du lägga till den andra klusternoden.

   ![Bild 26: Lägg till den andra klusternoden][sap-ha-guide-figure-3015]

   _**Bild 26:** Lägg till den andra klusternoden_

8. Ange ett namn för den andra klusternodens värd.

   ![Bild 27: Ange det andra värd namnet för klusternoden][sap-ha-guide-figure-3016]

   _**Bild 27:** Ange det andra värd namnet för klusternoden_

   > [!IMPORTANT]
   > Se till att kryss rutan **Lägg till alla tillgängliga lagrings enheter i klustret** **inte** är markerad.  
   >
   >

   ![Bild 28: Markera inte kryss rutan][sap-ha-guide-figure-3017]

   _**Bild 28:** Markera **inte** kryss rutan_

   Du kan ignorera varningar om kvorum och diskar. Du ställer in kvorumet och delar disken senare, enligt beskrivningen i [Installera SIOS DataKeeper Cluster Edition för SAP ASCS/SCS Cluster Share disk][sap-ha-guide-8.12.3].

   ![Bild 29: Ignorera varningar om kvorumdisken][sap-ha-guide-figure-3018]

   _**Bild 29:** Ignorera varningar om kvorumdisken_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Konfigurera ett kluster fil resurs vittne

Att konfigurera ett kluster fil resurs vittne omfattar följande uppgifter:

- Skapa en fil resurs
- Ange fil resurs vittnets kvorum i Klusterhanteraren för växling vid fel

##### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Skapa en fil resurs

1. Välj ett fil resurs vittne i stället för en kvorumdisk. SIOS DataKeeper stöder det här alternativet.

   I exemplen i den här artikeln finns fil resurs vittnet på den Active Directory/DNS-server som körs i Azure. Fil resurs vittnet kallas **domcontr-0**. Eftersom du skulle ha konfigurerat en VPN-anslutning till Azure (via plats-till-plats-VPN eller Azure ExpressRoute) är din Active Directory/DNS-tjänst lokalt och är inte lämplig för att köra ett fil resurs vittne.

   > [!NOTE]
   > Om din Active Directory-/DNS-tjänst bara körs lokalt konfigurerar du inte fil resurs vittnet på det Active Directory/DNS-Windows-operativsystem som körs lokalt. Nätverks fördröjningen mellan klusternoder som körs i Azure och Active Directory/DNS lokalt kan vara för stor och orsaka anslutnings problem. Se till att konfigurera fil resurs vittnet på en virtuell Azure-dator som kör nära klusternoden.  
   >
   >

   Kvorumdisken måste ha minst 1 024 MB ledigt utrymme. Vi rekommenderar 2 048 MB ledigt utrymme för kvorumdisken.

2. Lägg till objektet kluster namn.

   ![Bild 30: tilldela behörigheter för resursen för objektet kluster namn][sap-ha-guide-figure-3019]

   _**Bild 30:** Tilldela behörighet till resursen för objektet kluster namn_

   Se till att behörigheterna omfattar behörigheten att ändra data i resursen för objektet kluster namn (i vårt exempel **PR1-ASCs-vir $**).

3. Välj **Lägg** till om du vill lägga till kluster namns objekt i listan. Ändra filtret för att kontrol lera dator objekt, utöver de som visas i bild 31.

   ![Bild 31: ändra objekt typer för att inkludera datorer][sap-ha-guide-figure-3020]

   _**Bild 31:** Ändra objekt typerna för att inkludera datorer_

   ![Bild 32: Markera kryss rutan datorer][sap-ha-guide-figure-3021]

   _**Bild 32:** Markera kryss rutan **datorer**_

4. Ange objektet kluster namn som det visas i bild 31. Eftersom posten redan har skapats kan du ändra behörigheterna, som du ser i bild 30.

5. Välj fliken **säkerhet** i resursen och ange sedan mer detaljerade behörigheter för objektet kluster namn.

   ![Bild 33: Ange Säkerhetsattributen för objektet kluster namn på fil resursens kvorum][sap-ha-guide-figure-3022]

   _**Bild 33:** Ange Säkerhetsattributen för objektet kluster namn på fil resursens kvorum_

##### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Ange fil resurs vittnets kvorum i Klusterhanteraren för växling vid fel

1. Öppna guiden Konfigurera kvorumkonfigurationen.

   ![Bild 34: starta guiden Konfigurera klusterkvorum][sap-ha-guide-figure-3023]

   _**Bild 34:** Starta guiden Konfigurera klusterkvorum_

2. På sidan **Välj kvorumkonfiguration** väljer **du Välj ett kvorum**.

   ![Bild 35: kvorumkonfigurationen som du kan välja mellan][sap-ha-guide-figure-3024]

   _**Bild 35:** Kvorumresurser du kan välja mellan_

3. På sidan **Välj kvorumdisk** väljer du **Konfigurera ett fil resurs vittne**.

   ![Bild 36: Välj fil resurs vittnet][sap-ha-guide-figure-3025]

   _**Bild 36:** Välj fil resurs vittnet_

4. Ange UNC-sökvägen till fil resursen (i vårt exempel \\ domcontr-0\FSW). Om du vill se en lista över de ändringar som du kan göra väljer du **Nästa**.

   ![Bild 37: definiera fil resurs platsen för vittnes resursen][sap-ha-guide-figure-3026]

   _**Bild 37:** Definiera fil resurs platsen för vittnes resursen_

5. Välj de ändringar som du vill använda och välj sedan **Nästa**. Du måste konfigurera om kluster konfigurationen på det sätt som visas i bild 38.  

   ![Bild 38: bekräfta att du har konfigurerat om klustret][sap-ha-guide-figure-3027]

   _**Bild 38:** Bekräfta att du har konfigurerat om klustret_

När Windows-redundansklustret har installerats måste ändringar göras i vissa tröskelvärden för att anpassa identifieringen av redundans till villkor i Azure. De parametrar som ska ändras dokumenteras i den här bloggen: [https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) . Förutsatt att de två virtuella datorerna som bygger Windows-klustrets konfiguration för ASCS/SCS finns i samma undernät, måste följande parametrar ändras till dessa värden:  
- SameSubNetDelay = 2000  
- SameSubNetThreshold = 15  
- RoutingHistoryLength = 30  

De här inställningarna har testats med kunderna och fått en lämplig kompromiss för att vara tillräckligt flexibel på den ena sidan. Å andra sidan ger de här inställningarna snabb tillräckligt med redundans i verkliga fel tillstånd för SAP-program eller nod/VM-fel. 

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Installera SIOS DataKeeper Cluster Edition för SAP ASCS/SCS Cluster Share disk

Nu har du en fungerande konfiguration av redundanskluster för Windows Server i Azure. Men om du vill installera en SAP ASCS/SCS-instans behöver du en delad disk resurs. Du kan inte skapa de delade disk resurser du behöver i Azure. SIOS DataKeeper Cluster Edition är en lösning från tredje part som du kan använda för att skapa delade disk resurser.

Att installera SIOS DataKeeper Cluster Edition för SAP ASCS/SCS-klustrets resurs disk omfattar följande uppgifter:

- Lägga till .NET Framework 3,5
- Installera SIOS DataKeeper
- Konfigurera SIOS DataKeeper

#### <a name="add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Lägg till .NET Framework 3,5
Microsoft .NET Framework 3,5 aktive ras eller installeras inte automatiskt på Windows Server 2012 R2. Eftersom SIOS DataKeeper kräver att .NET Framework finns på alla noder som du installerar DataKeeper på, måste du installera .NET Framework 3,5 på gäst operativ systemet för alla virtuella datorer i klustret.

Det finns två sätt att lägga till .NET Framework 3,5:

- Använd guiden Lägg till roller och funktioner i Windows på det sätt som visas i bild 39.

  ![Bild 39: installera .NET Framework 3,5 med hjälp av guiden Lägg till roller och funktioner][sap-ha-guide-figure-3028]

  _**Bild 39:** Installera .NET Framework 3,5 med hjälp av guiden Lägg till roller och funktioner_

  ![Bild 40: förlopps indikator för installationen när du installerar .NET Framework 3,5 med hjälp av guiden Lägg till roller och funktioner][sap-ha-guide-figure-3029]

  _**Bild 40:** Installations förlopps indikator när du installerar .NET Framework 3,5 med hjälp av guiden Lägg till roller och funktioner_

- Använd kommando rads verktyget dism.exe. För den här typen av installation måste du ha åtkomst till katalogen SxS på installations mediet för Windows. Skriv följande i en upphöjd kommando tolk:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a> Installera SIOS DataKeeper

Installera SIOS DataKeeper Cluster Edition på varje nod i klustret. Skapa en virtuell delad lagring med SIOS DataKeeper genom att skapa en synkroniserad spegling och sedan simulera klusterdelad lagring.

Innan du installerar SIOS-programvaran skapar du **DataKeeperSvc** för domän användare.

> [!NOTE]
> Lägg till **DataKeeperSvc** -användaren i den **lokala administratörs** gruppen på båda klusternoderna.
>
>

Så här installerar du SIOS DataKeeper:

1. Installera SIOS-programvaran på båda klusternoderna.

   ![Installations program för SIOS][sap-ha-guide-figure-3030]

   ![Bild 41: första sidan i SIOS DataKeeper-installationen][sap-ha-guide-figure-3031]

   _**Bild 41:** Första sidan i SIOS DataKeeper-installationen_

2. Välj **Ja** i dialog rutan som visas i bild 42.

   ![Bild 42: DataKeeper informerar dig om att en tjänst kommer att inaktive ras][sap-ha-guide-figure-3032]

   _**Bild 42:** DataKeeper informerar dig om att en tjänst kommer att inaktive ras_

3. I den dialog ruta som visas i bild 43 rekommenderar vi att du väljer **domän-eller Server konto**.

   ![Bild 43: val av användare för SIOS-DataKeeper][sap-ha-guide-figure-3033]

   _**Bild 43:** Val av användare för SIOS-DataKeeper_

4. Ange det användar namn och lösen ord för domän kontot som du skapade för SIOS-DataKeeper.

   ![Bild 44: ange domän användar namn och lösen ord för SIOS DataKeeper-installationen][sap-ha-guide-figure-3034]

   _**Bild 44:** Ange domänens användar namn och lösen ord för SIOS DataKeeper-installationen_

5. Installera licens nyckeln för SIOS DataKeeper-instansen enligt bilden 45.

   ![Bild 45: Ange licens nyckeln för SIOS DataKeeper][sap-ha-guide-figure-3035]

   _**Bild 45:** Ange licens nyckeln för SIOS DataKeeper_

6. Starta om den virtuella datorn när du uppmanas till det.

#### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Konfigurera SIOS DataKeeper

När du har installerat SIOS DataKeeper på båda noderna måste du starta konfigurationen. Målet med konfigurationen är att ha synkron datareplikering mellan de ytterligare virtuella hård diskar som är anslutna till var och en av de virtuella datorerna.

1. Starta hanterings-och konfigurations verktyget för DataKeeper och välj sedan **Anslut Server**. (I bild 46 är det här alternativet inringat i rött.)

   ![Bild 46: hanterings-och konfigurations verktyg för SIOS DataKeeper][sap-ha-guide-figure-3036]

   _**Bild 46:** Hanterings-och konfigurations verktyg för SIOS-DataKeeper_

2. Ange namnet eller TCP/IP-adressen för den första noden som hanterings-och konfigurations verktyget ska ansluta till och, i ett andra steg, den andra noden.

   ![Bild 47: Infoga namnet eller TCP/IP-adressen för den första noden som hanterings-och konfigurations verktyget ska ansluta till, och i ett andra steg, den andra noden][sap-ha-guide-figure-3037]

   _**Bild 47:** Infoga namnet eller TCP/IP-adressen för den första noden som hanterings-och konfigurations verktyget ska ansluta till, och i ett andra steg, den andra noden_

3. Skapa ett replikerings jobb mellan de två noderna.

   ![Bild 48: skapa ett jobb för replikering][sap-ha-guide-figure-3038]

   _**Bild 48:** Skapa ett jobb för replikering_

   En guide vägleder dig genom processen med att skapa ett migreringsjobb.
4. Definiera namnet, TCP/IP-adressen och käll nodens disk volym.

   ![Bild 49: definiera namnet på replikeringssystemet][sap-ha-guide-figure-3039]

   _**Bild 49:** Definiera namnet på det replikerade jobbet_

   ![Bild 50: definiera grunddata för noden, som ska vara den aktuella Källnoden][sap-ha-guide-figure-3040]

   _**Bild 50:** Definiera grunddata för noden, som ska vara den aktuella Källnoden_

5. Definiera namn, TCP/IP-adress och disk volym för målnoden.

   ![Bild 51: definiera grunddata för noden, som ska vara den aktuella målnoden][sap-ha-guide-figure-3041]

   _**Bild 51:** Definiera grunddata för noden, som ska vara den aktuella målnoden_

6. Definiera algoritmerna för komprimering. I vårt exempel rekommenderar vi att du komprimerar replik strömmen. I synnerhet i omsynkroniseringen minskar replikeringen av replikeringsuppsättningen dramatiskt omsynkroniseringen avsevärt. Observera att komprimering använder processor-och RAM-resurser för en virtuell dator. När komprimerings hastigheten ökar så används volymen av processor resurser. Du kan också ändra den här inställningen senare.

7. En annan inställning som du måste kontrol lera är om replikeringen sker asynkront eller synkront. *När du skyddar SAP ASCS/SCS-konfigurationer måste du använda synkron replikering*.  

   ![Bild 52: definiera replikeringsinformation][sap-ha-guide-figure-3042]

   _**Bild 52:** Definiera replikeringsinformation_

8. Ange om volymen som replikeras av replikeringssystemet ska representeras för en kluster konfiguration för Windows Server-redundanskluster som en delad disk. För SAP ASCS/SCS-konfigurationen väljer du **Ja** så att Windows-klustret ser den replikerade volymen som en delad disk som kan användas som en kluster volym.

   ![Bild 53: Välj Ja för att ställa in den replikerade volymen som en kluster volym][sap-ha-guide-figure-3043]

   _**Bild 53:** Välj **Ja** för att ställa in den replikerade volymen som en kluster volym_

   När volymen har skapats visar hanterings-och konfigurations verktyget för DataKeeper att replikeringsläget är aktivt.

   ![Bild 54: DataKeeper synkron spegling för resursen SAP ASCS/SCS är aktiv][sap-ha-guide-figure-3044]

   _**Bild 54:** DataKeeper synkron spegling för resurs disken för SAP ASCS/SCS är aktiv_

   Klusterhanteraren för växling vid fel visar nu disken som en DataKeeper disk, som du ser i bild 55.

   ![Bild 55: Klusterhanteraren för växling vid fel visar den disk som DataKeeper replikerade][sap-ha-guide-figure-3045]

   _**Bild 55:** Klusterhanteraren för växling vid fel visar den disk som DataKeeper replikerade_

## <a name="install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Installera SAP NetWeaver-systemet

Vi beskriver inte DBMS-installationen eftersom installationen varierar beroende på vilket DBMS-system du använder. Vi förutsätter dock att hög tillgänglighets problem med DBMS är riktade mot de olika DBMS-leverantörernas support för Azure. Till exempel, Always on eller databas spegling för SQL Server och Oracle data Guard för Oracle-databaser. I det scenario vi använder i den här artikeln har vi inte lagt till mer skydd i DBMS.

Det finns inga särskilda överväganden när olika DBMS-tjänster interagerar med den här typen av klustrade SAP ASCS/SCS-konfiguration i Azure.

> [!NOTE]
> Installations procedurerna för SAP NetWeaver ABAP Systems, Java Systems och ABAP + Java system är nästan identiska. Den viktigaste skillnaden är att ett SAP ABAP-system har en ASCS-instans. SAP Java-systemet har en SCS-instans. SAP ABAP + Java-systemet har en ASCS-instans och en SCS-instans som körs i samma kluster grupp för Microsoft-redundans. Eventuella installations skillnader för varje SAP NetWeaver-installations stack anges uttryckligen. Du kan anta att alla andra delar är desamma.  
>
>

### <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Installera SAP med en ASCS/SCS-instans med hög tillgänglighet

> [!IMPORTANT]
> Se till att du inte placerar växlings filen på DataKeeper speglade volymer. DataKeeper har inte stöd för speglade volymer. Du kan lämna växlings filen på den temporära enheten D för en virtuell Azure-dator, vilket är standard. Om den inte redan finns där flyttar du Windows-Page-filen till enhet D på den virtuella Azure-datorn.
>
>

Att installera SAP med en ASCS/SCS-instans med hög tillgänglighet omfattar följande uppgifter:

- Skapa ett virtuellt värdnamn för den klustrade SAP ASCS/SCS-instansen
- Installera den första SAP-klusternoden
- Ändra SAP-profilen för ASCS/SCS-instansen
- Lägga till en avsöknings port
- Öppnar avsöknings porten för Windows-brandväggen

#### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Skapa ett virtuellt värdnamn för den klustrade SAP ASCS/SCS-instansen

1. I Windows DNS Manager skapar du en DNS-post för det virtuella värd namnet för ASCS/SCS-instansen.

   > [!IMPORTANT]
   > Den IP-adress som du tilldelar det virtuella värd namnet för ASCS/SCS-instansen måste vara samma som den IP-adress som du tilldelade till Azure Load Balancer (**< *sid*>-lb-ASCS**).  
   >
   >

   IP-adressen för det virtuella SAP ASCS/SCS-värdnamnet (**PR1-ASCS-SAP**) är samma som IP-adressen för Azure Load Balancer (**PR1-lb-ASCS**).

   ![Bild 56: definiera DNS-posten för SAP ASCS/SCS-klustrets virtuella namn och TCP/IP-adress][sap-ha-guide-figure-3046]

   _**Bild 56:** Definiera DNS-posten för SAP ASCS/SCS-klustrets virtuella namn och TCP/IP-adress_

2. Om du vill definiera IP-adressen som tilldelats det virtuella värd namnet väljer du **DNS Manager-**  >  **domän**.

   ![Bild 57: nytt virtuellt namn och TCP/IP-adress för SAP ASCS/SCS kluster konfiguration][sap-ha-guide-figure-3047]

   _**Bild 57:** Nytt virtuellt namn och TCP/IP-adress för SAP ASCS/SCS kluster konfiguration_

#### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Installera den första SAP-klusternoden

1. Kör den första klusternoden på klusternod A. Till exempel på **PR1-ASCs-0-** värden.
2. Om du vill behålla standard portarna för den interna Azure-belastningsutjämnaren väljer du:

   * **ABAP system**: **ASCS** instance Number **00**
   * **Java system**: **SCS** instance Number **01**
   * **ABAP + Java-system**: **ASCS** instance Number **00** och **SCS** instance Number **01**

   Om du vill använda andra instans nummer än 00 för ABAP ASCS-instansen och 01 för Java SCS-instansen måste du först ändra reglerna för standard belastnings utjämning för Azure Internal Load Balancer, som beskrivs i [ändra standard reglerna för belastnings utjämning i ASCS/SCS för Azure Internal Load Balancer][sap-ha-guide-8.9].

De kommande aktiviteterna beskrivs inte i standard dokumentationen för SAP-installationer.

> [!NOTE]
> I installations dokumentationen för SAP beskrivs hur du installerar den första ASCS/SCS-klusternoden.
>
>

#### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Ändra SAP-profilen för ASCS/SCS-instansen

Du måste lägga till en ny profil parameter. Profil parametern förhindrar anslutningar mellan SAP-arbetsprocesser och-servern från att stängas när de är inaktiva för länge. Vi nämndede problem scenariot i [lägga till register poster på båda klusternoderna för SAP ASCS/SCS-instansen][sap-ha-guide-8.11]. I det avsnittet har vi också lanserat två ändringar av några grundläggande TCP/IP-anslutningssträngar. I det andra steget måste du ange att servern ska skicka en `keep_alive` signal så att anslutningarna inte når den interna inaktiva tröskeln för Azures belastningsutjämnare.

Ändra SAP-profilen för ASCS/SCS-instansen:

1. Lägg till den här profil parametern till instansen av SAP ASCS/SCS, om du använder ENSA1:

   ```
   enque/encni/set_so_keepalive = true
   ```
   I vårt exempel är sökvägen:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Till exempel för SAP SCS-instans profil och motsvarande sökväg:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

   Se till att `keepalive` OS-parametrarna är inställda enligt beskrivningen i SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736)för både ENSA1 och ENSA2.  

2. Om du vill tillämpa ändringarna startar du om SAP ASCS/SCS-instansen.

#### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Lägg till en avsöknings port

Använd den interna belastningsutjämnaren för att göra så att hela kluster konfigurationen fungerar med Azure Load Balancer. Azures interna belastningsutjämnare distribuerar vanligt vis den inkommande arbets belastningen lika mellan deltagande virtuella datorer. Detta fungerar dock inte i vissa klusterkonfigurationer eftersom endast en instans är aktiv. Den andra instansen är passiv och kan inte acceptera någon av arbets belastningarna. En avsöknings funktion hjälper när den interna belastnings Utjämnings tjänsten i Azure bara tilldelar arbete till en aktiv instans. Med avsöknings funktionen kan den interna belastningsutjämnaren identifiera vilka instanser som är aktiva och sedan bara rikta in instansen med arbets belastningen.

Så här lägger du till en avsöknings port:

1. Kontrol lera den aktuella inställningen för **ProbePort** genom att köra följande PowerShell-kommando. Kör den inifrån en av de virtuella datorerna i kluster konfigurationen.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. Definiera en avsöknings port. Standard port numret för avsökningen är **0**. I vårt exempel använder vi avsöknings port **62000**.

   ![Bild 58: porten för avsökning av kluster konfiguration är 0 som standard][sap-ha-guide-figure-3048]

   _**Bild 58:** Standard porten för avsökning av kluster konfiguration är 0_

   Port numret definieras i SAP Azure Resource Manager-mallar. Du kan tilldela port numret i PowerShell.

   Kör följande PowerShell-skript för att ange ett nytt ProbePort-värde för **SAP <*sid*> IP-** klusterresursen. Uppdatera PowerShell-variablerna för din miljö. När skriptet har körts uppmanas du att starta om SAP-klustret för att aktivera ändringarna.

   ```PowerShell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   När du har försatt **SAP <*sid* >** -klusterresursen online kontrollerar du att **ProbePort** är inställt på det nya värdet.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![Bild 59: Avsök kluster porten när du har angett det nya värdet][sap-ha-guide-figure-3049]

   _**Bild 59:** Avsök kluster porten när du har angett det nya värdet_

#### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Öppna avsöknings porten för Windows-brandväggen

Du måste öppna en avsöknings port för Windows-brandväggen på båda klusternoderna. Använd följande skript för att öppna en avsöknings port för Windows-brandväggen. Uppdatera PowerShell-variablerna för din miljö.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** har angetts till **62000**. Nu kan du komma åt fil resursen **\\ \ascsha-clsap\sapmnt** från andra värdar, t. ex. från **ascsha-databas administratörer**.

### <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Installera databas instansen

Om du vill installera databas instansen följer du processen som beskrivs i installations dokumentationen för SAP.

### <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Installera den andra klusternoden

Följ anvisningarna i installations guiden för SAP för att installera det andra klustret.

### <a name="change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Ändra start typ för SAP ERS Windows-tjänstinstans

Ändra start typen för tjänsten SAP ERS Windows till **Automatisk (fördröjd start)** på båda klusternoderna.

![Bild 60: ändra tjänst typen för SAP ERS-instansen till fördröjd automatisk][sap-ha-guide-figure-3050]

_**Bild 60:** Ändra tjänst typen för SAP ERS-instansen till fördröjd automatisk_

### <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Installera den primära SAP-programservern

Installera den primära Application Server-instansen (PAS) <*SID*>-di-0 på den virtuella dator som du har angivit som värd för Pas. Det finns inga beroenden för Azure eller DataKeeper inställningar.

### <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Installera den ytterligare SAP-programservern

Installera en SAP-ytterligare program Server (AAS) på alla virtuella datorer som du har angivit för att vara värd för en SAP Application Server-instans. Till exempel på <*sid*>-di-1 till <*sid*>-di- &lt; n &gt; .

> [!NOTE]
> Detta avslutar installationen av ett SAP NetWeaver-system med hög tillgänglighet. Fortsätt sedan med testning av redundans.
>


## <a name="test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testa replikeringen av SAP ASCS/SCS-instansen och SIOS-replikering
Det är enkelt att testa och övervaka en SAP ASCS/SCS-instansen redundans och SIOS genom att använda Klusterhanteraren för växling vid fel och SIOS DataKeeper hantering och konfigurations verktyg.

### <a name="sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> SAP ASCS/SCS-instansen körs på klusternod A

Kluster gruppen **SAP PR1** körs på klusternod A. Till exempel på **PR1-ASCs-0**. Tilldela den delade disk enheten S, som är en del av gruppen **SAP PR1** , och vilken ASCS/SCS-instansen använder, till klusternod A.

![Bild 61: Klusterhanteraren för växling vid fel: <för SAP> kluster gruppen körs på klusternod A][sap-ha-guide-figure-5000]

_**Bild 61:** Klusterhanteraren för växling vid fel *: <för* SAP> kluster gruppen körs på klusternod A_

I hanterings-och konfigurations verktyget för SIOS-DataKeeper kan du se att delade disk data är synkront replikerade från käll volymens enhet S på klusternoden A till mål volym enheterna på klusternod B. Till exempel replikeras det från **PR1-ASCs-0 [10.0.0.40]** till **PR1-ASCs-1 [10.0.0.41]**.

![Bild 62: i SIOS DataKeeper replikerar du den lokala volymen från klusternod A till klusternod B][sap-ha-guide-figure-5001]

_**Bild 62:** Replikera den lokala volymen från klusternod A till klusternod B i SIOS DataKeeper_

### <a name="failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Redundans från nod A till nod B

1. Välj ett av dessa alternativ för att initiera en redundansväxling av SAP <*SID*> kluster gruppen från klusternoden a till klusternod B:
   - Använd Klusterhanteraren för växling vid fel  
   - Använda failover-kluster PowerShell

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Starta om klusternoden A i Windows gäst operativ system (detta initierar en automatisk redundansväxling av SAP <*SID*> kluster gruppen från nod A till nod B).  
3. Starta om klusternod A från Azure Portal (detta initierar en automatisk redundansväxling av *SAP <-*> kluster gruppen från nod A till nod B).  
4. Starta om klusternod A genom att använda Azure PowerShell (detta initierar en automatisk redundansväxling av *SAP <-*> kluster gruppen från nod A till nod B).

   Efter redundansväxlingen körs SAP <*SID*> kluster gruppen på klusternod B. Den körs till exempel på **PR1-ASCs-1**.

   ![Bild 63: i Klusterhanteraren för växling vid fel körs SAP <-> kluster gruppen på klusternod B][sap-ha-guide-figure-5002]

   _**Bild 63**: i KLUSTERHANTERAREN för växling vid fel körs *SAP <-*> kluster gruppen på klusternod B_

   Den delade disken monteras nu på klusternoden B. SIOS DataKeeper replikerar data från käll volymens enhet S på klusternod B till mål volym enheter i kluster nod A. Till exempel replikeras den från **PR1-ASCs-1 [10.0.0.41]** till **PR1-ASCs-0 [10.0.0.40]**.

   ![Bild 64: SIOS DataKeeper replikerar den lokala volymen från klusternod B till klusternod A][sap-ha-guide-figure-5003]

   _**Bild 64:** SIOS DataKeeper replikerar den lokala volymen från klusternod B till klusternod A_
