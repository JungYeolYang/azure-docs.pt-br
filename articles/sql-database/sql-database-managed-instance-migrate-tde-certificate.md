---
title: Migrar certificado de TDE - Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Migrar o certificado que protege a Chave de Criptografia do Banco de Dados de um banco de dados com Transparent Data Encryption para a Instância Gerenciada do Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: carlrab, jovanpop
manager: craigg
ms.date: 04/25/2019
ms.openlocfilehash: f54950ab96664b17aab056b468db0644216e8654
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64706108"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-database-managed-instance"></a>Migrar o certificado do banco de dados protegido por TDE para a Instância Gerenciada do Banco de Dados SQL do Azure

Durante a migração de um banco de dados protegido por [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) para a Instância Gerenciada do Banco de Dados SQL do Azure usando a opção de restauração nativa, o certificado correspondente do SQL Server local ou de IaaS deve ser migrado antes da restauração do banco de dados. Este artigo orienta você pelo processo de migração manual do certificado para a Instância Gerenciada do Banco de Dados SQL do Azure:

> [!div class="checklist"]
> * Exportação do certificado para um arquivo .pfx (Troca de Informações Pessoais)
> * Extração do certificado do arquivo para a cadeia de caracteres base 64
> * Upload do certificado usando o cmdlet do PowerShell

Para uma opção alternativa usando o serviço totalmente gerenciado para migração direta do banco de dados protegido por TDE e o certificado correspondente, consulte [Como migrar seu banco de dados local para a Instância Gerenciada usando o Serviço de Migração de Banco de Dados do Azure ](../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> O certificado migrado é usado apenas para a restauração do banco de dados protegido por TDE. Logo após a restauração é feita, o certificado migrado é substituído por um protetor de diferente serviços gerenciados de certificado ou chave assimétrica do Cofre de chaves, dependendo do tipo de transparent data encryption definidas na instância.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é compatível com o banco de dados SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para concluir as etapas neste artigo, você precisa dos seguintes pré-requisitos:

- Ferramenta de linha de comando [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) instalada no servidor local ou em outro computador com acesso ao certificado exportado como arquivo. A ferramenta Pvk2Pfx faz parte do [Kit de drivers corporativos do Windows](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk), um ambiente de linha de comando autônomo independente.
- [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell) versão 5.0 ou posterior instalada.
- Módulo do Azure PowerShell [instalados e atualizados](https://docs.microsoft.com/powershell/azure/install-az-ps).
- [Módulo Az.Sql](https://www.powershellgallery.com/packages/Az.Sql).
  Execute os seguintes comandos no PowerShell para instalar/atualizar o módulo do PowerShell:

   ```powershell
   Install-Module -Name Az.Sql
   Update-Module -Name Az.Sql
   ```

## <a name="export-tde-certificate-to-a-personal-information-exchange-pfx-file"></a>Exportação do certificado TDE para um arquivo .pfx (Troca de Informações Pessoais)

O certificado pode ser exportado diretamente do SQL Server de origem ou do repositório de certificados, se estiver nesse local.

### <a name="export-certificate-from-the-source-sql-server"></a>Exportar o certificado do SQL Server de origem

Use as etapas a seguir para exportar o certificado com o SQL Server Management Studio e convertê-la no formato pfx. Os nomes genéricos *TDE_Cert* e *full_path* estão sendo usados para nomes e caminhos de certificado e de arquivo por meio das etapas. Eles devem ser substituídos por nomes reais.

1. No SSMS, abra uma nova janela de consulta e conecte-se ao SQL Server de origem.
2. Use o script a seguir para listar os bancos de dados protegidos por TDE e obter o nome da criptografia de proteção de certificado do banco de dados a ser migrado:

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![lista de certificados TDE](./media/sql-database-managed-instance-migrate-tde-certificate/onprem-certificate-list.png)

3. Execute o script a seguir para exportar o certificado para um par de arquivos (.cer e .pvk), mantendo as informações de chave pública e privada:

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![backup de certificado TDE](./media/sql-database-managed-instance-migrate-tde-certificate/backup-onprem-certificate.png)

4. Use o console do PowerShell para copiar informações do certificado de um par de arquivos recém-criados para um arquivo .pfx (Troca de Informações Pessoais), usando a ferramenta Pvk2Pfx:

   ```powershell
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-certificate-from-certificate-store"></a>Exportar o certificado do repositório de certificados

Se o certificado for mantido no repositório de certificados do computador local do SQL Server, será possível exportá-lo usando as seguintes etapas:

1. Abra o console do PowerShell e execute o comando a seguir para abrir o snap-in de Certificados do Console de Gerenciamento da Microsoft:

   ```powershell
   certlm
   ```

2. Nos snap-in Certificados do MMC, expanda o caminho Pessoal -> Certificados para ver a lista de certificados

3. Clique com botão direito no certificado e clique em Exportar…

4. Siga o assistente para exportar o certificado e a chave privada para um formato de Troca de Informações Pessoais

## <a name="upload-certificate-to-azure-sql-database-managed-instance-using-azure-powershell-cmdlet"></a>Carregar o certificado na Instância Gerenciada do Banco de Dados SQL do Azure usando o cmdlet do Azure PowerShell

1. Comece com as etapas de preparação no PowerShell:

   ```powershell
   # Import the module into the PowerShell session
   Import-Module Az
   # Connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # List subscriptions available and copy id of the subscription target Managed Instance belongs to
   Get-AzSubscription
   # Set subscription for the session (replace Guid_Subscription_Id with actual subscription id)
   Select-AzSubscription Guid_Subscription_Id
   ```

2. Após a conclusão de todas as etapas de preparação, execute os comandos a seguir para carregar o certificado codificado em base-64 para a Instância Gerenciada de destino:

   ```powershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "SomeStrongPassword"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<ResourceGroupName>" -ManagedInstanceName "<ManagedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

Agora, o certificado está disponível para a Instância Gerenciada especificada e o backup do banco de dados protegido por TDE correspondente pode ser restaurado com êxito.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a migrar a chave de criptografia de proteção de certificado do banco de dados com a Transparent Data Encryption, do SQL Server local ou de IaaS para a Instância Gerenciada do Banco de Dados SQL do Azure.

Confira [Restaurar um backup de banco de dados em uma Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-get-started-restore.md) para saber como restaurar um backup de banco de dados em uma Instância Gerenciada do Banco de Dados SQL do Azure.
