cat <<'EOF' > evidencias/resultados.md
# Evidências de Execução — Simulado Cloud

## 1. Cluster e Namespace
root@controlplane:~$ mkdir -p manifests
kubectl get nodes
kubectl create namespace simulado-nuvem
kubectl get namespace simulado-nuvem
NAME           STATUS   ROLES           AGE     VERSION
controlplane   Ready    control-plane   6d12h   v1.37.1
node01         Ready    <none>          6d12h   v1.37.1
namespace/simulado-nuvem created
NAME             STATUS   AGE
simulado-nuvem   Active   0s

## 2. RBAC (list permitido, delete negado)
```text
# Cole aqui a saída dos dois comandos: kubectl auth can-i ...
```

## 3. 2 réplicas prontas e página na versão 1
```text
# Cole aqui a saída de: kubectl -n simulado-nuvem get deployment,pods,service
# E a saída de: kubectl -n simulado-nuvem exec cliente -- wget -qO- http://web (Versao 1)
```

## 4. Probes, logs e diagnóstico
```text
# Cole aqui trechos de: kubectl -n simulado-nuvem describe deployment web
# E a saída de: kubectl -n simulado-nuvem logs deployment/web
```

## 5. Escala e reposição do Pod
```text
# Cole aqui a saída de get pods (3 réplicas), após excluir 1 Pod (novo Pod criado) e o wget funcionando
```

## 6. Página na versão 2, falha intencional e recuperação por rollback
```text
# Cole aqui a saída do wget (Versao 2), o erro ImagePullBackOff/ErrImagePull e o retorno após rollout undo
```

## 7. Job concluído e seu log
```text
# Cole aqui a saída de: kubectl -n simulado-nuvem logs job/teste-web
```
EOF

