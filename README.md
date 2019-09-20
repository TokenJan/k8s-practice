## Add kubernetes dashboard service account
`kubectl create clusterrolebinding kubernetes-dashboard -n kube-system --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard`

## Install tiller
`kubectl create serviceaccount -n kube-system tiller`
`kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller`
`helm init --service-account tiller`