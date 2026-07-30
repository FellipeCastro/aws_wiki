# 📝 Resposta do Laboratório: A Wiki Perdida dos Arquivos Corporativos

> Preencha este arquivo com a sua proposta de solução.
>
> Sua resposta deve explicar como transformar os documentos brutos da pasta `raw/` em uma Wiki Corporativa Inteligente, pesquisável e segura usando apenas serviços da AWS.

---

## 👤 Identificação

**Nome:**  
Fellipe da Silva Castro

**Data:**  
30/07/2026

**Link do repositório:**  
(https://github.com/FellipeCastro/aws_wiki/)

---

# ✅ Quest 1: O Mapa dos Arquivos Perdidos

## 1.1 Formatos encontrados na pasta `raw/`

Descreva quais tipos de arquivos existem dentro da pasta `raw/`.

```md
Exemplo de como responder, com o formato e o que ele implica:
- <extensao>: <nasce digital ou precisa de OCR?>, <o que da para extrair>
```

> Abra a pasta e liste o que voce encontrou de fato. Esta quest avalia a sua
> leitura do acervo, entao a resposta certa e a que corresponde aos arquivos.

**Sua resposta:**

```md
- PDF: da pra extrair o texto sem OCR
- PNG: imagem pura, precisa de OCR
- CSV: tabela estruturada, fonte de dados 
```

---

## 1.2 Principais desafios encontrados

Explique quais dificuldades esses documentos podem apresentar.

```md
Exemplo:
- Arquivos sem padrão de nomenclatura
- Documentos escaneados com baixa qualidade
- Textos manuscritos ou parcialmente ilegíveis
- Atas com estruturas diferentes
- Informações importantes espalhadas em vários formatos
```

**Sua resposta:**

```md
- Arquivos sem padrão de nomenclatura
- Documentos escaneados com baixa qualidade
- Textos manuscritos ou parcialmente ilegíveis
- Atas com estruturas diferentes
- Informações importantes espalhadas em vários formatos
```

---

## 1.3 Informações importantes a serem extraídas

Liste quais informações precisam ser identificadas para transformar os documentos em conhecimento pesquisável.

**Sua resposta:**

```md
datas, participantes, decisões, responsáveis, prazos, riscos, projetos citados
```

---

## 1.4 Estratégia de classificação inicial

Como você classificaria os documentos sem depender de subpastas dentro de `raw/`?

**Sua resposta:**

```md
Classificando por metadado, não por caminho de pasta.
```

---

# ✅ Quest 2: O Portal de Entrada na AWS

## 2.1 Armazenamento dos arquivos brutos

Explique como os arquivos da pasta `raw/` seriam enviados e armazenados na AWS.

Serviços que você pode considerar:

- Amazon S3
- AWS IAM
- AWS KMS
- Amazon S3 Versioning
- Amazon S3 Lifecycle

**Sua resposta:**

```md
Bucket S3 "raw" (imutável) recebe os arquivos originais tal como estão.
```

---

## 2.2 Preservação dos arquivos originais

Explique como garantir que os arquivos originais sejam mantidos intactos e rastreáveis.

**Sua resposta:**

```md
Nunca sobrescrever o bucket raw. Todo processamento deve gerar novos objetos em um bucket separado.
```

---

## 2.3 Extração de texto dos documentos

Explique como cada tipo de arquivo seria processado.

Considere:

- PDFs escaneados;
- Imagens;
- PDFs digitais;
- Arquivos `.txt`;
- Arquivos `.docx`;
- Arquivos `.md`.

Serviços que você pode considerar:

- Amazon Textract
- AWS Lambda
- AWS Step Functions
- Amazon S3
- Amazon CloudWatch

**Sua resposta:**

```md
- Um evento de upload no S3 dispara uma Lambda
- A Lambda verifica a extensão/tipo do arquivo:
- .pdf com camada de texto → passa pelo Textract, mas sem precisar do modo de análise de imagem.
- .png (escaneado) → chama Amazon Textract para OCR.
- .csv → vai para o Textrack que lê como tabela estruturada.
- Step Functions para fluxo com decisão condicional e retry/tratamento de erro nativo.
```

---

## 2.4 Tratamento de falhas

Explique como sua solução identificaria e registraria erros de processamento.

**Sua resposta:**

```md
Step Functions com estados de erro nativos (catch/retry)
```

---

# ✅ Quest 3: A Relíquia dos Metadados

## 3.1 Padronização dos textos processados

Explique como os textos extraídos seriam limpos, normalizados e preparados para consulta.

**Sua resposta:**

```md
Definie um formato único de saída -. JSON - para todo texto extraído, independente da origem. Removendo quebras de linha duplicadas, cabeçalhos/rodapés repetidos, normalizando encoding.
```

---

## 3.2 Metadados propostos

Defina quais metadados você extrairia de cada documento.

| Metadado | Por que ele é importante? |
|---|---|
| Nome do documento | Identificação do documento |
| Tipo do documento | Da onde vieram os dados |
| Data identificada | Quando vieram os dados |
| Tema principal | Para uma busca rápida |
| Participantes | Quem estava envolvido |
| Decisões tomadas | Resultados da reunião |
| Responsáveis | Quem FEZ isso |
| Próximos passos | O que precisa ser feito agora |
| Nível de confidencialidade | É confiável ou não |
| Caminho do arquivo original | Acesso aos dados brutos |

Adicione outros metadados, se necessário.

---

## 3.3 Uso de IA para enriquecimento dos documentos

Explique como o Amazon Bedrock poderia ajudar a identificar temas, decisões, responsáveis, pendências e resumos dos documentos.

**Sua resposta:**

```md
Com o texto extraído, é só  mandar esse texto para um modelo - Bedrock - para que ele infira tema, decisões, responsáveis, pendências
```

---

## 3.4 Armazenamento dos metadados

Explique onde os metadados seriam armazenados e como seriam conectados aos documentos originais.

Serviços que você pode considerar:

- Amazon S3
- Amazon DynamoDB
- AWS Glue Data Catalog
- Amazon Bedrock Knowledge Bases

**Sua resposta:**

```md
Sempre manter o campo que aponta pro objeto original no S3 — é o que garante rastreabilidade.
```

---

# ✅ Quest 4: O Oráculo da Wiki Inteligente

## 4.1 Estratégia de indexação

Explique como os documentos seriam divididos em trechos menores e preparados para busca semântica.

**Sua resposta:**

```md
Dividir os textos processados em pedaços menores (ex. por parágrafo ou por limite de tokens) antes de gerar embeddings. Documentos muito longos (a ata de 5 páginas) precisam ser quebrados; senão a busca fica imprecisa.
```

---

## 4.2 Busca semântica e base vetorial

Explique como embeddings seriam gerados e onde seriam armazenados.

Serviços que você pode considerar:

- Amazon Bedrock Knowledge Bases
- Amazon OpenSearch Serverless
- Amazon Aurora PostgreSQL com pgvector
- Amazon S3 Vectors
- Modelos de embeddings no Amazon Bedrock

**Sua resposta:**

```md
Amazon Bedrock Knowledge Bases, apontando ele para o bucket S3 processado, ele cuida de chunking + geração de embeddings automaticamente (usando um modelo de embeddings do Bedrock) + indexação.
```

---

## 4.3 Geração de respostas com IA

Explique como a Wiki responderia perguntas em linguagem natural com base nos documentos originais.

Considere explicar:

- Como a pergunta do usuário seria recebida;
- Como os trechos relevantes seriam recuperados;
- Como o Amazon Bedrock geraria a resposta;
- Como a resposta indicaria as fontes utilizadas.

**Sua resposta:**

```md
Usuário faz pergunta → vai para um Bedrock Agent → o Knowledge Base busca os chunks mais relevantes na base vetorial → esses chunks + a pergunta são enviados como contexto para o modelo do Bedrock → o modelo gera a resposta citando os documentos-fonte
```

---

## 4.4 Interface de consulta

Proponha como os usuários acessariam essa Wiki Inteligente.

Serviços que você pode considerar:

- Amazon Q Business
- AWS Amplify
- Amazon API Gateway
- AWS Lambda
- Amazon Cognito

**Sua resposta:**

```md
API Gateway + Lambda expõe um endpoint que chama o Bedrock Agent.
```

---

## 4.5 Segurança, auditoria e monitoramento

Explique como controlar acesso, proteger dados, auditar consultas e monitorar custos, erros e qualidade das respostas.

Serviços que você pode considerar:

- AWS IAM
- AWS KMS
- Amazon Cognito
- AWS CloudTrail
- Amazon CloudWatch
- Amazon Macie
- AWS Cost Explorer

**Sua resposta:**

```md
IAM com least privilege em cada etapa, CloudTrail para auditoria de quem consultou o quê, e CloudWatch para métricas de erro/custo/latência.
```

---

# 🧩 Arquitetura Final da Solução

Agora reúna tudo em uma visão única.

## 1. Visão geral

Explique em poucas linhas a ideia central da sua arquitetura.

**Sua resposta:**

```md
A ideia é automatizar a transformação de documentos brutos (atas, escaneados ou digitais) em uma base de conhecimento pesquisável.
```

---

## 2. Serviços AWS utilizados

| Serviço AWS | Papel na solução |
|---|---|
Amazon S3	| Armazena os documentos brutos (raw/), os processados e os arquivos de saída, servindo como camada central de dados do pipeline |
Amazon Textract	| Extrai texto de documentos escaneados ou em imagem, incluindo tabelas e formulários, quando o texto não está disponível digitalmente |
Amazon Bedrock	| Executa os modelos de linguagem responsáveis por resumir conteúdo, extrair metadados e gerar respostas às perguntas dos usuários | 
Amazon Bedrock | Knowledge Bases	Mantém o índice vetorial dos documentos processados, permitindo busca semântica e respostas fundamentadas (RAG) |
AWS Lambda |	Executa as etapas de processamento (limpeza de texto, extração de metadados, disparo do Textract) de forma serverless |
AWS Step Functions |	Orquestra o fluxo completo entre as etapas, controlando a ordem de execução e tratando falhas e reprocessamentos |
Amazon CloudWatch	| Monitora execuções, registra logs de erro e permite configurar alertas em caso de falhas no pipeline |
AWS IAM |	Define permissões granulares entre os serviços, garantindo que cada componente acesse apenas o necessário |
AWS KMS	| Criptografa os documentos e metadados armazenados, protegendo dados sensíveis em repouso |

---

## 3. Fluxo de dados de ponta a ponta

Descreva o caminho dos dados desde a pasta `raw/` até a Wiki Inteligente.

```md
Exemplo de estrutura:

1. Arquivos estão inicialmente na pasta raw/
2. Arquivos são enviados para o Amazon S3
3. Documentos escaneados passam pelo Amazon Textract
4. Arquivos digitais têm seus textos extraídos
5. Textos são limpos e padronizados
6. Metadados são extraídos
7. Conteúdos são indexados em uma base pesquisável
8. Usuário pesquisa na Wiki
9. IA responde com base nos documentos originais
```

**Sua resposta:**

```md
1. Documentos chegam na pasta raw/, organizados por origem ou data
2. Um upload (ou processo automatizado) envia os arquivos para o Amazon S3
3. O AWS Step Functions detecta o novo arquivo e inicia o fluxo de processamento
4. Documentos escaneados são enviados ao Amazon Textract para extração de texto e tabelas
5. Documentos já digitais têm o texto extraído diretamente via Lambda
6. Uma função Lambda limpa e padroniza o texto (remove ruído, normaliza formatação)
7. Metadados (data, tipo de documento, participantes, decisões) são extraídos com apoio do Amazon Bedrock
8. O conteúdo tratado é armazenado no S3 processado e indexado na Bedrock Knowledge Base
9. O usuário faz uma pergunta na interface da Wiki Inteligente
10. O Bedrock consulta a Knowledge Base, recupera os trechos relevantes e gera uma resposta citando os documentos originais
11. CloudWatch registra cada etapa da execução para auditoria e monitoramento
```

---

## 4. Diagrama textual da arquitetura

Crie um diagrama simples usando texto.

```md
Exemplo:

raw/ → Amazon S3 → Lambda/Step Functions → Textract → S3 Processado → Bedrock Knowledge Bases → Interface de Consulta → Usuário Final
```

**Sua resposta:**

```md
raw/
  → Amazon S3 (bruto)
    → AWS Step Functions (orquestração)
      → AWS Lambda → Amazon Textract (se necessário)
      → AWS Lambda (limpeza + metadados via Bedrock)
    → Amazon S3 (processado)
    → Amazon Bedrock Knowledge Bases (indexação vetorial)
  → Interface de Consulta (chat/wiki)
    → Amazon Bedrock (geração de resposta com RAG)
  → Usuário Final

(CloudWatch monitora todas as etapas | IAM/KMS protegem acesso e dados em cada camada)
```

---

## 5. Riscos e limitações

Liste possíveis desafios da sua solução.

```md
Exemplo:
- Documentos ilegíveis podem prejudicar a extração de texto.
- OCR pode gerar erros em documentos com baixa qualidade.
- Custos podem aumentar conforme o volume de documentos.
- Metadados inferidos por IA podem precisar de validação humana.
- Respostas geradas por IA devem sempre referenciar documentos de origem.
```

**Sua resposta:**

```md
- Documentos manuscritos ou com baixa qualidade de digitalização podem gerar extrações incompletas mesmo com Textract.
- Erros de OCR podem se propagar para os metadados e, consequentemente, para as respostas da IA.
- O custo do pipeline cresce com o volume de documentos e a frequência de consultas ao Bedrock.
- Metadados gerados automaticamente (datas, responsáveis, decisões) podem exigir revisão humana antes de serem confiáveis para uso oficial.
- Respostas geradas por IA podem "alucinar" se a base de conhecimento estiver desatualizada ou incompleta, por isso é essencial exigir citação da fonte.
- Documentos muito longos podem exceder limites de contexto do modelo, exigindo estratégias de chunking bem ajustadas.
```

---

## 6. Melhorias futuras

Descreva como a solução poderia evoluir.

```md
Exemplo:
- Criar uma interface web para consulta.
- Criar um chat interno para perguntas sobre atas.
- Adicionar controle de acesso por departamento.
- Criar dashboard de decisões e pendências.
- Gerar alertas automáticos sobre ações em aberto.
- Integrar com ferramentas corporativas.
```

**Sua resposta:**

```md
- Desenvolver uma interface web dedicada para consulta e visualização dos documentos originais.
- Implementar um chat interno integrado ao Slack ou Teams para perguntas rápidas sobre atas e decisões.
- Adicionar controle de acesso por departamento ou nível hierárquico usando IAM e grupos de usuários.
- Criar um dashboard executivo com decisões tomadas, pendências e prazos extraídos automaticamente.
- Configurar alertas automáticos (via SNS) para ações em aberto próximas do vencimento.
- Integrar o pipeline com ferramentas corporativas já usadas pela equipe, como Jira ou Confluence.
- Adicionar suporte multilíngue para documentos e consultas em diferentes idiomas.
```

---

# 🧠 Checklist Final

Antes de entregar, confirme se sua solução responde:

- [ ] Como transformar documentos escaneados em texto?
- [ ] Como lidar com diferentes formatos dentro da mesma pasta `raw/`?
- [ ] Como armazenar os documentos originais?
- [ ] Como preservar a rastreabilidade entre resposta e documento fonte?
- [ ] Como organizar metadados?
- [ ] Como criar busca semântica?
- [ ] Como usar Amazon Bedrock na solução?
- [ ] Como proteger documentos sensíveis?
- [ ] Como monitorar falhas?
- [ ] Como a empresa usaria essa Wiki no dia a dia?

---

# 🏁 Conclusão

Escreva uma breve conclusão defendendo sua solução como se estivesse apresentando para uma liderança técnica ou de negócio.

**Sua resposta:**

```md
Preencha aqui.
```
