
---

# 📄 **Material Didático: Sistema de Login em PHP com `$_SESSION`**

> **Disciplina:** Linguagem e Técnicas de Programação    
> **Tema:** Variáveis de entrada e saída – Uso de sessões em PHP  
> **Professor:** José Carlos Flores  
> **Curso:** Engenharia de Software  
> **Objetivo:** Ensinar o uso de `$_SESSION` com separação de camadas (HTML, CSS, PHP)

---

## 🎯 Objetivo da Atividade

Ensinar como:
- Usar `$_SESSION` para manter dados do usuário
- Validar acesso a páginas protegidas
- Separar estilo (CSS) do conteúdo (HTML)
- Organizar arquivos em uma estrutura de pastas clara

---

## 🧩 Conceitos Abordados

| Conceito | Descrição |
|--------|---------|
| `session_start()` | Inicia ou retoma a sessão |
| `$_SESSION` | Armazena dados do usuário no servidor |
| `header('Location: ...')` | Redireciona para outra página |
| `session_destroy()` | Encerra a sessão |
| CSS externo | Boa prática de separação de camadas |
| Estrutura de pastas | Organização de projeto web |

---

## 📁 Estrutura do Projeto

```
sistema-login/
│
├── login.html     → Formulário de login
├── valida.php     → Valida login e inicia sessão
├── painel.php     → Página protegida
├── logout.php     → Encerra a sessão
└── css/
    └── estilo.css → Estilos compartilhados
```

> ✅ Todos os arquivos devem estar em:  
> `C:\xampp\htdocs\sistema-login\`

---

## ✅ 1. Arquivo CSS Externo: `css/estilo.css`

```css
/* estilo.css - Estilos para todo o sistema */

* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: #f0f2f5;
    line-height: 1.6;
}

.container {
    max-width: 400px;
    margin: 60px auto;
    background: white;
    padding: 30px;
    border-radius: 12px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
    text-align: center;
}

.container.full {
    max-width: 500px;
}

h2 {
    margin-bottom: 20px;
    color: #333;
    font-size: 1.5em;
}

p {
    color: #555;
    margin-bottom: 20px;
}

label {
    display: block;
    text-align: left;
    margin: 10px 0 5px;
    font-weight: 600;
    color: #333;
}

input[type="text"],
input[type="password"] {
    width: 100%;
    padding: 12px;
    margin-bottom: 20px;
    border: 1px solid #ccc;
    border-radius: 6px;
    font-size: 16px;
    transition: border-color 0.3s;
}

input[type="text"]:focus,
input[type="password"]:focus {
    border-color: #007cba;
    outline: none;
}

button {
    background: #007cba;
    color: white;
    padding: 12px 20px;
    border: none;
    border-radius: 6px;
    cursor: pointer;
    font-size: 16px;
    font-weight: 600;
    width: 100%;
    transition: background 0.3s;
}

button:hover {
    background: #005a87;
}

a {
    color: #007cba;
    text-decoration: none;
    font-weight: 500;
}

a:hover {
    text-decoration: underline;
}

.logout {
    margin-top: 20px;
    font-size: 0.9em;
}

/* Cores específicas por página */
.container.login h2 {
    color: #1a5fb4;
}

.container.painel h2 {
    color: #2e7d32;
}

.container.error h2 {
    color: #c62828;
}
```

---

## ✅ 2. `login.html` – Formulário com CSS Externo

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Login</title>
    <link rel="stylesheet" href="css/estilo.css">
</head>
<body>
    <div class="container login">
        <h2>🔐 Login</h2>
        <form action="valida.php" method="POST">
            <label for="usuario">Usuário:</label>
            <input type="text" id="usuario" name="usuario" required>

            <label for="senha">Senha:</label>
            <input type="password" id="senha" name="senha" required>

            <button type="submit">Entrar</button>
        </form>
    </div>
</body>
</html>
```

---

## ✅ 3. `valida.php` – Validação com Sessão

```php
<?php
// valida.php - Processa o login e inicia a sessão

session_start();

// Dados simulados (em produção: use banco de dados)
$usuario_correto = 'admin';
$senha_correta = '12345';

$usuario = $_POST['usuario'] ?? '';
$senha = $_POST['senha'] ?? '';

if ($usuario === $usuario_correto && $senha === $senha_correta) {
    // Armazena na sessão
    $_SESSION['logado'] = true;
    $_SESSION['usuario'] = $usuario;
    
    // Redireciona
    header('Location: painel.php');
    exit;
} else {
    // Página de erro com estilo
    echo "
    <!DOCTYPE html>
    <html lang='pt-BR'>
    <head>
        <meta charset='UTF-8'>
        <title>Erro</title>
        <link rel='stylesheet' href='css/estilo.css'>
    </head>
    <body>
        <div class='container error'>
            <h2>❌ Login inválido!</h2>
            <p>Usuário ou senha incorretos.</p>
            <a href='login.html'>Tentar novamente</a>
        </div>
    </body>
    </html>";
}
?>
```

---

## ✅ 4. `painel.php` – Página Protegida

```php
<?php
// painel.php - Página de boas-vindas

session_start();

// Protege a página
if (!isset($_SESSION['logado']) || $_SESSION['logado'] !== true) {
    header('Location: login.html');
    exit;
}

$usuario = htmlspecialchars($_SESSION['usuario'], ENT_QUOTES, 'UTF-8');
?>
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Painel</title>
    <link rel="stylesheet" href="css/estilo.css">
</head>
<body>
    <div class="container painel">
        <h2>✅ Bem-vindo, <?php echo $usuario; ?>!</h2>
        <p>Você está logado com sucesso no sistema.</p>
        
        <div class="logout">
            <a href="logout.php">Sair do sistema</a>
        </div>
    </div>
</body>
</html>
```

---

## ✅ 5. `logout.php` – Encerra a Sessão

```php
<?php
// logout.php - Encerra a sessão

session_start();
session_destroy();
?>
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Sair</title>
    <link rel="stylesheet" href="css/estilo.css">
</head>
<body>
    <div class="container">
        <h2>👋 Você saiu do sistema.</h2>
        <p>Sua sessão foi encerrada com segurança.</p>
        <a href="login.html">Fazer login novamente</a>
    </div>
</body>
</html>
```

---

## 🌐 Como testar

1. Crie a pasta: `C:\xampp\htdocs\sistema-login\`
2. Coloque todos os arquivos conforme a estrutura
3. Inicie o **Apache** no XAMPP
4. Acesse:  
   ```
   http://localhost/sistema-login/login.html
   ```
5. Faça login com:
   - **Usuário:** `admin`
   - **Senha:** `12345`

---

## 🧠 Atividades para os alunos

1. **Modifique o CSS** para um tema escuro.
2. Adicione um campo **"nível de acesso"** na sessão e mostre mensagens diferentes.
3. Crie uma página `perfil.php` acessível apenas pelo `admin`.
4. Armazene a data do login na sessão e exiba no painel.

---

## 📝 Boas práticas ensinadas

| Prática | Benefício |
|-------|----------|
| CSS externo | Reutilização, manutenção |
| `session_start()` no início | Evita erros |
| `htmlspecialchars()` | Prevenção contra XSS |
| `exit` após `header()` | Segurança |
| Estrutura de pastas | Organização profissional |

---

## 🔗 Recursos Complementares

- [PHP: Sessões – Manual Oficial](https://www.php.net/manual/pt_BR/book.session.php)
- [XAMPP – Baixe aqui](https://www.apachefriends.org)
- [CSS Básico – W3Schools](https://www.w3schools.com/css/)

---

## 📎 Créditos

Material elaborado por:  
**Prof. José Carlos Flores**  
Disciplina: Linguagem e Técnicas de Programação  
Instituição: Unicesumar  
Ano: 2025

---

---

# 📝 Questionário – Sistema de Login com `$_SESSION` em PHP (Aula 9)

**3. Qual arquivo é responsável por validar o login no sistema?**  
A) login.html  
B) painel.php  
C) logout.php  
D) valida.php  
E) estilo.css  
**✅ Resposta: D**

**4. Qual método HTTP é usado no formulário de login?**  
A) GET  
B) PUT  
C) DELETE  
D) POST  
E) HEAD  
**✅ Resposta: D**

**5. Por que usamos header('Location: painel.php') ?**  
A) Para exibir uma mensagem  
B) Para redirecionar o usuário  
C) Para encerrar a sessão  
D) Para validar o usuário  
E) Para carregar o CSS  
**✅ Resposta: B**

**6. O que acontece se session_start() for chamado após saída para o navegador?**  
A) A sessão é criada normalmente  
B) Um erro de cabeçalho (headers already sent) ocorre  
C) O usuário é redirecionado  
D) A sessão é salva no cookie  
E) Nada acontece  
**✅ Resposta: B**

**7. Qual superglobal armazena dados entre páginas com sessão?**  
A) $_GET  
B) $_POST  
C) $_COOKIE  
D) $_SESSION  
E) $_REQUEST  
**✅ Resposta: D**

**8. Como impedimos o acesso direto a painel.php por usuários não logados?**  
A) Usando exit()  
B) Verificando $_SESSION['logado']  
C) Usando htmlspecialchars()  
D) Apagando cookies  
E) Mudando a extensão do arquivo  
**✅ Resposta: B**

**9. Qual é o caminho correto para o arquivo CSS no sistema?**  
A) css/estilo.css  
B) /css/estilo.php  
C) estilo.css  
D) CSS/ESTILO.CSS  
E) ../css/estilo.html  
**✅ Resposta: A**

**10. O que faz session_destroy() ?**  
A) Apaga todos os dados da sessão  
B) Inicia uma nova sessão  
C) Redireciona para o login  
D) Valida o usuário  
E) Cria um cookie  
**✅ Resposta: A**

**11. Por que usamos htmlspecialchars() ao exibir o nome do usuário?**  
A) Para converter minúsculas em maiúsculas  
B) Para prevenir ataques XSS  
C) Para encurtar o nome  
D) Para salvar no banco de dados  
E) Para formatar a data  
**✅ Resposta: B**

**12. Qual tag HTML vincula um arquivo CSS externo?**  
A) &lt;style&gt;  
B) &lt;script&gt;  
C) &lt;link&gt;  
D) &lt;css&gt;  
E) &lt;import&gt;  
**✅ Resposta: C**

**13. O que significa HTTP 405?**  
A) Página não encontrada  
B) Método não permitido  
C) Erro interno do servidor  
D) Sessão expirada  
E) Arquivo não encontrado  
**✅ Resposta: B**

**14. Onde devem estar os arquivos PHP para funcionar com XAMPP?**  
A) Na área de trabalho  
B) Em C:\xampp\htdocs\  
C) Em C:\Windows\System32  
D) Em C:\Program Files\XAMPP  
E) Em qualquer pasta  
**✅ Resposta: B**

**15. Qual é a finalidade do exit; após header('Location: ...') ?**  
A) Mostrar uma mensagem  
B) Evitar execução de código posterior  
C) Reiniciar a sessão  
D) Fechar o navegador  
E) Salvar os dados  
**✅ Resposta: B**

**16. O que é $_POST['usuario'] ?? '' ?**  
A) Um operador de comparação  
B) Um operador de nave espacial  
C) Um operador de coalescência nula  
D) Um erro de sintaxe  
E) Um loop condicional  
**✅ Resposta: C**

**17. Qual é o valor padrão da variável $_SESSION['logado'] após login bem-sucedido?**  
A) null  
B) 'false'  
C) true  
D) 0  
E) 'off'  
**✅ Resposta: C**

**18. Qual arquivo não contém código PHP?**  
A) valida.php  
B) painel.php  
C) login.html  
D) logout.php  
E) Todos contêm PHP  
**✅ Resposta: C**

**19. O que acontece ao acessar logout.php ?**  
A) O usuário é redirecionado para o painel  
B) A sessão é destruída e o usuário sai  
C) Uma nova sessão é criada  
D) O CSS é recarregado  
E) O login é validado novamente  
**✅ Resposta: B**

**20. Qual é a porta padrão do Apache no XAMPP?**  
A) 8080  
B) 3306  
C) 21  
D) 80  
E) 443  
**✅ Resposta: D**
