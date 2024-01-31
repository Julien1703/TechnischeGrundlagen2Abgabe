<script>
  import { onMount } from 'svelte';
  import axios from 'axios';
  import { push } from 'svelte-spa-router';
  import { isLoggedIn } from './store';

  let sensorData = [];

  onMount(async () => {
    if (!$isLoggedIn) {
      push('/login');
    } else {
      try {
        const response = await axios.post('http://localhost:3000/data');
        console.log('Token-Verifizierung:', response);
        sensorData = response.data;
      } catch (error) {
        console.error('Fehler beim Abrufen der Sensor-Daten:', error);
      }
    }
  });

  const viewDeviceData = (mac) => {
    push(`#/devicedata/${mac}`);
  };
</script>

<style>
  .esp-container-group {
    display: flex;
    flex-wrap: wrap; /* Umbruch der Raum-Container bei Bedarf */
    justify-content: space-around; /* Zentriert die Raum-Container horizontal */
    margin: 40px 0 60px; /* Setzt den Margin oben und unten */
  }
  .esp-container {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    margin: 15px 5px;
    padding: 0px;
    padding-top: 0px;
    border-radius: 15px;
    background: linear-gradient(to bottom, #00cab9 0%, #00cab9 28%, #ffffff 28%, #ffffff);
    box-shadow: 0 3px 6px rgba(0, 0, 0, 0.1);
    transition: all 0.3s ease;
    position: relative; /* Positionierung für das Hintergrundbild */
    overflow: hidden; /* Verhindert das Überlaufen des Hintergrundbilds */
    z-index: 0;
  }

  .esp-container:hover {
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
    transform: translateY(-3px);
  }

  .esp-data {
    margin-right: 20px;
    margin-left: 20px;
    font-size: 20px;
    color: #ffffff;
    font-weight: bold; /* Fett für devicename */
    margin-bottom: 5px;
  }

  .esp-created-at {
    margin-left: 20px;
    font-size: 14px;
    color: grey;
  }

  button {
    width: 285px;
    padding: 15px 24px;
    border: none;
    border-radius: 10px;
    background: linear-gradient(to bottom, #00cab9 ,#00b1a2);
    color: white;
    cursor: pointer;
    font-size: 16px;
    margin-left: 20px;
    margin-right: 20px;
    margin-top: 15px;
    margin-bottom: 15px;
    z-index: 1;
    transition: background-color 0.3s, transform 0.3s;
  }

  button:hover {
    background-color: #0056b3;
  }
</style>

<!-- HTML Teil -->
<div class="esp-container-group">
  {#if sensorData.length > 0}
    <h2>Deine æDevices</h2>
    {#each sensorData as data}
      <div class="esp-container">
        <p class="esp-data">{data.devicename}</p>
        <p class="esp-created-at">{new Date(data.created_at).toLocaleString()}</p>
        <button on:click={() => viewDeviceData(data.mac)}>Gerätedaten</button>
      </div>
    {/each}
  {:else}
    <p>Keine Daten verfügbar.</p>
  {/if}
</div>
