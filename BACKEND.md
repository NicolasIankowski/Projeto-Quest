# Backend PHP

A API do Projeto Quest fica em `api/index.php` e salva contas e waypoints no MySQL usando PDO. Cada conta tem uma coleção privada de waypoints; é necessário entrar para carregar ou editar o mapa.

## Iniciar localmente

No painel do XAMPP, inicie **Apache** e **MySQL**. Em uma instalação nova, importe `api/schema.sql` pelo phpMyAdmin (`http://localhost/phpmyadmin`) ou execute:

```powershell
Get-Content -Raw api\schema.sql | C:\xampp\mysql\bin\mysql.exe -u root
```

O arquivo cria o banco `projeto_quest`, a tabela `users` e a tabela `locations` vinculada ao proprietário. Se o banco já usa a tabela antiga `locations`, execute uma vez `api/migrate_users.sql` antes de iniciar a nova versão:

```powershell
Get-Content -Raw api\migrate_users.sql | C:\xampp\mysql\bin\mysql.exe -u root
```

Waypoints antigos ficam sem proprietário e não aparecem para nenhuma conta. Para preservar esses dados, atribua-os manualmente a uma conta depois de criá-la, usando o e-mail dessa conta para encontrar seu `users.id`. A configuração padrão da API é `127.0.0.1`, banco `projeto_quest`, usuário `root` e senha vazia, que corresponde à instalação local padrão do XAMPP. Para outra configuração, defina `DB_HOST`, `DB_NAME`, `DB_USER` e `DB_PASSWORD` no ambiente do Apache.

No mapa, escolha **Criar conta** para cadastrar um e-mail e uma senha de pelo menos 8 caracteres. Cada conta só lista, altera ou remove os próprios waypoints.

Confirme que a extensão `pdo_mysql` está habilitada no `php.ini` do XAMPP. Em seguida, no PowerShell, dentro da pasta do projeto:

```powershell
php -S localhost:8000
```

Depois abra:

```text
http://localhost:8000/MapFront.html
```

A página consulta automaticamente:

```text
GET http://localhost:8000/api/index.php?resource=locations
```

## Pesquisa do mapa

A pesquisa encontra primeiro os waypoints retornados pela API local. Para ruas, cidades, países e outros locais, a chave do Google Maps precisa ter a **Maps JavaScript API** e a **Places API (New)** habilitadas, com faturamento ativo no projeto Google Cloud.

Abra o painel do Google Cloud e habilite **Places API (New)** para o mesmo projeto da chave usada em `js/config.js`. O campo mostra sugestões enquanto você digita; escolher uma sugestão localiza o ponto no mapa. O botão de pesquisa também consulta o texto digitado, e os waypoints da API local são pesquisados antes da consulta ao Google. A API antiga de sugestões Places não é utilizada.

## Rotas

A ferramenta **Planejar rota** aceita endereços ou nomes dos seus waypoints como origem e destino. Também permite usar a posição atual e escolher carro, caminhada, bicicleta ou transporte público. O mapa mostra o trajeto, duração, distância e instruções.

Habilite **Routes API** no mesmo projeto Google Cloud da chave do Maps e mantenha faturamento ativo. A disponibilidade de modos pode variar por região e os cálculos seguem a cobrança vigente do Google Maps Platform.

## Endpoints

### Verificar API

```text
GET /api/index.php?resource=health
```

### Listar locais

```text
GET /api/index.php?resource=locations
```

### Criar local

```powershell
Invoke-RestMethod `
  -Uri "http://localhost:8000/api/index.php?resource=locations" `
  -Method Post `
  -ContentType "application/json" `
  -Body '{"name":"Ilha do Tesouro","lat":-23.5505,"lng":-46.6333}'
```

Os campos obrigatórios são `name`, `lat` e `lng`. As coordenadas são validadas antes de serem salvas.

## Requisitos

O Apache, o MySQL e o PHP com `pdo_mysql` precisam estar ativos. Para verificar o PHP do XAMPP:

```powershell
php --version
```
