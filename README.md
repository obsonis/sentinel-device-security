# Microsoft Sentinel templates for device security events

Azure Resource Manager templates that prepare a Microsoft Sentinel workspace to receive device security events from the platform through the Azure Monitor Logs Ingestion API.

Deploy them in order, into the resource group of the Log Analytics workspace that has Microsoft Sentinel enabled.

## 1. Ingestion (required)

Creates the custom table `DeviceSecurity_CL`, a Data Collection Endpoint and a Data Collection Rule that maps incoming events to the table. If you pass the object id of your Entra app registration's service principal (`servicePrincipalObjectId`), it also grants that app the **Monitoring Metrics Publisher** role on the rule.

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fobsonis%2Fsentinel-device-security%2Fmain%2Fdevice-security-ingestion.json)

Outputs, needed when you configure the integration:

| Output | Integration field |
| --- | --- |
| `dataCollectionEndpointUri` | Data Collection Endpoint URI |
| `dataCollectionRuleImmutableId` | DCR Immutable ID |
| `streamName` | Stream name (`Custom-DeviceSecurity_CL`) |

## 2. Workbook (optional)

A device posture overview workbook over `DeviceSecurity_CL`.

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fobsonis%2Fsentinel-device-security%2Fmain%2Fdevice-security-workbook.json)

## 3. Analytics rules (optional)

Two scheduled rules:

- Critical vulnerability on a quarantined device.
- New device with a known exploited vulnerability (CISA KEV).

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fobsonis%2Fsentinel-device-security%2Fmain%2Fdevice-security-analytics-rules.json)

The quarantine rule matches the default Zero Trust status name "Quarantined". If you renamed that status, edit the rule query after deployment.

## Azure CLI

```sh
az deployment group create \
  --resource-group <rg> \
  --template-uri https://raw.githubusercontent.com/obsonis/sentinel-device-security/main/device-security-ingestion.json \
  --parameters workspaceName=<workspace> servicePrincipalObjectId=<object-id>
```

Replace the file name to deploy the workbook or analytics rules.

## Then

Add the Microsoft Sentinel integration in Config > Integrations with the Entra directory (tenant) id, client id and secret of the app registration, and the outputs above. See the integration guide in the platform documentation.
