---
title: Suporte para gerenciador de reinicialização - Lote do Azure
description: Usar o Azure para renderização com a integração do gerenciador de renderização de Lote do Azure
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: bcc66a73e3d7986b177b13eb309ad664a006b960
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118579"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Usar o Lote do Azure com gerenciadores de farm de renderização

Se você estiver usando um farm de renderização local existente, é altamente provável que um gerenciador de renderização controle a capacidade do farm de renderização e renderize trabalhos.

O Azure fornece complementos ou suporte interno a gerenciadores de renderização populares. Em seguida, será possível adicionar e remover VMs do Azure, incluindo VMs com o licenciamento de aplicativo de pagamento por uso e VMs de baixa prioridade.

Há suporte aos seguintes gerenciadores de renderização:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](http://www.royalrender.de/)
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/)

## <a name="using-azure-with-pipelinefx-qube"></a>Usar Azure com PipelineFX Qube

Scripts e instruções para habilitar que VMs de pool do Lote do Azure sejam usadas como trabalhos do Qube estão no [repositório GitHub](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Usar Azure com Royal Render

O Royal Render tem integração do Azure e Lote do Azure interna, permitindo que você estenda um farm de renderização com VMs baseadas no Azure. Para um resumo, consulte [os arquivos de ajuda](http://www.royalrender.de/help8/index.html?Cloudrendering.html).

Para um exemplo de um cliente Royal Render usando a integração do Azure, consulte a [história do cliente Jellyfish Pictures](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Usar Azure com Thinkbox Deadline

Scripts e instruções para habilitar que VMs de pool do Lote do Azure sejam usadas como subordinadas do Deadline estão no [repositório GitHub](https://github.com/Azure/azure-deadline).

## <a name="next-steps"></a>Próximas etapas

Experimente a integração do Lote do Azure para o gerenciador de renderização, usando o plug-in apropriado e as instruções no GitHub, onde aplicável.