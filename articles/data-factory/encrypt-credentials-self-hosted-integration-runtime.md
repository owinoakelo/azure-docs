---
title: Encrypt credentials in Azure Data Factory 
description: Learn how to encrypt and store credentials for your on-premises data stores on a machine with self-hosted integration runtime. 
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: lle 
ms.custom: devx-track-azurepowershell
---

# Encrypt credentials for on-premises data stores in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

You can encrypt and store credentials for any of your on-premises data stores (linked services with sensitive information) on a machine with self-hosted integration runtime.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

You pass a JSON definition file with credentials to the <br/>[**New-AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) cmdlet to produce an output JSON definition file with the encrypted credentials. Then, use the updated JSON definition to create the linked services.

## Create a linked service with encrypted credentials

This example shows how to create a linked service to an on-premise SQL Server data source with encrypted credentials.

### Create initial linked service JSON file description

Create a JSON file named **SqlServerLinkedService.json** in any folder with the following content:  

Replace `<servername>`, `<databasename>`, `<username>`, and `<password>` with values for your SQL Server before saving the file. And, replace `<integration runtime name>` with the name of your integration runtime. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

### Encrypt credentials
To encrypt the sensitive data from the JSON payload on an on-premises self-hosted integration runtime, run **New-AzDataFactoryV2LinkedServiceEncryptedCredential**, and pass on the JSON payload. This cmdlet ensures the credentials are encrypted using DPAPI and stored on the self-hosted integration runtime node locally. It can be run from any machine provided the **Remote access** option is enabled on the targeted self-hosted integration runtime, and Powershell 7.0 or higher is used to execute it. The output payload containing the encrypted reference to the credential can be redirected to another JSON file (in this case 'encryptedLinkedService.json').

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

### Use the JSON with encrypted credentials
Now, use the output JSON file from the previous command containing the encrypted credential to set up the **SqlServerLinkedService**.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## Next steps
For information about security considerations for data movement, see [Data movement security considerations](data-movement-security-considerations.md).

