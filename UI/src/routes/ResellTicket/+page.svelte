<!-- Obtained via ChatGPT and modified to fit needs -->
<script>
    // Temporary database with the same format as the create event form
    let events = [
        { id: 1, name: 'Concert A', location: 'Venue A', dateTime: '2024-06-01T19:00', description: 'A great concert', price: 50 },
        { id: 2, name: 'Concert B', location: 'Venue B', dateTime: '2024-06-10T20:00', description: 'Another great concert', price: 75 },
        { id: 3, name: 'Concert C', location: 'Venue C', dateTime: '2024-07-15T18:00', description: 'Yet another great concert', price: 100 },
		{ id: 4, name: 'Concert A', location: 'Venue A', dateTime: '2024-06-01T19:00', description: 'A great concert', price: 50 },
        { id: 5, name: 'Concert B', location: 'Venue B', dateTime: '2024-06-10T20:00', description: 'Another great concert', price: 75 },
        { id: 6, name: 'Concert C', location: 'Venue C', dateTime: '2024-07-15T18:00', description: 'Yet another great concert', price: 100 }
    ];

    // State to track resale details
    let resaleDetails = events.map(event => ({
        ...event,
        willSell: false,
        resalePrice: event.price
    }));

    function handleInputChange(event, id) {
        const { name, value, checked } = event.target;
        resaleDetails = resaleDetails.map(item =>
            item.id === id ? { ...item, [name]: name === 'willSell' ? checked : value } : item
        );
    }

    function handleSubmit() {
        const validResales = resaleDetails.filter(item => item.willSell && parseFloat(item.resalePrice) >= item.price && parseFloat(item.resalePrice) <= item.price * 1.1);
        console.log('Resale Details:', validResales);
    }
</script>

<svelte:head>
    <title>Resell Ticket</title>
    <meta name="ResellTicket" content="Resell ticket page" />
</svelte:head>

<div class="text-column">
    <h1>Ticket Resale Page</h1>
    <form on:submit|preventDefault={handleSubmit}>
        <table>
            <thead>
                <tr>
                    <th style="width: 20%;">Event Name</th>
                    <th style="width: 25%;">Location</th>
                    <th style="width: 15%;">Date and Time</th>
                    <th style="width: 10%;">Sell Ticket</th>
                    <th style="width: 10%;">Resale Price</th>
                </tr>
            </thead>
            <tbody>
                {#each resaleDetails as { id, name, location, dateTime, description, price, willSell, resalePrice }}
                    <tr>
                        <td>{name}</td>
                        <td>{location}</td>
                        <td>{new Date(dateTime).toLocaleString()}</td>
                        <td>
                            <input 
                                type="checkbox" 
                                name="willSell" 
                                checked={willSell} 
                                on:change={(e) => handleInputChange(e, id)} 
                            />
                        </td>
                        <td>
                            <input 
                                type="number" 
                                name="resalePrice" 
                                min={price} 
                                max={price * 1.1} 
                                step="0.01" 
                                value={resalePrice} 
                                on:input={(e) => handleInputChange(e, id)} 
                            />
                        </td>
                    </tr>
                {/each}
            </tbody>
        </table>
        <div class="button-container">
            <button type="submit">Submit Resale Details</button>
        </div>
    </form>
</div>

<style>
    .text-column {
        max-width: 800px;
        margin: 0 auto;
        padding: 20px;
        font-family: Arial, sans-serif;
    }
    table {
        width: 100%;
        border-collapse: collapse;
        margin-bottom: 16px;
    }
    th, td {
        border: 1px solid #ccc;
        padding: 8px;
        text-align: left;
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
	input[type="number"], input[type="checkbox"] {
        width: 100%;
        box-sizing: border-box;
    }
    button:hover {
        background-color: #0056b3;
    }
</style>
