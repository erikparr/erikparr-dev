<template>
  <section 
    class="portfolio" 
    :class="{ 'collapsed': isCollapsed }"
    @click="toggleCollapse"
  >
    <div class="portfolio-content">
      <div v-if="isCollapsed" class="portfolio-preview">
        {{ previewText }}
      </div>
      <template v-else>
        <div class="category-filter">
          <button 
            v-for="category in uniqueCategories" 
            :key="category" 
            @click.stop="setActiveCategory(category)"
            :class="{ active: activeCategory === category }"
          >
            {{ category }}
          </button>
        </div>

        <AIMLLab v-if="showAIMLLab" />
        <Project 
          v-for="project in filteredProjects" 
          :key="project.id" 
          v-bind="project"
        />
      </template>
    </div>
  </section>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import Project from './Project.vue';
import AIMLLab from './AIMLLab.vue';
import { projects, categories, type Project as ProjectType } from '~/data/projects';

const props = defineProps<{
  isCollapsed: boolean,
  previewText: string
}>();

const emit = defineEmits(['collapse']);

const toggleCollapse = () => {
  emit('collapse');
};

const activeCategory = ref('All');

const uniqueCategories = computed(() => {
  const categories = new Set(projects.flatMap(project => project.category.split(', ')));
  return ['All', ...Array.from(categories).sort()];
});

const filteredProjects = computed(() => {
  if (activeCategory.value === 'All') {
    return projects.filter(project => project.defaultVisible);
  }
  return projects.filter(project => project.category.includes(activeCategory.value));
});

const showAIMLLab = computed(() => {
  return activeCategory.value === 'AI';
});

const setActiveCategory = (category: string) => {
  activeCategory.value = category;
};
</script>   

<style scoped>
.portfolio {
  width: 100%;
  transition: all 0.5s ease;
  max-height: 100%;
  opacity: 1;
}

.portfolio.collapsed {
  padding: 8px 16px;
  background-color: var(--color-surface);
  border-radius: 20px;
  cursor: pointer;
  border: 1px solid transparent;
  max-height: 50px;
  opacity: 1;
}

.portfolio.collapsed:hover {
  background-color: var(--color-surface-hover);
  border: 1px solid var(--color-primary);
}

.portfolio-preview {
  text-align: center;
  font-size: 1rem;
  color: var(--color-text);
}

.category-filter {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  margin-bottom: 2rem;
  justify-content: center;
}

.category-filter button {
  padding: 8px 16px;
  background-color: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: 20px;
  cursor: pointer;
  transition: background-color 0.3s ease;
  color: var(--color-text);
}

.category-filter button.active {
  background-color: var(--color-primary);
  color: #000;
}

.category-filter button:hover {
  background-color: var(--color-surface-hover);
}

.category-filter button.active:hover {
  background-color: var(--color-primary);
}

@media (max-width: 768px) {
  .category-filter {
    gap: 8px;
  }
  
  .category-filter button {
    padding: 6px 12px;
    font-size: 0.9rem;
  }
}

.portfolio-content {
  transition: all 0.5s ease;
  opacity: 1;
}

.portfolio.collapsed .portfolio-content {
  opacity: 1;
}
</style>
