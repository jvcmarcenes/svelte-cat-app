<script>
  import axios from 'axios'
  import { catKey, dogKey } from './keys'

	let doggo = false
  $: tag = doggo ? 'Doggo' : 'Yako'
  $: icon = doggo ? ' fa-dog' : ' fa-cat'
  $: axios.defaults.headers.common['x-api-key'] = doggo ? dogKey : catKey
  $: api_url = `https://api.the${doggo ? 'dog' : 'cat'}api.com/v1/images/search`
  
  const requestParams = { limit: 1, size: 'full' }
  let req

  function fetchImage() {
    req = axios.get(api_url, { requestParams })
      .then(res => res.data[0])
      .catch(err => console.log(err))
  }

  $: fetchImage() && doggo
</script>

<svelte.head>
  <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.15.2/css/all.css">
</svelte.head>

<div id="app">
  <header>
    <h1> {tag} <i class={'fas' + icon}/> </h1>  
  </header>

  <main class="container">
    <figure class="image-container">
      {#await req} <div class="loader" />
      {:then img} <img src={img.url} alt="" id="image" class={img.width > img.height ? 'wide' : 'long'}>
      {/await}
    </figure>

    <section class="controls">
      <button class="btn" on:click={fetchImage}> Random {tag} <i class="fas fa-sync-alt"/> </button>

      <div class="doggo-toggle">
        <i class="icon fas fa-cat" class:active={!doggo} on:click={() => doggo = false}/>
        <label class="switch">
          <input type="checkbox" bind:checked={doggo}/> 
          <span class="slider"/>
        </label>
        <i class="icon fas fa-dog" class:active={doggo} on:click={() => doggo = true}/>
      </div>

      <footer class="credits">
        <span class="credits-author">An app by Joao Vitor Costa Marcenes</span>
        <span class="credits-images">Images by Aden Forshaw / theCatApi</span>
      </footer>
    </section>
  </main>

</div>

<style lang="scss">
  $primary-color: rgb(73, 126, 175);
  $off-white: #f2f2f2;
  $white: white;

  #app {
    position: relative;
    font-family: Arial, Helvetica, sans-serif;

    //min-height: 100vh;
    height: auto;
  }

  @keyframes spin {
    0% {
      transform: rotate(0deg);
    }
    100% {
      transform: rotate(360deg);
    }
  }

  .loader {
    width: 120px;
    height: 120px;

    border: 16px #444 solid;
    border-top: 16px #e2e2e2 solid;
    border-radius: 50%;

    animation: spin 2s ease-in-out infinite;
  }

  header {
    padding: 20px;

    //background: $primary-color;
    //color: #f2f2f2;
    background: #f2f2f2;
    color: $primary-color;

    box-shadow: 0px 0px 8px 2px #555;

    text-align: center;

    font-size: 2rem;
  }

  .container {
    padding-top: 30px;
    
    margin: auto;

    height: auto;
    width: 80vw;
  }

  .image-container {
    display: flex;

    margin-bottom: 10px;

    width: 80vw;
    height: 80vw;

    max-width: 28rem;
    max-height: 28rem;

    background: black;

    box-shadow: 0px 0px 8px 2px #555;

    justify-content: center;
    align-items: center;

    >img {
      flex-shrink: 0;

      margin: 0px;

      max-width: 100%;
      max-height: 100%;

      background: #f2f2f2;

      &.wide {
        width: 100%;
      }

      &.tall {
        height: 100%
      }
    }
  }

  .btn {
    padding: 10px;

    margin-top: 15px;

    height: 40px;
    width: 100%;

    background: #f2f2f2;
    color: $primary-color;

    border-radius: 5px;

    border: none;
    outline: none;

    box-shadow: 0px 0px 8px 1px #555;

    font-size: 1rem;
    font-weight: bold;

    cursor: pointer;

    transition: .4s;

    &:hover {
      box-shadow: 0px 0px 6px 0px #555
    }

    >i {
      margin-left: 10px;
    }
  }

  .doggo-toggle {
    display: flex;

    flex-direction: row;

    justify-content: center;
    align-items: center;

    width: 80%;

    padding: 10px;

    margin: auto;

    margin-top: 15px;
    padding: auto;

    >.icon {
      font-size: 4rem;

      padding: 5px;

      color: #ccc;

      cursor: pointer;

      transition: .4s;

      &.active {
        color: $primary-color
      }
    }

    >.switch {
      position: relative;
      display: inline-block;

      margin: 0px 10px 0px 10px;

      width: 90px;
      height: 51px;

      >input {
        opacity: 0;

        width: 0;
        height: 0;

        &:checked {
          + .slider::before {
            -webkit-transform: translateX(36px);
            -ms-transform: translateX(36px);
            transform: translateX(36px);
          }
        }
      }

      >.slider {
        position: absolute;

        top: 0;
        left: 0;
        right: 0;
        bottom: 0;

        background-color: #ccc;

        border-radius: 51px;

        cursor: pointer;

        -webkit-transition: .4s;
        transition: .4s;

        &::before {
          position: absolute;

          content: "";

          height: 43px;
          width: 43px;

          left: 4px;
          bottom: 4px;

          background-color: $primary-color;

          border-radius: 50%;

          -webkit-transition: .4s;
          transition: .4s;
        }
      }
    }
  }

  .credits {
    padding-top: 25px;

    height: 5rem;
    width: 100%;

    color: $primary-color;

    font-size: 1.1em;
    font-weight: 800;

    >span {
      display: block;

      text-align: center;
      line-height: 1.4rem;
    }
  }

  @media (min-width: 760px) {
    .container {
      display: flex;
      flex-direction: row;

      flex-shrink: 0;

      justify-content: center;
      align-items: center;

      width: 90%;
    }

    .image-container {
      display: inline-flex;

      height: 300px;
      width: 300px;
    }

    .controls {
      display: inline-block;

      //position: absolute;

      margin: 20px;

      min-width: 330px;
    }

    .credits {
      font-weight: 600;
    }
  }

  @media (min-width: 920px) {
    .image-container {
      height: 500px;
      width: 500px;
    }

    .controls {
      width: 400px;
    }
  }
</style>