<script lang="ts">
  import { onMount, onDestroy } from 'svelte';
  import LightBox from './lib/LightBox.svelte';

  type ImageType = {
    id: number;
    src: string;
    alt: string;
  };
  
  let selectedImage: ImageType | null = null;
  let images: { src: string, alt: string, id: number }[] = [];
  
  const selectImage = (image: typeof images[0]) => {
    selectedImage = image;
  };

  const closeLightbox = () => {
    selectedImage = null;
  };


  let isLightboxOpen = false;


  
  onMount(async () => {
    const imageModules = import.meta.glob('/src/lib/assets/*.jpg');    
    images = await Promise.all(
      Object.entries(imageModules).map(async ([path], index) => {
        const module = await imageModules[path]();
        return {
          src: module.default,
          alt: `Description ${index + 1}`,
          id: index + 1,
        };
      })
    );

  });


const handleKeyDown = (event: KeyboardEvent) => {
  if (event.key === 'Escape') {
    closeLightbox();
  }
};

// Watch for changes in selectedImage and add/remove event listener accordingly
$: if (selectedImage) {
  isLightboxOpen = true;
  window.addEventListener('keydown', handleKeyDown);
} else {
  isLightboxOpen = false;
  window.removeEventListener('keydown', handleKeyDown);
}

  onDestroy(() => {
  // Cleanup the event listener if it's still set when the component is destroyed
  if (isLightboxOpen) {
    window.removeEventListener('keydown', handleKeyDown);
  }
});
</script>

<main>
  <h1 class="title">Lynda's Brush</h1>

  <div class="gallery">
    {#each images as image}
    <div 
      class="image-container" 
      on:click={() => selectImage(image)}
      on:keydown={(event) => handleKeyDown(event)}
      tabindex="0" 
      role="button" 
      aria-label={image.alt}
    >
    <img class="img" src={image.src} alt={image.alt} />

    </div>
  {/each}
  </div>
  {#if selectedImage}
  <LightBox {...selectedImage} show={true} close={closeLightbox} />

  {/if}
</main>

<style>
  main {
    text-align: center;
  }

  .title {
    font-size: 6rem;
    font-weight: 700;
    margin-bottom: 2rem;

    @media (max-width: 768px) {
      font-size: 3rem;
    }
  }
  .gallery {
    display: flex;
    flex-wrap: wrap;
    gap: 6px; 
    justify-content: center;

    @media (max-width: 768px) {
      gap: 3px; 
    }
  }

  .image-container {
    max-width: 120px; 
    height: 160px;
    border-radius: 6px;
    @media (max-width: 768px) {
      max-width: 60px;
      height: 80px;
    }
  }

  .img {
    width: 120px;
    height: 160px;
    border-radius: 6px;
    object-fit: cover;
    cursor: pointer;
    transition: transform 0.4s ease-in-out; 

    @media (max-width: 768px) {
      width: 60px; 
      height: 80px;
    }
    &:hover {
      transform: translate(10px, -10px); 
      box-shadow: 
       -10px 10px 5px 0px rgba(0, 0, 0, 0.3),  /* left border with blur */
        0px 10px 5px 0px rgba(0, 0, 0, 0.3);  /* bottom border with blur */
    }
  }

  
  @media only screen and (max-width: 1000px) {
    .image-container {
      flex-basis: calc(50% - 16px); 
    }
  }

  @media only screen and (max-width: 600px) {
    .image-container {
      flex-basis: 100%; 
    }
  }
</style>
