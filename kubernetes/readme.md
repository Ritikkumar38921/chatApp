<!-- do port forwarding -->
 -> build for frontend and backend 
    (1) docker build -t dockerimagename .
    (2) docker login
    (3) docker push dockerimagename

Kubernetes : 
 do -> minikube start 
 and run in kubernetes folder
 (1) kubectl apply - .\namespace.yml
 (2) kubectl apply - .\mongodb-deployment.yml
 (3) kubectl apply - .\mongodb-pv.yml   
 (4) kubectl apply - .\mongodb-pvc.yml
 (5) kubectl apply - .\mongodb-service.yml
 (6) kubectl apply - .\backend-deployment.yml
 (7) kubectl apply - .\backend-service.yml  
 (8) kubectl apply - .\frontend-deployment.yml
 (9) kubectl apply - .\frontend-service.yml   

 and last port forward 
 
 -> kubectl port-forward pods/backendpodname -n chat-app 5001:5001
 -> kubectl port-forward pods/frontendpodname -n chat-app 80:80


these are not used here just for reading purposes. all file come from chat-app helm .