# Avaliação prática: CRUD de salas (PW3)

## 0. Preparação do repositório

### 0.1 Fork do repositório oficial
1. Acesse o repositório base no GitHub: https://github.com/etechas/pw3-ingressos-ui-av3
2. No canto superior direito da página, clique no botão **Fork**.
3. Escolha a sua conta pessoal (ou da dupla) no GitHub e confirme a criação do Fork.

### 0.2 Clone local
Abra o terminal e faça o clone do seu repositório bifurcado (troque `<seu-usuario>` pelo seu login do GitHub):
```bash
git clone https://github.com/<seu-usuario>/pw3-ingressos-av3.git
cd pw3-ingressos-av3
```

### 0.3 Branch de avaliação e identificação
Crie uma nova branch seguindo o padrão de nomenclatura com o primeiro nome dos integrantes:
```bash
git checkout -b alu1-alu2-av
```
Substitua `alu1` e `alu2` pelos nomes da dupla. Exemplo: `lucas-mariana-av`.

Confirme que você está na branch correta:
```bash
git branch
```
A linha correspondente à sua branch deve aparecer com asterisco: `* alu1-alu2-av`.

Abra o arquivo `README.md` e adicione o nome completo de cada integrante antes de iniciar o código.

---

## 1. Objetivo da atividade
Implementar as operações de CRUD para o recurso de salas no módulo administrativo em `/src/app/pages/admin/sala/`.

As telas devem se comunicar com a API REST disponível em:
`http://192.168.2.159:8080/salas`

A implementação deve seguir os mesmos padrões de arquitetura e codificação adotados no módulo de filmes (`/src/app/pages/filme/` e `src/app/core/services/filme.service.ts`):
- Injeção de dependências com a função `inject()`
- Uso de `Observable` consumido pelo pipe `async` nos templates
- Componentes no padrão Standalone
- Captura de parâmetros de rota com `ActivatedRoute` (`this.route.snapshot.params['id']`)
- Tipagem baseada nos modelos da pasta `src/app/core/models`

---

## 2. Endpoints da API back-end

A API está rodando na rede interna em `http://192.168.2.159:8080/salas`. Os endpoints disponíveis para a entidade Sala são:

| Método | Endpoint | Descrição | Corpo da requisição (payload) | Retorno HTTP |
| :--- | :--- | :--- | :--- | :--- |
| `GET` | `/salas` | Lista todas as salas ativas | Nenhum | `200 OK` com lista de salas |
| `GET` | `/salas/{id}` | Busca uma sala por ID | Nenhum | `200 OK` com dados da sala ou `404` |
| `POST` | `/salas` | Cadastra uma nova sala | `{"nome": string, "preco": number}` | `201 Created` com a sala gerada |
| `PUT` | `/salas/{id}` | Atualiza uma sala existente | `{"nome": string, "preco": number}` | `200 OK` com os dados atualizados |
| `DELETE` | `/salas/{id}` | Inativa/remove uma sala pelo ID | Nenhum | `204 No Content` |

Obs.: Consulte a documentação em http://192.168.2.159:8080/swagger-ui.html

---

## 3. Etapas de implementação

### 3.1 Serviço de salas (`src/app/core/services/sala.service.ts`)
Crie o arquivo `sala.service.ts` na pasta de serviços, seguindo a estrutura vista em `filme.service.ts`:
- Injete o cliente HTTP com `inject(HttpClient)`.
- Configure a URL base da API: `http://192.168.2.159:8080/salas`.
- Implemente os métodos tipados com a interface `Sala` (localizada em `src/app/core/models/sala.ts`):
  - `listar ativas` ou `listar`.
  - `buscar por id`.
  - `salvar`: se a sala já tiver `id`, envia requisição `PUT`; se não tiver, envia `POST`.
  - `excluir`.

### 3.2 Listagem de salas (`src/app/pages/admin/sala/sala-lista/`)
Na listagem (`sala-lista.ts` e `sala-lista.html`):
- No TypeScript, injete `SalaService` e declare a propriedade observável de `salas`.
- Carregue os dados chamando o método de listagem do serviço na inicialização do componente.
- No HTML, retire o comentário do laço `@for (item of (salas | async); track item.id)` para renderizar a tabela com os registros recebidos da API.
- No botão **Editar**, implemente a navegação para o formulário passando o identificador da sala.
- No botão **Excluir**, chame o método de exclusão do serviço. Ao concluir a exclusão com sucesso, atualize a listagem.

### 3.3 Formulário de salas (`src/app/pages/admin/sala/sala-form/`)
No componente de formulário (`sala-form.ts` e `sala-form.html`):
- Injete `SalaService`, `ActivatedRoute` e `Router`.
- O formulário reativo `formSala` já está estruturado com os campos `id`, `nome` e `preco`.
- No método `ngOnInit()`, leia o parâmetro `id` passado através da rota. Se o ID estiver presente:
  - Busque a sala na API por meio de `buscar por id`.
  - Preencha o formulário utilizando `this.formSala.patchValue(...)`.
- No método `save()`:
  - Chame o método `salvar` do serviço passando os dados do formulário.
  - Trate o retorno da requisição: após a confirmação de gravação, redirecione o usuário para a rota `/salas`.

### 3.4 Configuração de rotas (`src/app/app.routes.ts`)
Verifique se as rotas necessárias para a navegação do módulo de salas estão cadastradas:
- Rota de listagem: `/salas` apontando para `SalaListaComponent`
- Rota de cadastro: `/salas/novo` apontando para `SalaFormComponent`
- Rota de edição: por exemplo `/salas/:id/editar` ou `/salas/:id` apontando para `SalaFormComponent`

---

## 4. Critérios de correção
- Fork e branch criados estritamente no padrão solicitado.
- Arquivo `README.md` preenchido com nome completo e RM da dupla.
- `SalaService` criado utilizando `inject(HttpClient)` e métodos devidamente tipados com `Sala`.
- Tela de listagem exibindo os dados da API com o pipe `async`.
- Cadastro salvando novas salas via requisição `POST`.
- Edição recuperando os dados existentes via `GET` e gravando as alterações via `PUT`.
- Exclusão removendo a sala via `DELETE` e atualizando a tabela na tela.

---

## 5. Envio
Ao concluir e testar as funcionalidades:
1. Registre as alterações com commits na branch de trabalho da dupla.
2. Envie a branch para o repositório remoto:
   ```bash
   git push -u origin alu1-alu2-av
   ```
3. Verifique no GitHub se a branch e os arquivos atualizados foram publicados corretamente.
4. Compartilhe com o professor as alterações da sua branch no GitHub através da abertura de PR.




