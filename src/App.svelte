<script>
  import { onMount } from 'svelte';
  import Youtube from "svelte-youtube-embed";

  let eventId = ''; 
  let eventData = null; 

  async function fetchEventData() {
    try {
      const response = await fetch(`https://api.statbotics.io/v2/matches/event/${eventId}`, {
      });

      if (response.ok) {
        eventData = await response.json();
      } else {
        console.error('Failed to fetch data:', response.status);
      }
    } catch (error) {
      console.error('Error fetching data:', error);
    }
  }
  onMount(fetchEventData);

  function handleSubmit(event) {
    event.preventDefault();
    fetchEventData();
  }

  function groupEvents(events) {
    const groupedEvents = [];
    const grouped = {};

    events.forEach(event => {
      const key = `Match ${event.match}, Set ${event.set}`;
      if (!grouped[key]) {
        grouped[key] = [];
      }
      grouped[key].push(event);
    });

    Object.keys(grouped).forEach(key => {
      groupedEvents.push({
        key,
        events: grouped[key]
      });
    });

    return groupedEvents;
  }
  function video(id){
    return "https://www.youtube.com/embed/" + id;
  }
</script>


<main>
  <div class="title">
    <h1> FRC Events</h1>
    <form on:submit={handleSubmit}>
      <div class="search-bar">
        <input type="text" id="event-id" bind:value={eventId} placeholder="Enter event code" />
        <button type="submit">
          Seacrh
        </button>
      </div>
    </form>
  </div>
  

  {#if eventData}
    <div>
      <h2 style="color: black;">Event Information</h2>
      {#each groupEvents(eventData) as group}
        <div class="event-group">
          {#each group.events as event}
            <div class="event">
              <h3> {event.key} </h3>
              <table>
                <thead>
                  <tr>
                    <th>Set: {event.set_number}, Match: {event.match_number}</th>
                  </tr>
                </thead>
                <tbody>
                  {#if event.red_1}
                    <tr class="red-team">
                      <td>{event.red_1}</td>
                    </tr>
                  {/if}
                  {#if event.red_2}
                    <tr class="red-team">
                      <td>{event.red_2}</td>
                    </tr>
                  {/if}
                  {#if event.red_3}
                    <tr class="red-team">
                      <td>{event.red_3}</td>
                    </tr>
                  {/if}
                  {#if event.blue_1}
                    <tr class="blue-team">
                      <td>{event.blue_1}</td>
                    </tr>
                  {/if}
                  {#if event.blue_2}
                    <tr class="blue-team">
                      <td>{event.blue_2}</td>
                    </tr>
                  {/if}
                  {#if event.blue_3}
                    <tr class="blue-team">
                      <td>{event.blue_3}</td>
                    </tr>
                  {/if} 
                </tbody>
              </table>
              <table class="scores">
                <tbody>
                  <tr> 
                    <td> Alliance </td>
                    <td> Auto Score </td>
                    <td>Teleop Score</td>
                    <td> Endgame Score</td>
                    <td> Fouls </td>
                    <td> Total Score </td>
                    <td> Net EPA</td>
                  </tr>
                  <tr>
                    <td> Red </td>
                    <td> {event.red_auto} </td>
                    <td>{event.red_teleop} </td>
                    <td> {event.red_endgame} </td>
                    <td> {event.red_fouls}  </td>
                    <td> {event.red_score}  </td>
                    <td> {event.red_epa_sum} </td>
                  </tr>
                  <tr>
                    <td> Blue </td>
                    <td> {event.blue_auto} </td>
                    <td> {event.blue_teleop} </td>
                    <td> {event.blue_endgame} </td>
                    <td> {event.blue_fouls}  </td>
                    <td> {event.blue_score}  </td>
                    <td> {event.blue_epa_sum} </td>
                  </tr>
                </tbody>
              </table>
              <br>
              {#if event.video}
                <Youtube id={event.video}/>
              {/if}
            </div>
          {/each}
        </div>
      {/each}
      <!-- <pre>{JSON.stringify(eventData, null, 2)}</pre> -->
    </div>
  {:else if eventId !== ''}
    <p style="color: black;">You seem to have put the wrong id (Click the search button if you entered a correct id, otherwise double check that you entered the correct id)</p>
  {/if}
</main>

<style>
main {
  background-color: #f0f4f8;
  width: 100%;
  min-height: 100vh;
  padding: 20px;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.title {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 20px;
  color: black;
}

.search-bar {
  display: flex;
  width: 100%;
  max-width: 600px;
  margin-bottom: 20px;
  background-color: #ccc;
  border-radius: 5px;
  overflow: hidden;
}

.search-bar input[type="text"] {
  border: none;
  flex: 1;
  padding: 12px;
  font-size: 16px;
  color: black;
  background-color: #e6e6e6;
}

.search-bar button {
  background-color: #4caf50;
  color: white;
  border: none;
  padding: 12px 20px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.search-bar button:hover {
  background-color: #45a049;
}

.event-group {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  max-width: 900px;
  margin-bottom: 30px;
}

.event {
  width: 100%;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 8px;
  background-color: #fff;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  margin-bottom: 20px;
  text-align: center;
  color: black;
}

table {
  border-collapse: collapse;
  width: 100%;
  margin-top: 20px;
}

th, td {
  border: 1px solid #ddd;
  padding: 10px;
  text-align: center;
  color: #333;
}

th {
  background-color: #f2f2f2;
  font-weight: bold;
}

.red-team {
  background-color: rgb(255, 102, 102);
  color: black;
  font-weight: bold;
}

.blue-team {
  background-color: rgb(102, 163, 255);
  color: black;
  font-weight: bold;
}

.scores tr:nth-child(odd) {
  background-color: #f9f9f9;
}

.scores tr:nth-child(even) {
  background-color: #fff;
}

@media (max-width: 768px) {
  .event {
    padding: 15px;
  }

  table {
    font-size: 14px;
  }

  th, td {
    padding: 8px;
  }
}

@media (max-width: 600px) {
  .search-bar {
    flex-direction: column;
    width: 90%;
  }

  .search-bar input[type="text"],
  .search-bar button {
    border-radius: 4px;
    margin-bottom: 10px;
    width: 100%;
  }

  .event {
    padding: 10px;
  }

  table {
    font-size: 12px;
  }
}

</style>
