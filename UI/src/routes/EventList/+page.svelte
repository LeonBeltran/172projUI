<script lang="ts">
    import { ethers } from "ethers";
    import type { JsonRpcSigner } from "ethers";
    import { Contract } from "ethers";
    import { ABI } from "../abi";
    import { onMount } from "svelte";

    let ticketsArray = [];

    async function getEvents() {
      const { ethereum } = window as any;
      const provider = new ethers.BrowserProvider(ethereum);
      const signer = await provider.getSigner();
      const contract = await initializeContract(signer);
      const account = await signer.getAddress();

      const ticketsArray = [];

      for (let n = 1; n <= 10; n++) {
        const ticket = await contract.getTicketInfo(n);
        const ticketObject = {
          tokenId: ticket[0],
          totalTickets: ticket[1],
          ticketsSold: ticket[2],
          ticketPrice: ticket[3],
          ticketStartDate: ticket[4],
          ticketEndDate: ticket[5],
          ticketHoldDate: ticket[6],
          creator: ticket[7],
          ticketSold: ticket[8],
          isResellable: ticket[9]
        };
        ticketsArray.push(ticketObject);
      }

      return ticketsArray;
    };

    onMount(async () => {
      ticketsArray = await getEvents();
    });

    async function buyTicket(tokenId, ticketsToBuy) { 
      const { ethereum } = window as any;
      const provider = new ethers.BrowserProvider(ethereum);
      const signer = await provider.getSigner();
      const contract = await initializeContract(signer);
      const account = await signer.getAddress();

      const ticket = await contract.getTicketInfo(tokenId);
      console.log('Ticket Price');
      console.log(ticket.ticketPrice);

      console.log('Tickets to buy');
      console.log(ticketsToBuy);

      console.log('Total tickets');
      console.log(ticket.totalTickets);

      const purchaseFee = await contract.getPurchaseFeePercentage();
      console.log('Purchase Fee');
      console.log(purchaseFee);

      const totalPrice = BigInt(ticket.ticketPrice) * BigInt(ticketsToBuy);
      console.log('Total price');
      console.log(totalPrice);

      const totalPriceWithFee = BigInt(totalPrice) + BigInt(purchaseFee);
      console.log('Total price with fee');
      console.log(totalPriceWithFee);

      await contract.purchaseTicket(tokenId, ticketsToBuy, { value: totalPriceWithFee });
    };
    
    const initializeContract = async (signer: JsonRpcSigner) => {
        return new Contract(
        "0x593CE72a79b197a2980c0f74CB22371Fff175118",
        ABI,
        signer
        );
    };
</script>

<style>
  .ticket-container {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
  }

  .ticket-box {
    width: 300px;
    border: 1px solid #ccc;
    border-radius: 5px;
    padding: 20px;
    margin: 10px;
    background-color: white;
    box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.1);
    text-align: center;
    overflow: hidden;
    position: relative;
  }

  .ticket-info {
    margin-bottom: 20px;
    white-space: pre-wrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  .buy-button {
    position: absolute;
    bottom: 10px;
    left: 50%;
    transform: translateX(-50%);
    padding: 8px 16px;
    background-color: green;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }
</style>

<div class="ticket-container">
  {#each ticketsArray as ticket}
    <div class="ticket-box">
      <div class="ticket-info">
        <p><strong>Token ID:</strong> {ticket.tokenId}</p>
        <p><strong>Total Tickets:</strong> {ticket.totalTickets}</p>
        <p><strong>Tickets Sold:</strong> {ticket.ticketsSold}</p>
        <p><strong>Ticket Price:</strong> {ticket.ticketPrice}</p>
        <p><strong>Ticket Start Date:</strong> {ticket.ticketStartDate}</p>
        <p><strong>Ticket End Date:</strong> {ticket.ticketEndDate}</p>
        <p><strong>Ticket Hold Date:</strong> {ticket.ticketHoldDate}</p>
        <p><strong>Creator:</strong> {ticket.creator}</p>
        <p><strong>Ticket Sold:</strong> {ticket.ticketSold}</p>
        <p><strong>Is Resellable:</strong> {ticket.isResellable}</p>
      </div>
      <button class="buy-button" on:click={() => buyTicket(ticket.tokenId, 1)}>Buy an event ticket</button>
    </div>
  {/each}
</div>
