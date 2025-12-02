**O que é um POD no Kubernetes?**  
(A menor coisinha viva dentro do cluster)

Beleza, pensa assim: um Pod é tipo um **apartamento** dentro de um condomínio gigante — o cluster. Nada muito complicado: é a menor unidade que o Kubernetes realmente "gerencia".


**A analogia do AP**  
- **Cluster** = Condomínio (um monte de máquinas/nós)  
- **Pod** = Apartamento  
- **Containers** = Moradores que dividem tudo no mesmo AP  
E quando digo dividir, é pra valer:  
- Usam o mesmo IP  
- Mesmo “Wi-Fi do apê” (namespace de rede)  
- Mesma geladeira (volumes)  
- Seguem as mesmas regras do condomínio (security policies)


**Ideias importantes** (aquela que você guarda pra sempre)  

1. Você **nunca** sobe containers direto no Kubernetes — eles sempre vêm dentro de um Pod.  
2. Quer escalar? Não coloca mais gente no mesmo AP. Você clona o AP inteiro — é isso que um **Deployment** faz.  
3. Vários containers no mesmo Pod são tipo “roommates” — os famosos *sidecars* — morando juntos pra fazer uma coisa só funcionar.


**Coisas que muita gente esquece**  
- Pods são **efêmeros**. Se destruir, já era. Não guarde nada importante neles — só memórias passageiras.  
- Containers dentro do mesmo Pod compartilham namespace de rede, hostname e outras coisas do sistema. Compartilhar PID só se você pedir com jeitinho.


**YAML do AP**  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: meu-apartamento
spec:
  containers:
  - name: aplicacao-web
    image: nginx:latest
  - name: coletor-de-logs
    image: busybox
    command: ['sh', '-c', 'tail -f /var/log/nginx/access.log']
```


**Resumão rápido**  
Um Pod não é só um container. É o **ambiente** onde um ou mais containers moram e trabalham juntos, dividindo recursos como bons roommates.  

*Frase que salva em entrevista:*  
> “O Pod é a unidade atômica do Kubernetes.”


### `kubectl get pods` e `kubectl describe pod`  
**Os binóculos e o microscópio do zelador do cluster**

Imagina que você é o zelador do condomínio Kubernetes.  
O `get` é pra dar uma olhada geral nos apartamentos.  
O `describe` é quando você quer investigar até a respiração dentro do Pod.


**`kubectl get pods` — visão geral**  
```bash
kubectl get pods
```
Saída típica:
```
NAME        READY   STATUS    RESTARTS   AGE
meu-app     1/1     Running   0          2d
api-blog    2/2     Running   1          5h
```

**Flags legais**  
- Ver YAML (a planta do AP):  
  ```bash
  kubectl get pods -o yaml
  ```
- Ver labels:  
  ```bash
  kubectl get pods -L app,tier
  ```
- Outros andares (namespaces):  
  ```bash
  kubectl get pods -n kube-system
  kubectl get pods -A
  ```
- Saídas diferentes:  
  ```bash
  kubectl get pods -o name
  kubectl get pods -o wide
  ```


**`kubectl describe pod` — o microscópio**  
```bash
kubectl describe pod giropops
```
Aqui você vê:
- Labels, namespace, quando foi criado
- Containers, volumes, regras de segurança
- IP do Pod, nó onde tá hospedado
- E o mais importante: **EVENTS**  
  Exemplo:
  ```
  Normal  Pulling    kubelet  Pulling image nginx:latest
  Normal  Created    kubelet  Created container
  Normal  Started    kubelet  Started container
  ```
  É tipo o diário de bordo do que rolou no AP.


**Quando usar o quê**  

| Situação | Comando |
|----------|---------|
| Ver panorama geral | `kubectl get pods` |
| Investigar um Pod problemático | `kubectl describe pod` |
| Exportar YAML | `kubectl get pod -o yaml` |
| Ver Pods de sistema | `kubectl get pods -n kube-system` |
| Entender por que falhou | `kubectl describe pod` |


### `kubectl exec` e `kubectl attach`  
**Entrando no apartamento**

Agora você não fica só olhando de fora — entra no AP pra interagir.

**Criando uns Pods pra testar**  
```bash
kubectl run strigus --image nginx --port 80
kubectl get pods -o wide
kubectl run girus --image busybox
```


**`kubectl exec` — entrando pra fazer manutenção**  
```bash
kubectl exec -ti strigus -- bash
kubectl exec girus -- ls -la
```
Dicas:
- `-ti` = terminal interativo (pra você digitar)
- Sempre usa `--` antes do comando
- Em Pods com vários containers:  
  ```bash
  kubectl exec pod -c nome-do-container -- bash
  ```


**`kubectl attach` — ouvindo o processo principal**  
```bash
kubectl attach girus-1 -c girus-1 -ti
```
⚠️ **Atenção:**  
Ctrl+C aqui **MATA** o processo principal.  
Pra sair sem matar: **Ctrl+P, Ctrl+Q**.


### Criando Pod multi-container (na unha)  
Como `kubectl run` não faz isso, a gente escreve o manifesto:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: girus
spec:
  containers:
    - name: alpine
      image: alpine
      command: ["sh", "-c", "while true; do echo 'Alpine rodando...'; sleep 5; done"]
    - name: apache
      image: httpd
      ports:
        - containerPort: 80
```

Aplicar:  
```bash
kubectl apply -f pod.yml
```

Deletar:  
```bash
kubectl delete -f pod.yml
```

Ver logs:  
```bash
kubectl logs girus -c alpine -f
kubectl logs girus -c apache -f
```


### Limitando recursos (pro AP não estourar a luz do prédio)  
Sem limites, um morador pode consumir CPU e memória sem dó.  
Vamos botar regras no condomínio.

**Novo Pod com limites**  
```yaml
limits:
  cpu: 100m
  memory: 128Mi
requests:
  cpu: 10m
  memory: 64Mi
```

Aplicar:  
```bash
kubectl delete -f pod.yml
kubectl apply -f pod-with-limits.yml
kubectl describe pod giropops   # pra ver os limites aplicados
```


**Testando os limites**  
Entrar no Pod:  
```bash
kubectl exec -it giropops -- bash
```

Instalar ferramenta de stress:  
```bash
apt update -y && apt install -y stress
```

Testes de memória:  
```bash
stress --vm-bytes 64M --vm 1    # dentro do limite, suave
stress --vm-bytes 120M --vm 1   # no limite, ainda roda
stress --vm-bytes 130M --vm 1   # estourou — leva SIGKILL (sinal 9)
```

Quando estoura, aparece algo como:  
```
worker got signal 9
```  
Tradução: o sistema matou o processo porque passou do permitido.
