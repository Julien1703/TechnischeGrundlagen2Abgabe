<script>
  import axios from "axios";
  import { push } from "svelte-spa-router";
  import { isLoggedIn, token, username } from './store';
  import IoIosEye from 'svelte-icons/io/IoIosEye.svelte';
  import IoIosEyeOff from 'svelte-icons/io/IoIosEyeOff.svelte';

  let usernameInput = "";
  let password = "";
  let showPassword = false;
  let passwordInput; // Referenz zum Input-Element

  async function loginUser() {
    try {
      const response = await axios.post("http://localhost:3001/login", { username: usernameInput, password });
      console.log("Login erfolgreich:", response);
      token.set(response.data.token);
      isLoggedIn.set(true);
      username.update(prev => usernameInput);
      push("/raumdata");
    } catch (error) {
      console.error("Fehler beim Login:", error);
    }
  }

  function handlePasswordIconKeyDown(event) {
    if (event.key === 'Enter') {
      togglePasswordVisibility();
    }
  }

  function togglePasswordVisibility() {
    showPassword = !showPassword;
    
    if (passwordInput) {
      // Setze den Cursor ans Ende des Textes im Input-Feld
      requestAnimationFrame(() => {
        passwordInput.focus();
        const length = passwordInput.value.length;
        passwordInput.setSelectionRange(length, length);
      });
    }
  }

  axios.interceptors.request.use((config) => {
    config.headers.authorization = `Bearer ${$token}`;
    return config;
  }, (error) => {
    return Promise.reject(error);
  });

</script>

<div class="container">
  {#if !$isLoggedIn}
    <form on:submit|preventDefault={loginUser}>
      <h1>Login</h1>

      <div>
        <input id="username" placeholder="Benutzername" type="text" bind:value={usernameInput} />
      </div>
      <div>
        <input id="password" placeholder="Password" type="password" bind:value={password} />
      </div>
      <button type="submit">Einloggen</button>
    </form>
  {:else}
    <!-- Hier können zusätzliche Inhalte für eingeloggte Benutzer hinzugefügt werden -->
    <!-- <AddEsp {$username} /> -->
    <!-- <button on:click={logoutUser}>Ausloggen</button> -->
  {/if}
</div>


<style>
  .container {
    display: flex;
    justify-content: center;
    align-items: center; /* Zentriert den Inhalt vertikal */
    background-color: #f4f4f400;
    padding: 0px; /* Verhindert, dass der Inhalt direkt am Rand des Bildschirms ist */
    box-sizing: border-box;
  }
  
  form {
    width: 400px;
    margin-top: 90px;
    padding: 40px;
    border: none;
    border-radius: 15px;
    background: linear-gradient(to bottom, #00cab9 0%, #00cab9 26%, #ffffff 26%, #ffffff);
    box-shadow: 0 4px 8px rgba(152, 152, 152, 0.1);
  }

  h1 {
    color: #ffffff;
  }

  /* .password-container {
    display: flex;
    position: relative;
  } */
  
    input {
    width: calc(100% - 0px);
    box-sizing: border-box;
    padding: 12px;
    margin-bottom: 15px;
    border: 1px solid #bbb;
    border-radius: 4px;
    font-size: 16px;
    &:focus {
      border-color: #007bff;
      outline: none;
    }

    /* Neue Regel für den Placeholder im Fokus */
    &::placeholder {
      transition: transform 0.3s ease-out;
    }

    &:focus::placeholder {
      transform: translateY(-16px);
      font-size: 11px;
    }
  }

  /* .show-password {
    position: absolute;
    top: 50%;
    right: 0;
    transform: translateY(-50%);
    cursor: pointer;
    width: 20px;
    text-align: center;
  } */
  
    button {
    width: 100%; /* Breite des Buttons und der Inputfelder gleich setzen */
    box-sizing: border-box;
      padding: 14px; /* Größerer Innenabstand */
      margin-top: 20px;
      margin-bottom: 15px; /* Abstand zwischen den Feldern */
      border: none;
      background-color: #007bff; /* Blaue Farbe, passend zum App-Layout */
      background: linear-gradient(to bottom, #00cab9 ,#00b1a2);
      color: white;
      border-radius: 10px;
      cursor: pointer;
      font-size: 16px; /* Größere Schrift */
    }
  
    button:hover {
      background-color: #0056b3;
    }
    :global(h1){
      text-align: center;
      margin-top: 1px;
      margin-bottom: 40px;
    }
  </style>
