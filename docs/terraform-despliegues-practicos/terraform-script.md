# Scripts de la práctica · Terraform: Despliegues prácticos e infraestructuras complejas

## Bloque 0 · Arranque

### Comandos

```bash
cd ~/terraform-mioti-lab
terraform version
az account set --subscription <subscription_id>
az account show --query "{suscripcion:name, subscriptionId:id, tenant:tenantId}" -o table
az storage account check-name --name sttfmioti<sufijo> --query nameAvailable
git switch -c lab bloque-1
```

## Bloque 1 · Backend

### `00-backend/terraform.tfvars`

```hcl
subscription_id = "<subscription_id>"
unique_suffix   = "<sufijo>"
location        = "spaincentral"
owner           = "<tu-nombre>"
cost_center     = "CC-LAB"
```

### `10-plataforma-datos/envs/dev.tfvars`

```hcl
subscription_id = "<subscription_id>"
environment     = "dev"
unique_suffix   = "<sufijo>"
location        = "spaincentral"
owner           = "<tu-nombre>"
cost_center     = "CC-LAB"
```

### `10-plataforma-datos/envs/dev.backend.hcl`

```hcl
storage_account_name = "sttfmioti<sufijo>"
container_name       = "tfstate"
key                  = "plataforma-datos/dev.tfstate"
use_azuread_auth     = true
```

### Comandos

```bash
terraform plan -var-file=envs/dev.tfvars
rm -f terraform.tfstate terraform.tfstate.backup
```

## Bloque 2 · Cadena de bases de datos

### `10-plataforma-datos/main.tf`

```hcl
resource "azurerm_key_vault" "data" {
  name                       = "kv-${local.base}"        # 3-24 caracteres, único global
  location                   = azurerm_resource_group.data.location
  resource_group_name        = azurerm_resource_group.data.name
  tenant_id                  = data.azurerm_client_config.current.tenant_id
  sku_name                   = "standard"
  rbac_authorization_enabled = true
  purge_protection_enabled   = false   # solo laboratorio: permite purgar al limpiar
  soft_delete_retention_days = 7
  tags                       = local.tags
}

resource "azurerm_role_assignment" "kv_secrets" {
  scope                = azurerm_key_vault.data.id
  role_definition_name = "Key Vault Secrets Officer"
  principal_id         = data.azurerm_client_config.current.object_id
}

resource "random_password" "sql_admin" {
  length      = 24
  special     = true
  min_upper   = 2
  min_lower   = 2
  min_numeric = 2
}

resource "azurerm_mssql_server" "data" {
  name                         = "sql-${local.base}"
  resource_group_name          = azurerm_resource_group.data.name
  location                     = local.sql_location
  version                      = "12.0"
  administrator_login          = "sqladmin${var.unique_suffix}"
  administrator_login_password = random_password.sql_admin.result
  minimum_tls_version          = "1.2"
  tags                         = local.tags
}

resource "azurerm_mssql_database" "main" {
  name                 = "sqldb-ventas"
  server_id            = azurerm_mssql_server.data.id
  sku_name             = "Basic"
  storage_account_type = "Local"    # copias de seguridad locales
  tags                 = local.tags

  lifecycle {
    prevent_destroy = true
  }
}

resource "azurerm_key_vault_secret" "sql_admin" {
  name         = "sql-admin-password"
  value        = random_password.sql_admin.result
  key_vault_id = azurerm_key_vault.data.id
  depends_on   = [azurerm_role_assignment.kv_secrets]
}
```

### `10-plataforma-datos/outputs.tf`

```hcl
output "resource_group_name" {
  value = azurerm_resource_group.data.name
}

output "sql_server_fqdn" {
  value = azurerm_mssql_server.data.fully_qualified_domain_name
}

output "key_vault_id" {
  value = azurerm_key_vault.data.id
}

output "sql_admin_password" {
  value     = random_password.sql_admin.result
  sensitive = true    # se oculta en pantalla; el state lo guarda igual
}
```

### Comandos

```bash
terraform fmt
terraform validate
terraform plan -var-file=envs/dev.tfvars -out=tfplan
terraform apply tfplan
```

### `10-plataforma-datos/variables.tf`

```hcl
variable "databases" {
  type = map(object({ sku_name = string }))
  default = {
    ventas   = { sku_name = "Basic" }
    finanzas = { sku_name = "Basic" }
  }
}
```

### `10-plataforma-datos/main.tf`

```hcl
resource "azurerm_mssql_database" "this" {
  for_each             = var.databases
  name                 = "sqldb-${each.key}"
  server_id            = azurerm_mssql_server.data.id
  sku_name             = each.value.sku_name
  storage_account_type = "Local"
  tags                 = local.tags

  lifecycle {
    prevent_destroy = true
  }
}
```

### Bloque `moved` de `10-plataforma-datos/main.tf`

```hcl
moved {
  from = azurerm_mssql_database.main
  to   = azurerm_mssql_database.this["ventas"]
}
```

## Bloque 3 · Módulo

### Comandos

```bash
mkdir -p backups
terraform state pull > backups/antes-del-modulo.tfstate
terraform plan -var-file=envs/dev.tfvars
```

### `10-plataforma-datos/main.tf`

```hcl
module "sql" {
  source              = "../modules/sql-database"
  name                = "sql-${local.base}"
  resource_group_name = azurerm_resource_group.data.name
  location            = local.sql_location
  admin_login         = "sqladmin${var.unique_suffix}"
  admin_password      = random_password.sql_admin.result
  databases           = var.databases
  tags                = local.tags
}

moved {
  from = azurerm_mssql_server.data
  to   = module.sql.azurerm_mssql_server.this
}

moved {
  from = azurerm_mssql_database.this          # mueve todas las instancias del for_each
  to   = module.sql.azurerm_mssql_database.this
}
```

### `10-plataforma-datos/outputs.tf`

```hcl
output "sql_server_fqdn" {
  value = module.sql.server_fqdn
}
```

### Comandos

```bash
grep -nE "azurerm_mssql_server\.data|azurerm_mssql_database\.(main|this)" *.tf
terraform init -backend-config=envs/dev.backend.hcl
terraform plan -var-file=envs/dev.tfvars -out=tfplan
```

### `10-plataforma-datos/envs/pre.tfvars` y `10-plataforma-datos/envs/pre.backend.hcl`

```hcl
# envs/pre.tfvars
subscription_id = "<subscription_id>"
environment     = "pre"
unique_suffix   = "<sufijo>"
location        = "spaincentral"
owner           = "<tu-nombre>"
cost_center     = "CC-LAB"

# envs/pre.backend.hcl
storage_account_name = "sttfmioti<sufijo>"
container_name       = "tfstate"
key                  = "plataforma-datos/pre.tfstate"
use_azuread_auth     = true
```

## Bloque 4 · Operar el state

### Crear el grupo de recursos heredado

```bash
az group create -n rg-legacy-<sufijo> -l spaincentral --tags project=mioti owner=desconocido
```

### `10-plataforma-datos/imports.tf`

```hcl
import {
  to = azurerm_resource_group.legacy
  id = "/subscriptions/${var.subscription_id}/resourceGroups/rg-legacy-${var.unique_suffix}"
}

resource "azurerm_resource_group" "legacy" {
  name     = "rg-legacy-${var.unique_suffix}"
  location = var.location
  tags = {
    project = "mioti"
    owner   = "desconocido"    # copiado tal cual de Azure: import limpio
  }
}
```

### Actualizar etiquetas y generar el plan

```bash
az group update -n rg-mioti-dev-<sufijo> --set tags.owner=cambiado-en-portal
terraform plan -var-file=envs/dev.tfvars
```

## Recuperación por bloque

### Bloque 2

```bash
cd ~/terraform-mioti-lab
git clean -fd
git switch --discard-changes -C lab bloque-2
cd 10-plataforma-datos
terraform init -backend-config=envs/dev.backend.hcl
terraform plan -var-file=envs/dev.tfvars -out=tfplan
terraform apply tfplan
```

### Bloque 3

```bash
cd ~/terraform-mioti-lab
git clean -fd
git switch --discard-changes -C lab bloque-3
cd 10-plataforma-datos
terraform init -backend-config=envs/dev.backend.hcl
terraform plan -var-file=envs/dev.tfvars -out=tfplan
terraform apply tfplan
```

### Bloque 4

```bash
cd ~/terraform-mioti-lab
git clean -fd
git switch --discard-changes -C lab bloque-4
cd 10-plataforma-datos
terraform init -backend-config=envs/dev.backend.hcl
terraform plan -var-file=envs/dev.tfvars -out=tfplan
terraform apply tfplan
```

### Reabrir Cloud Shell

```bash
cd ~/terraform-mioti-lab/10-plataforma-datos
```

## Bloque 7 · Cierre

### Comandos de limpieza

```bash
cd ~/terraform-mioti-lab/10-plataforma-datos
rm -f tfplan plan.json
sed -i 's/prevent_destroy = true/prevent_destroy = false/' ../modules/sql-database/main.tf ../00-backend/main.tf
grep -n prevent_destroy ../modules/sql-database/main.tf ../00-backend/main.tf
terraform init -reconfigure -backend-config=envs/dev.backend.hcl
terraform destroy -var-file=envs/dev.tfvars
cd ../00-backend
terraform destroy
az group list --query "[?contains(name, '<sufijo>')].name" -o tsv
az keyvault list-deleted --query "[?contains(name, '<sufijo>')].name" -o tsv
```
