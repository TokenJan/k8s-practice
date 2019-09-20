## 前提条件:
1. 创建 AKS 时，网络类型必须选为`高级`
2. 创建应用程序网关时，层选为 `V2`
3. 网关与 AKS 在同一个虚拟网络下

## 创建 Azuer Identity 并且赋予权限
1. Create azure identity for vm resourceGroup
`az identity create -g <resource group name of VMs, start with MC_> -n <identity name>`

2. Give the identity `Contributor` access to you App Gateway. For this you need the ID of the App Gateway, which will look something like this: 
`/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`
Get the list of App Gateway IDs in your subscription with: `az network application-gateway list --query '[].id'`
```
az role assignment create \
    --role Contributor \
    --assignee <principalId> \
    --scope <App-Gateway-ID>
```

3. Give the identity `Reader` access to the App Gateway resource group. The resource group ID would look like: `/subscriptions/A/resourceGroups/B`.
You can get all resource groups with: `az group list --query '[].id'`
```
az role assignment create \
    --role Reader \
    --assignee <principalId> \
    --scope <App-Gateway-Resource-Group-ID>
```

## 启动 Azure App-GW ingress controller
1. Create rbac and api extensions for MIC and NMI
`kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml`

2. Create Azure Identity
`kubectl apply -f aadpodidentity.yml`

3. Create Azure Identity Binding
`kubectl apply -f aadpodidentitybinding.yml`

4. Install azure app-gw ingress controller
`helm install -f helm-config.yml application-gateway-kubernetes-ingress/ingress-azure`
```
helm upgrade \
    odd-billygoat \
    application-gateway-kubernetes-ingress/ingress-azure \
    --version 0.9.0-rc2
```

## 部署应用程序
1. You can generate a self-signed certificate and private key with:
`$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ${KEY_FILE} -out ${CERT_FILE} -subj "/CN=${HOST}/O=${HOST}"`

2.Then create the secret in the cluster via:
`kubectl create secret tls ${CERT_NAME} --key ${KEY_FILE} --cert ${CERT_FILE}`

3. Create application ingress, service, deployments...
`kubectl apply -f ing-guestbook.yml`
`kubectl apply -f guestbook-all-in-one.yml`