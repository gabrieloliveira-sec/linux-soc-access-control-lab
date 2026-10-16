# Linux SOC Access Control Lab 🛡️

### Gerenciamento de usuários, grupos e permissões no Ubuntu Linux

## 📌 Sobre o projeto

Este projeto apresenta um laboratório prático de segurança Linux, desenvolvido em ambiente virtualizado com Ubuntu, simulando o controle de acesso a um diretório utilizado por uma equipe de Security Operations Center (SOC).

O objetivo foi implementar e validar mecanismos de controle de acesso, garantindo que somente usuários autorizados possam acessar recursos internos.

## 🎯 Objetivos

- Administrar usuários e grupos no Linux.
- Implementar permissões de arquivos e diretórios.
- Aplicar o princípio do menor privilégio (Least Privilege).
- Configurar SGID para herança de grupo.
- Implementar Access Control Lists (ACLs).
- Validar acessos autorizados e não autorizados.

## 🖥️ Ambiente utilizado

| Tecnologia | Aplicação |
|---|---|
| Ubuntu Linux | Sistema operacional do laboratório |
| Oracle VirtualBox | Virtualização |
| Bash | Execução dos comandos |
| chmod / chown | Gerenciamento de permissões |
| usermod / id | Administração de usuários e grupos |
| setfacl / getfacl | Configuração e auditoria de ACLs |

## 🔐 Implementação

### 1. Configuração do usuário SOC

O usuário `analista1` foi configurado como membro do grupo `soc`.

```bash
sudo usermod -aG soc analista1
id analista1
```

### 2. Criação do diretório protegido

Foi criado o diretório `/srv/soc`, destinado aos arquivos da equipe de segurança.

```bash
sudo mkdir -p /srv/soc
sudo chown root:soc /srv/soc
sudo chmod 2770 /srv/soc
```

A permissão `2770` permite acesso completo ao proprietário e ao grupo, bloqueia outros usuários e ativa o SGID.

### 3. Teste de acesso autorizado

O usuário `analista1` conseguiu criar um arquivo no diretório protegido.

```bash
sudo -u analista1 touch /srv/soc/relatorio.txt
```

**Resultado:** operação concluída com sucesso.

### 4. Teste de acesso não autorizado

Foi realizado um teste utilizando o usuário `lab`, que não pertence ao grupo `soc`.

```bash
sudo -u lab ls /srv/soc
```

**Resultado:**

```text
Permission denied
```

O sistema impediu o acesso ao diretório, demonstrando a aplicação das permissões configuradas.

### 5. Proteção individual dos arquivos

O arquivo inicial recebeu a permissão `640`.

```bash
sudo chmod 640 /srv/soc/relatorio.txt
```

Essa configuração concede leitura e escrita ao proprietário, somente leitura ao grupo e nenhuma permissão aos demais usuários.

### 6. Implementação de ACL padrão

Para controlar as permissões de novos arquivos e subdiretórios, foi configurada uma ACL padrão:

```bash
sudo setfacl -d -m u::rwx,g::rwx,o::--- /srv/soc
getfacl /srv/soc
```

Em seguida, foi criado um segundo arquivo:

```bash
sudo -u analista1 touch /srv/soc/relatorio2.txt
sudo ls -l /srv/soc
```

**Resultado observado:**

```text
-rw-r----- analista1 soc relatorio.txt
-rw-rw---- analista1 soc relatorio2.txt
```

O segundo arquivo herdou as permissões definidas pela ACL padrão e o grupo `soc` por meio do SGID.

## 📸 Evidências

### Configuração final de permissões e ACL

![Permissões Linux SOC](../soc-permissions.png)

A captura demonstra:

- Diretório protegido com SGID ativo.
- Arquivos pertencentes ao grupo `soc`.
- Permissões `640` e `660`.
- ACL padrão configurada para restringir o acesso de outros usuários.

## 🧠 Conceitos de segurança aplicados

**Least Privilege:** restrição de acesso aos recursos conforme a necessidade de cada usuário.

**Discretionary Access Control (DAC):** controle de permissões baseado em proprietários, grupos e regras do sistema de arquivos.

**SGID:** mecanismo utilizado para manter a herança de grupo em arquivos e subdiretórios.

**Access Control Lists (ACLs):** mecanismo de controle de acesso que permite definir permissões adicionais e regras padrão.

**Defense in Depth:** utilização de controles complementares de acesso no diretório e nos arquivos.

## ✅ Conclusão

O laboratório demonstrou a implementação prática de controles de acesso em Linux, incluindo administração de usuários e grupos, permissões, SGID e ACLs.

Os testes confirmaram que um usuário autorizado conseguiu criar arquivos no diretório protegido, enquanto um usuário fora do grupo recebeu uma negativa de acesso.

O projeto reforçou conhecimentos fundamentais de administração Linux, segurança de sistemas e proteção de recursos em ambientes corporativos.

---

**Projeto desenvolvido para fins educacionais e prática de Cibersegurança.**
