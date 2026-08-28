# DriveLinkBridge

DriveLinkBridge é uma ponte simples entre links HTTPS e arquivos/pastas sincronizados pelo Google Drive for desktop no Windows. Foi pensado especialmente para links enviados por aplicativos que bloqueiam `file:///` e protocolos locais diretamente, como o aplicativo desktop do ChatGPT.

Repositório oficial: `https://github.com/josepilas/drive-link-bridge`

GitHub Pages: `https://josepilas.github.io/drive-link-bridge/`

## Como funciona

1. O ChatGPT entrega um link HTTPS do GitHub Pages contendo apenas o caminho **relativo** dentro de `Meu Drive`.
2. A página é invisível e chama o protocolo local `gdriveopen:`.
3. O handler instalado no Windows combina esse caminho relativo com a raiz local configurada (por exemplo `G:\Meu Drive`).
4. O Explorer abre a pasta ou seleciona o arquivo.
5. A página tenta fechar a própria aba automaticamente e não possui interface visual.

## Instalação no Windows

Baixe/clonar este repositório, abra a pasta `windows` e execute `INSTALAR.cmd`.

Por padrão o instalador sugere:

`G:\Meu Drive`

Para instalar diretamente com outra raiz:

`INSTALAR.cmd "H:\Meu Drive"`

A instalação copia o handler/configuração para:

`%LOCALAPPDATA%\DriveLinkBridge`

E registra, somente para o usuário atual:

`gdriveopen:`

Não exige privilégios de administrador.

## Desinstalação

Execute:

`windows\DESINSTALAR.cmd`

Isso remove o protocolo do Registro do usuário e a pasta `%LOCALAPPDATA%\DriveLinkBridge`.

## URL pública

Formato recomendado:

`https://josepilas.github.io/drive-link-bridge/?rel=<CAMINHO_RELATIVO_URL_ENCODED>`

Exemplo:

`Meu Drive\documentos\AÇÕES\CLIENTE\arquivo.pdf`

vira:

`https://josepilas.github.io/drive-link-bridge/?rel=documentos%5CA%C3%87%C3%95ES%5CCLIENTE%5Carquivo.pdf`

## Segurança

O handler normaliza o caminho e só permite abrir itens dentro da raiz configurada no instalador. Sequências `..` em caminhos relativos são rejeitadas. A página pública não lê arquivos locais e não recebe conteúdo dos arquivos; ela apenas encaminha o caminho para o protocolo local.

## Uso com ChatGPT

Veja `CHATGPT_PROMPT.md`. O ponto mais importante é que o ChatGPT deve reconstruir a cadeia real de pastas do Google Drive até `Meu Drive` antes de montar o link. Não deve presumir que a pasta encontrada está na raiz.

## Estrutura

- `index.html` — ponte HTTPS invisível publicada pelo GitHub Pages.
- `windows/INSTALAR.cmd` — instalador simples.
- `windows/instalar.ps1` — instalação/configuração.
- `windows/gdriveopen-handler.ps1` — handler do protocolo.
- `windows/DESINSTALAR.cmd` — remoção.
- `windows/TESTAR.cmd` — abre a raiz configurada pelo fluxo HTTPS → protocolo local.
- `CHATGPT_PROMPT.md` — instruções para o ChatGPT.
- `.github/workflows/pages.yml` — publicação automática do site no GitHub Pages.

## GitHub Pages

O site é publicado a partir deste repositório por GitHub Actions. O workflow está em `.github/workflows/pages.yml` e publica somente o `index.html` necessário para a ponte.

## Hospedagem própria

Qualquer pessoa pode hospedar o `index.html` em HTTPS. Se mudar a URL pública, substitua a URL-base em `CHATGPT_PROMPT.md` e em `windows/instalar.ps1`.

## Observação sobre fechamento da aba

A página chama `window.close()` após disparar o protocolo. Navegadores podem bloquear o fechamento automático em determinadas políticas/configurações. Mesmo nesse caso, a página permanece vazia e sem UI.
