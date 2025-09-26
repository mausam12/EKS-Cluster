<img width="421" height="106" alt="image" src="https://github.com/user-attachments/assets/eb1a8195-9bf5-4eff-a74d-977ed6e0f531" /># EKS-Cluster
Requirements
1.	EKS Cluster
o	Create an EKS cluster with at least 2 nodes in 2 AZs.
o	Configure proper VPC + subnets (public and private).
o	Deploy at least 5 demo services (simple apps like nginx or httpbin) to show the cluster is working.
2.	Service Mesh
o	Install Istio with Ambient profile.
o	Expose one service externally via Istio.
3.	Observability
o	Deploy ELK stack (Elasticsearch + Kibana) for logs.
o	Deploy Tempo for distributed tracing.
o	Deploy SigNoz for dashboards/APM.

1. EKS Cluster
VPC & Subnet Configuration:
•	Created a VPC with public and private subnets.
<img width="940" height="223" alt="image" src="https://github.com/user-attachments/assets/4b1661b5-ad5c-4d64-840c-dcd366b8c1d7" />

 
Set up an EKS Cluster using the AWS Console & created EKS Node Group:
 
Created EKS Node Group:
 
Commands to Verify Cluster and Services
Verify the EKS Cluster
1.	Update kubeconfig for accessing the EKS cluster:
aws eks --region ap-south-1 update-kubeconfig --name DemoEKScluster
kubectl get nodes

 
Deploying Demo Services (2 Apps)
We are deploying 2 demo services (e.g., Nginx, HTTPBin) on the EKS cluster.

 
 
Try Port-Forward
Once pod is Running and endpoints exist:
kubectl port-forward svc/nginx-service 8080:80
Then test:
curl http://localhost:8080

 
 

Ambient mesh + Istio components need ~8 GB RAM total → impossible on Free Tier.
2.Istio Service Mesh (Ambient Profile)
1.	Install Istio with the Ambient profile:
curl -L https://istio.io/downloadIstio | sh -
cd istio-*/bin
export PATH=$PWD:$PATH
istioctl install --set profile=ambient -y
2.	Expose the Nginx service via Istio:
3.	Apply Istio Gateway and VirtualService:
kubectl apply -f nginx-istio-gateway.yaml
kubectl apply -f nginx-istio-virtualservice.yaml
4.	Access Nginx through Istio:
kubectl get svc istio-ingressgateway -n istio-system
Use the external IP to access http://<external-ip>/nginx.
Observability Stack (ELK + Tempo + SigNoz)
Deploy Elasticsearch & Kibana
1.	Elasticsearch Deployment:
2.	Kibana Deployment:
3.	Deploy and Access Kibana:
kubectl apply -f elasticsearch-deployment.yaml
kubectl apply -f kibana-deployment.yaml
kubectl port-forward svc/kibana 5601:5601

Access Kibana at http://localhost:5601.
Deploy Tempo for Distributed Tracing
kubectl apply -f https://github.com/grafana/tempo/releases/download/v1.5.0/tempo.yaml
Deploy SigNoz for APM
kubectl apply -f https://github.com/SigNoz/signoz/releases/download/v0.12.0/signoz-kubernetes.yaml
To access SigNoz:
kubectl port-forward svc/signoz-frontend 3301:3301
Access SigNoz at http://localhost:3301.


