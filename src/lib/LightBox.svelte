<script lang="ts">
  export let src: string;
  export let alt: string;
  export let show: boolean;
  export let close: () => void;
</script>

{#if show}
  <div class="lightbox" on:click={close}>
    <img {src} {alt} />
  </div>
{/if}

<style>
  .lightbox {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.8);
    display: flex;
    align-items: center;
    justify-content: center;
  }
  img {
    max-width: 80%;
    max-height: 80%;
  }
</style>
