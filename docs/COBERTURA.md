# Cobertura da BRZ Api

> **Este arquivo e' GERADO** por `ferramentas/cobertura.py`, lendo
> `loader/BrzMotor.cpp`. Nao edite a mao: uma lista escrita a mao
> envelhece calada, e isso ja custou caro aqui.

Uma funcao conta como **ligada** quando o corpo dela faz alguma coisa de
verdade. As demais **devolvem falha e dizem no log qual sao e qual plugin**
**pediu** — nunca fingem sucesso, porque motor que devolve zero em silencio
faz o plugin agir sobre dado inventado.

## 124 de 124 (100%)

- **119 ligadas** — fazem o trabalho, sem ressalva
- **5 parciais** — entregam parte, e o log diz exatamente o que falta (marcadas com `BRZ_PARCIAL` no fonte)
- **0 substituidas** — recusam DE PROPOSITO, porque nao ha rota por reflexao, e apontam a funcao que faz o servico
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
- `AoMensagemDeChat`
- `AoSairJogador`
- `AoTick`
- `AoTimer`
- `AtoresNoRaio`
- `BaixarArquivo`
- `BlueprintDaClasse`
- `BlueprintDoItem`
- `BlueprintDoObjeto`
- `CaminhoConfig`
- `CaminhoDados`
- `CaminhoRaiz`
- `CancelarAgendamento`
- `ChamarFuncao`
- `ChamarFuncaoComRetorno`
- `ChamarFuncaoEstatica`
- `ChamarVirtual`
- `ContarItemNoInventario`
- `DadosDoJogo`
- `DarItemAoJogador`
- `DefinirRetorno`
- `DescendeDe`
- `DinoMontado`
- `EOSIDDoAtacante`
- `EOSIDPorIdDoJogador`
- `EOSIDdoJogador`
- `EhReplicado`
- `EnderecoDaAncora`
- `EnderecoDoSimbolo`
- `EnderecoDoSimboloInferido`
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
- `MascaraDoBit`
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
- `OrigemDoSimbolo`
- `PluginCarregado`
- `Posicao`
- `PosicaoParaCoordenadas`
- `Pronta`
- `PublicarServico`
- `QuantidadeDoItem`
- `QuantosSimbolos`
- `RemoverAoEntrarJogador`
- `RemoverAoMensagemDeChat`
- `RemoverAoSairJogador`
- `RemoverAoTick`
- `RemoverAoTimer`
- `RemoverComandoChat`
- `RemoverComandoConsole`
- `RemoverComandoRcon`
- `RemoverHook`
- `RemoverItem`
- `RequisicaoDelete`
- `RequisicaoGet`
- `RequisicaoGetSincrona`
- `RequisicaoPatch`
- `RequisicaoPost`
- `RodarComando`
- `StatusDoServidor`
- `TeleportarParaJogador`
- `TeleportarParaPosicao`
- `TextoParaUtf16`
- `TextoRecusa`
- `Utf16ParaTexto`

### Parciais

- `AcharDinoPorID`
- `CriarTextoDoJogo`
- `CriarTextoRicoDoJogo`
- `DarItem`
- `EnderecoIP`

### Ainda nao ligadas


