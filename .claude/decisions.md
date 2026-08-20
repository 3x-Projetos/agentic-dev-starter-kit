# Decisões do projeto

Registro de decisões que alguém pode questionar em três meses.
Mais recente no topo.

---

## 0001 · Stack inicial — OBRIGATÓRIA, preencha antes da primeira linha de código

> Conduzida pelo agente `stack-advisor`. Enquanto esta entrada estiver em branco,
> o projeto não tem decisão de stack — tem preferência não registrada.

### Restrições levantadas

| Dimensão | Resposta |
|---|---|
| Usuários — quantos, simultâneos, crescimento em 2 anos | |
| Interno ou externo / exposto na internet | |
| Dispositivo, offline, rede restrita | |
| Volume e natureza dos dados | |
| Dado pessoal ou regulado | |
| Integrações obrigatórias | |
| Onde roda — nuvem, servidor próprio, cliente | |
| Quem faz deploy e quem responde por incidente | |
| Infraestrutura já padronizada na empresa | |
| Quem mantém em 2 anos | |
| Prazo / durabilidade esperada | |

Restrição não respondida fica **explicitamente em branco** — não preencha por
suposição. Campo vazio aqui é aviso de risco, não descuido.

### Decisões por camada

| Camada | Escolha | Porta | Restrição que decidiu |
|---|---|:---:|---|
| Linguagem do servidor | | uma via | |
| Banco de dados | | uma via | |
| Hospedagem / execução | | uma via | |
| Framework de interface | | duas vias | |
| Build e empacotamento | | duas vias | |
| Autenticação | | | |

### Alternativas descartadas

| Alternativa | Por que não |
|---|---|
| | |

### Maturidade verificada

| Tecnologia | Último release | Mantenedor | Licença | Fonte |
|---|---|---|---|---|
| | | | | |

### Esqueleto ambulante

- Parte mais arriscada identificada:
- Provada de ponta a ponta no ambiente real? ( ) sim ( ) não
- O que a prova revelou:

### Gatilhos de revisão

O que obriga a reabrir esta decisão:

- [ ] Acima de `<N>` usuários simultâneos
- [ ] Se surgir requisito de `<...>`
- [ ] Se `<tecnologia>` entrar em fim de manutenção

---

## Template para as demais decisões

```markdown
## NNNN · AAAA-MM-DD — <título>

### Contexto
<a situação que forçou a decisão>

### Decisão
<o que foi decidido, sem ambiguidade>

### Justificativa
<o critério que decidiu>

### Alternativas descartadas
<o que foi considerado e por que não>

### Gatilho de revisão
<o que obriga a reabrir isto>

### Arquivos afetados
<lista>
```
