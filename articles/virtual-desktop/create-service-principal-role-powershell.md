---
title: Criar entidades de serviço e atribuições de função da Versão Prévia da Área de Trabalho Virtual do Windows com o PowerShell – Azure
description: Como criar entidades de serviço e atribuir funções com o PowerShell na Versão Prévia da Área de Trabalho Virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: d3357cec426585ba8550301dfa703f583a930ad0
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236932"
---
# <a name="tutorial-create-service-principals-and-role-assignments-with-powershell"></a>Tutorial: Criar entidades de serviço e atribuições de função com o PowerShell

As entidades de serviço são identidades que você pode criar no Azure Active Directory para atribuir funções e permissões para uma finalidade específica. Na Versão Prévia da Área de Trabalho Virtual do Windows, você pode criar uma entidade de serviço para:

- Automatizar tarefas específicas de gerenciamento da Área de Trabalho Virtual do Windows
- Usá-la como credenciais no lugar de usuários com MFA necessário durante a execução de qualquer modelo do Azure Resource Manager da Área de Trabalho Virtual do Windows

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar uma entidade de serviço no Azure Active Directory
> * Criar uma atribuição de função na Área de Trabalho Virtual do Windows
> * Entrar na Área de Trabalho Virtual do Windows com a entidade de serviço

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar entidades de serviço e atribuições de função, você precisará realizar três tarefas:

1. Instalar o módulo do Azure AD. Para instalar o módulo, execute o PowerShell como administrador e execute o seguinte cmdlet:

    ```powershell
    Install-Module AzureAD
    ```

2. Execute os cmdlets a seguir com os valores entre aspas substituídos pelos valores relevantes à sua sessão. Se você acabou de criar seu locatário da Área de Trabalho Virtual do Windows no tutorial [​​Criar um locatário na Área de Trabalho Virtual do Windows](./tenant-setup-azure-active-directory.md), use "Grupo de Locatários Padrão" como nome do grupo de locatários.

    ```powershell
    $myTenantGroupName = "<my-tenant-group-name>"
    $myTenantName = "<my-tenant-name>"
    ```

3. Siga todas as instruções deste artigo na mesma sessão do PowerShell. Isso poderá não funcionar se você fechar a janela e retornar a ela mais tarde.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Criar uma entidade de serviço no Azure Active Directory

Depois de atender aos pré-requisitos na sessão do PowerShell, execute os cmdlets do PowerShell a seguir para criar uma entidade de serviço multilocatário no Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Criar uma atribuição de função na Versão Prévia da Área de Trabalho Virtual do Windows

Agora que você criou uma entidade de serviço, use-a para entrar na Área de Trabalho Virtual do Windows. Entre com uma conta que tenha permissões para criar a atribuição de função.

Primeiro, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso.

Execute os cmdlets do PowerShell a seguir para se conectar à Área de Trabalho Virtual do Windows e criar uma atribuição de função para a entidade de serviço.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsContext -TenantGroupName $myTenantGroupName
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantGroupName $myTenantGroupName -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Entrar com a entidade de serviço

Depois de criar uma atribuição de função para a entidade de serviço, agora você deverá verificar se a entidade de serviço pode entrar na Área de Trabalho Virtual do Windows executando o seguinte cmdlet:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Depois de conectado, verifique se tudo está funcionando testando alguns cmdlets do PowerShell da Área de Trabalho Virtual do Windows com a entidade de serviço.

## <a name="view-your-credentials-in-powershell"></a>Exibir suas credenciais no PowerShell

Antes de encerrar sua sessão do PowerShell, você deverá exibir suas credenciais e anotá-las para referência futura. A senha é especialmente importante porque você não poderá recuperá-la depois de fechar essa sessão do PowerShell.

Estas são as três credenciais que você deverá anotar e os cmdlets que você precisará executar para obtê-las:

- Senha:

    ```powershell
    $svcPrincipalCreds.Value
    ```

- ID do locatário:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- ID do aplicativo:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="next-steps"></a>Próximas etapas

Depois de criar a entidade de serviço e atribuí-la uma função no locatário da Área de Trabalho Virtual do Windows, você poderá usá-la para criar um pool de host. Para saber mais sobre os pools de host, continue com o tutorial para criar um pool de hosts na Área de Trabalho Virtual do Windows.

 > [!div class="nextstepaction"]
 > [Tutorial de pool de hosts da Área de Trabalho Virtual do Windows](./create-host-pools-azure-marketplace.md)
