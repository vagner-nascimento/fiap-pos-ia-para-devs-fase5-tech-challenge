# Guardiã AI – PRD e Jornada do Usuário

Triagem de risco materno com apoio de IA · Tech Challenge Fase 5 · Versão 0.1 (rascunho para o grupo)

## 1. Visão geral

A **Guardiã AI** é um copiloto para profissionais de saúde que atendem gestantes. A partir dos dados clínicos aferidos na consulta e do relato da gestante, gravado em áudio e transcrito automaticamente (ou digitado), a aplicação sinaliza o nível de atenção do caso (baixo, médio ou alto risco), explica quais fatores pesaram na avaliação, busca orientações em protocolos oficiais e apresenta tudo em linguagem clara. Cada análise fica registrada para auditoria.

A decisão final é **sempre da profissional**. A Guardiã AI não diagnostica, não prescreve e não toma decisões de forma automática.

## 2. Problema e oportunidade

- **Triagem manual e demorada:** a profissional cruza sinais vitais, histórico, queixas e protocolos de cabeça, o que aumenta o risco de um caso grave ser priorizado tarde.
- **Informação dispersa:** dados estruturados, texto livre e protocolos ficam em lugares diferentes.
- **Consulta a protocolos é trabalhosa:** achar a orientação certa a cada caso consome tempo.
- **Oportunidade:** uma primeira camada de análise que organiza a informação, destaca sinais de alerta e aponta a fonte da orientação, liberando tempo para o cuidado.

## 3. Objetivos e métricas de sucesso

### Objetivos de produto

- Reduzir o tempo de análise inicial de um atendimento.
- Reduzir o esforço de digitação no registro, permitindo que o relato seja gravado em áudio e transcrito direto no formulário.
- Destacar casos que exigem atenção prioritária.
- Dar transparência: mostrar por que o caso foi sinalizado e em qual documento a orientação se baseia.
- Garantir rastreabilidade de toda análise gerada.

### Métricas de sucesso

| Dimensão | Métrica | Meta sugerida |
| --- | --- | --- |
| Modelo de ML | Recall da classe "alto risco" | Maximizar, com piso acordado em grupo (ex.: ≥ 0,85) |
| Modelo de ML | Precision e F1 por classe, matriz de confusão | Reportar sempre junto do recall |
| Explicabilidade | Casos com explicação SHAP individual exibida | 100% |
| RAG | Respostas com ao menos uma fonte citada | 100% |
| Guardrails | Respostas com aviso de apoio à decisão e sem recomendação definitiva | 100% |
| Auditoria | Análises com registro completo (entrada, saída, fontes) | 100% |
| Demo | Jornada completa no vídeo | Até 15 minutos |

A meta de recall é uma hipótese a validar no treinamento, não uma promessa. O dataset é pequeno e os resultados devem ser reportados com honestidade.

## 4. Escopo

### Dentro do escopo

- Formulário de atendimento com dados clínicos e campo de relato livre. O relato é gravado em áudio, transcrito automaticamente (speech-to-text) e inserido no campo, com revisão e edição da profissional antes do envio. A digitação continua como alternativa.
- Classificação de risco (baixo, médio, alto) com pelo menos dois modelos comparados.
- Explicabilidade por caso e global.
- LLM para resumir o relato, explicar o resultado e responder perguntas da profissional.
- RAG sobre base de protocolos oficiais, com citação de documento e trecho.
- Orquestração com LangGraph (ou LangChain).
- Registro de auditoria de cada análise.
- Interface em Streamlit, Dockerfile, README, relatório técnico em PDF e vídeo.

### Fora do escopo

- Diagnóstico definitivo, prescrição de medicamentos ou conduta clínica automática.
- Uso de dados reais identificáveis de pacientes.
- Integração com prontuário eletrônico real.
- Imagem e vídeo (possível evolução futura). O áudio entra apenas para capturar e transcrever o relato, sem análise de voz, de emoção ou identificação de quem fala.
- Múltiplos agentes (diferencial opcional, não obrigatório).

### Módulo opcional (se houver folga)

Um bloco de **sinais de alerta de violência no relato**, feito apenas com LLM e RAG (sem segundo modelo de ML), que sinaliza trechos do relato que merecem atenção e aponta o protocolo de acolhimento. Mantém a decisão humana e conecta o projeto aos dois contextos do edital.

## 5. Personas

### Marina, enfermeira de pré-natal (usuária principal)

Atende muitas gestantes por turno, tem pouco tempo por consulta e precisa decidir rapidamente quem merece avaliação médica prioritária. Quer uma segunda leitura confiável, que mostre o raciocínio e a fonte, e não uma "caixa-preta".

### Dr. Paulo, médico obstetra (usuário secundário)

Recebe os casos encaminhados. Quer um resumo organizado do que foi observado e do que o sistema sinalizou, para validar ou descartar rapidamente.

### Coordenação da unidade (stakeholder)

Precisa de rastreabilidade e garantia de que a IA respeita limites éticos e regulatórios. Consulta os logs de auditoria.

## 6. Requisitos funcionais

| ID | Requisito | Prioridade |
| --- | --- | --- |
| RF-01 | Registrar atendimento com idade, pressão sistólica e diastólica, glicemia, temperatura e frequência cardíaca, com validação de faixas e unidades | Alta |
| RF-02 | Aceitar o relato livre da gestante por digitação ou por transcrição de áudio (RF-15 a RF-18) e, opcionalmente, histórico resumido | Alta |
| RF-03 | Exibir exemplos de casos sintéticos pré-carregados para demonstração | Média |
| RF-04 | Executar o modelo de ML e retornar nível de risco e probabilidade por classe | Alta |
| RF-05 | Exibir explicação do resultado (SHAP individual) e importância global das variáveis | Alta |
| RF-06 | Resumir o relato com a LLM, destacando sintomas e sinais de alerta (ex.: dor de cabeça intensa, visão turva, inchaço, sangramento) | Alta |
| RF-07 | Recuperar trechos de protocolos relevantes via RAG e exibir documento, página ou seção e trecho usado | Alta |
| RF-08 | Gerar explicação em linguagem clara que integre predição, SHAP, relato e protocolos | Alta |
| RF-09 | Aplicar guardrails: sem diagnóstico, sem prescrição, sem recomendação definitiva quando faltar informação | Alta |
| RF-10 | Exibir aviso fixo de que a ferramenta é apoio à decisão profissional | Alta |
| RF-11 | Permitir que a profissional registre sua decisão final (concordo, discordo, observação) | Média |
| RF-12 | Gravar log de auditoria de cada análise (data e hora, entradas, versão do modelo, predição, probabilidade, documentos recuperados, resposta final, decisão da profissional) | Alta |
| RF-13 | Permitir perguntas de acompanhamento sobre o caso, respondidas com base no RAG | Média |
| RF-14 | Tela de consulta do histórico de análises | Baixa |
| RF-15 | Gravar o relato em áudio pelo navegador (iniciar, pausar, parar e regravar), com indicador visual de gravação e aviso de consentimento da gestante | Alta |
| RF-16 | Transcrever o áudio para texto em português do Brasil e inserir automaticamente o texto no campo de relato do formulário | Alta |
| RF-17 | Permitir que a profissional revise e edite o texto transcrito antes do envio, destacando trechos de baixa confiança da transcrição | Alta |
| RF-18 | Descartar o áudio após a transcrição e registrar no log apenas o texto final, a versão do modelo de transcrição e a indicação de que o relato veio de áudio | Alta |

## 7. Requisitos não funcionais, segurança e responsabilidade

- **Dados:** apenas dados públicos, anonimizados ou sintéticos. Nenhum dado pessoal real no repositório ou nos logs.
- **Transparência:** toda saída da IA identifica que é gerada por IA e que exige validação humana.
- **Prudência da LLM:** o prompt e uma verificação de saída impedem linguagem de diagnóstico ou prescrição. Com informação insuficiente, a resposta recomenda avaliação profissional.
- **Reprodutibilidade:** sementes fixas, versões de dependências e modelo registradas, execução via Docker.
- **Desempenho:** análise completa em poucos segundos em máquina comum (alvo: abaixo de 15 s).
- **Custo e portabilidade:** a LLM deve ser configurável por variável de ambiente (provedor e modelo), sem chaves no código.
- **Auditabilidade:** logs em formato estruturado (JSONL ou SQLite), imutáveis na aplicação.
- **Viés e limitações:** documentar que o dataset é pequeno, vem de uma população específica (Bangladesh) e não representa o cenário brasileiro.
- **Transcrição de áudio:** português do Brasil, com alvo de até 10 s para um relato de 1 minuto; serviço configurável por variável de ambiente (nuvem ou modelo local, como Whisper) e digitação como plano B se a transcrição falhar.
- **Privacidade do áudio:** o áudio é processado em memória e descartado após a transcrição; nenhuma gravação vai para o repositório ou para os logs. Na demonstração, usar apenas a voz do próprio grupo lendo relatos sintéticos.

## 8. Dados e Machine Learning

### Dataset

*Maternal Health Risk* (UCI Machine Learning Repository), com cerca de 1.000 registros públicos.

| Variável | Descrição | Observação |
| --- | --- | --- |
| Age | Idade | Em anos |
| SystolicBP / DiastolicBP | Pressão arterial | mmHg |
| BS | Glicemia | Em mmol/L no dataset. A interface deve converter de mg/dL, padrão no Brasil |
| BodyTemp | Temperatura | Em °F no dataset. A interface deve converter de °C |
| HeartRate | Frequência cardíaca | bpm |
| RiskLevel | Alvo | Baixo, médio, alto |

### Preparação dos dados

- Análise exploratória: distribuição das classes, outliers, correlações.
- Verificar e tratar registros duplicados (o dataset tem repetições) para evitar vazamento entre treino e teste.
- Validar valores implausíveis (ex.: idades extremas, frequência cardíaca incompatível).
- Divisão estratificada em treino e teste, com validação cruzada.

### Modelos a comparar

- Regressão Logística (baseline interpretável).
- Random Forest.
- XGBoost ou Gradient Boosting.

### Métricas e justificativa

- **Recall da classe "alto risco" como métrica principal.** Um falso negativo significa uma gestante de alto risco classificada como baixo risco, o erro mais grave do cenário.
- **Precision** para medir o custo dos falsos positivos (encaminhamentos desnecessários, sobrecarga do serviço).
- **F1-score por classe** e **matriz de confusão** para mostrar onde o modelo erra, especialmente entre médio e alto risco.
- Accuracy apenas como referência, nunca como critério único.
- Considerar ajuste de limiar de decisão e pesos de classe para priorizar recall do alto risco, discutindo o trade-off.

### Explicabilidade

- SHAP individual (por que esta gestante foi classificada assim) e global (variáveis mais influentes).
- Tradução dos valores SHAP em frases curtas para a LLM usar na explicação.

## 9. LLM e RAG

### Papel da LLM

- Resumir o relato da gestante e extrair sintomas e sinais de alerta.
- Explicar o resultado do modelo em linguagem clara, sem substituir a avaliação clínica.
- Responder perguntas da profissional usando somente o contexto recuperado.

### Base de conhecimento (RAG)

Pequena, curada e versionada. Candidatos a validar quanto à versão vigente:

- Manuais do Ministério da Saúde sobre pré-natal de baixo risco e gestação de alto risco.
- Materiais do Ministério da Saúde e da Febrasgo sobre síndromes hipertensivas e pré-eclâmpsia.
- Recomendações da OMS sobre cuidados pré-natais.
- Cartilhas públicas sobre sinais de alerta na gestação.

Implementação sugerida: divisão em trechos (chunks) com metadados de documento e página, embeddings, armazenamento em FAISS ou Chroma e recuperação dos trechos mais relevantes. A resposta lista os documentos e trechos usados.

### Guardrails

- Instruções de sistema proibindo diagnóstico, prescrição e conduta definitiva.
- Resposta sempre ancorada em trechos recuperados. Sem trecho relevante, a LLM informa que não encontrou orientação na base.
- Informação insuficiente leva a "recomenda-se avaliação profissional".
- Verificação de saída por regras simples (palavras e padrões de diagnóstico ou prescrição) antes de exibir.
- Tratar o relato transcrito como texto não verificado: com baixa confiança ou trechos ininteligíveis, a LLM sinaliza a limitação em vez de supor o conteúdo.

## 10. Arquitetura e fluxo

Antes do grafo, a interface grava o áudio do relato, transcreve em texto (speech-to-text) e insere o texto no campo de relato do formulário para revisão da profissional; só depois o formulário é enviado. O fluxo principal, orquestrado em LangGraph, segue em nós sequenciais:

1. **Validar entrada:** checa faixas, unidades e campos obrigatórios.
2. **Modelo de ML:** calcula o risco e a probabilidade por classe e o SHAP do caso.
3. **Analisar relato:** a LLM resume e destaca sinais de alerta.
4. **Consultar protocolos (RAG):** monta a consulta com risco, sintomas e fatores SHAP e recupera trechos.
5. **Gerar explicação:** a LLM integra tudo, cita fontes e aplica guardrails.
6. **Registrar auditoria:** grava entradas, saídas e fontes.
7. **Apresentar à profissional:** painel com resultado, explicação, fontes e campo de decisão final.

Componentes: Streamlit (interface), scikit-learn e XGBoost (modelos), SHAP, LangGraph e LangChain, FAISS ou Chroma (RAG), SQLite ou JSONL (logs), Whisper (API ou modelo local) ou outro serviço de transcrição, componente de gravação de áudio do Streamlit, Docker.

## 11. Mapa de aderência ao edital

| Exigência do edital | Como atendemos |
| --- | --- |
| Dataset público | Maternal Health Risk (UCI) e casos sintéticos para o relato |
| Dois ou mais modelos com métricas adequadas | Regressão Logística, Random Forest e XGBoost, com foco em recall do alto risco |
| Interpretação do modelo | SHAP individual e global |
| Integração com LLM | Resumo do relato, explicação e perguntas |
| RAG com indicação da fonte | Base de protocolos oficiais com citação de documento e trecho |
| LangChain ou LangGraph | Grafo linear de sete nós |
| Registro das análises | Log de auditoria estruturado |
| Decisão humana e limites da IA | Aviso fixo, guardrails e registro da decisão da profissional |
| Interface e Dockerfile | Streamlit e Dockerfile |
| Repositório, relatório PDF e vídeo | Entregáveis planejados no cronograma |
| Áudio (evolução opcional da Fase 4) | Captura de áudio e transcrição do relato direto no formulário, sem análise de voz |

## 12. Riscos e mitigações

| Risco | Impacto | Mitigação |
| --- | --- | --- |
| Dataset pequeno e com duplicatas | Métricas otimistas ou instáveis | Deduplicar, validação cruzada, intervalos de confiança e discussão honesta |
| População do dataset diferente da brasileira | Generalização limitada | Registrar como limitação e não prometer uso clínico real |
| Relato não existe no dataset | Parte textual sem rótulo | Usar relatos sintéticos apenas para demonstração e tratar o texto só via LLM, sem treino de ML |
| LLM alucinar ou recomendar condutas | Risco ético e de nota | Guardrails, resposta ancorada em RAG e verificação de saída |
| Documentos do RAG desatualizados | Orientação incorreta | Versionar a base e registrar data e edição de cada documento |
| Dependência de API de LLM (custo, chave, indisponibilidade) | Demo falhar | Provedor configurável, cache de exemplos e plano B com modelo local |
| Escopo crescer | Atrasar entrega | Módulo de violência como opcional, só se o núcleo estiver pronto |
| Erros de transcrição (sotaque, ruído, termos clínicos) | Relato distorcido leva a resumo e explicação equivocados | Revisão e edição obrigatórias do texto antes do envio, destaque de baixa confiança, digitação como alternativa e testes com relatos sintéticos gravados em ambientes diferentes |
| Privacidade do áudio e consentimento | Exposição de voz e de conteúdo sensível | Processar em memória, não guardar o áudio, pedir consentimento antes de gravar e, com serviço externo, enviar apenas dados sintéticos na demonstração |

## 13. Cronograma sugerido (8 semanas)

| Semana | Entregas | Frente sugerida |
| --- | --- | --- |
| 1 | Escolha final, dataset, EDA, definição de métricas | Dados e ML |
| 2–3 | Treino, comparação e SHAP | Dados e ML |
| 4 | Curadoria de documentos, chunking, embeddings e retriever | RAG e LLM |
| 5 | Grafo LangGraph, prompts, guardrails e logs | RAG e LLM |
| 6 | Interface Streamlit, captura e transcrição de áudio e Dockerfile | Interface e DevOps |
| 7 | Relatório técnico em PDF e README | Todos |
| 8 | Roteiro, gravação do vídeo e revisão final | Todos |

## 14. Critérios de aceite

- Dado um caso de teste, o sistema retorna risco, probabilidades, SHAP, resumo do relato, explicação e fontes em uma única tela.
- Nenhuma resposta contém diagnóstico ou prescrição definitivos.
- Com dados incompletos ou relato vago, a resposta indica informação insuficiente e recomenda avaliação profissional.
- Todas as análises geram registro de auditoria consultável.
- A profissional grava o relato em áudio, o texto transcrito aparece no campo de relato do formulário e pode ser editado antes do envio.
- O áudio não é guardado após a transcrição, e o log indica que o relato veio de áudio.
- A aplicação sobe com um único comando Docker, seguindo o README.
- O relatório técnico cobre problema, dados, preparação, modelos, métricas, LLM, RAG e limitações.
- O vídeo de até 15 minutos mostra a jornada completa.

## 15. Perguntas em aberto

- Qual provedor de LLM e de embeddings o grupo vai usar (custo, chave e limites)?
- Qual serviço de transcrição será usado (Whisper local, API de nuvem ou outro), considerando custo, qualidade em português e privacidade?
- Como será coletado e registrado o consentimento da gestante para gravar o relato?
- Vamos incluir o módulo opcional de sinais de alerta de violência?
- Qual será o piso de recall do alto risco que o grupo considera aceitável?
- Quem cuida de cada frente (dados e ML, RAG e LLM, interface e Docker, relatório e vídeo)?

# Jornada do Usuário

## Contexto da jornada

A jornada abaixo acompanha **Marina**, enfermeira de pré-natal, durante o atendimento de uma gestante. Ela também serve de base para o roteiro do vídeo de demonstração.

### Caso de exemplo (sintético)

Gestante de 35 anos, 34 semanas de gestação. Pressão 150/95 mmHg, glicemia elevada, frequência cardíaca 90 bpm e temperatura normal. No relato, gravado em áudio, diz estar com dor de cabeça forte há dois dias, visão embaçada e inchaço nas mãos. Todos os dados são fictícios.

## Etapas da jornada

| # | Etapa | O que a Marina faz | O que a Guardiã AI faz | Sentimento e dor | Controle humano |
| --- | --- | --- | --- | --- | --- |
| 1 | Acolhimento | Recebe a gestante, aferre sinais vitais e escuta a queixa | Nada ainda | Pressa, volume de atendimentos | Início do atendimento é da profissional |
| 2 | Registro (áudio e formulário) | Preenche os dados clínicos no formulário, grava em áudio o relato da gestante (com o consentimento dela) e revisa o texto transcrito | Transcreve o áudio em português, insere o texto no campo de relato do formulário e valida faixas e unidades (converte mg/dL e °C) | Menos digitação; receio de a transcrição errar termos clínicos | Marina edita o texto transcrito e confirma os dados antes de enviar |
| 3 | Análise | Clica em "Analisar caso" | Executa o modelo, calcula o SHAP, resume o relato, consulta protocolos e gera a explicação | Expectativa e ansiedade | Exibe aviso de apoio à decisão |
| 4 | Leitura do resultado | Vê o nível de risco, a probabilidade e os fatores que mais pesaram | Mostra gráfico SHAP e linguagem clara | Alívio por ter uma segunda leitura | Marina interpreta e pode discordar |
| 5 | Conferência das fontes | Abre os trechos dos protocolos citados | Lista documento, seção e trecho usado | Confiança vem da transparência | Marina verifica a fonte |
| 6 | Investigação | Pergunta "quais sinais de alerta devo checar agora?" | Responde com base nos protocolos, sem condutas definitivas | Dúvida pontual resolvida | Resposta sempre com ressalva |
| 7 | Decisão | Decide encaminhar ao obstetra, reavaliar ou acompanhar | Registra a decisão e a observação da Marina | Responsabilidade assumida | **Decisão final humana** |
| 8 | Encaminhamento | Passa o caso ao médico com o resumo gerado | Disponibiliza resumo estruturado do caso | Menos retrabalho na passagem | Médico valida ou descarta |
| 9 | Auditoria | Coordenação consulta o histórico | Exibe log com entradas, versão do modelo, fontes, resposta e decisão | Segurança institucional | Rastreabilidade completa |

## O que o sistema entrega em cada análise

- Nível de risco previsto e probabilidade por classe.
- Fatores que mais contribuíram para a predição, em gráfico e em texto.
- Resumo do relato, com sintomas e sinais de alerta destacados.
- Trechos de protocolos oficiais, com identificação do documento.
- Explicação em linguagem clara, com avisos de limites e de informação insuficiente, quando aplicável.
- Campo para a decisão final da profissional.

## Caminhos alternativos

- **Informação insuficiente:** campos faltando ou relato vago levam a uma resposta que pede dados adicionais e recomenda avaliação profissional, sem classificação conclusiva.
- **Valores fora de faixa:** o formulário alerta e pede confirmação antes de analisar.
- **Sem protocolo relevante na base:** a resposta informa que não há orientação encontrada e não inventa conteúdo.
- **Falha da LLM:** o sistema ainda mostra o resultado do modelo e o SHAP, com aviso de que a explicação textual está indisponível.
- **Discordância da profissional:** Marina registra a decisão diferente e o motivo, que fica no log.
- **Transcrição com erros ou áudio ruim:** o texto aparece com os trechos de baixa confiança marcados; Marina corrige no próprio campo ou regrava, e a análise só segue após a revisão.
- **Sem microfone, sem permissão ou sem consentimento:** Marina digita o relato no mesmo campo e o fluxo segue igual.

## Roteiro de demonstração (vídeo de até 15 minutos)

| Bloco | Conteúdo | Tempo sugerido |
| --- | --- | --- |
| 1 | Problema, cenário e limites da IA | 1,5 min |
| 2 | Dados, preparação e comparação dos modelos | 3 min |
| 3 | Métricas, recall do alto risco e impacto dos erros | 2 min |
| 4 | Arquitetura, LangGraph e RAG | 2 min |
| 5 | Demonstração da jornada completa com o caso de exemplo, incluindo a gravação do relato em áudio e a transcrição no formulário | 4 min |
| 6 | Log de auditoria, limitações e próximos passos | 1,5 min |

Para o bloco 5, mostrar também um caso com informação insuficiente, para evidenciar o comportamento prudente da IA.
