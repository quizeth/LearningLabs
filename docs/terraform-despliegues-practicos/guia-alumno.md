---
title: Terraform · Sesión 2 · Guía del laboratorio
---

# Terraform · Sesión 2 · Guía del laboratorio

**Despliegues prácticos e infraestructuras complejas** · Azure Cloud Shell (Bash) · 1 h 45 min

Hoy construyes una plataforma de datos encadenada: un backend seguro para el state, un Key Vault, un servidor SQL y sus bases de datos. Después la operas: la refactorizas a un módulo, importas un recurso creado a mano, detectas cambios hechos fuera de Terraform y, al final, lo limpias todo.

## Antes de empezar

### Cómo usar esta guía

- Sigue los pasos **en orden**. Cada paso parte de donde terminó el anterior.
- Cada bloque de código indica **en qué carpeta** estás y **qué fichero** tocas.
- Sustituye lo que va entre `< >` por tu valor, sin los símbolos `< >`.
- Para editar un fichero: `code <fichero>`. Escribe, guarda con `Ctrl+S` y cierra el editor.
- **Antes de cada `apply`, lee el plan.** Si no entiendes lo que propone, no lo apliques y avisa.
- Las preguntas marcadas como **Piensa** son para responder antes de ejecutar. La respuesta está debajo, plegada.

### Requisitos (tarea previa)

- Azure Cloud Shell abierto en modo **Bash** (el prompt termina en `$`, no en `PS ...>`).
- Almacenamiento de Cloud Shell montado.
- El repositorio clonado en `~/terraform-mioti-lab`.

Si aún no tienes el repositorio:

```bash
git clone https://github.com/quizeth/terraform-mioti-lab.git ~/terraform-mioti-lab
```

### Tus valores

Apúntalos antes de empezar. Los usarás en varios ficheros.

| Valor | Dónde se usa | Tu valor |
|---|---|---|
| `<sufijo>` | Nombres únicos en Azure. 4–8 caracteres, solo minúsculas y dígitos. No puede coincidir con el de ningún compañero. | |
| `<subscription_id>` | Ficheros `tfvars`. Te lo indica el instructor. | |
| `<tu-nombre>` | Tag `owner`. Minúsculas, sin espacios. | |
| Región | Siempre `spaincentral` | `spaincentral` |

### Si Cloud Shell se cierra

Cloud Shell se cierra tras 20 minutos sin actividad. Tus ficheros se conservan. Reábrelo en **Bash** y vuelve a la carpeta en la que estabas:

```bash
cd ~/terraform-mioti-lab/10-plataforma-datos
```

---

## Bloque 0 · Entorno (0:00–0:08)

### 0.1 · Leer un plan

El instructor proyecta un plan de ejemplo. Míralo con calma.

> **Piensa:** ¿cuántos recursos cambian? ¿Cuál es el peligroso y por qué? ¿Qué pasaría con los ficheros del container `raw`?

<details>
<summary>Respuesta</summary>

El grupo de recursos solo añade un tag: cambio en sitio, inocuo. El container se destruye y se recrea: pone «must be replaced» y dos atributos marcan «forces replacement». Si tiene ficheros, se pierden con él.

La última línea («Plan: 1 to add, 1 to change, 1 to destroy») no basta: una sustitución aparece como 1 add + 1 destroy. Busca siempre **must be replaced**, **forces replacement** y **-/+** en el detalle.

</details>

### 0.2 · Comprobar tu entorno

**Carpeta:** cualquiera.

```bash
cd ~/terraform-mioti-lab
terraform version
az account set --subscription <subscription_id>
az account show --query "{suscripcion:name, subscriptionId:id, tenant:tenantId}" -o table
az storage account check-name --name sttfmioti<sufijo> --query nameAvailable
git switch -c lab bloque-1
```

**Esperado:**
- `terraform version` muestra 1.7 o superior. Ignora el aviso «out of date».
- `SubscriptionId` coincide con el que te ha indicado el instructor.
- `check-name` devuelve `true`.
- Git responde «Switched to a new branch 'lab'».

**Si falla:**
- **`check-name` devuelve `false`:** el nombre ya existe en Azure. Elige otro sufijo y repite el comando.
- **«invalid reference: bloque-1»:** ejecuta `git fetch --tags` y repite `git switch`.
- **Otro tenant en `az account show`:** en el portal, cambia de directorio (Configuración → Directorios + suscripciones), reabre Cloud Shell y repite `az account set`.

---

## Bloque 1 · Backend y state seguros (0:08–0:20)

El state es la memoria de Terraform. Guarda en claro los atributos de todo lo que gestiona, contraseñas incluidas. Lo vas a mover a Azure, donde tendrá **bloqueo** (dos personas no aplican a la vez), **historial** (versiones para volver atrás) y **acceso por identidad** (sin claves).

El backend también es infraestructura. Por eso el stack `00-backend` guarda su propio state en local, a propósito.

### 1.1 · Leer el backend

Abre `00-backend/main.tf` y localiza estas cuatro líneas:

| Línea | Para qué sirve |
|---|---|
| `shared_access_key_enabled = false` | A la cuenta solo se entra con identidad, no con claves. |
| `versioning_enabled = true` y `delete_retention_policy` | Historial y papelera de los ficheros de state. |
| `prevent_destroy = true` | Terraform se niega a planificar la destrucción de esta cuenta. |
| `azurerm_role_assignment "state_rw"` | Te da permiso para leer y escribir el state con tu usuario. |

### 1.2 · Crear el backend

**Carpeta:** `00-backend`.

```bash
cd ~/terraform-mioti-lab/00-backend
cp terraform.tfvars.example terraform.tfvars
code terraform.tfvars
```

Deja `terraform.tfvars` así, con tus valores:

```hcl
subscription_id = "<subscription_id>"
unique_suffix   = "<sufijo>"
location        = "spaincentral"
owner           = "<tu-nombre>"
cost_center     = "CC-LAB"
```

> **Piensa:** ¿cuántos recursos va a crear el plan?

<details>
<summary>Respuesta</summary>

Cuatro: grupo de recursos, cuenta de almacenamiento, container y asignación de rol. El bloque `data` lee información, no crea nada.

</details>

```bash
terraform init
terraform plan -out=tfplan
terraform apply tfplan
```

Primero `plan -out` y luego `apply tfplan`: así aplicas exactamente el plan que has revisado, no uno nuevo calculado en el momento. Es lo que hace un pipeline.

**Esperado:** el plan dice `4 to add`. El apply termina sin errores en unos 90 segundos.

Comprueba que el rol se ha asignado a tu usuario:

```bash
az role assignment list --assignee "$(az ad signed-in-user show --query id -o tsv)" --scope "$(az storage account show -n sttfmioti<sufijo> --query id -o tsv)" --query "[].roleDefinitionName" -o tsv
```

**Esperado:** `Storage Blob Data Contributor`.

**Si falla:**
- **Error de validación de `unique_suffix`:** corrige el sufijo en `terraform.tfvars` y repite el plan.
- **`AuthorizationFailed` al crear el rol:** tu cuenta no puede asignar roles. Avisa al instructor.

### 1.3 · Verificar el endurecimiento (opcional)

```bash
SA=$(terraform output -raw storage_account_name)
RG=$(terraform output -raw resource_group_name)
az storage account show -n "$SA" -g "$RG" --query "{tls:minimumTlsVersion, sharedKey:allowSharedKeyAccess, publico:allowBlobPublicAccess}" -o table
az storage account blob-service-properties show --account-name "$SA" -g "$RG" --query "{versioning:isVersioningEnabled, softDelete:deleteRetentionPolicy.enabled}" -o table
```

**Esperado:** `TLS1_2`, `False`, `False` y después `True`, `True`.

### 1.4 · Crear la plataforma de datos con state local

**Carpeta:** `10-plataforma-datos`.

```bash
cd ../10-plataforma-datos
cp envs/dev.tfvars.example envs/dev.tfvars
cp envs/dev.backend.hcl.example envs/dev.backend.hcl
code envs/dev.tfvars
```

Deja `envs/dev.tfvars` así:

```hcl
subscription_id = "<subscription_id>"
environment     = "dev"
unique_suffix   = "<sufijo>"
location        = "spaincentral"
owner           = "<tu-nombre>"
cost_center     = "CC-LAB"
# sql_location  = "francecentral"    # solo si el bloque 2 falla por región
```

```bash
code envs/dev.backend.hcl
```

Deja `envs/dev.backend.hcl` así:

```hcl
storage_account_name = "sttfmioti<sufijo>"
container_name       = "tfstate"
key                  = "plataforma-datos/dev.tfstate"
use_azuread_auth     = true
```

Estos dos ficheros son personales: están en `.gitignore`, no se suben a Git y se conservan aunque cambies de rama.

```bash
terraform init
terraform plan -var-file=envs/dev.tfvars -out=tfplan
terraform apply tfplan
```

**Esperado:** `1 to add`: el grupo de recursos `rg-mioti-dev-<sufijo>`.

### 1.5 · Migrar el state al backend

El bloque `backend` no admite variables. Por eso el código solo dice `azurerm` y los valores de cada entorno llegan en `init` desde `envs/dev.backend.hcl`. Es la **configuración parcial** del backend.

```bash
mv backend.tf.disabled backend.tf
terraform init -migrate-state -backend-config=envs/dev.backend.hcl
```

`init` pregunta si quieres copiar el state existente al nuevo backend. Lee la pregunta y responde `yes`.

```bash
terraform state list
terraform plan -var-file=envs/dev.tfvars
rm -f terraform.tfstate terraform.tfstate.backup
```

**Esperado:**
- `state list` muestra `azurerm_resource_group.data` y `data.azurerm_client_config.current`, leídos ahora del state remoto.
- El plan dice **«No changes»**: el state remoto coincide con Azure.
- El `rm` borra la copia local del state antiguo, que ya no hace falta.

**Si falla:**
- **403 o `AuthorizationPermissionMismatch` en `init`:** el rol aún no se ha propagado. Espera 1–2 minutos y repite el mismo `init`.
- **Otro error de acceso:** comprueba que el nombre de la cuenta en `envs/dev.backend.hcl` es exactamente `sttfmioti<sufijo>`.

---

## Bloque 2 · Cadena de infraestructura de BBDD (0:20–0:48)

En un proyecto real, la base de datos va encadenada: el servidor necesita un grupo de recursos, la contraseña vive en un Key Vault y el Key Vault necesita permisos antes de aceptar secretos.

El orden de creación no lo escribes tú: Terraform lo calcula a partir de las referencias entre recursos. `depends_on` solo se escribe cuando una relación no se ve en el código.

### 2.1 · Completar la cadena

**Carpeta:** `10-plataforma-datos` · **Fichero:** `main.tf`.

```bash
code main.tf
```

Añade este código **al final** de `main.tf`, debajo de `azurerm_resource_group.data`:

```hcl
resource "azurerm_key_vault" "data" {
  name                       = "kv-${local.base}"
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

Fíjate en tres líneas antes de seguir:
- `resource_group_name = azurerm_resource_group.data.name`: dependencia **implícita**. Terraform la ve en el código.
- `depends_on = [azurerm_role_assignment.kv_secrets]`: dependencia **explícita**. El secreto no usa nada del rol, pero sin el rol Azure rechaza la escritura. Esa relación solo existe en Azure.
- `storage_account_type = "Local"`: por defecto, las copias de seguridad serían geo-redundantes, y en la región del laboratorio no se pueden crear así.

Ahora sustituye **todo** el contenido de `outputs.tf`:

```bash
code outputs.tf
```

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

> **Piensa:** ¿en qué orden se crearán los recursos? ¿Cuáles pueden crearse a la vez?

<details>
<summary>Respuesta</summary>

El grupo ya existe. El Key Vault y la contraseña no dependen entre sí: van en paralelo. El servidor espera a la contraseña. Después, el rol espera al Key Vault y la base de datos espera al servidor. El secreto es el último: necesita el Key Vault, la contraseña y el rol.

</details>

```bash
terraform fmt
terraform validate
terraform plan -var-file=envs/dev.tfvars -out=tfplan
terraform apply tfplan
```

**Esperado:**
- `validate` sin errores.
- El plan dice `6 to add`: contraseña, Key Vault, rol, servidor, base de datos y secreto.
- El apply tarda 3–4 minutos. Mientras tanto, observa en qué orden aparecen las líneas `Creating...` y compáralo con tu predicción.

**Si falla:**
- **El servidor falla con `RegionDoesNotAllowProvisioning`:** la región no admite servidores SQL nuevos. En `envs/dev.tfvars`, quita el `#` de la línea `sql_location` (queda `sql_location = "francecentral"`) y repite `plan -out` y `apply`.
- **La base de datos falla con `ProvisioningDisabled ... geo-redundant`:** falta `storage_account_type = "Local"`. Añádelo y repite `plan -out` y `apply`. Lo que ya se creó no se vuelve a crear.
- **403 al crear el secreto:** el rol del Key Vault aún no se ha propagado. Espera un minuto y repite `plan -out` y `apply`.

### 2.2 · ¿Dónde está la contraseña?

El instructor lo muestra en su pantalla.

Nadie ha escrito la contraseña: la genera Terraform y la guarda en el Key Vault. `sensitive = true` la oculta en pantalla (`terraform output` muestra `<sensitive>`), pero **no la cifra**: el state la contiene completa. Quien tenga acceso al state puede leerla. Por eso protegimos el backend en el bloque 1.

### 2.3 · De una base de datos a dos

Necesitas una segunda base de datos. En lugar de copiar el bloque, usarás `for_each`.

**Fichero:** `variables.tf`. Añade al final:

```bash
code variables.tf
```

```hcl
variable "databases" {
  type = map(object({ sku_name = string }))
  default = {
    ventas   = { sku_name = "Basic" }
    finanzas = { sku_name = "Basic" }
  }
}
```

**Fichero:** `main.tf`. Borra **entero** el bloque `resource "azurerm_mssql_database" "main"`, incluido su `lifecycle`, y escribe en su lugar:

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

> **Piensa:** ¿qué hará el plan con `sqldb-ventas`, que ya existe?

<details>
<summary>Respuesta</summary>

Para Terraform, `azurerm_mssql_database.main` y `azurerm_mssql_database.this["ventas"]` son direcciones distintas: propondrá **destruir** `sqldb-ventas` y crear dos bases nuevas.

`prevent_destroy` no lo impide: estaba dentro del bloque que has borrado y ha desaparecido con él.

</details>

Planifica **sin** `-out` y **no apliques**:

```bash
terraform plan -var-file=envs/dev.tfvars
```

**Esperado:** `azurerm_mssql_database.main will be destroyed`, se crean `this["finanzas"]` y `this["ventas"]`, y la línea final dice `Plan: 2 to add, 0 to change, 1 to destroy.` El plan tarda unos 20 segundos porque refresca cada recurso contra Azure: no se ha colgado.

Este plan borraría una base de datos con datos. Por eso nunca se aplica un plan sin leerlo.

Ahora dile a Terraform que es el mismo objeto con otra dirección. Añade al final de `main.tf`:

```hcl
moved {
  from = azurerm_mssql_database.main
  to   = azurerm_mssql_database.this["ventas"]
}
```

```bash
terraform plan -var-file=envs/dev.tfvars -out=tfplan
terraform apply tfplan
```

**Esperado:**
- El plan dice `azurerm_mssql_database.main has moved to azurerm_mssql_database.this["ventas"]`, crea `this["finanzas"]` y termina con `Plan: 1 to add, 0 to change, 0 to destroy.`
- El apply crea `sqldb-finanzas` en aproximadamente 1 min 30 s.

**Si falla:** si el plan sigue proponiendo destruir `main`, la clave del `moved` no coincide con la del mapa. Revisa `"ventas"`, con comillas.

> **Piensa:** ¿por qué el `depends_on` del secreto está justificado y uno entre el servidor y el grupo de recursos no lo estaría?

<details>
<summary>Respuesta</summary>

El servidor usa `azurerm_resource_group.data.name`: Terraform ya ve esa dependencia. El secreto no usa nada del rol, pero sin el rol la escritura falla. Esa relación solo existe en Azure, por eso se declara.

</details>

---

## Bloque 3 · Del código plano al módulo (0:48–1:03)

### 3.1 · Punto de partida limpio

Antes de mover nada: una copia del state y un plan que diga «No changes». Si el punto de partida no está limpio, no sabrás qué cambio viene del refactor.

```bash
mkdir -p backups
terraform state pull > backups/antes-del-modulo.tfstate
terraform plan -var-file=envs/dev.tfvars
```

**Esperado:** «No changes».

### 3.2 · El módulo es un contrato

Abre los ficheros de `modules/sql-database/`. No los modifiques.

- **`variables.tf`** es lo que el módulo acepta. La validación de `tags` exige `project`, `owner` y `cost_center` no vacíos: si faltan, el módulo se niega.
- **`outputs.tf`** es lo que el módulo promete: `server_id`, `server_fqdn` y `database_ids`.
- **`main.tf`** lleva `storage_account_type = "Local"`, igual que tu código. Si no lo llevara, el plan propondría cambiar las copias a geo-redundantes.
- **`prevent_destroy`** está dentro del módulo. Los argumentos de `lifecycle` solo admiten valores literales, así que quien usa el módulo no puede desactivarlo.

### 3.3 · Usar el módulo

**Fichero:** `main.tf`.
1. Borra entero el bloque `resource "azurerm_mssql_server" "data"`.
2. Borra entero el bloque `resource "azurerm_mssql_database" "this"`.
3. **No** borres el `moved` del bloque 2.
4. Añade al final:

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

**Fichero:** `outputs.tf`. Cambia solo el bloque `sql_server_fqdn`:

```hcl
output "sql_server_fqdn" {
  value = module.sql.server_fqdn
}
```

Comprueba que no quedan referencias a los recursos movidos fuera de los bloques `moved`:

```bash
grep -nE "azurerm_mssql_server\.data|azurerm_mssql_database\.(main|this)" *.tf
```

**Esperado:** solo aparecen líneas `from =` o `to =` de bloques `moved`. Cualquier otra línea es una referencia que hay que corregir.

Un módulo nuevo, aunque sea una carpeta local, hay que instalarlo. Por eso, antes del plan, `init`:

```bash
terraform init -backend-config=envs/dev.backend.hcl
terraform plan -var-file=envs/dev.tfvars -out=tfplan
```

> **Piensa:** ¿cuántos movimientos debe mostrar el plan? ¿Cuántos destroy?

<details>
<summary>Respuesta</summary>

Tres instancias se mueven: el servidor, `ventas` y `finanzas`. Ningún destroy. Si aparece uno solo, para y revisa.

</details>

**Esperado:** tres líneas `has moved to module.sql...` y `Plan: 0 to add, 0 to change, 0 to destroy.`

```bash
terraform apply tfplan
terraform state list
terraform plan -var-file=envs/dev.tfvars
```

**Esperado:**
- `Apply complete! Resources: 0 added, 0 changed, 0 destroyed.` Solo cambian direcciones en el state; Azure no se toca.
- `state list` muestra el servidor y las dos bases de datos dentro de `module.sql`.
- El último plan dice «No changes».

**Si falla:**
- **«Module not installed»:** falta el `init` anterior.
- **Error de referencia a `azurerm_mssql_server.data`:** no has actualizado `outputs.tf`.
- **El plan propone `~ storage_account_type = "Local" -> "Geo"`:** no apliques y avisa al instructor.
- **Falla la validación de tags:** `cost_center` está vacío en `envs/dev.tfvars`. Es el contrato del módulo funcionando.

### 3.4 · Versionar un módulo

Una carpeta `v1.0.0/` no es una versión: cualquiera puede editarla. Una versión es un tag de Git inmutable o una versión publicada en un registro.

```hcl
# Desde Git: se usa ?ref=
module "sql" {
  source = "git::https://<repo>/terraform-mioti-lab.git//modules/sql-database?ref=v1.0.0"
}

# Desde un registro: aquí sí funciona version
module "vnet" {
  source  = "<org>/vnet/azurerm"
  version = "~> 1.4"
}
```

Añadir un input con valor por defecto no rompe a nadie. Quitar un output o renombrar un input, sí.

### 3.5 · Mismo código, otro entorno

El mismo código con otro state es otro entorno. Vas a ver qué propondría en preproducción. **No lo apliques:** duplicaría el coste.

```bash
cp envs/pre.tfvars.example envs/pre.tfvars
cp envs/pre.backend.hcl.example envs/pre.backend.hcl
code envs/pre.tfvars
```

Igual que `dev.tfvars`, pero con `environment = "pre"`:

```hcl
subscription_id = "<subscription_id>"
environment     = "pre"
unique_suffix   = "<sufijo>"
location        = "spaincentral"
owner           = "<tu-nombre>"
cost_center     = "CC-LAB"
```

```bash
code envs/pre.backend.hcl
```

Igual que `dev.backend.hcl`, pero con otra `key`:

```hcl
storage_account_name = "sttfmioti<sufijo>"
container_name       = "tfstate"
key                  = "plataforma-datos/pre.tfstate"
use_azuread_auth     = true
```

> **Piensa:** ¿qué propondrá el plan de pre?

<details>
<summary>Respuesta</summary>

Crearlo todo: el state de pre está vacío. Mismo código, otro state, otro entorno.

</details>

```bash
terraform init -reconfigure -backend-config=envs/pre.backend.hcl
terraform plan -var-file=envs/pre.tfvars                 # NO aplicar
terraform init -reconfigure -backend-config=envs/dev.backend.hcl
terraform plan -var-file=envs/dev.tfvars                 # de vuelta en dev
```

**Esperado:** el plan de pre dice `Plan: 8 to add, 0 to change, 0 to destroy.` El último plan, ya en dev, dice «No changes».

**Si falla:** si el último plan propone crearlo todo, sigues apuntando a pre. Repite `terraform init -reconfigure -backend-config=envs/dev.backend.hcl`.

---

## Bloque 4 · Operar el state (1:03–1:13)

### 4.1 · Crear un recurso «heredado»

En todos los entornos hay algo creado a mano que nadie se atreve a tocar. Lo simulas con `az` y lo traes a Terraform sin cambiarlo.

```bash
az group create -n rg-legacy-<sufijo> -l spaincentral --tags project=mioti owner=desconocido
```

### 4.2 · Importarlo

**Fichero nuevo:** `imports.tf`. No hay que sustituir nada: usa tus variables.

```bash
code imports.tf
```

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

> **Piensa:** ¿qué hará el plan? ¿Y si en el código hubieras puesto un tag que no está en Azure?

<details>
<summary>Respuesta</summary>

«1 to import» y ningún cambio, porque el código copia lo que hay en Azure. Si el código fuera distinto, importaría y además propondría un cambio en sitio para igualarlo. Buena práctica: un PR con el import limpio y otro con la reconciliación.

</details>

```bash
terraform plan -var-file=envs/dev.tfvars -out=tfplan
terraform apply tfplan
terraform plan -var-file=envs/dev.tfvars
```

**Esperado:**
- El plan dice `azurerm_resource_group.legacy will be imported` y `Plan: 1 to import, 0 to add, 0 to change, 0 to destroy.`
- El apply termina con `Resources: 1 imported, 0 added, 0 changed, 0 destroyed.`
- El último plan dice «No changes». El bloque `import` puede quedarse en el código: ya no hace nada.

**Si falla:**
- **«Cannot import non-existent remote object»:** el grupo no existe o el nombre no coincide. Comprueba con `az group show -n rg-legacy-<sufijo> --query id -o tsv`.
- **El plan propone cambios además del import:** el código no copia exactamente lo que hay en Azure. Revisa los tags y la región.

### 4.3 · Drift: cambios hechos fuera de Terraform

Cambia un tag desde fuera de Terraform y planifica:

```bash
az group update -n rg-mioti-dev-<sufijo> --set tags.owner=cambiado-en-portal
terraform plan -var-file=envs/dev.tfvars
```

**Esperado:** `azurerm_resource_group.data will be updated in-place`, con una sola diferencia: `~ "owner" = "cambiado-en-portal" -> "<tu-nombre>"`. El plan dice `Plan: 0 to add, 1 to change, 0 to destroy.`

Ahora hay que decidir. O el cambio manual era un error y el apply lo revierte, o era correcto y hay que llevarlo al código. Lo que no vale es dejarlo así: el próximo apply de otra persona lo revertirá sin que nadie lo haya decidido.

Hoy decides que era un error, así que lo reviertes:

```bash
terraform plan -var-file=envs/dev.tfvars -out=tfplan
terraform apply tfplan
terraform plan -var-file=envs/dev.tfvars
```

**Esperado:** `Resources: 0 added, 1 changed, 0 destroyed.` y después «No changes».

### 4.4 · `state rm`, `removed` y `destroy`

El instructor lo muestra en su pantalla.

| | `terraform destroy -target` | `terraform state rm` | Bloque `removed` con `destroy = false` |
|---|---|---|---|
| En Azure | Borra el recurso | Sigue vivo y sin gestionar | Sigue vivo y sin gestionar |
| En el state | Lo quita | Lo quita | Lo quita |
| Trazabilidad | Plan revisable | Orden manual: no queda en Git | Declarativo: pasa por un PR |

Así se escribe un bloque `removed` (no lo apliques):

```hcl
removed {
  from = azurerm_resource_group.legacy
  lifecycle {
    destroy = false    # sale del state; sigue en Azure
  }
}
```

---

## Bloque 5 · Gate de políticas (1:13–1:21)

El instructor lo muestra en su pantalla. Tu entorno no cambia en este bloque.

Una revisión humana se olvida de un tag un viernes por la tarde; una regla, no. Las reglas se evalúan sobre el **plan**, no sobre el código: el plan ya tiene resueltas las variables, los módulos y los valores por defecto, y dice qué va a pasar.

El flujo del gate es:

```text
terraform plan -out=tfplan
terraform show -json tfplan > plan.json
conftest test plan.json --policy ../policies
```

Código de salida 0: el plan pasa. Código de salida 1: el pipeline se detiene.

Qué observar en la demo:
- **`policies/tags.rego`** exige `project`, `owner` y `cost_center` en los recursos que se crean o se actualizan. Un recurso que no cambia no dispara la regla.
- **`policies/network.rego`** bloquea un firewall del servidor SQL abierto a todo Internet (`0.0.0.0`–`255.255.255.255`).
- **`plan.json` contiene la contraseña del servidor SQL en claro.** No se abre, no se comparte y se borra al terminar.

Tres sitios para una regla:
1. **En el código:** `validation` de un módulo, `precondition`, `check`. Los controla quien escribe el código.
2. **En el PR:** este gate. Queda fuera del alcance de quien escribe el código.
3. **En la nube:** Azure Policy. Es la última línea y también frena lo que se crea desde el portal.

---

## Bloque 6 · Incidente: lock huérfano y state perdido (1:21–1:33)

El instructor lo muestra sobre su propio state. Tu entorno no cambia en este bloque.

### 6.1 · Lock huérfano

Un compañero lanza un `apply` y su proceso muere a mitad. El bloqueo del state queda puesto. Cualquier `plan` falla con `Error acquiring the state lock`.

Antes de forzar el desbloqueo, cuatro comprobaciones:
1. Preguntar al equipo si alguien está aplicando.
2. Mirar la antigüedad del bloqueo: si es reciente, esperar.
3. Revisar si hay un pipeline en marcha contra este state.
4. Apuntar quién desbloquea, cuándo y por qué.

Solo entonces: `terraform force-unlock <ID del bloqueo>`. Desbloquear con un apply vivo de verdad puede corromper el state.

### 6.2 · State borrado

Alguien borra el fichero del state. Sin state, el plan propone crearlo todo: si alguien lo aplicara, chocaría con recursos que ya existen. **No se aplica.**

Hay dos redes:
- **Soft delete** protege contra borrados.
- **Versioning** guarda una versión en cada escritura, así que también protege contra un state sobrescrito con algo corrupto.

Recuperar es promover una versión buena. Ojo: la versión más reciente **no** es la buena. El plan sin state ha escrito encima versiones casi vacías. La buena es la última versión grande (más de 1000 bytes). Después de restaurarla, el plan debe decir «No changes».

---

## Bloque 7 · Cierre y limpieza (1:33–1:45)

### 7.1 · El mismo patrón en otras nubes

| Patrón | Azure | AWS | GCP |
|---|---|---|---|
| Backend del state | `azurerm` (Blob Storage) | `s3` | `gcs` (el bucket debe existir antes) |
| Bloqueo | Nativo de Blob Storage | `use_lockfile = true` (el de DynamoDB está obsoleto) | Nativo del backend |
| Historial del state | Versioning y soft delete | Bucket Versioning | Object Versioning |
| Base de datos gestionada | `azurerm_mssql_server` / `_database` | `aws_db_instance` | `google_sql_database_instance` / `google_sql_database` |
| Secretos | Key Vault | Secrets Manager | Secret Manager |

### 7.2 · Checklist para tu repositorio

1. State remoto con bloqueo e historial.
2. `plan -out` y apply de ese plan, nunca de uno nuevo.
3. Copia del state antes de mover o importar nada.
4. `moved` e `import` en el código, no comandos sueltos.
5. Un refactor con un solo destroy no es un refactor: se para.
6. Contraseñas fuera del código, sabiendo que el state las guarda.

**Si no entiendes el plan, no estás listo para aplicar.**

### 7.3 · Limpieza

Destruyes todo, en orden. El backend va el último porque guarda el state de lo demás. Y `prevent_destroy` solo se quita editando el código: es incómodo a propósito.

**Paso 1 · Quitar `prevent_destroy`** (carpeta `10-plataforma-datos`):

```bash
cd ~/terraform-mioti-lab/10-plataforma-datos
rm -f tfplan plan.json
sed -i 's/prevent_destroy = true/prevent_destroy = false/' ../modules/sql-database/main.tf ../00-backend/main.tf
grep -n prevent_destroy ../modules/sql-database/main.tf ../00-backend/main.tf
```

**Esperado:** dos líneas, una por fichero, con `prevent_destroy = false`.

**Paso 2 · Destruir la plataforma de datos.** Lee el plan antes de responder `yes`:

```bash
terraform init -reconfigure -backend-config=envs/dev.backend.hcl
terraform destroy -var-file=envs/dev.tfvars
```

**Esperado:** `Plan: 0 to add, 0 to change, 9 to destroy.` (grupo de datos, Key Vault, rol, contraseña, secreto, servidor, dos bases de datos y `rg-legacy-<sufijo>`) y `Destroy complete! Resources: 9 destroyed.` en aproximadamente 1 min 30 s.

**Paso 3 · Destruir el backend.** Lee el plan antes de responder `yes`:

```bash
cd ../00-backend
terraform destroy
```

**Esperado:** `Plan: 0 to add, 0 to change, 4 to destroy.` y `Destroy complete! Resources: 4 destroyed.`

**Paso 4 · Comprobar que no queda nada:**

```bash
az group list --query "[?contains(name, '<sufijo>')].name" -o tsv
az keyvault list-deleted --query "[?contains(name, '<sufijo>')].name" -o tsv
```

**Esperado:** ninguna de las dos consultas devuelve nada.

**Si falla:**
- **`prevent_destroy` frena el destroy:** falta editar uno de los dos ficheros del paso 1.
- **El Key Vault aparece en `list-deleted`:** ejecuta `az keyvault purge -n kv-mioti-dev-<sufijo>`.

> **No borres** el grupo `cloud-shell-storage-…`: es el almacenamiento de tu Cloud Shell.

### 7.4 · Mini-reto para dos semanas

Sobre un repositorio real o inventado de tu equipo:
1. **Matriz de states:** entorno, dominio, responsable, clave del backend y quién puede aplicar.
2. **Un refactor sin destrucción** con `moved` o `import`, y el plan que demuestra 0 destroy.
3. **Un runbook de recuperación del state**, como el del bloque 6, adaptado a tu backend.

---

## Anexo · Si te quedas atrás

Cada bloque tiene un punto de partida guardado en el repositorio. Si te quedas atrás, salta al punto de partida del bloque en curso. Antes debes tener aplicado el backend (bloque 1.2) y creados `envs/dev.tfvars` y `envs/dev.backend.hcl` (bloque 1.4).

`git clean -fd` borra los ficheros nuevos que Git no sigue, pero **no** los ignorados: tus `envs/*.tfvars`, `envs/*.backend.hcl`, `backups/`, `.terraform/` y el state local se conservan. Sin él, Git se niega a cambiar de rama si ya tienes un fichero nuevo que el punto de partida también trae (por ejemplo, `backend.tf`).

Sustituye `<N>` por el bloque al que entras: `2`, `3` o `4`.

```bash
cd ~/terraform-mioti-lab
git clean -fd
git switch --discard-changes -C lab bloque-<N>
cd 10-plataforma-datos
terraform init -backend-config=envs/dev.backend.hcl
terraform plan -var-file=envs/dev.tfvars -out=tfplan
```

**Lee el plan antes de aplicar:** solo debe crear lo que te falte y **no destruir nada**. Si es así:

```bash
terraform apply tfplan
```

**Si falla:**
- **`init` dice que la configuración del backend ha cambiado:** tenías el state en local. Repite `init` con `-migrate-state` y responde `yes`.
- **«Untracked working tree file ... would be overwritten»:** no has ejecutado `git clean -fd`. Ejecútalo y repite `git switch`.
- **El plan propone destruir algo:** no apliques y avisa al instructor.
