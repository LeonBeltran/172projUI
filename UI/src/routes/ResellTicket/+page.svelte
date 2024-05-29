<!-- Obtained via ChatGPT and modified to fit needs -->
<script lang=ts>
    import { ethers } from "ethers";
    import type { JsonRpcSigner } from "ethers";
    import { Contract } from "ethers";
    import { ABI } from "../abi";
    
    let tickets = []
    // let tickets = [
    //     {
    //     id: 1,
    //     name: "Event A",
    //     location: "Venue A",
    //     date: "2024-06-01",
    //     time: "19:00",
    //     description: "Insert description 1",
    //     price: 0.004
    //     },
    //     {
    //     id: 2,
    //     name: "Event B",
    //     location: "Venue B",
    //     date: "2024-06-02",
    //     time: "20:00",
    //     description: "Insert description 2",
    //     price: 0.0004
    //     },
    // ];


    async function getTickets() {
        const { ethereum } = window as any;
        const provider = new ethers.BrowserProvider(ethereum);
        const account = await provider.send("eth_accounts", []);
        console.log("Account: " + account);

        const signer = await provider.getSigner();
        const contract = await initializeContract(signer);
        let ticketIDs = await contract.getUserTickets(signer)
        for (let tokenID in ticketIDs) {
            let ticket = await contract.getTicketInfo(tokenID)
            tickets.concat(ticket)
        }
    };

    getTickets()

    // State to track resale details
    let resaleDetails = tickets.map(event => ({
        ...event,
        willSell: false,
        resalePrice: event.ticketPrice
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

    const initializeContract = async (signer: JsonRpcSigner) => {
        return new Contract(
        "0x593CE72a79b197a2980c0f74CB22371Fff175118",
        ABI,
        signer
        );
    };
</script>

<svelte:head>
    <title>Resell Ticket</title>
    <meta name="ResellTicket" content="Resell ticket page" />
</svelte:head>

<div class="text-column">
    <h1>Ticket Resale Page</h1>
    <p>If no tickets pop up, check that you connected your wallet and you have purchased tickets.</p>
    <form on:submit|preventDefault={handleSubmit}>
        <table>
            <thead>
                <tr>
                    <th style="width: 30%;">Event Name</th>
                    <th style="width: 25%;">Location</th>
                    <th style="width: 25%;">Date and Time</th>
                    <th style="width: 5%;">Sell Ticket</th>
                    <th style="width: 15%;">Resale Price</th>
                </tr>
            </thead>
            <tbody>
                {#each resaleDetails as { id, name, location, date, time, price, willSell, resalePrice }}
                    <tr>
                        <td>{name}</td>
                        <td>{location}</td>
                        <td>{date} {time}</td>
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
