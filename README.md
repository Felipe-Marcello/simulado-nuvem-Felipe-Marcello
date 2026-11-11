Felipe Antonio Souza Marcello 
4º semestre TADS 
11/11/2026

Preparamos o ambiente separando as configurações externas (o conteúdo HTML da página via ConfigMap e uma credencial fictícia via Secret) e aplicamos o princípio do menor privilégio criando uma identidade (ServiceAccount) com permissão restrita apenas para visualizar Pods (Role e RoleBinding).
Em seguida, colocamos a aplicação no ar usando um Deployment com duas réplicas do Nginx, protegido por sondas de integridade (readinessProbe e livenessProbe) e exposto internamente por um Service do tipo ClusterIP. Por fim, validamos a resiliência do cluster na prática: testamos a escala manual, comprovamos a autorrecuperação ao apagar um Pod, atualizamos a página para a Versão 2, recuperamos a aplicação de uma falha proposital de imagem usando rollback e automatizamos a verificação de disponibilidade com um CronJob e um Job

1. Qual é a diferença entre contêiner, Pod e Deployment?
Contêiner: É o menor nível, onde o código e as dependências da aplicação realmente rodam isolados
Pod: É a menor unidade gerenciável pelo Kubernetes. Ele funciona como um "envelope" ao redor de um ou mais contêineres, compartilhando rede (um único IP) e armazenamento entre eles.
Deployment: É o controlador que gerencia o ciclo de vida dos Pods. Ele garante que a quantidade desejada de réplicas esteja sempre rodando e coordena atualizações ou reversões (rollbacks) sem tirar o sistema do ar.

2. Por que ConfigMap e Secret têm finalidades diferentes?
Ambos servem para tirar configurações de dentro do código da aplicação, mas se diferenciam pela sensibilidade da informação: ConfigMap armazena configurações comuns e não confidenciais em texto claro e o Secret armazena dados protegidos, como senhas, chaves de API e tokens.

3. Qual a diferença entre readiness e liveness?
Readiness verifica se a aplicação está pronta para receber requisições dos usuários. Se falhar, o Kubernetes apenas remove o Pod da lista do Service para que ele pare de receber tráfego até se recuperar, sem reiniciá-lo.
Liveness verifica se a aplicação está viva e respondendo. Se falhar, o Kubernetes entende que o contêiner quebrou, mata o processo e o reinicia automaticamente.

4. O que ocorreu após a exclusão do Pod?
Assim que o Pod foi apagado manualmente, o controlador (ReplicaSet, gerenciado pelo Deployment) detectou imediatamente que havia apenas 1 Pod ativo, enquanto o estado desejado definido no manifesto era de 2 réplicas. Para corrigir essa diferença, o Kubernetes criou automaticamente um novo Pod saudável em poucos segundos, mantendo a aplicação no ar e respondendo normalmente pelo Service sem impacto para o usuário.

5. O que o rollback recuperou? A página voltou à versão 1? Explique.

O comando kubectl rollout undo recuperou apenas a revisão anterior do Deployment (voltando a imagem quebrada nginx:tag-inexistente-simulado para a imagem funcional nginx:alpine), o que fez os Pods voltarem a rodar saudáveis.


6. Qual a diferença entre Deployment, Job e CronJob?
Deployment: É usado para aplicações contínuas que devem ficar rodando sem parar, como servidores web e APIs.
Job: É usado para tarefas pontuais que têm começo, meio e fim. Ele roda até concluir a tarefa com sucesso e depois encerra o Pod.
CronJob: É um agendador de Jobs. Ele cria e executa um Job automaticamente em horários ou intervalos programados.

