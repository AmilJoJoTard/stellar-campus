# Campañas desplegadas

El registro público de los contratos que cada quien despliega en la **Sesión 3**.

## Cómo registrar la tuya

Todo desde el navegador, sin instalar nada.

1. **Add file → Create new file**, aquí en esta carpeta
2. Nombra el archivo `TU-USUARIO-DE-GITHUB.json`
3. Copia y pega esto, y cambia los cinco valores por los tuyos:

```json
{
  "campana": "El nombre que le pusiste a tu campaña",
  "descripcion": "Una línea sobre qué financia",
  "contract_id": "EL_ID_QUE_TE_DIO_EL_DEPLOY",
  "github": "tu-usuario-de-github",
  "sede": "uninorte"
}
```

4. Abajo, escoge **Create a new branch and start a pull request**
5. **Propose changes** → **Create pull request**

Ese pull request es tu entrega.

> Un archivo por persona, con tu usuario como nombre. Así nadie pisa el trabajo
> de nadie y no hay conflictos al mezclar los pull requests.

## Los cinco campos

| Campo | Qué va |
| --- | --- |
| `campana` | El nombre que le pusiste en `initialize` |
| `descripcion` | Una línea sobre qué financia |
| `contract_id` | El ID que te devolvió `stellar contract deploy`. Empieza por `C` |
| `github` | Tu usuario, sin `@` y sin URL |
| `sede` | `uninorte` o `cuc` |

[`ejemplo.json`](./ejemplo.json) es un archivo real y válido: si te pierdes,
ábrelo, cópialo y cámbiale los valores.

## Cómo verificar la tuya

Pega tu `contract_id` en
[Stellar Expert (testnet)](https://stellar.expert/explorer/testnet) y deberías
ver tu contrato desplegado, contigo como administrador.
