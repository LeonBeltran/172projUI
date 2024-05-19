<!-- Obtained via ChatGPT and modified to fit needs -->
<script>
    let eventName = '';
    let eventLocation = '';
    let eventDateTime = '';
    let eventDescription = '';
    let ticketPrice = '';

    function handleSubmit() {
        const eventData = {
            name: eventName,
            location: eventLocation,
            dateTime: eventDateTime,
            description: eventDescription,
            price: ticketPrice,
        };
        
        console.log('Event Data:', eventData);

        // Example: Send data to your backend
        // fetch('/api/events', {
        //     method: 'POST',
        //     headers: {
        //         'Content-Type': 'application/json'
        //     },
        //     body: JSON.stringify(eventData)
        // }).then(response => {
        //     if (response.ok) {
        //         alert('Event created successfully!');
        //     } else {
        //         alert('Failed to create event.');
        //     }
        // });
    }
</script>

<svelte:head>
    <title>Create Event</title>
    <meta name="CreateEvent" content="Form to create a new event" />
</svelte:head>

<div class="text-column">
    <h1>Create a New Event</h1>
    <form on:submit|preventDefault={handleSubmit}>
        <div>
            <label for="eventName">Event Name:</label>
            <input id="eventName" type="text" bind:value={eventName} required />
        </div>
        <div>
            <label for="eventLocation">Event Location:</label>
            <input id="eventLocation" type="text" bind:value={eventLocation} required />
        </div>
        <div>
            <label for="eventDateTime">Event Date and Time:</label>
            <input id="eventDateTime" type="datetime-local" bind:value={eventDateTime} required />
        </div>
        <div>
            <label for="eventDescription">Event Description:</label>
            <textarea id="eventDescription" bind:value={eventDescription} required></textarea>
        </div>
        <div>
            <label for="ticketPrice">Ticket Price (Php):</label>
            <input id="ticketPrice" type="number" step="0.01" min="0" bind:value={ticketPrice} required />
        </div>
        <div class="button-container">
            <button type="submit">Create Event</button>
        </div>
    </form>
</div>

<style>
    .text-column {
        max-width: 600px;
        margin: 0 auto;
        padding: 20px;
        font-family: Arial, sans-serif;
    }
    label {
        display: block;
        margin-bottom: 8px;
        font-weight: bold;
    }
    input, textarea {
        width: 100%;
        padding: 8px;
        margin-bottom: 16px;
        border: 1px solid #ccc;
        border-radius: 4px;
    }
    .button-container {
        display: flex;
        justify-content: center;
    }
    button {
        padding: 10px 20px;
        background-color: #007BFF;
        color: white;
        border: none;
        border-radius: 4px;
        cursor: pointer;
    }
    button:hover {
        background-color: #0056b3;
    }
</style>