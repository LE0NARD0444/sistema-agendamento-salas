# Sistema de Gestão de Agendamento de Sublocação de Salas

**Documentação Técnica – Fase 1**  
**Janeiro de 2026**


## Índice

1. [Visão Geral do Projeto](#1-visão-geral-do-projeto)
2. [Objetivos do MVP](#2-objetivos-do-mvp)
3. [Requisitos Funcionais](#3-requisitos-funcionais)
4. [Requisitos Não-Funcionais](#4-requisitos-não-funcionais)
5. [Modelagem UML](#5-modelagem-uml)
   - 5.1 Diagrama de Classes  
   - 5.2 Diagrama de Componentes  
   - 5.3 Diagramas de Sequência
6. [Casos de Uso](#6-casos-de-uso)



## 1. Visão Geral do Projeto

**Título Original:**  
Sistema de Automação de Gestão de Agenda e Cobrança de Sublocação de Consultório.

**Escopo Remodelado (MVP):**  
Construção de um MVP focado exclusivamente no gerenciamento de agendamentos e cálculo de valores de sublocação de salas, sem integração com sistemas de cobrança financeira.

### Contexto

Este projeto tem como objetivo desenvolver um **MVP (Minimum Viable Product)** para gerenciar o agendamento e a sublocação de salas de consultório por profissionais.  
O sistema implementa regras de negócio para reservas, cancelamentos e cálculo de valores, sem realizar cobranças reais.

### Problema a Resolver

Profissionais que utilizam salas compartilhadas necessitam de um sistema que:

- Gerencie a disponibilidade das salas em tempo real
- Evite conflitos de agendamento
- Permita reservas avulsas e recorrentes
- Calcule automaticamente os valores mensais devidos
- Aplique regras de cancelamento justas

---

## 2. Objetivos do MVP

### Objetivo Geral

Desenvolver um sistema funcional para gerenciamento de agendamentos de salas, com cálculo automatizado de valores, sem integração com meios de pagamento.

### Objetivos Específicos

- **Modelagem de Dados:** Criar o esquema no PostgreSQL para as entidades principais (`Profissional`, `Sala`, `HorarioReservado`, `Recorrencia`)
- **Interface Web/Mobile:** Desenvolver aplicação em React para visualização de disponibilidade e agendamentos
- **Regras de Negócio:** Impedir conflitos de horários e controlar cancelamentos com antecedência mínima de 24 horas
- **Módulo de Cálculo:** Calcular o valor mensal devido com base nos horários utilizados


## 3. Requisitos Funcionais

### RF001 – Cadastrar Profissional
- **Descrição:** Permitir o cadastro de novos profissionais
- **Entrada:** Nome, e-mail, telefone, CPF
- **Saída:** ID do profissional e confirmação de cadastro

### RF002 – Visualizar Salas Disponíveis
- **Descrição:** Exibir salas disponíveis para locação
- **Saída:** Lista de salas com nome, capacidade e valor por hora

### RF003 – Criar Reserva de Horário
- **Descrição:** Criar reservas avulsas
- **Entrada:** Profissional, sala, data, hora início e fim
- **Validação:** Verificar conflitos de horário
- **Saída:** Confirmação da reserva com ID

### RF004 – Cancelar Reserva
- **Descrição:** Cancelar reservas com antecedência mínima de 24 horas
- **Entrada:** ID da reserva
- **Validação:** Verificação de prazo mínimo
- **Saída:** Confirmação ou mensagem de erro

### RF005 – Visualizar Disponibilidade
- **Descrição:** Exibir horários ocupados e livres por sala
- **Entrada:** Sala e data
- **Saída:** Lista de horários ocupados com nome do profissional

### RF006 – Criar Recorrência Semanal
- **Descrição:** Criar reservas recorrentes semanais
- **Entrada:** Profissional, sala, dia da semana, horários e período
- **Saída:** Confirmação da recorrência

### RF007 – Calcular Valor Mensal
- **Descrição:** Calcular valor mensal devido
- **Entrada:** Profissional, mês e ano
- **Cálculo:** Horas utilizadas × valor por hora
- **Saída:** Valor total e detalhamento

### RF008 – Projeção de Gastos Futuros
- **Descrição:** Projeção de gastos baseada em reservas futuras
- **Entrada:** Profissional e período
- **Saída:** Estimativa de valor
-
## 4. Requisitos Não-Funcionais

- **RNF001 – Performance:** Resposta em até 2 segundos
- **RNF002 – Disponibilidade:** 99% em horário comercial
- **RNF003 – Segurança:** Proteção de dados sensíveis (CPF)
- **RNF004 – Usabilidade:** Interface responsiva e intuitiva
- **RNF005 – Escalabilidade:** Suporte a 100 profissionais e 1000 reservas/mês
- **RNF006 – Manutenibilidade:** Código modular e documentado
- **RNF007 – Portabilidade:** Compatível com Linux, Windows e macOS
-

## 5. Modelagem UML

### 5.1 Diagrama de Classes 
┌─────────────────────────┐
│      Profissional       │
├─────────────────────────┤
│ - id: Integer           │
│ - nome: String          │
│ - email: String         │
│ - telefone: String      │
│ - cpf: String           │
│ - ativo: Boolean        │
│ - data_cadastro: Date   │
├─────────────────────────┤
│ + cadastrar()           │
│ + atualizar()           │
│ + desativar()           │
└─────────────────────────┘
         │ 1
         │
         │ *
         ↓
┌─────────────────────────┐
│    HorarioReservado     │
├─────────────────────────┤
│ - id: Integer           │
│ - sala_id: Integer      │
│ - profissional_id: Int  │
│ - data_reserva: Date    │
│ - hora_inicio: Time     │
│ - hora_fim: Time        │
│ - status: String        │
│ - data_criacao: DateTime│
│ - data_cancelamento: DT │
├─────────────────────────┤
│ + criar()               │
│ + cancelar()            │
│ + validarHorario()      │
│ + verificarConflito()   │
└─────────────────────────┘
         │ *
         │
         │ 1
         ↓
┌─────────────────────────┐
│         Sala            │
├─────────────────────────┤
│ - id: Integer           │
│ - nome: String          │
│ - capacidade: Integer   │
│ - valor_hora: Decimal   │
│ - descricao: Text       │
│ - ativa: Boolean        │
│ - data_cadastro: Date   │
├─────────────────────────┤
│ + cadastrar()           │
│ + atualizar()           │
│ + desativar()           │
│ + verificarDisponib()   │
└─────────────────────────┘


┌─────────────────────────┐
│      Recorrencia        │
├─────────────────────────┤
│ - id: Integer           │
│ - profissional_id: Int  │
│ - sala_id: Integer      │
│ - dia_semana: Integer   │
│ - hora_inicio: Time     │
│ - hora_fim: Time        │
│ - data_inicio: Date     │
│ - data_fim: Date        │
│ - ativa: Boolean        │
├─────────────────────────┤
│ + criar()               │
│ + atualizar()           │
│ + desativar()           │
│ + gerarHorarios()       │
└─────────────────────────┘


┌─────────────────────────┐
│     CalculoMensal       │
├─────────────────────────┤
│ - id: Integer           │
│ - profissional_id: Int  │
│ - mes: Integer          │
│ - ano: Integer          │
│ - total_horas: Decimal  │
│ - valor_total: Decimal  │
│ - data_calculo: DateTime│
├─────────────────────────┤
│ + calcular()            │
│ + obterHistorico()      │
│ + fazerProjecao()       │
└─────────────────────────┘


**5.2 Diagrama de Componentes**

┌───────────────────────────────────────────────┐
│              FRONTEND (React)                  │
├───────────────────────────────────────────────┤
│                                                │
│  ┌──────────────┐  ┌──────────────┐          │
│  │  Componente  │  │  Componente  │          │
│  │    Agenda    │  │   Reservas   │          │
│  └──────────────┘  └──────────────┘          │
│                                                │
│  ┌──────────────┐  ┌──────────────┐          │
│  │  Componente  │  │  Componente  │          │
│  │   Cálculo    │  │    Salas     │          │
│  └──────────────┘  └──────────────┘          │
│                                                │
│           API Service (Axios)                  │
└────────────────┬──────────────────────────────┘
                 │ HTTP REST
                 ↓
┌───────────────────────────────────────────────┐
│           BACKEND (Python/Flask)              │
├───────────────────────────────────────────────┤
│                                               │
│  ┌────────────────────────────────────────┐   │
│  │         API REST Controller             │  │
│  │  - /api/salas                           │  │
│  │  - /api/profissionais                   │  │
│  │  - /api/reservas                        │  │
│  │  - /api/recorrencias                    │  │
│  │  - /api/calculo                         │  │
│  └────────────────────────────────────────┘   │
│                    ↓                          │
│  ┌────────────────────────────────────────┐   │
│  │        Business Rules Layer             │  │
│  │  - Validação de horários                │  │
│  │  - Verificação de conflitos             │  │
│  │  - Regra de cancelamento 24h            │  │
│  └────────────────────────────────────────┘   │
│                    ↓                          │
│  ┌────────────────────────────────────────┐   │
│  │        Cálculo Service Layer            │  │
│  │  - Cálculo de valores mensais           │  │
│  │  - Projeção de gastos                   │  │
│  │  - Histórico de cálculos                │  │
│  └────────────────────────────────────────┘   │
│                    ↓                          │
│  ┌────────────────────────────────────────┐   │
│  │         Database Access Layer           │  │
│  │  - Connection Pool                      │  │
│  │  - Query Execution                      │  │
│  └────────────────────────────────────────┘   │
└────────────────┬──────────────────────────────┘
                 │ SQL
                 ↓
┌───────────────────────────────────────────────┐
│            DATABASE (PostgreSQL)              │
├───────────────────────────────────────────────┤
│  - profissional                               │
│  - sala                                       │
│  - horario_reservado                          │
│  - recorrencia                                │
│  - calculo_mensal                             │
└───────────────────────────────────────────────┘

**5.3 Diagrama de Sequência - Criar Reserva
Usuário    Frontend    API Backend    BusinessRules    Database**
   │           │            │               │             │
   │ Preenche  │            │               │             │
   │  Form     │            │               │             │
   │───────────>│            │               │             │
   │           │            │               │             │
   │           │ POST       │               │             │
   │           │ /reservas  │               │             │
   │           │────────────>│               │             │
   │           │            │               │             │
   │           │            │ validarHorario()            │
   │           │            │──────────────>│             │
   │           │            │               │             │
   │           │            │     OK        │             │
   │           │            │<──────────────│             │
   │           │            │               │             │
   │           │            │ verificarConflito()         │
   │           │            │──────────────>│             │
   │           │            │               │             │
   │           │            │               │ SELECT      │
   │           │            │               │─────────────>│
   │           │            │               │             │
   │           │            │               │  Resultado  │
   │           │            │               │<─────────────│
   │           │            │               │             │
   │           │            │  Sem conflito │             │
   │           │            │<──────────────│             │
   │           │            │               │             │
   │           │            │               │ INSERT      │
   │           │            │─────────────────────────────>│
   │           │            │               │             │
   │           │            │               │   ID        │
   │           │            │<─────────────────────────────│
   │           │            │               │             │
   │           │  201       │               │             │
   │           │  Created   │               │             │
   │           │<────────────│               │             │
   │           │            │               │             │
   │ Sucesso!  │            │               │             │
   │<───────────│            │               │             │

   
**5.4 Diagrama de Sequência - Cancelar Reserva
Usuário    Frontend    API Backend    BusinessRules    Database**
   │           │            │               │             │
   │ Clica     │            │               │             │
   │ Cancelar  │            │               │             │
   │───────────>│            │               │             │
   │           │            │               │             │
   │           │ POST       │               │             │
   │           │ /cancelar  │               │             │
   │           │────────────>│               │             │
   │           │            │               │             │
   │           │            │ podeCancelar()              │
   │           │            │──────────────>│             │
   │           │            │               │             │
   │           │            │               │ SELECT      │
   │           │            │               │─────────────>│
   │           │            │               │             │
   │           │            │               │ Reserva     │
   │           │            │               │<─────────────│
   │           │            │               │             │
   │           │            │ Verifica 24h  │             │
   │           │            │               │             │
   │           │            │     OK        │             │
   │           │            │<──────────────│             │
   │           │            │               │             │
   │           │            │               │ UPDATE      │
   │           │            │─────────────────────────────>│
   │           │            │               │             │
   │           │            │               │   OK        │
   │           │            │<─────────────────────────────│
   │           │            │               │             │
   │           │  200 OK    │               │             │
   │           │<────────────│              │             │
   │           │            │               │             │
   │ Cancelado │            │               │             │
   │<───────────│           │               │             │
   


## 6. Casos de Uso

### UC001 – Agendar Horário
- Ator: Profissional / Administrador
- Fluxo: Selecionar sala → Data → Horário → Confirmação

### UC002 – Cancelar Horário
- Ator: Profissional / Administrador
- Fluxo: Selecionar reserva → Cancelar → Validação 24h

### UC003 – Consultar Valor Mensal
- Ator: Profissional / Administrador
- Fluxo: Selecionar período → Cálculo → Exibição do total
🏢 Sistema de Gestão de Agendamento de Sublocação de Salas
📋 Descrição do Projeto :MVP de um sistema de gestão de agendamentos e regras de negócio para sublocação de salas, focado apenas na agenda e no cálculo, eliminando a cobrança real.




**Autor: Sistema de Agendamento de Salas**
**Versão: 1.0**
**Data: Janeiro 2026**
