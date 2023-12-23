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
      <h2>Event Information</h2>
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
    <p>You seem to have put the wrong id (Click the search button if you entered a correct id, otherwise double check that you entered the correct id)</p>
  {/if}
</main>

<style>
  main {
   background-color: teal;
   width: 100%;
   height: 100%;
 }

 .title {
  align-items: top;
 }

 .search-bar {
   display: flex;
 }

 .search-bar input[type="text"] {
   border: solid black;
   border-radius: 1px;
   outline: none;
   flex: 1;
   padding: 10px;
   font-size: 16px;
 }

 .search-bar button {
   background-color: #4caf50;
   padding: 10px;
 }
 .event-group {
    margin-bottom: 20px;
  }

  .event {
    margin: 20px;
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 8px;
    background-color: #fff;
    color: black;
  }

  table {
    border-collapse: collapse;
    width: 100%;
  }

  th,
  td {
    border: 1px solid #ddd;
    padding: 8px;
    text-align: left;
    color: black;
  }

  th {
    background-color: #f2f2f2;
    color: black;
  }

  .red-team {
    background-color: rgb(236, 62, 39);
    color: white;
  }

  .blue-team {
    background-color: blue;
    color: white;
  }
 
</style>
