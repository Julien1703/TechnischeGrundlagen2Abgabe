<script>
  import axios from "axios";
  import { push } from "svelte-spa-router";
  import { username, isLoggedIn, token } from './store';
  import { onMount, onDestroy } from 'svelte';
  import IoIosEye from 'svelte-icons/io/IoIosEye.svelte';
  import IoIosEyeOff from 'svelte-icons/io/IoIosEyeOff.svelte';

  let password = "";
  let passwordconfirm = "";
  let showPassword = false;
  let showConfirmPassword = false;
  let passwordInput;
  let confirmpasswordInput;

  onMount(() => {
    // Check if the password matches the confirmation
    checkPasswordMatch();
  });

  onDestroy(() => {
    // Clear the username store when the component is destroyed
    username.set('');
  });

  let passwordCursorPosition = 0;
  let confirmPasswordCursorPosition = 0;

  function togglePasswordVisibility() {
    showPassword = !showPassword;

    if (passwordInput && passwordInput instanceof HTMLInputElement) {
      // Save the current cursor position in the Password-Input
      passwordCursorPosition = passwordInput.selectionStart;

      // Set the cursor to the end of the text in the input field
      requestAnimationFrame(() => {
        passwordInput.focus();

        // Restore the cursor position
        passwordInput.setSelectionRange(passwordCursorPosition, passwordCursorPosition);
      });
    }
  }

  function toggleConfirmPasswordVisibility() {
    showConfirmPassword = !showConfirmPassword;

    if (confirmpasswordInput && confirmpasswordInput instanceof HTMLInputElement) {
      // Save the current cursor position in the ConfirmPassword-Input
      confirmPasswordCursorPosition = confirmpasswordInput.selectionStart;

      // Set the cursor to the end of the text in the input field
      requestAnimationFrame(() => {
        confirmpasswordInput.focus();

        // Restore the cursor position
        confirmpasswordInput.setSelectionRange(confirmPasswordCursorPosition, confirmPasswordCursorPosition);
      });
    }
  }

  function handlePasswordIconKeyDown(event) {
    if (event.key === 'Enter') {
      togglePasswordVisibility();
    }
  }

  function handleConfirmPasswordIconKeyDown(event) {
    if (event.key === 'Enter') {
      toggleConfirmPasswordVisibility();
    }
  }

  function handleIconMouseDown(event) {
    // Prevent the icon from taking focus
    event.preventDefault();
  }

  function checkPasswordMatch() {
    return password === passwordconfirm;
  }

  async function registerUser() {
    // Check if the password matches the confirmation
    if (!checkPasswordMatch()) {
      console.error("Passwords do not match.");
      return;
    }

    try {
      const response = await axios.post("http://localhost:3001/register", {
        username: $username,
        password: password,
        passwordconfirm: passwordconfirm,
      });

      console.log("Registration successful:", response);

      const loginResponse = await axios.post("http://localhost:3001/login", {
        username: $username,
        password: password,
      });

      token.set(loginResponse.data.token);
      isLoggedIn.set(true);
      push("#/login");
    } catch (error) {
      console.error("Error during registration and login:", error);
    }
  }

  // axios interceptor for JWT:
  axios.interceptors.request.use((config) => {
    config.headers.authorization = `Bearer ${$token}`;
    return config;
  }, (error) => {
    return Promise.reject(error);
  });
</script>

<div class="container">
  <form on:submit|preventDefault={registerUser}>
    <h1>Registrieren</h1>

    <div>
      <input id="username" bind:value={$username} placeholder="Benutzername erstellen" class="custom-margin" />
    </div>

    <div class="password-container">
      <input id="password"
        value={password}
        type={showPassword ? 'text' : 'password'}
        placeholder="Passwort hinzufügen"
        bind:this={passwordInput}
        on:blur={() => {
          if (!document.activeElement.classList.contains('show-password')) {
            passwordInput.blur();
          }
        }}
        on:input={() => { password = passwordInput.value; }}
      />

      <div
        class="show-password"
        on:mousedown={handleIconMouseDown}
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

    <div class="password-container">
      <input id="passwordconfirm"
        value={passwordconfirm}
        type={showConfirmPassword ? 'text' : 'password'}
        placeholder="Passwort wiederholen"
        bind:this={confirmpasswordInput}
        on:blur={() => {
          if (!document.activeElement.classList.contains('show-password')) {
            confirmpasswordInput.blur();
          }
        }}
        on:input={() => { passwordconfirm = confirmpasswordInput.value; }}
      />

      <div
        class="show-password"
        on:mousedown={handleIconMouseDown}
        on:click={toggleConfirmPasswordVisibility}
        on:keydown={handleConfirmPasswordIconKeyDown}
        tabindex="0"
        role="button"
        style="position: absolute; top: 40%; right: 10px; transform: translateY(-50%); cursor: pointer; width: 20px; text-align: center; color:gray;"
      >
        {#if showConfirmPassword}
          <IoIosEyeOff />
        {:else}
          <IoIosEye />
        {/if}
      </div>
    </div>

    <button type="submit">Registrieren</button>
  </form>
</div>

<style>
  .container {
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: #f4f4f400;
    padding: 0px;
    box-sizing: border-box;
  }

  form {
    width: 400px;
    margin-top: 90px;
    padding: 40px;
    border: none;
    border-radius: 15px;
    background: linear-gradient(to bottom, #00cab9 0%, #00cab9 23%, #ffffff 23%, #ffffff);
    box-shadow: 0 4px 8px rgba(152, 152, 152, 0.1);
  }

  .custom-margin {
    margin-bottom: 20px;
  }

  .password-container {
    display: flex;
    position: relative;
  }
  
  h1 {
    color: #ffffff;
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

    /* New rule for the placeholder in focus */
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
    width: 100%;
    box-sizing: border-box;
    padding: 14px;
    margin-top: 20px;
    margin-bottom: 15px;
    border: none;
    background: linear-gradient(to bottom, #00cab9 ,#00b1a2);
    color: white;
    border-radius: 10px;
    cursor: pointer;
    font-size: 16px;
  }

  button:hover {
    background-color: #0056b3;
  }

  :global(h1) {
    text-align: center;
    margin-top: 1px;
    margin-bottom: 40px;
  }
</style>
