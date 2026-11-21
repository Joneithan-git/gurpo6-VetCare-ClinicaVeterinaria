# Normalização e Regras de Integridade — VetCare (Grupo 06)

**Responsável:** Laura Nazário

## 1. Auditoria de Normalização (1FN, 2FN e 3FN)

* **Primeira Forma Normal (1FN):** Eliminamos grupos repetidos extraindo os telefones do tutor para a tabela `tutor_telefone`, assegurando valores atômicos em todas as colunas.
* **Segunda Forma Normal (2FN):** Os dados descritivos da vacina (`nome_vacina`, `fabricante`) foram separados na tabela `vacina` para não se repetirem na tabela associativa `aplicacao_vacina`. Na entidade fraca `prescricao_item`, os atributos `medicamento` e `posologia` dependem integralmente da chave composta `(id_consulta, numero_item)`.
* **Terceira Forma Normal (3FN):** Eliminamos dependências transitivas mantendo atributos cadastrais do tutor apenas em `tutor` e do veterinário apenas em `veterinario`, evitando anomalias de inserção, atualização e exclusão.

## 2. Restrições de Esquema (DDL Conceitual)

* **Obrigatoriedade (`NOT NULL`):** `animal.id_tutor`, `consulta.id_animal`, `consulta.id_veterinario`, `consulta.data_consulta` e `aplicacao_vacina.data_aplicacao`.
* **Unicidade (`UNIQUE`):** `tutor.cpf` e `veterinario.crmv`.
* **Restrições de Domínio (`CHECK`):**
  * `tutor_telefone.tipo IN ('celular', 'residencial', 'emergencia')`
  * `aplicacao_vacina.dose IN ('1a_dose', '2a_dose', '3a_dose', 'reforco')`
* **Integridade Referencial:**
  * `ON DELETE RESTRICT` em `tutor -> animal` (impede excluir tutor com animais cadastrados).
  * `ON DELETE CASCADE` em `consulta -> prescricao_item` (garante a dependência existencial da entidade fraca).

## 3. Limite do `CHECK` vs. Regra de Processo (`RN-04`)

A regra **RN-04** (*animal sem consulta nos últimos 30 dias é considerado sem acompanhamento*) não pode ser garantida por uma restrição `CHECK`, pois o `CHECK` avalia apenas valores estáticos da linha no momento da gravação e não monitora a passagem contínua do tempo. Essa regra é validada na camada de consulta/aplicação por meio do cálculo dinâmico:

`DATEDIFF(CURRENT_DATE, MAX(consulta.data_consulta)) > 30`
