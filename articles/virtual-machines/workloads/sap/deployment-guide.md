---
title: Distribuzione di Macchine virtuali di Azure per SAP NetWeaver | Microsoft Docs
description: Informazioni su come distribuire il software SAP in macchine virtuali Linux in Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: sedusch
ms.openlocfilehash: ddcf1d05933daadd29bf2f93850ac3299dc9e734
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020116"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Distribuzione di Macchine virtuali di Microsoft Azure per SAP NetWeaver

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
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Distribuzione DBMS di Macchine virtuali di Azure per SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Memorizzazione nella cache per macchine virtuali e dischi rigidi virtuali)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (RAID software)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Archiviazione di Microsoft Azure)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Struttura di una distribuzione RDBMS)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Disponibilità elevata e ripristino di emergenza con le macchine virtuali di Azure)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 e versioni successive)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 e versioni precedenti)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Uso di un'immagine di SQL Server da Azure Marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Riepilogo generale di SQL Server per SAP in Azure)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Specifiche di RDBMS di SQL Server)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Configurazione dell'archiviazione)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Backup e ripristino)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Considerazioni sulle prestazioni per il backup e il ripristino)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Altro)
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

[deployment-guide]:deployment-guide.md (Distribuzione di Macchine virtuali di Azure per SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Risorse SAP)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Distribuzione di una macchina virtuale con un'immagine personalizzata)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scenario 1: Distribuzione di una macchina virtuale da Azure Marketplace per SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scenario 2: Distribuzione di una macchina virtuale con un'immagine personalizzata per SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scenario 3: Spostamento di una macchina virtuale dall'ambiente locale tramite un disco rigido virtuale di Azure non generalizzato con SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Scenari di distribuzione di macchine virtuali per SAP in Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Distribuzione di cmdlet di Azure PowerShell)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Scaricare e importare cmdlet di PowerShell rilevanti per SAP)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Aggiungere una macchina virtuale a un dominio locale (solo per Windows))
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Scaricare, installare e abilitare l'agente di macchine virtuali di Azure)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Interfaccia della riga di comando di Azure)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configurare l'estensione Azure per SAP)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Controllo dello stato di preparazione per l'estensione Azure per SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Controllo dell'integrità della configurazione dell'estensione Azure per SAP)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Risoluzione dei problemi dell'estensione Azure per SAP)

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configurare l'estensione VM)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configurare il proxy)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Controlli e risoluzione dei problemi per la raccolta dati end-to-end per l'agente host SAP)

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

[planning-guide]:planning-guide.md (Pianificazione e implementazione di Macchine virtuali di Azure per SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Risorse)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Disponibilità elevata e ripristino di emergenza per SAP NetWeaver in esecuzione in Macchine virtuali di Azure)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Disponibilità elevata per i server applicazioni SAP)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Uso dell'avvio automatico per le istanze di SAP)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Solo cloud: distribuzioni di Macchine virtuali in Azure senza dipendenze nella rete locale dei clienti)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Cross-premise: distribuzione di una o più macchine virtuali SAP in Azure completamente integrate con la rete locale)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Aree di Azure)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domini di errore)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Domini di aggiornamento)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Set di disponibilità di Azure)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Concetto di macchine virtuali di Microsoft Azure)
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Archiviazione Premium di Azure)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Spostamento di una macchina virtuale dall'ambiente locale ad Azure con un disco non generalizzato)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Distribuzione di una macchina virtuale con un'immagine specifica del cliente)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Preparazione per lo spostamento di una macchina virtuale dall'ambiente locale ad Azure con un disco non generalizzato)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Preparazione per la distribuzione di una macchina virtuale con un'immagine specifica del cliente per SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparazione di macchine virtuali con SAP per Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Differenza tra un disco di Azure e un'immagine di Azure)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Caricamento di un disco rigido virtuale dall'ambiente locale in Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Copia di dischi tra account di archiviazione di Azure)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Struttura di macchine virtuali/dischi rigidi virtuali per le distribuzioni SAP)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Impostazione del montaggio automatico per dischi collegati)
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Scenario di training/dimostrativo di una singola macchina virtuale con SAP NetWeaver)
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Concetti della distribuzione solo cloud di istanze di SAP)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Soluzione di monitoraggio di Azure per SAP)
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Archiviazione Premium di Azure)
[planning-guide-managed-disks]:planning-guide.md#c55b2c6e-3ca1-4476-be16-16c81927550f (Dischi gestiti)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Rete di Microsoft Azure)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Archiviazione: Archiviazione di Microsoft Azure e dischi dati)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (Product Availability Matrix (PAM) SAP)
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
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
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
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Distribuire e gestire le macchine virtuali usando modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Gestire macchine virtuali con Azure Resource Manager e PowerShell)
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
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
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
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Macchine virtuali di Azure è la soluzione ideale per le organizzazioni che necessitano di risorse di calcolo e di archiviazione in tempi minimi e con cicli di approvvigionamento brevi. È possibile usare Macchine virtuali di Azure per distribuire applicazioni classiche, ad esempio applicazioni basate su SAP NetWeaver, in Azure. È possibile estendere l'affidabilità e la disponibilità di un'applicazione senza risorse locali aggiuntive. Macchine virtuali di Azure supporta la connettività cross-premise e quindi è possibile integrare Macchine virtuali di Azure nei domini locali, nei cloud privati e nel panorama applicativo del sistema SAP dell'organizzazione.

In questo articolo viene illustrata la procedura per distribuire applicazioni SAP in macchine virtuali in Azure, incluse le opzioni di distribuzione alternative e la risoluzione dei problemi. Questo articolo si basa sulle informazioni contenute in [Pianificazione e implementazione di Macchine virtuali di Azure per SAP NetWeaver][planning-guide]. Integra anche la documentazione sull'installazione di SAP e le note su SAP, che sono le risorse principali per l'installazione e la distribuzione del software SAP.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

La configurazione di una macchina virtuale di Azure per la distribuzione del software SAP richiede più passaggi e coinvolge diverse risorse. Prima di iniziare, verificare che siano soddisfatti i prerequisiti di installazione del software SAP in macchine virtuali in Azure.

### <a name="local-computer"></a>Computer locale

Per gestire VM Windows o Linux, è possibile usare uno script di PowerShell e il portale di Azure. Per entrambi gli strumenti, è necessario un computer locale che esegue Windows 7 o una versione successiva di Windows. Per gestire solo VM Linux e usare un computer Linux per questa attività, è possibile usare l'interfaccia della riga di comando di Azure.

### <a name="internet-connection"></a>Connessione Internet

Per scaricare ed eseguire gli strumenti e gli script necessari per la distribuzione del software SAP, è necessario essere connessi a Internet. Anche la VM di Azure che esegue l'estensione Azure per SAP deve avere accesso a Internet. Se la VM di Azure fa parte di una rete virtuale di Azure o di un dominio locale, verificare che le impostazioni proxy pertinenti siano configurate come descritto in [Configurare il proxy][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>iscriversi a Microsoft Azure

È necessario un account Azure attivo.

### <a name="topology-and-networking"></a>Topologia e rete

È necessario definire la topologia e l'architettura della distribuzione SAP in Azure:

* Account di archiviazione di Azure da usare
* Rete virtuale in cui si vuole distribuire il sistema SAP
* Gruppo di risorse in cui si vuole distribuire il sistema SAP
* Area di Azure in cui si vuole distribuire il sistema SAP
* Configurazione SAP (due livelli o tre livelli)
* Dimensioni delle macchine virtuali e numero di dischi dati aggiuntivi da montare nelle macchine virtuali
* Configurazione di SAP Correction and Transport System (CTS)

Creare e configurare gli account di archiviazione di Azure, se necessario, o le reti virtuali di Azure prima di iniziare il processo di distribuzione del software SAP. Per informazioni su come creare e configurare queste risorse, vedere [Pianificazione e implementazione di Macchine virtuali di Azure per SAP NetWeaver][planning-guide].

### <a name="sap-sizing"></a>Dimensionamento di SAP

Tenere presenti le informazioni seguenti per dimensionamento di SAP:

* Carico di lavoro SAP previsto, ad esempio, usando lo strumento SAP Quick Sizer e il numero SAP Application Performance Standard (SAPS)
* Utilizzo di memoria e risorse della CPU necessario per il sistema SAP
* Operazioni di input/output (I/O) al secondo necessarie
* Larghezza di banda di rete necessaria per l'eventuale comunicazione tra diverse VM in Azure
* Larghezza di banda di rete necessaria tra gli asset locali e i sistemi SAP distribuiti in Azure

### <a name="resource-groups"></a>Gruppi di risorse

In Azure Resource Manager è possibile usare i gruppi di risorse per gestire tutte le risorse dell'applicazione nella sottoscrizione di Azure. Per altre informazioni, vedere [Panoramica di Azure Resource Manager][resource-group-overview].

## <a name="resources"></a>Risorse

### <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Risorse SAP

Quando si configura la distribuzione del software SAP, sono necessarie le risorse SAP seguenti:

* Nota SAP [1928533], contenente:
  * Elenco delle dimensioni delle VM di Azure supportate per la distribuzione di software SAP
  * Importanti informazioni sulla capacità per le dimensioni delle VM di Azure
  * Software SAP e combinazioni di sistemi operativi e database supportati
  * Versione del kernel SAP richiesta per Windows e Linux in Microsoft Azure

* La nota SAP [2015553] elenca i prerequisiti per le distribuzioni di software SAP supportate da SAP in Azure.
* La nota SAP [2178632] contiene informazioni dettagliate su tutte le metriche di monitoraggio segnalate per SAP in Azure.
* La nota SAP [1409604] contiene la versione dell'agente host SAP per Windows necessaria in Azure.
* La nota SAP [2191498] contiene la versione dell'agente host SAP per Linux necessaria in Azure.
* La nota SAP [2243692] contiene informazioni sulle licenze SAP in Linux in Azure.
* La nota SAP [1984787] contiene informazioni generali su SUSE Linux Enterprise Server 12.
* La nota SAP [2002167] contiene informazioni generali su Red Hat Enterprise Linux 7.x.
* La nota SAP [2069760] contiene informazioni generali su Oracle Linux 7.x.
* La nota SAP [1999351] contiene informazioni aggiuntive sulla risoluzione dei problemi per l'estensione di monitoraggio avanzato di Azure per SAP.
* La nota SAP [1597355] contiene informazioni generali sullo spazio di swapping per Linux.
* Nella [pagina in SCN SAP in Azure](https://wiki.scn.sap.com/wiki/x/Pia7Gg) sono disponibili informazioni e una raccolta di risorse utili.
* [Community WIKI SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contiene tutte le note su SAP necessarie per Linux.
* Cmdlet di PowerShell specifici di SAP inclusi in [Azure PowerShell][azure-ps].
* Comandi dell'interfaccia della riga di comando di Azure specifici di SAP inclusi nell'[interfaccia della riga di comando di Azure][azure-cli].

### <a name="windows-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Risorse di Windows

Questi articoli Microsoft descrivono le distribuzioni SAP in Azure:

* [Guida alla pianificazione e all'implementazione di Macchine virtuali di Azure per SAP NetWeaver][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP NetWeaver (questo articolo)][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP NetWeaver][dbms-guide]

## <a name="deployment-scenarios-for-sap-software-on-azure-vms"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Scenari di distribuzione per il software SAP in VM di Azure

È possibile distribuire le VM e i dischi associati in Azure in diversi modi. È importante conoscere le differenze tra le opzioni di distribuzione, perché i passaggi eseguiti per preparare le VM per la distribuzione saranno diversi a seconda del tipo di distribuzione scelto.

### <a name="scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Scenario 1: Distribuzione di una macchina virtuale da Azure Marketplace per SAP

È possibile usare un'immagine fornita da Microsoft o da terze parti in Azure Marketplace per distribuire la VM. Il Marketplace offre alcune immagini di sistemi operativi standard per Windows Server e diverse distribuzioni Linux. È anche possibile distribuire un'immagine che include SKU di sistema di gestione di database (DBMS), ad esempio, Microsoft SQL Server. Per altre informazioni sull'uso di immagini con SKU DBMS, vedere [Distribuzione DBMS di Macchine virtuali di Azure per SAP NetWeaver][dbms-guide].

Il diagramma di flusso seguente illustra la sequenza di passaggi specifica di SAP per distribuire una VM da Azure Marketplace:

![Diagramma di flusso della distribuzione di una VM per sistemi SAP con un'immagine di VM di Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Creare una macchina virtuale usando il portale di Azure

Il modo più semplice per creare una nuova macchina virtuale con un'immagine di Azure Marketplace consiste nell'usare il portale di Azure.

1.  Passare a <https://portal.azure.com/#create/hub>.  oppure scegliere **+ Nuovo** dal menu del portale di Azure.
1.  Selezionare **Calcolo** e quindi selezionare il tipo di sistema operativo che si vuole distribuire. Ad esempio, Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12) o Red Hat Enterprise Linux 7.2 (RHEL 7.2) o Oracle Linux 7.2. L'elenco predefinito non visualizza tutti i sistemi operativi supportati. Selezionare **Visualizza tutto** per un elenco completo. Per altre informazioni sui sistemi operativi supportati per la distribuzione di software SAP, vedere la nota SAP [1928533].
1.  Nella pagina successiva esaminare le condizioni.
1.  Nella casella **Selezionare un modello di distribuzione** selezionare **Resource Manager**.
1.  Selezionare **Crea**.

La procedura guidata illustra in dettaglio l'impostazione dei parametri necessari per creare la macchina virtuale, oltre a tutte le risorse necessarie, come le interfacce di rete e gli account di archiviazione. Ecco alcuni di questi parametri:

1. **Nozioni di base**:
   * **Name**: nome della risorsa (la macchina virtuale).
   * **Tipo di disco VM**: selezionare il tipo di disco del disco con il sistema operativo. Se si vuole usare l'Archiviazione Premium per i dischi dati, è consigliabile usare l'Archiviazione Premium anche per il disco del sistema operativo.
   * **Nome utente e password** o **Chiave pubblica SSH**: immettere il nome utente e la password dell'utente creato durante il provisioning. Per una macchina virtuale Linux, è possibile immettere la chiave pubblica Secure Shell (SSH) che si usa per accedere al computer.
   * **Sottoscrizione** selezionare la sottoscrizione che si vuole usare per effettuare il provisioning della nuova macchina virtuale.
   * **Gruppo di risorse**: nome del gruppo di risorse della macchina virtuale. È possibile immettere il nome di un nuovo gruppo di risorse o il nome di uno già esistente.
   * **Località**: posizione in cui distribuire la nuova macchina virtuale. Se si vuole connettere la macchina virtuale alla rete locale, verificare di selezionare il percorso della rete virtuale che connette Azure alla rete locale. Per altre informazioni, vedere [Rete di Microsoft Azure][planning-guide-microsoft-azure-networking] in [Pianificazione e implementazione di Macchine virtuali di Azure per SAP NetWeaver][planning-guide].
1. **Size**:

     Per un elenco dei tipi di VM supportati, vedere la nota SAP [1928533]. Assicurarsi di selezionare il tipo di VM corretto se si vuole usare Archiviazione Premium di Azure. Non tutti i tipi di VM supportano Archiviazione Premium. Per altre informazioni, vedere [Archiviazione: Archiviazione di Microsoft Azure e dischi dati][planning-guide-storage-microsoft-azure-storage-and-data-disks] e [Archiviazione Premium di Azure][planning-guide-azure-premium-storage] in [Pianificazione e implementazione di Macchine virtuali di Azure per SAP NetWeaver][planning-guide].

1. **Impostazioni**:
   * **Storage**
     * **Tipo di disco**: selezionare il tipo di disco del disco con il sistema operativo. Se si vuole usare l'Archiviazione Premium per i dischi dati, è consigliabile usare l'Archiviazione Premium anche per il disco del sistema operativo.
     * **Usa il servizio Managed Disks**: se si vuole usare Managed Disks, selezionare Sì. Per altre informazioni su Managed Disks, vedere il capitolo [Managed Disks][planning-guide-managed-disks] nella guida alla pianificazione.
     * **Account di archiviazione**: selezionare un account di archiviazione esistente o crearne uno nuovo. Non tutti i tipi di archiviazione funzionano per l'esecuzione di applicazioni SAP. Per altre informazioni sui tipi di archiviazione, vedere [Struttura delle risorse di archiviazione di una VM per le distribuzioni RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64).
   * **Network**
     * **Rete virtuale** e **Subnet**: per integrare la macchina virtuale con la Intranet, selezionare la rete virtuale connessa alla rete locale.
     * **Indirizzo IP pubblico**: selezionare l'indirizzo IP pubblico che si vuole usare oppure immettere i parametri per crearne uno nuovo. È possibile usare un indirizzo IP pubblico per accedere alla macchina virtuale tramite Internet. Verificare anche di creare un gruppo di sicurezza di rete per proteggere l'accesso alla macchina virtuale.
     * **Network security group** (Gruppo di sicurezza di rete): per altre informazioni, vedere [Controllare il flusso del traffico di rete con i gruppi di sicurezza di rete][virtual-networks-nsg].
   * **Estensioni**: è possibile installare le estensioni macchina virtuale aggiungendole alla distribuzione. Non è necessario aggiungere le estensioni in questo passaggio. Le estensioni necessarie per il supporto SAP vengono installate in un secondo momento. Vedere il capitolo [Configurare l'estensione Azure per SAP][deployment-guide-4.5] in questa guida.
   * **Disponibilità elevata**: selezionare un set di disponibilità oppure immettere i parametri per crearne uno nuovo. Per altre informazioni, vedere [Set di disponibilità di Azure][planning-guide-3.2.3].
   * **Monitoring**
     * **Diagnostica di avvio**: è possibile selezionare **Disabilita** per la diagnostica di avvio.
     * **Diagnostica del sistema operativo guest**: è possibile selezionare **Disabilita** per la diagnostica del monitoraggio.

1. **Riepilogo**:

   Riesaminare le selezioni e quindi fare clic su **OK**.

La macchina virtuale viene distribuita nel gruppo di risorse selezionato.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Creare una macchina virtuale usando un modello

È possibile creare una macchina virtuale usando uno dei modelli SAP pubblicati nel [repository azure-quickstart-templates di GitHub][azure-quickstart-templates-github]. È anche possibile creare una macchina virtuale manualmente usando il [portale di Azure][virtual-machines-windows-tutorial], [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms] o l'[interfaccia della riga di comando di Azure][virtual-machines-linux-tutorial].

* [**Modello per configurazione a due livelli (una sola macchina virtuale)** (sap-2-tier-marketplace-image)][sap-templates-2-tier-marketplace-image]

  Per creare un sistema a due livelli usando una sola macchina virtuale, usare questo modello.
* [**Modello per configurazione a due livelli (una sola macchina virtuale) - Managed Disks** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  Per creare un sistema a due livelli usando una sola macchina virtuale e Managed Disks, usare questo modello.
* [**Modello per configurazione a tre livelli (più macchine virtuali)** (sap-3-tier-marketplace-image)][sap-templates-3-tier-marketplace-image]

  Per creare un sistema a tre livelli usando più macchine virtuali, usare questo modello.
* [**Modello per configurazione a tre livelli (più macchine virtuali) - Managed Disks** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  Per creare un sistema a tre livelli usando più macchine virtuali e Managed Disks, usare questo modello.

Nel portale di Azure immettere i parametri seguenti per il modello:

1. **Nozioni di base**:
   * **Sottoscrizione** sottoscrizione da usare per distribuire il modello.
   * **Gruppo di risorse**: gruppo di risorse da usare per distribuire il modello. È possibile creare un nuovo gruppo di risorse o selezionarne uno esistente nella sottoscrizione.
   * **Località**: posizione in cui distribuire il modello. Se si seleziona un gruppo di risorse esistente, viene usato il percorso di tale gruppo di risorse.

1. **Impostazioni**:
   * **SAP System ID** (ID sistema SAP): ID del sistema SAP (SID).
   * **Tipo di sistema operativo**: sistema operativo che si vuole distribuire, ad esempio Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) o Oracle Linux 7.2.

     L'elenco non visualizza tutti i sistemi operativi supportati. Per altre informazioni sui sistemi operativi supportati per la distribuzione di software SAP, vedere la nota SAP [1928533].
   * **SAP System Size** (Dimensioni sistema SAP): dimensioni del sistema SAP.

     Numero di SAPS forniti dal nuovo sistema. Se non si è certi del numero di SAPS necessari per il sistema, chiedere all'integratore di sistemi o al partner tecnologico SAP.
   * **Disponibilità del sistema** (solo per il modello a tre livelli): disponibilità del sistema.

     Selezionare **HA** per una configurazione adatta a un'installazione a disponibilità elevata. Vengono creati due server di database e due server per ABAP SAP Central Services (ASCS).
   * **Tipo di archiviazione** (solo per il modello a due livelli): tipo di archiviazione da usare.

     Per i sistemi più grandi, è consigliabile usare Archiviazione Premium di Azure. Per altre informazioni sui tipi di archiviazione, vedere queste risorse:
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance][2367194] (Uso dell'archiviazione unità SSD Premium di Azure per l'istanza DBMS di SAP)
      * [Struttura delle risorse di archiviazione di una VM per le distribuzioni RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: High-Performance Storage for Azure Virtual Machine Workloads][storage-premium-storage-preview-portal] (Archiviazione Premium: Archiviazione ad alte prestazioni per i carichi di lavoro delle macchine virtuali di Azure)
      * [Introduzione ad Archiviazione di Microsoft Azure][storage-introduction]
   * **Nome utente amministratore** e **Password amministratore**: nome utente e password.
     Viene creato un nuovo utente, per l'accesso alla macchina virtuale.
   * **New or existing subnet** (Subnet nuova o esistente): determina se devono essere create una nuova rete virtuale e una nuova subnet o se deve essere usata una subnet esistente. Se è già presente una rete virtuale connessa alla rete locale, selezionare **Esistente**.
   * **ID subnet**: Se si vuole distribuire la macchina virtuale in una rete virtuale esistente in cui è stata definita la subnet a cui assegnare la macchina virtuale, specificare l'ID di tale subnet. L'ID in genere è simile al seguente: /subscriptions/&lt;ID sottoscrizione>/resourceGroups/&lt;nome gruppo di risorse>/providers/Microsoft.Network/virtualNetworks/&lt;nome rete virtuale>/subnets/&lt;nome subnet>

1. **Condizioni**:  
    Riesaminare e accettare le note legali.

1. Selezionare **Acquisto**.

Quando si usa un'immagine di Azure Marketplace, per impostazione predefinita viene distribuito l'agente di macchine virtuali di Azure.

#### <a name="configure-proxy-settings"></a>Configurare le impostazioni proxy

A seconda della configurazione della rete locale, potrebbe essere necessario configurare il proxy nella VM. Se la VM è connessa alla rete locale tramite VPN o ExpressRoute, la VM potrebbe non riuscire ad accedere a Internet e non riuscirà a scaricare le estensioni VM necessarie o a raccogliere le informazioni sull'infrastruttura di Azure per l'agente host SAP tramite l'estensione SAP per Azure. Per altre informazioni, vedere [Configurare il proxy][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Aggiungere a un dominio (solo per Windows)

Se la distribuzione di Azure è connessa a un'istanza di DNS o di Active Directory locale tramite una connessione VPN da sito a sito di Azure o ExpressRoute (questa connessione è detta *cross-premise* in [Pianificazione e implementazione di Macchine virtuali di Azure per SAP NetWeaver][planning-guide]), è previsto che la VM sia aggiunta a un dominio locale. Per altre informazioni sulle considerazioni relative a questa attività, vedere [Aggiungere una VM a un dominio locale (solo per Windows)][deployment-guide-4.3].

#### <a name="configure-vm-extension"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Configurare l'estensione VM

Per assicurarsi che SAP supporti l'ambiente, configurare l'estensione Azure per SAP come descritto in [Configurare l'estensione Azure per SAP][deployment-guide-4.5]. Controllare i prerequisiti per SAP e le versioni minime obbligatorie del kernel SAP e dell'agente host SAP nelle risorse elencate in [Risorse SAP][deployment-guide-2.2].

#### <a name="vm-extension-for-sap-check"></a>Controllo dell'estensione della macchina virtuale per SAP

Controllare se l'estensione della macchina virtuale per SAP funziona, come descritto in [Controlli e risoluzione dei problemi per la raccolta dati end-to-end per l'agente host SAP][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Passaggi post-distribuzione

Dopo la creazione e la distribuzione della VM, è necessario installare i componenti software necessari nella VM. Data la sequenza di distribuzione/installazione del software in questo tipo di distribuzione di VM, il software da installare deve essere già disponibile in Azure in un'altra VM o come disco collegabile oppure considerare la possibilità di usare uno scenario cross-premise, in cui la connettività agli asset locali (condivisioni di installazione) è data per scontata.

Dopo aver distribuito la VM in Azure, installare il software SAP nella VM con le stesse linee guida e gli stessi strumenti usati in un ambiente locale. Per installare il software SAP in una VM di Azure, sia SAP che Microsoft consigliano di caricare e archiviare il supporto di installazione SAP in dischi rigidi virtuali di Azure o Managed Disks oppure di creare una VM di Azure che funge da file server contenente tutti i supporti di installazione SAP necessari.

### <a name="scenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Scenario 2: Distribuzione di una macchina virtuale con un'immagine personalizzata per SAP

Poiché le diverse versioni di un sistema operativo o DBMS richiedono patch diverse, le immagini trovare in Azure Marketplace potrebbero non soddisfare tutte le esigenze. È consigliabile invece creare una VM usando la propria immagine di VM del sistema operativo/DBMS, che sarà possibile distribuire di nuovo in un secondo momento.
La procedura per creare un'immagine privata per Linux è diversa da quella per crearne una per Windows.

---
> ![Windows][Logo_Windows] Windows
>
> Per preparare un'immagine Windows da poter usare per distribuire più macchine virtuali, è necessario eseguire l'astrazione o la generalizzazione delle impostazioni di Windows (come il nome host e il SID di Windows) nella VM locale. A tale scopo, è possibile usare [sysprep](https://msdn.microsoft.com/library/hh825084.aspx).
>
> ![Linux][Logo_Linux] Linux
>
> Per preparare un'immagine Linux da poter usare per distribuire più macchine virtuali, è necessario eseguire l'astrazione o la generalizzazione di alcune impostazioni di Linux nella VM locale. A tale scopo, è possibile usare `waagent -deprovision`. Per altre informazioni, vedere [Acquisire una macchina virtuale Linux in esecuzione in Azure][virtual-machines-linux-capture-image] e [Guida dell'utente dell'agente Linux di Azure][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

---
È possibile preparare e creare un'immagine personalizzata e quindi usarla per creare più VM nuove, come illustrato in [Pianificazione e implementazione di Macchine virtuali di Azure per SAP NetWeaver][planning-guide]. Configurare il contenuto del database usando SAP Software Provisioning Manager per installare un nuovo sistema SAP (che ripristina un backup del database da un disco collegato alla macchina virtuale) o ripristinando un backup del database direttamente da Archiviazione di Azure, se l'operazione è supportata dal sistema DBMS. Per altre informazioni, vedere [Distribuzione DBMS di Macchine virtuali di Azure per SAP NetWeaver][dbms-guide]. Se è già stato installato un sistema SAP nella macchina virtuale locale (soprattutto per sistemi a due livelli), è possibile adattare le impostazioni del sistema SAP dopo la distribuzione della macchina virtuale di Azure usando la procedura di ridenominazione del sistema supportata da SAP Software Provisioning Manager (nota SAP [1619720]). In caso contrario, è possibile installare il software SAP dopo la distribuzione della VM di Azure.

Il diagramma di flusso seguente illustra la sequenza di passaggi specifica di SAP per distribuire una VM da un'immagine personalizzata:

![Diagramma di flusso della distribuzione di una VM per sistemi SAP con un'immagine di VM di marketplace privato][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Creare una macchina virtuale usando il portale di Azure

Il modo più semplice per creare una nuova macchina virtuale da un'immagine di disco gestito consiste nell'usare il portale di Azure. Per altre informazioni su come creare un'immagine di disco gestito, leggere [Creare un'immagine gestita di una macchina virtuale generalizzata in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)

1.  Passare a <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. In alternativa scegliere **Immagini** dal menu del portale di Azure.
1.  Selezionare l'immagine del disco gestito che si vuole distribuire, fare clic su **Crea macchina virtuale**

La procedura guidata illustra in dettaglio l'impostazione dei parametri necessari per creare la macchina virtuale, oltre a tutte le risorse necessarie, come le interfacce di rete e gli account di archiviazione. Ecco alcuni di questi parametri:

1. **Nozioni di base**:
   * **Name**: nome della risorsa (la macchina virtuale).
   * **Tipo di disco VM**: selezionare il tipo di disco del disco con il sistema operativo. Se si vuole usare l'Archiviazione Premium per i dischi dati, è consigliabile usare l'Archiviazione Premium anche per il disco del sistema operativo.
   * **Nome utente e password** o **Chiave pubblica SSH**: immettere il nome utente e la password dell'utente creato durante il provisioning. Per una macchina virtuale Linux, è possibile immettere la chiave pubblica Secure Shell (SSH) che si usa per accedere al computer.
   * **Sottoscrizione** selezionare la sottoscrizione che si vuole usare per effettuare il provisioning della nuova macchina virtuale.
   * **Gruppo di risorse**: nome del gruppo di risorse della macchina virtuale. È possibile immettere il nome di un nuovo gruppo di risorse o il nome di uno già esistente.
   * **Località**: posizione in cui distribuire la nuova macchina virtuale. Se si vuole connettere la macchina virtuale alla rete locale, verificare di selezionare il percorso della rete virtuale che connette Azure alla rete locale. Per altre informazioni, vedere [Rete di Microsoft Azure][planning-guide-microsoft-azure-networking] in [Pianificazione e implementazione di Macchine virtuali di Azure per SAP NetWeaver][planning-guide].
1. **Size**:

     Per un elenco dei tipi di VM supportati, vedere la nota SAP [1928533]. Assicurarsi di selezionare il tipo di VM corretto se si vuole usare Archiviazione Premium di Azure. Non tutti i tipi di VM supportano Archiviazione Premium. Per altre informazioni, vedere [Archiviazione: Archiviazione di Microsoft Azure e dischi dati][planning-guide-storage-microsoft-azure-storage-and-data-disks] e [Archiviazione Premium di Azure][planning-guide-azure-premium-storage] in [Pianificazione e implementazione di Macchine virtuali di Azure per SAP NetWeaver][planning-guide].

1. **Impostazioni**:
   * **Storage**
     * **Tipo di disco**: selezionare il tipo di disco del disco con il sistema operativo. Se si vuole usare l'Archiviazione Premium per i dischi dati, è consigliabile usare l'Archiviazione Premium anche per il disco del sistema operativo.
     * **Usa il servizio Managed Disks**: se si vuole usare Managed Disks, selezionare Sì. Per altre informazioni su Managed Disks, vedere il capitolo [Managed Disks][planning-guide-managed-disks] nella guida alla pianificazione.
   * **Network**
     * **Rete virtuale** e **Subnet**: per integrare la macchina virtuale con la Intranet, selezionare la rete virtuale connessa alla rete locale.
     * **Indirizzo IP pubblico**: selezionare l'indirizzo IP pubblico che si vuole usare oppure immettere i parametri per crearne uno nuovo. È possibile usare un indirizzo IP pubblico per accedere alla macchina virtuale tramite Internet. Verificare anche di creare un gruppo di sicurezza di rete per proteggere l'accesso alla macchina virtuale.
     * **Network security group** (Gruppo di sicurezza di rete): per altre informazioni, vedere [Controllare il flusso del traffico di rete con i gruppi di sicurezza di rete][virtual-networks-nsg].
   * **Estensioni**: è possibile installare le estensioni macchina virtuale aggiungendole alla distribuzione. Non è necessario aggiungere l'estensione in questo passaggio. Le estensioni necessarie per il supporto SAP vengono installate in un secondo momento. Vedere il capitolo [Configurare l'estensione Azure per SAP][deployment-guide-4.5] in questa guida.
   * **Disponibilità elevata**: selezionare un set di disponibilità oppure immettere i parametri per crearne uno nuovo. Per altre informazioni, vedere [Set di disponibilità di Azure][planning-guide-3.2.3].
   * **Monitoring**
     * **Diagnostica di avvio**: è possibile selezionare **Disabilita** per la diagnostica di avvio.
     * **Diagnostica del sistema operativo guest**: è possibile selezionare **Disabilita** per la diagnostica del monitoraggio.

1. **Riepilogo**:

   Riesaminare le selezioni e quindi fare clic su **OK**.

La macchina virtuale viene distribuita nel gruppo di risorse selezionato.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Creare una macchina virtuale usando un modello

Per creare una distribuzione usando un'immagine del sistema operativo privata dal portale di Azure, usare uno dei modelli SAP seguenti. Questi modelli sono pubblicati nel [repository di GitHub azure-quickstart-templates][azure-quickstart-templates-github]. È anche possibile creare una macchina virtuale manualmente usando [PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Modello per configurazione a due livelli (una sola macchina virtuale)** (sap-2-tier-user-image)][sap-templates-2-tier-user-image]

  Per creare un sistema a due livelli usando una sola macchina virtuale, usare questo modello.
* [**Modello per configurazione a due livelli (una sola macchina virtuale) - Immagine di disco gestito** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  Per creare un sistema a due livelli usando una sola macchina virtuale e un'immagine di disco gestito, usare questo modello.
* [**Modello per configurazione a tre livelli (più macchine virtuali)** (sap-3-tier-user-image)][sap-templates-3-tier-user-image]

  Per creare un sistema a tre livelli usando più macchine virtuali o l'immagine del sistema operativo, usare questo modello.
* [**Modello per configurazione a tre livelli (più macchine virtuali) - Immagine di disco gestito** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  Per creare un sistema a tre livelli usando più macchine virtuali o l'immagine del sistema operativo e un'immagine di disco gestito, usare questo modello.

Nel portale di Azure immettere i parametri seguenti per il modello:

1. **Nozioni di base**:
   * **Sottoscrizione** sottoscrizione da usare per distribuire il modello.
   * **Gruppo di risorse**: gruppo di risorse da usare per distribuire il modello. È possibile creare un nuovo gruppo di risorse o selezionarne uno esistente nella sottoscrizione.
   * **Località**: posizione in cui distribuire il modello. Se si seleziona un gruppo di risorse esistente, viene usato il percorso di tale gruppo di risorse.
1. **Impostazioni**:
   * **SAP System ID** (ID sistema SAP): ID del sistema SAP.
   * **Tipo di sistema operativo**: tipo di sistema operativo da distribuire (Windows o Linux).
   * **SAP System Size** (Dimensioni sistema SAP): dimensioni del sistema SAP.

     Numero di SAPS forniti dal nuovo sistema. Se non si è certi del numero di SAPS necessari per il sistema, chiedere all'integratore di sistemi o al partner tecnologico SAP.
   * **Disponibilità del sistema** (solo per il modello a tre livelli): disponibilità del sistema.

     Selezionare **HA** per una configurazione adatta a un'installazione a disponibilità elevata. Vengono creati due server di database e due server per ASCS.
   * **Tipo di archiviazione** (solo per il modello a due livelli): tipo di archiviazione da usare.

     Per i sistemi più grandi, è consigliabile usare Archiviazione Premium di Azure. Per altre informazioni sui tipi di archiviazione, vedere le risorse seguenti:
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance][2367194] (Uso dell'archiviazione unità SSD Premium di Azure per l'istanza DBMS di SAP)
      * [Struttura delle risorse di archiviazione di una VM per le distribuzioni RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: archiviazione ad alte prestazioni per i carichi di lavoro delle macchine virtuali di Azure][storage-premium-storage-preview-portal]
      * [Introduzione ad Archiviazione di Microsoft Azure][storage-introduction]
   * **User image VHD URI** (URI VHD dell'immagine utente - solo per il modello di immagine del disco non gestito): URI del disco rigido virtuale dell'immagine del sistema operativo privata, ad esempio https://&lt;nomeaccount>.blob.core.windows.net/vhds/userimage.vhd.
   * **User image storage account** (Account di archiviazione dell'immagine utente - solo per il modello di immagine del disco non gestito): nome dell'account di archiviazione in cui è archiviata l'immagine privata del sistema operativo, ad esempio &lt;nomeaccount> in https://&lt;nomeaccount>.blob.core.windows.net/vhds/userimage.vhd.
   * **userImageId** (solo per il modello di immagine del disco gestito): ID dell'immagine del disco gestito da usare.
   * **Nome utente amministratore** e **Password amministratore**: nome utente e password.

     Viene creato un nuovo utente, per l'accesso alla macchina virtuale.
   * **New or existing subnet** (Subnet nuova o esistente): determina se devono essere create una nuova rete virtuale e una nuova subnet o se deve essere usata una subnet esistente. Se è già presente una rete virtuale connessa alla rete locale, selezionare **Esistente**.
   * **ID subnet**: Se si vuole distribuire la macchina virtuale in una rete virtuale esistente in cui è stata definita la subnet a cui assegnare la macchina virtuale, specificare l'ID di tale subnet. L'ID in genere è simile al seguente: /subscriptions/&lt;ID sottoscrizione>/resourceGroups/&lt;nome gruppo di risorse>/providers/Microsoft.Network/virtualNetworks/&lt;nome rete virtuale>/subnets/&lt;nome subnet>

1. **Condizioni**:  
    Riesaminare e accettare le note legali.

1. Selezionare **Acquisto**.

#### <a name="install-the-vm-agent-linux-only"></a>Installare l'agente di macchine virtuali (solo per Linux)

Per usare i modelli descritti nella sezione precedente, l'agente Linux deve essere già installato nell'immagine utente o la distribuzione avrà esito negativo. Scaricare e installare l'agente di macchine virtuali nell'immagine utente come descritto in [Scaricare, installare e abilitare l'agente di macchine virtuali di Azure][deployment-guide-4.4]. Se non si usano i modelli, si può anche installare l'agente di macchine virtuali in un secondo momento.

#### <a name="join-a-domain-windows-only"></a>Aggiungere a un dominio (solo per Windows)

Se la distribuzione di Azure è connessa a un'istanza di DNS o di Active Directory locale tramite una connessione VPN da sito a sito di Azure o Azure ExpressRoute (questa connessione è detta *cross-premise* in [Pianificazione e implementazione di Macchine virtuali di Azure per SAP NetWeaver][planning-guide]), è previsto che la VM sia aggiunta a un dominio locale. Per altre informazioni sulle considerazioni relative a questo passaggio, vedere [Aggiungere una VM a un dominio locale (solo per Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Configurare le impostazioni proxy

A seconda della configurazione della rete locale, potrebbe essere necessario configurare il proxy nella VM. Se la VM è connessa alla rete locale tramite VPN o ExpressRoute, la VM potrebbe non riuscire ad accedere a Internet e non riuscirà a scaricare le estensioni VM necessarie o a raccogliere le informazioni sull'infrastruttura di Azure per l'agente host SAP tramite l'estensione SAP per Azure, vedere [Configurare il proxy][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Configurare l'estensione VM di Azure per SAP

Per assicurarsi che SAP supporti l'ambiente, configurare l'estensione Azure per SAP come descritto in [Configurare l'estensione Azure per SAP][deployment-guide-4.5]. Controllare i prerequisiti per SAP e le versioni minime obbligatorie del kernel SAP e dell'agente host SAP nelle risorse elencate in [Risorse SAP][deployment-guide-2.2].

#### <a name="sap-vm-extension-check"></a>Verifica dell'estensione VM per SAP

Controllare se l'estensione della macchina virtuale per SAP funziona, come descritto in [Controlli e risoluzione dei problemi per la raccolta dati end-to-end per l'agente host SAP][deployment-guide-troubleshooting-chapter].


### <a name="scenario-3-moving-an-on-premises-vm-by-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Scenario 3: Spostamento di una macchina virtuale dall'ambiente locale tramite un disco rigido virtuale di Azure non generalizzato con SAP

In questo scenario si pianifica lo spostamento di un sistema SAP specifico da un ambiente locale ad Azure. È possibile eseguire questa operazione caricando in Azure il disco rigido virtuale contenente il sistema operativo, i file binari SAP e infine i file binari DBMS, nonché i dischi rigidi virtuali con i file di dati e di log del sistema DBMS. A differenza dello scenario descritto in [Scenario 2: Distribuzione di una macchina virtuale con un'immagine personalizzata per SAP][deployment-guide-3.3], in questo caso il nome host, il SID SAP e gli account utente SAP nella macchina virtuale di Azure vengono mantenuti perché sono stati configurati nell'ambiente locale. Non è necessario generalizzare il sistema operativo. Questo scenario si applica molto spesso agli scenari cross-premise in cui parte del panorama applicativo SAP viene eseguita in locale e parte in Azure.

In questo scenario l'agente di macchine virtuali **non** viene installato automaticamente durante la distribuzione. Poiché l'agente di macchine virtuali e l'estensione Azure per SAP sono necessari per eseguire SAP NetWeaver in Azure, è necessario scaricare, installare e abilitare entrambi i componenti manualmente dopo avere creato la macchina virtuale.

Per altre informazioni sull'agente di macchine virtuali di Azure, vedere le risorse seguenti.

---
> ![Windows][Logo_Windows] Windows
>
> [Panoramica dell'agente di macchine virtuali di Azure][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Guida dell'utente dell'agente Linux di Azure][virtual-machines-linux-agent-user-guide]
>
>

---

Il diagramma di flusso seguente illustra la sequenza di passaggi per spostare una VM locale usando un disco rigido virtuale di Azure non generalizzato:

![Diagramma di flusso della distribuzione di una VM per sistemi SAP con un disco della VM][deployment-guide-figure-400]

Se il disco è già caricato e definito in Azure (vedere [Pianificazione e implementazione di Macchine virtuali di Azure per SAP NetWeaver][planning-guide]), eseguire le attività descritte nelle sezioni successive.

#### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Per creare una distribuzione usando un disco del sistema operativo privato tramite il portale di Azure, usare il modello SAP pubblicato nel [repository azure-quickstart-templates di GitHub][azure-quickstart-templates-github]. È anche possibile creare una macchina virtuale manualmente usando PowerShell.

* [**Modello per configurazione a due livelli (una sola macchina virtuale)** (sap-2-tier-user-disk)][sap-templates-2-tier-os-disk]

  Per creare un sistema a due livelli usando una sola macchina virtuale, usare questo modello.
* [**Modello per configurazione a due livelli (una sola macchina virtuale) - Disco gestito** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  Per creare un sistema a due livelli usando una sola macchina virtuale e un disco gestito, usare questo modello.

Nel portale di Azure immettere i parametri seguenti per il modello:

1. **Nozioni di base**:
   * **Sottoscrizione** sottoscrizione da usare per distribuire il modello.
   * **Gruppo di risorse**: gruppo di risorse da usare per distribuire il modello. È possibile creare un nuovo gruppo di risorse o selezionarne uno esistente nella sottoscrizione.
   * **Località**: posizione in cui distribuire il modello. Se si seleziona un gruppo di risorse esistente, viene usato il percorso di tale gruppo di risorse.
1. **Impostazioni**:
   * **SAP System ID** (ID sistema SAP): ID del sistema SAP.
   * **Tipo di sistema operativo**: tipo di sistema operativo da distribuire (Windows o Linux).
   * **SAP System Size** (Dimensioni sistema SAP): dimensioni del sistema SAP.

     Numero di SAPS forniti dal nuovo sistema. Se non si è certi del numero di SAPS necessari per il sistema, chiedere all'integratore di sistemi o al partner tecnologico SAP.
   * **Tipo di archiviazione** (solo per il modello a due livelli): tipo di archiviazione da usare.

     Per i sistemi più grandi, è consigliabile usare Archiviazione Premium di Azure. Per altre informazioni sui tipi di archiviazione, vedere le risorse seguenti:
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance][2367194] (Uso dell'archiviazione unità SSD Premium di Azure per l'istanza DBMS di SAP)
      * [Struttura delle risorse di archiviazione di una VM per le distribuzioni RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: High-Performance Storage for Azure Virtual Machine Workloads][storage-premium-storage-preview-portal] (Archiviazione Premium: Archiviazione ad alte prestazioni per i carichi di lavoro delle macchine virtuali di Azure)
      * [Introduzione ad Archiviazione di Microsoft Azure][storage-introduction]
   * **OS disk VHD URI** (URI VHD del disco del sistema operativo - solo per il modello di disco non gestito): URI del disco del sistema operativo privato, ad esempio https://&lt;nomeaccount>.blob.core.windows.net/vhds/osdisk.vhd.
   * **OS disk Managed Disk Id** (ID disco gestito su disco sistema operativo - solo per il modello di disco gestito): ID del disco gestito del disco del sistema operativo, ad esempio /subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN
   * **New or existing subnet** (Subnet nuova o esistente): determina se devono essere create una nuova rete virtuale e una nuova subnet o se deve essere usata una subnet esistente. Se è già presente una rete virtuale connessa alla rete locale, selezionare **Esistente**.
   * **ID subnet**: Se si vuole distribuire la macchina virtuale in una rete virtuale esistente in cui è stata definita la subnet a cui assegnare la macchina virtuale, specificare l'ID di tale subnet. L'ID in genere è simile al seguente: /subscriptions/&lt;ID sottoscrizione>/resourceGroups/&lt;nome gruppo di risorse>/providers/Microsoft.Network/virtualNetworks/&lt;nome rete virtuale>/subnets/&lt;nome subnet>

1. **Condizioni**:  
    Riesaminare e accettare le note legali.

1. Selezionare **Acquisto**.

#### <a name="install-the-vm-agent"></a>Installare l'agente di macchine virtuali

Per usare i modelli descritti nella sezione precedente, l'agente di macchine virtuali deve essere installato nel disco del sistema operativo o la distribuzione avrà esito negativo. Scaricare e installare l'agente di macchine virtuali nella VM come descritto in [Scaricare, installare e abilitare l'agente di macchine virtuali di Azure][deployment-guide-4.4].

Se non si usano i modelli descritti nella sezione precedente, si può anche installare l'agente di macchine virtuali in un secondo momento.

#### <a name="join-a-domain-windows-only"></a>Aggiungere a un dominio (solo per Windows)

Se la distribuzione di Azure è connessa a un'istanza di DNS o di Active Directory locale tramite una connessione VPN da sito a sito di Azure o ExpressRoute (questa connessione è detta *cross-premise* in [Pianificazione e implementazione di Macchine virtuali di Azure per SAP NetWeaver][planning-guide]), è previsto che la VM sia aggiunta a un dominio locale. Per altre informazioni sulle considerazioni relative a questa attività, vedere [Aggiungere una VM a un dominio locale (solo per Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Configurare le impostazioni proxy

A seconda della configurazione della rete locale, potrebbe essere necessario configurare il proxy nella VM. Se la VM è connessa alla rete locale tramite VPN o ExpressRoute, la VM potrebbe non riuscire ad accedere a Internet e non riuscirà a scaricare le estensioni VM necessarie o a raccogliere le informazioni sull'infrastruttura di Azure per l'agente host SAP tramite l'estensione SAP per Azure, vedere [Configurare il proxy][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Configurare l'estensione VM di Azure per SAP

Per assicurarsi che SAP supporti l'ambiente, configurare l'estensione Azure per SAP come descritto in [Configurare l'estensione Azure per SAP][deployment-guide-4.5]. Controllare i prerequisiti per SAP e le versioni minime obbligatorie del kernel SAP e dell'agente host SAP nelle risorse elencate in [Risorse SAP][deployment-guide-2.2].

#### <a name="sap-vm-check"></a>Controllo VM per SAP

Controllare se l'estensione della macchina virtuale per SAP funziona, come descritto in [Controlli e risoluzione dei problemi per la raccolta dati end-to-end per l'agente host SAP][deployment-guide-troubleshooting-chapter].

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>Aggiornare la configurazione dell'estensione Azure per SAP

Aggiornare la configurazione dell'estensione Azure per SAP in uno degli scenari seguenti:
* Il team congiunto Microsoft/SAP estende le funzionalità dell'estensione VM e richiede più o meno contatori.
* Microsoft introduce una nuova versione dell'infrastruttura di Azure sottostante che fornisce i dati e l'estensione Azure per SAP deve essere adattata a tali modifiche.
* Si montano altri dischi dati nella macchina virtuale di Azure o si rimuove un disco dati. In questo scenario aggiornare la raccolta dei dati correlati all'archiviazione. Se si modifica la configurazione aggiungendo o eliminando endpoint o assegnando un indirizzo IP a una VM, ciò non influisce sulla configurazione dell'estensione.
* Si modificano le dimensioni della macchina virtuale di Azure, ad esempio dalla dimensione A5 a qualsiasi altra dimensione di macchina virtuale.
* Si aggiungono nuove interfacce di rete alla VM di Azure.

Per aggiornare le impostazioni, aggiornare la configurazione dell'estensione Azure per SAP seguendo la procedura descritta in [Configurare l'estensione Azure per SAP][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>Attività dettagliate per la distribuzione del software SAP

Questa sezione contiene i passaggi dettagliati per eseguire attività specifiche durante il processo di configurazione e distribuzione.

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Distribuire i cmdlet di Azure PowerShell

1. Andare alla pagina [Download di Microsoft Azure](https://azure.microsoft.com/downloads/).
1. In **Strumenti da riga di comando**, in **PowerShell** selezionare **Windows - Installazione**.
1. Nella finestra di dialogo Microsoft Download Manager, per il file scaricato (ad esempio, WindowsAzurePowershellGet.3f.3f.3fnew.exe), selezionare **Esegui**.
1. Per eseguire l'Installazione guidata piattaforma Web Microsoft, selezionare **Sì**.
1. Viene visualizzata una pagina simile a questa:

   ![Pagina di installazione dei cmdlet di Azure PowerShell][deployment-guide-figure-500]<a name="figure-5"></a>

1. Selezionare **Installa** e quindi accettare le Condizioni di licenza software Microsoft.
1. Azure PowerShell viene installato. Selezionare **Fine** per chiudere l'installazione guidata.

Controllare spesso la disponibilità di aggiornamenti per i cmdlet di PowerShell che in genere vengono aggiornati ogni mese. Il modo più semplice per verificare la disponibilità di aggiornamenti consiste nell'eseguire i passaggi di installazione precedenti fino alla pagina di installazione illustrata nel passaggio 5. La data di uscita e il numero di versione dei cmdlet sono inclusi nella pagina illustrata nel passaggio 5. Se non diversamente indicato nella nota SAP [1928533] o nella nota SAP [2015553], è consigliabile usare la versione più recente dei cmdlet di Azure PowerShell.

Per controllare la versione dei cmdlet di Azure PowerShell installati nel computer, eseguire questo comando di PowerShell:

```powershell
(Get-Module Az.Compute).Version
```

Il risultato è simile al seguente:

![Risultato del controllo della versione dei cmdlet di Azure PowerShell][deployment-guide-figure-600]
<a name="figure-6"></a>

Se la versione dei cmdlet di Azure installata nel computer è la versione corrente, viene indicato nella prima pagina dell'installazione guidata dall'aggiunta di **(Installed)** (Installato) al titolo del prodotto. Vedere lo screenshot seguente. I cmdlet di Azure PowerShell vengono aggiornati. Per chiudere l'installazione guidata, selezionare **Esci**.

![Pagina di installazione dei cmdlet di Azure PowerShell che indica che è installata la versione più recente dei cmdlet di Azure PowerShell][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Distribuire l'interfaccia della riga di comando di Azure

1. Andare alla pagina [Download di Microsoft Azure](https://azure.microsoft.com/downloads/).
1. In **Strumenti da riga di comando**, in **Interfaccia della riga di comando di Azure** selezionare il collegamento **Installazione** per il sistema operativo in uso.
1. Nella finestra di dialogo Microsoft Download Manager, per il file scaricato (ad esempio, WindowsAzureXPlatCLI.3f.3f.3fnew.exe), selezionare **Esegui**.
1. Per eseguire l'Installazione guidata piattaforma Web Microsoft, selezionare **Sì**.
1. Viene visualizzata una pagina simile a questa:

   ![Pagina di installazione dei cmdlet di Azure PowerShell][deployment-guide-figure-500]<a name="figure-5"></a>

1. Selezionare **Installa** e quindi accettare le Condizioni di licenza software Microsoft.
1. L'interfaccia della riga di comando di Azure viene installata. Selezionare **Fine** per chiudere l'installazione guidata.

Verificare spesso la disponibilità di aggiornamenti per l'interfaccia della riga di comando di Azure, che in genere viene aggiornata ogni mese. Il modo più semplice per verificare la disponibilità di aggiornamenti consiste nell'eseguire i passaggi di installazione precedenti fino alla pagina di installazione illustrata nel passaggio 5.

Per controllare la versione dell'interfaccia della riga di comando di Azure installata nel computer, eseguire questo comando:

```console
azure --version
```

Il risultato è simile al seguente:

![Risultato del controllo della versione dell'interfaccia della riga di comando di Azure][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="join-a-vm-to-an-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Aggiungere una VM a un dominio locale (solo per Windows)

Se si distribuiscono VM SAP in uno scenario cross-premise in cui Active Directory e DNS locali sono estesi in Azure, è previsto che le VM vengano aggiunte a un dominio locale. La procedura dettagliata eseguita per aggiungere una VM a un dominio locale e il software necessario per fare parte di un dominio locale variano a seconda del cliente. In genere, per aggiungere una VM a un dominio locale, è necessario installare altro software, ad esempio software antimalware e software per il backup o il monitoraggio.

In questo scenario, è anche necessario verificare che, se vengono forzate impostazioni proxy Internet quando una VM viene aggiunta a un dominio nell'ambiente in uso, l'account di sistema locale di Windows (S-1-5-18) nella VM guest abbia le stesse impostazioni proxy. L'opzione più semplice consiste nel forzare il proxy usando i Criteri di gruppo di un dominio che si applicano ai sistemi nel dominio.

### <a name="download-install-and-enable-the-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Scaricare, installare e abilitare l'agente di macchine virtuali di Azure

Per le macchine virtuali distribuite da un'immagine del sistema operativo non generalizzata (ad esempio un'immagine non originata nell'Utilità preparazione sistema, o sysprep), è necessario scaricare, installare e abilitare manualmente l'agente di macchine virtuali di Azure.

Se si distribuisce una VM da Azure Marketplace, questo passaggio non è obbligatorio. Le immagini di Azure Marketplace hanno già l'agente di macchine virtuali di Azure.

#### <a name="windows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Scaricare l'agente di macchine virtuali di Azure:
   1.  Scaricare il [pacchetto di installazione dell'agente di macchine virtuali di Azure](https://go.microsoft.com/fwlink/?LinkId=394789).
   1.  Archiviare il pacchetto MSI dell'agente di macchine virtuali in locale in un personal computer o in un server.
1. Installare l'agente di macchine virtuali di Azure:
   1.  Connettersi alla VM di Azure distribuita usando Remote Desktop Protocol (RDP).
   1.  Aprire una finestra di Esplora risorse nella macchina virtuale e quindi selezionare la directory di destinazione per il file MSI dell'agente di macchine virtuali.
   1.  Trascinare il file MSI di installazione dell'agente di macchine virtuali di Azure dal server/computer locale alla directory di destinazione dell'agente di macchine virtuali nella VM.
   1.  Fare doppio clic sul file MSI nella macchina virtuale.
1. Per le macchine virtuali aggiunte a domini locali, verificare che le eventuali impostazioni di proxy Internet si applichino anche all'account di sistema locale di Windows (S-1-5-18) nella macchina virtuale, come descritto in [Configurare il proxy][deployment-guide-configure-proxy]. L'agente di macchine virtuali viene eseguito in questo contesto e deve potersi connettere ad Azure.

Non è necessario l'intervento dell'utente per aggiornare l'agente di macchine virtuali di Azure. L'agente di macchine virtuali viene aggiornato automaticamente e non è necessario riavviare la VM.

#### <a name="linux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

Usare i comandi seguenti per installare l'agente di macchine virtuali per Linux:

* **SUSE Linux Enterprise Server (SLES)**

  ```console
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) o Oracle Linux**

  ```console
  sudo yum install WALinuxAgent
  ```

Se l'agente è già installato, per aggiornare l'Agente Linux di Azure, eseguire i passaggi descritti in [Aggiornare l'agente Linux di Azure in una macchina virtuale alla versione più recente da GitHub][virtual-machines-linux-update-agent].

### <a name="configure-the-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Configurare il proxy

I passaggi eseguiti per configurare il proxy in Windows sono diversi da quelli necessari per configurare il proxy in Linux.

#### <a name="windows"></a>Windows

Le impostazioni proxy devono essere configurate correttamente perché l'account di sistema locale possa accedere a Internet. Se le impostazioni proxy non sono impostate con Criteri di gruppo, si possono configurare per l'account di sistema locale.

1. Fare clic sul pulsante **Start**, immettere **gpedit.msc** e quindi premere **INVIO**.
1. Selezionare **Configurazione computer** > **Modelli amministrativi** > **Componenti di Windows** > **Internet Explorer**. Verificare che l'impostazione **Basa impostazioni proxy sul computer (non sull'utente)** sia disabilitata o non configurata.
1. Nel **Pannello di controllo** andare a **Centro connessioni di rete e condivisione** > **Opzioni Internet**.
1. Nella scheda **Connessioni** selezionare il pulsante **Impostazioni LAN**.
1. Deselezionare la casella di controllo **Rileva automaticamente impostazioni**.
1. Selezionare la casella di controllo **Usa un server di proxy per la rete LAN** e quindi immettere porta e indirizzo del proxy.
1. Selezionare il pulsante **Advanced** (Avanzate).
1. Nella casella **Eccezioni** immettere l'indirizzo IP **168.63.129.16**. Selezionare **OK**.

#### <a name="linux"></a>Linux

Configurare il proxy corretto nel file di configurazione dell'agente guest di Microsoft Azure, disponibile in \\etc\\waagent.conf.

Impostare i parametri seguenti:

1. **Host proxy HTTP**. Impostarlo, ad esempio, su **proxy.corp.local**.

   ```console
   HttpProxy.Host=<proxy host>

   ```

1. **Porta proxy HTTP**. Impostarla, ad esempio, su **80**.

   ```console
   HttpProxy.Port=<port of the proxy host>

   ```

1. Riavviare l'agente.

   ```console
   sudo service waagent restart
   ```

Le impostazioni proxy in \\etc\\waagent.conf si applicano anche alle estensioni di VM necessarie. Se si vogliono usare i repository di Azure, verificare che il traffico verso i repository non attraversi la Intranet locale. Se sono state create route definite dall'utente per abilitare il tunneling forzato, verificare di aggiungere una route che indirizza il traffico verso i repository direttamente in Internet e non tramite la connessione VPN da sito a sito.

* **SLES**

  È anche possibile aggiungere route per gli indirizzi IP elencati in \\etc\\regionserverclnt.cfg. La figura seguente mostra un esempio:

  ![Tunneling forzato][deployment-guide-figure-50]


* **RHEL**

  È necessario anche aggiungere route per gli indirizzi IP degli host elencati in \\etc\\yum.repos.d\\rhui-load-balancers. Per un esempio, vedere la figura precedente.

* **Oracle Linux**

  Non esistono repository per Oracle Linux in Azure. È necessario configurare repository personali per Oracle Linux o usare repository pubblici.

Per altre informazioni sulle route definite dall'utente, vedere [Route definite dall'utente e inoltro IP][virtual-networks-udr-overview].

### <a name="configure-the-azure-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Configurare l'estensione Azure per SAP

Dopo avere preparato la VM come descritto in [Scenari di distribuzione di VM per SAP in Azure][deployment-guide-3], l'agente di macchine virtuali di Azure è installato nella macchina virtuale. Il passaggio successivo consiste nel distribuire l'estensione Azure per SAP, disponibile nel repository di estensioni Azure nei data center di Azure globali. Per altre informazioni, vedere [Pianificazione e implementazione di Macchine virtuali di Azure per SAP NetWeaver][planning-guide-9.1].

Per installare e configurare l'estensione Azure per SAP, è possibile usare PowerShell o l'interfaccia della riga di comando di Azure. Per installare l'estensione in una VM Windows o Linux usando un computer Windows, vedere [Azure PowerShell][deployment-guide-4.5.1]. Per installare l'estensione in una VM Linux usando un desktop Linux, vedere [Interfaccia della riga di comando di Azure][deployment-guide-4.5.2].

#### <a name="azure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell per VM Linux e Windows

Per installare l'estensione Azure per SAP usando PowerShell:

1. Verificare di aver installato l'ultima versione dei cmdlet di Azure PowerShell. Per altre informazioni, vedere [Distribuzione di cmdlet di Azure PowerShell][deployment-guide-4.1].  
1. Eseguire il cmdlet di PowerShell seguente.
    Per un elenco degli ambienti disponibili, eseguire `commandlet Get-AzEnvironment`. Per usare il cloud globale di Azure, l'ambiente è **AzureCloud**. Per Azure in Cina, selezionare **AzureChinaCloud**.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Dopo che si sono immessi i dati dell'account e si è identificata la macchina virtuale di Azure, lo script distribuisce le estensioni necessarie e abilita le funzionalità obbligatorie. Ciò può richiedere alcuni minuti.
Per altre informazioni su `Set-AzVMAEMExtension`, vedere [Set-AzVMAEMExtension][msdn-set-Azvmaemextension].

![Esecuzione corretta del cmdlet di Azure specifico di SAP Set-AzVMAEMExtension][deployment-guide-figure-900]

La configurazione `Set-AzVMAEMExtension` esegue tutti i passaggi per configurare la raccolta dati dell'host per SAP.

L'output dello script include le informazioni seguenti:

* La conferma che è stata configurata la raccolta dati del disco del sistema operativo e di tutti i dischi dati aggiuntivi.
* I due messaggi successivi confermano la configurazione delle metriche di archiviazione per uno specifico account di archiviazione.
* Una riga dell'output contiene lo stato dell'aggiornamento effettivo della configurazione dell'estensione VM per SAP.
* Un'altra riga dell'output conferma che la configurazione è stata distribuita o aggiornata.
* L'ultima riga dell'output è informativa. Visualizza le opzioni per il test della configurazione dell'estensione VM per SAP.
* Per verificare che tutti i passaggi della configurazione dell'estensione VM di Azure per SAP siano stati completati e che l'infrastruttura di Azure fornisca i dati necessari, procedere con il controllo dello stato di preparazione dell'estensione Azure per SAP, come descritto in [Controllo dello stato di preparazione dell'estensione Azure per SAP][deployment-guide-5.1].
* Attendere da 15 a 30 minuti mentre Diagnostica di Azure raccoglie i dati pertinenti.

#### <a name="azure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Interfaccia della riga di comando di Azure per VM Linux

Per installare l'estensione Azure per SAP usando l'interfaccia della riga di comando di Azure:

   1. Installare l'interfaccia della riga di comando di Azure classica come descritto in [Installare l'interfaccia della riga di comando di Azure classica][azure-cli].
   1. Accedere con l'account Azure:

      ```console
      azure login
      ```

   1. Passare alla modalità Azure Resource Manager:

      ```console
      azure config mode arm
      ```

   1. Abilitare l'estensione Azure per SAP:

      ```console
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Eseguire l'installazione con l'interfaccia della riga di comando di Azure 2.0

   1. Installare l'interfaccia della riga di comando di Azure 2.0 come descritto in [Installare l'interfaccia della riga di comando di Azure 2.0][azure-cli-2].
   1. Accedere con l'account Azure:

      ```azurecli
      az login
      ```

   1. Installare l'estensione di monitoraggio avanzato dell'interfaccia della riga di comando di Azure
  
      ```azurecli
      az extension add --name aem
      ```
  
   1. Installare l'estensione con
  
      ```azurecli
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Verificare che l'estensione Azure per SAP sia attiva nella VM di Linux Azure. Controllare se il file \\var\\lib\\AzureEnhancedMonitor\\PerfCounters esiste. Se esiste, al prompt dei comandi eseguire questo comando per visualizzare le informazioni raccolte dall'estensione Azure per SAP:

   ```console
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   L'output è simile al seguente:

   ```output
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

## <a name="checks-and-troubleshooting-for-end-to-end-data-collection-for-sap-host-agent"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Controlli e risoluzione dei problemi per la raccolta dati end-to-end per l'agente host SAP

Al termine della distribuzione della VM di Azure e della configurazione dell'estensione Azure per SAP pertinente, verificare se tutti i componenti dell'estensione funzionano come previsto.

Eseguire il controllo dello stato di preparazione dell'estensione Azure per SAP come descritto in [Controllo dello stato di preparazione dell'estensione Azure per SAP][deployment-guide-5.1]. Se tutti i risultati dei controlli dello stato di preparazione sono positivi e tutti i contatori delle prestazioni pertinenti sono funzionanti, significa che l'estensione Azure per SAP è stata configurata correttamente. È possibile passare all'installazione dell'agente host SAP come descritto nelle note SAP in [Risorse SAP][deployment-guide-2.2]. Se il controllo dello stato di preparazione indica che alcuni contatori non sono presenti, eseguire il controllo dell'integrità dell'estensione Azure per SAP come descritto in [Controllo dell'integrità della configurazione dell'estensione Azure per SAP][deployment-guide-5.2]. Per altre opzioni di risoluzione dei problemi, vedere [Risoluzione dei problemi dell'estensione Azure per SAP][deployment-guide-5.3].

### <a name="readiness-check-for-the-azure-extension-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Controllo dello stato di preparazione per l'estensione Azure per SAP

Questo controllo verifica che tutte le metriche delle prestazioni che vengono visualizzate nell'applicazione SAP provengano dall'estensione Azure per SAP sottostante.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Eseguire il controllo dello stato di preparazione in una VM Windows

1. Accedere alla macchina virtuale Azure. Non è necessario usare un account amministratore.
1. Aprire una finestra del prompt dei comandi.
1. Al prompt dei comandi passare alla cartella di installazione dell'estensione Azure per SAP: C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;versione>\\drop

   La *versione* nel percorso dell'estensione può variare. Se nella cartella di installazione risultano presenti cartelle per più versioni dell'estensione, controllare la configurazione del servizio di Windows AzureEnhancedMonitoring e quindi passare alla cartella indicata come *Percorso file eseguibile*.

   ![Proprietà del servizio che esegue l'estensione Azure per SAP][deployment-guide-figure-1000]

1. Al prompt dei comandi eseguire **azperflib.exe** senza alcun parametro.

   > [!NOTE]
   > Azperflib.exe viene eseguito in ciclo e aggiorna i contatori raccolti ogni 60 secondi. Per terminare il ciclo, chiudere la finestra del prompt dei comandi.
   >
   >

Se l'estensione Azure per SAP non è installata o il servizio AzureEnhancedMonitoring non è in esecuzione, l'estensione non è stata configurata correttamente. Per informazioni dettagliate su come distribuire l'estensione, vedere [Risoluzione dei problemi relativi all'estensione Azure per SAP][deployment-guide-5.3].

> [!NOTE]
> Azperflib.exe è un componente che può essere utilizzato per scopi personalizzati. È un componente che offre i dati dell'infrastruttura di Azure correlati alla macchina virtuale solo all'agente host SAP.
> 

##### <a name="check-the-output-of-azperflibexe"></a>Controllare l'output di azperflib.exe

L'output di azperflib.exe visualizza tutti i contatori delle prestazioni di Azure popolati per SAP. Alla fine dell'elenco dei contatori raccolti un riepilogo e un indicatore di integrità indicano lo stato dell'estensione Azure per SAP.

![Output del controllo di integrità eseguito con azperflib.exe che indica che non sono presenti problemi][deployment-guide-figure-1100]
<a name="figure-11"></a>

Controllare il risultato restituito per l'output **Counters total**, segnalato come vuoto, e per **Health status**, visualizzati nella figura precedente.

Interpretare i valori dei risultati come segue:

| Valori dei risultati di azperflib.exe | Stato di integrità dell'estensione Azure per SAP |
| --- | --- |
| **API Calls - not available** | I contatori che non sono disponibili possono essere non applicabili alla configurazione della macchina virtuale oppure sono errori. Vedere **Health status**. |
| **Counters total - empty** |Possono essere vuoti i due contatori relativi all'archiviazione di Azure seguenti: <ul><li>Storage Read Op Latency Server msec</li><li>Storage Read Op Latency E2E msec</li></ul>Tutti gli altri contatori devono avere valori. |
| **Health status** |È positivo solo se lo stato restituito è **OK**. |
| **Diagnostica** |Informazioni dettagliate sullo stato di integrità. |

Se il valore di **Health status** non è **OK**, seguire le istruzioni in [Controllo dell'integrità della configurazione dell'estensione Azure per SAP][deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Eseguire il controllo dello stato di preparazione in una VM Linux

1. Connettersi alla macchina virtuale di Azure usando SSH.

1. Controllare l'output dell'estensione Azure per SAP.

   a.  Eseguire `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **Risultato previsto**: restituisce un elenco di contatori delle prestazioni. Il file non deve essere vuoto.

   b. Eseguire `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **Risultato previsto**: restituisce una riga in cui l'errore è **none**, ad esempio **3;config;Error;;0;0;none;0;1456416792;tst-servercs;**

   c. Eseguire `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

   **Risultato previsto**: restituisce un valore vuoto o un avviso di valore non esistente.

Se il controllo precedente non è riuscito, eseguire questi controlli aggiuntivi:

1. Verificare che waagent sia installato e abilitato.

   a.  Eseguire `sudo ls -al /var/lib/waagent/`

     **Risultato previsto**: elenca il contenuto della directory waagent.

   b.  Eseguire `ps -ax | grep waagent`

   **Risultato previsto**: visualizza una voce simile a `python /usr/sbin/waagent -daemon`

1. Verificare che l'estensione Azure per SAP sia installata e in esecuzione.

   a.  Eseguire `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`

   **Risultato previsto**: elenca il contenuto della directory dell'estensione Azure per SAP.

   b. Eseguire `ps -ax | grep AzureEnhanced`

   **Risultato previsto**: visualizza una voce simile a `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Installare l'agente host SAP come descritto nella nota SAP [1031096] e controllare l'output di `saposcol`.

   a.  Eseguire `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Eseguire `dump ccm`

   c.  Controllare se la metrica **Virtualization_Configuration\Enhanced Monitoring Access** è **true**.

Se è già installato un server applicazioni SAP NetWeaver ABAP, aprire la transazione ST06 e controllare se il monitoraggio avanzato è abilitato.

Se uno di questi controlli non riesce e per informazioni dettagliate su come ridistribuire l'estensione, vedere [Risoluzione dei problemi relativi all'estensione Azure per SAP][deployment-guide-5.3].

### <a name="health-check-for-the-azure-extension-for-sap-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Controllo dell'integrità della configurazione dell'estensione Azure per SAP

Se alcuni dati dell'infrastruttura non vengono forniti correttamente come indicato nel test descritto in [Controllo dello stato di preparazione dell'estensione Azure per SAP][deployment-guide-5.1], eseguire il cmdlet `Test-AzVMAEMExtension` per controllare se l'infrastruttura di Azure e l'estensione Azure per SAP sono configurate correttamente.

1. Verificare di aver installato l'ultima versione del cmdlet di Azure PowerShell come descritto in [Distribuzione dei cmdlet di Azure PowerShell][deployment-guide-4.1].
1. Eseguire il cmdlet di PowerShell seguente. Per un elenco degli ambienti disponibili, eseguire il cmdlet `Get-AzEnvironment`. Per usare Azure globale, selezionare l'ambiente **AzureCloud**. Per Azure in Cina, selezionare **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Immettere i dati dell'account e identificare la macchina virtuale di Azure.

   ![Pagina di input del cmdlet di Azure specifico di SAP Test-VMConfigForSAP_GUI][deployment-guide-figure-1200]

1. Lo script testa la configurazione della macchina virtuale selezionata.

   ![Output di test con esito positivo dell'estensione Azure per SAP][deployment-guide-figure-1300]

Verificare che il risultato di ogni controllo dell'integrità sia **OK**. Se alcuni controlli non indicano **OK**, eseguire il cmdlet di aggiornamento come descritto in [Configurare l'estensione Azure per SAP][deployment-guide-4.5]. Attendere 15 minuti e ripetere i controlli descritti in [Controllo dello stato di preparazione dell'estensione Azure per SAP][deployment-guide-5.1] e [Controllo dell'integrità della configurazione dell'estensione Azure per SAP][deployment-guide-5.2]. Se i controlli segnalano ancora un problema con alcuni o tutti i contatori, vedere [Risoluzione dei problemi relativi all'estensione Azure per SAP][deployment-guide-5.3].

> [!Note]
> Potrebbero essere visualizzati alcuni avvisi nei casi in cui si usi Managed Disks Standard di Azure. Gli avvisi saranno visualizzati in sostituzione degli "OK" restituiti dai test. Questo è normale e previsto per quel tipo di disco. Vedere anche [Risoluzione dei problemi relativi all'estensione di Azure per SAP][deployment-guide-5.3]
> 

### <a name="troubleshooting-azure-extension-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Risoluzione dei problemi relativi all'estensione di Azure per SAP

#### <a name="windowslogo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] I contatori delle prestazioni di Azure non vengono visualizzati

Il servizio di Windows AzureEnhancedMonitoring raccoglie le metriche delle prestazioni in Azure. Se il servizio non è stato installato correttamente o non è in esecuzione nella VM, non può essere raccolta alcuna metrica delle prestazioni.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>La directory di installazione dell'estensione Azure per SAP è vuota

###### <a name="issue"></a>Problema

La directory di installazione C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;versione>\\drop è vuota.

###### <a name="solution"></a>Soluzione

L'estensione non è installata. Determinare se si tratta di un problema del proxy (come descritto prima). Potrebbe essere necessario riavviare il computer o eseguire di nuovo lo script di configurazione `Set-AzVMAEMExtension`.

##### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>Il servizio per l'estensione Azure per SAP non esiste

###### <a name="issue"></a>Problema

Il servizio di Windows AzureEnhancedMonitoring non esiste.

L'output di azperflib.exe genera un errore:

![L'esecuzione di azperflib.exe indica che il servizio dell'estensione Azure per SAP non è in esecuzione][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Soluzione

Se il servizio non esiste, l'estensione Azure per SAP non è stata installata correttamente. Ridistribuire l'estensione usando la procedura descritta per lo scenario di distribuzione specifico in [Scenari di distribuzione di VM per SAP in Azure][deployment-guide-3].

Dopo avere distribuito l'estensione, dopo un'ora controllare di nuovo se i contatori delle prestazioni di Azure sono presenti nella VM di Azure.

##### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>Il servizio dell'estensione di Azure per SAP esiste, ma non si avvia

###### <a name="issue"></a>Problema

Il servizio di Windows AzureEnhancedMonitoring esiste ed è abilitato, ma non si avvia. Per altre informazioni, controllare il log eventi dell'applicazione.

###### <a name="solution"></a>Soluzione

La configurazione non è corretta. Riavviare l'estensione Azure per SAP nella VM, come descritto in [Configurare l'estensione Azure per SAP][deployment-guide-4.5].

#### <a name="windowslogo_windows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Alcuni contatori delle prestazioni di Azure non sono presenti

Il servizio di Windows AzureEnhancedMonitoring raccoglie le metriche delle prestazioni in Azure. Il servizio ottiene dati da più origini. Alcuni dati di configurazione vengono raccolti in locale e alcune metriche delle prestazioni vengono lette da Diagnostica di Azure. Vengono usati contatori di archiviazione dalla registrazione a livello di sottoscrizione di archiviazione.

Se la soluzione indicata nella nota SAP [1999351] non risolve il problema, eseguire di nuovo lo script di configurazione `Set-AzVMAEMExtension`. Potrebbe essere necessario attendere un'ora perché i contatori relativi all'analisi dell'archiviazione o alla diagnostica non possono essere creati subito dopo essere stati abilitati. Se il problema persiste, inviare un messaggio all'assistenza clienti SAP in merito al componente BC-OP-NT-AZR per Windows o al componente BC-OP-LNX-AZR per una macchina virtuale Linux.

#### <a name="linuxlogo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] I contatori delle prestazioni di Azure non vengono visualizzati

Le metriche delle prestazioni in Azure vengono raccolte da un daemon. Se il daemon non è in esecuzione, non può essere raccolta alcuna metrica delle prestazioni.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>La directory di installazione dell'estensione Azure per SAP è vuota

###### <a name="issue"></a>Problema

La directory \\var\\lib\\waagent\\ non ha una sottodirectory per l'estensione Azure per SAP.

###### <a name="solution"></a>Soluzione

L'estensione non è installata. Determinare se si tratta di un problema del proxy (come descritto prima). Potrebbe essere necessario riavviare il computer e/o eseguire di nuovo lo script di configurazione `Set-AzVMAEMExtension`.

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>L'esecuzione di Set-AzVMAEMExtension e Test-AzVMAEMExtension mostra messaggi di avviso indicanti che i Managed Disks Standard non sono supportati

###### <a name="issue"></a>Problema

Quando si esegue Set-AzVMAEMExtension o Test-AzVMAEMExtension vengono visualizzati messaggi simili ai seguenti:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

L'esecuzione di azperfli.exe come descritto in precedenza potrebbe consistere in un risultato che indica uno stato non integro. 

###### <a name="solution"></a>Soluzione

I messaggi sono causati dal fatto che i Managed Disks Standard non consegnano le API usate dall'estensione Azure per SAP per verificare le statistiche degli account di archiviazione Standard di Azure. Questo non consiste in un problema. Il motivo dell'introduzione della raccolta dati per gli account di archiviazione su disco Standard era la limitazione degli I/O che si verificava di frequente. I dischi gestiti eviteranno tale limitazione, limitando il numero di dischi in un account di archiviazione. Pertanto, la mancanza di quel tipo di dati non è critica.


#### <a name="linuxlogo_linux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Alcuni contatori delle prestazioni di Azure non sono presenti

Le metriche delle prestazioni in Azure vengono raccolte da un daemon, che ottiene i dati da diverse origini. Alcuni dati di configurazione vengono raccolti in locale e alcune metriche delle prestazioni vengono lette da Diagnostica di Azure. I contatori di archiviazione provengono dai log nella sottoscrizione di archiviazione.

Per un elenco completo e aggiornato dei problemi noti, vedere la nota SAP [1999351], che contiene informazioni aggiuntive sulla risoluzione dei problemi relativi all'estensione Azure per SAP.

Se la soluzione indicata nella nota SAP [1999351] non risolve il problema, eseguire di nuovo lo script di configurazione `Set-AzVMAEMExtension` come descritto in [Configurare l'estensione Azure per SAP][deployment-guide-4.5]. Potrebbe essere necessario attendere un'ora perché i contatori relativi all'analisi dell'archiviazione o alla diagnostica non possono essere creati subito dopo essere stati abilitati. Se il problema persiste, inviare un messaggio all'assistenza clienti SAP in merito al componente BC-OP-NT-AZR per Windows o al componente BC-OP-LNX-AZR per una macchina virtuale Linux.
