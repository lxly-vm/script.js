// Semestres y cursos
const semestres = {
  1: ["Introducción al cálculo", "Introducción al álgebra", "Introducción a la física", "Taller de comunicación y redacción de informes", "Introducción a la ingeniería"],
  2: ["Cálculo I", "Álgebra I", "Química inorgánica y orgánica", "Interpretación gráfica para la ingeniería", "Taller de Ingeniería"],
  3: ["Cálculo II", "Álgebra II", "Mecánica clásica", "Programación y algoritmo", "Gestión de empresas"],
  4: ["Cálculo III", "Ecuaciones diferenciales", "Ingeniería de materiales", "Termodinámica", "Contabilidad y costos"],
  5: ["Electromagnetismo", "Estadística y probabilidad", "Operaciones unitarias", "Mecánica de fluidos", "Microeconomía"],
  6: ["Formación cultural", "Investigación operativa", "Ingeniería económica", "Procesos industriales y manufactura", "Macroeconomía", "Taller de integración de conocimientos"],
  7: ["Gestión de operaciones", "Finanzas", "Marketing", "Comportamiento organizacional y capital humano", "Estadística para la ingeniería"],
  8: ["Sistema de información administrativa", "Preparación y evaluación de proyectos de ingeniería industrial", "Sistema de gestión y aseguramiento de la calidad", "Modelos estocásticos", "Econometría", "Primera práctica profesional"],
  9: ["Inglés comunicacional pre intermedio I", "Dirección estratégica", "Gestión de proyectos", "Gestión de operaciones II", "Logística"],
  10: ["Inglés comunicacional pre intermedio II", "EFP I", "EFP II", "EFP III", "EFP IV", "Segunda práctica profesional"],
  11: ["Electivo seminario"],
  12: ["Actividad de titulación"]
};

// Prerrequisitos
const cursos = {
  "Introducción al cálculo": {
    desbloquea: ["Cálculo I", "Interpretación gráfica para la ingeniería", "Formación cultural", "Taller de integración de conocimientos", "Primera práctica profesional", "EFP I", "EFP II", "EFP III", "EFP IV", "Segunda práctica profesional", "Electivo seminario"]
  },
  "Introducción al álgebra": {
    desbloquea: ["Álgebra I", "Formación cultural", "Taller de integración de conocimientos", "Primera práctica profesional", "EFP I", "EFP II", "EFP III", "EFP IV", "Segunda práctica profesional", "Electivo seminario"]
  },
  "Introducción a la física": {
    desbloquea: ["Química inorgánica y orgánica", "Interpretación gráfica para la ingeniería", "Mecánica clásica", "Formación cultural", "Taller de integración de conocimientos", "Primera práctica profesional", "EFP I", "EFP II", "EFP III", "EFP IV", "Segunda práctica profesional", "Electivo seminario"]
  },
  "Taller de comunicación y redacción de informes": {
    desbloquea: ["Taller de Ingeniería", "Formación cultural", "Taller de integración de conocimientos", "Primera práctica profesional", "EFP I", "EFP II", "EFP III", "EFP IV", "Segunda práctica profesional", "Electivo seminario"]
  },
  "Introducción a la ingeniería": {
    desbloquea: ["Taller de Ingeniería", "Formación cultural", "Taller de integración de conocimientos", "Primera práctica profesional", "EFP I", "EFP II", "EFP III", "EFP IV", "Segunda práctica profesional", "Electivo seminario"]
  },
  // Puedes continuar agregando aquí todos los demás cursos con su lista de desbloqueados...
};

// Estado inicial
const estado = {};
Object.keys(cursos).forEach(curso => estado[curso] = false);

// Crear los semestres
const contenedor = document.getElementById("malla");

Object.entries(semestres).forEach(([num, lista]) => {
  const divSemestre = document.createElement("div");
  divSemestre.className = "semestre";

  const titulo = document.createElement("h2");
  titulo.textContent = `Semestre ${num}`;
  divSemestre.appendChild(titulo);

  const divCursos = document.createElement("div");
  divCursos.className = "cursos";

  lista.forEach(nombre => {
    const div = document.createElement("div");
    div.className = "curso";
    div.id = nombre;
    div.innerText = nombre;
    divCursos.appendChild(div);
  });

  divSemestre.appendChild(divCursos);
  contenedor.appendChild(divSemestre);
});

// Habilitar cursos sin requisitos explícitos
function habilitarIniciales() {
  Object.keys(cursos).forEach(nombre => {
    const curso = cursos[nombre];
    if (!curso.requisitos || curso.requisitos.length === 0) {
      const div = document.getElementById(nombre);
      if (div) div.classList.add("habilitado");
    }
  });
}

// Marcar un curso como aprobado
function aprobar(nombre) {
  if (estado[nombre]) return;

  const div = document.getElementById(nombre);
  if (!div || !div.classList.contains("habilitado")) return;

  div.classList.remove("habilitado");
  div.classList.add("aprobado");
  estado[nombre] = true;

  const desbloquear = cursos[nombre]?.desbloquea || [];
  desbloquear.forEach(d => {
    const requisitos = Object.entries(cursos).filter(([k, v]) =>
      (v.desbloquea || []).includes(d)
    ).map(([k]) => k);

    const todosAprobados = requisitos.every(r => estado[r]);
    const divDestino = document.getElementById(d);
    if (divDestino && !estado[d] && todosAprobados) {
      divDestino.classList.add("habilitado");
    }
  });
}

// Clic para aprobar
document.getElementById("malla").addEventListener("click", e => {
  if (e.target.classList.contains("curso")) {
    aprobar(e.target.id);
  }
});

// Inicializar
habilitarIniciales();
