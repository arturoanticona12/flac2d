# Asentamiento sísmico del coronamiento FY39 frente al FY31 (texto para el memo FY39)

> Redactado el 2026-10-04 a partir de los resultados del MS FY39 (GM07 y GM08). Incluir en el memo FY39 actualizado.
> Figura de respaldo: `MS fy39/resultados/MS_mecanismo_talud.png` (y .pdf). Pendiente: figura equivalente del MSO
> cuando terminen sus dinámicos, y revisar que los números del MSO calcen con el mismo argumento.

El asentamiento máximo del coronamiento del MS al FY39 (0.62 m con GM07 y 0.55 m con GM08) es similar al obtenido al FY31 (0.6 m). Este resultado es consistente con el mecanismo de deformación que muestra el modelo, que no depende de la altura del muro.

1. **Mecanismo de deformación.** El desplazamiento sísmico se concentra en una franja superficial del talud aguas abajo. El desplazamiento paralelo a la cara alcanza unos 1.4 m en superficie, se reduce a la mitad entre 4 y 5 m de profundidad y es prácticamente nulo a 10–20 m (figura MS_mecanismo_talud). Su magnitud es casi uniforme a lo largo de toda la cara, del 10 % al 70 % de su longitud. Es el comportamiento de un talud infinito, ya identificado en el análisis FY31: «las deformaciones totales máximas se concentran superficialmente en la parte superior del talud aguas abajo, debido al menor confinamiento existente en esta zona».

2. **Origen del asentamiento del coronamiento.** El asentamiento en el borde aguas abajo del coronamiento es la componente vertical de ese deslizamiento superficial. Para una cara 2H:1V, 1.4 m × sen 26.6° ≈ 0.63 m, que coincide con el asentamiento máximo calculado.

3. **Por qué no crece con la altura.** El crecimiento al FY39 es aguas abajo. Mantiene la misma inclinación de cara (2H:1V) y el mismo enrocado sin cohesión, cuyo ángulo de fricción depende del confinamiento (42° cerca de la superficie). La aceleración de fluencia del deslizamiento superficial, k_y = tan(φ − β) ≈ 0.23–0.28 g, depende solo de la pendiente y de la resistencia cerca de la superficie. Por lo tanto es la misma en ambas configuraciones. El aumento de altura alarga la cara de 44 m a 78 m y aumenta el confinamiento del cuerpo del muro, que se mantiene prácticamente sin deformación bajo los 10–20 m superficiales. Ninguno de los dos cambios modifica la magnitud del deslizamiento superficial.

4. **Lo que sí aumenta.** Aumenta el volumen de enrocado movilizado (la franja superficial abarca ahora 78 m de cara) y el desplazamiento horizontal del talud: 1.0 m en el borde aguas abajo del coronamiento y 1.2 m a media altura del talud. En términos relativos, el asentamiento sobre la altura del muro baja de 2.4 % (FY31) a 1.8 % (FY39).

---

Notas internas (no incluir en el memo):
- No hay comparación FY31/FY39 con el mismo procedimiento numérico: malla FY39 de 2 m vs FY31 de 3 m; Rayleigh FY39 centrado en 2.9 Hz vs 2.5 Hz FY31. Se decidió no correr esas sensibilidades.
- Un Newmark de bloque rígido con la aceleración de cresta da menos que FLAC (0.15–0.6 m a lo largo del talud vs 1.4 m): no usarlo como prueba de magnitud.
