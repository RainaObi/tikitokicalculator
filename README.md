# Calculadora de precio — TikTok Shop MX

Trabaja hacia atrás desde lo que quieres cobrar. Escribes cuánto quieres que te
liquiden y la página despeja el **precio flash** que hay que publicar, el
**precio lista** que lo acompaña y el **ROI** si capturas tu costo.

Una sola página, sin dependencias, sin build. Todo el cálculo corre en el
navegador y nada se guarda ni se envía a ningún lado.

## Qué descuenta

| Concepto | Cómo se cobra |
|---|---|
| Tarifa de servicio del SFP | 8% del precio flash, con tope de $40 por artículo |
| Tarifa por artículo vendido | $6 fijos, **por artículo** y no por pedido |
| Comisión de plataforma | 0% durante la ventana de vendedor nuevo, 6% después |
| Retención ISR | 2.5% sobre la base sin IVA |
| Retención IVA | 8% sobre la base sin IVA |

La base de todas las tarifas es el precio **después** de tu descuento de
vendedor. El descuento que pone TikTok Shop lo absorbe TikTok: baja lo que paga
el cliente pero no reduce tu liquidación.

ISR e IVA no son costo real — el ISR es pago provisional acreditable y el IVA
retenido es IVA que de todos modos ibas a enterar. El costo de canal es
solamente SFP + $6 + comisión.

## Fórmula

```
precio flash = (lo que quieres recibir + $6) ÷ (1 − SFP − comisión − 9.05%)
```

El 9.05% son las retenciones expresadas sobre el precio con IVA:
`(2.5% + 8%) ÷ 1.16`. Arriba de $500 el SFP topa en $40 y el despeje cambia a
`(objetivo + $6 + $40) ÷ (1 − comisión − 9.05%)`.

## Parámetros

Los valores traen los porcentajes vigentes a agosto de 2026, verificados contra
nueve liquidaciones reales con diferencia máxima de un centavo. Si TikTok
cambia alguno, se edita en la sección *Parámetros de la plataforma* al pie de
la página y todo se recalcula.

## Publicar

Es un solo `index.html`. Se sube al repo, se activa GitHub Pages desde
`Settings → Pages` apuntando a la rama `main`, y queda en línea.

## Licencia

MIT
