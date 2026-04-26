# Apresentação — Crypto Monitor (máx. 7 minutos)

## Slide 1 — Abertura (0:30)
**Título:** Crypto Monitor — Cotação de Bitcoin em tempo real  
**Objetivo:** mostrar como o app busca, converte e exibe dados da API do Mercado Bitcoin com arquitetura MVVM.

**Fala sugerida (curta):**
- “Este app Android consulta o ticker de BTC/BRL e atualiza a tela por estado de forma reativa usando Compose.”

---

## Slide 2 — Fluxo geral do app (0:50)
**Pipeline:**
1. Usuário toca em **Carregar Cotação**.
2. `CryptoViewModel.fetchTickerData()` muda estado para `Loading`.
3. `MercadoBitcoinService` chama endpoint `GET api/BTC/ticker/`.
4. Retrofit + Gson convertem JSON para `TickerResponse`.
5. ViewModel publica `Success` (ou `Error`).
6. A UI recompõe e mostra a tela correspondente.

**Fala sugerida:**
- “A UI não busca dados diretamente; ela apenas observa estado.”

---

## Slide 3 — Comunicação com Mercado Bitcoin (a) (1:10)
**Onde acontece:**
- Interface de serviço: `MercadoBitcoinService`.
- Método: `suspend fun getTicker(): Response<TickerResponse>`.
- Endpoint: `api/BTC/ticker/`.
- Base URL configurada na factory: `https://www.mercadobitcoin.net/`.

**Pontos-chave para explicar:**
- Uso de `suspend` para chamada assíncrona com coroutines.
- `Response<TickerResponse>` permite validar sucesso HTTP e tratar erros por código.
- Chamada executada no `viewModelScope`, sem bloquear UI.

---

## Slide 4 — Conversão do payload para classes (b) (0:50)
**Como converte:**
- Retrofit usa `GsonConverterFactory` na criação do cliente.
- JSON retornado pela API é mapeado para:
  - `TickerResponse` (raiz)
  - `Ticker` (campos como `last`, `high`, `low`, `buy`, `sell`, `vol`, `date`)

**Mensagem curta:**
- “Sem parsing manual de JSON: os nomes dos campos batem com as propriedades Kotlin e o Gson faz o binding automático.”

---

## Slide 5 — IUState: o que é e estados (c) (1:10)
**`CryptoUiState` (sealed class):**
- `Initial`: tela inicial, antes da primeira consulta.
- `Loading`: indicador de carregamento durante chamada.
- `Success(ticker)`: dados carregados com sucesso.
- `Error(message)`: falha de rede/HTTP/resposta inválida.

**Por que isso é importante:**
- Garante previsibilidade da tela.
- Evita estados inválidos.
- `when` na UI cobre todos os casos em tempo de compilação.

---

## Slide 6 — Service + Factory (d) (0:50)
**Service (`MercadoBitcoinService`):** contrato dos endpoints.  
**Factory (`MercadoBitcoinServiceFactory`):** cria e configura Retrofit.

**Benefícios do padrão Factory:**
- Centraliza configuração de rede.
- Reduz acoplamento no ViewModel.
- Facilita manutenção e futura troca de implementação.

---

## Slide 7 — Interface gráfica por estado (e) + telas funcionando (1:30)
**Demonstração guiada (ao vivo ou vídeo curto):**
1. **Tela Initial:** título + botão “Carregar Cotação”.
2. Toque no botão → **Loading** com `CircularProgressIndicator`.
3. Retorno da API → **Success** com card BTC, preço atual, máxima/mínima, compra/venda, volume.
4. Botão “Atualizar Cotação” recarrega dados.
5. Simular falha de internet → **Error** com mensagem + “Tentar Novamente”.
6. “Voltar à Tela Inicial” retorna ao estado `Initial`.

**Dica de apresentação clara e concisa:**
- Narre em formato “ação do usuário → estado interno → resultado visual”.

---

## Encerramento (0:10)
**Resumo final (1 frase):**
- “O app aplica MVVM com estados explícitos, comunicação desacoplada via Service/Factory e UI reativa em Compose.”

---

## Checklist de tempo (total: ~6min50s)
- Slide 1: 0:30
- Slide 2: 0:50
- Slide 3: 1:10
- Slide 4: 0:50
- Slide 5: 1:10
- Slide 6: 0:50
- Slide 7: 1:30
- Encerramento: 0:10

---

## Apoio visual sugerido (para montar os slides rapidamente)
- Captura 1: Tela inicial (`InitialContent`).
- Captura 2: Loading (`CircularProgressIndicator`).
- Captura 3: Sucesso (`CryptoContent`) mostrando preço e card.
- Captura 4: Erro (`ErrorContent`) com botão de retry.

> Se não houver captura pronta, use as `@Preview` do Compose para gerar imagens das telas e inserir no slide.
