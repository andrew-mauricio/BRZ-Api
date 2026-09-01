# Cobertura da BRZ Api

> **Este arquivo e' GERADO** por `ferramentas/cobertura.py`, lendo
> `loader/BrzMotor.cpp`. Nao edite a mao: uma lista escrita a mao
> envelhece calada, e isso ja custou caro aqui.

Uma funcao conta como **ligada** quando o corpo dela faz alguma coisa de
verdade. As demais **devolvem falha e dizem no log qual sao e qual plugin**
**pediu** — nunca fingem sucesso, porque motor que devolve zero em silencio
faz o plugin agir sobre dado inventado.

## 106 de 106 (100%)

- **102 ligadas** — fazem o trabalho, sem ressalva
- **3 parciais** — entregam parte, e o log diz exatamente o que falta (marcadas com `BRZ_PARCIAL` no fonte)
- **1 substituidas** — recusam DE PROPOSITO, porque nao ha rota por reflexao, e apontam a funcao que faz o servico
- **0 nao ligadas** — recusam sempre, e dizem por que

### Ligadas

- `AcharClasse`
- `AcharControladorDoPersonagem`
- `AcharJogadorPorEOSID`
- `AcharJogadorPorNomeDePersonagem`
- `AcharJogadorPorNomeDePlataforma`
- `AcharObjeto`
- `AcharServico`
- `AdicionarComandoChat`
- `AdicionarComandoConsole`
- `AdicionarComandoRcon`
- `Agendar`
- `AoEntrarJogador`
- `AoSairJogador`
- `AoTick`
- `AoTimer`
- `AtoresNoRaio`
- `BlueprintDaClasse`
- `BlueprintDoItem`
- `BlueprintDoObjeto`
- `CaminhoConfig`
- `CaminhoDados`
- `CaminhoRaiz`
- `CancelarAgendamento`
- `ChamarFuncao`
- `ChamarFuncaoEstatica`
- `ChamarVirtual`
- `ContarItemNoInventario`
- `DadosDoJogo`
- `DarItemAoJogador`
- `DefinirRetorno`
- `DescendeDe`
- `DinoMontado`
- `EOSIDdoJogador`
- `EhReplicado`
- `EnderecoDaAncora`
- `EnderecoDoSimbolo`
- `EscreverBit`
- `EscreverMembro`
- `EscreverParm`
- `EstaMontado`
- `EstadoDoJogo`
- `EstatisticaHooks`
- `GerenciadorDeCheat`
- `HookFuncao`
- `HookProcessEvent`
- `HookProcessEventTudo`
- `HookVirtual`
- `IdDaTribo`
- `IdDoJogador`
- `InventarioDoJogador`
- `InventarioDoObjeto`
- `ItensDoInventario`
- `JogadorEstaMorto`
- `JogadoresConectados`
- `Legivel`
- `LerBit`
- `LerMembro`
- `LerParm`
- `LerRetorno`
- `LerTextoDoJogo`
- `Log`
- `MensagemDeChat`
- `MensagemDeChatParaTodos`
- `MensagemDeServidor`
- `MensagemDeServidorParaTodos`
- `ModoDeJogo`
- `MoverItem`
- `Mundo`
- `NascerDino`
- `NascerDrop`
- `NomeCompletoDoObjeto`
- `NomeDeFName`
- `NomeDoJogador`
- `NomeDoMembro`
- `NomeDoObjeto`
- `NomeDoPersonagem`
- `Notificacao`
- `NotificacaoParaTodos`
- `NumeroDeObjetos`
- `ObjetoPadrao`
- `ObjetoPorIndice`
- `OffsetDoMembro`
- `Posicao`
- `PosicaoParaCoordenadas`
- `Pronta`
- `PublicarServico`
- `QuantidadeDoItem`
- `QuantosSimbolos`
- `RemoverAoEntrarJogador`
- `RemoverAoSairJogador`
- `RemoverAoTick`
- `RemoverAoTimer`
- `RemoverComandoChat`
- `RemoverComandoConsole`
- `RemoverComandoRcon`
- `RemoverHook`
- `RemoverItem`
- `RodarComando`
- `StatusDoServidor`
- `TeleportarParaJogador`
- `TeleportarParaPosicao`
- `TextoRecusa`

### Parciais

- `CriarTextoDoJogo`
- `CriarTextoRicoDoJogo`
- `EnderecoIP`

### Substituidas (recusam de proposito)

- `DarItem` -> use `DarItemAoJogador`

### Ainda nao ligadas


