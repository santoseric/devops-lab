# Cluster Kubernetes com Rancher

## Passo a passo

1. Provisionar 4 servidores (2-4 vCPU, 4-6 GB memory, 30 GB disk SSD General purpose, OS Ubuntu)
    - 1 Rancher server
    - 3 para cluster k8s

2. Registrar domínio
    - rancher.domain
    - app1.domain
    - app2.domain
    
 3. Buildar imagens
    - Redis
    - Node
    - Nginx


 4. Instalar o Rancher Single Node no Rancher Server
    - 
    ```
    docker run -d --restart=unless-stopped -v /opt/rancher:/var/lib/rancher -p 80:80 -p 443:443 --privileged rancher/rancher:latest
    ```
    - Nessa altura, o Rancher já pode ser acessado através da URL http://rancher.domain
    - Criar cluster K8s através do Rancher, aguardar pois o provisionamento pode demorar um pouco. Aproveite enquanto aguarda para experiementar o [DataDog](https://www.datadoghq.com) para monitorar o seu cluster ;)
    - 
