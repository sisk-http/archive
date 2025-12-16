# Sisk Framework Archive - Instruções para Agentes AI

## Visão Geral do Projeto

Este é um repositório de **documentação e arquivos históricos** do Sisk Framework, um framework HTTP para .NET. Não contém código-fonte executável - apenas changelogs estruturados e documentação de extensões.

## Estrutura de Diretórios

- **`changelogs/core/`**: Changelogs do Sisk.Core organizados por versões principais (v0.8-v1.5)
  - `legacy/` contém versões v0.8 a v0.14
  - Versões posteriores têm subdiretórios próprios (ex: `v1.5/v1.5.0.md`, `v1.5/v1.5.2.md`)
- **`changelogs/json-rpc/`**: Changelogs do pacote Sisk JSON-RPC
- **`ext/`**: Documentação de extensões (ex: `ini-reader-syntax.md`)
- **`assets/`**: Imagens e recursos visuais (logo, benchmarks)

## Convenções de Documentação

### Formato de Changelogs

Todos os changelogs seguem markdown estruturado com:

1. **Título da versão** em H1: `# v1.5`, `# v1.0`
2. **Seções organizadas por tema**:
   - "Changes", "Breaking Changes", "Bug fixes and other changes"
   - Descrições de features agrupadas por componente afetado
3. **Exemplos de código** em C# quando relevante para demonstrar uso de APIs
4. **Formatação consistente**:
   - Use bullets (`-`) para listas de mudanças
   - Prefixos: `Feat:`, `Fix:`, `**Breaking**:` para classificar mudanças
   - Referências a classes/métodos em markdown inline code: `` `HttpServerConfiguration` ``
   - Links para documentação oficial: `[HttpListener](https://learn.microsoft.com/...)`

### Padrões de Escrita

- **Tom técnico e objetivo**: Foco em mudanças concretas de API e comportamento
- **Especifique impacto**: Marque explicitamente breaking changes com `**Breaking**:`
- **Exemplos práticos**: Inclua code snippets C# para features complexas
- **Contexto arquitetural**: Explique *por que* mudanças foram feitas (ex: "to reduce async/await errors")

## Evolução do Framework

### Marcos Importantes

- **v0.16**: Introduziu builder pattern HTTP, eliminou dependência de ServiceProvider externo
- **v1.0**: Primeira versão estável após 2 anos de desenvolvimento
- **v1.5**: Abstração de HTTP engine, independência do HttpListener, melhorias em WebSockets

### Componentes Principais

- **HttpServer/HttpServerConfiguration**: Configuração central do servidor
- **HttpRequest/HttpResponse**: Objetos de requisição/resposta
- **Router**: Sistema de roteamento
- **LogStream**: Sistema de logging com suporte assíncrono
- **HTTP Engines**: Abstração para trocar implementação HTTP (HttpListener, Kestrel, Cadente)

## Workflow para Atualizações

### Ao adicionar novo changelog:

1. Crie arquivo em `changelogs/{core|json-rpc}/vX.Y/vX.Y.Z.md`
2. Use versão minor existente ou crie novo subdiretório se for nova minor
3. Siga estrutura de seções: título versão → introdução → seções temáticas → bullet points
4. Marque breaking changes explicitamente
5. Inclua exemplos de código para APIs complexas

### Ao documentar extensões:

1. Coloque em `ext/` com nome descritivo
2. Inicie com título H1 e sumário da funcionalidade
3. Documente especificações técnicas com exemplos práticos
4. Use tabelas ou code blocks para sintaxe/formato

## Referências Úteis

- Documentação oficial: `https://docs.sisk-framework.org/`
- Repositório principal: `https://github.com/sisk-http/core`
- Projeto Cadente (HTTP engine): `https://github.com/sisk-http/core/tree/main/cadente`
