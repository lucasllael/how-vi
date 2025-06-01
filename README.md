# Projeto e Desenvolvimento de Sistema Computacional com Persistência de Dados

**Data:** 2025-05-19  
**Keywords:** #database #ads  
**Notas Permanentes:**  
**Notas de Literatura:**  
**Links Externos:**

---

---

## Enunciado

**A)** Defina uma entidade de dados e que possua uma segunda entidade vinculada. O tipo de vínculo deve configurar uma relação de composição ou agregação da orientação a objetos.

- Cada entidade deve ter no mínimo 3 atributos. (Campo "id" não conta).

**B)** Etapa 1:

- Desenhe um diagrama de classes ([[uml-diagrama-classe]]) e também um diagrama de entidade relacionamento (ou diagrama lógico de BD) ([[DER]] ou [[MER]]) representando estas duas entidades, na perspectiva de Programação Orientada a Objetos ([[OOP]]) e na perspectiva de tabelas, respectivamente.
- Entregue também protótipos de tela para as funcionalidades descritas na etapa 2.

**C)** Etapa 2:

- Implemente interfaces web (telas) para realização de cadastro e listagem dos dados destas entidades interagindo com o BD. Por meio de telas o usuário deve ser capaz de:
  - Incluir registros para cada uma das duas entidades;
  - Listar todos os registros de cada entidade.

## Entregas

Para cada etapa deve ser entregue um único arquivo PDF exibindo os artefatos que evidenciem as entregas: diagramas, código, telas, print screens, etc. O PDF deve ser bem estruturado com capa, nome da equipe, sumário, descrição do escopo do trabalho e explicação detalhada de cada evidência apresentada.

Para entrega da parte 2, além do PDF detalhado, deve ser entregue um vídeo de no máximo 4 minutos apresentando o software em funcionamento.

Obs: Apenas 1 arquivo PDF deve ser postado para cada uma das etapas.

- **Entrega 1**: não é necessário enviar os arquivos editáveis dos diagramas, e sim o print deles deve constar no PDF.
- **Entrega 2**: O código fonte não precisa ser entregue em separado. Ao invés disso, você deve documentar as principais partes do mesmo no documento PDF entregue. O vídeo que irá apresentar a aplicação em funcionamento e uma visão geral do código fonte pode ser postado diretamente na plataforma ou incluir o link (youtube por exemplo), no arquivo PDF entregue.

## Etapa 1

### Escopo

Uma empresa administradora de condomínio precisa de um sistema de controle de acesso para seus edifícios. A empresa optou por utilizar chaves de acesso com QR Code em todas as unidades, permitindo ser impresso em cartões ou disponível em dispositivos móveis.  
Sendo uma necessidade que o zelador responsável de cada edifício tenha acesso aos apartamentos para casos especiais de emergência ou manutenções requisitadas pelos condôminos.

### Levantamento de requisitos de banco de dados

1. **Gerenciamento de Perfis de Acesso:** O sistema de banco de dados deve suportar o gerenciamento robusto de diferentes perfis de acesso para unidades condominiais. Isso inclui a definição e aplicação de permissões de acesso **padrão** para condôminos em suas respectivas unidades, e **permissões monitoradas** para zeladores que terão uma chave mestra referente ao edifício, além do registro de tentativas de acesso não autorizadas.
2. **Autenticação de Usuários:** O banco de dados deve armazenar credenciais de autenticação seguras para zeladores, condôminos, garantindo a integridade e confidencialidade dos dados. A arquitetura deve permitir a validação e autorização de acessos com base nessas credenciais.
3. **Registro e Auditoria de Eventos de Acesso:** O sistema de banco de dados deve registrar todos os eventos de acesso, incluindo acessos bem-sucedidos de zeladores e tentativas de acesso suspeitas. Cada registro deve incluir um **timestamp** preciso. Além disso, o banco de dados precisa suportar a funcionalidade de acionamento para notificações automatizadas aos condôminos em caso de eventos específicos, como acessos de zeladores ou tentativas suspeitas.
4. **Integração com QR Codes e Fechaduras Inteligentes:** O design do banco de dados deve acomodar a associação de **QR Codes** únicos a usuários e unidades específicas, permitindo a validação da autenticidade por meio de fechaduras inteligentes. A estrutura de dados deve facilitar a rápida recuperação e validação de informações do QR Code para autorização de acesso em tempo real.

### Projeto conceitual

Edificio (<ins>ed_id</ins>, nome, pais, uf, cidade, bairro, rua, num, zelador_qrcode, data_criacao, data_ultima_alteracao)  
Apartamento (<ins>apto_id</ins>, ed_id, bl, num, apto_qrcode, data_criacao, data_ultima_alteracao)

- ed_id referencia edificio

RegistroAcesso (<ins>reg_id</ins>, apto_id, tipo, data_criacao)

- apto_id referencia apartamento
- tipo (zelador, suspeito)

### Diagrama de Classe

Criado no plantuml.com com PlantUML.

Os métodos estáticos, que pertencem a classe, estão sublinhados para diferenciar dos métodos de objetos instanciados.

```PlantUML
@startuml
skinparam classAttributeIconSize 0

class Edificio {
  - ed_id : int
  - nome : str
  - pais : str
  - uf : str
  - cidade: str
  - bairro : str
  - rua : str
  - num : int
  - zelador_qrcode : bytes
  - data_criacao : DateTime
  - data_ultima_alteracao : DateTime
  {static} + create(nome: str, pais: str, uf: str, cidade: str, bairro: str, rua: str, num: int, zelador_qrcode: bytes) : Edificio
  {static} + list(filtros: str, ordenacao: str, pagina: int, tamanho_pagina: int) : List<Edificio>
  + getZeladorQrcode(): bytes
  + setZeladorQrcode(novo_zelador_qrcode: bytes): void
  + delete() : void
}

class Apartamento {
  - apto_id : int
  - bloco : str
  - num : int
  - apto_qrcode : bytes
  - data_criacao : DateTime
  - data_ultima_alteracao : DateTime
  {static} + create(ed_id: int, bl: str, num: int, apto_qrcode: bytes) : Apartamento
  {static} + list(filtros: str, ordenacao: str, pagina: int, tamanho_pagina: int) : List<Apartamento>
  + getAptoQrcode(): bytes
  + setAptoQrcode(novo_apto_qrcode: bytes): void
  + delete() : void
}

class RegistroAcesso {
  - reg_id : int
  - tipo : Tipo
  - data_criacao : DateTime
  {static} + create(apto_id: int, tipo: Tipo, timestamp: DateTime) : RegistroAcesso
  {static} + list(filtros: str, ordenacao: str, pagina: int, tamanho_pagina: int) : List<RegistroAcesso>
}

enum Tipo <<enumeration>> {
  + zelador
  + suspeito
  {static} + getDescription(tipo: Tipo) : str
}

Edificio "1 " *-- "1..* " Apartamento : " - ed_id"
Apartamento "1 " *-- "0..* " RegistroAcesso : " - apto_id"
@enduml
```

![Diagrama de Classe](./diagramas/class_diagram.png)

### Projeto lógico

Criado no dbdiagram.io com Database Markup Language

```DBML
Table edificios {
  ed_id INTEGER(3) [primary key, unique, not null, increment]
  nome VARCHAR(20) [not null]
  pais VARCHAR(20) [not null]
  uf VARCHAR(2) [not null]
  cidade VARCHAR(30) [not null]
  bairro VARCHAR(20) [not null]
  rua VARCHAR(30) [not null]
  num INTEGER(5) [not null]
  zelador_qrcode BLOB [not null]
  data_criacao TIMESTAMP [not null, default: `now()`]
  data_ultima_alteracao TIMESTAMP [not null, default: `now()`]
}

Table apartamentos {
  apto_id INTEGER(4) [primary key, unique, not null, increment]
  ed_id INTEGER(3) [not null]
  bloco VARCHAR(10)
  num INTEGER(4) [not null]
  apto_qrcode BLOB [not null]
  data_criacao TIMESTAMP [not null, default: `now()`]
  data_ultima_alteracao TIMESTAMP [not null, default: `now()`]
}

Table registros_acesso {
  reg_id INTEGER(4) [primary key, unique, not null, increment]
  apto_id INTEGER(4) [not null]
  tipo Tipo [not null]
  data_criacao TIMESTAMP [not null, default: `now()`]
}

Enum Tipo {
  zelador
  suspeito
}

ref: apartamentos.ed_id > edificios.ed_id
ref: registros_acesso.apto_id > apartamentos.apto_id
```

### Diagrama de Entidade e Relacionamento

![Diagrama de Entidade e Relacionamento](./diagramas/der.png)
