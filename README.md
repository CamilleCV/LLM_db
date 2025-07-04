# PROJETO: Agente Industrial Inteligente

## Contexto

A SiderTech Solutions, empresa do setor metalmecânico com planta em Joinville (SC), iniciou sua transformação digital estruturando um banco de dados relacional local com informações detalhadas sobre manutenção industrial, incluindo equipamentos, ordens de manutenção e técnicos envolvidos.

Para democratizar o acesso a esses dados e facilitar consultas para diferentes níveis de usuários (operadores, engenheiros e gestores), desenvolvemos uma aplicação baseada em inteligência artificial capaz de interpretar perguntas em linguagem natural e responder com base nos dados do banco.

---

## Objetivo do Projeto

- Permitir que usuários façam perguntas em linguagem natural sobre os dados de manutenção.
- Interpretar essas perguntas, traduzir para consultas SQL, executar no banco SQLite (`manutencao_industrial.db`) e retornar respostas claras e contextualizadas.
- Facilitar o uso da base de dados mesmo para usuários sem conhecimento em SQL.

---

## Arquitetura da Solução

A solução é composta pelos seguintes componentes principais:

1. **Interface de Perguntas**  
   Interface em Python que recebe perguntas em linguagem natural, podendo ser executada interativamente ou via terminal.

2. **Pipeline de Consulta**  
   Utiliza a biblioteca LlamaIndex para:
   - Interpretar perguntas em linguagem natural.
   - Gerar consultas SQL baseadas no esquema do banco.
   - Executar as consultas no banco SQLite.
   - Analisar os resultados e construir respostas coerentes.

3. **Banco de Dados SQLite**  
   Arquivo `manutencao_industrial.db` com as tabelas:
   - `equipamentos`
   - `ordem_tecnico`
   - `ordens_manutencao`
   - `tecnicos`

4. **Modelos de Linguagem**  
   Modelos Groq para geração de SQL e respostas, e embeddings HuggingFace para mapear esquema e dados.

5. **Memória de Conversação**  
   Mantém contexto das perguntas anteriores para consultas encadeadas.


---

## Como Executar

### Pré-requisitos

- Python 3.8 ou superior
- Instalar dependências:

```bash
pip install -r requirements.txt
```

## Esquematização da LLM

O esquema da LLM foi elaborado com base em um curso prévio na Alura, introduzindo o conceito de memória. Um esboço inicial se encontra em `Arquitetura.png`.

O pipeline principal do modelo é:
```
qp.add_chain(['entrada', 'memory', 'acesso_tabela', 'contexto_tabela'])
qp.add_link('memory', 'prompt_1', dest_key='conversation_history')
qp.add_link('memory', 'prompt_2', dest_key='conversation_history')
qp.add_link('entrada', 'prompt_1', dest_key='pergunta_user')
qp.add_link('contexto_tabela', 'prompt_1', dest_key='schema')
qp.add_chain(['prompt_1', 'llm_1', 'consulta_sql', 'resultado_sql'])
qp.add_link('entrada', 'prompt_2', dest_key='pergunta_user')
qp.add_link('consulta_sql', 'prompt_2', dest_key='consulta')
qp.add_link('resultado_sql', 'prompt_2', dest_key='resultado')
qp.add_link('prompt_2', 'llm_2')
```

## Limitações e Sugestões de Melhoria

- **Cobertura do esquema**  
  O modelo depende do esquema extraído do banco de dados. Consultas muito complexas ou que envolvam relacionamentos não explícitos podem não ser geradas corretamente.

- **Qualidade das respostas**  
  A precisão das respostas depende diretamente do desempenho do modelo Groq e da qualidade dos prompts definidos. Respostas imprecisas podem ocorrer em casos específicos.

- **Interface de usuário**  
  Atualmente, a LLM está rodando apenas no Jupyter Notebook, não existindo ainda um script `.py` ou aplicação mais prática para uso direto via terminal ou interface dedicada.

