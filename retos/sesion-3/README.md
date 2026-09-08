# Sesión 3 — Escribe, despliega, registra

Hoy dejas de mirar código y escribes el tuyo. Al final de la sesión vas a tener
**tu propio contrato inteligente corriendo en Stellar**, con tu nombre y tus
reglas.

---

## Paso 0 · Abre el entorno

👉 **[Abrir en Codespaces](https://codespaces.new/QuillaBlocks/crowdfunding-dapp?quickstart=1)**

Trae Rust, el Stellar CLI y **el contrato ya compilado**, así que tus cambios
van a compilar en segundos y no en minutos.

Espera a que salga el recuadro que dice **«entorno listo»** antes de escribir
nada en la terminal.

Mientras carga, crea tu identidad y pide fondos de prueba:

```bash
stellar keys generate yo --network testnet --fund
stellar keys address yo          # cópiala, la vas a necesitar
```

---

## Paso 1 · El ejercicio: ponle un límite al aporte

¿Te acuerdas que en la Sesión 2 alguien mandó un aporte enorme y cerró la vaca
para todos los demás? Eso lo parchamos en la página web — pero **la página no es
la regla**. Cualquiera puede llamar al contrato directamente, sin pasar por
nuestra interfaz, y saltarse ese límite.

Hoy lo arreglamos donde de verdad manda: **dentro del contrato**.

### 1.1 · Agrega el error nuevo

Abre `contracts/crowdfunding/src/contract.rs`. Busca el `enum Error` y agrega
una variante al final:

```rust
pub enum Error {
    // … las que ya están …
    InvalidGoal = 11,
    AmountTooLarge = 12,     // ← agrega esta
}
```

> Los números no se repiten ni se reordenan: son parte del contrato. Si cambias
> el número de un error existente, rompes a cualquiera que ya lo estuviera
> manejando.

### 1.2 · Agrega la constante

Justo debajo de los `use` de arriba del archivo:

```rust
const MAX_CONTRIBUTION: i128 = 250_000_000;   // 25 XLM en stroops
```

> Un XLM son 10.000.000 de stroops. El contrato siempre trabaja en stroops,
> nunca en XLM.

### 1.3 · Agrega la guarda

Dentro de `contribute`, junto a la validación que ya existe de `amount <= 0`:

```rust
if amount <= 0 {
    return Err(Error::InvalidAmount);
}
if amount > MAX_CONTRIBUTION {         // ← agrega estas tres líneas
    return Err(Error::AmountTooLarge);
}
```

### 1.4 · Compila

```bash
stellar contract build
```

Si dice `✅ Build Complete`, tu Wasm ya es **distinto al original**. Acabas de
modificar un contrato inteligente.

Si sale un error, léelo: el compilador de Rust es de los que mejor explican qué
está mal. Casi siempre es una coma o un `;` que falta.

---

## ¿Vas rápido? Haz también esto

Agrega una función de solo lectura que exponga el límite, para que cualquiera
pueda consultarlo sin leer el código:

```rust
pub fn max_contribution(_env: Env) -> i128 {
    MAX_CONTRIBUTION
}
```

---

## Paso 2 · Despliega tu campaña

Los cuatro comandos, en orden. Cambia lo que está en MAYÚSCULAS.

```bash
# 1 · Sube tu Wasm a la red
stellar contract upload \
  --wasm target/wasm32v1-none/release/crowdfunding.wasm \
  --source yo --network testnet
```

Te devuelve un **hash**. Cópialo.

```bash
# 2 · Crea tu instancia con ese hash
stellar contract deploy \
  --wasm-hash EL_HASH_DEL_PASO_1 \
  --source yo --network testnet
```

Te devuelve tu **contract ID**, que empieza por `C`. **Ese es tu contrato.**
Guárdalo.

```bash
# 3 · Inicialízalo: aquí decides el nombre y la meta de TU campaña
stellar contract invoke --id TU_CONTRACT_ID --source yo --network testnet \
  -- initialize \
  --admin $(stellar keys address yo) \
  --name "EL NOMBRE DE TU CAMPAÑA" \
  --goal 1000000000 \
  --deadline 1790000000 \
  --token CDLZFC3SYJYDZT7K67VZ75HPJVIEUVNIXF47ZG2FB2RMQQVU2HHGCYSC
```

`--goal 1000000000` son 100 XLM. `--token` es el XLM nativo en testnet.

```bash
# 4 · Compruébalo
stellar contract invoke --id TU_CONTRACT_ID --source yo --network testnet \
  --send=no -- get_status
```

Y ábrelo en el explorador — es público, cualquiera en el mundo puede verlo:

```
https://stellar.expert/explorer/testnet/contract/TU_CONTRACT_ID
```

### Prueba que tu regla funciona

Este es el momento bonito. Intenta aportar más de lo que permitiste:

```bash
stellar contract invoke --id TU_CONTRACT_ID --source yo --network testnet \
  -- contribute --from $(stellar keys address yo) --amount 5000000000
```

Debe fallar con `Error(Contract, #12)` — que es tu `AmountTooLarge`. **Esa regla
la escribiste tú y ahora vive en la red.**

Ahora uno que sí pase:

```bash
stellar contract invoke --id TU_CONTRACT_ID --source yo --network testnet \
  -- contribute --from $(stellar keys address yo) --amount 100000000
```

---

## Paso 3 · Registra tu campaña

Para que quede en el registro público del programa, agrega tu archivo a
[`campanas/`](../../campanas) por pull request.

**Desde el navegador, sin usar git:**

1. Entra a [`campanas/`](../../campanas) en GitHub
2. **Add file → Create new file**
3. Nómbralo `TU-USUARIO-DE-GITHUB.json`
4. Copia esto y cambia los valores:

```json
{
  "campana": "El nombre de tu campaña",
  "descripcion": "Una línea sobre qué financia",
  "contract_id": "TU_CONTRACT_ID",
  "github": "tu-usuario",
  "red": "testnet",
  "sede": "cuc",
  "max_contribution": "25 XLM"
}
```

5. Abajo: **Create a new branch and start a pull request**
6. **Propose changes** → **Create pull request**

Listo. Ese pull request es tu entrega de la sesión.
