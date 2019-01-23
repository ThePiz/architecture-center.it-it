---
title: Estendere le funzionalità dei modelli di Azure Resource Manager
description: Fornisce suggerimenti e consigli su come estendere le funzionalità dei modelli di Azure Resource Manager.
author: petertay
ms.date: 06/09/2017
ms.topic: article
ms.service: architecture-center
ms.subservice: reference-architecture
ms.openlocfilehash: 108d82066d9867682c246c4de802849e2e561cbc
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54486438"
---
# <a name="extend-azure-resource-manager-template-functionality"></a>Estendere le funzionalità dei modelli di Azure Resource Manager

Nel 2016 il team di modelli e procedure di Microsoft ha creato un set di [blocchi predefiniti di modello](https://github.com/mspnp/template-building-blocks/wiki) di Azure Resource Manager allo scopo di semplificare la distribuzione delle risorse. Ogni blocco predefinito contiene un set di modelli predefiniti che distribuiscono set di risorse specificate da file di parametri separati.

I modelli di blocchi predefiniti sono progettati per essere combinati insieme per distribuzioni più grandi e complesse. Ad esempio, la distribuzione di una macchina virtuale in Azure richiede una rete virtuale, account di archiviazione e altre risorse. Il [modello di blocco predefinito di rete virtuale](https://github.com/mspnp/template-building-blocks/wiki/VNet-(v1)) consente di distribuire una rete virtuale e le relative subnet. Il [modello di blocco predefinito di macchina virtuale](https://github.com/mspnp/template-building-blocks/wiki/Windows-and-Linux-VMs-(v1)) consente di distribuire account di archiviazione, interfacce di rete e VM effettive. È quindi possibile creare uno script o un modello per chiamare entrambi i modelli di blocchi predefiniti con i file di parametri corrispondenti per distribuire un'architettura completa in un'unica operazione.

Durante lo sviluppo dei modelli di blocchi predefiniti, il team dei modelli e delle prassi ha progettato diversi concetti per estendere le funzionalità del modello di Azure Resource Manager. In questa serie saranno descritti alcuni di questi concetti in modo che l'utente possa impiegarli nei propri modelli.

> [!NOTE]
> Questi articoli presuppone una conoscenza approfondita dei modelli di Azure Resource Manager.