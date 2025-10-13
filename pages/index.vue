<template>
  <div class="container">
    <header>
      <h1>ERIK PARR</h1>
    </header>
    
    <Statement 
      :isExpanded="activeSection === 'statement'"
      @expand="toggleSection('statement')"
      @collapse="toggleSection('portfolio')"
      previewText="About The Studio"
    />
    
    <Portfolio 
      :isCollapsed="activeSection === 'statement'"
      @collapse="toggleSection('portfolio')"
      previewText="View Portfolio"
    />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import Statement from '~/components/Statement.vue';
import Portfolio from '~/components/Portfolio.vue';

const activeSection = ref('portfolio');

const toggleSection = (section: string) => {
  activeSection.value = section;
};
</script>

<style>
:root {
  --max-width: 1200px;
  --padding: 2rem;
}

body {
  font-family: 'Nunito Sans', sans-serif;
  line-height: 1.6;
}

.container {
  max-width: var(--max-width);
  margin: 0 auto;
  padding: var(--padding);
}

header {
  text-align: center;
  margin-bottom: 4rem;
}

h1 {
  font-size: 5rem;
  font-weight: 100;
  color: var(--color-text);
}

@media (max-width: 768px) {
  :root {
    --padding: 1rem;
  }

  h1 {
    font-size: 2rem;
  }
}
</style>