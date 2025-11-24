<?php
// Configurações básicas
$meu_email = "latiq.uea.lab@gmail.com"; // SUBSTITUA pelo email do laboratório
$assunto_padrao = "Contato do Site Latiq";

// Verificar se é um robô (honeypot)
if (!empty($_POST['url'])) {
    header("Location: Site_latiq.html?status=spam");
    exit;
}

// Coletar dados do formulário
$nome = trim($_POST['nome']);
$email = trim($_POST['email']);
$assunto = trim($_POST['assunto']);
$telefone = trim($_POST['telefone']);
$mensagem = trim($_POST['mensagem']);


if (empty($nome) || empty($email) || empty($assunto) || empty($mensagem)) {
    header("Location: Site_latiq.html?status=error");
    exit;
}

if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
    header("Location: Site_latiq.html?status=error");
    exit;
}

// Limitar tamanho da mensagem (opcional)
if (strlen($mensagem) > 2000) {
    header("Location: Site_latiq.html?status=error");
    exit;
}

// Preparar o conteúdo do email
$corpo_email = "
NOVA MENSAGEM DO SITE LATIQ

Nome: $nome
Email: $email
Telefone: " . ($telefone ?: 'Não informado') . "
Assunto: $assunto

MENSAGEM:
$mensagem

---
Enviado em: " . date('d/m/Y H:i:s') . "
IP: " . $_SERVER['216.172.172.193'] . "
";

// Preparar headers
$headers = "From: $email\r\n";
$headers .= "Reply-To: $email\r\n";
$headers .= "X-Mailer: PHP/" . phpversion();

// Tentar enviar o email
$email_enviado = mail($meu_email, $assunto_padrao . " - " . $assunto, $corpo_email, $headers);

// Redirecionar de volta para o site
if ($email_enviado) {
    header("Location: Site_latiq.html?status=success");
} else {
    header("Location: Site_latiq.html?status=error");
}
exit;
?>