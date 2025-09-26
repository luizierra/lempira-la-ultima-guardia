Diseño del Juego - Lempira: La Última Guardia
Índice
Flujo Principal del Juego

Especificaciones Técnicas

Diseño de la Interfaz (UI)

Mecánicas de Batalla

Sistema de Unidades

Diseño de Niveles

Progresión y Guardado

1. Flujo Principal del Juego
1.1 Diagrama de Flujo
text
App Inicia → Menú Principal → (Nueva Partida → Cutscene Intro) → Mapa de Batalla → 
→ (Victoria → Recompensas) || (Derrota → Game Over) → Guardar → Siguiente Nivel
1.2 Escenas Principales
Scene_MainMenu: Menú inicial con Nueva Partida/Continuar/Cargar

Scene_Cutscene_Intro: Narración histórica y tutorial

Scene_Battle_##: Escenas de batalla (01, 02, 03...)

Scene_Combate: Escena animada de pelea

Scene_GameOver: Pantalla de derrota/victoria

2. Especificaciones Técnicas
2.1 Plataforma y Controles
Plataforma: iOS/Android (móviles y tablets)

Resolución: 1920x1080 (adaptable a diferentes aspect ratios)

Controles: Exclusivamente táctiles (touch)

Orientación: Horizontal

2.2 Configuración de Cámara
Vista: Superior (top-down) con perspectiva 2D

Control de Cámara:

Press & Drag para desplazar vista

Límites: Grid 30x30

Zoom: Fixed (no zoom)

3. Diseño de la Interfaz (UI)
3.1 Menú Principal (Scene_MainMenu)
text
[ TÍTULO: Lempira - La Última Guardia ]
[ BOTÓN: NUEVA PARTIDA ]
[ BOTÓN: CONTINUAR ] (disabled si no hay save)
[ BOTÓN: CARGAR PARTIDA ]
Cargar Partida: Muestra 5 slots de guardado numerados

3.2 UI en Batalla (Scene_Battle)
Elementos permanentes:

Esquina Superior Derecha: Botón ☰ (Menú de Pausa)

Parte Superior: Condiciones de Victoria/Derrota

Parte Inferior: Panel de información de unidad (solo al seleccionar)

Menú de Pausa (al tocar ☰):

TERMINAR TURNO

REINICIAR NIVEL

SALIR AL MENÚ PRINCIPAL

3.3 Panel de Información de Unidad
Al seleccionar cualquier unidad:

text
[ Icono Unidad ] [ Nombre Unidad ]
[ Barra de Soldados: ██████████ (10/10) ]
ATQ: 5 | DEF: 4 | TER: [Icono Terreno]
4. Mecánicas de Batalla
4.1 Sistema de Turnos
Tipo: Turnos por equipos (jugador → IA)

Fase de Jugador: Mueve todas sus unidades

Fase de IA: Mueve todas las unidades enemigas

Indicador visual: Borde de color diferente para unidades activas

4.2 Movimiento y Selección
typescript
// Pseudocódigo - Lógica de selección
alTocarUnidad(unidad) {
  si (unidad.esAliada && esTurnoJugador) {
    resaltarCasillasMovimiento(unidad);
    oscurecerMapa(30%); // 70% de visibilidad
    mostrarPanelInfo(unidad);
  }
}
4.3 Tipos de Terreno y Movimiento
Terreno	Costo	Movimiento	Bono Defensa
Claro	1	4 espacios	+0
Selva	2	3 espacios	+1
Montaña	3	2 espacios	+2
Río	Infranqueable*	-	-1
*Cruzar río: Un turno en orilla → siguiente turno cruza

4.4 Sistema de Combate
Escena de Combate (Scene_Combate)
Duración: 2-3 segundos máximo

Elementos: Fondo según terreno + Sprites enfrentados

Animaciones:

Cuerpo a cuerpo: Avance y golpe

A distancia: Disparo estático

Sin sangre, impacto estilizado

Fórmulas de Combate
typescript
// Daño base ajustado por soldados vivos
dañoBase = ataqueAtacante * (soldadosActuales / soldadosMaximos);

// Bonificación de terreno para DEFENSOR
defensaFinal = defensaDefensor + bonoTerreno[terrenoDefensor];

// Daño final
dañoFinal = max(1, dañoBase - defensaFinal);

// Reducir soldados del defensor
defensor.soldadosActuales -= dañoFinal;
5. Sistema de Unidades
5.1 Clase Base de Unidad
typescript
class Unidad {
  // Identificación
  nombre: string;
  tipo: TipoUnidad; // Heroe, Arquero, Lancero, etc.
  facción: Facción; // Lenca, Español, Neutral
  
  // Estadísticas
  nivel: number = 1;
  experiencia: number = 0;
  soldadosMaximos: number = 10;
  soldadosActuales: number = 10;
  ataque: number;
  defensa: number;
  movimientoMaximo: number;
  
  // Estado en batalla
  casillaX: number;
  casillaY: number;
  movimientoRestante: number;
}
5.2 Unidades del Nivel 1
Facción Lenca (Jugador)
Unidad	Cantidad	Ataque	Defensa	Movimiento	Notas
Lempira (Héroe)	1	6	5	4	Cura tropas adyacentes
Arquero Lenca	4	5	2	4	A distancia
Lancero Lenca	6	3	6	4	Defensa alta
Facción Española (IA)
Unidad	Cantidad	Ataque	Defensa	Movimiento	Notas
Capitán Diego	1	6	5	4	Líder enemigo
Soldado Español	10	4	4	4	Balanceado
Unidades Neutrales
Unidad	Cantidad	Ataque	Defensa	Movimiento	Comportamiento
Aldeano	3	0	1	2	Huye de enemigos
5.3 Inteligencia Artificial (IA)
Comportamiento por prioridad:

Atacar: Si unidad aliada está adyacente y puede eliminarla

Flanquear: Moverse hacia unidades débiles (arqueros)

Proteger: Unidades heridas se retiran hacia capitán

Objetivo: Aldeanos son objetivo secundario

6. Diseño de Niveles
6.1 Nivel 1: "El Primer Ataque"
Mapa: 30x30 celdas
Objetivos:

Victoria: Eliminar todas las unidades enemigas

Derrota: Lempira muere OR todos los aldeanos mueren

Disposición inicial:

Lenca: Posiciones defensivas alrededor de la aldea

Españoles: Acercándose desde el oeste

Aldeanos: Dispersos en la aldea (zona central)

Características del terreno:

Zona central: Claro (aldea)

Flancos: Selva y montañas

Río divide el mapa horizontalmente

7. Progresión y Guardado
7.1 Sistema de Experiencia
Experiencia compartida: Todas las unidades aliadas ganan XP cuando una elimina enemigos

Subida de nivel: +1 a Ataque o Defensa (aleatorio o elegible)

Objetivo nivel 1: Lempira debe llegar a nivel 3

7.2 Sistema de Guardado
Datos guardados por slot:

{
  "slot_numero": 1,
  "timestamp_guardado": "2024-01-01T10:30:00",
  "numero_nivel": 1,
  "unidades": [
    {
      "nombre": "Lempira",
      "nivel": 3,
      "experiencia": 150,
      "ataque": 7,
      "defensa": 6
    }
  ],
  "mapa_desbloqueado": [1, 2, 3]
}
7.3 Condiciones de Victoria/Derrota
Por nivel:

Victoria principal: Eliminar líder enemigo/tropas

Victoria secundaria: Objetivos opcionales (ej: proteger aldeanos)

Derrota: Muerte del héroe OR fallo de objetivo crítico

Próximas Expansiones Planeadas
Sistema de clases: Evolución de héroes (Lempira → Guerrero Jaguar → Señor de la Guerra)

Más tipos de unidades: Chamanes, unidades montadas, artillería

Elementos de mapa interactivos: Trampas, fuentes de curación

Modo multijugador: Batallas PvP asincrónicas

Documento actualizado para la versión prototipo 0.1
