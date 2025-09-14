# Desafio Arquitetura Cloud
Imagine que você é o(a) arquiteto(a) de Nuvem / Engenheiro(a) DevOps em uma startup inovadora chamada "InovaSoluções Tech". A empresa está prestes a lançar uma nova plataforma global de e-commerce que precisa ser altamente disponível, escalável para lidar com picos sazonais (como Black Friday), segura para proteger dados de usuários e transações de pagamento, e otimizada em custos para garantir a sustentabilidade financeira da startup. Além disso a plataforma deve oferecer baixa latência para usuários em diferentes continentes.

Seu desafio é propor uma arquitetura de nuvem para a plataforma da "InovaSoluções Tech", justificando suas escolhas com base nos conceitos aprendidos ao longo das aulas. Sua proposta deve abordar os seguintes pontos:

1- Escolha do Provedor de Nuvem Principal (Ref. Aula 4):

- Selecione um dos principais provedores de nuvem (AWS, Azure, GCP ou OCI).
    
    > A escolha da AWS se deve ao seu amplo portfólio de serviços, à maturidade do ecossistema e ao fato de ser a provedora de nuvem com maior adoção no mercado. Isso garante maior facilidade de encontrar profissionais capacitados e suporte de uma comunidade ampla.
    > 
- Justifique sua escolha considerando os pontos fortes do provedor para uma aplicação de e-commerce global, como portfólio de serviços, presença global, e inovações em áreas relevantes.
    
    > A AWS possui uma gama maior de serviços, uma infraestrutura enorme globalmente falando, serviços de segurança robusta.
    > 
    > 
    > A AWS conta com **presença global robusta,** com **regiões e zonas de disponibilidade distribuídas em vários continentes**, o que facilita a implantação de uma aplicação de e-commerce com baixa latência para clientes de diferentes localidades.
    > 
    > Além disso, a AWS oferece **serviços avançados de segurança**, **observabilidade**, **bancos de dados otimizados para diferentes workloads** e **ferramentas nativas para escalabilidade elástica.**
    > 
    > Todos pontos críticos para um ambiente de comércio eletrônico que precisa lidar com picos de acesso (ex.: Black Friday).
    > 
    > Outro diferencial é a **constante inovação em áreas como inteligência artificial, machine learning, big data e serverless**, que podem agregar valor à evolução da plataforma.
    > 
- Mencione brevemente se consideraria uma estratégia multicloud para algum componente específico e por quê (ou por que não).
    
    > Neste momento, **não seria adotada uma estratégia multicloud**, pois isso aumentaria custos de saída de dados e complexidade operacional. A prioridade é manter a infraestrutura centralizada para garantir **eficiência de custos, simplicidade de gestão e performance**.
    No futuro, poderia-se considerar multicloud apenas para **resiliência crítica** ou **parcerias estratégicas**
    (por exemplo, uso do GCP para serviços avançados de dados ou do Azure para integração com clientes que já utilizem Microsoft 365).
    > 
    

2- Modelos de Serviço (Ref. Aulas 1, 2, 5).

- Para os diferentes componentes da plataforma de e-commerce (ex: front-end web, back-end de processamento de pedidos, catálogo de produtos, sistema de gerenciamento de usuários, banco de dados, funções de notificação, etc.), especifique quais modelos de serviço (IaaS, PaaS, SaaS, FaaS) seriam mais adequados.
    - PaaS
        - Front-End (Kubernetes)
        - Back-End (Kubernetes)
        - Banco de dados (RDS)
        - Processamento de pedidos (Kubernetes)
        - Catálogo de produtos  (Kubernetes)
    - SaaS
        - Sistema de gerenciamento de usuários (Caso fosse um sistema pronto, ex: AWS Cognito, caso não fosse aí seria um PaaS)
    - FaaS
        - Funções de notificação
    
- Justifique suas escolhas em termos de nível de controle, gerenciamento, escalabilidade e responsabilidades (lembre-se da Matriz de Responsabilidade Compartilhada).
    - **IaaS** → quando há necessidade de **controle total sobre o sistema operacional, configuração de rede e monitoramento detalhado da máquina**. Útil para workloads legados ou que exijam customização pesada. Escalabilidade depende do gerenciamento manual ou automações via scripts.
    - **PaaS** → ideal para aplicações modernas (front, back, banco de dados gerenciado). O provedor cuida da infra, patches, escalabilidade automática, enquanto o time de dev foca só no código e na modelagem de dados.
    - **SaaS** → elimina praticamente toda a responsabilidade de infraestrutura e aplicação, você só **consome o serviço pronto** (autenticação, ERP, CRM, catálogo pronto). Perfeito para áreas que não são core de inovação.
    - **FaaS** → funções leves, de curta duração, disparadas por eventos (ex.: fila, upload, trigger de API). Custo baseado em execução, escalabilidade automática e sem necessidade de gestão de servidores.

3- Componentes de Infraestrutura e Arquitetura (Ref. Aulas 3, 5):

Descreva os principais componentes de infraestrutura que sua arquitetura utilizaria:

- Computação: VMs, Contêineres, Funções Serverless? Para quais partes da aplicação?
    - **Contêineres (EKS/Kubernetes):**
        - Front-End
        - Back-End
        - Processamento de Pedidos
        - Catálogo de Produtos
    - **Funções Serverless (AWS Lambda):**
        - Funções de notificação (ex.: envio de e-mails e push notifications).
    - **Justificativa:** Opta-se por contêineres e serverless para reduzir custos e complexidade, evitando a necessidade de gerenciar VMs (IaaS). Assim, garante-se escalabilidade automática, agilidade no desenvolvimento e melhor utilização dos recursos.
- Armazenamento: Tipos de armazenamento (Object, Block, File) para diferentes tipos de dados (imagens de produtos, logs, backups, discos de VMs).
    - **Object Storage (Amazon S3):**
        - Armazenamento de imagens, vídeos e arquivos estáticos do catálogo de produtos.
    - **Logs e auditoria:**
        - Amazon S3 ou Amazon CloudWatch Logs para centralização e análise.
    - **Backups e DR:**
        - Snapshots automáticos dos volumes persistentes (EBS/EFS) vinculados a contêineres e bancos de dados, garantindo recuperação em caso de falhas.
    - **File Storage (EFS):**
        - Opcional, para compartilhamento de arquivos entre contêineres se necessário.
- Rede: Como você estruturaria a rede virtual (VPCs/Vnets, sub-redes públicas e privadas, roteamento, gateways)? Consideraria o uso de CDNs?
    - **VPC dedicada** com sub-redes públicas e privadas:
        - **Sub-redes privadas:** aplicações críticas (back-end, banco de dados).
        - **Sub-redes públicas:** somente serviços que exigem acesso externo (front-end, load balancer).
    - **Segurança:** regras de **Security Groups e NACLs** aplicadas com princípio de menor privilégio.
    - **Gateways:**
        - Internet Gateway para saída pública.
        - NAT Gateway para permitir que instâncias privadas acessem a internet de forma segura.
    - **CDN (Amazon CloudFront):** distribuição de conteúdo estático e dinâmico globalmente, reduzindo latência para clientes de diferentes regiões.
- Banco de Dados: Qual(is) tipo(s) de banco de dados (SQL, NoSQL, Data Warehouse) seriam usados para quais dados (transações, catálogo de produtos, análise de vendas)? Seriam serviços gerenciados?
    - **Transações (pedidos, usuários):**
        - Banco relacional gerenciado (Amazon RDS – ex.: Aurora ou PostgreSQL).
    - **Catálogo de produtos:**
        - Banco relacional (RDS) com **cache em Amazon ElastiCache (Redis)** para melhorar performance de consultas frequentes.
    - **Logs e análise de vendas:**
        - Armazenamento em **NoSQL (Amazon DynamoDB)** ou Data Warehouse (Amazon Redshift) para relatórios e BI.
    - **Justificativa:** O uso de serviços gerenciados reduz a sobrecarga de manutenção e garante alta disponibilidade.
- Balanceamento de Carga: Que tipos de balanceadores de carga (L4, L7) seriam usados e onde?
    - **Network Load Balancer (L4):**
        - Distribuição de tráfego em nível de rede (TCP/UDP) para cenários de baixa latência e alto throughput.
    - **Application Load Balancer (L7):**
        - Distribuição em nível de aplicação (HTTP/HTTPS), com suporte a roteamento inteligente por caminho/host, SSL termination e integração com contêineres (EKS).
    - **Justificativa:** A combinação de L4 + L7 permite lidar tanto com alto volume de conexões quanto com regras avançadas de aplicação.

4- Escalabilidade e Alta Disponibilidade (Ref. Aulas 3, 5, 6):

- Como sua arquitetura garantiria escalabilidade horizontal e/ou vertical para os diferentes componentes, especialmente para os picos de demanda?
    - A aplicação está baseada principalmente em **contêineres orquestrados no Amazon EKS (Kubernetes)**.
    - Isso permite **escalabilidade horizontal automática**, adicionando ou removendo **pods** e **nós de trabalho (workers)** conforme a demanda.
    - Em cenários de necessidade, também é possível aplicar **escalabilidade vertical** (aumentando recursos de CPU/RAM dos nós).
    - Funções Serverless (AWS Lambda) escalam automaticamente sob demanda, sem necessidade de provisionamento manual.
- Como você utilizaria Regiões e Zonas de Disponibilidade para garantir alta disponibilidade e resiliência?
    - A arquitetura será implantada em uma **região principal da AWS** (ex.: **us-east-1 – Virgínia do Norte**).
    - Todos os componentes críticos serão distribuídos em **mínimo 2 Zonas de Disponibilidade (AZs)** distintas, garantindo tolerância a falhas locais.
    - Serviços gerenciados como **RDS (Multi-AZ)** e **ElastiCache** também serão configurados para replicação entre zonas, reduzindo o risco de indisponibilidade.
    - O uso de **Amazon CloudFront (CDN)** aumenta a disponibilidade de conteúdos estáticos globalmente, reduzindo a dependência da região principal em acessos comuns (ex.: imagens de produtos).
- Mencione brevemente uma estratégia de Recuperação de Desastres (DR).
    - **Backups automáticos** dos bancos de dados (RDS snapshots e backups contínuos do DynamoDB).
    - **Armazenamento de imagens e dados críticos no Amazon S3**, com replicação habilitada para outra região (S3 Cross-Region Replication).
    - Estratégia de **failover multi-região** opcional: em caso de desastre completo na região principal, tráfego pode ser redirecionado via **Route 53** para uma região secundária.
    - Para contêineres, **infra como código (Terraform/CloudFormation)** garante rápida reimplantação em outra região caso necessário.

5- Segurança (Ref. Aulas 5, 6):

Quais mecanismos de segurança seriam implementados? Considere:

- Gerenciamento de identidade e Acesso (IAM), Autenticação (MFA).
- Criptografia de dados (em trânsito e em repouso).
- Segurança de rede (Security Groups/NSGs, NACLs, WAFs).
- Como o Modelo de Responsabilidade Compartilhada se aplica à sua arquitetura?
    
    
    **Gerenciamento de Identidade e Acesso (IAM)**
    
    - Implementação do princípio de **menor privilégio** em todas as permissões.
    - **IAM Roles** separados por função (desenvolvimento, infraestrutura, administração).
    - **Tag-based Access Control (ABAC):** permissões aplicadas com base em **tags de recursos** (ex.: `env=dev`, `env=prod`).
    - **MFA (Multi-Factor Authentication)** obrigatório para todos os usuários, especialmente contas administrativas e root.
    - **Acesso root restrito** apenas a situações excepcionais, com usuários específicos.
    
    **Criptografia de Dados**
    
    - **Em repouso:**
        - Dados em **S3, RDS, DynamoDB e EBS** criptografados usando **KMS (AWS Key Management Service)**.
    - **Em trânsito:**
        - Todo tráfego exposto ao público protegido com **TLS 1.2+ (HTTPS)**.
        - Comunicação interna entre serviços configurada com criptografia sempre que suportado (ex.: RDS, Redis, API Gateway).
        
    
    **Segurança de Rede**
    
    - **VPC isolada** com sub-redes privadas e públicas.
    - **Security Groups (SGs):** regras específicas por aplicação/serviço, aplicando princípio de menor privilégio.
    - **Network ACLs (NACLs):** regras adicionais em nível de sub-rede para reforçar proteção.
    - **WAF (Web Application Firewall):** proteção contra ataques de camada 7 (SQL Injection, XSS, DDoS básico).
    - **Shield (opcional):** para mitigação avançada contra DDoS em cargas críticas.
    
    **Modelo de Responsabilidade Compartilhada**
    
    - **AWS** é responsável pela **segurança da nuvem** (infraestrutura física, rede global, hipervisor, disponibilidade de serviços gerenciados).
    - **Cliente (nós)** é responsável pela **segurança na nuvem**, incluindo:
        - Configuração correta de IAM, SGs, NACLs, WAF.
        - Proteção dos dados (criptografia, backup, retenção).
        - Monitoramento e resposta a incidentes (CloudWatch, GuardDuty, CloudTrail).
    - Aplicando esse modelo, minimizamos riscos de configuração incorreta e acessos indevidos.

6- Otimização de Custos e Governança (Ref. Aula 6):

- Quais estratégias de otimização de custos (FinOps) você consideraria desde o início (ex: Right-sizing, instâncias reservadas/spot, serverless, etc.) ?
    - **Right-Sizing:** monitorar métricas de uso (CPU, memória, I/O) e ajustar o tamanho das instâncias e recursos de acordo com a necessidade real.
    - **Instâncias Spot e Savings Plans:** usar **instâncias Spot** para workloads tolerantes a interrupções (ex.: processamento em lote) e **Savings Plans/Reserved Instances** para workloads de longa duração e previsíveis (ex.: banco de dados).
    - **Serverless e Auto Scaling:** priorizar **Lambda** e **Auto Scaling no EKS** para pagar apenas pelo uso, evitando recursos ociosos.
    - **Armazenamento sob demanda:** configurar **lifecycle policies no S3** para mover dados antigos para classes mais baratas (ex.: S3 Glacier).
    - **Monitoramento de custos:** uso de **AWS Cost Explorer e Budgets** para acompanhar consumo e alertar sobre desvios.
- Como a governança (ex: tagging) seria aplicada para gerenciar recursos e custos?
    - **Tagging padronizado:**
        - `Owner`, `Environment (dev, test, prod)`, `CostCenter`, `Application`, `Sector (infra, dev, devops)`.
        - Permite rastrear custos por setor/equipe e identificar recursos órfãos ou subutilizados.
    - **Organização multi-conta (AWS Organizations):** separar ambientes (produção, desenvolvimento, testes) para melhor controle de custos, segurança e compliance.
    - **Políticas de Service Control Policies (SCPs):** limitar uso de serviços que não estejam autorizados pelo time de governança.
    - **Auditoria contínua:** uso de **AWS Config** e **Trusted Advisor** para identificar desperdícios, más práticas e oportunidades de otimização.

7- Boas práticas
(Well-Architected Framework) (Ref. Aulas 5, 6):

- De forma sucinta, como sua arquitetura proposta se alinha com os principais pilares do Well-Architected Framework (Excelência Operacional, Segurança, Confiabilidade, Eficiência de perfomance, Otimização de Custos)?
    - **Excelência Operacional:** uso de **Infra as Code (IaC)** (CloudFormation/Terraform) para padronização e rápida reimplantação, além de monitoramento centralizado via **CloudWatch, CloudTrail e GuardDuty** para auditoria e melhorias contínuas.
    - **Segurança:** aplicação do **princípio de menor privilégio no IAM**, autenticação MFA, **criptografia em repouso e em trânsito**, **WAF e Security Groups restritivos**, além de alinhamento ao **modelo de responsabilidade compartilhada**.
    - **Confiabilidade:** alta disponibilidade garantida por **Multi-AZ**, failover automatizado em bancos de dados gerenciados, uso de **CloudFront** para resiliência no tráfego global e plano de **Disaster Recovery** com backups e replicação cross-region.
    - **Eficiência de Performance:** aplicações distribuídas em **EKS (contêineres)** e **Lambda (serverless)** para escalabilidade automática sob demanda, uso de **cache em Redis** para consultas frequentes, e **CDN** para baixa latência global.
    - **Otimização de Custos:** estratégias de **FinOps** aplicadas desde o início, como **Right-Sizing, instâncias Spot/Savings Plans, políticas de lifecycle no S3**, e **tagging padronizado** para rastrear consumo por setor e identificar oportunidades de redução de custos.
