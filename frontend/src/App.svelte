<script>
  import Register from "./Register.svelte";
  import Login from "./Login.svelte";
  import Router from "svelte-spa-router";
  import EspData from "./EspData.svelte";
  import DeviceData from "./DeviceData.svelte";
  import AddEsp from "./AddEsp.svelte";
  import RaumData from "./RaumData.svelte";
  import { isLoggedIn } from "./store";
  import { push } from "svelte-spa-router";
  import IoIosAddCircleOutline from 'svelte-icons/io/IoIosAddCircleOutline.svelte'
  import MdDeviceHub from 'svelte-icons/md/MdDeviceHub.svelte'
  import MdPictureInPicture from 'svelte-icons/md/MdPictureInPicture.svelte'
  import IoMdLogOut from 'svelte-icons/io/IoMdLogOut.svelte'
  //import IoMdPerson from 'svelte-icons/io/IoMdPerson.svelte'

  const routes = {
    "/": Login,
    "/login": Login,
    "/register": Register,
    "/data": EspData,
    "/addesp": AddEsp,
    "/devicedata/:mac": DeviceData,
    "/raumdata": RaumData,
  };

  function logoutUser() {
    isLoggedIn.set(false);
    localStorage.clear();
    push("/login").then(() => {
      window.location.reload();
    });
  }
</script>

<header>
  <!-- Flex container for logo and logout -->
  <div class="header-content">
    <!-- SVG-Logo oben links -->
    <div class="logo">
      <img src="/images/LogoNew.svg" alt="Aeroom Logo">
      <img class="aeroom" src="/images/aeroom4.svg" alt="Aeroom">
    </div>

    <button class="user-logout" on:click={logoutUser} style="background: transparent; border: none;">
      <!-- <IoMdLogOut class="logout-icon"/> -->
      <span class="header-text">Logout</span>
    </button>
  </div>
</header>

<footer>
  {#if $isLoggedIn}
    <a href="#/raumdata" class="footer-link">
      <MdPictureInPicture style="width: 24px; height: 24px;"/>
      <span class="footer-text">Rooms</span>
    </a>
    <a href="#/data" class="footer-link">
      <MdDeviceHub style="width: 24px; height: 24px;"/>
      <span class="footer-text">Geräte</span>
    </a>
    <a href="#/addesp" class="footer-link">
      <IoIosAddCircleOutline style="width: 24px; height: 24px;"/>
      <span class="footer-text">Hinzufügen</span>
    </a>
    
  {:else}
    <a href="#/login" style="color: white;">Login</a>
    <a href="#/register" style="color: white;">Register</a>
  {/if}
</footer>
<main>
  <Router {routes} />
</main>

<style>
  .header-content {
    display: flex;
    justify-content:space-between;
    align-items: center;
    padding: 4px 0px;
    width: 100%;
  }

  .aeroom {
    margin-right: 300px;
    margin-top: 4px;
    height: 80%;
  }

  .logo {
    margin-right: -50px;
  }

  .logo img {
    width: 50px;
    height: 50px;
    margin-right: 10px; /* Adjusted */
  }

  .logo img {
    width: 50px;
    height: 50px;
    margin-right: 10px; /* Adjusted */
  }

  .user-logout {
    display: flex;
    align-items: center;
    text-decoration: none;
    color: rgb(202, 202, 202);
  }

  /* .logout-icon {
    display: flex;
    align-items: center;
    margin-right: 5px;
    color: rgb(202, 202, 202);
  }

  // .logout-icon :global(svg) {
    width: 18px;
    height: 18px;
  } */

  .header-text {
    margin-right: 46px;
  }

  .user-logout:hover {
    text-decoration: none;
    color: #ffffff;
  }

  header {
    background-color: rgba(1, 187, 147, 0.95); /* 95% Transparenz */
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 4px 20px;
    text-align: center;
    position: fixed;
    top: 0;
    width: 100%;
    height: 8%;
    z-index: 2;
    backdrop-filter: blur(10px); /* Hintergrund-Blur mit 10 Pixel */
  }

  footer {
    border-top-right-radius: 10px;
    border-top-left-radius: 10px;
    display: flex;
    justify-content: space-around;
    background-color: rgba(1, 187, 147, 0.95); /* 95% Transparenz */
    padding: 10px 0;
    text-align: center;
    position: fixed;
    bottom: 0;
    width: 100%;
    height: 8%;
    z-index: 2;
    backdrop-filter: blur(10px); /* Hintergrund-Blur mit 10 Pixel */
  }

  .footer-link {
    text-decoration: none;
    display: flex;
    flex-direction: column;
    align-items: center;
    color: rgb(202, 202, 202);
  }

  .footer-link span {
    font-size: 12px;
    margin-top: 2px;
  }

  footer a:hover {
    color: #ffffff;
    text-decoration: none;
  }

  .footer-text {
    font-size: 12px;
    margin-top: 2px;
    margin-right: 0;
    font-weight: lighter; /* Startwert */
  }

  .footer-link:hover .footer-text {
    font-weight: normal; /* Ändern Sie dies nach Bedarf */
  }

  main {
    padding: 20px;
    text-align: center;
    margin-bottom: 60px; /* Platz für den Footer */
  }

  :global(html),
  :global(body) {
    margin: 0;
    padding: 0;
    height: 100%;
    font-family: Arial, sans-serif;
    background:#a9e6d9
  }
</style>
