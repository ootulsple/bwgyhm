# 如何配置 Azure OpenAI 服务的 Microsoft Entra ID 身份验证

## 本文内容

在复杂的安全方案中，Azure 基于角色的访问控制 (Azure RBAC) 是必不可少的。本文将详细介绍如何使用 Microsoft Entra ID 对 Azure OpenAI 资源进行身份验证。

你将通过 Azure CLI 进行登录，并获取持有者令牌来调用 OpenAI 资源。如果在操作过程中遇到问题，可以参考每个部分提供的链接以及 Azure Cloud Shell/Azure CLI 中每个命令的所有可用选项。

## 先决条件

## 分配角色

首先，你需要为自己分配[认知服务 OpenAI 用户](https://learn.microsoft.com/zh-cn/azure/ai-services/openai/how-to/role-based-access-control#cognitive-services-openai-user)或[认知服务 OpenAI 参与者](https://learn.microsoft.com/zh-cn/azure/ai-services/openai/how-to/role-based-access-control#cognitive-services-openai-contributor)角色。这样，你可以使用帐户进行 Azure OpenAI 接口 API 调用，而无需使用基于密钥的身份验证。请注意，完成此更改后，最多可能需要 5 分钟才能生效。

## 登录到 Azure CLI

要登录到 Azure CLI，请运行以下命令并完成登录。如果会话空闲时间过长，可能需要再次执行此操作。

bash
az login


python
from azure.identity import DefaultAzureCredential, get_bearer_token_provider
from openai import AzureOpenAI

token_provider = get_bearer_token_provider(
    DefaultAzureCredential(), "https://cognitiveservices.azure.com/.default"
)

client = AzureOpenAI(
    api_version="2024-02-15-preview",
    azure_endpoint="https://{your-custom-endpoint}.openai.azure.com/",
    azure_ad_token_provider=token_provider
)

response = client.chat.completions.create(
    model="gpt-35-turbo-0125", # model = "deployment_name".
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Does Azure OpenAI support customer managed keys?"},
        {"role": "assistant", "content": "Yes, customer managed keys are supported by Azure OpenAI."},
        {"role": "user", "content": "Do other Azure AI services support this too?"}
    ]
)

print(response.choices[0].message.content)


## 使用控制平面 API 查询 Azure OpenAI

python
import requests
import json
from azure.identity import DefaultAzureCredential

region = "eastus"
token_credential = DefaultAzureCredential()
subscriptionId = "{YOUR-SUBSCRIPTION-ID}" 

token = token_credential.get_token('https://management.azure.com/.default')
headers = {'Authorization': 'Bearer ' + token.token}

url = f"https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.CognitiveServices/locations/{region}/models?api-version=2023-05-01"

response = requests.get(url, headers=headers)

data = json.loads(response.text)

print(json.dumps(data, indent=4))


## 授权访问托管标识

OpenAI 支持使用 [Azure 资源的托管标识](https://learn.microsoft.com/zh-cn/azure/active-directory/managed-identities-azure-resources/overview)进行 Microsoft Entra 身份验证。Azure 资源的托管标识可以从 Azure 虚拟机 (VM)、函数应用、虚拟机规模集和其他服务中运行的应用程序使用 Microsoft Entra 凭据授权对 Azure AI 服务资源的访问权限。将 Azure 资源的托管标识与 Microsoft Entra 身份验证结合使用，可避免将凭据随在云中运行的应用程序一起存储。

## 在 VM 上启用托管标识

👉 [WildCard | 一分钟注册，轻松订阅海外线上服务](https://bbtdd.com/WildCard)