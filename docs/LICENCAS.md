# As licenças da BRZ Api, e por que são duas

> É o mesmo arranjo da nossa API do Conan, e pelo mesmo motivo.

| O quê | Licença | Arquivo |
|---|---|---|
| **runtime e loader** — a `winmm.dll`, que vai compilada | **proprietária** | [`LICENSE`](../LICENSE) |
| **o header público** — `Brz/BrzPluginApi.h`, o SDK | **MIT** | [`api/LICENSE`](../api/LICENSE) |
| **os exemplos** — copie e faça o que quiser | **MIT** | `exemplos/*/LICENSE` |

## O que isso quer dizer, em uma frase cada

**Você pode vender o seu plugin.** O plugin é seu, a licença dele é escolha sua,
você não deve nada e não compartilha nada. Rodar a API em quantos servidores
quiser, inclusive servidores que cobram dos jogadores, também pode.

**O que não pode:** vender a própria API, re-hospedar os arquivos (compartilhe o
link, não o arquivo), e usar este material para fazer outra API de plugins para
ARK.

## Por que a divisão

A API é a fundação: é onde mora a mecânica de como as coisas funcionam por
dentro, e ela fica com quem a mantém. Isso existe para haver **uma** fundação,
com caminho de atualização quando o jogo muda, em vez de cinco cópias
divergentes que ninguém consegue acompanhar.

O que você constrói em cima é outra coisa, e é sua.

## Uma nota sobre o que veio antes

A API de terceiro em que este servidor rodava até 31/08/2026 é a **AsaApi**, da
ArkServerApi, sob **MIT** (Copyright ArkServerApi / Game Servers Hub / Michidu).
A BRZ Api **não** é derivada dela: o carregador entra por outro mecanismo
(sequestro da ordem de busca de DLL, no molde da nossa API do Conan), a fronteira
com o plugin é uma tabela C que desenhamos, e o header público é nosso.

Onde o código da AsaApi ainda estiver presente — e enquanto estiver — o aviso de
copyright MIT dela acompanha, como a licença exige. Isso vale em especial para
qualquer pacote que ainda leve os headers do SDK antigo.
