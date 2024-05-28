<script>
//Event list redirect:
//Event list with brief info
//When the event is clicked, purchase button is displayed
//Checkout: show ticket information e.g. price
//Confirmation of purchase / receipt

  let events = [
  {
    id: 1,
    name: "Event A",
    location: "Venue A",
    date: "2024-06-01",
    time: "19:00",
    description: "Insert description 1",
    price: 0.004
  },
  {
    id: 2,
    name: "Event B",
    location: "Venue B",
    date: "2024-06-02",
    time: "20:00",
    description: "Insert description 2",
    price: 0.0004
  },
];

let selectedEvent = null;
let confirmed = false;

function handleEventClick(event) {
  selectedEvent = event;
}

async function confirmPurchase() {
  // Check if MetaMask is installed and connected
  if (typeof window.ethereum !== 'undefined') {
    // Request account access
    await window.ethereum.request({ method: 'eth_requestAccounts' });
    const accounts = await window.ethereum.request({ method: 'eth_accounts' });
    const account = accounts[0];

    // Convert the price to Wei
    const priceWei = window.ethereum.request({
      method: 'web3_fromWei',
      params: [selectedEvent.price.toString(), 'ether'],
      to: 'web3.toWei'
    });

    // Send the transaction
    await window.ethereum.request({
      method: 'eth_sendTransaction',
      params: [{
        from: account,
        to: "0x30E6Ba84Aff8277390B29cfE66c4735dE6D9767c", // contract address
        value: priceWei,
        gasPrice: '0x0', // Use the default gas price
        gas: '0x0' // Use the default gas limit
      }]
    });

    // Set confirmation flag
    confirmed = true;
  } else {
    // MetaMask is not installed
    alert('Please install MetaMask to purchase tickets.');
  }
}
</script>

<style>
  body {
    font-family: 'Arial', sans-serif;
    background-color: #f0f2f5;
    margin: 0;
    padding: 20px;
  }

  .container {
    max-width: 800px;
    margin: 0 auto;
    padding: 20px;
    background-color: #fff;
    border-radius: 8px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  }

  .header {
    text-align: center;
    margin-bottom: 20px;
    color: #000000;
    font-size: 24px;
    font-weight: bold;
  }

  .event {
    border: 1px solid #e0e0e0;
    padding: 15px;
    margin: 10px 0;
    border-radius: 8px;
    transition: background-color 0.3s, box-shadow 0.3s;
  }

  .event:hover {
    cursor: pointer;
    background-color: #fafafa;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  }

  .event h2 {
    color: #007bff;
  }

  .checkout, .confirmation {
    margin: 20px 0;
    padding: 20px;
    border: 1px solid #e0e0e0;
    background-color: #f9f9f9;
    border-radius: 8px;
  }

  .checkout h2, .confirmation h2 {
    color: #007bff;
  }

  h2 {
    color: #333;
  }

  p {
    color: #555;
    line-height: 1.6;
  }

  button {
    padding: 10px 20px;
    font-size: 16px;
    color: #fff;
    background-color: #007bff;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    transition: background-color 0.3s;
  }

  button:hover {
    background-color: #0056b3;
  }
</style>

<div class="container">
  <div class="header">
    Welcome to our Event List! Feel free to browse.
  </div>
  {#if !selectedEvent}
    <div>
      {#each events as event}
        <div class="event" on:click={() => handleEventClick(event)}>
          <h2>{event.name}</h2>
          <p><strong>Location:</strong> {event.location}</p>
          <p><strong>Date & Time:</strong> {event.date} at {event.time}</p>
          <p>{event.description}</p>
          <p><strong>Price:</strong> {event.price} ETH</p>
        </div>
      {/each}
    </div>
  {:else if !confirmed}
    <div class="checkout">
      <h2>Checkout</h2>
      <p><strong>Event:</strong> {selectedEvent.name}</p>
      <p><strong>Price:</strong> {selectedEvent.price} ETH</p>
      <button on:click={confirmPurchase}>Confirm Purchase</button>
    </div>
  {:else}
    <div class="confirmation">
      <h2>Confirmation</h2>
      <p>Thank you for your purchase!</p>
      <p><strong>Event:</strong> {selectedEvent.name}</p>
      <p><strong>Price:</strong> {selectedEvent.price} ETH</p>
      <button on:click={() => { selectedEvent = null; confirmed = false; }}>Back to Events</button>
    </div>
  {/if}
</div>
