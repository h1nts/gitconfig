
# Configuração de Chaves SSH e Git para mais de um usuário/token

## 1. Criar uma chave SSH para cada conta

Exemplo:
```sh
ssh-keygen -t rsa -b 4096 -C "email@empresa1.com.br"
```

Ao criar, use um nome específico para a nova chave, como:
- `id_rsa_empresa1`
- `id_rsa_empresa2`
- `id_rsa_pessoal`
- E assim por diante.

## 2. Mover as chave criadas para o diretório `~/.ssh/`

```sh
mv id_rsa_* ~/.ssh/
```

## 3. Garantir o nível de acesso correto

```sh
chmod 600 ~/.ssh/id_rsa*
```
## 4. Adicionar chave SSH na respectiva conta GIT

Adicione a chave criada na respectiva conta do git https://github.com/settings/keys.

Nessa etapa, o pbcopy vai ajudar a copiar a chave:
```sh
 pbcopy < ~/.ssh/id_rsa_empresa1.pub
```

## 5. Configurar o Git


Aqui você está garantindo que cada pasta que você vai concentrar os repositório de cada conta, terá seu próprio gitconfig.

Abra o arquivo de configuração do Git:
```sh
nano ~/.gitconfig
```


Cole o seguinte conteúdo:
```txt
# Work GitHub
[user]
        name = Seu Nome
[core]
        autocrlf = input
[filter "lfs"]
        required = true
        clean = git-lfs clean -- %f
        smudge = git-lfs smudge -- %f
        process = git-lfs filter-process

[includeIf "gitdir:~/gits/empresa1/"]
    path = ~/gits/empresa1/.gitconfig

[includeIf "gitdir:~/gits/empresas/"]
    path = ~/gits/empresa2/.gitconfig

[includeIf "gitdir:~/gits/pessoal/"]
    path = ~/gits/pessoal/.gitconfig
```


## 5. Editar os arquivos específicos nas variáveis `path`

Aqui você vai criar uma espécie de alias, forçando o git a trocar os valores da url conforme configuracao abaixo.

Exemplo para `~/gits/profile1/.gitconfig`:
```sh
[user]
    email = email@empresa1.com.br
[url "empresa1:empresa-1"]
    insteadOf = git@github.com:empresa-1
```
No caso acima, consideramos que o codigo da empresa 1 no github é empresa-1.

## 6. Configurar o arquivo SSH

Nessa etapa você vai aproveitar o aliás que deu ao host, para informar ao seu ssh qual configuração específica é necessária para cada host, incluindo a chave ssh.

Abra o arquivo de configuração do SSH:
```sh
nano ~/.ssh/config
```

Cole o seguinte conteúdo:
```txt
Host empresa1
  HostName github.com
  AddKeysToAgent yes
  User git
  UseKeychain yes
  IdentityFile ~/.ssh/id_rsa_empresa1

Host empresa2
  HostName github.com
  AddKeysToAgent yes
  User git
  UseKeychain yes
  IdentityFile ~/.ssh/id_rsa_empresa2

Host pessoal
  HostName github.com
  AddKeysToAgent yes
  User git
  UseKeychain yes
  IdentityFile ~/.ssh/id_rsa_pessoal

Host *
  IdentitiesOnly yes
```

## 7. Remover o remoto do Git de cada repositório e readicionar

Por último você altera o remoto de cada repositório conforme o aliás configurado.

Exemplo para o repositório `exemple-repository`:
```sh
git remote set-url origin empresa1:empresa-1/exemple-repository.git
```

Pronto... Gostou? Deixa um star ali no cantinho...
Vlw!
