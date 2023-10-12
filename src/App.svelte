<script lang="ts">
  type ImageType = {
    id: number;
    src: string;
    alt: string;
  };
  let selectedImage: ImageType | null = null;
  let images: { src: string, alt: string, id: number }[] = [];
  import LightBox from './lib/LightBox.svelte';
  import { onMount, onDestroy } from 'svelte';


  

  onMount(async () => {
    const imageModules = import.meta.glob('/src/assets/*.jpg');
    images = Object.keys(imageModules).map((path, index) => ({
      src: path,
      alt: `Description ${index + 1}`,
      id: index + 1,
    }));
  });

  const selectImage = (image: typeof images[0]) => {
    selectedImage = image;
  };

  const closeLightbox = () => {
    selectedImage = null;
  };


  let isLightboxOpen = false;

// Function to handle keydown event
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
  <h1>Image Gallery</h1>
  <div class="gallery">
    {#each images as image}
    <div 
      class="image-container" 
      on:click={() => selectImage(image)}
      on:keydown={(event) => handleKeyDown(event, image)}
      tabindex="0" 
      role="button" 
      aria-label={image.alt}
    >
    <img src={image.src} alt={image.alt} />

    </div>
  {/each}
  </div>
  {#if selectedImage}
  <Lightbox {selectedImage} show={true} {closeLightbox} />
  {/if}
</main>

<style>
  main {
    text-align: center;
  }
  .gallery {
    display: flex;
    flex-wrap: wrap;
    gap: 2px; 
    justify-content: center;
  }

  .image-container {
    flex: 1 1 auto;
  }

  img {
    width: 100px; /* Ensure the image doesn't overflow its container */
    border-radius: 6px;
    object-fit: cover; /* To ensure images maintain aspect and don’t stretch */
  }

  /* Optional: Responsive break points */
  @media only screen and (max-width: 1000px) {
    .image-container {
      flex-basis: calc(50% - 16px); /* Two images per row on medium screens */
    }
  }

  @media only screen and (max-width: 600px) {
    .image-container {
      flex-basis: 100%; /* One image per row on small screens */
    }
  }
</style>
