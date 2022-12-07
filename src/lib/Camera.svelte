<script lang="ts">
  export let pic: string
  let preview: string

  let mediaStreamPromise: Promise<MediaStream>
  let video: HTMLVideoElement
  let canvas: HTMLCanvasElement
  let imgSize = 400

  const enableCamera = () => {
    mediaStreamPromise = navigator.mediaDevices.getUserMedia({
      video: true,
    })
  }

  const srcObject = (video: HTMLVideoElement, stream: MediaStream) => {
    video.srcObject = stream
    return {
      update(newStream: MediaStream) {
        if (video.srcObject !== newStream) video.srcObject = newStream
      },
      destroy() {},
    }
  }

  const takePicture = () => {
    if (!canvas) return
    const context = canvas.getContext('2d')!

    const min = Math.min(video.videoWidth, video.videoHeight)
    let width = (video.videoWidth / min) * imgSize
    let height = (video.videoHeight / min) * imgSize
    let x = (imgSize - width) / 2
    let y = (imgSize - height) / 2
    // context.clearRect(0, 0, imgSize, imgSize)
    // context.clearRect(0, 0, canvas.width, canvas.height)
    context.scale(-1, 1)
    context.drawImage(video, -x, y, -width, height)
    context.resetTransform()
    preview = canvas.toDataURL('image/png')
  }
</script>

{#if preview}
  <img src={preview} alt="" />
  <button
    on:click={() => {
      preview = ''
    }}>Retake</button
  >
  <button
    on:click={() => {
      pic = preview
    }}>Save</button
  >
{:else}
  {#await mediaStreamPromise}
    Requesting permission...
  {:then mediaStream}
    {#if mediaStream}
      <div class="video-square">
        <!-- svelte-ignore a11y-media-has-caption -->
        <video
          bind:this={video}
          use:srcObject={mediaStream}
          autoplay
          playsinline
        />
      </div>
      <canvas bind:this={canvas} width={imgSize} height={imgSize} />
      <button on:click|preventDefault={takePicture}>📷</button>
    {:else}
      <button on:click={enableCamera}>Take a picture!</button>
    {/if}
  {:catch error}
    Enable your camera to add a picture!
  {/await}
{/if}

<style>
  .video-square {
    display: grid;
    width: 400px;
    max-width: 100%;
    aspect-ratio: 1 / 1;
  }
  video {
    width: 100%;
    height: 100%;
    object-fit: cover;
    scale: -1 1;
  }
  canvas {
    display: none;
  }
</style>
