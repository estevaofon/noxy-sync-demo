# noxy-sync-demo

Projeto mínimo para exercitar o package manager do [Noxy](https://github.com/estevaofon/noxy) (v0.24.0+): `noxy.mod` declara as dependências diretas, `noxy.sum` é o lock com o fechamento inteiro, e `noxy_libs/` é derivado (fora do git).

```bash
git clone https://github.com/estevaofon/noxy-sync-demo.git
cd noxy-sync-demo
noxy --sync            # clona quicksort@v0.1.0 e baixa o binário do noxy_dynamodb@v0.3.0
noxy main.nx
```

- `noxy --sync --locked` falha se `noxy.sum` não descreve exatamente o que `noxy.mod` pede (use em CI).
- `noxy --get github.com/estevaofon/quicksort` sobe o pacote para a tag mais nova e regrava o lock.
- Apague `noxy_libs/` e rode `noxy --sync` de novo: o resultado é idêntico, verificado pelos hashes do `noxy.sum`.

O `main.nx` ordena um array com o `quicksort` (pacote de fonte) e carrega a extensão `noxy_dynamodb` (plugin por processo), fazendo um `scan`. Sem credenciais AWS o `scan` falha, e isso é esperado: o objetivo é ver o binário ser baixado, verificado e iniciado. Com credenciais, escolha a tabela:

```bash
AWS_PROFILE=meu_perfil DYNAMO_TABLE=minha_tabela noxy main.nx
```

No PowerShell (Windows), com `git` e `noxy.exe` (v0.24.0+) no PATH:

```powershell
git clone https://github.com/estevaofon/noxy-sync-demo.git; cd noxy-sync-demo
noxy main.nx                 # antes do sync: erro com a dica "run 'noxy --sync'"
noxy --sync                  # clona o quicksort e baixa noxy-plugin-dynamodb-windows-amd64.exe
$env:AWS_PROFILE = "meu_perfil"; $env:DYNAMO_TABLE = "minha_tabela"
noxy main.nx
noxy --sync --locked         # tudo "cached", nada reescrito
```

O perfil é lido de `%USERPROFILE%\.aws\credentials`.
