<script setup>
import { ref, computed } from "vue";

const skills = ref([
  { id: 1, name: "Sentire Manae I", x: 300, y: 200, parent: null, title: "Sentire Manae I", description: "O feiticeiro possui a habilidade de perceber sua própria mana. Esse é o primeiro passo que todo aprendiz deve dominar para trilhar o caminho de um grande conjurador.", system: "-", hovered: false, offsetX: 0, offsetY: 0 },
  { id: 2, name: "Sentire Manae II", x: 350, y: 50, parent: 1, title: "Sentire Manae II", description: "O feiticeiro é capaz de detectar a mana de um indivíduo, caso ele a possua, permitindo assim identificar sua aproximação. Esse efeito ocorre automaticamente e sem custo.", system: "-", hovered: false, offsetX: 0, offsetY: 0 },
  { id: 3, name: "Conjurare Manae", x: 300, y: 320, parent: 1, title: "Conjurare Manae", description: "O feiticeiro é capaz de conjurar sua mana podendo utilizar para lançar essa energia em inimigos podendo causar dano.", system: "A cada 5 de mana gasto ocorrerá um aumento 1 de dano. Dano + 1D4.", hovered: false, offsetX: 0, offsetY: 0 },
  { id: 4, name: "Sphaera Manae", x: 150, y: 250, parent: 1, title: "Sphaera Manae", description: "O feiticeiro conjura em sua mão uma esfera de luz.", system: "A cada 2 de mana fica 1 hora ativa. Ilumina 3M².", hovered: false, offsetX: 0, offsetY: 0 },
  { id: 5, name: "Scutum Manae", x: 450, y: 250, parent: 1, title: "Scutum Manae", description: "O feiticeiro conjura um escudo ao seu redor capaz de absorver dano.", system: "A cada 1 de mana absorve 1 de dano.", hovered: false, offsetX: 0, offsetY: 0 },
]);

const lines = computed(() =>
  skills.value
    .filter((skill) => skill.parent !== null)
    .map((skill) => {
      const parent = skills.value.find((s) => s.id === skill.parent);
      return { x1: parent.x, y1: parent.y, x2: skill.x, y2: skill.y };
    })
);

const getTransform = (skill) => {
  return skill.hovered ? `translate(${skill.offsetX}px, ${skill.offsetY}px)` : `translate(0px, 0px)`;
};

const handleMouseEnter = (skill) => {
  skill.hovered = true;
  startSmoothTremor(skill);
};

const handleMouseLeave = (skill) => {
  skill.hovered = false;
  stopTremor(skill);
};

const intervals = new Map();

const startSmoothTremor = (skill) => {
  if (intervals.has(skill.id)) return;

  intervals.set(
    skill.id,
    setInterval(() => {
      skill.offsetX = (Math.random() - 0.5) * 2;
      skill.offsetY = (Math.random() - 0.5) * 2;
    }, 400)
  );
};

const stopTremor = (skill) => {
  if (intervals.has(skill.id)) {
    clearInterval(intervals.get(skill.id));
    intervals.delete(skill.id);
    skill.offsetX = 0;
    skill.offsetY = 0;
  }
};
</script>

<template>
  <div class="tree-container">
    <svg width="1200" height="400">
      <!-- Linhas conectando as habilidades -->
      <line
        v-for="(line, index) in lines"
        :key="'line-' + index"
        :x1="line.x1"
        :y1="line.y1"
        :x2="line.x2"
        :y2="line.y2"
        stroke="gray"
        stroke-width="2"
      />
      <!-- Nós das habilidades -->
      <g
        v-for="skill in skills"
        :key="skill.id"
        @mouseenter="handleMouseEnter(skill)"
        @mouseleave="handleMouseLeave(skill)"
      >
        <circle
          :cx="skill.x"
          :cy="skill.y"
          r="40"
          fill="lightblue"
          stroke="black"
          stroke-width="2"
          :style="{ transition: 'transform 0.5s ease', transform: getTransform(skill) }"
        />
        <text
          :x="skill.x"
          :y="skill.y"
          text-anchor="middle"
          dominant-baseline="middle"
          font-size="12"
        >
          {{ skill.name }}
        </text>
        
        <!-- Tooltip -->
        <foreignObject
          v-if="skill.hovered"
          :x="skill.x + 50"
          :y="skill.y - 80"
          width="220"
          :height="calculateTooltipHeight(skill)"
        >
          <div class="tooltip">
            <h3>{{ skill.title }}</h3>
            <p>{{ skill.description }}</p>
            <h3>Sistema</h3> 
            <p>{{ skill.system }}</p>
          </div>
        </foreignObject>
      </g>
    </svg>
  </div>
</template>

<style scoped>
.tree-container {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 20px;
}

.tooltip {
  background-color: rgba(0, 0, 0, 0.7);
  color: white;
  padding: 10px;
  border-radius: 5px;
  font-family: Arial, sans-serif;
  max-width: 180px;
  position: relative;
  z-index: 3;
}

.tooltip h3 {
  margin: 0;
  font-size: 14px;
  font-weight: bold;
}

.tooltip p {
  font-size: 12px;
}

.tooltip small {
  font-size: 10px;
  display: block;
  margin-top: 5px;
}
</style>

<script>
const calculateTooltipHeight = (skill) => {
  const baseHeight = 50; // Altura mínima
  const titleHeight = 20; // Altura do título
  const descriptionHeight = skill.description ? Math.ceil(skill.description.length / 40) * 15 : 0; // Calcula a altura extra para a descrição
  const systemHeight = skill.system ? 20 : 0; // Altura para o sistema (se houver)
  return baseHeight + titleHeight + descriptionHeight + systemHeight; // Retorna altura total
};
</script>
