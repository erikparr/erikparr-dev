<template>
    <section class="project">
      <h2 class="project-title">AI / ML Lab</h2>
      <div class="project-content">
        <div class="project-image">
        <img src="~/assets/images/ai-ml-lab.png" alt="AI / ML Lab" />
        <p class="image-caption">Native iOS app demonstrating ml-core for in-device AI generation for 3D textures</p>
      </div>
        <div class="project-description">
          <p>
            AI and ML is quickly changing the landscape of digital experiences. To keep up with the latest research and tools, AI / ML Lab is a living sandbox that tracks my research and r&d efforts, including in depth analysis of new software, tools, and workflows.
          </p>
          <NuxtLink to="/ai-ml-lab" class="cta-link">
      Launch Site
    </NuxtLink>
        </div>
      </div>
    </section>
  </template>
  
  <script setup>
  // Component logic can be added here if needed
  </script>
  
  <style scoped>
  .project {
    margin-bottom: calc(var(--spacing-unit) * 6);
    max-width: 800px;
    margin-left: auto;
    margin-right: auto;
    font-family: 'Nunito', sans-serif;
  }
  
  .project-title {
    font-size: 2rem;
    font-weight: 700;
    margin-bottom: calc(var(--spacing-unit) * 2);
    color: var(--color-text);
    font-family: 'Nunito', sans-serif;
    font-weight: 200;
  }
  
  .project-content {
    display: flex;
    flex-direction: column;
    gap: 2rem;
  }
  
  .project-image {
    position: relative;
  }
  
  .project-image img {
    width: 100%;
    height: auto;
    object-fit: cover;
  }
  
  .image-caption {
    margin-top: 0.5rem;
    font-size: 0.9rem;
    color: #666;
    text-align: center;
    font-style: italic;
  }
  
  .project-description {
    font-size: 1rem;
    line-height: 1.6;
    color: #444;
  }
  
  .cta-container {
    margin-top: 2rem;
    text-align: center;
  }
  
  .cta-link {
    display: inline-block;
    color: #000000; /* Black text */
    text-decoration: none;
    font-size: 1rem;
    font-weight: 600;
    transition: color 0.3s ease;
  }
  
  .cta-link:hover {
    color: #00008B; /* Dark blue hover state */
  }
  </style>