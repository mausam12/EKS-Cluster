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

 EKS Cluster
 
VPC & Subnet Configuration:
•	Created a VPC with public and private subnets.
<img width="940" height="223" alt="image" src="https://github.com/user-attachments/assets/4b1661b5-ad5c-4d64-840c-dcd366b8c1d7" />

 
Set up an EKS Cluster using the AWS Console & created EKS Node Group:
<img width="940" height="337" alt="image" src="https://github.com/user-attachments/assets/50f71c42-3e12-4e00-b9c6-cc5ebf344885" />

 
Created EKS Node Group:
<img width="940" height="140" alt="image" src="https://github.com/user-attachments/assets/7090fd46-dbb0-4920-a3e4-8568c490131f" />

 
Commands to Verify Cluster and Services
Verify the EKS Cluster
	Update kubeconfig for accessing the EKS cluster
  
aws eks --region ap-south-1 update-kubeconfig --name DemoEKScluster

kubectl get nodes
<img width="940" height="119" alt="image" src="https://github.com/user-attachments/assets/fdd2aee3-fa30-4a23-a6ad-7fcc6774763a" />

 
Deploying Demo Services (2 Apps)
We are deploying 2 demo services (e.g., Nginx, HTTPBin) on the EKS cluster.

 <img width="731" height="95" alt="image" src="https://github.com/user-attachments/assets/aee9b05a-5404-4d22-939f-24446690d3fa" />


 
 <img width="750" height="228" alt="image" src="https://github.com/user-attachments/assets/e386857b-26a2-42a2-a849-23831231f461" />


 
Try Port-Forward
Once pod is Running and endpoints exist:

kubectl port-forward svc/nginx-service 8080:80

Then test:
curl http://localhost:8080

 
 <img width="603" height="515" alt="image" src="https://github.com/user-attachments/assets/f53d7d03-3ed8-4b3e-8df6-6500e434d2e9" />


 
 <img width="388" height="204" alt="image" src="https://github.com/user-attachments/assets/d7bd9f21-10c9-4f4e-a918-35809d50ca42" />


Ambient mesh + Istio components need ~8 GB RAM total → impossible on Free Tier.


Istio Service Mesh (Ambient Profile)

**	Install Istio with the Ambient profile:
curl -L https://istio.io/downloadIstio | sh -

cd istio-*/bin

export PATH=$PWD:$PATH

istioctl install --set profile=ambient -y

**Expose the Nginx service via Istio:

Apply Istio Gateway and VirtualService:

kubectl apply -f nginx-istio-gateway.yaml

kubectl apply -f nginx-istio-virtualservice.yaml

** Access Nginx through Istio:

kubectl get svc istio-ingressgateway -n istio-system

Use the external IP to access http://<external-ip>/nginx.


**Observability Stack (ELK + Tempo + SigNoz)

**Deploy Elasticsearch & Kibana

1.	Elasticsearch Deployment:
2.	Kibana Deployment:
3.	Deploy and Access Kibana:
   
kubectl apply -f elasticsearch-deployment.yaml

kubectl apply -f kibana-deployment.yaml

kubectl port-forward svc/kibana 5601:5601

Access Kibana at http://localhost:5601.

**Deploy Tempo for Distributed Tracing


kubectl apply -f https://github.com/grafana/tempo/releases/download/v1.5.0/tempo.yaml

**Deploy SigNoz for APM


kubectl apply -f https://github.com/SigNoz/signoz/releases/download/v0.12.0/signoz-kubernetes.yaml

To access SigNoz:


kubectl port-forward svc/signoz-frontend 3301:3301

Access SigNoz at http://localhost:3301.


