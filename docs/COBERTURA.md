# Cobertura da BRZ Api

> **Este arquivo e' GERADO** por `ferramentas/cobertura.py`, lendo
> `loader/BrzMotor.cpp`. Nao edite a mao: uma lista escrita a mao
> envelhece calada, e isso ja custou caro aqui.

Uma funcao conta como **ligada** quando o corpo dela faz alguma coisa de
verdade. As demais **devolvem falha e dizem no log qual sao e qual plugin**
**pediu** — nunca fingem sucesso, porque motor que devolve zero em silencio
faz o plugin agir sobre dado inventado.

## 87 de 89 (97%)

- **85 ligadas** — fazem o trabalho, sem ressalva
- **2 parciais** — entregam parte, e o log diz exatamente o que falta (marcadas com `BRZ_PARCIAL` no fonte)
- **2 nao ligadas** — recusam sempre, e dizem por que

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
- `Agendar`
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
- `ChamarVirtual`
- `ContarItemNoInventario`
- `DadosDoJogo`
- `DefinirRetorno`
- `DescendeDe`
- `DinoMontado`
- `EOSIDdoJogador`
- `EhReplicado`
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
- `RemoverAoTick`
- `RemoverAoTimer`
- `RemoverComandoChat`
- `RemoverComandoConsole`
- `RemoverComandoRcon`
- `RemoverHook`
- `RodarComando`
- `StatusDoServidor`
- `TeleportarParaJogador`
- `TeleportarParaPosicao`
- `TextoRecusa`

### Parciais

- `AdicionarComandoRcon`
- `CriarTextoDoJogo`

### Ainda nao ligadas

- `CriarTextoRicoDoJogo`
- `EnderecoIP`

