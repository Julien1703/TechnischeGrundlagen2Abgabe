<script>
  import axios from "axios";
  import { push } from "svelte-spa-router";
  import { isLoggedIn, token } from './store';
  import IoIosEye from 'svelte-icons/io/IoIosEye.svelte';
  import IoIosEyeOff from 'svelte-icons/io/IoIosEyeOff.svelte';

  let username = "";
  let password = "";
  let showPassword = false;
  let passwordInput; // Referenz zum Input-Element

  async function loginUser() {
    try {
      const response = await axios.post("http://localhost:3001/login", { username, password });
      console.log("Login erfolgreich:", response);
      token.set(response.data.token);
      isLoggedIn.set(true);
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
        <input id="username" bind:value={username} placeholder="Benutzername" />
      </div>
      <div class="password-container">
        <input id="password"
          bind:this={passwordInput}
          value={password}
          type={showPassword ? 'text' : 'password'}
          placeholder="Passwort"
          on:blur|preventDefault={() => {}}
          on:input={() => { password = passwordInput.value; }}
        />
        <div
          class="show-password"
          on:click={togglePasswordVisibility}
          on:keydown={handlePasswordIconKeyDown}
          tabindex="0"
          role="button"
          style="position: absolute; top: 40%; right: 10px; transform: translateY(-50%); cursor: pointer; width: 20px; text-align: center; color:gray;"
        >
          {#if showPassword}
            <IoIosEyeOff />
          {:else}
            <IoIosEye />
          {/if}
        </div>
      </div>
      <button type="submit">Anmelden</button>
    </form>
  {:else}
    <!-- Add content for logged-in user here -->
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

  .password-container {
    display: flex;
    position: relative;
  }
  
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

  .show-password {
    position: absolute;
    top: 50%;
    right: 0;
    transform: translateY(-50%);
    cursor: pointer;
    width: 20px;
    text-align: center;
  }
  
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
  