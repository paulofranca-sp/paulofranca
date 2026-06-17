# Como publicar o sistema no GitHub Pages

## O que você vai precisar
- Uma conta gratuita no GitHub: https://github.com
- Os arquivos desta pasta

---

## Passo a passo

### 1. Criar conta no GitHub
Acesse https://github.com e crie uma conta gratuita.
Use um e-mail que você acessa normalmente.

---

### 2. Criar um repositório
Após entrar no GitHub:
- Clique no botão verde **"New"** (canto superior esquerdo)
- Em **Repository name**, digite exatamente: `paulofranca`
- Deixe marcado como **Public**
- Clique em **"Create repository"**

---

### 3. Fazer upload dos arquivos
Na página do repositório recém-criado:
- Clique em **"uploading an existing file"**
- Arraste todos os arquivos desta pasta para a área indicada:
  - `index.html`
  - `anamnese.html`
  - `pesos.html`
  - `empreendimentos.html`
  - `motor.html`
- Clique em **"Commit changes"**

---

### 4. Ativar o GitHub Pages
- Clique em **"Settings"** (aba no menu do repositório)
- No menu lateral, clique em **"Pages"**
- Em **"Source"**, selecione **"Deploy from a branch"**
- Em **"Branch"**, selecione **"main"** e clique em **"Save"**

---

### 5. Acessar o site
Após 1–2 minutos, seu sistema estará disponível em:

```
https://SEU-USUARIO-GITHUB.github.io/paulofranca/
```

Substitua `SEU-USUARIO-GITHUB` pelo nome de usuário que você criou.

---

## Links que você vai compartilhar

| Destinatário | Link |
|---|---|
| Cliente (Anamnese) | `https://SEU-USUARIO.github.io/paulofranca/anamnese.html` |
| Cliente (Mapa de Pesos) | `https://SEU-USUARIO.github.io/paulofranca/pesos.html` |
| Uso interno (Motor) | `https://SEU-USUARIO.github.io/paulofranca/motor.html` |
| Uso interno (Empreendimentos) | `https://SEU-USUARIO.github.io/paulofranca/empreendimentos.html` |

---

## Domínio próprio (opcional)

Se você tiver ou quiser registrar `paulofranca.com.br`:
1. No registro do domínio, aponte o DNS para o GitHub Pages
2. No GitHub, em Settings > Pages, adicione o domínio personalizado

Os links ficariam:
- `paulofranca.com.br/anamnese.html`
- `paulofranca.com.br/pesos.html`

---

## Atualizar os arquivos no futuro

Quando precisar atualizar qualquer arquivo:
- Acesse o repositório no GitHub
- Clique no arquivo que quer substituir
- Clique no ícone de lápis (editar) ou use o botão de upload
- O site atualiza automaticamente em 1–2 minutos

---

## Observação importante

O banco de empreendimentos e os dados do localStorage ficam
**no navegador de cada usuário** — não no GitHub.

Isso significa:
- Paulo deve sempre usar o mesmo navegador e computador para o Motor
- O banco de empreendimentos cadastrado por Paulo fica salvo localmente
- Os JSONs baixados automaticamente são o backup permanente de cada cliente

