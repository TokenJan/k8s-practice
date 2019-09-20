# helm install jenkins
`kubectl apply -f jenkins-volumn.yml -n jenkins`
`helm install --name jenkins --namespace jenkins --values=jenkins-values.yml stable/jenkins`

# Create a ServiceAccount named `jenkins` in a given namespace.
`kubectl -n <namespace> create serviceaccount jenkins`

# The next line gives `jenkins` administator permissions for this namespace.
# * You can make it an admin over all namespaces by creating a `ClusterRoleBinding` instead of a `RoleBinding`.
# * You can also give it different permissions by binding it to a different `(Cluster)Role`.
`kubectl -n <namespace> create rolebinding jenkins-binding --clusterrole=cluster-admin --serviceaccount=jenkins`

# docker login & push to acr
`kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>`
`chmod 666 /var/run/docker.sock`
```
sh 'docker login -u <acr_username> -p <acr_password> <acr_server_url>'
sh 'docker build -t <acr_server_url>/<image>:<tag> .'
sh 'docker push <acr_server_url>/<image>:<tag>'
```