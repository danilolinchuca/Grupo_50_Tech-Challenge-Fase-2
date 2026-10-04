# data/

**Nada aqui é versionado.** O `.gitignore` bloqueia o conteúdo destas pastas de propósito:
datasets em Git incham o repositório e frequentemente violam a licença da fonte.

| Pasta | Conteúdo |
|---|---|
| `raw/` | arquivos originais, exatamente como baixados da fonte — nunca editados |
| `processed/` | saída dos notebooks de pré-processamento (`.parquet` ou `.csv`) |

Documente abaixo como obter os dados brutos, para que qualquer pessoa consiga reproduzir o projeto.

## Como obter

1. Baixe os arquivos brutos disponibilizados para o Tech Challenge — Fase 2.
2. Salve os arquivos como:
   - `data/raw/application_record.csv`
   - `data/raw/credit_record.csv`
3. Mantenha os arquivos originais sem alterações antes da execução dos notebooks.
4. Os arquivos brutos não são versionados no GitHub.
5. Checksum (opcional, recomendado):
   - `shasum -a 256 data/raw/application_record.csv`
   - `shasum -a 256 data/raw/credit_record.csv`
