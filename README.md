# Cluster Kubernetes com Rancher

## Passo a passo

1. Provisionar 4 servidores (2-4 vCPU, 4-6 GB memory, 30 GB disk SSD General purpose, OS Ubuntu)
    - 1 Rancher server
    - 3 para cluster k8s

Para este projeto, iremos utilizar a insfraestrutura da AWS e, afim de manter conformidade utilizaremos IaaS com o ClouFormation para provisionar o ambiente.
Observações importantes: Verificamos que a t3.medium é a opção mais em conta (nesta data) que compre os pré-requisitos.   


2. Registrar domínio
    - rancher.domain
    - app1.domain
    - app2.domain
    