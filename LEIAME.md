# Baixar áudios de Reels do Instagram em lote no Windows

**Baixe seus conteúdos principais do Instagram para ouví-los no Spotify no conforto da sua cama, academia ou aprendizado enquanto trabalha.**

Este procedimento utiliza o yt-dlp e o FFmpeg para baixar o áudio de uma lista de Reels, converter para MP3 e organizar os arquivos para reprodução local, inclusive no Spotify.

> Utilize o procedimento para conteúdo seu ou para o qual você tenha autorização. O Instagram pode exigir login ou impedir determinados downloads.

## 1. Instalar as ferramentas

Abra o PowerShell e verifique se o gerenciador de pacotes do Windows está disponível:

```powershell
winget --version
```

Se aparecer um número de versão, execute os comandos abaixo, um de cada vez.

### Instalar o yt-dlp

```powershell
winget install --source winget --id yt-dlp.yt-dlp --exact
```

### Instalar o FFmpeg

```powershell
winget install --source winget --id Gyan.FFmpeg --exact
```

O pacote FFmpeg inclui os executáveis `ffmpeg` e `ffprobe`.

Leia e confirme as solicitações de instalação quando necessário.

## 2. Reiniciar o terminal e verificar a instalação

Depois de instalar as ferramentas:

1. Feche completamente o PowerShell ou Windows Terminal.
2. Abra novamente pelo menu Iniciar.
3. Execute:

```powershell
yt-dlp --version
ffmpeg -version
ffprobe -version
```

Cada comando deve mostrar informações de versão.

Se aparecer a mensagem “não é reconhecido”, o programa pode não estar instalado ou sua pasta pode não estar disponível no PATH.

## 3. Entender como acessar pastas no PowerShell

Não digite apenas:

```powershell
$HOME\Music\
```

Para entrar na pasta, utilize:

```powershell
cd "$HOME\Music"
```

Ou informe o caminho completo:

```powershell
cd "C:\Users\MY_USER\Music"
```

### Significado dos elementos

| Elemento | Significado |
|---|---|
| `cd` | Altera a pasta atual do terminal. |
| `$HOME` | Representa a pasta do usuário atual. |
| `"$HOME\Music"` | Caminho da pasta de músicas dentro da pasta do usuário. |
| Aspas duplas | Permitem interpretar a variável `$HOME` e tratar o caminho como um único argumento. |

Se o comando funcionar, o prompt poderá ficar assim:

```text
PS C:\Users\MY_USER\Music>
```

> Não é necessário entrar na pasta de músicas antes de executar os downloads. O comando do yt-dlp já informa onde salvar os arquivos.

## 4. Testar o download de um Reel

Antes de processar a lista inteira, teste um único link.

Substitua `CODIGO_DO_REEL` pelo código do Reel ou substitua o endereço inteiro pelo link real:

```powershell
yt-dlp -x --audio-format mp3 --audio-quality 2 --windows-filenames -P "$HOME\Music\ReelsAudio" -o "%(id)s.%(ext)s" "[https://www.instagram.com/reel/CODIGO_DO_REEL/](https://www.instagram.com/reel/CODIGO_DO_REEL/)"
```

O arquivo MP3 será salvo em:

```text
C:\Users\SEU_USUARIO\Music\ReelsAudio
```

No exemplo do usuário MY_USER:

```text
C:\Users\MY_USER\Music\ReelsAudio
```

O nome do arquivo será baseado no identificador do Reel.

## 5. Criar uma lista de links

Abra o Bloco de Notas pelo PowerShell:

```powershell
notepad "$HOME\Downloads\links-reels.txt"
```

Se o programa perguntar se deseja criar o arquivo, confirme.

Cole um endereço de Reel por linha:

```text
[https://www.instagram.com/reel/CODIGO_1/](https://www.instagram.com/reel/CODIGO_1/)
[https://www.instagram.com/reel/CODIGO_2/](https://www.instagram.com/reel/CODIGO_2/)
[https://www.instagram.com/reel/CODIGO_3/](https://www.instagram.com/reel/CODIGO_3/)
```

Adicione todos os links desejados.

Depois:

1. Pressione `Ctrl + S`.
2. Feche o Bloco de Notas.

O arquivo ficará em:

```text
C:\Users\SEU_USUARIO\Downloads\links-reels.txt
```

> O arquivo de texto elimina a necessidade de executar um comando por Reel. Ainda é necessário reunir os links dos Reels que você deseja baixar.

## 6. Baixar todos os áudios em lote

Copie a linha inteira abaixo para o PowerShell e pressione Enter uma vez:

```powershell
yt-dlp --batch-file "$HOME\Downloads\links-reels.txt" -x --audio-format mp3 --audio-quality 2 --windows-filenames -P "$HOME\Music\ReelsAudio" -o "%(id)s.%(ext)s" --download-archive "$HOME\Downloads\reels-baixados.txt" --sleep-interval 5 --max-sleep-interval 10 --retries 5
```

### O que cada opção faz

| Opção | Finalidade |
|---|---|
| `--batch-file` | Lê os links do arquivo de texto. |
| `-x` | Extrai o áudio da mídia. |
| `--audio-format mp3` | Converte o áudio para MP3. |
| `--audio-quality 2` | Define a qualidade de conversão do áudio. |
| `--windows-filenames` | Usa nomes compatíveis com o Windows. |
| `-P` | Define a pasta de destino. |
| `-o "%(id)s.%(ext)s"` | Usa o identificador do Reel como nome do arquivo. |
| `--download-archive` | Registra os itens concluídos e evita downloads repetidos. |
| `--sleep-interval 5` | Define um intervalo mínimo de espera entre downloads. |
| `--max-sleep-interval 10` | Define o limite superior do intervalo de espera. |
| `--retries 5` | Permite novas tentativas em falhas de download. |

> As pausas não garantem proteção contra bloqueios. Novas tentativas também não resolvem necessariamente erros de login ou de extração.

## 7. Retomar downloads ou acrescentar novos links

Para baixar novos Reels:

1. Abra novamente a lista:

   ```powershell
   notepad "$HOME\Downloads\links-reels.txt"
   ```

2. Acrescente os novos links.
3. Salve o arquivo.
4. Execute novamente o comando de download em lote.

O arquivo `reels-baixados.txt` mantém o registro dos itens concluídos.

Preserve esse arquivo para que o yt-dlp possa ignorar os Reels já baixados.

Se o processo for interrompido, execute novamente o mesmo comando para retomar os itens pendentes.

## 8. Quando o Instagram exigir login

Primeiro, confirme se o Reel abre normalmente no navegador.

Se o download exigir autenticação, o yt-dlp pode tentar utilizar os cookies de uma sessão já conectada.

### Exemplo com Firefox

Entre no Instagram pelo Firefox e teste:

```powershell
yt-dlp --cookies-from-browser firefox -x --audio-format mp3 --audio-quality 2 -P "$HOME\Music\ReelsAudio" -o "%(id)s.%(ext)s" "[https://www.instagram.com/reel/CODIGO_DO_REEL/](https://www.instagram.com/reel/CODIGO_DO_REEL/)"
```

### Processar a lista com a sessão do Firefox

```powershell
yt-dlp --cookies-from-browser firefox --batch-file "$HOME\Downloads\links-reels.txt" -x --audio-format mp3 --audio-quality 2 --windows-filenames -P "$HOME\Music\ReelsAudio" -o "%(id)s.%(ext)s" --download-archive "$HOME\Downloads\reels-baixados.txt" --sleep-interval 5 --max-sleep-interval 10 --retries 5
```

### Cuidados com a autenticação

- Teste primeiro sem cookies.
- Nunca compartilhe cookies ou arquivos de sessão.
- A leitura dos cookies pode falhar por permissões ou proteções do navegador.
- A autenticação não garante que o download funcionará.
- A automação pode resultar em restrições da conta.

## 9. Atualizar o yt-dlp

Se a instalação foi feita pelo WinGet:

```powershell
winget upgrade --source winget --id yt-dlp.yt-dlp --exact
```

Se estiver utilizando o executável oficial com atualização própria:

```powershell
yt-dlp -U
```

Manter o programa atualizado pode ajudar quando o Instagram altera o funcionamento da plataforma.

## 10. Extrair áudio de um vídeo já baixado

Se você já possui o arquivo MP4, pode converter o áudio diretamente, sem acessar o Instagram:

```powershell
ffmpeg -i "reel.mp4" -vn -c:a libmp3lame -q:a 2 "reel.mp3"
```

Execute o comando na pasta onde está o vídeo ou informe os caminhos completos.

### Exemplo com caminhos completos

```powershell
ffmpeg -i "$HOME\Downloads\reel.mp4" -vn -c:a libmp3lame -q:a 2 "$HOME\Music\reel.mp3"
```

O parâmetro `-vn` remove o vídeo da saída.

> A extração mantém o áudio presente no Reel, incluindo voz, música e efeitos misturados. Ela não separa instrumentos nem recupera a qualidade perdida no original.

## 11. Ouvir os arquivos no Spotify desktop

Depois de baixar os MP3s:

1. Abra o aplicativo Spotify no computador.
2. Acesse a foto do perfil → Configurações.
3. Localize a seção “Sua Biblioteca”.
4. Ative “Mostrar arquivos locais”.
5. Clique em “Adicionar uma fonte”.
6. Selecione:

   ```text
   C:\Users\SEU_USUARIO\Music\ReelsAudio
   ```

7. Abra “Sua Biblioteca” → “Arquivos locais”.
8. Selecione as músicas desejadas.
9. Clique com o botão direito e escolha adicionar à playlist.

### Selecionar todos os áudios

Na lista de arquivos locais:

1. Clique em uma faixa.
2. Pressione `Ctrl + A`.
3. Clique com o botão direito sobre a seleção.
4. Adicione as faixas à playlist desejada.

Se outras pastas estiverem habilitadas como fontes, a seleção poderá incluir músicas dessas pastas. Desative temporariamente as fontes que não deseja incluir.

> A fonte indica onde estão os arquivos. Você adiciona as músicas à playlist, não a pasta em si.

## 12. Ouvir no celular sem Premium

A reprodução dos seus arquivos locais não exige que você baixe a playlist pelo Spotify.

Os MP3s precisam estar armazenados no próprio celular.

### Android

1. Copie os MP3s do computador para o celular, por exemplo, para:

   ```text
   Music/ReelsAudio
   ```

2. Abra o Spotify.
3. Acesse a foto do perfil → Configurações e privacidade → Apps e dispositivos.
4. Ative “Arquivos de áudio locais”.
5. Conceda as permissões de acesso a músicas e áudio.
6. Abra “Sua Biblioteca” → “Arquivos locais”.
7. Teste a reprodução e adicione as faixas à playlist, se necessário.

### iPhone

1. No Spotify, acesse Configurações e privacidade → Apps e dispositivos.
2. Ative “Arquivos de áudio locais”.
3. Abra o aplicativo Arquivos.
4. Copie os MP3s para:

   ```text
   No Meu iPhone → Spotify
   ```

5. Volte ao Spotify.
6. Abra “Sua Biblioteca” → “Arquivos locais”.

> A playlist criada no computador não transfere automaticamente os MP3s para o celular. Não confunda a reprodução de arquivos locais com o download de músicas do catálogo do Spotify.

## 13. Problemas comuns

### “yt-dlp não é reconhecido”

Possíveis causas:

- O programa ainda não foi instalado.
- O terminal foi aberto antes da instalação.
- O executável não está no PATH.

Feche o terminal, abra novamente e teste:

```powershell
yt-dlp --version
```

Se baixou o executável manualmente para Downloads:

```powershell
cd "$HOME\Downloads"
.\yt-dlp.exe --version
```

No PowerShell, `.\` indica que o executável está na pasta atual.

### “ffmpeg não é reconhecido”

Instale o pacote:

```powershell
winget install --source winget --id Gyan.FFmpeg --exact
```

Depois, feche completamente o terminal, abra novamente e teste:

```powershell
ffmpeg -version
ffprobe -version
```

### “Unexpected token '\Music\'”

O caminho foi digitado sem um comando de navegação.

Use:

```powershell
cd "$HOME\Music"
```

Em vez de:

```powershell
$HOME\Music\
```

### “Instagram sent an empty media response” ou exigência de login

1. Confirme se o link abre no navegador.
2. Atualize o yt-dlp.
3. Teste um único Reel antes de executar a lista inteira.
4. Se necessário, tente a opção `--cookies-from-browser`.
5. Se persistir, pode haver bloqueio ou incompatibilidade temporária com o Instagram.

### Faixas cinzas no Spotify do celular

1. Confirme que os MP3s foram copiados para o celular.
2. Ative os arquivos locais e conceda as permissões.
3. Verifique se as faixas aparecem e tocam em “Arquivos locais”.
4. Se necessário, adicione as faixas à playlist a partir do próprio celular.

## Resumo do fluxo

```text
Instalar yt-dlp e FFmpeg
          ↓
Testar um Reel
          ↓
Reunir os links em links-reels.txt
          ↓
Executar um único comando de download
          ↓
Obter MP3s em Music\ReelsAudio
          ↓
Adicionar a pasta como fonte no Spotify desktop
          ↓
Adicionar as músicas