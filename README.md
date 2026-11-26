# RAG Os Sertões - Guia de Execução

Este projeto implementa 3 abordagens de RAG para responder perguntas sobre "Os Sertões" de Euclides da Cunha.

## Estrutura dos Notebooks

| Notebook | Abordagem | Descrição |
|----------|-----------|-----------|
| `naive_rag.ipynb` | **Naive RAG** | Chunking fixo + embeddings + FAISS + LLM |
| `parent_rag.ipynb` | **Parent RAG** | Documentos pai (páginas) + chunks filhos + FAISS |
| `rerank_rag.ipynb` | **Rerank RAG** | Bi-encoder (busca ampla) + Cross-encoder (rerank) |
| `comparison.ipynb` | **Comparação** | Análise lado a lado dos resultados |

## Pré-requisitos

```bash
pip install pypdf sentence-transformers faiss-cpu langchain-openai langchain-community langchain-text-splitters tiktoken python-dotenv tqdm
```

## Configuração

1. **OpenAI API Key**: Configure sua chave de API:
   ```bash
   export OPENAI_API_KEY="sua-chave-aqui"
   ```
   Ou edite os notebooks e descomente a linha:
   ```python
   os.environ["OPENAI_API_KEY"] = "sua-chave-aqui"
   ```

2. **PDF**: O PDF será baixado automaticamente na primeira execução de cada notebook.

## Ordem de Execução Recomendada

1. **`naive_rag.ipynb`** - Execute todas as células (baseline)
2. **`parent_rag.ipynb`** - Execute todas as células (contexto completo)
3. **`rerank_rag.ipynb`** - Execute todas as células (alta precisão)
4. **`comparison.ipynb`** - Compare os resultados

## Perguntas Respondidas

1. Visão de Euclides da Cunha sobre o ambiente natural do sertão nordestino
2. Características da população sertaneja e relação com o ambiente
3. Contexto histórico e político da Guerra de Canudos
4. Figura de Antônio Conselheiro e seu papel
5. Crítica social e política em "Os Sertões"

## Modelos Utilizados

- **Embeddings**: `sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2` (multilíngue, bom para PT-BR)
- **Reranker**: `BAAI/bge-reranker-base` (cross-encoder multilíngue)
- **LLM**: `gpt-4o-mini` (OpenAI)

## Resultados

Cada notebook gera um arquivo JSON com resultados:
- `naive_rag_results.json`
- `parent_rag_results.json`
- `rerank_rag_results.json`

## Diferenças Principais

### Naive RAG
- Chunks de ~1000 chars com overlap de 200
- Busca única por similaridade de cosseno
- Rápido, mas pode perder contexto

### Parent RAG
- Documentos pai = páginas completas do PDF
- Chunks filhos de ~400 chars para busca
- Retorna páginas completas como contexto
- Melhor para documentos longos estruturados

### Rerank RAG
- Stage 1: Bi-encoder busca top-30
- Stage 2: Cross-encoder rerankeia top-5
- Mais preciso semanticamente
- 2-3x mais lento que Naive RAG

## Personalização

Para ajustar parâmetros, edite as variáveis nos notebooks:

```python
# Naive RAG
chunk_size=1000, chunk_overlap=200, k=5

# Parent RAG
child_chunk_size=400, k=10 (filhos), parent_k=3 (pais)

# Rerank RAG
initial_k=30 (bi-encoder), final_k=5 (reranker)
```