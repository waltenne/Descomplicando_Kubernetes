# Desafio Pod Faminto

## Parte 1: Preparando o Terreno

Criação do namespace


```bash
~/Documents/Descomplicando_Kubernetes/day02/desafio main* ❯ kubectl create namespace treinamento-ch2
namespace/treinamento-ch2 created
~/Documents/Descomplicando_Kubernetes/day02/desafio main* ❯ kubectl get namespaces
NAME                 STATUS   AGE
default              Active   6h23m
kube-node-lease      Active   6h23m
kube-public          Active   6h23m
kube-system          Active   6h23m
local-path-storage   Active   6h23m
treinamento-ch2      Active   4s
```
## Parte 2: O Teste de Estresse (O Erro Esperado)

Criação do [pod-faminto.yml](pod-faminto.yaml) e aplicação do mesmo, e acompanhamento do erro


```bash
~/Documents/Descomplicando_Kubernetes/day02/desafio main* ❯ kubectl apply -f pod-faminto.yaml
pod/pod-faminto created
~/Documents/Descomplicando_Kubernetes/day02/desafio main* ❯ kubectl get pod -n treinamento-ch2 -w

NAME          READY   STATUS              RESTARTS   AGE
pod-faminto   0/1     ContainerCreating   0          3s
pod-faminto   0/1     OOMKilled           0          5s
pod-faminto   0/1     OOMKilled           1 (2s ago)   7s
pod-faminto   0/1     CrashLoopBackOff    1 (1s ago)   8s
^C%                                                                                                                                                                               
~/Documents/Descomplicando_Kubernetes/day02/desafio main* 10s ❯ kubectl describe pod pod-faminto -n treinamento-ch2
LAST SEEN   TYPE      REASON      OBJECT            MESSAGE
14s         Normal    Scheduled   pod/pod-faminto   Successfully assigned treinamento-ch2/pod-faminto to giropops-worker
9s          Normal    Pulling     pod/pod-faminto   Pulling image "polinux/stress"
9s          Normal    Pulled      pod/pod-faminto   Successfully pulled image "polinux/stress" in 4.500768038s (4.500776143s including waiting)
7s          Normal    Created     pod/pod-faminto   Created container stress
7s          Normal    Started     pod/pod-faminto   Started container stress
7s          Normal    Pulled      pod/pod-faminto   Successfully pulled image "polinux/stress" in 1.416066397s (1.416074542s including waiting)
6s          Warning   BackOff     pod/pod-faminto   Back-off restarting failed container stress in pod pod-faminto_treinamento-ch2(d8ec8819-3d23-4553-9351-7f14e8364849)
```
Aplicando a correção

```bash
~/Documents/Descomplicando_Kubernetes/day02/desafio main* ❯ k delete -f pod-faminto.yaml                       
pod "pod-faminto" deleted from treinamento-ch2 namespace
~/Documents/Descomplicando_Kubernetes/day02/desafio main* ❯ k apply -f pod-comportado.yaml 
pod/pod-comportado created
~/Documents/Descomplicando_Kubernetes/day02/desafio main* ❯ 
~/Documents/Descomplicando_Kubernetes/day02/desafio main* ❯ kubectl get pod -n treinamento-ch2 -w              
NAME             READY   STATUS    RESTARTS   AGE
pod-comportado   1/1     Running   0          10s
```

#Perguntas para Reflexão (Responda mentalmente ou anote)

1) Quando o Pod foi morto na "Parte 2", qual mecanismo do Linux (que vimos no Cap 1) foi acionado pelo Kubernetes/Container Runtime para parar o processo?

Foi o OOM Killer do Linux.

É tipo aquele segurança do condomínio que aparece quando você faz barulho demais depois das 22h. O container tentou usar 250MB de RAM, mas só tinha permissão pra 200MB. O OOM Killer viu isso e disse: "Chega!" – e desligou a festa na hora.


2) Qual a diferença prática entre definir um request de 100Mi e um limit de 200Mi? O que o Scheduler usa para decidir onde colocar o Pod?

Request (100Mi) = "Preciso de 1 vaga na garagem pra morar aqui"
→ O porteiro (scheduler) só te deixa entrar se tiver vaga

Limit (200Mi) = "Posso usar no máximo 2 vagas"
→ O síndico (kubelet) te corta se você tentar estacionar 3 carros

Resumindo:

> Request decide ONDE você mora.
> Limit decide QUANTO você pode bagunçar.